
### <a name="create-an-application-express"></a>Creare un'applicazione (Rapida)
È ora necessario registrare l'applicazione nel portale di registrazione delle applicazioni Microsoft:

1.  Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'*installazione guidata* sia selezionata
4.  Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Aggiungere le informazioni di registrazione dell'applicazione alla soluzione (Avanzata)

1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica 
3. Assicurarsi che l'opzione per l'*installazione guidata* sia deselezionata
4.  Fare clic su `Add Platform`, quindi selezionare `Web`
5. Aggiungere un URL di reindirizzamento all'applicazione. L'URL di reindirizzamento è l'URL per la pagina `index.html` basata su server Web
6. Fare clic su *Save*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Istruzioni di Visual Studio per ottenere l'URL di reindirizzamento tramite SSL
> Se si usa Visual Studio, configurare il progetto in modo da usare SSL e quindi impiegare l'URL SSL per configurare le informazioni di registrazione dell'applicazione procedendo come segue:
> 1.    In Esplora soluzioni selezionare il progetto e controllare la finestra `Properties` (se non viene visualizzata una finestra delle proprietà, premere F4)
> 2.    Modificare `SSL Enabled` in `True`
> 3.    Copiare il valore da `SSL URL` negli Appunti:<br/> ![Proprietà del progetto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Selezionare il menu `Project` e quindi selezionare `{Project} Properties...` (dove {Project} è il nome del progetto)
> 5.    Aprire la scheda `Web`
> 6.    Incollare il valore di `SSL URL` nel campo `Project Url`
> 7.    Tornare al portale di registrazione dell'applicazione e incollare il valore in `Redirect URL` come URL di reindirizzamento, quindi fare clic su *Salva*


#### <a name="configure-your-javascript-spa-application"></a>Configurare l'applicazione JavaScript SPA

1.  Creare un file denominato `msalconfig.js` contenente le informazioni di registrazione dell'applicazione. Se si usa Visual Studio, selezionare il progetto (cartella radice del progetto), fare clic con il pulsante destro del mouse e selezionare: `Add` > `New Item` > `JavaScript File`. Assegnare il nome `msalconfig.js`
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