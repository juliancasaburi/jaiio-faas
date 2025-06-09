# Tabla 1: Resumen de Conceptos y Servicios de AWS (Capítulo 4)

Esta tabla proporciona una descripción comparativa de los conceptos discutidos en el Capítulo 4, contrastando los enfoques típicos utilizados en `AWS Lambda` (FaaS) con los requeridos para una aplicación monolítica implementada en `Amazon EC2` (IaaS).

*   **Característica/Concepto:** Identifica el área técnica específica que se está discutiendo.
*   **Enfoque con AWS Lambda (FaaS):** Describe cómo la característica es manejada inherentemente o implementada típicamente dentro del entorno sin servidor de Lambda.
*   **Enfoque con EC2 Monolítico (IaaS):** Describe cómo la misma característica es gestionada o necesita ser configurada explícitamente dentro de una aplicación monolítica tradicional que se ejecuta en instancias de EC2.
*   **Consideraciones Clave para la Migración:** Destaca las decisiones críticas, desafíos o cambios requeridos al realizar la transición del enfoque FaaS al enfoque IaaS para esa característica específica.

| Característica/Concepto | Enfoque con AWS Lambda (FaaS) | Enfoque con EC2 Monolítico (IaaS) | Consideraciones Clave para la Migración |
| :--- | :--- | :--- | :--- |
| **Estructura del Código** | *Lambdalith* (función única) o Funciones de Propósito Único. | Lógica de la aplicación consolidada. | La complejidad depende de la estructura original de FaaS; se necesita refactorización para la mantenibilidad. |
| **Gestión de Estado** | Sin estado; requiere almacenamiento externo (ej., `DynamoDB`, `S3`). | Puede ser con estado (*sticky sessions*) o sin estado (preferido para escalar). | Garantizar la falta de estado para el escalado horizontal o gestionar el estado apropiadamente (ej., caché/BD externa). |
| **Programación Orientada a Eventos** | Integración nativa con servicios de AWS (ej., *triggers* de `S3`). | Requiere implementación explícita (ej., colas de mensajes como `SQS`, *polling*). | Replicar los *triggers* de eventos usando colas y consumidores dentro del monolito o mediante servicios externos. |
| **Limitación de Tasa (*Rate Limiting*)** | Generalmente manejado a través de `Amazon API Gateway`. | Implementado en la aplicación (en memoria para una sola instancia, BD externa como `Redis` para escalar) o mediante un *reverse proxy*/*API Gateway*. | Elegir la implementación según las necesidades de escalado y la infraestructura existente (ej., NGINX, `Amazon API Gateway`). |
| **Transacciones de Base de Datos** | Las transacciones distribuidas a menudo usan el patrón SAGA. | Transacciones ACID estándar dentro del monolito. | Consolidar la lógica de transacciones distribuidas; la complejidad de SAGA se elimina. |
| **Autenticación** | Gestionada mediante servicios en la nube (ej., `Amazon Cognito`, Auth0). | Opciones: Mantener el servicio en la nube (SDK/API), implementar SSO (ej., Keycloak), gestionar dentro del monolito (ej., JWT). | Decidir si mantener el proveedor de autenticación externo o incorporar la lógica de autenticación en el monolito. |
| **Registro y Monitoreo** | Integración automática con `CloudWatch Logs`. | Requiere una configuración de registro centralizada (ej., Logstash, Agente de CloudWatch) y monitoreo (ej., `CloudWatch`, Prometheus). | Implementar una agregación de registros robusta y monitoreo en todas las las instancias. |
| **Permisos de Servicios de AWS** | Políticas granulares de `IAM` adjuntas directamente a las funciones. | Rol de `IAM` adjunto a la(s) instancia(s) de `EC2` o credenciales gestionadas para alojamiento fuera de AWS. | Definir roles de `IAM` apropiados para las instancias de `EC2` según el acceso a servicios requerido. |
| **Alta Disponibilidad** | Por defecto (desplegado en múltiples `Availability Zones`). | Requiere configuración explícita (múltiples instancias en *Availability Zones*, *Load Balancer*, *Auto Scaling*). | Diseñar una estrategia de despliegue para Alta Disponibilidad si es necesario (`Auto Scaling Groups`, múltiples `Availability Zones`); no siempre es requerido, ofreciendo ahorros de costos potenciales. |

---

### **Resumen de Servicios de AWS**

*   **AWS Lambda**: Es un servicio de computación sin servidor (*serverless*) que ejecuta código en respuesta a eventos y gestiona automáticamente los recursos computacionales.
    *   [Documentación de AWS Lambda](https://docs.aws.amazon.com/lambda/)
*   **Amazon EC2 (Elastic Compute Cloud)**: Es un servicio web que proporciona capacidad de computación redimensionable en la nube. Permite a los desarrolladores tener control total sobre sus recursos de computación, similar a tener servidores físicos.
    *   [Documentación de Amazon EC2](https://docs.aws.amazon.com/ec2/)
*   **Amazon DynamoDB**: Es un servicio de base de datos NoSQL totalmente gestionado que ofrece un rendimiento rápido y predecible con una escalabilidad perfecta.
    *   [Documentación de Amazon DynamoDB](https://docs.aws.amazon.com/dynamodb/)
*   **Amazon S3 (Simple Storage Service)**: Es un servicio de almacenamiento de objetos que ofrece escalabilidad, disponibilidad de datos, seguridad y rendimiento líderes en la industria.
    *   [Documentación de Amazon S3](https://docs.aws.amazon.com/s3/)
*   **Amazon SQS (Simple Queue Service)**: Es un servicio de colas de mensajes totalmente gestionado que permite desacoplar y escalar microservicios, sistemas distribuidos y aplicaciones sin servidor.
    *   [Documentación de Amazon SQS](https://docs.aws.amazon.com/sqs/)
*   **Amazon API Gateway**: Es un servicio totalmente gestionado que facilita a los desarrolladores la creación, publicación, mantenimiento, monitorización y protección de APIs a cualquier escala.
    *   [Documentación de Amazon API Gateway](https://docs.aws.amazon.com/apigateway/)
*   **Amazon Cognito**: Permite añadir de forma rápida y sencilla el registro, inicio de sesión y control de acceso de usuarios a las aplicaciones web y móviles.
    *   [Documentación de Amazon Cognito](https://docs.aws.amazon.com/cognito/)
*   **Amazon CloudWatch**: Es un servicio de monitorización y observabilidad para los recursos y aplicaciones de AWS y en las instalaciones.
    *   [Documentación de Amazon CloudWatch](https://docs.aws.amazon.com/cloudwatch/)
*   **AWS IAM (Identity and Access Management)**: Es un servicio web que ayuda a controlar de forma segura el acceso a los recursos de AWS. Se utiliza `IAM` para controlar quién está autenticado (ha iniciado sesión) y autorizado (tiene permisos) para utilizar los recursos.
    *   [Documentación de AWS IAM](https://docs.aws.amazon.com/iam/)
*   **Availability Zones (Zonas de Disponibilidad)**: Son ubicaciones aisladas dentro de las regiones de AWS que están diseñadas para estar aisladas de fallos en otras zonas de disponibilidad. Proporcionan una alta disponibilidad para las aplicaciones.
    *   [Documentación de AWS Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)
*   **Auto Scaling Groups**: Permiten escalar automáticamente la capacidad de `Amazon EC2` hacia arriba o hacia abajo según las condiciones que se definan para asegurar que se tiene el número correcto de instancias disponibles para manejar la carga de la aplicación.
    *   [Documentación de AWS Auto Scaling](https://docs.aws.amazon.com/autoscaling/)
