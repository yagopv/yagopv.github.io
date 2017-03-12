---
layout: post
title: El patrón Agregado (Aggregate)
date: '2012-02-29 09:08:07'
tags:
- patterns
categories:
- .NET
---


El patrón Agregado es ampliamente utilizado en los modelos de datos basados en Diseños Orientados al Dominio (DDD).

Proporciona un forma de encapsular nuestras entidades y los accesos y relaciones que se establecen entre las mismas de manera que se simplifique la complejidad del sistema en la medida de lo posible.

En un modelo de datos existen múltiples relaciones entre las diferentes entidades y objetos que lo conforman. Estas relaciones se reflejan normalmente en la base de datos que se genera a partir del modelo.

El patrón **Agregado** trata de poner un poco de orden en este conjunto de relaciones que puede llegar a ser complicado de manejar.

El patrón **Agregado** trata de minimizar la complejidad del conjunto de relaciones que se establecen entre las entidades de un modelo de datos. ¿Cómo se consigue esto? … en primer lugar, se define un agregado como un conjunto de entidades que forman un todo. Estas entidades, en lo que se respecta a cambios sobre los datos, actúan como un único conjunto, **el Agregado**.

Cada agregado que se define tiene una **Entidad Raíz (Root)** de la que colgarán el resto de entidades del agregado. Si quiero modificar cualquier entidad del agregado, tiene que ser siempre a partir de la entidad raíz. Por tanto, es esta entidad raíz la única accesible desde el exterior del agregado y por tanto el resto de agregados que se quieran relacionar con la entidades de este, han de hacerlo siempre a través de la entidad raíz.

Si una entidad que forma parte de un agregado ha de persistirse contra la base de datos, esto siempre se debe hacer a través de la entidad raíz. Por tanto y muy importante, **sólo las entidades raíz han de tener repositorios de datos asociados (Patrón Repositorio), y sólo se han de definir servicios de aplicación para estos repositorios.**

Esto no quiere decir que las entidades y objetos que forman parte de un agregado y que no son la raíz no se puedan relacionar con otros agregados, pero de hacerlo, ha de ser a través de la entidad raíz de los mismos.

Por tanto, extrayendo las ideas principales:


- Un <strong>Agregado</strong> es un conjunto de entidades que trabajan juntas como un todo y que siempre se accederán a través de una <strong>Entidad</strong> denominada <strong>Raíz</strong></p>
- <p>La <strong>Raíz</strong> de un agregado tiene que ser una <strong>Entidad</strong></p>
- <p>Los objetos que forman parte de un agregado pueden ser  <strong>Entidades</strong> u <strong>Objetos Valor </strong></p>
- <p>Una  <strong>Entidad</strong> pueden mantener referencias a cualquier <strong>Raíz </strong>de un <strong>Agregado</strong> pero nunca a otra <strong>Entidad</strong> u <strong>Objeto Valor</strong> que no sea <strong>Raíz </strong>y que pertenezca a un agregado. Para acceder a los objetos que no son <strong>Raíz</strong> siempre debe hacerse a través de su <strong>Agregado</strong></p>
- <p>Sólo se ha de definir un <strong>Repositorio</strong> por <strong>Agregado</strong> ( ... y un <strong>Servicio de Aplicación</strong> normalmente)</p>


Vamos a ver un sencillo ejemplo que ilustre este concepto

```c
public class Cliente { 
  public int IdCliente {get; set;} 
  public string Nombre {get; set;} 
  public string Apellidos {get; set;} 
  public virtual ICollection<documento> DocumentosCliente {get; set;} 
  public void AddDocumento(Documento doc) { 
    if (doc == null) throw new ArgumentNullException("documento");
    doc.IdCliente = this.IdCliente;
    this.DocumentosCliente.Add(doc); 
  } 
  public Documento CrearDocumento(string doc) { 
    if (doc == null) throw new ArgumentNullException("documento"); 
    Documento nuevodoc = new Documento();
    nuevodoc.Documento = doc;
    nuevodoc.IdCliente = this.IdCliente;
    return nuevodoc; 
  } 
} 
public class Documento { 
  public int IdDocumento {get; set;} 
  public string NumeroDocumento {get; set;} 
  public int IdCliente {get; set;} 
}
```

Como se puede apreciar en este ejemplo. La entidad **Cliente** sería la entidad raíz de un agregado formado por **Cliente** y **Documento**.

Si quiero añadir un nuevo documento, siempre lo tendré que hacerlo a través de **Cliente**. Si quiero crear un nuevo documento, lo mismo, lo haré a través de la entidad **Cliente**.

Este es un ejemplo bastante sencillo. Ahora vamos a ver uno un poco más complejo (aunque no demasiado J) que añade lógica y comportamiento a la entidad además de encapsulación en un agregado.

```c
// Entidad raiz del agregado de Cuentas Bancarias
public class CuentaBancaria
{
    //Propiedades
    public int IdCuentaBancaria { get; set; }
    public NumeroCuentaBancaria NumeroCuentaBancaria { get; set; }
    public decimal Balance { get; private set; }
    public int IdCliente { get; set; }   
	
	//Propiedades de navegacion
    public virtual Cliente Cliente { get; private set; }
    HashSet<MovimientoCuenta> _movimientocuenta;
    public virtual ICollection<MovimientoCuenta> MovimientoCuenta
    {
        get
        {
            if (_movimientocuenta == null)
                _movimientocuenta = new HashSet<MovimientoCuenta>();
             return _movimientocuenta;
        }
        set
        {
            _movimientocuenta = new HashSet<MovimientoCuenta>(value);
        }
    }
	
	//Añadir importe a una cuenta bancaria
    public void DepositarImporte(decimal cantidad,string descripcion)
    {
        if (cantidad < 0) throw new ArgumentException("El importe es menor que cero");
        Balance += cantidad;
        this.MovimientoCuenta.Add(new MovimientoCuenta()
        {
            Fecha = DateTime.UtcNow,
            Cantidad = cantidad,
            DescripcionMovimiento = descripcion
        });        
    }
	
	//Extraer importe de una cuenta bancaria
    public void RetirarImporte(decimal cantidad,string descripcion)
    {
        if ( cantidad < 0 )  throw new ArgumentException("El importe es menor que cero");
        this.Balance -= cantidad;
        this.MovimientoCuenta.Add(new MovimientoCuenta()
        {
            Fecha = DateTime.UtcNow,
            Cantidad =- cantidad,
            DescripcionMovimiento = descripcion
        });
	}
	
    //Establecer el Cliente asociado
    public void AsociarCliente(Cliente cliente)
    {
        if (cliente == null)
        {
            throw new ArgumentException("El Cliente no está informado");
        }        
        this.Cliente = cliente;
    } 
}
 
//Entidad del Agregado de Cuentas Bancarias
public class MovimientoCuenta
{
	//Propiedades
	public int IdMovimientoCuenta {get; set;}    
    public DateTime Fecha { get; set; }
    public decimal Cantidad { get; set; }
    public string DescripcionMovimiento { get; set; }
    
    //Propiedades de Navegacion
    public int IdCuentaBancaria { get; set; }
    public virtual CuentaBancaria CuentaBancaria {get; set; }
}
 
//Objeto valor del Agregado de Cuentas Bancarias
public class NumeroCuentaBancaria
{
	//ctor&acute;s
    public NumeroCuentaBancaria(string numerooficina, string codigobanco, string numerocuenta, string digitocontrol)
    {
        NumeroOficina = numerooficina;
        CodigoBanco = codigobanco;
        NumeroCuenta = numerocuenta;
        DigitoControl = digitocontrol;
    }
    public NumeroCuentaBancaria() { }
    
    //Propiedades
    public string NumeroOficina { get; private set; }
    public string CodigoBanco { get; private set; }
    public string NumeroCuenta { get; private set; }
    public string DigitoControl { get; private set; }
}
 
//Entidad Cliente. Raiz del Agregado de Clientes
public class Cliente
{
	//Propiedades
    public int IdCliente {get; set; }
    public string Nombre { get; set; }
    public string Apellidos { get; set; }
    public string NombreCompleto
    {
        get
        {
            return string.Format("{0}, {1}", this.Apellidos, this.Nombre);
        }
        set { } 
    }
    public string Telefono { get; set; }
    public string Empresa { get; set; }
    public decimal LimiteCredito { get; set; }
    
    //Propiedades de navegacion
    public virtual Direccion Direccion { get; set; }
}
 
//Objeto valor del agregado de Clientes
public class Direccion
{
	// ctors
    public Direccion(string ciudad, string codigopostal, string textodireccion)
    {
        this.Ciudad = ciudad;
        this.CodigoPostal = codigopostal;
        this.TextoDireccion = textodireccion;
    }
    public Direccion() { }        
    
    // Propiedades
    public string Ciudad { get; private set; }
    public string CodigoPostal { get; private set; }
    public string TextoDireccion { get; private set; }
}
```

En este caso, estamos definiendo dos agregados que serán el agregado de **Cuentas Bancarias** y el agregado de **Clientes**

En el caso del agregado de cuentas, vemos que lo forman tres clases, dos entidades (**CuentaBancaria** y **MovimientoBancario**) y un objeto valor (**NumeroCuenta)**. La diferencia entre entidades y objetos valor es que las entidades tienen una identidad y los objetos valor no. Las entidades se traducen en tablas en la base de datos, sin embargo los objetos valor simplemente actúan como una conjunto de propiedades que se asocian a una entidad y que no tienen razón de ser por si solos, es decir, no tienen identidad.

La entidad raíz del agregado de cuentas es la **CuentaBancaria**. Esta cuenta bancaria se asocia con *N* **MovimientoBancario** y las cuentas bancarias tienen un **NumeroCuenta**.

Vemos como el acceso al resto de objetos del agregado es siempre a través de la entidad raíz, la **CuentaBancaria**, de la cual se extrae dinero (*RetirarImporte()*) o bien se deposita (*DepositarImporte()*). Vemos asimismo que la creación de entidades asociadas del mismo agregado también se lleva a cabo en la entidad raíz, en este caso en el interior de los métodos, aunque se podría crear un método factoría que crease los objetos.

Por otra parte, tenemos el agregado de Clientes, formado por una entidad raíz **Cliente** y por un objeto valor **Direccion**.

Es importante recalcar, como comentábamos al principio del post, que las relaciones entre agregados se establecerán a través de las entidades raíz. Esto se refleja con el método *AsociarCliente()*, en el que se asocia un Cliente con una **CuentaBancaria** y con el establecimiento de la propiedad de navegación **Cliente**

```c
public virtual Cliente Cliente { get; private set; }
```

Podríamos también asociar la entidad raíz **Cliente** con otras entidades que no sean raíz pero no podemos asociar entidades no raíz con otras entidades no raíz. Siempre debemos hacerlo a través de la raíz del agregado.

Por tanto, mantendremos dos agregados y si queremos crear repositorios de acceso a estos agregados, crearíamos dos repositorios, un repositorio de clientes y un repositorio de cuentas bancarias. Lo mismo aplicaría a la definición de servicios de aplicación para ser consumidos en el cliente que se está utilizando.

Hasta aquí este repaso al patrón agregado y a las posibilidades que nos ofrece para desarrollar modelos de datos de manera independiente al almacén de datos que se quiera usar.

Hasta pronto!!


