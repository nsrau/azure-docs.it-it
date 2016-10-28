<properties
   pageTitle="Informazioni sul manifesto dell'applicazione in Azure Active Directory | Microsoft Azure"
   description="Descrizione dettagliata dell'uso del manifesto dell'applicazione Azure Active Directory, che rappresenta una configurazione dell'identità dell'applicazione in un tenant di Azure AD e che viene usato per facilitare l'autorizzazione OAuth, per l'esperienza di consenso e altro ancora."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/25/2016"
   ms.author="dkershaw;bryanla"/>

# Informazioni sul manifesto dell'applicazione in Azure Active Directory

Le applicazioni che si integrano con Azure Active Directory (AD) devono essere registrate con un tenant di Azure AD, fornendo una configurazione dell'identità permanente per l'applicazione. Questa configurazione viene valutata in fase di esecuzione, rendendo possibili scenari che consentono a un'applicazione di eseguire l'outsourcing e negoziare le operazioni di autenticazione/autorizzazione tramite Azure AD. Per altre informazioni sul modello applicativo di Azure AD, vedere l'articolo su [Aggiunta, aggiornamento e rimozione di un'applicazione][ADD-UPD-RMV-APP].

## Aggiornamento della configurazione dell'identità di un'applicazione

Per l'aggiornamento delle proprietà di configurazione dell'identità di un'applicazione esistono in effetti più opzioni, che variano per funzionalità e livelli di difficoltà, incluse le seguenti:

- L'**interfaccia utente Web del [portale di Azure classico][AZURE-CLASSIC-PORTAL]** consente di aggiornare le proprietà più comuni di un'applicazione. Questo è il modo più rapido e meno soggetto a errori di aggiornare le proprietà dell'applicazione, ma non consente l'accesso completo a tutte le proprietà come i due metodi seguenti.
- Per gli scenari più avanzati in cui è necessario aggiornare proprietà non esposte nel portale di Azure classico, si può modificare il **manifesto dell'applicazione**. Questo è l'argomento su cui verte questo articolo, che verrà illustrato più dettagliatamente nella sezione successiva.
- È anche possibile ** scrivere un'applicazione che usa l'[API Graph][GRAPH-API]** per aggiornare l'applicazione, cosa che richiede l'impegno maggiore. Questa può tuttavia essere un'opzione interessante se si scrive software di gestione o se è necessario aggiornare le proprietà dell'applicazione a intervalli regolari e in modo automatico.

## Uso del manifesto dell'applicazione per aggiornare la configurazione dell'identità di un'applicazione
Tramite il [portale di Azure classico][AZURE-CLASSIC-PORTAL] è possibile gestire la configurazione dell'identità dell'applicazione scaricando e caricando la rappresentazione di un file JSON, detto manifesto dell'applicazione. Nella directory non viene archiviato un file effettivo, perché il manifesto dell'applicazione è semplicemente un'operazione GET HTTP sull'entità dell'applicazione API Graph di Azure AD e il caricamento è un'operazione PATCH HTTP sull'entità dell'applicazione.

Per comprendere il formato e le proprietà del manifesto dell'applicazione, sarà quindi necessario vedere la documentazione relativa all'[entità dell'applicazione][APPLICATION-ENTITY] dell'API Graph. Ecco alcuni esempi di aggiornamenti che possono essere eseguiti con il caricamento del manifesto dell'applicazione:

- **Dichiarare gli ambiti di autorizzazione** (oauth2Permissions) esposti dall'API Web. Per informazioni sull'implementazione della rappresentazione dell'utente usando l'ambito dell'autorizzazione delegata oauth2Permissions, vedere l'argomento "Esposizione di API Web ad altre applicazioni" in [Integrazione di applicazioni con Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Come accennato in precedenza, tutte le proprietà dell'entità dell'applicazione sono documentate nell'articolo [Riferimento a entità e tipi complessi][APPLICATION-ENTITY] dell'API Graph, compresa la proprietà oauth2Permissions che è una raccolta di tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Dichiarare i ruoli applicazione (appRoles) esposti dall'app**. La proprietà appRoles dell'entità dell'applicazione è una raccolta di tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Per un esempio di implementazione, vedere l'articolo relativo al [Controllo di accesso basato sui ruoli nelle applicazioni cloud tramite Azure AD][RBAC-CLOUD-APPS-AZUREAD].
- **Dichiarare le applicazioni client note (knownClientApplications)** che consentono di associare in modo logico il consenso delle applicazioni client specificate all'API Web o della risorsa.
- **Richiedere ad Azure AD di rilasciare l'attestazione di appartenenza al gruppo** per l'utente connesso (groupMembershipClaims). NOTA: è possibile configurare anche il rilascio di attestazioni relative alle appartenenze ai ruoli della directory. Per un esempio di implementazione, vedere l'articolo relativo all'[Autorizzazione nelle applicazioni cloud usando i gruppi di AD][AAD-GROUPS-FOR-AUTHORIZATION].
- **Consentire all'applicazione di supportare flussi di concessione implicita OAuth 2.0** (oauth2AllowImplicitFlow). Questo tipo di flusso di concessione viene usato con pagine Web con JavaScript incorporato o con applicazioni a pagina singola (Single Page Applications, SPA). Per ulteriori informazioni sulla concessione dell'autorizzazione implicita, vedere [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory][IMPLICIT-GRANT].
- **Abilitare l'uso di certificati X509 come chiave privata** (keyCredentials). Per degli esempi di implementazione, vedere gli articoli [Servizio di creazione e app daemon in Office 365][O365-SERVICE-DAEMON-APPS] e [Guida per gli sviluppatori all’autorizzazione con l'API di Gestione risorse di Azure ][DEV-GUIDE-TO-AUTH-WITH-ARM].
- **Aggiungere un nuovo URI ID app** per l'applicazione (identifierURIs). Gli URI ID app vengono usati per identificare in modo univoco un'applicazione nel tenant di Azure AD (o in più tenant di Azure AD, per gli scenari multi-tenant quando vengono qualificati tramite un dominio personalizzato verificato). Vengono usati quando si richiedono autorizzazioni a un'applicazione della risorsa o si acquisisce un token di accesso per un'applicazione della risorsa. Quando si aggiorna questo elemento, lo stesso aggiornamento viene eseguito nella raccolta servicePrincipalNames dell'entità servizio corrispondente, che si trova nel tenant home dell'applicazione.

Il manifesto dell'applicazione fornisce anche un ottimo modo per tenere traccia dello stato di registrazione dell'applicazione. Essendo disponibile in formato JSON, la rappresentazione del file può essere verificata nel controllo del codice sorgente insieme al codice sorgente della propria applicazione.

## Esempio dettagliato
Si esamineranno ora i passaggi necessari per aggiornare la configurazione dell'identità dell'applicazione tramite il manifesto dell'applicazione. Verrà evidenziato uno degli esempi forniti in precedenza, in cui viene illustrato come dichiarare un nuovo ambito di autorizzazione per un'applicazione della risorsa:

1. Passare al [portale di Azure classico][AZURE-CLASSIC-PORTAL] e accedere con un account che abbia privilegi di amministratore del servizio o di co-amministratore.

2. Dopo l'autenticazione, scorrere e selezionare l'estensione Azure "Active Directory" nel riquadro di spostamento sinistro (1), quindi fare clic sul tenant di Azure AD in cui è registrata l'applicazione (2).

    ![Selezionare il tenant di Azure AD][SELECT-AZURE-AD-TENANT]

3. Quando viene visualizzata la pagina della directory, fare clic su "Applicazioni" (1) in alto nella pagina per visualizzare un elenco delle applicazioni registrate nel tenant. Trovare quindi l'applicazione che si vuole aggiornare e fare clic sul nome (2).

    ![Selezionare il tenant di Azure AD][SELECT-AZURE-AD-APP]

4. Una volta selezionata la pagina principale dell'applicazione, si noterà la funzionalità "Gestisci manifesto" in basso nella pagina (1). Se si fa clic su questo collegamento, verrà richiesto di scaricare o caricare il file manifesto JSON. Fare clic su "Scarica manifesto" (2). Verrà visualizzata immediatamente la finestra di dialogo di conferma del download che chiede di confermare facendo clic su "Scarica manifesto" (3), quindi aprire o salvare il file in locale (4).

    ![Gestire il manifesto, opzione di download][MANAGE-MANIFEST-DOWNLOAD]

    ![Scaricare il manifesto][DOWNLOAD-MANIFEST]

5. In questo esempio il file è stato salvato in locale, per poterlo aprire in un editor, apportare modifiche al file JSON e salvarlo di nuovo. Ecco come appare la struttura di nell'editor JSON di Visual Studio. Si noti che segue lo schema dell'[entità dell'applicazione][APPLICATION-ENTITY] citata in precedenza:

    ![Aggiornare il file manifesto JSON][UPDATE-MANIFEST]

    Supponendo ad esempio che si voglia implementare o esporre una nuova autorizzazione chiamata "Employees.Read.All" nell'applicazione della risorsa (API), è sufficiente aggiungere un elemento nuovo o un secondo elemento alla raccolta oauth2Permissions, ossia:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    La voce deve essere univoca, pertanto è necessario generare un nuovo ID univoco globale (GUID) per la proprietà `"id"`. In questo caso, poiché si è specificato `"type": "User"`, è possibile concedere l'autorizzazione da qualsiasi account autenticato dal tenant di Azure AD nel quale è registrata l'applicazione API o della risorsa, concedendo così all'applicazione client l'autorizzazione ad accedervi per conto dell'account. Le stringhe del nome visualizzato e della descrizione vengono usate durante il processo di consenso e per la visualizzazione nel portale di Azure classico.

6. Dopo aver completato l'aggiornamento del manifesto, ritornare alla pagina dell'applicazione Azure AD nel portale di Azure classico, fare clic di nuovo sulla funzionalità "Gestisci manifesto" (1), ma questa volta selezionare l'opzione "Carica manifesto" (2). In modo analogo al download, verrà visualizzata di nuovo una seconda finestra di dialogo che richiede il percorso del file JSON. Fare clic su "Cerca file..." (3), quindi usare la finestra di dialogo "Selezionare il file da caricare" per selezionare il file JSON (4) e fare clic su "Apri". Una volta chiusa la finestra di dialogo fare clic sul segno di spunta "OK" (5) per caricare il manifesto.

    ![Gestire il manifesto, opzione di caricamento][MANAGE-MANIFEST-UPLOAD]

    ![Caricare il file manifesto JSON][UPLOAD-MANIFEST]

    ![Caricare il file manifesto JSON, conferma][UPLOAD-MANIFEST-CONFIRM]

Ora che il manifesto è stato salvato, è possibile fornire a un'applicazione client registrata accesso alla nuova autorizzazione aggiunta in precedenza, ma questa volta è possibile usare l'interfaccia utente Web del portale di Azure classico anziché modificare il manifesto dell'applicazione client:

1. Passare innanzitutto alla pagina "Configura" dell'applicazione client in cui si vuole aggiungere l'accesso alla nuova API e fare clic sul pulsante "Aggiungi applicazione".
2. Verrà quindi visualizzato l'elenco delle applicazioni della risorsa registrate (API) nel tenant. Fare clic sul segno più (+) accanto al nome dell'applicazione della risorsa per selezionarlo.
3. Fare quindi clic sul segno di spunta in basso a destra.
4. Quando si ritorna alla sezione "Aggiungi applicazione" della pagina di configurazione del client, la nuova applicazione della risorsa verrà visualizzata nell'elenco. Se si passa il mouse sopra la sezione "Autorizzazioni delegate" a destra della riga, verrà visualizzato un elenco a discesa. Fare clic sull'elenco, quindi selezionare la nuova autorizzazione per aggiungerla all'elenco di autorizzazioni richieste dal client. Nota: la nuova autorizzazione verrà archiviata nella configurazione dell'identità dell'applicazione client, nella proprietà della raccolta "requiredResourceAccess".

![Autorizzazioni per altre applicazioni][PERMS-TO-OTHER-APPS]

![Autorizzazioni per altre applicazioni][PERMS-SELECT-APP]

![Autorizzazioni per altre applicazioni][PERMS-SELECT-PERMS]

È tutto. Ora le applicazioni verranno eseguite usando la nuova configurazione dell'identità.

## Passaggi successivi
Usare la sezione dei commenti DISQUS di seguito per fornire commenti e suggerimenti utili per perfezionare e definire il contenuto.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_0727_2016-->