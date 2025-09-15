---
# tom front matter så Jekyll modtager Liquid i denne side
---

# Tid til Troels V4.0

**Aktør:** Troels Trab  
**Titel:** Professionel mødeafholder
---

## Ugens indlæg

- 29/08-2025: [Ugen der gik](indlaeg/29-08.md)

---

## Formål
I dette semester fokuserer jeg på at skabe en stærk struktur til at dokumentere og konsolidere både:
- Min arbejdsproces i projekter
- Min sideløbende læringsproces, som skal understøtte arbejdet

---

## Fokusområder
- [IT-Projektledelse & Software Delivery](valgfag/Projektledelse12-09.md)
- [Backend-udvikling & API-Design](valgfag/Backend03-09.md)

- [Hvorfor har jeg valgt de emner?](indlaeg/HvorforEmner.md)
---

## Nøgleprincipper & refleksioner
- Opdater **Ugentligt**
- Reflekter **løbende**
- Skab en **gnidningsfri struktur** for arbejdsprocessen

Porteføljen skal:
- Konsolidere oplevelser og indsigter
- Skabe grobund for eftertanke og læring af fejl
- Eventuelt suppleres med videoindlæg for at styrke præsentationskompetencer
- Indeholde status på projekter/guilds, noter om hændelser, og næste skridt

---

## Mål
- At kunne vise og tale om, hvordan jeg har opnået læringsmålene for semestret
- At bruge refleksionerne som guldkorn for mit fremtidige jeg


## Arkiv

<div style="margin:.5rem 0 1rem">
  <input id="q" type="search" placeholder="Søg i titel/uddrag..." style="max-width:420px;width:100%;padding:.5rem;border:1px solid #ddd;border-radius:8px" />
</div>

<div id="tags" style="display:flex;gap:.5rem;flex-wrap:wrap;margin-bottom:1rem"></div>

<ul id="results" style="list-style:none;padding:0;display:flex;flex-direction:column;gap:1rem"></ul>

<noscript>
  <h3>Dagens indlæg (statisk fallback)</h3>
  <ul>
      <li><a href="indlaeg/15-08.md">15-08-2025-Proces</a></li>
      <li><a href="valgfag/BackendKlar.md">15-08-2025-API-Design</a></li>
      <li><a href="valgfag/ProjektledelseKlar.md">15-08-2025-Projektledelse</a></li>
      <li><a href="indlaeg/HvorforEmner.md">Emne valg og uddybelse</a></li>
  </ul>
</noscript>

<script>
(async function () {
  const res = await fetch('{{ "/search.json" | relative_url }}');
  const posts = (await res.json())
    .map(p => ({ ...p, dateObj: p.date ? new Date(p.date) : null }))
    .sort((a,b) => (b.dateObj?.getTime()||0) - (a.dateObj?.getTime()||0));

  // Unik tagliste (vil du kun vise udvalgte tags, erstat næste linje med en fast liste)
  const allTags = [...new Set(posts.flatMap(p => p.tags || []))].sort((a,b)=>a.localeCompare(b,'da'));
  const selected = new Set();

  const $tags = document.getElementById('tags');
  allTags.forEach(tag => {
    const b = document.createElement('button');
    b.textContent = tag; b.type = 'button';
    b.style.cssText = 'padding:.35rem .6rem;border:1px solid #ddd;border-radius:999px;background:#fff;cursor:pointer';
    b.onclick = () => {
      selected.has(tag) ? selected.delete(tag) : selected.add(tag);
      b.classList.toggle('active');
      b.style.background = b.classList.contains('active') ? '#111' : '#fff';
      b.style.color = b.classList.contains('active') ? '#fff' : '#000';
      b.style.borderColor = b.classList.contains('active') ? '#111' : '#ddd';
      render(); updateUrl();
    };
    $tags.appendChild(b);
  });

  const $q = document.getElementById('q');
  const $out = document.getElementById('results');

  // Deep link fra URL (fx ?tags=App-udvikling,Flutter&q=kalender)
  const params = new URLSearchParams(location.search);
  $q.value = params.get('q') || '';
  (params.get('tags') || '').split(',').filter(Boolean).forEach(t => {
    const btn = [...$tags.querySelectorAll('button')].find(b => b.textContent === t);
    if (btn && !btn.classList.contains('active')) btn.click();
  });
  $q.addEventListener('input', () => { render(); updateUrl(); });

  function updateUrl() {
    const p = new URLSearchParams();
    if ($q.value.trim()) p.set('q', $q.value.trim());
    if (selected.size) p.set('tags', [...selected].join(','));
    history.replaceState(null,'', location.pathname + (p.toString()?('?'+p.toString()):''));
  }

  function render() {
    const q = $q.value.trim().toLowerCase();
    const need = [...selected];

    const filtered = posts.filter(p => {
      const text = (p.title + ' ' + (p.excerpt || '')).toLowerCase();
      const qOk = !q || text.includes(q);
      const tags = p.tags || [];
      const tagsOk = need.every(t => tags.includes(t));
      return qOk && tagsOk;
    });

    $out.innerHTML = '';
    filtered.forEach(p => {
     const date = p.date ? new Date(p.date).toLocaleDateString('da-DK') : '';

// Lav små badges af tags (maks 3, juster efter behov), Har slettet .slice(0,3) for at den altid skal finde alle.
const pills = (p.tags || []).map(t => `
  <span class="tag-pill">${t}</span>
`).join('');

const li = document.createElement('li');
li.innerHTML = `
  <div class="post-row">
    <div class="title-line">
      <a href="${p.url}" class="post-title">${p.title}</a>
      ${date ? `<small class="post-date">- ${date}</small>` : ``}
    </div>
    <div class="tag-line">${pills}</div>
  </div>
`;
$out.appendChild(li);

    });

    if (!filtered.length) $out.innerHTML = '<li>Ingen indløg matchede din søgning/valgte tags.</li>';
  }

  render();
})();
</script>
<style>
.post-row{
  padding:.65rem 1rem;border:1px solid #eee;border-radius:10px;
}
.title-line{display:flex;gap:.5rem;align-items:baseline;flex-wrap:wrap}
.post-title{font-weight:600}
.post-date{opacity:.7}
.tag-line{margin-top:.25rem;display:flex;gap:.35rem;flex-wrap:wrap}
.tag-pill{
  font-size:.75rem;padding:.15rem .5rem;border:1px solid #e5e7eb;
  border-radius:999px;background:#f8fafc; /* lys baggrund */
  line-height:1.2; white-space:nowrap;
}
</style>
