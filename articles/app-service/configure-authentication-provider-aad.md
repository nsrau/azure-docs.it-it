---
title: Configurare l'autenticazione di Azure AD
description: Informazioni su come configurare l'autenticazione di Azure Active Directory come provider di identità per un'app del servizio app o di Funzioni di Azure.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 5d5348f8abe8d30c7f23882974b8c121af39636c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448142"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configurare un'app del servizio app o di Funzioni di Azure per l'uso dell'account di accesso di Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare il servizio app di Azure o Funzioni di Azure per l'uso di Azure Active Directory (Azure AD) come provider di autenticazione.

> [!NOTE]
> Il flusso di impostazioni rapide configura una registrazione dell'applicazione AAD V1. Se si vuole usare [Azure Active Directory v 2.0](../active-directory/develop/v2-overview.md) (incluso [MSAL](../active-directory/develop/msal-overview.md)), seguire le [istruzioni per la configurazione avanzata](#advanced).

Seguire queste procedure consigliate per la configurazione dell'app e dell'autenticazione:

- Assegnare a ogni app del servizio app le autorizzazioni specifiche e il consenso.
- Configurare ogni app del servizio app con la relativa registrazione.
- Evitare la condivisione delle autorizzazioni tra ambienti usando registrazioni di app separate per slot di distribuzione distinti. Quando si esegue il test di nuovo codice, questa procedura consente di evitare problemi che influiscono sull'app di produzione.

> [!NOTE]
> Questa funzionalità non è attualmente disponibile nel piano a consumo per Linux per Funzioni di Azure

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configurazione con impostazioni rapide

> [!NOTE]
> L'opzione **Rapida** non è disponibile per i cloud per enti pubblici.

1. Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app.
2. Nel riquadro di spostamento sinistro selezionare **Autenticazione/Autorizzazione** > **Sì**.
3. Selezionare **Azure Active Directory** > **Rapida**.

   Se invece si vuole scegliere una registrazione di app esistente:

   1. Scegliere **Seleziona app AD esistente** e quindi fare clic su **App Azure AD**.
   2. Scegliere una registrazione di app esistente e fare clic su **OK**.

3. Selezionare **OK** per registrare l'applicazione dei servizi app in Azure Active Directory. Viene creata una nuova registrazione per l'app.

    ![Impostazioni rapide in Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)

4. (Facoltativo) Per impostazione predefinita, il servizio app fornisce l'autenticazione, ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app. Per consentire l'accesso al sito solo agli utenti autenticati da Azure Active Directory, impostare **Azione da eseguire quando la richiesta non è autenticata** su **Accedi con Azure Active Directory**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate ad Azure Active Directory per l'autenticazione.

    > [!CAUTION]
    > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, il che potrebbe non essere opportuno per le app dotate di una home page disponibile pubblicamente, come nel caso di molte applicazioni a pagina singola. Per queste applicazioni, potrebbe essere preferibile scegliere **Consenti richieste anonime (nessuna azione)** con l'app stessa che avvia manualmente l'accesso. Per altre informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).
5. Selezionare **Salva**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configurazione con impostazioni avanzate

È possibile configurare manualmente le impostazioni dell'app se si vuole usare una registrazione di app di un tenant diverso di Azure AD. Per completare questa configurazione personalizzata:

1. Creare una registrazione in Azure AD.
2. Fornire alcuni dettagli sulla registrazione al servizio app.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Creare una registrazione in Azure AD per l'app del servizio app

Per configurare l'app del servizio app, è necessario avere le informazioni seguenti:

- ID client
- ID tenant
- Segreto client (facoltativo)
- URI dell'ID applicazione

Eseguire la procedura seguente:

1. Accedere al [Azure portal], cercare e selezionare **Servizi app** e quindi selezionare la propria app. Prendere nota dell'**URL** dell'app. Verrà usato per configurare la registrazione dell'app di Azure Active Directory.
1. Selezionare **Azure Active Directory** > **Registrazioni app** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un valore per **Nome** per la registrazione dell'app.
1. In **URI di reindirizzamento** selezionare **Web** e digitare `<app-url>/.auth/login/aad/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Selezionare **Create** (Crea).
1. Una volta creata la registrazione dell'app, copiare i valori di **ID applicazione (client)** e **ID della directory (tenant)** , che serviranno più avanti.
1. Selezionare **Autenticazione**. In **Concessione implicita** abilitare **Token ID** per consentire gli accessi utente OpenID Connect dal servizio app.
1. (Facoltativo) Selezionare **Personalizzazione**. In **URL pagina iniziale** immettere l'URL dell'app del servizio app e selezionare **Salva**.
1. Selezionare **Esporre un'API** > **Imposta**. Per l'app a singolo tenant, incollare l'URL dell'app del servizio app e selezionare **Salva**, mentre per l'app multi-tenant, incollare l'URL basato su uno dei domini verificati del tenant e quindi selezionare **Salva**.

   > [!NOTE]
   > Questo valore corrisponde a **URI ID applicazione** della registrazione dell'app. Se l'app Web richiede l'accesso a un'API nel cloud, il valore di **URI ID applicazione** dell'app Web è necessario quando si configura la risorsa del servizio app cloud. È possibile usare questo valore se ad esempio si vuole che il servizio cloud conceda esplicitamente l'accesso all'app Web.

1. Selezionare **Aggiungi un ambito**.
   1. In **Nome ambito** immettere *user_impersonation*.
   1. Nelle caselle di testo immettere il nome dell'ambito del consenso e la descrizione da mostrare agli utenti nella pagina del consenso. Immettere ad esempio *Accesso alla mia app*.
   1. Selezionare **Aggiungi ambito**.
1. (Facoltativo) Per creare un segreto client, selezionare **Certificati e segreti** > **Nuovo segreto client** > **Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Non verrà più visualizzato.
1. (Facoltativo) Per aggiungere più valori per **URL di risposta**, selezionare **Autenticazione**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Abilitare Azure Active Directory nell'app del servizio app

1. Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app.
1. Nel riquadro sinistro, in **Impostazioni**, selezionare **Autenticazione/Autorizzazione** > **Sì**.
1. (Facoltativo) Per impostazione predefinita, l'autenticazione del servizio app consente l'accesso non autenticato all'app. Per applicare l'autenticazione degli utenti, impostare **Azione da eseguire quando la richiesta non è autenticata** su **Accedi con Azure Active Directory**.
1. In **Provider di autenticazione** fare clic su **Azure Active Directory**.
1. In **Modalità di gestione** selezionare **Avanzata** e configurare l'autenticazione del servizio app in base alla tabella seguente:

    |Campo|Descrizione|
    |-|-|
    |ID client| Usare il valore di **ID applicazione (client)** della registrazione dell'app. |
    |URL autorità di certificazione| Usare `<authentication-endpoint>/<tenant-id>/v2.0` e sostituire *\<authentication-endpoint>* con l' [endpoint di autenticazione per l'ambiente cloud](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) , ad esempio " https://login.microsoft.com " per Azure globale, sostituendo anche *\<tenant-id>* con l'ID di **Directory (tenant)** in cui è stata creata la registrazione dell'app. Questo valore viene usato per reindirizzare gli utenti al tenant di Azure AD corretto, oltre che per scaricare i metadati appropriati in modo da determinare, ad esempio, i valori corretti delle chiavi di firma del token e dell'attestazione dell'autorità emittente del token corretti. Per le applicazioni che usano Azure AD V1 e per le app di funzioni di Azure, omettere `/v2.0` nell'URL.|
    |Segreto client (facoltativo)| Usare il segreto client generato nella registrazione dell'app.|
    |Destinatari token consentiti| Se si tratta di un'app cloud o server e si vogliono consentire i token di autenticazione di un'app Web, aggiungere qui il valore di **URI dell'ID applicazione** dell'app Web. L'**ID client** configurato viene *sempre* considerato implicitamente come destinatario consentito. |

2. Selezionare **OK** e quindi **Salva**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app del servizio app.

## <a name="configure-a-native-client-application"></a>Configurare un'applicazione client nativa

È possibile registrare client nativi per consentire l'autenticazione alle API Web ospitate nell'app usando una libreria client, ad esempio **Active Directory Authentication Library**.

1. Nel [Azure portal] selezionare **Active Directory** > **Registrazioni app** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un valore per **Nome** per la registrazione dell'app.
1. In **URI di reindirizzamento** selezionare **Client pubblico (per dispositivi mobili e desktop)** e digitare l'URL `<app-url>/.auth/login/aad/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Per un'applicazione di Microsoft Store, usare invece il [SID pacchetto](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) come URI.
1. Selezionare **Create** (Crea).
1. Dopo la creazione della registrazione dell'app, copiare il valore di **ID applicazione (client)** .
1. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **Le mie API**.
1. Selezionare la registrazione creata in precedenza per l'app del servizio app. Se la registrazione dell'app non è visibile, verificare di aver aggiunto l'ambito **user_impersonation** in [Creare una registrazione di app in Azure AD per l'app del servizio app](#register).
1. Selezionare **user_impersonation** in **Autorizzazioni delegate**, quindi selezionare **Aggiungi autorizzazioni**.

È stata configurata un'applicazione client nativa che può accedere all'app del servizio app per conto dell'utente.

## <a name="configure-a-daemon-client-application-for-service-to-service-calls"></a>Configurare un'applicazione client daemon per le chiamate da servizio a servizio

L'applicazione può acquisire un token per chiamare un'API Web ospitata nel servizio app o nell'app per le funzioni per conto di se stessa (non per conto di un utente). Questo scenario è utile per le applicazioni daemon non interattive che eseguono attività senza un utente connesso. Usa la concessione di [credenziali client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) OAuth 2.0 standard.

1. Nel [Azure portal] selezionare **Active Directory** > **Registrazioni app** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un valore in **Nome** per la registrazione dell'app daemon.
1. Per un'applicazione daemon, non è necessario un URI di reindirizzamento, quindi è possibile lasciare questo campo vuoto.
1. Selezionare **Create** (Crea).
1. Dopo la creazione della registrazione dell'app, copiare il valore di **ID applicazione (client)** .
1. Selezionare **Certificati e segreti** > **Nuovo segreto client** > **Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Non verrà più visualizzato.

È ora possibile [richiedere un token di accesso usando l'ID client e il segreto client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) impostando il parametro `resource` sull'**URI ID applicazione** dell'app di destinazione. Il token di accesso generato può quindi essere presentato all'app di destinazione usando l'[intestazione dell'autorizzazione OAuth 2.0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource) standard e l'autenticazione/autorizzazione del servizio app convaliderà e userà il token come di consueto per indicare che il chiamante (in questo caso un'applicazione, non un utente) è autenticato.

Al momento, questo consente a _qualsiasi_ applicazione client nel tenant di Azure AD di richiedere un token di accesso ed eseguire l'autenticazione all'app di destinazione. Per imporre all'_autorizzazione_ di consentire solo determinate applicazioni client, è necessario eseguire alcune operazioni di configurazione aggiuntive.

1. [Definire un ruolo dell'app](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) nel manifesto della registrazione dell'app che rappresenta il servizio app o l'app per le funzioni che si vuole proteggere.
1. Nella registrazione app che rappresenta il client che deve essere autorizzato selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **API personali**.
1. Selezionare la registrazione app creata in precedenza. Se la registrazione app non è visualizzata, verificare di aver [aggiunto un ruolo dell'app](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. In **Autorizzazioni applicazione** selezionare il ruolo dell'app creato in precedenza e quindi selezionare **Aggiungi autorizzazioni**.
1. Assicurarsi di fare clic su **Fornisci il consenso amministratore** per autorizzare l'applicazione client a richiedere l'autorizzazione.
1. Analogamente allo scenario precedente (prima dell'aggiunta di eventuali ruoli), è ora possibile [richiedere un token di accesso](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) per la stessa `resource` di destinazione e il token di accesso includerà un'attestazione `roles` contenente i ruoli dell'app autorizzati per l'applicazione client.
1. All'interno del servizio app di destinazione o del codice dell'app per le funzioni, è ora possibile verificare che i ruoli previsti siano presenti nel token. Questa operazione non viene eseguita dall'autenticazione/autorizzazione del servizio app. Per altre informazioni, vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims).

È stata configurata un'applicazione client daemon che può accedere all'app del servizio app usando la propria identità.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel Servizio app di Azure](app-service-web-tutorial-auth-aad.md)
<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
