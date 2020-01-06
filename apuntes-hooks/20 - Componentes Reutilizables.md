# Componentes reutilizables

En JavaScript vainilla, cuando necesitamos reutilizar código, lo abstraemos en una función, y esta nos queda disponible para ser usada en cualquier lado. Con React ya vimos que podemos hacer algo parecido con los `hooks` cuando queremos reutilizar cierta lógica. Pero, ¿qué pasa con los componentes? Los componentes son los ladrillos con los que se construyen las aplicaciones en React. Imagina construir una casa donde cada ladrillo tuviese que ser distinto (o cada ventana, o cada puerta, o cada cerámica), sería muy costosa e improductiva. Lo mismo pasa en React.

Supongamos que necesitamos dos botones, uno que diga "Aceptar" y otro que diga "Cancelar". Podríamos hacer lo siguiente:

```jsx
const AcceptButton = () => (
	<button>Aceptar</button>
)

const CancelButton = () => (
	<button>Cancelar</button>
)
```

Estos botones son componentes específicos, sólo pueden usarse para un caso particular. Ahora imagina hacer lo mismo *para cada botón* que nuestra aplicación necesita. Ya puedes darte cuente que esto es totalmente impráctico, genera una duplicación innecesaria de código, requiere más tiempo de desarrollo y mantenimiento, y es mucho más propenso a bugs y problemas.

Gran parte de la arquitectura de una aplicación de React, y buena parte de su flexibilidad y potencia, consiste en crear **componentes reutilizables**. Un componente reutilizables es un componente lo suficientemente genérico que puede utilizarse en muchos casos y situaciones diferentes. Pero, ¿cómo hacer un componente reutilizable?


## Diseñando componentes reutilizables

Lamentablemente, no existe una fórmula o regla que podamos aplicar cada vez que necesitamos hacer un componente reutilizable, pero sí existen una serie de estrategias y tips & tricks que podemos poner en práctica. Al igual que con las funciones, abstraer y generalizar componentes es una tarea que tiene su complejidad, cuanto más genérico y flexible queremos que nuestro componente sea, es decir, cuantos más casos queremos que abarque, más tenemos que pensar con detenimiento cómo armarlo.

Utilicemos un caso en particular de un componente y vayamos viendo cómo podemos hacer para volverlo reutilizable. Supongamos que tenemos este alert:

```jsx

const AlertError = ({isOpen}) => (
	const [isVisible, setIsVisible] = useState(isOpen)

	const close = () => setIsVisible(false)

	return (
		isVisible &&
		<div>
			<h3>Error</h3>
			<p>Ha ocurrido un error en la carga, por favor, vuelva a intentarlo</p>
			<>
		</div>
	)
)



Estrategias

- Generalizar
- Componentes idiotas / No logica
- No layout
- Personalizacion
- Agregar atributos
- Composicion
- Notacion de puntos
- Partir componentes



