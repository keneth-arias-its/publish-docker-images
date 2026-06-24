## Passo 2: Migliora il workflow con le azioni Docker

Ottimo lavoro!

Il commit che hai appena inviato dovrebbe aver avviato il workflow per la prima volta e pubblicato un'immagine Docker su GitHub Container Registry.

Ora scaricherai quell'immagine, la eseguirai nel Codespace e poi migliorerai il workflow con azioni Docker open source.

### 📖 Teoria: Azioni Docker Specializzate

Oltre a `docker/login-action`, che hai già usato, esistono altre azioni open source utili per i workflow dei container:

| Azione                       | Benefici                                                                                                          |
| :--------------------------- | :---------------------------------------------------------------------------------------------------------------- |
| `docker/build-push-action`   | Supporta build multi-piattaforma, segreti, cache remota e funzionalità di build avanzate                          |
| `docker/setup-qemu-action`   | Abilita build per architetture diverse, ad esempio ARM64                                                          |
| `docker/setup-buildx-action` | Abilita build multi-piattaforma, export della cache e supporto completo a [BuildKit](https://docs.docker.com/build/buildkit/) |

> [!TIP]
> Queste azioni, come molte altre, sono disponibili nel [GitHub Marketplace](https://github.com/marketplace?query=docker&type=actions).

### ⌨️ Attività: Scarica ed esegui la tua immagine Docker

Il commit del passo precedente dovrebbe aver pubblicato un'immagine Docker su GitHub Container Registry.

Scarica quell'immagine ed eseguila nel Codespace per vedere il gioco in funzione.

1. Vai alla [pagina principale](https://github.com/{{ full_repo_name }}) del tuo repository
1. Sul lato destro, nella sezione **Packages**, fai clic su `{{ repo_name | lower }}/stackoverflown`

   <img width="300" alt="Pulsante Packages nella pagina del repository" src="https://github.com/user-attachments/assets/ada36c5a-f1ce-4125-9008-661976ffaa15" />


1. Copia il comando che inizia con `docker pull ...`
1. Torna nel Codespace ed esegui quel comando nel terminale per scaricare l'immagine dal registro container
1. Verifica che l'immagine sia disponibile localmente eseguendo:

   ```bash
   docker images
   ```

1. Avvia un container Docker da quell'immagine per vedere l'app Stackoverflown in esecuzione:

   ```bash
   docker run -p 8080:80 ghcr.io/{{ full_repo_name | lower }}/stackoverflown:main
   ```

1. Apri l'applicazione dalla scheda `Ports`, sulla porta `8080`.

   <img width="600" alt="Scheda Ports del Codespace" src="https://github.com/user-attachments/assets/80944d79-898a-43f9-94a0-6a9cc153f38d" />


   > ✨ Fai uno screenshot del gioco in esecuzione, salvalo sul tuo computer e aggiungilo al repository.

1. Salva uno screenshot del gioco in esecuzione e caricalo nel repository come file `.png`, ad esempio `stackoverflown.png`.

1. Per fermare l'applicazione, premi `Ctrl + C` nel terminale.

> [!NOTE]
> Durante questo esercizio, pubblicherai diverse versioni dell'immagine. Puoi sempre usare questi stessi passaggi per scaricare ed eseguire qualsiasi versione tu crei, anche se non esplicitamente richiesto.

### ⌨️ Attività: Usa le azioni Docker open source

Ora modifica il workflow per usare le azioni Docker ufficiali. Il processo di build sarà più robusto e più facile da estendere.

1. Apri il file `.github/workflows/docker-publish.yml`.
1. A partire dalla riga 23, rimuovi lo step esistente `Build and push Docker image` con i comandi `docker`. Lo sostituirai con azioni Docker dedicate.

    > ❗ **Attenzione:** rimuovi solo lo step `Build and push Docker image`. **Non** rimuovere gli step con le azioni `actions/checkout` e `docker/login-action`.

   Al suo posto, aggiungi i tre step seguenti con l'indentazione corretta.

   Questi step configurano QEMU per build multi-architettura, configurano Docker Buildx e pubblicano l'immagine Docker con due tag diversi.

   ```yaml
        - name: Set up QEMU
          uses: docker/setup-qemu-action@v3

        - name: Set up Docker Buildx
          uses: docker/setup-buildx-action@v3

        - name: Build and push Docker image
          uses: docker/build-push-action@v6
          with:
            context: .
            push: true
            platforms: linux/amd64,linux/arm64
            tags: |
              ghcr.io/{{ full_repo_name | lower }}/stackoverflown:main
              ghcr.io/{{ full_repo_name | lower }}/stackoverflown:{% raw %}${{ github.sha }}{% endraw %}
   ```

   Assicurati che l'indentazione YAML sia corretta.

   > 💡 **Suggerimento:** puoi eseguire `actionlint` nel terminale del Codespace per verificare la formattazione del workflow.

   <details>
   <summary>Hai problemi? 🤷 Vedi il file workflow completo</summary><br/>

   Se ti serve un confronto, ecco il contenuto completo del file workflow aggiornato:

    ```yaml
    name: Docker Publish

    on:
      push:
        branches:
          - main

    permissions:
      contents: read
      packages: write
      
    jobs:
      build-and-push:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v6
          - name: Log in to the Container registry
            uses: docker/login-action@v3
            with:
              registry: ghcr.io
              username: {% raw %}${{ github.actor }}{% endraw %}
              password: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}

          - name: Set up QEMU
            uses: docker/setup-qemu-action@v3

          - name: Set up Docker Buildx
            uses: docker/setup-buildx-action@v3

          - name: Build and push Docker image
            uses: docker/build-push-action@v6
            with:
              context: .
              push: true
              platforms: linux/amd64,linux/arm64
              tags: |
                ghcr.io/{{ full_repo_name | lower }}/stackoverflown:main
                ghcr.io/{{ full_repo_name | lower }}/stackoverflown:{% raw %}${{ github.sha }}{% endraw %}
    ```

   </details>

1. Effettua il commit e il push delle modifiche al branch `main`. Dopo il push, Octocat controllerà il tuo lavoro e preparerà il passo successivo.
1. Monitora l'esecuzione del workflow nella scheda [Actions](https://github.com/{{ full_repo_name }}/actions) del tuo repository e **assicurati che termini con successo**.
