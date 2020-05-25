# Justificación de arquitectura Banco ABC

## Description

documentación del proyecto de pago de servicios por medio de una entidad bancaria.

## Table of contents <a name="table-of-contents-main"></a>
1. [Patrones][a-description].
2. [Estilo de realización de servicios][vp-description].
3. [TradeOff] (#tradeoff).
4. [Estilo de arquitectura][vp-description].
5. [Artefactos de servicios][vp-description].

![alt text][fig1]

Figura 1: Módelo de procesos

![alt text][fig2]

Figura 2: Diagrama de compoenentes

![alt text][fig3]

Figura 3: Módelo entidad relación 

[a-description]: /wiki/Pagina-1
[vp-description]: /wiki/Pagina-1

[fig1]: /img/DP_Servicios.png "Módelo de procesos"
[fig2]: /img/COMPD_Servicios.jpg "Diagrama de compoenentes"
[fig3]: /img/mer.jpg "Módelo entidad relación"

# TradeOff
| Decisión de Arquitectura | Descripción                                                                                                                                                                                                                               | Consecuencias (Contras)                                                                                                                                                                                                                 |
|:------------------------:|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Coreografía              | Debido a la necesidad de comunicación entre varios servicios, la coreografía nos permite la interacción entre varios para un objetivo común.                                                                                              | Al ser distribuido los puntos de fallos pueden ser múltiples y es difícil conocer los estados del flujo.                                                                                                                                |
|                          | Permite desacoplar las peticiones de los servicios de los proveedores, el coreógrafo puede enviar peticiones asíncronas a diferentes servicios.                                                                                           | Al trabajar de manera asincrónica, tiene que contar con los recursos totalmente suficientes para ir enrutando mensajes y dejar servicios en cola en caso de ser necesario.                                                              |
|                          | Puede utilizar un transformador para poderse comunicar con cualquier servicio sin importar su implementación, para colaborar entre sí.                                                                                                    | Aunque parte de la abstracción, al tratar de escalar una funcionalidad puede afectar todo el flujo debido que es un trabajo conjunto.                                                                                                   |
| SOA                      | Para realizar un trabajo conjunto entre varias partes como lo es el coreógrafo - transformador - enrutador.                                                                                                                               | La implementación de SOA suele ser un poco compleja y hay que dedicar más tiempo de desarrollo para su realización.                                                                                                                     |
|                          | Ayuda en el procesamiento de independencia de los servicios a trabajar conjuntamente, por ello reduce el acoplamiento entre ellos.                                                                                                        | Para una buena implementación de este, se debe tener un buen análisis apoyándose de herramientas como MDD, lo cual hace que sea necesario conocer y plasmar los procesos de negocio necesarios.                                         |
| Intermedia Routing       | Soluciona la complejidad del intercambio de datos punto a punto de un sistema que parte de la composición.                                                                                                                                | En el momento de modificación dinámica del enrutamiento y sus reglas pone en riesgo la lógica.                                                                                                                                          |
|                          | Debido que se quiere solucionar el problema mediante coreografía el motor de enrutamiento puede simular el proceso de centralizar las peticiones.                                                                                         | Al tener un componente de enrutamiento este sobrecarga el rendimiento de la aplicación.                                                                                                                                                 |
| Back-Ends for Front-Ends | Con el fin de tener una disposición de código separado optimizado permite tener un gateway para cada interfaz de acceso.                                                                                                                  | Conlleva mucho más tiempo de desarrollo ya que no desarrollaría 1 api gateway, si no 3 diferentes optimizados para cada canal.                                                                                                          |
|                          | Disminuye el número de solicitudes (Request / Response) de la transacciones.                                                                                                                                                              | Los llamados pueden verse afectados en su tiempo de respuesta debido al salto de red adicional.                                                                                                                                         |
| Dispatcher               | Al tener servicios que se comunican de diferentes maneras, es necesario tener un dispatcher abstracto que defina los contratos de la petición.                                                                                            | El desarrollo de estos componentes debe tener en cuenta cualquier escenario que se puedan presentar debido que todos los servicios aunque se comuniquen igual pueden variar entre ellos. Esto implica un tiempo alto para su ejecución. |
|                          | Permite una comunicación y ejecución junto con el coreógrafo, lo que permite la comunicación del flujo esperado.                                                                                                                          | Se debe agrega una lógica en el enrutador debido que además de saber a dónde ir, tiene que sabe como debe transformarlos y desde que dispatcher debe ejecutarlos.                                                                       |
| Publisher -Subscriber    | Al tomar la decisión de hacer uso  de coreografía en vez de orquestación, el coreógrafo actúa como publicador para enviar mensajes a los servicios de los proveedores y a su vez suscriptor para tomar las respuestas de estos servicios. | Se debe pensar en alguna tecnología que realice esto debido que el desarrollo de esta funcionalidad puede ser complejo, y no cumplir con lo esperado.                                                                                   |
|                          | Este permitiría la agilidad de los procesos y que puedan ser asíncronos, sin estar esperando una respuesta de una solicitud para poder enviar otra.                                                                                       | El proceso de mensajería tiene proporcionar mecanismos que los consumidores puedan utilizar para suscribirse o dejar de usar de los canales disponibles.                                                                                |