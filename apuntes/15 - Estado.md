# Estado

En React el estado es uno de los conceptos más importantes que tenemos que manejar. De hecho, es el núcleo y la razón por la que React existe: *facilitar el manejo del estado de una aplicación*. Podemos entender al estado como el **conjunto de propiedades, valores, y datos que algo (un componente, una aplicación, etc) contiene *en un momento dado***. El estado es lo que hace *dinámica* a una aplicación, es aquello que cambia: algo está en un estado, y pasa a estarlo en otro. Si no hay cambios de estado, es porque nada se modifica.

Todo aquello que cambie en un componente, o que pueda ser alterado mediante alguna acción o interacción, forma parte del estado de ese componente. Por ejemplo, en un botón que al hacer click cambia de color, se puede decir que pasa de un estado no clickeado a uno en el que sí lo está. Cualquier cosa que necesitemos modificar dentro de un componente tiene que ser parte de su estado (u del estado de otros, como ya veremos). En React no está permitido modificar otras cosas dentro de un componente, como los props. Por ejemplo, lo siguiente no debe hacerse:

```javascript
class Modal extends React.Component {
  close = () => {
    this.props.visible = false // MAL!
  }
  render(){
    return(
        this.props.visible &&
        <div className='modal' onClick={this.close} />        
    )
  }
}
```

Cuando queremos definir un cierto estado, tenemos que agregarlo a la propiedad `state` del componente. `state` es un objeto donde cada propiedad va a representar un estado. Siguiendo el caso anterior:


```javascript
class Modal extends React.Component {
  state = {
    visible: true
  }
  close = () => {
    // ...
  }
  render(){
    return(
        this.state.visible &&
        <div className='modal' onClick={this.close} />        
    )
  }
}
```

Dentro de `state` podemos definir todas las propiedades/estados que querramos, con los nombres que querramos y los valores que deseemos. No es más que un objeto de JS.


## Inmutabilidad

Otro concepto de suma importancia a la hora de trabajar con React y con estados, es el de **inmutabilidad**. Inmutable es aquello que no puede cambiar, que no puede ser alterado. En React **el estado es inmutable**. ¿Qué significa esto? Que *no podemos modificar el estado*. En ningún sentido: no podemos sumarle un número, ni cambiarle un valor, ni agregarle una letra, ni pushear un ítem a un array, ni modificar la propiedad de un objeto. Nada. Es decir, en el caso anterior, por ejemplo, no podemos hacer lo siguiente:

```javascript
class Modal extends React.Component {
  state = {
    visible: true
  }
  close = () => {
    this.state.visible = false // MAL!
  }
  render(){
    return(
        this.state.visible &&
        <div className='modal' onClick={this.close} />        
    )
  }
}
```

¿Cómo se concilia esto con que el estado de un componente cambia y es lo que permite que sea dinámico, si no podemos alterar o mutar el estado? La regla es que **no podemos mutar el estado de un componente *directamente***. Para hacerlo, *tenemos que avisarle a React que queremos manipular el estado*. Para esto, tenemos que usar el método `setState`.

## setState

`setState` es un método de la clase `React.Component`, y nuestro componente, al extender de esta, también lo hereda. Por lo tanto, podemos accederlo como `this.setState()` en cualquier parte de nuestra clase. Este método toma como parámetro un objeto, donde sus propiedades son los nombres de aquellas propiedades del estado que queremos cambiar, y sus valores aquellos valores que queremos asignarle a dicho estado. Volviendo a nuestro caso, nos quedaría de la siguiente forma:

```javascript
class Modal extends React.Component {
  state = {
    visible: true
  }
  close = () => {
    this.setState({ visible: false })
  }
  render(){
    return(
        this.state.visible &&
        <div className='modal' onClick={this.close} />        
    )
  }
}
```

## Por qué no puedo modificar el estado directamente?

Dos razones: performance y coherencia.

  * **perfomance**: un array tiene una *id* que es lo que se conoce como **referencia**, que es la dirección en la que está guardado en la memoria. Si tenemos un array de 10000 items, React para saber si ese array fue modificado o no y actualizar el componente, tiene que recorrer los 10000 ítems y chequearlos con los valores anteriores, uno por uno. En cambio, si prohíbe hacer cambios a un array, la única forma de hacer un cambio es creando un array nuevo, por lo tanto, React sólo tiene que comparar la referencia de un array con el nuevo para saber si es el mismo array o no. Si no coinciden, es porque hubo algún cambio, y tiene que renderizar las cosas. Comparar una referencia es muchísimo más rápido que comparar todos los ítems uno por uno. 
  * **estabilidad**: al tener el control de cuándo el estado cambia, React puede actualizar los componentes que corresponden en el momento dado. En cambio, tener cosas que cambian por fuera del control hace que React tenga que estar chequeando todo el tiempo todo para ver si cambió algo (en vez de ya saber qué tiene que cambiar), y que pueda haber un desfasaje entre cosas que cambiaron y todavía no se actualizaron.

## Resumiendo

  * Todo aquello que puede cambiar en un componente, es parte del estado
  * El estado es una propiedad del componente/clase
  * El estado es un objeto cuyas propiedad son las "variables" que queremos tener
  * Al ser un objeto, sus propiedades pueden tener cualquier nombre y tener cualquier valor
  * El estado **nunca jamás se muta directamente**
  * Para modificar un estado necesitamos usar el método heredado de `React.Component`, `setState`
  * `setState` acepta como parámetro un objeto
  * Dicho objeto debe tener como propiedad los nombres de aquellas propiedades que queremos cambiar, y los nuevos valores que queremos que tengan
