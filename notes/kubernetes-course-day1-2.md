# Curso de Kubernetes - Apuntes Consolidados

## Días 1, 2, 3 y 4 - Fundamentos, Deployments y Configuración

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

### Referencia
21. [Consultas Teóricas Frecuentes](#21-consultas-teóricas-frecuentes)
22. [Glosario de Términos](#22-glosario-de-términos)
23. [Comandos Útiles](#23-comandos-útiles)

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

## 21. Consultas Teóricas Frecuentes

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

**Última actualización:** Días 1, 2, 3 y 4 del curso  
**Versión de Kubernetes:** v1.35.0  
**Versión de Minikube:** v1.38.0  
**Versión de kubectl:** v1.35.0

---

**Microservicios desplegados:**
- ✅ synergychat-web (3 réplicas)
- ✅ synergychat-api (1 réplica)
- ✅ synergychat-crawler (1 réplica)
