## Passo 1: Primo contatto con GitHub Packages

Con il tuo team hai lavorato a un gioco web chiamato **Stackoverflown**. Il gioco funziona, e ora vuoi **containerizzarlo e versionarlo** per distribuirlo in modo semplice. Così i giocatori potranno eseguirlo ovunque, anche sui loro server.

<img alt="Schermata del gioco Stackoverflown" src="https://github.com/user-attachments/assets/8eb5fa0c-9282-459e-9d15-320c13f74263" width="900">

Per farlo in modo efficiente, userai GitHub Actions per automatizzare la creazione e la pubblicazione delle nuove versioni dell'app.

### 📖 Teoria: Informazioni su GitHub Packages e Container Registry

GitHub Packages è una piattaforma per ospitare e gestire pacchetti, inclusi container e altre dipendenze. Offre registri per diversi ecosistemi, tra cui:

- 🐳 **Docker**
- 📦 npm
- 💎 RubyGems
- 🪶 Apache Maven
- 🐘 Gradle
- 🔷 NuGet

I pacchetti di GitHub Packages sono associati al namespace di un account GitHub (utente o organizzazione), non solo a un repository. Questo è utile quando vuoi distribuire un pacchetto e riutilizzarlo in più repository.

In questo esercizio imposterai l'automazione per pubblicare immagini 🐳 **[Docker](https://docs.docker.com/get-started/docker-overview/)** su **[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)** (`ghcr.io`). Il workflow dovrà concedere il permesso `packages`, così il token integrato `GITHUB_TOKEN` potrà autenticarsi al registro.


> [!NOTE]
> GitHub Packages è gratuito per i repository pubblici.
>
> Per i repository privati è inclusa ogni mese una quota gratuita di archiviazione e trasferimento dati. Per i dettagli, consulta [Informazioni sulla fatturazione per GitHub Packages](https://docs.github.com/en/billing/concepts/product-billing/github-packages).

### ⌨️ Attività: Prepara il tuo ambiente di sviluppo

Userai **GitHub Codespaces** per creare un ambiente di sviluppo nel cloud. Sarà l'ambiente di lavoro per il resto dell'esercizio.

1. Fai clic con il tasto destro sul pulsante qui sotto e apri la pagina **Create Codespace** in una nuova scheda. Usa la configurazione predefinita.

   [![Apri in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/{{full_repo_name}}?quickstart=1)

1. Controlla che il campo **Repository** punti alla tua copia dell'esercizio, non al repository originale. Poi fai clic sul pulsante verde **Create Codespace**.

   - ✅ La tua copia: `/{{full_repo_name}}`
   - ❌ Originale: `/skills/publish-docker-images`

1. Attendi che Visual Studio Code si carichi completamente nel browser.

### ⌨️ Attività: Crea un workflow di base per pubblicare container Docker

Iniziamo creando un workflow che crea e pubblica **Stackoverflown** come immagine Docker.

1. Nel tuo Codespace, dentro la directory `.github/workflows`, crea un nuovo file workflow chiamato:

   ```text
   docker-publish.yml
   ```

1. In quel file, definisci il nome del workflow, l'evento di attivazione e i permessi:

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

   Questo workflow verrà eseguito a ogni commit inviato al branch `main`. Avrà i permessi per leggere i contenuti del repository e pubblicare pacchetti su GitHub Container Registry.

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

   Questo job esegue il checkout del repository, si autentica a GitHub Container Registry con `GITHUB_TOKEN`, crea l'immagine Docker e la pubblica nel registro sotto il tuo account GitHub.

   > 🪧 Nota: il comando `docker build` segue le istruzioni del file 🐳 **Dockerfile** presente nel repository.

1. Effettua il commit e il push delle tue modifiche al branch `main`.
1. Quando invii le modifiche, il workflow dovrebbe avviarsi per la prima volta. Monitoralo nella scheda [**Actions**](https://github.com/{{ full_repo_name }}/actions) e **assicurati che termini con successo**.

<details>
<summary>Hai problemi? 🤷</summary><br/>

Se il workflow fallisce, controlla la scheda **Actions** per i log degli errori. Problemi comuni includono:

- Indentazione YAML errata. Prova a eseguire `actionlint` nel terminale del Codespace e controlla gli eventuali errori.
- Lettere maiuscole nel nome dell'immagine (Docker richiede sempre lettere minuscole)

</details>
