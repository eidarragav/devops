# Lab 04 — Kubernetes: Guía de Preparación 🐳☸️

> **Introducción a DevOps · UNAL Manizales · Profesor: Cesar Augusto Palacios Alarcón**
> **Hacer ANTES del miércoles 21 de octubre** — el día del lab arrancamos directo con el clúster.

🎯 **Objetivo:** llegar a la sesión práctica con la EC2 lista y las herramientas instaladas. Si completan esta guía (30–45 min), el miércoles empiezan creando su clúster Kubernetes. Se verifica al inicio de la sesión.

⏱️ Todo lo que instalen queda guardado en el disco de la instancia: **apagar la EC2 no borra nada**.

---

## Paso 0 · Lo que necesitas antes de empezar

| Requisito | Detalle |
|---|---|
| Invitación de AWS Academy | Aceptada desde el correo institucional (la de la Nota 4). ¿Nunca aceptada? Escriban al profe HOY |
| Archivos del laboratorio | `kind.yml` · `deployment.yml` · `service.yml` (esta carpeta) |
| Computador con navegador | Para la consola de AWS y el SSH |

## Paso 1 · Entrar al Learner Lab

1. Entrar a **awsacademy.instructure.com** → curso **AWS Academy Learner Lab**
2. Botón **Start Lab** — esperar el círculo verde ✓ (1–2 min)
3. El botón **AWS** abre la consola con los créditos del curso

> 🟡 **Regla de oro:** los créditos son limitados y compartidos. Al terminar cualquier sesión: **End Lab** o como mínimo **Stop instance**.

## Paso 2 · Su instancia EC2 — dos caminos

### Camino A — ya tienen la instancia del Lab 03 (Docker)

EC2 → Instances → seleccionar su instancia → **Instance state → Start instance** → esperar *Running* → **anotar la nueva IPv4 pública** (cambia cada encendida). Saltar al Paso 3.

### Camino B — desde cero

EC2 → **Launch instance** con esta configuración:

| Campo | Valor |
|---|---|
| Nombre | `srv-devops-equiponn` (su número de equipo) |
| AMI | Debian 12 (o Ubuntu 22.04) — x86_64 |
| Tipo | `t2.micro` o `t3.micro` (capa gratuita) |
| Par de llaves | `vockey` — descargar: botón **AWS Details** (arriba en el Learner Lab) → Download PEM |
| Security group | Nuevo, DOS reglas: **SSH (22) origen "My IP"** + **Custom TCP (30001) origen 0.0.0.0/0** ← el puerto del lab, lo dejamos abierto de una vez |
| Almacenamiento | 8–10 GB gp3 (por defecto) |

Launch → esperar **Running** → copiar la **Dirección IPv4 pública**.

## Paso 3 · Conectarse por SSH

**Opción CloudShell (sin instalar nada):** abrir CloudShell (esquina de la consola AWS) → subir `labsuser.pem` (Actions → Upload):

```bash
chmod 400 labsuser.pem
ssh -i labsuser.pem admin@SU-IP-PUBLICA   # Debian: "admin" / Ubuntu: "ubuntu"
```

**Opción MobaXterm/Terminal:** sesión SSH, host = su IP, usuario `admin` (Debian), el `.pem` como llave. Aceptar la huella del servidor la primera vez.

## Paso 4 · Docker (solo Camino B — el A ya lo tiene)

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl git
curl -fsSL https://get.docker.com | sudo sh
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
exit   # salir y volver a entrar (permisos docker)
docker run hello-world   # debe imprimir "Hello from Docker!"
```

## Paso 5 · kubectl y Kind (todos)

```bash
# kubectl — el control del clúster
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client

# kind — el clúster que vive en Docker
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.29.0/kind-linux-amd64
chmod +x ./kind && sudo mv ./kind /usr/local/bin/kind
kind version   # v0.29.0
```

## Paso 6 · Los archivos del laboratorio

Clonar este repositorio (si tienen acceso) o descargar el ZIP de Moodle y subirlo a la instancia:

```bash
git clone <URL-del-repo-del-curso>
ls Devops/k8s/   # kind.yml  deployment.yml  service.yml  README.md
```

---

## ✅ Checklist final — así llegan listos el miércoles

- [ ] Instancia encendida + IP anotada
- [ ] `docker version` responde
- [ ] `kubectl version --client` instalado
- [ ] `kind version` instalado
- [ ] Los 3 YAML en la instancia
- [ ] Security group con el 30001 abierto
- [ ] Instancia **detenida** al terminar la guía (¡Stop, NO Terminate!)

> ⚠️ **Detener ≠ Terminar:** *Stop instance* apaga y libera cómputo (lo instalado queda en disco); *Terminate* destruye todo. El miércoles: Start instance y todo sigue donde lo dejaron.

## El día del lab (miércoles 21)

El enunciado completo está en Moodle/Drive: crear el clúster Kind con `kind.yml`, completar `deployment.yml` y `service.yml`, exponer el sitio por el NodePort 30001 y experimentar con escalado, auto-reparación y rolling updates. **Entrega: miércoles 21, 11:59 p. m.**

✉️ ¿Se atascaron? Escriban al profe indicando el PASO exacto y el mensaje de error — respuesta garantizada antes del miércoles.
