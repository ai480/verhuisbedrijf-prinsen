# Verhuisbedrijf Prinsen Website Design

A fast, utilitarian website for a Dutch moving company.

## Goals

- Primary action: request a quote via form
- Secondary: display phone number for direct calls
- Aesthetic: McMaster-Carr inspired — functional, minimal, fast
- Performance: under 14kb compressed, instant loading

## Pages

| Page | URL | Purpose |
|------|-----|---------|
| Home | `/` | Hero, quote form, quick intro |
| Diensten | `/diensten` | Services offered |
| Over ons | `/over-ons` | Company background |
| Contact | `/contact` | Full contact details + form |

## Navigation

```
[Logo: Verhuisbedrijf Prinsen]    Home | Diensten | Over ons | Contact    [Tel: 06-XXXXXXXX]
```

Simple horizontal bar, present on all pages. Phone number visible at all times.

## Visual Style

**Colors:**
- Background: `#fff` (white)
- Text: `#333` (dark gray)
- Accent: `#e65c00` (orange) for buttons and highlights
- Links: `#0066cc` (classic blue), underlined

**Typography:**
```css
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
```
- Base size: 16-18px
- Line height: 1.5-1.6
- Headings: bold, no fancy fonts

**Layout:**
- Max width: ~800px, centered
- Generous whitespace
- No decorative images or icons

## Quote Form Fields

1. Naam (text, required)
2. E-mail (email, required)
3. Telefoon (tel, required)
4. Type dienst (dropdown: Verhuizing / Opslag / Verhuizing + Opslag / Anders)
5. Verhuisdatum (date)
6. Van adres (text)
7. Naar adres (text)
8. Omschrijving (textarea — "Vertel ons wat u wilt verhuizen of opslaan")
9. Submit button: "Verstuur aanvraag"

## Page Content

### Homepage

```
[Navigation]

VERHUISBEDRIJF PRINSEN
Uw verhuizing in vertrouwde handen

[Quote form - compact]

Liever bellen? 06-XXXXXXXX

---

Waarom Prinsen?
- [3-4 bullet points]

Onze diensten
- [Brief overview with link to /diensten]
```

### Diensten

- Verhuizingen (particulier, zakelijk)
- Opslag (tijdelijk, langdurig)
- CTA button to request quote

### Over ons

- Company history/background
- What makes them different
- Service area (if relevant)
- CTA button to request quote

### Contact

- Full address
- Phone number
- Email
- KvK number
- Full quote form repeated

## Technical Implementation

### File Structure

```
/
├── index.html
├── diensten.html
├── over-ons.html
├── contact.html
├── style.css
└── favicon.ico
```

### Form Handling

Web3Forms (free):
- POST to their endpoint
- Submissions emailed directly
- Honeypot spam protection
- No backend required

### Hosting

Cloudflare Pages:
- Free tier
- Global CDN
- Connect via GitHub
- Automatic deploys

### Performance Targets

- Total page size: <20kb uncompressed (<5kb compressed)
- No JavaScript required
- Works with JS disabled
- First paint: <100ms

### Browser Support

Progressive enhancement — works everywhere, looks best in modern browsers.

## What We're NOT Including

- Stock photos
- Social media buttons
- Cookie banners
- Analytics scripts
- Chat widgets
- JavaScript frameworks
- Testimonial carousels
