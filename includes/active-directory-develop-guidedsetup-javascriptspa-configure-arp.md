## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario configurare l'URL di reindirizzamento delle informazioni di registrazione dell'applicazione e quindi aggiungere l'ID applicazione all'applicazione SPA JavaScript.

### <a name="configure-redirect-url"></a>Configurare l'URL di reindirizzamento

Configurare il campo `Redirect URL` con l'URL della pagina index.html in base al server Web, quindi fare clic su *Aggiorna*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento
> Per ottenere l'URL di reindirizzamento:
> 1.    In *Esplora soluzioni* selezionare il progetto e controllare la finestra `Properties`. Se non viene visualizzata una finestra delle proprietà, premere `F4`.
> 2.    Copiare il valore da `URL` negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Incollare il valore come `Redirect URL` nella parte superiore della pagina, quindi fare clic su `Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Configurazione dell'URL di reindirizzamento per Python
> Per Python è possibile configurare la porta del server Web tramite la riga di comando. Questa configurazione guidata usa la porta 8080 come riferimento, ma è possibile usare qualsiasi altra porta disponibile. In ogni caso, seguire le istruzioni seguenti per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione:<br/>
> Impostare `http://localhost:8080/` come `Redirect URL` nella parte superiore della pagina o usare `http://localhost:[port]/` se si usa un una porta TCP personalizzata, dove *[port]* è il numero di porta TCP', quindi fare clic su 'Update' (Aggiorna).

### <a name="configure-your-javascript-spa-application"></a>Configurare l'applicazione JavaScript SPA

1.  Creare un file denominato `msalconfig.js` contenente le informazioni di registrazione dell'applicazione. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e scegliere `Add` > `New Item` > `JavaScript File`. Assegnare il nome `msalconfig.js`
2.  Aggiungere il codice seguente al file `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
