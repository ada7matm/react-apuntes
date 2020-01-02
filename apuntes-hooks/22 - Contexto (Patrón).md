# Contexto (Patrón)

Un patrón en desarrollo es una solución común a un problema recurrente, que ya fue probada y utilizada en muchísimos casos y se ha encontrado que es útil a la hora de resolverlo. Conocer (y aplicar) distintos patrones acelera nuestra velocidad de desarrollo, ya que no tenemos que estar pensando e implementando soluciones desde cero, con todo el tiempo y el testeo y debuggeo que eso implica.

Con respecto a contextos en React, hay un patrón muy utilizado que trae varias ventajas. Para construirlo, necesitamos seguir los siguientes pasos:

<br />

## Pasos para crear el patrón

### 1. Crear un archivo separado

Por lo general, el archivo va a llamarse `nombre del contexto + Context`. Por ejemplo:

  - AppContext
  - LoginContext
  - AdminContext
  - etc
  
Estos archivos se guardan dentro de una carpeta `Contexts` dentro de `components`. 

<br />

### 2. Crear un contexto

Dentro de nuestro archivo, tenemos que crear nuestro contexto:

```javascript
import React from 'react'

const MiContexto = React.createContext()
```

<br />

### 3. Crear un componente de clase

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  render(){
    return ()
  }
}

export default MiProvider
```

Si bien este componente tiene en su nombre `Provider`, no hay que dejarse confundir por el mismo (tranquilamente podría tener otro nombre). El `Provider` real es el que sale de `MiContexto.Provider`, ese es el que ofrece la posibilidad de compartir estados. Nuestro componente (`MiProvider`) es un componente contenedor (o *wrapper*, en inglés). ¿Qué significa esto? Que *lo único que hace es contener (o envolver) a otro componente*. Esa es toda su responsabilidad y función, no renderiza nada, simplemente contiene a otro componente. Ya veremos las ventajas de esto, pero el componente contenedor es un patrón también muy utilizado dentro de React.

<br />

### 4. Agregar el `Provider` dentro del método `render`

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  render(){
    return (
      <MiContexto.Provider></MiContexto.Provider>
    )
  }
}

export default MiProvider
```

De nuevo, `MiContexto.Provider` es nuestro `Provider` real. Si nos fijamos bien, `MiProvider` *contiene* a `MiContexto.Provider`

<br />

### 5. Agregar el `this.props.children` dentro del `Provider`

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  render(){
    return (
      <MiContexto.Provider>
        {this.props.children}
      </MiContexto.Provider>
    )
  }
}

export default MiProvider
```

Al hacer esto lo que permitimos es que los componentes hijos que anidemos en nuestro componente `MiProvider`, cuando lo utilicemos, pasen a incluirse realmente en el `Provider` verdadero. De esta forma, "tomamos" los componentes hijos del componente contenedor y los "inyectamos" en el `Provider`, que es lo que va a permitir compartir estados en estos componentes.

<br />

### 6. Definir los estados y métodos que vamos a necesitar

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  state = {
    algunEstado: 'algun valor'
  }
  cambiarEstado = nuevoValor => {
    this.setState({ algunEstado: nuevoValor })
  }
  render(){
    return (
      <MiContexto.Provider>
        {this.props.children}
      </MiContexto.Provider>
    )
  }
}

export default MiProvider
```

Todos los estados y métodos que necesitemos que nuestro `Provider` comparta necesitamos definirlo en nuestro componente contenedor (`MiProvider`). Es importante notar que si bien tenemos que definir todo lo que queremos compartir, no todo lo que definamos necesitamos compartirlo. Es posible que necesitemos estados o métodos propios a nuestro contenedor que no deseamos que los demás componentes accedan, y esto es perfectamente válido.

<br />

### 7. Definir el prop `value` en el `Provider` y pasarle un objeto

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  state = {
    algunEstado: 'algun valor'
  }
  cambiarEstado = nuevoValor => {
    this.setState({ algunEstado: nuevoValor })
  }
  render(){
    return (
      <MiContexto.Provider
        value={{}}
      >
        {this.props.children}
      </MiContexto.Provider>
    )
  }
}

export default MiProvider
```

<br />

### 8. Definir como propiedades del objeto del `value` aquellas cosas que queremos compartir

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  state = {
    algunEstado: 'algun valor'
  }
  cambiarEstado = nuevoValor => {
    this.setState({ algunEstado: nuevoValor })
  }
  render(){
    return (
      <MiContexto.Provider
        value={{
          algunEstado: this.state.algunEstado
          cambiarEstado: this.cambiarEstado
        }}
      >
        {this.props.children}
      </MiContexto.Provider>
    )
  }
}

export default MiProvider
```

Este paso es fundamental. No basta con declarar los estados y los métodos que queremos compartir en nuestro componente, también *tenemos que indicar que queremos compartirlo*. Como quien pone a disposición de los `Consumer` los estados, tenemos que definirlo dentro de nuestro `Provider` real (`MiContexto.Provider`). Para compartir un valor, necesitábamos declarar el prop `value`.

Ahora bien, como por lo general tenemos muchas cosas que compartir (muchos estados y métodos), y a `value` sólo podemos pasarle un valor, para solucionar esto lo que se hace es pasarle un objeto. Un objeto, al ser una estructura de datos, puede contener muchos datos, que es algo que nos viene bien para esta situación. Por lo tanto, la estrategia es asignarle una propiedad al objeto por cada cosa que queremos compartir, y al valor de dicha propiedad le asignamos aquello que queremos compartir (un estado, un método, un valor).

Las propiedades no tienen necesariamente que tener los mismos nombres que aquello que comparten, pero hacerlo nos evita tener que estar pensando en nombres alternativos, y se presta un poco menos a confusión.

Es importante entender que como este objeto es el que comparte el `Provider`, cuando accedemos desde el `Consumer` a lo que hay en el `value`, esto va a ser un objeto, y por lo tanto, cuando queramos usar cosas de ese objeto, **tenemos que usar los nombres de las propiedades del objeto**, y no los nombres de las cosas en la clase.

Por ejemplo, si tenemos lo anterior pero con otro nombre:

```javascript
<MiContexto.Provider
  value={{
    algunEstado: this.state.algunEstado
    modificarEstado: this.cambiarEstado
  }}
>
```

Desde el `Consumer`, si queremos utilizar el método de la clase `cambiarEstado`, tenemos que accederlo con el nombre de la propiedad del objeto al que está asignado, en este caso, `modificarEstado`.

<br />

### 9. Exportar el `Consumer` como export nombrado 

```javascript
import React from 'react'

const MiContexto = React.createContext()

class MiProvider extends React.Component {
  state = {
    algunEstado: 'algun valor'
  }
  cambiarEstado = nuevoValor => {
    this.setState({ algunEstado: nuevoValor })
  }
  render(){
    return (
      <MiContexto.Provider
        value={{
          algunEstado: this.state.algunEstado
          cambiarEstado: this.cambiarEstado
        }}
      >
        {this.props.children}
      </MiContexto.Provider>
    )
  }
}

export const MiConsumer = MiContexto.Consumer
export default MiProvider
```

<br />

### 10. Importar y utilizar nuestro componente `Provider`

```javascript
import React from 'react'
import MiProvider from 'components/Contexts/MiContexto'

const App = () => (
  <MiProvider>
    <UnComponente />
    <OtroComponente />
  </MiProvider>
)
```

En este paso se ve la función de contenedor o *wrapper* que tiene nuestro componente. Lo que hace `MiProvider` es "envolver" nuestro `Provider` real, ocultarlo de la vista, tomar los componentes que tiene anidados (en este caso `UnComponente` y `OtroComponente`) y pasárselos al `Provider` real mediante `props.children`.

<br />

### 11. Importar y utilizar nuestro componente `Consumer`, desestructurando las propiedades que necesitemos

```javascript
import React from 'react'
import { MiConsumer } from 'components/Contexts/MiContexto'

const AlgunComponente = () => (
  <MiConsumer>
    {
      value => <AlgunOtroComponente algunProp={value.algunEstado} />
    }
  </MiConsumer>
)
```

Esta parte no difiere mucho de cómo usamos el `Consumer` realmente. En el parámetro `value` accedemos a un objeto, por lo tanto podemos utilizar sus propiedades, que declaramos previamente. También, como tenemos un objeto, podemos aprovecharlo y desestructurarlo:

```javascript
import React from 'react'
import { MiConsumer } from 'components/Contexts/MiContexto'

const AlgunComponente = () => (
  <MiConsumer>
    {
      ({algunEstado}) => <AlgunOtroComponente algunProp={algunEstado} />
    }
  </MiConsumer>
)
```

<br />

### Resumiendo

1. Crear un archivo separado
2. Crear un contexto
3. Crear un componente de clase
4. Agregar el `Provider` dentro del método `render`
5. Agregar el `this.props.children` dentro del `Provider`
6. Definir los estados y métodos que vamos a necesitar
7. Definir el prop `value` en el `Provider` y pasarle un objeto
8. Definir como propiedades del objeto del `value` aquellas cosas que queremos compartir
9. Exportar el `Consumer` como export nombrado 
10. Importar y utilizar nuestro componente `Provider`
11. Importar y utilizar nuestro componente `Consumer`, desestructurando las propiedades que necesitemos


<br />

## Ventajas

Si bien parecen muchos pasos y trabajo extra para poder utilizar contextos, este patrón tiene una serie de ventajas que lo compensan:

### Separación de componentes

Al tener toda la lógica (estados y métodos) propios de un contexto en un componente aparte, evitamos la confusión de estar mezclando las cosas propias de un contexto con las cosas propias de un componente (y ni hablar si tuviésemos varios contextos en un mismo componente, entonces la mezcla y la confusión se incrementarían enormemente).

### Separación de responsabilidades

Un componente debería siempre en lo posible tener una única responsabilidad (no hacer una única acción en particular, sino encargarse de una única funcionalidad de la aplicación). Esto es deseable porque facilita las cosas muchísimo a la hora de analizar, pensar, y entender un componente, y reduce enormemente la carga cognitiva y el tiempo que necesitamos para hacerlo. De esta forma, tener UN componente que se encargue de la lógica de UN contexto, es mucho mejor que tener todos los contextos mezclados en un componente.

### Centralización de lógica

Al tener la lógica en un sólo lugar, ya sabemos dónde encontrarla cuando necesitemos hacer una modificación, o cuando necesitamos ver qué cosas está compartiendo.

### Sintaxis más limpia

Por último, y no menos importante, poder utilizar el `Provider` como un único componente, sin necesidad de estar declarando el `value` y los estados y métodos a compartir en el mismo componente, hace que el código quede mucho más reducido, simple y fácil de leer, además de permitir mover con mucha mayor comodidad el `Provider` en caso de que lo necesitemos (si estaría todo junto y mezclado, tendríamos que mover estado y métodos también).
