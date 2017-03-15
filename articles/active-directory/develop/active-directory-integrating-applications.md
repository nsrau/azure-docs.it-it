---
title: Integrazione di applicazioni con Azure Active Directory | Documentazione Microsoft
description: Informazioni dettagliate su come aggiungere, aggiornare o rimuovere un&quot;applicazione in Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: mbaldwin;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 57383c11682342cb0a6446c79e603843a698fc8c
ms.openlocfilehash: 835e1c494de59576fd8ac529240729cb33eaa50b
ms.lasthandoff: 03/01/2017


---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrazione di applicazioni con Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Gli sviluppatori aziendali e i provider SaaS possono sviluppare servizi cloud commerciali o applicazioni line-of-business che possono essere integrate con Azure Active Directory (Azure AD) per garantire la sicurezza dell'accesso e delle autorizzazioni per i servizi. Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare i dettagli dell'applicazione con Azure AD nel portale di Azure classico.

Questo articolo mostra come aggiungere, aggiornare o rimuovere un'applicazione in Azure AD. Vengono indicati i diversi tipi di applicazioni che possono essere integrate con Azure AD e vengono fornite le informazioni su come configurare le applicazioni per accedere ad altre risorse, come le API Web e altro ancora.

Per altre informazioni sui due oggetti Azure AD che rappresentano un'applicazione registrata e la relazione tra di essi, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md). Per altre informazioni sulle linee guida sulla personalizzazione da usare quando si sviluppano applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione per le app integrate](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Aggiunta di un'applicazione
Qualsiasi applicazione che vuole usare le funzionalità di Azure AD deve prima essere registrata in un tenant di Azure AD. Questo processo di registrazione comporta l'assegnazione ad Azure AD dei dettagli sull'applicazione, come l'URL in cui è disponibile, l'URL per inviare le risposte dopo l'autenticazione di un utente, l'URI che identifica l'app e così via.

Per creare un'applicazione Web che richiede solo il supporto dell'accesso per gli utenti in Azure AD, è sufficiente seguire le istruzioni riportate di seguito. Se l'applicazione necessita di credenziali o autorizzazioni per accedere a un'API Web o se deve consentire agli utenti di altri tenant di Azure AD di accedervi, passare alla sezione [Aggiornamento di un'applicazione](#updating-an-application) per continuare la configurazione dell'applicazione.

### <a name="to-register-a-new-application-in-the-azure-portal"></a>Per registrare una nuova applicazione nel Portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Nel riquadro di spostamento a sinistra scegliere **More Services** (Altri servizi), fare clic su **Registrazioni per l'app** e quindi su **Aggiungi**.
4. Seguire le istruzioni e creare una nuova applicazione. Per ottenere esempi specifici per applicazioni Web o per applicazioni native, vedere le [Guide introduttive](active-directory-developers-guide.md).
  * Per le applicazioni Web fornire l'**URL accesso**, ovvero l'URL di base dell'app con cui gli utenti possono accedere, ad esempio `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Per le applicazioni native fornire un **URI di reindirizzamento**, che Azure AD usa per restituire le risposte dei token. Immettere un valore specifico per l'applicazione in uso, ad esempio `http://MyFirstAADApp`
5. Dopo avere completato la registrazione, Azure AD assegna all'applicazione un identificatore client univoco, l'ID applicazione. L'applicazione viene aggiunta e si viene quindi reindirizzati alla pagina Avvio rapido per l'applicazione. A seconda che si tratti di un'applicazione Web o nativa, verranno visualizzate opzioni diverse per l'aggiunta di altre funzionalità all'applicazione. Dopo aver aggiunto l'applicazione, è possibile iniziare ad aggiornarla per consentire agli utenti di effettuare l'accesso, accedere ad API Web in altre applicazioni o configurare un'applicazione multi-tenant, che consente ad altre organizzazioni di accedere all'applicazione.

> [!NOTE]
> Per impostazione predefinita, la registrazione delle nuove applicazioni create viene configurata per consentire agli utenti della directory di accedere all'applicazione.
> 
> 

## <a name="updating-an-application"></a>Aggiornamento di un'applicazione
Una volta registrata con Azure AD, l'applicazione potrebbe dover essere aggiornata per fornire l'accesso ad API Web, essere resa disponibile in altre organizzazioni e altro ancora. In questa sezione sono descritte le varie configurazioni aggiuntive dell'applicazione che potrebbero rendersi necessarie. Per prima cosa verrà presentata una panoramica del framework di consenso, importante per capire se le applicazioni delle API/risorse in fase di compilazione verranno utilizzate dalle applicazioni client compilate dagli sviluppatori della propria organizzazione o di un'altra.

Per altre informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

### <a name="overview-of-the-consent-framework"></a>Panoramica del framework di consenso
Il framework di consenso di Azure AD semplifica lo sviluppo di applicazioni client Web e native multi-tenant che devono accedere ad API Web protette da un tenant di Azure AD, diverso da quello in cui l'applicazione client viene registrata. Tra queste API Web sono incluse l'API di Microsoft Graph (per l'accesso ad Azure Active Directory, Intune e ai servizi di Office 365) e le API di altri servizi Microsoft, oltre alle API Web personalizzate. Il framework è basato sulla possibilità per un utente o un amministratore di fornire il consenso alla richiesta di registrazione di un'applicazione nella propria directory, che può comportare l'accesso a dati di directory.

Ad esempio, se un'applicazione client Web deve leggere le informazioni del calendario dell'utente da Office 365, l'utente dovrà fornire il consenso all'applicazione client. Una volta ottenuto il consenso, l'applicazione client potrà chiamare l'API di Microsoft Graph per conto dell'utente e usare le informazioni del calendario nel modo necessario. L'[API di Microsoft Graph](https://graph.microsoft.io) fornisce l'accesso ai dati in Office 365, ad esempio calendari e messaggi di Exchange, siti ed elenchi di SharePoint, documenti di OneDrive, blocchi appunti di OneNote, attività di Pianificazione, cartelle di lavoro di Excel e così via; fornisce anche l'accesso agli utenti e gruppi di Azure AD e ad altri oggetti di dati da più servizi cloud di Microsoft. 

Il framework di consenso è basato su OAuth 2.0 e i relativi flussi diversi, come la concessione del codice di autorizzazione e delle credenziali utente, mediante client pubblici o riservati. Tramite OAuth 2.0 Azure AD consente di compilare molti tipi diversi di applicazioni client, ad esempio su un telefono, un tablet, un server o un'applicazione Web, e di ottenere l'accesso alle risorse necessarie.

Per informazioni più dettagliate sul framework di consenso, vedere [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md) e per altre informazioni su come ottenere l'accesso ad Office 365 usando Microsoft Graph, vedere [App authentication with Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview) (Autenticazione di app con Microsoft Graph).

#### <a name="example-of-the-consent-experience"></a>Esempio dell'esperienza di autorizzazione
I passaggi seguenti illustrano il funzionamento dell'esperienza di consenso per lo sviluppatore e l'utente dell'applicazione.

1. Nella pagina di configurazione dell'applicazione client Web nel Portale di Azure impostare le autorizzazioni richieste dall'applicazione usando i menu nella sezione Autorizzazioni necessarie.
   
    ![Autorizzazioni per altre applicazioni](./media/active-directory-integrating-applications/requiredpermissions.png)
2. Considerare che sono state aggiornate le autorizzazioni dell'applicazione, che l'applicazione è in esecuzione e che un utente sta per usarla per la prima volta. Se l'applicazione non ha già ottenuto un token di accesso o di aggiornamento, deve richiedere all'endpoint di autorizzazione di Azure AD un codice di autorizzazione da usare per acquisire il token di accesso e di aggiornamento.
3. Se l'utente non è ancora autenticato, gli verrà richiesto di accedere ad Azure AD.
   
    ![Accesso utente o amministratore ad Azure AD](./media/active-directory-integrating-applications/usersignin.png)
4. Dopo che l'utente ha effettuato l'accesso, Azure AD determinerà se l'utente deve essere reindirizzato a una pagina di consenso. Questa decisione dipende dal fatto che l'utente o l'amministratore dell'organizzazione abbia o meno già concesso il consenso dell'applicazione. Se il consenso non è già stato concesso, Azure AD lo richiederà all'utente e visualizzerà le autorizzazioni necessarie per il funzionamento. Il set di autorizzazioni visualizzato nella finestra di dialogo di consenso corrisponde alle impostazioni selezionate in Autorizzazioni delegate nel Portale di Azure.
   
    ![Esperienza di autorizzazione utente](./media/active-directory-integrating-applications/consent.png)
5. Dopo che l'utente concede il consenso, all'applicazione viene restituito un codice di autorizzazione, che può essere riscattato per acquisire un token di accesso e di aggiornamento. Per altre informazioni su questo flusso, vedere la sezione [Da applicazione Web ad API Web](active-directory-authentication-scenarios.md#web-application-to-web-api) di [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

6. In qualità di amministratore, è possibile inoltre consentire le autorizzazioni delegate di un'applicazione per conto di tutti gli utenti nel proprio tenant. Ciò impedirà che la finestra di dialogo di consenso sia visualizzata per ogni utente nel tenant. È possibile eseguire questa operazione dal [Portale di Azure](https://portal.azure.com) dalla pagina dell'applicazione. Dal pannello **Impostazioni** dell'applicazione selezionare **Autorizzazioni necessarie** e fare clic sul pulsante **Concedi autorizzazioni**. 

    ![Concedere le autorizzazioni per il consenso esplicito dell'amministratore](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> La concessione del consenso esplicito tramite il pulsante **Concedi autorizzazioni** è attualmente necessaria per applicazioni a singola pagina (SPA) che usano ADAL. js, poiché il token di accesso viene richiesto senza una richiesta di consenso, che avrà esito negativo se non è già stata concessa l'autorizzazione.   

### <a name="configuring-a-client-application-to-access-web-apis"></a>Configurazione di un'applicazione client per accedere alle API Web
Per consentire a un'applicazione Web/client riservato di partecipare a un flusso di concessioni di autorizzazioni che richiede l'autenticazione (e ottenere un token di accesso), è necessario definire credenziali protette. Il metodo di autenticazione predefinito supportato dal portale di Azure è ID client + chiave simmetrica. Questa sezione illustra i passaggi di configurazione necessari per fornire alla chiave privata le credenziali del client.

Inoltre, prima che un client possa accedere a un'API Web esposta da un'applicazione della risorsa (ad esempio, l'API di Microsoft Graph), il framework di consenso assicurerà che il client ottenga la concessione delle autorizzazioni necessaria, in base alle autorizzazioni richieste. Per impostazione predefinita, tutte le applicazioni possono scegliere le autorizzazioni da Azure Active Directory (API Graph) e dall'API di gestione del servizio Azure con l'autorizzazione "Abilita accesso e lettura dei profili degli utenti" di Azure AD già selezionata per impostazione predefinita. Se l'applicazione client viene registrata in un tenant di Azure AD per Office 365, saranno disponibili per la selezione anche le API Web e le autorizzazioni per SharePoint ed Exchange Online. È possibile scegliere tra [due tipi di autorizzazioni](active-directory-dev-glossary.md#permissions) disponibili nel menu a discesa accanto all'API Web desiderata:

* Autorizzazioni applicazione: l'applicazione client deve accedere direttamente all'API Web come se stessa (nessun contesto utente). Questo tipo di autorizzazione richiede il consenso dell'amministratore e non è disponibile per le applicazioni client native.
* Autorizzazioni delegate: l'applicazione client deve accedere all'API Web come utente connesso, ma con accesso limitato dall'autorizzazione selezionata. Questo tipo di autorizzazione può essere concesso da un utente, a meno che l'autorizzazione non sia configurata perché sia l'amministratore a dover concedere il consenso. 

> [!NOTE]
> L'aggiunta di un'autorizzazione delegata a un'applicazione non concede automaticamente il consenso all'utente all'interno del tenant, come accadeva nel portale di Azure classico. Gli utenti devono comunque concedere manualmente il consenso per le autorizzazioni delegate aggiuntive al runtime, a meno che l'amministratore non faccia clic sul pulsante **Concedi autorizzazioni** dalla sezione **Autorizzazioni necessarie** della pagina dell'applicazione nel Portale di Azure. 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Per aggiungere credenziali o autorizzazioni per accedere alle API Web
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Scegliere **Azure Active Directory**dal menu in alto, fare clic su **Registrazioni per l'app** e quindi sull'applicazione da configurare. In questo modo sarà visualizzata la pagina di avvio rapido dell'applicazione, nonché il pannello Impostazioni per l'applicazione.
4. Per aggiungere una chiave privata per le credenziali dell'applicazione Web, fare clic sulla sezione "Chiavi" nel pannello Impostazioni.  
   
   * Aggiungere una descrizione per la chiave e selezionare una durata di 1 o 2 anni. 
   * Dopo che le modifiche apportate alla configurazione sono state salvate, il valore della chiave viene visualizzato nella colonna di destra. Accertarsi di tornare a questa sezione e di copiarne il contenuto dopo il salvataggio, così da avere i dati disponibili nell'applicazione client durante l'autenticazione in fase di esecuzione.
5. Per aggiungere autorizzazioni per accedere alle API delle risorse dal client, fare clic sulla sezione "Autorizzazioni necessarie" nel pannello Impostazioni. 
   
   * Innanzitutto, fare clic sul pulsante "Aggiungi".
   * Fare clic su "Selezionare un'API" per visualizzare il tipo di risorse da cui scegliere.
   * Scorrere l'elenco delle API disponibili o usare la casella di ricerca per selezionare fra le applicazioni di risorse disponibili nella directory che espongono un'API Web. Fare clic sulla risorsa desiderata, quindi scegliere **Seleziona**.
   * Quando è stata effettuata la selezione, è possibile spostarsi al menu **Selezionare le autorizzazioni** in cui scegliere le autorizzazioni per l'applicazione e le autorizzazioni delegate per l'applicazione.
   
6. Al termine, scegliere il pulsante **Fine**.

> [!NOTE]
> Facendo clic sul pulsante **Fine**, vengono automaticamente impostate anche le autorizzazioni per l'applicazione nella directory in base a quelle configurate per altre applicazioni.  È possibile visualizzare queste autorizzazioni per l'applicazione osservando il contenuto del pannello **Impostazioni** dell'applicazione stessa.
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurazione di un'applicazione della risorsa per esporre le API Web
È possibile sviluppare un'API Web e renderla disponibile alle applicazioni client esponendo gli [ambiti](active-directory-dev-glossary.md#scopes) e i [ruoli](active-directory-dev-glossary.md#roles) di accesso. Un'API Web correttamente configurata viene resa disponibile come le altre API Web Microsoft, tra cui l'API Graph e le API di Office 365. Gli ambiti e i ruoli di accesso vengono esposti con il [manifesto dell'applicazione](active-directory-dev-glossary.md#application-manifest), ovvero un file JSON che rappresenta la configurazione dell'identità dell'applicazione.  

La sezione seguente illustra come esporre gli ambiti di accesso modificando il manifesto dell'applicazione della risorsa.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Aggiunta di ambiti di accesso all'applicazione della risorsa
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Scegliere **Azure Active Directory**dal menu in alto, fare clic su **Registrazioni per l'app** e quindi sull'applicazione da configurare. In questo modo sarà visualizzata la pagina di avvio rapido dell'applicazione, nonché il pannello Impostazioni per l'applicazione.
4. Fare clic su **Manifesto** nella pagina dell'applicazione per aprire l'editor manifesto incorporato. 
5. Sostituire il nodo "oauth2Permissions" con il frammento di codice JSON seguente. Questo frammento di codice è un esempio di come esporre un ambito noto come "rappresentazione dell'utente", che consente a un proprietario di risorse di assegnare a un'applicazione client un tipo di accesso delegato a una risorsa. Verificare di avere modificato il testo e i valori per la propria applicazione:
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    Il valore Il valore ID deve essere un nuovo GUID generato, creato utilizzando uno [strumento di generazione di GUID](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) o a livello di codice. Rappresenta un identificatore univoco per l'autorizzazione esposta dall'API Web. Una volta che il client è configurato in modo appropriato per richiedere l'accesso all'API Web e chiama l'API Web, presenta un token JWT OAuth 2.0 con l'attestazione scope (scp) impostata sul parametro value indicato sopra, in questo caso user_impersonation.
   
   > [!NOTE]
   > Se necessario, è possibile esporre altri ambiti successivamente. Tenere presente che l'API Web può esporre più ambiti associati a molte funzioni diverse. A questo punto, è possibile controllare l'accesso all'API Web usando l'attestazione scope (scp) nel token JWT OAuth 2.0 ricevuto.
   > 
   > 
6. Fare clic su **Salva** per salvare il manifesto. L'API Web è ora configurata in modo da essere usata da altre applicazioni nella directory.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>Per verificare che l'API Web sia esposta ad altre applicazioni nella directory
1. Scegliere **Registrazioni per l'app** dal menu in alto, selezionare l'applicazione client per cui si vuole configurare l'accesso all'API Web e quindi passare al pannello Impostazioni.
2. Dalla sezione **Autorizzazioni necessarie** selezionare l'API Web per la quale è stata appena esposta un'autorizzazione. Nel menu a discesa Autorizzazioni delegate selezionare la nuova autorizzazione.

![Visualizzazione di autorizzazioni Elenco azioni](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>Altre informazioni sul manifesto dell'applicazione
Il manifesto dell'applicazione è in realtà un meccanismo per l'aggiornamento dell'entità applicazione, che definisce tutti gli attributi della configurazione dell'identità di un'applicazione Azure AD, inclusi gli ambiti di accesso API di cui si è parlato sopra. Per altre informazioni sull'entità applicazione, vedere la [documentazione sull'entità applicazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), che include informazioni di riferimento complete sui membri dell'entità applicazione usati per specificare le autorizzazioni per l'API:  

* Il membro appRoles, che è una raccolta di entità [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) che possono essere usate per definire le **Autorizzazioni applicazione** per un'API Web  
* Il membro oauth2Permissions, che è una raccolta di entità [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) che possono essere usate per definire le **Autorizzazioni delegate** per un'API Web

Per altre informazioni sui concetti generali relativi al manifesto dell'applicazione, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Accesso ad Azure AD Graph e Office 365 usando le API di Microsoft Graph  
Come accennato in precedenza, oltre a esporre/accedere alle API nelle applicazioni della risorsa, è possibile aggiornare l'applicazione client per accedere alle API esposte dalle risorse Microsoft.  L'API di Microsoft Graph, chiamata "Microsoft Graph" nell'elenco di autorizzazioni per le altre applicazioni, è disponibile per tutte le applicazioni registrate con Azure AD. Se si sta registrando l'applicazione client in un tenant di Azure AD di cui è stato effettuato il provisioning da Office 365, è anche possibile accedere a tutte le autorizzazioni esposte dall'API di Microsoft Graph per diverse risorse di Office 365.

Per informazioni dettagliate sugli ambiti di accesso esposti dall'API di Microsoft Graph, vedere l'articolo [Ambiti di autorizzazione | Concetti relativi all'API Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

> [!NOTE]
> A causa di una limitazione attualmente in vigore, le applicazioni client native possono richiamare l'API Graph di Azure AD solo se usano l'autorizzazione "Accedere alla directory dell'organizzazione".  Questa restrizione non è valida per le applicazioni Web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configurazione di applicazioni multi-tenant
Quando si aggiunge un'applicazione ad Azure AD, potrebbe essere necessario consentire l'accesso all'applicazione solo agli utenti dell'organizzazione. In alternativa, è possibile consentire l'accesso all'applicazione anche agli utenti di organizzazioni esterne. Questi due tipi applicazione sono chiamate applicazioni single-tenant e applicazioni multi-tenant. È possibile modificare la configurazione di un'applicazione single-tenant per trasformarla in applicazione multi-tenant. La procedura viene fornita di seguito in questa sezione.

È importante notare le differenze tra un'applicazione single-tenant e una multi-tenant:  

* Un'applicazione single-tenant è destinata all'uso in un'organizzazione. In genere si tratta di un'applicazione line-of-business scritte da uno sviluppatore aziendale. Un'applicazione con un singolo tenant deve essere accessibile solo agli utenti di una directory e quindi è necessario eseguirne il provisioning in una sola directory.
* Un'applicazione multi-tenant è destinata all'uso in più organizzazioni. Si tratta di un'applicazione Web SaaS (Software-as-a-Service) scritta in genere da un fornitore di software indipendente (ISV). È necessario effettuare il provisioning delle applicazioni multi-tenant in ogni directory in cui verranno usate ed è richiesto il consenso dell'utente o dell'amministratore per registrarle, supportato dal framework di consenso di Azure AD. Si noti che, per impostazione predefinita, tutte le applicazioni client native sono multi-tenant perché sono installate nel dispositivo del proprietario della risorsa. Vedere sopra la sezione Panoramica del framework di consenso per altri dettagli sul framework di consenso.

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>Abilitazione degli utenti esterni a concedere l'accesso alle proprie risorse da parte dell'applicazione
Se si scrive un'applicazione che si vuole rendere disponibile a clienti o partner esterni all'organizzazione, è necessario aggiornare la definizione dell'applicazione nel Portale di Azure.

> [!NOTE]
> Se si abilita il multi-tenant, è necessario assicurarsi che l'URI ID app dell'applicazione faccia parte di un dominio verificato. Inoltre, l'URL restituito deve iniziare con https://. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md).
> 
> 

Per abilitare l'accesso all'app per gli utenti esterni: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Scegliere **Azure Active Directory**dal menu in alto, fare clic su **Registrazioni per l'app** e quindi sull'applicazione da configurare. In questo modo sarà visualizzata la pagina di avvio rapido dell'applicazione, nonché il pannello Impostazioni per l'applicazione.
4. Dal pannello Impostazioni fare clic su **Proprietà** e impostare **Multi-tenant** su **Sì**.

Una volta apportata la modifica indicata sopra, utenti e amministratori di altre organizzazioni potranno concedere all'applicazione l'accesso alla propria directory e ad altri dati.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>Attivazione del framework di consenso di Azure AD in fase di esecuzione
Per usare il framework di consenso, l'applicazione client multi-tenant deve richiedere l'autorizzazione con OAuth 2.0. Sono disponibili [esempi di codice](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) per indicare in che modo un'applicazione Web, un'applicazione nativa o un'applicazione server/daemon richiede i codici di autorizzazione e i token di accesso per chiamare le API Web.

L'applicazione Web può offrire anche un'esperienza di iscrizione agli utenti. In questo caso, l'utente farà clic su un pulsante di iscrizione che reindirizzerà il browser all'endpoint di autorizzazione OAuth2.0 di Azure AD o a un endpoint userinfo OpenID Connect. Questi endpoint consentono all'applicazione di ottenere informazioni sul nuovo utente controllando il valore id_token. Durante la fase di iscrizione all'utente verrà presentata una richiesta di consenso simile a quella visualizzata sopra nella sezione Panoramica del framework di consenso.

In alternativa, l'applicazione Web può offrire anche un'esperienza che permette agli amministratori di iscrivere la propria società. Anche in questa esperienza l'utente viene reindirizzato all'endpoint di autorizzazione OAuth 2.0 di Azure AD. In questo caso, si passa un parametro prompt=admin_consent all'endpoint di autorizzazione per forzare l'esperienza di consenso da parte dell'amministratore, per cui l'amministratore concederà il consenso per conto della propria organizzazione. Solo un utente che esegue l'autenticazione con un account che appartiene al ruolo Amministratore globale può fornire il consenso. Gli altri riceveranno un errore. Una volta concesso il consenso, la risposta conterrà admin_consent=true. Quando si riscatta un token di accesso, si riceve anche un id_token che contiene informazioni sull'organizzazione e l'amministratore che ha effettuato l'iscrizione per l'applicazione.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Abilitazione della concessione implicita OAuth 2.0 per le applicazioni a singola pagina
Le applicazioni a singola pagina (SPA) sono in genere strutturate con un front-end JavaScript eseguito nel browser, che chiama il back-end dell'API Web dell'applicazione per eseguirne la logica di business. Per le applicazioni a singola pagina ospitate in Azure AD, è possibile usare la concessione implicita OAuth 2.0 per autenticare l'utente con Azure AD e ottenere un token da usare per chiamate protette dal client JavaScript dell'applicazione all'API Web di back-end. Dopo che l'utente ha concesso il consenso, lo stesso protocollo di autenticazione può essere usato per ottenere token per proteggere le chiamate tra il client e altre risorse dell'API Web configurate per l'applicazione. Per altre informazioni sulla concessione di autorizzazione implicita e per stabilire se sia adatta allo scenario della propria applicazione, vedere [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory (AD) ](active-directory-dev-understanding-oauth2-implicit-grant.md).

Per impostazione predefinita, la concessione implicita OAuth 2.0 è disabilitata per le applicazioni. È possibile abilitare la concessione implicita OAuth 2.0 per l'applicazione impostando il valore `oauth2AllowImplicitFlow` nel relativo [manifesto dell'applicazione](active-directory-application-manifest.md), ovvero un file JSON che rappresenta la configurazione dell'identità dell'applicazione.

#### <a name="to-enable-oauth-20-implicit-grant"></a>Per abilitare la concessione implicita OAuth 2.0
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Scegliere **Azure Active Directory**dal menu in alto, fare clic su **Registrazioni per l'app** e quindi sull'applicazione da configurare. In questo modo sarà visualizzata la pagina di avvio rapido dell'applicazione, nonché il pannello Impostazioni per l'applicazione.
4. Nella pagina dell'applicazione fare clic su **Manifesto** per aprire l'editor manifesto incorporato.
   Individuare e impostare il valore "oauth2AllowImplicitFlow" su "true". Per impostazione predefinita il valore è "false".
   
    `"oauth2AllowImplicitFlow": true,`
5. Salvare il manifesto aggiornato. Dopo il salvataggio, l'API Web è configurata in modo da usare la concessione implicita OAuth 2.0 per autenticare gli utenti.


## <a name="removing-an-application"></a>Rimozione di un'applicazione
Questa sezione descrive come rimuovere un'applicazione dal tenant di Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Rimozione di un'applicazione autorizzata dall'organizzazione
Si tratta delle applicazioni visualizzate nel filtro "Applicazioni di proprietà dell'azienda" nella pagina "Applicazioni" principale per il tenant di Azure AD. In termini tecnici, sono applicazioni registrate manualmente nel portale di Azure classico o a livello di codice con PowerShell o l'API Graph. Più precisamente, sono rappresentate sia da un oggetto applicazione che da un oggetto entità servizio nel tenant. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md) .

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Per rimuovere un'applicazione single-tenant dalla directory
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Scegliere **Azure Active Directory**dal menu in alto, fare clic su **Registrazioni per l'app** e quindi sull'applicazione da configurare. In questo modo sarà visualizzata la pagina di avvio rapido dell'applicazione, nonché il pannello Impostazioni per l'applicazione.
4. Nella pagina dell'applicazione fare clic su **Elimina**.
5. Nel messaggio di conferma fare clic su **Sì** .

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>Per rimuovere un'applicazione multi-tenant dalla directory
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Scegliere **Azure Active Directory**dal menu in alto, fare clic su **Registrazioni per l'app** e quindi sull'applicazione da configurare. In questo modo sarà visualizzata la pagina di avvio rapido dell'applicazione, nonché il pannello Impostazioni per l'applicazione.
4. Nel pannello Impostazioni scegliere **Proprietà** e impostare **Multi-tenant** su **No**. L'applicazione viene convertita in applicazione single-tenant, ma resta nell'organizzazione che ne ha già concesso il consenso.
5. Fare clic sul pulsante **Elimina** dalla pagina dell'applicazione.
6. Nel messaggio di conferma fare clic su **Sì** .

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Rimozione di un'applicazione multi-tenant autorizzata da un'altra organizzazione
Si tratta di un subset di applicazioni visualizzate nel filtro "Applicazioni usate dall'azienda" nella pagina "Applicazioni" principale per il tenant di Azure AD, in particolare quelle non elencate in "Applicazioni di proprietà dell'azienda". In termini tecnici, sono applicazioni multi-tenant registrate durante il processo di consenso. Più precisamente, sono rappresentate solo da un oggetto entità servizio. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md) .

Per rimuovere l'accesso di un'applicazione multi-tenant alla directory (dopo avere concesso il consenso), l'amministratore della società deve avere una sottoscrizione di Azure per rimuovere l'accesso tramite il Portale di Azure. In alternativa, l'amministratore della società può usare i [cmdlet di PowerShell per Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) per rimuovere l'accesso.

## <a name="next-steps"></a>Passaggi successivi
* Per suggerimenti sulle indicazioni visive per l'applicazione, vedere [Linee guida sulla personalizzazione per le applicazioni](active-directory-branding-guidelines.md).
* Per altri dettagli sulla relazione tra gli oggetti applicazione e gli oggetti entità servizio di un'applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](active-directory-application-objects.md).
* Per informazioni sul ruolo svolto dal manifesto dell'applicazione, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md).
* Per le definizioni di alcuni concetti di Azure Active Directory (AD) fondamentali per gli sviluppatori, vedere il [Glossario per gli sviluppatori di Azure Active Directory](active-directory-dev-glossary.md).
* Per una panoramica di tutti i contenuti correlati allo sviluppo, visitare la [Guida per gli sviluppatori di Active Directory](active-directory-developers-guide.md).


