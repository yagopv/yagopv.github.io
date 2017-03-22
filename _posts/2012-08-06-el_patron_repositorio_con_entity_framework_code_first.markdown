---
layout: post
title: El patrón Repositorio con Entity Framework Code First
date: '2012-08-06 03:50:06'
tags:
- asp mvc
- entity framework
categories:
- .NET
---

En este post vamos a implementar un Repositorio genérico mediante Entity Framework Code First y crearemos Repositorios específicos con operaciones especiales para las entidades.

En un [post anterior](../../../Post/GetPostByCode/el_patron_unit_of_work_con_entity_framework_code_first "Unit Of Work con Entity Framework"), estuvimos hablando de cómo podíamos crear una Unit of Work con Entity Framework. Comentamos que en realidad los DbContext de Entity Framework son unidades de trabajo en sí mismos y que la implementación de la que íbamos a hablar tenía como objetivo el aislamiento del mecanismo de persistencia del dominio de nuestra aplicación entre otras cosas. En el caso de los repositorios, podemos decir exactamente lo mismo, ya que DbContext ya nos proporciona la funcionalidad necesaria en forma de métodos como Find, Get, … Por lo tanto las abstracciones que vamos a crear en este post tienen el mismo propósito que el comentado en el caso de la unidad de trabajo.

Vamos a crear una implementación del patrón Repositorio utilizando la unidad de trabajo creada en el anterior apartado.

El repositorio nos dará acceso a la funcionalidad básica de nuestro contexto de trabajo. Además relacionaremos el repositorio con la unidad de trabajo de manera que pueda acceder a sus operaciones principales.

La técnica que vamos a usar, consistirá en la creación de un repositorio genérico en el que se especificará la funcionalidad básica y que además podamos derivar en la búsqueda de funcionalidad específica.

### En la capa del Dominio

En primer lugar crearemos una interfaz genérica

```javascript
namespace MyApp.Domain.RepositoryContracts
{
    /// <summary>
    /// Métodos genéricos para todos los repositorios
    /// </summary>
    public interface IRepository<TEntity>
    {
        /// <summary>
        /// Traemos la Unit of Work al repositorio para así poder hacer Commit, Rollback ... de los cambios
        /// </summary>
        IUnitOfWork UnitOfWork { get; }
        IEnumerable<TEntity> Get(Expression<Func<TEntity, bool>> filter = null, Func<IQueryable<TEntity>, IOrderedQueryable<TEntity>> orderBy = null, string includeProperties = "");
        TEntity GetByID(object id);
        void Insert(TEntity entity);
        void Delete(object id);
        void Delete(TEntity entityToDelete);
        void Update(TEntity entityToUpdate);
        IEnumerable<TEntity> GetPagedElements<TKey>(int pageIndex, int pageCount, Expression<Func<TEntity, TKey>> orderByExpression, bool ascending = true);
        IEnumerable<TEntity> GetPagedElements<TKey>(int pageIndex, int pageCount, Expression<Func<TEntity, TKey>> orderByExpression, bool ascending = true, string includeProperties = "");
        IEnumerable<TEntity> GetFromDatabaseWithQuery(string sqlQuery, params object[] parameters);
        int ExecuteInDatabaseByQuery(string sqlCommand, params object[] parameters);
    }
}
```

En esta interfaz hemos definido los métodos genéricos que deberían ser comunes a cualquier Repositorio creado en nuestra aplicación. Podemos observar (según el namespace definido) que este contrato se crea en la capa de Dominio de nuestra aplicación y por tanto será el contacto con la capa de acceso a datos específica. La implementación posterior se podría hacer usando Entity Framework como mecanismo de persistencia o cualquier otro como NHibernate, ADO.NET …

En la misma capa, vamos a crear interfaces específicas para las entidades que forman parte de nuestro Dominio. Normalmente se deberían definir Repositorios para las entidades raíz de nuestros [agregados](http://www.yagoperez.com/Post/GetPostByCode/patron_agreggate "El patrón agregado"). Estas raices, son las entidades principales a las que vamos a acceder. Sin embargo habrá otras que no tendrán sentido por sí mismas y sin relacionarse con dichas entidades raíz. Por tanto, no deberíamos definir repositorios para ellas y sí acceder a través de estas entidades raíz.

Para la entidad Post definimos el siguiente contrato

```javascript
namespace MyApp.Domain.RepositoryContracts
{
    /// <summary>
    /// Contrato para los métodos no genéricos de Post
    /// </summary>
    public interface IPostRepository:IRepository<Post>
    {
        ICollection<Post> GetPostsByCategory(string category)
    }
}
```

Como se puede apreciar, nuestro contrato específico, deriva de IRepository<Post> por lo que hereda todas las operaciones del repositorio genérico. A mayores se incluyen las operaciones específicas que queremos implementar para la entidad Post y que no guardarán relación con las demás. En este caso incluimos GetPostsByCategory() que recupera los Posts agrupados por categoría.

Hacemos el mismo ejercicio para el resto de entidades …

Category, que no incluirá métodos específicos …

```javascript
using MyApp.Domain.EntityModel;

namespace BgEngine.Domain.RepositoryContracts
{
    /// <summary>
    /// Contrato para los método no genéricos de  Category
    /// </summary>
    public interface ICategoryRepository : IRepository<Category>
    {
        
    }
}
```

… y Comment, en el que añadimos un método para recuperar el total de Comentarios …

```javascript
using Myapp.Domain.EntityModel;

namespace BgEngine.Domain.RepositoryContracts
{
    /// <summary>
    /// Contrato para los métodos no genéricos de  Comment
    /// </summary>
    public interface ICommentRepository:IRepository<Comment>
    {
         int GetCount();
    }
}
```


### La capa de Infraestructura

En la capa de Infraestructura implementaremos los Repositorios, tanto el genérico como los específicos. La implementación genérica sería algo así

```javascript
namespace MyApp.Infraestructure.Repositories
{
    /// <summary>
    /// Implementacion de un repositorio generico
    /// </summary>
    public class Repository<TEntity> : IRepository<TEntity> where TEntity : class
    {
        /// <summary>
        /// Unidad de trabajo de este contexto de trabajo
        /// </summary>
        IBlogUnitOfWork unitofwork;

        /// <summary>
        /// Unidad de trabajo expuesta a la aplicacion
        /// </summary>
        /// <param name="unitofwork">The current Unit of Work</param>
        public IUnitOfWork UnitOfWork 
        { 
            get 
            {
                return this.unitofwork; 
            } 
        }

        /// <summary>
        /// ctor
        /// </summary>
        /// <param name="unitofwork">Unidad de trabajo</param>
        public Repository(IBlogUnitOfWork unitofwork)
        {
            this.unitofwork = unitofwork;
        }

        /// <summary>
        /// Metodo generico para recuperar una coleccion de entidades
        /// </summary>
        /// <param name="filter">Expresion para filtrar las entidades</param>
        /// <param name="orderBy">Orden en el que se quiere recuperar las entidades</param>
        /// <param name="includeProperties">Propiedades de Navegacion a incluir</param>
        /// <returns>Un listado de objetos de la entidadgenerica</returns>
        public virtual IEnumerable<TEntity> Get(Expression<Func<TEntity, bool>> filter = null, Func<IQueryable<TEntity>, IOrderedQueryable<TEntity>> orderBy = null, string includeProperties = "")
        {
            IQueryable<TEntity> query = unitofwork.Set<TEntity>();

            if (filter != null)
            {
                query = query.Where(filter);
            }

            if (!String.IsNullOrEmpty(includeProperties))
            {
                foreach (var includeProperty in includeProperties.Split
                    (new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries))
                {
                    query = query.Include(includeProperty);
                }
            }

            if (orderBy != null)
            {
                return orderBy(query).ToList();
            }
            else
            { 
                return query.ToList();
            }
        }
        /// <summary>
        /// Metodo generico para recuperar una entidad a partir de su identidad
        /// </summary>
        /// <param name="id">La identidad de la entidad</param>
        /// <returns>La entidad</returns>
        public virtual TEntity GetByID(object id)
        {
            return unitofwork.Set<TEntity>().Find(id);
        }

        /// <summary>
        /// Metodo generico para añadir una entidad al contexto de trabajo
        /// </summary>
        /// <param name="entity">La entidad para añadir</param>
        public virtual void Insert(TEntity entity)
        {
            unitofwork.Set<TEntity>().Add(entity);
        }

        /// <summary>
        /// Metodo generico para eliminar una entidad del contexto de trabajo
        /// </summary>
        /// <param name="id">La identidad de la entidad</param>
        public virtual void Delete(object id)
        {
            TEntity entityToDelete = unitofwork.Set<TEntity>().Find(id);
            Delete(entityToDelete);
        }

        /// <summary>
        /// Metodo generico para eliminar una entidad del contexto de trabajo pasandole la entidad
        /// </summary>
        /// <param name="entityToDelete">Entidad a eliminar</param>
        public virtual void Delete(TEntity entityToDelete)
        {
            unitofwork.Attach<TEntity>(entityToDelete);
            unitofwork.Set<TEntity>().Remove(entityToDelete);
        }

        /// <summary>
        /// Metodo generico para modificar una entidad en el contexto de trabajo
        /// </summary>
        /// <param name="entityToUpdate">La entidad a modificar</param>
        public virtual void Update(TEntity entityToUpdate)
        {
            unitofwork.SetModified<TEntity>(entityToUpdate);
        }

        /// <summary>
        /// Implementacion generica de un metodo para paginar
        /// </summary>
        /// <typeparam name="TKey">Clave para el orden</typeparam>
        /// <param name="pageIndex">Indice de la pagina a recuperar</param>/// 
        /// <param name="pageCount">Numero de entidades a recuperar</param>
        /// <param name="orderByExpression">Order expression</param>
        /// <param name="ascending">Orden ascendente o descendente</param>
        /// <returns>Listado de todas las entidades qeu cumplan los requisitos</returns>
        public IEnumerable<TEntity> GetPagedElements<TKey>(int pageIndex, int pageCount, Expression<Func<TEntity, TKey>> orderByExpression, bool ascending = true)
        {
            if (pageIndex < 1) { pageIndex = 1; }

            if (orderByExpression == (Expression<Func<TEntity, TKey>>)null)
                    throw new ArgumentNullException();

            return (ascending) 
                            ?
                        unitofwork.Set<TEntity>().OrderBy(orderByExpression)                      
                            .Skip((pageIndex - 1) * pageCount)
                            .Take(pageCount)
                            .ToList()
                            :
                        unitofwork.Set<TEntity>().OrderByDescending(orderByExpression)
                            .Skip((pageIndex - 1) * pageCount)
                            .Take(pageCount)
                            .ToList();
        }
        /// <summary>
        /// Implementacion generica de un metodo para paginar
        /// </summary>
        /// <typeparam name="TKey">Clave para el orden</typeparam>
        /// <param name="pageIndex">Indice de la pagina a recuperar</param>/// 
        /// <param name="pageCount">Numero de entidades a recuperar</param>
        /// <param name="orderByExpression">La expresion para establecer el orden</param>
        /// <param name="ascending">Si el orden es ascendente o descendente</param>
        /// <param name="includeProperties">Includes</param>
        /// <returns>Listado con todas las entidades que cumplan los criterios</returns>        
        public IEnumerable<TEntity> GetPagedElements<TKey>(int pageIndex, int pageCount, Expression<Func<TEntity, TKey>> orderByExpression, bool ascending = true, string includeProperties = "")
        {
            IQueryable<TEntity> query = unitofwork.Set<TEntity>();

            if (!String.IsNullOrEmpty(includeProperties))
            {
                foreach (var includeProperty in includeProperties.Split
                    (new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries))
                {
                    query = query.Include(includeProperty);
                }
            }
            
            if (pageIndex < 1) { pageIndex = 1; }

            if (orderByExpression == (Expression<Func<TEntity, TKey>>)null)
                throw new ArgumentNullException();

            return (ascending)
                            ?
                        query.OrderBy(orderByExpression)
                            .Skip((pageIndex - 1) * pageCount)
                            .Take(pageCount)
                            .ToList()
                            :
                        query.OrderByDescending(orderByExpression)
                            .Skip((pageIndex - 1) * pageCount)
                            .Take(pageCount)
                            .ToList();
        }
        /// <summary>
        /// Ejecutar una query en la base de datos
        /// </summary>
        /// <param name="sqlQuery">La Query</param>
        /// <param name="parameters">The parameters</param>
        /// <returns>Listado de entidades que recupera la query</returns>
        public IEnumerable<TEntity> GetFromDatabaseWithQuery(string sqlQuery, params object[] parameters)
        {
            return this.unitofwork.ExecuteQuery<TEntity>(sqlQuery, parameters);
        }

        /// <summary>
        /// Ejecutar un command en la base de datos 
        /// </summary>
        /// <param name="sqlCommand">La query</param>
        /// <param name="parameters">Los parametros</param>
        /// <returns>El sql code que devuelve la query</returns>
        public int ExecuteInDatabaseByQuery(string sqlCommand, params object[] parameters)
        {            
            return this.unitofwork.ExecuteCommand(sqlCommand,parameters);
        }
```

Una vez tenemos la implementación genérica, vamos con las específicas. En primer lugar para la entidad Post

```javascript
namespace MyApp.Infraestructure.Repositories
{
    /// <summary>
    /// Implementación de los métodos no genéricos de Post
    /// </summary>
    public class PostRepository : Repository<Post>, IPostRepository
    {
        /// <summary>
        /// ctor
        /// </summary>
        /// <param name="unitofwork">La unidad de trabajo actual</param>
        public PostRepository(IBlogUnitOfWork unitofwork) : base(unitofwork) {   }

        /// <summary>
        /// Recupera Posts filtrados por Category
        /// </summary>
        /// <param name="category">El nombre de la Category</param>
        /// <returns>Colección de  Posts</returns>
        public ICollection<Post> GetPostsByCategory(string category)
        {
            return  (this.UnitOfWork as IBlogUnitOfWork).Posts
                    .Include(p => p.Category)
                    .Where(p => p.Category.Name == category)
                    .OrderByDescending(p => p.DateCreated).ToList();
        }

        /// <summary>
        /// Insertar un nuevo Post
        /// Crea un nuevo Post con la fecha actual
        /// </summary>
        /// <param name="entity">El Post a insertar</param>
        public override void Insert(Post entity)
        {
            entity.DateCreated = DateTime.Now;
            base.Insert(entity);
        }
    }
}
```

Como se puede observar, además de implementar el método GetPostsByCategory(), hemos sobreescrito el método genérico Insert() para informar siempre la fecha de creación de un Post.

No vamos a crear ninguna implementación específica para ICategoryRepository, ya que no habíamos incluido ningún método en el contrato. Si queremos usar un repositorio de Categorías podemos hacerlo con el genérico IRepository<Category> / Repository<Category>. Esto nos facilita mucho las cosas ya que puede haber muchas entidades que no requieran de operaciones específicas. Por tanto, con el repositorio genérico ya podemos operar con cualquiera de ellas.

Para la entidad Comment sí que habíamos definido un método, por tanto la implementación sería así …

```javascript
namespace MyApp.Infraestructure.Repositories
{
    /// <summary>
    /// Implementación específica de la entidad Comment
    /// </summary>
    public class CommentRepository : Repository<Comment>, ICommentRepository
    {
        /// <summary>
        /// ctor
        /// </summary>
        /// <param name="unitofwork">La unidad de trabajo actual</param>
        public CommentRepository(IBlogUnitOfWork unitofwork) : base(unitofwork) { }

        /// <summary>
        /// Recupera la suma de Comentarios total
        /// </summary>        
        public int GetCount()
        {            
            return  (this.UnitOfWork as IBlogUnitOfWork).Set<Comment>().Count();
        }
    }
}
```

Con esto hemos finalizado la implementación genérica y específica de nuestros contratos de la capa del Dominio en la capa de Infraestructura.

### Resumen

En este post hemos creado una posible implementación del patrón Repositorio usando la Unidad de trabajo de la que habíamos hablado en un [post anterior](../../../Post/GetPostByCode/el_patron_unit_of_work_con_entity_framework_code_first "Unit of work"). Nótese que definimos los contratos de los Repositorios en la capa del Dominio y los implementamos en la capa de Infraestructura, que normalmente serán dos proyectos separados. La capa de Dominio no debería saber nada de Entity Framework y con nuestra implementación realmente sólo se necesita referenciar EntityFramework.dll en la capa de Infraestructura. Hemos conseguido por tanto que nuestra capa de dominio no sepa nada del mecanismo de persistencia.

Por otra parte, los contratos de la capa de Dominio se referenciarán normalmente desde capas superiores, normalmente una capa de Servicios de la Aplicación que a su vez sea usada por la Interfaz de usuario (Un proyecto web por ejemplo). Es en estas capas superiores donde se inyectarán las dependencias con los repositorios implementados mediante el sistema que se prefiera (StructureMap, Unity, …).

Hasta pronto!!

