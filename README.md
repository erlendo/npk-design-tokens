# npk-design-tokens

Delt kilde til sannhet for de rå merkevare-verdiene (farge, hjørne-form, typografi)
som brukes av Norsk Pointerklubs to digitale flater:

- **pointerdatabasen.pointer.no** (`/Users/erlendo/Pointerdatabasen`) — datasøk/admin-verktøy, Next.js + Tailwind, egen fullere semantisk token-utvidelse i `src/app/globals.css` (surface-muted, dark mode, osv.)
- **pointervercsan.vercel.app** (`/Users/erlendo/pointervercsan`) — klubbens markedsføringsside, Astro + Tailwind, tokens i `src/styles/global.css`

## ⚠️ Dette repoet er OFFENTLIG — aldri legg hemmeligheter her

Gjort offentlig 2026-08-14 for at Pointerdatabasens `npm install` skal kunne
klone dette repoet direkte fra Vercels byggmiljø (Vercel har ikke SSH-tilgang
til andre private repoer enn det som faktisk deployes — bekreftet ved en
mislykket test med `git@github.com: Permission denied (publickey)` da repoet
var privat). Siden det er offentlig, gjelder følgende **uten unntak**:

- Aldri commit API-nøkler, tokens, `.env`-filer, Supabase/Sanity-hemmeligheter,
  interne URL-er med tilgangsnøkler, eller annen sensitiv informasjon her.
- Dette repoet skal KUN inneholde rå, ikke-sensitive design-primitiver
  (fargekoder, radius-verdier, font-navn) — akkurat slik det er i dag.
- Hvis noe sensitivt noensinne havner her ved en feil: fjern det med en gang,
  rotér eventuelle eksponerte nøkler (en fjernet commit er fortsatt synlig i
  git-historikken til noen har rewritet den), og vurder om repoet må roteres
  helt (nytt repo, gammelt slettet) i stedet for å stole på historikk-opprydding.

## Hvorfor dette repoet finnes

Se ADR-001 (i Pointerdatabasen sin samtalehistorikk/CODEX.md, 2026-08-14): fargepalett, hjørne-radius
og typografi ble tidligere kopiert manuelt mellom sidene ved å lese av beregnede
nettleser-stiler. Dette hadde allerede ført til intern drift i pointervercsan selv
(en ubrukt `tailwind.css`-fargeskala som ikke stemte med den faktisk aktive
`src/styles/global.css`). Dette repoet er den lettvekts løsningen: **kun rå
tokens (farge/radius/type), ingen komponenter**, som begge sider kan
sammenligne seg mot.

## Hva som IKKE er her

- Ingen React/Astro-komponenter. De to sidene har ulike rammeverk, ulike
  brukergrupper og ulike interaksjonsmønstre — et fullt komponentbibliotek
  ble vurdert og avvist som overkill for to eiendommer (se ADR).
- Ingen semantiske utvidelser (surface-info, badge-varianter, mørk modus-logikk).
  De hører hjemme i hver enkelt sides egen `globals.css`, siden Pointerdatabasen
  har behov (mørk modus, tett admin-UI) som pointervercsan ikke har.

## Konsumering

**Pointerdatabasen** har en reell npm git-avhengighet mot dette repoet:
`"npk-design-tokens": "github:erlendo/npk-design-tokens#v0.1.0"` i
`package.json`, og `@import "npk-design-tokens/tokens.css";` øverst i
`src/app/globals.css`. Dette FORUTSETTER at repoet er offentlig - en
git-avhengighet mot et privat repo feiler i Vercels byggmiljø med
`Permission denied (publickey)` siden Vercel ikke autoriserer SSH mot andre
repoer enn det som deployes (bekreftet med en reell testdeploy 2026-08-14,
se commit-historikk). Ikke gjør repoet privat igjen uten samtidig å bytte
konsumeringsmetode (f.eks. et faktisk npm-pakkeregister eller en
token-basert HTTPS-løsning i et preinstall-script).

**pointervercsan** har foreløpig IKKE en tilsvarende npm-avhengighet - den
konsumerer fortsatt via manuell kopiering:

1. Oppdater `tokens.css`/`tokens.json` i dette repoet, commit med en beskrivende melding, og tagg en ny versjon (`git tag vX.Y.Z && git push origin vX.Y.Z`) hvis Pointerdatabasen skal peke på den nye verdien.
2. Kopier de endrede verdiene manuelt inn i pointervercsan sin `src/styles/global.css` (`:root`-blokken).
3. Bump versjonstaggen i Pointerdatabasens `package.json` og kjør `npm install` der, slik at begge sider peker på samme faktiske innhold.
4. Referer til commit-SHA/tag fra dette repoet i commit-meldingen på den konsumerende siden, slik at det er sporbart hvilken token-versjon som er i bruk.

## Tokens

| Token | Verdi | Bruk |
|---|---|---|
| `--npk-blue` | `#233b63` | Primærfarge (CTA-knapper, lenker, aktive filtre) |
| `--npk-blue-hover` | `#1e3356` | Hover/aktiv-tilstand for primærfarge |
| `--npk-green` | `#445b45` | Sekundær aksentfarge |
| `--npk-offwhite` | `#f7f5f1` | Sidebakgrunn (lys modus) |
| `--npk-soft` | `#e5e3de` | Myk kant/skille-farge |
| `--npk-copper` | `#b46a3c` | Aksentfarge |
| `--npk-text` | `#1f2937` | Primær brødtekst |
| `--npk-radius` | `0px` | **Alt** skal ha skarpe 90°-hjørner — se historikk under |
| `--npk-font-body` | Inter / IBM Plex Sans | Brødtekst og de aller fleste overskrifter |
| `--npk-font-display` | Libre Baskerville (serif) | KUN pointervercsan sin forside-hero-H1 — ikke et systemomfattende display-font |

## Hjørne-historikk (viktig, ikke reversér uten å lese dette)

`--npk-radius: 0px` gjelder **uten unntak**, inkludert CTA-knapper. Dette ble
avklart 2026-08-14 etter en feilaktig antakelse: pointervercsan sin
`.npk-btn`-systemklasse er selv definert med `border-radius: 0`, men
header-CTA-en ("Bli medlem" i `BaseLayout.astro`) hadde en frittstående
`rounded-full`-Tailwind-klasse limt på i markup — en isolert kodefeil, ikke et
bevisst "pille-CTA"-designspråk. Alle andre CTA-knapper på siden
(`index.astro` sin hero, "Se alle resultater", "Se alle valper") bruker allerede
ren `npk-btn` uten avrunding. Pointerdatabasen hadde et unntak for CTA-knapper
(`.solid-button-shell`) bygget på den feilaktige antakelsen — dette er nå
fjernet i begge repoer, se commit-historikk i hver side rundt 2026-08-14.

Eneste legitime unntak fra `--npk-radius: 0px`: genuint sirkulære elementer
(avatarer, statuspunkter, switch-brytere) som er sirkler av funksjon, ikke
"avrundede rektangler".
