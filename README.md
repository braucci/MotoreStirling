# Simulatore Motore Stirling Alpha

Simulazione web interattiva e fisicamente fondata di un **motore Stirling di tipo Alpha**, realizzata in un singolo file HTML con [Three.js](https://threejs.org/). Il progetto unisce un'animazione 3D del meccanismo a manovelle ai diagrammi termodinamici del ciclo, pensato come strumento didattico per lo studio dei motori a ciclo chiuso e della termodinamica applicata.

> Il manovellismo non è un'animazione decorativa: la posizione dei pistoni è **derivata** dall'angolo di manovella risolvendo il vincolo geometrico di biella rigida, e il regime di rotazione **emerge** dalla dinamica del volano. Allo stesso modo, i diagrammi p–V e T–s rappresentano il ciclo Stirling ideale con i suoi quattro processi reversibili e la rigenerazione.

<!-- Suggerimento: inserisci qui uno screenshot, es. ![Screenshot](docs/screenshot.png) -->

---

## Caratteristiche

- Modello 3D di un Alpha Stirling in configurazione a **V di 90°**: volano, albero a gomiti su cavalletti, cilindro caldo (con fascia riscaldante) e cilindro freddo (con alette di dissipazione), uniti dal condotto di vetro.
- **Cinematica esatta** del manovellismo di spinta (slider-crank) con biella rigida e sfasamento di 90° tra i due pistoni.
- **Dinamica del volano**: avviamento, salita di giri e decelerazione per inerzia emergono dall'equilibrio tra coppia motrice e attrito, non da un regime imposto.
- **Gas come popolazione di particelle** la cui distribuzione tra cilindro caldo, freddo e condotto segue i volumi istantanei.
- Diagrammi **p–V** e **T–s** del ciclo Stirling ideale, con i quattro stati numerati, le isoterme, le isocore di rigenerazione e il marcatore dello stato corrente sincronizzato con l'animazione.
- Rendering con environment map, tone mapping ACES e ombre.
- Controlli interattivi per calore del bruciatore, sfasamento, carico, quantità di gas e viste della camera.

---

## La fisica implementata

### Cinematica del manovellismo

Con l'asse del cilindro passante per il centro manovella $O$ e $\vec{w}$ il vettore al perno (con $|\vec{w}| = R$, raggio di manovella), la posizione $s$ dello spinotto lungo l'asse del cilindro si ottiene dal vincolo di biella rigida di lunghezza $L$:

$$s = \hat{u}\cdot\vec{w} + \sqrt{L^2 - R^2 + (\hat{u}\cdot\vec{w})^2}$$

La corsa risulta esattamente $2R$ e la lunghezza della biella è costante per costruzione. I due pistoni sono collegati a perni sfasati di **90°**, la caratteristica costitutiva del ciclo Alpha.

### Dinamica del volano

La velocità angolare $\omega$ è integrata nel tempo a partire dal bilancio di coppie:

$$I\,\dot{\omega} = \tau_{\text{mot}}(\text{calore},\,\eta) - \left[\tau_{\text{attrito secco}} + (c_v + c_L\,\text{carico})\,\omega\right]$$

Sotto una soglia minima di calore il motore non si avvia, perché il salto di temperatura non basta a vincere l'attrito: comportamento fisicamente corretto.

### Modello del gas

Il gas è rappresentato da particelle ripartite tra lo spazio caldo, lo spazio freddo e il condotto/rigeneratore in proporzione ai volumi istantanei $V_{\text{caldo}}(\theta)$ e $V_{\text{freddo}}(\theta)$, calcolati dalla stessa cinematica del meccanismo. Il gas si addensa visibilmente nel cilindro caldo durante l'espansione e migra nel freddo durante la compressione.

### Ciclo termodinamico

I diagrammi rappresentano il **ciclo Stirling ideale** (riferimento: Çengel & Boles, *Thermodynamics: An Engineering Approach*), composto da quattro processi reversibili:

| Processo | Trasformazione | Scambio termico |
|----------|----------------|-----------------|
| 1 → 2 | Espansione isoterma a $T_H$ | Calore assorbito dalla sorgente |
| 2 → 3 | Raffreddamento a volume costante | Calore ceduto al rigeneratore |
| 3 → 4 | Compressione isoterma a $T_L$ | Calore ceduto al pozzo |
| 4 → 1 | Riscaldamento a volume costante | Calore restituito dal rigeneratore |

Le isocore di rigenerazione (2→3 e 4→1) sono rappresentate come **curve** ($s = s_0 + c_v \ln(T/T_0)$), non come segmenti verticali: un processo a volume costante non è isentropico. Con rigenerazione ideale il lavoro netto vale $\oint p\,dV = \oint T\,ds = (T_H - T_L)\ln(V_2/V_1)$ e il rendimento è

$$\eta_{\text{Stirling}} = 1 - \frac{T_L}{T_H}$$

pari a quello di Carnot tra le stesse temperature.

---

## Come usarlo

Nessuna installazione e nessun server: è un singolo file HTML.

1. Scarica `stirling_alpha_simulator.html`.
2. Aprilo in un browser moderno (Chrome, Firefox, Safari, Edge).

Le librerie Three.js sono caricate da CDN tramite *import map*, quindi è necessaria una connessione a internet al primo avvio.

### Controlli

- **Burner heat** — calore del bruciatore: determina $T_H$ e quindi avviamento e regime di giri.
- **Phase** — sfasamento tra i pistoni (60°–120°); a 90° il lavoro per ciclo è circa massimo.
- **Load** — carico resistente applicato all'albero.
- **Working gas / Glass refraction** — visibilità del gas e trasparenza del vetro.
- **Thermo diagrams** — pannello con i diagrammi p–V e T–s.
- **Camera** — alterna alcune inquadrature; trascinando si orbita liberamente.
- **Pausa / Reset** — ferma o azzera l'animazione.

### Come leggere i diagrammi

- **p–V** (curva dorata): l'area racchiusa è il lavoro indicato per ciclo. Aumentando il calore l'area cresce; annullando il bruciatore il ciclo degenera (lavoro nullo).
- **T–s** (curva azzurra): le due isoterme orizzontali sono gli scambi termici con sorgente e pozzo, le due curve laterali sono le rigenerazioni a volume costante. L'area racchiusa è il lavoro netto; il rapporto tra area e calore assorbito dà il rendimento.

Il pallino bianco indica lo stato istantaneo del motore e avanza sincronizzato su entrambi i diagrammi.

---

## Tecnologie

- HTML5, CSS, JavaScript (ES modules)
- [Three.js](https://threejs.org/) per la grafica 3D (WebGL)
- Canvas 2D per i diagrammi termodinamici

---

## Riferimenti

- Çengel, Y. A., & Boles, M. A. (2015). *Thermodynamics: An Engineering Approach* (8th ed.).
- Cano González, J., Gómez Jiménez, S., Bedoya Atehortúa, S., & Serna Tavera, T. *Alpha Stirling Engine – SUNNYTECH SC005*. Universidad de Antioquia, Facultad de Ingeniería.

---

## Licenza

Distribuito con licenza MIT. Vedi il file `LICENSE` (modifica liberamente se preferisci un'altra licenza).

---

## Autore

**Biagio Raucci** — [github.com/braucci](https://github.com/braucci)
