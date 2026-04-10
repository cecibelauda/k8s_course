# Curso de Kubernetes - Apuntes Consolidados

## Días 1, 2, 3, 4, 5 y Gateway - Fundamentos hasta Exposición Externa

---

## Tabla de Contenidos

### Días 1 y 2 - Fundamentos
1. [Introducción al Curso](#1-introducción-al-curso)
2. [¿Qué es Kubernetes?](#2-qué-es-kubernetes)
3. [Kubectl - Herramienta de Línea de Comandos](#3-kubectl---herramienta-de-línea-de-comandos)
4. [Configuración del Repositorio Git](#4-configuración-del-repositorio-git)
5. [Minikube - Kubernetes Local](#5-minikube---kubernetes-local)
6. [Desplegando Synergychat](#6-desplegando-synergychat)
7. [Minikube vs Producción](#7-minikube-vs-producción)
8. [Pods - Unidad Básica de Kubernetes](#8-pods---unidad-básica-de-kubernetes)
9. [Naturaleza Efímera de los Pods](#9-naturaleza-efímera-de-los-pods)
10. [Direcciones IP Únicas](#10-direcciones-ip-únicas)

### Día 3 - Deployments y Configuración YAML
11. [Deployments - Estado Declarativo](#11-deployments---estado-declarativo)
12. [ReplicaSets](#12-replicasets)
13. [Configuración YAML](#13-configuración-yaml)
14. [Desplegando API Service](#14-desplegando-api-service)
15. [Pods que Fallan (Thrashing Pods)](#15-pods-que-fallan-thrashing-pods)

### Día 4 - ConfigMaps y Gestión de Configuración
16. [ConfigMaps - Gestión de Variables de Entorno](#16-configmaps---gestión-de-variables-de-entorno)
17. [Aplicando ConfigMaps a Deployments](#17-aplicando-configmaps-a-deployments)
18. [Seguridad de ConfigMaps](#18-seguridad-de-configmaps)
19. [Desplegando el Crawler](#19-desplegando-el-crawler)
20. [env vs envFrom](#20-env-vs-envfrom)

### Día 5 - Services y Networking
21. [Services - Endpoints Estables](#21-services---endpoints-estables)
22. [Service Types - Tipos de Servicios](#22-service-types---tipos-de-servicios)
23. [API Service - NodePort](#23-api-service---nodeport)
24. [Crawler Service - ClusterIP](#24-crawler-service---clusterip)
25. [Distribución de IPs en Kubernetes](#25-distribución-de-ips-en-kubernetes)
26. [Change API Service](#26-change-api-service)
27. [Cómo se Crean los Pods](#27-cómo-se-crean-los-pods)

### Gateway - Exposición Externa
28. [Gateway - Introducción](#28-gateway---introducción)
29. [DNS Local - /etc/hosts](#29-dns-local---etchosts)
30. [Minikube Tunnel](#30-minikube-tunnel)
31. [Gateway Types y Annotations](#31-gateway-types-y-annotations)
32. [Conectando Frontend y API](#32-conectando-frontend-y-api)

### Sección 6 - Storage (Almacenamiento)
- Storage en Kubernetes
- emptyDir - Almacenamiento Temporal
- Persistent Volumes (PV) y Persistent Volume Claims (PVC)
- Storage Classes
- Configuración de almacenamiento para SynergyChat

### Sección 7 - Namespaces
- Namespaces - Organización Lógica
- Creación y gestión de namespaces
- DNS interno entre namespaces
- Migración del Crawler a namespace dedicado
- Comunicación cross-namespace

### Sección 8 - Observability & Resource Management
- Metrics Server - Monitoreo de recursos
- Resource Requests - Reserva de recursos
- Resource Limits - CPU y Memory
- Breaking the Limits - Pruebas de límites
- Horizontal Pod Autoscaling (HPA)

### Referencia
33. [Consultas Teóricas Frecuentes](#33-consultas-teóricas-frecuentes)
34. [Glosario de Términos](#34-glosario-de-términos)
35. [Comandos Útiles](#35-comandos-útiles)

---

## 1. Introducción al Curso

### Bienvenido a "Aprende Kubernetes"

Este curso es diferente a otros cursos de Boot.dev. Haremos muy poca codificación en el navegador. **Kubernetes** es un sistema distribuido de servidores que alojan aplicaciones de software, e interactúas con él principalmente a través de tu línea de comandos local - no es un lenguaje de programación.

Completarás la mayoría de este curso en tu propia máquina. Se usará una combinación de pruebas basadas en HTTP y cuestionarios para asegurar el progreso.

**Conceptos clave:**
- Kubernetes NO es un lenguaje de programación
- Es un **sistema de orquestación de contenedores**
- Trabajarás principalmente desde tu terminal/línea de comandos local
- El aprendizaje será práctico en tu propia máquina

**📚 Documentación oficial:**
- https://kubernetes.io/docs/home/

---

## 2. ¿Qué es Kubernetes?

### Definición Oficial

> "Kubernetes, también conocido como K8s, es un sistema de código abierto para automatizar el despliegue, escalado y gestión de aplicaciones en contenedores."
> 
> -- El equipo de Kubernetes

### ¿Qué hace Kubernetes?

Kubernetes orquesta y gestiona colecciones de contenedores (frecuentemente usando runtimes de contenedores como containerd). Se encarga del escalado, distribución y conectividad entre estos contenedores. 

**Piénsalo como:** Un sistema para gestionar muchos contenedores y la infraestructura en la que se ejecutan.

### Ejemplo Práctico

Podrías instalar Docker en un solo servidor y dirigir el tráfico directamente a él. Eso es bastante simple de configurar, pero:

- ¿Qué pasa si quieres 10 instancias de ese servidor?
- ¿Y 1000 instancias?
- ¿Qué pasa si quieres desplegar muchos servicios diferentes, cada uno escalando con más instancias dependiendo de la carga?

**Estos son los problemas que Kubernetes resuelve.**

### Conceptos Importantes

- **K8s** = abreviatura de Kubernetes (8 letras entre K y s)
- Kubernetes NO reemplaza a Docker/contenedores, los **orquesta**
- Problema principal que resuelve: **gestionar múltiples contenedores a escala**

### Casos de Uso

- Escalar de 1 a 1000+ instancias automáticamente
- Distribuir carga entre múltiples servicios
- Gestionar alta disponibilidad y recuperación ante fallos

**📚 Referencias:**
- Conceptos básicos: https://kubernetes.io/docs/concepts/overview/

---

## 3. Kubectl - Herramienta de Línea de Comandos

### ¿Qué es Kubectl?

La herramienta de línea de comandos de Kubernetes, `kubectl`, te permite ejecutar comandos contra clústeres de Kubernetes. Es un cliente que se comunica con un servidor API de Kubernetes.

### Instalación

Sigue las instrucciones oficiales de instalación para kubectl:
- https://kubernetes.io/docs/tasks/tools/

**En macOS con Homebrew:**
```bash
brew install kubectl
```

### Verificar la Instalación

```bash
kubectl version --client
```

**Salida esperada:**
```
Client Version: v1.35.0
Kustomize Version: v5.7.1
```

**Nota:** Es normal ver un error de conexión al servidor en este punto, ya que aún no tienes un clúster configurado.

### Conceptos Clave

- `kubectl` = CLI oficial de Kubernetes (pronunciado "kube-control" o "kube-c-t-l")
- Es un **cliente** que habla con el API Server de Kubernetes
- Por ahora solo verás la versión del cliente (normal sin clúster configurado)

**Comando útil:**
```bash
kubectl version --output=yaml  # Más detalles de la versión
```

---

## 4. Configuración del Repositorio Git

### Crear Repositorio

Se recomienda crear un repositorio Git para almacenar todos los archivos de configuración del curso.

**Repositorio creado:** https://github.com/cecibelauda/k8s_course

### Clonar Localmente

```bash
cd ~
git clone https://github.com/cecibelauda/k8s_course.git
cd k8s_course
```

### Archivo .gitignore Recomendado

```gitignore
# Secrets y archivos sensibles
*.key
*.crt
*.pem
secrets.yaml
*-secret.yaml

# Archivos temporales
*.tmp
*.swp
*~

# Configuraciones locales
.kube/
kubeconfig
.env

# IDE
.vscode/
.idea/
*.iml

# macOS
.DS_Store
```

### Estructura Sugerida

```
k8s_course/
├── README.md
├── .gitignore
├── day01/
├── day02/
└── notes/
```

### Configuración de Git

```bash
# Configurar usuario
git config --global user.name "cecibelauda"
git config --global user.email "tu-email@gmail.com"

# Autenticación con token personal de GitHub
git remote set-url origin https://cecibelauda@github.com/cecibelauda/k8s_course.git
```

**Importante:** GitHub requiere Personal Access Token en lugar de contraseña.

---

## 5. Minikube - Kubernetes Local

### ¿Qué es Minikube?

Durante este curso, estaremos usando **Minikube** para practicar con Kubernetes. En producción, probablemente no usarías Minikube, usarías un clúster de servidores, probablemente en la nube. ¡Eso es costoso! 

Minikube es una herramienta fantástica que te permite ejecutar un clúster de Kubernetes de un solo nodo en tu máquina local.

**📚 Documentación oficial:**
- https://minikube.sigs.k8s.io/docs/start/

### Requisitos del Sistema

- 2 CPUs o más
- 2GB de memoria libre
- 20GB de espacio en disco
- Conexión a internet
- Docker o similar instalado

### Instalación con Homebrew (macOS)

```bash
brew install minikube
```

### Verificar Instalación

```bash
minikube version
```

**Salida esperada:**
```
minikube version: v1.38.0
commit: ...
```

### Asegurarse que Docker esté Corriendo

```bash
# Verificar que Docker está activo
docker ps
```

### Iniciar Minikube

```bash
minikube start
```

**Salida esperada:**
```
😄  minikube v1.38.0 on Darwin 26.2 (arm64)
✨  Automatically selected the docker driver
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image...
💾  Downloading Kubernetes v1.35.0 preload...
🔥  Creating docker container (CPUs=2, Memory=4000MB)...
🐳  Preparing Kubernetes v1.35.0 on Docker 29.2.0...
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Verificar Estado del Clúster

```bash
# Ver estado de Minikube
minikube status

# Ver información del clúster
kubectl cluster-info
```

**Salida de `kubectl cluster-info`:**
```
Kubernetes control plane is running at https://127.0.0.1:55894
CoreDNS is running at https://127.0.0.1:55894/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

**Explicación:**
- **Control plane** = El "cerebro" de Kubernetes (gestiona todo el clúster)
- **127.0.0.1:55894** = IP local donde el API Server está escuchando
- **CoreDNS** = Servicio DNS interno de Kubernetes para comunicación entre pods

### Dashboard de Kubernetes

```bash
minikube dashboard --port=63840
```

Esto abrirá una ventana del navegador con un dashboard alojado localmente para tu clúster. Puedes usar este dashboard para ver y gestionar tu clúster.

**Nota importante:** Mantén Minikube corriendo durante todo el curso.

### Comandos Útiles de Minikube

```bash
# Detener Minikube
minikube stop

# Eliminar el clúster
minikube delete

# Ver nodos
kubectl get nodes
```

### Solución de Problemas

Si tienes conflictos con instalaciones previas de Minikube:

```bash
minikube stop
minikube delete
minikube start
```

---

## 6. Desplegando Synergychat

### Introducción a SynergyChat

¡Has sido contratada por SynergyChat! **SynergyChat** es una aplicación de chat habilitada para el metaverso que proporciona información basada en datos a través de modelos de IA de vanguardia que se ejecutan en infraestructura Web 3.

En resumen: es como Discord pero con características adicionales para empresas.

**Para el resto de este curso, estaremos desplegando servicios web de SynergyChat en Kubernetes.**

### Desplegando una Imagen

El comando `kubectl create deployment` creará un "deployment" (despliegue) para nosotros. Para decirlo de manera simple, solo necesitamos proporcionar dos cosas:

1. El nombre del deployment (puede ser cualquier cosa, se usa para identificar el deployment)
2. El ID de la imagen de Docker que queremos desplegar

```bash
kubectl create deployment synergychat-web --image=docker.io/bootdotdev/synergychat-web:latest
```

**Salida esperada:**
```
deployment.apps/synergychat-web created
```

Este comando desplegará un contenedor construido desde la imagen de Docker a tu clúster k8s local.

### Visualizando Deployments

Para asegurarte de que el deployment fue exitoso:

```bash
kubectl get deployments
```

**Salida esperada:**
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
synergychat-web   1/1     1            1           10s
```

### Accediendo a la Aplicación

Por defecto, los recursos dentro de Kubernetes se ejecutan en una red privada y aislada. Son visibles para otros recursos dentro del clúster, pero no para el mundo exterior.

#### Paso 1: Ver los Pods

```bash
kubectl get pods
```

**Salida esperada:**
```
NAME                              READY   STATUS    RESTARTS   AGE
synergychat-web-679cbcc6cd-cq6vx  1/1     Running   0          20m
```

**Nota:** Un pod es una abstracción sobre un contenedor. Para simplificar, un pod es una aplicación en ejecución.

#### Paso 2: Port Forwarding

Para acceder a la aplicación desde tu red local, necesitarás usar `kubectl` para hacer un reenvío de puertos:

```bash
kubectl port-forward PODNAME 8080:8080
```

**Ejemplo con nombre real del pod:**
```bash
kubectl port-forward synergychat-web-679cbcc6cd-cq6vx 8080:8080
```

#### Paso 3: Acceder desde el Navegador

Abre tu navegador y navega a `http://localhost:8080`. 

Deberías ver una página web titulada "SynergyChat". Ten en cuenta que aún no hemos configurado todos los recursos que la página necesita, por lo que los formularios no funcionarán, pero la página debería cargarse.

### Conceptos Clave

- **Deployment** = Objeto de Kubernetes que gestiona réplicas de pods
- **Pod** = Abstracción sobre un contenedor (la unidad mínima desplegable)
- **Port-forward** = Túnel temporal para acceder a recursos internos del clúster desde tu máquina local
- Por defecto, todo en K8s está en red privada (aislado)

### Jerarquía de Conceptos

```
Imagen Docker → Contenedor → Pod → Deployment
```

**📚 Referencias:**
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- Pods: https://kubernetes.io/docs/concepts/workloads/pods/

---

## 7. Minikube vs Producción

### Diferencia Principal

Minikube es una gran herramienta para aprender Kubernetes, pero no es un clúster de Kubernetes a escala de producción. La diferencia principal:

- **Minikube:** Clúster de un solo nodo
- **Producción:** Sistema distribuido de múltiples nodos

### Los Sistemas Distribuidos Son Complejos

Siempre que estés tratando con un sistema que involucra múltiples máquinas comunicándose entre sí a través de una red, estás tratando con un sistema distribuido. 

Los sistemas distribuidos son inherentemente complejos, y Kubernetes no es la excepción, pero esa complejidad generalmente está abstraída de ti como usuario de K8s. **¡Eso es lo que hace que Kubernetes sea tan genial!** Hace mucho del trabajo pesado por ti.

### Recursos y Nodos

El trabajo de Kubernetes es ejecutar aplicaciones de software, y las aplicaciones requieren recursos:

- CPU
- Memoria
- Espacio en disco

Kubernetes gestiona esos recursos y los asigna a las aplicaciones que se están ejecutando.

### Ejemplo de Distribución de Recursos

**3 Nodos (Máquinas):**

| Nodo   | RAM  |
|--------|------|
| Nodo 1 | 16GB |
| Nodo 2 | 8GB  |
| Nodo 3 | 8GB  |

**5 Pods (Aplicaciones):**

| App   | RAM Requerida |
|-------|---------------|
| App 1 | 12GB          |
| App 2 | 2GB           |
| App 3 | 5GB           |
| App 4 | 4GB           |
| App 5 | 4GB           |

**Distribución que Kubernetes podría hacer:**

| Nodo   | Apps                      | RAM Sobrante |
|--------|---------------------------|--------------|
| Nodo 1 | App 1 (12GB), App 2 (2GB) | 2GB          |
| Nodo 2 | App 4 (4GB), App 5 (4GB)  | 0GB          |
| Nodo 3 | App 3 (5GB)               | 3GB          |

### ¿Qué Pasa con Nuevas Aplicaciones?

Si obtenemos una nueva aplicación que requiere 10GB de RAM, el clúster no tiene suficientes recursos para ejecutarla.

**¿La solución?** Fácil. Solo agrega otro nodo al clúster y deja que Kubernetes decida dónde ejecutarla.

### Esto No Funciona Con Minikube

Con Minikube, ¡solo obtienes un nodo! Entonces, una vez que tu máquina se queda sin recursos, no tienes suerte. Por eso Minikube es genial para aprender, pero no para producción.

### Casos Reales de Producción

Hay clústeres de Kubernetes ejecutándose en producción que tienen **miles de nodos**. 

**Ejemplo:** Bloomberg ejecuta cientos de clústeres con miles de nodos cada uno.

### Resumen de Diferencias

| Característica        | Minikube           | Producción         |
|-----------------------|--------------------|--------------------|
| Número de nodos       | 1                  | Cientos/Miles      |
| Propósito             | Aprendizaje        | Aplicaciones reales|
| Alta disponibilidad   | No                 | Sí                 |
| Escalabilidad         | Limitada           | Ilimitada          |
| Tolerancia a fallos   | No                 | Sí                 |

**Kubernetes actúa como un "orquestador de recursos":**
1. Analiza recursos disponibles (CPU, RAM, disco)
2. Analiza requisitos de cada aplicación
3. Decide dónde ejecutar cada pod (scheduling)
4. Rebalancea automáticamente si es necesario

**Ventajas de multi-nodo:**
- ✅ Alta disponibilidad (si un nodo falla, otros toman el control)
- ✅ Escalabilidad horizontal (agregar más nodos = más capacidad)
- ✅ Distribución de carga
- ✅ Tolerancia a fallos

**📚 Referencias:**
- Arquitectura de clústeres: https://kubernetes.io/docs/concepts/architecture/
- Nodos: https://kubernetes.io/docs/concepts/architecture/nodes/

---

## 8. Pods - Unidad Básica de Kubernetes

### Definición Oficial

> "Los Pods son las unidades de computación más pequeñas y desplegables que puedes crear y gestionar en Kubernetes."
> 
> -- El equipo de Kubernetes

Un Pod es la unidad más pequeña y simple en el modelo de objetos de Kubernetes que creas o despliegas. Representa uno (o a veces más) contenedor(es) en ejecución en un clúster.

### Ejemplos de Uso

#### Aplicación Web Simple

Podrías tener un solo pod: el servidor web. A medida que el tráfico crece, podrías desplegar ese mismo código en múltiples pods para manejar la carga aumentada.

**→ Varios pods, un solo código base.**

#### Sistema Backend Complejo

Podrías tener varios pods para el servidor web y varios pods que manejan el procesamiento de video.

**→ Múltiples pods, múltiples códigos base.**

### Concepto Fundamental

**Los Pods son solo envoltorios alrededor de contenedores.** 

Puedes pensarlo como un contenedor de Docker con un poco de magia extra de Kubernetes. El contenedor es la aplicación real, y el Pod es la abstracción de Kubernetes que gestiona el contenedor y los recursos que necesita para ejecutarse.

### Relación de Conceptos

```
Imagen Docker → Contenedor → Pod → Deployment
```

### Características de los Pods

- **Pod** = Unidad mínima desplegable en Kubernetes
- Un pod puede contener 1 o más contenedores (generalmente 1)
- Los pods son **efímeros** (temporales, pueden ser eliminados y recreados)
- **Pod ≈ Contenedor + metadata de K8s**

### ¿Por Qué Kubernetes Usa Pods?

#### 1. Agrupación Lógica

A veces necesitas que múltiples contenedores trabajen muy estrechamente juntos:

```
Pod de Aplicación Web
├── Contenedor 1: Aplicación principal (Node.js)
└── Contenedor 2: Sidecar de logging (Fluentd)
```

Estos contenedores necesitan:
- Compartir el mismo sistema de archivos
- Comunicarse vía `localhost`
- Iniciarse/detenerse juntos

#### 2. Abstracción de Red Compartida

Los contenedores en el mismo Pod:
- ✅ Comparten la **misma IP**
- ✅ Pueden comunicarse vía `localhost`
- ✅ Comparten el mismo espacio de red

**Sin Pods:** Cada contenedor tendría su propia IP → más complejo para comunicación

#### 3. Compartir Recursos y Volúmenes

Los contenedores en un Pod pueden:
- Compartir volúmenes de almacenamiento
- Compartir memoria y CPU asignados al Pod
- Acceder a los mismos secretos/configuraciones

#### 4. Unidad Atómica de Escalado

Kubernetes escala **Pods completos**, no contenedores individuales:

```bash
# Escalar a 3 réplicas = 3 Pods completos
kubectl scale deployment synergychat-web --replicas=3
```

#### 5. Ciclo de Vida Unificado

Todos los contenedores en un Pod:
- Se inician juntos
- Se detienen juntos
- Comparten el mismo estado (Running, Pending, Failed)

### Analogía

**Sin Pods:** Como enviar piezas de un mueble en cajas separadas sin instrucciones

**Con Pods:** Como enviar un mueble pre-ensamblado listo para usar

### Resumen en Inglés

**Summary:**

Pods are the **minimum deployment unit** because:

1. They group containers that need to work together
2. They simplify networking and communication
3. They facilitate resource sharing
4. They create an atomic unit for scaling
5. They unify the lifecycle

**📚 Referencias:**
- Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- Por qué Pods: https://kubernetes.io/docs/concepts/workloads/pods/#why-pods

---

## 9. Naturaleza Efímera de los Pods

### Los Pods Mueren

Los Pods mueren, mueren a menudo, y a veces sin previo aviso.

La naturaleza **efímera** (palabra elegante para "temporal") de los Pods es una de las características definitorias de Kubernetes. 

A diferencia de las máquinas virtuales (VMs) tradicionales o servidores físicos que podrían ejecutarse indefinidamente (o hasta que falle el hardware), los Pods están diseñados para ser iniciados, destruidos y reiniciados en cualquier momento.

### ¿Por Qué Son Temporales?

**Flexibilidad y resiliencia.** 

Si un Pod encuentra un problema, puede ser fácilmente terminado y reemplazado con una nueva instancia saludable. Este modelo no solo permite alta disponibilidad, sino que también promueve la **inmutabilidad**. 

En lugar de parchear o actualizar manualmente entornos existentes, inicias nuevas versiones del entorno completo.

### ¿Cómo Te Afecta Como Desarrolladora?

Es crucial entender que **rara vez es una buena idea almacenar datos persistentes en un Pod**. 

Pueden ser terminados y reemplazados, y cualquier dato guardado localmente se perderá. 

**¡Planifica que tu imagen se reinicie desde cero a menudo!**

### Comparación: Tradicional vs Kubernetes

| Tradicional (VM/Servidor físico) | Kubernetes (Pods)        |
|----------------------------------|--------------------------|
| Vive indefinidamente             | Temporal                 |
| Se parchea/actualiza             | Se reemplaza completamente |
| Estado guardado localmente       | Estado externo (stateless) |
| Difícil de escalar               | Fácil de escalar         |

### Concepto Importante: Inmutabilidad

- No modificas un pod en ejecución
- Creas uno nuevo con los cambios
- Eliminas el viejo
- Más predecible y confiable

### Cuando un Pod es Eliminado Manualmente

**Pregunta:** ¿Qué pasa cuando eliminas un pod?

**Respuesta correcta:** Un nuevo pod es creado desde la misma imagen (que se siente como un reinicio)

#### Explicación

Cuando eliminas un pod manualmente, Kubernetes NO reutiliza el mismo pod. En su lugar:

1. El pod original es **completamente destruido**
2. Un **nuevo pod** es creado desde cero
3. Se usa la **misma imagen** Docker
4. El nuevo pod obtiene un **nuevo nombre** y **nueva IP**

#### ¿Por Qué Pasa Esto?

Porque el **Deployment** está configurado para mantener un número específico de réplicas corriendo. Si eliminas un pod:

1. El Deployment detecta que falta un pod
2. Automáticamente crea uno nuevo para mantener el número deseado de réplicas

#### Demostración Práctica

```bash
# Ver el pod actual
kubectl get pods
# Ejemplo: synergychat-web-679cbcc6cd-cq6vx

# Eliminar el pod
kubectl delete pod synergychat-web-679cbcc6cd-cq6vx

# Ver inmediatamente los pods
kubectl get pods
# Verás un pod NUEVO con diferente nombre
# Ejemplo: synergychat-web-679cbcc6cd-xyz12
```

**Nota:** El hash al final del nombre cambia porque es un pod completamente nuevo.

### Buenas Prácticas

- ✅ Diseñar aplicaciones **stateless** (sin estado local)
- ✅ Usar bases de datos externas para datos persistentes
- ✅ Usar Volumes de Kubernetes para datos que deben persistir
- ✅ Asumir que tu pod puede morir en cualquier momento

**📚 Referencias:**
- Pod Lifecycle: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/

---

## 10. Direcciones IP Únicas

### Cada Pod Tiene su IP

Cada Pod en un clúster de Kubernetes tiene una dirección IP interna única dentro de k8s. 

Al darle a cada Pod una IP única, Kubernetes simplifica la comunicación y el descubrimiento de servicios dentro del clúster. Los Pods dentro del mismo Nodo o a través de diferentes Nodos pueden comunicarse fácilmente.

### IPs Virtualizadas

Todos los recursos dentro de un clúster de k8s están virtualizados. Entonces, la dirección IP de un Pod **no es la misma** que la dirección IP del Nodo en el que se está ejecutando. 

Es una dirección IP virtual que solo es accesible desde dentro del clúster.

### Ejemplo de IPs

```
Nodo (IP física: 192.168.1.10)
  ├── Pod 1 (IP virtual: 10.244.0.5)
  └── Pod 2 (IP virtual: 10.244.0.6)

Nodo (IP física: 192.168.1.11)
  └── Pod 3 (IP virtual: 10.244.1.2)
```

### Comunicación

- Pod 1 puede hablar con Pod 3 usando `10.244.1.2`
- Pero desde tu laptop NO puedes acceder a `10.244.0.5` directamente
- Por eso usamos `kubectl port-forward` para acceder desde fuera

### Características Importantes

- Cada Pod tiene su **propia IP única**
- Esta IP es **virtual** (solo existe dentro del clúster)
- La IP del Pod ≠ IP del Nodo físico
- Los Pods pueden comunicarse entre sí usando estas IPs
- **Importante:** Estas IPs NO son accesibles desde fuera del clúster

### Asignación Práctica

#### Ver IPs de los Pods

```bash
kubectl get pods -o wide
```

**Salida esperada:**
```
NAME                              READY   STATUS    RESTARTS   AGE   IP           NODE
synergychat-web-679cbcc6cd-xxxxx  1/1     Running   0          10m   10.244.0.5   minikube
```

Este comando muestra columnas adicionales incluyendo:
- IP del Pod
- Nodo donde se está ejecutando
- Más información útil

#### Usar el Proxy de Kubernetes

```bash
kubectl proxy
```

Esto iniciará un servidor proxy en tu máquina local, probablemente en `127.0.0.1:8001`.

Navega a: `http://127.0.0.1:8001/api/v1/namespaces/default/pods`

Verás un JSON que describe los pods que tienes ejecutándose.

**Este proxy te permite:**
- Acceder al API Server de Kubernetes desde tu navegador
- Ver información detallada de los recursos en formato JSON
- No necesitas autenticación (el proxy maneja eso)

### Comandos Útiles

```bash
# Ver solo las IPs
kubectl get pods -o wide | awk '{print $1, $6}'

# Describir un pod específico
kubectl describe pod <pod-name>
```

**📚 Referencias:**
- Networking: https://kubernetes.io/docs/concepts/cluster-administration/networking/

---

## 11. Deployments - Estado Declarativo

### Definición

Un **Deployment** proporciona actualizaciones declarativas para Pods y ReplicaSets.

Describes tu estado deseado en un Deployment, y el trabajo del Controlador de Deployment es hacer que el estado actual coincida con el estado deseado. Tú declaras tus esperanzas y sueños, y es el trabajo de Kubernetes hacerlos realidad.

---

### ¿Por Qué Eliminar un Pod No Se Siente Como una Eliminación?

¿Recuerdas cuando eliminaste un pod, solo para ver que se creó un nuevo pod en su lugar? Es como cortar cabezas de una hidra.

Eso es porque el **estado deseado** descrito en nuestro Deployment dice que queremos un cierto número de pods ejecutándose en todo momento. Cuando eliminamos uno, el Controlador de Deployment ve que el estado actual no coincide con el estado deseado, entonces crea un nuevo pod para que coincidan nuevamente.

---

### Ver y Editar Deployments

**Ver el YAML del Deployment:**
```bash
kubectl get deployment synergychat-web -o yaml
```

**Editar el Deployment:**
```bash
kubectl edit deployment synergychat-web
```

Este comando abrirá el YAML del deployment en un editor. Busca la sección `spec.replicas` que indica cuántos pods deben estar corriendo.

**Cambiar réplicas:**
```yaml
spec:
  replicas: 10  # Cambiar de 1 a 10
```

**Guardar cambios:**
- En `vi`: Presiona `ESC`, luego escribe `:wq` y presiona `ENTER`
- En `nano`: Presiona `CTRL + O`, luego `ENTER`, luego `CTRL + X`

---

### Verificar Deployments y Pods

**Ver deployments:**
```bash
kubectl get deployments
```

**Salida esperada:**
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
synergychat-web   10/10   10           10          5m
```

**Ver pods:**
```bash
kubectl get pods
```

**Ver pods en tiempo real:**
```bash
kubectl get pods --watch
```

---

### Escalado de Forma Alternativa

**Comando directo para escalar (sin editar YAML):**
```bash
kubectl scale deployment synergychat-web --replicas=10
```

Este es un atajo, pero el método preferido es editar el YAML para tener todo documentado.

---

### Conceptos Clave

#### Estado Declarativo vs Imperativo

**Declarativo (Deployment):**
```yaml
"Quiero 10 pods corriendo"
```
Kubernetes se encarga de crear, mantener y recrear pods para mantener ese estado.

**Imperativo (manual):**
```bash
"Crea este pod específico"
```
Si lo eliminas, desaparece para siempre.

---

#### ¿Qué hace el Deployment Controller?

```
Estado Deseado: 10 pods
Estado Actual: 7 pods

→ Deployment Controller crea 3 pods más
→ Estado Actual = Estado Deseado ✅
```

---

#### Comparación Visual

```
Sin Deployment:
Eliminas pod → Pod desaparece → Fin

Con Deployment:
Eliminas pod → Deployment detecta falta → Crea nuevo pod → Mantiene el número deseado
```

---

### Comandos Útiles

```bash
# Ver deployments
kubectl get deployments

# Ver configuración completa
kubectl get deployment synergychat-web -o yaml

# Editar deployment
kubectl edit deployment synergychat-web

# Escalar (forma alternativa sin editar YAML)
kubectl scale deployment synergychat-web --replicas=10

# Ver pods en tiempo real
kubectl get pods --watch

# Ver estado detallado de un deployment
kubectl describe deployment synergychat-web
```

---

### Conceptos Importantes

- **Deployment** = Controlador que mantiene el estado deseado de tus pods
- **ReplicaSet** = Objeto interno que el Deployment usa para gestionar réplicas
- **Estado Deseado** = Lo que declaras en el YAML (ej: 10 réplicas)
- **Estado Actual** = Lo que realmente está corriendo
- **Reconciliación** = Proceso de hacer que Estado Actual = Estado Deseado

**📚 Documentación oficial:**
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

---

## 12. ReplicaSets

### Definición

Un **ReplicaSet** mantiene un conjunto estable de Pods réplica ejecutándose en cualquier momento dado. Es lo que se asegura de que el número de Pods que quieres ejecutando sea el mismo que el número de Pods que realmente están ejecutándose.

---

### ReplicaSet vs Deployment

Podrías estar pensando: "Pensé que eso es lo que hace un Deployment." Bueno... sí.

Un **Deployment es una abstracción de nivel superior** que gestiona los ReplicaSets por ti. Puedes pensar en un Deployment como un envoltorio alrededor de un ReplicaSet.

### El punto clave:

**Probablemente nunca usarás ReplicaSets directamente.** Solo necesitas saber qué son porque escucharás el término mencionado, e incluso podrías verlos referenciados en logs y similares.

---

### Jerarquía de Objetos

```
Deployment (lo que tú creas y gestionas)
  └── ReplicaSet (creado automáticamente por el Deployment)
       └── Pods (creados automáticamente por el ReplicaSet)
```

**En la práctica:**
- ✅ Tú trabajas con **Deployments**
- ⚙️ Kubernetes gestiona **ReplicaSets** automáticamente
- 🚀 Los **Pods** son creados y gestionados por los ReplicaSets

---

### Ver ReplicaSets

```bash
kubectl get replicasets
# o abreviado:
kubectl get rs
```

**Salida esperada:**
```
NAME                        DESIRED   CURRENT   READY   AGE
synergychat-web-679cbcc6cd  10        10        10      5m
```

---

### Observación Importante

Justo como con los pods, **nunca creaste directamente el ReplicaSet**. 

**Flujo de creación:**
1. Tú creaste un **Deployment**
2. El Deployment creó el **ReplicaSet**
3. El ReplicaSet creó los **Pods**

---

### Conceptos Clave

#### ¿Qué hace un ReplicaSet?

- Asegura que el número correcto de pods esté ejecutándose
- Reemplaza pods que fallan
- Mantiene el estado deseado de réplicas

#### ¿Por qué existen si los Deployments hacen lo mismo?

Los **Deployments** agregan funcionalidad adicional sobre los ReplicaSets:

| ReplicaSet                  | Deployment                           |
|-----------------------------|--------------------------------------|
| Mantiene número de réplicas | Mantiene número de réplicas          |
| ❌ No gestiona actualizaciones | ✅ Gestiona actualizaciones rolling  |
| ❌ No tiene historial       | ✅ Mantiene historial de versiones   |
| ❌ No permite rollback      | ✅ Permite rollback a versiones anteriores |

---

### Ejemplo Visual

```
Usuario crea Deployment:
kubectl create deployment synergychat-web --image=...

↓

Deployment crea ReplicaSet:
synergychat-web-679cbcc6cd

↓

ReplicaSet crea Pods:
synergychat-web-679cbcc6cd-abc12
synergychat-web-679cbcc6cd-def34
synergychat-web-679cbcc6cd-ghi56
...
```

---

### Comandos Útiles

```bash
# Ver ReplicaSets
kubectl get replicasets
# o abreviado:
kubectl get rs

# Ver ReplicaSets con más detalles
kubectl get rs -o wide

# Describir un ReplicaSet específico
kubectl describe rs <nombre-replicaset>

# Ver en formato YAML
kubectl get rs <nombre-replicaset> -o yaml
```

---

### Nota sobre el nombre del ReplicaSet

Observa el nombre: `synergychat-web-679cbcc6cd`

- `synergychat-web` = Nombre del Deployment
- `679cbcc6cd` = Hash único generado por el Deployment

Este hash cambia cuando actualizas la imagen o configuración del Deployment, permitiendo actualizaciones rolling.

---

### Resumen

- **ReplicaSet** = Objeto que mantiene el número deseado de pods
- Probablemente **nunca crearás uno directamente**
- Los **Deployments** crean y gestionan ReplicaSets automáticamente
- Los ReplicaSets son una **capa intermedia** entre Deployments y Pods
- Conocerlos es importante para entender logs y debugging

**📚 Documentación oficial:**
- ReplicaSets: https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/

---

## 13. Configuración YAML

### Introducción

Los recursos de Kubernetes se configuran principalmente usando archivos YAML. Hemos usado el comando `kubectl edit` para editar recursos en el clúster bajo demanda, pero ahora inspeccionaremos los archivos YAML más de cerca.

---

### Descargar Configuración YAML

**Exportar deployment a archivo:**
```bash
kubectl get deployment synergychat-web -o yaml > web-deployment.yaml
```

Este comando:
- Obtiene la configuración del deployment
- La formatea en YAML
- La guarda en el archivo `web-deployment.yaml`

---

### Estructura de un YAML de Deployment

**Hay 5 campos de nivel superior:**

#### 1. `apiVersion: apps/v1`
Especifica la versión de la API de Kubernetes que estás usando para crear el objeto.

#### 2. `kind: Deployment`
Especifica el tipo de objeto que estás configurando.

#### 3. `metadata`
Metadata sobre el deployment, como cuándo fue creado, su nombre y su ID.

**Ejemplo:**
```yaml
metadata:
  name: synergychat-web
  namespace: default
  creationTimestamp: "2024-..."
```

#### 4. `spec`
El **estado deseado** del deployment. Las ediciones más impactantes, como cuántas réplicas quieres, se harán aquí.

**Ejemplo:**
```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: synergychat-web
  template:
    spec:
      containers:
      - image: docker.io/bootdotdev/synergychat-web:latest
        name: synergychat-web
```

#### 5. `status`
El **estado actual** del deployment. No editarás esto directamente, es solo para que veas qué está pasando.

**Ejemplo:**
```yaml
status:
  availableReplicas: 3
  readyReplicas: 3
  replicas: 3
```

---

### Editar y Aplicar Cambios

**Editar el archivo localmente:**
```bash
# Abrir con editor
code web-deployment.yaml
# o
nano web-deployment.yaml

# Cambiar réplicas
spec:
  replicas: 3  # Cambiar de 10 a 3
```

**Aplicar los cambios:**
```bash
kubectl apply -f web-deployment.yaml
```

---

### Advertencia sobre `last-applied-configuration`

La primera vez que uses `kubectl apply` en un recurso creado con `kubectl create`, verás una advertencia:

```
Warning: resource deployments/synergychat-web is missing the kubectl.kubernetes.io/last-applied-configuration annotation...
```

**¿Por qué?**
- Creaste el deployment con `kubectl create deployment`
- No con `kubectl apply -f`
- La anotación se agrega automáticamente en el primer `apply`
- No verás la advertencia en aplicaciones posteriores

---

### kubectl create vs kubectl apply

| `kubectl create`              | `kubectl apply`                    |
|-------------------------------|-------------------------------------|
| Crea recursos imperativamente | Gestiona recursos declarativamente  |
| No guarda configuración       | Guarda last-applied-configuration  |
| Falla si el recurso existe    | Actualiza si existe, crea si no     |
| ❌ No recomendado para producción | ✅ Recomendado para producción  |

---

### Infraestructura como Código (IaC)

**Ventajas de usar archivos YAML en Git:**
- ✅ Historial de cambios (control de versiones)
- ✅ Revisión de código (code reviews)
- ✅ Recuperación ante desastres (backup)
- ✅ Reproducibilidad (recrear el entorno exacto)
- ✅ Documentación (el código es la documentación)

**Guardar en Git:**
```bash
cd ~/k8s_course

mkdir -p deployments
mv web-deployment.yaml deployments/

git add deployments/web-deployment.yaml
git commit -m "Add web deployment YAML configuration"
git push
```

---

### Comandos Útiles

```bash
# Exportar deployment a YAML
kubectl get deployment <nombre> -o yaml > deployment.yaml

# Aplicar cambios desde archivo
kubectl apply -f deployment.yaml

# Ver diferencias antes de aplicar
kubectl diff -f deployment.yaml

# Aplicar todos los archivos en un directorio
kubectl apply -f ./deployments/

# Eliminar recursos usando archivo
kubectl delete -f deployment.yaml

# Validar sintaxis sin aplicar
kubectl apply -f deployment.yaml --dry-run=client
```

---

### Estructura Recomendada del Repositorio

```
k8s_course/
├── README.md
├── .gitignore
├── notes/
│   └── kubernetes-course-completo.md
└── deployments/
    ├── web-deployment.yaml
    ├── api-deployment.yaml
    └── api-configmap.yaml
```

---

### Conceptos Importantes

- **YAML** = Formato de configuración legible para humanos
- **Declarativo** = Describes el estado que quieres, Kubernetes lo hace realidad
- **Infrastructure as Code** = Configuración versionada en Git
- **kubectl apply** = Método preferido para gestionar recursos en producción
- **last-applied-configuration** = Anotación que Kubernetes usa para rastrear cambios

**📚 Documentación oficial:**
- YAML en Kubernetes: https://kubernetes.io/docs/concepts/configuration/overview/
- kubectl apply: https://kubernetes.io/docs/reference/kubectl/generated/kubectl_apply/

---

## 14. Desplegando API Service

### Introducción

Es hora de desplegar un segundo servicio: el backend API para nuestra aplicación de chat. Este servicio no sirve una página web, sino una API JSON.

---

### Crear Deployment desde Cero

Vamos a escribir un deployment YAML desde cero para el servicio API.

**Crear archivo:**
```bash
cd ~/k8s_course/deployments
touch api-deployment.yaml
```

---

### Estructura del YAML

**Campos requeridos:**

1. `apiVersion`: `apps/v1`
2. `kind`: `Deployment`
3. `metadata/name`: `synergychat-api`
4. `metadata/labels/app`: `synergychat-api`
5. `spec/replicas`: `1`
6. `spec/selector/matchLabels/app`: `synergychat-api`
7. `spec/template/metadata/labels/app`: `synergychat-api`
8. `spec/template/spec/containers`:
   - `name`: `synergychat-api`
   - `image`: `bootdotdev/synergychat-api:latest`

---

### Archivo YAML Completo

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-api
  labels:
    app: synergychat-api
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-api
  template:
    metadata:
      labels:
        app: synergychat-api
    spec:
      containers:
      - name: synergychat-api
        image: bootdotdev/synergychat-api:latest
```

---

### Importancia de los Labels

Los labels son cruciales en Kubernetes:

```yaml
metadata:
  labels:
    app: synergychat-api    # Label del Deployment

spec:
  selector:
    matchLabels:
      app: synergychat-api  # Debe coincidir ↑

  template:
    metadata:
      labels:
        app: synergychat-api # Y también aquí ↑
```

**¿Por qué 3 veces?**
1. **metadata/labels**: Identifica el Deployment mismo
2. **selector/matchLabels**: Le dice al Deployment qué Pods gestionar
3. **template/metadata/labels**: Los Pods creados tendrán este label

Si no coinciden, el Deployment no sabrá qué Pods gestionar.

---

### Crear el Deployment

```bash
kubectl apply -f api-deployment.yaml
```

**Salida esperada:**
```
deployment.apps/synergychat-api created
```

---

### Verificar los Pods

```bash
kubectl get pods
```

**Observación inicial:**
El pod de la API probablemente estará en estado `Error` o `CrashLoopBackOff` porque le falta configuración (variables de entorno). Esto es normal y lo resolveremos con ConfigMaps.

---

### Referencia JSON

El curso proporciona el JSON equivalente para entender la estructura:

```json
{
  "apiVersion": "apps/v1",
  "kind": "Deployment",
  "metadata": {
    "name": "synergychat-api",
    "labels": {
      "app": "synergychat-api"
    }
  },
  "spec": {
    "replicas": 1,
    "selector": {
      "matchLabels": {
        "app": "synergychat-api"
      }
    },
    "template": {
      "metadata": {
        "labels": {
          "app": "synergychat-api"
        }
      },
      "spec": {
        "containers": [
          {
            "name": "synergychat-api",
            "image": "bootdotdev/synergychat-api:latest"
          }
        ]
      }
    }
  }
}
```

---

### Conceptos Importantes

- **Labels** = Etiquetas key-value para identificar y seleccionar recursos
- **Selector** = Define qué Pods pertenecen a un Deployment usando labels
- **Template** = Plantilla que define cómo crear los Pods
- **Containers** = Lista de contenedores que correrán en cada Pod

**📚 Documentación oficial:**
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- Labels y Selectors: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/

---

## 15. Pods que Fallan (Thrashing Pods)

### Introducción

Uno de los problemas más comunes que encontrarás al trabajar con Kubernetes es que los Pods sigan crasheando y reiniciándose. Esto se llama **"thrashing"** (golpeteo/fallo repetitivo).

---

### Causas Comunes de Thrashing

1. **La aplicación recientemente tuvo un bug introducido en la última versión de la imagen**
2. **La aplicación está mal configurada y no puede iniciarse correctamente**
3. **Una dependencia de la aplicación está mal configurada**
4. **La aplicación está intentando usar demasiada memoria y está siendo eliminada por Kubernetes**

---

### ¿Qué es "CrashLoopBackoff"?

Cuando el estado de un pod es `CrashLoopBackoff`, eso significa que el contenedor está crasheando (el programa está saliendo con código de error `1` o distinto de cero).

Porque Kubernetes se trata de construir sistemas auto-sanables, automáticamente reiniciará el contenedor. Sin embargo, cada vez que intenta reiniciar el contenedor, si crashea nuevamente, esperará cada vez más tiempo entre reinicios. **Por eso se llama "backoff"** (retroceso/espera incremental).

---

### Ciclo de CrashLoopBackoff

```
Contenedor inicia
     ↓
Crashea (exit code 1)
     ↓
Kubernetes espera 10 segundos → Reinicia
     ↓
Crashea de nuevo
     ↓
Kubernetes espera 20 segundos → Reinicia
     ↓
Crashea de nuevo
     ↓
Kubernetes espera 40 segundos → Reinicia
     ↓
... (el tiempo sigue aumentando)
```

**El tiempo de espera se duplica cada vez** hasta un máximo (generalmente 5 minutos).

---

### Estados Relacionados con Fallos

| Estado              | Significado                                           |
|---------------------|-------------------------------------------------------|
| `Error`             | El contenedor falló una vez                           |
| `CrashLoopBackOff`  | El contenedor está fallando repetidamente            |
| `ImagePullBackOff`  | No puede descargar la imagen del contenedor          |
| `Pending`           | El pod está esperando ser programado en un nodo      |
| `Running`           | ✅ El contenedor está ejecutándose correctamente     |

---

### Códigos de Salida Comunes

| Exit Code | Significado                                    |
|-----------|------------------------------------------------|
| 0         | Éxito                                          |
| 1         | Error genérico de aplicación                   |
| 2         | Uso incorrecto (argumentos inválidos)          |
| 126       | Comando no se puede ejecutar                   |
| 127       | Comando no encontrado                          |
| 137       | Proceso terminado por SIGKILL (OOMKilled)      |
| 143       | Proceso terminado por SIGTERM                  |

---

### Pregunta Teórica: ¿Cuándo ocurre CrashLoopBackOff?

**A CrashLoopBackOff happens because...**

✅ **The container is continuously exiting with a non-zero exit code**

(El contenedor está saliendo continuamente con un código de salida distinto de cero)

---

#### Explicación:

En sistemas Unix/Linux, cuando un programa termina:
- **Exit code 0** = Éxito ✅
- **Exit code 1 (o cualquier otro número)** = Error ❌

**Proceso:**
1. Contenedor inicia
2. Programa falla (bug, configuración incorrecta)
3. Contenedor sale con exit code ≠ 0
4. Kubernetes detecta el fallo y reinicia
5. El contenedor vuelve a fallar
6. Kubernetes espera (backoff) antes de reintentar
7. El ciclo continúa → CrashLoopBackOff

---

#### Por qué las otras opciones son incorrectas:

❌ **"There aren't enough resources"**
- Estado sería: `Pending` o `FailedScheduling`
- NO CrashLoopBackOff

❌ **"The container is logging to standard error"**
- Loggear a stderr es completamente normal
- Kubernetes captura stdout y stderr
- Esto NO causa crashes

---

### Sistema Auto-Sanante de Kubernetes

**Filosofía de Kubernetes:**
```
Algo falló → Kubernetes lo detecta → Intenta arreglarlo automáticamente
```

**Ventajas:**
- ✅ No necesitas intervención manual inmediata
- ✅ Fallos temporales se resuelven solos
- ✅ Alta disponibilidad

**Implicaciones:**
- ⚠️ Problemas de configuración seguirán repitiéndose
- ⚠️ Necesitas logs y monitoreo para diagnosticar

---

### Conceptos Clave

- **Thrashing** = Pod crasheando y reiniciándose repetidamente
- **CrashLoopBackOff** = Estado que indica fallo cíclico con espera incremental
- **Exit code** = Código que devuelve un programa al terminar
- **Backoff** = Tiempo de espera que aumenta exponencialmente
- Kubernetes **intenta auto-sanar** reiniciando contenedores fallidos

**📚 Documentación oficial:**
- Pod Lifecycle: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
- Debug Pods: https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/

---

## 16. ConfigMaps - Gestión de Variables de Entorno

### Introducción

Hay varias formas de gestionar variables de entorno en Kubernetes. Una de las formas más comunes es usar **ConfigMaps**. Los ConfigMaps nos permiten desacoplar nuestras configuraciones de nuestras imágenes de contenedor.

---

### El Problema con Variables en Dockerfile

En un Dockerfile podemos establecer variables de entorno así:

```dockerfile
ENV PORT=3000
```

**El problema es:**
- Todos los que usen esa imagen tendrán que usar el puerto 3000
- Si queremos cambiar el puerto, tenemos que **reconstruir la imagen**
- No es flexible ni escalable

---

### La Solución: ConfigMaps

ConfigMaps permiten:
- ✅ Separar configuración del código
- ✅ Cambiar valores sin reconstruir imágenes
- ✅ Usar diferentes configuraciones para diferentes entornos
- ✅ Centralizar configuraciones

---

### Crear un ConfigMap

**Archivo `api-configmap.yaml`:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-api-configmap
data:
  API_PORT: "8080"
```

**Notas importantes:**
- ConfigMaps usan `apiVersion: v1` (no `apps/v1`)
- `kind: ConfigMap`
- La sección `data` contiene pares clave-valor
- Los valores numéricos deben estar entre comillas (para tratarlos como strings)

---

### Aplicar el ConfigMap

```bash
kubectl apply -f api-configmap.yaml
```

**Salida esperada:**
```
configmap/synergychat-api-configmap created
```

---

### Verificar ConfigMaps

```bash
# Ver todos los ConfigMaps
kubectl get configmaps
# o abreviado:
kubectl get cm
```

**Salida esperada:**
```
NAME                         DATA   AGE
synergychat-api-configmap    1      10s
```

**Ver detalles:**
```bash
kubectl describe configmap synergychat-api-configmap
```

---

### Anatomía de un ConfigMap

```yaml
apiVersion: v1              # Versión de la API
kind: ConfigMap             # Tipo de recurso
metadata:
  name: mi-config           # Nombre único del ConfigMap
data:                       # Sección de datos
  VARIABLE1: "valor1"       # Clave: valor
  VARIABLE2: "valor2"       # Otro par clave-valor
  DATABASE_URL: "postgres://localhost:5432/db"
```

---

### Tipos de Datos en ConfigMaps

**1. Variables simples:**
```yaml
data:
  API_PORT: "8080"
  DEBUG: "true"
```

**2. Archivos completos:**
```yaml
data:
  config.json: |
    {
      "port": 8080,
      "debug": true
    }
```

**3. Múltiples valores:**
```yaml
data:
  API_PORT: "8080"
  DATABASE_URL: "postgres://db:5432/mydb"
  CACHE_TTL: "3600"
```

---

### Comandos Útiles

```bash
# Ver ConfigMaps
kubectl get configmaps

# Ver detalles
kubectl describe configmap <nombre>

# Ver en formato YAML
kubectl get configmap <nombre> -o yaml

# Editar
kubectl edit configmap <nombre>

# Eliminar
kubectl delete configmap <nombre>
```

---

### Conceptos Importantes

- **ConfigMap** = Objeto para almacenar datos de configuración
- **Desacoplamiento** = Separar configuración del código/imagen
- **data** = Sección donde se definen pares clave-valor
- **apiVersion: v1** = ConfigMaps usan v1, no apps/v1
- Los ConfigMaps son **independientes** de los Pods

**📚 Documentación oficial:**
- ConfigMaps: https://kubernetes.io/docs/concepts/configuration/configmap/

---

## 17. Aplicando ConfigMaps a Deployments

### Conectar ConfigMap al Deployment

Ahora que tenemos un ConfigMap, necesitamos conectarlo a nuestro deployment para que los pods puedan acceder a las variables de entorno.

---

### Actualizar el Deployment

**Agregar sección `env` al contenedor:**

```yaml
spec:
  containers:
  - name: synergychat-api
    image: bootdotdev/synergychat-api:latest
    env:
    - name: API_PORT
      valueFrom:
        configMapKeyRef:
          name: synergychat-api-configmap
          key: API_PORT
```

---

### Archivo api-deployment.yaml Completo

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-api
  labels:
    app: synergychat-api
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-api
  template:
    metadata:
      labels:
        app: synergychat-api
    spec:
      containers:
      - name: synergychat-api
        image: bootdotdev/synergychat-api:latest
        env:
        - name: API_PORT
          valueFrom:
            configMapKeyRef:
              name: synergychat-api-configmap
              key: API_PORT
```

---

### ¿Qué hace esta configuración?

```yaml
env:                          # Lista de variables de entorno
  - name: API_PORT            # Nombre de la variable en el contenedor
    valueFrom:                # El valor viene de...
      configMapKeyRef:        # ...una referencia a un ConfigMap
        name: synergychat-api-configmap  # Nombre del ConfigMap
        key: API_PORT         # Clave dentro del ConfigMap
```

**Esto le dice a Kubernetes:**
1. Crear una variable de entorno llamada `API_PORT` en el contenedor
2. Obtener su valor del ConfigMap `synergychat-api-configmap`
3. Usar el valor de la clave `API_PORT` (que es "8080")

---

### Aplicar Cambios

```bash
kubectl apply -f api-deployment.yaml
```

**Salida esperada:**
```
deployment.apps/synergychat-api configured
```

**Nota:** Dice "configured" (no "created") porque ya existía.

---

### Verificar que el Pod Funciona

```bash
kubectl get pods
```

**Ahora deberías ver:**
```
NAME                               READY   STATUS    RESTARTS   AGE
synergychat-api-xxxxx-yyyyy        1/1     Running   0          30s
```

✅ `STATUS: Running` (ya no Error/CrashLoopBackOff)
✅ `READY: 1/1`
✅ `RESTARTS: 0`

---

### ¿Qué pasó cuando aplicamos el cambio?

1. **Deployment detectó un cambio** en el template del pod
2. **ReplicaSet creó un nuevo pod** con la configuración actualizada
3. **El nuevo pod inició exitosamente** con `API_PORT=8080`
4. **El pod viejo fue terminado** (rolling update)

---

### Verificación Visual del Flujo

```
ConfigMap:
  API_PORT: "8080"
       ↓
Deployment:
  env:
    - name: API_PORT
      valueFrom:
        configMapKeyRef: ...
       ↓
Pod/Contenedor:
  Environment Variables:
    API_PORT=8080
       ↓
Aplicación:
  process.env.API_PORT → "8080"
```

---

### Port Forward y Prueba

**Hacer port-forward:**
```bash
kubectl port-forward <pod-name> 8080:8080
```

**Probar la API:**
```bash
curl http://localhost:8080
```

**Salida esperada:**
```
404 page not found
```

**Esto es correcto:** La API está funcionando, pero la ruta raíz (`/`) no existe. La API tiene endpoints específicos.

---

### Comandos Útiles

```bash
# Ver variables de entorno de un pod
kubectl exec <pod-name> -- env

# Ver solo la variable configurada
kubectl exec <pod-name> -- env | grep API_PORT

# Describir el pod
kubectl describe pod <pod-name>

# Ver logs
kubectl logs <pod-name>
```

---

### Conceptos Importantes

- **env** = Lista de variables de entorno para el contenedor
- **valueFrom** = El valor viene de una fuente externa
- **configMapKeyRef** = Referencia a una clave en un ConfigMap
- **Rolling update** = Kubernetes reemplaza pods gradualmente
- ConfigMaps permiten **cambiar configuración sin reconstruir imágenes**

**📚 Documentación oficial:**
- Configure Pods with ConfigMaps: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/

---

## 18. Seguridad de ConfigMaps

### ⚠️ Los ConfigMaps No Son Seguros

Los ConfigMaps son una excelente forma de gestionar variables de entorno inocentes en Kubernetes. Cosas como:

* Puertos
* URLs de otros servicios
* Feature flags (banderas de características)
* Configuraciones que cambian entre entornos, como el modo `DEBUG`

---

### Advertencia Crítica

Sin embargo, **no son criptográficamente seguros**. Los ConfigMaps no están encriptados, y pueden ser accedidos por cualquiera con acceso al clúster.

Si necesitas almacenar información sensible, deberías usar **Kubernetes Secrets** o una solución de terceros.

---

### ❌ Qué NUNCA Guardar en ConfigMaps

**NO uses ConfigMaps para:**

1. **Contraseñas**
2. **API Keys / Tokens**
3. **Certificados privados**
4. **Credenciales de base de datos**
5. **Claves SSH**
6. **Números de tarjetas de crédito**
7. **Tokens de OAuth**
8. **Información PII (Personally Identifiable Information)**

---

### ✅ Qué SÍ es Seguro Guardar

**Ejemplos apropiados:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  API_PORT: "8080"                    # ✅ Puerto - público
  DATABASE_HOST: "postgres.default"   # ✅ Hostname - no sensible
  ENVIRONMENT: "production"           # ✅ Nombre de entorno
  DEBUG: "false"                      # ✅ Flag de configuración
  MAX_CONNECTIONS: "100"              # ✅ Límite de conexiones
  FEATURE_NEW_UI: "true"              # ✅ Feature flag
  LOG_LEVEL: "info"                   # ✅ Nivel de logging
  API_URL: "https://api.example.com"  # ✅ URL pública
```

---

### ¿Por Qué No Son Seguros?

#### 1. No están encriptados

```bash
# Cualquiera con acceso puede ver el contenido
kubectl get configmap app-config -o yaml
```

#### 2. Se almacenan en etcd sin encriptar

El almacén de datos de Kubernetes (etcd) guarda ConfigMaps en texto plano por defecto.

#### 3. Visibles en logs y eventos

```bash
kubectl describe pod mi-pod
# Puede mostrar valores del ConfigMap
```

#### 4. Accesibles vía API

Cualquiera con permisos puede hacer requests y leer ConfigMaps.

---

### Alternativas Seguras

#### 1. Kubernetes Secrets (mejora básica)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  username: YWRtaW4=        # base64 encoded
  password: cGFzc3dvcmQ=    # base64 encoded
```

**Nota:** Los Secrets tienen limitaciones:
- Solo base64 (NO encriptados por defecto)
- Necesitas configurar encriptación en etcd
- Mejor que ConfigMaps, pero no perfectos

---

#### 2. Soluciones de Terceros (más seguras)

**Opciones populares:**

- **HashiCorp Vault**
  - Encriptación fuerte
  - Rotación automática de secretos
  - Auditoría completa

- **AWS Secrets Manager**
- **Google Secret Manager**
- **Azure Key Vault**

- **Sealed Secrets** (Bitnami)
  - Encripta secrets antes de Git
  - Solo se descifran en el clúster

- **External Secrets Operator**
  - Sincroniza desde proveedores externos

---

### Ejemplo de Uso Correcto Combinado

```yaml
# ConfigMap para configuración NO sensible
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  API_PORT: "8080"
  DATABASE_HOST: "postgres.default"
---
# Secret para información sensible
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  DATABASE_PASSWORD: cGFzc3dvcmQxMjM=  # base64
  API_KEY: c2tfbGl2ZV81MUg=             # base64
```

**En el Deployment:**
```yaml
containers:
- name: app
  env:
  # ConfigMap para valores públicos
  - name: API_PORT
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: API_PORT
  # Secret para valores sensibles
  - name: DATABASE_PASSWORD
    valueFrom:
      secretKeyRef:
        name: app-secrets
        key: DATABASE_PASSWORD
```

---

### Mejores Prácticas

**✅ DO (Hacer):**
1. Usar ConfigMaps solo para configuración no sensible
2. Usar Secrets para datos sensibles
3. Habilitar encriptación en etcd
4. Usar RBAC para limitar acceso
5. Rotar secretos regularmente
6. No commitear ConfigMaps con datos sensibles a Git

**❌ DON'T (No hacer):**
1. Nunca guardar contraseñas en ConfigMaps
2. No commitear Secrets en Git (incluso en base64)
3. No asumir que base64 = encriptación
4. No dar acceso amplio a ConfigMaps/Secrets

---

### Resumen Comparativo

| Tipo         | Uso                      | Seguridad                | Cuándo usar                        |
|--------------|--------------------------|--------------------------|-------------------------------------|
| ConfigMap    | Configuración pública    | ❌ No encriptado         | Puertos, URLs, flags, settings      |
| Secret       | Datos sensibles          | ⚠️ Base64 (no encriptado por defecto) | Contraseñas, tokens, claves |
| Vault/KMS    | Datos muy sensibles      | ✅ Encriptado fuerte     | Secretos críticos de producción     |

---

### Analogía

**ConfigMap** = Post-it pegado en tu monitor (todos lo pueden ver)

**Secret** = Post-it en un sobre (un poco mejor, pero el sobre no está sellado)

**Vault/KMS** = Caja fuerte con combinación (realmente seguro)

---

### Conceptos Clave

- **ConfigMaps** = NO son seguros, solo para datos públicos
- **Texto plano** = Los datos son legibles directamente
- **Base64** = Codificación, NO encriptación
- **Secrets** = Mejor opción que ConfigMaps
- **Soluciones externas** = Máxima seguridad para producción

**📚 Documentación oficial:**
- Secrets: https://kubernetes.io/docs/concepts/configuration/secret/
- Encrypting Secret Data: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/

---

## 19. Desplegando el Crawler

### Introducción

Tenemos una última aplicación para desplegar: el crawler (rastreador). Esta aplicación rastrea continuamente **Project Gutenberg** y expone los datos que encuentra a través de una API JSON.

---

### Crear ConfigMap para Crawler

**Archivo `crawler-configmap.yaml`:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-crawler-configmap
data:
  CRAWLER_PORT: "8080"
  CRAWLER_KEYWORDS: "love,hate,joy,sadness,anger,disgust,fear,surprise"
```

**Aplicar:**
```bash
kubectl apply -f crawler-configmap.yaml
```

---

### Crear Deployment para Crawler

**Archivo `crawler-deployment.yaml`:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-crawler
  labels:
    app: synergychat-crawler
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-crawler
  template:
    metadata:
      labels:
        app: synergychat-crawler
    spec:
      containers:
      - name: synergychat-crawler
        image: bootdotdev/synergychat-crawler:latest
        envFrom:
        - configMapRef:
            name: synergychat-crawler-configmap
```

**Aplicar:**
```bash
kubectl apply -f crawler-deployment.yaml
```

---

### Nota sobre Puertos

Está bien que `CRAWLER_PORT` sea el mismo que `API_PORT` (ambos "8080"). Están en diferentes pods, y estos son puertos internos del pod.

---

### Verificar el Deployment

```bash
kubectl get pods
```

**Deberías ver:**
```
NAME                                   READY   STATUS    RESTARTS   AGE
synergychat-crawler-xxxxx-yyyyy        1/1     Running   0          30s
synergychat-api-xxxxx-zzzzz            1/1     Running   0          10m
synergychat-web-xxxxx-aaaaa            1/1     Running   0          1h
```

---

### Estado Actual del Clúster

**3 microservicios desplegados:**

```bash
kubectl get deployments
```

**Salida:**
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
synergychat-web       3/3     3            3           4d
synergychat-api       1/1     1            1           2h
synergychat-crawler   1/1     1            1           5m
```

- ✅ Web (front-end) - 3 réplicas
- ✅ API (backend) - 1 réplica
- ✅ Crawler (rastreador) - 1 réplica

---

## 20. env vs envFrom

### Introducción

Hemos visto dos formas de inyectar variables de entorno desde ConfigMaps:
- `env` (variable por variable)
- `envFrom` (todo el ConfigMap)

---

### Formato env (Verboso)

**Inyectar variables individuales:**

```yaml
env:
- name: API_PORT
  valueFrom:
    configMapKeyRef:
      name: synergychat-api-configmap
      key: API_PORT
- name: DATABASE_URL
  valueFrom:
    configMapKeyRef:
      name: synergychat-api-configmap
      key: DATABASE_URL
```

---

### Formato envFrom (Conciso)

**Inyectar todas las variables del ConfigMap:**

```yaml
envFrom:
- configMapRef:
    name: synergychat-crawler-configmap
```

**Esto inyecta TODAS las claves del ConfigMap como variables de entorno.**

---

### Comparación

| `env`                                      | `envFrom`                              |
|--------------------------------------------|----------------------------------------|
| Inyecta variables **individuales**         | Inyecta **todas** las variables        |
| Más verboso (lista cada variable)          | Más conciso                            |
| Puedes renombrar variables                 | Variables mantienen nombres originales |
| Puedes mezclar múltiples fuentes           | Importa todo de una fuente             |
| **Mejor para:** pocas variables            | **Mejor para:** muchas variables       |

---

### Ejemplo de env

```yaml
env:
- name: VAR1
  valueFrom:
    configMapKeyRef:
      name: mi-config
      key: VAR1
- name: VAR2
  valueFrom:
    configMapKeyRef:
      name: mi-config
      key: VAR2
```

**Resultado en el contenedor:**
```
VAR1=valor1
VAR2=valor2
```

---

### Ejemplo de envFrom

```yaml
envFrom:
- configMapRef:
    name: mi-config
```

**Si `mi-config` tiene:**
```yaml
data:
  VAR1: "valor1"
  VAR2: "valor2"
  VAR3: "valor3"
```

**Resultado en el contenedor:**
```
VAR1=valor1
VAR2=valor2
VAR3=valor3
```

---

### Combinar Ambos

```yaml
containers:
- name: app
  # Inyectar todo el ConfigMap
  envFrom:
  - configMapRef:
      name: common-config
  # Más variables específicas
  env:
  - name: SPECIAL_VAR
    value: "special-value"
  - name: API_KEY
    valueFrom:
      secretKeyRef:
        name: api-secrets
        key: key
```

---

### Verificar Variables de Entorno

```bash
# Ver todas las variables de entorno del pod
kubectl exec <pod-name> -- env

# Filtrar solo las del crawler
kubectl exec <pod-name> -- env | grep CRAWLER

# Deberías ver:
# CRAWLER_PORT=8080
# CRAWLER_KEYWORDS=love,hate,joy,sadness,anger,disgust,fear,surprise
```

---

### Conceptos Importantes

- **envFrom** = Inyecta todas las variables de un ConfigMap automáticamente
- **configMapRef** = Referencia a un ConfigMap completo
- **Puertos internos** = Pods diferentes pueden usar los mismos puertos
- **Microservicios** = Aplicaciones separadas que trabajan juntas

**📚 Documentación oficial:**
- Configure Pod with ConfigMap: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/

---

## 21. Services - Endpoints Estables

### Introducción

Hemos iniciado pods y nos hemos conectado a ellos individualmente, pero eso francamente no es súper útil si queremos distribuir tráfico real a través de esos pods. Ahí es donde entran los servicios.

Los **Services** proporcionan un endpoint estable para los pods. Son una abstracción usada para proporcionar un endpoint estable y balancear la carga de tráfico a través de un grupo de Pods. Por "endpoint estable", simplemente quiero decir que el servicio siempre estará disponible en una URL dada, incluso si el pod es destruido y recreado.

---

### Crear un Service para Web

**Preparación - Escalar a 3 réplicas:**
```bash
kubectl scale deployment synergychat-web --replicas=3
```

**Archivo `web-service.yaml`:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: synergychat-web
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

**Aplicar:**
```bash
kubectl apply -f web-service.yaml
```

---

### ¿Qué hace este Service?

Esto crea un nuevo servicio llamado `web-service` con algunas propiedades:

- **Escucha en el puerto `80`** para tráfico entrante
- **Reenvía ese tráfico a pods** que están escuchando en su puerto `8080`
- **Su controlador** continuamente escanea pods que coincidan con el selector de label `app: synergychat-web` y automáticamente los agrega a su pool

---

### Verificar el Service

```bash
kubectl get services
# o abreviado:
kubectl get svc
```

**Salida esperada:**
```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP   5d
web-service   ClusterIP   10.96.123.45    <none>        80/TCP    10s
```

---

### Port Forward al Service

**Diferencia clave:**
```bash
# Antes (port-forward a un pod específico):
kubectl port-forward pod/synergychat-web-xxxxx 8080:8080

# Ahora (port-forward al servicio):
kubectl port-forward service/web-service 8080:80
```

**Ejecutar:**
```bash
kubectl port-forward service/web-service 8080:80
```

Abre `http://localhost:8080` en tu navegador. ¡Las solicitudes están siendo **balanceadas** a través de 3 pods!

---

### Diferencia: Port-forward a Pod vs Service

| Port-forward a Pod                    | Port-forward a Service                |
|---------------------------------------|---------------------------------------|
| Conecta a UN pod específico           | Conecta al Service (load balancer)    |
| Si el pod muere, pierdes conexión     | Si un pod muere, sigue funcionando    |
| Sin balanceo de carga                 | ✅ Balanceo de carga entre pods       |
| `kubectl port-forward pod/nombre ...` | `kubectl port-forward service/nombre ...` |

---

### ¿Cómo funciona el balanceo de carga?

```
Usuario → service/web-service:80
              ↓
         (Load Balancer)
              ↓
    ┌─────────┼─────────┐
    ↓         ↓         ↓
  Pod 1     Pod 2     Pod 3
  :8080     :8080     :8080
```

---

### Anatomía del Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service        # Nombre del servicio
spec:
  selector:                # ¿Qué pods gestionar?
    app: synergychat-web   # Pods con este label
  ports:                   # Configuración de puertos
  - protocol: TCP          # Protocolo
    port: 80               # Puerto del servicio
    targetPort: 8080       # Puerto del pod
```

---

### Ver Endpoints del Service

Los **endpoints** muestran qué pods están detrás del servicio:

```bash
kubectl get endpoints web-service
```

**Salida esperada:**
```
NAME          ENDPOINTS                                      AGE
web-service   10.244.0.5:8080,10.244.0.6:8080,10.244.0.7:8080   2m
```

---

### Conceptos Importantes

**Service:**
- Abstracción que proporciona un endpoint estable
- Balancea carga automáticamente entre pods
- Sobrevive a la muerte/recreación de pods

**Selector:**
- Define qué pods pertenecen al servicio
- Usa labels para identificar pods
- Actualización automática cuando pods cambian

**Port Mapping:**
- `port`: Puerto donde el servicio escucha (externo al pod)
- `targetPort`: Puerto donde el pod escucha (interno al pod)
- Pueden ser diferentes (ej: servicio:80 → pod:8080)

---

### Comandos Útiles

```bash
# Ver servicios
kubectl get services
kubectl get svc

# Describir un servicio
kubectl describe service web-service

# Ver endpoints (pods asociados)
kubectl get endpoints web-service

# Ver en formato YAML
kubectl get service web-service -o yaml

# Port-forward a un servicio
kubectl port-forward service/web-service 8080:80

# Eliminar un servicio
kubectl delete service web-service
```

**📚 Documentación oficial:**
- Services: https://kubernetes.io/docs/concepts/services-networking/service/

---

## 22. Service Types - Tipos de Servicios

### Inspeccionar tu Service

```bash
kubectl get svc web-service -o yaml
```

**Verás algo como:**
```yaml
spec:
  clusterIP: 10.96.213.234
  ...
  type: ClusterIP
```

---

### ¿Por qué ClusterIP?

**¡No especificamos un tipo de servicio!** Es porque `ClusterIP` es el **tipo de servicio por defecto**.

El `clusterIP` es la dirección IP a la que el servicio está vinculado en la red interna de Kubernetes.

---

### Tipos de Services

#### 1. ClusterIP (Por Defecto)

**Características:**
- Expone el servicio en una IP interna del clúster
- Solo accesible desde **dentro** del clúster
- No accesible desde internet o tu laptop (sin port-forward)

**Cuándo usar:**
- Servicios internos (microservicios que se hablan entre sí)
- Bases de datos internas
- APIs internas

**Ejemplo:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: ClusterIP  # ← Opcional (es el default)
  selector:
    app: synergychat-web
  ports:
  - port: 80
    targetPort: 8080
```

---

#### 2. NodePort

**Características:**
- Expone el servicio en la IP de **cada nodo** en un puerto estático
- Accesible desde fuera del clúster usando `<NodeIP>:<NodePort>`
- Es un `ClusterIP` **+ exposición en nodos**

**Cuándo usar:**
- Desarrollo/testing
- Cuando no tienes un load balancer cloud
- Acceso directo a servicios

**Ejemplo:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service-nodeport
spec:
  type: NodePort
  selector:
    app: synergychat-web
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080  # Puerto en cada nodo (30000-32767)
```

**Acceso:**
```
http://<IP-del-Nodo>:30080
```

---

#### 3. LoadBalancer

**Características:**
- Crea un **load balancer externo** en el cloud (AWS, GCP, Azure)
- Asigna una IP externa fija al servicio
- Es un `NodePort` **+ load balancer cloud**

**Cuándo usar:**
- Producción en la nube
- Cuando quieres exposición externa automática
- Distribución de tráfico entre nodos

**Ejemplo:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service-lb
spec:
  type: LoadBalancer
  selector:
    app: synergychat-web
  ports:
  - port: 80
    targetPort: 8080
```

**Nota:** En Minikube no funciona igual (no estás en cloud real).

---

#### 4. ExternalName

**Características:**
- **Redirección DNS** solamente
- Mapea el servicio a un hostname externo
- No hace proxy, solo DNS CNAME
- Es completamente diferente a los otros tipos

**Cuándo usar:**
- Migración a servicios externos
- Acceso a servicios fuera del clúster con nombre consistente

**Ejemplo:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-api
spec:
  type: ExternalName
  externalName: api.example.com  # DNS externo
```

---

### Jerarquía de Tipos (Construcción Incremental)

```
ClusterIP (base)
    ↓
    + Exposición en nodos
    ↓
NodePort
    ↓
    + Load balancer externo
    ↓
LoadBalancer
```

**ExternalName** está completamente separado (solo DNS).

---

### Comparación de Tipos

| Tipo          | Acceso                    | IP Pública | Uso Principal              |
|---------------|---------------------------|------------|----------------------------|
| ClusterIP     | Solo dentro del clúster   | ❌         | Servicios internos         |
| NodePort      | Nodos + dentro            | ❌         | Dev/testing                |
| LoadBalancer  | Internet + nodos + dentro | ✅         | Producción cloud           |
| ExternalName  | Redirección DNS           | N/A        | Migración/servicios externos |

---

### Pregunta: ¿Cuál es falso?

❌ **FALSO: An ExternalName service has the functionality of all the other types combined**

**Explicación:**

ExternalName es completamente DIFERENTE a los otros tipos:
- Solo hace redirección DNS (CNAME)
- NO crea ClusterIP
- NO crea NodePort
- NO hace proxy de tráfico

Los otros tipos SÍ construyen incrementalmente:
- ✅ NodePort crea ClusterIP interno
- ✅ LoadBalancer expone NodePorts
- ✅ ClusterIP solo expone internamente

---

### Conceptos Clave

- **ClusterIP** = Tipo por defecto, solo interno
- **NodePort** = ClusterIP + puerto en cada nodo
- **LoadBalancer** = NodePort + load balancer cloud
- **ExternalName** = Redirección DNS (caso especial)
- Los tipos se **construyen incrementalmente** unos sobre otros
- Elige según **dónde necesitas acceso** (interno vs externo)

**📚 Documentación oficial:**
- Service Types: https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types

---

## 23. API Service - NodePort

### Crear API Service con NodePort

**Archivo `api-service.yaml`:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  type: NodePort
  selector:
    app: synergychat-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30080
```

---

### Explicación de los campos

```yaml
spec:
  type: NodePort             # Tipo de servicio (expone en nodos)
  
  selector:
    app: synergychat-api     # Selecciona pods con este label
  
  ports:
  - protocol: TCP            # Protocolo
    port: 80                 # Puerto del servicio (interno)
    targetPort: 8080         # Puerto del pod (donde escucha la API)
    nodePort: 30080          # Puerto expuesto en cada nodo (30000-32767)
```

---

### Flujo de tráfico

```
Externo → Nodo:30080
              ↓
          Service:80
              ↓
          Pod:8080
```

---

### Aplicar y verificar

```bash
kubectl apply -f api-service.yaml
kubectl get svc
```

**Salida esperada:**
```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
web-service   ClusterIP   10.96.213.234   <none>        80/TCP         10m
api-service   NodePort    10.96.145.67    <none>        80:30080/TCP   5s
```

**Observa:**
- **TYPE:** `NodePort`
- **PORT(S):** `80:30080/TCP` (servicio:nodePort)

---

### Acceder al API Service

**Con Minikube:**
```bash
minikube ip  # Obtener IP del nodo
# Ejemplo: 192.168.49.2

curl http://192.168.49.2:30080

# O usar atajo de Minikube:
minikube service api-service
```

---

### Rango de NodePort

**NodePort válidos:** 30000-32767

**Ejemplos:**
```yaml
nodePort: 30000  # ✅ Válido
nodePort: 30080  # ✅ Válido
nodePort: 32767  # ✅ Válido (máximo)
nodePort: 8080   # ❌ Inválido (fuera de rango)
```

**📚 Documentación oficial:**
- NodePort: https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport

---

## 24. Crawler Service - ClusterIP

### Crear Crawler Service

El servicio `crawler` solo necesita estar disponible para el servicio `api` (interno al clúster).

**Archivo `crawler-service.yaml`:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: crawler-service
spec:
  selector:
    app: synergychat-crawler
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

**Nota:** `type: ClusterIP` es opcional (es el default).

---

### ¿Por qué ClusterIP?

**El crawler es un servicio interno:**
- Solo necesita ser accesible por el servicio `api`
- NO necesita ser accesible desde fuera del clúster
- NO necesita NodePort o LoadBalancer

**Comunicación interna:**
```
api-service
    ↓ (hace request a)
crawler-service:80
    ↓
crawler pod:8080
```

---

### Aplicar y verificar

```bash
kubectl apply -f crawler-service.yaml
kubectl get svc
```

**Salida esperada:**
```
NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
web-service       ClusterIP   10.96.213.234   <none>        80/TCP         20m
api-service       NodePort    10.96.145.67    <none>        80:30080/TCP   10m
crawler-service   ClusterIP   10.96.178.92    <none>        80/TCP         5s
```

---

### Verificar Endpoints

```bash
kubectl get endpoints crawler-service
```

**Salida esperada:**
```
NAME              ENDPOINTS         AGE
crawler-service   10.244.0.8:8080   10s
```

---

### Resumen de Servicios Creados

| Servicio        | Tipo      | Propósito                            |
|-----------------|-----------|--------------------------------------|
| web-service     | ClusterIP | Balanceo entre pods web (interno)    |
| api-service     | NodePort  | Exponer API al exterior              |
| crawler-service | ClusterIP | Comunicación API → Crawler (interno) |

---

### Arquitectura de Servicios

```
                    FUERA DEL CLÚSTER
                           |
                           ↓
                    api-service:30080 (NodePort)
                           |
    ┌──────────────────────┴──────────────────────┐
    |              DENTRO DEL CLÚSTER             |
    |                                             |
    |  web-service:80        api-service:80       |
    |  (ClusterIP)           (ClusterIP)          |
    |       ↓                     ↓                |
    |  web pods (3x)         api pod (1x)         |
    |                             ↓                |
    |                    crawler-service:80        |
    |                    (ClusterIP)               |
    |                             ↓                |
    |                    crawler pod (1x)          |
    └─────────────────────────────────────────────┘
```

---

### Comunicación Entre Servicios

**Dentro del clúster, los servicios se comunican usando sus nombres:**

```bash
# Desde el pod de la API, hacer request al crawler:
curl http://crawler-service:80

# Kubernetes resuelve automáticamente:
# crawler-service → 10.96.178.92:80
```

---

### Conceptos Clave

**ClusterIP (web y crawler):**
- Solo accesible dentro del clúster
- Ideal para servicios internos
- Comunicación entre microservicios

**NodePort (api):**
- Accesible desde fuera del clúster
- Expone puerto en cada nodo
- Para que clientes externos accedan

**Descubrimiento de Servicios:**
- Los servicios se llaman por nombre: `http://crawler-service`
- Kubernetes DNS resuelve automáticamente
- No necesitas IPs hardcoded

**📚 Documentación oficial:**
- DNS for Services: https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/

---

## 25. Distribución de IPs en Kubernetes

### Tipos de IPs en Kubernetes

Hay **3 tipos diferentes de IPs** en juego:

1. **Cluster-IP (IP del Servicio)** - Virtual y estable
2. **Pod IP (IP del Endpoint)** - Real y efímera
3. **Node IP (IP del Nodo)** - Infraestructura física

---

### 1️⃣ Cluster-IP (IP del Servicio)

**Qué es:**
- IP **virtual** asignada al **Service**
- Única y estable durante la vida del servicio
- Solo accesible **dentro del clúster**

**Ejemplo:**
```
api-service       → 10.96.144.49
crawler-service   → 10.98.130.138
web-service       → 10.107.211.19
```

**Rango de IPs:**
- Usualmente: `10.96.x.x`, `10.98.x.x`, `10.107.x.x`
- Configurado en el clúster (Service CIDR)
- **Asignadas automáticamente** por Kubernetes

---

### 2️⃣ Pod IP (Endpoint IP)

**Qué es:**
- IP **real** del **Pod**
- Asignada cuando el pod se crea
- **Cambia** si el pod se recrea

**Ejemplo:**
```
Endpoints: 10.244.0.59:8080
```

Esta es la IP del **pod del crawler**, no del servicio.

**Rango:**
- Pod IPs: `10.244.x.x` (Pod CIDR)
- Diferente al rango de Services

---

### 3️⃣ Node IP (IP del Nodo)

**Qué es:**
- IP de la **máquina física/virtual** donde corre Kubernetes
- En Minikube es la IP de la VM de Minikube

**Verificar:**
```bash
minikube ip
```

**Ejemplo:**
```
192.168.49.2
```

---

### Diagrama Completo de IPs

```
┌─────────────────────────────────────────┐
│         MINIKUBE NODE (VM)              │
│         IP: 192.168.49.2                │
│                                         │
│  ┌──────────────────────────────────┐  │
│  │  SERVICES (ClusterIPs)           │  │
│  │                                  │  │
│  │  crawler-service → 10.98.130.138 │  │
│  │  api-service     → 10.96.144.49  │  │
│  │  web-service     → 10.107.211.19 │  │
│  └────────────┬─────────────────────┘  │
│               ↓ (enrutan a)            │
│  ┌──────────────────────────────────┐  │
│  │  PODS (IPs Reales)               │  │
│  │                                  │  │
│  │  crawler-pod  → 10.244.0.59:8080 │  │
│  │  api-pod      → 10.244.0.45:8080 │  │
│  │  web-pod-1    → 10.244.0.12:8080 │  │
│  └──────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

---

### Flujo de una Request

**Desde dentro del clúster:**
```
api-pod quiere hablar con crawler-pod
        ↓
curl http://crawler-service:80
        ↓
DNS resuelve: crawler-service → 10.98.130.138
        ↓
Request va a: 10.98.130.138:80 (ClusterIP)
        ↓
kube-proxy enruta a: 10.244.0.59:8080 (Pod IP)
        ↓
crawler-pod recibe la request
```

---

### Resumen de Rangos de IP

| Tipo               | Rango Ejemplo    | Propósito                              |
|--------------------|------------------|----------------------------------------|
| **Node IP**        | 192.168.49.x     | IP de la máquina/VM                    |
| **Service IP**     | 10.96.x.x        | IP virtual del servicio (ClusterIP)    |
|                    | 10.98.x.x        |                                        |
|                    | 10.107.x.x       |                                        |
| **Pod IP**         | 10.244.x.x       | IP real del pod                        |

---

### ¿Por qué diferentes rangos?

**Separación lógica:**

1. **Service IPs** (virtuales y estables)
   - NO cambian aunque los pods mueran
   - Balanceo de carga automático

2. **Pod IPs** (reales y efímeras)
   - Cambian cuando el pod se recrea
   - Comunicación directa pod-a-pod

3. **Node IPs** (infraestructura)
   - Para acceso externo

---

### Verificar todas las IPs

```bash
# 1. IPs de Servicios (ClusterIPs)
kubectl get svc -o wide

# 2. IPs de Pods
kubectl get pods -o wide

# 3. IP del Nodo (Minikube)
minikube ip

# 4. Endpoints (qué pod está detrás de cada servicio)
kubectl get endpoints
```

---

### Ejemplo práctico

**Crawler Service:**
```
Service IP:    10.98.130.138:80    ← IP virtual (estable)
    ↓ (enruta a)
Endpoint IP:   10.244.0.59:8080    ← IP del pod (cambia si se recrea)
```

**Si eliminas el pod del crawler:**

```
Service IP:    10.98.130.138:80    ← MISMA (no cambia) ✅
    ↓
Endpoint IP:   10.244.0.61:8080    ← NUEVA (cambió) ⚠️
```

**Por eso usamos servicios** - la IP del servicio es estable aunque los pods cambien.

---

### Conceptos Clave

1. **Service ClusterIP** = IP virtual y estable del servicio
2. **Pod IP (Endpoint)** = IP real del pod (efímera)
3. **Node IP** = IP de la máquina física/VM
4. **Diferentes rangos** = Separación lógica de redes
5. **Los servicios abstraen** la IP del pod (por eso son útiles)

---

## 26. Change API Service

### Cambiando de NodePort a ClusterIP

En la mayoría de los entornos de Kubernetes basados en la nube, usarás un objeto **Gateway** para exponer servicios. El Gateway no solo expone servicios, sino que también permite:

* Alojar múltiples servicios en la misma dirección IP
* Alojar múltiples servicios en el mismo puerto (enrutamiento basado en rutas)
* Terminar SSL
* Integrar directamente con DNS externo y load balancers

Porque configuraremos un Gateway en el próximo capítulo, no hay razón para exponer el servicio de la API con `NodePort`. Cambiémoslo de vuelta a `ClusterIP`.

---

### Editar api-service.yaml

**Antes (NodePort):**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  type: NodePort          # ← Eliminar
  selector:
    app: synergychat-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30080       # ← Eliminar
```

**Después (ClusterIP):**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  selector:
    app: synergychat-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

---

### Aplicar cambios

```bash
kubectl apply -f api-service.yaml
```

**Salida esperada:**
```
service/api-service configured
```

---

### Verificar el cambio

```bash
kubectl get svc
```

**Salida esperada:**
```
NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
api-service       ClusterIP   10.96.144.49    <none>        80/TCP    15m
crawler-service   ClusterIP   10.98.130.138   <none>        80/TCP    5m
web-service       ClusterIP   10.107.211.19   <none>        80/TCP    25m
```

**Todos los servicios ahora son ClusterIP.**

---

### Estado Actual

**Arquitectura:**
```
┌────────────────────────────────────────┐
│      DENTRO DEL CLÚSTER                │
│                                        │
│  web-service:80 (ClusterIP)            │
│  api-service:80 (ClusterIP)            │
│  crawler-service:80 (ClusterIP)        │
└────────────────────────────────────────┘
```

---

### ¿Por qué este cambio?

**Gateway es mejor para producción:**

**Antes (múltiples NodePorts):**
```
Internet → NodePort1:30080
Internet → NodePort2:30081
Internet → NodePort3:30082
```

**Con Gateway (próximo capítulo):**
```
Internet → Gateway (único punto)
   ↓
/api/*  → api-service (ClusterIP)
/web/*  → web-service (ClusterIP)
```

---

### Ventajas del Gateway

- Múltiples servicios en la misma IP
- Enrutamiento basado en paths
- Terminación SSL/TLS automática
- Integración con DNS externo
- Más seguro (menos superficie de ataque)

---

### Conceptos Clave

**Gateway (próximo capítulo):**
- Reemplazo moderno de Ingress
- Punto único de entrada al clúster
- Enrutamiento avanzado
- Gestión de SSL/TLS

**Por qué ClusterIP internamente:**
- Servicios internos no necesitan exposición directa
- Gateway maneja toda la exposición externa
- Más seguro y flexible

**📚 Documentación oficial:**
- Gateway API: https://gateway-api.sigs.k8s.io/

---

## 27. Cómo se Crean los Pods

### Respuesta Corta

**Normalmente NO creas pods directamente.** Creas un **Deployment**, y el Deployment crea automáticamente los pods por ti.

---

### Formas de Crear Pods

#### ❌ 1. Crear Pod Directamente (NO recomendado)

**Comando imperativo:**
```bash
kubectl run mi-pod --image=nginx
```

**¿Por qué NO se recomienda?**
- Si el pod muere, NO se recrea automáticamente
- No hay escalado
- No hay actualizaciones rolling
- No hay auto-sanación

---

#### ✅ 2. Crear Pod vía Deployment (RECOMENDADO)

**Con archivo YAML:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mi-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mi-app
  template:           # ← Aquí defines cómo será el pod
    metadata:
      labels:
        app: mi-app
    spec:
      containers:
      - name: nginx
        image: nginx
```

```bash
kubectl apply -f deployment.yaml
```

---

### Flujo de Creación

```
1. Tú creas un Deployment
   ↓
   kubectl apply -f deployment.yaml

2. El Deployment crea un ReplicaSet
   ↓
   Deployment Controller

3. El ReplicaSet crea los Pods
   ↓
   ReplicaSet Controller

4. Los Pods se ejecutan en los Nodos
   ↓
   Kubelet (agente en el nodo)
```

---

### Ejemplo: Synergychat-web

**Cuando ejecutaste:**
```bash
kubectl create deployment synergychat-web --image=bootdotdev/synergychat-web:latest
```

**Proceso:**

1. Se creó el **Deployment**: `synergychat-web`
2. El Deployment creó un **ReplicaSet**: `synergychat-web-679cbcc6cd`
3. El ReplicaSet creó el **Pod**: `synergychat-web-679cbcc6cd-abc12`

---

### Verificar el Flujo

```bash
# 1. Ver el Deployment
kubectl get deployments

# 2. Ver el ReplicaSet (creado automáticamente)
kubectl get replicasets

# 3. Ver los Pods (creados automáticamente)
kubectl get pods
```

---

### La Sección `template` en el Deployment

**Esta sección define CÓMO será el pod:**

```yaml
spec:
  replicas: 3
  template:              # ← Template del Pod
    metadata:
      labels:
        app: synergychat-web
    spec:                # ← Especificación del Pod
      containers:        # ← Contenedores del Pod
      - name: web
        image: bootdotdev/synergychat-web:latest
        ports:
        - containerPort: 8080
```

---

### ¿Qué pasa cuando escalas?

```bash
kubectl scale deployment synergychat-web --replicas=5
```

**Proceso:**
1. Actualizas el Deployment (replicas: 5)
2. El Deployment actualiza el ReplicaSet (desired: 5)
3. El ReplicaSet **crea 2 pods adicionales** automáticamente

---

### ¿Qué pasa cuando eliminas un Pod?

```bash
kubectl delete pod synergychat-web-679cbcc6cd-abc12
```

**Proceso:**
1. El pod es eliminado
2. El ReplicaSet detecta: "Tengo 2 pods, pero necesito 3"
3. El ReplicaSet **crea un nuevo pod automáticamente**
4. El nuevo pod tiene un nombre diferente

---

### Comparación Visual

**Sin Deployment (Pod directo):**
```
Tú → Creas Pod → Pod corre
              ↓
         Pod muere
              ↓
          Se acabó ❌
```

**Con Deployment:**
```
Tú → Creas Deployment → ReplicaSet → Pods
                            ↓
                     Monitorea constantemente
                            ↓
                      Pod muere?
                            ↓
                   Crea nuevo pod ✅
```

---

### Resumen

**El Deployment:**
- Crea el ReplicaSet
- El ReplicaSet crea los Pods
- Si un pod muere, se recrea automáticamente
- Puedes escalar fácilmente
- Actualizaciones rolling automáticas

**Todos tus pods fueron creados vía Deployments:**
- `web-deployment.yaml` → creó pods web
- `api-deployment.yaml` → creó pods api
- `crawler-deployment.yaml` → creó pods crawler

**📚 Documentación oficial:**
- Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

---

## 28. Gateway - Introducción

### ¿Qué es un Gateway?

Un recurso **Gateway** expone servicios al mundo exterior y se usa frecuentemente en entornos de producción.

**De la documentación oficial:**

> Un Gateway describe una instancia de infraestructura de manejo de tráfico. Define un endpoint de red que puede ser usado para procesar tráfico, es decir, filtrar, balancear, dividir, etc. para backends como un Service. Por ejemplo, un Gateway puede representar un load balancer en la nube o un servidor proxy dentro del clúster que está configurado para aceptar tráfico HTTP.

---

### Gateway vs Ingress

**Gateway** es la alternativa más nueva a **Ingress** que todavía podrías encontrar en sistemas de producción.

| Característica | Ingress | Gateway |
|----------------|---------|---------|
| **Edad** | Antiguo (desde 2015) | Nuevo (desde 2021) |
| **Flexibilidad** | Limitado | Muy flexible |
| **Tipos de tráfico** | Solo HTTP/HTTPS | HTTP, TCP, gRPC, etc. |
| **Configuración** | Monolítica | Modular (roles) |
| **Estandarización** | Anotaciones propietarias | API estándar |

---

### Arquitectura con Gateway

```
Internet / Cliente
        ↓
  Gateway (punto único de entrada)
        ↓
   HTTPRoutes (reglas de enrutamiento)
        ↓
   Services (ClusterIP)
        ↓
   Pods (aplicaciones)
```

---

### Instalación de Envoy Gateway

La API de Gateway es una especificación que tiene diferentes implementaciones. Usamos **Envoy Gateway**.

**Instalar:**
```bash
kubectl apply --server-side -f https://github.com/envoyproxy/gateway/releases/download/v1.5.1/install.yaml
```

**Esperar a que se complete:**
```bash
kubectl wait --timeout=5m -n envoy-gateway-system deployment/envoy-gateway --for=condition=Available
```

---

### Componentes del Gateway

#### 1. GatewayClass

Define QUÉ implementación usar (Envoy, Istio, etc.)

**Archivo: `app-gatewayclass.yaml`**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: app-gatewayclass
spec:
  controllerName: gateway.envoyproxy.io/gatewayclass-controller
```

**Concepto:**
- GatewayClass : Gateway
- Como StorageClass : PersistentVolume

---

#### 2. Gateway

Instancia real del load balancer.

**Archivo: `app-gateway.yaml`**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: app-gateway
spec:
  gatewayClassName: app-gatewayclass
  listeners:
    - name: http
      protocol: HTTP
      port: 80
```

**Este Gateway:**
- Usa la clase `app-gatewayclass`
- Escucha en el puerto 80
- Acepta tráfico HTTP

---

#### 3. HTTPRoute

Define reglas de enrutamiento HTTP.

**Archivo: `web-httproute.yaml`**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-httproute
spec:
  parentRefs:
    - name: app-gateway
  hostnames:
    - "synchat.internal"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: web-service
          port: 80
```

**Archivo: `api-httproute.yaml`**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: api-httproute
spec:
  parentRefs:
    - name: app-gateway
  hostnames:
    - "synchatapi.internal"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /
      backendRefs:
        - name: api-service
          port: 80
```

**Esto dice que:**
- Tráfico a `synchat.internal` → `web-service`
- Tráfico a `synchatapi.internal` → `api-service`

---

### Aplicar recursos

```bash
# 1. GatewayClass
kubectl apply -f app-gatewayclass.yaml

# 2. Gateway
kubectl apply -f app-gateway.yaml

# 3. HTTPRoutes
kubectl apply -f web-httproute.yaml
kubectl apply -f api-httproute.yaml
```

---

### Verificar recursos

```bash
# Ver GatewayClass
kubectl get gatewayclass

# Ver Gateway
kubectl get gateway

# Ver HTTPRoutes
kubectl get httproute
```

**Salida esperada:**
```
# GatewayClass
NAME               CONTROLLER                                      ACCEPTED
app-gatewayclass   gateway.envoyproxy.io/gatewayclass-controller   True

# Gateway
NAME          CLASS              ADDRESS   PROGRAMMED   AGE
app-gateway   app-gatewayclass             True         30s

# HTTPRoute
NAME             HOSTNAMES              AGE
web-httproute    ["synchat.internal"]   20s
api-httproute    ["synchatapi.internal"] 15s
```

---

### Flujo de una Request con Gateway

```
1. Usuario → http://synchat.internal
2. app-gateway recibe en puerto 80
3. Busca HTTPRoute con hostname "synchat.internal"
4. Encuentra web-httproute
5. Enruta a web-service
6. web-service balancea entre 3 web pods
7. Respuesta al usuario
```

---

### Ventajas del Gateway

**vs NodePort/LoadBalancer:**
- ✅ Múltiples servicios en la misma IP
- ✅ Enrutamiento basado en hostname y path
- ✅ Configuración más rica (headers, pesos, etc.)
- ✅ Terminación SSL/TLS

**vs Ingress:**
- ✅ API más moderna y extensible
- ✅ Soporte para más protocolos
- ✅ Separación de roles (admin vs dev)
- ✅ Mejor para casos de uso complejos

---

### Comandos útiles

```bash
# Ver GatewayClasses
kubectl get gatewayclass
kubectl describe gatewayclass app-gatewayclass

# Ver Gateways
kubectl get gateway
kubectl describe gateway app-gateway

# Ver HTTPRoutes
kubectl get httproute
kubectl describe httproute web-httproute

# Ver logs de Envoy Gateway
kubectl logs -n envoy-gateway-system deployment/envoy-gateway
```

**📚 Documentación oficial:**
- Gateway API: https://gateway-api.sigs.k8s.io/
- Envoy Gateway: https://gateway.envoyproxy.io/

---

## 29. DNS Local - /etc/hosts

### Configuración DNS local

Ahora que el Gateway está configurado para enrutar dominios, necesitamos configurar nuestra máquina local para resolver esos dominios al Gateway load balancer.

**NO estaremos configurando DNS global** - solo nuestra máquina local.

---

### El archivo /etc/hosts

Hay un archivo llamado `/etc/hosts` en tu máquina local que se usa para resolver nombres de dominio a direcciones IP.

**Ubicación:**
- **macOS/Linux:** `/etc/hosts`
- **Windows:** `C:\Windows\System32\drivers\etc\hosts`

---

### Editar /etc/hosts

**En macOS/Linux:**

```bash
sudo nano /etc/hosts
```

**Agregar al final del archivo:**

```
127.0.0.1        synchat.internal
127.0.0.1        synchatapi.internal
```

**Guardar:** `CTRL + O`, `ENTER`, `CTRL + X`

---

### Verificar que funciona

```bash
ping synchat.internal
```

**Salida esperada:**
```
PING synchat.internal (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.104 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.105 ms
...
```

**Detener el ping:** `CTRL + C`

**Verificar ambos dominios:**
```bash
ping synchatapi.internal
```

---

### Múltiples dominios en la misma IP

**El archivo /etc/hosts permite múltiples hostnames** apuntando a la misma IP:

```
127.0.0.1       localhost
127.0.0.1       synchat.internal
127.0.0.1       synchatapi.internal
```

**Todos apuntan a `127.0.0.1` y todos funcionan.** ✅

---

### ¿Cómo diferencia el Gateway?

Aunque todos apuntan a `127.0.0.1`, el **Gateway sabe diferenciarlos** por el **HTTP Host header**.

**Request a synchat.internal:**
```http
GET / HTTP/1.1
Host: synchat.internal   ← Gateway ve esto y enruta a web-service
```

**Request a synchatapi.internal:**
```http
GET / HTTP/1.1
Host: synchatapi.internal ← Gateway ve esto y enruta a api-service
```

---

### Características de /etc/hosts

**✅ LOCAL:**
- Solo afecta a TU máquina
- Otros no pueden ver tus entradas

**✅ PRIORIDAD:**
- Se consulta ANTES que DNS servers
- Sobrescribe DNS público

**✅ INMEDIATO:**
- Cambios toman efecto inmediatamente
- No requiere reiniciar

---

### Secuencia de resolución

```
1. DNS (/etc/hosts)
   synchat.internal → ¿qué IP?
   /etc/hosts responde: 127.0.0.1

2. IP Address
   Navegador abre conexión TCP a: 127.0.0.1:80

3. Request HTTP con Host Header
   GET / HTTP/1.1
   Host: synchat.internal

4. Gateway lee el Host header y enruta
```

---

### Troubleshooting

**Si obtienes "cannot resolve host":**

1. **Verificar el archivo:**
   ```bash
   cat /etc/hosts | grep synchat
   ```

2. **Verificar sintaxis:**
   - Espacios (no tabs) entre IP y hostname
   - Sin espacios extras al inicio

3. **Verificar permisos:**
   - Asegúrate de usar `sudo` al editar

---

### Para macOS (si 127.0.0.1 no funciona)

Usa la IP de Minikube:

```bash
minikube ip
# Ejemplo: 192.168.49.2
```

**Actualizar /etc/hosts:**
```
192.168.49.2     synchat.internal
192.168.49.2     synchatapi.internal
```

---

### Conceptos clave

- **`/etc/hosts`** - Archivo local de mapeo DNS
- **`127.0.0.1`** - Dirección IP de localhost
- **DNS Local vs Global** - Local solo para tu máquina
- **Host header** - Cómo el Gateway diferencia dominios

**📚 Documentación oficial:**
- hosts file: https://en.wikipedia.org/wiki/Hosts_(file)

---

## 30. Minikube Tunnel

### ¿Por qué necesitamos tunnel?

En producción, el Gateway tendría una IP pública real. En desarrollo con Minikube, el Gateway está en una VM aislada.

**El problema:**
- Minikube corre en una VM aislada en tu máquina
- El Gateway tiene una IP interna (10.96.x.x) que solo existe dentro de Minikube
- Tu navegador NO puede acceder a esa IP interna

---

### ¿Qué hace minikube tunnel?

Crea un "puente" entre tu laptop y la red interna de Minikube.

```
Tu navegador (127.0.0.1:80)
        ↓
minikube tunnel (PUENTE)
        ↓
Gateway en Minikube (10.96.x.x)
        ↓
Services → Pods
```

---

### Abrir el tunnel

```bash
minikube tunnel -c
```

**Nota:** Probablemente necesitarás ingresar tu contraseña.

**Salida esperada:**
```
Status:	
	machine: minikube
	pid: 12345
	route: 10.96.0.0/12 -> 192.168.49.2
	minikube: Running
	services: [envoy-default-app-gateway-7e01cf4f]
```

---

### Mantener el túnel abierto

**Recomendación:** Mantén este comando corriendo en una **terminal separada**.

```bash
# Terminal 1: Túnel (déjalo corriendo)
minikube tunnel -c

# Terminal 2: Otros comandos
kubectl get pods
```

---

### Acceder a las aplicaciones

**Con el tunnel corriendo, abre en tu navegador:**

```
http://synchat.internal
```

**Deberías ver:** La aplicación web de SynergyChat 🎉

**Probar la API:**
```
http://synchatapi.internal/healthz
```

---

### Comparación: Desarrollo vs Producción

#### DESARROLLO (con tunnel):

```
http://synchat.internal
    ↓
/etc/hosts: synchat.internal → 127.0.0.1
    ↓
minikube tunnel: 127.0.0.1 → 10.96.x.x (Gateway)
    ↓
Gateway: synchat.internal → web-service
    ↓
web-service → web pods
```

#### PRODUCCIÓN (sin tunnel):

```
http://synchat.com
    ↓
DNS público: synchat.com → 34.123.45.67
    ↓
Cloud LoadBalancer: 34.123.45.67 → Gateway
    ↓
Gateway: synchat.com → web-service
    ↓
web-service → web pods
```

---

### Razones del tunnel

**1. Minikube es una VM aislada**
```
Tu laptop (red: 192.168.1.x)
   ↓
Minikube VM (red interna: 10.96.x.x)
```

Dos redes separadas que no se hablan naturalmente.

**2. LoadBalancer simulado**

En producción, `type: LoadBalancer` crea un LoadBalancer real con IP pública.

En Minikube, `minikube tunnel` SIMULA el LoadBalancer.

**3. Acceso desde el navegador**

Sin tunnel, el navegador no puede llegar a IPs internas de Minikube.

---

### Comparación de arquitecturas

| Aspecto | Producción | Desarrollo (Minikube) |
|---------|------------|----------------------|
| **LoadBalancer** | Real (en la nube) | Simulado (minikube tunnel) |
| **IP** | Pública (34.x.x.x) | Local (127.0.0.1) |
| **DNS** | DNS global | /etc/hosts local |
| **Acceso** | Desde cualquier parte | Solo tu laptop |
| **Necesita tunnel** | ❌ NO | ✅ SÍ |

---

### Troubleshooting

**Si no puedes acceder:**

1. **Verificar que el túnel esté corriendo:**
   ```bash
   ps aux | grep "minikube tunnel"
   ```

2. **Verificar /etc/hosts:**
   ```bash
   cat /etc/hosts | grep synchat
   ```

3. **Verificar el Gateway:**
   ```bash
   kubectl get gateway
   ```

---

### Conceptos clave

- **minikube tunnel** - Expone LoadBalancer a localhost
- **Requiere privilegios** - Por eso pide password
- **Debe mantenerse corriendo** - Terminal separada
- **Solo para desarrollo** - Producción no lo necesita

**El tunnel es un "hack de desarrollo"** para simular lo que un LoadBalancer real haría en producción.

---

## 31. Gateway Types y Annotations

### API Versions en Kubernetes

**Recursos core:**
```yaml
apiVersion: v1  # Pod, Service, ConfigMap
```

**Gateway (extensión oficial):**
```yaml
apiVersion: gateway.networking.k8s.io/v1  # Gateway, HTTPRoute
```

---

### Grupos de API

| Recurso | apiVersion | Tipo |
|---------|------------|------|
| Pod | `v1` | Core |
| Service | `v1` | Core |
| Deployment | `apps/v1` | Core extension |
| **Gateway** | `gateway.networking.k8s.io/v1` | **Official extension** |
| **HTTPRoute** | `gateway.networking.k8s.io/v1` | **Official extension** |

---

### ¿Por qué Gateway es una extensión?

**Razones:**

1. **No todos lo necesitan** - Kubernetes puede funcionar sin Gateway
2. **Evoluciona más rápido** - Las extensiones pueden actualizarse sin cambiar el core
3. **Modular** - Puedes elegir qué implementación usar (Envoy, Istio, etc.)
4. **Cloud-specific** - Diferentes clouds tienen diferentes necesidades

---

### Annotations

El **core API de Kubernetes** se mantiene intencionalmente **pequeño**.

**Solución:** Kubernetes permite agregar **annotations arbitrarias** a recursos.

**Ejemplo en Google Cloud Platform:**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: external-http
  annotations:
    networking.gke.io/certmap: certmap-name-here
spec:
  gatewayClassName: gke-l7-global-external-managed
```

---

### Annotations por Cloud Provider

#### Google Cloud Platform (GKE)
```yaml
annotations:
  networking.gke.io/certmap: my-cert-map
  cloud.google.com/neg: '{"ingress": true}'
```

#### Amazon Web Services (EKS)
```yaml
annotations:
  service.beta.kubernetes.io/aws-load-balancer-ssl-cert: arn:aws:acm:...
```

#### Microsoft Azure (AKS)
```yaml
annotations:
  service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

---

### Concepto clave

En producción, usarás **annotations específicas del proveedor de nube**.

Cada cloud provider tiene sus propios productos y configuraciones.

**Los conceptos son universales**, la implementación varía por cloud.

---

## 32. Conectando Frontend y API

### Crear ConfigMap para Web Service

El frontend necesita saber la URL de la API.

**Archivo: `web-configmap.yaml`**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-web-configmap
data:
  WEB_PORT: "8080"
  API_URL: "http://synchatapi.internal"
```

---

### Actualizar Web Deployment

**Archivo: `web-deployment.yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-web
  labels:
    app: synergychat-web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: synergychat-web
  template:
    metadata:
      labels:
        app: synergychat-web
    spec:
      containers:
      - name: synergychat-web
        image: docker.io/bootdotdev/synergychat-web:latest
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: synergychat-web-configmap
```

---

### Aplicar cambios

```bash
# 1. Crear el ConfigMap
kubectl apply -f web-configmap.yaml

# 2. Actualizar el Deployment
kubectl apply -f web-deployment.yaml
```

---

### Probar la aplicación completa

**1. Asegúrate que minikube tunnel esté corriendo:**
```bash
# En terminal separada
minikube tunnel -c
```

**2. Abrir en navegador:**
```
http://synchat.internal
```

**3. Enviar mensajes:**
- Ingresa un username
- Escribe un mensaje
- Presiona Enter

**4. Refrescar la página:**
- Los mensajes deberían seguir ahí (guardados en memoria del pod de la API)

---

### Experimento: Eliminar el pod de la API

**1. Crear varios mensajes**

**2. Eliminar el pod:**
```bash
kubectl get pods -l app=synergychat-api
kubectl delete pod <api-pod-name>
```

**3. Esperar a que Kubernetes cree un nuevo pod:**
```bash
kubectl get pods -l app=synergychat-api -w
```

**4. Refrescar la página**

**Resultado:** Los mensajes desaparecieron ❌

**¿Por qué?** Porque estaban guardados en la memoria del pod eliminado. El nuevo pod es una instancia completamente nueva.

---

### Flujo de comunicación

```
Navegador
    ↓
http://synchat.internal
    ↓
web-service → web pod
    ↓ (página cargada)
Navegador ejecuta JavaScript
    ↓
fetch('http://synchatapi.internal/messages')
    ↓
api-httproute → api-service → api pod
    ↓
API guarda mensaje en memoria
```

---

### Limitación importante

**Los datos guardados en memoria del pod NO son persistentes.**

**En el futuro del curso aprenderás:**
- **Volumes** - Para almacenamiento persistente
- **Databases** - Para guardar datos de forma duradera
- **StatefulSets** - Para aplicaciones que necesitan estado

---

### Archivos creados/modificados en esta sección

```
deployments/
├── web-configmap.yaml          ← NUEVO
├── web-deployment.yaml         ← MODIFICADO
├── api-configmap.yaml
├── crawler-configmap.yaml
├── web-service.yaml
├── api-service.yaml
├── crawler-service.yaml
├── api-deployment.yaml
├── crawler-deployment.yaml
├── app-gatewayclass.yaml       ← NUEVO
├── app-gateway.yaml            ← NUEVO
├── web-httproute.yaml          ← NUEVO
└── api-httproute.yaml          ← NUEVO
```

---

### Conceptos clave aprendidos

1. **Gateway para exposición externa** - Punto único de entrada
2. **Hostname-based routing** - Gateway enruta por dominio
3. **DNS local para desarrollo** - /etc/hosts mapea dominios a localhost
4. **minikube tunnel** - Simula LoadBalancer en desarrollo
5. **Datos en memoria son efímeros** - Se pierden al eliminar pods
6. **ConfigMaps para frontend** - El web también necesita configuración

**📚 Documentación oficial:**
- Gateway API: https://gateway-api.sigs.k8s.io/
- Envoy Gateway: https://gateway.envoyproxy.io/

---

## 33. Consultas Teóricas Frecuentes

### ¿Por qué necesito Docker corriendo para Minikube?

#### Minikube necesita un "driver"

Minikube no crea máquinas virtuales por sí solo. Necesita una herramienta que le permita crear y gestionar contenedores o VMs. Esta herramienta se llama **"driver"**.

#### Drivers disponibles:

1. **Docker** (el más común) ⭐
2. **VirtualBox**
3. **VMware**
4. **Hyper-V** (Windows)
5. **KVM** (Linux)
6. **Podman**

---

#### ¿Por qué Docker específicamente?

**Docker crea el "nodo" del clúster:**

```
Tu MacBook
  └── Docker Desktop (corriendo)
       └── Contenedor especial de Minikube
            └── Dentro: Kubernetes completo
                 └── Tus pods (contenedores de aplicación)
```

**El clúster de Minikube es en sí mismo un contenedor de Docker.**

---

#### Contenedores dentro de contenedores

```
┌─────────────────────────────────────┐
│   Docker Desktop (debe estar ON)   │
│                                     │
│  ┌───────────────────────────────┐ │
│  │ Contenedor de Minikube        │ │
│  │ (el "nodo" de Kubernetes)     │ │
│  │                               │ │
│  │  ┌─────────┐  ┌─────────┐    │ │
│  │  │ Pod 1   │  │ Pod 2   │    │ │
│  │  │ (App)   │  │ (App)   │    │ │
│  │  └─────────┘  └─────────┘    │ │
│  └───────────────────────────────┘ │
└─────────────────────────────────────┘
```

---

#### Verificación práctica

```bash
# Ver contenedores de Docker corriendo
docker ps

# Deberías ver algo como:
# CONTAINER ID   IMAGE                    NAMES
# abc123def456   gcr.io/k8s-minikube/...  minikube
```

**Ese contenedor llamado "minikube" ES tu clúster de Kubernetes.**

---

#### Resumen

**Docker es la "plataforma base" donde Minikube construye el clúster de Kubernetes.**

- Docker = La herramienta de virtualización
- Minikube = Usa Docker para crear un nodo de Kubernetes
- Tus apps = Corren como pods dentro de ese nodo

**📚 Documentación:**
- Drivers de Minikube: https://minikube.sigs.k8s.io/docs/drivers/

---

### ¿Qué hace el guion `-` en YAML?

#### El guion indica un elemento de lista

El guion `-` en YAML indica un **elemento de una lista** (array).

---

#### Ejemplos básicos

**Lista simple:**
```yaml
frutas:
  - manzana
  - naranja
  - plátano
```

**Equivalente en JSON:**
```json
{
  "frutas": ["manzana", "naranja", "plátano"]
}
```

---

#### En Kubernetes

**Lista de contenedores:**
```yaml
containers:
  - name: contenedor1
    image: nginx
  - name: contenedor2
    image: redis
```

Esto significa: "El pod tiene una **lista** de 2 contenedores"

---

**Lista de variables de entorno:**
```yaml
env:
  - name: API_PORT
    value: "8080"
  - name: DATABASE_URL
    value: "postgres://db:5432"
```

Esto significa: "El contenedor tiene una **lista** de 2 variables de entorno"

---

#### Regla de Indentación

El `-` debe estar **al mismo nivel de indentación** que los otros elementos de la lista.

**✅ Correcto:**
```yaml
env:
  - name: VAR1
    value: "valor1"
  - name: VAR2
    value: "valor2"
```

**❌ Incorrecto:**
```yaml
env:
- name: VAR1           # ← Mal indentado
  value: "valor1"
```

---

#### En tu deployment

```yaml
spec:
  containers:          # ← Lista de contenedores
  - name: api          # ← Elemento 1
    image: ...
    env:               # ← Lista de variables
    - name: VAR1       # ← Elemento 1 de env
      value: "..."
    - name: VAR2       # ← Elemento 2 de env
      value: "..."
```

---

#### Resumen

- **`-`** = "Este es un elemento de una lista/array"
- Se usa cuando un campo puede tener **múltiples valores**
- Común en: `containers`, `env`, `ports`, `volumes`

**Sin `-`**: Un solo valor  
**Con `-`**: Lista de valores

---

### Definiciones de Cluster, Nodos y Pods

#### Cluster (definición general)

**Un cluster es un grupo de computadoras (servidores) que trabajan juntas como si fueran un solo sistema.**

**Características:**
- Múltiples máquinas conectadas por red
- Trabajan coordinadamente
- Se ven como un sistema único desde el exterior
- Distribuyen el trabajo entre todas las máquinas

---

#### Jerarquía en Kubernetes

```
Cluster de Kubernetes (1 sistema completo)
  └── Nodos (1 o más máquinas físicas/virtuales)
       └── Pods (múltiples)
            └── Contenedores (1 o más por pod, usualmente 1)
```

---

#### Definiciones:

**Cluster de Kubernetes:**
- TODO el sistema completo
- Hay UN SOLO Kubernetes por cluster
- El cluster contiene nodos

**Nodos (Nodes):**
- Son las máquinas (servidores físicos o VMs)
- Un cluster tiene múltiples nodos en producción
- Minikube = cluster con 1 solo nodo (tu laptop)

**Pods:**
- Son abstracciones que envuelven contenedores
- Múltiples pods pueden existir en un nodo
- Un pod generalmente contiene 1 contenedor

**Contenedores:**
- Son las aplicaciones corriendo (Docker)
- Viven dentro de los pods

---

#### Diagrama completo

```
┌─────────────────────────────────────────┐
│   CLUSTER DE KUBERNETES (1 sistema)     │
│                                         │
│  ┌──────────────┐  ┌──────────────┐   │
│  │   NODO 1     │  │   NODO 2     │   │
│  │              │  │              │   │
│  │  ┌────────┐  │  │  ┌────────┐  │   │
│  │  │ Pod 1  │  │  │  │ Pod 3  │  │   │
│  │  │ ┌────┐ │  │  │  │ ┌────┐ │  │   │
│  │  │ │Cont│ │  │  │  │ │Cont│ │  │   │
│  │  │ └────┘ │  │  │  │ └────┘ │  │   │
│  │  └────────┘  │  │  └────────┘  │   │
│  │              │  │              │   │
│  │  ┌────────┐  │  │  ┌────────┐  │   │
│  │  │ Pod 2  │  │  │  │ Pod 4  │  │   │
│  │  │ ┌────┐ │  │  │  │ ┌────┐ │  │   │
│  │  │ │Cont│ │  │  │  │ │Cont│ │  │   │
│  │  │ └────┘ │  │  │  │ └────┘ │  │   │
│  │  └────────┘  │  │  └────────┘  │   │
│  └──────────────┘  └──────────────┘   │
└─────────────────────────────────────────┘
```

---

#### Analogía con edificios

- **Cluster** = Ciudad completa
- **Nodos** = Edificios en la ciudad
- **Pods** = Departamentos en los edificios
- **Contenedores** = Personas viviendo en los departamentos

---

#### En tu caso (Minikube)

```
Cluster "minikube"
  └── 1 Nodo (tu MacBook)
       └── Pod: synergychat-web-xxxxx
            └── 1 Contenedor (imagen synergychat-web)
```

---

## 22. Glosario de Términos

### Cluster

**Definición general:** Un grupo de computadoras (servidores) que trabajan juntas como si fueran un solo sistema.

**Cluster de Kubernetes:** Grupo de servidores que ejecutan y gestionan contenedores de manera coordinada.

**Características:**
- Múltiples máquinas conectadas por red
- Trabajan coordinadamente para lograr un objetivo común
- Se ven como un sistema único desde el exterior
- Distribuyen el trabajo entre todas las máquinas

### Jerarquía en Kubernetes

```
Cluster de Kubernetes (1 sistema completo)
  └── Nodos (1 o más máquinas físicas/virtuales)
       └── Pods (múltiples)
            └── Contenedores (1 o más por pod, usualmente 1)
```

### Nodo (Node)

Son las **máquinas** (servidores físicos o VMs) donde se ejecutan los Pods.

- Un cluster tiene **múltiples nodos** en producción
- Minikube = cluster con **1 solo nodo** (tu laptop)

### Pod

Unidad mínima desplegable en Kubernetes. Es una abstracción que envuelve uno o más contenedores.

### Contenedor

Las **aplicaciones corriendo** dentro de los Pods (generalmente Docker).

### Deployment

Objeto de Kubernetes que gestiona y mantiene réplicas de Pods.

### K8s

Abreviatura de Kubernetes (8 letras entre K y s).

### Kubectl

Herramienta de línea de comandos para interactuar con clústeres de Kubernetes.

### Minikube

Herramienta que permite ejecutar un clúster de Kubernetes de un solo nodo localmente.

### Control Plane

El "cerebro" de Kubernetes que gestiona todo el clúster.

### API Server

Componente del Control Plane que acepta y procesa comandos de kubectl.

### CoreDNS

Servicio DNS interno de Kubernetes que permite comunicación entre Pods usando nombres.

### Efímero

Temporal, no permanente. Los Pods son efímeros.

### Inmutabilidad

Concepto de no modificar recursos existentes, sino reemplazarlos con nuevas versiones.

### Stateless

Aplicación que no guarda estado local, todos los datos están en almacenamiento externo.

### Port-forward

Túnel temporal para acceder a recursos internos del clúster desde tu máquina local.

### Namespace

Espacio de nombres lógico para organizar recursos en Kubernetes (default, kube-system, etc.).

---

## 12. Comandos Útiles

### Kubectl - Comandos Básicos

```bash
# Ver versión de kubectl
kubectl version --client

# Ver información del clúster
kubectl cluster-info

# Ver nodos del clúster
kubectl get nodes

# Ver todos los recursos
kubectl get all
```

### Deployments

```bash
# Crear un deployment
kubectl create deployment <nombre> --image=<imagen>

# Ver deployments
kubectl get deployments

# Escalar un deployment
kubectl scale deployment <nombre> --replicas=3

# Eliminar un deployment
kubectl delete deployment <nombre>
```

### Pods

```bash
# Ver pods
kubectl get pods

# Ver pods con información adicional
kubectl get pods -o wide

# Describir un pod específico
kubectl describe pod <nombre-pod>

# Ver logs de un pod
kubectl logs <nombre-pod>

# Ejecutar comando en un pod
kubectl exec -it <nombre-pod> -- /bin/bash

# Eliminar un pod
kubectl delete pod <nombre-pod>

# Port forwarding
kubectl port-forward <nombre-pod> <puerto-local>:<puerto-pod>
```

### Minikube

```bash
# Iniciar Minikube
minikube start

# Ver estado de Minikube
minikube status

# Detener Minikube
minikube stop

# Eliminar el clúster de Minikube
minikube delete

# Abrir dashboard
minikube dashboard --port=63840

# Ver IP del clúster
minikube ip

# SSH al nodo de Minikube
minikube ssh
```

### Kubernetes Proxy

```bash
# Iniciar proxy
kubectl proxy

# Acceder al API
# Navegar a: http://127.0.0.1:8001/api/v1/namespaces/default/pods
```

### Namespace

```bash
# Ver namespaces
kubectl get namespaces

# Ver pods en un namespace específico
kubectl get pods -n <namespace>
```

### Información y Debug

```bash
# Ver eventos del clúster
kubectl get events

# Ver recursos de un pod
kubectl top pod <nombre-pod>

# Ver configuración completa
kubectl get pod <nombre-pod> -o yaml
```

---

## Conceptos Clave Aprendidos

1. **Kubernetes** es un sistema de orquestación de contenedores
2. **kubectl** es la herramienta CLI para interactuar con clústeres
3. **Minikube** permite ejecutar Kubernetes localmente
4. **Pods** son la unidad mínima desplegable y son efímeros
5. **Deployments** gestionan y mantienen réplicas de Pods
6. **ConfigMaps** gestionan variables de entorno sin reconstruir imágenes
7. **Services** proporcionan endpoints estables y balanceo de carga
8. **Gateway** expone servicios al exterior con enrutamiento inteligente
9. **HTTPRoutes** definen reglas de enrutamiento basadas en hostname y path
10. **DNS local (/etc/hosts)** permite resolver dominios personalizados
11. **minikube tunnel** simula LoadBalancer en desarrollo
12. El concepto de **inmutabilidad**: reemplazar en lugar de modificar

---

## Próximos Pasos

- Aprender sobre Secrets para datos sensibles
- Estudiar Volumes para almacenamiento persistente
- Explorar Persistent Volumes y Persistent Volume Claims
- StatefulSets para aplicaciones con estado
- Configurar SSL/TLS en Gateway para producción

---

## Referencias Importantes

- **Documentación oficial de Kubernetes:** https://kubernetes.io/docs/home/
- **kubectl Cheat Sheet:** https://kubernetes.io/docs/reference/kubectl/cheatsheet/
- **Minikube Docs:** https://minikube.sigs.k8s.io/docs/
- **Gateway API:** https://gateway-api.sigs.k8s.io/
- **Envoy Gateway:** https://gateway.envoyproxy.io/
- **Repositorio del curso:** https://github.com/cecibelauda/k8s_course

---

**Última actualización:** Días 1-5 + Gateway  
**Versión de Kubernetes:** v1.35.0  
**Versión de Minikube:** v1.38.0  
**Versión de kubectl:** v1.35.0  
**Envoy Gateway:** v1.5.1

---

**Recursos Desplegados:**

**Deployments:**
- ✅ synergychat-web (3 réplicas)
- ✅ synergychat-api (1 réplica)
- ✅ synergychat-crawler (1 réplica)

**Services:**
- ✅ web-service (ClusterIP)
- ✅ api-service (ClusterIP)
- ✅ crawler-service (ClusterIP)

**ConfigMaps:**
- ✅ synergychat-web-configmap
- ✅ synergychat-api-configmap
- ✅ synergychat-crawler-configmap

**Gateway Resources:**
- ✅ app-gatewayclass (Envoy)
- ✅ app-gateway (puerto 80)
- ✅ web-httproute (synchat.internal)
- ✅ api-httproute (synchatapi.internal)

**DNS Local:**
- ✅ synchat.internal → 127.0.0.1
- ✅ synchatapi.internal → 127.0.0.1

**Acceso:**
- 🌐 Web: http://synchat.internal
- 🌐 API: http://synchatapi.internal
---

## 7. Namespaces

### 7.1 Introducción a Namespaces

Los __Namespaces__ son una forma de aislar recursos del cluster en grupos. Son un poco como directorios en tu computadora, pero en lugar de contener archivos, contienen objetos de Kubernetes.

**Concepto clave:**
- Cada recurso en Kubernetes tiene un **nombre único**
- Solo puedes usar un nombre una vez **dentro del mismo namespace**
- Los namespaces permiten usar el mismo nombre para diferentes recursos, siempre que estén en diferentes namespaces

**Identificador único de un recurso:**
```
nombre + namespace = identificador único
```

**Ejemplo:**
- `synergychat-api` en namespace `default` ≠ `synergychat-api` en namespace `production`
- Son dos recursos diferentes

### Namespaces por defecto en Kubernetes

Cuando instalas Kubernetes, se crean varios namespaces automáticamente:

```bash
kubectl get namespaces
# o versión corta:
kubectl get ns
```

**Namespaces del sistema:**

| Namespace | Descripción | ¿Tocar? |
|-----------|-------------|---------|
| `default` | Namespace por defecto para recursos del usuario | ✅ Sí |
| `kube-system` | Componentes core de Kubernetes (DNS, API, etc.) | ❌ No tocar |
| `kube-public` | Recursos públicos accesibles por todos | ⚠️ Raramente |
| `kube-node-lease` | Información de heartbeat de nodos | ❌ No tocar |

**Otros namespaces comunes:**
- `envoy-gateway-system` - Sistema de Gateway API (Envoy)
- `kubernetes-dashboard` - Dashboard web de Kubernetes

**Comportamiento por defecto:**
- Si NO especificas un namespace, los recursos se crean en `default`
- Hasta ahora, todos tus recursos (api, web, crawler) han estado en `default`

---

### 7.2 Creando y Usando Namespaces

#### Crear un namespace

```bash
# Crear namespace
kubectl create ns <nombre>

# Ejemplo:
kubectl create ns crawler
```

#### Trabajar con recursos en un namespace específico

**Ver recursos en un namespace:**
```bash
kubectl -n <namespace> get pods
kubectl -n <namespace> get svc
kubectl -n <namespace> get configmaps

# Ejemplos:
kubectl -n crawler get pods
kubectl -n kube-system get pods
```

**Sin especificar `-n`:**
```bash
kubectl get pods  # Busca en 'default'
```

#### Agregar namespace a recursos YAML

Para que un recurso viva en un namespace específico, agregar `namespace` en `metadata`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-crawler-config
  namespace: crawler              # ← Especifica el namespace
data:
  CRAWLER_KEYWORDS: "fantasy,science,fiction,magic,space,adventure"
  # ... resto de datos
```

**Aplicar el recurso:**
```bash
kubectl apply -f crawler-configmap.yaml
```

Si el namespace en el YAML es diferente al namespace donde existe actualmente el recurso, Kubernetes lo **crea** (no actualiza):

```bash
# Recurso existe en 'default'
kubectl apply -f crawler-configmap.yaml  # Con namespace: crawler
# Output: configmap/synergychat-crawler-config created  ← "created" no "configured"
```

---

### 7.3 Mover Recursos a un Nuevo Namespace

#### Ejemplo: Mover crawler a su propio namespace

**Paso 1: Crear el namespace**
```bash
kubectl create ns crawler
```

**Paso 2: Agregar `namespace: crawler` a todos los recursos del crawler**

```yaml
# crawler-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-crawler-config
  namespace: crawler              # ← Agregar
data:
  # ... datos
```

```yaml
# crawler-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-crawler
  namespace: crawler              # ← Agregar
spec:
  # ... resto del spec
```

```yaml
# crawler-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: crawler-service
  namespace: crawler              # ← Agregar
spec:
  # ... resto del spec
```

**Paso 3: Aplicar los recursos**
```bash
kubectl apply -f crawler-configmap.yaml
kubectl apply -f crawler-deployment.yaml
kubectl apply -f crawler-service.yaml
```

**Paso 4: Verificar que están en el nuevo namespace**
```bash
kubectl -n crawler get pods
kubectl -n crawler get svc
kubectl -n crawler get configmaps
```

**Paso 5: Eliminar los recursos viejos del namespace default**
```bash
kubectl delete deployment synergychat-crawler
kubectl delete service crawler-service
kubectl delete configmap synergychat-crawler-config
```

**Paso 6: Verificar que ya no están en default**
```bash
kubectl get pods        # No debería aparecer crawler
kubectl get svc         # No debería aparecer crawler-service
kubectl get configmaps  # No debería aparecer crawler-config
```

---

### 7.4 DNS Intra-Cluster

Kubernetes crea automáticamente **entradas DNS** para cada servicio que pueden usarse para enrutar tráfico HTTP entre servicios dentro del cluster.

#### Formato DNS interno

**Formato completo:**
```
<service-name>.<namespace>.svc.cluster.local
```

**Variantes válidas:**
```
# Completo (siempre funciona)
http://crawler-service.crawler.svc.cluster.local

# Sin .svc.cluster.local (funciona en la mayoría de casos)
http://crawler-service.crawler

# Solo nombre (solo si están en el MISMO namespace)
http://crawler-service
```

#### Ejemplo: API conectándose al Crawler

**Escenario:**
- `api` en namespace `default`
- `crawler` en namespace `crawler`
- `api` necesita hacer HTTP requests al `crawler`

**Solución: Usar DNS interno**

```yaml
# api-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-api-configmap
data:
  API_PORT: "8080"
  API_DB_FILEPATH: "/persist/db.json"
  CRAWLER_BASE_URL: "http://crawler-service.crawler.svc.cluster.local:80"
```

**Desglose de la URL:**
```
http://crawler-service.crawler.svc.cluster.local:80
     ↓                ↓           ↓         ↓       ↓
  Nombre del      Namespace    Servicio  Dominio Puerto
  servicio                     interno   cluster
```

- `crawler-service` → Nombre del Service
- `crawler` → Namespace donde vive el servicio
- `svc.cluster.local` → Dominio interno de Kubernetes (estándar)
- `80` → Puerto del servicio

**Aplicar el ConfigMap:**
```bash
kubectl apply -f api-configmap.yaml
```

**Reiniciar el pod del API para cargar la nueva variable:**
```bash
kubectl delete pod <api-pod-name>
```

#### Flujo de comunicación DNS

```
┌─────────────────────────────────────────────────┐
│           NAMESPACE: default                    │
│                                                 │
│  ┌──────────────┐                              │
│  │   API Pod    │                              │
│  │              │                              │
│  │ Hace request:│                              │
│  │ GET http://crawler-service.crawler...      │
│  └──────┬───────┘                              │
│         │                                       │
└─────────┼───────────────────────────────────────┘
          │
          │ DNS resuelve a la IP del servicio
          │
┌─────────▼───────────────────────────────────────┐
│           NAMESPACE: crawler                    │
│                                                 │
│  ┌──────────────────┐                          │
│  │ crawler-service  │ (ClusterIP)              │
│  │ IP: 10.96.x.x    │                          │
│  └────────┬─────────┘                          │
│           │                                     │
│           ├─► Pod crawler (C1, C2, C3)         │
│           │                                     │
└───────────┴─────────────────────────────────────┘
```

**Proceso:**
1. API hace request a `http://crawler-service.crawler.svc.cluster.local:80`
2. DNS de Kubernetes resuelve `crawler-service.crawler` a la IP del servicio
3. El servicio enruta la petición a uno de los pods del crawler
4. El crawler responde
5. API recibe la respuesta

#### Debugging de conectividad DNS

**Verificar que la variable esté configurada:**
```bash
kubectl exec <api-pod> -- env | grep CRAWLER_BASE_URL
```

**Probar conectividad desde el pod del API:**
```bash
# Entrar al pod
kubectl exec -it <api-pod> -- sh

# Dentro del pod, resolver DNS
nslookup crawler-service.crawler.svc.cluster.local

# Probar conexión HTTP
wget -O- http://crawler-service.crawler.svc.cluster.local:80
# o
curl http://crawler-service.crawler.svc.cluster.local:80

# Salir
exit
```

---

### 7.5 Comunicación Interna vs Externa

#### Arquitectura de SynergyChat

```
MUNDO EXTERIOR (Internet)
        │
        │ Usuario visita http://synchat.internal
        ▼
┌────────────────────────────────────────────────┐
│            GATEWAY (Punto de entrada)          │
└────────────────┬───────────────────────────────┘
                 │
        ┌────────┴────────┐
        │                 │
        ▼                 ▼
┌─────────────┐    ┌─────────────┐
│  WEB (UI)   │    │  API (JSON) │ ← ÚNICA API expuesta
│ (Frontend)  │    │             │
└─────────────┘    └──────┬──────┘
                          │
                          │ Comunicación INTERNA
                          │ (DNS interno, no sale a Internet)
                          ▼
                   ┌─────────────┐
                   │  CRAWLER    │ ← NO expuesto al exterior
                   │ (Backend)   │    Solo accesible internamente
                   └─────────────┘
```

#### Servicios Expuestos vs Internos

**Servicios EXPUESTOS al exterior:**
- Tienen un HTTPRoute conectado al Gateway
- Tienen un dominio público (ej: `synchatapi.internal`)
- Accesibles desde Internet

```yaml
# Ejemplo: api-httproute.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: api-httproute
spec:
  parentRefs:
    - name: app-gateway        # ← Conectado al Gateway
  hostnames:
    - "synchatapi.internal"    # ← Dominio PÚBLICO
  rules:
    - backendRefs:
        - name: api-service
```

**Resultado:** `Internet → Gateway → API` ✅

**Servicios INTERNOS (no expuestos):**
- NO tienen HTTPRoute al Gateway
- NO tienen dominio público
- Solo accesibles dentro del cluster

**Resultado:** `Internet → Gateway → Crawler` ❌

Pero dentro del cluster:
```
API → crawler-service.crawler.svc.cluster.local ✅
```

#### Ventajas de la Comunicación Interna

**1. Velocidad**
- Comunicación dentro de la red interna del cluster
- Latencia: 1-5 ms
- No sale del datacenter

vs.

- Comunicación externa: 50-200 ms
- Sale y entra del datacenter

**2. Seguridad**
- No expone servicios internos a Internet
- Reduce superficie de ataque
- No necesita HTTPS (la red interna ya es segura)

**3. Simplicidad**
- No requiere DNS público para cada servicio
- No requiere configurar HTTPRoutes para cada servicio
- Un solo punto de entrada (Gateway/API)

#### Flujo de comunicación: Comando /stats

**Usuario escribe `/stats`:**

```
1. Usuario → WEB (http://synchat.internal)
   ↓
2. WEB → API (http://synchatapi.internal) - EXTERNA
   ↓
3. API → Crawler (http://crawler-service.crawler) - INTERNA
   ↓
4. Crawler responde a API
   ↓
5. API responde a WEB
   ↓
6. WEB muestra respuesta al usuario
```

**Puntos clave:**
- El usuario NUNCA habla directamente con el Crawler
- El usuario SOLO habla con WEB y API (expuestos)
- API internamente coordina con Crawler (interno)
- Crawler está protegido detrás del API

---

### 7.6 Resumen de Namespaces

#### Conceptos clave

| Concepto | Descripción |
|----------|-------------|
| **Namespace** | Agrupación lógica de recursos en Kubernetes |
| **Identificador único** | `nombre + namespace` |
| **default** | Namespace por defecto si no se especifica otro |
| **kube-system** | Namespace del sistema (no tocar) |
| **DNS interno** | `<service>.<namespace>.svc.cluster.local` |
| **Comunicación interna** | Más rápida, segura y simple que externa |

#### Cuándo usar namespaces

**Usar namespaces separados cuando:**
- Equipos diferentes trabajan en diferentes servicios
- Entornos diferentes (dev, staging, production)
- Necesitas aislamiento de recursos
- Quieres organizar un cluster grande

**Un solo namespace (default) es suficiente cuando:**
- Cluster pequeño
- Pocos servicios
- Un solo equipo

#### Comandos clave

```bash
# Ver y crear namespaces
kubectl get namespaces
kubectl get ns                        # Versión corta
kubectl create ns <nombre>

# Trabajar con recursos en namespaces
kubectl -n <namespace> get pods
kubectl -n <namespace> get svc
kubectl -n <namespace> get configmaps
kubectl -n <namespace> describe pod <pod-name>
kubectl -n <namespace> logs <pod-name>

# Eliminar recursos en namespace
kubectl -n <namespace> delete pod <pod-name>

# Debugging
kubectl exec <pod> -- env | grep VARIABLE
kubectl exec -it <pod> -- sh
nslookup <service>.<namespace>.svc.cluster.local
```

#### Archivos YAML modificados

**Para mover recursos a un namespace:**

```yaml
metadata:
  name: <nombre-recurso>
  namespace: <nombre-namespace>  # ← Agregar esta línea
```

**Archivos modificados en esta sección:**
- `crawler-configmap.yaml` - agregamos `namespace: crawler`
- `crawler-deployment.yaml` - agregamos `namespace: crawler`
- `crawler-service.yaml` - agregamos `namespace: crawler`
- `api-configmap.yaml` - agregamos `CRAWLER_BASE_URL`

#### Patrones de comunicación

**Mismo namespace:**
```yaml
CRAWLER_BASE_URL: "http://crawler-service"
```

**Diferentes namespaces:**
```yaml
CRAWLER_BASE_URL: "http://crawler-service.crawler"
# o completo:
CRAWLER_BASE_URL: "http://crawler-service.crawler.svc.cluster.local"
```

**Con puerto específico:**
```yaml
CRAWLER_BASE_URL: "http://crawler-service.crawler.svc.cluster.local:80"
```

#### Arquitectura recomendada

**Para producción:**
```
Gateway (único punto de entrada)
    ↓
Servicios públicos (API, WEB) - Expuestos
    ↓
Servicios internos (Crawler, DB, etc.) - NO expuestos
```

**Ventajas:**
- ✅ Seguridad (menos superficie de ataque)
- ✅ Simplicidad (menos configuración)
- ✅ Performance (comunicación interna rápida)

---

## 8. Observability & Resource Management

### 8.1 Métricas con kubectl top

Para poder monitorear el uso de recursos (CPU y memoria) de los pods, necesitamos habilitar el addon `metrics-server` en Minikube.

#### Habilitar metrics-server

```bash
minikube addons enable metrics-server
```

Este addon despliega un pod en el namespace `kube-system` que recolecta métricas de todos los nodos y pods del cluster.

#### Verificar el metrics-server

```bash
kubectl -n kube-system get pod
```

Deberías ver un pod llamado `metrics-server-xxxxx`. Espera a que esté en estado `Running`.

#### Comando kubectl top

Similar al comando `top` de Unix/Linux, `kubectl top` muestra el uso de recursos en tiempo real.

**Ver métricas de pods:**
```bash
kubectl top pod
```

**Salida esperada:**
```
NAME                               CPU(cores)   MEMORY(bytes)
synergychat-api-76b796b58d-x5wpk   1m           14Mi
synergychat-web-846d86c444-d9c8q   1m           15Mi
synergychat-web-846d86c444-sk6n4   1m           15Mi
synergychat-web-846d86c444-w2pqg   1m           15Mi
```

**Ver métricas de nodos:**
```bash
kubectl top node
```

**Interpretación:**
- `1m` = 1 milli-core = 0.001 cores = 0.1% de un CPU core
- `14Mi` = 14 mebibytes = ~14.7 megabytes

**Nota:** Puede tomar 1-2 minutos después de habilitar metrics-server para que las métricas estén disponibles.

---

### 8.2 Escalado Vertical vs Horizontal

Hay dos formas principales de escalar una aplicación en Kubernetes:

#### Escalado Vertical (Scale Up)

**Definición:** Aumentar los recursos (CPU, RAM) de cada nodo o pod individual.

**Ejemplo:**
```
Servidor original:
- CPU: 2 cores
- RAM: 4 GB
- Capacidad: 1000 req/s

Después de escalar verticalmente:
- CPU: 8 cores     ← Más recursos
- RAM: 16 GB       ← Más recursos
- Capacidad: 4000 req/s
```

**Analogía:** Contratar a un trabajador MÁS CAPACITADO en lugar de contratar más trabajadores.

**Ventajas:**
- Simplicidad (menos instancias que gestionar)
- Menor latencia de red entre componentes

**Desventajas:**
- ❌ Límite físico del hardware (no puedes crecer infinitamente)
- ❌ Punto único de falla (si el nodo muere, todo se cae)
- ❌ Más costoso (hardware de gama alta es exponencialmente más caro)

#### Escalado Horizontal (Scale Out)

**Definición:** Aumentar el número de nodos o pods (réplicas).

**Ejemplo:**
```
Configuración original:
2 servidores × (2 cores, 4GB) = 2000 req/s

Después de escalar horizontalmente:
4 servidores × (2 cores, 4GB) = 4000 req/s
```

**Analogía:** Contratar MÁS trabajadores en lugar de capacitar más a los existentes.

**Ventajas:**
- ✅ Alta disponibilidad (si un nodo falla, otros continúan)
- ✅ Distribución geográfica (nodos en diferentes datacenters)
- ✅ Sin límite teórico (puedes agregar nodos indefinidamente)
- ✅ Más económico (hardware commodity es más barato)

**Desventajas:**
- Más complejo de gestionar
- Requiere arquitectura distribuida

#### Comparación Visual

**Vertical:**
```
1 POD                          1 POD (mejorado)
┌─────────────┐               ┌─────────────┐
│ CPU: 0.5    │               │ CPU: 2.0    │
│ RAM: 1 GB   │  ──────►      │ RAM: 4 GB   │
└─────────────┘               └─────────────┘
```

**Horizontal:**
```
1 POD                         3 PODS
┌─────────────┐               ┌─────────┐ ┌─────────┐ ┌─────────┐
│ CPU: 0.5    │               │ 0.5 CPU │ │ 0.5 CPU │ │ 0.5 CPU │
│ RAM: 1 GB   │  ──────►      │ 1 GB    │ │ 1 GB    │ │ 1 GB    │
└─────────────┘               └─────────┘ └─────────┘ └─────────┘
```

#### Escalado en Kubernetes

**A nivel de POD:**
```yaml
# Vertical: aumentar recursos del pod
resources:
  limits:
    cpu: "2"      # Era 0.5
    memory: "4Gi" # Era 1Gi

# Horizontal: aumentar réplicas
replicas: 5  # Era 1
```

**A nivel de NODO:**
```bash
# Vertical: comprar nodos más grandes
# Antes: 3 nodos × (4 cores, 16GB)
# Después: 3 nodos × (16 cores, 64GB)

# Horizontal: agregar más nodos
# Antes: 3 nodos × (4 cores, 16GB)
# Después: 6 nodos × (4 cores, 16GB)
```

#### Regla general en Kubernetes

**En Kubernetes, es generalmente mejor escalar horizontalmente.**

**Razones:**
- Kubernetes está diseñado para gestionar múltiples réplicas
- Alta disponibilidad automática
- Load balancing integrado
- Más alineado con arquitecturas cloud-native

---

### 8.3 Resource Limits - CPU

En un entorno de producción, es fundamental establecer límites de recursos para evitar que un pod acapare todos los recursos de un nodo y "sofoque" a los demás pods.

#### ¿Por qué establecer límites?

**Sin límites:**
```
┌───────────────────────────────┐
│   Nodo (8 cores totales)      │
│                               │
│  Pod A: 🔥🔥🔥🔥🔥🔥🔥 (7 cores) │ ← Acapara todo
│  Pod B: 💤 (sin recursos)    │ ← Sofocado
│  Pod C: 💤 (sin recursos)    │ ← Sofocado
└───────────────────────────────┘
```

**Con límites:**
```
┌───────────────────────────────┐
│   Nodo (8 cores totales)      │
│                               │
│  Pod A: 🔥 (1 core límite)   │ ← Limitado
│  Pod B: ✅ (1 core)          │ ← Funciona
│  Pod C: ✅ (1 core)          │ ← Funciona
│  Libre: 5 cores              │
└───────────────────────────────┘
```

#### Sintaxis de límites de CPU

```yaml
spec:
  containers:
    - name: my-container
      image: my-image
      resources:
        limits:
          cpu: "<max-cpu>"
```

**Unidades de CPU:**

| Valor | Significado | Equivalente |
|-------|-------------|-------------|
| `1` | 1 core completo | 1000m |
| `500m` | Medio core | 0.5 cores |
| `100m` | Un décimo de core | 0.1 cores |
| `50m` | 5% de un core | 0.05 cores |
| `1m` | 0.1% de un core | 0.001 cores |

**`m` = milli-cores**

#### Ejemplo práctico: testcpu

**Aplicación de prueba:**
- Imagen: `bootdotdev/synergychat-testcpu:latest`
- Comportamiento: Consume toda la CPU disponible (loop infinito)

**Deployment con límite de 50m:**

```yaml
# testcpu-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: synergychat-testcpu
  name: synergychat-testcpu
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-testcpu
  template:
    metadata:
      labels:
        app: synergychat-testcpu
    spec:
      containers:
        - image: bootdotdev/synergychat-testcpu:latest
          name: synergychat-testcpu
          resources:
            limits:
              cpu: "50m"
```

**Aplicar y verificar:**
```bash
kubectl apply -f testcpu-deployment.yaml
kubectl get pods
kubectl top pod
```

**Resultado esperado:**
```
NAME                                  CPU(cores)   MEMORY(bytes)
synergychat-testcpu-xxxxxxxxx-xxxxx   50m          10Mi
```

El pod está usando exactamente **50m** (o muy cerca) porque Kubernetes está **limitando (throttling)** el pod.

#### Throttling de CPU

**Comportamiento:**
- El pod intenta usar TODA la CPU disponible
- Kubernetes limita la velocidad a 50m
- El pod continúa funcionando pero MÁS LENTO
- NO se mata el pod, solo se reduce su velocidad

**Flujo:**
```
Pod: "Quiero usar 8 cores"
    ↓
Kubernetes: "Solo puedes usar 50m (0.05 cores)"
    ↓
Pod: Funciona a velocidad reducida ⏱️
```

---

### 8.4 Resource Limits - Memory

Los límites de memoria funcionan de manera diferente a los de CPU.

#### Diferencia clave: CPU vs Memory

| Aspecto | CPU | Memory |
|---------|-----|--------|
| **Si excede límite** | Throttling (más lento) | OOMKilled (pod muere) |
| **Pod sigue vivo** | ✅ Sí | ❌ No (se reinicia) |
| **Comportamiento** | Limita velocidad | Termina el proceso |

#### Sintaxis de límites de memoria

```yaml
spec:
  containers:
    - name: my-container
      image: my-image
      resources:
        limits:
          memory: "<max-memory>"
          cpu: "<max-cpu>"
```

**Unidades de memoria:**

| Valor | Significado | Notas |
|-------|-------------|-------|
| `128974848` | 128974848 bytes | Poco legible |
| `129M` | 129 megabytes (decimal) | Base 10 |
| `123Mi` | 123 mebibytes (binario) | **Recomendado** |
| `1Gi` | 1 gibibyte | **Recomendado** |
| `1G` | 1 gigabyte (decimal) | Base 10 |

**Diferencia Mi vs M:**
- `1Mi` = 1024 KiB = 1,048,576 bytes (binario)
- `1M` = 1000 KB = 1,000,000 bytes (decimal)

**Recomendación:** Usa `Mi` (mebibytes) y `Gi` (gibibytes) en Kubernetes.

#### Ejemplo práctico: testram

**Aplicación de prueba:**
- Imagen: `bootdotdev/synergychat-testram:latest`
- Comportamiento: Asigna X megabytes de memoria según variable de entorno
- Variable: `MEGABYTES` (configurable vía ConfigMap)

**ConfigMap:**

```yaml
# testram-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-testram-configmap
data:
  MEGABYTES: "200"
```

**Deployment con límite de 256Mi:**

```yaml
# testram-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: synergychat-testram
  name: synergychat-testram
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-testram
  template:
    metadata:
      labels:
        app: synergychat-testram
    spec:
      containers:
        - image: bootdotdev/synergychat-testram:latest
          name: synergychat-testram
          envFrom:
            - configMapRef:
                name: synergychat-testram-configmap
          resources:
            limits:
              memory: "256Mi"
```

**Aplicar y verificar:**
```bash
kubectl apply -f testram-configmap.yaml
kubectl apply -f testram-deployment.yaml
kubectl get pods
kubectl top pod
```

**Resultado esperado:**
```
NAME                                  CPU(cores)   MEMORY(bytes)
synergychat-testram-xxxxxxxxx-xxxxx   1m           205Mi
```

El pod está usando aproximadamente **200-210Mi** de memoria (según configuración en ConfigMap).

#### OOMKilled - Out Of Memory Killed

**¿Qué pasa si el pod excede el límite de memoria?**

Si cambias `MEGABYTES: "300"` (excede el límite de 256Mi):

```bash
kubectl apply -f testram-configmap.yaml
kubectl delete pod <testram-pod-name>
kubectl get pods
```

Verás:
```
NAME                                  READY   STATUS      RESTARTS   AGE
synergychat-testram-xxxxxxxxx-xxxxx   0/1     OOMKilled   1          1m
```

**OOMKilled** = Out Of Memory Killed

**Flujo:**
```
Pod intenta asignar 300Mi
    ↓
Límite: 256Mi
    ↓
300Mi > 256Mi
    ↓
Kubernetes: "Límite excedido"
    ↓
Pod MUERE 💀
    ↓
Kubernetes lo REINICIA automáticamente
    ↓
Ciclo se repite (CrashLoopBackOff)
```

**Ver detalles:**
```bash
kubectl describe pod <testram-pod-name>
```

En `Last State` verás:
```
Reason: OOMKilled
Exit Code: 137
```

#### ¿Por qué testram necesita ConfigMap?

**testcpu:**
- Simplemente consume CPU al máximo (loop infinito)
- No necesita configuración
- Kubernetes la limita con throttling

**testram:**
- Necesita saber CUÁNTA memoria asignar
- Lee la variable `MEGABYTES` del ConfigMap
- Asigna exactamente esa cantidad

**Pseudo-código:**
```python
# testcpu
while True:
    do_computation()  # Consume CPU

# testram
megabytes = int(os.getenv("MEGABYTES"))
data = allocate_memory(megabytes)  # Necesita saber cuánto
```

---

### 8.5 Resumen de Observability & Resource Management

#### Comandos clave

```bash
# Habilitar métricas
minikube addons enable metrics-server

# Ver métricas
kubectl top pod
kubectl top node
kubectl top pod -n <namespace>

# Ver recursos de pods
kubectl get pods
kubectl describe pod <pod-name>

# Aplicar recursos de prueba
kubectl apply -f testcpu-deployment.yaml
kubectl apply -f testram-configmap.yaml
kubectl apply -f testram-deployment.yaml

# Limpiar recursos de prueba
kubectl delete deployment synergychat-testcpu
kubectl delete deployment synergychat-testram
kubectl delete configmap synergychat-testram-configmap
```

#### Conceptos clave

| Concepto | Descripción |
|----------|-------------|
| **Escalado Vertical** | Aumentar recursos por nodo/pod (scale up) |
| **Escalado Horizontal** | Aumentar número de nodos/pods (scale out) |
| **Resource Limits** | Límites máximos de CPU y memoria |
| **Throttling** | Limitar velocidad de CPU (pod sigue vivo) |
| **OOMKilled** | Matar pod que excede límite de memoria |
| **Milli-core (m)** | 1/1000 de un CPU core |
| **Mebibyte (Mi)** | 1024 KiB (binario, recomendado) |

#### Patrones de límites

**Límite solo de CPU:**
```yaml
resources:
  limits:
    cpu: "500m"
```

**Límite solo de memoria:**
```yaml
resources:
  limits:
    memory: "512Mi"
```

**Ambos límites (recomendado en producción):**
```yaml
resources:
  limits:
    cpu: "500m"
    memory: "512Mi"
```

#### Comportamiento de límites

**CPU:**
- Excede límite → Throttling (funciona más lento)
- Pod sigue vivo
- No se reinicia

**Memory:**
- Excede límite → OOMKilled (pod muere)
- Pod se reinicia automáticamente
- Puede entrar en CrashLoopBackOff

#### Archivos creados en esta sección

**Creados:**
- `testcpu-deployment.yaml` - Pod de prueba de CPU con límite 50m
- `testram-configmap.yaml` - ConfigMap con MEGABYTES: "200"
- `testram-deployment.yaml` - Pod de prueba de memoria con límite 256Mi

#### Best Practices

**En producción:**
1. ✅ Siempre establecer resource limits
2. ✅ Monitorear con `kubectl top` regularmente
3. ✅ Escalar horizontalmente cuando sea posible
4. ✅ Usar `Mi` y `Gi` para memoria (no `M` o `G`)
5. ✅ Establecer límites realistas basados en métricas reales
6. ⚠️ No establecer límites demasiado bajos (causa throttling/OOMKilled)
7. ⚠️ No dejar pods sin límites (pueden ahogar a otros pods)

#### Escalado recomendado en Kubernetes

**Para la mayoría de casos:**
- ✅ Escalado horizontal (más réplicas)
- ✅ Nodos de tamaño medio (no muy grandes)
- ✅ Alta disponibilidad automática

**Solo usar escalado vertical cuando:**
- Aplicación no puede distribuirse (legacy)
- Limitaciones de licenciamiento
- Requisitos de latencia extremadamente bajos

---

### 8.4 Breaking the Limits - Pruebas de Límites de Memoria

#### Concepto

**Diferencia entre CPU y Memory:**
- **CPU:** Las aplicaciones no "eligen" cuánta CPU usar - van tan rápido como pueden. Si se excede el límite, Kubernetes hace **throttling** (el pod va más lento pero sigue vivo)
- **Memory:** Las aplicaciones asignan memoria activamente. Si se excede el límite, el pod **crashea** (OOMKilled - Out Of Memory Killed)

**Esta subsección prueba el comportamiento de límites de memoria.**

#### Ejercicio: Exceder el límite de memoria

**Objetivo:** Configurar testram para que intente asignar más memoria de la permitida y observar el crash.

**Pasos realizados:**

1. **Actualizar ConfigMap de testram:**
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: testram-config
   data:
     MEGABYTES: "500"  # 500 MB
   ```

2. **Aplicar cambios:**
   ```bash
   kubectl apply -f testram-configmap.yaml
   kubectl delete pod <testram-pod-name>  # Para que tome la nueva variable
   ```

3. **Verificar el crash:**
   ```bash
   kubectl get pods
   # Output: STATUS: CrashLoopBackOff o OOMKilled
   
   kubectl describe pod <testram-pod-name>
   ```

**Salida esperada del describe:**
```
Containers:
  synergychat-testram:
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    137
```

#### ¿Qué está pasando?

**Análisis del crash:**
- Aplicación intenta asignar: **500 MB** (`MEGABYTES=500`)
- Límite del contenedor: **256 Mi** (en `testram-deployment.yaml`)
- **500 MB > 256 Mi** → Excede el límite
- Kubernetes mata el proceso: **OOMKilled** (Out Of Memory Killed)
- Exit Code: **137** = señal de terminación por OOM (128 + 9)
- Kubernetes intenta reiniciar automáticamente → **CrashLoopBackOff**

**CrashLoopBackOff:**
- Kubernetes detecta que el pod crashea repetidamente
- Aumenta el tiempo entre reintentos (backoff exponencial)
- Patrón: 0s → 10s → 20s → 40s → 80s → hasta 5 minutos máximo

#### Comandos clave

```bash
# Ver estado de pods
kubectl get pods

# Describir pod para ver motivo del crash
kubectl describe pod <testram-pod-name>

# Ver logs del pod (antes del crash)
kubectl logs <testram-pod-name>

# Ver eventos recientes
kubectl get events --sort-by='.lastTimestamp'
```

---

### 8.5 Fix the Limits - Arreglar los Límites

#### Concepto

Después de probar el crash por exceso de memoria, restauramos la aplicación testram a un estado saludable para no tener pods constantemente crasheando ni consumiendo recursos excesivos.

#### Ejercicio: Reducir el uso de memoria

**Objetivo:** Configurar testram para que use solo 10 MB de memoria (dentro del límite de 256 Mi).

**Pasos realizados:**

1. **Actualizar ConfigMap de testram:**
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: testram-config
   data:
     MEGABYTES: "10"  # Solo 10 MB
   ```

2. **Aplicar cambios:**
   ```bash
   kubectl apply -f testram-configmap.yaml
   kubectl delete pod <testram-pod-name>
   ```

3. **Verificar que el pod está saludable:**
   ```bash
   kubectl get pods
   # Output: STATUS: Running, READY: 1/1
   
   kubectl top pods
   # Output: testram usando ~10Mi de memoria
   ```

#### Comparación: Antes vs Después

**Antes (MEGABYTES=500):**
- Intenta asignar: 500 MB
- Límite: 256 Mi
- Resultado: **OOMKilled** ❌
- Estado: CrashLoopBackOff

**Ahora (MEGABYTES=10):**
- Asigna: 10 MB
- Límite: 256 Mi
- Resultado: **Running** ✅
- Estado: Saludable, sin reinicios

#### Verificación

```bash
# Ver estado del pod
kubectl get pods
# Esperado: Running, READY 1/1, RESTARTS 0

# Ver uso de recursos
kubectl top pods
# Esperado: testram usando ~10Mi de memoria

# Describir pod (opcional)
kubectl describe pod <testram-pod-name>
# Esperado: State: Running, Ready: True, Restart Count: 0
```

---

### 8.6 Horizontal Pod Autoscaling (HPA) - Escalado Automático

#### Concepto

**Horizontal Pod Autoscaler (HPA):**
- Escala automáticamente el **número de pods** en un Deployment basándose en métricas observadas
- Métrica principal: **Utilización de CPU** (también soporta métricas personalizadas)
- Objetivo: Mantener la utilización promedio de CPU en el valor objetivo configurado

**¿Cómo funciona?**
1. HPA monitorea cada 15 segundos las métricas de los pods
2. Calcula el promedio de utilización de CPU: `uso_actual / request * 100`
3. Compara con el objetivo (`targetCPUUtilizationPercentage`)
4. **Si promedio > objetivo:** Crea más pods (scale up)
5. **Si promedio < objetivo:** Elimina pods (scale down)

**Algoritmo básico:**
```
desired_replicas = ceil(current_replicas * (current_cpu / target_cpu))
```

#### Ejercicio 1: HPA para testcpu (alta CPU)

**Objetivo:** Configurar HPA para testcpu que consume mucha CPU, observar el escalado hacia arriba.

**Archivos creados/modificados:**

1. **testcpu-deployment.yaml** (modificado - sin replicas):
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     labels:
       app: synergychat-testcpu
     name: synergychat-testcpu
   spec:
     selector:
       matchLabels:
         app: synergychat-testcpu
     template:
       metadata:
         labels:
           app: synergychat-testcpu
       spec:
         containers:
           - image: bootdotdev/synergychat-testcpu:latest
             name: synergychat-testcpu
             resources:
               requests:
                 cpu: 100m
               limits:
                 cpu: 200m
   ```
   **Nota importante:** Se eliminó `replicas: 1` para que el HPA tenga control total.

2. **testcpu-hpa.yaml** (creado):
   ```yaml
   apiVersion: autoscaling/v1
   kind: HorizontalPodAutoscaler
   metadata:
     name: testcpu-hpa
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: synergychat-testcpu
     minReplicas: 1
     maxReplicas: 4
     targetCPUUtilizationPercentage: 50
   ```

**Pasos realizados:**

```bash
# 1. Editar deployment (eliminar replicas)
nano testcpu-deployment.yaml
kubectl apply -f testcpu-deployment.yaml

# 2. Crear y aplicar HPA
nano testcpu-hpa.yaml
kubectl apply -f testcpu-hpa.yaml

# 3. Monitorear el escalado
kubectl get hpa
kubectl get pods
kubectl top pods

# 4. Ver detalles del HPA
kubectl describe hpa testcpu-hpa
```

**Resultados observados:**

```bash
# HPA en acción
kubectl get hpa
NAME          REFERENCE                        TARGETS    MINPODS   MAXPODS   REPLICAS
testcpu-hpa   Deployment/synergychat-testcpu   100%/50%   1         4         4

# Pods escalando
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
synergychat-testcpu-xxxxxxxxxx-xxxxx   1/1     Running   0          5m
synergychat-testcpu-xxxxxxxxxx-yyyyy   1/1     Running   0          3m
synergychat-testcpu-xxxxxxxxxx-zzzzz   1/1     Running   0          3m
synergychat-testcpu-xxxxxxxxxx-wwwww   1/1     Running   0          2m
```

**¿Por qué escaló a 4 pods?**
- Cada pod usa ~50m de CPU (cerca del límite de 50m original o 200m después)
- Request: 100m
- Utilización: 50m / 100m = **50%** (o más si el límite es bajo)
- Como la utilización está en o por encima del 50% objetivo, el HPA crea más pods para distribuir la carga
- Escala hasta `maxReplicas: 4`

#### Ejercicio 2: HPA para web (baja CPU)

**Objetivo:** Configurar HPA para web que consume poca CPU, observar que mantiene el mínimo de pods.

**Archivos creados/modificados:**

1. **web-deployment.yaml** (modificado - agregar resources, sin replicas):
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: synergychat-web
     labels:
       app: synergychat-web
   spec:
     selector:
       matchLabels:
         app: synergychat-web
     template:
       metadata:
         labels:
           app: synergychat-web
       spec:
         containers:
         - name: synergychat-web
           image: docker.io/bootdotdev/synergychat-web:latest
           ports:
           - containerPort: 8080
           envFrom:
           - configMapRef:
               name: synergychat-web-configmap
           resources:
             requests:
               cpu: 10m
               memory: 20Mi
             limits:
               cpu: 50m
               memory: 100Mi
   ```
   **Cambios:** Agregado `resources` (critical para HPA), eliminado `replicas: 3`.

2. **web-hpa.yaml** (creado):
   ```yaml
   apiVersion: autoscaling/v1
   kind: HorizontalPodAutoscaler
   metadata:
     name: web-hpa
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: synergychat-web
     minReplicas: 1
     maxReplicas: 4
     targetCPUUtilizationPercentage: 50
   ```

**Pasos realizados:**

```bash
# 1. Editar deployment (agregar resources, eliminar replicas)
nano web-deployment.yaml
kubectl apply -f web-deployment.yaml

# 2. Crear HPA (copiar desde testcpu-hpa.yaml)
cp testcpu-hpa.yaml web-hpa.yaml
nano web-hpa.yaml  # Cambiar nombre y deployment target
kubectl apply -f web-hpa.yaml

# 3. Esperar 1-2 minutos para métricas
kubectl get hpa

# 4. Esperar ~5 minutos para escalado hacia abajo
kubectl get pods
kubectl top pods
```

**Resultados observados:**

```bash
# HPA después de 2 minutos
kubectl get hpa
NAME          REFERENCE                        TARGETS    MINPODS   MAXPODS   REPLICAS
web-hpa       Deployment/synergychat-web       10%/50%    1         4         3

# Después de 5 minutos (scale down completo)
kubectl get hpa
NAME          REFERENCE                        TARGETS    MINPODS   MAXPODS   REPLICAS
web-hpa       Deployment/synergychat-web       10%/50%    1         4         1

# Un solo pod de web
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
synergychat-web-xxxxxxxxxx-xxxxx       1/1     Running   0          5m
```

**¿Por qué solo 1 pod?**
- Cada pod de web usa ~1m de CPU
- Request: 10m
- Utilización: 1m / 10m = **10%**
- Como 10% < 50% objetivo, el HPA reduce pods al mínimo: `minReplicas: 1`

#### Ejercicio 3: HPA Fix - Reducir consumo de testcpu

**Objetivo:** Reducir el consumo de recursos de testcpu para no saturar la máquina.

**Archivos modificados:**

1. **testcpu-deployment.yaml** (actualizado con límites bajos):
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     labels:
       app: synergychat-testcpu
     name: synergychat-testcpu
   spec:
     selector:
       matchLabels:
         app: synergychat-testcpu
     template:
       metadata:
         labels:
           app: synergychat-testcpu
       spec:
         containers:
           - image: bootdotdev/synergychat-testcpu:latest
             name: synergychat-testcpu
             resources:
               requests:
                 cpu: 10m
               limits:
                 cpu: 10m
   ```
   **Cambios:** `limits.cpu: 10m`, `requests.cpu: 10m`

2. **testcpu-hpa.yaml** (actualizado con maxReplicas: 1):
   ```yaml
   apiVersion: autoscaling/v1
   kind: HorizontalPodAutoscaler
   metadata:
     name: testcpu-hpa
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: synergychat-testcpu
     minReplicas: 1
     maxReplicas: 1
     targetCPUUtilizationPercentage: 50
   ```
   **Cambio:** `maxReplicas: 1` (forzar solo 1 pod)

**Pasos realizados:**

```bash
# 1. Actualizar deployment
nano testcpu-deployment.yaml
kubectl apply -f testcpu-deployment.yaml

# 2. Actualizar HPA
nano testcpu-hpa.yaml
kubectl apply -f testcpu-hpa.yaml

# 3. Observar el scale down (rápido porque maxReplicas=1)
kubectl get pods
# Los pods extras pasan a Terminating inmediatamente

kubectl get hpa
# REPLICAS baja de 4 a 1 rápidamente
```

**Resultados:**

```bash
# Antes
kubectl get hpa
testcpu-hpa   Deployment/synergychat-testcpu   100%/50%   1   4   4

kubectl top pods
testcpu-xxxxx   50m   6Mi
testcpu-yyyyy   50m   6Mi
testcpu-zzzzz   50m   6Mi
testcpu-wwwww   50m   6Mi
# Total CPU: ~200m

# Después
kubectl get hpa
testcpu-hpa   Deployment/synergychat-testcpu   10%/50%   1   1   1

kubectl get pods
testcpu-xxxxx   1/1   Running   0   1m
# Total CPU: ~10m

# Reducción: De ~200m a ~10m = 95% menos CPU
```

**¿Por qué el scale down fue rápido?**
- Normalmente el HPA tarda ~5 minutos en escalar hacia abajo (para evitar "flapping")
- **PERO** con `maxReplicas: 1`, el HPA está **forzado** a mantener máximo 1 pod
- Viola la restricción con 4 pods → elimina pods extras **inmediatamente**
- No espera el período de cooldown normal

#### Requisito crítico para HPA

**El HPA NECESITA `resources.requests.cpu` en el Deployment:**

```yaml
resources:
  requests:
    cpu: 10m  # REQUERIDO para HPA
  limits:
    cpu: 50m
```

**Sin `requests.cpu`:**
```bash
kubectl get hpa
NAME      REFERENCE         TARGETS              MINPODS   MAXPODS   REPLICAS
web-hpa   Deployment/web    cpu: <unknown>/50%   1         4         0
```

**Con `requests.cpu`:**
```bash
kubectl get hpa
NAME      REFERENCE         TARGETS    MINPODS   MAXPODS   REPLICAS
web-hpa   Deployment/web    10%/50%    1         4         1
```

**¿Por qué?**
- HPA calcula utilización: `uso_actual / request * 100`
- Sin `request` → no puede calcular el porcentaje → `<unknown>`

#### Regla de Kubernetes: Requests ≤ Limits

**Válido:**
```yaml
requests:
  cpu: 10m
limits:
  cpu: 10m    # 10m <= 10m ✅

requests:
  cpu: 100m
limits:
  cpu: 200m   # 100m <= 200m ✅
```

**Inválido:**
```yaml
requests:
  cpu: 100m
limits:
  cpu: 10m    # 100m > 10m ❌

# Error: must be less than or equal to cpu limit
```

#### Tiempos de escalado del HPA

**Scale Up (crear pods):**
- Tiempo: ~30 segundos - 1 minuto
- Razón: Responder rápido a aumento de carga

**Scale Down (eliminar pods):**
- Tiempo: ~5 minutos
- Razón: Evitar "flapping" (escalado constante arriba/abajo)
- Excepción: Violación de `maxReplicas` → inmediato

**Frecuencia de verificación:**
- HPA revisa métricas cada **15 segundos**

#### Archivos creados en esta subsección

**Creados:**
- `testcpu-hpa.yaml` - HPA para testcpu (min: 1, max: 4 inicialmente, luego max: 1)
- `web-hpa.yaml` - HPA para web (min: 1, max: 4)

**Modificados:**
- `testcpu-deployment.yaml` - Eliminado replicas, agregado/ajustado resources
- `web-deployment.yaml` - Eliminado replicas, agregado resources
- `testram-configmap.yaml` - MEGABYTES: 500 → 10

#### Comandos clave de HPA

```bash
# Ver todos los HPAs
kubectl get hpa

# Ver HPA específico con métricas actualizadas
kubectl get hpa <hpa-name>

# Describir HPA (ver eventos de escalado)
kubectl describe hpa <hpa-name>

# Monitoreo continuo (actualiza cada 2 segundos)
watch kubectl get hpa
watch kubectl get pods
watch kubectl top pods

# Eliminar HPA
kubectl delete hpa <hpa-name>
```

#### Conceptos clave aprendidos

**HPA vs Réplicas fijas:**
- **Réplicas fijas:** `replicas: 3` en Deployment → siempre 3 pods
- **HPA:** Número de pods **dinámico** basado en métricas → entre minReplicas y maxReplicas

**Alta CPU → Scale Up:**
- testcpu: 100% CPU → HPA crea más pods → distribuye la carga

**Baja CPU → Scale Down:**
- web: 10% CPU → HPA reduce a minReplicas → ahorra recursos

**Configuración óptima:**
- Establecer `minReplicas` según carga mínima esperada
- Establecer `maxReplicas` según capacidad del clúster
- Ajustar `targetCPUUtilizationPercentage` según tipo de aplicación:
  - **50-70%**: Aplicaciones con carga variable (recomendado)
  - **30-50%**: Aplicaciones críticas (más margen)
  - **70-90%**: Aplicaciones batch (maximizar recursos)

#### Best Practices para HPA

**Configuración:**
1. ✅ Siempre eliminar `replicas` del Deployment (dejar control total al HPA)
2. ✅ Establecer `requests.cpu` en el Deployment (requerido)
3. ✅ Configurar `minReplicas >= 1` (alta disponibilidad)
4. ✅ Establecer `maxReplicas` basado en capacidad del clúster
5. ✅ Target 50-70% para la mayoría de aplicaciones

**Monitoreo:**
1. ✅ Usar `kubectl top pods` para verificar uso real
2. ✅ Revisar eventos del HPA: `kubectl describe hpa`
3. ✅ Monitorear frecuencia de scaling (evitar flapping)

**Evitar:**
1. ❌ No establecer `minReplicas: 0` (aplicación no disponible si no hay tráfico)
2. ❌ No establecer target muy bajo (<30%) - desperdicio de recursos
3. ❌ No establecer target muy alto (>90%) - riesgo de saturación
4. ❌ No olvidar `requests.cpu` - HPA no funcionará

---

## Resumen de la Sección 8: Observability & Resource Management

### Temas Cubiertos

1. **Metrics Server** - Instalación y verificación del sistema de métricas
2. **Resource Requests** - Reserva garantizada de recursos (CPU y memoria)
3. **Resource Limits - CPU** - Límites máximos de CPU (throttling)
4. **Resource Limits - Memory** - Límites máximos de memoria (OOMKilled)
5. **Breaking the Limits** - Pruebas de exceso de límites de memoria
6. **Fix the Limits** - Restauración a configuración saludable
7. **Horizontal Pod Autoscaling (HPA)** - Escalado automático basado en métricas

### Puntos Clave

#### Metrics Server
- Sistema de monitoreo de recursos en Kubernetes
- Requerido para `kubectl top` y HPA
- Comando: `kubectl top pods`, `kubectl top nodes`

#### Resource Requests
- **Garantía mínima** de recursos reservados
- Scheduler usa requests para decidir dónde ubicar pods
- No evita que el pod use más (si está disponible)

#### Resource Limits
- **Máximo absoluto** de recursos que puede usar un pod
- **CPU:** Exceso → throttling (va más lento, no muere)
- **Memory:** Exceso → OOMKilled (pod muere, Exit Code 137)

#### Unidades
- **CPU:** milli-cores (m) - 1000m = 1 CPU core
- **Memory:** Mi (mebibytes) o Gi (gibibytes) - binarios, recomendados

#### HPA (Horizontal Pod Autoscaler)
- Escala número de pods automáticamente
- Basado en utilización de CPU (o métricas custom)
- Requiere `requests.cpu` en Deployment
- Scale up: ~30s-1min, Scale down: ~5min
- Formula: `desired = ceil(current * (actual/target))`

### Archivos Creados

**Deployments:**
- `testcpu-deployment.yaml` - Pod de prueba de CPU
- `testram-deployment.yaml` - Pod de prueba de memoria
- `web-deployment.yaml` - Actualizado con resources

**ConfigMaps:**
- `testram-configmap.yaml` - Variable MEGABYTES para testram

**HPAs:**
- `testcpu-hpa.yaml` - Autoscaler para testcpu
- `web-hpa.yaml` - Autoscaler para web

### Comandos Importantes

```bash
# Monitoreo de recursos
kubectl top pods
kubectl top nodes

# Gestión de HPA
kubectl get hpa
kubectl describe hpa <hpa-name>
kubectl delete hpa <hpa-name>

# Debugging
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get events --sort-by='.lastTimestamp'
```

### Conceptos Críticos

**Requests vs Limits:**
```
Requests ≤ Uso Real ≤ Limits
```

**Comportamiento de límites:**
- CPU: Throttling (más lento)
- Memory: OOMKilled (muerte del pod)

**HPA Targets:**
```
Utilización = (uso_actual / request) * 100%
```

**Escalado en Kubernetes:**
- ✅ Preferir: Horizontal (más pods)
- ⚠️ Evitar: Vertical (pods más grandes)

### Lecciones Aprendidas

1. **Siempre establecer resource limits en producción**
2. **HPA requiere `requests.cpu` para funcionar**
3. **Memoria OOMKilled → crashea, CPU throttle → lento**
4. **Escalado horizontal > escalado vertical en K8s**
5. **HPA necesita tiempo (métricas cada 15s, scale down ~5min)**
6. **Requests ≤ Limits (regla de oro)**

---
