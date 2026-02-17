## Passo 2: Salire di livello con le Docker Actions

Ottimo lavoro!

Quel commit che hai appena fatto dovrebbe aver attivato la prima esecuzione del tuo workflow e pubblicato un'immagine Docker nel GitHub Container Registry sotto il tuo account.

Vediamo come scaricare quell'immagine e migliorare il workflow con azioni docker open source.

### 📖 Teoria: Azioni Docker Specializzate

Proprio come `docker/login-action` che hai appena usato, ci sono anche altre azioni open source che forniscono miglioramenti significativi per i workflow dei container. Diamo un'occhiata ad alcune di esse:

| Azione                       | Benefici                                                                                                          |
| :--------------------------- | :---------------------------------------------------------------------------------------------------------------- |
| `docker/build-push-action`   | Supporta build multi-piattaforma, segreti, cache remota e funzionalità di build avanzate                          |
| `docker/setup-qemu-action`   | Abilita la compilazione per diverse architetture (es. ARM64)                                                      |
| `docker/setup-buildx-action` | Abilita build multi-piattaforma, export della cache e supporto completo a [BuildKit](https://docs.docker.com/build/buildkit/) |

> [!TIP]
> Queste azioni, come molte altre, sono disponibili nel [GitHub Marketplace](https://github.com/marketplace?query=docker&type=actions).

### ⌨️ Attività: Pull ed esecuzione della tua immagine docker

Il commit dal tuo passo precedente dovrebbe aver attivato la prima esecuzione del tuo workflow e pubblicato un'immagine Docker nel GitHub Container Registry.

Facciamo il pull di quell'immagine ed eseguiamola nel tuo codespace per vedere il gioco in funzione!

1. Vai alla [pagina principale](https://github.com/{{ full_repo_name }}) del tuo repository
1. Sul lato destro, sotto la sezione **Packages**, clicca `{{ repo_name | lower }}/stackoverflown`

   <img width="300" alt="Image showing packages button" src="https://github.com/user-attachments/assets/ada36c5a-f1ce-4125-9008-661976ffaa15" />


1. Copia il comando che inizia con `docker pull ...`
1. Torna nel tuo codespace, esegui quel comando nel terminale per scaricare l'immagine dal container registry
1. Verifica che l'immagine sia disponibile localmente eseguendo:

   ```bash
   docker images
   ```

1. Lanciamo un container Docker da quell'immagine e vediamo l'app stackoverflown in esecuzione!

   ```bash
   docker run -p 8080:80 ghcr.io/{{ full_repo_name | lower }}/stackoverflown:main
   ```

1. Puoi accedere all'applicazione attraverso la scheda `Ports` - sulla porta `8080`

   <img width="600" alt="Image showing the ports tab" src="https://github.com/user-attachments/assets/80944d79-898a-43f9-94a0-6a9cc153f38d" />


   > ✨ Fai uno screenshot del gioco in esecuzione, salvalo come stackoverflown.png e aggiungilo al repository

1. Puoi fermare l'esecuzione dell'applicazione premendo `Ctrl + C` nel terminale

> [!NOTE]
> Durante questo esercizio, pubblicherai diverse versioni dell'immagine. Puoi sempre usare questi stessi passaggi per scaricare ed eseguire qualsiasi versione tu crei, anche se non esplicitamente richiesto.

### ⌨️ Attività: Sfruttare le Docker Actions open source

Modifichiamo il workflow per usare le azioni Docker ufficiali per un processo di build più robusto e ricco di funzionalità.

1. Apri il file `.github/workflows/docker-publish.yml`.
1. Rimuovi lo step esistente `Build and push Docker image` con i comandi `docker`. Lo sostituiremo con altre Actions open source.

    > ❗ **Attenzione:** Rimuovi solo lo step `Build and push Docker image`. **Non** rimuovere gli step con le Actions `actions/checkout` e `docker/login-action`.

   Ora, aggiungi questi tre step seguenti al posto dello step `Build and push Docker image` che hai appena rimosso.

   Questi step imposteranno QEMU per build multi-architettura, imposteranno Docker Buildx, e poi compileranno e invieranno l'immagine Docker con due tag diversi.

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

   Assicurati che l'indentazione yaml sia impostata correttamente!

   > 💡 **Suggerimento:** Puoi eseguire il comando `actionlint` nel terminale di codespace per vedere se il workflow è formattato correttamente.

   <details>
   <summary>Hai problemi? 🤷 Vedi il file workflow completo</summary><br/>

   Nel caso ne avessi bisogno, ecco il contenuto completo del file workflow aggiornato:

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

1. Effettua il commit e il push delle tue modifiche al branch `main`. Mentre invii le tue modifiche Octocat controllerà il tuo lavoro e preparerà il passo successivo in questo esercizio!
1. Monitora l'esecuzione del tuo workflow nella scheda [Actions](https://github.com/{{ full_repo_name }}/actions) del tuo repository e **assicurati che venga completato con successo**.
