# Historial de cambios

Todos los cambios relevantes del proyecto. El formato sigue [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y las versiones siguen [Versionado Semántico](https://semver.org/lang/es/).

## [1.0.0] - 2026-10-31

Primera versión completa: todos los requisitos del enunciado implementados, probados, empaquetados con Docker y publicados en Azure. La relación requisito → prueba está en [Trazabilidad](docs/07-trazabilidad.md).

**En línea:** https://entidad-financiera-sofia.northcentralus.cloudapp.azure.com

### Clientes
- CRUD completo con fechas de creación y modificación automáticas (#3).
- Reglas: mayor de edad (18 años), identificación única por tipo (CC, CE, PA), correo `xxxx@xxxxx.xxx`, nombres y apellido de al menos 2 caracteres, y no se elimina un cliente con productos (#3).

### Cuentas
- Cuentas de ahorros y corriente con número automático de 10 dígitos (53 / 33) generado con secuencias de PostgreSQL. Patrones **Strategy** (reglas por tipo de cuenta) y **Factory** (creación y numeración) (#4).
- Activar, inactivar y cancelar con saldo $0. Una cuenta cancelada es definitiva (#4).
- La cuenta corriente admite **sobregiro** hasta un cupo, y el **saldo disponible** es saldo + cupo (#4).
- Al cancelar una corriente se cierra su cupo de sobregiro (SUP-14) (#16).
- Máximo una cuenta de ahorros y una corriente activas o inactivas por cliente (SUP-15) (#24, #25).

### Transacciones
- Consignaciones, retiros y transferencias con débito en el origen y crédito en el destino, agrupados por una referencia (#5).
- **GMF 4x1000** como movimiento aparte en retiros y transferencias de cuentas no exentas (#5).
- Estado de cuenta con el saldo después de cada movimiento (#5).
- Operaciones **ACID** con bloqueo pesimista ordenado por id, sin *deadlocks* entre transferencias cruzadas. Una prueba de concurrencia lo demuestra contra PostgreSQL real (#5).

### Frontend (Angular 22 + Angular Material)
- Estructura base, layout con menú lateral y avisos de error en español con los mensajes de la API (#6, #7).
- Diseño "banca moderna": panel principal con indicadores reales, tablas con avatares y tarjetas de cuenta tipo bancarias (#10).
- Pantallas de clientes, detalle con cuentas, apertura y cambios de estado de cuentas (#8, #9, #11, #12).
- Consignar, retirar y transferir con vista previa del 4x1000 y del uso del sobregiro, y comprobante de cada operación (#13, #14, #17).
- Estado de cuenta con totales, filtro por tipo y descarga en CSV para Excel (#15).
- Campos de dinero con separadores de miles (`1.250.000,50`) y calendario en español (#9, #13).

### Base de datos (PostgreSQL 16 + Flyway)
- `V1` esquema con restricciones de negocio, `V2` datos de ejemplo, `V3` cuentas canceladas sin cupo y `V4` una cuenta abierta de cada tipo por cliente (#2, #16, #24).

### Contenedores y nube
- Imágenes Docker multi-stage del backend (JRE Alpine, capas del JAR, sin root, *healthcheck*) y del frontend (Nginx sin root, proxy de `/api`, cabeceras de seguridad) (#18, #19).
- `docker compose up --build` levanta los 3 contenedores en orden, esperando a que cada uno esté sano (#20).
- Despliegue en una máquina virtual de **Microsoft Azure** (Azure for Students), solo con los puertos 80 y 443 públicos (#26, #27).
- **HTTPS** con Caddy y un certificado gratuito de Let's Encrypt que se renueva solo (#28).

### Calidad
- 170 pruebas del backend (JUnit 5, Mockito, MockMvc y Testcontainers) y 171 del frontend (Vitest).
- **100 %** de cobertura en Service y Controller, exigido por JaCoCo: el build falla si baja (#23).
- Integración continua con GitHub Actions: pruebas, cobertura e imágenes Docker en cada Pull Request (#21, #22, #23).

### Documentación
- Requerimientos y supuestos, arquitectura (hexagonal, patrones, SOLID y ACID), modelo de datos, API, Git Flow, despliegue y trazabilidad (#1, #29).
- Seis decisiones de arquitectura registradas como ADR.
- API documentada con OpenAPI / Swagger UI (#2).

[1.0.0]: https://github.com/Sofialjure/prueba-tecnica-entidad-financiera/releases/tag/v1.0.0
