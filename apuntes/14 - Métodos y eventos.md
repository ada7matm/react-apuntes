# Métodos y eventos

## Métodos

Al igual que un objeto (una clase no es más que un objeto en última instancia) una clase puede tener métodos, es decir, acciones que puede realizar. Ya vimos un método que necesita todo componente, `render`, y que se encarga de devolver la lista de elementos que el componente tiene que renderizar. La sintaxis para otros métodos es la misma, dentro de las llaves de la clase, se escribe el nombre del método, paréntesis, donde van los parámentros, en caso de que los tenga, y luego llaves, donde va el cuerpo de instrucciones que se ejecuta al llamar el método:

```javascript
class MiComponente extends React.Component {
  miMetodo() {
    // acciones del metodo
  }
  render() {
    // ...
  }
}
```

A diferencia de un objeto, los métodos de una clase no se separan con coma. Esta sintaxis tiene un problema, y es que no asegura cuando se hace uso de un método, dentro de ese método, `this` refiera a la clase.

Por ejemplo, 

```javascript
class MiComponente extends React.Component {
  miMetodo() {
    console.log(this.props.name)
  }
  render() {
    return (
      <div onClick={this.miMetodo} />
    )
  }
}
```

si hacemos click en el `div de este componente, nos va a tirar que `props` no es una propiedad de `undefined`. Esto es porque el `this` del método `render` sí refiere a la clase, mientras que el del `console.log`. El problema es que `this` es una palabra **contextual**, su valor depende de dónde está siendo llamada.

Para evitar esto, tenemos que usar en los métodos la sintaxis de propiedad de clase, junto a la de funciones flecha. Es decir, asignarle una propiedad de la clase una función, es decir:


```javascript
class MiComponente extends React.Component {
  miMetodo = () => {
    console.log(this.props.name)
  }
  render() {
    return (
      <div onClick={this.miMetodo} />
    )
  }
}
```

De esta forma, `this` siempre refiere a la clase. Esto se debe a que las funciones flecha no crean su propio contexto, por lo que siempre utilizan la referncia al `this` del contexto superior desde donde son llamadas.

El método `render` no lo ponemos de esta forma y sí lo usamos con la sintaxis normal por una cuestión de performance. Este método, dependiendo el caso, puede llegar a tener que ejecutarse muchas veces. La función flecha es una función que se crea cada vez que se ejecuta, por lo tanto es una pequeña porción de memoria que ocupa y se va acumulando en caso de que haya muchas. En la práctica esto es pocas veces evidente, pero es una buena costumbre ya prevenir esto simplemente teniendo en cuenta este detalle. De esta forma, tenemos lo mejor de ambos mundos: `this` no contextual y performance.


## Eventos

Un evento es algo que se ejecuta cuando sucede una cierta acción o pasas algo. Es la forma mediante la cual el usuario interactúa con la interfaz: con clicks, touchs, movimientos de mouse, teclas presionadas, etc. Podemos definir un evento un callback que va ejecutarse dentro de elementos HTML. Los nombres por lo general son bastante similares a los que ya conocemos desde HTML, solo que en `camelCase`:

  * onClick
  * onMouseOver
  * onKeyPressDown
  * onLoad
  * etc

Para utilizarlos, tenemos que asignarlos a un elemento HTML. **Asignarlos a un componente directamente no tiene efecto**, puesto que un componente es un conjunto de elementos, React no sabe a cuál de todos esos tiene que asociar dicho evento. El callback que definimos tiene que ser la referencia a un método, o puede ser la declaración de una función en línea. Volviendo al caso anterior:


```javascript
class MiComponente extends React.Component {
  miMetodo = () => {
    console.log(this.props.name)
  }
  render() {
    return (
      <div onClick={this.miMetodo} />
    )
  }
}
```

llamamos al `this.miMetodo` como callback del `onClick` del `div`. Si queremos pasarle un parámetro, no podemos hacer lo siguiente:

```javascript
class MiComponente extends React.Component {
  miMetodo = (parametro) => {
    console.log(parametro)
  }
  render() {
    return (
      <div onClick={this.miMetodo("valor")} />
    )
  }
}
```

Porque al hacer `this.miMetodo("valor")` estamos invocando directamente la función, y esta va a ejecutarse ni bien se interpreta. Lo que tenemos que hacer es definir un callback, es decir, dar la definición de un función. Como nuestro método ya está definido, necesitamos una función que llame al mismo con su parámentro:

```javascript
class MiComponente extends React.Component {
  miMetodo = (parametro) => {
    console.log(parametro)
  }
  render() {
    return (
      <div onClick={() => this.miMetodo("valor")} />
    )
  }
}
```
Por lo tanto, esto lo hacemos solamente cuando necesitamos que nuestro callback tenga uno o más parámetros.
