# WebLogic and Oracle Forms Deployment Roadmap

## Overview
This repository contains the configuration and workflow for deploying a WebLogic Server instance and an Oracle Forms application within a containerized Docker environment. This setup allows for the migration and testing of legacy Oracle applications in isolated, reproducible environments.

## Prerequisites
- Docker Engine
- Docker Compose
- Oracle Installation Media (Required binaries):
  - `fmw_12.2.1.4.0_infrastructure.jar`
  - `fmw_12.2.1.4.0_forms.jar`
  - Oracle JDK 8uXXX

## Database Connectivity
The application relies on a connection to an Oracle Database instance. 
- **Data Source Configuration:** Configured via the WebLogic Administration Console or WLST.
- **Connection Parameters:** 
  - JDBC Driver: `oracle.jdbc.OracleDriver`
  - Connection Pool: Managed by the WebLogic server, ensuring optimal connection reuse.
  - Integration: Credentials and TNS entries must be mapped via the container's environment variables or a secure properties file (not committed to version control).

## Deployment Workflow

### 1. Environment Initialization
The environment relies on persistent volumes to store WebLogic domain data outside of the container lifecycle.
- Ensure the `domain_data/` directory exists in the host machine.
- Define the necessary environment variables for the Oracle Installer in the `.env` file.

### 2. Base Configuration
The deployment utilizes a custom Docker image built from Oracle Fusion Middleware (FMW) base images. 
- The `Dockerfile` must include the FMW Infrastructure and Forms components.
- The `docker-compose.yml` orchestrates the service, mapping local source directories to the container’s deployment paths.

### 3. Middleware Configuration (setDomainEnv.sh)
To resolve `ClassNotFoundException` errors regarding FMW notification listeners, the `PRE_CLASSPATH` must be manually updated in the domain configuration:
- Locate `setDomainEnv.sh` within the domain home.
- Append the path to the required FMW module JARs:
  `export PRE_CLASSPATH=$PRE_CLASSPATH:/u01/oracle/oracle_common/modules/...`

### 4. Deployment Steps
1. Build the container image:
   `docker-compose build`
2. Start the services:
   `docker-compose up -d`
3. Access the WebLogic Administration Console via the configured port (default: 7001).
4. Deploy the EAR/WAR application files using the WLST (WebLogic Scripting Tool) or the Web Console.

## Troubleshooting
- **Dependency Issues:** If the application fails to initialize, verify the existence of `oracle.as.config` modules in the classpath.
- **Log Inspection:** Logs are persisted in `domain_data/nuevo_dominio/logs/`.
- **Persistence:** All domain changes are stored in the volume mapped to `/u01/oracle/user_projects/domains/`.


---

### 🏁 ¡Proyecto Culminado!
