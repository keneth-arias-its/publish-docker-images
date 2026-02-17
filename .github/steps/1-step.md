## Step 1: Primo contatto con GitHub Packages

Col tuo team hai lavorato duramente su un fantastico gioco web chiamato **Stackoverflown**. È un successo, e ora vuoi **containerizzarlo e versionarlo** per la distribuzione in modo che i giocatori possano eseguirlo ovunque facilmente e distribuirlo sui loro server.

<img alt="Screenshot of the Stackoverflown game" src="https://github.com/user-attachments/assets/8eb5fa0c-9282-459e-9d15-320c13f74263" width="900">

Per farlo in modo efficiente, automatizziamo il processo di build e publish delle nuove versioni della tua app usando GitHub Actions!

### 📖 Teoria: Informazioni su GitHub Packages e Container Registry

GitHub Packages è una piattaforma per ospitare e gestire pacchetti, inclusi container e altre dipendenze. Offre diversi registri di pacchetti per i gestori di pacchetti comunemente usati, come:

- 🐳 **Docker**
- 📦 npm
- 💎 RubyGems
- 🪶 Apache Maven
- 🐘 Gradle
- 🔷 NuGet

I GitHub Packages sono associati a un namespace di un account GitHub (Utente o Organizzazione), non solo a un repository. Questo è spesso utile perché potresti voler distribuire un pacchetto e riutilizzarlo in più repository.

In questo esercizio imposteremo l'automazione per pubblicare immagini 🐳 **[Docker](https://docs.docker.com/get-started/docker-overview/)** su **[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)** (`ghcr.io`). Dovremo assicurarci che il permesso `packages` sia impostato in modo che il segreto `GITHUB_TOKEN` integrato in GitHub Actions possa essere utilizzato per l'autenticazione al registro.


> [!NOTE]
> GitHub Packages è gratuito per i repository pubblici.
>
> Per i repository privati, c'è una certa quantità di archiviazione e trasferimento dati gratuiti ogni mese. Dai un'occhiata a [Informazioni sulla fatturazione per GitHub Packages](https://docs.github.com/en/billing/concepts/product-billing/github-packages) per i dettagli.

### ⌨️ Attività: Imposta il tuo ambiente di sviluppo

Usiamo **GitHub Codespaces** per impostare un ambiente di sviluppo nel cloud e lavorarci per il resto dell'esercizio!

1. Fai clic con il tasto destro sul pulsante in basso per aprire la pagina **Create Codespace** in una nuova scheda. Usa la configurazione predefinita.

   [![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/{{full_repo_name}}?quickstart=1)

1. Conferma che il campo **Repository** sia la tua copia dell'esercizio, non l'originale, quindi clicca il pulsante verde **Create Codespace**.

   - ✅ La tua copia: `/{{full_repo_name}}`
   - ❌ Originale: `/skills/publish-docker-images`

1. Attendi un attimo che Visual Studio Code si carichi completamente nel tuo browser.

### ⌨️ Attività: Crea un Workflow base per pubblicare container Docker

Iniziamo creando un workflow per compilare e pubblicare il nostro gioco **Stackoverflown** come immagine docker.

1. Nel tuo codespace, all'interno della directory `.github/workflows` crea un nuovo file workflow chiamato:

   ```text
   docker-publish.yml
   ```

1. All'interno di quel file, iniziamo definendo il nome del workflow, l'evento scatenante e i permessi:

   ```yaml
   name: Docker Publish

   on:
     push:
       branches:
         - main

   permissions:
     contents: read
     packages: write
   ```

   Questo workflow verrà eseguito su tutti i commit inviati al branch `main` con permessi per leggere i contenuti del repository e inviare pacchetti al GitHub Container Registry.

1. Aggiungi il job `build-and-push` alla fine del file:

   ```yaml
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
         - name: Build and push Docker image
           run: |
             docker build . --tag ghcr.io/{{ full_repo_name | lower }}/stackoverflown:main
             docker push ghcr.io/{{ full_repo_name | lower }}/stackoverflown:main
   ```

   Questo job esegue il checkout del codice del repository, si autentica al GitHub Container Registry usando `GITHUB_TOKEN`, compila l'immagine Docker e la pubblica nel registro sotto il tuo account GitHub.

   > 🪧 Nota: Il comando `docker build` segue le istruzioni nel file 🐳 **Dockerfile** presente nel repository su come containerizzare l'applicazione

1. Effettua il commit e il push delle tue modifiche al branch `main`.
1. Quando invii le tue modifiche, il workflow che hai appena creato dovrebbe avviarsi anche per la prima volta. Monitoralo nella scheda [**Actions**](https://github.com/{{ full_repo_name }}/actions) e **assicurati che venga completato con successo**.

<details>
<summary>Hai problemi? 🤷</summary><br/>

Se il workflow fallisce, controlla la scheda **Actions** per i log degli errori. Problemi comuni includono:

- Indentazione errata in YAML. Prova a eseguire il comando `actionlint` nel terminale di codespace e vedi se appaiono errori.
- Lettere maiuscole nel nome dell'immagine (Docker richiede sempre lettere minuscole)

</details>
