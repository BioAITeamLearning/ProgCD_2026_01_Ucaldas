---
title: Unidad  7
---
# Unidad 7: Concurrencia para desarrollo web


## Ejemplos visuales

Vamos a describir más a fondo los paradigmas clave de concurrencia con diagramas visuales:

### Código sincrónico

Código sincrónico: Con el código sincrónico, las tareas se ejecutan secuencialmente, una tras otra.

<img src="https://preview.redd.it/lpf0u9nbj7w41.jpg?auto=webp&s=baeaffd073acee25e051403940485f8f192a9caa"/>

### Código Async/await

Con async/await, todo el código de Python se ejecuta en un solo hilo. Las tareas se ejecutan de manera concurrente (pero no en paralelo). Establecemos pasos de espera en el código donde esperamos que se complete el trabajo limitado por E/S y otras tareas continúan trabajando en el hilo principal mientras esperan. Muchas tareas parecen ejecutarse al mismo tiempo porque muchas pueden estar esperando trabajo de E/S al mismo tiempo. Un bucle de eventos coordina las tareas en espera, reanudando el código de Python cuando se completa el trabajo de E/S en segundo plano.


<img src="https://www.khmer168.com/wp-content/uploads/2024/01/1_GQAZITgyVhwf98vjki7S9A-e1704192587774.png"/>

### Código con hilos

Con el código con hilos, las tareas se ejecutan de manera concurrente (pero no en paralelo). A diferencia del código estilo async/await, no establecemos lugares específicos para esperar a que se completen las tareas en segundo plano. En cambio, nuestra CPU coordina todos los hilos, alternando cuál hilo ejecuta código en cualquier momento dado. Debido al GIL (Global Interpreter Lock), solo un hilo puede ejecutar código de Python a la vez. Sin embargo, muchos hilos pueden estar esperando que se complete el trabajo limitado por E/S en segundo plano mientras un hilo activo ejecuta código de Python. La CPU alterna rápidamente el hilo activo, a veces incluso alternando entre el inicio de una línea de código de Python y el final de esa misma línea. Por lo tanto, las condiciones de carrera en el código con hilos son menos predecibles que en el código async/await.

<img src="https://media2.dev.to/cdn-cgi/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fh68e994yuiu8pr14h7lk.png"/>

### Código con procesamiento múltiple (multiprocessing)

Con el código de multiprocesamiento en Python, las tareas se ejecutan en paralelo en múltiples núcleos de CPU al mismo tiempo. Cada tarea de multiproceso se ejecuta en un proceso de Python separado y mayormente aislado. El multiprocesamiento puede acelerar enormemente el código de Python que no involucra trabajo de E/S, como operaciones intensivas en matemáticas. Sin embargo, el multiprocesamiento tiene limitaciones. Hay un costo de inicio significativo para los nuevos procesos, y la comunicación entre procesos es limitada y relativamente lenta.

<img src="https://miro.medium.com/v2/resize:fit:1400/1*6Y7JWcTJUS4v4_CVHx8CCA.png"/>


## Veamos más a fondo el proceso de concurrencia y paralelismo

```{note}
Adaptado de: [FastAPI docs](https://fastapi.tiangolo.com/async/?h=concu)

```

### Concurrencia y Hamburguesas

El concepto de código asíncrono descrito anteriormente a veces también se llama "concurrencia". Es diferente del "paralelismo".

Concurrencia y paralelismo ambos se relacionan con "cosas diferentes que suceden más o menos al mismo tiempo".

Pero los detalles entre concurrencia y paralelismo son bastante diferentes.

Para entender las diferencias, imagina la siguiente historia sobre hamburguesas:

#### Hamburguesas Concurrentes

Vas con la persona que te gusta 😍 a pedir comida rápida 🍔, haces cola mientras el cajero 💁 recoge los pedidos de las personas de delante tuyo.

<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-01.png"/>


Llega tu turno, haces tu pedido de 2 hamburguesas impresionantes para esa persona 😍 y para ti.


<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-02.png"/>


El cajero 💁 le dice algo al chico de la cocina 👨‍🍳 para que sepa que tiene que preparar tus hamburguesas 🍔 (a pesar de que actualmente está preparando las de los clientes anteriores).

<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-03.png"/>

Pagas 💸. El cajero 💁 te da el número de tu turno.

<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-04.png"/>


Mientras esperas, vas con esa persona 😍 y eliges una mesa, se sientan y hablan durante un rato largo (ya que las hamburguesas son muy impresionantes y necesitan un rato para prepararse ✨🍔✨).

Mientras te sientas en la mesa con esa persona 😍, esperando las hamburguesas 🍔, puedes disfrutar ese tiempo admirando lo increíble, inteligente, y bien que se ve ✨😍✨.


<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-05.png"/>


Mientras esperas y hablas con esa persona 😍, de vez en cuando, verificas el número del mostrador para ver si ya es tu turno.

Al final, en algún momento, llega tu turno. Vas al mostrador, coges tus hamburguesas 🍔 y vuelves a la mesa.


<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-06.png"/>

Tú y esa persona 😍 se comen las hamburguesas 🍔 y la pasan genial ✨.

<img src="https://fastapi.tiangolo.com/img/async/concurrent-burgers/concurrent-burgers-07.png"/>



#### Hamburguesas Paralelas

Ahora imagina que estas no son "Hamburguesas Concurrentes" sino "Hamburguesas Paralelas".

Vas con la persona que te gusta 😍 por comida rápida paralela 🍔.

Haces la cola mientras varios cajeros (digamos 8) que a la vez son cocineros 👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳 toman los pedidos de las personas que están delante de ti.

Todos los que están antes de ti están esperando 🕙 que sus hamburguesas 🍔 estén listas antes de dejar el mostrador porque cada uno de los 8 cajeros prepara la hamburguesa de inmediato antes de recibir el siguiente pedido.

<img src="https://fastapi.tiangolo.com/img/async/parallel-burgers/parallel-burgers-01.png"/>

Entonces finalmente es tu turno, haces tu pedido de 2 hamburguesas 🍔 impresionantes para esa persona 😍 y para ti.

Pagas 💸.


<img src="https://fastapi.tiangolo.com/img/async/parallel-burgers/parallel-burgers-02.png"/>

El cajero va a la cocina 👨‍🍳.

Esperas, de pie frente al mostrador 🕙, para que nadie más recoja tus hamburguesas 🍔, ya que no hay números para los turnos.

<img src="https://fastapi.tiangolo.com/img/async/parallel-burgers/parallel-burgers-03.png"/>


Como tu y esa persona 😍 están ocupados en impedir que alguien se ponga delante y recoja tus hamburguesas apenas llegan 🕙, tampoco puedes prestarle atención a esa persona 😞.

Este es un trabajo "síncrono", estás "sincronizado" con el cajero / cocinero 👨‍🍳. Tienes que esperar y estar allí en el momento exacto en que el cajero / cocinero 👨‍🍳 termina las hamburguesas 🍔 y te las da, o de lo contrario, alguien más podría cogerlas.


<img src="https://fastapi.tiangolo.com/img/async/parallel-burgers/parallel-burgers-04.png"/>


Luego, el cajero / cocinero 👨‍🍳 finalmente regresa con tus hamburguesas 🍔, después de mucho tiempo esperando 🕙 frente al mostrador.

<img src="https://fastapi.tiangolo.com/img/async/parallel-burgers/parallel-burgers-05.png"/>

Coges tus hamburguesas 🍔 y vas a la mesa con esa persona 😍.

Sólo las comes y listo 🍔 ⏹.

<img src="https://fastapi.tiangolo.com/img/async/parallel-burgers/parallel-burgers-06.png"/>


No has hablado ni coqueteado mucho, ya que has pasado la mayor parte del tiempo esperando 🕙 frente al mostrador 😞.



En este escenario de las hamburguesas paralelas, tú eres un sistema / programa 🤖 con dos procesadores (tú y la persona que te gusta 😍), ambos esperando 🕙 y dedicando su atención ⏯ a estar "esperando en el mostrador" 🕙 durante mucho tiempo.

La tienda de comida rápida tiene 8 procesadores (cajeros / cocineros) 👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳. Mientras que la tienda de hamburguesas concurrentes podría haber tenido solo 2 (un cajero y un cocinero) 💁 👨‍🍳.

Pero aún así, la experiencia final no es la mejor 😞.


### ¿Es la concurrencia mejor que el paralelismo?

¡No! Esa no es la moraleja de la historia.

La concurrencia es diferente al paralelismo. Y es mejor en escenarios específicos que implican mucha espera. Debido a eso, generalmente es mucho mejor que el paralelismo para el desarrollo de aplicaciones web. Pero no para todo.

Entonces, para explicar eso, imagina la siguiente historia corta:


>
> Tienes que limpiar una casa grande y sucia.
>

No hay esperas 🕙, solo hay mucho trabajo por hacer, en varios lugares de la casa.

Podrías tener turnos como en el ejemplo de las hamburguesas, primero la sala de estar, luego la cocina, pero como no estás esperando nada, solo limpiando y limpiando, los turnos no afectarían nada.

Tomaría la misma cantidad de tiempo terminar con o sin turnos (concurrencia) y habrías hecho la misma cantidad de trabajo.

Pero en este caso, si pudieras traer a los 8 ex cajeros / cocineros / ahora limpiadores 👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳👨‍🍳, y cada uno de ellos (y tú) podría tomar una zona de la casa para limpiarla, podría hacer todo el trabajo en paralelo, con la ayuda adicional y terminar mucho antes.

En este escenario, cada uno de los limpiadores (incluido tú) sería un procesador, haciendo su parte del trabajo.

Y como la mayor parte del tiempo de ejecución lo coge el trabajo real (en lugar de esperar), y el trabajo en un sistema lo realiza una CPU , a estos problemas se les llama "CPU bound".

### Ejemplos de operaciones CPU-bound

Ejemplos típicos de operaciones dependientes de CPU son cosas que requieren un procesamiento matemático complejo.

Por ejemplo:

* Audio o procesamiento de imágenes.
* Visión por computadora: una imagen está compuesta de millones de píxeles, cada píxel tiene 3 valores / colores, procesamiento que normalmente requiere calcular algo en esos píxeles, todo al mismo tiempo.
* Machine Learning: normalmente requiere muchas multiplicaciones de "matrices" y "vectores". Imagina en una enorme hoja de cálculo con números y tener que multiplicarlos todos al mismo tiempo.
* Deep Learning: este es un subcampo de Machine Learning, por lo tanto, aplica lo mismo. Es solo que no hay una sola hoja de cálculo de números para multiplicar, sino un gran conjunto de ellas, y en muchos casos, usa un procesador especial para construir y / o usar esos modelos.

## Repositorio con códigos de la sesión.

::::{card-carousel} 1

:::{card}
:margin: 3
:class-body: text-center
:class-header: bg-light text-center
:link: https://github.com/johanpina/concurrency_parallelism
**💬 Repositorio de apoyo**
^^^
```{image} https://images.seeklogo.com/logo-png/30/2/github-logo-png_seeklogo-304612.png
:height: 100
```

Repositorio con ejemplos de concurrencia y paralelismo en Python.
+++
Explore this repo {fas}`arrow-right`
:::
::::
