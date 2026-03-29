# 🗂️ Config Repo - ElectrodoStore

## 📌 Descripción
Repositorio centralizado de configuraciones para todos los microservicios
de ElectrodoStore. Es consumido por el **Config Server**, que se encarga
de servirlas a cada servicio en el momento de su arranque.

---

## 📁 Estructura del repositorio
```text
config-repo/
├── application.yml            # Configuración global compartida por todos los servicios
├── database-config.yml        # Configuración de base de datos (compartida)
├── eureka-client-config.yml   # Configuración de Eureka Client (compartida)
├── api-gateway.yml            # Configuración del API Gateway
├── eureka-server.yml          # Configuración del Eureka Server
├── producto-service.yml       # Configuración del Producto Service
├── cliente-service.yml        # Configuración del Cliente Service
├── carrito-service.yml        # Configuración del Carrito Service
└── venta-service.yml          # Configuración del Venta Service
```

---

## ⚙️ Archivos compartidos

### `application.yml`
Configuración global aplicada a **todos los servicios** automáticamente:
- Exposición de endpoints de salud (`health`, `info`) mediante Spring Actuator

### `database-config.yml`
Configuración de base de datos compartida por todos los servicios con
persistencia. Las credenciales se inyectan mediante variables de entorno:

| Variable | Descripción |
|---|---|
| `DB_URL` | URL de conexión a la base de datos |
| `DB_USERNAME` | Usuario de la base de datos |
| `DB_PASSWORD` | Contraseña de la base de datos |

### `eureka-client-config.yml`
Configuración de registro en Eureka compartida por todos los servicios.
La URL del servidor se inyecta mediante variable de entorno:

| Variable | Descripción |
|---|---|
| `EUREKA_URL` | URL del Eureka Server |

---

## 🔌 Puertos por servicio

| Servicio | Puerto por defecto |
|---|---|
| `api-gateway` | `9090` |
| `eureka-server` | `8761` |
| `producto-service` | `8181` |
| `cliente-service` | `8080` |
| `carrito-service` | `8282` |
| `venta-service` | `9191` |

> Los puertos son configurables mediante la variable de entorno `PORT`
> en cada servicio.

---

## 🛡️ Configuración de Resilience4j

Los servicios con Circuit Breaker configurado son:

### `carrito-service`

| Circuito | Excepciones ignoradas |
|---|---|
| `cliente-service` | `ClienteNotFoundException` |
| `producto-service` | `ProductoNotFoundException`, `ProductoStockInsuficienteException` |
| `venta-service` | `ClienteNotFoundException`, `ProductoNotFoundException`, `ProductoStockInsuficienteException` |

### `venta-service`

| Circuito | Excepciones ignoradas |
|---|---|
| `producto-service-read` | `ProductoNotFoundException` |
| `producto-service-write` | `ProductoStockInsuficienteException` |
| `cliente-service` | `ClienteNotFoundException` |

Configuración base compartida:
```yaml
slidingWindowSize: 10
minimumNumberOfCalls: 5
failureRateThreshold: 50
waitDurationInOpenState: 30s
```

---

## 🔒 Seguridad

- ✅ Repositorio público — sin credenciales hardcodeadas
- ✅ Todas las variables sensibles externalizadas mediante `.env`
- ✅ Ningún archivo contiene valores reales de producción

---

## 💡 Decisiones de diseño

- Archivos compartidos (`database-config.yml`, `eureka-client-config.yml`)
  para evitar duplicación entre servicios
- Configuración de Resilience4j centralizada por servicio
- Variables de entorno para toda información sensible
- Un archivo por servicio para mantener separación de responsabilidades