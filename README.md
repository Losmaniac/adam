# 🚜 Velká garáž

Interaktivní hra se stroji pro nejmenší (cca 2 roky). Klepneš na stroj a hraješ si:
jezdí, couvá, houká, svítí a každý umí ještě svůj vlastní kousek.

Čisté HTML + CSS + JavaScript. **Žádné knihovny, žádný build, žádné externí
soubory** — stačí otevřít `index.html` v prohlížeči. Funguje i bez internetu.

---

## Stroje

| Stroj | Prostředí | Co umí navíc |
|---|---|---|
| 🚜 **Traktor** | pole | spustí **pluh** a oře — od radlic létá hlína |
| 🚧 **Bagr** | stavba | **kopání** — výložník, násada i lžíce se hýbou jako doopravdy |
| 🏗️ **Buldozer** | stavba | **hrne radlicí** — zvedne ji, spustí a tlačí hlínu |
| 🚛 **Americký truck** | pouštní silnice | **blikačky** — obrysová světla běhají a zaduje vzduchová houkačka |
| 🌾 **Kombajn** | zlaté pole | **sklízí** — točí se motovidlo, sype se zrní a vzadu létá sláma |
| 🛻 **Domíchávač** | stavba | **lije beton** — buben se točí, skluz se sklopí |
| ⛸️ **Rolba** | zimní stadion | **hladí led** — stříká voda, odlétá sníh a led se leskne |
| 🛞 **Čtyřkolka** | terén | prohání se **blátem** — stříká z obou zadních kol |
| 🏍️ **Motorka** | silnice | umí **kolečko** — přední kolo se zvedne, výfuk bafne |
| 🚐 **Multivan** | město | otevře **posuvné dveře** a odhalí interiér |
| 🚙 **Dacia Bigster** | terén | vyrazí do **terénu** — stříká bláto od všech kol |
| 🚗 **Škoda Superb** | město | otevře **kufr** a ukáže zavazadlo |
| 🍃 **Nissan Leaf** | město | **nabíjí se** — klapka se otevře, jiskří elektřina |
| 🛺 **Velorex** | pole | sklopí plátěnou **stříšku** — tři kola, dvoutaktní put-put |
| 🚲 **Kolo** | park | **trik** s předním kolem, zvonek místo houkačky, žádný motor |

Každý stroj má vlastní zvuk motoru i houkačky, blatníky, majáček nebo směrovky,
řidiče v kabině a kola, která se točí přesně podle ujeté vzdálenosti. Kolo motor
nemá vůbec — místo hukotu cvaká volnoběžka a troubení nahrazuje zvonek.

## Ovládání

Velká tlačítka dole:

- **Couvat** / **Jeď** / **Stůj** — tlačítko se zapíná a vypíná dalším klepnutím
- **Houkat** — každý stroj troubí jinak
- **Světla** — reflektory, kužely, zadní světla i oranžový majáček
- **[činnost]** — zvláštní kousek daného stroje

Navíc:

- **klepnutí přímo na stroj** = zatroubí, poskočí a vyletí konfety
- šipky nahoře přepínají mezi stroji, ikona vlevo vede zpátky do garáže
- při couvání se rozsvítí bílá světla a pípá couvací pípák

### Klávesnice

| Klávesa | Akce |
|---|---|
| `→` / `D` | jeď |
| `←` / `A` | couvej |
| `↓` / `S` | stůj |
| `mezerník` / `H` | houkat |
| `L` | světla |
| `↑` / `Enter` / `W` | zvláštní činnost |
| `N` / `P` | další / předchozí stroj |
| `Esc` | zpět do garáže |

## Spuštění

```bash
# stačí otevřít soubor
xdg-open index.html          # Linux
open index.html              # macOS

# nebo přes jednoduchý server (kvůli manifestu / přidání na plochu)
npx http-server . -p 8080
```

Na tabletu se hodí tlačítko **celé obrazovky** vpravo nahoře; díky
`manifest.webmanifest` jde stránka přidat na plochu jako aplikace.

## Jak je to poskládané

```
index.html               kostra obrazovek
css/styles.css           vzhled, velká tlačítka, rozložení
js/art.js                SVG „dílna" – kola, pásy, světla, sklo, řidič, majáček
js/vehicles.js           patnáct strojů: kresba, zvuk, pohyblivé díly, částice
js/scenes.js             kulisy (pole, obilí, stavba, silnice, zimní stadion,
                         terén, město, park)
js/particles.js          kouř, prach, hlína, bahno, zrní, sníh, voda, beton,
                         elektrické jiskry, konfety, hvězdičky
js/audio.js              Web Audio – motor, houkačky, hydraulika, pípák, řeč,
                         volnoběžka kola, nabíjecí tón elektromobilu
js/app.js                stav hry, ovládání a hlavní smyčka
assets/icon.svg          ikona aplikace
```

### Pár technických poznámek

- **Grafika** je celá ve vektorech generovaných v JavaScriptu. Všechny stroje
  sdílejí soustavu souřadnic `viewBox="0 0 560 300"`, zem je na `y = 262`
  a stroje jsou otočené doprava.
- **Kola se točí podle ujeté dráhy** (`úhel = dráha / poloměr`), takže se nikdy
  „neklouzají" — a pozadí se posouvá stejnou rychlostí, takže jízda sedí.
- **Pohyblivé díly** (rameno bagru, radlice, pluh, skluz) jsou vnořené skupiny
  s `data-part` a `data-pivot`; činnost je popsaná klíčovými snímky a interpoluje
  se s měkkým náběhem.
- **Kulisy** jsou dlaždice v SVG, které se vodorovně opakují a posouvají různou
  rychlostí — vzniká tím hloubka. Scéna může mít `standH` menší než `groundH`,
  a pak stroj stojí kousek „uvnitř" terénu a je za ním vidět kus země.
- **Zvuk** se celý syntetizuje (pila + obdélník + šum + pomalá modulace = naftový
  motor). Nic se nestahuje. Zvuk jde vypnout ikonou reproduktoru a volba se
  pamatuje.
- **Jméno stroje** se při výběru přečte nahlas přes `speechSynthesis`, pokud má
  prohlížeč český hlas.
- Respektuje `prefers-reduced-motion` a všechno má popisky pro čtečky.

## Pro rodiče

Stránka nikam neodkazuje, nic neposílá na internet a nic neukládá kromě
jediné volby „zvuk zapnutý/vypnutý" v `localStorage`. Dvojité klepnutí
nepřibližuje obraz a dlouhý stisk nevyvolá kontextové menu, aby dítě
nevyskočilo z hry.
