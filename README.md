# Proyecto: Automatizando el despliegue de infraestructura en la nube con Terraform y Jenkins

**Autor:** Victor Ponce | **Contacto:** [Linkedin](https://www.linkedin.com/in/victorhugoponce) | **Website:** [victorponce.com](https://victorponce.com)

## 1. Descripción General 

En este proyecto diseñé una arquitectura en AWS para sostener un ecosistema encargado del despliegue automatizado de infraestructura en la nube, compatible tanto para AWS, Azure y GCP.

## 2. Arquitectura

<div align="center">

<img src="README/diagram-spanish.png" width="700">

**(img. 1 – Arquitectura de entorno Jenkins)**

</div>

### Componentes principales:

**Instancia EC2:** sobre ella corren un contenedor de Jenkins y otro contenedor con Terraform.

<div align="center">

<img src="README/verify_containers.png" width="700">

**(img. 2 – Contenedores Jenkins y Terraform corriendo en host EC2)**

</div>

**ECR:** almacena las imágenes Docker de Jenkins y Terraform previamente construidas, las cuales se utilizan para levantar ambos contenedores.

**Docker Compose:** se encarga de la orquestación y gestión de los contenedores. 

**ALB:** el Application Load Balancer se encarga de direccionar las peticiones encriptadas hacia la instancia EC2.

**Route 53:** se utiliza un dominio único para exponer Jenkins.

**GitHub:** repositorio para el código de Terraform.

**Jenkins:** herramienta de automatización y CI/CD de código abierto que nos permite crear, probar y desplegar infraestructura como código y software de forma automática mediante pipelines. En este proyecto se integra con Docker para interactuar con Terraform a través de un contenedor y generar infraestructura en AWS.

**Terraform:** herramienta de infraestructura como código (IaC) que permite definir, provisionar y gestionar infraestructura de forma declarativa y automatizada.

## 3. Flujo automatizado:

1. El flujo comienza con un commit hacia el branch main.

2. Se dispara un webhook que notifica a Jenkins.

3. Jenkins ejecuta un Job que corre un pipeline con los comandos terraform init y terraform plan.

<div align="center">

<img src="README/jenkins_job_pipeline.png" width="700">

**(img. 3 – Jenkins Pipeline (init/plan)**

</div>

4. Si el Job finaliza sin errores, se ejecuta nuevamente el Job, esta vez añadiendo terraform apply, lo que despliega la infraestructura en AWS.

<div align="center">

<img src="README/jenkins_job_build_apply_pipeline.png" width="700">

**(img. 4 – Jenkins Pipeline (apply)**

</div>

## 4. Seguridad:

**Grupos de seguridad:** permiten el ingreso únicamente desde IPs conocidas.

**ALB con ACM:** el tráfico entre el equipo DevOps y el entorno con Jenkins se encuentra encriptado.

**Subnet privada:** el cómputo se ejecuta dentro de una subnet privada.

## 5. Beneficios:

Ejecutar Jenkins en infraestructura propia permite tener control total del flujo de CI/CD, además de opciones de automatización prácticamente ilimitadas, a diferencia de soluciones administradas como GitHub Actions o Terraform Cloud.


## 6. Desventajas:

**Costos:** la infraestructura necesaria para operar Jenkins de forma segura puede ser más costosa que otras alternativas. Sin embargo, esta diferencia se justifica cuando el volumen de trabajo es mayor.

**Requerimientos técnicos:** se requiere un buen conocimiento de Jenkins, contenedores, networking y seguridad, ya que toda la administración recae en nuestras manos. 


## 7. Conclusiones:

Este proyecto me tomó más tiempo del que tenía contemplado. Durante la fase de diseño me di cuenta de que iba a necesitar el uso de contenedores para generar una arquitectura de trabajo inmutable, capaz de levantarse nuevamente en minutos en caso de fallas o corrupción del entorno.

Me encontré con varios errores que me hicieron darme cuenta de que CloudFront no era una buena opción para este tipo de arquitectura. Finalmente, el proyecto presentado cumple con los objetivos de automatización, seguridad y estabilidad necesarios para un entorno productivo DevOps.