<div align="center">

# 🗂️ Config Repo

### Repositorio centralizado de configuraciones para ElectrodoStore
#### Spring Cloud Config · Git Backend · Configuración como código

![Spring Cloud Config](https://img.shields.io/badge/Spring_Cloud_Config-6DB33F?style=for-the-badge&logo=spring&logoColor=white)
![GitHub](https://img.shields.io/badge/Git_Backend-181717?style=for-the-badge&logo=github&logoColor=white)
![Resilience4j](https://img.shields.io/badge/Resilience4j-6DB33F?style=for-the-badge&logo=spring&logoColor=white)

</div>

---

Repositorio centralizado de configuraciones para todos los componentes de **ElectrodoStore**.

Es consumido por **Config Server**, que distribuye la configuración a cada servicio durante su arranque, permitiendo centralizar propiedades, variables de entorno y configuraciones compartidas de la plataforma.

---

## 🎯 Responsabilidades

- ⚙️ Centralizar configuraciones de infraestructura
- 🔐 Compartir configuración de seguridad OAuth2
- 🗄️ Compartir configuración de persistencia
- 📍 Compartir configuración de Eureka Client
- 🛡️ Centralizar configuración de resiliencia
- 🌐 Configurar rutas y comportamiento del API Gateway
- 📦 Mantener configuración desacoplada del código fuente

---

## 📁 Estructura del repositorio

```text
config-repo/
│
├── application.yml               # Configuración global compartida
├── database-config.yml           # Persistencia compartida
├── eureka-client-config.yml      # Service discovery compartido
├── security-config.yml           # OAuth2 / JWT compartido
│
├── config-server.yml
├── eureka-server.yml
├── api-gateway.yml
│
├── auth-service.yml
├── producto-service.yml
├── cliente-service.yml
├── carrito-service.yml
└── venta-service.yml
```

---

## ⚙️ Configuración compartida

### `application.yml`

Configuración global compartida por todos los servicios: Actuator, Health Checks e información básica de monitoreo.

---

### `database-config.yml`

Configuración común para servicios con persistencia.

| Variable | Descripción |
| --- | --- |
| `DB_URL` | URL de conexión |
| `DB_USERNAME` | Usuario |
| `DB_PASSWORD` | Contraseña |

---

### `eureka-client-config.yml`

Configuración compartida de registro y descubrimiento de servicios.

| Variable | Descripción |
| --- | --- |
| `EUREKA_URL` | URL de Eureka Server |

---

### `security-config.yml`

Configuración compartida para los servicios que funcionan como OAuth2 Resource Server. Define la ubicación de la clave pública RSA utilizada para validar la firma de los JWT emitidos por Auth Service.

---

## 🔌 Puertos por servicio

| Servicio | Puerto |
| --- | --- |
| `config-server` | `8888` |
| `eureka-server` | `8761` |
| `api-gateway` | `9090` |
| `auth-service` | `7474` |
| `producto-service` | `8181` |
| `cliente-service` | `8080` |
| `carrito-service` | `8282` |
| `venta-service` | `9191` |

> Todos los puertos pueden sobrescribirse mediante la variable de entorno `PORT`.

---

## 🛡️ Configuración de Resilience4j

### Configuración base

```yaml
slidingWindowSize: 10
minimumNumberOfCalls: 5
failureRateThreshold: 50
waitDurationInOpenState: 30s
```

### carrito-service

| Circuito | Excepciones ignoradas |
| --- | --- |
| `cliente-service` | `ClienteNotFoundException` |
| `producto-service` | `ProductoNotFoundException`, `ProductoStockInsuficienteException` |
| `venta-service` | Excepciones de dominio |

### venta-service

| Circuito | Excepciones ignoradas |
| --- | --- |
| `cliente-service` | `ClienteNotFoundException` |
| `producto-service-read` | `ProductoNotFoundException` |
| `producto-service-write` | `ProductoNotFoundException`, `ProductoStockInsuficienteException` |

### cliente-service

| Circuito | Excepciones ignoradas |
| --- | --- |
| `venta-service` | Excepciones de dominio |

### auth-service

| Circuito | Excepciones ignoradas |
| --- | --- |
| `cliente-service` | `ClienteNotFoundException` |

---

## 🔒 Seguridad

- ✅ Repositorio público sin credenciales embebidas
- ✅ Variables sensibles externalizadas
- ✅ Claves privadas RSA fuera del repositorio
- ✅ Configuración compartida de validación JWT
- ✅ Compatible con OAuth2 Resource Server

---

## 🏗️ Arquitectura

- ⚙️ Configuración centralizada mediante Spring Cloud Config
- 📂 Backend Git como fuente única de configuración
- 📍 Descubrimiento dinámico mediante Eureka
- 🛡️ Resiliencia configurada por servicio
- 🌐 Gateway como punto único de entrada

---

## 💡 Decisiones de diseño

<details>
<summary><b>⚙️ Configuración centralizada</b></summary>
<br>
Toda la configuración de infraestructura se mantiene fuera de los repositorios de código, permitiendo modificar comportamiento sin recompilar servicios.
</details>

<details>
<summary><b>📍 Eureka compartido</b></summary>
<br>
La configuración de descubrimiento de servicios se centraliza para evitar duplicación entre microservicios.
</details>

<details>
<summary><b>🗄️ Configuración reutilizable</b></summary>
<br>
Archivos compartidos como <code>database-config.yml</code>, <code>eureka-client-config.yml</code> y <code>security-config.yml</code> permiten reutilizar configuraciones comunes entre múltiples servicios.
</details>

<details>
<summary><b>🛡️ Resiliencia centralizada</b></summary>
<br>
La configuración de Circuit Breaker y Retry se mantiene junto a la configuración del servicio correspondiente para facilitar su administración.
</details>

---

## 🚀 Mejoras futuras

| Mejora | Descripción |
| --- | --- |
| 🔄 **Refresh dinámico** | Actualización de configuración sin reiniciar servicios |
| 🔐 **Config Encryption** | Cifrado de propiedades sensibles |
| 📡 **Config Bus** | Distribución automática de cambios |
| 📊 **Observabilidad** | Métricas y monitoreo del Config Server |
| 🔑 **Rotación de claves** | Gestión automática de claves RSA |

