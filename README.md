# Sistema Maestro-Detalle con Control de Reglas de Negocio en BD

Este proyecto implementa una arquitectura desacoplada utilizando una interfaz visual para la gestión de flujos de trabajo enlazada a un motor de base de datos relacional. Toda la lógica transaccional y las restricciones críticas de negocio se encuentran delegadas y blindadas mediante **Stored Procedures**, garantizando la integridad de los datos independientemente del cliente que consuma los servicios.

## 🏗️ Arquitectura del Entorno

La solución se encuentra empaquetada y automatizada utilizando contenedores, aislando el motor de persistencia y facilitando su despliegue inmediato.

```text
nombre-de-tu-proyecto/
├── database/
│   └── init_db.sql          # Scripts de inicialización y procedimientos
├── forms-app/               # Binarios y archivos fuentes de la interfaz
└── docker-compose.yml       # Orquestación de los contenedores


Despliegue del Entorno (Contenedores)

Para levantar el entorno de desarrollo de manera local y automatizada, ejecute los siguientes comandos en su terminal:
1. Construir y levantar los servicios

Para iniciar el proceso de descarga y configuración de los contenedores en segundo plano:
Bash

docker compose up -d

2. Verificar el estado de los contenedores

Para asegurarse de que los servicios asignados a  e infraestructura estén operando correctamente:
Bash

docker ps

3. Monitorear los logs de inicialización

Si desea auditar la correcta ejecución de los scripts de inicialización automatizados (init_db.sql):
Bash

docker logs <nombre-de-contenedor> -f

4. Detener el entorno de desarrollo

Para pausar los servicios sin destruir los volúmenes de datos persistentes:
Bash

docker compose down

Diseño del Modelo y Reglas de Negocio Blindadas

El diccionario de datos se autoconfigura al iniciar el contenedor. La lógica implementada en  restringe las siguientes acciones operativas:

    Inserción Segura: No se permite añadir registros dependientes (detalles) a un nodo maestro cuyo estado operativo figure como 'COMPLETED'. El intento gatilla una excepción RAISE_APPLICATION_ERROR.

    Eliminación Restringida: Queda prohibido alterar o remover el historial de elementos dependientes si el elemento maestro se encuentra cerrado.

    Flujo Inverso Automatizado: Al completarse de forma legítima el último elemento del detalle, un desencadenador o procedimiento evalúa el lote y promueve automáticamente el estado del elemento maestro a 'COMPLETED'.

💻 Integración con el Cliente Visual

La interfaz de usuario interactúa directamente a través de disparadores nativos (Triggers) interceptando el ciclo de vida del guardado:

    PRE-INSERT / PRE-DELETE: Invoca los procedimientos alojados en <nombre-de-contenedor>, capturando excepciones globales mediante RAISE FORM_TRIGGER_FAILURE para evitar el tráfico innecesario hacia la red si la regla de negocio es violada.


---

### 🏁 ¡Proyecto Culminado!
