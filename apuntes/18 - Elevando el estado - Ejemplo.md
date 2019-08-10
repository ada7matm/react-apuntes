# Elevando el estado - Ejemplo

Supongamos que tenemos un modal que puede abrirse y cerrarse. El componente sería más o menos así:

```javascript
class Modal extends React.Component {
  state = {
    visible: true
  }
  open = () => {
    this.setState({ visible: true })
  }
  close = () => {
    this.setState({ visible: false })
  }
  render(){
    return (
      visible &&
      <div className='modal'>
        <button onClick={this.close}>X</button>
      </div>
    )
  }
} 
```

`Modal` tiene un estado `visible` que determina si el componente se renderiza o no. A su vez, tiene los métodos `open` y `close` que controlan el cambio de estado. 

El problema que tenemos es que un modal no tiene forma de mostrarse a si mismo. No tiene ningún botón ni nada que permita ejecutar el método `open`, porque para eso, *ya tendría que estar mostrándose*.

Entonces, necesitamos que algo más, es decir, otro componente, pueda abrir nuestro modal. Supongamos que nuestra aplicación tiene la siguiente estructura:

```
        App 
         |     
         |     
   .--Container--. 
   |             |
 Modal          Button
```

En nuestro caso, queremos que el componente `Button` pueda abrir nuestro modal. Como ya mencionamos, el estado tiene que encontrarse en un único lugar, es decir, tiene que haber una *única fuente de la verdad*, y si queremos que distintos métodos compartan un estado y las acciones que lo modifican, tenemos que nuclearlo en un único componente y compartir los métodos que cada uno necesitan.

Para eso, tenemos que **elevar el estado** del componente `Modal`. A dónde lo elevamos? **Al ancestro común entre los componentes que necesitan compartir el estado más inmediato**. En este caso, tanto `Modal` como `Button` son hermanos, por lo que su ancestro común más inmediato es `Container`. Por lo tanto, tenemos que elevar el estado de `Modal` a `Container`. 

Cómo hacemos esto? Primero tenemos que *extraer* el estado y los métodos que lo modifican, y cambiarlo de componente. En nuestro caso:

```javascript
class Container extends React.Component {
  state = {
    visible: true
  }
  open = () => {
    this.setState({ visible: true })
  }
  close = () => {
    this.setState({ visible: false })
  }
  render(){
    return (
      <>
        <Button />
        <Modal />
      </>
    )
  }
} 
```

```javascript
class Modal extends React.Component {
  render(){
    return(
      visible &&
      <div className='modal'>
        <button onClick={this.close}>X</button>
      </div>
    )
  }
} 
```

`Container` posee ahora el estado `visible` y los métodos `open` y `close`. Podemos renombrarlos a algo un poco más significativo:

```javascript
class Container extends React.Component {
  state = {
    modalVisible: true
  }
  openModal = () => {
    this.setState({ modalVisible: true })
  }
  closeModal = () => {
    this.setState({ modalVisible: false })
  }
  render(){
    return (
      <>
        <Button />
        <Modal />
      </>
    )
  }
} 
```

Cómo hacemos ahora para compartir este estado y estos métodos? Mediante **props**. Empecemos por compartirle el estado `modalVisible` a nuestro componente `Modal`:

```javascript
class Container extends React.Component {
  state = {
    modalVisible: true
  }
  openModal = () => {
    this.setState({ modalVisible: true })
  }
  closeModal = () => {
    this.setState({ modalVisible: false })
  }
  render(){
    return (
      <>
        <Button />
        <Modal visible={this.state.modalVisible}/>
      </>
    )
  }
} 
```

Ahora `Modal` tiene un `prop` `visible` que va a depender del estado `modalVisible`. Dentro de `Modal` tenemos que obtenerlo de los `props`.

```javascript
class Modal extends React.Component {
  render(){
    const {visible} = this.props
    return (
      visible &&
      <div className='modal'>
        <button onClick={this.close}>X</button>
      </div>
    )
  }
} 
```

El siguiente paso es pasarle el método `closeModal` al componente `Modal`. Esto también lo hacemos mediante `props`:

```javascript
class Container extends React.Component {
  state = {
    modalVisible: true
  }
  openModal = () => {
    this.setState({ modalVisible: true })
  }
  closeModal = () => {
    this.setState({ modalVisible: false })
  }
  render(){
    return (
      <>
        <Button />
        <Modal 
          visible={this.state.modalVisible}
          onClose={this.closeModal}
        />
      </>
    )
  }
} 
```

Es importante destacar que lo que estamos pasándole a `Modal` en el prop `onClose` es una *referencia* al método `closeModal`. Entonces, dentro de nuestro `Modal`, podemos utilizarla así:

```javascript
class Modal extends React.Component {
  render(){
    const {visible, onClose} = this.props
    return (
      visible &&
      <div className='modal'>
        <button onClick={onClose}>X</button>
      </div>
    )
  }
} 
```

Dentro del prop `onClose` está la referencia al método `closeModal`. Cuando se haga click en el botón y se ejecute el callback que tiene asignado, React encuentra que tiene una referencia a otro método, va a buscar ese método, y luego lo ejecuta. Por lo tanto, al hacer click en el botón en el componente `Modal` estamos ejecutando el método `closeModal` del componente `Container`.

Si nos fijamos, ahora nuestro componente Modal ya no tiene un estado interno propio, por lo que podemos refactorizarlo en un componente funcional:

```javascript
const Modal = ({visible, onClose}) => (
    visible &&
    <div className='modal'>
      <button onClick={onClose}>X</button>
    </div>
)
```

Ahora sólo nos queda pasar de la misma forma el método `openModal` a nuestro componente `Button`:

```javascript
class Container extends React.Component {
  state = {
    modalVisible: true
  }
  openModal = () => {
    this.setState({ modalVisible: true })
  }
  closeModal = () => {
    this.setState({ modalVisible: false })
  }
  render(){
    return (
      <>
        <Button onClick={this.openModal}/>
        <Modal 
          visible={this.state.modalVisible}
          onClose={this.closeModal}
        />
      </>
    )
  }
} 
```

y en el componente `Button`, usar dicho prop con el método asignado para algo:

```javascript
const Button = ({onClick}) => (
  <button onClick={onClick} />
)
```

Si bien `onClick={onClick}` puede prestarse a confusión, es importante entender qué significa cada cosa. El primer `onClick` representa un evento del elemento `button`, el segundo es un prop del componente `Button` que contiene el método que se le asignó cuando se creó el componente.

## Resumiendo

* Teníamos dos componentes, `Modal` y `Button`, que necesitaban compartir ciertas acciones que modificasen un estado en particular.
* Para esto buscamos el ancestro *común más inmediato*, el componente `Container`, de los cuales `Modal` y `Button` son componentes hijos.
* "Elevamos" el estado y las acciones que modifican dicho estado a dicho componente.
* Pasamos el estado y los métodos a cada componente que lo necesita mediante props.
* Dentro de cada componente, utilizamos esos props (que incluyen referencias a métodos del componente padre) para asignarlos a los elementos que corresponden.
* Refactorizamos los componentes de clase que ya no tienen estado interno a componentes funcionales.

Hecho esto, es importante destacar un par de cosas:

* Hay una única "fuente de la verdad" del estado `modalVisible`, que reside en el componente `Container`.
* El flujo de la información es de arriba hacia abajo, `Container` pasa el estado y los métodos a sus componentes hijos, pero no a la inversa.
* `Container` en ningún momento hace uso de dicho estado o de dichos métodos, simplemente se encarga de compartirlos entre sus componentes hijos (esto no es un requisito, puede que tenga que hacer uso de ellos dependiendo de la ocasión).
