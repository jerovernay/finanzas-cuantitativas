# Finanzas Cuantitativas — Mapa de conceptos

> Glosario en red de la materia (Schifani, FCEN-UBA). No es un apunte lineal:
> cada concepto es un nodo corto, y las flechas `→` son las aristas que lo conectan
> con el resto. El valor está en las conexiones, no en las definiciones.

---

## Cómo se usa este archivo

**Fuente de verdad.** Este `.md` es la copia durable de la materia. Vive en tu repo
(VSCode / Claude Code). Cuando arranques un chat nuevo con Claude, pegalo o subilo:
es el mecanismo de continuidad (Claude no recuerda entre conversaciones).


**Formato de una entrada — dos niveles.** Lo que se lee siempre es la primera línea;
el *por qué* y el ejemplo viven plegados, a un clic.
```
### Nombre del concepto  [C2 §2]
La esencia en 1–2 líneas. Fórmula si aplica: \(C - P = S - K e^{-rT}\).

→ concepto_conectado · otro_concepto · idea_transversal

<details><summary>más</summary>

De dónde sale (el argumento, la cuenta clave, la intuición).
Ej.: caso concreto con números o un evento real que muestre por qué importa.

</details>
```

**La tapa de cada clase — `## Idea general`.** Antes del primer concepto, cada `# Ci` abre
con un bloque en prosa, sin fórmulas, con tres partes fijas:
- **En una frase.** Qué idea deja la clase, dicha en palabras simples.
- **Qué problema del mercado resuelve.** Para qué lo usa un desk / por qué existe.
- **Cómo conecta.** Hacia atrás (qué clases anteriores usa), hacia adelante (dónde reaparece)
  y el patrón transversal ⚡ si lo hay.
Es lo que se lee *antes* de entrar a los nodos, y lo que hay que poder repetir de memoria
después. Si no podés resumir la clase en esas tres partes, no la entendiste todavía.

**Convenciones.**
- `[C2 §2]` = clase 2, sección 2 de las slides. Para filtrar una clase, buscá `[C2`.
- `→` = "se conecta con". Es la red. Es lo que hace que esto sea un mapa y no una lista.
- `<details>` = el detalle plegado: el argumento + el `Ej.:`. La lectura rápida no lo abre;
  el estudio sí. En el sitio (`index.html`) es un desplegable; en VSCode se ve como texto.
- Fórmulas en LaTeX con los delimitadores de KaTeX: barra + paréntesis para inline, barra +
  corchete para display (como en el bloque de ejemplo de arriba). No usar `$…$` porque el
  texto usa `$` para montos. Los backticks quedan solo para identificadores (archivos, tags,
  nombres de función).
- `⚡` = arista transversal fuerte (el mismo esqueleto matemático reaparece en otra clase).
- `❓ DUDA` = pregunta abierta tuya, sin resolver todavía.
- En VSCode: el panel *Outline* te da la navegación por concepto (cada `###` es un ítem).

---

## Índice de clases

| Clase | Tema | Estado |
|---|---|---|
| C1 | Introducción: mercados, tasas, FX y curvas | ✅ armada |
| C2 | Opciones y Black-Scholes | ✅ armada |
| C3 | Volatilidad, tasas y crédito | ✅ armada |
| C4 | Factores y series temporales (del mundo Q al mundo P) | ✅ armada |
| C5 | Teoría de portafolio: Markowitz, Black-Litterman y Risk Parity | ✅ armada |
| C6 | Machine Learning: el pipeline riguroso (López de Prado) | ✅ armada |
| C7–C8 | Unidad 3 (HFT / baja latencia) | ⬜ pendiente |
| C9 | Wrap del curso + presentaciones (formato póster) | ⬜ pendiente |

**Hilo conductor del curso:** casi todo el riesgo se lee como una **expansión de Taylor**.
Primera derivada (sensibilidad) y segunda derivada (curvatura) reaparecen con otro nombre
en cada clase: duration/convexidad (bonos) → DV01/convexidad (swaps) → delta/gamma (opciones). ⚡

**Segundo hilo (aparece en C4):** **pocos factores explican muchas series.** Nelson-Siegel
resume toda la curva en nivel/pendiente/curvatura (C1) → Heston resume la superficie de vol en
5 parámetros (C3) → Fama-French resume miles de acciones en 3 factores y PCA *redescubre*
nivel/pendiente/curvatura desde los datos (C4). Lo que cambia es quién elige los factores:
el modelador (paramétrico) o los datos (estadístico). ⚡

**Tercer hilo (C4 → C5 → C6): encoger hacia algo simple.** Ridge encoge un vector de coeficientes hacia
cero (C4) → Ledoit-Wolf encoge la matriz Σ entera hacia la identidad, y Black-Litterman encoge μ
hacia el equilibrio de mercado (C5). Siempre el mismo trade-off sesgo/varianza: un estimador un
poco sesgado pero estable predice mejor que uno "perfecto" pero ruidoso. En C6 el hilo se vuelve
regla de diseño: L1 en la logística, `min_samples_leaf`, dropout; y en finanzas el mínimo del error
de test está *muy* a la izquierda — modelos simples ganan. ⚡

---

# C1 — Introducción: mercados, tasas, FX y curvas

## Idea general

**En una frase.** Todo lo que se opera en un mercado es una promesa de plata futura, y la
herramienta central de la materia es una sola: **descontar** — traducir plata de mañana a
plata de hoy usando la tasa que el mercado exige para ese plazo y ese riesgo.

**Qué problema del mercado resuelve.** Nadie te dice cuánto vale un bono, un swap o un forward
de moneda: hay que *construirlo*. La clase arma la caja de herramientas mínima para eso:
qué instrumentos existen y quién los opera, qué es una tasa (simple, compuesta, continua) y
por qué hay una distinta por cada plazo (la curva), cómo extraer esa curva de los precios que
sí se ven (bootstrapping, Nelson-Siegel) y cómo usarla para ponerle precio a un swap y a un
forward de FX (CIP). El resultado práctico es poder decir "este swap vale X y pierde Y si la
curva sube 1bp".

**Cómo conecta.** Hacia adelante: el factor de descuento \(P(0,T)\) es el ladrillo que usa
*todo* el pricing de C2 y C3 (la \(K\cdot e^{-rT}\) de Black-Scholes es la misma curva). La CIP es
la primera aparición del argumento de **no-arbitraje** — dos caminos sin riesgo tienen que
rendir lo mismo — que en C2 se convierte en la put-call parity. Y calibrar Nelson-Siegel es
el mismo flujo (datos → modelo paramétrico → optimización) que calibrar Heston en C3 ⚡.
El patrón matemático que se repite: duration = primera derivada, convexidad = segunda;
en C2 se van a llamar delta y gamma ⚡.

> Arco de la clase: qué se opera → quién lo opera → el precio del tiempo (tasas) →
> el mercado FX → construir y calibrar la curva de tasas → usarla para valuar un swap.
> Objetivo declarado: *calibrar una curva de rendimientos real y valuar swaps.*

## §2 Instrumentos

### Renta fija / Bono  [C1 §2]
Promesa de flujos futuros. Su precio es el valor presente de esos flujos: \(P = \sum C/(1+y)^t + VN/(1+y)^T\).

→ duration · convexidad · YTM · factor_de_descuento · bootstrapping

<details><summary>más</summary>

El precio no es un dato del emisor sino del mercado: sale de descontar flujos fijos a la tasa \(y\)
que el mercado exige *hoy* para ese riesgo y ese plazo.
Ej.: bono VN 1000, cupón 5% anual, 3 años; si \(y = 6\%\) el precio da ≈ 973 — bajo la par, porque
paga menos que lo que exige el mercado.

</details>

### Duration  [C1 §2]
**Primera derivada** del precio respecto de la tasa: \(\Delta P/P \approx -D_{\text{mod}}\cdot \Delta y\).

→ convexidad · DV01 · delta ⚡ · relación_tasa_precio

<details><summary>más</summary>

*Macaulay*: tiempo promedio ponderado hasta los flujos (en años, el "centro de masa" del bono).
*Modificada* = Macaulay/(1+y): la elasticidad, ya en unidades de "% de precio por punto de tasa".
Ej.: un bono con \(D_{\text{mod}} = 7\) pierde ≈7% de precio si la tasa sube 1 punto — con eso un trader
dimensiona el riesgo sin recalcular el precio entero.

</details>

### Convexidad  [C1 §2]
**Segunda derivada** del precio respecto de la tasa: \(\Delta P/P \approx -D_{\text{mod}}\cdot \Delta y + \tfrac{1}{2}\cdot C\cdot \Delta y^2\).

→ duration · gamma ⚡ · expansión_de_Taylor

<details><summary>más</summary>

Corrige lo que la duration (recta tangente) sobre/subestima. Duration = velocidad,
convexidad = aceleración.
Ej.: ante una baja fuerte de tasas (\(\Delta y = -2\%\)), la sola duration subestima la suba real del
precio; sumar el término de convexidad corrige ese error.

</details>

### Acción / Renta variable  [C1 §2]
Fracción de propiedad de una empresa: derecho sobre ganancias (dividendos) y votos. **No promete flujos.**

→ P/E_ratio · riesgo_sistemático · índices

<details><summary>más</summary>

Es la diferencia estructural con el bono: no hay contrato de pago, hay residuo.
Ej.: comprar una acción de YPF da derecho a cobrar dividendo *si* la empresa reparte y a votar
en la asamblea — nada garantizado.

</details>

### P/E ratio  [C1 §2]
Precio sobre ganancias por acción: cuántos años de ganancias actuales "pagás" por la acción.

→ acción

<details><summary>más</summary>

Proxy grueso de valuación; sirve para comparar, no para valuar.
Ej.: una tech con P/E 25 "cuesta" 25 años de ganancias actuales, vs. un banco con P/E 10.

</details>

### Riesgo sistemático (beta)  [C1 §2]
Sensibilidad de una acción al movimiento del mercado. Es el riesgo que **no** se diversifica.

→ acción · factores (C4) · portfolios (C5)

<details><summary>más</summary>

El riesgo idiosincrático se diluye juntando activos; el sistemático no, por eso es el que se paga.
Ej.: una acción con beta 1.5 se mueve ≈1.5% cuando el índice se mueve 1% — con eso se arma un
hedge o se explica por qué un portfolio es más volátil que el mercado.

</details>

### Commodities  [C1 §2]
Activos físicos fungibles (petróleo, oro, soja). Se operan sobre todo vía futuros.

→ futuros

<details><summary>más</summary>

Ej.: un productor agropecuario vende futuro de soja en Rofex para fijar hoy el precio de la
cosecha que todavía no levantó.

</details>

### Derivados  [C1 §2]
Contratos cuyo valor **deriva** de un subyacente. Tres familias: futuros, swaps, opciones.

→ futuros · swaps · opciones · IRS

<details><summary>más</summary>

**Futuros**: comprar/vender a precio fijo en fecha futura. **Swaps**: intercambio de flujos.
**Opciones**: derecho, no obligación.
Ej.: una opción sobre acciones de Apple no vale nada por sí sola — su precio se mueve porque
se mueve el precio de Apple.

</details>

### Call / Put  [C1 §2]
**Call**: derecho de *comprar* a precio K. **Put**: derecho de *vender*. Europea = solo en T; americana = en cualquier momento ≤ T.

→ opciones (C2) · put_call_parity (C2) · valor_intrínseco (C2)

<details><summary>más</summary>

Lo que se compra es opcionalidad: la pérdida está acotada a la prima, la ganancia no.
Ej.: comprar un call de GGAL strike $100 es apostar a que la acción sube por encima de $100
antes del vencimiento, arriesgando solo la prima.

</details>

### CDS (Credit Default Swap)  [C1 §2]
Seguro de crédito: se paga una prima periódica y se cobra si el emisor entra en default.

→ derivados · crédito_estructural (C3)

<details><summary>más</summary>

Protagonista de la crisis de 2008: permite vender protección sin tener el bono.
Ej.: el CDS sobre deuda soberana argentina se dispara cada vez que sube el riesgo percibido de
default — se usa como termómetro de riesgo país en tiempo real.

</details>

## §3 Actores del mercado

### Actores institucionales  [C1 §3]
Los regulados: bancos centrales (Fed, BCE, BCRA), bancos, fondos mutuos/de pensión, corporativos, aseguradoras.

→ actores_especulativos · market_maker

<details><summary>más</summary>

Muchos operan por mandato (cubrirse, calzar pasivos), no por vista de mercado.
Ej.: el BCRA interviniendo en el mercado de cambios para mover o contener el tipo de cambio.

</details>

### Actores especulativos  [C1 §3]
Buscan retorno tomando riesgo: hedge funds, **market makers** y **arbitrajistas**.

→ market_maker · CIP (arbitraje) · no_arbitraje (C2)

<details><summary>más</summary>

El market maker gana el spread proveyendo liquidez; el arbitrajista explota diferencias de
precio y, al hacerlo, las cierra.
Ej.: un fondo compra el mismo bono más barato en un mercado y lo vende más caro en otro,
embolsando la diferencia hasta que el hueco desaparece.

</details>

### Market maker / liquidez  [C1 §3]
Cotiza compra y venta simultáneas; gana el spread sin tomar posición direccional.

→ payment_for_order_flow · LOB (Unidad 3)

<details><summary>más</summary>

Ej.: cotiza bid $99.5 / ask $100.5 en GGAL — compra a 99.5 y vende a 100.5, ganando $1 de spread.

</details>

### Retail / Payment for order flow  [C1 §3]
El retail pasó a ~20–25% del volumen de acciones en EEUU. **PFOF**: el broker no cobra comisión y le *vende* el flujo de órdenes a un mayorista.

→ market_maker · GameStop (ej. 2021)

<details><summary>más</summary>

La ejecución puede ocurrir fuera de bolsa: el "sin comisión" se paga con el flujo.
Ej.: Robinhood no le cobra al usuario pero le vende sus órdenes a Citadel Securities, que las
ejecuta y le paga por ese flujo.

</details>

## §4 El precio del tiempo: tasas de interés

### Tasa simple / compuesta / continua  [C1 §4]
\(VF = VP(1 + r\cdot t)\) · \(VF = VP(1+r)^t\) · \(VF = VP\cdot e^{rt}\). La continua es la que se usa para descontar en pricing.

→ factor_de_descuento · VPN

<details><summary>más</summary>

La continua es el límite de capitalizar infinitas veces; se elige porque hace la matemática
aditiva en el exponente.
Ej.: $100 a 1 año con r=10%: simple $110, compuesta anual $110, mensual ≈$110.47, continua
≈$110.52 — la brecha crece con el plazo.

</details>

### TNA / TEA / TEM  [C1 §4]
**TNA** = nominal anual (ignora la capitalización). **TEA/TEM** = efectivas (sí la incluyen).

→ tasa_compuesta

<details><summary>más</summary>

Ej.: un plazo fijo con TNA 40% y capitalización mensual rinde una TEA de ≈48% — la TNA sola
subestima lo que efectivamente se cobra.

</details>

### VPN (Valor Presente Neto)  [C1 §4]
\(\text{VPN} = \sum CF_t/(1+r)^t\) con \(CF_0 < 0\). Regla: invertir si VPN > 0.

→ TIR · factor_de_descuento

### TIR / YTM  [C1 §4]
**TIR**: la tasa que hace VPN = 0. **YTM**: la TIR de un bono. No tiene fórmula cerrada.

→ VPN · bono · tasas_spot · limitaciones_TIR

<details><summary>más</summary>

Es *una* tasa promedio que resume muchas (una por plazo): por eso la curva spot es más informativa.
Ej.: un bono VN 1000 que cotiza a $950 — se resuelve numéricamente qué \(y^*\) hace que el VP de
sus flujos dé exactamente 950.

</details>

### Factor de descuento  [C1 §4]
\(P(0,T) = e^{-r(T)\cdot T}\): cuánto vale hoy $1 seguro a recibir en T. Ladrillo de toda valuación.

→ tasas_spot · curva_de_descuento · IRS

<details><summary>más</summary>

Ej.: con r=5% a 2 años, \(P(0,2) = e^{-0.10} \approx 0.905\) — un cupón cero que paga $1 en 2 años vale
hoy 90.5 centavos.

</details>

### Curva de rendimiento  [C1 §4]
La tasa como **función del plazo**, \(r(T)\). Tres formas: normal, flat, invertida.

→ tasas_spot · bootstrapping · Nelson-Siegel · teorías_estructura_temporal

<details><summary>más</summary>

La forma invertida (cortas > largas) es la señal recesiva clásica.
Ej.: la curva de EEUU se invirtió en 2022-2023 y se leyó como anticipo de recesión.

</details>

### Relación tasa ↔ precio  [C1 §4]
**↑ tasa ⇒ ↓ precio** y viceversa. La relación más fundamental de renta fija.

→ bono · duration · curva_de_rendimiento

<details><summary>más</summary>

Es aritmética, no comportamiento: el precio es el VP de flujos fijos, y descontar más fuerte
baja el valor.
Ej.: con la Fed subiendo agresivamente en 2022, los bonos largos del Tesoro cayeron más de 20%.

</details>

### La Fed / federal funds rate  [C1 §4]
Tasa overnight objetivo que fija la Fed; ancla el extremo corto de la curva y se propaga al resto.

→ curva_de_rendimiento · LIBOR_SOFR

<details><summary>más</summary>

Ej.: el ciclo 2022-2023 llevó la tasa de referencia de ~0% a 5.25%–5.50%, encareciendo el
crédito en toda la economía.

</details>

### LIBOR → SOFR  [C1 §4]
De una tasa basada en *encuestas* a bancos (manipulable) a una basada en *transacciones* reales de repo.

→ la_Fed

<details><summary>más</summary>

De la declaración al dato duro: el cambio de referencia es un cambio de fuente de verdad.
Ej.: el escándalo de manipulación del LIBOR (2012, bancos coludiendo para mover la tasa
reportada) fue el motivo concreto de la migración a SOFR.

</details>

## §5 FX (mercado de divisas)

### FX / Forwards de moneda  [C1 §5]
El mercado más grande que existe (~USD 9.6 billones/día). **Forward**: fijar hoy el tipo de cambio F para intercambiar en T.

→ CIP · carry_trade · NDF · spot

<details><summary>más</summary>

Ej.: un importador que va a pagar en USD dentro de 90 días fija hoy el tipo de cambio con un
forward para no quedar expuesto a la suba del dólar.

</details>

### Covered Interest Parity (CIP)  [C1 §5]
Dos rutas sin riesgo deben rendir igual: \(F = S\cdot e^{(r_d - r_f)\cdot T}\). El forward **no es una predicción**.

→ no_arbitraje (C2) · forwards · carry_trade · arbitrajistas

<details><summary>más</summary>

Es aritmética de las tasas de hoy: quedarse en USD vs. pasar por otra moneda y volver con
forward tiene que dar lo mismo, o hay arbitraje. Se puede invertir para extraer la tasa implícita.
Ej.: a partir del spot USD/ARS y las tasas en pesos y en dólares se calcula el forward "justo";
si el mercado cotiza otro, ahí hay una tasa implícita distinta a la observada.

</details>

### Carry trade  [C1 §5]
La versión **sin cobertura** de la CIP: fondearse en moneda de tasa baja e invertir en la de tasa alta.

→ CIP · FX

<details><summary>más</summary>

Gana el diferencial mientras el tipo de cambio no se mueva en contra: es vender seguro contra
el salto cambiario.
Ej.: el unwind del carry en yenes de agosto 2024 — el BoJ subió tasas, el yen se apreció de
golpe y forzó a cerrar posiciones fondeadas en JPY con pérdidas.

</details>

### NDF (Non-Deliverable Forward)  [C1 §5]
Forward que se liquida por diferencia en USD, sin entrega física de la moneda.

→ forwards · FX

<details><summary>más</summary>

Existe justamente para monedas con restricciones cambiarias, como el ARS.
Ej.: una empresa se cubre del ARS con un NDF que liquida en USD la diferencia de tipo de cambio,
evitando el cepo que impide operar el forward físico de pesos.

</details>

## §6 Construir y calibrar la curva

### Tasas spot / forward / descuento  [C1 §6]
Tres vistas de la misma curva: \(r(T)\) (invertir hoy hasta T), la tasa futura implícita entre dos plazos, y \(P(0,T)\).

→ factor_de_descuento · bootstrapping · YTM

<details><summary>más</summary>

Si conocés una, conocés las tres: son transformaciones algebraicas.
Ej.: con spot a 1 año de 4% y a 2 de 5%, se despeja la forward implícita "entre el año 1 y el 2",
la tasa que el mercado ya está cotizando para ese tramo.

</details>

### Teorías de la estructura temporal  [C1 §6]
Por qué la curva tiene la forma que tiene: **expectativas puras**, **prima de liquidez**, **segmentación**.

→ curva_de_rendimiento

<details><summary>más</summary>

Expectativas: la tasa larga es el promedio de las cortas esperadas. Liquidez: los plazos largos
pagan extra por inmovilizar. Segmentación: cada tramo tiene su propia oferta y demanda.
Ej.: una curva invertida se explica, bajo expectativas puras, porque el mercado espera que las
tasas cortas bajen (anticipando una recesión).

</details>

### Bootstrapping  [C1 §6]
Extracción **iterativa** de tasas spot a partir de bonos con cupón, plazo por plazo.

→ tasas_spot · Nelson-Siegel · curva_de_rendimiento

<details><summary>más</summary>

El problema: un bono con cupón mezcla flujos de muchas fechas, así que su precio no da una spot
directo. La solución es en cascada: usás las spot ya conocidas para descontar los cupones
intermedios y despejás la que falta.
Ej.: para la spot a 2 años, primero descontás el cupón del año 1 con la spot a 1 año (ya conocida)
y con lo que queda despejás la de 2.

</details>

### Nelson-Siegel / NSS  [C1 §6]
Modelo **paramétrico** de la curva: NS con 4 parámetros (nivel + pendiente + curvatura), NSS con 6.

→ bootstrapping · calibración ⚡ · riesgo_país (spread de curvas)

<details><summary>más</summary>

Calibrar = mínimos cuadrados no lineales, el mismo flujo que ajustar un modelo en física
(datos → modelo paramétrico → optimización). Regla: parámetros < puntos, o estás interpolando
en vez de ajustando.
Ej.: ajustar la curva soberana argentina (decenas de bonos, precios ruidosos) con 4-6 parámetros
da una curva suave en vez de un zigzag punto por punto.

</details>

### Z-spread / OAS  [C1 §6]
Cuánto hay que sumarle a *cada* tasa spot del gobierno para que los flujos del bono descuenten a su precio de mercado.

→ tasas_spot · CDS · crédito_estructural (C3)

<details><summary>más</summary>

Es el spread de crédito "limpio": a diferencia de comparar YTMs, no mezcla el efecto de la
forma de la curva.
Ej.: un corporativo con Z-spread de 300bp sobre el Tesoro — esos 300bp son el precio que el
mercado le pone al riesgo de crédito de esa empresa.

</details>

## §7 Interest Rate Swaps

### IRS (Interest Rate Swap)  [C1 §7]
Intercambio OTC de flujos sobre un nocional N (que no se transfiere). El *payer* paga fija y recibe flotante.

→ par_swap_rate · DV01 · MTM · curva_de_descuento · swaptions (C2)

<details><summary>más</summary>

Valor = pata flotante − pata fija, ambas descontadas con la curva; se valúa como un portafolio
de FRAs.
Ej.: una empresa con deuda a tasa variable entra en un IRS pagando fija y recibiendo flotante,
para dejar de estar expuesta a que la Fed siga subiendo.

</details>

### Par swap rate  [C1 §7]
La tasa fija \(c^*\) que hace el swap valer 0 al inicio: \(c^* = (1 - P(0,T)) / \sum \delta _i\cdot P(0,t_i)\).

→ IRS · MTM

<details><summary>más</summary>

Ej.: al armar un swap a 5 años hoy se calcula \(c^*\) con la curva de descuento vigente — es la
tasa fija "justa" para que ninguna parte pague de más al firmar.

</details>

### DV01 / MTM  [C1 §7]
**MTM**: el swap arranca en 0 y después gana o pierde valor. **DV01**: cuánto cambia ese valor ante 1bp de movimiento paralelo de la curva.

→ duration ⚡ · IRS · convexidad

<details><summary>más</summary>

El DV01 es la duration del swap medida en dólares: misma primera derivada, otras unidades.
Ej.: un swap con DV01 de $5.000 pierde $500.000 de MTM si la curva entera sube 100bp — así se
traduce el riesgo de tasa a dinero concreto.

</details>

## ❓ Dudas abiertas de C1

- **¿Por qué el dinero futuro vale menos que el de hoy?** (raíz de la TIR/descuento). Orientación: costo de oportunidad (podés invertirlo ya) + inflación + riesgo de no cobrar. Tu intuición de las notas (si espero que algo suba, compro hoy) es el canal *expectativas de inflación*. → discutir con Claude.
- **¿La compra anticipada por miedo a la inflación no aumenta la inflación?** (liquidez / demanda). Pregunta macro válida, no cubierta en la clase. → discutir.
- **¿Se miden los modelos de forma parecida en el riesgo de tasa?** (nota junto a IRS). Sí: duration/DV01 son la misma idea (1ª derivada) medida en distintas unidades. Confirmado en C2: delta y gamma son exactamente lo mismo sobre S. ⚡

---

# C2 — Opciones y Black-Scholes

## Idea general

**En una frase.** Una opción es un *derecho* (no una obligación), y ponerle precio a un
derecho es el problema fundacional de las finanzas cuantitativas: la clase muestra primero
todo lo que se puede decir **sin ningún modelo** (solo prohibiendo la plata gratis) y después
el primer modelo que da un número, Black-Scholes.

**Qué problema del mercado resuelve.** Cómo se le pone precio a las cosas en el mercado. La
respuesta tiene dos capas. La primera es el **no-arbitraje**: si dos portafolios pagan lo
mismo pase lo que pase, tienen que costar lo mismo hoy — de ahí salen las cotas, la put-call
parity, y la sorpresa de que los precios de las opciones ya contienen una distribución de
probabilidad completa (Breeden-Litzenberger). La segunda capa es el **modelo**: para pasar de
cotas a un precio hace falta describir cómo se mueve el subyacente (browniano, Itô, GBM) y ahí
aparece la idea clave del curso — cubrir el riesgo con delta-hedging hace que el precio no
dependa de cuánto creés que va a subir la acción, solo de cuánto se *mueve* (σ). Las griegas
son el tablero de ese hedge: qué riesgo te queda después de cubrir la dirección.

**Cómo conecta.** Hacia atrás: la tasa \(r\) y el descuento vienen de C1; la tasa implícita
de las opciones es la CIP con otro disfraz ⚡; delta y gamma son duration y convexidad ⚡.
Hacia adelante: la clase termina mostrando que la σ de Black-Scholes **no es constante**
(smile), y eso es literalmente el programa de C3. Volatilidad como cosa que se compra y se
vende (trading de vol) es el puente entre pricing y trading que reaparece en toda la Unidad 2.

> Arco de la clase — *Pricing I: del no-arbitraje al primer modelo.* Cuatro bloques, y el
> orden es el argumento: §1 repaso de bonos (la Taylor que vuelve) → §2 todo lo que se puede
> decir de las opciones **sin modelo** (solo no-arbitraje) → §3 el aparato estocástico
> (Browniano, Itô, GBM, Girsanov) → §4 Black-Scholes, las griegas y por qué su σ constante
> es mentira.

## §1 Bonos: repaso

### Precio, duration y convexidad como derivadas  [C2 §1]
\(D_{\text{mod}} = -(1/P)\cdot \partial P/\partial y\), \(C = (1/P)\cdot \partial ^2P/\partial y^2\), y \(\Delta P/P \approx -D_{\text{mod}}\cdot \Delta y + \tfrac{1}{2}\cdot C\cdot (\Delta y)^2\).

→ duration (C1) · convexidad (C1) · delta ⚡ · gamma ⚡ · expansión_de_Taylor

<details><summary>más</summary>

La clase arranca acá a propósito: la sensibilidad primera y la segunda **son** delta y gamma
cuando el subyacente pasa de ser la tasa a ser el precio de una acción. Mismo esqueleto,
otro nombre.
Ej.: el trader de bonos que piensa en \(D_{\text{mod}}\) y \(C\) y el de opciones que piensa en \(\Delta\) y \(\Gamma\)
están haciendo la misma expansión de Taylor sobre variables distintas.

</details>

## §2 Opciones sin modelo: cotas, paridad y estrategias

### Europea vs. americana (y Bermuda)  [C2 §2]
Europea: se ejerce **solo** en T. Americana: en cualquier momento ≤ T. Bermuda: en fechas discretas prefijadas.

→ call_put (C1) · cotas · put_call_parity · ejercicio_anticipado

<details><summary>más</summary>

Más derechos nunca valen menos: \(C_{\text{am}} \ge C_{\text{eur}}\) y \(P_{\text{am}} \ge P_{\text{eur}}\). Es no-arbitraje puro, no hace
falta ningún modelo para afirmarlo.
Ej.: los índices (SPX) y el FX suelen cotizar opciones europeas; las acciones individuales,
americanas — el tipo cambia qué cotas y qué paridad podés usar.

</details>

### Valor intrínseco / temporal / moneyness  [C2 §2]
Prima = **intrínseco** (\(\max(S-K,0)\) o \(\max(K-S,0)\)) + **temporal** (lo que se paga por la opcionalidad restante, ≥ 0, muere en T).

→ griegas · theta · volatilidad_implícita

<details><summary>más</summary>

**Moneyness**: ITM (intrínseco > 0), ATM (S ≈ K, todo el precio es temporal), OTM (intrínseco = 0,
100% temporal). Tres propiedades del valor temporal: es **máximo en ATM** (es donde la
incertidumbre sobre "termina ITM o no" vale más), **crece con σ y con T**, y **cae hacia el
vencimiento** (eso es theta).
Ej.: vender una opción es cobrar valor temporal hoy a cambio de asumir el riesgo del payoff.

</details>

### Cotas de call y put  [C2 §2]
\(\max(S - Ke^{-rT}, 0) \le C \le S\) y \(\max(Ke^{-rT} - S, 0) \le P \le Ke^{-rT}\). Libres de modelo.

→ no_arbitraje · put_call_parity · ejercicio_anticipado

<details><summary>más</summary>

El techo \(C \le S\): el derecho a comprar el activo no puede valer más que el activo. El piso sale
de un portafolio: si \(C < S - Ke^{-rT}\), comprás la call, vendés en corto la acción y prestás
\(Ke^{-rT}\) — te embolsás plata hoy y en T el resultado nunca es negativo. Plata gratis, prohibido.
Ej.: con r > 0 vale \(C \ge S - Ke^{-rT} > S - K\), o sea la call europea siempre vale **más** que su
intrínseco; por eso nunca conviene ejercer anticipadamente una call americana sin dividendos.

</details>

### Put-Call Parity (PCP)  [C2 §2]
\(C - P = S - K\cdot e^{-rT}\) (europeas, sin dividendos). Vale para **cualquier** modelo de precios.

→ no_arbitraje · box_spread · forward_sintético · tasa_implícita

<details><summary>más</summary>

Prueba por replicación: el portafolio A (call + \(K\cdot e^{-rT}\) invertido a la tasa libre de riesgo)
y el B (put + acción) valen los dos \(\max(S_T, K)\) en T. Mismo payoff ⇒ mismo precio hoy.
Consecuencias: el put se despeja del call (\(P = C - S + Ke^{-rT}\)); un call largo + un put corto
del mismo strike **es** un forward sintético, y sale "sin capital" solo si \(K = F = S\cdot e^{rT}\).
Para americanas no es igualdad sino desigualdad: \(C_{\text{am}} - P_{\text{am}} \in [S - K, S - Ke^{-rT}]\).
Ej.: si el mercado cotiza \(C - P \ne S - Ke^{-rT}\), comprás el lado barato y vendés el caro:
arbitraje instantáneo sin riesgo.

</details>

### Monotonicidad en strike  [C2 §2]
\(K_1 < K_2 \Rightarrow C(K_1) \ge C(K_2)\) (y el put al revés): el derecho a comprar más barato vale más.

→ spreads_verticales · convexidad_en_strike · densidad_implícita

<details><summary>más</summary>

**La prueba es una estrategia**: armá el bull call spread \(+C(K_1) - C(K_2)\). Su payoff vale 0
hasta K₁, sube 1 a 1 y se clava en \(K_2-K_1\): **nunca es negativo**. Un payoff que nunca es
negativo no puede costar menos que 0 hoy, y ese costo es exactamente \(C(K_1) - C(K_2) \ge 0\).
El bear put spread \(+P(K_2) - P(K_1)\) es el argumento espejo para el put.
Ej.: es la respuesta a la duda de tus notas — no hay que "ver" la monotonicidad, se demuestra
construyendo el portafolio cuyo precio *es* la diferencia.

</details>

### Spreads verticales (bull call / bear put)  [C2 §2]
\(+C(K_1) - C(K_2)\) (alcista) y \(+P(K_2) - P(K_1)\) (bajista). Payoff acotado entre 0 y \(K_2-K_1\) ⇒ precio acotado igual.

→ monotonicidad_en_strike · opción_digital · box_spread · estrategias_direccionales

<details><summary>más</summary>

De la cota del payoff sale la cota del precio: \(0 \le C(K_1) - C(K_2) \le (K_2-K_1)\cdot e^{-rT}\).
Los dos cuestan menos que la opción sola: pagás por direccionalidad, no por convexidad ilimitada.
Ej.: vendés el upside que no creés que vaya a ocurrir (la call de strike alto) para financiar
el que sí creés.

</details>

### Opción digital (el spread angosto)  [C2 §2]
Achicá el vertical y normalizá por su ancho: \(-\partial C/\partial K = e^{-rT}\cdot Q(S_T > K)\).

→ spreads_verticales · breeden_litzenberger · densidad_implícita

<details><summary>más</summary>

El payoff normalizado del bull call spread converge a un escalón en K₁: eso **es** una apuesta
binaria (paga 1 si \(S_T > K\), 0 si no). Y su precio es, literalmente, la probabilidad neutral al
riesgo de terminar ITM, descontada.
Ej.: la pendiente de la curva \(C(K)\) que ves en pantalla ya te está diciendo qué probabilidad
le asigna el mercado a cada nivel de precio.

</details>

### Tasa implícita (conversion / reversal)  [C2 §2]
Despejando la PCP: \(e^{-r_{\text{impl}}\cdot T} = (S - C + P)/K\). Las opciones esconden una tasa de interés.

→ put_call_parity · box_spread · CIP (C1) ⚡

<details><summary>más</summary>

Si esa tasa difiere de la del mercado, hay una posición que la arbitra: **conversion**
(\(r_{\text{impl}} > r_{\text{mkt}}\): vendés sintético \(+P-C\) y comprás la acción ⇒ prestás a la tasa alta) y
**reversal** (\(r_{\text{impl}} < r_{\text{mkt}}\): comprás sintético y vendés la acción en corto ⇒ te fondeás barato).
Ej.: es la misma estructura lógica que la CIP de C1 — dos caminos sin riesgo tienen que rendir
lo mismo, y si no, la diferencia se cobra.

</details>

### Box spread  [C2 §2]
Bull call + bear put con los mismos strikes: paga \(K_2 - K_1\) **pase lo que pase**. Es un bono cupón cero disfrazado: \(\text{Precio} = (K_2-K_1)\cdot e^{-rT}\).

→ put_call_parity · tasa_implícita · spreads_verticales

<details><summary>más</summary>

No es un método aparte: es la **PCP restada dos veces**. Escribí la paridad en K₁ y en K₂ y restá:
la S se cancela y queda \([C(K_1)-C(K_2)] + [P(K_2)-P(K_1)] = (K_2-K_1)e^{-rT}\).
Justamente porque la S se cancela, en índices se prefiere el box: no hace falta un spot tradeable
ni ajustar por dividendos, solo precios de opciones. Solo con europeas: el ejercicio anticipado
rompe el "garantizado".
Ej.: la tasa del box de SPX sigue de cerca a la curva del Tesoro — dos mercados, una sola tasa.

</details>

### Convexidad en strike y butterfly  [C2 §2]
\(C(K_1) - 2C(K_2) + C(K_3) \ge 0\): \(C(K)\) es **convexa** en el strike.

→ breeden_litzenberger · monotonicidad_en_strike · densidad_implícita

<details><summary>más</summary>

Otra vez la prueba es una estrategia: el butterfly tiene payoff triangular, ≥ 0 en todo escenario,
así que su precio hoy también es ≥ 0 — y ese precio es exactamente la diferencia segunda.
Si un butterfly cotizara negativo sería plata gratis; por eso nunca lo vas a ver.
Ej.: monotonicidad = primera derivada acotada, convexidad = segunda ≥ 0. Las dos juntas son la
condición para que lo que sale de los precios sea una densidad válida.

</details>

### Breeden-Litzenberger  [C2 §2]
\(\partial ^2C/\partial K^2 = e^{-rT}\cdot q(K)\): la segunda derivada del call en el strike **es** la densidad neutral al riesgo de \(S_T\).

→ butterfly · densidad_implícita · smile (C3) · vol_local (C3)

<details><summary>más</summary>

Sale de derivar dos veces \(C(K) = e^{-rT}\int _K^\infty (S-K)q(S)dS\): la primera derivada da
\(-e^{-rT}Q(S_T>K)\) (la digital), la segunda da la densidad. El butterfly angosto es esa derivada
**ejecutada con dinero real**: \(q(K) = \lim_{h\to 0} e^{rT}\cdot [C(K-h) - 2C(K) + C(K+h)]/h^2\).
Sí, el precio del butterfly tiende a 0, pero precio/h² no: es un 0/0 que converge a una densidad.
Ej.: el mercado de opciones publica, strike a strike, una distribución de probabilidad completa
— no hay que suponer ninguna.

</details>

### Usos de la densidad implícita  [C2 §2]
Pricing **sin modelo** de cualquier payoff europeo: \(\text{Precio} = e^{-rT}\int \text{payoff}(S)\cdot q(S)dS\).

→ breeden_litzenberger · VIX · smile (C3)

<details><summary>más</summary>

No hace falta asumir GBM ni calibrar Heston: usás la \(q(K)\) que sale de la cadena de opciones.
Además la densidad muestra la **forma** del miedo, no solo su tamaño: una cola izquierda gorda vs.
la lognormal significa que el mercado cobra prima extra por asegurar contra un crash.
Ej.: el VIX es un caso particular de esta idea — se construye integrando una franja de precios
OTM en todos los strikes, sin modelo.

</details>

### Calendar spread / monotonicidad en T  [C2 §2]
\(T_1 < T_2 \Rightarrow C(K,T_1) \le C(K,T_2)\): más plazo nunca vale menos. El calendar es \(-C(K,T_1) + C(K,T_2)\).

→ theta · trading_de_volatilidad · smile (C3) · no_arbitraje

<details><summary>más</summary>

La opción larga "contiene" a la corta (ojo: con dividendos entre T₁ y T₂ la monotonicidad puede
fallar para europeas). Como estrategia: vendés la corta, comprás la larga, y ganás si S se queda
cerca de K hasta T₁ — cobrás la theta de la corta y la larga sobrevive. Es un trade sobre la
**estructura temporal de la vol**.
Ej.: junto con el butterfly, esto define el no-arbitraje sobre la superficie completa
\(\sigma _{\text{impl}}(K,T)\): convexidad en K y monotonicidad en T.

</details>

### Estrategias direccionales  [C2 §2]
Combinar opciones con el subyacente: **covered call** (acción + call vendida), **protective put** (acción + put comprado), **collar** (las dos).

→ spreads_verticales · put_call_parity · griegas

<details><summary>más</summary>

Covered call: cobrás prima y resignás el rally. Protective put: un seguro con franquicia (el strike).
Collar: protección financiada, costo ≈ 0.
La PCP recicla todo: covered call = short put sintético (mismo payoff). Toda estrategia tiene un
sintético, y si cotizan distinto hay arbitraje.
Ej.: si "vender puts" te suena más riesgoso que "covered call", la paridad dice que es la misma
posición.

</details>

### Estrategias de volatilidad  [C2 §2]
La dirección no importa: **straddle** \(+C(K) + P(K)\) (ATM, payoff \(|S_T - K|\)) y **strangle** \(+C(K_2) + P(K_1)\) (OTM, más barato).

→ trading_de_volatilidad · vega · butterfly · straddle

<details><summary>más</summary>

El straddle gana si \(|S_T - K|\) supera la suma de las dos primas; el strangle cuesta menos pero
necesita un movimiento mayor. Son la forma directa de comprar (o vender) movimiento.
Ej.: comprar un straddle antes de un balance es apostar a que el salto va a ser más grande que
lo que el mercado ya cobró en las primas.

</details>

### Swaptions  [C2 §2]
Opción sobre un swap: derecho a entrar en un IRS desde T₀ en condiciones pactadas hoy.

→ IRS (C1) · call_put · SABR (C3)

<details><summary>más</summary>

**Payer swaption**: derecho a entrar pagando fija (te cubrís si las tasas suben).
**Receiver**: derecho a entrar recibiendo fija (si bajan). Parámetros: strike = tasa fija \(c_K\),
y el tenor del swap subyacente (\(T_n - T_0\)).
Ej.: es el puente natural entre las opciones de esta clase y las tasas de C1 — y el mercado más
grande que casi nadie ve.

</details>

## §3 Procesos estocásticos y cálculo de Itô

### Por qué una ecuación diferencial estocástica  [C2 §3]
\(dX = a(X,t)dt + b(X,t)dW\): **drift** (tendencia determinista) + **difusión** (amplitud del ruido) × incremento browniano.

→ movimiento_browniano · lema_de_Itô · GBM · CIR (C3)

<details><summary>más</summary>

Una ODE (\(dS/dt = \mu S\)) da \(S(t) = S_0e^{\mu t}\): precio determinista, sin incertidumbre. La realidad
es que el precio de mañana es incierto, y la EDE es la forma mínima de meter esa aleatoriedad
sin perder la estructura.
Ej.: los dos casos del curso: GBM (\(dS = \mu S dt + \sigma S dW\)) para pricing de acciones, y CIR
(\(dr = \kappa (\theta -r)dt + \sigma \sqrt{r} dW\)) para tasas.

</details>

### Movimiento browniano  [C2 §3]
\(W_0 = 0\), incrementos independientes y estacionarios (\(W_t - W_s \sim N(0, t-s)\)), trayectorias continuas.

→ variación_cuadrática · integral_de_Itô · martingala

<details><summary>más</summary>

Propiedades derivadas: es una **martingala** (\(E[W_t | F_s] = W_s\): el mejor pronóstico es el
valor de hoy, sin ventaja) y \(\mathrm{Var}(W_t) = t\), la incertidumbre crece linealmente con el tiempo
(el desvío, como √t).
Lo paradójico y fundamental: **no es diferenciable en ningún punto**, así que \(dW\) no es una
derivada ordinaria, y su variación total es infinita.
Ej.: la dispersión de un haz de trayectorias simuladas crece como √t, no como t — es lo que se
ve en las bandas de la simulación.

</details>

### Variación cuadrática: \((dW)^2 = dt\)  [C2 §3]
Para una función suave la variación cuadrática es 0; para el browniano vale \([W]_T = T\). Ese único hecho genera todo el cálculo de Itô.

→ movimiento_browniano · lema_de_Itô · integral_de_Itô

<details><summary>más</summary>

Con \(Q_n = \sum (W_{t_i} - W_{t_{i-1}})^2\) sale \(E[Q_n] = T\) y \(\mathrm{Var}(Q_n) = 2T^2/n \to 0\): converge a T
en media cuadrática, **trayectoria por trayectoria**, no solo en promedio.
La razón: \(\Delta W\) es de orden \(\sqrt{\Delta t}\) (su desvío), así que \((\Delta W)^2\) es de orden \(\Delta t\), no \((\Delta t)^2\).
En cálculo ordinario \((dx)^2\) se va a 0 más rápido que \(dt\) y se descarta; acá no.
Ej.: de ahí sale la tabla de multiplicación del cálculo estocástico: \(dt\cdot dt = 0\), \(dt\cdot dW = 0\),
\(dW\cdot dW = dt\).

</details>

### Integral de Itô  [C2 §3]
\(\int f dW := \lim \sum f(W_{t_k}, t_k)\cdot (W_{t_{k+1}} - W_{t_k})\): se evalúa \(f\) en el extremo **izquierdo** de cada intervalo.

→ variación_cuadrática · martingala · lema_de_Itô

<details><summary>más</summary>

El problema: la integral de Riemann-Stieltjes solo converge si \(g\) tiene variación total finita,
y el browniano no la tiene — evaluar \(f\) a izquierda, a derecha o en el medio daría **tres
límites distintos**. Itô (1944) no arregla el problema: elige una convención y la vuelve la
definición.
Elegir el extremo izquierdo tiene sentido financiero: \(f\) no puede "ver" el incremento futuro
(es no-anticipante), y como consecuencia la integral es una martingala: \(E[\int f dW] = 0\).
Ej.: es la formalización de "decidís la posición con la información de hoy y el mercado se mueve
después".

</details>

### Lema de Itô  [C2 §3]
La regla de la cadena estocástica: \(dF = [\partial F/\partial t + a\cdot \partial F/\partial x + \tfrac{1}{2}b^2\cdot \partial ^2F/\partial x^2]dt + b\cdot \partial F/\partial x\cdot dW\).

→ variación_cuadrática · GBM · Black-Scholes · delta_hedging

<details><summary>más</summary>

Sale de una Taylor a segundo orden sobre \(F(X,t)\): el término \(\tfrac{1}{2}\cdot \partial ^2F/\partial x^2\cdot (dX)^2\) no se anula
porque \((dX)^2 = b^2dt\). Ese \(\tfrac{1}{2}b^2\cdot \partial ^2F/\partial x^2\) es **el término de Itô**, la corrección de convexidad
estocástica; en cálculo ordinario no existe.
Ej.: es el motor de todo el pricing de derivados — sin el lema no hay ni PDE ni fórmula de
Black-Scholes.

</details>

### GBM y su solución exacta  [C2 §3]
\(dS = \mu S dt + \sigma S dW\) ⇒ \(S_T = S_0\cdot \exp[(\mu - \sigma ^2/2)T + \sigma \sqrt{T}\cdot Z]\), con \(Z \sim N(0,1)\). El precio es log-normal, nunca negativo.

→ lema_de_Itô · Black-Scholes · supuestos_BS

<details><summary>más</summary>

Se resuelve aplicando Itô a \(F = \ln S\): los términos dan \(d[\ln S] = (\mu - \sigma ^2/2)dt + \sigma dW\), que ya
es integrable directo. La corrección \(-\sigma ^2/2\) es el término de Itô: sin él, la esperanza saldría
mal. Ojo con la lectura: \(E[S_T] = S_0e^{\mu T}\) — μ es la tasa de crecimiento del precio, no la del
log-precio.
Ej.: simular con \(S_0=100, \mu =8\%, \sigma =20\%\) a un año da una distribución sesgada a la derecha, con
cola larga hacia arriba y piso en 0.

</details>

### Teorema de Girsanov (P → Q)  [C2 §3]
Existe una medida **Q** bajo la cual el drift de todos los activos es \(r\): \(dS = rS dt + \sigma S d\tilde W\).

→ pricing_neutro_al_riesgo · Black-Scholes · d1_d2

<details><summary>más</summary>

Bajo la medida real P el drift μ depende de las preferencias de los inversores — imposible de
estimar y distinto para cada uno. Girsanov (1960) permite cambiar de medida con
\(\tilde W_t = W_t + ((\mu -r)/\sigma )\cdot t\), donde \((\mu -r)/\sigma\) es el **precio de mercado del riesgo**.
Consecuencia directa: para pricear no hace falta conocer μ, solo \(r\) y \(\sigma\) (observables). El
precio de cualquier derivado es \(V = e^{-r(T-t)}\cdot E^Q[\text{payoff}]\).
Ej.: es por eso que μ **no aparece** en la fórmula de Black-Scholes, cosa que de otro modo
sería sospechosa.

</details>

## §4 Black-Scholes: pricing de opciones europeas

### Delta-hedging y la PDE de Black-Scholes  [C2 §4]
Portafolio \(\Pi = C - \Delta \cdot S\) con \(\Delta = \partial C/\partial S\): el \(dW\) se cancela ⇒ debe rendir \(r\) ⇒ \(\partial C/\partial t + \tfrac{1}{2}\sigma ^2S^2\cdot \partial ^2C/\partial S^2 + rS\cdot \partial C/\partial S - rC = 0\).

→ lema_de_Itô · griegas · pricing_neutro_al_riesgo · PDE

<details><summary>más</summary>

Es la ruta alternativa a Girsanov, y funciona bajo la medida **real** P. Aplicás Itô a \(C(S,t)\),
armás la cartera, y elegir \(\Delta = \partial C/\partial S\) cancela el término aleatorio; de yapa cancela también el
término de μ (cubrir el riesgo cancela la prima de riesgo). Lo que queda es un portafolio sin
riesgo instantáneo, y por no-arbitraje tiene que rendir la tasa libre de riesgo.
Ej.: **la respuesta a tu duda de las notas**: no es que "la opción venda acciones". Sos vos quien
vende Δ acciones *contra* la opción que tenés; la opción queda intacta y el paquete es el que
queda inmunizado.

</details>

### Pricing neutro al riesgo  [C2 §4]
\(V(S,t) = e^{-r(T-t)}\cdot E^Q[\text{payoff}(S_T)]\). Con \(S_T\) log-normal bajo Q, la integral da la fórmula cerrada.

→ girsanov · Black-Scholes · d1_d2

<details><summary>más</summary>

\(C = e^{-rT}E^Q[(S_T-K)\cdot \mathbf{1}_{S_T>K}] = e^{-rT}[E^Q[S_T\cdot \mathbf{1}_{S_T>K}] - K\cdot Q(S_T>K)]\): los dos términos
de la fórmula son exactamente esos dos pedazos.
**Por qué no alcanza con descontar \(E^P\) a la tasa r**: P y Q le asignan pesos distintos a los
mismos resultados, así que las esperanzas ya difieren antes de descontar; y \(r\) es la tasa
correcta solo bajo Q. Usar μ tampoco sirve: la opción no tiene el riesgo de la acción, tiene el
riesgo de la acción **apalancado** (\(r_{\text{opción}} = r + \Omega (\mu -r)\), con \(\Omega = \Delta S/C\)).
Ej.: descontar \(E^P[\text{payoff}]\) a \(r\) da un precio sobrevaluado, no un error de redondeo.

</details>

### Fórmula de Black-Scholes  [C2 §4]
\(C = S\cdot N(d_1) - K\cdot e^{-rT}\cdot N(d_2)\) y \(P = K\cdot e^{-rT}\cdot N(-d_2) - S\cdot N(-d_1)\).

→ d1_d2 · griegas · volatilidad_implícita · supuestos_BS

<details><summary>más</summary>

Black & Scholes (1973, Nobel 1997). La PDE con condición de frontera \(C(S,T) = \max(S-K,0)\) se
transforma, con un cambio de variables, en la ecuación del calor de la física.
Lectura probabilística: \(S\cdot N(d_1)\) es el valor esperado del subyacente si la call termina ITM, y
\(K\cdot e^{-rT}\cdot N(d_2)\) es el VP del strike ponderado por la probabilidad de ejercicio bajo Q.
Ej.: en toda la fórmula aparecen solo \(S, K, r, \sigma , T\). μ no está — exactamente lo que Girsanov
había anticipado.

</details>

### d₁ y d₂  [C2 §4]
\(d_2 = [\ln(S/K) + (r - \sigma ^2/2)T]/(\sigma \sqrt{T})\) y \(d_1 = d_2 + \sigma \sqrt{T}\). \(N(d_2) = Q(S_T > K)\).

→ Black-Scholes · pricing_neutro_al_riesgo · delta

<details><summary>más</summary>

\(d_2\) es el límite de integración escrito en unidades de z: la condición \(S_T > K\) traducida a
"cuántos desvíos estándar". \(d_1\) aparece al integrar el término \(S_T\cdot \mathbf{1}_{S_T>K}\): multiplicar la
densidad normal por \(S_T\) "completa el cuadrado" y corre la gaussiana en \(\sigma \sqrt{T}\).
Cada pedazo: \(\ln(S/K)\) es el moneyness en escala log; \((r \pm \sigma ^2/2)T\) es el drift esperado del
log-precio bajo Q; dividir por \(\sigma \sqrt{T}\) normaliza todo a un z-score.
Ej.: por eso \(N(d_1)\) termina siendo el delta y \(N(d_2)\) la probabilidad de ejercicio: son la misma
integral evaluada en límites corridos.

</details>

### Griegas I — Delta  [C2 §4]
\(\Delta = \partial C/\partial S = N(d_1) \in (0,1)\) para el call. OTM → ≈0; ATM → ≈0.5; deep ITM → ≈1.

→ delta_hedging · duration (C1) ⚡ · gamma

<details><summary>más</summary>

Es a la vez la sensibilidad y el **hedge ratio**: cuántas acciones vender para inmunizar la
opción (el mismo Δ que salió al derivar la PDE). Una call con \(\Delta = 0.30\) se mueve, localmente,
como 0.30 acciones.
Ej.: delta-neutral **no** es sin riesgo: queda toda la exposición de orden superior (gamma, vega).

</details>

### Griegas II — Gamma y Theta  [C2 §4]
\(\Gamma = \partial ^2C/\partial S^2 = n(d_1)/(S\cdot \sigma \cdot \sqrt{T})\) (la convexidad) y \(\Theta = \partial C/\partial t\) (el decaimiento).

→ convexidad (C1) ⚡ · trading_de_volatilidad · P&L_del_delta_hedger

<details><summary>más</summary>

Gamma es cuánto cambia el delta cuando se mueve S, o sea cuánto rebalanceo exige el hedge: máxima
ATM y explota cuando \(T \to 0\). Estar **long gamma** es sistemáticamente favorable en el rebalanceo
(comprás abajo, vendés arriba). Es igual para calls y puts, consecuencia directa de la PCP.
Theta es negativa para opciones largas y se acelera al final.
El trade-off central sale de la propia PDE: \(-\Theta = \tfrac{1}{2}\sigma ^2S^2\cdot \Gamma + rS\cdot \Delta - rC\). **Theta es el alquiler de
la gamma**: quien está long gamma la paga todos los días, quien está short la cobra. Por eso no
existe una posición delta-neutral con \(\Gamma > 0\) y \(\Theta > 0\): la convexidad se paga.
Ej.: un portfolio delta-hedgeado puede perder plata igual si tiene gamma alto y el subyacente
saltea fuerte — el hedge lineal no cubre el segundo orden.

</details>

### Griegas III — Vega y Rho  [C2 §4]
\(\nu = \partial C/\partial \sigma = S\cdot n(d_1)\cdot \sqrt{T}\) (siempre positiva) y \(\rho = \partial C/\partial r = K\cdot T\cdot e^{-rT}\cdot N(d_2)\).

→ trading_de_volatilidad · volatilidad_implícita · supuestos_BS

<details><summary>más</summary>

Vega es máxima ATM y crece con √T: las opciones largas son "vehículos de vega". Rho es chica en
opciones cortas.
La distinción clave para el trading: **riesgo gamma** = la vol *realizada* (el subyacente se
mueve más o menos de lo cubierto); **riesgo vega** = la vol *implícita* (el mercado re-precia σ y
tu libro se marca a mercado). Corto plazo es trade de gamma, largo plazo trade de vega: mismos
signos, distinto reloj.
Ej.: en BS σ y r son constantes, así que vega y rho miden literalmente la sensibilidad a que el
modelo se viole. 2022 lo recordó: con la Fed moviendo 500bp, rho dejó de ser la griega ignorada.

</details>

### Trading de volatilidad  [C2 §4]
La vol como activo: \(\sigma _{\text{real}}\) (ex-post, rolling std de retornos) vs. \(\sigma _{\text{impl}}\) (la que el mercado paga hoy). Si \(\sigma _{\text{impl}} > \sigma _{\text{real}}\), las opciones están caras.

→ vega · straddle · P&L_del_delta_hedger · VIX

<details><summary>más</summary>

El instrumento directo es el straddle ATM: long compra vega (gana si se mueve mucho), short la
vende (gana si se queda quieto, cobrando theta). Regla práctica del costo:
\(\text{straddle ATM} \approx 0.8\cdot \sigma _{\text{impl}}\cdot S\cdot \sqrt{T}\).
El P&L diario del delta-hedger es \(\approx \tfrac{1}{2}\Gamma (\Delta S)^2 - \Theta \cdot \Delta t\): si \((\Delta S)^2 > \sigma ^2\Delta t\) gana el long gamma,
si no gana el short.
Ej.: el VIX es el índice de vol implícita del S&P 500 a 30 días — el barómetro del miedo.

</details>

### P&L del delta-hedger  [C2 §4]
\(P\&L = \int _0^T \tfrac{1}{2}\cdot \Gamma \cdot S^2\cdot (\sigma _{\text{real}}^2 - \sigma _{\text{impl}}^2)dt\): cosechás la diferencia entre la vol que pagaste y la que el mundo entregó.

→ trading_de_volatilidad · gamma · breakeven_diario

<details><summary>más</summary>

Dos consecuencias no obvias: (1) el P&L es **path-dependent**, porque la diferencia pesa por la
*dollar gamma* \(\Gamma \cdot S^2\) de cada momento; (2) podés tener razón (\(\sigma _{\text{real}} > \sigma _{\text{impl}}\) en promedio) y
perder plata igual, si la gamma estaba lejos del strike cuando el mercado se movió.
Regla práctica del breakeven diario: \(|\Delta S|/S = \sigma _{\text{impl}}/\sqrt{252}\). Con \(\sigma _{\text{impl}} = 20\%\), el breakeven es
≈1.26% diario.
Ej.: 19 días pegado al strike con vol baja (perdiendo, porque Γ era alta) y después un salto
enorme pero ya lejos del strike (con \(\Gamma \approx 0\)): vol realizada total ~94% vs. 20% implícita, y el
P&L final igual negativo. Timing y ubicación importan.

</details>

### Volatilidad implícita, smile y skew  [C2 §4]
\(\sigma _{\text{impl}}(K,T)\) = la σ que hace \(BS = \text{precio de mercado}\). Si BS fuera correcto sería constante; **no lo es**.

→ Black-Scholes · breeden_litzenberger · smile (C3) · supuestos_BS

<details><summary>más</summary>

Se calcula numéricamente (inversión de BS, no hay fórmula cerrada). La realidad: varía
sistemáticamente con el strike (**smile**) y con el plazo (**term structure**), y en equities el
skew es negativo: las puts OTM tienen mayor IV que las calls OTM.
Causa: la distribución real tiene colas más pesadas y asimetría negativa vs. la log-normal, y
además hay demanda estructural de protección.
Ej.: desde el crash de 1987 el mercado paga prima extra por cubrirse de la caída. Esa superficie
\(\sigma _{\text{impl}}(K,T)\) es la agenda entera de C3 (Heston, SABR, vol local).

</details>

### Supuestos de BS y dónde fallan  [C2 §4]
GBM con σ constante · sin costos ni impuestos · fraccionamiento perfecto · sin dividendos · sin arbitraje · r constante · trading continuo.

→ volatilidad_implícita · smile (C3) · saltos (C3) · P&L_del_delta_hedger

<details><summary>más</summary>

Qué falla en la práctica: σ varía en el tiempo y con el nivel del precio (vol smile/surface);
bid-ask, comisiones e impacto de mercado limitan el hedge continuo; los lotes mínimos impiden
replicar exacto; los dividendos discrecionales mueven el subyacente de golpe; los flash crashes
y gaps de apertura violan la continuidad del GBM.
Ej.: en la práctica se rebalancea discretamente, así que el P&L del hedger tiene ruido aunque el
modelo sea "correcto".

</details>

## Notas de clase -- Opciones sin modelo: cotas, paridad y estategias

*§2 sin modelo:* --- Ideea detras de las cotas, el modelo se ve despues

Opcion: Definicion. Veremos opciones europeas, pero existen mas de estas (distintas reglas). Especulacion?
Call -> opcion de compra (max (S-K, 0))
Put -> Opcion de venta( max (K-S, 0))
donde S es el valor del producto financiero en si, K es el valor al que se ejerce la opcion

cotas de call/put, defino cotas para delimitar lo que conviene a momento de ejercer.
Como definir la cota: P6 y comparacion de portfolio para encontrar valoresa futuro de Calls o Puts (Incluso tasas)
Futuro como opcion <- forward = contrato en el futuro en el cual hoy pactamos un precio. Se plantea como una manera de amortiguacion?

monotonicidad en strike, *No entendi esta parte*
Metodo Box spread vs PCP (solo para opciones europeas)
Monotonicidad = primera derivada acotada; convexidad = segunda ≥ 0: q es una densidad válida

*§3 estocástico:*

El precio de mañana es incierto, necesitamos incorporar aleatoriedad
Ejemplos: GBM(Movimiento browniano geometrico)
Integral de Ito como solucion al metodo estocastico (o al menos mejorarlo)
Lema de Ito es muy relevante, ayuda a la forma de medir el pricing.

*§4 Black-Scholes:*
Modelo de pricing.
Relevancia de las griegas.

Como funciona la venta de delta, gammas y vegas. Entiendo la logica, no entiendo como una opcion puede vender acciones y mantener la misma opcion. ?Trading de volatilidad?


LAs opciones son un derecho a comprar algo.
Esa prima para comprar ese derecho tiene un valor intrinsico y temporal. Black Scholes es el primer modelo de pricing, pero la volatilidad esta mal.
Las opciones se inventaron para Hedgearse. La opcion sirve para cubrirse , pero toma riesgos de volatilidad (Vega), tasa( rho) , riesgosde temporalidad (gamma) 

## ❓ Dudas de C2 (marcadas en tus notas)

- **"monotonicidad en strike, *No entendi esta parte*"** → Resuelta. No se "ve": se **demuestra
  con un portafolio**. Armás el bull call spread \(+C(K_1) - C(K_2)\); su payoff es 0 hasta K₁, sube
  1 a 1 y se clava en \(K_2-K_1\), así que nunca es negativo. Un payoff nunca negativo no puede
  costar menos que 0 hoy, y ese costo *es* \(C(K_1) - C(K_2)\). Por lo tanto \(C(K_1) \ge C(K_2)\).
  El bear put spread hace lo mismo del lado del put. → monotonicidad_en_strike
- **"no entiendo como una opcion puede vender acciones y mantener la misma opcion"** → Resuelta.
  La opción no vende nada. **Vos** vendés \(\Delta\) acciones (en corto) *contra* la opción que tenés:
  la opción sigue en tu libro intacta, lo que cambia es el paquete \(\Pi = C - \Delta \cdot S\), que queda
  instantáneamente sin riesgo. Cuando S se mueve, Δ cambia (eso es gamma) y tenés que rebalancear
  esa posición de acciones, no la opción. → delta_hedging · griegas
- **"¿Trading de volatilidad?"** → Sí: si delta-hedgeás hasta T, tu P&L deja de depender de la
  dirección y pasa a ser \(\int \tfrac{1}{2}\Gamma S^2(\sigma _{\text{real}}^2 - \sigma _{\text{impl}}^2)dt\). Comprás una vol y cosechás otra.
  → P&L_del_delta_hedger

---

# C3 — Volatilidad, tasas y crédito

## Idea general

**En una frase.** Black-Scholes supone que σ es una constante, y el mercado dice que no.
Esta clase es un catálogo de **parches** a BS, cada uno para un problema puntual: Dupire
arregla la σ como función, Heston le da vida propia, Merton agrega saltos, Vasicek/CIR/
Hull-White hacen lo mismo para la tasa, y Merton-crédito usa la maquinaria de opciones para
leer el riesgo de quiebra de una empresa.

**Qué problema del mercado resuelve.** Un desk no puede pricear exóticos, swaptions ni deuda
corporativa con un solo número de vol: necesita que el modelo reproduzca la sonrisa que
cotiza la pantalla (Dupire / Heston / SABR), que admita gaps (Merton jumps), que sepa que la
tasa revierte a un nivel y puede ser ~0 (modelos de tasa corta) y que lea la probabilidad de
default desde el precio de la acción (Merton crédito). Ningún modelo gana en todo: el
criterio de elección es siempre el trade-off **calibración vs. dinámica vs. velocidad**, y
la §7 es el mapa de qué modelo usa cada mesa.

**Cómo conecta.** Hacia atrás: todo sale de C2 — el smile es la densidad de
Breeden-Litzenberger, la fórmula de Dupire es butterfly sobre calendar, la varianza de Heston
es el mismo proceso CIR de la tasa, y el balance de una empresa es una call sobre sus
activos. Hacia adelante: la superficie σ(K,T) es un input de riesgo (C5) y un feature para ML
(C6). Patrón repetido ⚡: **promover un parámetro a función y calibrarlo a los datos de hoy**
(NSS en C1 → Dupire → Hull-White) compra ajuste perfecto hoy a cambio de dinámica dudosa
mañana (sticky strike).

> Arco de la clase — *Pricing II: cuando Black-Scholes no alcanza.*
> §1 por qué falla BS (el smile como diagnóstico) → §2 vol local (Dupire) →
> §3 vol estocástica (Heston) → §4 SABR, híbridos y saltos (Merton) → §5 modelos de tasa corta
> (Vasicek/CIR/Hull-White) → §6 crédito estructural (Merton) → §7 el mapa de qué desk usa qué.

## §1 Lo que Black-Scholes no puede hacer

### Skew vs. smile  [C3 §1]
La **forma** de la sonrisa es la densidad que el mercado cotiza. Equities: skew (\(\sigma _{\text{impl}}(K<S) > \sigma _{\text{impl}}(K>S)\)). FX pareja: smile simétrico.

→ volatilidad_implícita (C2) · breeden_litzenberger (C2) · heston · risk_reversal

<details><summary>más</summary>

Leído con Breeden-Litzenberger: el skew de equities implica **cola izquierda gorda** (el crash
cotizado); el smile simétrico de FX implica **las dos colas gordas** (cualquiera de las dos
monedas puede saltar).
La simetría de FX es estructural: la caída del euro *es* la suba del dólar, el put de una moneda
es la call de la otra, y los dos lados tienen hedgers y bancos centrales que pueden sorprender.
Ej.: no todo FX es simétrico — USDJPY (yen refugio) y las monedas emergentes tienen skew fuerte:
un USD/BRL puede tener RR25 ≈ −5 puntos de vol, porque la devaluación **sí** es un lado
privilegiado.

</details>

### Causas del skew negativo en equities  [C3 §1]
**Leverage effect** (si S baja, σ sube: correlación precio-vol negativa) · **saltos** (los crashes son más probables y más grandes que las subas) · **demanda de protección**.

→ skew_vs_smile · heston · merton_jumps

<details><summary>más</summary>

Las tres causas se traducen en tres parámetros de modelo distintos, y por eso importa distinguirlas:
el leverage effect se fabrica con \(\rho < 0\), la kurtosis con vol-of-vol, y el gap con un proceso de
saltos.
Ej.: la superficie cruda \(\sigma (K,T)\) tiene \(N_K \times N_T\) números: describe, pero no tiene dinámica.
Lo que se busca son modelos que la generen desde pocos parámetros interpretables.

</details>

### Las tres curas para σ  [C3 §1]
\(\sigma (S,t)\) determinista (**vol local**, §2) · σ con vida propia (**vol estocástica**, §3–§4) · el precio **salta** (§4).

→ vol_local · heston · sabr · merton_jumps

<details><summary>más</summary>

El diagnóstico común es que la σ de BS no puede ser una constante; las tres curas difieren en
qué le agregan.
Ej.: el criterio de selección es siempre el mismo trade-off — calibración vs. dinámica vs.
velocidad.

</details>

## §2 Volatilidad local: Dupire

### La idea de Dupire  [C3 §2]
\(dS = r\cdot S\cdot dt + \sigma _{\text{local}}(S,t)\cdot S\cdot dW\): un solo browniano, pero σ es una **función**, no un número. Se **lee** de los precios, no se optimiza.

→ breeden_litzenberger (C2) · formula_de_dupire · sticky_strike

<details><summary>más</summary>

Por qué es posible: por Breeden-Litzenberger los precios de opciones contienen la densidad de
\(S_T\) para *cada* vencimiento; con las densidades marginales a todos los plazos, la difusión que
las genera queda determinada. Es un problema inverso: de las marginales al proceso.
Ej.: calibración **perfecta por construcción** (error cero contra la superficie de hoy). Es el
benchmark contra el que se mide todo lo demás y la base del pricing de exóticos en equity.

</details>

### La fórmula de Dupire  [C3 §2]
\(\sigma ^2_{\text{local}}(K,T) = (2\cdot \partial C/\partial T) / (K^2\cdot \partial ^2C/\partial K^2)\).

→ breeden_litzenberger (C2) · calendar_spread (C2) · butterfly (C2)

<details><summary>más</summary>

Todo el contenido es de C2: el **denominador** es la densidad (el butterfly) y el **numerador** es
el valor del tiempo extra (el calendar spread). σ_local² es cuánta difusión hace falta en (K,T)
para explicar cómo fluye la probabilidad.
Costo práctico: exige derivar **dos veces** precios ruidosos, así que hay que suavizar/interpolar
la superficie antes. Y si la superficie viola el no-arbitraje de butterfly o de calendar, \(\sigma ^2\)
sale negativa.
Ej.: los argumentos (K,T) son las coordenadas de la superficie de *hoy*; (S,t) en la SDE son
dónde va a estar el camino simulado *mañana*.

</details>

### La falla famosa: sticky strike vs. sticky moneyness  [C3 §2]
Calibración perfecta hoy ≠ dinámica correcta mañana.

→ formula_de_dupire · heston · SLV

<details><summary>más</summary>

Vol local predice que si el spot sube, la sonrisa se queda pegada a los **strikes** viejos
(sticky strike): la curva \(\sigma (K)\) no se mueve, así que en el nuevo ATM da una vol más chica y el
modelo predice que subir aplana el skew. El mercado hace otra cosa: la sonrisa **viaja con el
spot** conservando su forma relativa al ATM (sticky moneyness).
Ej.: con el spot de 100 → 110, la diferencia entre las dos predicciones en el nuevo ATM es de
casi 3 puntos de vol. Vol local supone que el mercado olvida el precio de ayer; en la práctica
recuerda la forma del riesgo relativo al precio de hoy.

</details>

## §3 Volatilidad estocástica: Heston

### Modelo de Heston  [C3 §3]
\(dS = rS dt + \sqrt{v}\cdot S dW^1\), \(dv = \kappa (\theta -v)dt + \xi \sqrt{v} dW^2\), \(dW^1dW^2 = \rho dt\). La varianza es un proceso CIR.

→ CIR · vol_of_vol · smile · vasicek_CIR

<details><summary>más</summary>

Los 5 parámetros, cada uno controla algo visible: **κ** velocidad de reversión hacia θ (el
resorte); **θ** varianza de largo plazo; **ξ** vol-of-vol (curtosis / curvatura del smile);
**ρ** correlación precio-vol (el skew, \(\rho < 0\) en equities); **v₀** varianza inicial (casi
observable).
Ej.: es el mismo proceso de reversión a la media que la tasa corta de §5 — la varianza de Heston
*es* un CIR.

</details>

### Por qué el smile emerge  [C3 §3]
En BS el smile es un parche; en Heston es una **consecuencia** de la dinámica.

→ heston · skew_vs_smile · causas_del_skew

<details><summary>más</summary>

\(\rho < 0\) genera el skew: las caídas de precio vienen con subas de varianza, se amplifican, la
distribución de \(S_T\) queda sesgada a la izquierda y las puts OTM valen más.
\(\xi > 0\) genera la curvatura: varianza aleatoria = mezcla de lognormales con distintas σ ⇒ colas
pesadas de los dos lados ⇒ suben las dos alas (el butterfly de C2).
\(\kappa\) y \(\theta\) generan la estructura temporal: si \(v_0 < \theta\), la vol implícita crece con T; el smile
corto es pronunciado y el largo se aplana por promediación.

</details>

### Pricing y calibración de Heston  [C3 §3]
\(C(K,T) = S\cdot P_1 - K\cdot e^{-rT}\cdot P_2\), con \(P_1, P_2\) por inversión de Fourier: milisegundos por precio.

→ heston · nelson_siegel (C1) ⚡ · SLV

<details><summary>más</summary>

La función característica de \(\ln S_T\) tiene forma cerrada, así que queda una integral 1D numérica
— por eso es calibrable contra miles de opciones, a diferencia de Monte Carlo.
Calibrar es \(\min \sum [\sigma _{\text{impl}}^{\text{modelo}} - \sigma _{\text{impl}}^{\text{mercado}}]^2\) sobre la superficie: problema no convexo con
parámetros parcialmente degenerados (κ vs. ξ), así que necesita puntos iniciales y cotas. Es el
mismo flujo que la NSS de C1: datos → modelo paramétrico → optimización. ⚡
Ej.: lo que Heston **no** arregla: 5 parámetros contra \(N_K \times N_T\) datos no dan ajuste perfecto,
y una difusión pura no alcanza a fabricar el skew de plazo cortísimo.

</details>

## §4 SABR, híbridos y saltos

### SABR (Hagan et al., 2002)  [C3 §4]
\(dF = \sigma F^\beta dW\), \(d\sigma = \nu \sigma dZ\), \(dW dZ = \rho dt\). Su arma es una **fórmula algebraica** para \(\sigma _{\text{impl}}(K,T)\).

→ heston · swaptions (C2) · smile

<details><summary>más</summary>

Sin integrales, sin Fourier, sin Monte Carlo: calibrar un smile es ajustar 3 números en
microsegundos, por eso es EL estándar en swaptions.
Cómo lo lee un trader: **α** el nivel (vol ATM), **ρ** el skew (la pendiente), **ν** la curvatura
(las alas). **β ∈ [0,1]** es la "naturaleza" de la vol (0 = normal, típico de tasas; 1 = lognormal,
equities) y se **fija por convención**, no se calibra.
Ej.: limitaciones — es una aproximación asintótica, falla en T largos y strikes extremos, y da
densidades negativas lejos del dinero.

</details>

### Merton jump-diffusion  [C3 §4]
\(dS/S = \mu dt + \sigma dW + (Y-1)dN\): difusión de siempre + saltos contados por un Poisson de intensidad λ.

→ causas_del_skew · heston · supuestos_BS (C2)

<details><summary>más</summary>

Solución: el GBM de siempre multiplicado por el producto de los saltos ocurridos.
El precio es una **suma de Poisson de precios Black-Scholes**:
\(C = \sum _n [e^{-\lambda 'T}(\lambda 'T)^n/n!]\cdot BS(S,K,T,r_n,\sigma _n)\) — "condicional en que hubo n saltos, es un BS con
vol y tasa ajustadas", promediado sobre el Poisson.
Ej.: un salto es un **gap** que ninguna difusión continua puede generar; por eso Merton produce
skew negativo incluso a 1 semana de plazo, justo donde Heston se queda corto.

</details>

### Híbridos y frontera: SLV y rough vol  [C3 §4]
**SLV** = σ_local × factor estocástico (calibración exacta de Dupire + dinámica decente de Heston). **Rough vol** = browniano fraccionario con \(H \approx 0.1\).

→ vol_local · heston · sticky_strike

<details><summary>más</summary>

SLV es el estándar de producción para exóticos de equity y FX (barreras, autocallables).
Rough vol (Gatheral-Jaisson-Rosenbaum, 2018) explica el skew explosivo de plazos cortos con menos
parámetros, a costa de no ser markoviano.
Ej.: el patrón general del bloque — cada modelo nuevo compra realismo pagando velocidad o
tratabilidad.

</details>

## §5 Modelos de tasa corta

### Por qué la tasa necesita su propio modelo  [C3 §5]
No es una acción: **revierte** a un nivel, puede ser ~0 o negativa, y lo que se valúa depende del **camino**: \(P(t,T) = E^Q[e^{-\int r(s)ds}]\).

→ GBM (C2) · vasicek_CIR · hull_white · curva_de_rendimiento (C1)

<details><summary>más</summary>

Un GBM no sirve: deriva al infinito, no puede cruzar cero y modela un valor terminal, no un
acumulado. El proceso natural es el de reversión a la media con ruido (Ornstein-Uhlenbeck).
Ej.: con la curva de C1 como condición de contorno, todo el curso converge acá.

</details>

### Vasicek y CIR  [C3 §5]
\(dr = \kappa (\theta -r)dt + \sigma dW\) (Vasicek, gaussiano) · \(dr = \kappa (\theta -r)dt + \sigma \sqrt{r} dW\) (CIR, \(r \ge 0\)).

→ heston · hull_white · bono

<details><summary>más</summary>

Vasicek: solución gaussiana exacta, half-life de la reversión \(\ln2/\kappa\), precio de bono analítico
en forma afín \(P(t,T) = A(t,T)\cdot e^{-B(t,T)\cdot r}\). Su defecto célebre es que r puede ser negativa —
que después de la era europea pasó de bug a feature.
CIR: el \(\sqrt{r}\) apaga el ruido cuando \(r \to 0\), así que la tasa no cruza el cero (la misma condición
que la varianza de Heston: es el mismo proceso).
Ej.: defecto compartido — son **endógenos**: su \(P(0,T)\) no coincide con la curva de mercado de hoy.

</details>

### Hull-White  [C3 §5]
\(dr = [\theta (t) - a\cdot r]dt + \sigma dW\), con \(\theta (t)\) **despejada** de la curva forward observada, no estimada.

→ vasicek_CIR · nelson_siegel (C1) ⚡ · vol_local ⚡

<details><summary>más</summary>

\(\theta (t) = \partial f(0,t)/\partial t + a\cdot f(0,t) + \sigma ^2(1-e^{-2at})/(2a)\). Resultado: \(P(0,T)\) del modelo = \(P(0,T)\)
del mercado exactamente, para todo T. La curva de C1 (NSS) entra como input y el modelo la
respeta por construcción; sigue siendo afín, así que bonos, caps y swaptions tienen fórmula
cerrada. Es el modelo de los sistemas de riesgo de tasa de los bancos.
Ej.: es **el mismo movimiento que Dupire** hizo con la vol — promover un parámetro a función y
calibrarla a los datos de hoy — con la misma advertencia: calibración perfecta hoy no garantiza
dinámica correcta mañana. ⚡

</details>

## §6 Crédito estructural: el modelo de Merton

### El balance como posición en opciones  [C3 §6]
Activos \(V_t\) siguen un GBM y la deuda es un pago único D en T ⇒ **equity = call sobre los activos** (strike D) y **deuda = bono seguro − put**.

→ call_put (C1) · Black-Scholes (C2) · CDS (C1) · distancia_al_default

<details><summary>más</summary>

\(E_T = \max(V_T - D, 0)\) y \(D_T = \min(V_T, D) = D - \max(D - V_T, 0)\): en T, si \(V > D\) los
accionistas pagan la deuda y se quedan el resto; si no, entregan la firma.
Todo C2 aplica de inmediato: el **spread crediticio ES la prima de ese put**. Y explica
incentivos: más riesgo (\(\sigma _V \uparrow\)) sube el call (accionistas) y baja la deuda (acreedores) — el
conflicto de agencia, en una fórmula.

</details>

### Distancia al default y PD  [C3 §6]
\(E = V\cdot N(d_1) - D\cdot e^{-rT}\cdot N(d_2)\), con \(DD = d_2\) ("a cuántos sigmas está la quiebra") y \(PD = N(-d_2)\).

→ balance_como_opciones · d1_d2 (C2) · CDS (C1)

<details><summary>más</summary>

El problema práctico: \(V\) y \(\sigma _V\) no se observan; lo que se ve es \(E\) (market cap) y \(\sigma _E\)
(vol del equity). Dos ecuaciones, dos incógnitas ⇒ sistema no lineal ⇒ `fsolve`.
Ej.: con \(V_0=130, D=100, \sigma _V=25\%, T=1a\) sale \(DD = 1.12\) sigmas y \(PD = N(-DD) = 13\%\) — es la masa
lognormal que queda por debajo de D.

</details>

## §7 El mapa: qué modelo usa cada desk

### Criterios de selección  [C3 §7]
El trade-off es siempre **calibración vs. dinámica vs. velocidad**.

→ vol_local · heston · sabr · hull_white

<details><summary>más</summary>

**Tasas:** Vasicek (analítico, punto de partida conceptual) · CIR (\(r \ge 0\), chi², el clásico
académico) · Hull-White (calibra exacto la curva → estándar de riesgo de tasa en bancos) ·
HJM/LMM (toda la curva forward → exóticos de tasas) · SABR(-LMM) (el smile de swaptions).
**Volatilidad y crédito:** vol local/Dupire (calibración perfecta, mala dinámica; interpolador y
exóticos simples) · Heston (semi-analítico, skew + término, 5 parámetros, estándar equity) ·
SLV (Dupire + Heston, el caballo de batalla de barreras y autocallables) · rough vol (frontera
académica) · Merton/KMV (crédito estructural; forma reducida para CDS).

</details>

## ⬜ Conceptos de C3 por completar (no cubiertos en el teórico)

**§1:** term structure de la vol · superficie \(\sigma (K,T)\) cruda

**§2:** suavizado/interpolación de la superficie · condiciones de no-arbitraje sobre σ_local · exóticos de equity con vol local

**§3:** condición de Feller · función característica de Heston · degeneración κ vs. ξ en la calibración

**§4:** proceso de Poisson y Lévy · Kou · calibración de saltos

**§5:** modelos afines · caps y floors · HJM / LMM en detalle

**§6:** forma reducida (intensidad de default) vs. estructural · KMV en la práctica · del Z-spread (C1) a la PD


## ❓ Dudas de C3

La volatilidad como un asset a tner en cuenta en el trade. 

En que sentido es malo "pagar velocidad o tratabilidad" -> Adapatacion del modelo o problema ante la ejecucion.

Conviene un modelo que quiza tarde mas, pero precise en tiempos distintos? Puede un modelo adapatarse bien a una consecuencia temporal, peor ser malo en maoyores y menores?

P34 -> Mapa de comparacion de los metodos
---

# C4 — Factores y series temporales: del mundo Q al mundo P

## Idea general

**En una frase.** Hasta ahora *valuábamos*: el hedge cancelaba μ y solo hacía falta σ. Desde
hoy *estimamos*: predecir, explicar, armar portfolios y medir el riesgo de mañana son preguntas
donde nada cancela a μ, y μ es lo más difícil de estimar de todas las finanzas.

**Qué problema del mercado resuelve.** Un desk de trading fabrica payoffs (mundo Q); un fondo,
un asset manager o un área de riesgo tienen que responder otra cosa: por qué dos acciones
rinden distinto (CAPM, factores), si el precio de mañana se predice con el de hoy (ARIMA —
spoiler: no), si el *riesgo* de mañana se predice (GARCH — sí), cómo comprimir muchas series
correlacionadas en pocos números (PCA de la curva), y qué hacer cuando hay más variables
candidatas que datos (Ridge/Lasso). La clase entera es una sola pregunta repetida —
*"¿qué NO sé, y con qué herramienta lo estimo?"* — primero entre acciones distintas (§2–3),
después en el tiempo de una sola serie (§4–5), con varias series a la vez (§6) y con más
variables que datos (§7).

**Cómo conecta.** Hacia atrás: P y Q son las dos medidas de Girsanov (C2), y dQ/dP es la
densidad implícita de Breeden-Litzenberger dividida por la real — el skew de C3 *es* el
pricing kernel. El AR(1) es la discretización del Vasicek de C3, y GARCH es Heston en tiempo
discreto: la varianza con vida propia ⚡. PCA redescubre nivel/pendiente/curvatura, los tres
factores que Nelson-Siegel imponía a mano en C1 ⚡. Hacia adelante: la \(\sigma _t\) de GARCH y
las exposiciones a factores son los inputs del portfolio de C5; Ridge/Lasso ya son ML y la
advertencia final (K-fold miente con series de tiempo) es el programa de C6; ARIMA reaparece
donde sí hay estructura: los spreads del pair trading de la Unidad 2.

> Arco de la clase — *Econometría financiera: de valuar a estimar.*
> §1 por qué cambia la pregunta (P vs. Q) → §2 CAPM empírico (β, α, SML) → §3 Fama-French y
> el zoológico → §4 ARIMA y por qué falla en retornos → §5 GARCH y el VaR condicional →
> §6 PCA de la curva → §7 Ridge y Lasso, el puente al ML.

## §1 Del mundo Q al mundo P

### Retorno simple vs. logarítmico  [C4 §1]
Simple: \(r_t = (P_t - P_{t-1} + D_t)/P_{t-1}\). Log: \(r_t = \ln(P_t/P_{t-1})\). El log **suma en el tiempo sin error**; el simple no.

→ GBM (C2) · estacionariedad · CAPM

<details><summary>más</summary>

Para movimientos chicos dan casi lo mismo (\(\ln 1.08 \approx 7.7\%\) vs. 8%), pero el log-retorno
de dos días es la suma de los log-retornos diarios, y eso es lo que hace tratable toda la
econometría: promedios, varianzas y autocorrelaciones se calculan sobre algo aditivo. Es la
misma razón por la que en C2 se aplicó Itô a \(\ln S\) y no a \(S\).
Ej.: comprás a $100, vale $107 y cobrás $1 de dividendo: retorno simple 8%, log ≈ 7.7%.

</details>

### P vs. Q: las dos medidas  [C4 §1]
**P** = el mundo real (lo que va a pasar). **Q** = el mundo del pricing (cuánto debe costar). La brecha entre las dos **es la prima de riesgo**.

→ girsanov (C2) ⚡ · pricing_neutro_al_riesgo (C2) · dQ_dP · de_valuar_a_estimar

<details><summary>más</summary>

El seguro de la casa: la probabilidad real de incendio es 0.1% anual (P), pero la aseguradora
cobra como si fuera 0.3% (Q). No es un error: perder la casa duele más de lo que "vale" en
probabilidad pura, y ese 0.3% inflado ya incorpora la aversión al riesgo. En finanzas es lo
mismo: bajo P el S&P sube en promedio \(\mu \approx 10\%\); bajo Q el drift es \(r\) y los
escenarios malos pesan más de lo que realmente son. Los precios de opciones de C2 están
calculados en Q — por eso "descontar \(E^P\) a la tasa r" daba un precio equivocado.
Ej.: P pregunta "¿qué va a pasar?"; Q pregunta "¿cuánto debe costar?". Mismo mundo, dos medidas.

</details>

### dQ/dP: el pricing kernel  [C4 §1]
Derivada de Radon-Nikodym: escenario por escenario, **cuánto más (o menos) peso le pone el mercado** que la realidad. \(dQ/dP > 1\) en los crashes, \(< 1\) en las subas grandes.

→ P_vs_Q · densidad_implícita (C2) · skew_vs_smile (C3) · girsanov (C2)

<details><summary>más</summary>

Es densidad × aversión al riesgo. Bajo lognormal con drift μ y drift r sale una curva
decreciente en el retorno: alta en las pérdidas grandes (el mercado las trata como mucho más
probables de lo que son → asegurarse ahí es caro) y por debajo de 1 en las ganancias grandes
(nadie paga de más por la bonanza). Conexión directa con C2–C3: la \(q(K)\) de
Breeden-Litzenberger es la densidad bajo Q; dividirla por el histograma real de retornos da
exactamente este cociente, y el skew de equities de C3 es su firma visible.
Ej.: en el gráfico de la slide, Q es la misma campana que P corrida a la izquierda y con la
cola izquierda más gorda; el área entre las dos en la zona de pérdidas es "lo que infla Q".

</details>

### De valuar a estimar  [C4 §1]
C2–C3 pudieron ignorar μ porque el precio de una opción es el **costo de fabricar el payoff** y el hedge cancela la dirección. Predecir, explicar, armar carteras y medir riesgo **no tienen réplica que salve**: hay que estimar μ.

→ delta_hedging (C2) · CAPM · ARIMA · VaR_condicional

<details><summary>más</summary>

La receta de replicación usa σ (qué tan agitado es el precio, lo que sí importa para fabricar
el payoff) pero no μ (hacia dónde va en promedio). Cruzar a P significa que cambia la
*pregunta*, no el activo: P es lo que estima un histograma de retornos históricos, y desde acá
cada sección de la clase es una versión de "¿qué no sé y con qué lo estimo?".
Ej.: un market maker de opciones vive en Q y nunca opina sobre μ; un fondo que arma un
portfolio vive en P y no puede evitar opinar.

</details>

## §2 CAPM como modelo empírico

### CAPM y la Security Market Line  [C4 §2]
\(E[r_i] = r_f + \beta _i\,(E[r_m] - r_f)\). Dos acciones tienen distinto retorno esperado **solo** si tienen distinto β: es la única diferencia que el CAPM reconoce.

→ beta · riesgo_sistemático (C1) · alpha_de_jensen · fama_french

<details><summary>más</summary>

Tres retornos en juego: \(r_i\) (una acción), \(r_m\) (el mercado, ej. S&P 500) y \(r_f\) (el
piso sin riesgo, ej. T-bill a 3 meses). \(E[r_m] - r_f\) es la prima que pide el mercado por
arriesgar. Graficada con β en el eje horizontal, la ecuación **es una recta**: la SML. Como no
se observa \(E[r_i]\), en la práctica se corre OLS de retornos *realizados* en exceso:
\(r_i - r_f = \alpha + \beta (r_m - r_f) + \varepsilon\) — "en exceso" de los dos lados.
Ej.: \(r_f = 5\%\), \(E[r_m] = 10\%\), \(\beta = 1.4\) ⇒ \(E[r_i] = 5\% + 1.4\cdot 5\% = 12\%\); con
\(\beta = 0.5\) sería 7.5% — una acción más tranquila exige menos retorno.

</details>

### β: qué mide y de dónde sale  [C4 §2]
\(\beta _i = \mathrm{Cov}(r_i, r_m)/\mathrm{Var}(r_m)\): cuánto se mueve *esta* acción cuando se mueve el mercado. No es arbitraria: es la **solución de mínimos cuadrados** de \(r_i \approx \alpha + \beta r_m\).

→ CAPM · riesgo_sistemático_vs_idiosincrático · beta_se_mueve · riesgo_sistemático (C1)

<details><summary>más</summary>

Derivar el error cuadrático e igualar a 0 devuelve exactamente Cov/Var. \(\beta = 1\) se mueve
como el mercado; \(\beta = 2\): mercado +1% ⇒ activo ≈ +2%; \(\beta = 0.5\): ≈ +0.5%;
\(\beta \le 0\): nulo o inverso. Es la versión estimada del beta que en C1 era solo una
definición.
Ej.: en el S&P 2020-2024 Nvidia tiene \(\beta \approx 1.8\), Apple/Microsoft ≈ 1.2, J&J ≈ 0.5.

</details>

### Riesgo sistemático vs. idiosincrático  [C4 §2]
\(\sigma _i^2 = \beta _i^2\,\sigma _m^2 + \sigma ^2(\varepsilon _i)\). El primero lo comparten todas las acciones y **no se diversifica**; el segundo es propio de la empresa y **se cancela en promedio** en una cartera.

→ beta · riesgo_sistemático (C1) · portfolios (C5) · sharpe

<details><summary>más</summary>

Por eso el mercado solo paga por el riesgo sistemático: el idiosincrático (un juicio, un mal
balance) se puede eliminar gratis diversificando, así que nadie te compensa por cargarlo. Esta
descomposición es lo que C5 va a usar para armar carteras.
Ej.: una cartera de 30 acciones de distintos sectores tiene casi solo \(\beta ^2\sigma _m^2\); la
parte \(\sigma ^2(\varepsilon )\) de cada una se diluye.

</details>

### Alpha de Jensen y Sharpe  [C4 §2]
**α** = el intercepto de la regresión: el retorno que sobra por encima de lo que el β explica. **Sharpe** \(= (r - r_f)/\sigma\): retorno extra por unidad de riesgo *total*.

→ CAPM · SML · zoologico_de_factores · portfolios (C5)

<details><summary>más</summary>

En la SML: punto *sobre* la recta, el CAPM predijo bien (\(\alpha \approx 0\)); *arriba*, rindió más
de lo que le tocaba por su riesgo (\(\alpha > 0\), generó valor de verdad); *abajo*, perdió valor
pese al riesgo tomado. Alpha responde "¿le ganaste al mercado o solo tomaste más riesgo?".
Sharpe es la métrica más usada de la industria; el S&P histórico da ≈ 0.45.
Ej.: un fondo rindió 15% en un año en que, según su β, le tocaba 12% ⇒ \(\alpha = +3\%\). En la
slide: Nvidia con \(\alpha \approx +50\%\) anual, J&J y Coca-Cola bajo la recta.

</details>

### β no existe sin decir cómo se mide  [C4 §2]
Ventana (¿1, 2, 5 años?), frecuencia (¿diaria, semanal, mensual?) y un **error estándar**: con 252 días \(SE(\hat\beta ) \approx \pm 0.1\)–0.2, así que "β = 1.3" significa "β ∈ [1.1, 1.5]".

→ beta · practica_C4 (Ej. 1) · lopez_de_prado (C6)

<details><summary>más</summary>

No hay respuesta única, y encima se mueve: el β rolling cambia con el ciclo, el β de crisis no
es el β de calma. β sin barras de error es mala práctica — el notebook lo grafica así a
propósito.
Ej.: reportar β = 1.3 con ±0.2 y β = 1.1 con ±0.2 como "distintos" es leer ruido.

</details>

## §3 Fama-French y el zoológico de factores

### Qué es un factor  [C4 §3]
Un **patrón compartido por un grupo** de acciones, no una acción puntual. Se arma como **estrategia comprable**: comprar un grupo, vender otro (long-short).

→ fama_french_3 · momentum · nelson_siegel (C1) ⚡ · PCA_curva

<details><summary>más</summary>

"Las chicas" o "las baratas" se mueven parecido entre sí más allá del mercado general. Que
un factor sea una cartera long-short es lo que lo vuelve medible: tiene un retorno diario y
se le puede correr una regresión.
Ej.: en el notebook, SMB ≈ IWM − SPY (chicas menos grandes) y HML ≈ IVE − IVW (value menos
growth), con ETFs líquidos en vez de la data library académica.

</details>

### Fama-French 3 factores  [C4 §3]
\(r_i - r_f = \alpha _i + \beta _i\,MKT + s_i\,SMB + h_i\,HML + \varepsilon _i\). **MKT** el mercado, **SMB** premio al tamaño (chicas − grandes), **HML** premio al valor (baratas − caras).

→ CAPM · que_es_un_factor · momentum · ridge_lasso (§7) · practica_C4 (Ej. 1)

<details><summary>más</summary>

Es el CAPM con dos regresores más, y la pregunta empírica es cuánto sube el R² y cuánto se
achican los α al agregarlos: si los factores nuevos explican lo que el mercado solo no
explicaba, el α "misterioso" de CAPM se convierte en exposición a tamaño o valor. Datos: Ken
French (Dartmouth), desde 1926. En la ventana 2014-2024 de la slide, SMB y HML caen
sostenidamente (el premio se revierte) y MKT domina en Sharpe; la baja correlación entre
factores es lo que justifica cada adición.
Ej.: misma idea que Nelson-Siegel en C1 — resumir muchas series en pocos factores — pero acá
los factores son carteras, no parámetros. ⚡

</details>

### Momentum y Fama-French 5  [C4 §3]
**Momentum** (Carhart 1997): lo que subió el último año sigue subiendo un poco más. **FF5** (2015) agrega **RMW** (rentables − no rentables) y **CMA** (invierten poco − mucho).

→ fama_french_3 · zoologico_de_factores

<details><summary>más</summary>

Momentum es lo contrario de lo que uno esperaría intuitivamente, y es el cuarto factor
clásico: comprar ganadores, vender perdedores. Premio histórico grande (~5%/año) pero con
crashes brutales (2009: −80% en meses). Con los 5 de FF5 juntos, HML deja de aportar
información nueva: queda explicado por los otros.
Ej.: MOM fue el más estable de los cuatro en 2014-2024 mientras SMB y HML perdían.

</details>

### El zoológico de factores  [C4 §3]
La academia publicó **cientos** de factores. No puede haber 400 fuentes distintas de riesgo: es **multiple testing + p-hacking**.

→ fama_french_3 · lasso (§7) · lopez_de_prado (C6) · practica_C4 (Ej. 4)

<details><summary>más</summary>

Si probás 400 señales al azar, ~20 van a "funcionar" solo por casualidad, y solo se publican
las que funcionaron: los otros 380 intentos fallidos nadie los ve. Consecuencia medida:
la mitad de los factores publicados se evapora al probarlos con datos nuevos, y los que
sobreviven pierden ~50% del premio post-publicación (McLean-Pontiff 2016) — el mercado
aprende y el arbitraje se lo come. Lasso (§7) es la respuesta mecánica a "¿qué factores
sobreviven?"; C6 trae la caja de herramientas completa.
Ej.: el notebook parte HML en enero de 1993 (cuando Fama-French lo publicaron) y compara el
Sharpe pre y post, aislando además la "década perdida" 2010-2020.

</details>

## §4 Series temporales: ARIMA

### Estacionariedad  [C4 §4]
Una serie es estacionaria si su comportamiento promedio **no cambia con el tiempo**: media, dispersión y relación pasado-presente estables. Sin eso no hay estimación posible.

→ ADF_KPSS · ARIMA · retorno_logaritmico · GBM (C2)

<details><summary>más</summary>

Sin estacionariedad cada momento del tiempo sería una muestra de un solo dato. El diagnóstico
financiero básico: los **precios no** son estacionarios (caminan sin límite, es el GBM de C2),
los **retornos aproximadamente sí**. Por eso se modela \(r_t\), nunca \(P_t\).
Ej.: diferenciar una vez (\(d = 1\)) convierte precios en retornos, y ahí recién se puede
estimar algo.

</details>

### Tests ADF y KPSS  [C4 §4]
Tienen la **nula invertida** (ADF: \(H_0\) = raíz unitaria; KPSS: \(H_0\) = estacionaria), por eso se corren **juntos, nunca solos**.

→ estacionariedad · ARIMA · pair_trading (Unidad 2)

<details><summary>más</summary>

Si coinciden, confío: ADF rechaza + KPSS no rechaza ⇒ estacionaria; ADF no rechaza + KPSS
rechaza ⇒ no estacionaria. Si discrepan es **zona gris**: series en el límite, con reversión
muy lenta, casi caminata aleatoria — ahí ningún test alcanza solo.
Ej.: ruido blanco (\(\varphi = 0\)) y caminata aleatoria (\(\varphi = 1\)) son los casos fáciles y los
tests acuerdan; un AR(1) con \(\varphi = 0.97\) es genuinamente estacionario pero *casi* no lo es,
y los dos tests discrepan.

</details>

### AR, MA y ARIMA(p,d,q)  [C4 §4]
**AR(p)**: \(r_t = \mu + \varphi _1 r_{t-1} + \cdots + \varphi _p r_{t-p} + \varepsilon _t\) (memoria de p valores). **MA(q)**: \(r_t = \mu + \varepsilon _t + \theta _1\varepsilon _{t-1} + \cdots + \theta _q\varepsilon _{t-q}\) (memoria de q shocks). **d** = veces que hay que diferenciar.

→ vasicek_CIR (C3) ⚡ · ACF_PACF · AIC_BIC · ljung_box · box_jenkins

<details><summary>más</summary>

El AR(1) ya se usó: es la discretización del Vasicek de C3 — \(dr = \kappa (\theta - r)dt + \sigma dW\)
en pasos discretos es \(r_t = c + \varphi r_{t-1} + \varepsilon _t\) con \(\varphi = 1 - \kappa \Delta t\). ⚡
Para precios \(d = 1\). La receta de **Box-Jenkins**: 1. ADF para \(d\) → 2. ACF/PACF para \(p, q\) →
3. máxima verosimilitud → 4. residuos = ruido blanco (Ljung-Box). Es *el* algoritmo de la
econometría clásica, y funciona muy bien… donde hay señal.
Ej.: "el presente depende de p valores pasados" (AR) vs. "de q sorpresas pasadas" (MA): dos
formas de memoria, y ARMA las combina.

</details>

### ACF y PACF: cómo se leen  [C4 §4]
**ACF**(k): correlación de la serie consigo misma corrida k días. **PACF**(k): lo mismo, **descontando el efecto de los días intermedios**.

→ AR_MA_ARIMA · autocorrelacion_depende · ljung_box

<details><summary>más</summary>

Si hoy se parece a hace 2 días *solo porque* ambos se parecen a hace 1 día, la PACF en el
rezago 2 lo filtra y da ~0: ACF mezcla el efecto directo con el indirecto, PACF aísla solo el
directo. Sirven juntas porque cada una delata un modelo distinto: en un **AR(p)** la ACF decae
de a poco y la PACF se corta de golpe en p; en un **MA(q)** es al revés. La barra del rezago 0
siempre da 1 y no es evidencia de nada.
Ej.: ACF/PACF de retornos del SPY 2010-2025: desde el rezago 1 todas las barras caen dentro
de la banda — la firma del ruido blanco, la primera pista de que no hay memoria explotable.

</details>

### AIC y BIC: elegir el orden sin sobreajustar  [C4 §4]
\(AIC = 2k - 2\ln\hat L\), \(BIC = k\ln n - 2\ln\hat L\). Premian el ajuste (\(\hat L\)) y **penalizan** la cantidad de parámetros (\(k\)). Se elige el más bajo.

→ AR_MA_ARIMA · ridge_lasso (§7) · lopez_de_prado (C6)

<details><summary>más</summary>

El problema: más parámetros *siempre* ajustan el pasado igual o mejor. Un AR(5) nunca explica
peor los datos ya vistos que un AR(1); si el criterio fuera "mejor ajuste" siempre ganaría el
más complejo — el sobreajuste de siempre. BIC penaliza más fuerte con muchos datos
(\(\ln n\) crece, el 2 de AIC no) y tiende a elegir modelos más simples. Es la misma tensión
que Ridge/Lasso resuelven con λ en §7.
Ej.: agregar un parámetro que casi no mejora \(\hat L\) empeora el AIC/BIC.

</details>

### Ljung-Box: ¿el modelo dejó algo sin explicar?  [C4 §4]
Residuo = dato real − predicción. Si el modelo es bueno, los residuos son **ruido puro**. Ljung-Box testea varios rezagos a la vez: \(H_0\) = sin estructura remanente.

→ ACF_PACF · AR_MA_ARIMA · autocorrelacion_depende

<details><summary>más</summary>

Si **rechaza**: sobró estructura, el modelo está incompleto, hay que agrandarlo. Si **no
rechaza**: los residuos son indistinguibles de ruido, no hay más para exprimir con un modelo
lineal. Mira varios rezagos porque un modelo puede parecer bien en uno y fallar en otro.
Ej.: un AR(2) simulado y ajustado como AR(1): queda estructura en el rezago 2, Ljung-Box
rechaza; ajustado como AR(2), no rechaza en ningún rezago.

</details>

### ¿Queremos autocorrelación? Depende de qué serie  [C4 §4]
En los **retornos** \(r_t\): casi no hay (sería plata gratis). En la **volatilidad** \(r_t^2\): enorme, y es la base de GARCH. En los **residuos** de un modelo ajustado: no debe quedar.

→ ACF_PACF · por_que_ARIMA_falla · GARCH · ljung_box

<details><summary>más</summary>

No es que la "querramos" o no: es lo que hay. Si \(r_t\) tuviera autocorrelación fuerte,
predecir mañana con el dato de hoy sería gratis y alguien ya lo estaría haciendo. Si \(r_t^2\) no
la tuviera, no habría nada que modelar en §5.
Ej.: en el SPY, ACF de \(r_t\) ≈ nada; ACF de \(r_t^2\) enorme y persistente por 30 rezagos — el
efecto ARCH a simple vista.

</details>

### Por qué ARIMA no predice retornos  [C4 §4]
ARIMA sobre retornos del SPY: pronóstico ≈ la media ≈ 0, **indistinguible del naive**. La eficiencia del mercado es un **punto fijo**: si \(r_t\) fuera predecible, alguien lo explotaría y al hacerlo borraría el patrón.

→ autocorrelacion_depende · GARCH · pair_trading (Unidad 2) · practica_C4 (Ej. 2)

<details><summary>más</summary>

La predictibilidad lineal se auto-destruye; lo que queda son autocorrelaciones de ~0.01,
señal/ruido ínfimo e inestable. El pronóstico es una recta porque \(\varphi \approx -0.03\) y no
significativo: \(\hat r_{t+h} = \mu + \varphi ^h(r_t - \mu )\) converge a μ en 1-2 días, no hay
memoria que estirar. **La lección correcta no es "nada se puede predecir"**: el primer momento
(retornos) casi imposible a corto plazo; el segundo (volatilidad) muy predecible. Donde ARIMA
sí sirve en finanzas: spreads (pair trading, C7), volumen, series macro — donde hay estructura.
Ej.: el fracaso es el resultado, y es información. Otra forma de verlo: la serie es señal +
ruido y ARMA es un filtro que intenta separarlos — Feng & Palomar (2016) mostraron que es
matemáticamente lo mismo que sacarle la estática a una radio.

</details>

## §5 GARCH y el VaR condicional

### ARCH y GARCH: la varianza condicional  [C4 §5]
\(r_t = \mu + \varepsilon _t\), \(\varepsilon _t = \sigma _t z_t\). **ARCH(1)** (Engle): \(\sigma _t^2 = \omega + \alpha \varepsilon _{t-1}^2\). **GARCH(1,1)** (Bollerslev): \(\sigma _t^2 = \omega + \alpha \varepsilon _{t-1}^2 + \beta \sigma _{t-1}^2\).

→ heston (C3) ⚡ · autocorrelacion_depende · varianza_largo_plazo · leverage_GJR · VaR_condicional

<details><summary>más</summary>

"Condicional" = la varianza de *hoy* dado lo que pasó hasta ayer, no un promedio fijo de todos
los años. Cada símbolo: **ω** el piso (garantiza que \(\sigma _t^2\) no colapse a cero);
**\(\varepsilon _{t-1}^2\)** el shock de ayer al cuadrado; **α** la reactividad (cuánto pesa la
sorpresa de ayer); **\(\sigma _{t-1}^2\)** lo que el modelo mismo había predicho para ayer;
**β** la persistencia. Es el hecho estilizado #1 de C1 ("la calma y la tormenta se agrupan")
convertido en ecuación, y es Heston en tiempo discreto: la varianza con dinámica propia. ⚡
Ej.: el notebook lo estima **a mano** por máxima verosimilitud:
\(\ln L = -\tfrac{1}{2}\sum _t[\ln(2\pi \sigma _t^2) + \varepsilon _t^2/\sigma _t^2]\), diez líneas.

</details>

### Varianza de largo plazo y colas gordas  [C4 §5]
\(\bar\sigma ^2 = \omega /(1 - \alpha - \beta )\): el nivel "normal" al que la vol siempre vuelve, a velocidad que depende de \(\alpha + \beta\). Mezclar campanas de distinto ancho **fabrica curtosis** sin supuestos raros.

→ GARCH · heston (C3) · merton_jumps (C3) · VaR_condicional

<details><summary>más</summary>

Después de un shock grande el pronóstico baja de a poco hacia \(\bar\sigma ^2\); si \(\alpha + \beta\)
está cerca de 1 los shocks duran mucho, cerca de 0 se olvidan al día siguiente. Esto es
justo lo que ARIMA no pudo con el retorno: la varianza *sí* tiene estructura predecible.
Y de yapa: cada día el shock es una normal común, pero su ancho \(\sigma _t\) cambia; mezclar
muchos días con anchos distintos da colas más gordas que una sola campana fija — el hecho
estilizado #2 de C1, ahora explicado. Mismo mecanismo que \(\xi > 0\) en Heston.
Ej.: en el SPY, GARCH(1,1) típicamente da \(\alpha + \beta \approx 0.98\): la tormenta de marzo 2020
tardó meses en volver al nivel normal.

</details>

### Leverage effect: GJR-GARCH y EGARCH  [C4 §5]
GARCH(1,1) es simétrico, pero un retorno **negativo dispara más vol futura** que uno positivo del mismo tamaño. **GJR**: \(\sigma _t^2 = \omega + (\alpha + \gamma \,\mathbb{1}_{\varepsilon _{t-1}<0})\,\varepsilon _{t-1}^2 + \beta \sigma _{t-1}^2\).

→ GARCH · causas_del_skew (C3) ⚡ · heston (C3)

<details><summary>más</summary>

Nombre histórico: leverage effect (Black 1976) — una acción que cae aumenta el apalancamiento
de la empresa y eso sube su riesgo. Es la misma asimetría que en C3 se fabricaba con \(\rho < 0\)
en Heston. ⚡ GJR agrega un "interruptor": si el shock de ayer fue negativo pesa \(\alpha + \gamma\)
en vez de α. **EGARCH** hace lo mismo sobre \(\ln \sigma _t^2\), con asimetría suave en vez de salto.
La **News Impact Curve** lo grafica: varianza de mañana vs. shock de hoy; GARCH es una parábola
simétrica, GJR y EGARCH están inclinadas hacia los shocks negativos.
Ej.: una caída del 5% viene seguida de más nerviosismo que una suba del 5%; el GARCH simétrico
subestima el riesgo justo después de una caída.

</details>

### VaR y Expected Shortfall  [C4 §5]
**VaR**(confianza, horizonte): la pérdida máxima esperable. **ES**: cuánto se pierde *en promedio* una vez que ya se entró en la cola. VaR dice **dónde** empieza la cola; ES dice **cuánto** duele.

→ VaR_condicional · GARCH · portfolios (C5) · densidad_implícita (C2)

<details><summary>más</summary>

Por eso Basilea III exige ES, no solo VaR, para el capital de los bancos: dos carteras con el
mismo VaR pueden tener colas completamente distintas más allá del umbral.
Ej.: VaR(99%, 1 día) = USD 1M significa "hay 1% de probabilidad de perder más de 1M mañana";
el ES dice cuánto se pierde en promedio en ese 1% de días.

</details>

### VaR condicional  [C4 §5]
\(VaR_t(99\%) = \mu - 2.33\,\sigma _t\), con la \(\sigma _t\) de GARCH que **cambia día a día**. El límite de riesgo se agranda solo cuando la tormenta empieza.

→ VaR_ES · GARCH · practica_C4 (Ej. 2) · portfolios (C5)

<details><summary>más</summary>

Una σ fija supone que marzo 2020 y un agosto tranquilo tienen el mismo riesgo. El VaR
estático usa una sola ventana histórica y tarda en "enterarse" de un shock; el condicional se
recalcula cada día y se anticipa. Lo que falla igual en los dos: la *cantidad* total de
excedencias, porque las colas reales son más gordas que la normal (el 2.33 es gaussiano). Se
backtestea contando excedencias y, para el regulador, chequeando que no vengan agrupadas
(test de Christoffersen): la independencia importa tanto como la cantidad.
Ej.: el notebook compara VaR estático vs. condicional en el SPY con 2020 como prueba de fuego.

</details>

## §6 PCA de la curva

### PCA: diagonalizar la matriz de covarianza  [C4 §6]
Pregunta: ¿qué dirección \(v\) captura la **mayor varianza** al proyectar los datos? \(\max _v v^\top \Sigma v\) s.a. \(\|v\| = 1\) ⇒ (Lagrange) \(\Sigma v = \lambda v\).

→ nivel_pendiente_curvatura · fama_french_3 · nelson_siegel (C1) ⚡

<details><summary>más</summary>

Cada componente principal **es** un autovector de Σ y su autovalor λ **es** la varianza que
explica (% de la comp. \(i\) = \(\lambda _i/\sum \lambda _j\)). Las siguientes maximizan lo mismo
restringidas a ser ortogonales a las anteriores — por eso los patrones no se pisan. Mismo
espíritu que Fama-French: comprimir muchos números correlacionados en pocos factores, pero acá
los factores los elige la matriz, no el modelador.
Ej.: la curva de tasas tiene ~11 plazos que casi nunca se mueven de forma independiente;
Σ de los cambios diarios de los N tenores es el objeto de partida.

</details>

### Nivel, pendiente y curvatura  [C4 §6]
Tres componentes explican **más del 95%** de cómo se mueve *toda* la curva. **Nivel** (~90%): todo sube o baja por igual. **Pendiente** (~6%): corto y largo en direcciones opuestas. **Curvatura** (~3%): la panza relativa a los extremos.

→ PCA · nelson_siegel (C1) ⚡ · duration_DV01 (C1) · curva_de_rendimiento (C1) · hull_white (C3)

<details><summary>más</summary>

Las cargas de cada PC tienen forma reconocible: PC1 plana (todos los tenores con el mismo
signo), PC2 monótona de + a −, PC3 en U. Son exactamente los tres factores que Nelson-Siegel
*imponía* paramétricamente en C1; PCA los *descubre* desde los datos. ⚡ El nivel domina porque
política monetaria e inflación esperada mueven todos los plazos a la vez — y es el "shock
paralelo de 1bp" que el DV01 de C1 mide.
Ej.: un desk de tasas cubre el nivel con DV01 y después se preocupa por pendiente y curvatura,
en ese orden, porque en ese orden está el riesgo.

</details>

## §7 Ridge y Lasso: regularizar o sobreajustar

### El problema: más variables candidatas que datos  [C4 §7]
Con muchos factores candidatos (§3) y pocos años de datos, **OLS inventa**: coeficientes gigantes, signos sin sentido, ajusta perfecto el pasado y falla fuera de muestra.

→ zoologico_de_factores · ridge_lasso · AIC_BIC · lopez_de_prado (C6)

<details><summary>más</summary>

Es el sobreajuste de §4 en versión multivariada: más regresores nunca ajustan peor el pasado.
La solución tiene la misma forma que AIC/BIC — premiar el ajuste y penalizar la complejidad —
pero ahora la penalización entra en la función de pérdida y se elige por validación cruzada.
Ej.: el notebook explica una acción con 8 factores reales (ETFs) + 5 factores de ruido puro
"que publicó un paper", y todo se juzga en el 20% final de la muestra.

</details>

### Ridge (L2) vs. Lasso (L1)  [C4 §7]
**Ridge**: \(\min _\beta \|r - F\beta \|^2 + \lambda \|\beta \|_2^2\), encoge *todo* hacia cero, nada llega exacto. **Lasso**: \(\min _\beta \|r - F\beta \|^2 + \lambda \|\beta \|_1\), **puede llevar coeficientes exactamente a cero**: selección automática.

→ el_problema_regularizacion · zoologico_de_factores · puente_al_ML · practica_C4 (Ej. 3)

<details><summary>más</summary>

La geometría explica la diferencia: las curvas de nivel del error son elipses centradas en el
OLS; la región permitida por la penalización es un **círculo** (L2) o un **rombo** (L1). El
punto donde la elipse toca el círculo cae en cualquier lugar del borde (las dos coordenadas
distintas de cero); con el rombo, por las puntas, suele caer justo en una **esquina**, sobre
un eje — y ahí una coordenada es exactamente 0. Lasso responde la pregunta del zoológico:
¿qué factores sobreviven? En el path de regularización, Ridge encoge todo suavemente; Lasso
anula los factores de ruido primero. El R² out-of-sample del óptimo empata con OLS sin perder
generalización, y un λ demasiado grande sí cuesta caro.
Ej.: con 4 factores verdaderos + 6 de ruido, Lasso deja los 6 de ruido en cero exacto.

</details>

### El puente al ML (y la advertencia para C6)  [C4 §7]
Ridge y Lasso **ya son machine learning**: función de pérdida + regularización + validación cruzada es el esqueleto de todo el ML supervisado. Pero la **validación cruzada estándar miente** con datos financieros.

→ ridge_lasso · lopez_de_prado (C6) · beta_se_mueve

<details><summary>más</summary>

Lo no lineal (árboles, redes) extiende la misma lógica. El problema: K-fold mezcla el tiempo
y puede terminar entrenando con el futuro para predecir el pasado. Hoy se plantea el
problema; C6 (López de Prado) trae la caja de herramientas para resolverlo (purged CV,
embargo). Por eso el notebook divide train/test **en orden temporal, sin barajar**.
Ej.: un modelo que "predice" 2019 habiendo entrenado con 2020 da un R² excelente y no sirve
para nada.

</details>

## Práctica de C4 (notebook `clase4_practica_alumnos.ipynb`)

Cuatro ejercicios, cada uno con predicción escrita *antes* de correr:

- **Ej. 1 — CAPM y Fama-French con ETFs.** α, β, SE(β) y R² de 6 acciones (AAPL, NVDA, JPM,
  XOM, KO, F) contra SPY; después FF3 con SMB ≈ IWM − SPY y HML ≈ IVE − IVW. ¿Cuánto sube el R²
  y cuánto se achican los α? → CAPM · beta_se_mueve · fama_french_3
- **Ej. 2 — ARIMA fracasa, GARCH funciona.** SPY 10 años: ADF, las dos ACF, mejor ARIMA por AIC
  vs. naive out-of-sample; GARCH(1,1) por máxima verosimilitud *a mano*; VaR condicional vs.
  estático con backtest de excedencias. → por_que_ARIMA_falla · GARCH · VaR_condicional
- **Ej. 3 — Lasso vs. Ridge en vivo.** 8 factores reales (ETFs) + 5 de ruido inyectado; split
  temporal 80/20 sin barajar; ¿Lasso pone los 5 fantasmas en cero exacto? → ridge_lasso
- **Ej. 4 (extra) — El factor que muere al publicarse.** HML mensual desde 1926 (Ken French),
  partido en enero 1993; Sharpe pre vs. post, y la década perdida 2010-2020 aparte.
  → zoologico_de_factores

## ❓ Dudas de C4

*(Por completar después de repasar la clase.)*

Esta clase siempre la misma pregunta de fondo, '¿qué NO sé, y con qué herramienta lo estimo?

Ir cambiando de parametros en base a los datos de hoy?

"Condicional" = la varianza de hoy dado lo que pasó hasta ayer, no un promedio fijo de todos los años



---

# C5 — Teoría de portafolio: Markowitz, Black-Litterman y Risk Parity

## Idea general

**En una frase.** La matemática de Markowitz es elegante y correcta: dado μ y Σ, el portafolio
óptimo se resuelve con un Lagrangiano. Lo que falla son los *inputs*: μ no se puede estimar bien
con los datos que hay, y Σ tiene N² números. Todo lo que viene después de §1 es una secuencia
de respuestas a la misma pregunta — *¿cómo armo una cartera razonable con inputs que no puedo
estimar bien?* — y cada respuesta ataca un input distinto o cambia la pregunta.

**Qué problema del mercado resuelve.** Un inversor tiene N activos y capital para repartir. La
intuición ingenua ("todo al de mayor retorno esperado") ignora que ese retorno es incierto;
Markowitz (1952) convierte "diversificar es prudente" en un problema de optimización resoluble,
y la industria global administra billones resolviendo variantes de ese problema. Pero el óptimo
de papel se ve hermoso in-sample y colapsa afuera de la muestra (Michaud: "optimizadores de
error"), así que lo que la industria usa de verdad son las correcciones: shrinkage de Σ
(Ledoit-Wolf), Black-Litterman (fondos, bancos centrales, fondos soberanos: una cartera que un
humano puede *explicar*), y risk parity / HRP (Bridgewater, All Weather: repartir riesgo, no
capital, sin opinar sobre μ).

**Cómo conecta.** Hacia atrás: el CAPM de C4 era una regresión sobre datos; hoy vuelve como
**consecuencia matemática del equilibrio** — si todos hacen Markowitz + Tobin, el portafolio
tangente tiene que ser el mercado, y β mide la contribución de cada acción a su riesgo. Misma
fórmula, origen opuesto. La curva de diversificación es la descomposición
sistemático/idiosincrático de C4 vista con N activos; el piso \(\sigma\sqrt{\bar\rho}\) *es* el
riesgo sistemático. Shrinkage de Σ es Ridge aplicado a una matriz (C4 §7) ⚡, y el prior de
Black-Litterman es shrinkage de μ hacia el equilibrio. El colapso out-of-sample es el mismo
sobreajuste de Ridge/Lasso. La \(\sigma _t\) de GARCH (C4) es un input natural para Σ. Hacia
adelante: walk-forward y look-ahead bias son la versión simple del problema que C6 retoma en
serio (purged K-fold, embargo); HRP es López de Prado; el Lagrangiano con restricciones vuelve
en Almgren-Chriss (ejecución óptima, Unidad 2); el market maker de Avellaneda-Stoikov también
resuelve un problema de inventario/riesgo.

> Arco de la clase — *De la solución de papel a lo que se usa de verdad.*
> §1 Markowitz: retorno lineal, riesgo cuadrático, frontera, MVP, tangente, CML, Tobin y el
> CAPM como equilibrio → §2 por qué falla: Michaud, dimensionalidad, colapso out-of-sample,
> shrinkage → §3 Black-Litterman: prior de equilibrio + views, Bayes → §4 sus límites →
> §5 Risk parity, risk budgeting, HRP → §6 el backtest honesto: no hay ganador universal.

## §1 Markowitz: retorno, riesgo y frontera

### Qué problema resuelve Markowitz  [C5 §1]
N activos, capital para repartir, retornos **inciertos**. Markowitz (1952) formaliza "diversificar es prudente" como un problema de optimización: elegir pesos \(w\) que minimicen el riesgo para un retorno dado. Nobel 1990 (con Sharpe y Miller).

→ retorno_de_portafolio · riesgo_de_portafolio · frontera_eficiente · de_valuar_a_estimar (C4)

<details><summary>más</summary>

La pregunta que responde es "¿por qué los inversores diversifican, si *deberían* poner todo en
el activo de mayor valor esperado?". Respuesta: porque el valor esperado no es lo único que
importa — la dispersión también, y combinar activos la baja. Es el punto de partida de toda la
teoría de portafolio moderna. No se resuelve una vez: los pesos reales se alejan de los objetivo
a medida que los activos rinden distinto, y hay que **rebalancear**.
Ej.: un "60/40" (60% acciones, 40% bonos — el benchmark clásico de riesgo moderado) dejado
quieto 15 años con acciones al 9% y bonos al 3% termina en 78/22: la cartera se volvió más
arriesgada sin que nadie lo decidiera. Rebalanceando una vez por año vuelve exacto al 60%.

</details>

### Retorno de un portafolio  [C5 §1]
\(r_p = w_1 r_1 + \dots + w_N r_N = w^\top r\), y por linealidad de la esperanza \(\mu _p = w^\top\mu\). Es un **promedio ponderado**: la correlación entre activos no juega ningún rol.

→ markowitz · riesgo_de_portafolio · retorno_simple_vs_log (C4)

<details><summary>más</summary>

\(E[\text{suma}] = \text{suma de } E[\cdot]\), sin condiciones. Funciona igual con posiciones
cortas (algún \(w_i < 0\)): la fórmula no cambia. Por eso el retorno esperado es la parte
"aburrida" del problema — toda la sorpresa está en el riesgo.
Ej.: 60% en acciones (\(\mu = 10\%\)) y 40% en bonos (\(\mu = 4\%\)):
\(\mu _p = 0.6\cdot 10\% + 0.4\cdot 4\% = 7.6\%\).

</details>

### Riesgo de un portafolio: acá está la sorpresa  [C5 §1]
\(\sigma _p^2 = w^\top\Sigma w = \sum _i w_i^2\sigma _i^2 + \sum _{i\ne j} w_i w_j\sigma _{ij}\). **No** es un promedio ponderado de los \(\sigma _i\): depende de la matriz de covarianza completa.

→ retorno_de_portafolio · diversificacion · maldicion_de_la_dimensionalidad · PCA (C4)

<details><summary>más</summary>

Con 2 activos, escrito a mano: \(\sigma _p^2 = w_1^2\sigma _1^2 + w_2^2\sigma _2^2 + 2w_1w_2\rho _{12}\sigma _1\sigma _2\).
Los dos primeros términos son "lo esperado" (cada activo aporta su riesgo, ponderado al
cuadrado); el tercero es un término **cruzado** que depende de cómo covarían — no existe en el
caso del retorno. Con 3 activos: 3 propios + 3 cruzados. Con N: N propios + \(N(N-1)/2\)
cruzados, uno por cada *par*. Los cruzados crecen mucho más rápido que los propios, y eso es
a la vez la fuente de la diversificación (§1) y de la maldición de la dimensionalidad (§2).
Ej.: con 10 activos ya hay 45 covarianzas a estimar; con 3, \(\sigma _p^2 = w_1^2\sigma _1^2 + w_2^2\sigma _2^2 + w_3^2\sigma _3^2 + 2w_1w_2\sigma _{12} + 2w_1w_3\sigma _{13} + 2w_2w_3\sigma _{23}\).

</details>

### Diversificación y el piso \(\sigma\sqrt{\bar\rho}\)  [C5 §1]
Un portafolio equi-ponderado de N activos con la misma σ y correlación promedio \(\bar\rho\) tiene \(\sigma _p^2 = \sigma ^2\left[\tfrac{1}{N} + \tfrac{N-1}{N}\bar\rho\right] \to \sigma ^2\bar\rho\). El riesgo **converge a un piso, no a cero**: lo que sobra es el riesgo sistemático.

→ riesgo_de_portafolio · riesgo_sistematico_vs_idiosincratico (C4) ⚡ · beta (C4)

<details><summary>más</summary>

La parte \(1/N\) es el riesgo idiosincrático y se diluye; la parte \(\bar\rho\) es lo que todos
comparten y no se va con más activos. Cuanto más baja la correlación promedio, más bajo el piso;
con \(\bar\rho = 0\) el riesgo desaparece en el límite teórico. La ganancia de diversificar no es
todo-o-nada: la mayor parte ya se consigue con ρ moderadamente bajo, no hace falta llegar a
negativo (nadie encuentra \(\rho = -1\) entre activos de riesgo reales).
Ej.: \(\sigma _i = 30\%\), \(\bar\rho = 0.3\): N=1 → 30%, N=5 → 19.9%, N=20 → 17.4%, N=50 →
16.8%, límite \(0.3\sqrt{0.3} = 16.4\%\). Dos activos con σ=20% cada uno, 50/50: ρ=+1 → 20% (nada
ganado), ρ=0 → 14% (bajó gratis), ρ=−1 → 0% (el riesgo desaparece).

</details>

### Frontera eficiente  [C5 §1]
Cada vector de pesos da un punto \((\sigma _p, \mu _p)\). La frontera es el **borde superior** de esa nube: para cada nivel de riesgo, el máximo retorno posible. Todo lo que no está en la frontera está *dominado*.

→ MVP · portafolio_tangente · lagrangiano_de_markowitz · sharpe (C4)

<details><summary>más</summary>

Se obtiene resolviendo, para cada retorno objetivo \(\mu _p\):
\[\mathcal L(w,\lambda _1,\lambda _2) = \tfrac12 w^\top\Sigma w - \lambda _1(w^\top\mu - \mu _p) - \lambda _2(w^\top\mathbf 1 - 1),\]
y la solución cerrada da una hipérbola en el plano \((\sigma, \mu)\):
\(\sigma _p = \sqrt{(C\mu _p^2 - 2A\mu _p + B)/D}\) con \(A, B, C, D\) escalares que dependen de
\(\Sigma ^{-1}\), μ y \(\mathbf 1\). La composición óptima cambia *continuamente* a lo largo de la
curva: al pedir más retorno, la cartera se desliza suavemente de "mayoría bonos" a "mayoría
acciones", no en dos saltos.
Ej.: en la slide, miles de portafolios aleatorios de 5 activos (acciones USA/EM, bonos,
commodities, REIT) coloreados por Sharpe; la curva naranja de arriba es la frontera y ningún
punto queda por encima.

</details>

### Cartera de mínima varianza (MVP)  [C5 §1]
El punto más a la izquierda de la frontera: menor riesgo posible sin importar el retorno. \(w_{MVP} = \dfrac{\Sigma ^{-1}\mathbf 1}{\mathbf 1^\top\Sigma ^{-1}\mathbf 1}\). **No usa ninguna estimación de μ** — solo Σ.

→ frontera_eficiente · portafolio_tangente · MVP_vs_tangente · shrinkage_ledoit_wolf

<details><summary>más</summary>

Problema: \(\min _w \tfrac12 w^\top\Sigma w\) sujeto a \(w^\top\mathbf 1 = 1\) (una sola
restricción, sin retorno objetivo). Lagrangiano, derivar e igualar a cero:
\(\partial _w L = \Sigma w - \lambda\mathbf 1 = 0 \Rightarrow w = \lambda\Sigma ^{-1}\mathbf 1\);
sustituir en la restricción despeja λ y da la fórmula. Por "solo necesita Σ", muchos gestores
institucionales lo usan como base por defecto cuando desconfían de sus propios pronósticos de
retorno — la idea vuelve en §5 (risk parity tampoco usa μ).
Ej.: con 2 activos, \(\Sigma ^{-1}\mathbf 1 = \frac{1}{\sigma _1^2\sigma _2^2 - \sigma _{12}^2}\begin{pmatrix}\sigma _2^2 - \sigma _{12}\\ \sigma _1^2 - \sigma _{12}\end{pmatrix}\):
el activo 1 recibe más peso cuanto menor es su propia varianza y más alta la del otro.

</details>

### Portafolio tangente  [C5 §1]
Con un activo libre de riesgo \(r_f\), la mejor combinación ya no es un punto de la frontera: es mezclar \(r_f\) con **un solo** portafolio riesgoso, el que maximiza el Sharpe \((\mu _p - r_f)/\sigma _p\). \(w^* \propto \Sigma ^{-1}(\mu - r_f\mathbf 1)\).

→ frontera_eficiente · CML · teorema_de_separacion · MVP_vs_tangente · sharpe (C4)

<details><summary>más</summary>

Qué cambia matemáticamente al agregar \(r_f\): es determinístico (varianza cero, no correlaciona
con nada), así que el portafolio invierte \(w\) en riesgosos y \(1 - w^\top\mathbf 1\) en \(r_f\):
\(\mu _p = w^\top\mu + (1 - w^\top\mathbf 1)\,r_f\), pero \(\sigma _p^2 = w^\top\Sigma w\) **exactamente
igual que antes**. El retorno gana un término; el riesgo sigue viniendo 100% de los activos
riesgosos. Y \(1 - w^\top\mathbf 1\) puede ser negativo: pedir prestado a \(r_f\) para invertir más
del 100% en riesgosos (apalancamiento), que amplifica retorno esperado y riesgo en la misma
proporción.
Ej.: \(w^\top\mathbf 1 = 1.5\) es 150% en riesgosos con el 50% extra prestado a \(r_f\). En la
práctica hay margin requirements y la tasa de préstamo no es exactamente \(r_f\), pero el modelo
lo permite sin restricción.

</details>

### Capital Market Line (CML)  [C5 §1]
\(E[R_P] = r_f + \sigma _P\cdot\dfrac{E[R_M] - r_f}{\sigma _M}\). El retorno esperado crece **lineal** con el riesgo; la pendiente es el Sharpe del portafolio de mercado. A la izquierda de M prestás, a la derecha pedís prestado.

→ portafolio_tangente · teorema_de_separacion · CAPM_como_equilibrio · SML (C4)

<details><summary>más</summary>

Es la recta que sale de \((0, r_f)\) y toca la frontera en el tangente M; domina a toda la
frontera porque para cada σ da más retorno. La misma recta a los dos lados de M: mezclar con
\(r_f\) (\(w_M < 1\)) o apalancarse (\(w_M > 1\)). Ojo con el apalancamiento: al ir 2×, lo que se
duplica es \(\sigma _P\) **y el exceso** sobre \(r_f\), no el retorno total — \(r_f\) es la
ordenada al origen y no escala. No confundir con la SML de C4: la CML grafica retorno contra
σ *total* y solo vale para portafolios eficientes; la SML grafica contra β y vale para
cualquier activo.
Ej.: \(r_f = 3\%\), M con \(\sigma _M = 12\%\), \(\mu _M = 9\%\) (Sharpe 0.50). Apalancado 2×:
\(\sigma = 24\%\), \(\mu = 3\% + 24\%\cdot 0.5 = 15\%\) — no 18%.

</details>

### Teorema de separación (Tobin)  [C5 §1]
Con \(r_f\) disponible, **todos** los inversores, sea cual sea su aversión al riesgo, deberían tener la **misma** cartera de activos riesgosos (M). Solo cambia cuánto mezclan con \(r_f\). "Separación en dos fondos."

→ portafolio_tangente · CML · CAPM_como_equilibrio

<details><summary>más</summary>

Dos caminos que parecen distintos llegan al mismo portafolio. Directo: maximizar el Sharpe —
el cociente no cambia si escalás \(w\), por eso no hace falta la restricción \(w^\top\mathbf 1 = 1\)
desde el arranque. Largo: resolver Lagrange "minimizar varianza dado un retorno objetivo
\(\alpha _0\)" con \(r_f\) incluido, para cualquier \(\alpha _0\): la solución es
\(w_0 = \lambda _1(\alpha _0)\cdot\Sigma ^{-1}(\mu - r_f\mathbf 1)\). La **dirección**
\(\Sigma ^{-1}(\mu - r_f\mathbf 1)\) es siempre la misma; solo cambia el escalar adelante. Como
todos apuntan a la misma dirección, todos tienen el mismo Sharpe; el tangente es el único con
\(w^\top\mathbf 1 = 1\).
Ej.: un jubilado y un trader de 25 años deberían tener la misma cartera riesgosa; el jubilado
la mezcla con 70% de T-bills, el trader se apalanca 1.5×.

</details>

### MVP vs. tangente  [C5 §1]
**MVP**: ¿cuál es el portafolio de *menor* riesgo? Solo necesita Σ. **Tangente**: ¿cuál tiene *mejor* retorno por unidad de riesgo? Necesita μ, Σ y \(r_f\) — y es tan bueno como la calidad de tu estimación de μ, el input más ruidoso de todos.

→ MVP · portafolio_tangente · el_problema_son_los_inputs · michaud

<details><summary>más</summary>

Menos riesgo pide más bonos (el activo más tranquilo); más Sharpe pide más acciones (el activo
con mayor μ). El diversificador de baja correlación con ambos (oro) se mantiene estable en los
dos. Entre MVP y tangente, la frontera resuelta para cada retorno objetivo muestra la
composición deslizándose suavemente.
Ej.: SPY (acciones USA), TLT (bonos largos), GLD (oro) con \(\mu \approx (10, 3, 6)\%\),
\(\sigma \approx (18, 12, 15)\%\) y \(\rho (\text{SPY},\text{TLT}) \approx -0.30\) (¡negativa! —
TLT sube cuando SPY cae, esa es la clave). MVP: \(w \approx (29, 53, 18)\%\), \(\sigma \approx
8.0\%\). Tangente con \(r_f = 3\%\): \(w \approx (55, 18, 26)\%\), Sharpe ≈ 0.44. En 2022 esa
correlación se volvió positiva (suben y bajan juntos) y la frontera histórica colapsó.

</details>

### CAPM como equilibrio  [C5 §1]
Si **todos** los inversores hacen Markowitz + Tobin y toda acción emitida se termina vendiendo, el portafolio tangente tiene que ser el mercado por capitalización. De ahí sale la misma fórmula \(E[r_i] = r_f + \beta _i(E[r_m] - r_f)\) de C4 — pero como **consecuencia**, no como ajuste estadístico.

→ CAPM (C4) ⚡ · teorema_de_separacion · black_litterman_prior · fama_french (C4)

<details><summary>más</summary>

C4: β se *estima* con OLS sobre datos históricos; es una herramienta ("esto ajusta
razonablemente bien") que no supone nada sobre los demás inversores. C5: β *sale* de la teoría
sin datos, **si** todos hacen Markowitz; es una consecuencia ("esto tiene que pasar si el
mercado se vacía"), y β mide la contribución de cada acción al riesgo del portafolio de
mercado. La realidad usa la versión empírica; la teórica es la justificación de por qué tiene
sentido. Fama-French vuelve con la misma pregunta: para FF, SMB y HML son riesgos sistémicos de
equilibrio (empresas frágiles, ilíquidas, propensas a shocks) que exigen prima; para la visión
conductual, son anomalías por ineficiencia (sesgos que sobrevaloran el crecimiento y castigan
de más a las acciones poco analizadas).
Ej.: esta lectura es la que Black-Litterman invierte en §3: si el mercado *es* el tangente,
de sus pesos se puede despejar qué μ lo hace óptimo.

</details>

## §2 Por qué Markowitz falla en la práctica

### El problema son los inputs, no la matemática  [C5 §2]
Markowitz asume μ y Σ **conocidos**. En la realidad se estiman con datos históricos, y C4 ya mostró lo ruidosas que son esas estimaciones. μ es el peor: distinguir un retorno esperado del ruido pide muchos más años de los que hay. No es un descuido del modelo — es su limitación central.

→ beta_se_mueve (C4) · michaud · maldicion_de_la_dimensionalidad · de_valuar_a_estimar (C4)

<details><summary>más</summary>

El error estándar de una media es \(\sigma/\sqrt T\): con \(\sigma = 20\%\) anual hacen falta
~100 años para que el intervalo de μ tenga ±2%. Σ se estima algo mejor (la varianza se
identifica con la frecuencia, no con el horizonte), pero también con error, y son muchos más
números. La secuencia del resto de la clase es "qué input ataca cada cura": shrinkage → Σ;
Black-Litterman → μ; risk parity → no usa μ; HRP → no invierte Σ.
Ej.: en C4 un α tardaba décadas en confirmarse — ese mismo α es el que el optimizador toma como
dato exacto.

</details>

### Michaud (1989): optimizadores de error  [C5 §2]
El optimizador no distingue señal de ruido: le da **más peso** a los activos con la estimación de retorno **más optimista** — que suele ser, justamente, la más ruidosa. Resultado típico: carteras "de esquina", concentradas en 2-3 activos con posiciones extremas.

→ el_problema_son_los_inputs · colapso_out_of_sample · black_litterman_prior · portafolio_tangente

<details><summary>más</summary>

Es el nombre que Richard Michaud le puso al problema que Black y Litterman veían a diario en
Goldman (§3). La sensibilidad es brutal cuando hay activos parecidos: el optimizador los trata
como sustitutos y vuelca todo al que tenga el μ apenas mayor. Con ±2% de perturbación en los
retornos esperados, los pesos del tangente fluctúan violentamente (cajas de 0% a 70% para un
mismo activo).
Ej.: A y B casi gemelos (\(\rho = 0.98\)): con \(\mu _B - \mu _A = 0\) reciben 25% cada uno; con
+0.2 pp (del orden del error normal de estimación) B se lleva 50% y A cae a 0. Un tercer activo
diversificador C queda estable en ~50% — el daño es entre los sustitutos.

</details>

### Maldición de la dimensionalidad  [C5 §2]
Σ tiene \(N\) varianzas + \(N(N-1)/2\) covarianzas, todas estimadas con la **misma** historia. Crecimiento cuadrático: con N=10 son 45 covarianzas, con N=50 son 1275 parámetros. Regla práctica: hacen falta del orden de \(N^2\) observaciones.

→ riesgo_de_portafolio · shrinkage_ledoit_wolf · HRP · PCA (C4) · ridge_lasso (C4)

<details><summary>más</summary>

Cada covarianza queda más ruidosa cuantas más haya que estimar a la vez — no es gratis agregar
activos. El error relativo de \(\hat\Sigma\) crece con N para T fijo, y con 60 observaciones ya
supera 1 (inestable) para N > 10; con 5 años diarios (1260 obs.) aguanta hasta N ≈ 25. Es la
misma tensión que Ridge/Lasso en C4 (más parámetros que datos) y la misma que PCA resolvía
comprimiendo la curva en 3 factores — acá la cura es shrinkage (Σ hacia algo simple) o HRP
(no invertir Σ).
Ej.: N=50 acciones diarias → 1275 números; con la regla \(N^2 = 2500\) observaciones son ~10
años de historia, durante los cuales la Σ "verdadera" cambió varias veces.

</details>

### Colapso out-of-sample  [C5 §2]
El portafolio óptimo se ve hermoso con los datos que lo construyeron (in-sample). Con datos nuevos suele rendir **peor que un 1/N** equi-ponderado. DeMiguel, Garlappi & Uppal (2009): 14 modelos de asignación, ninguno le ganó de forma consistente al 1/N ingenuo.

→ michaud · maldicion_de_la_dimensionalidad · ridge_lasso (C4) ⚡ · walk_forward · lopez_de_prado (C6)

<details><summary>más</summary>

Es exactamente el sobreajuste de C4: más "libertad" para ajustar el pasado no es gratis. La
simulación de la slide lo aísla: N=10 activos con μ y Σ verdaderos conocidos, 500 repeticiones,
*sin* cambio de régimen — solo ruido de estimación. Markowitz ajustado in-sample da Sharpe 0.61;
evaluado con datos nuevos de la **misma** distribución cae a 0.25; el 1/N evaluado en las mismas
condiciones da 0.32. Es decir: aun si el mundo no cambiara, estimar μ y Σ y optimizar sobre
ellos pierde contra no estimar nada.
Ej.: el 1/N no tiene parámetros que estimar, así que no tiene error de estimación que
amplificar — ese es todo su secreto.

</details>

### Shrinkage de Σ y Ledoit-Wolf (2004)  [C5 §2]
\(\Sigma _{\text{shrink}} = \delta\,F + (1-\delta)\,\hat\Sigma\): un promedio ponderado entre "confiar en los datos" (\(\hat\Sigma\) cruda) y "confiar en una estructura simple" (F: identidad, o "todos correlacionan igual"). Ledoit-Wolf da el \(\delta ^*\) óptimo con una fórmula: `sklearn.covariance.LedoitWolf()`.

→ ridge_lasso (C4) ⚡ · maldicion_de_la_dimensionalidad · black_litterman_prior · HRP

<details><summary>más</summary>

Bias-variance trade-off, la misma idea que Ridge: un estimador con menos varianza (más
estable), aunque tenga un poco de sesgo, predice mejor que uno "perfecto" pero ruidoso. Ridge
encogía un *vector* β hacia cero; acá se encoge una *matriz* entera hacia una estructura simple.
\(\delta = 0\) es la estimación cruda (sesgo 0, toda la varianza); \(\delta = 1\) es ignorar los
datos (varianza 0, todo el sesgo); el error total es la suma y tiene mínimo en un punto
intermedio, nunca en los extremos. Ledoit y Wolf encontraron la fórmula del δ que minimiza el
error esperado, calculada de los mismos datos — no hay que adivinarlo. **Pero solo ataca Σ**:
μ, el input más ruidoso, sigue intacto. Para eso hace falta otra idea → Black-Litterman.
Ej.: N=20, T=30, 300 corridas: \(\delta ^* \approx 0.30\); el error de Frobenius baja de 0.025
(cruda) a 0.017.

</details>

## §3 Black-Litterman: la primera solución

### La idea: partir del equilibrio, no de cero  [C5 §3]
En vez de estimar μ de datos históricos (ruidoso), arrancar de un **prior razonable**: los retornos implícitos \(\Pi\) que hacen que el portafolio de mercado sea el óptimo — el CAPM invertido. Se toman los pesos de capitalización de hoy y se despeja qué μ los haría óptimos.

→ CAPM_como_equilibrio · views · formula_black_litterman · shrinkage_ledoit_wolf

<details><summary>más</summary>

Fischer Black (el de Black-Scholes) y Robert Litterman, en Goldman Sachs, 1990 (publicado
1992). El problema que veían a diario: los portafolios de Markowitz que armaban para clientes
eran inestables, poco intuitivos, con cortos absurdos. Nació de la práctica, no de la academia.
Ingeniería inversa del tangente: si \(w_{mkt} \propto \Sigma ^{-1}(\mu - r_f\mathbf 1)\), entonces
\(\Pi = \lambda\,\Sigma\,w_{mkt}\) con λ la aversión al riesgo del mercado. En vez de estimar
μ desde cero, se lo *despeja* de los pesos que ya observamos.
Ej.: si Apple pesa 7% del S&P 500 hoy, ese 7% ya contiene la opinión colectiva del mercado
sobre su retorno esperado.

</details>

### Views  [C5 §3]
Una *view* es una opinión concreta y cuantificada sobre el futuro — "tecnología le gana al mercado en 2 pp este año" — con un nivel de **confianza**. Pueden ser absolutas o **relativas** entre dos activos ("oro le gana a bonos en 3%") sin decir nada del resto. No hace falta opinar sobre todos los activos.

→ black_litterman_prior · formula_black_litterman · views_subjetivas

<details><summary>más</summary>

La combinación es literalmente una regla de Bayes: prior (equilibrio) + evidencia (views) →
posterior (μ ajustado). Cuanta más confianza le pongas a una view, más se mueve el retorno
esperado hacia lo que creés; con poca confianza el resultado queda cerca del equilibrio y no
vuela la cartera. Caso límite para entender la fórmula: confianza cero en todas las views →
BL devuelve exactamente el equilibrio, ninguna sorpresa.
Ej.: view relativa "oro − bonos = 3%" toca solo dos activos; los otros tres quedan en su prior.

</details>

### La fórmula de Black-Litterman  [C5 §3]
\[E[R] = \left[(\tau\Sigma)^{-1} + P^\top\Omega ^{-1}P\right]^{-1}\left[(\tau\Sigma)^{-1}\Pi + P^\top\Omega ^{-1}Q\right].\]
\(\Pi\): prior de equilibrio. \(P, Q\): qué activos toca cada view y qué dice. \(\Omega\): la confianza en cada view, como una varianza (Ω chico = mucha confianza). \(\tau\): cuánto se confía en el prior.

→ black_litterman_prior · views · por_que_arregla_michaud · shrinkage_ledoit_wolf

<details><summary>más</summary>

Es la media posterior de dos normales: el prior \(\mu \sim N(\Pi, \tau\Sigma)\) y la evidencia
\(P\mu \sim N(Q, \Omega)\). Precisiones (inversas de varianzas) se suman; el resultado es un
promedio de Π y las views ponderado por precisión — exactamente la forma del shrinkage de §2,
pero sobre μ. Verificación del caso límite: si \(\Omega \to \infty\) (confianza cero), el término
de las views desaparece y \(E[R] \to \Pi\) exacto.
Ej.: views "Oro +3% sobre Bonos" y "EM +2% sobre USA" sobre 5 activos: Oro pasa de 0.84% (prior)
a 2.01% (posterior), EM de 6.70% a 7.57%, y USA/Bonos/REIT casi no se mueven. El vector de
retornos se *tilta* suavemente, no se reemplaza.

</details>

### Por qué arregla el problema de Michaud  [C5 §3]
El punto de partida ya es razonable (equilibrio), no una estimación ruidosa desde cero. Una view individual, aunque esté mal, mueve **una parte** de μ, no todo. El prior es un **ancla** que impide que un solo número ruidoso vuele la cartera. BL no *resuelve* la estimación de μ — la **esquiva**, empezando de un lugar mejor.

→ michaud · formula_black_litterman · limites_de_BL · risk_parity

<details><summary>más</summary>

Por eso es uno de los modelos más usados en gestión institucional (fondos, family offices,
algunos bancos): combina rigor con algo que un gestor humano puede *explicar* — "esta cartera,
porque tal view, con tal confianza". También lo usan bancos centrales y fondos soberanos, donde
una cartera "rara" es políticamente costosa de justificar. Disponible en `PyPortfolioOpt`; no
hace falta programarlo desde cero.
Ej.: en la slide, los pesos BL con views quedan diversificados sobre los mismos activos que el
equilibrio, mientras que Markowitz crudo con esos mismos μ habría concentrado en 2.

</details>

## §4 Los límites de Black-Litterman

### Sigue necesitando Σ  [C5 §4]
BL ataca μ, pero Σ sigue siendo la estimada de siempre — y aparece **dos veces** en la fórmula (para despejar Π y para combinarlo con las views). Todo el problema de §2 sigue intacto. BL (μ) y shrinkage (Σ) atacan problemas **distintos**: en la práctica profesional se combinan.

→ formula_black_litterman · shrinkage_ledoit_wolf · maldicion_de_la_dimensionalidad

<details><summary>más</summary>

Shrinkage no es parte del modelo BL en sí mismo; es un preprocesamiento de Σ que se le pasa.
El pipeline institucional típico: Σ por Ledoit-Wolf → Π despejado de los pesos de mercado →
views del comité → posterior → optimizar (con restricciones de no-cortos y de concentración
encima, porque nadie confía del todo).
Ej.: un error del 20% en \(\sigma _{\text{oro,bonos}}\) mueve Π y mueve la ponderación de la view
"oro − bonos = 3%" a la vez.

</details>

### El equilibrio como ancla es un supuesto fuerte  [C5 §4]
Todo el prior asume que el mercado **hoy** está razonablemente bien valuado. Si hay una burbuja, el prior hereda ese error y no lo corrige. No es una falla técnica: es una elección filosófica — "confiar en el mercado como punto de partida".

→ black_litterman_prior · CAPM_como_equilibrio · dQ_dP (C4)

<details><summary>más</summary>

Es el precio de esquivar la estimación de μ: reemplazás "lo que dicen los datos históricos" por
"lo que dice el mercado", y el mercado también se equivoca — solo que de forma distinta. Un BL
sin views es, literalmente, comprar el índice por capitalización.
Ej.: en la burbuja puntocom (1999-2000) los pesos de mercado sobrevaloraban sistemáticamente a
las tecnológicas; un prior BL de esa época heredaba esa distorsión y la presentaba como
"equilibrio".

</details>

### Las views siguen siendo subjetivas  [C5 §4]
¿De dónde sale "tech le gana al mercado en 2%"? De un analista, un modelo propio, una corazonada. ¿Y la confianza? También es una elección humana. BL no elimina el juicio humano — lo hace **controlable**, no lo hace desaparecer. Puente a §5: ¿y si dejamos de pelear con μ?

→ views · risk_parity · risk_budgeting

<details><summary>más</summary>

En la industria las views salen de un comité de inversión, no de una fórmula: la subjetividad
es humana, no estadística. Markowitz necesita μ y Σ; BL mejora μ pero sigue siendo, en el
fondo, un problema de estimación. La alternativa es un cambio de **pregunta**, no solo de método:
en vez de "¿cuánto va a rendir cada activo?", preguntar "¿cuánto riesgo quiero tomar en cada
uno?" — mucho más fácil de responder con confianza que un pronóstico de retorno.
Ej.: un comité puede acordar "no más del 30% del riesgo en emergentes" mucho más fácil que
"emergentes va a rendir 7.5%".

</details>

## §5 Risk Parity: igualar riesgo, no capital

### Contribución al riesgo vs. capital  [C5 §5]
Markowitz y BL reparten **capital** según cuánto retorno promete cada activo. Risk parity reparte **riesgo**: cada activo debería aportar la misma cantidad de riesgo total. Un 60/40 está balanceado en plata, no en riesgo: las acciones son 60% del capital y **92% de la varianza**.

→ riesgo_de_portafolio · risk_parity_pesos · risk_budgeting · all_weather

<details><summary>más</summary>

La contribución de \(i\) al riesgo es \(w_i\,(\Sigma w)_i / \sigma _p^2\) — su peso por su
covarianza con la cartera entera; suman 1 por Euler. Con activos de volatilidad muy distinta, el
capital y el riesgo se desalinean mucho: para que el riesgo quede 50/50, el capital se
desbalancea *a propósito* hacia el activo tranquilo.
Ej.: 60/40 con \(\sigma _{\text{acc}} = 16\%\), \(\sigma _{\text{bonos}} = 6\%\), \(\rho = 0.1\):
acciones aportan 92% de la varianza, bonos 8%. Acciones con el doble de vol que bonos → capital
33/67 para riesgo 50/50.

</details>

### Cómo se arma: pesos inversamente proporcionales al riesgo  [C5 §5]
Sin correlaciones: \(w_i \propto 1/\sigma _i\) — al activo más volátil, menos capital. Con correlaciones, hay que igualar la contribución **marginal** de cada uno (una optimización numérica). **No hace falta μ para nada**: solo σ y correlaciones — otra vez un problema de covarianza.

→ contribucion_al_riesgo · MVP · risk_parity_necesita_sigma · HRP

<details><summary>más</summary>

Comparte con MVP el "solo necesita Σ", pero responde otra pregunta: MVP minimiza el riesgo
total (y por eso concentra en el activo más tranquilo); risk parity lo *reparte* (y por eso
diversifica por construcción). Es el caso intermedio entre 1/N (ignora Σ) y MVP (la explota al
máximo).
Ej.: \(\sigma _{\text{acc}} = 20\%\), \(\sigma _{\text{bonos}} = 8\%\) → \(w_{\text{acc}} \propto
1/20\), \(w_{\text{bonos}} \propto 1/8\): los bonos reciben 2.5 veces más capital que las acciones
(≈ 29% / 71%).

</details>

### Dalio, Bridgewater y el All Weather  [C5 §5]
Ray Dalio (Bridgewater, el hedge fund más grande del mundo) popularizó la idea en los 90. El fondo *All Weather* busca rendir razonablemente bien en **cualquier** escenario macro — inflación, deflación, crecimiento, recesión — balanceando riesgo entre escenarios, no capital.

→ contribucion_al_riesgo · risk_budgeting · actores_institucionales (C1)

<details><summary>más</summary>

La lógica: nadie sabe qué escenario macro viene, así que en vez de apostar a uno, se balancea
el riesgo entre todos los posibles. Cada escenario tiene activos que le van bien (crecimiento →
acciones; inflación → commodities, TIPS; deflación → bonos largos), y se les asigna el mismo
presupuesto de riesgo. Es una de las estrategias institucionales más replicadas del mundo, no
una curiosidad académica; Bridgewater arrancó en 1975 en un departamento de Nueva York y hoy
administra más de USD 100 mil millones.
Ej.: como los bonos tienen mucha menos vol que las acciones, un All Weather termina con mucho
más capital en bonos — y típicamente *apalancado* para llevar el riesgo total al nivel deseado.

</details>

### Risk budgeting  [C5 §5]
Risk parity es el caso particular "todos aportan el **mismo** riesgo". Risk budgeting generaliza: asignar deliberadamente más presupuesto de riesgo donde hay más convicción, menos donde hay menos. El presupuesto lo define el inversor, no una estimación de retorno.

→ contribucion_al_riesgo · views · black_litterman_prior

<details><summary>más</summary>

Es el puente natural entre "no confío en μ para nada" (risk parity puro) y "tengo algo de
convicción pero no quiero apostar todo" (Black-Litterman): la convicción entra como
*proporción de riesgo*, no como número de retorno. Sigue sin necesitar μ como input directo.
Ej.: un fondo que confía más en su research de crédito que en acciones le asigna 40% del
presupuesto de riesgo a crédito en vez de igualarlo con el resto.

</details>

### El problema que risk parity sí tiene: Σ  [C5 §5]
Evita el problema de μ por completo, pero calcular "contribución al riesgo" todavía necesita Σ (varianzas **y** correlaciones). Con muchos activos, invertir o manipular Σ sigue siendo numéricamente inestable — el mismo problema de §2. Cambia de problema, no lo elimina.

→ maldicion_de_la_dimensionalidad · shrinkage_ledoit_wolf · HRP

<details><summary>más</summary>

Shrinkage se puede combinar acá también — no es exclusivo de Markowitz. Pero la cura más
radical es no invertir Σ en absoluto: HRP.
Ej.: con 50 activos, la cuenta de contribución marginal pasa por \(\Sigma w\) con una Σ de 1275
parámetros ruidosos.

</details>

### HRP: Hierarchical Risk Parity (López de Prado, 2016)  [C5 §5]
En vez de invertir Σ de una sola vez, **clustering jerárquico**: (1) agrupar los activos más correlacionados entre sí (acciones con acciones, bonos con bonos) en un árbol; (2) repartir el capital de arriba hacia abajo en ese árbol, sin álgebra matricial pesada. Evita invertir la matriz completa → mucho más estable con muchos activos.

→ risk_parity_necesita_sigma · maldicion_de_la_dimensionalidad · lopez_de_prado (C6) · PCA (C4)

<details><summary>más</summary>

La distancia entre activos es \(\sqrt{(1-\rho _{ij})/2}\); el dendrograma agrupa
oro/commodities, luego acciones/REITs, luego bonos/HY. En cada bifurcación el capital se
reparte en proporción inversa a la varianza de cada rama, y se baja recursivamente. El resultado
es "ni tan plano como 1/N, ni tan concentrado como MVP". Es la misma jugada que PCA en C4: usar
la *estructura* de Σ (pocos grupos) en vez de sus N² números crudos.
Ej.: 8 ETFs con vol entre 5% y 22%: equal weight σ=8.2%; mínima varianza concentra 78% en un
solo activo (bonos medios), σ=4.6%; HRP máximo 46% y peso real en 4-5 activos, σ=5.3% — casi el
riesgo de MVP sin la concentración extrema.

</details>

## §6 El backtest honesto: ¿funciona afuera de la muestra?

### Comparación final: qué necesita cada familia  [C5 §6]
| | Markowitz | Black-Litterman | Risk Parity / HRP |
|---|---|---|---|
| ¿Necesita estimar μ? | Sí, crudo | Sí, anclado al equilibrio | No |
| ¿Necesita Σ completa? | Sí | Sí | Sí (HRP evita invertirla) |
| Estabilidad práctica | Baja | Media-alta | Alta |
| Filosofía | Optimizar retorno/riesgo | Bayes: prior + views | Igualar riesgo, no capital |

→ MVP_vs_tangente · por_que_arregla_michaud · HRP · walk_forward

<details><summary>más</summary>

Leída por columnas es la historia de la clase: cada familia sacrifica algo de "optimalidad"
teórica a cambio de estabilidad, porque la optimalidad teórica dependía de inputs que no se
tienen. Leída por filas: μ es el input que todas intentan esquivar; Σ es el que ninguna puede
esquivar del todo.
Ej.: un gestor que desconfía de sus pronósticos va a la derecha de la tabla; uno con research
propio y un comité que lo respalde, al medio.

</details>

### Backtest walk-forward  [C5 §6]
La forma correcta de simular una estrategia en el tiempo usando **solo** información que existía en cada momento: en cada fecha de rebalanceo, mirar los últimos 252 días hacia atrás, calcular los pesos con eso, sostenerlos y medir el retorno **real** del próximo trimestre; repetir. La ventana "camina" con el calendario.

→ colapso_out_of_sample · lopez_de_prado (C6) · practica_C4 (Ej. 3)

<details><summary>más</summary>

La alternativa tramposa: usar *toda* la historia (incluido el futuro) para fijar los pesos de
todo el período. Eso es **look-ahead bias**, y hace que cualquier estrategia se vea mejor de lo
que hubieras logrado. Es la versión más simple de un problema que C6 retoma en serio (purged
K-fold, embargo): con datos financieros, hasta un walk-forward como este puede tener fugas
sutiles (solapamiento de ventanas, parámetros elegidos mirando el resultado).
Ej.: rebalancear en marzo 2020 con una Σ estimada sobre 2019-2020 completo "sabe" que viene el
crash — un walk-forward honesto solo ve hasta febrero.

</details>

### No hay ganador universal  [C5 §6]
Mismo backtest walk-forward, 8 ETFs reales, dos ventanas: en **2016-2026** (bull run) gana equal weight; en **2008-2020** (crisis + recuperación) ganan mínima varianza y HRP. Quién gana depende del régimen que le toque a la muestra.

→ walk_forward · HRP · colapso_out_of_sample · VaR_condicional (C4)

<details><summary>más</summary>

Sharpe y drawdown *calculados* de la curva realizada, no elegidos de antemano. 2017-2026:
EW 0.66 vs. MV 0.46 vs. HRP 0.52 — la menor volatilidad de MV/HRP no alcanzó a compensar su
menor retorno; drawdowns similares (17.5%-19.4%). 2008-2020: MV 0.60 y HRP 0.44 vs. EW 0.36;
en la crisis de 2008 sola, el drawdown de EW llegó a −36.9% contra −14.7% de HRP. Lo que las
familias de riesgo compran no es más Sharpe en promedio: es **protección en las colas**, que
solo se ve cuando la muestra incluye una.
Ej.: todo lo de hoy se puede (y se debe) comparar con un backtest honesto — pero backtestear
estrategias de asignación tiene sus propias trampas, y la clase 6 las pone sobre la mesa.

</details>

## ❓ Dudas de C5

*(Por completar después de repasar la clase.)*

Como puede ser posible que si se usan tecnicas similares entre todos, se logre una diferencia. Como el riesgo de la mayoria de los serctores fiannecieros debe de definir el mercado actual.

Como estimo al mercado presente? 

Cuan riesgoso es apostar al futuro largo?

Problematica de las views con el sesgo general (algo debe haber ahi).

Pensar en el S&P como logica de market benchmark y como todo se piensa en torno a eso. (como jugar con las acciones sesgadas del S&P y pensar como jugarle en contra/favor)

HRP

se puee modelar merados que no uedo predecir

---

# C6 — Machine Learning: el pipeline riguroso

## Idea general

**En una frase.** Si le aplicás a datos financieros el pipeline que un data scientist usa por
reflejo (datos → features → modelo → cross-validation → accuracy alto → producción), obtenés
modelos que se ven buenísimos y pierden plata. La clase es López de Prado: una herramienta por
cada supuesto que el mercado rompe, y la tesis de que **el modelo es lo de menos**. El rigor
está en las puntas (cómo muestreás, qué etiquetás, cómo validás, cuántas cosas probaste), no
en elegir XGBoost en vez de un random forest.

**Qué problema del mercado resuelve.** C5 dejó abierto de dónde sale μ: la media histórica
tiene error \(\sigma/\sqrt{T}\), enorme, y Black-Litterman la reemplaza por equilibrio + views.
La promesa del ML es *aprender* esas views: retornos esperados condicionales al estado del
mercado. Si hay señal, aunque sea débil, un modelo puede captarla mejor que la media. Pero
con tan poca señal y tanto ruido, cualquier modelo flexible memoriza el ruido, y el backtest
es la única historia que hay: no se puede repetir el experimento. Lo que un desk necesita no
es un modelo más potente, sino un **proceso que no se mienta**: dollar bars, triple barrera,
meta-labeling, purged K-fold, Deflated Sharpe.

**Cómo conecta.** Hacia atrás: Ridge/Lasso de C4 §7 ya eran ML, y ahí quedó planteado que
K-fold miente con series de tiempo; hoy se resuelve (purga + embargo). El zoológico de
factores de C4 era multiple testing; hoy se cuantifica con DSR y PBO ⚡. El walk-forward y
el look-ahead bias de C5 §6 vuelven, ahora con purga. La vol de ventana corta (o GARCH, C4)
dimensiona las barreras; la estacionariedad y el test ADF de C4 §4 deciden cuánto diferenciar;
las colas gordas del GARCH son las que las dollar bars achican. El T≥10N de C5 reaparece
como "cuánta información independiente tengo". Tercer hilo ⚡: regularizar (L1 en la
logística, `min_samples_leaf`, dropout) es otra vez encoger hacia algo simple, y en finanzas
el punto óptimo está *muy* a la izquierda. Hacia adelante: el lado del próximo tick del LOB
(market making) y RL para ejecución (Almgren-Chriss) aparecen en la Unidad 2/3; LSTM y
CNN-1D sirven sobre todo en alta frecuencia, donde sí hay datos de sobra.

> Arco de la clase — *Una herramienta por problema.*
> §1 por qué el ML de manual falla (siete problemas + un adversario que se adapta) →
> §2 datos: dollar bars → §3 etiquetas: triple barrera + meta-labeling → §4 pesos: unicidad +
> diferenciación fraccionaria → §5 validación: purged K-fold + embargo → §6 modelos
> (logística, árboles, RF, boosting, redes) y qué aprendieron (MDI/MDA) → §7 el veredicto:
> Deflated Sharpe + PBO → §8 AUC y curva de aprendizaje.

## §1 Por qué el ML de manual falla en finanzas

### Dónde aparece cada paradigma en un desk  [C6 §1]
**Clasificación:** ¿supera al benchmark en T+1? ¿cambia el régimen de vol? ¿qué lado toma el próximo tick? **Regresión:** IV futura de un strike, retorno condicional. **Clustering:** segmentar el universo antes de entrenar. **Redes/RL:** memoria en secuencias cortas, minimizar impacto de ejecución.

→ smile_de_volatilidad (C3) · market_maker (C1) · almgren_chriss (U2) · meta_labeling

<details><summary>más</summary>

El clustering va *antes* del modelo: en vez de un solo modelo para las 500 acciones del S&P
(que mezcla bancos con tecnológicas), un modelo por grupo parecido — la misma idea que el
dendrograma de HRP en C5. La pregunta de fondo de las redes recurrentes es si el precio es
Markoviano (todo lo relevante está en el último precio) o si hay memoria real que explotar.
Ej.: predecir la IV de un strike a 30 días le sirve a un desk de opciones para armar
calendar spreads (C2) apostando a vega.

</details>

### Tres familias de features  [C6 §1]
(1) **Momentum y reversión a la media** (RSI y similares), (2) **flujo institucional** (volumen, put/call ratio, open interest, 13F), (3) **fundamentales y macro** (P/E, P/B, β, correlaciones con FX y commodities). Regla: **más features no es mejor** — 5 limpias le ganan a 200 ruidosas.

→ momentum (C4) · P/E (C1) · beta (C4) · multiple_testing · lasso (C4)

<details><summary>más</summary>

**Momentum** (trend-following): subió N días → seguir; son señales tardías, confirman después
del movimiento. **Contrarian**: subió demasiado rápido → probablemente vuelva al promedio.
**RSI**: compara la suba promedio contra la baja promedio en una ventana de N días; va de 0 a
100, >70 "sobrecomprado", <30 "sobrevendido". **13F**: los fondos con más de $100M
reportan sus posiciones a la SEC cada trimestre — una señal pública (y lenta) de hacia dónde
apuestan los institucionales. Cada feature extra es una oportunidad más de encontrar ruido
que parece señal: es el zoológico de factores de C4 dentro de tu propio modelo.
Ej.: un modelo con 200 indicadores técnicos va a encontrar alguno "significativo" en
cualquier muestra; uno con momentum, vol y volumen tiene menos formas de engañarte.

</details>

### Los siete problemas estructurales  [C6 §1]
(1) datos **no IID**, (2) **señal/ruido ínfimo**, (3) **no estacionariedad**, (4) **leakage**, (5) **overfitting**, (6) **multiple testing**, (7) el backtest es **irrepetible**. Cada herramienta de la clase ataca uno.

→ pipeline_lopez_de_prado · estacionariedad (C4) · zoologico_de_factores (C4) · colapso_out_of_sample (C5)

<details><summary>más</summary>

sklearn asume muestras independientes y de la misma distribución; los retornos tienen
autocorrelación, clustering de vol (GARCH, C4), y el mercado de 2010 no es el de 2020. El
pasado caduca, y caduca *endógenamente* (ver adversario adaptativo). El leakage es el futuro
filtrándose al entrenamiento por vías que no se ven en el índice. Con poca señal, cualquier
modelo flexible memoriza ruido. Si probaste 100 variantes, la mejor está sesgada hacia
arriba. Y hay una sola historia: no se puede "probar con nuevos datos del pasado".
Ej.: un modelo entrenado sobre 2015-2019 no tenía cómo saber qué hacer en marzo 2020 (COVID).

</details>

### Señal/ruido: ¿51% es suerte o habilidad?  [C6 §1]
En 252 días hábiles, acertar el 51% son 127 aciertos contra 125 fallos: indistinguible de una moneda. Un 55% sostenido 5 años (~1260 días) ya es **extraordinario**.

→ siete_problemas · deflated_sharpe · AUC

<details><summary>más</summary>

Con una moneda justa, la tasa de acierto en n días tiene desvío \(0.5/\sqrt{n}\). En un año,
\(0.5/\sqrt{252} \approx 3.1\%\): el 51% está a 0.3 desvíos del 50%, ruido puro. En 5 años,
\(0.5/\sqrt{1260} \approx 1.4\%\): el 55% está a ~3.5 desvíos. La señal existe pero es tan
chica que hacen falta muchos años para verla — y en muchos años el mercado cambió de régimen.
Ej.: por eso en finanzas un AUC de 0.6 ya es "interesante" (§8), algo que en visión por
computadora sería un fracaso.

</details>

### El adversario adaptativo  [C6 §1]
En física el electrón no lee tus papers. El mercado sí: si tu patrón funciona y se conoce, otros lo operan y **el patrón desaparece**. La no estacionariedad no es ruido de fondo: es endógena.

→ zoologico_de_factores (C4) · no_estacionariedad · siete_problemas

<details><summary>más</summary>

McLean-Pontiff (C4): los factores publicados pierden ~50% del premio post-publicación. Tu
competencia lee los mismos libros, baja los mismos datos y corre los mismos modelos. La
ventaja sostenible viene de solo tres lugares: **datos** que otros no tienen, **rigor** que
otros no aplican, o **velocidad** (Unidad 3). Esta clase es sobre el rigor.
Ej.: esto responde en parte la duda de C5 de "cómo se logra una diferencia si todos usan
técnicas similares": la técnica no alcanza; el diferencial es datos, rigor o velocidad.

</details>

### Calibrar expectativas: el Sharpe honesto  [C6 §1]
Un Sharpe **out-of-sample** de 0.5 sostenido ya es muy bueno; 1.0 es excelente; 2+ es sospechoso. Si un backtest muestra Sharpe 3, la pregunta no es "cuánto invierto" sino "**dónde está el bug**".

→ alpha_y_sharpe (C4) · deflated_sharpe · leakage

<details><summary>más</summary>

Los Sharpe altos de un backtest suelen venir de leakage (el futuro filtrado), de haber
probado muchas variantes (multiple testing) o de ignorar costos de transacción. El resto de
la clase es la caja de herramientas para encontrar cuál de los tres es.
Ej.: los números del backtest de C5 (Sharpe 0.4–0.7) son los normales para estrategias
reales de asignación.

</details>

### El pipeline de López de Prado: una herramienta por problema  [C6 §1]
**Datos** → dollar bars · **Etiquetas** → triple barrera + meta-labeling · **Pesos** → unicidad + diferenciación fraccionaria · **Validación** → purged K-fold + embargo · **Modelo** → bagging/boosting + MDI/MDA · **Veredicto** → Deflated Sharpe + PBO. El orden importa.

→ siete_problemas · dollar_bars · triple_barrera · purged_kfold · deflated_sharpe

<details><summary>más</summary>

Cada etapa asume que la anterior se hizo bien. El error típico es sofisticar el modelo
(etapa 5) con etiquetas malas y una validación incorrecta. López de Prado invierte la
prioridad: los modelos son lo de menos (un random forest alcanza), el rigor está en datos,
etiquetas y validación.
Ej.: un XGBoost con K-fold estándar va a "ganarle" a un random forest con purged K-fold, y
va a ser el que pierda plata en producción.

</details>

## §2 Los datos: muestrear por actividad, no por reloj

### Vela (bar): OHLC  [C6 §2]
Cada vela resume un intervalo con cuatro números: **Open, High, Low, Close**. El cuerpo es el rango Open–Close (color según el signo), la mecha es lo que el precio tocó y no sostuvo. Lo único que cambia entre time/tick/volume/dollar bars es **la regla que cierra la vela**.

→ time_bars · dollar_bars

<details><summary>más</summary>

Es el bloque atómico de toda serie de precios: cualquier dato "diario" de yfinance es una
serie de velas de 1 día.
Ej.: una vela con cuerpo chico y mechas largas dice "hubo mucha pelea y nadie ganó".

</details>

### Time bars y su problema  [C6 §2]
Cortar en intervalos iguales de reloj (1 día, 1 hora, 5 min). Pero el mercado no genera información a ritmo de reloj: **sobre-muestreás la siesta y sub-muestreás el pánico**. Resultado: colas pesadas, vol agrupada, autocorrelación en \(r^2\).

→ vela_OHLC · dollar_bars · GARCH (C4) · siete_problemas

<details><summary>más</summary>

Un martes de agosto a las 12:30 no pasa nada y la time bar registra un punto igual; un día de
Fed a las 9:30 pasa todo y sigue siendo un solo punto. Esas son justo las propiedades
(colas gordas, clustering de vol) que GARCH modelaba en C4, y las que rompen los supuestos de
cualquier modelo de ML. La idea: que el reloj corra al ritmo de la **actividad**, no del
tiempo físico. Analogía física: muestrear por eventos (cada colisión), no por segundos.
Ej.: si las velas se cierran por actividad, un flash crash se parte en muchas velas y cada
retorno individual es menos extremo.

</details>

### Tick, volume y dollar bars  [C6 §2]
**Tick:** una vela cada N operaciones. **Volume:** cada N acciones. **Dollar:** cada N **dólares** operados. Las dollar bars son las más robustas, y sus retornos tienen menos curtosis y menos autocorrelación en \(r^2\): más cerca de IID-normal.

→ time_bars · siete_problemas · colas_gordas (C4) · LOB (U2)

<details><summary>más</summary>

Tick bars: una orden de 10.000 acciones partida en 100 ticks infla la actividad. Volume bars
corrigen eso, pero 100 acciones de 2010 no son 100 acciones de 2026 (el precio cambió 5×).
Dollar bars son robustas a splits, a la suba del precio y comparables entre activos y épocas.
La recompensa es medible: mejor materia prima para ML porque se violan menos supuestos.
Ej.: en SPY intradía, con la misma cantidad de barras, los retornos de dollar bars tienen
menos exceso de curtosis que los de time bars (el Ejercicio 1 de la práctica lo testea).

</details>

## §3 Las etiquetas: qué le preguntás al modelo

### Fixed-horizon y sus dos fallas  [C6 §3]
El etiquetado ingenuo: mirar el retorno a N días fijos, +1 si sube, −1 si baja. Falla 1: **ignora el camino** (el stop-loss te sacó antes). Falla 2: **ignora la vol del momento** (un +1% en calma es señal, en pánico es ruido).

→ triple_barrera · GARCH (C4)

<details><summary>más</summary>

Caíste 8% el día 3 y recuperaste el día 10: la etiqueta dice "ganaste", pero en la vida real
tu stop te sacó el día 3. La etiqueta describe un trade que nadie vivió. Y con umbral fijo y
vol variable, las etiquetas terminan midiendo el régimen de vol, no tu señal.
Ej.: con fixed-horizon, casi todas las etiquetas "extremas" caen en 2008 y 2020, porque es
cuando los retornos son grandes, no cuando la señal funcionó.

</details>

### Triple barrera  [C6 §3]
Tres barreras alrededor de cada entrada: **take-profit** (+1), **stop-loss** (−1) y **vertical** de tiempo máximo (0, o el signo del retorno). La etiqueta es **la primera que se toca**. Barreras adaptativas a la vol: \(p^{+} = p_0(1 + k\hat\sigma _t)\), \(p^{-} = p_0(1 - k\hat\sigma _t)\).

→ fixed_horizon · meta_labeling · etiquetas_solapadas · GARCH (C4)

<details><summary>más</summary>

\(k\) es un hiperparámetro; \(\hat\sigma _t\) es el desvío de los últimos 20 retornos. Se puede
usar GARCH si ya está calibrado, pero la vol histórica simple alcanza: el objetivo es
adaptar las barreras al régimen, no modelar la dinámica de la vol. En calma las barreras se
acercan, en pánico se alejan: la **misma pregunta** en todo régimen. Barreras asimétricas
(arriba ≠ abajo) expresan un sesgo direccional. La barrera vertical es la que después genera
el solapamiento de etiquetas (§4) y el leakage (§5).
Ej.: con \(k = 2\) y \(\hat\sigma _t = 1\%\) diario, entrar a $100 pone barreras en $102 y
$98; si la vol sube a 3%, pasan a $106 y $94.

</details>

### Meta-labeling  [C6 §3]
Dos modelos. El **primario** decide la **dirección** (puede ser simple: cruce de medias, un factor de C4, la intuición del gestor). El **meta-modelo** no predice el mercado: predice **cuándo el primario acierta**, y eso define el tamaño de la apuesta.

→ triple_barrera · bet_sizing_kelly · precision_vs_recall · black_litterman (C5)

<details><summary>más</summary>

Receta: (1) correr el primario y obtener señales largo/corto; (2) etiquetar cada señal con la
triple barrera (¿el trade habría ganado?); (3) entrenar el meta-modelo (un RF alcanza) con
features del momento — vol, régimen, fuerza de la señal, hora, liquidez — para estimar
P(el primario acierta); (4) en producción, tamaño = dirección_primario × f(prob_meta).
"¿Sube o baja?" y "¿es buen momento para mi señal?" no son igual de difíciles: la segunda
tiene estructura persistente (el régimen) que la primera no tiene. Triple barrera es una
herramienta de *etiquetado*; meta-labeling es un *framework de dos modelos* que usa esas
etiquetas.
Ej.: un momentum que acierta 52% en general puede acertar 60% en regímenes de vol baja y 45%
en crisis; el meta-modelo aprende a apagarlo en crisis.

</details>

### Precision vs. recall: por qué el meta-modelo sube el Sharpe  [C6 §3]
**Recall** = fracción de los trades buenos que capturaste. **Precision** = fracción de los trades que tomaste que fueron buenos. El meta-modelo **sube precision y baja recall** — y eso es exactamente lo que querés.

→ meta_labeling · AUC · bet_sizing_kelly

<details><summary>más</summary>

Sin meta operás todas las señales: recall alto (no te perdés ninguno bueno) pero precision
baja (muchos malos también). Con meta operás solo en régimen favorable: casi todos los que
tomás son buenos, y te perdés algunos buenos que no pasaron el filtro. El Sharpe sube aunque
aciertes menos veces **en total**: apostaste grande cuando había que apostar.
Ej.: 100 señales, 50 buenas. Sin meta: operás 100, precision 50%, recall 100%. Con meta:
operás 30, 24 buenas → precision 80%, recall 48%.

</details>

### Bet sizing: criterio de Kelly  [C6 §3]
De la probabilidad al tamaño: \(K\% = W - \dfrac{1 - W}{R}\), con \(W\) la tasa de acierto y \(R\) el ratio beneficio/riesgo promedio. La \(W\) puede ser la probabilidad del meta-modelo.

→ meta_labeling · regresion_logistica

<details><summary>más</summary>

Kelly maximiza el crecimiento logarítmico del capital. Si \(K \le 0\), no hay ventaja: no se
opera. En la práctica se usa una fracción de Kelly (medio Kelly), porque \(W\) es una
estimación con error y Kelly completo con \(W\) sobreestimado lleva a la ruina.
Ej.: \(W = 0.55\), \(R = 1\): \(K = 0.55 - 0.45 = 10\%\) del capital. Con \(W = 0.50\),
\(K = 0\).

</details>

### Clases desbalanceadas: class_weight  [C6 §3]
En el dataset del meta-modelo, ~90% de las etiquetas son 0 (no era buen momento). Un modelo que **siempre predice 0 tiene 90% de accuracy** y es inútil. Solución: `class_weight='balanced'`, que pesa más los errores en la clase rara.

→ meta_labeling · AUC · pesos_por_unicidad

<details><summary>más</summary>

Es el mismo problema que en detección de fraude: los eventos raros son justo los que
importan. Con 10% de unos, `balanced` penaliza un error en un 1 nueve veces más que un error
en un 0. Es una línea de código, no toca los datos y no introduce leakage (a diferencia de
sobre-muestrear, que duplica ventanas solapadas). Y por esto mismo la métrica no es accuracy
sino AUC (§8).
Ej.: `RandomForestClassifier(class_weight='balanced')`.

</details>

## §4 Los pesos: cuánta información independiente tenés

### Etiquetas solapadas: contar dos veces  [C6 §4]
Con barrera vertical de 10 días, la etiqueta del lunes usa los días 1–10 y la del martes los días 2–11: comparten 9 de 10. El dataset dice n = 2.500; de información independiente tenés quizás **250**.

→ triple_barrera · pesos_por_unicidad · purged_kfold · T≥10N (C5)

<details><summary>más</summary>

Todo lo que asume independencia se sobre-confía: errores estándar, p-values, la CV, y el
bootstrap del random forest (¡muestrea con reemplazo casi las mismas ventanas!). Es el mismo
pecado que el T≥10N de C5 (menos datos efectivos que los que creés), en versión temporal.
Ej.: un RF con 500 árboles sobre etiquetas solapadas termina con árboles casi idénticos entre
sí, y el promedio no reduce la varianza como debería.

</details>

### Pesos por unicidad  [C6 §4]
\(c_t\) = cuántas etiquetas tienen su ventana sobre el día \(t\); la unicidad de la muestra \(i\) es \(\bar u_i = \frac{1}{|T_i|}\sum _{t \in T_i} \frac{1}{c_t}\). No comparte días → \(\bar u_i = 1\); solapa todo → \(\bar u_i \approx 1/\text{solapamiento}\).

→ etiquetas_solapadas · class_weight · random_forest

<details><summary>más</summary>

Se enchufa directo: `sample_weight=u` en sklearn. Una línea, "medio dataset de honestidad":
las muestras que repiten información pesan menos, y el modelo ve aproximadamente la cantidad
de información que realmente hay.
Ej.: con ventanas de 10 días que empiezan todos los días, cada día está cubierto por ~10
etiquetas, así que \(\bar u_i \approx 0.1\) para casi todas.

</details>

### Diferenciación fraccionaria  [C6 §4]
Precio (\(d = 0\)): tiene memoria pero no es estacionario. Retorno (\(d = 1\)): estacionario pero amnésico. Diferenciar \(d \in (0,1)\): \(\tilde X_t \approx X_t - 0.40X_{t-1} - 0.12X_{t-2} - 0.06X_{t-3} - \dots\) (con \(d = 0.4\)). Buscar el **mínimo d que pasa el ADF**.

→ estacionariedad (C4) · tests_ADF_KPSS (C4) · ARIMA (C4) · retorno_simple_vs_log (C4)

<details><summary>más</summary>

El precio sabe cosas útiles (¿estamos en máximo histórico? ¿cerca de un soporte?), pero no
sirve para entrenar porque no es estacionario. Diferenciarlo entero (\(d = 1\), la "d" de
ARIMA en C4) borra el nivel. Con \(d\) fraccionario los pesos son
\(w_k = -w_{k-1}\frac{d - k + 1}{k}\), que decaen suavemente: la serie recuerda el pasado con
cada vez menos peso, y eso alcanza para volverla estacionaria. En índices de acciones
típicamente \(d^* \approx 0.35\)–\(0.5\).
Ej.: con \(d = 0.4\) la serie pasa el ADF y tiene correlación >0.99 con el precio original:
entrenable y con memoria del nivel.

</details>

## §5 La validación honesta: purged K-fold y embargo

### Por qué K-fold estándar miente: dos vías de leakage  [C6 §5]
**Vía 1, barajar el tiempo** (la obvia): entrenar con marzo para "predecir" febrero. **Vía 2, solapamiento de etiquetas** (la invisible): una muestra de entrenamiento del 28/1 con ventana hasta el 5/2 ya vio el test que empieza el 1/2.

→ el_puente_al_ML (C4) · etiquetas_solapadas · purga · embargo · walk_forward (C5)

<details><summary>más</summary>

K-fold parte en K bloques, entrena en K−1, testea en 1 y rota: con datos IID es perfecto. Con
series financieras, la segunda vía es la traicionera: la fecha de la muestra dice "enero",
pero su contenido es febrero. El leakage no está en el índice, está en la **ventana**. El
resultado: scores inflados con cara de legítimos; el modelo "funciona" hasta producción.
Ej.: es la advertencia que C4 §7 dejó planteada y C5 §6 volvió a nombrar.

</details>

### Purga  [C6 §5]
Eliminar del **entrenamiento** toda muestra cuya ventana de etiqueta se solape con el test. Si el test es \([t_1, t_2]\) y la etiqueta de \(i\) vive en \([t_i, t_i + h]\), se descarta \(i\) si los intervalos se intersecan.

→ k_fold_miente · embargo · etiquetas_solapadas

<details><summary>más</summary>

Ataca la vía 2 directamente. Regla práctica: ventana de etiqueta de h días → purga de h días
a cada lado del test.
Ej.: test = febrero, h = 10 días → se tiran del train las muestras de los últimos 10 días de
enero.

</details>

### Embargo  [C6 §5]
Además de purgar, eliminar un **colchón extra después del test** (ej. 1% del dataset, o ≥ h/2). Motivo: la correlación serial — la primera muestra post-test "recuerda" al test aunque su ventana no lo toque.

→ purga · GARCH (C4) · clustering_de_vol (C4)

<details><summary>más</summary>

El clustering de vol de C4 transporta información: si el test terminó en pánico, los días
siguientes también son de vol alta, y entrenar con ellos filtra el régimen del test.
Ej.: test en marzo 2020; sin embargo, entrenar con abril 2020 le enseña al modelo el
régimen de marzo.

</details>

### Purged K-fold vs. walk-forward con purga  [C6 §5]
**Purged K-fold** usa todos los datos (hasta futuros en train): eficiente, para **elegir modelo e hiperparámetros**. **Walk-forward + purga** entrena en el pasado y testea en el futuro: replica producción, para el **veredicto final**.

→ walk_forward (C5) · purga · embargo · deflated_sharpe

<details><summary>más</summary>

Walk-forward es el más honesto, pero los últimos datos casi no entrenan y las estimaciones
son más variables. Purged K-fold no simula producción exactamente pero aprovecha mejor la
muestra. En la práctica: purged K-fold para seleccionar, walk-forward + purga para decidir
antes de producción.
Ej.: el backtest de C5 §6 era un walk-forward sin etiquetas solapadas (pesos a partir de
252 días atrás); con triple barrera habría que agregarle la purga.

</details>

## §6 El modelo y sus features: qué aprendió de verdad

### Regresión logística  [C6 §6]
\(P(y = 1 \mid x) = \sigma(w^\top x + b) = \dfrac{1}{1 + e^{-(w^\top x + b)}}\). Interpretable, probabilística, rara vez sobreajusta. Con penalización \(\lambda\lVert w\rVert _1\) (la de Lasso) lleva pesos irrelevantes a cero. **El baseline obligado.**

→ ridge_lasso (C4) · bet_sizing_kelly · AUC · seis_modelos

<details><summary>más</summary>

El ajuste busca \(w\) y \(b\) que hagan las probabilidades predichas lo más parecidas posible a
lo que pasó (máxima verosimilitud). La L1 no es "correr un Lasso aparte": es sumar esa
penalización a lo que se minimiza (las slides dicen "clase 5"; en este mapa Lasso está en
C4 §7). \(w_j > 0\) → la feature \(j\) empuja la probabilidad hacia arriba, y la salida es
directamente un input de bet sizing. Limitaciones: frontera lineal (un hiperplano) y no
captura interacciones salvo que las agregues a mano (ej. momentum × vol). Si la señal es
débil y lineal, ningún modelo complejo le gana.
Ej.: en las slides, con vol baja el modelo da P = 0.87 de señal válida; en pánico (vol 80%)
cae a 0.11; con RSI muy sobrevendido (20), 0.03.

</details>

### Árbol de decisión  [C6 §6]
Una cadena de preguntas binarias ("¿momentum > 0.3?"); cada hoja predice el promedio o la clase mayoritaria. Cada corte es **perpendicular a un eje**: el espacio queda partido en **rectángulos**. Hiperparámetros: `max_depth` y `min_samples_leaf`.

→ random_forest · gradient_boosting · redes_neuronales

<details><summary>más</summary>

`max_depth` chico = underfit; grande = memoriza el ruido. Subir `min_samples_leaf` es la
forma más simple de frenar el sobreajuste. Captura no-linealidades e interacciones sin
asumir forma funcional, pero si la señal es una interacción diagonal (no alineada a los
ejes), el árbol la aproxima con una escalera de rectángulos.
Ej.: profundidad 3 sobre (momentum, vol): "¿vol ≤ 0.2? → ¿momentum > 0.1? → operar".

</details>

### Random Forest (bagging)  [C6 §6]
**B árboles en paralelo**, cada uno sobre un remuestreo bootstrap (y un subconjunto de features), promediados al final: el promedio **reduce varianza**. Robusto, difícil de arruinar, poco tuneo; rara vez el mejor, rara vez el peor.

→ arbol_de_decision · gradient_boosting · pesos_por_unicidad · MDI_MDA · meta_labeling

<details><summary>más</summary>

Hiperparámetros: `n_estimators` (más es casi gratis), `max_features` (≈ \(\sqrt p\)),
`max_depth`. Los árboles nunca se miran entre sí durante el entrenamiento. Es el modelo que
López de Prado recomienda por defecto: maneja features mixtas y da importancias. Su punto
débil en finanzas es el bootstrap sobre etiquetas solapadas (§4): hay que pasarle
`sample_weight` por unicidad.
Ej.: el meta-modelo del §3 es típicamente un RF.

</details>

### Gradient boosting: XGBoost / LightGBM  [C6 §6]
Árboles **en secuencia**: cada uno aprende el **residuo** de los anteriores, y la predicción es la suma. State of the art en datos tabulares; puede ganarle al RF si la señal es real y no lineal — y es **el que más explota el leakage** si la CV no es honesta.

→ random_forest · purged_kfold · seis_modelos

<details><summary>más</summary>

Hiperparámetros: `learning_rate` (η), `n_estimators` + early stopping, `max_depth` chico.
LightGBM es 5–20× más rápido (crece hoja por hoja). RF reduce varianza promediando árboles
profundos; boosting reduce sesgo sumando árboles chicos. Justamente por ser tan bueno
encontrando estructura, encuentra también la estructura que filtra el leakage.
Ej.: si con K-fold estándar XGBoost gana por mucho y con purged K-fold empata con la
logística, lo que había aprendido era el leakage.

</details>

### Redes neuronales y funciones de activación  [C6 §6]
Capas que combinan linealmente y aplican una no-linealidad: \(h = \varphi(W h_{\text{ant}} + b)\). Sin \(\varphi\), apilar capas sigue siendo lineal. **Sigmoide** (salida como probabilidad), **tanh**, **ReLU** \(\max(0, z)\) (default en capas ocultas: no satura del lado positivo).

→ arbol_de_decision · LSTM · ridge_lasso (C4)

<details><summary>más</summary>

Apiladas, las capas "doblan" el espacio hasta que una frontera curva se vuelve separable: por
eso trazan curvas suaves en vez de rectángulos. Hiperparámetros: profundidad y ancho (más
capacidad para señal *y* ruido), `learning_rate` (el más sensible), dropout y weight decay
(las versiones-red de la λ de Ridge/Lasso). Cuando sigmoide/tanh saturan, su derivada es ≈0 y
el gradiente se diluye capa a capa. Máxima flexibilidad, la más fácil de sobreajustar con
pocos datos y la menos interpretable.
Ej.: en `make_moons` (dos medialunas entrelazadas) el árbol talla escalones; la red sigue la
curva.

</details>

### LSTM  [C6 §6]
Red **recurrente**: procesa la secuencia paso a paso y su estado (memoria) vuelve a entrar como insumo del paso siguiente. Las **compuertas aprendibles** (qué olvidar, qué agregar, qué mostrar) resuelven que una RNN simple olvide rápido.

→ redes_neuronales · CNN_1D · LOB (U2)

<details><summary>más</summary>

Promesa del deep learning: darle la serie *cruda* y que aprenda sus propios patrones, en vez
de features que diseñamos nosotros (vol, momentum, RSI). Hiperparámetro extra: tamaño del
estado de memoria. Contra: necesita **muchos** más datos que un RF. Por eso sirve sobre todo
en alta frecuencia y LOB, donde hay millones de observaciones y patrones locales fuertes
(Zohren).
Ej.: con 20 años de datos diarios (~5000 puntos, ~500 independientes) una LSTM memoriza;
con un día de ticks de un LOB tiene material de sobra.

</details>

### CNN-1D: ARIMA con pesos aprendidos  [C6 §6]
Desliza una ventana de pesos sobre la serie (un promedio ponderado de los últimos K valores), como un promedio móvil pero con pesos **aprendidos**, muchos kernels en paralelo y capas apiladas. **ARIMA es una CNN con un solo kernel fijo y sin capas.**

→ ARIMA (C4) · LSTM · redes_neuronales

<details><summary>más</summary>

Cada kernel detecta un patrón local distinto de precio/volumen, y las capas apiladas
combinan patrones en otros más abstractos. La diferencia con ARIMA no es la operación (una
suma ponderada del pasado) sino quién elige los pesos: un modelo teórico con pocos
parámetros o los datos.
Ej.: es el segundo hilo del curso ⚡ otra vez — el modelador elige la estructura (ARIMA,
Nelson-Siegel) o los datos la descubren (CNN, PCA).

</details>

### Importancia de features: MDI vs. MDA  [C6 §6]
**MDI** (Mean Decrease Impurity): cuánto reduce cada feature la impureza de los cortes del árbol, **in-sample**. **MDA** (Mean Decrease Accuracy): cuánto empeora el score **out-of-sample** si permutás esa feature. Regla de la clase: **MDA sobre MDI**.

→ random_forest · purged_kfold · efecto_sustitucion · zoologico_de_factores (C4)

<details><summary>más</summary>

*(Las slides de MDI/MDA no vinieron en el PDF — el conteo salta de la 32 a la 35 y de la 41
a la 43; esto es de López de Prado, completar con lo que se vio en clase.)* MDI sale gratis
del RF, pero se calcula sobre el train: premia features que sirvieron para memorizar, y
sesga hacia features con muchos valores posibles. MDA se calcula sobre el test (con purged
K-fold): mide lo que la feature aporta a predecir, no a memorizar. **Trampa de la
sustitución:** si dos features son casi iguales (momentum 20 y 21 días), permutar una no
duele porque la otra la reemplaza, y las dos parecen inútiles. Arreglo: agrupar features
correlacionadas (clusters, como en HRP) y medir la importancia del grupo.
Ej.: una feature con importancia alta en MDI y ~0 en MDA es una que el modelo usó para
memorizar ruido.

</details>

### Seis modelos, una sola conclusión  [C6 §6]
Cambia la interpretabilidad (logística > árboles > redes), el costo (logística barata → LSTM/CNN caras) y la capacidad no lineal (al revés). **No cambia:** con señal/ruido tan bajo, más capacidad solo memoriza mejor el ruido. El "mejor modelo" lo decide la **validación**, no la arquitectura.

→ pipeline_lopez_de_prado · purged_kfold · curva_sesgo_varianza

<details><summary>más</summary>

Con CV ingenua gana el más complejo; con purged CV casi ninguno gana de verdad. Por eso
López de Prado lo dice sin vueltas: sofisticar el modelo es lo **último**; primero datos,
etiquetas y validación honesta.
Ej.: "un random forest con buenos datos y buenas etiquetas le gana a XGBoost con datos
sucios" (lo que se llevan, punto 5).

</details>

## §7 El backtest que miente: Deflated Sharpe y PBO

### El Sharpe selectivo: el máximo del ruido  [C6 §7]
Tirás 200 monedas 5 años y elegís la que más caras dio: parece que "funciona", pero su Sharpe esperado es 0. **SR₀** = el mejor Sharpe que el azar fabrica con N intentos. Con N = 200 y 5 años, las slides dan SR₀ ≈ 0.7–1.0.

→ zoologico_de_factores (C4) · deflated_sharpe · PBO · que_cuenta_como_intento

<details><summary>más</summary>

Es exactamente lo que hacés al probar 200 variantes y quedarte con la mejor. SR₀ crece con
el número de intentos (como el máximo de N normales, ~\(\sqrt{2\ln N}\)) y baja con la
longitud de la muestra (~\(1/\sqrt{T}\)). Si tu modelo reporta Sharpe 0.8 después de 200
variantes, no hay evidencia de señal: es lo que el ruido ya daba.
Ej.: es el zoológico de factores de C4 en tu propia computadora ⚡.

</details>

### Qué cuenta como un intento  [C6 §7]
**Todo lo que hiciste mirando el resultado.** Tres learning rates → +3. Mover el umbral de la triple barrera → +1. Agregar una feature porque mejoró → +1. Probar SPY y QQQ y quedarte con el mejor → +2. Cambiar el período de train "porque el mercado cambió" → +1.

→ sharpe_selectivo · deflated_sharpe · checklist_de_higiene

<details><summary>más</summary>

El N del DSR es el N **verdadero**, no el confesable. Por eso hay que registrar todos los
experimentos, incluidos los que fallaron.
Ej.: un "solo probé un modelo" suele esconder 20–50 decisiones tomadas mirando el backtest.

</details>

### Deflated Sharpe Ratio (DSR)  [C6 §7]
¿Tu Sharpe es significativamente mayor que SR₀? \[DSR = \Phi\!\left(\frac{(\widehat{SR} - SR_0)\sqrt{T - 1}}{\sqrt{1 - \gamma _3\widehat{SR} + \frac{\gamma _4 - 1}{4}\widehat{SR}^2}}\right)\] Es la **probabilidad de que la estrategia sea genuina**. DSR > 0.95: defendible; ≈ 0.5: una moneda; < 0.5: peor que el ruido.

→ sharpe_selectivo · alpha_y_sharpe (C4) · colas_gordas (C4) · PBO

<details><summary>más</summary>

Numerador: cuánto te alejás del mejor ruido, escalado por la longitud de la muestra.
Denominador: el error de estimación del Sharpe, corregido por no-normalidad — \(\gamma _3\) es
la asimetría y \(\gamma _4\) la curtosis (3 en la normal, así que \((\gamma _4 - 1)/4 = 1/2\) da
el caso gaussiano). Skew negativa y colas gordas agrandan el denominador: inflan el Sharpe
calculado, así que se penalizan. \(\Phi\) es la normal acumulada. Es el mismo test de
hipótesis de siempre, pero contra SR₀ en vez de contra 0.
Ej.: si el DSR deflacta tu Sharpe a 0.3, no es mala suerte, es información: el experimento
gastó el dataset sin encontrar señal.

</details>

### PBO: Probability of Backtest Overfitting  [C6 §7]
Elegiste la mejor estrategia mirando una mitad de los datos. Si hubieras mirado la **otra** mitad, ¿seguiría siendo la mejor? PBO repite eso sobre todas las particiones y cuenta qué fracción de veces la campeona in-sample cae entre las **peores** out-of-sample.

→ deflated_sharpe · colapso_out_of_sample (C5) · purged_kfold

<details><summary>más</summary>

PBO ≈ 50%: elegir la estrategia ganadora es lo mismo que tirar una moneda — el proceso de
selección encontró suerte, no señal. DSR mira a la estrategia elegida; PBO mira al
*proceso* de elegir.
Ej.: es el colapso out-of-sample de Markowitz (C5) medido como probabilidad: el portafolio
"óptimo" in-sample termina entre los peores afuera.

</details>

### Checklist de higiene  [C6 §7]
✓ Registrar **todos** los experimentos · ✓ features solo con datos hasta \(t\) (tampoco normalizar con medias del futuro) · ✓ purged CV con embargo ≥ ventana de etiqueta · ✓ **costos de transacción realistas** · ✓ el test final se toca **una vez**.

→ que_cuenta_como_intento · purga · embargo · walk_forward (C5)

<details><summary>más</summary>

El backtest no es un experimento repetible: hay una historia, y cada vez que la mirás gastás
un poco. El dataset de test es un recurso no renovable.
Ej.: estandarizar las features con `StandardScaler().fit(X)` sobre todo el dataset antes
de partir en train/test ya es leakage: la media incluye el futuro.

</details>

### La curva sesgo-varianza en finanzas  [C6 §7]
Error de train siempre baja con la complejidad (memoriza); el de test baja y **vuelve a subir**. En finanzas el mínimo está **muy a la izquierda**: hay tan poca señal que ganan los modelos simples. DSR y PBO son la versión cuantificada de "a la derecha de este mínimo te estás mintiendo".

→ ridge_lasso (C4) · shrinkage_ledoit_wolf (C5) · seis_modelos · curva_de_aprendizaje

<details><summary>más</summary>

El eje X puede ser complejidad del modelo (más árboles, más profundidad, más parámetros) o,
para el DSR, cantidad de intentos. Es el tercer hilo del curso ⚡: un estimador un poco
sesgado pero estable predice mejor que uno flexible y ruidoso.
Ej.: por eso la logística es el baseline obligado y el RF con `min_samples_leaf` alto el
default.

</details>

## §8 Herramientas de evaluación y cierre

### AUC-ROC  [C6 §8]
La curva ROC grafica, para cada umbral, **TPR** (= recall, fracción de los 1 reales detectados) contra **FPR** (fracción de los 0 reales marcados como 1). AUC = 1: perfecto; 0.5: moneda. **No depende del umbral ni del balance de clases** — en finanzas, AUC 0.6 ya es interesante.

→ class_weight · precision_vs_recall · senal_ruido

<details><summary>más</summary>

Subir el umbral hace al modelo más conservador: menos 1 detectados, pero menos falsas
alarmas; la curva traza ese trade-off. El AUC también se lee como la probabilidad de que el
modelo le asigne más score a un 1 que a un 0 elegidos al azar. Por eso es preferible a
accuracy cuando los eventos son raros (señales, fraude).
Ej.: en las slides, logística y árbol profundo llegan los dos a AUC 0.75 con curvas de
forma distinta: la logística mejora el recall de a poco, el árbol da un salto inicial.

</details>

### Curva de aprendizaje: ¿más datos o mejor modelo?  [C6 §8]
Score de train y test contra el **tamaño del training set**, con el modelo fijo. Convergen arriba: bien. Convergen abajo: **underfitting** (más complejidad). Gap grande que no cierra: **overfitting estructural** (regularizar, simplificar). En finanzas casi siempre es el tercer caso.

→ curva_sesgo_varianza · no_estacionariedad · seis_modelos

<details><summary>más</summary>

La curva sesgo-varianza varía la complejidad con los datos fijos; la de aprendizaje varía los
datos con el modelo fijo. En finanzas el gap no cierra porque, con señal/ruido tan bajo, el
modelo encuentra patrones espurios en cualquier muestra finita, y agregar más datos del
pasado no ayuda: agregás otro régimen.
Ej.: pasar de 5 a 20 años de historia no cierra el gap si esos 15 años extra son de un
mercado distinto.

</details>

### El pipeline completo y lo que se llevan  [C6 §8]
Dollar bars → triple barrera → unicidad + diferenciación fraccionaria → modelo → purged CV → MDI/MDA → DSR/PBO. **El rigor está en las puntas**, no en el modelo del medio. Siempre la métrica que no miente: **AUC sobre accuracy, MDA sobre MDI, DSR sobre Sharpe**.

→ pipeline_lopez_de_prado · trabajo_integrador

<details><summary>más</summary>

Las siete ideas de cierre: (1) el pipeline estándar de DS aplicado a finanzas miente, y cada
supuesto que viola es plata que perdés; (2) muestrear por actividad; (3) etiquetar como se
opera, con barreras adaptadas a la vol; (4) la validación es lo más importante; (5) el modelo
es lo de menos; (6) la métrica que no miente; (7) un DSR bajo es información, no mala suerte.
Ej.: para el trabajo integrador, este pipeline es el esqueleto de cualquier parte "ML macro"
de la Unidad 1.

</details>

## ❓ Dudas de C6

*(Por completar después de repasar la clase.)*

- ❓ DUDA: las slides de MDI/MDA y la "trampa de la sustitución" (33, 34, 42) no están en el
  PDF. Confirmar con lo visto en clase.


---

# C7–C8 — Unidad 3 (HFT / baja latencia)

## Idea general

⬜ Por completar al cursar. Hipótesis: a escala de milisegundos el "precio" deja de ser un
número y pasa a ser un **libro de órdenes** (LOB); el market maker de C1 vuelve como
protagonista (Avellaneda-Stoikov), la ejecución de una orden grande es un problema de
optimización (Almgren-Chriss), y la tecnología (FIX, C++ lock-free) y la regulación
(spoofing, MiFID II) son parte del modelo, no un detalle.

# C9 — Wrap + presentaciones (póster)

## Idea general

⬜ Por completar al cursar. El cierre: las tres unidades son la misma pregunta (cómo se le
pone precio al riesgo) a tres escalas de tiempo distintas — años, horas, milisegundos — y el
trabajo integrador tiene que mostrar ese hilo sobre datos reales.
