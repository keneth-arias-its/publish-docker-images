## Passo 4: Pubblica una release del gioco

Hai configurato un workflow che crea tag per le immagini Docker in base al contesto Git.

Ora lo metterai alla prova con un ciclo di sviluppo realistico: aggiungerai una funzionalità, farai il merge della pull request e pubblicherai una nuova versione.

### ⌨️ Attività: Aggiungi una funzionalità

Inizia con una piccola modifica al codice sorgente.

1. Crea un nuovo branch chiamato `feature/add-high-score` e passa a quel branch.
   Da terminale:
   ```bash
   git branch feature/add-high-score
   git checkout feature/add-high-score
   ```

1. Apri il file `src/index.html`.
1. Dalle righe `20` a `24`, sostituisci l'area `info-section` relativa al punteggio con l'esempio qui sotto.

   ```txt
   <div class="info-section">
      <h3>Current Score</h3>
      <div class="score" id="score">0</div>
      <h3>High Score</h3>
      <div class="high-score" id="high-score">0</div>
   </div>
   ```

   Questa modifica mostra tre interventi:

   - Cambia l'etichetta `Score` in `Current Score`.
   - Aggiunge la sezione `High Score`.
   - Rimuove le informazioni di `status`.

1. Effettua il commit e il push delle tue modifiche al branch `feature/add-high-score`.
   Da terminale:
   ```bash
   git add src/index.html
   git commit -m "Add high score display"
   git push -u origin feature/add-high-score
   ```

### ⌨️ Attività: Crea una pull request

Ora che il branch della funzionalità è pronto, crea una pull request. Userai il workflow per verificare che l'immagine venga creata con il tag `pr-X` corretto.

1. In una nuova scheda del browser, naviga alla scheda [Pull Requests](https://github.com/{{full_repo_name}}/pulls) del tuo repository.
1. Crea una pull request dal branch che hai appena creato verso il branch `main`.
   > ⏳ **Aspetta:** Non fare ancora il merge!
1. Vai alla scheda **[Actions](https://github.com/{{full_repo_name}}/actions)** e osserva l'esecuzione del workflow `Docker Publish` attivata dalla pull request.
   - Questa esecuzione creerà l'immagine con il tag `pr-X` (es. `pr-2`).
1. Quando il workflow termina con successo, verifica che l'immagine sia presente nella sezione **Packages** del repository.
1. Puoi scaricare ed eseguire l'immagine nel Codespace per vedere le modifiche prima del merge.

   Sostituisci `<PR_NUMBER>` con il numero effettivo della pull request:

   ```bash
   docker run -d -p 8080:80 ghcr.io/{{ full_repo_name | lower }}/stackoverflown:pr-<PR_NUMBER>
   ```

   Apri l'applicazione dalla scheda `Ports`, sulla porta `8080`, e verifica la nuova funzionalità `High Score`.

   <img width="600" alt="Scheda Ports del Codespace" src="https://github.com/user-attachments/assets/80944d79-898a-43f9-94a0-6a9cc153f38d" />

### ⌨️ Attività: Esegui il merge e crea una release

Bene! Ora fai il merge della pull request e crea una release stabile con un tag di versione.

1. Torna alla pull request e fai il merge nel branch `main`.
   > 🪧 **Nota:** Questo attiverà anche una nuova esecuzione del workflow **Docker Publish**
1. Vai alla scheda **Code** del repository e fai clic su **Releases** nella barra laterale destra.

   <img width="300" alt="Sezione Releases nella pagina del repository" src="https://github.com/user-attachments/assets/a132ef5f-43cb-490c-b1fc-119f40eedb7c" />

1. Crea una nuova release:
   - Scegli un tag: `v1.0.0` (Create new tag)
   - Target: `main`
   - Release Title: `v1.0.0`
   - Release notes: `First official release with high score tracking!`

   <details>
   <summary>📸 Mostra screenshot</summary><br/>


   <img width="600" alt="Schermata della pagina di creazione di una release" src="https://github.com/user-attachments/assets/c7e275b7-6881-41e0-a0d6-e59194f0c335" />
   
   </details>

1. In fondo alla pagina, fai clic su **Publish release**.
1. Vai alla scheda **[Actions](https://github.com/{{full_repo_name}}/actions)** un'ultima volta. Dovresti vedere un'esecuzione del workflow attivata dal nuovo tag.
   - Questa esecuzione creerà l'immagine con il tag `v1.0.0`.
1. Quando il workflow termina con successo, verifica che l'immagine `v1.0.0` sia presente nella sezione **Packages** del repository.
1. Dopo la pubblicazione della release, Octocat tornerà con una breve revisione dell'esercizio.

### ⌨️ Attività: Gestisci le impostazioni del pacchetto

Hai finito! Come passaggio opzionale, puoi esplorare le impostazioni del pacchetto della tua immagine Docker. Da lì puoi regolare la visibilità, gestire gli accessi o eliminare il pacchetto.

<details>
<summary>✨ Passaggi bonus: gestisci le impostazioni del pacchetto</summary>

1. Vai alla pagina dei pacchetti di **Stackoverflown** e fai clic su **Package settings** sul lato destro.
   
   <img width="600"  alt="Pulsante Package settings nella pagina del pacchetto" src="https://github.com/user-attachments/assets/0c3c531b-5337-4d02-a364-b0ff28abb582" />


1. (opzionale) Sotto **Manage Actions access**, puoi scegliere altri repository autorizzati ad accedere al pacchetto.

   <img width="600" alt="Sezione Manage Actions access del pacchetto" src="https://github.com/user-attachments/assets/ed15d318-2b44-4120-9c83-a5d01afdadd2" />

   > 💡 **Suggerimento:** Questo è particolarmente utile in un contesto organizzativo dove più repository potrebbero aver bisogno di accedere allo stesso pacchetto.

1. (opzionale) Sotto **Danger Zone** puoi cambiare la visibilità o eliminare il pacchetto, incluse tutte le sue versioni.

   <img width="600" alt="Sezione Danger Zone delle impostazioni del pacchetto" src="https://github.com/user-attachments/assets/7928c7c0-def3-4d51-9a1d-42273ff68a36" />

   > ❕ **Importante:** i pacchetti sono collegati al tuo **account**, non solo al repository. Eliminare il repository **non** elimina il pacchetto. Se vorrai eliminarlo, dovrai farlo da questa pagina.

</details>
