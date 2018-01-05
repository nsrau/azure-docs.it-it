---
title: Integrazione di applicazioni con Azure Active Directory
description: Come aggiungere, aggiornare o rimuovere un'applicazione in Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: 
author: PatAltimore
manager: mtillman
editor: mbaldwin
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: e398536ff6f660c75e4e063040eab33a831d65c6
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrazione di applicazioni con Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Gli sviluppatori aziendali e i provider SaaS possono sviluppare servizi cloud commerciali o applicazioni line-of-business che possono essere integrate con Azure Active Directory (Azure AD) per garantire la sicurezza dell'accesso e delle autorizzazioni per i loro servizi. Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare l'applicazione con Azure AD.

Questo articolo mostra come aggiungere, aggiornare o rimuovere la registrazione di un'applicazione in Azure AD. Vengono indicati i diversi tipi di applicazioni che possono essere integrate con Azure AD e vengono fornite le informazioni su come configurare le applicazioni per accedere ad altre risorse, come le API Web e altro ancora.

Per altre informazioni sui due oggetti Azure AD che rappresentano un'applicazione registrata e la relazione tra di essi, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md). Per altre informazioni sulle linee guida sulla personalizzazione da usare quando si sviluppano applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione per le app integrate](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Aggiunta di un'applicazione
Qualsiasi applicazione che vuole usare le funzionalità di Azure AD deve prima essere registrata in un tenant di Azure AD. Questo processo di registrazione comporta l'assegnazione ad Azure AD dei dettagli sull'applicazione, come l'URL in cui è disponibile, l'URL per inviare le risposte dopo l'autenticazione di un utente, l'URI che identifica l'app e così via.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Per registrare una nuova applicazione mediante il portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** e fare clic su **Registrazione nuova applicazione**.

   ![Registrare una nuova applicazione](./media/active-directory-integrating-applications/add-app-registration.png)

4. Quando viene visualizzata la pagina **Crea**, immettere le informazioni di registrazione dell'applicazione: 

  - **Nome:** immettere un nome significativo per l'applicazione
  - **Tipo di applicazione:** 
    - Selezionare "Nativa" per le [applicazioni client](active-directory-dev-glossary.md#client-application) che sono installate localmente in un dispositivo. Questa impostazione viene usata per i [client nativi](active-directory-dev-glossary.md#native-client) OAuth pubblici.
    - Selezionare "App/API Web" per le [applicazioni client](active-directory-dev-glossary.md#client-application) e le [applicazioni della risorsa/API](active-directory-dev-glossary.md#resource-server) installate su un server protetto. Questa impostazione viene utilizzata per i [client Web](active-directory-dev-glossary.md#web-client) OAuth riservati e i [client basati su agente utente](active-directory-dev-glossary.md#user-agent-based-client) pubblici. La stessa applicazione può anche esporre sia un'API client che un'API di risorse.
  - **URL Sign-On:** per le applicazioni "App/API Web", specificare l'URL di base dell'app. Ad esempio, `http://localhost:31544` potrebbe essere l'URL per un'app Web eseguita sul computer locale. Gli utenti possono usare questo URL per accedere a un'applicazione client Web. 
  - **URI di reindirizzamento**: per le applicazioni "native" fornire l'URI utilizzato da Azure AD per restituire le risposte dei token. Immettere un valore specifico per l'applicazione in uso, ad esempio `http://MyFirstAADApp`

   ![Registrare una nuova applicazione - Crea](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Per ottenere esempi specifici per applicazioni Web o per applicazioni native, vedere le [Guide introduttive](active-directory-developers-guide.md#get-started).

5. Al termine, fare clic su **Crea**. Azure AD assegna un ID applicazione univoco all'applicazione e si viene reindirizzati alla pagina di registrazione principale dell'applicazione. A seconda che si tratti di un'applicazione Web o nativa, sono fornite opzioni diverse per l'aggiunta di altre funzionalità all'applicazione. Vedere la sezione successiva per una panoramica del consenso e informazioni dettagliate sull'abilitazione di funzionalità di configurazione aggiuntive nella registrazione dell'applicazione (credenziali, autorizzazioni, abilitazione dell'accesso per gli utenti di altri tenant).

  > [!NOTE]
  > Per impostazione predefinita, l'applicazione appena registrata viene configurata in modo da consentire **solo** agli utenti dello stesso tenant di accedere all'applicazione stessa.
  > 
  > 

## <a name="updating-an-application"></a>Aggiornamento di un'applicazione
Una volta registrata con Azure AD, l'applicazione potrebbe dover essere aggiornata per fornire l'accesso ad API Web, essere resa disponibile in altre organizzazioni e altro ancora. Questa sezione descrive vari modi in cui è possibile configurare ulteriormente l'applicazione. Si inizierà con una panoramica del framework di consenso, che è importante conoscere quando si compilano applicazioni che devono essere usate da altri utenti o applicazioni.

### <a name="overview-of-the-consent-framework"></a>Panoramica del framework di consenso

Il framework di consenso di Azure AD semplifica lo sviluppo di applicazioni client Web multi-tenant e native, incluse le applicazioni a più livelli. Queste applicazioni consentono l'accesso da parte degli account utente da un tenant di Azure AD diverso da quello in cui l'applicazione è registrata. Esse possono anche dover accedere ad API Web come l'API Microsoft Graph (per l'accesso ad Azure Active Directory, Intune e ai servizi di Office 365) e le API di altri servizi Microsoft, oltre alle API Web personalizzate. Il framework è basato sulla possibilità per un utente o un amministratore di fornire il consenso alla richiesta di registrazione di un'applicazione nella propria directory, che può comportare l'accesso a dati di directory.

Ad esempio, se un'applicazione client Web deve leggere le informazioni del calendario dell'utente da Office 365, l'utente deve prima fornire il consenso all'applicazione client. Una volta ottenuto il consenso, l'applicazione client potrà chiamare l'API di Microsoft Graph per conto dell'utente e usare le informazioni del calendario nel modo necessario. L'[API Microsoft Graph](https://graph.microsoft.io) fornisce l'accesso ai dati in Office 365 (come calendari e messaggi di Exchange, siti ed elenchi di SharePoint, documenti di OneDrive, blocchi appunti di OneNote, attività di Planner, cartelle di lavoro di Excel e così via), nonché a utenti e gruppi di Azure AD e ad altri oggetti dati di più servizi cloud Microsoft. 

Il framework di consenso è basato su OAuth 2.0 e i relativi flussi diversi, come la concessione del codice di autorizzazione e delle credenziali utente, mediante client pubblici o riservati. Tramite OAuth 2.0 Azure AD consente di compilare molti tipi diversi di applicazioni client, ad esempio su un telefono, un tablet, un server o un'applicazione Web, e di ottenere l'accesso alle risorse necessarie.

Per altre informazioni sull'utilizzo del framework di consenso con concessioni di autorizzazione di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web con OAuth 2.0 e Azure Active Directory](active-directory-protocols-oauth-code.md) e [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md). Per informazioni su come ottenere l'accesso autorizzato a Office 365 tramite Microsoft Graph, vedere [App authentication with Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview) (Autenticazione delle app con Microsoft Graph).

#### <a name="example-of-the-consent-experience"></a>Esempio dell'esperienza di autorizzazione

I passaggi seguenti illustrano il funzionamento dell'esperienza di consenso per lo sviluppatore e l'utente dell'applicazione.

1. Si supponga di avere un'applicazione client Web che deve richiedere autorizzazioni specifiche per accedere a una risorsa/API. Si apprenderà come eseguire questa configurazione nella sezione successiva, ma essenzialmente il portale di Azure viene usato per dichiarare le richieste di autorizzazione al momento della configurazione. Come altre impostazioni di configurazione, diventano parte della registrazione dell'applicazione in Azure Active Directory:
   
  ![Autorizzazioni per altre applicazioni](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Considerare che sono state aggiornate le autorizzazioni dell'applicazione, che l'applicazione è in esecuzione e che un utente sta per usarla per la prima volta. L'applicazione deve prima ottenere un codice di autorizzazione dall'endpoint `/authorize` di Azure AD. Il codice di autorizzazione può quindi essere usato per acquisire un nuovo un token di accesso e aggiornamento.

3. Se l'utente non è già autenticato, l'endpoint `/authorize` di Azure AD richiede l'accesso.
   
  ![Accesso utente o amministratore ad Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Dopo che l'utente ha effettuato l'accesso, Azure AD determinerà se l'utente deve essere reindirizzato a una pagina di consenso. Questa decisione dipende dal fatto che l'utente o l'amministratore dell'organizzazione abbia o meno già concesso il consenso dell'applicazione. Se il consenso non è già stato concesso, Azure AD lo richiede all'utente e visualizza le autorizzazioni necessarie per il funzionamento. Il set di autorizzazioni visualizzato nella finestra di dialogo di consenso corrisponde a quelle selezionate in Autorizzazioni delegate nel portale di Azure.
   
  ![Esperienza di autorizzazione utente](./media/active-directory-integrating-applications/consent.png)

5. Dopo che l'utente ha concesso il consenso, all'applicazione viene restituito un codice di autorizzazione, che viene riscattato per acquisire un token di accesso e di aggiornamento. Per altre informazioni su questo flusso, vedere la sezione [Da applicazione Web ad API Web di Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. In qualità di amministratore, è possibile inoltre consentire le autorizzazioni delegate di un'applicazione per conto di tutti gli utenti nel proprio tenant. Il consenso amministrativo evita la visualizzazione della finestra di dialogo di consenso per ogni utente del tenant e viene eseguita la pagina dell'applicazione nel [portale di Azure](https://portal.azure.com). Dalla pagina **Impostazioni** dell'applicazione selezionare **Autorizzazioni necessarie** e fare clic sul pulsante **Concedi autorizzazioni**. 

  ![Concedere le autorizzazioni per il consenso esplicito dell'amministratore](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > La concessione esplicita del consenso utilizzando il pulsante **Concedi autorizzazioni** è attualmente richiesta per le applicazioni a pagina singola (SPA) che usano ADAL.js. In caso contrario, l'applicazione non funziona quando viene richiesto il token di accesso.   

### <a name="configure-a-client-application-to-access-web-apis"></a>Configurare un'applicazione client per accedere alle API Web
Per consentire a un'applicazione Web/client riservato di partecipare a un flusso di concessioni di autorizzazioni che richiede l'autenticazione (e ottenere un token di accesso), è necessario definire credenziali protette. Il metodo di autenticazione predefinito supportato dal portale di Azure è ID client + chiave privata. Questa sezione illustra i passaggi di configurazione necessari per fornire alla chiave privata le credenziali del client.

Inoltre, prima che un client possa accedere a un'API Web esposta da un'applicazione della risorsa (ad esempio l'API Microsoft Graph), il framework di consenso assicura che il client ottenga la concessione delle autorizzazioni necessaria, in base alle autorizzazioni richieste. Per impostazione predefinita, tutte le applicazioni possono scegliere le autorizzazioni da "Windows Azure Active Directory" (API Graph) e "API Gestione dei servizi di Microsoft Azure". L'[autorizzazione "Accedi e leggi il profilo di un altro utente" dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) è selezionata anch'essa per impostazione predefinita. Se il client viene registrato in un tenant che dispone di account con sottoscrizione per Office 365, saranno disponibili per la selezione le API Web e le autorizzazioni per SharePoint ed Exchange Online. È possibile scegliere fra [due tipi di autorizzazioni](active-directory-dev-glossary.md#permissions) per ogni API Web desiderata:

- Autorizzazioni applicazione: l'applicazione client deve accedere direttamente all'API Web come se stessa (nessun contesto utente). Questo tipo di autorizzazione richiede il consenso dell'amministratore e non è disponibile per le applicazioni client native.

- Autorizzazioni delegate: l'applicazione client deve accedere all'API Web come utente connesso, ma con accesso limitato dall'autorizzazione selezionata. Questo tipo di autorizzazione può essere concesso da un utente, a meno che l'autorizzazione richieda il consenso dell'amministratore. 

  > [!NOTE]
  > Aggiunta di un'autorizzazione delegata a un'applicazione non automaticamente consentire agli utenti nel tenant. Gli utenti devono comunque concedere manualmente il consenso per le autorizzazioni delegate aggiuntive al runtime, a meno che l'amministratore non faccia clic sul pulsante **Concedi autorizzazioni** nella sezione **Autorizzazioni necessarie** della pagina dell'applicazione nel portale di Azure. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Per aggiungere credenziali dell'applicazione o autorizzazioni per accedere alle API Web
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare.

   ![Aggiornare la registrazione di un'applicazione](./media/active-directory-integrating-applications/update-app-registration.png)

4. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione. Per aggiungere una chiave privata per le credenziali dell'applicazione Web:
  - Fare clic nella sezione **Chiavi** della pagina **Impostazioni**.  
  - Aggiungere una descrizione per la chiave.
  - Selezionare la durata di uno o due anni.
  - Fare clic su **Save**. Dopo che le modifiche apportate alla configurazione sono state salvate, il valore della chiave viene visualizzato nella colonna di destra. **Assicurarsi di copiare la chiave** per l'utilizzo nel codice dell'applicazione client, in quanto non è più accessibile dopo aver lasciato la pagina.

  ![Aggiornare la registrazione di un'applicazione - Chiavi](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Per aggiungere le autorizzazioni per accedere alle API di risorsa dal client
  - Fare clic nella sezione **Autorizzazioni necessarie** della pagina **Impostazioni**. 
  - Fare clic su **Add** .
  - Fare clic su **Selezionare un'API** per visualizzare il tipo di risorse da cui scegliere.
  - Scorrere l'elenco delle API disponibili o usare la casella di ricerca per selezionare fra le applicazioni di risorse disponibili nella directory che espongono un'API Web. Fare clic sulla risorsa desiderata, quindi scegliere **Seleziona**.
  - Viene visualizzata la pagina **Abilita accesso**. Selezionare le autorizzazioni per l'applicazione e/o le autorizzazioni delegate di cui l'applicazione ha bisogno quando accede all'API.
   
  ![Aggiornare la registrazione di un'applicazione - API di autorizzazione](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Aggiornare la registrazione di un'applicazione - Autorizzazioni](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Al termine, fare clic sul pulsante **Seleziona** della pagina **Abilita accesso**, quindi sul pulsante **Fatto** della pagina **Aggiungi accesso all'API**. Verrà visualizzata di nuovo la pagina **Autorizzazioni necessarie**, in cui la nuova risorsa viene aggiunta all'elenco di API.

  > [!NOTE]
  > Facendo clic sul pulsante **Fine**, vengono automaticamente impostate anche le autorizzazioni per l'applicazione nella directory in base a quelle configurate per altre applicazioni.  È possibile visualizzare queste autorizzazioni per l'applicazione osservando il contenuto della pagina **Impostazioni** dell'applicazione stessa.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurazione di un'applicazione della risorsa per esporre le API Web

È possibile sviluppare un'API Web e renderla disponibile alle applicazioni client esponendo gli [ambiti](active-directory-dev-glossary.md#scopes) e i [ruoli](active-directory-dev-glossary.md#roles) di accesso. Un'API Web correttamente configurata viene resa disponibile come le altre API Web Microsoft, tra cui l'API Graph e le API di Office 365. Gli ambiti e i ruoli di accesso vengono esposti con il [manifesto dell'applicazione](active-directory-dev-glossary.md#application-manifest), ovvero un file JSON che rappresenta la configurazione dell'identità dell'applicazione. 

La sezione seguente illustra come esporre gli ambiti di accesso modificando il manifesto dell'applicazione della risorsa.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Aggiunta di ambiti di accesso all'applicazione della risorsa

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.

3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare.

   ![Aggiornare la registrazione di un'applicazione](./media/active-directory-integrating-applications/update-app-registration.png)

4. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione. Passare alla pagina **Modifica manifesto** facendo clic su **Manifesto** dalla pagina di registrazione dell'applicazione. Si apre un editor di manifesto basato sul Web che consente di **modificare** il manifesto all'interno del portale. Facoltativamente è possibile fare clic su **Scarica** e modificare il manifesto localmente, quindi usare **Carica** per associarlo nuovamente all'applicazione.

5. In questo esempio si esporrà un nuovo ambito denominato `Employees.Read.All` nella risorsa/API, aggiungendo l'elemento JSON seguente all'insieme `oauth2Permissions`. L'ambito esistente `user_impersonation` viene fornito per impostazione predefinita durante la registrazione. `user_impersonation` consente a un'applicazione client di richiedere l'autorizzazione per accedere alla risorsa con l'identità dell'utente connesso. Assicurarsi di aggiungere la virgola dopo l'elemento di ambito esistente `user_impersonation` e modificare i valori delle proprietà in base alle esigenze della risorsa. 

  ```json
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
  ```
  > [!NOTE]
  > Il valore "id" deve essere generato a livello di codice oppure usando uno strumento per la generazione di GUID, ad esempio [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). Rappresenta un identificatore univoco per l'ambio esposto dall'API Web. Dopo che un client è stato configurato in modo appropriato con le autorizzazioni di accesso all'API Web, viene generato un token di accesso OAuth 2.0 da Azure AD. Quando il client chiama l'API Web, presenta il token di accesso che ha l'attestazione di ambito (scp) impostata sulle autorizzazioni richieste nella registrazione della relativa applicazione.
  >
  > Se necessario, è possibile esporre altri ambiti successivamente. Tenere presente che l'API Web può esporre più ambiti associati a molte funzioni diverse. La risorsa può controllare l'accesso all'API Web in fase di esecuzione, valutando l'attestazione (o le attestazioni) di ambito (`scp`) nel token di accesso OAuth 2.0 ricevuto.
  > 

6. Al termine fare clic su **Salva**. Ora l'API Web è configurata in modo che possa essere usata da altre applicazioni nella directory.  

  ![Aggiornare la registrazione di un'applicazione](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verificare che l'API Web sia esposta ad altre applicazioni nel tenant
1. Tornare al tenant di Azure AD, fare clic nuovamente su **Registrazioni per l'app**, quindi trovare/fare clic sull'applicazione client che si vuole configurare.

   ![Aggiornare la registrazione di un'applicazione](./media/active-directory-integrating-applications/update-app-registration.png)

2. Ripetere il passaggio 5 di [Configurare un'applicazione client per accedere alle API Web](#configure-a-client-application-to-access-web-apis). Quando si raggiunge il passaggio **Selezionare un'API**, cercare la risorsa immettendo il relativo nome dell'applicazione nel campo di ricerca e fare clic su **Seleziona**. 

3. Nella pagina **Abilita accesso** sarà visibile il nuovo ambito, disponibile per le richieste di autorizzazione del client.

  ![Le nuove autorizzazioni sono visualizzate](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Altre informazioni sul manifesto dell'applicazione

Il manifesto dell'applicazione è in realtà un meccanismo per l'aggiornamento dell'entità applicazione, che definisce tutti gli attributi della configurazione dell'identità di un'applicazione Azure AD, inclusi gli ambiti di accesso API di cui si è parlato sopra. Per altre informazioni sull'entità applicazione e il relativo schema, vedere la [documentazione sull'entità applicazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), che include informazioni di riferimento complete sui membri dell'entità applicazione utilizzati per specificare le autorizzazioni per l'API, fra cui:  

- Il membro appRoles, che è una raccolta di entità [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) utilizzate per definire le [Autorizzazioni applicazione](active-directory-dev-glossary.md#permissions) per un'API Web. 
- Il membro oauth2Permissions, che è una raccolta di entità [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) utilizzate per definire le [Autorizzazioni delegate](active-directory-dev-glossary.md#permissions) per un'API Web.

Per altre informazioni sui concetti generali relativi al manifesto dell'applicazione, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Accesso ad Azure AD Graph e Office 365 usando le API di Microsoft Graph  

Come accennato in precedenza, oltre a esporre/accedere alle API nelle applicazioni, è possibile registrare l'applicazione client per accedere alle API esposte dalle risorse Microsoft. L'API Microsoft Graph, indicata come "Microsoft Graph" nell'elenco di risorse/API del portale, è disponibile per tutte le applicazioni che sono registrate con Azure AD. Se si sta registrando l'applicazione client in un tenant che contiene account dotati di sottoscrizione per Office 365, è possibile accedere anche agli ambiti esposti da varie risorse di Office 365.

Per informazioni dettagliate sugli ambiti esposti dall'API Microsoft Graph, vedere l'articolo [Ambiti di autorizzazione | Concetti relativi all'API Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

> [!NOTE]
> A causa di una limitazione attualmente in vigore, le applicazioni client native possono richiamare l'API Graph di Azure AD solo se usano l'autorizzazione "Accedere alla directory dell'organizzazione". Questa restrizione non è valida per le applicazioni Web.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Configurazione di applicazioni multi-tenant

Quando si registra un'applicazione in Azure AD, potrebbe essere necessario consentire l'accesso all'applicazione solo agli utenti dell'organizzazione. In alternativa, è possibile consentire l'accesso all'applicazione anche agli utenti di organizzazioni esterne. Questi due tipi di applicazione sono chiamati applicazioni a tenant singolo e applicazioni multi-tenant. Questa sezione spiega come modificare la configurazione di un'applicazione a tenant singolo per trasformarla in applicazione multi-tenant.

È importante notare le differenze tra un'applicazione a tenant singolo e un'applicazione multi-tenant:  

- Un'applicazione a tenant singolo è destinata all'uso in un'organizzazione. In genere si tratta di un'applicazione line-of-business scritta da uno sviluppatore aziendale. Un'applicazione a tenant singolo è accessibile solo dagli utenti che hanno un account nello stesso tenant in cui l'applicazione è registrata. Di conseguenza è necessario eseguirne il provisioning solo in una directory.
- Un'applicazione multi-tenant è destinata all'uso in più organizzazioni. È indicata come applicazione Web SaaS (Software-as-a-Service) ed è scritta in genere da un fornitore di software indipendente (ISV). Per le applicazioni multi-tenant è necessario eseguirne il provisioning in ogni tenant a cui gli utenti devono accedere. Per i tenant diversi da quello in cui l'applicazione è registrata, è richiesto il consenso dell'utente o dell'amministratore per la loro registrazione. Si noti che, per impostazione predefinita, le applicazioni client native sono multi-tenant perché sono installate nel dispositivo del proprietario della risorsa. Per altri dettagli sul framework di consenso, vedere la precedente sezione [Panoramica del framework di consenso](#overview-of-the-consent-framework).

Per ottenere che un'applicazione diventi multi-tenant, sono necessarie sia modifiche alla registrazione dell'applicazione sia modifiche all'applicazione Web stessa. Le sezioni seguenti trattano entrambe le procedure.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Modifica della registrazione dell'applicazione affinché supporti la modalità multi-tenant

Se si scrive un'applicazione che si vuole rendere disponibile a clienti o partner esterni all'organizzazione, è necessario aggiornare la definizione dell'applicazione nel portale di Azure.

> [!IMPORTANT]
> Azure AD richiede che l'URI ID app delle applicazioni multi-tenant sia globalmente univoco. L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo. Per un'applicazione single-tenant, è sufficiente che l'URI dell'ID App sia univoco all'interno del tenant. Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'applicazione in tutti i tenant. L'univocità globale viene applicata richiedendo che l'URI dell'ID App abbia un nome host corrispondente a un dominio verificato del tenant di Azure AD. Ad esempio, se il nome del tenant è contoso.onmicrosoft.com, l'URI ID app sarà https://contoso.onmicrosoft.com/myapp. Se il tenant dispone del dominio verificato contoso.com, anche https://contoso.com/myapp sarebbe un URI ID app valido. Se l'URI dell'ID App non segue questo modello, l'impostazione di un'applicazione come multi-tenant ha esito negativo.
> 

Per consentire agli utenti esterni di accedere all'applicazione: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
4. Dalla pagina **Impostazioni** fare clic su **Proprietà** e cambiare il parametro **Multi-tenant** su **Sì**.

Dopo che sono state apportate le modifiche, gli utenti e gli amministratori di altre organizzazioni possono concedere ai loro utenti la possibilità di accedere all'applicazione, consentendo all'applicazione di accedere alle risorse protette dal loro tenant.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Modifica dell'applicazione affinché supporti la modalità multi-tenant

Il supporto per le applicazioni multi-tenant si basa principalmente sul framework di consenso di Azure AD. Il consenso è il meccanismo che consente a un utente di un altro tenant di concedere all'applicazione l'accesso alle risorse protette dal tenant dell'utente. Questa esperienza è detta "consenso dell'utente".

L'applicazione Web può offrire anche:

- La possibilità per gli amministratori di "iscriversi alla società". Questa esperienza, detta "consenso dell'amministratore", dà a un amministratore la possibilità di concedere il consenso per conto di *tutti gli utenti* della propria organizzazione. Solo un utente che esegue l'autenticazione con un account che appartiene al ruolo Amministratore globale può fornire il consenso dell'amministratore; gli altri riceveranno un errore.

- Esperienza di iscrizione per gli utenti. È previsto che sia fornito all'utente il pulsante "iscrizione" che reindirizzerà il browser all'endpoint `/authorize` OAuth 2.0 di Azure AD o a un endpoint `/userinfo` OpenID Connect. Questi endpoint consentono all'applicazione di ottenere informazioni sul nuovo utente controllando il valore id_token. Durante la fase di iscrizione viene presentata all'utente una richiesta di consenso simile a quella visualizzata nella sezione [Panoramica del framework di consenso](#overview-of-the-consent-framework).

Per altre informazioni sulle modifiche dell'applicazione necessarie per supportare l'accesso multi-tenant e le esperienze di accesso/iscrizione, vedere:

- [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](active-directory-devhowto-multi-tenant-overview.md)
- L'elenco di [esempi di codice multi-tenant](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Avvio rapido: Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso in Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Abilitazione della concessione implicita OAuth 2.0 per le applicazioni a singola pagina

Le applicazioni a singola pagina (SPA) sono in genere strutturate con un front-end JavaScript eseguito nel browser, che chiama il back-end dell'API Web dell'applicazione per eseguirne la logica di business. Per le applicazioni a singola pagina ospitate in Azure AD, è possibile usare la concessione implicita OAuth 2.0 per autenticare l'utente con Azure AD e ottenere un token da usare per chiamate protette dal client JavaScript dell'applicazione all'API Web di back-end. 

Dopo che l'utente ha concesso il consenso, lo stesso protocollo di autenticazione può essere usato per ottenere token per proteggere le chiamate tra il client e altre risorse dell'API Web configurate per l'applicazione. Per ulteriori informazioni sulla concessione di autorizzazione implicita e per stabilire se sia adatta allo scenario della propria applicazione, vedere [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

Per impostazione predefinita, la concessione implicita OAuth 2.0 è disabilitata per le applicazioni. È possibile abilitare la concessione implicita di OAuth 2.0 per l'applicazione impostando il valore `oauth2AllowImplicitFlow` nel relativo [manifesto dell'applicazione](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Per abilitare la concessione implicita OAuth 2.0

> [!NOTE]
> Per informazioni dettagliate su come modificare il manifesto dell'applicazione, vedere innanzitutto la precedente sezione [Configurazione di un'applicazione della risorsa per esporre le API Web](#configuring-a-resource-application-to-expose-web-apis).
>

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
4. Passare alla pagina **Modifica manifesto** facendo clic su **Manifesto** dalla pagina di registrazione dell'applicazione. Si apre un editor di manifesto basato sul Web che consente di **modificare** il manifesto all'interno del portale. Individuare e impostare il valore "oauth2AllowImplicitFlow" su "true". Per impostazione predefinita, il valore è impostato su "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Salvare il manifesto aggiornato. Dopo il salvataggio, l'API Web è configurata in modo da usare la concessione implicita OAuth 2.0 per autenticare gli utenti.

## <a name="removing-an-application"></a>Rimozione di un'applicazione
Questa sezione descrive come rimuovere la registrazione di un'applicazione dal tenant di Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Rimozione di un'applicazione autorizzata dall'organizzazione
Le applicazioni che l'organizzazione ha registrato compaiono sotto il filtro "Mie app" nella pagina principale "Registrazioni per l'app" del tenant. Queste sono applicazioni registrate manualmente nel portale di Azure classico o a livello di codice con PowerShell o l'API Graph. Più precisamente, sono rappresentate sia da un oggetto applicazione che da un oggetto entità servizio nel tenant. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Per rimuovere un'applicazione a tenant singolo dalla directory
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
4. Nella pagina principale della registrazione dell'applicazione fare clic su **Elimina**.
5. Nel messaggio di conferma fare clic su **Sì** .

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Per rimuovere un'applicazione multi-tenant dalla sua home directory
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
4. Nella pagina **Impostazioni** scegliere **Proprietà** e cambiare il parametro **Multi-tenant** in **No**, per convertire prima di tutto l'applicazione alla modalità a tenant singolo, quindi fare clic su **Salva**. Gli oggetti entità di servizio dell'applicazione rimangono nei tenant di tutte le organizzazioni che hanno già concesso il consenso.
5. Fare clic sul pulsante **Elimina** dalla pagina di registrazione principale dell'applicazione.
6. Nel messaggio di conferma fare clic su **Sì** .

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Rimozione di un'applicazione multi-tenant autorizzata da un'altra organizzazione
Un sottoinsieme delle applicazioni visualizzate con il filtro "Tutte le app" (escluse le registrazioni "Mie app") nella pagina "Registrazioni per l'app" principale del tenant, sono applicazioni multi-tenant. In termini tecnici, queste applicazioni multi-tenant provengono da un altro tenant e sono state registrate nel tenant durante il processo di consenso. Più precisamente, sono rappresentate solo da un oggetto entità servizio nel tenant, senza oggetto applicazione corrispondente. Per altre informazioni sulle differenze fra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure AD](active-directory-application-objects.md).

Per rimuovere l'accesso di un'applicazione multi-tenant alla directory (dopo avere concesso il consenso), l'amministratore della società deve rimuovere la sua entità servizio. L'amministratore deve avere l'accesso di amministratore globale e può rimuovere tramite il portale di Azure [i cmdlet di Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151) per rimuovere l'accesso.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sul funzionamento dell'autenticazione in Azure AD, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).
- Per suggerimenti sulle indicazioni visive per l'applicazione, vedere [Linee guida sulla personalizzazione per le applicazioni](active-directory-branding-guidelines.md).
- Per altre informazioni sulla relazione tra gli oggetti applicazione e gli oggetti entità servizio di un'applicazione, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](active-directory-application-objects.md).
- Per informazioni sul ruolo svolto dal manifesto dell'applicazione, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md).
- Per le definizioni di alcuni concetti di Azure Active Directory (AD) fondamentali per gli sviluppatori, vedere il [Glossario per gli sviluppatori di Azure Active Directory](active-directory-dev-glossary.md).
- Per una panoramica di tutti i contenuti correlati allo sviluppo, vedere la [Guida per gli sviluppatori di Active Directory](active-directory-developers-guide.md).

