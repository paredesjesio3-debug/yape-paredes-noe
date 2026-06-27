# Evaluación Parcial — Big Data DD283 | Semestre 2026-1

**Estudiante:** Noe Paredes | **Docente:** Mg. Rubén Quispe Llacctarimay | **Ciclo:** VIII

---

## Descripción del proyecto

Este repositorio contiene la solución a la evaluación parcial del curso de Big Data, cuyo caso central es **Yape (BCP)**, la fintech más grande del Perú. El problema principal es que el sistema Oracle actual tarda 45 segundos en cargar el historial de un usuario, lo cual es inaceptable para una plataforma con 3.2 millones de transacciones diarias y picos de 450 TPS.

La solución propone rediseñar la arquitectura usando herramientas modernas de Big Data, distribuidas en cuatro partes: diseño de arquitectura, procesamiento en Databricks, base de datos NoSQL en MongoDB Atlas y contenedores con Docker.

---

## Parte A — Diseño de Arquitectura

Se diseñó una arquitectura multi-tecnología donde cada componente del sistema Yape usa la herramienta más adecuada a su naturaleza:

- El **core de pagos** requiere consistencia total, por lo que se propone CockroachDB (NewSQL con ACID distribuido), que resuelve la limitación de escala vertical de Oracle.
- Las **sesiones activas** de 15M de usuarios se manejan con Redis por su latencia de microsegundos y soporte nativo de TTL (expiración automática a los 30 minutos).
- El **perfil del comerciante** usa MongoDB por su esquema flexible, ya que una bodega, un taxi y un restaurante tienen atributos completamente distintos.
- El **historial de transacciones** (18 TB/año) se almacena en un Data Lakehouse con Parquet + Databricks, optimizado para lectura analítica masiva.
- La **detección de fraude** usa Neo4j para detectar ciclos de transacciones sospechosas (A→B→C→A) en menos de 5 minutos mediante consultas de grafos.
- El **dashboard ejecutivo** consolida métricas diarias con Spark y las visualiza en una herramienta BI.

---

## Parte B — Pipeline Medallion en Databricks

Se implementó un pipeline de procesamiento de 2,000 transacciones sintéticas de Yape usando la arquitectura Medallion sobre Databricks Community Edition:

- **Bronze:** ingesta raw de datos y almacenamiento en Parquet sin transformaciones.
- **Silver:** filtrado de transacciones completadas, eliminación de montos nulos, clasificación por categoría de monto (micro / medio / alto), detección de hora pico y cálculo de comisión Yape (1.5% a comercios).
- **Gold:** agregaciones de negocio — top 5 distritos por volumen de pagos e ingresos de Yape por hora del día — visualizados en un dashboard con matplotlib.

---

## Parte C — MongoDB Atlas (NoSQL)

Se insertaron 5 comerciantes con estructuras de documento distintas (bodega, restaurante, farmacia, taxi y empresa) en MongoDB Atlas M0, demostrando la ventaja del esquema flexible frente a SQL, donde estos datos requerirían docenas de columnas NULL.

Se implementaron tres queries con operadores MongoDB (`$gt`, `$in`, condiciones compuestas) y un Aggregation Pipeline que genera un reporte de facturación total, calificación promedio y cobertura de delivery agrupado por tipo de comercio.

---

## Parte D — Docker Desktop

Se levantó una instancia local de MongoDB 7.0 en un contenedor Docker como entorno de desarrollo aislado, replicando la estructura de Atlas sin depender de internet. Se conectó desde Python usando `pymongo` e insertó un documento de prueba con el campo `entorno: "docker_local"` para diferenciar el origen de los datos.

---

## Estructura del repositorio

```
semana_04/Soluciones/Paredes_Noe/
├── P1_arquitectura.md
├── P2_databricks_yape.ipynb
├── P3_mongodb_atlas.py
├── P4_docker.py
├── screenshots/
│   ├── databricks_celda1.png
│   ├── databricks_celda2.png
│   ├── databricks_celda3.png
│   ├── databricks_dashboard.png
│   ├── atlas_collections.png
│   ├── atlas_pipeline_output.png
│   └── docker_desktop.png
└── README.md
```

---

## Video de sustentación

🔗 https://______________________________

---

*Big Data DD283 | Universidad | 2026-1*
