# XULETA ALTERVECTOR — JavaScript + CSS
*Guia de consulta i aprenentatge · Actualitzada juny 2026*

---

## ÍNDEX RÀPID
- [Variables](#variables)
- [Condicions](#condicions)
- [Funcions](#funcions)
- [Funcions asíncrones](#funcions-asincrones)
- [Gestió d'errors](#gestio-errors)
- [Arrays](#arrays)
- [Objectes](#objectes)
- [Strings (text)](#strings)
- [JSON](#json)
- [DOM — manipular l'HTML](#dom)
- [Events](#events)
- [Emmagatzematge al navegador](#emmagatzematge)
- [Fetch — cridar al Worker](#fetch)
- [Bombolla privada (IIFE)](#iife)
- [Navegació i temporitzadors](#navegacio)
- [Depuració](#depuracio)
- [CSS — Display](#css-display)
- [CSS — Position](#css-position)
- [CSS — Mides i unitats](#css-mides)
- [CSS — Padding i Margin](#css-spacing)
- [CSS — Flexbox](#css-flexbox)
- [CSS — Overflow](#css-overflow)
- [CSS — Z-index](#css-zindex)
- [CSS — Object-fit (imatges)](#css-objectfit)
- [CSS — Media Queries (responsive)](#css-media)
- [CSS — Variables i :root](#css-variables)
- [CSS — Patrons habituals](#css-patrons)

---

## VARIABLES {#variables}

La regla d'or: **`const` per al 95% dels casos. `let` només si el valor ha de canviar.**

| JS | VB6 | Quan usar |
|---|---|---|
| `const x = "text"` | `Const x As String` | Valor que no canvia mai |
| `let x = "text"` | `Dim x As String` | Valor que pot canviar (comptadors, bucles) |
| `window.x = ...` | `Public x` | Variable/funció accessible des de fora del fitxer |

```js
// Tipus bàsics
const nom = "Àgora"         // text (String)
const preu = 12.50          // número (sempre punt per als decimals, mai coma)
const actiu = true          // booleà (true/false, sense cometes)
const llista = []           // array buit
const objecte = {}          // objecte buit

// IMPORTANT: const no vol dir immutable del tot.
// No pots reassignar el "calaix", però sí modificar el seu contingut:
const CONFIG = { NOM: "Àgora", TEL: "600000000" }
CONFIG.TEL = "611111111"    // ✅ Vàlid — modifiques una propietat
// CONFIG = {}              // ❌ Error — no pots substituir l'objecte sencer

// let: per a valors que realment canvien
let comptador = 0
comptador = comptador + 1   // ✅ Funciona
```

---

## CONDICIONS {#condicions}

```js
// Comparacions bàsiques
if (x === "algo") { ... }       // si x és IGUAL a algo (=== és estricte: compara valor I tipus)
if (x !== "algo") { ... }       // si x NO és igual a algo
if (x > 10) { ... }            // si x és més gran que 10
if (!x) { ... }                // si x NO existeix / és fals / és buit / és null
if (x && y) { ... }            // si x I y són certs (AND)
if (x || y) { ... }            // si x O y és cert (OR)

// Equivalent VB6:
// If x = "algo" Then ...       → if (x === "algo")
// If Not x Then ...            → if (!x)
// If x And y Then ...          → if (x && y)
// If x Or y Then ...           → if (x || y)

// === vs ==
// Usa sempre ===  (tres iguals). L'== de dos iguals fa conversions estranyes.
// Exemple: 0 == "0" és true, però 0 === "0" és false.

// Ternari — if/else en una línia
const resultat = condicio ? "si és cert" : "si és fals"
// Equivalent VB6: IIf(condicio, "si és cert", "si és fals")

// Ternaris encadenats (útil per traduccions o mapejats)
const nom = seccio === 'Cocteles' ? 'Còctels' :
            seccio === 'Fríos'    ? 'Freds'   :
            seccio                             // valor per defecte (l'últim, sense ?)
```

---

## FUNCIONS {#funcions}

```js
// ── DECLARACIÓ ──────────────────────────────────────────────

// Privada — només accessible dins la mateixa bombolla (IIFE) o fitxer
const fer_algo = function() { ... }
const fer_algo = () => { ... }       // versió curta (arrow function), fan el mateix

// Pública — accessible des de l'HTML i altres fitxers JS
window.fer_algo = function() { ... }

// Equivalent VB6:
// Private Sub fer_algo()   → const fer_algo = () => { ... }
// Public Sub fer_algo()    → window.fer_algo = () => { ... }


// ── AMB PARÀMETRES ──────────────────────────────────────────

const sumar = function(a, b) {
    return a + b    // retorna el resultat
}
const resultat = sumar(3, 5)   // resultat = 8

// Paràmetre amb valor per defecte (si no es passa, usa el valor indicat)
const obrirModal = function(tipus, titol, esCarta = false) {
    // si no passes esCarta, serà false automàticament
}
obrirModal('menu', 'Diari')         // esCarta = false (per defecte)
obrirModal('menu', 'Carta', true)   // esCarta = true

// Equivalent VB6:
// Function calcular(x) ... calcular = x * 2  → const calcular = (x) => x * 2
```

---

## FUNCIONS ASÍNCRONES {#funcions-asincrones}

Quan una funció ha d'**esperar una resposta externa** (Worker, Airtable, internet...),
ha de ser `async` i usar `await` al punt d'espera.

```js
// async → "aquesta funció pot fer pauses"
// await → "espera aquí fins que arribi la resposta"

const carregar_dades = async () => {
    const res = await fetch(url)    // espera la resposta del Worker
    const dades = await res.json()  // espera que es llegeixi el JSON
    // ara dades ja té el contingut
}

// Equivalent VB6: DoEvents (deixa que passin altres coses mentre espera)

// Si crides una funció async des d'una altra, també has de posar await:
const iniciar = async () => {
    await carregar_dades()   // espera que acabi carregar_dades
    mostrar_menu()           // ara sí, executa això
}
```

---

## GESTIÓ D'ERRORS {#gestio-errors}

```js
try {
    // intenta fer això
    const res = await fetch(url)
    const dades = await res.json()
} catch (error) {
    // si peta per qualsevol motiu, fes això
    console.log("Error:", error)
    // "error" és l'objecte amb la descripció del problema
}

// Equivalent VB6: On Error GoTo GestioError

// Pots combinar try/catch amb if per gestionar errors de negoci:
try {
    const res = await fetch(url)
    if (!res.ok) {
        console.log("El servidor ha retornat error:", res.status)
        return
    }
    const dades = await res.json()
} catch (error) {
    console.log("Error de xarxa:", error)
}
```

---

## ARRAYS (llistes ordenades) {#arrays}

```js
// Un array és una llista ordenada. Cada element té una posició (índex) que comença en 0.
const llista = []                       // llista buida
const fruites = ["poma", "pera", "pesca"]

// Accedir
fruites[0]                              // "poma" (el primer)
fruites[2]                              // "pesca" (el tercer)
fruites.length                          // 3 (quants elements té)

// Afegir i treure
llista.push(element)                    // afegeix al final
llista.pop()                            // treu i retorna l'últim element

// ── RECÓRRER ───────────────────────────────────────────────

// forEach → recorre cada element (com For Each de VB6)
fruites.forEach(fruita => {
    console.log(fruita)
})

// map → recorre i TRANSFORMA, retorna un array nou
const majuscules = fruites.map(fruita => fruita.toUpperCase())
// ["POMA", "PERA", "PESCA"]

// filter → retorna un array nou amb només els elements que compleixen la condició
const actius = plats.filter(plat => plat.fields.Visible === true)
// Només els plats amb Visible=true

// find → retorna el PRIMER element que compleix la condició (o undefined si no en troba)
const plat = plats.find(p => p.id === "abc123")

// sort → ordena l'array (modifica l'original)
plats.sort((a, b) => (a.fields.Ordre || 0) - (b.fields.Ordre || 0))
// Ordena per Ordre numèric (ascendent)

// Comprovar si és un array
Array.isArray(x)                        // true si x és un array
```

---

## OBJECTES (claus i valors) {#objectes}

```js
// Un objecte és com una fitxa amb camps. Cada camp té un nom (clau) i un valor.
const plat = {
    nom: "Truita",
    preu: 8.50,
    visible: true
}

// Accedir als camps
plat.nom                    // "Truita" — notació amb punt (la més usada)
plat["nom"]                 // "Truita" — notació amb claudàtors (útil si el nom és variable)

// Modificar
plat.preu = 9.00
plat["preu"] = 9.00         // fan el mateix

// Comprovar si existeix un camp
if (!objecte["clau"]) objecte["clau"] = []   // si no existeix → crea'l buit

// Object.keys / values / entries — recórrer un objecte
const seccions = { Entrants: [...], Primer: [...] }

Object.keys(seccions)       // ["Entrants", "Primer"] — retorna les claus
Object.values(seccions)     // [[...], [...]] — retorna els valors
Object.entries(seccions)    // [["Entrants", [...]], ["Primer", [...]]] — clau + valor

// Exemple real: agrupar plats per secció
const grups = {}
plats.forEach(plat => {
    const seccio = plat.fields.Seccio
    if (!grups[seccio]) grups[seccio] = []   // si no existeix, crea array buit
    grups[seccio].push(plat)                 // afegeix el plat a la secció
})
// grups["Entrants"] = [plat1, plat2]
// grups["Primer"]   = [plat3, plat4]
```

---

## STRINGS (text) {#strings}

```js
// ── OPERACIONS BÀSIQUES ─────────────────────────────────────

text.trim()                     // elimina espais al principi i al final
text.toUpperCase()              // MAJÚSCULES
text.toLowerCase()              // minúscules
text.length                     // longitud (número de caràcters)
text.includes("paraula")        // true si conté "paraula"
text.startsWith("hola")         // true si comença per "hola"
text.endsWith("z")              // true si acaba per "z"

// ── EXTREURE PARTS ──────────────────────────────────────────

text.slice(0, 5)                // agafa del caràcter 0 al 4 (el 5 no s'inclou)
text.slice(2)                   // agafa des del caràcter 2 fins al final
text.slice(0, -1)               // treu l'últim caràcter
text.slice(-3)                  // agafa els 3 últims caràcters

// Equivalent VB6:
// Trim(text)          → text.trim()
// UCase(text)         → text.toUpperCase()
// Len(text)           → text.length
// InStr(text,"xxx")   → text.includes("xxx")
// Left(text,5)        → text.slice(0,5)
// Mid(text,3,4)       → text.slice(2,6)   (OJO: JS comença en 0, VB6 en 1)
// Right(text,3)       → text.slice(-3)

// ── SUBSTITUIR I NETEJAR ─────────────────────────────────────

text.replace("vell", "nou")     // substitueix la PRIMERA aparició
text.replaceAll("vell", "nou")  // substitueix TOTES les aparicions

// Exemple real: treure prefix numèric de les seccions d'Àgora
const seccio = "01 Entrants"
const net = seccio.replace(/^\d+\s*/, '')   // → "Entrants"
// /^\d+\s*/ és una expressió regular: \d+ = dígits, \s* = espais

// ── PLANTILLES DE TEXT (template literals) ───────────────────

// Amb les cometes inclinades ` pots posar variables dins el text amb ${}
const nom = "Carles"
const missatge = `Hola, ${nom}! Benvingut.`   // "Hola, Carles! Benvingut."

// Pots posar expressions directament:
const preu_amb_iva = `Preu: ${(preu * 1.21).toFixed(2)} €`

// Molt útil per generar HTML:
const html = `
    <div class="plat">
        <span>${plat.nom}</span>
        <span>${plat.preu} €</span>
    </div>
`
```

---

## JSON {#json}

JSON és el format universal per enviar i rebre dades per internet (com Airtable, Workers...).
Sembla un objecte JS però tot va entre cometes i no pot tenir funcions.

```js
// ── CONVERTIR OBJECTE → TEXT (per guardar o enviar) ─────────

const dades = { nom: "Truita", preu: 8.50 }
const text = JSON.stringify(dades)
// text = '{"nom":"Truita","preu":8.5}'

// Cas pràctic: guardar a localStorage (que només accepta text)
localStorage.setItem("dades", JSON.stringify(dades))

// ── CONVERTIR TEXT → OBJECTE (per llegir el que arriba) ─────

const text_rebut = '{"nom":"Truita","preu":8.5}'
const objecte = JSON.parse(text_rebut)
objecte.nom    // "Truita"
objecte.preu   // 8.5

// Cas pràctic: llegir de localStorage
const guardat = localStorage.getItem("dades")
const recuperat = JSON.parse(guardat)
recuperat.nom  // "Truita"

// ⚠️ Si el text no és JSON vàlid, JSON.parse peta.
// Sempre ficar-ho dins try/catch si véns de fonts externes:
try {
    const obj = JSON.parse(text_estrany)
} catch (e) {
    console.log("No era JSON vàlid:", e)
}
```

---

## DOM — MANIPULAR L'HTML {#dom}

El DOM és l'arbre d'elements HTML de la pàgina. Amb JS el pots llegir i modificar.

```js
// ── SELECCIONAR ELEMENTS ─────────────────────────────────────

document.getElementById('modal-menu')          // per id (l'id és únic a la pàgina)
document.querySelector('#modal-menu')          // igual que getElementById, per id
document.querySelector('.boto-tancar')         // per classe (agafa el PRIMER que troba)
document.querySelectorAll('.fila-plat')        // per classe (agafa TOTS → NodeList)

// Quan usar un o l'altre:
// getElementById → quan busques per id (és el més ràpid)
// querySelector  → quan busques per classe o selector complex (com CSS)

// Comprovar si existeix
if (!document.getElementById('modal-menu')) { ... }   // si NO existeix

// ── CANVIAR VISIBILITAT ──────────────────────────────────────

element.style.display = 'none'    // desapareix (però existeix al codi)
element.style.display = 'block'   // apareix com a bloc
element.style.display = 'flex'    // apareix com a flex

// ── LLEGIR I ESCRIURE CONTINGUT ──────────────────────────────

element.textContent = 'Hola'           // escriu text pla (segur, no interpreta HTML)
element.innerHTML = '<b>Hola</b>'      // escriu HTML (pot ser perillós si ve d'usuari)
element.innerText = 'Hola'            // com textContent però té en compte estils CSS

// Injectar HTML
element.innerHTML = `<p>Contingut nou</p>`               // substitueix el contingut
document.body.insertAdjacentHTML('beforeend', `<div>...`) // afegeix al FINAL del body

// Posicions de insertAdjacentHTML:
// 'beforebegin' → just ABANS de l'element
// 'afterbegin'  → dins l'element, al PRINCIPI
// 'beforeend'   → dins l'element, al FINAL (el més usat)
// 'afterend'    → just DESPRÉS de l'element

// ── INPUTS ──────────────────────────────────────────────────

input.value              // llegeix el valor actual
input.value.trim()       // llegeix el valor sense espais accidentals
input.value = ''         // buida l'input
input.focus()            // posa el cursor a l'input
input.checked            // true/false per a checkboxes

// ── CLASSES CSS ─────────────────────────────────────────────

element.classList.add('actiu')          // afegeix una classe
element.classList.remove('actiu')       // treu una classe
element.classList.toggle('actiu')       // si la té la treu, si no la té l'afegeix
element.classList.contains('actiu')     // true si la té

// ── ATRIBUTS ────────────────────────────────────────────────

element.getAttribute('data-id')         // llegeix un atribut
element.setAttribute('data-id', '123')  // escriu un atribut
element.dataset.id                      // drecera per a atributs data-xxx
```

---

## EVENTS (escoltar clics, tecles...) {#events}

```js
// Equivalent VB6: Private Sub boto_Click()
element.addEventListener('click', fer_algo)

// Amb funció inline (anònima)
element.addEventListener('click', () => {
    console.log("has clicat!")
})

// Amb paràmetre de l'event (per saber QUÈ s'ha clicat, quina tecla...)
element.addEventListener('click', (event) => {
    console.log(event.target)       // l'element que s'ha clicat
})

// Tecles — Equivalent VB6: Private Sub input_KeyDown()
element.addEventListener('keydown', (e) => {
    if (e.key === 'Enter') fer_algo()
    if (e.key === 'Escape') tancar_modal()
})

// Long press (mantenir el dit premut)
let temporitzador
element.addEventListener('touchstart', () => {
    temporitzador = setTimeout(obrir_login, 1500)   // 1.5 segons
})
element.addEventListener('touchend', () => {
    clearTimeout(temporitzador)     // cancel·la si l'aixeques abans
})

// Esdeveniments habituals:
// 'click'       → clic de ratolí o tap
// 'touchstart'  → inici de toc (mòbil)
// 'touchend'    → fi de toc (mòbil)
// 'keydown'     → tecla premuda
// 'change'      → canvi en un input/select
// 'input'       → mentre s'escriu (cada caràcter)
// 'submit'      → enviament de formulari
// 'load'        → quan la pàgina ha carregat
```

---

## EMMAGATZEMATGE AL NAVEGADOR {#emmagatzematge}

Tres nivells de memòria al navegador:

| | Dura | On es veu | Quan usar |
|---|---|---|---|
| Variable JS | Fins recarregar | Consola → no | Càlculs temporals |
| `sessionStorage` | Fins tancar el navegador | F12 → Application | Sessió de login activa |
| `localStorage` | Permanent (fins esborrar) | F12 → Application | Preferències, superadmin |

```js
// ── sessionStorage ──────────────────────────────────────────

sessionStorage.setItem('admin_clau', '1234')    // guarda
sessionStorage.getItem('admin_clau')            // llegeix (null si no existeix)
sessionStorage.removeItem('admin_clau')         // esborra un element
sessionStorage.clear()                          // esborra tot

// ── localStorage ─────────────────────────────────────────────

localStorage.setItem('tema', 'dark')
localStorage.getItem('tema')                    // "dark"
localStorage.removeItem('tema')

// Guardar un objecte (localStorage solo accepta text → JSON)
const config = { idioma: "ca", tema: "dark" }
localStorage.setItem('config', JSON.stringify(config))

// Recuperar-lo
const guardat = localStorage.getItem('config')
const config_recuperat = JSON.parse(guardat)
config_recuperat.idioma   // "ca"

// ⚠️ Per veure localStorage i sessionStorage:
// Obrir la web → F12 (Inspeccionar) → pestanya "Application" → Local Storage / Session Storage
// NO busquis els arxius a Windows Explorer — no hi són com fitxers.
```

---

## FETCH — CRIDAR AL WORKER {#fetch}

```js
// ── GET bàsic ────────────────────────────────────────────────

const res = await fetch('https://agora.altervector.workers.dev/')
const dades = await res.json()

// ── GET amb paràmetres a la URL ──────────────────────────────

const clau = "abc&123"
const res = await fetch(`${CONFIG.BASE_WORKER}/login?p=${encodeURIComponent(clau)}`)
// encodeURIComponent → converteix caràcters especials (& → %26, espais → %20...)
// sense això, els caràcters especials trenquen la URL

// ── POST (enviar dades) ──────────────────────────────────────

const res = await fetch(CONFIG.BASE_WORKER, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ nom: "Nou plat", preu: 10.50 })
})

// ── LLEGIR LA RESPOSTA ───────────────────────────────────────

res.ok              // true si ha anat bé (HTTP 200-299)
res.status          // codi numèric (200=ok, 404=no trobat, 500=error servidor)
await res.text()    // llegeix la resposta com a text pla
await res.json()    // llegeix la resposta com a objecte JS (si el Worker envia JSON)

// ── EXEMPLE COMPLET AMB GESTIÓ D'ERRORS ─────────────────────

const carregar = async () => {
    try {
        const res = await fetch(CONFIG.BASE_WORKER)
        if (!res.ok) {
            console.log("Error del servidor:", res.status)
            return
        }
        const dades = await res.json()
        // fer coses amb dades...
    } catch (error) {
        console.log("Error de xarxa:", error)
    }
}
```

---

## BOMBOLLA PRIVADA (IIFE) {#iife}

Cada fitxer JS hauria d'anar dins una IIFE per evitar que les variables es barregin
entre fitxers. Només surten les que poses `window.` davant.

```js
// IIFE = Immediately Invoked Function Expression
// Es declara i s'executa a la vegada

(function() {

    const privat = "ningú em veu des de fora"      // privat
    window.public = "tothom em veu des de fora"    // públic

    const fer_coses_internes = () => { ... }        // privat
    window.obrir_modal = () => { ... }              // públic — accessible des de l'HTML

})();   // ← els () del final l'executen immediatament

// Equivalent VB6: mòdul amb Sub's Private i Public
// Private Sub fer_algo()   → const privat dins la IIFE
// Public Sub fer_algo()    → window.public fora

// ⚠️ Si dues IIFE de fitxers diferents defineixen window.xxx amb el mateix nom,
// la segona sobreescriu la primera. Noms únics per evitar conflictes.
```

---

## NAVEGACIÓ I TEMPORITZADORS {#navegacio}

```js
// ── NAVEGACIÓ ────────────────────────────────────────────────

window.location.href = 'admin.html'         // va a una altra pàgina
window.location.href = CONFIG.URL_ADMIN     // des d'una variable
window.location.reload()                    // recarrega la pàgina actual

// ── TEMPORITZADORS ───────────────────────────────────────────

// setTimeout → executa UNA vegada après X mil·lisegons
// 1000ms = 1 segon
setTimeout(() => input.focus(), 100)        // espera 100ms i posa el focus

const id = setTimeout(() => fer_algo(), 2000)   // guarda l'id
clearTimeout(id)                                // cancel·la si cal

// setInterval → executa REPETIDAMENT cada X mil·lisegons
const interval = setInterval(() => actualitzar(), 5000)   // cada 5 segons
clearInterval(interval)                                    // para l'interval
```

---

## DEPURACIÓ {#depuracio}

```js
// console.log → el "MsgBox invisible" de JS
// Es veu a F12 → pestanya "Console"

console.log("hola")                    // text simple
console.log("valor:", variable)        // text + valor
console.log("objecte:", dades)         // mostra l'objecte complet, expandible
console.log("array:", llista)          // mostra l'array complet

// Molt útil per saber si el codi arriba a un punt:
console.log("--- INICI de carregar_dades ---")
console.log("res.ok:", res.ok)
console.log("dades rebudes:", dades)

// On es veu: Obrir web → F12 → Console
// En mòbil: no es veu directament, però pots usar: alert("valor: " + variable)
```

---
---

# CSS

---

## DISPLAY — com es comporta el bloc {#css-display}

`display` controla com un element ocupa l'espai i com es relaciona amb els seus veïns.

```css
display: block;         /* ocupa tota l'amplada, cada element va a sota */
display: inline;        /* va en línia amb el text, NO accepta width/height */
display: inline-block;  /* va en línia PERÒ accepta width i height */
display: none;          /* desapareix completament (no ocupa espai) */
display: flex;          /* activa Flexbox — veure secció Flexbox */
```

| Valor | Qui l'usa per defecte | Característiques |
|---|---|---|
| `block` | `div`, `p`, `h1`... | Ocupa tota l'amplada, va a la línia de sota |
| `inline` | `span`, `a` | Va dins del text, no accepta width/height |
| `inline-block` | botons, etiquetes | Va en línia PERÒ accepta mides |
| `none` | — | Desapareix. El JS usa `display = 'block'` per tornar-lo |
| `flex` | contenidors | Organitza els fills en fila o columna |

```css
/* Amagar i mostrar des de JS */
element.style.display = 'none'     /* amaga */
element.style.display = 'block'    /* mostra com a block */
element.style.display = 'flex'     /* mostra com a flex */
```

---

## POSITION — on es col·loca {#css-position}

`position` controla com un element es situa a la pàgina.
Sense position, `top`/`left`/`right`/`bottom` no fan res.

| Valor | Referència | Ocupa espai? | Quan s'usa |
|---|---|---|---|
| `static` | Normal (per defecte) | Sí | El default, no cal escriure'l |
| `relative` | Ell mateix | Sí | Moure lleugerament, o ser referència d'un fill absolute |
| `absolute` | El pare amb position ≠ static | No | Posar coses a llocs exactes dins un contenidor |
| `fixed` | La pantalla (viewport) | No | Navbar, modals flotants, botons fixos |
| `sticky` | El scroll del contenidor | Sí | Capçaleres de taula, títols que es queden fixos en scroll |

```css
/* RELATIVE — es mou respecte a on estaria normalment */
.element {
    position: relative;
    top: 20px;      /* 20px cap avall del seu lloc natural */
    left: 10px;     /* 10px cap a la dreta */
}

/* ABSOLUTE dins RELATIVE — el combo habitual */
.pare {
    position: relative;     /* ← necessari! sense això el fill vola fins al body */
}
.fill {
    position: absolute;
    bottom: 8px;    /* 8px des de la part inferior del pare */
    right: 8px;     /* 8px des de la dreta del pare */
}

/* FIXED — queda fix a la pantalla mentre fas scroll */
.navbar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 100;
}

/* STICKY — es comporta com relative fins que arriba al scroll, llavors es fixa */
.capçalera-taula {
    position: sticky;
    top: 0;
}
/* ⚠️ ATENCIÓ: si el pare té overflow:auto o overflow:hidden, sticky no funciona */
```

---

## MIDES I UNITATS {#css-mides}

| Unitat | Significa | Exemple |
|---|---|---|
| `px` | Píxels fixos | `width: 200px` — sempre 200px |
| `%` | % del pare | `width: 50%` — la meitat del contenidor |
| `vw` | % de l'amplada de pantalla | `width: 100vw` — tota la pantalla d'ample |
| `vh` | % de l'alçada de pantalla | `height: 100vh` — tota la pantalla d'alt |
| `rem` | Relatiu a la font base (16px) | `font-size: 1.5rem` = 24px |
| `em` | Relatiu a la font del pare | S'acumula — millor evitar-lo |
| `auto` | El navegador calcula | `margin: 0 auto` — centra el bloc |
| `calc()` | Càlcul mixt d'unitats | `width: calc(100% - 40px)` |

```css
/* Contenidor responsive clàssic */
.wrapper {
    width: 100%;
    max-width: 1200px;   /* mai més ample que això en pantalles grans */
    min-width: 300px;    /* mai més estret que això */
    margin: 0 auto;      /* centrat horitzontalment */
    padding: 0 20px;     /* protecció als costats en mòbil */
}

/* Centrar un bloc */
.element {
    width: 60%;
    margin: 0 auto;   /* necessita width definit per funcionar */
}

/* Proporcions lògiques — millor que calc() per alçades proporcionals */
.imatge-portada {
    width: 100%;
    aspect-ratio: 16 / 9;   /* manté proporció de vídeo sigui quina sigui l'amplada */
    object-fit: cover;
}
```

---

## PADDING I MARGIN — espais {#css-spacing}

- **Padding** = espai per **dins** (entre el contingut i la vora del bloc)
- **Margin** = espai per **fora** (entre el bloc i els seus veïns)

```css
/* 4 valors: dalt  dreta  baix  esquerra (sentit horari) */
padding: 10px 20px 10px 20px;

/* 2 valors: dalt/baix  dreta/esquerra */
padding: 10px 20px;

/* 1 valor: tots iguals */
padding: 10px;

/* Per separat */
padding-top: 10px;
padding-right: 20px;
padding-bottom: 10px;
padding-left: 20px;

/* Margin funciona igual */
margin: 16px;
margin: 0 auto;          /* centrar — el truc clàssic */
margin-inline: auto;     /* equivalent modern */

/* box-sizing: border-box — molt important! */
/* Per defecte, el padding S'AFEGEIX a l'amplada declarada.
   Amb border-box, el padding ja va INCLÒS dins el width. */
* { box-sizing: border-box; }   /* aplicar a tot — molt recomanable */
```

---

## FLEXBOX — organitzar fills {#css-flexbox}

Flexbox actua sobre el **pare** per organitzar els **fills**.
Sempre: `display: flex` al pare.

```css
/* ── AL PARE ─────────────────────────────────────────────── */

display: flex;

/* Direcció */
flex-direction: row;       /* → fills en fila (per defecte) */
flex-direction: column;    /* ↓ fills en columna */

/* Alineació eix principal (horitzontal si row, vertical si column) */
justify-content: flex-start;     /* al principi */
justify-content: center;         /* centrat */
justify-content: flex-end;       /* al final */
justify-content: space-between;  /* primer i últim als extrems, resta repartit */
justify-content: space-around;   /* espai igual al voltant de cada fill */

/* Alineació eix perpendicular */
align-items: flex-start;   /* alineats a dalt (si row) */
align-items: center;       /* centrats */
align-items: flex-end;     /* alineats a baix */
align-items: stretch;      /* s'estiren per omplir (per defecte) */

/* Saltar de línia si no caben */
flex-wrap: wrap;    /* els fills salten de línia — molt útil per responsive */
flex-wrap: nowrap;  /* sempre en una línia (per defecte) */

/* Espai entre fills */
gap: 16px;           /* espai uniforme entre tots */
gap: 10px 20px;      /* espai vertical  espai horitzontal */


/* ── ALS FILLS ───────────────────────────────────────────── */

/* flex — com creix un fill per ocupar l'espai disponible */
flex: 1;    /* ocupa tot l'espai disponible */
flex: 2;    /* ocupa el doble que un fill amb flex:1 */
/* Exemple: 3 fills amb flex:1 es reparteixen l'espai a parts iguals */
```

---

## OVERFLOW — quan el contingut no cap {#css-overflow}

```css
overflow: visible;   /* el contingut surt per fora (per defecte) */
overflow: hidden;    /* talla el que sobra — invisible */
overflow: auto;      /* posa scrollbar si cal */
overflow: scroll;    /* scrollbar sempre visible */

/* Per eix separat */
overflow-x: auto;    /* scrollbar horitzontal si cal */
overflow-y: hidden;  /* talla el contingut vertical */

/* ⚠️ PROBLEMA CLÀSSIC: overflow trenca sticky */
/* Si un contenidor té overflow:auto o overflow:hidden,
   els fills amb position:sticky no funcionen correctament.
   Solució: posar l'overflow al body, no al contenidor. */

/* ❌ No funciona */
.contenidor { overflow-x: auto; }
.fill { position: sticky; top: 0; }

/* ✅ Solució */
body { overflow-x: auto; }
```

---

## Z-INDEX — qui va per sobre {#css-zindex}

El z-index controla qui tapa qui en cas de superposició.
**Només funciona si l'element té `position` diferent de `static`.**

```css
/* Escala habitual de valors */
z-index: 1;      /* contingut normal */
z-index: 10;     /* elements flotants */
z-index: 100;    /* navbar, capçaleres */
z-index: 9999;   /* modals (sempre per sobre de tot) */

/* Exemple: navbar per sobre del contingut */
.navbar {
    position: fixed;
    top: 0;
    z-index: 100;
}

/* Exemple: modal per sobre de tot */
.modal {
    position: fixed;
    z-index: 9999;
}
```

---

## OBJECT-FIT — imatges que s'adapten {#css-objectfit}

Quan poses una imatge dins un contenidor de mida fixa, `object-fit` controla com s'adapta.

```css
img {
    width: 100%;
    height: 100%;
    object-fit: cover;    /* ← el més usat: omple i retalla. No deforma. */
    object-fit: contain;  /* entra sencer sense retallar (pot deixar espai buit) */
    object-fit: fill;     /* estira per omplir (DEFORMA la imatge) */

    object-position: center;     /* on centra el retall (per defecte) */
    object-position: top;        /* mostra la part superior */
    object-position: center top; /* horitzontal vertical */
}

/* Exemple pràctic: miniatura quadrada */
.miniatura {
    width: 80px;
    height: 80px;
    object-fit: cover;    /* omple el quadrat sense deformar */
    border-radius: 4px;
}
```

---

## MEDIA QUERIES — responsive {#css-media}

Permeten aplicar estils diferents segons la mida de pantalla.
El patró és **mobile-first**: primer escrius els estils per mòbil, i després afegeixes els canvis per a pantalles més grans.

```css
/* ── MÒBIL (per defecte, sense media query) ── */
.element {
    width: 95%;
    font-size: 14px;
    flex-direction: column;
}

/* ── TABLET (a partir de 768px) ── */
@media (min-width: 768px) {
    .element {
        width: 85%;
        font-size: 16px;
    }
}

/* ── PC (a partir de 1024px) ── */
@media (min-width: 1024px) {
    .element {
        width: 70%;
        max-width: 1200px;
        font-size: 18px;
        margin: 0 auto;
    }
}
```

| Dispositiu | min-width | Rang típic |
|---|---|---|
| Mòbil petit | — | fins 480px |
| Mòbil gran | 480px | fins 768px |
| Tablet | 768px | fins 1024px |
| PC | 1024px | fins 1440px |
| PC gran | 1440px | i més |

```css
/* Exemple: navbar responsive */

/* MÒBIL: menú ocult, botó hamburguesa visible */
.navbar-menu { display: none; }
.btn-hamburguesa { display: block; }

/* PC: menú visible, sense hamburguesa */
@media (min-width: 1024px) {
    .navbar-menu {
        display: flex;
        gap: 20px;
    }
    .btn-hamburguesa { display: none; }
}
```

---

## VARIABLES CSS I :ROOT {#css-variables}

Les variables CSS permeten definir colors, mides i valors reutilitzables en un sol lloc.

```css
/* Definir les variables a :root (accessible a tota la pàgina) */
:root {
    --color-principal: #2c3e35;
    --color-accent: #c8973a;
    --fons: #f8f5f0;
    --font-base: 16px;
    --background: url('images/background.jpg');
}

/* Usar les variables */
.navbar {
    background: var(--color-principal);
    color: var(--color-accent);
}

html {
    background: var(--background);
    background-size: cover;
}

/* ⚠️ IMPORTANT: CSS no pot llegir variables de JS (CONFIG).
   Si necesites una URL d'imatge al CSS, ha de ser una variable CSS a :root,
   no una variable JS. */
```

---

## PATRONS HABITUALS {#css-patrons}

```css
/* ── RESET BÀSIC ─────────────────────────────────────────── */
* {
    box-sizing: border-box;  /* padding inclòs dins el width */
    margin: 0;
    padding: 0;
}

/* ── CONTENIDOR MAESTRO ──────────────────────────────────── */
/* Evita que la web "exploti" en pantalles ultra-panoràmiques */
.wrapper {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
}

/* ── CENTRAR VERTICAL I HORITZONTALMENT ──────────────────── */
.centrat {
    display: flex;
    justify-content: center;
    align-items: center;
}

/* ── IMATGE DE FONS QUE COBREIX TOT ─────────────────────── */
.hero {
    background-image: url('imatge.jpg');
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
}

/* ── PROTOCOL D'ERRORS CSS ───────────────────────────────── */
/*
  Si un element no es veu o no es comporta com esperes:
  1. Gramàtica: has escrit "height" o "heigth"? CSS ignora el que no entén sense avisar.
  2. Jerarquia: el pare té display:flex? Si no, el fill no s'alinearà.
  3. Capes: prova z-index o canvia el color de fons per veure on és.
  4. Sticky: el pare té overflow:auto? Això trenca el sticky dels fills.
  5. Position: tens top/left sense position? No fan res sense position.
*/
```

---

*Xuleta AlterVector — Carles 2026 · Afegir seccions a mesura que s'aprèn*
