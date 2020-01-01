# setState: arrays y objectos

Trabajar con arrays y objetos como estado tiene la peculiaridad en React de que cada vez que queremos hacer alguna modificación a alguno de estos, tenemos que *proporcionar un nuevo array u objeto*. Para esto, podemos aprovechar varios métodos y técnicas, como todos los métodos que devuelvan un nuevo elemento (`map`, `filter`, `reduce`, `slice`) y el `spread operator`

## Spread operator

El `spread operator` es una característica de ES6. La sintaxis es la siguiente:

```javascript
const numeros = [1, 2, 3, 4]
console.log(...numeros) //
```

El `spread operator` se escribe con 3 puntos (`...`) seguido del nombre del array (u objeto), y contiene todos los elementos (u propiedades) del mismo. En principio esto no parece muy útil, pero nos permite hacer cosas como la siguiente:

```javascript
const frutas = ['kiwi', 'uva']
const frutasYVerduras = ['lechuga', ...frutas, 'repollo']
console.log(frutasYVerduras) // ['lechuga', 'kiwi', 'uva', 'repollo']
```

Lo mismo con un objeto:

```javascript
const persona = {
  nombre: 'Ada',
  apellido: 'Lovelace'
}

const empleada = {
  puesto: 'desarrolladora',
  infoPersonal: {
    ...persona
  }
}

console.log(empleada) /** {
  puesto: 'desarrolladora',
  infoPersonal: {
    nombre: 'Ada',
    apellido: 'Lovelace'
  }
} */
```

<br />

## Trabajando con arrays

#### Vaciando un array

Reemplazamos el array anterior por uno nuevo y vacío

```javascript
this.setState({ frutas: [] })
```

#### Concatenando arrays

Creamos un nuevo array que contenga todos los ítems de distintos arrays

```javascript
this.setState({ comidas: [...saladas, ...dulces, ...snacks] })
```

#### Agregando un item

Creamos un nuevo array que contenga todos los ítems del array original, más el nuevo

```javascript
this.setState({ frutas: [...this.state.frutas, 'Pera'] })
```

#### Eliminando un item

Filtramos el array original buscando sólo aquellos elementos que no queremos eliminar (es decir, los que son distintos al que queremos sacer) y obtenemos un nuevo array con todos los ítems menos el que no necesitamos (se pueden eliminar más ítems agregando más condiciones)

```javascript
this.setState({ frutas: this.state.frutas.filter(fruta => fruta !== 'Manzana') })
```

También podemos utilizar *slice* si sabemos el índice del elemento que queremos extraer

```javascript
this.setState({ frutas: this.state.frutas.slice(3, 5) })
```

#### Reemplazando un item

Obtenemos un nuevo array resultado de mapear todos los elementos del array a sí mismo, excepto el que queremos cambiar, que se mapea a uno nuevo

```javascript
this.setState({ frutas: this.state.frutas.map[(fruta => fruta === 'Manzana' ? 'Pera' : fruta) })
```

<br />

## Trabajando con objetos

#### Agregando una propiedad

Creamos un nuevo objeto que contenga todos las propiedades del objeto original, más la nueva

```javascript
this.setState({ animal: {...this.state.animal, tipo: 'Perro'} })
```

#### Modificando una propiedad

Creamos un nuevo objeto que contenga todos las propiedades del objeto original, y agregamos la que queremos modificar, que si ya estaba en las anteriores se sobreescribe

```javascript
this.setState({ animal: {...this.state.animal, tipo: 'Perro'} })
```

#### Eliminando una propiedad

Desestructuramos el objeto en la propiedad que queremos eliminar y las demás, y creamos un objeto nuevo con las demás

```javascript
const {propToDelete, ...others} = this.state.myObject
this.setState({ myObject: others })
```

#### Uniendo las propiedades de varios objetos

Creamos un nuevo objeto que contenga todos las propiedades de distintos objetos

```javascript
this.setState({ usuario: {...infoPersonal, ...infoDeContanto, ...infoLaboral} })
```

<br />

## Trabajando con propiedades anidades

Si bien React no está pensado para utilizar estado anidado, y por lo tanto no es recomendable hacerlo y siempre que se pueda hay que evitarlo, hay veces que no tenemos muchas alternativas. Por lo tanto, si necesitamos actualizar alguna propiedades anidada, podemos utilizar las técnicas anteriores para ir reconstruyendo el objeto:

```javascript
this.setState({ usuario: 
  {
    ...this.state.usuario,
    personalInfo: {
      ...this.state.usuario.personalInfo,
      nombre: 'Ada'
    }
  }
})
```
