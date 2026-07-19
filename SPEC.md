# 📘 SPEC — Energie-Tag

**Ernährungs-Lernspiel für ein 7-jähriges Kind.** Vollständige Spezifikation: Datenmodell, Mechaniken, Darstellung, Layout, Inventar, gebaute Artefakte.

`lebensmittel.json` v10.0 · **111 Zutaten · 16 Gerichte · 12 Regale · 6 Rollen · 158 „Wusstest du?"-Fakten**

---

## 0. Die Rahmung (das Wichtigste)

Alles andere ist verhandelbar, das hier nicht:

- **Kein "gesund/ungesund".** Regale sortieren nach *Ort*, Rollen nach *Funktion*. Nirgends eine Wertung.
- **Kein Essen ist eine Sache.** Rollen sind **mehrfach** — Gouda ist 💪 *und* 🧈. Schokolade ist 💪 *und* ⚡ *und* 🧈.
- **Kein Game-Over.** Kein Scheitern, nur Konsequenzen.
- **kcal-Zahl standardmäßig AUS.** Energie als Balken, nicht als Ziffer zum Zählen.
- **Bäuchlein neutral und reversibel.** Kein Gewicht, keine Wertung des Aussehens.
- **Erfolg heißt wach / konzentriert / stark** — nicht "schlank".
- **Das Happy Meal gewinnt beim Eisen** und verliert beim Salz. Das Spiel sagt beides.
- **Trauben werden nicht bestraft.** Sie sind lecker und dürfen sein — sie sind nur kein Vitaminlieferant.
- **Kein Angst-System.** Siehe §4: Fast nichts ist bei einem Kind aus normalem Essen "zu viel". Für jeden Nährstoff eine Strafe zu erfinden wäre faktisch falsch.

---

## 1. Datenmodell

### 1.1 Zutat

```jsonc
{
  "id": "karotte_mittel",
  "name": "Karotte roh (mittel)",
  "emoji": "🥕",
  "portion_g": 80,
  "regal": "gemuese",            // wo man es findet (Navigation)
  "rollen": ["🌾 Lange Energie", "⚡ Schnelle Energie"],   // BERECHNET, nicht gepflegt
  "kcal": 33,
  "gi_faktor": 0.20,             // wie viel der komplexen KH wirkt wie Zucker
  "wasser_ml": 70,
  "phytatreich": false,
  "enthaelt_fleisch": false,
  "makro":   { "kohlenhydrate_g", "davon_zucker_g", "ballaststoffe_g",
               "eiweiss_g", "fett_g", "fett_gesaettigt_g", "fett_ungesaettigt_g" },
  "mineral": { "eisen_haem_mg", "eisen_nicht_haem_mg", "kalzium_mg", "magnesium_mg",
               "natrium_mg", "kalium_mg", "zink_mg", "jod_ug", "selen_ug" },
  "vitamin": { "vitamin_a_ug", "vitamin_c_mg", "vitamin_d_ug", "vitamin_e_mg", "vitamin_k_ug",
               "vitamin_b1_mg", "vitamin_b2_mg", "vitamin_b6_mg", "vitamin_b12_ug",
               "folat_ug", "niacin_mg" },
  "kinder_text": "…",
  "wusstest_du": [ { "icons": "🥕🔥😀", "text": "…", "stimmung": "gut|schlecht|wow" } ]
}
```

### 1.2 Gericht (compound)

Gerichte speichern **nur** `bestandteile[]`. Alle Nährwerte, `wasser_ml`, `portion_g` und `gi_faktor` werden **abgeleitet** — nie von Hand gepflegt.

```jsonc
{ "id": "spinat_mit_ei", "bestandteile": [
    { "zutat": "spinat_gekocht",  "menge": 1 },
    { "zutat": "ei_gekocht",      "menge": 1 },
    { "zutat": "kartoffel_mittel","menge": 1 },
    { "zutat": "butter",          "menge": 1 } ],
  "berechnet": { … }   // generiert
}
```

### 1.3 Tagesbedarf (7-jähriges Kind, D-A-CH)

| | Soll | | Soll |
|---|---:|---|---:|
| Energie | 1800 kcal | Eisen | 10 mg |
| Eiweiß | 28 g | Calcium | 900 mg |
| Kohlenhydrate | 225 g | Magnesium | 170 mg |
| Fett | 60 g | Kalium | 3300 mg |
| Ballaststoffe | 15 g | Zink | 6 mg |
| Wasser | 970 ml | Jod | 140 µg |
| Vitamin A | 700 µg | Selen | 45 µg |
| Vitamin C | 65 mg | Vitamin B6 | 0,7 mg |
| Vitamin D | 20 µg | Vitamin B12 | 1,8 µg |
| Vitamin E | 9 mg | Folat (B9) | 180 µg |
| Vitamin K | 30 µg | | |

**Obergrenzen:** 🧂 Natrium max **1800 mg** (Soll 750) · 🍬 Zucker max **45 g** (WHO: 10 % der Energie)

> ✅ **Zucker-Limit nachgeprüft (2026-07):** WHO (2015) empfiehlt < 10 % der Energie an freien Zuckern — bei den 1800 kcal Tagesbedarf dieser App exakt **45 g**. Häufig zitierte ~42 g für 7–10-Jährige beruhen auf einer niedrigeren Referenzenergie (~1700 kcal). Die strengere WHO/DGE-Empfehlung (< 5 %) läge bei ~22 g.

---

## 2. Die zwei Kategorien-Systeme

> **Befund:** Ich habe versucht, jedem Lebensmittel *eine* Kategorie automatisch aus den Nährwerten zu geben. Ergebnis: **Joghurt → "Fett"** (48 % der Energie), **Kiwi → "Schnelle Energie"** (Zucker dominiert). Technisch korrekt, pädagogisch absurd.
>
> **Einzelkategorien sind genau das Denken, das dieses Spiel abtrainieren soll.** Deshalb zwei getrennte Systeme.

### 📚 REGAL — *wo man es findet* (Navigation, keine Wertung)

`🍞 Brot & Gebäck` · `🥣 Frühstück` · `🍎 Obst` · `🥬 Gemüse` · `🧀 Milch & Käse` · `🍔 Fleisch & Fast Food` · `🍽️ Fertige Teller` · `🍦 Eis & Kuchen` · `🍫 Süßes` · `🧈 Fette & Würze` · `🧃 Getränke` · `🥪 Gerichte`

### 🎭 ROLLEN — *was der Körper damit macht* (**mehrfach**, aus den Daten berechnet)

| Rolle | Auslöser | Anzahl |
|---|---|---:|
| ⚡ **Schnelle Energie** | schnelle KH ≥ 35 % der Energie | 40× |
| 🛡️ **Schutzstoff** | Vitamin C ≥ 25 % Tagesbedarf **oder** Ballaststoffe ≥ 3 g | 21× |
| 🧈 **Fett** | Fett ≥ 35 % der Energie | 16× |
| 💪 **Baustoff** | Eiweiß ≥ 5 g **oder** ≥ 20 % der Energie | 15× |
| 💧 **Durstlöscher** | ≥ 88 % Wasser **und** < 20 kcal | 7× |
| 🌾 **Lange Energie** | langsame KH ≥ 30 % der Energie | 6× |

Rollen werden **berechnet, nicht gepflegt**. Neues Lebensmittel eintragen → Rollen entstehen von selbst.

> ⚠️ Die Schutzstoff-Prüfung läuft **vor** der Durstlöscher-Abkürzung — sonst wäre die Mini-Paprika (65 % Vitamin C, aber 92 % Wasser und 9 kcal) nur ein "Durstlöscher".

---

## 3. Mechaniken

### 3.1 🌡️ Glykämischer Faktor

> **Modellfehler, den ich beheben musste:** Weißtoast landete unter "🌾 Lange Energie". Chemisch stimmt das (von 19,2 g KH sind nur 1,3 g Zucker), aber Weißmehl verhält sich im Körper *wie Zucker*.

```
schnelle_KH = Zucker + komplexeKH × gi_faktor
```

| | gi | | gi |
|---|---:|---|---:|
| 🍟 Pommes | 0,70 | 🍮 Pudding | 0,50 |
| 🥔 Kartoffel | 0,70 | 🍓 Marmelade | 0,35 |
| 🍞 Weißtoast | 0,65 | 🍌 Banane | 0,25 |
| 🍩 Donut / Amerikaner | 0,60 | 🍎 Obst, Gemüse | 0,20 |
| 🍪 Cookie / 🍔 Burger / 🍚 Couscous | 0,55 | 🥣 Hafermüsli | 0,15 |

### 3.2 ⏱️ Sättigung

```
Sättigung_min = kcal·0.08 + Eiweiß·6 + Ballast·10 + Fett·2 + langsameKH·1.5 − schnelleKH·2
```

Kalibriert: ausgewogenes Frühstück trägt bis zur Schulpause, Junk nicht.

**Kernlernmoment:** Cookie + Capri-Sonne = 458 kcal (mehr als Müsli mit 354) → **trotzdem Hunger um 8:13.**

Zwei Fehlerarten getrennt benannt: 💥 *"zu viel Zucker"* vs. *"zu wenig gegessen"*.

### 3.3 🩸 Eisen-Aufnahme (die wichtigste Mechanik)

> *"Es zählt nicht, was man isst — sondern was aufgenommen wird."*

**Zwei Türen:**

| | Basisquote | blockierbar? |
|---|---:|---|
| 🥩 **Häm-Eisen** (Fleisch, Fisch) | 25 % | kaum |
| 🌱 **Nicht-Häm** (Pflanze) | 10 % | stark |

**Faktoren auf Nicht-Häm** (gedeckelt bei 25 %):

| Faktor | Wirkung |
|---|---|
| 🍊 Vitamin C | **×3** — `min(3, 1 + VitC/25)` |
| 🥛 Calcium | **×0,5–1,0** — gemeinsame Tür |
| 🌾 Phytat | **×0,7** |
| 🥩 Fleisch in der Mahlzeit | **×1,5** |

**Befund:** Gleiches Müsli, **Faktor 4,2 Unterschied** je nach Beilage (Kuhmilch 0,06 mg vs. Hafermilch + Orange 0,26 mg aufgenommen).

**Praktische Regel:** *zu jeder Mahlzeit eine Kiwi oder Orange* · *Milch zeitlich trennen*.

**🥬 Popeye-Falle:** Spinat hat 5,0 mg Eisen (50 % Tagesbedarf) — blockiert es aber mit Oxalsäure/Phytat **selbst**.

### 3.4 ⚡🦴 Muskel-Regler

Calcium **spannt an**, Magnesium **lässt los**. Mg < 50 % + Sport → 🦵 Wadenkrampf, Sport-Ziel nicht schaffbar.

**Knochen-Depot:** der Calcium-Vorrat-Balken *ist* der Knochen — er schrumpft sichtbar.

### 3.5 💧 Wasserhaushalt

Soll 970 ml. Sport: −500 ml/h + Elektrolytverlust (Na −400, K −150, Mg −10 pro h). Schweiß ist salzig.

### 3.6 📊 Ist / Soll / Vorrat

Drei Werte pro Nährstoff, Vorrat-Delta über Tage. Speicherbarkeit unterschiedlich: Vit C und Wasser niedrig (täglich nötig), Eisen hoch (puffert).

### 3.7 ☀️ Vitamin D — die Lücke, die kein Teller schließt

| | µg | % Tagesbedarf (20 µg) |
|---|---:|---:|
| 🐟 Lachs (gebraten) | 13,4 | **67 %** |
| 🍳 **Rührei (2 Eier)** | 4,4 | **22 %** ← beste kinderfreundliche Quelle |
| 🐟 Thunfisch (halbe Dose) | 2,3 | 12 % |
| 🐟 7 Fischstäbchen | 2,1 | 11 % |
| 🍄 Champignons | 1,5 | 8 % |
| 🥚 Gekochtes Ei | 1,1 | 6 % |
| 🥞 Pfannkuchen | 0,8 | 4 % |
| 🥛 **Glas Milch** | **0,2** | **1 %** |

Um 100 % **nur aus Essen** zu decken, bräuchte ein Kind: 1,2 Portionen Lachs **oder** 9 Eier **oder** **100 Gläser Milch**.

> ☀️ Die Haut produziert bei 15 Minuten Sommersonne das **500- bis 1250-Fache** eines Lachsstücks. **80–90 % des Vitamin D kommt nicht aus dem Essen.**
>
> **Das Spiel sagt das ausdrücklich.** Ein Nährstoff-Tracker, der so tut, als könnte man Vitamin D wegessen, lügt. Die Lücke im Tortendiagramm ist kein Fehler — sie ist die Botschaft: *manche Lücke schließt kein Teller, sondern das Rausgehen.*

**Ebenso ehrlich:** 🥛 Milch hat in Deutschland **fast kein Vitamin D** (nicht angereichert, anders als in den USA). Ein weit verbreiteter Irrtum, den das Spiel korrigiert.


---

## 4. 🦷 Übermaß — die drei echten Mechaniken

> **Nachgerechnet: Fast nichts ist bei einem Kind aus normalem Essen realistisch "zu viel".**

| | Realistischer Tag | Verdikt |
|---|---|---|
| 🍬 **Zucker** | 1 Capri-Sonne + 1 Cookie = **47 g** (WHO 45 g) | **🔴 schon überschritten** |
| 🧂 Natrium | Happy Meal = 900 von 1800 mg | 🟢 unauffällig |
| 🌿 Ballaststoffe | Müsli + Beeren + Brokkoli + Apfel = 16 von 25 g | 🟢 unauffällig |
| 💪 Eiweiß | Fischstäbchen + Ei + Joghurt = 40 von 85 g | 🟢 unauffällig |
| 🥕 Vitamin A | 10 Karotten = 1430 % | 🟢 **kann nicht vergiften** |

Beta-Carotin wird nur *nach Bedarf* umgewandelt. Eisen-Überdosis aus Essen: unmöglich (der Darm drosselt). Vitamin C: wird ausgespült.

### Die drei echten Mechaniken

**🦷 Das Säure-Fenster** — Mundbakterien machen aus Zucker Säure, ~30 Min nach *jedem* Mal.

> Nicht die **Menge** zählt, sondern **wie oft**. 5 Gummibärchen über den Tag = **5 Säure-Fenster**. Die ganze Tüte auf einmal = **eines** — und das ist besser für die Zähne. Kontraintuitiv und wahr.

**🧂 Natrium → Durst** — über 1800 mg leert sich der Wassertank schneller. Koppelt an §3.5.

**🌿 Ballaststoffe → Trade-off** — Futter für gute Darmbakterien, aber >25 g: 💨 Bauchgrummeln **und** Phytat blockiert die Eisen-Tür. Keine Strafe, eine Abwägung.

**🥕 Bonus:** sehr viele Karotten → **orange Handflächen** (Karotinämie). Harmlos, reversibel, sichtbar.

### 🍽️ Die eigentliche Mechanik: VERDRÄNGUNG

> Ein 🍬 Zucker-Tag: 2117 kcal, 188 g Zucker — und **neun Nährstoffe stehen bei null** (Vitamin A, D, E, K, B6, B12, Folat, Jod, Selen).
>
> **Der Tag ist nicht giftig. Er ist leer.**
>
> Das Energiebudget ist begrenzt. Füllt es sich mit nährstoffarmem Essen, bleibt kein Platz. **Das ist kein Verbot — das ist eine Rechnung.** Und sie kommt ohne das Wort "ungesund" aus.

---

## 5. Darstellung

### 5.1 Farbsystem

| Token | Farbe | |
|---|---|---|
| `--zucker` | `#E5477E` | pink-rot |
| `--kh` | `#A85742` | bräunlich-rot (andere KH) |
| `--eiweiss` | `#3E86C8` | blau |
| `--fett-ges` | `#F0A830` | oranges Gelb |
| `--fett-unges` | `#C4CC4A` | grünliches Gelb |
| `--ballast` | `#4F9142` | grün |
| `--wasser` | `#4A9FD8` | blau |

**Mineralstoffe:** `--eisen #C0392B` · `--calcium #2E6F95` · `--magnesium #8B5CF6` · `--kalium #F39C12` · `--natrium #95A5A6` · `--zink #16A085` · `--jod #C2185B` · `--selen #6D8B3C`

**Vitamine:** `A #E8871E` · `C #F4A300` · `D #F2C94C` · `E #A3B565` · `K #2E7D32` · `B-Familie #B5628C`

> ⚠️ Calcium hatte ursprünglich `#4A9FD8` — **kollidierte mit Wasser**. Verschoben auf `#2E6F95`.

### 5.1b Texturen

Die Blöcke im Tropfen-Treemap sind nicht flach, sondern tragen **fotorealistische Kachel-Texturen** (WebP, 256 px, als Data-URI eingebettet, ein gemeinsamer `<pattern>`-Satz pro Dokument):

| Block | Textur | Zusatz |
|---|---|---|
| 🍬 Zucker | weiße Zuckerkristalle | Farbton-Overlay `--zucker` 28 % (Farbcode bleibt lesbar) |
| 🌾 Kohlenhydrate | Körnerbrot-Kruste | Overlay `--kh` 28 % |
| 🧈 Fett gesättigt | überbackener Käse | Overlay `--fett-ges` 28 % |
| 🌿 Pflanzenfett (unges.) | grün schimmernde Öl-Oberfläche | Overlay `--fett-unges` 28 % |
| 🥦 Ballaststoffe | grünes Gemüse (Artischocke/Brokkoli/Spargel) | auch im Kreis-Chart (Ballast-Segment) |
| 💧 Wassertropfen (Hülle) | fotorealistische Wasseroberfläche | mit leichtem Blau-Gradient-Overlay; Tropfenform, Rotation und Glanzlicht unverändert |
| Eiweiß | — bleibt flach blau | |

Prozent-Beschriftungen auf Textur-Blöcken: weiß mit dunkler Kontur (`paint-order:stroke`). Die Zucker-Textur füllt außerdem den 🍬-Zähler und seinen Fortschrittsbalken.

**App-Hintergrund:** Food-Doodle-Kacheltextur hinter Body und Screen, Deckkraft per Regler **🖼️ Hintergrund** einstellbar (0–100 %, Standard 50 %; innerhalb des Screens auf ~55 % gedämpft, damit Inhalte lesbar bleiben).

**Schrift:** Fredoka (Display) · Nunito (Body) · IBM Plex Mono (Zahlen). Hell, kindgerecht — *nicht* die Observatory-Dunkelästhetik.

### 5.2 Flächen-Diagramm

| Element | Bedeutung |
|---|---|
| **Fläche** des Kastens | Gewicht (maßstabsgetreu, `Fläche ∝ Gramm`) |
| **Form** | Wassergehalt |
| **Innere Kästen** | Nährstoffe, flächentreue **squarified Treemap**, beschriftet in **%** |
| **Ballaststoffe + Mineralstoffe** | *ein* grüner Kasten |

Bei sehr großer Spannweite (Gummibärchen 2 g … Happy Meal 616 px) schaltet sich ein **Zoom** ein, der sich selbst anzeigt (`Maßstab ×2,81`). Flächentreue bleibt **innerhalb** einer Ansicht exakt erhalten.

### 5.3 💧 Tropfen ⇄ 🔲 Kasten (reines CSS)

```css
--t = clamp((Wasser% − 10) / 40, 0, 1)     /* 0 = Kasten … 1 = Tropfen */

.huelle {
  /* Spitze oben rechts → nach −45° Drehung zeigt sie nach OBEN */
  border-radius: var(--rund) var(--spitz) var(--rund) var(--rund);
  transform: rotate(calc(var(--t) * -45deg)) scale(calc(1 + .0916*var(--t)));
  transition: border-radius .85s, transform .85s;
}
```

- `--rund` = `11% + 39%·t` · `--spitz` = `11%·(1−t)`
- **Flächenkorrektur ×1,0916**: Der Tropfen hat nur **83,9 %** der Quadratfläche. Ohne Korrektur würde ein wasserreiches Lebensmittel *leichter aussehen*.
- **Der Nährstoff-Block dreht nicht mit** — er ist ein *Geschwister* der Hülle, kein Kind. Keine Gegenrotation nötig.
- **Geometrische Deckung:** In den Innenkreis eines Tropfens passt maximal ein Rechteck mit **50 % der Fläche**. Und >50 % Wasser heißt <50 % Nährstoffe. Die 50-%-Schwelle ist exakt die geometrische Grenze.

### 5.4 👍 Mikronährstoff-Auszeichnung

Unter dem Wassertropfen. **EU-Konvention** (nicht selbst ausgedacht):

| | Schwelle | |
|---|---|---|
| 👍 klein | ab **15 %** Tagesbedarf | "gute Quelle" |
| 👍 **groß** | ab **30 %** | "viel drin" |

- **Vitamine** → Kreis mit Buchstabe: `Ⓐ` `Ⓒ` `B12`
- **Mineralstoffe** → farbiges Label-Pill in der zugewiesenen Farbe
- Dahinter: **wofür** (`👁️ Augen`, `🧠 Gehirn`)
- **Umschalter 🧒 Kind ↔ 🔬 Echte Werte** — Kind sieht Daumen, Experte `668 µg · 95 %`. Echte Werte bleiben immer in der DB.

> Spinat hat rechnerisch **3708 % Vitamin K**. Echt, aber als Zahl für ein Kind unbrauchbar → deshalb der Umschalter.

### 5.5 💡 Wusstest du?

**133 Fakten — jeder der 112 Einträge hat mindestens einen.** Immer nur *einer* sichtbar, 🔄 blättert.

**Icon-Kette** (3 Emoji, mit Pfeilen): `🥕 → 🔥 → 😀`

| `stimmung` | Rand | Kopfzeile |
|---|---|---|
| `gut` | 🟢 grün | "Gute Nachricht" |
| `schlecht` | 🔴 rot | "Schade, aber wahr" |
| `wow` | 🟡 amber | "Wusstest du?" |

> ⚠️ **Korrektur:** Gekochte Karotten geben **mehr** Vitamin A ab als rohe — die Hitze knackt die Zellwände. Die Verlust-Geschichte stimmt bei **Brokkoli** (im Wasser gekocht: halbes Vitamin C weg).

### 5.6 🥧 Tagesabdeckung (Tortendiagramm)

- **20 Segmente**, gleicher Winkel (18°), gruppiert: Makros (5) · Mineralstoffe (7) · Vitamine (8)
- **Radius ∝ % vom Tagesbedarf**, **gekappt bei 100 %**
- **Über 100 % wächst das Stück NICHT** — die **Farbe wird dunkler**, plus Marker `2,2×`
- Blasser Rest zeigt, was bis 100 % fehlt
- **Pfeile nach außen** mit Label + Funktion: *Eiweiß → Reparatur & Bausteine für Zellen, vor allem Muskeln*
- **Hover auf ein Segment** → Popup mit den **Top 3** Lebensmitteln, die es füllen würden
  - sortiert nach **Wirkung pro Kalorie** (nicht nach absolutem Gehalt — sonst schlägt das Tool auf "Vitamin C fehlt" ein Couscous-Gericht vor)
  - dedupliziert nach Grundname (sonst 3× Mini-Paprika)
  - Schwelle: muss ≥ 10 % beitragen
  - ist das Segment ≥ 100 % → *"✅ Schon gedeckt"*

### 5.7 ⚖️ Verhältnis Fett : Kohlenhydrate : Eiweiß

**D-A-CH-Referenzwerte, Kind 4–15 Jahre — Anteil an der *Energie*, nicht am Gewicht:**

| | Bereich | Soll-Zone |
|---|---|---:|
| 🧈 **Fett** | 30–35 % | 32 % |
| 🌾 **Kohlenhydrate** | 50–55 % | 53 % |
| 💪 **Eiweiß** | 10–15 % | 15 % |

> ⚠️ **Das Feld gilt nur für den GANZEN TAG, nie für ein einzelnes Lebensmittel.** Butter ist 100 % Fett, ein Apfel 95 % Kohlenhydrate — beides völlig normal. Ein Verhältnis-Urteil über eine einzelne Zutat wäre genau die Wertung, die §0 verbietet. Bei leerem Teller sagt das Feld das ausdrücklich.

**Mechanik der Überdehnung:** Die drei Anteile summieren sich **zwingend zu 100 %**. Wird einer zu groß, sind die anderen *automatisch* zu klein — das ist keine Design-Entscheidung, sondern Arithmetik.

- **Kästchen-Breite** = Soll-Anteil (`flex: 32 / 53 / 15`)
- **Balken darunter** = Ist-Anteile, hintereinander gelegt
- **Vertikale Ticks** bei 32 % und 85 % markieren die Soll-Grenzen
- Ist ein Segment über seinem Max → **rot schraffiert** und es **schiebt sich sichtbar über den Tick** in die Nachbarzone
- Kästchen: 🔴 über Max · 🟡 unter Min · 🟢 im Bereich
- Der Hinweistext benennt, **wem** der Platz weggenommen wird

**Kalibriert an drei Tagen:**

| Tag | 🧈 | 🌾 | 💪 |
|---|---:|---:|---:|
| Guter Tag | 36 % 🔴 | 51 % 🟢 | 13 % 🟢 |
| Zucker-Tag | 36 % 🔴 | 56 % 🔴 | 8 % 🟡 |
| Käse-Tag | **58 % 🔴** | 26 % 🟡 | 16 % 🔴 |

Der Käse-Tag zeigt die Mechanik am deutlichsten: Fett bei 58 % schiebt sich fast bis in die Eiweiß-Zone.

### 5.8 🍬 Zucker-Warnung

Über 45 g (WHO): das **ganze Feld** wird rot, Text weiß, plus Bezifferung *„🔴 9 g über dem Limit (45 g)"*.


---

## 6. Layout — Tagesplaner (v3)

```
┌──────────────────────────────────────────────────────────────────────────┐
│  🍽️ Tagesplaner        11 Sachen · 1204 kcal · 54 g Zucker      7 Jahre  │
├───────────────┬──────────────────────────────┬───────────────────────────┤
│ ☑ ✕ Nutzlose  │        🥕 Karotte (mittel)   │  ┌──────────┬──────────┐ │
│   ausgrauen   │        80 g · 33 kcal        │  │🥕 Dieses │🍽️ Ganzer │ │
│ ☑ ⚖️ Verhältnis│                              │  │  Essen   │ Tag (11) │ │
│               │                              │  └──────────┴──────────┘ │
│               │                              │  ┌─────────────────────┐  │
│               │                              │  │ 🥧 Dein Tag      🔍 │  │
│ ┌───────────┐ │                              │  │ 12 von 20 voll      │  │
│ │ BROT      │ │             ╱╲               │  │      ╭─────╮        │  │
│ │ 🍞 Toast +│ │            ╱  ╲              │  │    ╱ Torte  ╲       │  │
│ │ 🍪 Cookie+│ │           │ ▓▓ │  ← Tropfen  │  │   │ 20 Seg.  │      │  │
│ │ OBST      │ │            ╲__╱   Fläche =   │  │    ╲       ╱        │  │
│ │ 🥝 Kiwi−1+│ │                   Gewicht    │  │      ╰─────╯        │  │
│ │ 🍎 Apfel +│ │      Ⓐ 👁️ Augen 👍           │  │ 👆 große Ansicht    │  │
│ │ GEMÜSE    │ │      Ⓚ 🩹 Wunden 👍          │  └─────────────────────┘  │
│ │ 🥕 Karotte│ │                              │  ┌─────────┬─────────┐   │
│ │ ✕🥒 Gurke │ │  ┌────────────────────────┐  │  │ Energie │ 🍬Zucker│   │
│ │ …         │ │  │ 💡 Wusstest du?    🔄  │  │  │1204 kcal│ 54 g 🔴 │   │
│ │ GERICHTE  │ │  │   🥕 → 🔥 → 😀         │  │  └─────────┴─────────┘   │
│ │ 🥬 Spinat │ │  │ Gekochte Karotten …    │  │  ┌─────────────────────┐  │
│ └───────────┘ │  └────────────────────────┘  │  │ ⚖️ 🧈 : 🌾 : 💪      │  │
│               │                              │  │ ┌──┬─────────┬──┐    │  │
│               │                              │  │ │36│   51    │13│    │  │
│               │                              │  │ └──┴─────────┴──┘    │  │
│               │                              │  │ ███▓░░░░░░░░░░░░░    │  │
│               │                              │  │   ▲          ▲       │  │
│               │                              │  └─────────────────────┘  │
│               │                              │  ┌─────────────────────┐  │
│               │                              │  │ 🍽️ Heute gegessen   │  │
│    250px      │           flex               │  └─────────────────────┘  │
└───────────────┴──────────────────────────────┴───────────────────────────┘
                                                          320px
```

### 6.1 🔀 Umschalter: Dieses Essen ⇄ Ganzer Tag

Segmented Control über dem Chart. Er steuert **Chart, Zähler, Verhältnis-Feld und Modal** gemeinsam.

| | 🥕 **Dieses Essen** | 🍽️ **Ganzer Tag** |
|---|---|---|
| **Chart** | nur das betrachtete Lebensmittel | die Summe des Tellers |
| **Mitte des Kreises** | *"Dieses Essen"* | *"Tagesabdeckung"* |
| **Untertitel** | *"füllt 3 von 20 Nährstoffen ganz"* | *"12 von 20 Nährstoffen voll"* |
| **Energie-Zähler** | + Anteil: *"13 % deines Tages"* | Balken + Rot-Warnung über 1800 kcal |
| **Zucker-Zähler** | rot **nur**, wenn ein einzelnes Ding allein das Tages-Limit reißt | rot + Bezifferung der Überschreitung |
| **⚖️ Verhältnis** | **beschrieben, NICHT bewertet** — keine roten Kästchen | bewertet (🔴 / 🟡 / 🟢) |
| **Modal-Lücken** | *"Deckt allein den ganzen Tag bei: …"* | *"Fehlt noch fast ganz: …"* |

**Startzustand:** `Dieses Essen`. Beim **ersten +** springt der Umschalter **automatisch** auf `Ganzer Tag` — danach hat der Nutzer die Kontrolle (`manuell`-Flag).

> ⚠️ **Der entscheidende Punkt: im Modus „Dieses Essen" wird das Verhältnis NIE bewertet.**
> Butter ist 100 % Fett. Eine Kartoffel 90 % Kohlenhydrate. Ein Lachs 46 % Eiweiß. Alles davon ist **völlig normal** — rote Kästchen an einer einzelnen Zutat wären genau die „gut/böse"-Wertung aus §0.
> Stattdessen sagt das Feld beschreibend: *„Dieses Lebensmittel ist vor allem 🧈 Fett (100 %). Einzelne Lebensmittel sind fast immer einseitig — **bewertet wird nur der ganze Tag**."*
>
> Der Zucker-Zähler darf im Einzel-Modus rot werden, aber nur bei einer echten Ansage: **eine ganze Tafel Schokolade (55 g) reißt allein das Tages-Limit von 45 g.** Das ist eine Tatsache, keine Wertung.

Der frühere automatische „👁️ Vorschau"-Badge ist damit **abgelöst** — das Verhalten ist jetzt explizit und steuerbar.


### Verhalten

| | |
|---|---|
| **🔍 Suche** | Suchfeld über dem Picker: filtert live nach Name **und** Regal, Umlaut-tolerant („kase" findet „Käse"). ✕ oder `Esc` leert; Treffer-los zeigt einen freundlichen Hinweis |
| **+ / −** | addiert/entfernt; Mehrfach möglich (`×2`, `×3`) |
| **Klick auf Name/Emoji** | inspiziert das Lebensmittel in der Mitte (fügt **nicht** hinzu) |
| **🔀 Umschalter** | `🥕 Dieses Essen` ⇄ `🍽️ Ganzer Tag` — siehe §6.1 |
| **Klick in „Heute gegessen"** | wählt das Item an (Tropfen in der Mitte) und schaltet automatisch auf die **Einzelansicht**; das angewählte Item bekommt eine grüne Umrandung |
| **🚫 Ignorieren** | Toggle-Button pro „Heute gegessen"-Item: schließt es aus der **gesamten Tagesberechnung** aus (Chart, Zähler, Verhältnis, Stand-Zeile zeigt `n× 🚫`), es bleibt aber halbtransparent auf dem Teller. Nochmal klicken zählt es wieder mit; ✕ räumt den Ignoriert-Status mit auf |
| **Nach dem ersten +** | springt automatisch auf `Ganzer Tag`, danach manuell steuerbar |
| **Hover auf Segment** | Popup: Funktion + **Top 3**, was es füllen würde. Wenn ≥ 100 %: *"✅ Schon gedeckt"*. Das Popup klappt an den Rändern um und wird **immer vollständig im Anzeigebereich** gehalten (auch bei aktivem 🔎-Zoom) |
| **Klick auf Segment** | pinnt das Popup fest und **umrandet das angeklickte Stück in seiner Segmentfarbe** (20 % abgedunkelt, kein Fokus-Rechteck). Lösen: nochmal klicken, woanders klicken oder `Esc` |
| **🔍 Lupe im Chart** | **Nur** der Lupen-Button (oben rechts in der Chartbox) öffnet das Modal mit der großen Ansicht (Pfeile, Labels, Funktionen); Hover übers Chart zeigt weiterhin die Segment-Popups. `Esc` / `✕` schließt |
| **⭐ Wichtige Mikros hervorheben** | Toggle im ⚙️-Dialog: hebt **Kalium, Vitamin D, Eisen, Calcium** im Kreis-Chart hervor — schraffierter (diagonaler) Hintergrund in Segmentfarbe, ⭐ vor dem Label (große Ansicht) und ein Kurz-Hinweis im Hover-Popup (z. B. „Kalium: Gegenspieler zu Salz"). Persistiert |
| **☑ ✕ Nutzlose ausgrauen** | markiert Lebensmittel, die zu **keinem** Nährstoff unter 100 % noch ≥ 2 % beitragen |
| **☑ ⚖️ Verhältnis anzeigen** | blendet das Fett:KH:Eiweiß-Feld ein/aus |
| **🌍 Sprache** | Sprachwahl im ⚙️-Dialog. **Standard: Deutsch** (fest im HTML verankert, läuft ohne Fetch). 13 weitere Sprachen aus externem [`i18n.json`](i18n.json): Bairisch, Mainzerisch, Hessisch, English, Español, Français, Italiano, Svenska, Norsk, Suomi, עברית (Hebräisch, RTL), ייִדיש (Jiddisch, RTL), Sindarin. Übersetzt sind die **UI-Chrome** (Titel, Buttons, Zähler, Einstellungen, Chart-Mitte, Fußzeile via `data-i18n` / `-ph` / `-html`) **und die feste Datenschicht**: die **12 Regal-Namen** (`_shelves`), alle **20 Segment-Labels + Funktionstexte** (`_seg`, wirken in Torte, Tooltip, Verhältnis-Feld, Lücken-Listen), die Verhältnis-Hinweise, „Wusstest du?"-Kopfzeilen und Makro-Profilnamen. Platzhalter-Templates (`{n}` etc.) via `TF()`. Fehlende Strings fallen sauber auf Deutsch zurück. Persistiert. *Hinweis: Dialekte, Hebräisch/Jiddisch, Finnisch und das (spielerische) Sindarin sind ungeprüfte Best-Effort-Übersetzungen.* |
| **🍎 Lebensmittel-Namen & Fakten übersetzen (KI)** | Da **127 Namen + ~158 „Wusstest du?"-Fakten** nicht sinnvoll für 13 Sprachen von Hand pflegbar sind, übersetzt sie ein optionaler **BYOK-KI-Lauf**: Bei einer aktiven Nicht-Deutsch-Sprache erscheint im ⚙️-Dialog (unter der Sprachwahl) ein **🌍-Button**; mit hinterlegtem Anthropic-Key übersetzt er alle Lebensmittel **in Batches** (`claude-opus-4-8`, Structured Output, HTML-Tags & Emojis bleiben erhalten) und legt das Ergebnis pro Sprache in `localStorage` (`foodT_<lang>`) ab. `nameOf(f)` / `factsOf(f)` ziehen es dann überall (Picker, Titel, Teller-Liste, Tooltip-Vorschläge, „Wusstest du?"). **⬇︎** lädt es als `foods.<lang>.json` herunter (zum Einchecken ins Repo), **🗑** verwirft es. Ohne Übersetzung bleiben Namen/Fakten deutsch. |
| **⚙️ Einstellungen** | Zahnrad-Button in der Top-Leiste öffnet einen Dialog mit allen Optionen (Nutzlose ausgrauen, Verhältnis, Fruchtzucker, Hintergrund-Deckkraft). ✕ / `Esc` / Klick daneben schließt. **Alle Einstellungen überleben einen Reload** (`localStorage`, Schlüssel `einstellungen`) |
| **⤢ Vollbild** | Button in der Top-Leiste schaltet echtes Browser-Vollbild an/aus (Fullscreen API); Icon wechselt ⤢/⤡, `Esc` verlässt wie gewohnt |
| **🎚 🖼️ Hintergrund** | Schieberegler (im ⚙️-Dialog) für die Deckkraft der Hintergrund-Textur (0–100 %, Standard 50 %) |
| **🔎 Zoom** | +/−-Stepper im ⚙️-Dialog: CSS-Zoom der ganzen Oberfläche in 10-%-Stufen, **60–160 %** (Standard 100 %), persistiert. Dialoge und Popups sind zoom-korrigiert auf **max. 95 % der sichtbaren Breite** begrenzt (`--zf`-Faktor rechnet vw/vh zurück) |
| **🤖 KI-Anbieter (BYOK, Multi-Provider)** | Im ⚙️-Dialog wählbar: **Anthropic·Claude**, **OpenAI·GPT**, **Google·Gemini**, **xAI·Grok** oder **Custom (OpenAI-kompatibel)**. Pro Anbieter werden **Key, Modell und Endpoint** getrennt in `localStorage` gespeichert (`EINST.provider` / `.keys[p]` / `.models[p]` / `.endpoints[p]`; alter Einzel-`apiKey` wird zu `keys.anthropic` migriert). Modell- und Endpoint-Feld sind editierbar (Platzhalter = Default des Anbieters; z. B. `claude-opus-4-8`, `gpt-4o`, `gemini-2.0-flash`, `grok-2-latest`); bei *Custom* sind beide Pflicht. Ein Hilfe-Link führt zur Key-Seite des Anbieters (Anthropic Console, `platform.openai.com`, `aistudio.google.com/api-keys`, `console.x.ai`). **Ein `kiCall()` spricht drei Dialekte:** Anthropic `POST /v1/messages` (Structured Output, `anthropic-dangerous-direct-browser-access`), OpenAI-kompatibel `POST /chat/completions` (`Authorization: Bearer`, `response_format: json_object` + Schema im System-Prompt) und Gemini `…/models/{model}:generateContent?key=` (`responseMimeType: application/json`). Antworten werden robust geparst (Markdown-Zäune/Prosa werden entfernt). Nutzung: „🤖 … nachschlagen" (unbekannte Lebensmittel → Objekt mit hergeleiteten `a`-Anteilen, 🤖-Suffix, Fakt, in `eigeneFoods` gespeichert) **und** die Lebensmittel-Übersetzung. **Hinweis:** direkter Browser-Call ⇒ der Anbieter muss **CORS** erlauben (Anthropic & Gemini tun das zuverlässig; OpenAI/Grok evtl. nicht — dann Custom-Endpoint/Proxy). |
| **🔑 Key testen** | Button unter dem Key-Feld: minimaler Aufruf beim **gewählten Anbieter** (`max_tokens 1`, „hi"). Zeigt inline die **volle, ungekürzte** Antwort: grünes „funktioniert" bei `200`, sonst der echte API-Fehlertext (z. B. „credit balance too low", `insufficient_quota`), bei Guthaben-/Kontingent-Fehlern zusätzlich ein **Billing-Hinweis** mit Link zum Anbieter-Konto (API-Guthaben ≠ Chat-Abos wie Claude Pro / ChatGPT Plus). Meldung verschwindet beim Ändern von Key/Modell/Endpoint |
| **☑ 🔎 KI-Zeile in der Suche** | Sub-Toggle unter dem API-Key (**Standard: an**, persistiert als `kiSuchRow`). Wenn **aktiv gesucht** wird **und** ein Key hinterlegt ist, hängt der Picker eine „🤖 „…" mit KI nachschlagen"-Zeile ans **Ende der Trefferliste** — also auch dann, wenn es bereits Treffer gibt, nicht nur im Leer-Fall. Ohne Suche keine Zeile; Toggle aus ⇒ keine KI-Zeile (auch der Leer-Fall zeigt dann keinen Button). Dieselbe `kiSuche()`-Logik wie der Leer-Fall-Button |
| **↔️ Spalten-Griffe** | Zwei schmale vertikale Griffe zwischen den drei Spalten (Desktop): **Ziehen** (Maus/Touch, Pointer Events) macht Picker bzw. rechte Spalte breiter/schmaler (CSS-Variablen `--c1`/`--c3`, per `clamp()` begrenzt), **Doppelklick** setzt zurück, Breiten überleben Reload (`localStorage`). Im gestapelten Portrait-Layout ausgeblendet |
| **☐ 🍎 Fruchtzucker wie Zucker** | **Standard: aus.** Zucker aus ganzem Obst (Regal 🍎) zählt dann als normale Kohlenhydrate — WHO-Logik „freie Zucker": intrinsischer Fruchtzucker fällt nicht unters 45-g-Limit, *Trauben werden nicht bestraft*. **Eingeschaltet** zählt Obst-Zucker überall wie einfacher Zucker: 🍬-Zähler & Warnung, Teller-Liste, Tropfen-Treemap (pinker Block statt braunem KH-Block), Hover-Tipps und Modal. Säfte, Smoothies & Trockenobst gelten immer als Zucker (stehen nicht im Obst-Regal). |
| **Zähler** | Energie + Zucker mit Balken. Zucker warnt gestuft: **ab 90 %** des Limits wird der Balken rot · **ab 100 %** wird das ganze Feld rot, bekommt eine rote Umrandung und **blinkt/faded** (ease-in-out, 1 s) + Bezifferung der Überschreitung · **ab 200 %** blinkt es schnell (0,4 s). Bei `prefers-reduced-motion` kein Blinken |
| **🥗 Makro-Verteilung** | Auswahl im ⚙️-Dialog, welches **Soll** das Verhältnis-Feld nutzt: **DGE Standard** (53·15·32), **Sportlich/Athlet** (50·22·28), **Low-Carb** (20·35·45), **Ketogen** (5·22·73) oder **„— ausblenden —"** (blendet das Feld aus, ersetzt die frühere „Verhältnis anzeigen"-Checkbox). Das Profil steht auch als „Soll: …" in der Feld-Überschrift. Persistiert; ausführlich in [`MAKRONAEHRSTOFFE.md`](MAKRONAEHRSTOFFE.md) |
| **⚖️ Verhältnis** | **Tagesansicht:** Drei Kästchen (Breite = Soll-Anteil des gewählten Makro-Profils). Der Ist-Balken **überdehnt sichtbar** in die Nachbarzone, wenn ein Makro zu viel wird. Bei leerem Teller: Hinweis, dass das Verhältnis nur für den **ganzen Tag** gilt. **Einzelansicht:** andere Darstellung ohne Soll — Kästchenbreite = **echter Energie-Anteil dieses Lebensmittels**, gestapelter Balken ohne Soll-Zonen/Ticks, keine Soll-Spannen, keine Bewertung (Anteile < 0,5 % werden ausgeblendet) |

### Screen-Maße

- Tablet-Rahmen `1160px`, Screen `790px` hoch, `overflow:hidden`
- Grid `250px | 1fr | 320px`, gap `12px`
- **Alle Screens per Playwright auf Overflow und Clipping geprüft** — kein Element ragt heraus

---

## 7. Inventar

### 🍞 Brot & Gebäck (14)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🍞 Weißtoast (klein) | 25 g | 66 | 0.8 g | 120 mg | ⚡ |
| 🍞 Weißtoast (groß) | 40 g | 105 | 1.3 g | 190 mg | ⚡ |
| 🍪 Amerikaner | 70 g | 250 | 22.0 g | 210 mg | ⚡ |
| 🍪 Cookie (groß, Bäckerei) | 80 g | 380 | 28.0 g | 250 mg | ⚡ 🧈 |
| 🍩 Donut | 60 g | 250 | 14.0 g | 190 mg | ⚡ 🧈 |
| 🥨 Brezel mit Salz | 80 g | 240 | 1.5 g | 1150 mg | 💪 ⚡ |
| 🍞 Scheibe Roggenbrot | 45 g | 100 | 1.0 g | 240 mg | 🛡️ 🌾 ⚡ |
| 🍞 Scheibe Sauerteigbrot | 45 g | 105 | 0.8 g | 250 mg | 🌾 ⚡ |
| 🍞 Scheibe Vollkornbrot | 45 g | 95 | 1.2 g | 235 mg | 🛡️ 🌾 |
| 🥐 Croissant | 60 g | 245 | 5.0 g | 320 mg | 💪 🧈 |
| 🥨 Käsebrezel | 100 g | 330 | 2.0 g | 1250 mg | 💪 ⚡ |
| 🥨 Pfefferbrezel | 80 g | 245 | 1.5 g | 1100 mg | 💪 ⚡ |
| 🥐 Schokocroissant | 70 g | 287 | 10.5 g | 280 mg | 🧈 |
| 🥐 Laugencroissant | 65 g | 247 | 2.6 g | 585 mg | 💪 🧈 |

### 🥣 Frühstück (6)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🥛 Becher Joghurt (natur) | 150 g | 100 | 7.0 g | 60 mg | 💪 🧈 |
| 🥣 Schoko-Müsli mit Kuhmilch | 200 g | 300 | 16.0 g | 93 mg | 🛡️ 💪 |
| 🥣 Schoko-Müsli mit Hafermilch | 200 g | 271 | 14.0 g | 85 mg | 🛡️ 💪 🌾 |
| 🍚 Portion Couscous (gekocht) | 200 g | 224 | 0.2 g | 10 mg | 💪 🌾 ⚡ |
| 🥞 Pfannkuchen (1 Stück) | 90 g | 195 | 3.0 g | 150 mg | 💪 🧈 |
| 🍚 Portion Milchreis | 200 g | 260 | 18.0 g | 70 mg | 💪 ⚡ |

### 🍎 Obst (21)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🍏 Apfel grün (klein) | 100 g | 52 | 10.0 g | 1 mg | ⚡ |
| 🍏 Apfel grün (groß) | 180 g | 94 | 18.0 g | 2 mg | 🛡️ ⚡ |
| 🍎 Apfel rot (klein) | 100 g | 54 | 11.0 g | 1 mg | ⚡ |
| 🍎 Apfel rot (groß) | 180 g | 97 | 20.0 g | 2 mg | 🛡️ ⚡ |
| 🍐 Birne | 150 g | 87 | 15.0 g | 2 mg | 🛡️ ⚡ |
| 🍌 Banane | 120 g | 107 | 15.0 g | 1 mg | 🛡️ 🌾 ⚡ |
| 🫐 Pflaume | 60 g | 28 | 6.0 g | 0 mg | ⚡ |
| 🍊 Orange | 150 g | 70 | 12.0 g | 0 mg | 🛡️ ⚡ |
| 🍑 Aprikose | 40 g | 19 | 3.7 g | 0 mg | ⚡ |
| 🍇 Trauben (eine Handvoll) | 100 g | 69 | 16.0 g | 2 mg | ⚡ |
| 🥝 Kiwi | 75 g | 46 | 7.0 g | 2 mg | 🛡️ ⚡ |
| 🫐 Handvoll Heidelbeeren | 80 g | 46 | 8.0 g | 1 mg | ⚡ |
| 🫐 Handvoll Himbeeren | 80 g | 42 | 3.5 g | 1 mg | 🛡️ ⚡ |
| 🫐 Handvoll Brombeeren | 80 g | 34 | 3.9 g | 1 mg | 🛡️ ⚡ |
| 🍒 Handvoll Johannisbeeren (rot) | 80 g | 45 | 5.9 g | 1 mg | 🛡️ ⚡ |
| 🍓 Handvoll Erdbeeren | 100 g | 32 | 5.0 g | 1 mg | 🛡️ ⚡ |
| 🍒 Handvoll Kirschen | 100 g | 63 | 12.8 g | 1 mg | ⚡ |
| 🍉 Stück Wassermelone | 150 g | 45 | 9.0 g | 2 mg | ⚡ |
| 🍎 Portion Apfelmus | 100 g | 72 | 16.0 g | 2 mg | ⚡ |
| 🍑 Nektarine | 140 g | 62 | 11.9 g | 0 mg | ⚡ |
| 🐉 Drachenfrucht (halb) | 150 g | 90 | 12.0 g | 1 mg | 🛡️ ⚡ |

### 🥬 Gemüse (19)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🍅 Halbe Tomate | 60 g | 11 | 1.6 g | 3 mg | 💧 |
| 🥬 Salatblatt | 5 g | 1 | 0.1 g | 1 mg | 💧 |
| 🥒 5 Scheiben Gurke | 30 g | 4 | 0.5 g | 1 mg | 💧 |
| 🥕 Karotte roh (klein) | 50 g | 21 | 2.4 g | 35 mg | 🌾 ⚡ |
| 🥕 Karotte roh (mittel) | 80 g | 33 | 3.8 g | 55 mg | 🌾 ⚡ |
| 🥕 Karotte roh (groß) | 120 g | 49 | 5.6 g | 83 mg | 🛡️ 🌾 ⚡ |
| 🫑 Mini-Paprika (rot) | 30 g | 9 | 1.3 g | 1 mg | 🛡️ 💧 |
| 🫑 Mini-Paprika (orange) | 30 g | 9 | 1.3 g | 1 mg | 🛡️ 💧 |
| 🫑 Mini-Paprika (grün) | 30 g | 6 | 0.7 g | 1 mg | 🛡️ 💧 |
| 🥦 Handvoll Brokkoli (gekocht) | 80 g | 28 | 1.1 g | 33 mg | 🛡️ 💪 |
| 🥬 Blattspinat (gedünstet) | 150 g | 35 | 0.6 g | 105 mg | 🛡️ 💪 |
| 🥔 Gekochte Kartoffel (klein) | 70 g | 61 | 0.6 g | 4 mg | ⚡ |
| 🥔 Gekochte Kartoffel (mittel) | 120 g | 104 | 1.1 g | 6 mg | ⚡ |
| 🥔 Gekochte Kartoffel (groß) | 180 g | 157 | 1.6 g | 9 mg | 🛡️ ⚡ |
| 🍄 Champignons (gebraten) | 80 g | 30 | 0.8 g | 5 mg | 💪 🧈 |
| 🥔 Portion Kartoffelbrei | 180 g | 160 | 2.0 g | 280 mg | ⚡ |
| 🌽 Portion Mais | 80 g | 70 | 5.0 g | 8 mg | ⚡ |
| 🟢 Portion Erbsen | 80 g | 66 | 4.0 g | 4 mg | 🛡️ 💪 ⚡ |
| 🥒 Mini-Gurke (1 Stück) | 100 g | 12 | 1.7 g | 2 mg | 💧 |

### 🧀 Milch & Käse (7)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🧀 Scheibe Cheddar | 20 g | 83 | 0.1 g | 130 mg | 💪 🧈 |
| 🧀 Scheibe Gouda | 20 g | 71 | 0.5 g | 165 mg | 💪 🧈 |
| 🧀 Gouda (dicke Scheibe) | 30 g | 107 | 0.8 g | 248 mg | 💪 🧈 |
| 🧀 Babybel (1 Stück) | 20 g | 66 | 0.0 g | 130 mg | 💪 🧈 |
| 🧀 Portion Frischkäse | 30 g | 75 | 1.0 g | 130 mg | 🧈 |
| 🧀 Scheibe Butterkäse | 20 g | 68 | 0.1 g | 130 mg | 💪 🧈 |
| 🧀 Geriebener Käse (Portion) | 25 g | 100 | 0.5 g | 400 mg | 💪 🧈 |

### 🍔 Fleisch & Fast Food (10)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🍟 Pommes (klein) | 80 g | 230 | 0.3 g | 180 mg | 🛡️ ⚡ 🧈 |
| 🍔 Cheeseburger | 114 g | 302 | 7.0 g | 700 mg | 💪 🧈 |
| 🥚 Ein gekochtes Ei | 55 g | 78 | 0.2 g | 70 mg | 💪 🧈 |
| 🐟 Fischstäbchen (1 Stück) | 30 g | 62 | 0.3 g | 135 mg | 💪 🧈 |
| 🐟 Stück Lachs (gebraten) | 80 g | 165 | 0.0 g | 50 mg | 💪 🧈 |
| 🍳 Rührei (2 Eier) | 110 g | 190 | 0.8 g | 180 mg | 💪 🧈 |
| 🐟 Thunfisch (halbe Dose) | 50 g | 58 | 0.0 g | 180 mg | 💪 |
| 🍗 Chicken Nuggets (5 Stück) | 90 g | 250 | 0.5 g | 480 mg | 💪 🧈 |
| 🌭 Wiener Würstchen (1 Stück) | 50 g | 145 | 0.3 g | 620 mg | 💪 🧈 |
| 🥓 Scheibe Salami (klein) | 10 g | 40 | 0.1 g | 195 mg | 💪 🧈 |

### 🍫 Süßes (12)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🐻 Gummibärchen (1 Stück) | 2.2 g | 8 | 1.2 g | 1 mg | ⚡ |
| 🍮 Pudding (Becher, Schoko) | 125 g | 130 | 17.0 g | 60 mg | ⚡ |
| 🍫 Schokokuss | 20 g | 85 | 11.0 g | 12 mg | ⚡ 🧈 |
| 🍫 Rippchen Schokolade | 8 g | 43 | 4.4 g | 6 mg | ⚡ 🧈 |
| 🍫 Ganze Tafel Schokolade | 100 g | 535 | 55.0 g | 75 mg | 💪 ⚡ 🧈 |
| 🍓 Portion Marmelade | 20 g | 55 | 13.0 g | 3 mg | ⚡ |
| 🍬 Kaubonbon (1 Stück) | 6 g | 24 | 4.2 g | 0 mg | ⚡ |
| 🍫 Nussnougatcreme (1 EL) | 20 g | 108 | 11.0 g | 8 mg | ⚡ 🧈 |
| 🍯 Honig (1 TL) | 12 g | 36 | 9.5 g | 0 mg | ⚡ |
| 🍿 Portion Popcorn | 25 g | 95 | 0.3 g | 130 mg | 🛡️ ⚡ |
| 🥨 Handvoll Salzstangen | 25 g | 95 | 1.0 g | 430 mg | ⚡ |
| 🍬 Bonbon (1 Stück) | 5 g | 20 | 4.7 g | 0 mg | ⚡ |

### 🧈 Fette & Würze (3)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🧈 Butter (1 Messerspitze) | 5 g | 37 | 0.0 g | 5 mg | 🧈 |
| 🥫 Ketchup (1 EL) | 15 g | 17 | 3.5 g | 160 mg | ⚡ |
| 🧂 Prise Jodsalz | 1 g | 0 | 0.0 g | 390 mg | 🥬 |

### 🧃 Getränke (9)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🧃 Glas Orangensaft | 200 g | 90 | 18.0 g | 2 mg | 🛡️ ⚡ |
| 🧃 Capri-Sonne (1 Tüte) | 200 g | 78 | 19.0 g | 20 mg | ⚡ |
| 💧 Glas Wasser | 200 g | 0 | 0.0 g | 10 mg | 💧 |
| 🧃 Glas Apfelsaft | 200 g | 92 | 21.0 g | 4 mg | ⚡ |
| 🧃 Glas Multivitaminsaft | 200 g | 110 | 24.0 g | 5 mg | 🛡️ ⚡ |
| 🧃 Glas Traubensaft | 200 g | 134 | 32.0 g | 6 mg | ⚡ |
| 🥛 Glas Milch | 200 g | 128 | 9.6 g | 90 mg | 💪 🧈 |
| 🍫 Glas Kakao | 200 g | 165 | 21.0 g | 95 mg | 💪 ⚡ |
| 🧃 Glas Eistee | 200 g | 56 | 14.0 g | 4 mg | ⚡ |

### 🍦 Eis & Kuchen (6)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🍨 Kugel Milcheis | 55 g | 110 | 11.0 g | 40 mg | ⚡ 🧈 |
| 🍧 Kugel Fruchteis (Sorbet) | 55 g | 70 | 16.0 g | 0 mg | ⚡ |
| 🍦 Waffeltüte (leer) | 12 g | 47 | 1.5 g | 25 mg | ⚡ |
| 🍭 Fruchteis am Stiel | 70 g | 65 | 15.5 g | 0 mg | ⚡ |
| 🍰 Stück Kuchen (Marmor) | 80 g | 330 | 22.0 g | 210 mg | ⚡ 🧈 |
| 🥧 Stück Apfelstrudel | 120 g | 275 | 21.0 g | 140 mg | ⚡ 🧈 |

### 🍽️ Fertige Teller (4)

| | Portion | kcal | Zucker | Natrium | Rollen |
|---|---:|---:|---:|---:|---|
| 🍝 Teller Rotelline (Ricotta + Spinat) | 220 g | 400 | 3.0 g | 620 mg | 🛡️ 💪 |
| 🍝 Teller Spaghetti mit Tomatensoße | 250 g | 380 | 8.0 g | 480 mg | 🛡️ 💪 🌾 ⚡ |
| 🍕 Stück Pizza Margherita | 120 g | 290 | 3.0 g | 640 mg | 💪 |
| 🍕 Mini-Pizza (1 Stück) | 60 g | 145 | 1.5 g | 320 mg | 💪 |

### 🥪 Gerichte (16)

| | Portion | kcal | Natrium | Aus |
|---|---:|---:|---:|---|
| 🥪 Grilled Cheese Sandwich | 190 g | 476 | 651 mg | 2x Weißtoast + 4x Butter + 1x Gouda + 1x Halbe Tomate |
| 🥪 Tomaten-Sandwich | 195 g | 347 | 793 mg | 2x Weißtoast + 1x Ketchup + 1x Gouda + 1x Halbe Tomate + 2x Salatblatt |
| 🍟 Happy Meal | 394 g | 610 | 900 mg | 1x Cheeseburger + 1x Pommes + 1x Capri-Sonne |
| 🐟 7 Fischstäbchen mit Gemüse | 385 g | 512 | 1193 mg | 7x Fischstäbchen + 1x Handvoll Brokkoli + 1x Karotte roh + 1x Ketchup |
| 🍚 Couscous mit Pfannengemüse | 460 g | 383 | 111 mg | 1x Portion Couscous + 2x Mini-Paprika + 1x Mini-Paprika + 1x Karotte roh + 1x Handvoll Brokkoli + 2x Butter |
| 🥬 Spinat mit Ei | 330 g | 254 | 186 mg | 1x Blattspinat + 1x Ein gekochtes Ei + 1x Gekochte Kartoffel + 1x Butter |
| 🍞 Butterbrot mit Marmelade | 70 g | 234 | 203 mg | 1x Weißtoast + 2x Butter + 1x Portion Marmelade |
| 🍦 Waffeltüte mit 1 Kugel | 67 g | 157 | 65 mg | 1x Waffeltüte + 1x Kugel Milcheis |
| 🍦 Waffeltüte mit 3 Kugeln | 177 g | 337 | 105 mg | 1x Waffeltüte + 2x Kugel Milcheis + 1x Kugel Fruchteis |
| 🍞 Brot mit Nussnougatcreme | 60 g | 213 | 198 mg | 1x Weißtoast + 1x Nussnougatcreme |
| 🍳 Rührei mit Vollkornbrot | 205 g | 417 | 655 mg | 1x Rührei + 2x Scheibe Vollkornbrot + 1x Butter |
| 🥪 Vollkornbrot mit Frischkäse & Gurke | 150 g | 269 | 601 mg | 2x Scheibe Vollkornbrot + 1x Portion Frischkäse + 1x 5 Scheiben Gurke |
| 🍗 Nuggets mit Pommes | 185 g | 497 | 820 mg | 1x Chicken Nuggets + 1x Pommes + 1x Ketchup |
| 🥪 Vollkornbrot mit Butterkäse & Salami | 195 g | 295 | 952 mg | 1x Scheibe Vollkornbrot + 1x Scheibe Butterkäse + 3x Scheibe Salami + 1x Mini-Gurke |
| 🍝 Spaghetti mit Käse & Tomatensoße | 275 g | 480 | 880 mg | 1x Teller Spaghetti mit Tomatensoße + 1x Geriebener Käse |
| 🍕 Ganze Pizza (klein) | 480 g | 1160 | 2560 mg | 4x Stück Pizza Margherita |

---

## 8. Zentrale Befunde

| | |
|---|---|
| ⚖️ **Fett ist klein und schwer** | Im 328-g-Joghurt: **3,3 % des Gewichts, 48 % der Energie.** 1 g Fett = 9 kcal, 1 g Zucker = 4. |
| 💧 **Energiedichte ist eine Frage des Wassers** | 100 g Trauben → **22 g Rosinen**. Gleiche Nährstoffe, gleiche kcal, ⅕ des Gewichts. Dichte springt 70 → 314 kcal/100 g. |
| 🍪 **Cookie vs. Apfel** | Cookie 80 g → 380 kcal. Apfel 180 g → 97 kcal. *Doppeltes Gewicht, ein Viertel der Energie.* |
| 🧃 **Traubensaft schlägt Capri-Sonne** | **10,7 Würfelzucker** gegen 6,3 — und nur 3 % Vitamin C. |
| 🧃 **Apfelsaft hat kein Vitamin C** | 6 % des Tages. Beim Pressen bleibt alles im Trester. |
| 🥝 **Kiwi ist die Königin** | 108 % Vitamin C bei 2,3 Würfeln Zucker. Beste Bilanz im Spiel. |
| 🍇 **Trauben enttäuschen** | 6 % Vitamin C, 0,9 g Ballaststoffe (Apfel: 4 g). |
| 🫐 **Himbeeren sind Ballaststoff-König** | 5,2 g in einer Hand — mehr als ein ganzer Apfel. |
| 🫑 **Mini-Paprika ist die Überraschung** | 30 g, **9 kcal**, **65 % Vitamin C**. |
| 🥕 **Karotten sind der Augen-Star** | Eine mittlere = **95 % Vitamin A**. |
| 🍞 **Brot ist die größte Salzquelle** | Nicht Chips. Grilled Cheese: 651 mg — das meiste aus dem Brot. |
| 🥨 **Laugengebäck ist die Salzfalle** | Brezel 1150 mg, Käsebrezel **1250 mg**, ganze Pizza **2560 mg = 142 % der Obergrenze**. Salz kommt aus Teig *und* Lauge. |
| 🐉 **Exotisch ≠ besser** | Die Drachenfrucht sieht spektakulär aus, hat aber **weniger Vitamin C als ein Apfel**. Die Kiwi schlägt sie um das Zwölffache. Ihre echte Stärke: 4,5 g Ballaststoffe. |
| 🍬 **Bonbon vs. Schokolade** | Ein Bonbon (20 kcal) wird **10 Minuten gelutscht** → das Säure-Fenster bleibt offen. Ein Stück Schokolade ist in 30 Sekunden weg — und damit **besser für die Zähne**, obwohl mehr Zucker drin ist. Für die Zähne zählt die **Dauer**, nicht die Menge. |
| 🍞 **Sauerteig knackt das Phytat** | Die Bakterien im Teig bauen den Blocker ab → aus Sauerteigbrot kommt **mehr Eisen an** als aus Vollkornbrot, obwohl weniger drin ist. |
| 🦋☀️ **Zwei Lücken bleiben ehrlich** | **Jod** (Fisch/Jodsalz) und **Vitamin D** (Sonne) sind über den Teller kaum zu decken. Das darf das Spiel sagen. |

### 🏆 Kennzahl: Vitamin C pro Würfelzucker

```
🥝 Kiwi              46   ████████████████████████████
🍊 Orange            31   ██████████████████
🧃 Orangensaft       21   ████████████
🧃 Multivitaminsaft  11   ██████
🍎 Apfel              4   ██
🍇 Trauben          1,2
🧃 Apfelsaft        0,9
🧃 Traubensaft      0,3
🧃 Capri-Sonne        0
```

Hängt direkt an §3.3: **nur Kiwi, Orange, Orangensaft, Multivitaminsaft, Paprika und Brokkoli öffnen die Eisen-Tür.**

### 🧂 Kennzahl: Natrium (Obergrenze 1800 mg)

```
🍕 Ganze Pizza (klein)      2560 mg   142 %  🔴 ÜBER DER OBERGRENZE
🥨 Käsebrezel               1250 mg    69 %  ███████████
🐟 7 Fischstäbchen          1193 mg    66 %  ███████████
🥨 Brezel mit Salz          1150 mg    64 %  ██████████
🥨 Pfefferbrezel            1100 mg    61 %  ██████████
🥪 Vollkorn + Käse + Salami  952 mg    53 %  ████████
🍟 Happy Meal                900 mg    50 %  ████████
```

> **Natrium ist der einzige Nährstoff mit einer Obergrenze** (§1.3) — und der einzige Bereich, in dem ein Kind aus normalem Essen realistisch *drüber* kommt (§4). Das Laugengebäck-Regal ist der Grund: Brezeln kriegen ihr Salz **doppelt** — aus dem Teig **und** aus dem Laugenbad.

---

## 9. Gebaute Artefakte

Alle **self-contained HTML**, Dateinamen mit Timestamp `_YYYY-MM-DD_HHMM`.

| Datei | Inhalt |
|---|---|
| **`energie_tag_planer`** | **v3 — der Tagesplaner.** +/−, Vorschau, Hover-Top-3, ✕-Markierung, klickbares Chart → Modal |
| `energie_tag_wasserregler` | v2 — Wasser-Regler über alle Lebensmittel, Mikro-Badges, Wusstest-du |
| `energie_tag_torte` | Tortendiagramm (guter Tag / Zucker-Tag) + Übermaß-Mechaniken |
| `energie_tag_saft` | Saft-Regal, Obst-Regal, Rangliste Vitamin C pro Würfelzucker |
| `energie_tag_form` | Tropfen ⇄ Kasten-Morph mit Slider |
| `energie_tag_flaechen` | Flächen-Treemap, maßstabsgetreu |
| `energie_tag_farbsystem` | Farbsystem, Masse vs. Energie |
| `energie_tag_gerichte` | Rezept-Baukasten, zwei Eisen-Türen |
| `energie_tag_naehrstoffe` | Eisen-Trichter, Steckbriefe, Muskel-Labor |
| `energie_tag_review` | Tagesreview + Einstellungen (Detailtiefe) |
| `energie_tag_screens` / `_tablet` | Handy- und Tablet-Screens |
| `lebensmittel.json` | Die Datenbank |

---

## 10. Offen

1. **🍬 Zucker trennen:** Das WHO-Limit (45 g) gilt für *zugesetzten/freien* Zucker, **nicht** für den in ganzen Früchten. Aktuell zählt das Modell alles zusammen → ein Tag aus Müsli, Obst und Gemüse reißt das Limit. **So bestraft das Spiel den Apfel.** → `zucker_frei` vs. `zucker_intrinsisch` einbauen.
2. **⏱️ Uhr-Mechanik:** A (Hunger im Unterricht senkt Konzentration — *empfohlen*) oder B (Hunger-Uhr pausiert in Pausen)?
3. **🍇 Rosinen / Trockenobst** als eigene Zutaten? (Der Wasser-Regler legt es nahe.)
4. ~~8 Einträge ohne "Wusstest du?"~~ ✅ **erledigt — alle 112 haben jetzt einen.**
5. **"Hand Gewichte"** — Verschreiber? Ich habe auf Verdacht 🍓 **Erdbeeren** und 🍒 **Kirschen** ergänzt. Falls Hanteln gemeint waren: dann Sport-Mechanik statt Regal.
6. **Nächster großer Schritt:** spielbarer React-Prototyp (React + Vite + Zustand) mit dieser DB, Sättigungsformel, Eisen-Trichter, laufender Uhr.
