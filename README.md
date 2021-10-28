# Ejemplo de Callback, Promise y Async/await

En los comienzos de internet los sitios web ofrecían datos estáticos en páginas HTML. Actualmente las aplicaciones web son interactivas y dinámicas y se ha incrementado la necesidad de hacer operaciones como peticiones en la red para recuperar datos a través de un API. Para manejar estas operaciones en JavaScript, un desarrollador debe usar técnicas asíncronas de programación.

JavasScript es un lenguaje de un solo hilo(thread) con un
modelo de ejecución síncrono que procesa una operación trás otra y solo puede procesar una instrucción al mismo tiempo.  Sin embargo, una acción como pedir datos a un API puede consumir una cantidad tiempo grande dependiendo de la velocidad de la red, la cantidad de datos solicitados y otros factores. Si la llamada a la API se hace de forma síncrona, el buscador no será capaz de manejar las acciones del usuario como hacer scrolling o pulsar un botón hasta que la operación se haya completado. Esto se conoce como blocking.

Para evitar que se produzca "blocking", los entornos de browser (Chrome, Firefox, Edge, Opera, Safari, etc.) han desarrollado Web APIs para que desde JavaScript los accesos puedan ser asíncronos, de tal manera que se pueden ejecutar en paralelo con otras operaciones. Esto previene el "blocking" porque el usuario puede seguir interactuando con la página mientras la petición asíncrona se está procesando. 

Cómo desarrollador de JavaScript es necesario saber como trabajar con las Web APis asíncronas y manejar los datos recibidos de ellas. En este ejemplo, vamos a trabajar con varías técnicas de tratamiento de la asíncronia: callbacks, promesas y async/await.

## Ejemplo

Es un módulo que en la función hello() llama a una función asíncrona setTimeout(). Esta función le asigna el valor 'Buenos días' a la variable greetings al cabo de 1000 milisegundos(1 segundo).
La función setTimeout() es asíncrona y no bloqueante ya que se lanza su ejecución y el programa continúa ejecutando las siguientes instrucciones sin esperar a que setTimeout() haya terminado. ¿Qué se puede hacer para controlar el orden en que se ejecutan las sentencias tras la llamada a setTimeout()? ¿Qué pasa si setTimeout() obtiene algún dato que se requiere para tomar una decisión en el programa o para mostrarlo en la consola? Veamos:

### No se maneja la asincronía (sincronizar1.js)
Cuando se hace console.log de la variable **greetings** esta está vacía. La sentencia de asignación se ejecutará después de que se hayan hecho los console logs.

~~~
let greetings = '';

function hello() {
  setTimeout(()=>{greetings = 'Buenos Días';}, 1000);
}

function synchronize(){
  console.log ('\x1b[36m%s\x1b[0m', '1- Empieza synchronize');
  hello();
  console.log ('2- greetings está vacio', greetings);
  console.log ('\x1b[36m%s\x1b[0m', '3- Acaba synchronize');
}

console.log ('\x1b[31m%s\x1b[0m', '0- Empieza módulo');
synchronize();
console.log ('\x1b[31m%s\x1b[0m','4- Acaba módulo');
~~~

siendo el resultado de la ejecución

![image](./ejecucion1.png)

### Se maneja la asincronía con callback (sincronizar2.js)
~~~
let greetings = '';

function hello(fdespues) { 
   setTimeout(()=>{
     greetings = 'Buenos Días', fdespues()}, 1000 );  
}

function mostrar(){
    console.log ('2-', greetings)
}

function synchronize(){
  console.log ('\x1b[36m%s\x1b[0m', '1- Empieza synchronize');
  hello(mostrar);  
  console.log ('\x1b[36m%s\x1b[0m', '3- Acaba synchronize');
}

console.log ('\x1b[31m%s\x1b[0m', '0- Empieza módulo');
synchronize();
console.log ('\x1b[31m%s\x1b[0m','4- Acaba módulo');
~~~

siendo el resultado de la ejecución

![image](./ejecucion2.png)

### Se maneja la asincronía con Promise y then() (sincronizar3.js)
El objeto Promise se ha diseñado para tratar con funciones asíncronas y dispone de dos parámetros: resolve que representa a una función que indica que todo ha ido bien y la función asíncrona ha terminado con éxito y reject que indica que ha habido algún error. En este módulo la función hello() devuelve una promesa y con el método then() se consigue que el console.log de la variable no se lleve a cabo hasta que la variable tiene el valor asignado. El metodo then() ejecuta la función especificada cuando la promesa ha efectuado un resolve().

~~~
let greetings = '';

function hello() { 
  return new Promise((resolve, reject) =>{    
      setTimeout(()=>{
        greetings = 'Buenos Días';
        resolve();} , 1000 );
  });
}

function synchronize(){
  console.log ('\x1b[36m%s\x1b[0m', '1- Empieza synchronize'); 
  hello().then(() => console.log ('2-', greetings));    
  console.log ('\x1b[36m%s\x1b[0m', '3- Acaba synchronize');
}

console.log ('\x1b[31m%s\x1b[0m', '0- Empieza módulo');
synchronize();
console.log ('\x1b[31m%s\x1b[0m','4- Acaba módulo');
~~~

siendo el resultado de la ejecución

![image](./ejecucion2.png)

### Se maneja la asincronia con async/await (sincronizar4.js)
Este ejemplo es igual al anterior con la diferencia de que en vez de hacer uso del método then() se hace uso las palabras clave async/await. La palabra clave await se puede usar para llamar a funciones que devuelven promesas. Es el mismo comportamiento que el método then(), hasta que la promesa no se resuelve (resove()) la secuencia de ejecución no continua. Para poder usar await es obligatorio que la función en la que se usar haya sido definida como async.
~~~
let greetings = '';

function hello() { 
  return new Promise((resolve, reject) =>{    
      setTimeout(()=>{greetings = 'Buenos Días';
                      resolve();} , 1000 );
  });
} 

async function synchronize(){
  console.log ('\x1b[36m%s\x1b[0m', '1- Empieza synchronize'); 
  await hello();    
  console.log ('2-', greetings);
  console.log ('\x1b[36m%s\x1b[0m', '3- Acaba synchronize');
}

console.log ('\x1b[31m%s\x1b[0m', '0- Empieza módulo');
synchronize();
console.log ('\x1b[31m%s\x1b[0m','4- Acaba módulo');
~~~

siendo el resultado de la ejecución

![image](./ejecucion4.png)

### Sincronizando todo el código (sincronizar5.js)
En los ejemplos anteriores el console.log ('2- ', greetings) se ejecuta tras el resto de console.log, esto se debe a que lo único que sincroniza el código de los ejemplos anteriores es que se asigne el valor a la variable antes de que esta se muestre en consola. En este ejemplo se sincroniza todo, de manera que los console.log después del de la variable se ejecutan en el orden en que están numerados. Para conseguir esto hacen falta 2 promesas. 

~~~
let greetings = '';

function hello() { 
  return new Promise((resolve, reject) =>{    
      setTimeout(()=>{greetings = 'Buenos Días';
                      resolve();} , 1000 );
  });
} 

async function synchronize(){
  return new Promise (async function(resolve, reject) {
    console.log ('\x1b[36m%s\x1b[0m', '1- Empieza synchronize'); 
    await hello();
    console.log ('2-', greetings);
    console.log ('\x1b[36m%s\x1b[0m', '3- Acaba synchronize');
    resolve();
  })
}

console.log ('\x1b[31m%s\x1b[0m', '0- Empieza módulo');
synchronize().then(() => console.log ('\x1b[31m%s\x1b[0m', '4- Acaba modulo'))
~~~

siendo el resultado de la ejecución

![image](./ejecucion5.png)

Tratar con la asincronía es delicado. Dejo aquí un enlace que sobre como funcionan los motores que ejecutan JavaScript. [Enlace](http://latentflip.com/loupe/?code=dmFyIG15RnJpZW5kQ2F0cyA9IDI7CgpmdW5jdGlvbiBzaG93TXlDYXRzKCkgewogIGNvbnNvbGUubG9nKCdteUNhdHMnLCBteUNhdHMpOwogIHNob3dNeUZyaWVuZENhdHMoKTsKfQoKZnVuY3Rpb24gc2hvd015RnJpZW5kQ2F0cygpIHsKICBjb25zb2xlLmxvZygnbXlGcmllbmRDYXRzJywgbXlGcmllbmRDYXRzKTsKICBzaG93TXlVbmNsZUNhdHMoKTsKfQoKZnVuY3Rpb24gc2hvd015VW5jbGVDYXRzKCkgewogIGNvbnNvbGUubG9nKCdNeSB1bmNsZSBkb2VzIG5vdCBoYXZlIGNhdHMuJyk7Cn0KCnNob3dNeUNhdHMoKTsKCnZhciBteUNhdHMgPSAzOw%3D%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)"latentflip.com/loupe")

Espero que todo esto sea de ayuda y si tenéis alguna pregunta marisaafuera@gmail.com
