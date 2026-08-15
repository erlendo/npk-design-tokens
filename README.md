# npk-design-tokens

Delt kilde til sannhet for de rå merkevare-verdiene (farge, hjørne-form, typografi)
som brukes av Norsk Pointerklubbs to digitale flater:

- **pointerdatabasen.pointer.no** (`/Users/erlendo/Pointerdatabasen`) — datasøk/admin-verktøy, Next.js + Tailwind, egen fullere semantisk token-utvidelse i `src/app/globals.css` (surface-muted, dark mode, osv.)
- **pointervercsan.vercel.app** (`/Users/erlendo/pointervercsan`) — klubbens markedsføringsside, Astro + Tailwind, tokens i `src/styles/global.css`

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

## Konsumering (v1 — ingen pakkeregister satt opp ennå)

Ingen automatisk `npm install`-kobling foreløpig. Når verdier her endres:

1. Oppdater `tokens.css`/`tokens.json` i dette repoet, commit med en beskrivende melding.
2. Kopier de endrede verdiene manuelt inn i:
   - Pointerdatabasen: `src/app/globals.css` (`:root`/`.dark`-blokkene)
   - pointervercsan: `src/styles/global.css` (`:root`-blokken)
3. Referer til commit-SHA fra dette repoet i commit-meldingen på den konsumerende siden, slik at det er sporbart hvilken token-versjon som er i bruk.

Fremtidig forbedring (se ADR action items): pakke dette som en reell
avhengighet (npm/git-dependency) slik at steg 2 blir automatisk ved
`npm install` + versjonsbump, i stedet for manuell kopiering. Ikke gjort ennå
fordi det krever et valg om pakkeregister/git-dependency-autentisering i
Vercel-bygg som ikke er tatt.

## Tokens

| Token | Verdi | Bruk |
|---|---|---|
| `--npk-blue` | `#233b63` | Primærfarge (CTA-knapper, lenker, aktive filtre) |
| `--npk-blue-hover` | `#1a2c4a` | Hover/aktiv-tilstand for primærfarge |
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
