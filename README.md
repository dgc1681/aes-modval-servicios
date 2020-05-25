# Justificación de arquitectura Banco ABC

## Descripción

Este repositorio tiene como fin documentar el proyecto de pago de servicios por medio de una entidad bancaria, aquí se encontrará la definición de la arquitectura propuesta para el ejercicio de la asignatura Modelado y Validación de Arquitectura de la especialización Arquitectura Empresarial de Software.

Integrantes:
- Johan Miguel Céspedes Ortega
- Diego Armando Gómez Cuervo 
- Juan Pablo Reyes Cárdenas

## Tabla de contenido <a name="table-of-contents"></a>

1. [Estilo de arquitectura](#estilo-arquitectura).
2. [Patrones y herramientas](#patrones-herramientas).
3. [Arquitectura](#arquitectrua)
    1. [Vista de proceso](#vista-proceso)
    2. [Vista estructural](#vista-estructural)
4. [Justificación de arquitectura](#justificacion-arquitectura).
    1. [Desciciones](#decisiones-arquitectura)
    2. [TradeOff](#tradeoff).
5. [Artefactos](#artefactos).
    1. [API Gateway](#api-gateway)
    2. [Gestion de convenios](#gestion-convenios)
    3. [Enrutador](#enrutador)
    4. [Transformador](#transformador)
    5. [Dispatcher REST](#dispatcher-rest)
    6. [Dispatcher SOAP](#dispatcher-soa)
    7. [Orquestador](#orquestador)
6. [Referencias](#referencias).

## 1. Estilo de arquitectura <a name="estilo-arquitectura"></a>
## 2. Patrones y herramientas <a name="patrones-herramientas"></a>
## 3. Arquitectrua <a name="arquitectrua"></a>
## 3.1 Vista de proceso <a name="vista-proceso"></a>
A continuación, se presenta en la figura 1 la vista de proceso ilustrada por un diagrama de archi en la cuál se presenta el 
flujo del proceso para el pago de servicios por medio de los canales dispuestos por el banco. 
![alt text][fig1]

Figura 1: Modelo de procesos

En este diagrama se describe una capa de negocio, una capa de aplicaciones y servicios junto con la definición del actor Cliente
vinculado a su rol de usuario.

En la capa de negocio se muestra el proceso de Gestión de pagos y exponiendo el servicio de pagos para el uso del rol usuario.

En la capa de aplicaciones y servicios se dispone un servicio de aplicaciones denominado "pago de servicios" el cual contiene 
los tres servicios para cada uno de los canales disponibles (web, móvil, cajeros). Estos servicios son expuestos por cada una 
de las aplicaciones en su respectivo canal.

Para estas aplicaciones se dispone una capacidad que sirve como intermediarios entre las peticiones que realizan las aplicaciones y
los servicios que se componen para responder a estas peticiones.

En el último nivel se encuentran los servicios que interactúan con la capacidad de intermediación e interoperan entre ellos para 
generar una respuesta a cada una de las aplicaciones.

## 3.2 Vista estructural <a name="vista-estructural"></a>
Descripción del modelo
![alt text][fig2]

Figura 2: Diagrama de compoenentes

Descripción del modelo
![alt text][fig3]

Figura 3: Módelo entidad relación

## 4. Justificación de arquitectura <a name="justificacion-arquitectura"></a>

## 4.1 Decisiones de arquitectura <a name="decisiones-arquitectura"></a> 

## 4.2. TradeOff de la Arquitectura <a name="tradeoff"></a>

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

## 5. Artefactos <a name="artefactos"></a>

1. [API Gateway][contrato-apigateway]
2. [Gestión de convenios][contrato-gestion]
3. [Enrutador][contrato-enrutador]
4. [Transformador][contrato-transformador]
5. [Dispatcher REST][contrato-dispatcher-rest]
6. [Dispatcher SOAP][contrato-dispatcher-soap]
7. [Orquestador][contrato-orquestador]

## 6. Referencias <a name="referencias"></a>

1. [Service-Oriented Design (Service Contract)][soa]
2. [SOA principles - Standarized service contract][contract]
3. [Patron Intermediate Routing][intermediate-routing]
4. [Patron Api Gateway][apigateway]

[contrato-apigateway]: contratos/apiGateway/ApiGateway.raml
[contrato-gestion]: contratos/convenioContrato/Convenio.raml
[contrato-enrutador]: contratos/Enrutador.raml
[contrato-transformador]: contratos/Transformador.raml
[contrato-dispatcher-rest]: contratos/Enrutador.raml
[contrato-dispatcher-soap]: contratos/Enrutador.raml
[contrato-orquestador]: contratos/contratoKafka/Kafka.raml

[contract]: https://es.slideshare.net/MohamedZakarya2/soa-principles-1-standarized-service-contract
[intermediate-routing]: https://patterns.arcitura.com/soa-patterns/design_patterns/intermediate_routing
[apigateway]: https://microservices.io/patterns/apigateway.html
[soa]: https://patterns.arcitura.com/soa-patterns/basics/soaproject/serviceorienteddesign

[fig1]: /img/DP_Servicios.png "Módelo de procesos"
[fig2]: /img/COMPD_Servicios.jpg "Diagrama de compoenentes"
[fig3]: /img/mer.jpg "Módelo entidad relación"