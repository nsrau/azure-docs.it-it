<properties
	pageTitle="Collegare un'app mobile a una soluzione aziendale SaaS | Microsoft Azure"
	description="Informazioni su come effettuare chiamate a risorse aziendali come SharePoint Online"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="06/19/2015"
	ms.author="mahender"/>

# Connettere un'app mobile ad API SaaS

In questa esercitazione si connetterà la propria app mobile a una soluzione SaaS (Software-as-a-Service). Si aggiornerà l'app dell'esercitazione [Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library] per creare un documento di Word in SharePoint Online quando viene aggiunto un nuovo elemento TodoItem.

Per completare questa esercitazione, è necessario disporre di:

* Disporre di Visual Studio 2013 in esecuzione in Windows 8.1
* Disporre di una sottoscrizione attiva a [SharePoint Online]
* Aver completato l'esercitazione [Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library]. Usare il tenant fornito dalla sottoscrizione a SharePoint.

## <a name="configure-permissions"></a>Configurare l'applicazione per l'accesso delegato a SharePoint
Per impostazione predefinita, il token ricevuto da AAD dispone di autorizzazioni limitate. Per poter accedere a una risorsa di terze parti o a un'applicazione SaaS come SharePoint Online, è necessario consentirlo in modo esplicito.

1. Nella sezione **Active Directory** del [portale di gestione di Azure] selezionare il tenant usato. Passare all'applicazione Web creata per il servizio mobile.

2. Nella scheda **Configura** scorrere nella pagina fino alla sezione delle autorizzazioni per altre applicazioni. Selezionare **Office 365 SharePoint Online** e concedere l'autorizzazione delegata per **modificare o eliminare i file degli utenti**. Fare quindi clic su **Salva**.

    ![][1]

A questo punto si è configurato AAD per il rilascio di un token di accesso di SharePoint al servizio app.

## <a name="store-credentials"></a>Aggiungere le informazioni di SharePoint all'app per dispositivi mobili

Per poter effettuare una chiamata a SharePoint, è necessario specificare gli endpoint con cui l'app mobile deve comunicare. È inoltre necessario poter provare l'identità del servizio app. usando una coppia ID e segreto client. L'ID client del servizio app è già stato ottenuto e archiviato durante la configurazione dell'accesso ad AAD. Dal momento che queste credenziali sono riservate, è consigliabile non archiviarle come testo non crittografato nel codice. Questi valori verranno invece impostati come impostazioni applicazione del codice dell'app mobile.

1. Tornare alla scheda delle applicazioni AAD del tenant e selezionare l'applicazione Web per il servizio app.

2. In Configura scorrere fino a Chiavi. Si otterrà un segreto client generando una nuova chiave. Notare che una volta creata una chiave e chiusa la pagina, non è possibile ottenerla di nuovo dal portale. In seguito alla creazione è quindi necessario copiare e salvare questo valore in un luogo sicuro. Selezionare una durata per la chiave, quindi fare clic su salva e copiare il valore risultante.

3. Nella sezione relativa al codice dell'app mobile del portale di gestione passare alla scheda Configura e scorrere verso il basso fino a Impostazioni app. In questa finestra è possibile fornire una coppia chiave-valore per facilitare il riferimento alle credenziali necessarie.

* Impostare SP\_Authority come endpoint dell'autorità del tenant AAD. Questo valore deve essere identico a quello usato per l'app client ed è espresso nel formato `https://login.windows.net/contoso.onmicrosoft.com`

* Impostare SP\_ClientSecret specificando il valore del segreto client ottenuto in precedenza.

* Impostare SP\_SharePointURL specificando l'URL del sito di SharePoint ed è espresso nel formato `https://contoso-my.sharepoint.com`

A questo punto sarà possibile ottenere di nuovo i valori nel codice usando ApiServices.Settings.

## <a name="obtain-token"></a>Ottenere un token di accesso e chiamare l'API di SharePoint

Per poter accedere a SharePoint, è necessario uno speciale token di accesso la cui destinazione è SharePoint. Per ottenere questo token, è necessario chiamare di nuovo AAD con l'identità del servizio app e il token rilasciato per l'utente.

1. Aprire il progetto di codice dell'app mobile in Visual Studio.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. In Gestione pacchetti NuGet fare clic su **Online**. Immettere **Microsoft.Azure.Mobile.Server.AppService** come termine di ricerca. Fare clic su **Installa** per installare il pacchetto [estensione del servizio app back-end .NET per app per dispositivi mobili]. Questo pacchetto fornisce metodi di estensione per usare le informazioni sull'utente attualmente connesso.

2. Nel progetto di codice dell'app per dispositivi mobili creare una nuova classe denominata SharePointUploadContext. Aggiungere un'istruzione `using Microsoft.Azure.Mobile.Server.AppService;` al file. Aggiungere quindi i dati seguenti alla classe:

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web";
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. Creare ora un metodo per aggiungere il file alla raccolta documenti dell'utente:

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Creare e caricare un documento di Word

Per creare un documento di Word, verrà usato il pacchetto NuGet OpenXML. Per installare questo pacchetto, aprire Gestione NuGet e cercare DocumentFormat.OpenXml.

1. Aggiungere il codice seguente a TodoItemController: In tal modo verrà creato un documento di Word basato su un elemento TodoItem. Il testo del documento costituirà il nome dell'elemento.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. Sostituire PostTodoItem con il codice seguente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Testare l'applicazione

1. Pubblicare le modifiche nel back-end ed eseguire l'applicazione client. Accedere quando richiesto e inserire un nuovo elemento TodoItem.

2. Passare al sito di SharePoint e accedere con lo stesso utente.

3. Selezionare la scheda OneDrive. Nella cartella Documenti dovrebbe essere presente un documento di Word il cui titolo è GUID. Aprire il documento per trovare il testo dell'elemento TodoItem.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/it-it/sharepoint/
[Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[estensione del servizio app back-end .NET per app per dispositivi mobili]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.AppService/
 

<!---HONumber=August15_HO7-->