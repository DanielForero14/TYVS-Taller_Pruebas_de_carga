# Matriz de rendimiento

| Escenario | Modelo | Duración | SLO | Resultado | Artefacto |
|---|---|---|---|---|---|
| Baseline | 20 VUs constantes, sin pausa | 5 min | p95 ≤ 300 ms | p95 = 1,50 ms. Cumple | perf/results/summary-baseline-original.json |
| Carga | 0→200 VUs, SLEEP_MS=100 | 14 min | p95 ≤ 300 ms | p95 = 0,73 ms. Cumple | perf/results/summary-load.json |
| Estrés | 200→600 VUs, SLEEP_MS=100 | 10 min | Error < 1 % | register_failed = 0,22 %. Cumple | perf/results/summary-stress.json |
| Negocio (voters) | 20 VUs, SLEEP_MS=100 | 5 min | register_failed < 1 % | 0 %. Cumple | perf/results/summary-voters-baseline.json |

## Comparación cliente vs. servidor (misma corrida de 2 min)

| Métrica | Cliente (k6) | Servidor (Actuator) |
|---|---|---|
| p95 | 1,28 ms | 0,36 ms |

Notas: se midió en un portátil con k6 y el servicio en la misma máquina; sirve para ver la forma, no como capacidad. Las cifras de baseline y load/stress usan condiciones distintas (SLEEP_MS 0 vs 100).
