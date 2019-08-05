# JSX

JSX (JavaScript extension) es una extensión del lenguaje JavaScript. Cuando escribimos JSX, si bien parece que estamos escribiendo HTML, en realidad es código JSX, que se traduce luego en JS. Por ejemplo, el código:

```javascript
<div>Hola JSX</div>
```

se termina traduciendo en

```javascript
React.createElement('div', {}, 'Hola JSX')
```

Por esto es que necesitamos importar la librería React en la constante `React` cuando utilizamos JSX en un archivo.

Si bien JSX es muy similar en sintaxis a HTML, hay algunas diferencias:

1. Las etiquetas pueden ser abiertas (`<div />`) o cerradas (`<div></div>`)
2. Los atributos van en *camelCase*, por ejemplo `<div onClick />`
3. Algunos atributos tienen nombre distinto al de que se utiliza en HTML, por ejemplo `className` en vez de `class`
4. Como eso se compila a código JS, cada línea se termina autocompletando con un punto y coma, por lo tanto, si ponemos:

```javascript
return
  <div />
```

esto se termina transformando en:


```javascript
return;
  React.createElement('div', {}, null);
```

por lo que la instrucción de crear el elemento de React no se ejecutaría nunca, ya que el `return` corta el flujo de la instrucción. Por eso si queremos utilizarlo en una nueva línea, tenemos que encerrarlo dentro de un paréntesis, de esta forma, el compilador sabe que tiene que esperar a cerrar el paréntesis para poner el punto y coma:

```javascript
return (
  <div />
)
```

Como el compilador tiene que saber diferenciar código JS de JSX, tenemos que identificar de alguna forma cuándo estamos escribiendo uno u otro código. Para esto, utilizamos las llaves `{}` para definir que lo que va dentro de estas es código JS **cuando estamos dentro de código JSX**. Siempre es importante fijarse dentro de qué contexto estamos, porque dentro de contexto de código JS, `{}` representan un objeto. Por ejemplo:

```javascript
<div>
  {
      // Contexto JS
  }
</div>
```

Dentro de las llaves, podemos poner toda instrucción válida de JS que *devuelva* un valor, por ejemplo, un valor puro (un número, un booleano, un array, un objto), una variable, una función, un operador ternario, etc. A su vez, dentro de un contexto JS, podemos anidar elementos JSX que a su vez tengan llaves y generen un nuevo contexto JS, por ejemplo:

```javascript
<div>
  {
      // Contexto JS
      <div index={1} />
  }
</div>
```

Una expresión muy utilizada, y que suele confundir, es cuando usamos un objeto dentro de una expresión JSX. En ese caso, se utiliza doble llaves, donde las primeras llaves indican que lo que viene a continuación es código JS, y las segundas llaves representan el objeto en sí:

```javascript
<div style={{ color: 'red' }} />
```
