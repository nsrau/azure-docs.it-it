---
title: Configurare l'autenticazione di Azure AD
description: Informazioni su come configurare l'autenticazione Azure Active Directory come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 60a5d50b511fc9db02daa9b7e74eedfe40eeb7a5
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609902"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare Azure AD account di accesso

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare Azure Active Directory (Azure AD) come provider di autenticazione.

> [!NOTE]
> Il flusso di impostazioni rapide configura una registrazione dell'applicazione AAD V1. Se si vuole usare [Azure Active Directory v 2.0](../active-directory/develop/v2-overview.md) (incluso [MSAL](../active-directory/develop/msal-overview.md)), seguire le istruzioni per la [configurazione avanzata](#advanced).

Seguire queste procedure consigliate durante la configurazione dell'app e dell'autenticazione:

- Assegnare a ogni app del servizio app le autorizzazioni e il consenso.
- Configurare ogni app del servizio app con la propria registrazione.
- Evitare la condivisione delle autorizzazioni tra ambienti usando registrazioni di app separate per slot di distribuzione distinti. Quando si esegue il test di un nuovo codice, questa procedura consente di evitare problemi che interessano l'app di produzione.

> [!NOTE]
> Questa funzionalità non è attualmente disponibile nel piano a consumo Linux per funzioni di Azure

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configurazione con impostazioni rapide

> [!NOTE]
> L'opzione **Express** non è disponibile per i cloud governativi.

1. Nella [portale di Azure]cercare e selezionare **Servizi app**e quindi selezionare l'app.
2. Nel percorso di spostamento**a**sinistra selezionare **autenticazione/autorizzazione** > .
3. Selezionare **Azure Active Directory** > **Express**.

   Se invece si vuole scegliere una registrazione dell'app esistente:

   1. Scegliere **Seleziona app ad esistente**, quindi fare clic su **app Azure ad**.
   2. Scegliere una registrazione dell'app esistente e fare clic su **OK**.

3. Selezionare **OK** per registrare l'applicazione dei servizi app in Azure Active Directory. Viene creata una nuova registrazione dell'app.

    ![Impostazioni rapide in Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)

4. Opzionale Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto del sito e alle API. È necessario autorizzare gli utenti nel codice dell'app. Per limitare l'accesso alle app solo agli utenti autenticati da Azure Active Directory, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per **accedere con Azure Active Directory**. Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutti i Azure Active Directory non autenticati per l'autenticazione.

    > [!CAUTION]
    > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere utili per le app con un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** potrebbe essere preferibile, con l'app che avvia manualmente l'accesso. Per altre informazioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).
5. Selezionare **Salva**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configurazione con impostazioni avanzate

È possibile configurare manualmente le impostazioni dell'app se si vuole usare la registrazione di un'app da un tenant di Azure AD diverso. Per completare questa configurazione personalizzata:

1. Creare una registrazione in Azure AD.
2. Fornire alcuni dettagli di registrazione al servizio app.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Creare una registrazione dell'app in Azure ad per l'app del servizio app

Quando si configura l'app del servizio app, sono necessarie le informazioni seguenti:

- ID client
- ID tenant
- Segreto client (facoltativo)
- URI ID applicazione

Eseguire la procedura seguente:

1. Accedere alla [portale di Azure], cercare e selezionare **Servizi app**e quindi selezionare l'app. Prendere nota dell' **URL**dell'app. Verrà usato per configurare la registrazione dell'app Azure Active Directory.
1. Selezionare **Azure Active Directory** > **registrazioni app** > **nuova registrazione**.
1. Nella pagina **registra un'applicazione** immettere un **nome** per la registrazione dell'app.
1. In **URI di reindirizzamento**selezionare **Web** e digitare `<app-url>/.auth/login/aad/callback`. Ad esempio, `https://contoso.azurewebsites.net/.auth/login/aad/callback`
1. Selezionare **Crea**.
1. Dopo la creazione della registrazione dell'app, copiare l'ID **applicazione (client)** e l' **ID directory (tenant)** per un momento successivo.
1. Selezionare **Autenticazione**. In **concessione implicita**abilitare i **token ID** per consentire gli accessi utente di OpenID Connect dal servizio app.
1. Opzionale Selezionare **personalizzazione**. In **URL della Home page**immettere l'URL dell'app del servizio app e selezionare **Salva**.
1. Selezionare **esporre un set di API** > **Set**. Per l'app single-tenant, incollare l'URL dell'app del servizio app e selezionare **Salva** e per app multi-tenant, incollare l'URL basato su uno dei domini verificati del tenant e quindi selezionare **Salva**.

   > [!NOTE]
   > Questo valore è l' **URI dell'ID applicazione** della registrazione dell'app. Se l'app Web richiede l'accesso a un'API nel cloud, è necessario l' **URI dell'ID applicazione** dell'app Web quando si configura la risorsa del servizio app cloud. Questa operazione può essere usata, ad esempio, se si vuole che il servizio cloud conceda in modo esplicito l'accesso all'app Web.

1. Selezionare **Aggiungi un ambito**.
   1. In **nome ambito**immettere *user_impersonation*.
   1. Nelle caselle di testo immettere il nome dell'ambito di consenso e la descrizione che si desidera che gli utenti visualizzino nella pagina di consenso. Ad esempio, immettere *Accedi all'app*.
   1. Selezionare **Aggiungi ambito**.
1. Opzionale Per creare un segreto client, selezionare **certificati &** > Secret**nuovo segreto** > client**Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Non verrà più visualizzato.
1. Opzionale Per aggiungere più **URL di risposta**, selezionare **Authentication (autenticazione**).

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Abilitare Azure Active Directory nell'app del servizio app

1. Nella [portale di Azure]cercare e selezionare **Servizi app**e quindi selezionare l'app.
1. Nel riquadro sinistro, in **Impostazioni**, selezionare **autenticazione/autorizzazione** > **.**
1. Opzionale Per impostazione predefinita, l'autenticazione del servizio app consente l'accesso non autenticato all'app. Per applicare l'autenticazione utente, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per l' **accesso con Azure Active Directory**.
1. In **Provider di autenticazione** fare clic su **Azure Active Directory**.
1. In **modalità di gestione**selezionare **Avanzate** e configurare l'autenticazione del servizio app in base alla tabella seguente:

    |Campo|Descrizione|
    |-|-|
    |ID client| Usare l' **ID applicazione (client)** della registrazione dell'app. |
    |URL autorità di certificazione| Usare `<authentication-endpoint>/<tenant-id>/v2.0`e sostituire * \<Authentication-endpoint>* con l' [endpoint di autenticazione per l'ambiente cloud](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) , ad esempio "https://login.microsoft.com" per Azure globale, sostituendo * \<anche l'ID tenant>* con l'ID di **Directory (tenant)** in cui è stata creata la registrazione dell'app. Questo valore viene usato per reindirizzare gli utenti al tenant di Azure AD corretto, nonché per scaricare i metadati appropriati per determinare le chiavi di firma del token appropriate e il valore dell'attestazione dell'emittente del token, ad esempio. La `/v2.0` sezione può essere omessa per le applicazioni che usano AAD V1. |
    |Segreto client (facoltativo)| Usare il segreto client generato nella registrazione dell'app.|
    |Destinatari di token consentiti| Se si tratta di un'app Cloud o server e si desidera consentire i token di autenticazione da un'app Web, aggiungere qui l' **URI dell'ID applicazione** dell'app Web. L' **ID client** configurato viene *sempre* considerato in modo implicito come un pubblico consentito. |

2. Selezionare **OK** e quindi **Salva**.

A questo punto si è pronti per usare Azure Active Directory per l'autenticazione nell'app del servizio app.

## <a name="configure-a-native-client-application"></a>Configurare un'applicazione client nativa

È possibile registrare client nativi per consentire l'autenticazione all'API Web ospitata nell'app usando una libreria client come la **Active Directory Authentication Library**.

1. Nella [portale di Azure]selezionare **Active Directory** > **registrazioni app** > **nuova registrazione**.
1. Nella pagina **registra un'applicazione** immettere un **nome** per la registrazione dell'app.
1. In **URI di reindirizzamento**selezionare **client pubblico (mobile & desktop)** e digitare l'URL `<app-url>/.auth/login/aad/callback`. Ad esempio, `https://contoso.azurewebsites.net/.auth/login/aad/callback`

    > [!NOTE]
    > Per un'applicazione Microsoft Store, utilizzare invece il [SID del pacchetto](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) come URI.
1. Selezionare **Crea**.
1. Dopo la creazione della registrazione dell'app, copiare il valore dell' **ID applicazione (client)**.
1. Selezionare **autorizzazioni** > **API Aggiungi un'autorizzazione** > **API personali**.
1. Selezionare la registrazione dell'app creata in precedenza per l'app del servizio app. Se la registrazione dell'app non viene visualizzata, assicurarsi di avere aggiunto l'ambito **user_impersonation** in [creare una registrazione dell'app in Azure ad per l'app del servizio app](#register).
1. Selezionare **user_impersonation**, quindi selezionare **Aggiungi autorizzazioni**.

A questo punto è stata configurata un'applicazione client nativa che può accedere all'app del servizio app per conto di un utente.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
