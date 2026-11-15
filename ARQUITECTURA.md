# Arquitectura

```
[Fuentes]                [Backend Python / FastAPI]                         [Frontend Next.js — pendiente]
Earthdata (earthaccess) ─┐
AppEEARS (área, GeoTIFF) ├─> 1 Obtención ─> 2 Normalización ─> 3 Procesamiento ─> 4 Estadística ─> API REST ─> Mapas (MapLibre)
GES DISC IMERG (HDF5)  ──┘     (ingest/*)     (unidades, QA,     (mensual,          (stats/*)                   Series (Plotly)
Demo sintético ────────────────────────────>   procedencia)      malla)           5 Espacial                    Comparación
                                                                                   6 Interpretación              Informes
                                                                                   7 Informes (CSV hoy; PDF F8)
```

Separación de capas del prompt maestro: 1 `ingest/` · 2 `provenance.py` + conversiones en lectores ·
3 `to_monthly`, `grid_to_wide` · 4 `stats/trend.py` · 5 `stats/spatial.py` · 6 `interpret.py` ·
7 `api/main.py` (`/export`).

Almacenamiento hoy: CSV + JSON de procedencia en `data/processed/` (pocos MB). PostgreSQL/PostGIS y
Zarr se justifican cuando haya mallas grandes (Fase 7); la interfaz `registry.py` aísla ese cambio.

Acceso a fuentes verificado en el trabajo del equipo:

| Fuente | Mecanismo usado | Autenticación |
|---|---|---|
| GPM IMERG (GES DISC) | `earthaccess` (notebook) | Token Earthdata (secreto de Colab) |
| GPM IMERG 1998–2025 (GES DISC) | `scripts/fetch_imerg_opendap.py` (HTTPS directo + recorte h5py) | Token Earthdata (Bearer) + EULA GES DISC |
| MODIS MOD11A2 / MOD13Q1 | Solicitud de área AppEEARS → GeoTIFF | Cuenta Earthdata |
| GPCP (GPCPMON V3.3) | `earthaccess` (descargado, aún no usado) | Token Earthdata |

Las demás fuentes del prompt (Giovanni, NOAA CDO, KNMI, CSA RCM/SWOT) siguen pendientes de
verificar en la Fase 7; no se asume que tengan una API uniforme.
