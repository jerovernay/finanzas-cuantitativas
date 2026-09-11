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

# C4 — Factores

## Idea general

⬜ Por completar al cursar. Hipótesis desde el programa: los retornos de miles de acciones
se explican con **pocas fuentes de riesgo comunes** (mercado, tamaño, value, momentum…) y
no acción por acción. El beta de C1 es el primer factor; acá se generaliza. Conecta con C5
(un portfolio se arma sobre exposiciones a factores, no sobre nombres) y con C6 (un factor
es, en el fondo, un feature con prima).

# C5 — Portfolios

## Idea general

⬜ Por completar al cursar. Hipótesis: cómo combinar activos para que el riesgo total sea
menor que la suma de los riesgos (diversificación), y cómo medir ese riesgo (VaR, Expected
Shortfall, Basel III). Conecta con C1 (duration/DV01 como riesgo de tasa del portfolio),
C3 (la superficie de vol como input de riesgo) y C4 (exposición a factores).

# C6 — Machine Learning

## Idea general

⬜ Por completar al cursar. Hipótesis: qué cambia cuando en vez de un modelo con 5
parámetros interpretables (Heston) usás uno con miles (LSTM, XGBoost, LGBM), y por qué en
finanzas el overfitting es peor que en otros dominios (poca señal, mucho ruido, series no
estacionarias — Lopez de Prado). Conecta con C4 (factores como features) y con la Unidad 2
(pair trading, predicción intradía).

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
