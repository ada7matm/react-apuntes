# Fetch en React

El problema que tenemos cuando utilizamos `fetch` en React es que `fetch` es una operación asíncrona, es decir, tarda un tiempo en ejecutarse y obtener una respuesta. Si tenemos que esperar a que nos lleguen los datos para setear el estado de un componente, va a haber un momento en que dicho componente no va a tener estado, y nos va a tirar error.

Entonces, la estrategia es la siguiente:

  * Se define un estado vacío
  * Inicialmente, se utiliza ese estado para renderizar el componente
  * Se hace un `fetch` ya sea en un callback de un evento, o el método `componentDidMount`
  * Cuando se obtiene la respuesta del `fetch`, se actualiza el estado
  
## Fetch en un evento

```javascript
class UsersInfo extends React.Component {
  state = {
    users: []
  }
  loadData = () => {
    fetch('https://www.miapi.com')
    .then(response => response.json())
    .then(data => this.setState({ users: data.users }))
  }
  render(){
    return (
      <>
        {
          this.state.users.map(user => <p>{user}</p>)   
        }
        <div onClick={this.loadData}>Cargar</div>
      </>
    )
  }
}
```

Primero definimos un componente. Dicho componente tiene un estado, `users`, que es un array, inicialmente vacío. Ese estado es utilizado para renderizar una serie de elementos `p` con el nombre de cada usuario. Inicialmente, dichos elementos no se muestran porque el array no contiene nada. 

El componente también tiene un `div` que al hacerle click, ejecuta el método `loadData`. Este método hace una llamada a una api, y cuando obtiene la respuesta, actualiza el estado con `setState`, pasándole la información que le llegó desde la api. 

Cuando el estado se actualiza, React detecta que hubo un cambio, y actualiza el componente renderizando ahora sí el listado de nombres de usuarios.

<br/>

## Fetch al inicializar un componente

Pero qué pasa si queremos que el `fetch` se realice cuando el componente es creado, y no cuando se dispara alguna acción? Para eso necesitamos utilizar el método `componentDidMount`, que se ejecuta inmediatamente después de que el componente se "monta", es decir, se agrega al DOM/renderiza.

Modificando nuestro ejemplo anterior, nos quedaría:

```javascript
class UsersInfo extends React.Component {
  state = {
    users: []
  }
  componentDidMount = () => {
    fetch('https://www.miapi.com')
    .then(response => response.json())
    .then(data => this.setState({ users: data.users }))
  }
  render(){
    return (
        <div>
        {
          this.state.users.map(user => <p>{user}</p>)   
        }
        </div>
    )
  }
}
```

En este caso, apenas el componente se agregue al DOM, no se renderizará nada, porque `users` es un array vacío. Inmediatamente, React ejecuta `componentDidMount` y realiza el `fetch`, cuando recibe la respuesta, actualiza el estado y por lo tanto el componente se rerenderiza.

<br/>

## Agregando un loader

Qué pasa si queremos agregar un loader mientras se está esperando la respuesta de la api? Para eso necesitamos un estado (por ejemplo, `isLoading`), que pase a ser `true` cuando comienza a realizarse el `fetch` y `false` cuando recibimos la respuesta. Con ese estado luego podemos mostrar/ocultar algo en el componente, cambiar alguna clase o estilo, etc.

```javascript
class UsersInfo extends React.Component {
  state = {
    isLoading: false,
    users: []
  }
  componentDidMount = () => {
    this.setState({ isLoading: true })
    
    fetch('https://www.miapi.com')
    .then(response => response.json())
    .then(data => this.setState({ users: data.users, isLoading: false }))
  }
  render(){
    return (
      <div>
        { 
          this.state.isLoading && <p>Cargando...</p> 
        }
        { 
          this.state.users.map(user => <p>{user}</p>) 
        }
      </div> 
    )
  }
}
```

<br/>

## Patrón contenedor

Un patrón muy útil a la hora de trabajar con `fetch` en React es el **patrón contenedor**. La idea básica es separar la lógica (la que genera el pedido a la api y tiene el estado que se actualiza) de la representación.

Supongamos que tenemos el siguiente componente:

```javascript
class UsersInfo extends React.Component {
  state = {
    users: []
  }
  componentDidMount = () => {
    this.setState({ isLoading: true })
    
    fetch('https://www.miapi.com')
    .then(response => response.json())
    .then(data => this.setState({ users: data.users }))
  }
  render(){
    return (
      <div> 
      {
       this.state.users.map(user => (
         <Card>
           <Avatar img={user.img}>
           <Link href={user.profileUrl}>
             <Username username={user.username}>
           </Link>
           <p>Registrado desde: <Date date={user.signupDate} /></p>
           <p>Cantidad de posts: {user.posts.length} /></p>
         </Card>
       ))
      }
      </div>
    )
  }
}
```

En este componente, tenemos mezclado la representación (lo que se renderiza) de la lógica que obtiene la info. Para separar esto, necesitamos un componente "contenedor", de la siguiente forma:

```javascript
class UsersInfoContainer extends React.Component {
  state = {
    users: []
  }
  componentDidMount = () => {
    this.setState({ isLoading: true })
    
    fetch('https://www.miapi.com')
    .then(response => response.json())
    .then(data => this.setState({ users: data.users }))
  }
  render(){
    return (
      <div>
      {
        this.state.users.map(user => <UserInfo user={user} />)
      }
      </div>
    )
  }
}
```

Y nuestro componente encargado de la vista:

```javascript
const UserInfo = ({user}) => (
    <Card>
      <Avatar img={user.img}>
      <Link href={user.profileUrl}>
        <Username username={user.username}>
      </Link>
      <p>Registrado desde: <Date date={user.signupDate} /></p>
      <p>Cantidad de posts: {user.posts.length} /></p>
    </Card>
)
```

De esta forma, separamos responsabilidades, nos quedan componentes más pequeños y sencillos de entender, y en el caso de tener que modificar una cosa (lógica o representación), sabemos donde encontrarla. 

Este patrón se puede combinar con el patrón de lista, de la siguiente forma:

```javascript
const UserInfoList = ({users}) => (
   <div>
   {
     users.map(user => <UserInfo user={user} />)
   }
   </div>
)
```

Y en nuestro componente contenedor, modificar lo siguiente:


```javascript
class UsersInfoContainer extends React.Component {
  state = {
    users: []
  }
  componentDidMount = () => {
    this.setState({ isLoading: true })
    
    fetch('https://www.miapi.com')
    .then(response => response.json())
    .then(data => this.setState({ users: data.users }))
  }
  render(){
    return (
      <UserInfoList users={this.state.users} />
    )
  }
}
```
