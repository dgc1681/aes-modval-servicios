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
La arquitectura orientada a servicios (SOA) es un estilo arquitectónico que nos permite de una manera desacoplada definir funciones de negocio de una empresa, además de ello define que la comunicación entre mensajes sea un protocolo necesario para realizar un flujo de un proceso. <br/>
Por ello, este modelo ayudará a resolver la problemática del banco ABC la cual es la interacción con sus proveedores de servicios, ofreciendo un diseño basado en los servicios de negocio plasmando las actividades de interacción entre un cliente del banco y un proveedor del servicio como un componente del sistema dentro del contexto de negocio.
Además de ello este cuenta con una seríe de beneficios que permitirán que el diseño de la arquitectura se pueda moldear a través de las implementaciones de patrones y tecnologías. <br/>
Para complementar el cumplimiento mediante este estilo se definió el uso de coreografía por encima de orquestación, debido a la cantidad de solicitudes por procesar y a la respuesta de los proveedores.


## 2. Patrones y herramientas <a name="patrones-herramientas"></a>
PATRÓN NUCLEAR:

SOA: Como se definió anteriormente, este patrón permitirá que todo el desarrollo se rija frente a la formación del proceso a través de la composición de los integrantes de este comunicandose de diferentes maneras, dandole valor al negocio.

PATRONES DE ACOMPAÑAMIENTO:

| Patrón             | Descripción                                                                                                                                                                                                                                                                                                                                                                                 | Rep. Gráfica                                                                                                                                                               |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Intermedia Routing | Patrón utilizado para la comunicación punto a punto, este define el uso de dos enrutadores dentro de la arquitectura para transmitir mensajes a ciertos lugares dentro del flujo del software.                                                                                                                                                                                              | <img src="https://patterns.arcitura.com/wp-content/uploads/2018/09/fig1-71.png" width="500">                                                                               |
| Api - Gateway      | Dado a la definición del primer patrón, es necesario una puerta de enlace hacia las interfaces de consumo, para acceder a este servicio. Para su aplicación se puede hacer uso de herramientas que cuentan frameworks tales como Spring Cloud Gateway, AWS Api Gateway, entre otros.                                                                                                        | <img src="https://microservices.io/i/apigateway.jpg" width="500">                                                                                                          |
| Dispatcher         | Debido que tenemos una comunicación con servidores que están respondiendo a peticiones, es necesario generar componentes granulares que permitan la conexión hacia ellos. Este sólo actuaría como un receptor de solicitudes y envío de mensajes hacia ellos. Estos componentes pueden tener sus funcionalidad a través de clases que realizan la comunicación entre diferentes protocolos. | <img src="https://www.researchgate.net/profile/Ciro_Barbosa/publication/242378736/figure/fig14/AS:669490765365252@1536630445053/Event-dispatcher-pattern.png" width="500"> |

1. Intermedia Routing: Patrón utilizado para la comunicación punto a punto, este define el uso de dos enrutadores dentro de la arquitectura para transmitir mensajes a ciertos lugares dentro del flujo del software.

2. Api - Gateway: Dado a la definición del primer patrón, es necesario una puerta de enlace hacia las interfaces de consumo, para acceder a este servicio. Para su aplicación se puede hacer uso de herramientas que cuentan frameworks tales como Spring Cloud Gateway, AWS Api Gateway, entre otros.

3. Dispatcher: Debido que tenemos una comunicación con servidores que están respondiendo a peticiones, es necesario generar componentes granulares que permitan la conexión hacia ellos. Este sólo actuaría como un receptor de solicitudes y envío de mensajes hacia ellos. Estos componentes pueden tener sus funcionalidad a través de clases que realizan la comunicación entre diferentes protocolos.

4. SAGA: Debido que el sistema del banco se comporta como una secuencia de transacciones, este patron decscribre la definición como un sistema desde una primera transacción desde una solicitud externa, y el paso para para completar la solicitud. Esta funcionalidad de complementa con intermedia routing y da como fin la implementación de la coreografía.

5. Publicador - Suscriptor: Por el uso de coreografía en el desarrollo del proyecto, es necesario pensar que el banco actuará como Publicador (Envío de transacciones) y Suscriptor (Recepción de las respuestas), por ello la importancia del uso de este patrón ya que permite el procesamiento de transacciones. Para su aplicación se puede hacer uso de herramientas como apache camel y kafka.

## 3. Arquitectrua <a name="arquitectrua"></a>
## 3.1 Vista de proceso <a name="vista-proceso"></a>
A continuación, se presenta en la figura 1 la vista de proceso ilustrada por un diagrama de archi en la cual se presenta el flujo del proceso para el pago de servicios por medio de los canales dispuestos por el banco.
 
![alt text][fig1]

Figura 1: Modelo de procesos

En este diagrama se describe una capa de negocio, una capa de aplicaciones y servicios junto con la definición del actor Cliente vinculado a su rol de usuario.

En la capa de negocio se muestra el proceso de Gestión de pagos y exponiendo el servicio de pagos para el uso del rol usuario.

En la capa de aplicaciones y servicios se dispone un servicio de aplicaciones denominado "pago de servicios" el cual contiene los tres servicios para cada uno de los canales disponibles (web, móvil, cajeros). Estos servicios son expuestos por cada una de las aplicaciones en su respectivo canal.

Para estas aplicaciones se dispone una capacidad que sirve como intermediarios entre las peticiones que realizan las aplicaciones y los servicios que se componen para responder a estas peticiones.

En el último nivel se encuentran los servicios que interactúan con la capacidad de intermediación e interoperan entre ellos para generar una respuesta a cada una de las aplicaciones.

## 3.2 Vista estructural <a name="vista-estructural"></a>

La vista estructural se presenta por medio del diagrama de componentes. En este diagrama podemos describir algunos de los principales patrones y decisiones arquitectónicas que se tomaron, decisiones que explicaremos en la siguiente sección.

![alt text][fig2]

Figura 2: Diagrama de componentes

Contamos con los componentes de cada uno de los canales que dispone el banco para que sus clientes realicen el pago de servicios. Cada una de los canales se conecta con su propio api gateway, las peticiones que ingresan por los api gateway y que llevarán el código de factura se conectan a una interface (tópico) expuesta por el orquestador y a la cual está conectado y escuchando el servicio de gestión de convenios, este servicio se encarga de aplicar la lógica necesaria (basada en el modelo de datos de la figura 3) para determinar el convenio a la que pertenece esta factura.

![alt text][fig3]

Figura 3: Modelo entidad relación

Una vez determinado el convenio y con esto el tipo de objeto que necesito enviar, se produce un objeto json a nuevo tópico expuesto por el coreógrafo y el cual se encuentra escuchando la respuesta del servicio de gestión de convenios. En este servicio y con la información suministrada, se determina que tipo de objeto necesito construir, si un objeto json o xml para enviarlo a un siguiente tópico el cual es escuchado por el transformador y este produjera el concepto de factura que posteriormente se enviará al dispatcher que le corresponda según la transformación que se haya aplicado.

## 4. Justificación de arquitectura <a name="justificacion-arquitectura"></a>

## 4.1 Decisiones de arquitectura <a name="decisiones-arquitectura"></a>

Basados en el diagrama de componentes, figura 2 se exponen las siguientes decisiones de arquitectura.

Patrón API Gateway: Adoptar la variación del patrón Api Gateway denominada Backends for frontends (figura 4) por medio de la cual se define una puerta de entrada para cada cliente. Distribuyendo la responsabilidad de cada canal a un único componente.

Patrón intermediate routing: Empleamos este patrón con el uso de api gateway como enrutador que conduce la petición del usuario al coreógrafo para dar inicio al proceso junto con un segundo enrutador el cual se encarga de decidir y enviar un objeto según el tipo de mensaje que se requiera manejar para cada convenio.

Enrutador y transformador: Decidimos usar primero el enrutador y contar con dos opciones de producción en dos tópicos a los que escuchan dos métodos independientes del transformador, de esta forma según los atributos que reciba por parte del servicio de gestión de convenios el enrutador determinará a cuál de los dos tópicos envía un objeto json el cual lleva un payload con un string que tiene la estructura de un objeto json o de xml con los datos que se necesitan para generar en el transformador una representación de la factura la cual se emitirá al dispatcher de REST o al dispatcher de SOAP.

Dispatcher independientes: Se crearon dos dispatcher, uno que recibe y envía por medio de una interfaz todos los mensajes tipo json para enviar a los convenios que manejan la estructura REST y otro que recibe y envia en su interfaz los mensajes xml y que pertenecen a los convenios que manejan mensajes tipo soap. De esta forma podemos garantizar un menor acoplamiento con el objetivo de que al momento de agregar un nuevo convenio solo con tipificarlo ya pueden ser enrutados y transformados los mensajes para que este opere.

Número del convenio: El número de convenio viajará en la estructura de los mensajes que llegaban cualquiera de los dos dispatcher con el objetivo de que el dispatcher sepa a qué servicio debe realizar el pago.

Coreografía: Se seleccionó un esquema de coreografía para la composición de servicios por encima de orquestación, esto por que consideramos que para la composición de los servicios propuestos no se requiere operar bajo un esquema de comunicación  síncrona ya que no consideramos necesario que el usuario obtenga la respuesta inmediata del éxito de la transacción, sino que puede emitir un mensaje de transacción recibida y posteriormente notificar cuando la transacción se haya finalizado. de esta forma generamos una mejor experiencia de usuario basándonos en el atributo de calidad de disponibilidad.

![alt text][fig4]

Figura 4: Representación del patron API Gateway, Variación Backends for frontends

## 4.2. TradeOff de la Arquitectura <a name="tradeoff"></a>

| Decisión de Arquitectura | Fuerzas (Descripción)                                                                                                                                                                                                                               | Consecuencias (Contras)                                                                                                                                                                                                                 |
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
5. [Publish-subscribe messaging system][publish-subscribe]
6. [Event-driven architecture (Saga)][saga]

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
[publish-subscribe]: https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781787283985/1/01lvl1sec5/publish-subscribe-messaging-system
[saga]: https://microservices.io/patterns/data/saga.html

[fig1]: /img/DP_Servicios.png "Módelo de procesos"
[fig2]: /img/COMPD_Servicios.jpg "Diagrama de compoenentes"
[fig3]: /img/mer.jpg "Módelo entidad relación"
[fig4]: /img/bffe.png "API Gateway"
