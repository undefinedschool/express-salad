# express-salad

Ejercicio integrador de todos los conceptos vistos de `ExpressJS`

## Enunciado

- Utilizar [`express-generator`](http://expressjs.com/en/starter/generator.html) para generar el _scaffolding_ del proyecto

```bash
npx express-generator --git express-salad
```

- Crear una aplicación con `Express`, que tenga al archivo `app.js` como _entrypoint_ y escuche en el puerto `9001` (leerlo del archivo `.env`). En el caso de que no haya un puerto seteado en `.env`, la aplicación debe escuchar en el puerto `8001`. Cuando el servidor esté levantado, loguear el mensaje `Server listening on http://${HOSTNAME}:${PORT}`, donde `HOSTNAME` es otra variable de entorno con el valor `localhost`.
- Todos los _assets_ (archivos estáticos) estarán ubicados dentro de `/public` (y sus subdirectorios). Se debe utilizar el middleware `express.static(PATH)` para servirlos.
- Las imágenes deben estar ubicadas en la carpeta `/public/images`.  
- Probar los endpoints con _Postman_. 
- Utilizar el middleware [`body-parser`](https://www.npmjs.com/package/body-parser) para parsear los requests que envíen `JSON`
- `app.js` debe contar con las rutas definidas a continuación (**la lógica de _routing_ debe estar separada, utilizando el `Router` de Express**), definidas en `/routes/index.js` e importadas y utilizadas desde `app.js`.
- Utilizar el método [`route()`](http://expressjs.com/en/4x/api.html#router.route) del `Router`, para definir las rutas de una forma más declarativa. 
- En caso de necesitar _debuggear_ la aplicación, utilizar [esta guía](https://itnext.io/the-absolute-easiest-way-to-debug-node-js-with-vscode-2e02ef5b1bad).

### Rutas

- `GET /`: debe retornar un `JSON` listando todos los endpoints disponibles, agrupados por tipo de verbo HTTP
- `GET /over/:ki`: si el parámetro recibido es mayor a 9000, debemos retornar [esta imagen](https://scontent.faep8-2.fna.fbcdn.net/v/t1.0-9/13557935_10154299834588430_6953082742839667877_n.jpg?_nc_cat=108&_nc_ohc=NaJZnDsaLH4AQmlOMjlSHEY-Ie0cKmNMiM6JfFXvi5XqS7Vy7dFIqjyWg&_nc_ht=scontent.faep8-2.fna&oh=e8224bf46f2ff62926ce3edb89229f17&oe=5EA8758F). Si el parámetro está recibido entre 8000 y 9000, debe responder con [esta imagen](https://i.pinimg.com/originals/c4/5a/2b/c45a2b80dfe53775508dad0335eb117f.jpg)
- `GET /download`: debe retornar el mensaje (string) `No te entiendo.`
- `GET /download/internet`: debe generar una descarga de [esta imagen](https://www.mememaker.net/api/bucket?path=static/img/memes/full/2017/Jan/18/16/download-all-the-internet.jpg), con el nombre `download-all-the-internet.jpg`. Investigar qué método de `Express` provee esta funcionalidad.
- `GET /area51`: responder con el `JSON` `{ message: "RESTRICTED AREA. NO TRESPASSING." }` y status code `401`
- `POST /area51`: enviar el `JSON` `{ "secret": {SECRET_VALUE}}`. Si `SECRET_VALUE` es `aliens`, responder con el `JSON` `{ message: "ACCESS GRANTED." }` y status code `200`, sino responder con el `JSON` `{ message: "RESTRICTED AREA. NO TRESPASSING." }` y status code `401`
- `GET /undefined`: responder con `"404 - Ni idea, no lo encuentro"` y status code `404`
- `GET /shout/:word`: responder con el `JSON` `Quisiste decir {WORD}???` donde `WORD` es el parámetro recibido, convertido a mayúsculas
- `POST /users`: recibe un request con un `JSON` de la forma `{ name: {NAME}, email: {EMAIL}, age: {AGE} }`. Usar [`express-validator`](https://express-validator.github.io/docs/) para validar que `name` tiene al menos 3 letras (y sólo contiene letras), `email` es un mail válido y `age` es un entero entre 0 y 120. Si pasa la validación y no existe otro usuario con ese email, agregarlo al array `registeredUsers` con la propiedad `id`, que es un entero mayor que cero que no se repite y corresponde al orden en que se creó el usuario (el primer usuario tendrá `id=1`, el segundo `id=2`, etc), dentro del módulo `users.js`. Si algún campo no pasa la validación, responder con un status [422](https://httpstatuses.com/422) y retornar los errores en formato `JSON`. Usar además, las funcionalidades que nos provee [`express-validator`](https://express-validator.github.io/docs/) para _sanitizar el input_, de forma tal que `name` no contenga espacios al inicio ni al final ni otros caracteres especiales, `email` esté _normalizado_ y `age` no contenga espacios al inicio ni al final ni otros caracteres especiales.
- `GET /users`: retorna un HTML generado con`pug`, el cual contiene una tabla con las columnas `Nombre`, `Email` y `Edad`, generada a partir del contenido del array `registeredUsers`.
- Agregar un _middleware custom_ para manejar los errores y responder, en formato `JSON` con el formato

```js
{
  message: err.message,
  /*
   if we're in development mode, include stack trace (full error object)
   otherwise, it's an empty object so the user doesn't see all of that
  */
  error: app.get("env") === "development" ? err : {}
}
```

- `PUT /users/:id` permite editar los campos `name`, `email` o `age` del usuario con el `id` correspondiente (el `id` no puede editarse). Si el `id` utilizado como parámetro no corresponde con ningún usuario, generar el mismo error anterior.
- `DELETE /users/:id` permite eliminar el usuario de `users`, con el `id` correspondiente. Si el `id` utilizado como parámetro no corresponde con ningún usuario, generar el mismo error anterior.
- `GET /html/:name/:color`: `name` y `color` son parámetros que representan un nombre y un color, respectivamente. 
  - Responder con el [siguiente HTML](https://gist.githubusercontent.com/nhsz/5d4d9c339e99ad565116ddc8de0bb199/raw/25277d382208e3aa335d24b3b1888364084b015a/index.html), en un archivo `index.html`, generado con [`Pug`](https://pugjs.org/). Si el color recibido es `red` ó `blue`, el html debe tener un `background-color: red` ó `background-color: blue`, respectivamente. Este CSS debe estar definido en un archivo `styles.css`. Tanto el `index.html` como el `styles.css` deberán ser servidos estáticamente desde la carpeta `/public`.
- `GET /google`: redirigir a `https://google.com`, con un status code `301`.
- `POST /series`: el `body` del request será [el siguiente `JSON`](http://api.tvmaze.com/singlesearch/shows?q=mr-robot&embed=episodes).   
  - Retornar un `JSON` con la siguiente info, procesada y extraída a partir del request: 

```json
{ 
  "series name": {nombre de la serie},
  "number of episodes": {cantidad total de episodios},
  "total runtime": {cantidad total de minutos que dura la serie},
  "first episode": {
    "name": {nombre},
    "date": {fecha de estreno}
  },
  "last episode": {
    "name": {nombre},
    "date": {fecha de estreno}
  },
}
```
