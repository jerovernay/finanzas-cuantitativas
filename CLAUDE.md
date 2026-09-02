# Finanzas Cuantitativas

Materia optativa, Prof. Guido Schifani. Inicio: jueves 3/9/2026. Cursada: lunes 18-22h (teórica/práctica), jueves 18-20h teórica + 20-22h práctica (LABO 2).

Otras materias en paralelo este cuatrimestre: Final de Probabilidad (16/9), parcial Estadística (17/9), parcial Algo 3 (23/9).

## Estructura del curso

3 unidades, cada una a una escala temporal distinta — los modelos y herramientas difieren según la unidad:

- **Unidad 1 — Macro** (años/meses/días): bonos, acciones, derivados, ARIMA/VAR, VaR, Expected Shortfall, Basel III, curvas de rendimiento, ML macro (LSTM, XGBoost).
- **Unidad 2 — Intradía** (horas/min/seg): LOB, procesos Hawkes, Heston/SABR, GARCH, LASSO/Ridge, pair trading (cointegración), Random Forest/LGBM, Avellaneda-Stoikov (market making), Almgren-Chriss (ejecución óptima), VWAP/TWAP.
- **Unidad 3 — HFT** (ms a ns): MiFID II/SEC/CNV, protocolo FIX, C++ concurrente/lock-free, volatilidad instantánea, event-driven ML, spoofing/layering detection.

## Entregables (relevantes para código)

- **Guías prácticas (40%)** — 3 entregas computacionales, una por unidad.
- **Trabajo integrador (60%)** — proyecto final que articula las 3 unidades sobre datos reales de mercado, con defensa oral. Tema/dataset se definen durante el curso.

## Cómo trabajar el código

- **Lenguaje:** Python por defecto. C++ solo para temas de Unidad 3 (HFT/baja latencia).
- **Nivel:** buena base en programación y matemática — sí explicar conceptos financieros/matemáticos nuevos (no asumir conocimiento previo de finanzas).
- **Guías prácticas:** estructurar como notebook (estilo Jupyter): importaciones, carga de datos, modelo, resultados, visualización, interpretación.
- **Enfoque:** priorizar código funcional sobre datos reales/de ejemplo + visualizaciones, no solo teoría.
- **Datos de mercado:** yfinance (diario), Binance API (intradía crypto), Kaggle/LOBSTER (LOB y tick data).
- **Entorno:** venv en `venv/` (Python 3.12). Activar antes de instalar dependencias; agregar lo que se use a `requirements.txt`.

## Bibliografía (referenciar cuando sea relevante al tema)

- Derivados/opciones → Hull
- Cálculo estocástico → Shreve
- Microestructura/LOB → Cont & Stoikov, Bouchaud et al.
- Market making → Avellaneda-Stoikov (2008)
- Ejecución óptima → Almgren-Chriss (2001)
- ML en finanzas → Lopez de Prado (2020)
- Algo trading en Python → Hilpisch (2020)
- Volatilidad → Gatheral (2018), Guyon (2023)
- Deep learning series temporales → Zohren (2023, 2026)

## Notas

- El programa es un punto de partida, no fijo — priorizar siempre lo que se vea en clase por sobre lo listado acá.
- Septiembre está cargado de parciales de otras materias: ser eficiente y priorizar entregas próximas.
