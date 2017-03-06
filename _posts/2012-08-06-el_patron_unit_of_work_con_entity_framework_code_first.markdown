---
layout: post
title: El patrón Unit of Work con Entity Framework Code First
date: '2012-08-06 03:50:06'
---


En este post vamos a ver un ejemplo de cómo podríamos crear una abstracción del patrón Unit Of Work con Entity Framework Code First.

DbContext es una unidad de trabajo en sí, pero si queremos aislar nuestro contexto de trabajo del mecanismo de persistencia debemos hacer algo de trabajo extra.

Cuando miramos hacia los [DbContext](http://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext%28v=vs.103%29.aspx "DbContext") de Entity Framework, vemos que son una implementación del patrón [Unit of Work](http://martinfowler.com/eaaCatalog/unitOfWork.html "Unit of Work") (en adelante UoW). Podemos usar estos contextos de trabajo directamente y aprovechar las ventajas del patrón directamente y sin más trabajo extra.

La desventaja de usar directamente DbContext es que su implementación permanece ligada a Entity Framework y por tanto la división de una aplicación en capas y la separación de responsabilidades es más complicada si no hacemos un poco de trabajo extra.

En este artículo vamos a ver como realizar una abstracción de un DbContext de manera que nos permita respetar los principios de una arquitectura orientada al dominio y de esta forma aislemos nuestro modelo de datos de Entity Framework, facilitemos la inyección de dependencias y la realización de pruebas unitarias.

La siguiente interfaz representa el contrato para la implementación del patrón Unit of Work. Normalmente se colocará en la capa de Dominio de nuestra aplicación, facilitando de esta forma la labor de aislar el mecanismo de persistencia utilizado de las operaciones que debe cumplir.

{% highlight javascript %}
namespace MyApp.Domain.UnitOfWork
{
    public interface IUnitOfWork:IDisposable
    {
        //Commit sobre la base de datos. Si hay un problema de concurrencia lanzará una excepción
        void Commit();
        //Commit sobre la base de datos. Si hay un problema de concurrencia "refrescará" los datos del cliente. Aproximación "Client wins"
        void CommitAndRefreshChanges();
        //Rollback de los cambios que se han producido en la Unit of Work y que están siendo observados por ella
        void Rollback();
    }
}
{% endhighlight %}

Los tres métodos principales de la interfaz, crean el contexto común que se debería implementar sea cual sea el mecanismo de persistencia utilizado, sea Entity Framework o cualquier otro.

Ahora vamos a crear las entidades que formarán nuestro modelo de datos simulando un sistema de Blogging. La definición de estas entidades también se ubicaría en la capa de Dominio.

{% highlight javascript %}
namespace MyApp.Domain.EntityModel
{
    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Description { get; set; }
        public string Text { get; set; }
        public bool IsPostCommentsClosed { get; set; }
        public int Visits { get; set; }
        public int CategoryId { get; set; }
        public DateTime DateCreated { get; set; }

        public virtual Category Category { get; set; }
        public virtual ICollection<Comment> Comments { get; set; }

    }
}

namespace MyApp.Domain.EntityModel
{
    public class Category
    {
        public int CategoryId { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public virtual ICollection<Post> Posts { get; set; }
    }
}

namespace MyApp.Domain.EntityModel
{
    public class Comment
    {
        public int CommentId { get; set; }
        public string Message { get; set; }
        public int PostId { get; set; }
        public virtual Post Post { get; set; }
    }
}
{% endhighlight %}

Hasta ahora, en realidad no tenemos ningún componente ligado a Entity Framework. Lo que hemos definido son simplemente interfaces a cumplir por nuestro mecanismo de persistencia y clases POCO que representarán a nuestras entidades.

Como siguiente paso crearemos otra interfaz que sí que representará el contrato que ha de cumplir una implementación concreta de Entity Framework.

{% highlight javascript %}
namespace MyApp.Infraestructure.UnitOfWork
{
    public interface IEntityFrameworkUnitOfWork: IUnitOfWork
    {
        IDbSet<TEntity> Set<TEntity>() where TEntity : class;
        void Attach<TEntity>(TEntity item) where TEntity : class;
        void SetModified<TEntity>(TEntity item) where TEntity : class;
        IEnumerable<TEntity> ExecuteQuery<TEntity>(string sqlQuery, params object[] parameters);
        int ExecuteCommand(string sqlCommand, params object[] parameters);
    }
}
{% endhighlight %}

Esta interfaz hereda de IUnitOfWork y añade los métodos necesarios para el trabajo con Entity Framework. Si eligiésemos cualquier otro mecanismo de persistencia, añadiríamos aquí los métodos propios de dicho mecanismo. A este componente ya la ubicaríamos en la capa de Infraestructura de nuestra aplicación.

Una vez tenemos la interfaz con las operaciones necesarias de Entity Framework, crearemos otra que herede de esta y defina el contrato real para la UoW de nuestro Blog.

Esta última interfaz podemos usarla para crear mocks para testeo, implementar la dependencia con el ORM seleccionado, … etc.

{% highlight javascript %}
namespace MyApp.Infraestructure.UnitOfWork
{
    public interface IBlogUnitOfWork : IEntityFrameworkUnitOfWork
    {
        IDbSet<Post> Posts {get; }
        IDbSet<Category> Categories {get; }
        IDbSet<Comment> Comments { get; }
    }
}
{% endhighlight %}

Ya casi estamos. Por último, implementemos la UoW ligada a Entity Framework y que heredará de DbContext e implementará todo lo definido en IBlogUnitOfWork

{% highlight javascript %}
namespace MyApp.Infraestructure.UnitOfWork
{
    public class BlogUnitOfWork : DbContext, IBlogUnitOfWork
    {
        public BlogUnitOfWork() { }

		// Implementación de IBlogUnitOfWork

		IDbSet<Post> posts;
		public IDbSet<Post> Posts
		{
			get
			{
				if (posts == null)
				posts = base.Set<Post>();
				return posts;
			}
        }


		IDbSet<Category> categories;
		public IDbSet<Category> Categories
		{
			get
			{
				if (categories == null)
			    categories = base.Set<Category>();
			    return categories;
			}
		}


		IDbSet<Comment> comments;
		public IDbSet<Comment> Comments
		{
		    get
			{
				if (comments == null)
				comments = base.Set<Comment>();
				return comments;
			}
		}

        // Implementación de IEntityFrameworkUnitOfWork

		public new IDbSet<TEntity> Set<TEntity>() where TEntity : class
		{
			return base.Set<TEntity>();
		}

		public void Attach<TEntity>(TEntity entity) where TEntity: class
		{
			if (base.Entry<TEntity>(entity).State == EntityState.Detached)
			{
				base.Set<TEntity>().Attach(entity);
			}
        }

        public IEnumerable<TEntity> ExecuteQuery<TEntity>(string sqlQuery, params object[] parameters)
        {
            return base.Database.SqlQuery<TEntity>(sqlQuery, parameters);
        }

        public int ExecuteCommand(string sqlCommand, params object[] parameters)
        {
            return base.Database.ExecuteSqlCommand(sqlCommand, parameters);
        }

		// Implementación de IUnitOfWork

		public void SetModified<TEntity>(TEntity entity) where TEntity : class
		{
			base.Entry<TEntity>(entity).State = EntityState.Modified;
		}

		public void Commit()
		{
			base.SaveChanges();
		}

        public void CommitAndRefreshChanges()
        {
			bool saveFailed = false;

            do
            {
                try
                {
                    base.SaveChanges();

                    saveFailed = false;

                }
                catch (DbUpdateConcurrencyException ex)
                {
                    saveFailed = true;

                    ex.Entries.ToList()
                              .ForEach(entry =>
                               {                                   
							       entry.OriginalValues.SetValues(entry.GetDatabaseValues());
                               });
                }
            } while (saveFailed);
        }

        public void Rollback()
        {
            base.ChangeTracker.Entries()
                              .ToList()
                              .ForEach(entry => entry.State = System.Data.EntityState.Unchanged);
        }
       
		// Sobreescibimos OnModelCreating de DdContext

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
		{
            this.Configuration.LazyLoadingEnabled = false;

			modelBuilder.Conventions.Remove<OneToManyCascadeDeleteConvention>();

            modelBuilder.Entity<Post>().Property(po => po.Text).IsMaxLength();
        }

		public new void Dispose()
		{
			base.Dispose();
		}
    }
}
{% endhighlight %}

Y ya estamos!!

Con esto ya hemos descompuesto el DbContext inicial en N componentes que nos permiten separar las responsabilidades y aislar al ORM de persistencia del Modelo de datos. Esto es simplemente una manera de hacerlo, se le pueden dar más vueltas o menos. Obviamente para aplicaciones sencillas ligadas totalmente a .NET y en las que no se pretenda cambiar el ORM de persistencia podemos saltarnos todos estos pasos y trabajar directamente con DbContext.

En el [siguiente post](../../../Post/GetPostByCode/el_patron_repositorio_con_entity_framework_code_first "El patrón Repositorio"), veremos como encajar esta UoW con el uso de Repositorios ya que estos patrones van de la mano.

Hasta pronto!!

