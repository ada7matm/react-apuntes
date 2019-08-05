# JSX

JSX (JavaScript extension) es una extensión del lenguaje JavaScript. Cuando escribimos JSX, si bien parece que estamos escribiendo HTML, en realidad es código JSX, que se traduce luego en JS. Por ejemplo, el código:

```javascript
<div>Hola JSX</div>
```

se termina traduciendo en

```javascript
React.createElement('div', 'Hola JSX')
```

Por esto es que necesitamos importar la librería React en la constante `React` cuando utilizamos JSX en un archivo.

Si bien JSX es muy similar en sintaxis a HTML, hay algunas diferencias:

1. Las etiquetas pueden ser abiertas (`<div />`) o cerradas (`<div></div>`)
2. Los atributos van en *camelCase*, por ejemplo `<div onClick />`
3. Algunos atributos tienen nombre distinto al de que se utiliza en HTML, por ejemplo `className` en vez de `class`
4. Como eso se compila a código JS, cada línea se termina autocompletando con un punto y coma, por lo tanto, si ponemos:

```javascript
return
  <div><div />
```

Esto se termina transformando en:


```javascript
return;
  React.createElement('div', '');
```
