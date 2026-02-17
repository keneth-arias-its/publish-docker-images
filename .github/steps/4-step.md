## Passo 4: Rilasciare il Gioco

Hai configurato con successo un workflow robusto che etichetta automaticamente le tue immagini Docker in base al contesto Git!

Ora, mettiamolo alla prova simulando un ciclo di sviluppo reale: aggiungere una funzionalità, farne il merge e rilasciare una nuova versione.

### ⌨️ Attività: Aggiungi una funzionalità

Iniziamo aggiungendo una semplice modifica al codice sorgente.

1. Inizia passando a un nuovo branch chiamato `feature/add-high-score`.
   Da terminale:
   ```bash
   git branch feature/add-high-score
   git checkout feature/add-high-score
   ```

1. Apri il file `src/index.html`.
1. Dalla `linea 20` alla `linea 24`, sostituisci l'area `info-section` relativa al punteggio con l'esempio qui sotto.

   ```txt
   <div class="info-section">
      <h3>Current Score</h3>
      <div class="score" id="score">0</div>
      <h3>High Score</h3>
      <div class="high-score" id="high-score">0</div>
   </div>
   ```

   Questo dimostrerà 3 tipi di modifiche:

   - Modifica l'etichetta `Score` in `Current Score`
   - Aggiunge le informazioni su `High Score`.
   - Rimuove le informazioni su `status`.

1. Effettua il commit e il push delle tue modifiche al branch `feature/add-high-score`.
   Da terminale:
   ```bash
   git add src/index.html
   git commit -m "Add high score display"
   git push -u origin feature/add-high-score
   ```

### ⌨️ Attività: Crea una pull request

Ora che hai il tuo branch della funzionalità pronto, creiamo una Pull Request per vedere se il tuo workflow compila l'immagine con il tag `pr-X` appropriato.

1. In una nuova scheda del browser, naviga alla scheda [Pull Requests](https://github.com/{{full_repo_name}}/pulls) del tuo repository.
1. Crea una Pull Request indirizzata al branch `main` dal branch che hai appena creato.
   > ⏳ **Aspetta:** Non fare ancora il merge!
1. Vai alla scheda **[Actions](https://github.com/{{full_repo_name}}/actions)** e osserva l'esecuzione del workflow `Docker Publish` attivata dalla Pull Request.
   - Questa esecuzione compilerà l'immagine con il tag `pr-X` (es. `pr-2`).
1. Una volta che il workflow termina con successo, verifica che l'immagine sia presente nella sezione **Packages** del tuo repository.
1. Puoi scaricare ed eseguire l'immagine nel tuo codespace per vedere le modifiche in azione prima del merge!

   Sostituisci `<PR_NUMBER>` con il numero effettivo della tua Pull Request:

   ```bash
   docker run -d -p 8080:80 ghcr.io/{{ full_repo_name | lower }}/stackoverflown:pr-<PR_NUMBER>
   ```

   Puoi accedere all'applicazione attraverso la scheda `Ports` - sulla porta `8080` e vedere la nuova funzionalità high score!

   <img width="600" alt="Image showing the ports tab" src="https://github.com/user-attachments/assets/80944d79-898a-43f9-94a0-6a9cc153f38d" />

### ⌨️ Attività: Fai il merge della pull request e crea una release

Bene! Ora facciamo il merge della pull request e creiamo una release stabile con un tag di versione appropriato.

1. Torna alla Pull Request e fai il merge nel branch `main`.
   > 🪧 **Nota:** Questo attiverà anche una nuova esecuzione del workflow **Docker Publish**
1. Vai alla scheda **Code** del tuo repository e clicca su **Releases** (nella barra laterale destra).

   <img width="300" alt="Image showing the releases section of the repository" src="https://github.com/user-attachments/assets/a132ef5f-43cb-490c-b1fc-119f40eedb7c" />

1. Crea una nuova release:
   - Scegli un tag: `v1.0.0` (Create new tag)
   - Target: `main`
   - Release Title: `v1.0.0`
   - Release notes: `First official release with high score tracking!`

   <details>
   <summary>📸 Mostra screenshot</summary><br/>


   <img width="600" alt="screenshot of create release page" src="https://github.com/user-attachments/assets/c7e275b7-6881-41e0-a0d6-e59194f0c335" />
   
   </details>

1. In fondo, clicca il pulsante **Publish release**.
1. Vai alla scheda **[Actions](https://github.com/{{full_repo_name}}/actions)** un'ultima volta. Dovresti vedere un'esecuzione del workflow attivata dal nuovo tag.
   - Questa esecuzione compilerà l'immagine con il tag `v1.0.0`.
1. Una volta che il workflow termina con successo, verifica che l'immagine `v1.0.0` sia presente nella sezione **Packages** del tuo repository.
1. Una volta pubblicata la release, Octocat tornerà da te con una rapida revisione dell'esercizio!

### ⌨️ Attività: Gestisci le impostazioni del pacchetto

Hai finito! Come passaggio opzionale, puoi esplorare le impostazioni del pacchetto per la tua immagine Docker per regolare la visibilità, il controllo degli accessi o eliminare il pacchetto se lo desideri.

<details>
<summary>✨ Passaggi bonus: gestisci le impostazioni del pacchetto</summary>

1. Vai alla pagina dei pacchetti di **Stackoverflown** e clicca **Package settings** sul lato destro.
   
   <img width="600"  alt="Image showing package settings button" src="https://github.com/user-attachments/assets/0c3c531b-5337-4d02-a364-b0ff28abb582" />


1. (opzionale) Sotto **Manage Actions access**, puoi scegliere repository aggiuntivi che possono accedere a questo pacchetto oltre al repository corrente.

   <img width="600" alt="Image showing manage actions access" src="https://github.com/user-attachments/assets/ed15d318-2b44-4120-9c83-a5d01afdadd2" />

   > 💡 **Suggerimento:** Questo è particolarmente utile in un contesto organizzativo dove più repository potrebbero aver bisogno di accedere allo stesso pacchetto.

1. (opzionale) Sotto **Danger Zone** puoi cambiare la visibilità o eliminare il pacchetto (tutte le sue versioni).

   <img width="600" alt="Image showing danger zone section" src="https://github.com/user-attachments/assets/7928c7c0-def3-4d51-9a1d-42273ff68a36" />

   > ❕ **Importante:** Poiché i pacchetti sono collegati al tuo **account** (non al repository), eliminare questo repository **non** eliminerà il pacchetto. Se vorrai mai eliminare il pacchetto, devi farlo da questa pagina.

</details>
