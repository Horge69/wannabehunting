# Uuden tuotteen lisäysohje — WannabeHunting

Kopioi alla olevat pätkät ja korvaa kaikki `TUOTE_ID`, `TUOTE_AVAIN`, `KOMPONENTTI_ID` ja `TUOTE_NIMI` oikeilla arvoilla.

---

## 1. Mistä saat tarvittavat tiedot?

| Tieto | Mistä? |
|---|---|
| **Shopify Product ID** (numero) | Shopify Admin → Products → klikkaa tuotetta → URL:ssa viimeinen numero |
| **Buy Button embed-koodi** | Shopify Admin → Sales Channels → Buy Button → Create Button |
| **KOMPONENTTI_ID** | Kopioi Buy Button -koodista: `id='product-component-XXXXXXXXX'` |
| **TUOTE_AVAIN** | Keksi lyhyt englanninkielinen tunniste, esim. `lippalakki`, `takki` |
| **TUOTE_NIMI** | Näkyy kortissa ja modaalissa — hae Shopifysta tai kirjoita itse |

---

## 2. HTML — Tuotekortti (lisää product-grid:iin)

```html
<div class="product-card has-modal" id="TUOTE_AVAIN-card" onclick="openTUOTE_AVAINModal()">
  <div class="product-info">
    <h3 id="TUOTE_AVAIN-title">TUOTE_NIMI</h3>
    <div class="shopify-buy-btn-wrap" onclick="event.stopPropagation()">
      <div id='product-component-KOMPONENTTI_ID'></div>
    </div>
  </div>
</div>
```

---

## 3. HTML — Modaali (lisää muiden modaalien perään)

```html
<div class="pmodal-overlay" id="TUOTE_AVAINModal" onclick="closePModalOnBg(event)">
  <div class="pmodal" role="dialog" aria-modal="true" aria-label="TUOTE_NIMI">
    <button class="pmodal-close" onclick="closeTUOTE_AVAINModal()" aria-label="Sulje">✕</button>
    <div class="pmodal-body">

      <!-- Vasen: Shopify Buy Button -->
      <div class="pmodal-shopify-main">
        <div id='product-component-modal-TUOTE_AVAIN' style="width:100%;"></div>
        <button class="pmodal-zoom-btn" onclick="openLightboxFromModal('product-component-modal-TUOTE_AVAIN')">🔍 Suurenna kuva</button>
      </div>

      <!-- Oikea: Tuotetiedot -->
      <div class="pmodal-info">
        <span class="pmodal-badge">Printful EU</span>
        <h2 id="TUOTE_AVAIN-modal-title">TUOTE_NIMI</h2>
        <p class="pmodal-desc" id="TUOTE_AVAIN-modal-desc"></p>

        <div>
          <div class="pmodal-section-title">Size guide</div>
          <table class="size-table">
            <thead><tr><th>Koko</th><th>Rintaympärys (cm)</th><th>Pituus (cm)</th></tr></thead>
            <tbody>
              <tr><td>S</td><td>86–91</td><td>70</td></tr>
              <tr><td>M</td><td>91–96</td><td>72</td></tr>
              <tr><td>L</td><td>99–104</td><td>74</td></tr>
              <tr><td>XL</td><td>107–112</td><td>76</td></tr>
              <tr><td>2XL</td><td>117–122</td><td>79</td></tr>
            </tbody>
          </table>
        </div>
      </div>

    </div>
  </div>
</div>
```

---

## 4. JavaScript — Modal-funktiot (lisää muiden modal-funktioiden perään)

```javascript
function openTUOTE_AVAINModal() {
  document.getElementById('TUOTE_AVAINModal').classList.add('open');
  document.body.style.overflow = 'hidden';
}
function closeTUOTE_AVAINModal() {
  document.getElementById('TUOTE_AVAINModal').classList.remove('open');
  document.body.style.overflow = '';
}
```

Lisää myös ESC-sulkuun (etsi `closeProductModal()` sisältävä rivi):
```javascript
// Lisää rivin loppuun:
closeTUOTE_AVAINModal();
```

---

## 5. JavaScript — Storefront API -haku (lisää muiden fetchProductData-kutsujen perään)

```javascript
fetchProductData('gid://shopify/Product/TUOTE_ID', 'TUOTE_AVAIN');
```

Lisää myös `fetchProductData`-funktion sisälle uusi `else if` -haara:

```javascript
} else if (key === 'TUOTE_AVAIN') {
  var ct = document.getElementById('TUOTE_AVAIN-title'); if (ct) ct.textContent = title;
  var mt = document.getElementById('TUOTE_AVAIN-modal-title'); if (mt) mt.textContent = title;
  var md = document.getElementById('TUOTE_AVAIN-modal-desc'); if (md) md.textContent = desc;
}
```

---

## 6. JavaScript — Shopify Buy Button -skripti (kortti + modaali)

Liitä Shopify Administa saatu Buy Button -skripti ja muokkaa `"product"`-osion `contents` ja `styles` seuraaviksi:

**Kortti:**
```javascript
"styles": {
  "product": { "@media (min-width: 601px)": { "max-width": "calc(25% - 20px)", "margin-left": "20px", "margin-bottom": "50px" } },
  "button": { ":hover": {"background-color":"#bf5817"}, "background-color":"#d4621a", ":focus": {"background-color":"#bf5817"} },
  "price": { "color": "#d4621a", "font-size": "1.15em", "font-weight": "700" }
},
"contents": { "img": true, "imgWithCarousel": false, "title": false, "price": true, "options": true, "button": true },
"text": { "button": "Lisää koriin" }
```

**Modaali:**
```javascript
"styles": {
  "product": { "max-width": "100%", "margin": "0" },
  "button": { ":hover":{"background-color":"#bf5817"}, "background-color":"#d4621a", ":focus":{"background-color":"#bf5817"}, "width":"100%", "margin-top":"0.8rem" },
  "price": { "color": "#d4621a", "font-size": "1.2em", "font-weight": "700" }
},
"contents": { "img": false, "imgWithCarousel": true, "title": false, "price": true, "options": true, "button": true },
"text": { "button": "Lisää koriin" }
```

**Cart ja toggle (kaikissa skripteissä sama):**
```javascript
"cart": {
  "styles": { "button": { ":hover":{"background-color":"#bf5817"}, "background-color":"#d4621a", ":focus":{"background-color":"#bf5817"} } },
  "text": { "total": "Yhteensä", "button": "Kassalle" }
},
"toggle": { "styles": { "toggle": { "background-color":"#d4621a", ":hover":{"background-color":"#bf5817"}, ":focus":{"background-color":"#bf5817"} } } }
```

---

## 7. Git — Muutosten tallennus

```bash
cd ~/git/wannabehunting
git add index.html
git commit -m "Lisää uusi tuote: TUOTE_NIMI"
git push
```

---

## Nopea tarkistuslista

- [ ] Kortti lisätty product-grid:iin
- [ ] Modaali lisätty HTML:ään
- [ ] Modal-funktiot lisätty JS:ään
- [ ] ESC-sulku päivitetty
- [ ] fetchProductData-kutsu lisätty
- [ ] else if -haara fetchProductData-funktioon
- [ ] Buy Button -skripti (kortti + modaali) liitetty oikeilla styles/contents-arvoilla
- [ ] `git push` ajettu
