---
title: Integrazione di applicazioni con Azure Active Directory
description: Informazioni su come aggiornare un'applicazione in Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103809"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Avvio rapido: Aggiornare un'applicazione in Azure Active Directory

Gli sviluppatori aziendali e i provider di software-as-a-service (SaaS) che hanno effettuato la registrazione delle applicazioni con Azure Active Directory (Azure AD) potrebbero dover configurare le applicazioni per accedere ad altre risorse, ad esempio le API Web, renderle disponibili in altre organizzazioni e altro ancora.

In questa guida introduttiva si apprenderanno i vari modi in cui è possibile configurare o aggiornare l'applicazione in modo da soddisfare i requisiti o le esigenze della propria organizzazione o di altre organizzazioni.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, assicurarsi di aver completato i passaggi seguenti:

* Leggere la panoramica del [framework di consenso di Azure AD](consent-framework.md), che è importante conoscere quando si compilano applicazioni che devono essere usate da altri utenti o applicazioni.
* Disporre di un tenant di Azure AD con le applicazioni registrate.
  * Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](quickstart-create-new-tenant.md).
  * Se non si dispone di app registrate nel tenant, [vedere come aggiungere un'applicazione ad Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Configurare un'applicazione client per accedere alle API Web

Per consentire a un'applicazione Web/client riservata di partecipare a un flusso di concessioni di autorizzazioni che richiede l'autenticazione (e ottenere un token di accesso), è necessario definire credenziali protette. Il metodo di autenticazione predefinito supportato dal portale di Azure è ID client + chiave privata. Questa sezione illustra i passaggi di configurazione necessari per fornire alla chiave privata le credenziali del client.

Prima che un client possa accedere a un'API Web esposta da un'applicazione della risorsa (ad esempio l'API Microsoft Graph), il framework di consenso assicura che il client ottenga la concessione delle autorizzazioni necessaria, in base alle autorizzazioni richieste. Per impostazione predefinita, tutte le applicazioni possono scegliere le autorizzazioni da **Azure Active Directory** (API Graph) e dal modello di distribuzione classica di Azure. L'[autorizzazione "Accedi e leggi il profilo di un altro utente" dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) è selezionata anch'essa per impostazione predefinita. Se il client viene registrato in un tenant che dispone di account con sottoscrizione per Office 365, saranno disponibili per la selezione le API Web e le autorizzazioni per SharePoint ed Exchange Online. È possibile selezionare fra due tipi di autorizzazioni per ogni API Web desiderata:

- Autorizzazioni dell'applicazione: l'applicazione client deve accedere all'API Web direttamente come se stessa (senza contesto utente). Questo tipo di autorizzazione richiede il consenso dell'amministratore e non è disponibile per le applicazioni client native.
- Autorizzazioni delegate: l'applicazione client deve accedere all'API Web come utente connesso, ma con accesso limitato dall'autorizzazione selezionata. Questo tipo di autorizzazione può essere concesso da un utente, a meno che l'autorizzazione richieda il consenso dell'amministratore.

  > [!NOTE]
  > L'aggiunta di un'autorizzazione delegata a un'applicazione non concede automaticamente il consenso all'utente all'interno del tenant. Gli utenti devono comunque concedere manualmente il consenso per le autorizzazioni delegate aggiuntive in fase di esecuzione, a meno che l'amministratore non conceda il consenso per conto di tutti gli utenti.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Aggiungere credenziali dell'applicazione o autorizzazioni per accedere alle API Web

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**, selezionare **Registrazioni app** quindi trovare/selezionare l'applicazione che si vuole configurare.

   ![Aggiornare la registrazione di un'applicazione](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Viene visualizzata la pagina di registrazione principale dell'applicazione che contiene la pagina **Impostazioni** per l'applicazione. Per aggiungere una credenziale per l'applicazione Web:
   1. Selezionare la sezione **Chiavi** della pagina **Impostazioni**.
   1. Per aggiungere un certificato:
      - Selezionare **Carica la chiave pubblica**.
      - Selezionare il file da caricare. Il tipo di file deve essere uno dei seguenti: .cer, .pem, .crt.
   1. Per aggiungere una password:
      - Aggiungere una descrizione per la chiave.
      - Selezionare una durata.
      - Selezionare **Salva**. Dopo che le modifiche apportate alla configurazione sono state salvate, il valore della chiave viene visualizzato nella colonna di destra. **Assicurarsi di copiare la chiave** per l'utilizzo nel codice dell'applicazione client, in quanto non è più accessibile dopo aver lasciato la pagina.

5. Per aggiungere le autorizzazioni per accedere alle API di risorsa dal client
   1. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie** e quindi **Aggiungi**.
   1. Scegliere **Selezionare un'API** per visualizzare il tipo di risorse da cui scegliere.
   1. Scorrere l'elenco delle API disponibili o usare la casella di ricerca per selezionare fra le applicazioni di risorse disponibili nella directory che espongono un'API Web. Selezionare la risorsa desiderata, quindi scegliere **Seleziona**.
   1. Nella pagina **Abilita accesso** selezionare le autorizzazioni per l'applicazione e/o le autorizzazioni delegate di cui l'applicazione ha bisogno quando accede all'API.
   
   ![Aggiornare la registrazione di un'applicazione - API di autorizzazione](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![Aggiornare la registrazione di un'applicazione - Autorizzazioni](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Al termine, selezionare il pulsante **Seleziona** della pagina **Abilita accesso**, quindi sul pulsante **Fatto** della pagina **Aggiungi accesso all'API**. Verrà visualizzata di nuovo la pagina **Autorizzazioni necessarie**, in cui la nuova risorsa viene aggiunta all'elenco di API.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurazione di un'applicazione della risorsa per esporre le API Web

È possibile sviluppare un'API Web e renderla disponibile alle applicazioni client esponendo gli [ambiti](developer-glossary.md#scopes) e i [ruoli](developer-glossary.md#roles) di accesso. Un'API Web correttamente configurata viene resa disponibile come le altre API Web Microsoft, tra cui l'API Graph e le API di Office 365. Gli ambiti e i ruoli di accesso vengono esposti con il [manifesto dell'applicazione](developer-glossary.md#application-manifest), ovvero un file JSON che rappresenta la configurazione dell'identità dell'applicazione.

La sezione seguente illustra come esporre gli ambiti di accesso modificando il manifesto dell'applicazione della risorsa.

### <a name="add-access-scopes-to-your-resource-application"></a>Aggiungere ambiti di accesso all'applicazione della risorsa

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, selezionare l'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory > Registrazioni app**, quindi trovare/selezionare l'applicazione che si vuole configurare.

   ![Aggiornare la registrazione di un'applicazione](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

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
   > Il valore di `id` deve essere generato a livello di codice oppure usando uno strumento per la generazione di GUID come [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). `id` rappresenta un identificatore univoco per l'ambito esposto dall'API Web. Dopo che un client è stato configurato in modo appropriato con le autorizzazioni di accesso all'API Web, viene generato un token di accesso OAuth 2.0 da Azure AD. Quando il client chiama l'API Web, presenta il token di accesso che ha l'attestazione di ambito (scp) impostata sulle autorizzazioni richieste nella registrazione della relativa applicazione.
   >
   > Se necessario, è possibile esporre altri ambiti successivamente. Tenere presente che l'API Web può esporre più ambiti associati a molte funzioni diverse. La risorsa può controllare l'accesso all'API Web in fase di esecuzione, valutando l'attestazione (o le attestazioni) di ambito (`scp`) nel token di accesso OAuth 2.0 ricevuto.

6. Al termine fare clic su **Salva**. Ora l'API Web è configurata in modo che possa essere usata da altre applicazioni nella directory.

   ![Aggiornare la registrazione di un'applicazione](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verificare che l'API Web sia esposta ad altre applicazioni nel tenant

1. Tornare al tenant di Azure AD, selezionare nuovamente **Registrazioni app**, quindi trovare/selezionare l'applicazione client che si vuole configurare.

   ![Aggiornare la registrazione di un'applicazione](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Ripetere il passaggio 5 di [Configurare un'applicazione client per accedere alle API Web](#configure-a-client-application-to-access-web-apis). Quando si raggiunge il passaggio **Selezionare un'API**, cercare la risorsa immettendo il relativo nome dell'applicazione nel campo di ricerca e fare clic su **Seleziona**.

3. Nella pagina **Abilita accesso** sarà visibile il nuovo ambito, disponibile per le richieste di autorizzazione del client.

   ![Le nuove autorizzazioni sono visualizzate](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Altre informazioni sul manifesto dell'applicazione

Il manifesto dell'applicazione è un meccanismo per l'aggiornamento dell'entità Applicazione, che definisce tutti gli attributi della configurazione dell'identità di un'applicazione Azure AD, inclusi gli ambiti di accesso API di cui si è parlato sopra. Per altre informazioni sull'entità applicazione e il relativo schema, vedere la [documentazione sull'entità applicazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), che include informazioni di riferimento complete sui membri dell'entità applicazione utilizzati per specificare le autorizzazioni per l'API, fra cui:  

- Il membro appRoles, che è una raccolta di entità [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) utilizzate per definire le [Autorizzazioni applicazione](developer-glossary.md#permissions) per un'API Web. 
- Il membro oauth2Permissions, che è una raccolta di entità [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) utilizzate per definire le [Autorizzazioni delegate](developer-glossary.md#permissions) per un'API Web.

Per altre informazioni sui concetti generali relativi al manifesto dell'applicazione, vedere [Manifesto dell'applicazione di Azure AD](reference-app-manifest.md).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Accesso ad Azure AD Graph e Office 365 usando le API di Microsoft Graph  

Come accennato in precedenza, oltre a esporre/accedere alle API nelle applicazioni, è possibile registrare l'applicazione client per accedere alle API esposte dalle risorse Microsoft. L'API Microsoft Graph, indicata come "Microsoft Graph" nell'elenco di risorse/API del portale, è disponibile per tutte le applicazioni che sono registrate con Azure AD. Se si sta registrando l'applicazione client in un tenant che contiene account dotati di sottoscrizione per Office 365, è possibile accedere anche agli ambiti esposti da varie risorse di Office 365.

Per informazioni dettagliate sugli ambiti esposti dall'API Graph Microsoft, vedere l'articolo [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference) (Riferimenti autorizzazioni Microsoft Graph).

> [!NOTE]
> A causa di una limitazione attualmente in vigore, le applicazioni client native possono richiamare l'API Graph di Azure AD solo se usano l'autorizzazione "Accedere alla directory dell'organizzazione". Questa restrizione non è valida per le applicazioni Web.

## <a name="configuring-multi-tenant-applications"></a>Configurazione di applicazioni multi-tenant

Quando si registra un'applicazione in Azure AD, potrebbe essere necessario consentire l'accesso all'applicazione solo agli utenti dell'organizzazione. In alternativa, è possibile consentire l'accesso all'applicazione anche agli utenti di organizzazioni esterne. Questi due tipi di applicazione sono chiamati applicazioni a tenant singolo e applicazioni multi-tenant. Questa sezione spiega come modificare la configurazione di un'applicazione a tenant singolo per trasformarla in applicazione multi-tenant.

È importante notare le differenze tra un'applicazione a tenant singolo e un'applicazione multi-tenant:  

- Un'applicazione a tenant singolo è destinata all'uso in un'organizzazione. In genere si tratta di un'applicazione line-of-business scritta da uno sviluppatore aziendale. Un'applicazione a tenant singolo è accessibile solo dagli utenti che hanno un account nello stesso tenant in cui l'applicazione è registrata. Di conseguenza è necessario eseguirne il provisioning solo in una directory.
- Un'applicazione multi-tenant è destinata all'uso in più organizzazioni. È indicata come applicazione Web SaaS (Software-as-a-Service) ed è scritta in genere da un fornitore di software indipendente (ISV). Per le applicazioni multi-tenant è necessario eseguirne il provisioning in ogni tenant a cui gli utenti devono accedere. Per i tenant diversi da quello in cui l'applicazione è registrata, è richiesto il consenso dell'utente o dell'amministratore per la loro registrazione. Si noti che, per impostazione predefinita, le applicazioni client native sono multi-tenant perché sono installate nel dispositivo del proprietario della risorsa. Per altri dettagli sul framework di consenso, vedere la precedente sezione Panoramica del framework di consenso.

Per ottenere che un'applicazione diventi multi-tenant, sono necessarie sia modifiche alla registrazione dell'applicazione sia modifiche all'applicazione Web stessa. Le sezioni seguenti trattano entrambe le procedure.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Modifica della registrazione dell'applicazione affinché supporti la modalità multi-tenant

Se si scrive un'applicazione che si vuole rendere disponibile a clienti o partner esterni all'organizzazione, è necessario aggiornare la definizione dell'applicazione nel portale di Azure.

> [!IMPORTANT]
> Azure AD richiede che l'URI ID app delle applicazioni multi-tenant sia globalmente univoco. L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo. Per un'applicazione single-tenant, è sufficiente che l'URI dell'ID App sia univoco all'interno del tenant. Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'applicazione in tutti i tenant.
> L'univocità globale viene applicata richiedendo che l'URI dell'ID App abbia un nome host corrispondente a un dominio verificato del tenant di Azure AD. Ad esempio, se il nome del tenant è contoso.onmicrosoft.com, l'URI dell'ID app è https://contoso.onmicrosoft.com/myapp. Se il tenant dispone del dominio verificato contoso.com, anche https://contoso.com/myapp è un URI di ID app valido. Se l'URI dell'ID App non segue questo modello, l'impostazione di un'applicazione come multi-tenant ha esito negativo.

Per consentire agli utenti esterni di accedere all'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
4. Dalla pagina **Impostazioni** fare clic su **Proprietà** e cambiare il parametro **Multi-tenant** su **Sì**.

Dopo che sono state apportate le modifiche, gli utenti e gli amministratori di altre organizzazioni possono concedere ai loro utenti la possibilità di accedere all'applicazione, consentendo all'applicazione di accedere alle risorse protette dal loro tenant.

### <a name="changing-the-application-to-support-multi-tenant"></a>Modifica dell'applicazione affinché supporti la modalità multi-tenant

Il supporto per le applicazioni multi-tenant si basa principalmente sul framework di consenso di Azure AD. Il consenso è il meccanismo che consente a un utente di un altro tenant di concedere all'applicazione l'accesso alle risorse protette dal tenant dell'utente. Questa esperienza è detta "consenso dell'utente".

L'applicazione Web può offrire anche:

- La possibilità per gli amministratori di "iscriversi alla società". Questa esperienza, detta "consenso dell'amministratore", dà a un amministratore la possibilità di concedere il consenso per conto di *tutti gli utenti* della propria organizzazione. Solo un utente che esegue l'autenticazione con un account che appartiene al ruolo Amministratore globale può fornire il consenso dell'amministratore; gli altri riceveranno un errore.
- Esperienza di iscrizione per gli utenti. È previsto che sia fornito all'utente il pulsante "iscrizione" che reindirizzerà il browser all'endpoint `/authorize` OAuth 2.0 di Azure AD o a un endpoint `/userinfo` OpenID Connect. Questi endpoint consentono all'applicazione di ottenere informazioni sul nuovo utente controllando il valore id_token. Durante la fase di iscrizione viene presentata all'utente una richiesta di consenso simile a quella visualizzata nella sezione Panoramica del framework di consenso.

Per altre informazioni sulle modifiche dell'applicazione necessarie per supportare l'accesso multi-tenant e le esperienze di accesso/iscrizione, vedere:

- [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md)
- L'elenco di [esempi di codice multi-tenant](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant).
- [Guida introduttiva: Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso in Azure AD](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Abilitazione della concessione implicita OAuth 2.0 per le applicazioni a pagina singola

Le applicazioni a pagina singola (SPA) sono in genere strutturate con un front-end JavaScript eseguito nel browser, che chiama il back-end dell'API Web dell'applicazione per eseguirne la logica di business. Per le applicazioni a singola pagina ospitate in Azure AD, è possibile usare la concessione implicita OAuth 2.0 per autenticare l'utente con Azure AD e ottenere un token da usare per chiamate protette dal client JavaScript dell'applicazione all'API Web di back-end.

Dopo che l'utente ha concesso il consenso, lo stesso protocollo di autenticazione può essere usato per ottenere token per proteggere le chiamate tra il client e altre risorse dell'API Web configurate per l'applicazione. Per ulteriori informazioni sulla concessione di autorizzazione implicita e per stabilire se sia adatta allo scenario della propria applicazione, vedere [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Per impostazione predefinita, la concessione implicita OAuth 2.0 è disabilitata per le applicazioni. È possibile abilitare la concessione implicita di OAuth 2.0 per l'applicazione impostando il valore `oauth2AllowImplicitFlow` nel relativo [manifesto dell'applicazione](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Per abilitare la concessione implicita OAuth 2.0

> [!NOTE]
> Per informazioni dettagliate su come modificare il manifesto dell'applicazione, vedere innanzitutto la precedente sezione [Configurazione di un'applicazione della risorsa per esporre le API Web](#configuring-a-resource-application-to-expose-web-apis).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Se l'account consente di accedere a più tenant, fare clic sull'account in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
3. Nel riquadro di spostamento a sinistra fare clic sul servizio **Azure Active Directory**, fare clic su **Registrazioni per l'app** quindi trovare/fare clic sull'applicazione che si vuole configurare. Viene visualizzata la pagina di registrazione principale dell'applicazione che apre la pagina **Impostazioni** per l'applicazione.
4. Passare alla pagina **Modifica manifesto** facendo clic su **Manifesto** dalla pagina di registrazione dell'applicazione. Si apre un editor di manifesto basato sul Web che consente di **modificare** il manifesto all'interno del portale. Individuare e impostare il valore "oauth2AllowImplicitFlow" su "true". Per impostazione predefinita, il valore è impostato su "false".
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. Salvare il manifesto aggiornato. Dopo il salvataggio, l'API Web è configurata in modo da usare la concessione implicita OAuth 2.0 per autenticare gli utenti.

## <a name="next-steps"></a>Passaggi successivi

Altre guide introduttive relative alla gestione delle app applicabili alle app che usano la versione 1.0 dell'endpoint di Azure AD:
- [Aggiungere un'applicazione ad Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Rimuovere un'applicazione da Azure AD](quickstart-v1-remove-azure-ad-app.md)

Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

Per altre informazioni sulle linee guida sulla personalizzazione da seguire per lo sviluppo di applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md).
