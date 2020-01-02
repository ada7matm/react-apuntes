# Contexto

Existen ocasiones en que las estrategias de composición se quedan cortas a la hora de solucionar el problema de `prop drilling` especialmente cuando los componentes están muy alejados en la jerarquía de nuestra aplicación. Para evitar este inconveniente, tenemos un recursos a nuestro disposición: el **contexto**.

*Un contexto es una forma de compartir información entre componentes sin tener que estar pasándoselos mediantes props, saltando todos los componentes intermedios en la jerarquía*. 

Se puede pensar al contexto como una especia de estado "global" disponible para cualquier componente *en una cierta jerarquía o árbol de componentes*. Esto es fundamental entenderlo, y ya lo veremos más en detalle, pero un concepto importante del contexto es que ese estado que comparte está disponible para un *subconjunto* de componentes (los que definamos), no *necesariamente* para todos los componentes de la aplicación, a menos que así lo deseemos.

Para crear un contexto, simple tenemos que utilizar el método de React, `createContext`, de la siguiente forma:

```javascript
const MyContext = React.createContext()
```

`MyContext` es un objeto que nos provee de dos propiedades, el `Provider` (proveedor) y el `Consumer` (consumidor). El `Provider` es el que comparte la información dentro de los componentes incluido dentro del contexto, es decir, pone a disposición o provee la información a ser compartida. Los `Consumer` son aquellos que acceden a esa información dispuesta por el proveedor. 

**Con el Provider definimos la información a compartir y con los Consumer accedemos a la misma.**

Una vez que tenemos nuestro contexto, podemos utilizar nuestro `Provider` en cualquier componente que deseemos colocarlo. Simplemente, lo que tenemos que hacer es declararlo y anidar dentro suyo los componentes que van a estar incluidos dentro del contexto:

```javascript
const MyContext = React.createContext()

const MyComponent = () => (
  <>
    <MyContext.Provider>
      {/** Componentes que pueden acceder al contexto */}
    </MyContext.Provider>
    // Componentes que no pueden acceder al contexto
    <OtherComponent />
  </>
)  
```

Es importante notar que sólo aquellos componentes que estén dentro de las etiquetas de apertura y cierre del `Provider` van a tener acceso a los valores que comparta, así como también sus componentes hijos (dado que lo que accede un componente lo puede pasar a sus componentes hijos, el flujo de datos en una aplicación de React es top-down, de arriba a abajo). Por lo tanto, un `Provider` contiene *una cierta jerarquía de componentes*, aquellos que queden fuera o no estén incluidos dentro de dicha jerarquía no podrán acceder a los valores que el `Provider` disponga. Dentro del `Provider` se pueden incluir todos los componentes que deseemos.

Por su parte, para declarar (al menos) un `Consumer`, tenemos que hacerlo en algún componente *que esté incluido dentro del contexto, es decir, de la jerarquía que contiene el `Provider`. Si incluimos un `Consumer` en un componente que no se encuentra dentro de esta jerarquía, no podremos acceder a la información compartida. Para hacer esto, de la misma forma que usamos el `Provider`, creamos un `Consumer` en un componente. 

```javascript
const MyContext = React.createContext()

const MyComponent = () => (
    <MyContext.Provider>
      <MyChildComponent />
    </MyContext.Provider>
)

const MyChildComponent = () => (
  <MyGrandChildComponent />
) 

const MyGrandChildComponent = () => (
  <MyContext.Consumer>
    {/** Aca accedemos a los datos que comparte el Provider */}
  </MyContext.Consumer>
)
```

**Dentro de la jerarquía que contiene un Provider podemos tener múltiples Consumer**

Una vez que ya tenemos un `Provider` y al menos un `Consumer`, ya podemos compartir información entre estos sin tener que pasar por todos los componentes intermedios que hay entre ambos.

Para hacer eso, tenemos que declarar qué información queremos compartir. Esto lo hacemos en el `Provider`. El `Provider` tiene una prop especial llamada `value`, cuyo valor será aquello que el `Provider` ofrecerá a los `Consumer` para que puedan utilizarlo. Como toda prop, el valor puede ser cualquier tipo de dato válido de JS: número, string, booleano, array, objeto, función. Esto se escribe de la siguiente forma:

```javascript
<Context.Provider value={/** valor a compartir */}>
  {/** componentes dentro del contexto */}
</Context.Provider>
```

Por ejemplo, si quisiéramos compartir una string, tendríamos que hacer lo siguiente:

```javascript
const MyContext = React.createContext()

const MyComponent = () => (
    <MyContext.Provider value='Ada Lovelace'>
      <MyChildComponent />
    </MyContext.Provider>
)

const MyChildComponent = () => (
  <MyGrandChildComponent />
) 

const MyGrandChildComponent = () => (
  <MyContext.Consumer>
    {/** Aca accedemos a los datos que comparte el Provider */}
  </MyContext.Consumer>
)
```

Ahora nuestro `Provider` ya está compartiendo un valor mediante el prop `value`. Para acceder a este valor, tenemos que hacerlo desde algún `Consumer`. El `Consumer` dentro suyo toma un callback. Como esto es sintaxis de JS embebida en sintaxis JSX, necesitamos separarla mediante llaves:

```javascript
<Context.Consumer>
  {() => ()}
</Context.Consumer>
```

Dicho callback acepta un parámetro, que va a contener el valor compartido en el prop `value` del `Provider`. Por lo tanto, si definimos


```javascript
<Context.Consumer>
  {value => ()}
</Context.Consumer>
```

El parámetro `value` va a tener aquello que el `Provider` provea mediante su prop `value`. Dentro de dicho callback, luego, podemos hacer lo que queramos con esa información, por ejemplo, renderizar un componente pasándole dicho value como prop.

Volviendo a nuestro ejemplo anterior:

```javascript
const MyContext = React.createContext()

const MyComponent = () => (
    <MyContext.Provider value='Ada Lovelace'>
      <MyChildComponent />
    </MyContext.Provider>
)

const MyChildComponent = () => (
  <MyGrandChildComponent />
) 

const MyGrandChildComponent = () => (
  <MyContext.Consumer>
    {value => (
      <p>{value}</p>
    )}  
  </MyContext.Consumer>
)
```

Aquí estamos accediendo desde nuestro `Consumer` al `value` que comparte el `Provider`, y utilizándolo para renderizar un elemento `p` con el valor de dicho `value` como texto. En este ejemplo se puede ver bien que el componente intermedio `MyChildComponent` no necesita pasar el prop, sino que accedemos directamente al mismo desde el `Consumer`.

<br />

## Dónde colocar el Provider?

El criterio para determinar esto es el mismo que utilizamos a la hora de "elevar el estado". De hecho, podemos pensar al contexto como una forma de elevar el estado de un componente para que otros componentes puedan accederlo, con la ventaja de que no tenemos que pasar los props por todos los componentes intermedios en la jerarquía.

Por lo tanto, lo que tenemos que considerar es cuál es el *componente ancestro común más inmediato*, es decir, el componente que contiene a todos aquellos componentes que necesitan compartir un cierto estado, y que está primero en la jerarquía. La idea de esto es que el `Provider` sólo contenga dentro de su jerarquía a aquellos componentes que van a tener que estar al tanto del estado compartido, y dejar afuera a aquellos componentes que no les interesa ni les incumbe dicho estado. De esta forma, separamos responsabilidades. Para esto entonces tenemos que:

  * identificar cuáles van a ser aquellos componentes que necesiten compartir un cierto estado. Estos componentes van a contener los `Consumer`.
  * una vez hecho esto, tenemos que empezar a ascender en la jerarquía y ver cuál es el primer componente que encontramos que contiene a todos estos. Este componente será el que contenga al `Provider`.
  
<br />

## Resumiendo

  **Contexto**

  * Un contexto es una forma de compartir estados entre dos o más componentes, sin tener que estar pasando props por todos los componentes intermedios de la jerarquía
  * Un contexto se crea con el método `React.createContext()`
  * Un contexto contiene a un subárbol o subjerarquía de componentes. Sólo los componentes que estén dentro de esta jerarquía podrán acceder a los estados compartidos
  * Podemos tener muchos contextos en una aplicación
  * Cada Contexto debería referir a una cierta cosa en específico, a un tipo de estado, feature, o característica de la aplicación que necesite ser compartido
  * Podemos anidar contextos dentro de otros en cualquier momento de la jerarquía
  * Un contexto ofrece dos propiedades: el `Provider` y el `Consumer`.
  * Podemos anidar distintos `Providers` y `Consumers` entre sí si es necesario 

  **Provider**  
  
  * Sólo puede haber un `Provider` por Contexto, pero muchos `Consumer`.
  * El `Provider` es el que provee o distribuye el estado a ser compartido
  * Para declarar un `Provider` simplemente necesitamos usar el componente `<Context.Provider></Context.Provider>`
  * Dentro del `Provider` incluimos aquellos componentes que queramos que tengan acceso al estado compartido
  * Como el flujo de datos en React es top-down (de arriba hacia abajo), aquellos componentes hijos de los componentes que estén declarados dentro de las etiquetas del `Provider` también tendran acceso a lo que el `Provider` comparta
  * Para compartir un valor en el `Provider` tenemos que declarar la prop `value y pasarle el valor que deseemos
  * El criterio que utilizamos para colocar a nuestro `Provider` es el mismo que utilizamos a la hora de elevar el estado de un componente, es decir, buscando el *componente ancestro común más inmediato* de los componentes que necesitan compartir el estado
  
  **Consumer**
  
  * El `Consumer` es el que accede al estado que el `Provider` comparte
  * El `Consumer` debe estar en un componente que se encuentre dentro de la jerarquía del `Provider`
  * Para declarar un `Consumer` en un componente que necesita acceder al estado que el `Provider` comparte, tenemos que utilizar el componente `<Context.Consumer></Context.Consumer>`
  * Dentro del `Consumer` va un callback
  * Dicho callback toma un parámetro, ese parámetro va a contener el valor que el `Provider` ofrezca
  * Con ese parámetro luego podemos hacer lo que deseemos, como utilizarlo para algún componente
  * Lo que el callback incluido dentro del `Consumer` devuelva va a ser renderizado, si devuelve un componente este va a mostrarse
