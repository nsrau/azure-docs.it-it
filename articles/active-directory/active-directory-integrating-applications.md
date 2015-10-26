<properties
   pageTitle="Integrazione di applicazioni con Azure Active Directory | Microsoft Azure"
   description="Informazioni dettagliate su come aggiungere, aggiornare o rimuovere un'applicazione in Azure Active Directory (Azure AD)."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/25/2015"
   ms.author="mbaldwin" />

# Integrazione di applicazioni con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Gli sviluppatori aziendali e i provider SaaS possono sviluppare servizi cloud commerciali o applicazioni line-of-business che possono essere integrate con Azure Active Directory (Azure AD) per garantire la sicurezza dell'accesso e delle autorizzazioni per i servizi. Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare i dettagli dell'applicazione con Azure AD mediante il portale di gestione di Azure.

Questo articolo mostra come aggiungere, aggiornare o rimuovere un'applicazione in Azure AD. Vengono indicati i diversi tipi di applicazioni che possono essere integrate con Azure AD e vengono fornite le informazioni su come configurare le applicazioni per accedere ad altre risorse, come le API Web e altro ancora.

Per altre informazioni sulle proprietà delle app, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md). Per conoscere le linee guida sulla personalizzazione da usare quando si sviluppano applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione per le app integrate](active-directory-branding-guidelines.md); i manifesti delle applicazioni vengono illustrate in [Informazioni sul manifesto dell’applicazione di Azure Active Directory](active-directory-application-manifest.md).

## Aggiunta di un'applicazione

Qualsiasi applicazione che vuole usare le funzionalità di Azure AD deve prima essere registrata in una directory. Questo processo di registrazione comporta l'assegnazione ad Azure AD dei dettagli sull'applicazione, come l'URL in cui è disponibile, l'URL per inviare le risposte dopo l'autenticazione di un utente, l'URI che identifica l'app e così via.

Se si vuole creare un'applicazione Web che richiede solo l'accesso per gli utenti in Azure AD, è semplicemente possibile seguire le istruzioni riportate di seguito. Se l'applicazione deve accedere a un'API Web, si compila un'applicazione nativa che deve accedere a un'API Web o si vuole che l'applicazione sia multi-tenant, è necessario passare alla sezione [Aggiornamento di un'applicazione](#updating-an-application) per continuare la configurazione dell'applicazione.

Se si vuole rendere l'applicazione disponibile per altre organizzazioni, è anche necessario consentire l'accesso esterno una volta aggiunta l'applicazione.

### Per registrare un'applicazione nel portale di gestione di Azure

1. Accedere al portale di gestione di Azure.

1. Scegliere l'icona Active Directory dal menu a sinistra e quindi fare clic sulla directory desiderata.

1. Nel menu in alto fare clic su Applicazioni. Se alla directory non è stata aggiunta alcuna applicazione, la pagina mostrerà solo il collegamento Aggiungi app. Fare clic sul collegamento o, in alternativa, fare clic sul pulsante Aggiungi sulla barra dei comandi.

1. Nella pagina Come procedere fare clic sul collegamento Aggiungi un'applicazione che l'organizzazione sta sviluppando.

1. Nella pagina Informazioni sull'applicazione è necessario specificare un nome per l'applicazione, nonché indicare il tipo di applicazione che si vuole registrare con Azure AD. È possibile scegliere tra un'applicazione Web e/o un'applicazione client nativa o API Web (predefinita) che rappresenta un'applicazione installata in un dispositivo come un telefono o un computer. Al termine, fare clic sull'icona della freccia nell'angolo inferiore destro della pagina.

1. Nella pagina Proprietà dell'app impostare i valori di URL accesso e URI ID app per l'applicazione Web (o solo di URI di reindirizzamento per un'applicazione client nativa), quindi fare clic sulla casella di controllo nell'angolo inferiore destro della pagina.

1. L'applicazione viene aggiunta e si viene quindi reindirizzati alla pagina Avvio rapido per l'applicazione. A seconda che si tratti di un'applicazione Web o nativa, verranno visualizzate opzioni diverse per l'aggiunta di altre funzionalità all'applicazione. Dopo aver aggiunto l'applicazione, è possibile iniziare ad aggiornarla per consentire agli utenti di effettuare l'accesso, accedere ad API Web in altre applicazioni o configurare un'applicazione multi-tenant, che consente ad altre organizzazioni di accedere all'applicazione.

>[AZURE.NOTE]Per impostazione predefinita, la registrazione delle nuove applicazioni create viene configurata per consentire agli utenti della directory di accedere all'applicazione.

## Aggiornamento di un'applicazione

Una volta registrata con Azure AD, l'applicazione potrebbe dover essere aggiornata per fornire l'accesso ad API Web, essere resa disponibile in altre organizzazioni e altro ancora. Questa sezione descrive come configurare ulteriormente l'applicazione. Per altre informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

### Panoramica del framework di consenso

Il nuovo framework di consenso di Azure AD semplifica lo sviluppo di applicazioni Web e native che devono accedere ad API Web protette da Azure AD. Tra queste API Web sono incluse l'API Graph, l'API di Office 365 e di altri servizi Microsoft, oltre alle API Web personalizzate. Il framework è basato sulla possibilità per un utente o un amministratore di fornire il consenso alla registrazione di un'applicazione nella propria directory, che può comportare l'accesso a dati di directory. Ad esempio, se un'applicazione Web deve chiamare l'API Web di Office 365 per leggere le informazioni del calendario dell'utente, l'utente dovrà fornire il consenso all'applicazione. Una volta ottenuto il consenso, l'applicazione Web potrà chiamare l'API Web di Office 365 per conto dell'utente e usare le informazioni del calendario nel modo necessario.

Il framework di consenso è basato su OAuth 2.0 e i relativi flussi diversi, come la concessione del codice di autorizzazione e delle credenziali utente, mediante client pubblici o riservati. Tramite OAuth 2.0 Azure AD consente di compilare molti tipi diversi di applicazioni client, ad esempio su un telefono, un tablet, un server o un'applicazione Web, e di ottenere l'accesso alle risorse necessarie.

Per altre informazioni sul framework di consenso, vedere [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md) e l'argomento di Office 365 relativo ad [autenticazione e autorizzazione mediante il framework di consenso comune](https://msdn.microsoft.com/library/office/dn605895(v=office.15).aspx).

#### Esempio dell'esperienza di autorizzazione

I passaggi seguenti illustrano il funzionamento dell'esperienza di consenso per lo sviluppatore e l'utente dell'applicazione.

1. Nella pagina di configurazione dell'applicazione nel portale di gestione di Azure impostare le autorizzazioni necessarie per l'applicazione usando i menu a discesa nel controllo Autorizzazioni per altre applicazioni.

    ![Autorizzazioni per altre applicazioni](./media/active-directory-integrating-applications/permissions.png)

1. Considerare che sono state aggiornate le autorizzazioni dell'applicazione, che l'applicazione è in esecuzione e che un utente sta per usarla per la prima volta. Se l'applicazione non ha già ottenuto un token di accesso o di aggiornamento, deve richiedere all'endpoint di autorizzazione di Azure AD un codice di autorizzazione da usare per acquisire il token di accesso e di aggiornamento.

1. Se l'utente non è ancora autenticato, gli verrà richiesto di accedere ad Azure AD.

    ![Accesso utente o amministratore ad Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. Dopo che l'utente ha effettuato l'accesso, Azure AD determinerà se l'utente deve essere reindirizzato a una pagina di consenso. Questa decisione dipende dal fatto che l'utente o l'amministratore dell'organizzazione abbia o meno già concesso il consenso dell'applicazione. Se il consenso non è già stato concesso, Azure AD lo richiederà all'utente e visualizzerà le autorizzazioni necessarie per il funzionamento. Il set di autorizzazioni visualizzato nella finestra di dialogo di consenso corrisponde alle impostazioni selezionate nel controllo Autorizzazioni per altre applicazioni nel portale di gestione di Azure.

    ![Esperienza di autorizzazione utente](./media/active-directory-integrating-applications/userconsent.png)

1. Dopo che l'utente concede il consenso, all'applicazione viene restituito un codice di autorizzazione, che può essere riscattato per acquisire un token di accesso e di aggiornamento. Per altre informazioni su questo flusso, vedere la sezione [Da applicazione Web ad API Web](active-directory-authentication-scenarios.md#web-application-to-web-api) in [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

### Accesso ad API Web in altre applicazioni

Tramite il framework di consenso descritto nella sezione precedente, è possibile configurare l'applicazione in modo da richiedere le autorizzazioni necessarie per l'accesso ai dati esposti dalle API Web nella directory. Per impostazione predefinita, tutte le applicazioni possono scegliere le autorizzazioni da Azure Active Directory (API Graph) e dall'API di gestione del servizio Azure con l'autorizzazione "Abilita accesso e lettura dei profili degli utenti" di Azure AD già selezionata per impostazione predefinita. Se la directory ha anche una sottoscrizione di Office 365, saranno disponibili per la selezione anche le API Web e le autorizzazioni per SharePoint ed Exchange Online. È possibile scegliere tra due tipi di autorizzazioni dal menu a discesa accanto all'API Web desiderata:

- Autorizzazioni applicazione: l'applicazione deve accedere all'API Web direttamente come se stessa (nessun contesto utente). Questo tipo di autorizzazione richiede il consenso dell'amministratore e non è disponibile per le applicazioni client native.

- Autorizzazioni delega: l'applicazione deve accedere all'API Web come utente connesso, ma con accesso limitato dall'autorizzazione selezionata. Questo tipo di autorizzazione può essere concesso da un utente, a meno che l'autorizzazione non sia configurata perché sia l'amministratore a dover concedere il consenso.

#### Per aggiungere l'accesso ad API Web in altre applicazioni

1. Accedere al portale di gestione di Azure.

1. Scegliere l'icona Active Directory dal menu a sinistra, quindi fare clic sulla directory desiderata.

1. Scegliere Applicazioni dal menu in alto e quindi fare clic sull'applicazione che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.

1. Espandere la sezione Accesso ad API Web in altre applicazioni della pagina Avvio rapido e quindi fare clic sul collegamento Configura ora nella sezione Selezionare le autorizzazioni. Verrà visualizzata la pagina delle proprietà dell'applicazione.

1. Scorrere verso il basso fino alla sezione Autorizzazioni per altre applicazioni. La prima colonna consente di selezionare le applicazioni disponibili nella directory che espongono un'API Web. Una volta selezionata l'applicazione, è possibile selezionare le autorizzazioni applicazione e di delega esposte dall'API Web.

1. Quindi, fare clic sul pulsante Salva sulla barra dei comandi.

>[AZURE.NOTE]Facendo clic sul pulsante Salva, vengono automaticamente impostate anche le autorizzazioni per l'applicazione nella directory in base a quelle configurate nella sezione Autorizzazioni per altre applicazioni. È possibile visualizzare queste autorizzazioni applicazione osservando il contenuto della scheda Proprietà di ogni applicazione.

### Esposizione di API Web ad altre applicazioni

È possibile sviluppare un'API Web e renderla disponibile ad altre organizzazioni esponendo gli ambiti di autorizzazione ad altri sviluppatori di applicazioni. Un'API Web correttamente configurata viene resa disponibile come le altre API Web Microsoft, tra cui l'API Graph e le API di Office 365. L'API Web viene resa disponibile configurando un [manifesto dell'applicazione](active-directory-application-manifest.md), ovvero un file JSON che rappresenta la configurazione dell'identità dell'applicazione. Per esporre gli ambiti di autorizzazione, è possibile passare all'applicazione nel portale di gestione di Azure e fare clic sul pulsante Manifesto applicazione sulla barra dei comandi. Per ulteriori informazioni, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md).

#### Per esporre un'API Web ad altre applicazioni

1. Accedere al portale di gestione di Azure.

1. Scegliere l'icona Active Directory dal menu a sinistra, quindi fare clic sulla directory desiderata.

1. Scegliere Applicazioni dal menu in alto e quindi fare clic sull'applicazione che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.

1. Fare clic sul pulsante Gestisci manifesto sulla barra dei comandi e selezionare Scarica manifesto.

1. Aprire il file manifesto dell'applicazione JSON e sostituire il nodo "oauth2Permissions" con il frammento di codice JSON seguente. Poiché questo frammento di codice è un esempio di come esporre un ambito di autorizzazione noto come rappresentazione dell'utente, assicurarsi di modificare il testo e i valori per la propria applicazione:

		"oauth2Permissions": [
		{
			"adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in 	user",
			"adminConsentDisplayName": "Have full access to the Todo List service",
			"id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
			"isEnabled": true,
			“origin”: “Application”
			"type": "User",
			"userConsentDescription": "Allow the application full access to the todo service on your behalf",
			"userConsentDisplayName": "Have full access to the todo service",
			"value": "user_impersonation"
			}
		],

    Il valore id deve essere un nuovo GUID generato, creato usando uno strumento di generazione di GUID o a livello di codice. Rappresenta un identificatore univoco per l'autorizzazione esposta dall'API Web. Una volta che il client è configurato in modo appropriato per richiedere l'accesso all'API Web e chiama l'API Web, presenta un token JWT OAuth 2.0 con l'attestazione scope (scp) impostata sul parametro value indicato sopra, in questo caso user\_impersonation.

	>[AZURE.NOTE]Se necessario, è possibile esporre altri ambiti di autorizzazione successivamente. Tenere presente che l'API Web può esporre più autorizzazioni associate a molte funzioni diverse. A questo punto, è possibile controllare l'accesso all'API Web usando l'attestazione scope (scp) nel token JWT OAuth 2.0 ricevuto.

1. Salvare il file JSON aggiornato e caricarlo facendo clic sul pulsante Gestisci manifesto sulla barra dei comandi, selezionando Carica manifesto, passando al file manifesto aggiornato e quindi selezionandolo. Una volta caricato il manifesto, l'API Web è configurata per essere usata da altre applicazioni nella directory.

#### Per verificare che l'API Web sia esposta ad altre applicazioni nella directory

1. Scegliere Applicazioni dal menu in alto, selezionare l'applicazione per cui si vuole configurare l'accesso all'API Web e quindi fare clic su Configura.

1. Scorrere verso il basso fino alla sezione Autorizzazioni per altre applicazioni. Fare clic sul menu a discesa Selezionare l'applicazione per selezionare l'API Web per cui è stata appena esposta un'autorizzazione. Nel menu a discesa Autorizzazioni delega selezionare la nuova autorizzazione.

![Visualizzazione di autorizzazioni Elenco azioni](./media/active-directory-integrating-applications/listpermissions.png)

#### Schema AppPermissions per file manifesto JSON dell'applicazione

La tabella seguente elenca i possibili valori per la parte oauth2Permissions del file JSON del manifesto dell'applicazione.

|Elemento|Descrizione|
|---|---|
|adminConsentDescription|Descrizione della Guida al passaggio del puntatore sulla finestra di dialogo di consenso dell'amministratore e nella pagina delle proprietà dell'app per cui è stato concesso il consenso.|
|adminConsentDisplayName|Nome descrittivo per l'autorizzazione visualizzata negli elenchi a discesa dell'ambito delle autorizzazioni applicazione e/o di delega e agli amministratori durante il consenso, nonché nella pagina delle proprietà dell'app.|
|id|Rappresenta un identificatore interno univoco per l'ambito di autorizzazione. Deve essere univoco tra tutte le autorizzazioni per l'applicazione e deve anche essere un GUID.|
|isEnabled|Quando si crea o si aggiorna un'autorizzazione OAuth2, impostare sempre questo valore su true. Se si vuole eliminare un'autorizzazione, prima di tutto è necessario impostare questo valore su false e quindi caricare il manifesto. È quindi possibile rimuovere l'autorizzazione in un successivo caricamento del manifesto.|
|origin|Riservato per utilizzi futuri. Sempre impostato su "application".|
|type|I possibili valori sono i seguenti: "User": il consenso può essere concesso dagli utenti finali; "Admin": il consenso deve essere concesso da un amministratore della società.|
|userConsentDescription|Descrizione della Guida al passaggio del puntatore sulla finestra di dialogo di consenso dell'utente.|
|userConsentDisplayName|Nome descrittivo per l'autorizzazione visualizzata agli utenti finali durante il consenso e nella pagine della proprietà dell'app nel pannello di accesso all'app.|
|value|Questo valore viene inserito nell'attestazione scp del token di accesso OAuth 2.0 se per questa particolare autorizzazione è stato concesso il consenso dall'utente. L'API Web può usare questo valore per definire l'ambito del livello di accesso dell'applicazione quando rappresenta l'utente. Questo valore non deve contenere spazi vuoti e deve essere univoco tra tutte le autorizzazioni nell'applicazione.|

### Accesso all'API Graph

Questa sezione descrive come aggiornare l'applicazione per accedere all'API Graph, associata al nome "Azure Active Directory" nell'elenco Autorizzazioni per altre applicazioni. È disponibile per impostazione predefinita per tutte le applicazioni registrate con Azure AD. Ecco le autorizzazioni che è possibile selezionare con l'API Graph:

|Nome autorizzazione|Descrizione|Tipo|
|---|---|---|
|Abilita accesso e lettura dei profili degli utenti|Consente agli utenti di accedere all'applicazione con i propri account aziendali e permette all'applicazione di leggere i profili degli utenti connessi, come l'indirizzo di posta elettronica e le informazioni di contatto.|Solo autorizzazione di delega. Il consenso può essere concesso dagli utenti.|
|Accedere alla directory dell'organizzazione|Consente all'applicazione di accedere alla directory dell'organizzazione per conto dell'utente connesso.|Solo autorizzazione di delega. Il consenso può essere concesso dagli utenti in un client nativo e solo da un amministratore per le applicazioni Web.|
|Leggi i dati della directory|Consente all'applicazione di leggere dati, ad esempio su utenti, gruppi e applicazioni, nella directory dell'organizzazione.|Autorizzazione di delega e applicazione. Il consenso deve essere concesso dall'amministratore.|
|Leggi e scrivi i dati della directory|Consente all'applicazione di leggere e scrivere dati, ad esempio su utenti e gruppi, nella directory dell'organizzazione.|Autorizzazione di delega e applicazione. Il consenso deve essere concesso dall'amministratore.|

Per gli utenti del portale di gestione di Azure, l'impostazione delle autorizzazioni applicazione Leggi i dati della directory e Leggi e scrivi i dati della directory tramite il nuovo controllo Autorizzazioni per altre applicazioni equivale all'uso della procedura guidata Gestisci accesso precedente. Per visualizzare gli ambiti di autorizzazione esposti da Office 365, vedere l'argomento relativo ad [autenticazione e autorizzazione mediante il framework di consenso comune](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

>[AZURE.NOTE]A causa di una limitazione attualmente in vigore, le applicazioni client native possono richiamare l'API Graph di Azure AD solo se usano l'autorizzazione "Accedere alla directory dell'organizzazione". Questa restrizione non è valida per le applicazioni Web.

### Configurazione di applicazioni multi-tenant

Quando si aggiunge un'applicazione ad Azure AD, potrebbe essere necessario consentire l'accesso all'applicazione solo agli utenti dell'organizzazione. In alternativa, è possibile consentire l'accesso all'applicazione anche agli utenti di organizzazioni esterne. Questi due tipi applicazione sono chiamate applicazioni single-tenant e applicazioni multi-tenant. È possibile modificare la configurazione di un'applicazione single-tenant per trasformarla in applicazione multi-tenant. La procedura viene fornita di seguito in questa sezione.

È importante notare le differenze tra un'applicazione single-tenant e una multi-tenant. Un'applicazione single-tenant è destinata all'uso in un'organizzazione. In genere si tratta di un'applicazione line-of-business scritte da uno sviluppatore aziendale. Un'applicazione con un singolo tenant deve essere accessibile solo agli utenti di una directory e quindi è necessario eseguirne il provisioning in una sola directory. Un'applicazione multi-tenant è destinata all'uso in più organizzazioni. In genere si tratta di un'applicazione SaaS (Software-as-a-Service) scritta da un fornitore di software indipendente (ISV). È necessario eseguire il provisioning delle applicazioni multi-tenant in ogni directory in cui verranno usate ed è richiesto il consenso dell'utente o dell'amministratore per registrarle.

#### Consentire agli utenti esterni di concedere l'accesso

Se si scrive un'applicazione che si vuole rendere disponibile a clienti o partner esterni all'organizzazione, è necessario aggiornare la definizione dell'applicazione nel portale di gestione di Azure.

>[AZURE.NOTE]Se si sceglie di consentire l'accesso esterno, è necessario assicurarsi che l'URI ID app dell'applicazione faccia parte di un dominio verificato. Inoltre, l'URL restituito deve iniziare con https://. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md).

##### Per abilitare l'accesso all'app per gli utenti esterni

1. Accedere al portale di gestione di Azure.

1. Scegliere l'icona Active Directory dal menu a sinistra, quindi fare clic sulla directory desiderata.

1. Scegliere Applicazioni dal menu in alto e quindi fare clic sull'applicazione che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.

1. Espandere la sezione Configura applicazione multi-tenant della pagina Avvio rapido, quindi fare clic sul collegamento Configura ora nella sezione Abilita accesso. Verrà visualizzata la pagina delle proprietà dell'applicazione.

1. Fare clic sul pulsante Sì accanto a L'applicazione è multi-tenant, quindi sul pulsante Salva sulla barra dei comandi.

Una volta apportata la modifica indicata sopra, utenti e amministratori di altre organizzazioni potranno concedere all'applicazione l'accesso alla propria directory e ad altri dati.

### Concessione dell'accesso mediante il framework di consenso

Per concedere l'accesso mediante il framework di consenso, l'applicazione client deve richiedere l'autorizzazione con OAuth 2.0. Sono disponibili [esempi di codice](https://github.com/AzureADSamples) per indicare in che modo un'applicazione Web, un'applicazione nativa o un'applicazione server/daemon richiede i codici di autorizzazione e i token di accesso per chiamare le API Web.

L'applicazione Web può offrire un'esperienza di iscrizione agli utenti. In questo caso, l'utente farà clic su un pulsante di iscrizione o di accesso che reindirizzerà il browser all'endpoint di autorizzazione OAuth2.0 di Azure AD o a un endpoint userinfo OpenID Connect. Questi endpoint consentono all'applicazione di ottenere informazioni sul nuovo utente controllando il valore id\_token.

In alternativa, l'applicazione Web può offrire anche un'esperienza che permette agli amministratori di iscrivere la propria società. Anche in questa esperienza l'utente viene reindirizzato all'endpoint di autorizzazione OAuth 2.0 di Azure AD. In questo caso, si può anche passare un parametro prompt=admin\_consent per attivare l'esperienza di consenso da parte dell'amministratore, per cui l'amministratore concederà il consenso per conto della propria organizzazione. Una volta concesso il consenso, la risposta conterrà admin\_consent=true. Quando si riscatta un token di accesso, si riceve anche un id\_token che contiene informazioni sull'organizzazione e l'amministratore che ha effettuato l'iscrizione per l'applicazione.

#### Abilitazione della concessione implicita OAuth 2.0 per le applicazioni a singola pagina

Le applicazioni a singola pagina (SPA) sono in genere strutturate con un front-end JavaScript eseguito nel browser, che chiama il back-end dell'API Web dell'applicazione per eseguirne la logica di business. Per le applicazioni a singola pagina ospitate in Azure AD, è possibile usare la concessione implicita OAuth 2.0 per autenticare l'utente con Azure AD e ottenere un token da usare per chiamate protette dal client JavaScript dell'applicazione all'API Web di back-end. Dopo che l'utente ha concesso il consenso, lo stesso protocollo di autenticazione può essere usato per ottenere token per proteggere le chiamate tra il client e altre risorse dell'API Web configurate per l'applicazione. Per impostazione predefinita, la concessione implicita OAuth 2.0 è disabilitata per le applicazioni. È possibile abilitare la concessione implicita OAuth 2.0 per l'applicazione impostando il valore `oauth2AllowImplicitFlow`" nel relativo [manifesto dell'applicazione](active-directory-application-manifest.md), ovvero un file JSON che rappresenta la configurazione dell'identità dell'applicazione.

##### Per abilitare la concessione implicita OAuth 2.0

1. Accedere al portale di gestione di Azure.
1. Scegliere l'icona **Active Directory** dal menu a sinistra, quindi fare clic sulla directory desiderata.
1. Scegliere **Applicazioni** dal menu in alto e quindi fare clic sull'applicazione che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.
1. Fare clic sul pulsante **Gestisci manifesto** sulla barra dei comandi e selezionare **Scarica manifesto**. Aprire il file manifesto JSON dell'applicazione e impostare il valore "oauth2AllowImplicitFlow" su "true". Per impostazione predefinita il valore è "false".

       "oauth2AllowImplicitFlow": true,

1. Salvare il file JSON aggiornato e caricarlo facendo clic sul pulsante **Gestisci manifesto** sulla barra dei comandi, selezionando **Carica manifesto**, passando al file manifesto aggiornato e quindi selezionandolo. Dopo il caricamento, l'API Web è ora configurata per usare la concessione implicita OAuth 2.0 per autenticare gli utenti.


### Esperienze legacy relative alla concessione dell'accesso

Questa sezione descrive l'esperienza di consenso impiegata prima del 12 marzo 2014. Questa esperienza è ancora supportata e viene descritta di seguito. Prima della nuova funzionalità, era possibile concedere l'accesso solo per queste autorizzazioni:

- Accesso degli utenti dalla propria organizzazione

- Accesso degli utenti e lettura dei dati di directory dell'organizzazione (solo come applicazione)

- Accesso degli utenti e lettura e scrittura dei dati di directory dell'organizzazione (solo come applicazione)

È possibile seguire i passaggi indicati in [Sviluppo di applicazioni Web multi-tenant con Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) per concedere l'accesso per le nuove applicazioni registrate in Azure AD. È importante notare che il nuovo framework di consenso consente applicazioni molto più potenti e permette anche agli utenti, e non solo agli amministratori, di concedere il consenso a tali applicazioni.

#### Creazione del collegamento che concede l'accesso per gli utenti esterni (legacy)

Perché utenti esterni possano iscriversi all'app usando i propri account aziendali, è necessario aggiornare l'app in modo da aggiungere un pulsante che colleghi alla pagina su Azure AD e che consenta loro di concedere l'accesso. Le indicazioni di personalizzazione per questo pulsante di iscrizione sono disponibili nell'argomento [Linee guida sulla personalizzazione per le applicazioni integrate](active-directory-branding-guidelines.md). Dopo che l'utente concede o nega l'accesso, la pagina di concessione dell'accesso di Azure AD reindirizzerà di nuovo il browser all'app con una risposta. Per altre informazioni sulle proprietà dell'applicazione, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md).

La pagina di concessione dell'accesso viene creata da Azure AD e un collegamento a questa pagina è disponibile nella pagina di configurazione dell'app nel portale di gestione. Per visualizzare la pagina di configurazione, fare clic sul collegamento Applicazioni nel menu in alto del tenant di Azure AD, fare clic sull'app che si vuole configurare e quindi fare clic su Configura nel menu in alto nella pagina Avvio rapido.

Il collegamento per l'applicazione sarà simile al seguente: `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. La tabella seguente descrive le parti del collegamento:

|Parametro|Descrizione|
|---|---|
|ClientId|Obbligatorio. ID client ottenuto durante l'aggiunta dell'app.|
|RequestedPermissions|Facoltativa. Livello di accesso richiesto dall'app, che verrà visualizzato all'utente che concede l'accesso all'app. Se non è specificato, il livello di accesso richiesto verrà automaticamente impostato solo su Single Sign-On. Le altre opzioni sono DirectoryReaders e DirectoryWriters. Per altri dettagli su questi livelli di accesso, vedere i livelli di accesso all'applicazione.|
|ConsentReturnUrl|Facoltativa. URL a cui deve essere restituita la risposta per concedere l'accesso. Questo valore deve essere codificato con URL e appartenere allo stesso dominio dell'URL di risposta configurato nella definizione dell'app. Se omesso, la risposta per concedere l'accesso verrà reindirizzata all'URL di risposta configurato.|

Specificando un ConsentReturnUrl distinto dall'URL di risposta, l'app potrà implementare una logica separata in grado di elaborare la risposta in un URL diverso dall'URL di risposta (che in genere elabora i token SAML per l'accesso). Si possono specificare anche altri parametri nell'URL codificato ConsentReturnURL, che verranno passati come parametri stringa di query all'app durante il reindirizzamento. Questo meccanismo può essere usato per mantenere informazioni aggiuntive o per collegare la richiesta dell'app per la concessione dell'accesso alla risposta di Azure AD.

#### Esperienza utente e risposta per la concessione dell'accesso (legacy)

Le immagini seguenti descrivono l'esperienza utente quando un'applicazione esegue il reindirizzamento al collegamento di concessione dell'accesso.

Se l'utente non ha già effettuato l'accesso, gli verrà richiesto di farlo:

![Accedi ad AAD](./media/active-directory-integrating-applications/signintoaad.png)

Quando l'utente viene autenticato, Azure AD lo reindirizzerà alla pagina di concessione dell'accesso:

![Concedi accesso](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE]Solo l'amministratore dell'organizzazione esterna può concedere l'accesso all'app. Se l'utente non è un amministratore della società, avrà l'opzione di scrivere all'amministratore della società per richiedere che all'app venga concesso l'accesso.

Dopo che il cliente ha concesso l'accesso per l'app facendo clic su Concedi accesso oppure se ha negato l'accesso facendo clic su Annulla, Azure AD invia una risposta a ConsentReturnUrl o all'URL di risposta configurato. La risposta conterrà i parametri seguenti:

|Parametro|Descrizione|
|---|---|
|TenantId|ID univoco dell'organizzazione in Azure AD che ha concesso l'accesso per l'applicazione. Questo parametro verrà specificato solo se il cliente ha concesso l'accesso.|
|Consenso|Il valore verrà impostato su Granted se all'app è stato concesso l'accesso oppure su Denied se la richiesta è stata rifiutata.|

Parametri aggiuntivi verranno restituiti all'app se sono stati specificati come parte dell'URL codificato ConsentReturnUrl. Di seguito viene illustrata una risposta di esempio a una richiesta di concessione dell'accesso che indica che l'applicazione è stata autorizzata e che include un ContextID incluso nella richiesta: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

>[AZURE.NOTE]La risposta di concessione dell'accesso non conterrà un token di sicurezza per l'utente e l'app deve consentire separatamente l'accesso dell'utente.

Quella che segue è una risposta di esempio a una richiesta di concessione dell'accesso rifiutata: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### Rollover delle chiavi dell'app per l'accesso ininterrotto all'API Graph (legacy)

Durante il ciclo di vita dell'app, potrebbe essere necessario modificare le chiavi usate per richiamare Azure AD per l'acquisizione di un token di accesso per chiamare l'API Graph. La modifica delle chiavi rientra in genere in due categorie: rollover di emergenza in caso di chiave danneggiata o rollover a causa dell'imminente scadenza della chiave corrente. Per fornire all'app accesso ininterrotto durante l'aggiornamento delle chiavi (prevalentemente per il secondo caso), è necessario usare la procedura seguente.

1. Nel portale di gestione di Azure fare clic sul tenant di directory, scegliere Applicazioni dal menu in alto e quindi fare clic sull'app che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.

1. Scegliere Configura dal menu in alto per visualizzare un elenco delle proprietà dell'app. Verrà visualizzato anche un elenco delle chiavi.

1. In Chiavi fare clic sull'elenco a discesa Seleziona durata, selezionare 1 anno o 2 anni e quindi fare clic su Salva sulla barra dei comandi. Viene generata una nuova chiave password per l'app. Copiare la nuova chiave password. A questo punto, sia la chiave esistente che quella nuova possono essere usate dall'app per ottenere un token di accesso da Azure AD.

1. Tornare all'app e aggiornare la configurazione per iniziare a usare la nuova chiave password. Per un esempio del punto in cui eseguire questo aggiornamento, vedere [Uso dell'API Graph per eseguire query su Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx).

1. È ora necessario implementare questa modifica in tutto l'ambiente di produzione, verificandola su un solo nodo del servizio prima di implementarla completamente.

1. Al termine dell'aggiornamento nella distribuzione di produzione, è possibile tornare al portale di gestione di Azure per rimuovere la chiave precedente.

#### Modifica delle proprietà dell'app dopo l'abilitazione dell'accesso (legacy)

Una volta consentito l'accesso per gli utenti esterni all'app, è comunque possibile apportare modifiche alle proprietà dell'app nel portale di gestione di Azure. Tuttavia, se i clienti hanno concesso l'accesso all'app prima che fossero apportate modifiche all'app, quando visualizzeranno i dettagli sull'app nel portale di gestione di Azure, le modifiche non risulteranno applicate. Una volta resa disponibile l'app ai clienti, è necessario prestare particolare attenzione quando si apportano determinate modifiche. Se, ad esempio, si aggiorna l'URI ID app, i clienti esistenti che hanno concesso l'accesso prima di questa modifica non potranno accedere all'app con gli account aziendale o dell'istituto di istruzione.

Se si apporta una modifica a RequestedPermissions per richiedere un livello di accesso maggiore, i clienti esistenti che usano la funzionalità dell'app che sfrutta ora chiamate a nuove API con questo livello di accesso maggiore potrebbero ricevere una risposta di accesso negato dall'API Graph. L'app deve gestire questi casi e chiedere al cliente di concedere l'accesso all'app con la richiesta di un livello di accesso maggiore.

## Rimozione di un'applicazione

In questa sezione viene descritto come rimuovere un'applicazione dalla directory per applicazioni single-tenant e multi-tenant.

### Per rimuovere un'applicazione single-tenant dalla directory

1. Accedere al portale di gestione di Azure.

1. Scegliere l'icona Active Directory dal menu a sinistra e quindi fare clic sulla directory desiderata.

1. Scegliere Applicazioni dal menu in alto e quindi fare clic sull'applicazione che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.

1. Fare clic sul pulsante Elimina nella barra dei comandi.

1. Nel messaggio di conferma fare clic su Sì.

### Per rimuovere un'applicazione multi-tenant dalla directory

1. Accedere al portale di gestione di Azure.

1. Scegliere l'icona Active Directory dal menu a sinistra e quindi fare clic sulla directory desiderata.

1. Scegliere Applicazioni dal menu in alto e quindi fare clic sull'applicazione che si vuole configurare. Verrà visualizzata la pagina Avvio rapido, con informazioni su Single Sign-On e altre informazioni di configurazione.

1. Nella sezione L'applicazione è multi-tenant fare clic su No. L'applicazione viene convertita in applicazione single-tenant, ma resta nell'organizzazione che ne ha già concesso il consenso.

1. Fare clic sul pulsante Elimina nella barra dei comandi.

1. Nel messaggio di conferma fare clic su Sì.

Un amministratore della società che vuole rimuovere l'accesso di un'app alla directory (dopo avere concesso il consenso) deve avere una sottoscrizione di Azure per rimuovere l'accesso tramite il portale di gestione di Azure. In alternativa, l'amministratore della società può usare i [cmdlet di PowerShell per Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) per rimuovere l'accesso.

## Passaggi successivi

- Vedere [Linee guida sulla personalizzazione per le app integrate](active-directory-branding-guidelines.md)

- Informazioni su [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md)

- Informazioni sul [manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md)

- Vedere la [Guida per gli sviluppatori di Active Directory](active-directory-developer's guide.md)

<!---HONumber=Oct15_HO3-->