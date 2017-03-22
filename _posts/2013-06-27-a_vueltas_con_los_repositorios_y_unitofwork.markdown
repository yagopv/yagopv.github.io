---
layout: post
title: A vueltas con los Repositorios y UnitOfWork
date: '2013-06-27 02:07:00'
tags:
- asp mvc
- entity framework
categories:
- .NET
---


Hace un tiempo publiqué un par de posts acerca de cómo se pueden usar estos patrones con Entity Framework Code First.

En este nuevo artículo vamos a ver otra forma de hacerlo, ni mejor ni peor, sino diferente :) … Cuantas más opciones mejor.

Hace un tiempo publiqué un par de posts acerca del patrón [Repositorio](GetPostByCode/el_patron_repositorio_con_entity_framework_code_first) y [UnitOfWork](GetPostByCode/el_patron_unit_of_work_con_entity_framework_code_first). La implementación vista en ambos artículos es bastante común y se puede encontrar con facilidad en cualquier proyecto o artículo en la web.

<span style="font-size: 1em; line-height: 1.6em;">Como para casi todo, no es la única manera de hacerlo. En este post vamos a ver otra forma diferente que también me parece muy útil, sobre todo por su sencillez y que a mi entender encaja muy bien con tecnologías de desarrollo actuales en las que se tiende a incrementar la importancia del cliente frente al servidor y la comunicación mediante servicios REST además de la agilidad en el desarrollo, véase frameworks javascript como **AngularJs**, **Backbone**, **EmberJs**, **Meteor** y el pack **DurandalJs + WebAPI + Breeze**.</span>

<span style="font-size: 1em; line-height: 1.6em;">La estrategia será, en lugar de inyectar la unidad de trabajo en los repositorios, inyectaremos los repositorios en la unidad de trabajo. Esto nos permitirá además tener una única dependencia que referenciar en nuestra capa cliente (Por ejemplo, desde los controladores **ASP MVC**).</span>

<span style="font-size: 1em; line-height: 1.6em;">Vamos a dividir el post en tres secciones, la capa de **Dominio**, la capa de **Acceso a Datos** que contendrá las implementaciones exclusivas para Entity Framework de nuestro Dominio, y la capa **Cliente** en la que se demuestra como usar la implementación con ASP MVC.</span>


## La capa de Dominio

En el dominio de nuestra aplicación, como siempre, comenzamos definiendo nuestras entidades de datos:

Por ejemplo, definamos una entidad Artículo …

```c
public class Article
{
    [Key]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public int ArticleId { get; set; }

    [Required]
    [StringLength(200)]
    public string Title { get; set; }

    [Required]
    [StringLength(500)]
    public string Description { get; set; }

    [DataType(DataType.Html)]
    public string Text { get; set; }
}
```

… y una entidad perfil de usuario.

```c
public class UserProfile
{
    [Key]
    [DatabaseGeneratedAttribute(DatabaseGeneratedOption.Identity)]
    public int UserProfileId { get; set; }

    [Required]
    [StringLength(50)]
    public string UserName { get; set; }

    [Required]
    [DataType(DataType.EmailAddress)]
    [StringLength(200)]
    public string Email { get; set; }
}
```

A continuación vamos a definir el contrato que debe cumplir cada repositorio de nuestro dominio. Cada uno de nosotros definirá las operaciones que más le convengan.

```c
public interface IRepository<T> where T : class
{
    IQueryable<T> AsQueryable();
    IEnumerable<T> GetAll();
    IEnumerable<T> Find(Expression<Func<T, bool>> predicate);
    T FirstOrDefault(Expression<Func<T, bool>> predicate);
    T First(Expression<Func<T, bool>> predicate);
    T GetById(int id);

    void Add(T entity);
    void Delete(T entity);
    void Attach(T entity);                   
}
```

Ahora el contrato que debe cumplir nuestra unidad de trabajo y que usaremos para inyectar como dependencia en donde se requiera.

```c
public interface IUnitOfWork
{
    IRepository<Article> ArticleRepository { get; }
    IRepository<UserProfile> UserProfileRepository { get; }
	
    void Commit();
}
```

Con esto tenemos la capa de dominio lista, a continuación creamos otra nueva capa (nuevo proyecto) que implementará estos contratos usando Entity Framework como dependencia.


## La capa de acceso a datos

Por una parte implementaremos el repositorio genérico definido en el dominio en el paso anterior.

```c
public class Repository<TEntity> : IRepository<TEntity> where TEntity : class
{
    private readonly DbSet<TEntity> dbSet;

    public Repository(DbSet<TEntity> dbSet)
    {
        this.dbSet = dbSet;
    }

    public IQueryable<TEntity> AsQueryable()
    {
        return this.dbSet.AsQueryable();
    }

    public IEnumerable<TEntity> GetAll()
    {
        return this.dbSet;
    }

    public IEnumerable<TEntity> Find(System.Linq.Expressions.Expression<Func<TEntity, bool>> predicate)
    {
        return this.dbSet.Where(predicate);
    }

    public TEntity FirstOrDefault(System.Linq.Expressions.Expression<Func<TEntity, bool>> predicate)
    {
        return this.dbSet.Where(predicate).FirstOrDefault();
    }

    public TEntity First(System.Linq.Expressions.Expression<Func<TEntity, bool>> predicate)
    {
        return this.dbSet.Where(predicate).First();
    }

    public TEntity GetById(int id)
    {
        return this.dbSet.Find(id);
    }

    public void Add(TEntity entity)
    {
        this.dbSet.Add(entity);
    }

    public void Delete(TEntity entity)
    {
        this.dbSet.Remove(entity);
    }

    public void Attach(TEntity entity)
    {
        this.dbSet.Attach(entity);
    }
}
```

y a continuación la implementación de la unidad de trabajo.

```c
public class EFUnitOfWork : DbContext, IUnitOfWork
{
    private readonly Repository<Article> articleRepository;
    private readonly Repository<UserProfile> userProfileRepository;

    public EFUnitOfWork()
                : base("DefaultConnection")
    {
        this.articleRepository = new Repository<Article>(Articles);
        this.userProfileRepository = new Repository<UserProfile>(UserProfiles);
        // Estrategia de inicialización
	// DropCreateDatabaseAlways, MigrateDatabaseToLatestVersion  o lo que se prefiera, por ejemplo:
        //Database.SetInitializer(new MigrateDatabaseToLatestVersion<EFUnitOfWork, Migrations.Configuration>());
    }

    public DbSet<Article> Articles { get; set; }        
    public DbSet<UserProfile> UserProfiles { get; set; }

    public IRepository<Article> ArticleRepository
    {
        get { return articleRepository; }
    }

    public IRepository<UserProfile> UserProfileRepository
    {
        get { return userProfileRepository; }
    }

    public void Commit()
    {
        this.SaveChanges();                
    }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        Configuration.LazyLoadingEnabled = false;
        modelBuilder.Conventions.Remove<OneToManyCascadeDeleteConvention>();
    }
}
```

Con esto, ya esta lista nuestra implementación usando Entity Framework.

Con toda seguridad necesitaremos en cualquier aplicación medianamente seria, utilizar consultas más complejas contra la base de datos, por lo que deberíamos tener un mecanismo de extensión de nuestro repositorio genérico. Para ello podríamos contemplar un par de opciones:

#### *Métodos de extensión*

```c
public static class ArticleRepositoryExtension
{
    public static IEnumerable<Article> GetArticlesMatchingParams(this IRepository<Article>, int param1, string param2)
    {
        // La query y devolver el resultado
    }
}
```

Crearemos tantos métodos como operaciones complejas necesitemos.

#### *Nueva Implementación del repositorio genérico*

```c
public class ArticleRepository : Repository<Article>
{
    public IEnumerable<Article> GetArticlesMatchingParams(int param1, string param2)
    {
		// implementación del método
    }
	
    public override Add(TEntity entity)
    {
        // Sobreescribimos Add() de la clase base Repository<Article>
    }
}
```

Esta nueva implementación será la que inyecte en la unidad de trabajo en lugar del repositorio genérico ***Repository<article></article>***

```c
this.articleRepository = new ArticleRepository(Articles);
```

También podría crear un contrato *IArticleRepository* que heredase de *IRepository<article></article>* en el dominio y obligar a *ArticleRepository* a implementarlo … o lo que prefiera. Se puede complicar tanto como se desee :)


## La capa Cliente

Ahora será muy fácil inyectar nuestra unidad de trabajo en, por ejemplo, los controladores ASP MVC. Usando nuestro framework de inyección de dependencias favorito, el código en el controlador quedaría algo así (El ejemplo viene a ser una modificación del controlador *AccountController* que viene con la plantilla por defecto de ASP MVC 4 en VS2012 y que usaría nuestra implementación).

```c
[Authorize]
public class AccountController : Controller
{
    private IUnitOfWork UnitOfWork { get; set; }

    public AccountController(IUnitOfWork uow)
    {
        this.UnitOfWork = uow;
    }

	..... Resto acciones ...
	
	[HttpPost]
	[AllowAnonymous]
	[ValidateAntiForgeryToken]
	public ActionResult ExternalLoginConfirmation(RegisterExternalLoginModel model, string returnUrl)
	{
		string provider = null;
		string providerUserId = null;

		if (User.Identity.IsAuthenticated || !OAuthWebSecurity.TryDeserializeProviderUserId(model.ExternalLoginData, out provider, out providerUserId))
		{
			return RedirectToAction("Manage");
		}

		if (ModelState.IsValid)
		{
			// Insert a new user into the database

			UserProfile user = UnitOfWork.UserProfileRepository.FirstOrDefault(u => u.UserName.ToLower() == model.UserName.ToLower());
			 // Check if user already exists
			 if (user == null)
			 {
					// Insert name into the profile table
					UnitOfWork.UserProfileRepository.Add(new UserProfile { UserName = model.UserName });
					UnitOfWork.Commit();

					OAuthWebSecurity.CreateOrUpdateAccount(provider, providerUserId, model.UserName);
					OAuthWebSecurity.Login(provider, providerUserId, createPersistentCookie: false);

					return RedirectToLocal(returnUrl);
			 }
			 else
			 {
					ModelState.AddModelError("UserName", "User name already exists. Please enter a different user name.");
			 }
			
		}

		ViewBag.ProviderDisplayName = OAuthWebSecurity.GetOAuthClientData(provider).DisplayName;
		ViewBag.ReturnUrl = returnUrl;
		return View(model);
	}
	... Resto acciones ...
}
```

Como podemos ver, inyectando IUnitOfWork ya nos daría acceso a todos los repositorios y a la operación *Save()* para persistir los cambios en el contexto de trabajo. En la implementación que mostré en los post que comento al principio, se inyectarían cada uno de los repositorios necesarios, que a su vez tendrían acceso a la unidad de trabajo definidida en su interior.


## Ya está

Pues con esto ya tendríamos nuestra aplicación lista y dividida en tres capas.

– El Dominio con sus entidades y contratos

– La capa de Acceso a Datos, con su implementación particular para Entity Framework

– La Capa Cliente que consumirá la unidad de trabajo

Además, creando un proyecto de Visual Studio por capa, sólo tendré que referenciar la dll de Entity Framework en la capa de datos y por tanto la implementación será independiente del dominio de nuestra aplicación, si es que eso nos interesa.

<span style="font-size: 1em; line-height: 1.6em;">Hasta pronto!!</span>


