# Finanzas Cuantitativas — Mapa de conceptos

> Glosario en red de la materia (Schifani, FCEN-UBA). No es un apunte lineal:
> cada concepto es un nodo corto, y las flechas `→` son las aristas que lo conectan
> con el resto. El valor está en las conexiones, no en las definiciones.

---

## Cómo se usa este archivo

**Fuente de verdad.** Este `.md` es la copia durable de la materia. Vive en tu repo
(VSCode / Claude Code). Cuando arranques un chat nuevo con Claude, pegalo o subilo:
es el mecanismo de continuidad (Claude no recuerda entre conversaciones).

**Flujo de captura.**
1. Después de cada clase, le pasás a Claude el texto de las slides o tus notas.
2. Claude devuelve las entradas nuevas en este formato.
3. Las pegás acá.
4. Cuando estudiás, le pedís a Claude que renderice la vista que quieras
   (glosario de una clase, submapa de un tema, checklist para un parcial).

**Formato de una entrada.**
```
### Nombre del concepto  [C2 §2]
Qué es, en 1–2 líneas. Fórmula si aplica.
→ concepto_conectado · otro_concepto · idea_transversal
```

**Convenciones.**
- `[C2 §2]` = clase 2, sección 2 de las slides. Para filtrar una clase, buscá `[C2`.
- `→` = "se conecta con". Es la red. Es lo que hace que esto sea un mapa y no una lista.
- `⚡` = arista transversal fuerte (el mismo esqueleto matemático reaparece en otra clase).
- `❓ DUDA` = pregunta abierta tuya, sin resolver todavía.
- En VSCode: el panel *Outline* te da la navegación por concepto (cada `###` es un ítem).

---

## Índice de clases

| Clase | Tema | Estado |
|---|---|---|
| C1 | Introducción: mercados, tasas, FX y curvas | ✅ armada |
| C2 | Opciones y Black-Scholes | 🌱 sembrada (completar) |
| C3 | Volatilidad, tasas y crédito | 📥 material subido, pendiente clase |
| C4 | Factores | ⬜ pendiente |
| C5 | Portfolios | ⬜ pendiente |
| C6 | Machine Learning | ⬜ pendiente |
| C7–C8 | Unidad 3 (HFT / baja latencia) | ⬜ pendiente |
| C9 | Wrap del curso + presentaciones (formato póster) | ⬜ pendiente |

**Hilo conductor del curso:** casi todo el riesgo se lee como una **expansión de Taylor**.
Primera derivada (sensibilidad) y segunda derivada (curvatura) reaparecen con otro nombre
en cada clase: duration/convexidad (bonos) → DV01/convexidad (swaps) → delta/gamma (opciones). ⚡

---

# C1 — Introducción: mercados, tasas, FX y curvas

> El arco de la clase: qué se opera → quién lo opera → el precio del tiempo (tasas) →
> el mercado FX → construir y calibrar la curva de tasas → usarla para valuar un swap.
> Objetivo declarado: *calibrar una curva de rendimientos real y valuar swaps.*

## §2 Instrumentos

### Renta fija / Bono  [C1 §2]
Instrumento de deuda: una promesa de flujos futuros. Su precio es el valor presente de esos flujos, descontados a la tasa de mercado *y*: `P = Σ C/(1+y)^t + VN/(1+y)^T`.
→ duration · convexidad · YTM · factor_de_descuento · bootstrapping

### Duration  [C1 §2]
Sensibilidad del precio del bono a la tasa. *Macaulay*: tiempo promedio ponderado hasta los flujos (años, "centro de masa"). *Modificada* = Macaulay/(1+y): la elasticidad, `ΔP/P ≈ −D_mod·Δy`. Es la **primera derivada** del precio respecto de la tasa.
→ convexidad · DV01 · delta ⚡ · relación_tasa_precio

### Convexidad  [C1 §2]
**Segunda derivada** del precio respecto de la tasa: corrige lo que la duration (recta tangente) sobre/subestima. `ΔP/P ≈ −D_mod·Δy + ½·C·Δy²`. Duration = velocidad, convexidad = aceleración.
→ duration · gamma ⚡ · expansión_de_Taylor

### Acción / Renta variable  [C1 §2]
Fracción de propiedad de una empresa: derecho sobre ganancias (dividendos) y votos. A diferencia del bono, no promete flujos.
→ P/E_ratio · riesgo_sistemático · índices

### P/E ratio  [C1 §2]
Precio sobre ganancias por acción. Cuántos años de ganancias actuales "pagás" por la acción; proxy grueso de valuación.
→ acción

### Riesgo sistemático (beta)  [C1 §2]
Sensibilidad de una acción al movimiento del mercado. Es el riesgo que **no** se diversifica (el idiosincrático sí).
→ acción · factores (C4) · portfolios (C5)

### Commodities  [C1 §2]
Activos físicos fungibles (petróleo, oro, soja). Se operan sobre todo vía futuros.
→ futuros

### Derivados  [C1 §2]
Contratos cuyo valor deriva de un subyacente. Tres familias: **futuros** (comprar/vender a precio fijo en fecha futura), **swaps** (intercambio de flujos), **opciones** (derecho, no obligación).
→ futuros · swaps · opciones · IRS

### Call / Put  [C1 §2]
**Call**: derecho de *comprar* el subyacente a precio K en (o antes de) T. **Put**: derecho de *vender*. Europea = solo en T; americana = en cualquier momento ≤ T.
→ opciones (C2) · put_call_parity (C2) · valor_intrínseco (C2)

### CDS (Credit Default Swap)  [C1 §2]
Seguro de crédito: el comprador paga una prima periódica; si el emisor entra en default, cobra. Protagonista de la crisis de 2008.
→ derivados · crédito_estructural (C3)

## §3 Actores del mercado

### Actores institucionales  [C1 §3]
Regulados: bancos centrales (Fed, BCE, BCRA), bancos, fondos mutuos/de pensión, empresas no financieras (corporativos que se cubren), aseguradoras.
→ actores_especulativos · market_maker

### Actores especulativos  [C1 §3]
Buscan retorno tomando riesgo: hedge funds, **market makers** (proveen liquidez cotizando bid/ask), **arbitrajistas** (explotan diferencias de precio y las cierran).
→ market_maker · CIP (arbitraje) · no_arbitraje (C2)

### Market maker / liquidez  [C1 §3]
Cotiza compra y venta simultáneas; gana el spread y provee liquidez. Contraparte del retail vía payment for order flow.
→ payment_for_order_flow · LOB (Unidad 3)

### Retail / Payment for order flow  [C1 §3]
El retail pasó de ruido de fondo a ~20–25% del volumen de acciones en EEUU (Robinhood). **PFOF**: el broker no cobra comisión y le *vende* el flujo de órdenes a un market maker mayorista; puede ejecutarse fuera de bolsa.
→ market_maker · GameStop (ej. 2021)

## §4 El precio del tiempo: tasas de interés

### Tasa simple / compuesta / continua  [C1 §4]
**Simple**: `VF = VP(1 + r·t)` (interés proporcional al tiempo, sin reinversión). **Compuesta**: `VF = VP(1+r)^t` (se reinvierte). **Continua**: `VF = VP·e^{rt}` (límite de capitalizar infinitas veces; la que se usa para descontar en pricing).
→ factor_de_descuento · VPN

### TNA / TEA / TEM  [C1 §4]
Tasas equivalentes. **TNA** = nominal anual (la "anunciada", ignora la capitalización). **TEA** = efectiva anual, **TEM** = efectiva mensual (sí incluyen el efecto de capitalizar).
→ tasa_compuesta

### VPN (Valor Presente Neto)  [C1 §4]
Suma de flujos descontados menos la inversión inicial: `VPN = Σ CF_t/(1+r)^t`, con `CF_0 < 0`. Regla: invertir si VPN > 0.
→ TIR · factor_de_descuento

### TIR / YTM  [C1 §4]
**TIR**: la tasa que hace VPN = 0. **YTM** (yield to maturity): la TIR de un bono, la `y*` que iguala el precio de mercado al VP de sus flujos. No hay fórmula cerrada → se resuelve numéricamente. Es *una* tasa promedio de muchas (una por plazo).
→ VPN · bono · tasas_spot · limitaciones_TIR

### Factor de descuento  [C1 §4]
`P(0,T) = e^{−r(T)·T}`: cuánto vale hoy $1 seguro a recibir en T años. Ladrillo de toda valuación.
→ tasas_spot · curva_de_descuento · IRS

### Curva de rendimiento  [C1 §4]
La tasa como **función del plazo** `r(T)`. Tres formas: **normal** (sube con el plazo), **flat**, **invertida** (baja; señal recesiva clásica).
→ tasas_spot · bootstrapping · Nelson-Siegel · teorías_estructura_temporal

### Relación tasa ↔ precio  [C1 §4]
La relación más fundamental de renta fija: **↑ tasa ⇒ ↓ precio** y **↓ tasa ⇒ ↑ precio** (el precio es el VP de flujos, y descontar más fuerte baja el valor).
→ bono · duration · curva_de_rendimiento

### La Fed / federal funds rate  [C1 §4]
Tasa overnight objetivo que fija la Fed; ancla el extremo corto de la curva y se propaga al resto.
→ curva_de_rendimiento · LIBOR_SOFR

### LIBOR → SOFR  [C1 §4]
Transición de la tasa de referencia: de LIBOR (basada en *encuestas* a bancos, manipulable) a **SOFR** (basada en *transacciones* reales de repo). De la declaración al dato duro.
→ la_Fed

## §5 FX (mercado de divisas)

### FX / Forwards de moneda  [C1 §5]
Mercado más grande que existe (~USD 9.6 billones/día). **Forward**: fijar hoy el tipo de cambio F para intercambiar monedas en T.
→ CIP · carry_trade · NDF · spot

### Covered Interest Parity (CIP)  [C1 §5]
Ley de una sola tasa: dos rutas sin riesgo (quedarse en USD vs. pasar por otra moneda con forward) deben rendir igual → `F = S·e^{(r_d − r_f)·T}`. El forward **no es una predicción**: es aritmética de las tasas de hoy. Se puede invertir para extraer la tasa implícita.
→ no_arbitraje (C2) · forwards · carry_trade · arbitrajistas

### Carry trade  [C1 §5]
La versión **sin cobertura** de la CIP: fondearse en moneda de tasa baja (histórico: JPY) e invertir en la de tasa alta. Gana el diferencial mientras el tipo de cambio no se mueva en contra. `❓` (nota: "Japón hace dos semanas" — episodio de unwind del carry JPY).
→ CIP · FX

### NDF (Non-Deliverable Forward)  [C1 §5]
Forward que se liquida por diferencia en USD (no hay entrega física de la moneda). Se usa para monedas con restricciones cambiarias, como el ARS.
→ forwards · FX

## §6 Construir y calibrar la curva

### Tasas spot / forward / descuento  [C1 §6]
**Spot** `r(T)`: tasa para invertir hoy hasta T. **Forward**: tasa futura implícita entre dos plazos. **Descuento** `P(0,T)`: precio hoy de $1 en T. Son tres vistas de la misma curva.
→ factor_de_descuento · bootstrapping · YTM

### Teorías de la estructura temporal  [C1 §6]
Por qué la curva tiene la forma que tiene: **expectativas puras** (la tasa larga = promedio de cortas esperadas), **prima de liquidez** (los plazos largos pagan extra), **segmentación** (oferta/demanda por tramo).
→ curva_de_rendimiento

### Bootstrapping  [C1 §6]
Extracción **iterativa** de tasas spot. Problema: los bonos con cupón mezclan flujos en múltiples fechas → no dan la spot directo. Solución: despejar plazo por plazo, usando las spot ya conocidas para descontar los cupones intermedios.
→ tasas_spot · Nelson-Siegel · curva_de_rendimiento

### Nelson-Siegel / NSS  [C1 §6]
Modelo **paramétrico** de la curva. NS: 4 parámetros (nivel + pendiente + curvatura). **NSS** (Svensson): agrega una 2ª curvatura (β₃, λ₂) → 6 parámetros. Calibrar = mínimos cuadrados no lineales (mismo flujo que ajustar un modelo en física). Regla: parámetros < puntos, o interpolás en vez de ajustar.
→ bootstrapping · calibración ⚡ · riesgo_país (spread de curvas)

### Z-spread / OAS  [C1 §6]
Spread de **crédito** limpio: cuánto hay que sumarle a *cada* tasa spot del gobierno para que los flujos del bono corporativo/soberano descuenten a su precio de mercado.
→ tasas_spot · CDS · crédito_estructural (C3)

## §7 Interest Rate Swaps

### IRS (Interest Rate Swap)  [C1 §7]
Contrato OTC: intercambio de flujos sobre un nocional N (no se transfiere N). El *payer* paga fija y recibe flotante. Valor = pata flotante − pata fija, ambas descontadas con la curva. Se valúa como portafolio de FRAs.
→ par_swap_rate · DV01 · MTM · curva_de_descuento · swaptions (C2)

### Par swap rate  [C1 §7]
La tasa fija `c*` que hace el swap valer 0 al inicio: `c* = (1 − P(0,T)) / Σ δ_i·P(0,t_i)`.
→ IRS · MTM

### DV01 / MTM  [C1 §7]
**MTM** (mark-to-market): un swap arranca en 0; con el tiempo y el movimiento de la curva gana o pierde valor. **DV01** (Dollar Value of 1bp): cuánto cambia el valor ante 1 punto básico de movimiento paralelo de la curva. Es la duration del swap, en dólares.
→ duration ⚡ · IRS · convexidad

## ❓ Dudas abiertas de C1

- **¿Por qué el dinero futuro vale menos que el de hoy?** (raíz de la TIR/descuento). Orientación: costo de oportunidad (podés invertirlo ya) + inflación + riesgo de no cobrar. Tu intuición de las notas (si espero que algo suba, compro hoy) es el canal *expectativas de inflación*. → discutir con Claude.
- **¿La compra anticipada por miedo a la inflación no aumenta la inflación?** (liquidez / demanda). Pregunta macro válida, no cubierta en la clase. → discutir.
- **¿Se miden los modelos de forma parecida en el riesgo de tasa?** (nota junto a IRS). Sí: duration/DV01 son la misma idea (1ª derivada) medida en distintas unidades. → confirmar en C2 con delta/gamma. ⚡

---

# C2 — Opciones y Black-Scholes

> 🌱 **Sembrada, no completa.** Abajo dejé ~9 entradas "columna vertebral" ya escritas
> como ejemplo del formato, y después la lista de conceptos que faltan (solo el nombre)
> para que los completes vos con las slides o de memoria. Cuando quieras, le pedís a Claude
> que llene cualquiera.
>
> La clase va en 4 bloques: §2 opciones **sin modelo** (todo lo que impone el no-arbitraje)
> → §3 cálculo estocástico (Browniano, Itô, GBM) → §4 Black-Scholes y griegas.

## Entradas sembradas (ejemplo del formato)

### Valor intrínseco / temporal / moneyness  [C2 §2]
Toda prima = **intrínseco** (lo que vale ejercer ya: call `max(S−K,0)`, put `max(K−S,0)`) + **temporal** (el valor de la opcionalidad que queda, ≥0, muere en T). **Moneyness**: ITM (intrínseco>0), ATM (S≈K, todo temporal), OTM (intrínseco=0). El temporal es máximo en ATM y crece con σ y con T.
→ call_put (C1) · griegas · volatilidad_implícita

### Put-Call Parity  [C2 §2]
`C − P = S − K·e^{−rT}` (europeas, sin dividendos). Sale del **no-arbitraje**, vale para *cualquier* modelo de precio. Es el nodo central de "opciones sin modelo": un call menos un put replica un forward sintético.
→ no_arbitraje · box_spread · forward_sintético · tasa_implícita

### Box spread  [C2 §2]
Combinar un spread alcista y uno bajista para cobrar `K₂−K₁` seguro pase lo que pase → es un bono sintético. No es un método aparte: es la **Put-Call Parity restada dos veces**. Esconde una tasa de interés implícita.
→ put_call_parity · tasa_implícita

### Breeden-Litzenberger  [C2 §2]
La **segunda derivada** del precio del call respecto del strike ES la densidad de probabilidad (neutral al riesgo) que el mercado le asigna a `S_T`. Permite pricing sin modelo de cualquier payoff europeo con la distribución que el mercado ya cotiza. El butterfly es su versión discreta.
→ butterfly · convexidad_en_strike · densidad_implícita · smile (C3)

### Lema de Itô  [C2 §3]
La "regla de la cadena" del cálculo estocástico. Para `dX = a·dt + b·dW`, aparece un término extra de segundo orden porque `(dW)² = dt` (variación cuadrática no nula). Motor de todo el pricing de derivados.
→ movimiento_browniano · variación_cuadrática · GBM · Black-Scholes

### GBM (Geometric Brownian Motion)  [C2 §3]
Modelo del precio de una acción: `dS = μS·dt + σS·dW`. Vía Itô da `S_T = S_0·exp((μ−σ²/2)T + σW_T)` → log-normal. Es el supuesto de precio bajo Black-Scholes.
→ lema_de_Itô · Black-Scholes · supuestos_BS

### Black-Scholes  [C2 §4]
Precio de una opción europea bajo GBM + no-arbitraje, vía **delta-hedging** (una cartera opción−Δ·acción es sin riesgo instantáneo) o **pricing neutro al riesgo** (bajo la medida Q, el precio = payoff esperado descontado a r). `C = S·N(d₁) − K·e^{−rT}·N(d₂)`, con `N(d₂)=Q(S_T>K)`.
→ GBM · griegas · volatilidad_implícita · pricing_neutro_al_riesgo · d1_d2

### Griegas  [C2 §4]
Sensibilidades del precio de la opción. **Delta** (∂/∂S, hedge ratio) ⚡ análogo a duration. **Gamma** (∂²/∂S², convexidad) ⚡ análogo a convexidad. **Theta** (∂/∂t, decaimiento). **Vega** (∂/∂σ). **Rho** (∂/∂r). Mismo esqueleto Taylor que bonos/swaps.
→ delta_hedging · duration (C1) ⚡ · convexidad (C1) ⚡ · trading_de_volatilidad

### Volatilidad implícita / smile  [C2 §4]
La σ que, metida en Black-Scholes, reproduce el precio de mercado de la opción. Si BS fuera exacto sería constante en todo strike; **no lo es** → el "smile"/"skew" es la agenda entera de C3.
→ Black-Scholes · smile (C3) · Breeden-Litzenberger

## ⬜ Conceptos de C2 por completar (solo el nombre)

**§2 sin modelo:** europeas vs americanas · cotas de call/put · monotonicidad en strike · spreads verticales (bull/bear) · opción digital · butterfly y convexidad en strike · densidad implícita (usos) · calendar spread (monotonicidad en T) · estrategias direccionales · estrategias de volatilidad (straddle/strangle) · swaptions

**§3 estocástico:** movimiento browniano · integral de Itô · variación cuadrática · teorema de Girsanov (medida P → Q)

**§4 Black-Scholes:** delta-hedging · pricing neutro al riesgo · d₁ y d₂ · PDE de Black-Scholes · trading de volatilidad (vol realizada vs implícita) · P&L del delta-hedger · supuestos de BS y dónde fallan

---

# C3 — Volatilidad, tasas y crédito

> 📥 Material ya subido (vas a la clase primero). Arco: por qué falla Black-Scholes (el smile)
> → vol local (Dupire) → vol estocástica (Heston) → SABR → saltos (Merton jump) →
> modelos de tasa corta (Vasicek/CIR/Hull-White) → crédito estructural (Merton) → mapa de qué desk usa qué.
> *Se completa después de la clase.*

---

# C4 — Factores
⬜ Pendiente. (Riesgo sistemático de C1 y beta reaparecen acá.)

# C5 — Portfolios
⬜ Pendiente.

# C6 — Machine Learning
⬜ Pendiente.

# C7–C8 — Unidad 3 (HFT / baja latencia)
⬜ Pendiente. (Market maker y LOB de C1 reaparecen acá.)

# C9 — Wrap + presentaciones (póster)
⬜ Pendiente.
