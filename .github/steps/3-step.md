## Passo 3: Tagging Dinamico con Metadati

Ottimo lavoro! Ora hai un workflow che usa le azioni Docker ufficiali per compilare e inviare la tua immagine per piattaforme multiple.

Ora, rendiamo il workflow più intelligente generando automaticamente tag diversi per l'immagine in base ai diversi eventi scatenanti.

### 📖 Teoria: Pubblicazione Basata sul Ciclo di Sviluppo

In un ciclo di vita di sviluppo software (SDLC) reale, spesso si ha bisogno di pubblicare immagini Docker in vari fasi, non solo quando invii modifiche al branch main.

Per questi eventi diversi spesso si scelgono strategie di tagging diverse, ecco alcuni esempi di scenari comuni:

- **Nuovi commit**:
  - Tag mutabili usando i nomi dei branch (es. `main`).
  - Tag univoci usando l'hash del commit (es. `28270383c5854bf`).
- **Aggiornamenti delle Pull Request**: Containerizzazione e pubblicazione per test (es. `pr-123`).
- **Rilasci di versione**: Versioni stabili attivate dai tag Git (es. `v1.0.0`, `prod`).

Gestire tutte queste diverse strategie di tagging manualmente nel tuo file workflow può diventare disordinato. L'azione `docker/metadata-action` semplifica tutto ciò generando automaticamente tag Docker basati sul elemento Git (branch, tag o PR) che ha attivato il workflow.

| Evento            | Ref                        | Tag                        |
| :---------------- | :------------------------- | :------------------------- |
| Pull Request      | `refs/pull/2/merge`        | `pr-2`                     |
| Push Branch       | `refs/heads/main`          | `main`                     |
| Push Tag          | `refs/tags/v1.2.3`         | `v1.2.3`, `prod`         |
| Push Tag          | `refs/tags/v2.0.8-beta.67` | `v2.0.8-beta.67`, `non-prod` |
| workflow_dispatch | `refs/heads/main`          | `main`                     |

### ⌨️ Attività: Aggiunta di trigger e action per i metadati

Aggiorniamo il nostro workflow per supportare trigger multipli e usare l'Action dei metadati per il tagging dinamico delle immagini docker.

1. Modifica il file `.github/workflows/docker-publish.yml`.
1. Aggiorna la parte dei trigger degli eventi del workflow per includere tutti i seguenti trigger

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

1. Aggiungi uno step per estrarre i metadati per le immagini Docker

    ❗️ Posizionalo prima dello step `docker/build-push-action`.

    ```yaml
    - name: Extract metadata for Docker
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ghcr.io/{{ full_repo_name | lower }}/stackoverflown
    ```

1. Aggiorna lo step `docker/build-push-action` per usare i tag generati.

   ```yaml
   - name: Build and push Docker image
     uses: docker/build-push-action@v5
     with:
       context: .
       push: true
       platforms: linux/amd64,linux/arm64
       tags: {% raw %}${{ steps.meta.outputs.tags }}{% endraw %}
   ```

    Assicurati che l'indentazione yaml sia impostata correttamente!

    > 💡 **Suggerimento:** Puoi eseguire il comando `actionlint` nel terminale per vedere se il workflow è formattato correttamente.

1. Effettua il commit e il push delle tue modifiche al branch `main`.
1. Mentre invii le tue modifiche, Octocat preparerà il passo successivo in questo esercizio!
