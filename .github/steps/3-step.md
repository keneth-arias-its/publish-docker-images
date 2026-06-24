## Passo 3: Tagging dinamico con i metadati

Ottimo lavoro! Ora hai un workflow che usa le azioni Docker ufficiali per creare e pubblicare la tua immagine su più piattaforme.

Adesso renderai il workflow più intelligente: genererà automaticamente tag diversi per l'immagine in base all'evento che lo avvia.

### 📖 Teoria: Pubblicazione Basata sul Ciclo di Sviluppo

In un ciclo di vita di sviluppo software (SDLC) reale, spesso devi pubblicare immagini Docker in varie fasi, non solo quando invii modifiche al branch `main`.

Eventi diversi richiedono spesso strategie di tagging diverse. Ecco alcuni scenari comuni:

- **Nuovi commit**:
  - Tag mutabili usando i nomi dei branch (es. `main`).
  - Tag univoci usando l'hash del commit (es. `28270383c5854bf`).
- **Aggiornamenti delle pull request**: creazione e pubblicazione di immagini per test (es. `pr-123`).
- **Rilasci di versione**: Versioni stabili attivate dai tag Git (es. `v1.0.0`, `prod`).

Gestire manualmente tutte queste strategie nel file workflow può diventare confuso. L'azione `docker/metadata-action` semplifica il processo: genera automaticamente tag Docker basati sull'elemento Git che ha attivato il workflow, come branch, tag o pull request.

| Evento            | Ref                        | Tag                        |
| :---------------- | :------------------------- | :------------------------- |
| Pull request      | `refs/pull/2/merge`        | `pr-2`                     |
| Push Branch       | `refs/heads/main`          | `main`                     |
| Push Tag          | `refs/tags/v1.2.3`         | `v1.2.3`, `prod`         |
| Push Tag          | `refs/tags/v2.0.8-beta.67` | `v2.0.8-beta.67`, `non-prod` |
| workflow_dispatch | `refs/heads/main`          | `main`                     |

### ⌨️ Attività: Aggiungi trigger e metadati

Aggiorna il workflow per supportare più trigger e usare `docker/metadata-action` per il tagging dinamico delle immagini Docker.

1. Modifica il file `.github/workflows/docker-publish.yml`.
1. Aggiorna la sezione degli eventi del workflow includendo questi trigger:

   ```yaml
   on:
     push:
       branches:
         - main
       tags:
         - "v*"
     pull_request:
       branches:
         - main
     workflow_dispatch:
   ```

1. Aggiungi uno step per estrarre i metadati dell'immagine Docker.

    ❗️ Posizionalo prima dello step `Build and push Docker image`, quello che usa `docker/build-push-action`.

  ```yaml
      - name: Extract metadata for Docker
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/{{ full_repo_name | lower }}/stackoverflown
  ```

1. Aggiorna lo step `docker/build-push-action` per usare i tag generati automaticamente.

  ```yaml
      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          platforms: linux/amd64,linux/arm64
          tags: {% raw %}${{ steps.meta.outputs.tags }}{% endraw %}
  ```

    Assicurati che l'indentazione YAML sia corretta.

    > 💡 **Suggerimento:** puoi eseguire `actionlint` nel terminale per verificare la formattazione del workflow.

1. Effettua il commit e il push delle tue modifiche al branch `main`.
1. Dopo il push, Octocat controllerà il lavoro e preparerà il passo successivo.
