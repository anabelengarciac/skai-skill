---
name: skai-creative-performance-pipeline
description: Export, structure, and quality-check Skai performance data for creative analytics. Use when Codex needs to build date- and country-specific datasets, compare periods, inspect campaigns, brands, channels, ads, drivers, outliers, and prepare analysis-ready CSV/JSON outputs. If no country is specified, assume USA.
---

# Skai Creative Performance Pipeline

## Overview

Use this skill to read Skai platform data and convert it into analysis-ready exports for creative performance work. It is designed for workflows that need repeatable data pulls by date range and country, configurable fields, quality checks, and period comparisons.

## Workflow

1. Confirm the business or research question, date range, country, and whether a prior period is needed.
2. Read [configuration.md](references/configuration.md) if credentials are missing or fields need to be changed.
3. Use [default-field-config.json](references/default-field-config.json) by default for an `AdId`-level export.
4. Run [skai_report_export.py](scripts/skai_report_export.py) to export Skai data.
5. Load `skai_report_records` and answer with reproducible calculations, not intuition.
6. For period comparisons, run the script twice and perform the comparison locally.

## Quick Start

Comando base:

```bash
python3 scripts/skai_report_export.py \
  --start-date 2026-04-01 \
  --end-date 2026-04-30 \
  --country ES \
  --output-dir /tmp/skai-creative-performance-pipeline-es
```

Para comparar periodos:

```bash
python3 scripts/skai_report_export.py \
  --start-date 2026-04-01 \
  --end-date 2026-04-30 \
  --country ES \
  --output-dir /tmp/skai-creative-performance-pipeline-current

python3 scripts/skai_report_export.py \
  --start-date 2026-03-01 \
  --end-date 2026-03-31 \
  --country ES \
  --output-dir /tmp/skai-creative-performance-pipeline-prior
```

If the analysis should follow the same exclusion convention as the `skai` enrichment skill, add `--exclude-brand`. If the analysis should keep only non-video creatives, add `--exclude-video`.

## Question Types

Usar esta skill para preguntas como:

- que campaigns, brands o sources explican una caida de CTR
- top ads o campaigns por volumen, clicks, conversions o CTR
- comparativas entre paises, channels o brands dentro de un periodo
- outliers con muchas impresiones y bajo CTR
- chequeos de calidad, por ejemplo filas sin `Country`, `AdId` o `CampaignId`
- enriquecimiento de una investigacion posterior con `ImageUrl` y metadata de creatividad

## Analysis Rules

- Recalcular metricas derivadas despues de agregar. No promediar `CTR` fila a fila.
- Tratar `CTR` como porcentaje, no como proporcion decimal, salvo que el export real diga otra cosa.
- Para comparativas, mostrar delta absoluto y delta relativo.
- Senalar muestras pequenas antes de sacar conclusiones. Por ejemplo, alto CTR con pocos clicks puede ser ruido.
- Si la pregunta requiere campos no presentes en el export por defecto, copiar el field config y extenderlo. No inventar respuestas.
- Si el usuario pide datos canonicos de negocio y existe una fuente curada fuera de Skai, explicitar que estas respondiendo desde Skai platform data.

See [analysis-playbook.md](references/analysis-playbook.md) for formulas and analysis patterns.

## Outputs

El script genera:

- `skai_report_records.csv` y/o `skai_report_records.json`
- `summary.json`

`skai_report_records` es la base analitica principal. `summary.json` documenta filtros aplicados, cobertura y columnas disponibles.

## Deliverable

Responder como un senior data analyst:

- respuesta directa primero
- evidencia cuantitativa despues
- explicacion del metodo y filtros usados
- caveats si falta algun campo, si hubo que excluir EXCLUDED_BRAND, o si el volumen es bajo
