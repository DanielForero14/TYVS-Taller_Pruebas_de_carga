# Registro de Defectos

Curso: Testing y Validación de Software
Proyecto: Pruebas de Carga y Rendimiento

## Formato 1: Lista detallada

### Defecto PERF-01

- **Caso de prueba**: Escenario `stress` (200→600 VUs, 10 min) con `register_person_k6.js`
- **Entrada**: POST /register con ids generados por `__VU * 1000000 + __ITER`
- **Resultado esperado**: `register_failed` < 1 % y respuesta `VALID` en todas las peticiones
- **Resultado obtenido**: `register_failed` = 0,22 % (respuestas `DUPLICATED` con status 200). p95 = 0,56 ms; `http_req_failed` = 0 %
- **Causa probable**: estado de prueba. El servicio con H2 en memoria conserva los ids registrados y el script repite ids al iniciar VUs (regla 04 del taller)
- **Estado**: Abierto

### Defecto PERF-02

- **Caso de prueba**: Escenarios `load` y `stress` sin pausas de usuario (`SLEEP_MS=0`)
- **Entrada**: 200 y 600 VUs sin `sleep`
- **Resultado esperado**: el servicio atiende la carga sin errores de conexión
- **Resultado obtenido**: `connection refused` a los ~156 s con 200 VUs; corrida inválida
- **Causa probable**: sin think time el inyector satura el equipo local (k6 y servicio comparten CPU). Se corrigió con `SLEEP_MS=100`
- **Estado**: Resuelto

### Defecto PERF-03

- **Caso de prueba**: Código de `RegistryRepository.getConnection()`
- **Entrada**: cada petición ejecuta `existsById` y `save`
- **Resultado esperado**: reutilizar conexiones mediante un pool
- **Resultado obtenido**: se abre una conexión nueva por operación (dos por petición) con `DriverManager`. No se midió su efecto en este equipo
- **Causa probable**: ausencia de pool de conexiones (HikariCP)
- **Estado**: Abierto

## Formato 2: Tabla de defectos

| ID | Caso de Prueba | Entrada | Resultado Esperado | Resultado Obtenido | Causa Probable | Estado |
|----|----------------|---------|--------------------|--------------------|----------------|--------|
| PERF-01 | stress 600 VUs | ids por VU/iteración | register_failed < 1 % | 0,22 % DUPLICATED | Estado de prueba (ids repetidos) | Abierto |
| PERF-02 | load/stress sin pausas | SLEEP_MS=0 | Sin errores de conexión | connection refused a 156 s | Inyector saturando el equipo | Resuelto |
| PERF-03 | RegistryRepository | 2 operaciones por petición | Uso de pool | Conexión nueva por operación | Sin pool de conexiones | Abierto |

## Convenciones de Estado

- **Abierto** → El defecto aún no se corrige.
- **En progreso** → El defecto está siendo trabajado.
- **Resuelto** → El defecto fue corregido y validado con pruebas.
