
## <a name="register-your-application"></a>Registrare l'applicazione

È possibile creare un'applicazione in più modi. Selezionarne uno:

### <a name="option-1-register-your-application-express-mode"></a>Opzione 1: Registrare l'applicazione (modalità Rapida)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:

1.  Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'*installazione guidata* sia selezionata
4.  Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice

### <a name="option-2-register-your-application-advanced-mode"></a>Opzione 2: Registrare l'applicazione (modalità avanzata)

1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica 
3. Assicurarsi che l'opzione per l'*installazione guidata* sia deselezionata
4.  Fare clic su `Add Platform`, quindi selezionare `Web`
5. Aggiungere l'elemento `Redirect URL` corrispondente all'URL dell'applicazione basato sul server Web. Per le istruzioni per impostare/ottenere l'URL di reindirizzamento in Visual Studio e Python, vedere le sezioni seguenti.
6. Fare clic su *Save*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento
> Seguire le istruzioni per ottenere l'URL di reindirizzamento:
> 1.    In *Esplora soluzioni* selezionare il progetto e controllare la finestra `Properties`. Se non viene visualizzata una finestra delle proprietà, premere `F4`.
> 2.    Copiare il valore da `URL` negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Tornare al *portale di registrazione delle applicazioni*, incollare il valore come `Redirect URL` e fare clic su "Salva"

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Configurazione dell'URL di reindirizzamento per Python
> Per Python è possibile configurare la porta del server Web tramite la riga di comando. Questa configurazione guidata usa la porta 8080 come riferimento, ma è possibile usare qualsiasi altra porta disponibile. In ogni caso, seguire le istruzioni riportate di seguito per configurare un URL di reindirizzamento nelle informazioni di registrazione dell'applicazione:<br/>
> - Tornare al *portale di registrazione delle applicazioni* e impostare `http://localhost:8080/` come `Redirect URL` o usare `http://localhost:[port]/` se si usa una porta TCP personalizzata (dove *[port]* è il numero di porta TCP personalizzata) e fare clic su "Salva"


#### <a name="configure-your-javascript-spa"></a>Configurare JavaScript SPA

1.  Creare un file denominato `msalconfig.js` contenente le informazioni di registrazione dell'applicazione. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e scegliere `Add` > `New Item` > `JavaScript File`. Assegnare il nome `msalconfig.js`
2.  Aggiungere il codice seguente al file `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Sostituire <code>Enter_the_Application_Id_here</code> con l'ID applicazione appena registrato
</li>
</ol>
