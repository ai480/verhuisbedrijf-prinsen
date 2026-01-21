# Verhuisbedrijf Prinsen Website Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a fast, minimal 4-page static website for a Dutch moving company with a quote request form.

**Architecture:** Plain HTML/CSS with no build step. Form submissions handled by Web3Forms (external service). Static files served from Cloudflare Pages.

**Tech Stack:** HTML5, CSS3, Web3Forms (form handling)

---

## Task 1: Project Setup & Base CSS

**Files:**
- Create: `style.css`

**Step 1: Create the stylesheet with reset and base styles**

```css
/* Reset & Base */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  font-size: 18px;
  line-height: 1.6;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
  color: #333;
  background: #fff;
}

a {
  color: #0066cc;
}

a:hover {
  color: #004499;
}

/* Layout */
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 0 1rem;
}

/* Header */
.header {
  border-bottom: 2px solid #333;
  padding: 1rem 0;
}

.header-inner {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
}

.logo {
  font-weight: bold;
  font-size: 1.2rem;
  color: #333;
  text-decoration: none;
}

.nav {
  display: flex;
  gap: 1.5rem;
}

.nav a {
  text-decoration: none;
  color: #333;
}

.nav a:hover {
  text-decoration: underline;
}

.phone {
  font-weight: bold;
  color: #e65c00;
  text-decoration: none;
}

.phone:hover {
  text-decoration: underline;
}

/* Main */
main {
  padding: 2rem 0;
}

h1 {
  font-size: 1.8rem;
  margin-bottom: 0.5rem;
}

h2 {
  font-size: 1.4rem;
  margin: 2rem 0 1rem;
  border-bottom: 1px solid #ccc;
  padding-bottom: 0.3rem;
}

h3 {
  font-size: 1.1rem;
  margin: 1.5rem 0 0.5rem;
}

p {
  margin-bottom: 1rem;
}

ul, ol {
  margin: 0 0 1rem 1.5rem;
}

li {
  margin-bottom: 0.3rem;
}

/* Hero */
.hero {
  text-align: center;
  padding: 2rem 0;
  border-bottom: 1px solid #ccc;
  margin-bottom: 2rem;
}

.tagline {
  font-size: 1.1rem;
  color: #666;
}

/* Form */
.form-section {
  background: #f5f5f5;
  padding: 1.5rem;
  margin: 2rem 0;
}

.form-grid {
  display: grid;
  gap: 1rem;
}

@media (min-width: 500px) {
  .form-grid {
    grid-template-columns: 1fr 1fr;
  }
  .form-grid .full-width {
    grid-column: 1 / -1;
  }
}

label {
  display: block;
  font-weight: bold;
  margin-bottom: 0.3rem;
  font-size: 0.9rem;
}

input, select, textarea {
  width: 100%;
  padding: 0.5rem;
  font-size: 1rem;
  font-family: inherit;
  border: 1px solid #ccc;
  background: #fff;
}

input:focus, select:focus, textarea:focus {
  outline: 2px solid #e65c00;
  outline-offset: 1px;
}

textarea {
  min-height: 100px;
  resize: vertical;
}

button {
  background: #e65c00;
  color: #fff;
  border: none;
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  background: #cc5200;
}

/* Call CTA */
.call-cta {
  text-align: center;
  margin: 1.5rem 0;
  font-size: 1.1rem;
}

/* Footer */
.footer {
  border-top: 2px solid #333;
  padding: 1.5rem 0;
  margin-top: 2rem;
  font-size: 0.9rem;
  color: #666;
}

/* Utility */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}
```

**Step 2: Commit**

```bash
git init
git add style.css
git commit -m "feat: add base stylesheet"
```

---

## Task 2: Homepage

**Files:**
- Create: `index.html`

**Step 1: Create the homepage with header, hero, form, and footer**

```html
<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Verhuisbedrijf Prinsen - Uw verhuizing in vertrouwde handen</title>
  <meta name="description" content="Verhuisbedrijf Prinsen verzorgt uw verhuizing of opslag. Vraag vrijblijvend een offerte aan.">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header class="header">
    <div class="container header-inner">
      <a href="/" class="logo">Verhuisbedrijf Prinsen</a>
      <nav class="nav">
        <a href="/">Home</a>
        <a href="/diensten.html">Diensten</a>
        <a href="/over-ons.html">Over ons</a>
        <a href="/contact.html">Contact</a>
      </nav>
      <a href="tel:+31612345678" class="phone">06-12345678</a>
    </div>
  </header>

  <main class="container">
    <div class="hero">
      <h1>Verhuisbedrijf Prinsen</h1>
      <p class="tagline">Uw verhuizing in vertrouwde handen</p>
    </div>

    <section class="form-section">
      <h2>Offerte aanvragen</h2>
      <form action="https://api.web3forms.com/submit" method="POST">
        <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY_HERE">
        <input type="hidden" name="subject" value="Nieuwe offerte aanvraag - Verhuisbedrijf Prinsen">
        <input type="checkbox" name="botcheck" class="sr-only" style="display:none">

        <div class="form-grid">
          <div>
            <label for="naam">Naam *</label>
            <input type="text" id="naam" name="naam" required>
          </div>
          <div>
            <label for="email">E-mail *</label>
            <input type="email" id="email" name="email" required>
          </div>
          <div>
            <label for="telefoon">Telefoon *</label>
            <input type="tel" id="telefoon" name="telefoon" required>
          </div>
          <div>
            <label for="dienst">Type dienst</label>
            <select id="dienst" name="dienst">
              <option value="verhuizing">Verhuizing</option>
              <option value="opslag">Opslag</option>
              <option value="verhuizing-opslag">Verhuizing + Opslag</option>
              <option value="anders">Anders</option>
            </select>
          </div>
          <div>
            <label for="datum">Gewenste datum</label>
            <input type="date" id="datum" name="datum">
          </div>
          <div>
            <label for="van">Van adres</label>
            <input type="text" id="van" name="van_adres">
          </div>
          <div>
            <label for="naar">Naar adres</label>
            <input type="text" id="naar" name="naar_adres">
          </div>
          <div class="full-width">
            <label for="omschrijving">Omschrijving</label>
            <textarea id="omschrijving" name="omschrijving" placeholder="Vertel ons wat u wilt verhuizen of opslaan..."></textarea>
          </div>
          <div class="full-width">
            <button type="submit">Verstuur aanvraag</button>
          </div>
        </div>
      </form>
    </section>

    <p class="call-cta">Liever bellen? <a href="tel:+31612345678" class="phone">06-12345678</a></p>

    <section>
      <h2>Waarom Verhuisbedrijf Prinsen?</h2>
      <ul>
        <li>Betrouwbare en ervaren verhuizers</li>
        <li>Zorgvuldige omgang met uw spullen</li>
        <li>Flexibele planning</li>
        <li>Eerlijke prijzen zonder verrassingen</li>
      </ul>
    </section>

    <section>
      <h2>Onze diensten</h2>
      <p>Wij verzorgen particuliere en zakelijke verhuizingen, en bieden opslagmogelijkheden voor kort of lang.</p>
      <p><a href="/diensten.html">Bekijk al onze diensten →</a></p>
    </section>
  </main>

  <footer class="footer">
    <div class="container">
      <p>Verhuisbedrijf Prinsen · Adresstraat 123, 1234 AB Plaats · <a href="tel:+31612345678">06-12345678</a> · KvK: 12345678</p>
    </div>
  </footer>
</body>
</html>
```

**Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add homepage with quote form"
```

---

## Task 3: Services Page

**Files:**
- Create: `diensten.html`

**Step 1: Create the services page**

```html
<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Diensten - Verhuisbedrijf Prinsen</title>
  <meta name="description" content="Verhuizingen en opslag door Verhuisbedrijf Prinsen. Particulier en zakelijk.">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header class="header">
    <div class="container header-inner">
      <a href="/" class="logo">Verhuisbedrijf Prinsen</a>
      <nav class="nav">
        <a href="/">Home</a>
        <a href="/diensten.html">Diensten</a>
        <a href="/over-ons.html">Over ons</a>
        <a href="/contact.html">Contact</a>
      </nav>
      <a href="tel:+31612345678" class="phone">06-12345678</a>
    </div>
  </header>

  <main class="container">
    <h1>Onze diensten</h1>

    <section>
      <h2>Verhuizingen</h2>

      <h3>Particuliere verhuizingen</h3>
      <p>Of u nu een appartement of een heel huis verhuist, wij zorgen dat uw spullen veilig op de nieuwe locatie aankomen. Wij nemen het zware werk uit handen zodat u zich kunt richten op uw nieuwe begin.</p>

      <h3>Zakelijke verhuizingen</h3>
      <p>Kantoor verhuizen? Wij zorgen voor minimale downtime en een efficiënte verhuizing van uw bedrijfsinventaris, inclusief IT-apparatuur en archieven.</p>
    </section>

    <section>
      <h2>Opslag</h2>

      <h3>Tijdelijke opslag</h3>
      <p>Tussen twee woningen in? Of tijdelijk ruimte nodig tijdens een verbouwing? Wij bieden veilige opslagruimte voor korte periodes.</p>

      <h3>Langdurige opslag</h3>
      <p>Voor spullen die u (nog) niet kwijt wilt maar ook geen plek voor heeft. Onze opslagruimtes zijn droog, veilig en toegankelijk.</p>
    </section>

    <section>
      <h2>Offerte aanvragen</h2>
      <p>Wilt u weten wat wij voor u kunnen betekenen? Vraag vrijblijvend een offerte aan.</p>
      <p><a href="/">Naar het offerteformulier →</a></p>
    </section>
  </main>

  <footer class="footer">
    <div class="container">
      <p>Verhuisbedrijf Prinsen · Adresstraat 123, 1234 AB Plaats · <a href="tel:+31612345678">06-12345678</a> · KvK: 12345678</p>
    </div>
  </footer>
</body>
</html>
```

**Step 2: Commit**

```bash
git add diensten.html
git commit -m "feat: add services page"
```

---

## Task 4: About Page

**Files:**
- Create: `over-ons.html`

**Step 1: Create the about page**

```html
<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Over ons - Verhuisbedrijf Prinsen</title>
  <meta name="description" content="Leer Verhuisbedrijf Prinsen kennen. Wie we zijn en waar we voor staan.">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header class="header">
    <div class="container header-inner">
      <a href="/" class="logo">Verhuisbedrijf Prinsen</a>
      <nav class="nav">
        <a href="/">Home</a>
        <a href="/diensten.html">Diensten</a>
        <a href="/over-ons.html">Over ons</a>
        <a href="/contact.html">Contact</a>
      </nav>
      <a href="tel:+31612345678" class="phone">06-12345678</a>
    </div>
  </header>

  <main class="container">
    <h1>Over Verhuisbedrijf Prinsen</h1>

    <section>
      <h2>Wie wij zijn</h2>
      <p>Verhuisbedrijf Prinsen is een familiebedrijf dat al jaren verhuizingen verzorgt met zorg en aandacht. Wij geloven dat een verhuizing meer is dan dozen sjouwen — het is een belangrijk moment in uw leven, en dat behandelen wij ook zo.</p>
    </section>

    <section>
      <h2>Onze aanpak</h2>
      <p>Bij ons geen haastwerk of onzorgvuldigheid. Wij nemen de tijd om uw verhuizing goed te plannen en uit te voeren. Onze verhuizers zijn ervaren, vriendelijk en gaan zorgvuldig om met uw bezittingen.</p>
      <ul>
        <li>Persoonlijke benadering</li>
        <li>Duidelijke communicatie</li>
        <li>Geen verborgen kosten</li>
        <li>Flexibel in planning</li>
      </ul>
    </section>

    <section>
      <h2>Werkgebied</h2>
      <p>Wij zijn actief in de regio en daarbuiten. Neem contact op voor de mogelijkheden.</p>
    </section>

    <section>
      <h2>Offerte aanvragen</h2>
      <p>Benieuwd wat wij voor u kunnen doen?</p>
      <p><a href="/">Vraag een offerte aan →</a></p>
    </section>
  </main>

  <footer class="footer">
    <div class="container">
      <p>Verhuisbedrijf Prinsen · Adresstraat 123, 1234 AB Plaats · <a href="tel:+31612345678">06-12345678</a> · KvK: 12345678</p>
    </div>
  </footer>
</body>
</html>
```

**Step 2: Commit**

```bash
git add over-ons.html
git commit -m "feat: add about page"
```

---

## Task 5: Contact Page

**Files:**
- Create: `contact.html`

**Step 1: Create the contact page with full form**

```html
<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Contact - Verhuisbedrijf Prinsen</title>
  <meta name="description" content="Neem contact op met Verhuisbedrijf Prinsen of vraag een offerte aan.">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header class="header">
    <div class="container header-inner">
      <a href="/" class="logo">Verhuisbedrijf Prinsen</a>
      <nav class="nav">
        <a href="/">Home</a>
        <a href="/diensten.html">Diensten</a>
        <a href="/over-ons.html">Over ons</a>
        <a href="/contact.html">Contact</a>
      </nav>
      <a href="tel:+31612345678" class="phone">06-12345678</a>
    </div>
  </header>

  <main class="container">
    <h1>Contact</h1>

    <section>
      <h2>Contactgegevens</h2>
      <p>
        <strong>Verhuisbedrijf Prinsen</strong><br>
        Adresstraat 123<br>
        1234 AB Plaats
      </p>
      <p>
        Telefoon: <a href="tel:+31612345678">06-12345678</a><br>
        E-mail: <a href="mailto:info@verhuisbedrijfprinsen.nl">info@verhuisbedrijfprinsen.nl</a>
      </p>
      <p>KvK: 12345678</p>
    </section>

    <section class="form-section">
      <h2>Offerte aanvragen</h2>
      <form action="https://api.web3forms.com/submit" method="POST">
        <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY_HERE">
        <input type="hidden" name="subject" value="Nieuwe offerte aanvraag - Verhuisbedrijf Prinsen">
        <input type="checkbox" name="botcheck" class="sr-only" style="display:none">

        <div class="form-grid">
          <div>
            <label for="naam">Naam *</label>
            <input type="text" id="naam" name="naam" required>
          </div>
          <div>
            <label for="email">E-mail *</label>
            <input type="email" id="email" name="email" required>
          </div>
          <div>
            <label for="telefoon">Telefoon *</label>
            <input type="tel" id="telefoon" name="telefoon" required>
          </div>
          <div>
            <label for="dienst">Type dienst</label>
            <select id="dienst" name="dienst">
              <option value="verhuizing">Verhuizing</option>
              <option value="opslag">Opslag</option>
              <option value="verhuizing-opslag">Verhuizing + Opslag</option>
              <option value="anders">Anders</option>
            </select>
          </div>
          <div>
            <label for="datum">Gewenste datum</label>
            <input type="date" id="datum" name="datum">
          </div>
          <div>
            <label for="van">Van adres</label>
            <input type="text" id="van" name="van_adres">
          </div>
          <div>
            <label for="naar">Naar adres</label>
            <input type="text" id="naar" name="naar_adres">
          </div>
          <div class="full-width">
            <label for="omschrijving">Omschrijving</label>
            <textarea id="omschrijving" name="omschrijving" placeholder="Vertel ons wat u wilt verhuizen of opslaan..."></textarea>
          </div>
          <div class="full-width">
            <button type="submit">Verstuur aanvraag</button>
          </div>
        </div>
      </form>
    </section>
  </main>

  <footer class="footer">
    <div class="container">
      <p>Verhuisbedrijf Prinsen · Adresstraat 123, 1234 AB Plaats · <a href="tel:+31612345678">06-12345678</a> · KvK: 12345678</p>
    </div>
  </footer>
</body>
</html>
```

**Step 2: Commit**

```bash
git add contact.html
git commit -m "feat: add contact page"
```

---

## Task 6: Setup Web3Forms

**Step 1: Get Web3Forms access key**

1. Go to https://web3forms.com/
2. Enter the email address where you want to receive form submissions
3. Copy the access key they provide

**Step 2: Update access key in both forms**

Replace `YOUR_ACCESS_KEY_HERE` in:
- `index.html` (line with `access_key`)
- `contact.html` (line with `access_key`)

**Step 3: Commit**

```bash
git add index.html contact.html
git commit -m "chore: configure Web3Forms access key"
```

---

## Task 7: Add Favicon

**Files:**
- Create: `favicon.ico` (or `favicon.svg`)

**Step 1: Create a simple favicon**

Option A: Use a favicon generator like https://favicon.io/ with the letter "P" or a simple box icon.

Option B: Create a minimal SVG favicon:

Create `favicon.svg`:
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100">
  <rect width="100" height="100" fill="#e65c00"/>
  <text x="50" y="75" font-size="70" font-family="Arial" font-weight="bold" fill="white" text-anchor="middle">P</text>
</svg>
```

**Step 2: Add favicon link to all HTML files**

Add this line in the `<head>` section of all 4 HTML files, after the `<title>` tag:
```html
<link rel="icon" href="/favicon.svg" type="image/svg+xml">
```

**Step 3: Commit**

```bash
git add favicon.svg index.html diensten.html over-ons.html contact.html
git commit -m "feat: add favicon"
```

---

## Task 8: Final Review & Deploy Prep

**Step 1: Test locally**

Open `index.html` in browser and verify:
- All pages load correctly
- Navigation works between pages
- Form displays correctly
- Responsive on mobile (use browser dev tools)
- Phone links work (tel:)

**Step 2: Create .gitignore**

Create `.gitignore`:
```
.DS_Store
*.log
```

**Step 3: Final commit**

```bash
git add .gitignore
git commit -m "chore: add gitignore and prep for deploy"
```

---

## Task 9: Deploy to Cloudflare Pages

**Step 1: Push to GitHub**

1. Create new repository on GitHub: `verhuisbedrijf-prinsen`
2. Push local repo:

```bash
git remote add origin git@github.com:USERNAME/verhuisbedrijf-prinsen.git
git branch -M main
git push -u origin main
```

**Step 2: Connect to Cloudflare Pages**

1. Go to https://dash.cloudflare.com/
2. Pages → Create a project → Connect to Git
3. Select the repository
4. Build settings:
   - Framework preset: None
   - Build command: (leave empty)
   - Build output directory: `/` (root)
5. Deploy

**Step 3: Configure custom domain**

1. In Cloudflare Pages project → Custom domains
2. Add `verhuisbedrijfprinsen.nl`
3. Update DNS records as instructed

---

## Post-Deploy Checklist

- [ ] All 4 pages load on live URL
- [ ] Form submits and email arrives
- [ ] Phone links work on mobile
- [ ] HTTPS working
- [ ] Custom domain configured
- [ ] Test on mobile device
