
## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario configurare l'URL di reindirizzamento delle informazioni di registrazione dell'applicazione e quindi aggiungere l'ID dell'applicazione per l'applicazione SPA di JavaScript.

### <a name="configure-redirect-url"></a>Configurare l'URL di reindirizzamento

Configurare il campo `Redirect URL` in alto con l'URL della pagina index.html basata sul server Web, quindi fare clic su *Aggiorna*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento tramite SSL
> Se si usa Visual Studio, configurare il progetto in modo da usare SSL e quindi impiegare l'URL SSL per configurare le informazioni di registrazione dell'applicazione procedendo come segue:
> 1.    In Esplora soluzioni selezionare il progetto e controllare la finestra `Properties` (se non viene visualizzata una finestra delle proprietà, premere F4)
> 2.    Modificare `SSL Enabled` in `True`
> 3.    Copiare il valore da `SSL URL` negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Selezionare il menu `Project` e quindi selezionare `{Project} Properties...` (dove {Project} è il nome del progetto)
> 5.    Aprire la scheda `Web`
> 6.    Incollare il valore di `SSL URL` nel campo `Project Url`
> 7.    Copiare anche il valore nel campo `Redirect URL` disponibile nella parte superiore della pagina e quindi fare clic su *Aggiorna*


### <a name="configure-your-javascript-spa-application"></a>Configurare l'applicazione JavaScript SPA

1.  Creare un file denominato `msalconfig.js` contenente le informazioni di registrazione dell'applicazione. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e selezionare: `Add` > `New Item` > `JavaScript File`. Assegnare il nome `msalconfig.js`
2.  Aggiungere il codice seguente al file `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
