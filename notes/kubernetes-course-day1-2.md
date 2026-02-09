# Curso de Kubernetes - Apuntes Consolidados

## Días 1 y 2 - Fundamentos e Introducción

---

## Tabla de Contenidos

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
11. [Glosario de Términos](#11-glosario-de-términos)
12. [Comandos Útiles](#12-comandos-útiles)

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

## 11. Glosario de Términos

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

1. **Kubernetes** es un sistema de orquestación de contenedores, no un lenguaje de programación
2. **kubectl** es la herramienta CLI para interactuar con clústeres de Kubernetes
3. **Minikube** permite ejecutar Kubernetes localmente para aprendizaje y desarrollo
4. **Pods** son la unidad mínima desplegable y son efímeros (temporales)
5. **Deployments** gestionan y mantienen réplicas de Pods
6. Cada Pod tiene su propia **IP virtual** única dentro del clúster
7. Los recursos en Kubernetes están en una **red privada** por defecto
8. Kubernetes resuelve el problema de **gestionar contenedores a escala**
9. Los Pods son **abstracciones** sobre contenedores que añaden funcionalidad de Kubernetes
10. El concepto de **inmutabilidad**: reemplazar en lugar de modificar

---

## Próximos Pasos

- Continuar con el curso explorando más objetos de Kubernetes
- Aprender sobre Services para exponer aplicaciones
- Explorar ConfigMaps y Secrets para configuración
- Estudiar Volumes para almacenamiento persistente
- Practicar con escalado horizontal de Pods

---

## Referencias Importantes

- **Documentación oficial de Kubernetes:** https://kubernetes.io/docs/home/
- **kubectl Cheat Sheet:** https://kubernetes.io/docs/reference/kubectl/cheatsheet/
- **Minikube Docs:** https://minikube.sigs.k8s.io/docs/
- **Repositorio del curso:** https://github.com/cecibelauda/k8s_course

---

**Última actualización:** Días 1 y 2 del curso  
**Versión de Kubernetes:** v1.35.0  
**Versión de Minikube:** v1.38.0  
**Versión de kubectl:** v1.35.0
