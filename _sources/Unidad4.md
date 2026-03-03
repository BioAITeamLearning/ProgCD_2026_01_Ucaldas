---
title: Unidad 4
---

# Unidad 4: Hilos y sockets


## Contenido de la unidad

<img src="_static/images/contenidoU4.png"/>

## Introducción a Hilos

### Concepto de procesos

#### ¿Cómo funciona a nivel de hardware?

Imagina que la memoria RAM de tu computadora es como una gran pizarra.

Cuando un proceso inicia (por ejemplo, el navegador web), el sistema operativo le asigna un espacio de la memoria en esa pizarra. Este espacio es privado para ese proceso. Se divide en secciones como:

* **Área de Código**: Donde está el programa en sí (las instrucciones que se van a ejecutar).

* **Área de Datos:** Donde se guardan las variables globales.

* **Heap**: Una zona de memoria para variables creadas dinámicamente (por ejemplo, con new en Java o C++).

* **Stack**: Un área de memoria para las variables locales de cada función.

<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*7OcxF0ZUFinmq14bzqFjYg.png"/>

Ahora, cuando creas un hilo dentro de ese proceso, el sistema operativo no le asigna un nuevo espacio de memoria completo. En su lugar, el nuevo hilo comparte la mayoría de las áreas que ya existen: el código, los datos y el heap.

La única parte que es privada para cada hilo es su propio Stack. En el stack se almacenan las variables locales de las funciones que el hilo está ejecutando.

#### ¿Qué hace el SO?

Aquí es donde entra el concepto de planificación o scheduling del sistema operativo (OS).

El núcleo del procesador es la unidad física que realmente ejecuta las instrucciones. Piensa en él como un operario muy, muy rápido 👷‍♂️. Ese operario solo puede concentrarse en una lista de tareas (un hilo) a la vez.

El **sistema operativo** actúa como el jefe de obra 🧑‍💼. Su trabajo es:

Cargar el hilo en el núcleo: Toma las instrucciones del hilo y se las pasa al núcleo para que las ejecute.

Sacar el hilo del núcleo: Después de un tiempo muy corto (milisegundos), detiene la ejecución de ese hilo y lo guarda, anotando exactamente dónde se quedó.

Cambiar de contexto: Carga otro hilo en el mismo núcleo. Este proceso de guardar y cargar un hilo se llama cambio de contexto (context switch).

El sistema operativo hace esto tan rápido y continuamente que a nosotros, los humanos, nos da la ilusión de que todos los hilos se están ejecutando al mismo tiempo en el mismo núcleo. Esto se conoce como concurrencia. Sin embargo, en un núcleo físico, la ejecución es secuencial, un hilo después del otro.

### Concepto de hilos (threads)

* Múltiples tareas al mismo tiempo de manera concurrente

* Comparte recursos con otros hilos en el mismo proceso.

<img src="_static/images/U4_1.jpg"/>

Los hilos o "threads" son una forma de permitir que un programa realice múltiples tareas al mismo tiempo de manera concurrente, y una unidad básica de ejecución de código que comparte recursos con otros hilos en el mismo proceso. 

Esto nos permite aprovechar las capacidades multiprocesador de nuestras máquinas para ejecutar varias instrucciones a la vez, como subprocesos independientes.

### Procesos versus hilos (processes versus threads)

Un proceso puede constar de varios hilos paralelos, y la creación y gestión de un hilo por parte del sistema operativo es generalmente menos costosa en términos de recursos de CPU que la creación y gestión de un proceso. Los hilos se utilizan para tareas pequeñas, mientras que los procesos se utilizan para tareas más pesadas, como la ejecución de aplicaciones, y los hilos de un mismo proceso comparten el espacio de direcciones y otros recursos, a diferencia de los procesos, que son independientes entre sí.

::::{grid}
:gutter: 3

:::{grid-item-card}
:class-body: text-right
:class-header: bg-light text-center
```{dropdown} Procesos
* Un proceso puede constar de varios hilos.
* La creación y gestión  de un proceso es costosa en recursos de CPU.
* Se utilizan en tareas pesadas (ejecución de aplicaciones).
* Los procesos son independientes entre sí.

```
:::

:::{grid-item-card}
:class-body: text-right
:class-header: bg-light text-center
```{dropdown} Hilos
* El hilo es la unidad fundamental de ejecución del código.
* La creación de un hilo en el SO es menos costosa en recursos de CPU.
* Se utilizan en tareas pequeñas.
* Los hilos de un mismo proceso comparten el espacio de direcciones y otros recursos.
```
:::

::::

### Ventajas de los hilos

- **Mejora del rendimiento** al permitir la ejecución concurrente de tareas, especialmente en sistemas con múltiples núcleos de procesador.

- **Uso eficiente de recursos** al compartir el mismo espacio de memoria y recursos del sistema, lo que los hace más ligeros en comparación con los subprocesos.

- **Comunicación rápida y eficiente entre hilos** debido al uso del mismo espacio de memoria.

- **Tiempo de respuesta mejorado** al permitir la ejecución paralela o intercalada de tareas, lo que es especialmente útil en aplicaciones de interfaz gráfica (un hilo puede manejar la entrada del usuario mientras otro hilo realiza tareas de procesamiento en segundo plano.)

```{dropdown} Ventajas

<img src="_static/images/U4_2.jpg"/>

```

### Desventajas de los hilos
- **Problemas de sincronización** pueden ocurrir cuando se accede y modifica datos compartidos entre hilos, lo que puede causar condiciones de carrera y bloqueos si no se manejan adecuadamente.

- En CPython, el **Global Interpreter Lock (GIL) limita el aprovechamiento del paralelismo real** en sistemas con múltiples núcleos de procesador, aunque la concurrencia sigue siendo posible.

- **La depuración de aplicaciones multihilo puede ser más complicada** debido al comportamiento no determinista de los hilos.

- **Un error en un hilo puede afectar a otros hilos y al proceso completo** debido al aislamiento limitado entre los hilos que comparten el mismo espacio de memoria.

```{dropdown} Desventajas

<img src="_static/images/U4_3.jpg"/>

```
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*wax7jfcrYCSDlBBNEbb6QQ.png">

**¿Qué problemas crees que podrían surgir si dos hilos intentan escribir en la misma variable global al mismo tiempo?**

```{note}
**Gemini**

https://g.co/gemini/share/23b7e4b80265 

```

## La clase Thread y sus métodos principales

### Herramienta para los hilos en Python
El subprocesamiento del módulo de Python tiene el método **Thread()**:


**class threading.Thread(group=None, target=None, name=None, args=(), kwargs={})**

<img src="_static/images/U4_4.jpg"/>

* **group**: Este es el valor del grupo que debe ser None; esto está reservado para futuras implementaciones.
* **target**: Esta es la función que se ejecutará cuando inicie una actividad de subproceso.
* **name**: Este es el nombre del hilo; por defecto, se le asigna un nombre único de la forma Thread-N.
* **args**: Esta es la tupla de argumentos que se pasarán a un destino.
* **kwargs**: Este es el diccionario de argumentos de palabras clave que se utilizarán para la función de destino.


## Ambientes de ejecución
### Configuremos nuestros ambientes locales
¿Por qué pasarnos a trabajar localmente? ¿Por qué no usar Colab?

<img src="_static/images/U4_5.jpg"/>

- El colab solo tiene dos procesadores.
- Las salidas de las celdas presentan retrasos, por lo que lo que se escribe no es en tiempo real y eso es crítico para nuestros temas.
- Los gestores de ambientes y paquetes como Anaconda ayudan a aislar los entornos de desarrollo que se usan para diferentes proyectos.
- VSC es una herramienta completa para desarrolladores y permite integrar muchos ambientes, diferentes lenguajes y extensiones.

### Instalemos y configuremos
#### Python
<img src="_static/images/U4_6.jpg"/>

```{tip}
<a href="https://docs.anaconda.com/anaconda/install/windows/" target="_blank">Installing on Windows — Anaconda documentation</a>
```

<img src="_static/images/U4_7.jpg"/>

#### Java
Hacer la configuración correspondiente para trabajar con Java.

::::{card-carousel} 1

:::{card}
:margin: 3
:class-body: text-center
:class-header: bg-light text-center
:link: https://github.com/BioAITeamLearning/ProgCD_2023_03_Ucaldas/blob/main/Unidades/Unidad_4/Configuracion-WSL-Notebooks-Java.docx

**💬 Configurar Java**
^^^
```{image} https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/Google_Docs_2020_Logo.svg/558px-Google_Docs_2020_Logo.svg.png
:height: 100
```

Configuración subsistema Linux y notebooks de java Locales
+++
Explore this book {fas}`arrow-right`
:::
::::

## Creación y uso de hilos
### Herramienta para los hilos en Python

La forma más sencilla de usar un subproceso es **instanciarlo** con una **función de destino** como parámetro y luego llamar al método **start()** para que comience a realizar el hilo. 

```{note}
**Time code**

Analizar el notebook llamado **Hilos_en_Python.ipynb**

```

### Herramienta para los hilos en Java
Asegurarse de haber realizado correctamente la configuración del entorno con Java.

```{note}
**Time code**

Analizar el notebook llamado **HilosJavaBasico.ipynb**
```
<!--

### Tarea 1
```{note}

Realizar la **Tarea 1** de esta Unidad 4.

```




### Tarea 2
```{note}

Realizar la **Tarea 2** de esta Unidad 4.

```

-->