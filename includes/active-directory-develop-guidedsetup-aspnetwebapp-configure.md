
## <a name="create-an-application-express"></a>Creare un'applicazione (Rapida)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'installazione guidata sia selezionata
4.  Seguire le istruzioni per aggiungere un URL di reindirizzamento all'applicazione

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Aggiungere le informazioni di registrazione dell'applicazione alla soluzione (Avanzata)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica 
3.  Assicurarsi che l'opzione per l'installazione guidata sia deselezionata
4.  Fare clic su `Add Platform`, quindi selezionare `Web`
5.  Tornare a Visual Studio e in Esplora soluzioni selezionare il progetto, quindi esaminare la finestra Proprietà (se la finestra Proprietà non è visualizzata, premere F4)
6.  Impostare il valore di SSL abilitato su `True`
7.  Copiare l'URL SSL e aggiungere tale URL all'elenco di URL di reindirizzamento nell'elenco corrispondente del portale di registrazione:<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Aggiungere il codice seguente in `web.config`, disponibile nella sezione `configuration\appSettings` della cartella radice:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Sostituire `ClientId` con l'ID applicazione appena registrato
</li>
<li>
Sostituire `redirectUri` con l'URL SSL del progetto
</li>
</ol>
<!-- End Docs -->
