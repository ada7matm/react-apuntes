# Componentes de clase

Los componentes de clase son otra forma de declarar componentes en React. Nos permiten generan componentes más dinámicos que pueden cambiar y manipular su estado interno. Un componente puede ser funcional o de clase, no puede ser los dos a la vez. Lo mejor es siempre empezar con un componente funcional (porque es más simple y liviano), y si lo necesitamos, convertirlo a componente de clase.

Los pasos para pasar un componente funcional a uno de clase son los siguientes:

1. Crear una clase con el nombre del componente
2. Extender dicha clase de la clase `React.Component`
3. Agregarle un método `render` a la clase
4. Dentro de dicho método, poner un `return()`
5. Dentro de los paréntesis del `return` poner todo el JSX que estaba en el componente funcional

Por ejemplo, supongamos que tenemos el siguiente componente:

```javascript
import React from 'react'
// ...otros imports

const PopUpSubscription = () => (
  <PopUp>
    <EmailForm />
    <Button type="submit" />
  </PopUP>
)

export default PopUpSubscription
```

y queremos pasarlo a un componente de clase. 

#### 1. Crear una clase con el nombre del componente

```javascript
class PopUpSubscription {

}
```

#### 2. Extender dicha clase de la clase `React.Component`


```javascript
class PopUpSubscription extends React.Component {

}
```

#### 3. Agregarle un método `render` a la clase


```javascript
class PopUpSubscription extends React.Component {
  render(){
  
  }
}
```

#### 4. Dentro de dicho método, poner un `return()`

```javascript
class PopUpSubscription extends React.Component {
  render(){
    return(
    
    )
  }
}
```

#### 5. Dentro de los paréntesis del `return` poner todo el JSX que estaba en el componente funcional

```javascript
class PopUpSubscription extends React.Component {
  render(){
    return(
      <PopUp>
        <EmailForm />
        <Button type="submit" />
      </PopUP>
    )
  }
}
```

Esto finalmente nos quedaría así:

```javascript
import React from 'react'
// ... otros imports

class PopUpSubscription extends React.Component {
  render(){
    return(
      <PopUp>
        <EmailForm />
        <Button type="submit" />
      </PopUP>
    )
  }
}

export default PopUpSubscription
```

Esto es lo mínimo que necesitamos para tener un componente de clase funcionando. Un componente de clase tiene que tener sí o sí un método `render`, y dicho método tiene que devolver JSX válido.


## props

Los props son pasados internamente por React cuando crea un componente funcional, por lo tanto, en cualquier parte de la clase tenemos acceso a estos mediante `this.props`. Si queremos usarlo por ejemplo en el método `render`, por ejemplo, podríamos hacer esto:

```javascript
class Button extends React.Component {
  render(){
    return(
      <div>{this.props.text}</div>
    )
  }
}
```

También podemos *desestructurar* los props para que queden de forma más visible y sean fácilmente reconocibles, además de permitirnos usarlos sin `this.props`

```javascript
class Comment extends React.Component {
  render(){
    const {title, text, username, date} = this.props
    return (
      <div className='comment'>
        <p>{title}</p>
        <p>{text}</p>
        <p>{username}</p>
        <p>{date}</p>
      </div>
    )
  }
}
```
