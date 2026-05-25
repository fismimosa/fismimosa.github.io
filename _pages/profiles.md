---
layout: page
permalink: /people/
title: People
description: Members of the project
nav: true
nav_order: 4
---

<style>
  .mimosa-people {
    margin-top: 2rem;
  }

  .mimosa-people .person-card {
    display: flex;
    gap: 1.5rem;
    align-items: flex-start;
    margin-bottom: 2.5rem;
  }

  .mimosa-people .person-photo {
    width: 150px;
    height: 150px;
    max-width: 150px;
    object-fit: cover;
    border-radius: 8px;
    flex-shrink: 0;
  }

  .mimosa-people .person-info h3 {
    margin-top: 0;
    margin-bottom: 0.25rem;
  }

  .mimosa-people .person-position {
    margin-bottom: 0.25rem;
  }

  .mimosa-people .person-role {
    margin-bottom: 0.75rem;
    font-weight: 600;
  }

  .mimosa-people .person-bio {
    white-space: pre-line;
  }

  .mimosa-people .person-links {
    margin-top: 0.75rem;
  }

  @media (max-width: 576px) {
    .mimosa-people .person-card {
      flex-direction: column;
      gap: 1rem;
    }

    .mimosa-people .person-photo {
      width: 130px;
      height: 130px;
      max-width: 130px;
    }
  }
</style>

<div id="people-container" class="mimosa-people">
  <p>Loading people...</p>
</div>

<script>
  const dataBaseUrl = "https://raw.githubusercontent.com/fismimosa/mimosa-public-data/main/";
  const peopleUrl = dataBaseUrl + "data/people.json";

  const container = document.getElementById("people-container");

  function escapeHtml(value) {
    if (value === null || value === undefined) return "";
    return String(value)
      .replaceAll("&", "&amp;")
      .replaceAll("<", "&lt;")
      .replaceAll(">", "&gt;")
      .replaceAll('"', "&quot;")
      .replaceAll("'", "&#039;");
  }

  function resolveAssetUrl(path) {
    if (!path) return "";

    const value = String(path);

    if (value.startsWith("http://") || value.startsWith("https://")) {
      return value;
    }

    return dataBaseUrl + value.replace(/^\/+/, "");
  }

  function isActive(person) {
    return person.active === true || person.active === "true" || person.active === undefined;
  }

  function renderPerson(person) {
    const fullName = `${escapeHtml(person.first_name)} ${escapeHtml(person.last_name)}`.trim();
    const position = escapeHtml(person.position);
    const role = escapeHtml(person.role);
    const bio = escapeHtml(person.bio);
    const photo = resolveAssetUrl(person.photo);
    const email = escapeHtml(person.email);
    const website = escapeHtml(person.website);

    return `
      <article class="person-card">
        ${
          photo
            ? `<img src="${photo}" alt="${fullName}" class="person-photo" loading="lazy">`
            : ""
        }

        <div class="person-info">
          <h3>${fullName}</h3>

          ${
            position
              ? `<p class="person-position"><em>${position}</em></p>`
              : ""
          }

          ${
            role
              ? `<p class="person-role">${role}</p>`
              : ""
          }

          ${
            bio
              ? `<div class="person-bio">${bio}</div>`
              : ""
          }

          ${
            email || website
              ? `
                <p class="person-links">
                  ${email ? `<a href="mailto:${email}">Email</a>` : ""}
                  ${email && website ? " · " : ""}
                  ${website ? `<a href="${website}">Website</a>` : ""}
                </p>
              `
              : ""
          }
        </div>
      </article>
    `;
  }

  async function loadPeople() {
    try {
      const response = await fetch(peopleUrl);

      if (!response.ok) {
        throw new Error(`Could not load people data: HTTP ${response.status}`);
      }

      const data = await response.json();
      const peopleArray = Array.isArray(data) ? data : data.people;

      if (!Array.isArray(peopleArray)) {
        throw new Error("The people data must be a JSON array or an object with a people array.");
      }

      const people = peopleArray
        .filter(isActive)
        .sort((a, b) => Number(a.order || 999) - Number(b.order || 999));

      if (people.length === 0) {
        container.innerHTML = "<p>No active people found.</p>";
        return;
      }

      container.innerHTML = people.map(renderPerson).join("");
    } catch (error) {
      console.error(error);
      container.innerHTML = `
        <p><strong>Error loading people.</strong></p>
        <pre>${escapeHtml(error.message)}</pre>
      `;
    }
  }

  loadPeople();
</script>