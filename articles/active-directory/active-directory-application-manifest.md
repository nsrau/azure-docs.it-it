<properties
   pageTitle="Informazioni sul manifesto dell'applicazione in Azure Active Directory | Microsoft Azure"
   description="Fornisce dettagli aggiuntivi sul manifesto dell'applicazione in Azure AD che accompagna ogni configurazione dell'applicazione in un tenant di Azure AD."
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
   ms.date="09/03/2015"
   ms.author="dkershaw;bryanla"/>

# Informazioni sul manifesto dell'applicazione in Azure Active Directory

Le applicazioni che si integrano con Azure Active Directory (AD) devono essere registrate con un tenant di Azure AD, fornendo una configurazione dell'identità permanente per l'applicazione. Questa configurazione viene valutata in fase di esecuzione, rendendo possibili scenari che consentono a un'applicazione di eseguire l'outsourcing e negoziare le operazioni di autenticazione/autorizzazione tramite Azure AD. Per altre informazioni sul modello applicativo di Azure AD, vedere l'articolo su [Aggiunta, aggiornamento e rimozione di un'applicazione][ADD-UPD-RMV-APP].

## Aggiornamento della configurazione dell'identità di un'applicazione

Per l'aggiornamento delle proprietà di configurazione dell'identità di un'applicazione esistono in effetti più opzioni, che variano per funzionalità e livelli di difficoltà, incluse le seguenti:

- L'**interfaccia utente Web del [portale di Azure][AZURE-PORTAL]** consente di aggiornare le proprietà più comuni di un'applicazione. Questo è il modo più rapido e meno soggetto a errori di aggiornare le proprietà dell'applicazione, ma non consente l'accesso completo a tutte le proprietà come i due metodi seguenti.
- Per gli scenari più avanzati in cui è necessario aggiornare proprietà non esposte nel portale di Azure, si può modificare il **manifesto dell'applicazione**. Questo è l'argomento su cui verte questo articolo, che verrà illustrato più dettagliatamente nella sezione successiva.
- È anche possibile ** scrivere un'applicazione che usa l'[API Graph][GRAPH-API]** per aggiornare l'applicazione, cosa che richiede l'impegno maggiore. Questa può tuttavia essere un'opzione interessante se si scrive software di gestione o se è necessario aggiornare le proprietà dell'applicazione a intervalli regolari e in modo automatico.

## Uso del manifesto dell'applicazione per aggiornare la configurazione dell'identità di un'applicazione
Tramite il [portale di Azure][AZURE-PORTAL] è possibile gestire la configurazione dell'identità dell'applicazione scaricando e caricando la rappresentazione di un file JSON, detto manifesto dell'applicazione. Nella directory non viene archiviato un file effettivo, perché il manifesto dell'applicazione è semplicemente un'operazione GET HTTP sull'entità dell'applicazione API Graph di Azure AD e il caricamento è un'operazione PATCH HTTP sull'entità dell'applicazione.

Per comprendere il formato e le proprietà del manifesto dell'applicazione, sarà quindi necessario vedere la documentazione relativa all'[entità dell'applicazione][APPLICATION-ENTITY] dell'API Graph. Ecco alcuni esempi di aggiornamenti che possono essere eseguiti con il caricamento del manifesto dell'applicazione:

- Dichiarare gli ambiti di autorizzazione (oauth2Permissions) esposti dall'API Web. Per informazioni sull'implementazione della rappresentazione dell'utente usando l'ambito dell'autorizzazione delegata oauth2Permissions, vedere l'argomento "Esposizione di API Web ad altre applicazioni" in [Integrazione di applicazioni con Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Come accennato in precedenza, tutte le proprietà dell'entità dell'applicazione sono documentate nell'articolo di [riferimento su entità e tipi complessi][APPLICATION-ENTITY] dell'API Graph, inclusa oauth2Permissions che è di tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION]
- Dichiarare i ruoli applicazione (appRoles) esposti dall'app. Per un esempio di implementazione, vedere l'articolo relativo al [controllo di accesso basato sui ruoli nelle applicazioni cloud tramite Azure AD][RBAC-CLOUD-APPS-AZUREAD].
- Dichiarare le applicazioni client note.
- Richiedere ad Azure AD di rilasciare l'attestazione di appartenenza al gruppo per l'utente connesso. NOTA: è possibile configurare anche il rilascio di attestazioni relative alle appartenenze ai ruoli della directory. Per un esempio di implementazione, vedere l'articolo relativo all'[autorizzazione nelle applicazioni cloud usando i gruppi di AD][AAD-GROUPS-FOR-AUTHORIZATION].
- Consentire all'applicazione di supportare i flussi di concessioni implicite OAuth 2.0 (per le pagine Web JavaScript incorporate o Single Page Applications (SPA))
- Abilitare l'uso di certificati X509 come chiave privata. Per un esempio di implementazione, vedere l'articolo relativo alla[compilazione di app di servizi e daemon in Office 365][O365-SERVICE-DAEMON-APPS].

Il manifesto dell'applicazione fornisce anche un ottimo modo per tenere traccia dello stato di registrazione dell'applicazione. Essendo disponibile in formato JSON, la rappresentazione del file può essere verificata nel controllo del codice sorgente insieme al codice sorgente della propria applicazione.

### Esempio dettagliato
Si esamineranno ora i passaggi necessari per aggiornare la configurazione dell'identità dell'applicazione tramite il manifesto dell'applicazione:

1. Passare al [portale di Azure][AZURE-PORTAL] e accedere con un account che abbia privilegi di amministratore del servizio o coamministratore.


2. Dopo l'autenticazione, scorrere e selezionare l'estensione Azure "Active Directory" nel riquadro di spostamento sinistro (1), quindi fare clic sul tenant di Azure AD in cui è registrata l'applicazione (2).

	![Selezionare il tenant di Azure AD][SELECT-AZURE-AD-TENANT]


3. Quando viene visualizzata la pagina della directory, fare clic su "Applicazioni" (1) in alto nella pagina per visualizzare un elenco delle applicazioni registrate nel tenant. Trovare quindi l'applicazione che si vuole aggiornare e fare clic sul nome (2).

	![Selezionare il tenant di Azure AD][SELECT-AZURE-AD-APP]


4. Una volta selezionata la pagina principale dell'applicazione, si noterà la funzionalità "Gestisci manifesto" in basso nella pagina (1). Se si fa clic su questo collegamento, verrà richiesto di scaricare o caricare il file manifesto JSON. Fare clic su "Scarica manifesto" (2). Verrà visualizzata immediatamente la finestra di dialogo di conferma del download che chiede di confermare facendo clic su "Scarica manifesto" (3), quindi aprire o salvare il file in locale (4).

	![Gestire il manifesto, opzione di download][MANAGE-MANIFEST-DOWNLOAD]

	![Scaricare il manifesto][DOWNLOAD-MANIFEST]


5. In questo esempio il file è stato salvato in locale, per poterlo aprire in un editor, apportare modifiche al file JSON e salvarlo di nuovo. Ecco come appare la struttura di nell'editor JSON di Visual Studio. Si noti che segue lo schema dell'[entità dell'applicazione][APPLICATION-ENTITY] citata in precedenza:

	![Aggiornare il file manifesto JSON][UPDATE-MANIFEST]


6. Dopo aver completato l'aggiornamento del manifesto, ritornare alla pagina dell'applicazione Azure AD nel portale di Azure, fare clic di nuovo sulla funzionalità "Gestisci manifesto" (1), ma questa volta selezionare l'opzione "Carica manifesto" (2). In modo analogo al download, verrà visualizzata di nuovo una seconda finestra di dialogo che richiede il percorso del file JSON. Fare clic su "Cerca file..." (3), quindi usare la finestra di dialogo "Selezionare il file da caricare" per selezionare il file JSON (4) e fare clic su "Apri". Una volta chiusa la finestra di dialogo fare clic sul segno di spunta "OK" (5) per caricare il manifesto.

	![Gestire il manifesto, opzione di caricamento][MANAGE-MANIFEST-UPLOAD]

	![Caricare il file manifesto JSON][UPLOAD-MANIFEST]

	![Caricare il file manifesto JSON, conferma][UPLOAD-MANIFEST-CONFIRM]

È tutto. Ora l'applicazione può essere eseguita con la nuova Configurazione applicazione basata sulle modifiche apportate al manifesto.

## Passaggi successivi
Usare la sezione dei commenti DISQUS di seguito per fornire commenti e suggerimenti utili per perfezionare e definire il contenuto.

<!--Image references-->
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-PORTAL]: https://manage.windowsazure.com
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=Oct15_HO3-->