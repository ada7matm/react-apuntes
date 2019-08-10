# Inputs controlados

Supongamos que queremos tener un `input` que, a medida que se va escribiendo algo dentro de este, se vaya mostrando a su vez lo que se escribre en un elemento `p`, y que tengamos un botón para borrar todo escrito. 

Nuestro componente inicial sería algo así:

```javascript
const LiveText = () => (
  <>
    <input type='text' />
    <p></p>
    <button>Borrar</button> 
  </>
)
```

Como nuestro componente va a tener algo que va a ir cambiando (el texto que se muestra), y en React todo lo que cambia tiene que ser parte del estado de un componente, necesitamos refactorizarlo a un componente de clase:

```javascript
class LiveText extends React.Component {
  render() {
    return (
      <>
        <input type='text' />
        <p></p>
        <button>Borrar</button> 
      </>
    )
  }
}
```

El siguiente paso es agregarle nuestro estado:

```javascript
class LiveText extends React.Component {
  state = {
    text: ''
  }
  render() {
    return (
      <>
        <input type='text' />
        <p>{this.state.text}</p>
        <button>Borrar</button> 
      </>
    )
  }
}
```

Y ahora necesitamos que a medida que ingresamos algo en el `input`, este vaya modificando el estado `text`. Para esto, podemos utilizar el evento `onChange`, que ejecuta un callback cada vez que un elemento con `value` (aquellos que se utilizan para formularios) cambia su valor.

Por lo tanto, tenemos que definir un método y asignarlo como callback al `onChange` del `input`:

```javascript
class LiveText extends React.Component {
  state = {
    text: ''
  }
  updateText = () => {
    this.setState({ text: ??? })
  }
  render() {
    return (
      <>
        <input 
          type='text'
          onChange={this.updateText}
        />
        <p>{this.state.text}</p>
        <button>Borrar</button> 
      </>
    )
  }
}
```

El problema con el que nos encontramos es que no tenemos una forma (sencilla, al menos) de obtener el `value` del `input`. En React no es recomendable trabajar con los elementos del DOM directamente, a menos que no quede otra opción. 

Por suerte, para este caso, podemos contar con una cierta particularidad de React. Cuando a un método que actúa como callback de un evento le definimos un parámetro, como ser:

```javascript
class LiveText extends React.Component {
  state = {
    text: ''
  }
  updateText = event => {
    this.setState({ text: ??? })
  }
  render() {
    return (
      <>
        <input 
          type='text'
          onChange={this.updateText}
        />
        <p>{this.state.text}</p>
        <button>Borrar</button> 
      </>
    )
  }
}
```

ese parámetro React lo llena con un objeto que contiene mucha información sobre el evento generado. Lo bueno de este objeto es que tiene una propiedad, `target`, que tiene asignado el elemento donde se generó el evento (en este caso, el `input`), y dentro de ese `target` podemos acceder al `value` de dicho elemento.

Por lo tanto, podemos hacer lo siguiente:

```javascript
class LiveText extends React.Component {
  state = {
    text: ''
  }
  updateText = event => {
    this.setState({ text: event.targe.value })
  }
  render() {
    return (
      <>
        <input 
          type='text'
          onChange={this.updateText}
        />
        <p>{this.state.text}</p>
        <button>Borrar</button> 
      </>
    )
  }
}
```

De esta forma, a medida que escribimos en el `input`, se dispara el método `updateText`, desde el cual obtenemos el `value` del `input` (el `target` del evento) y actualizamos el estado `text`. Como el elemento `p` muestra el contenido de dicho estado, se va a ir actualizando en vivo.

Agreguemos ahora un método para borrar el estado:

```javascript
class LiveText extends React.Component {
  state = {
    text: ''
  }
  updateText = event => {
    this.setState({ text: event.targe.value })
  }
  deleteText = () => {
    this.setState({ text: '' })
  }
  render() {
    return (
      <>
        <input 
          type='text'
          onChange={this.updateText}
        />
        <p>{this.state.text}</p>
        <button onClick={this.deleteText}>Borrar</button> 
      </>
    )
  }
}
```

Si hacemos esto, nos vamos a encontrar con que cuando escribimos algo, se actualiza el contenido del texto, y cuando hacemos click, este se borra, pero en nuestro `input` va a seguir quedando lo que tenemos escrito. Por qué sucede esto? El problema acá es que el `value` del input no depende del estado `text`. Esto es lo que se conoce como un **input no controlado**, es decir, un input cuyo estado (lo que está mostrando), no lo controla React, sino el navegador. 

Para solucionar esto, tenemos que hacer que el `value` del `input` dependa del estado del componente. Esto lo hacemos de la siguiente manera:

```javascript
class LiveText extends React.Component {
  state = {
    text: ''
  }
  updateText = event => {
    this.setState({ text: event.targe.value })
  }
  deleteText = () => {
    this.setState({ text: '' })
  }
  render() {
    return (
      <>
        <input 
          type='text'
          value={this.state.text}
          onChange={this.updateText}
        />
        <p>{this.state.text}</p>
        <button onClick={this.deleteText}>Borrar</button> 
      </>
    )
  }
}
```

De esta forma, el input se vuelve **controlado**. Cuando hay un cambio en el `value` del `input`, se dispara el callback del evento `onChange`, `updateText`, que actualiza el estado `text`. Al hacerlo, se actualiza también el `value` del `input` con el valor asignado en dicho estado, que, hasta el momento, es lo que acabamos de escribir.

Parece un paso extra e innecesario, pero lo bueno de controlar el `value` del `input` es que cuando modificamos el estado mediante algún otro elemento u componente (en este caso, el botón), el `input` también se actualiza. De esta forma, no hay discordancia entre el estado del input y el estado que manipula.

## Resumiendo

Para tener un input controlado necesitamos:

* Un estado donde ir guardando el `value` del `input`
* Un método que cambie dicho estado
* Asignar dicho método al `onChange` del `input`
* En dicho método, declararle un parámetro (por lo general, se llama event) y acceder al valor del input mediante `event.target.value`
* Asignarle al `value` del input dicho estado
