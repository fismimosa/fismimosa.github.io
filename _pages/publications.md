---
layout: page
permalink: /publications/
title: Publications
description:
nav: true
nav_order: 2
---

<!-- _pages/publications.md -->

<div id="publications" class="mimosa-publications">
  <div class="publications-loading">Loading publications...</div>
</div>

<style>
.mimosa-publications {
  max-width: 900px;
  margin: 0 auto;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  color: #222;
}

.publications-loading {
  color: #666;
  font-size: 0.95rem;
  padding: 1rem 0;
}

.mimosa-publications h3.year-heading {
  margin-top: 2.5rem;
  margin-bottom: 1rem;
  font-size: 1.35rem;
  font-weight: 700;
  line-height: 1.2;
  border-bottom: 2px solid #ddd;
  padding-bottom: 0.4rem;
  color: #222 !important;
}

.mimosa-publications h3.year-heading:first-child {
  margin-top: 0;
}

.publication-entry {
  margin-bottom: 1rem;
  padding: 1rem 1.1rem;
  border: 1px solid #e2e2e2;
  border-radius: 10px;
  line-height: 1.55;
  background: #fff;
  color: #222;
}

.publication-entry:hover {
  border-color: #cfcfcf;
}

.publication-entry .csl-entry {
  margin: 0;
  color: #222;
}

.publication-entry a {
  font-weight: 500;
  text-decoration: none;
  color: #005ea8;
}

.publication-entry a:hover {
  text-decoration: underline;
}

.publications-error {
  padding: 1rem 1.1rem;
  border: 1px solid #e2e2e2;
  border-radius: 10px;
  background: #fff;
  color: #555;
  line-height: 1.5;
}

@media (max-width: 700px) {
  .mimosa-publications {
    max-width: 100%;
  }

  .year-heading {
    font-size: 1.2rem;
  }

  .publication-entry {
    padding: 0.9rem;
  }
}
</style>

<script>
(function() {
  const container = document.getElementById("publications");

  if (!container) {
    return;
  }

  const zoteroApiUrl =
    "https://api.zotero.org/groups/6564431/items/top" +
    "?format=json" +
    "&include=data,bib" +
    "&style=apa" +
    "&linkwrap=1" +
    "&sort=date" +
    "&direction=desc" +
    "&limit=100" +
    "&v=3";

  function getYear(item) {
    const date = item.data && item.data.date ? item.data.date : "";
    const match = date.match(/\d{4}/);
    return match ? match[0] : "Undated";
  }

  function isBibliographicItem(item) {
    if (!item || !item.data || !item.bib) {
      return false;
    }

    const excludedTypes = [
      "attachment",
      "note",
      "annotation"
    ];

    return !excludedTypes.includes(item.data.itemType);
  }

  function renderPublications(items) {
    const bibliographicItems = items.filter(isBibliographicItem);

    if (bibliographicItems.length === 0) {
      container.innerHTML =
        '<div class="publications-error">No publications are currently available.</div>';
      return;
    }

    container.innerHTML = "";

    let currentYear = null;

    bibliographicItems.forEach(function(item) {
      const year = getYear(item);

      if (year !== currentYear) {
        currentYear = year;

        const heading = document.createElement("div");
        heading.className = "year-heading";
        heading.textContent = year;
        container.appendChild(heading);
      }

      const entry = document.createElement("div");
      entry.className = "publication-entry";
      entry.innerHTML = item.bib;
      container.appendChild(entry);
    });
  }

  function renderError() {
    container.innerHTML =
      '<div class="publications-error">Publications could not be loaded at this time.</div>';
  }

  fetch(zoteroApiUrl)
    .then(function(response) {
      if (!response.ok) {
        throw new Error("Zotero API returned HTTP " + response.status);
      }

      return response.json();
    })
    .then(renderPublications)
    .catch(renderError);
})();
</script>