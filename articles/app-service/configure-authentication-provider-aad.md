---
title: Configurare l'autenticazione di Azure AD
description: Informazioni su come configurare l'autenticazione di Azure Active Directory come provider di identità per l'app del servizio app.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: fdad1f820d006c39fa135a29a5ec7377c47591f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80046453"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Configurare l'app del servizio app per usare l'account di accesso di Azure ADConfigure your App Service app to use Azure AD login

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare il servizio app di Azure per usare Azure Active Directory (Azure AD) come provider di autenticazione.

Segui queste procedure consigliate per la configurazione dell'app e dell'autenticazione:

- Assegnare a ogni app del servizio app le proprie autorizzazioni e il proprio consenso.
- Configurare ogni app del servizio app con la propria registrazione.
- Evitare la condivisione delle autorizzazioni tra ambienti usando registrazioni di app separate per slot di distribuzione separati. Quando si testa nuovo codice, questa procedura consente di evitare problemi che influiscono sull'app di produzione.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configurazione con impostazioni rapide

> [!NOTE]
> L'opzione **Express** non è disponibile per i cloud governativi. 

1. Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app.
2. Nel riquadro di spostamento sinistro selezionare **Autenticazione/Autorizzazione** > **attivata**.
3. Selezionare **Azure Active Directory** > **Express**.

   Se invece vuoi scegliere una registrazione dell'app esistente:

   1. Scegliere **Seleziona app AD esistente**, quindi fare clic su App Azure **AD**.
   2. Scegliere una registrazione dell'app esistente e fare clic su **OK**.

3. Selezionare **OK** per registrare l'applicazione dei servizi app in Azure Active Directory. Viene creata una nuova registrazione dell'app.
   
    ![Impostazioni rapide in Azure Active DirectoryExpress settings in Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Facoltativo) Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso autorizzato al contenuto e alle API del sito. È necessario autorizzare gli utenti nel codice dell'app. Per limitare l'accesso alle app solo agli utenti autenticati da Azure Active Directory, impostare **Azione da eseguire quando la richiesta non è autenticata su** Accedi con Azure Active **Directory.** Quando imposti questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza inoltre tutti i dati non autenticati ad Azure Active Directory per l'autenticazione.

    > [!CAUTION]
    > Limitare l'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderabili per le app che dispongono di una home page disponibile pubblicamente, come in molte applicazioni a pagina singola. Per tali applicazioni, **Consenti richieste anonime (nessuna azione)** potrebbe essere preferito, con l'applicazione che avvia manualmente l'accesso stesso. Per ulteriori informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).
5. Selezionare **Salva**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configurazione con impostazioni avanzate

È possibile configurare manualmente le impostazioni dell'app se si vuole usare la registrazione di un'app da un tenant di Azure AD diverso. Per completare questa configurazione personalizzata:

1. Creare una registrazione in Azure AD.
2. Fornire alcuni dettagli di registrazione al servizio app.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Creare una registrazione dell'app in Azure AD per l'app del servizio appCreate an app registration in Azure AD for your App Service app

Quando si configura l'app del servizio app, sono necessarie le informazioni seguenti:You'll need the following information when you configure your App Service app:

- ID client
- ID tenant
- Segreto client (facoltativo)
- URI ID applicazione

Eseguire la procedura seguente:

1. Accedere al portale di [Azure,]cercare e selezionare **App Services**e quindi selezionare l'app. Nota **l'URL**della tua app. Lo userai per configurare la registrazione dell'app Azure Active Directory.
1. Selezionare**Registrazioni** > app **Azure Active Directory** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un **Nome** per la registrazione dell'app.
1. In **URI di reindirizzamento**selezionare **Web** e digitare `<app-url>/.auth/login/aad/callback`. Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Selezionare **Crea**.
1. Dopo aver creato la registrazione dell'app, copiare **l'ID applicazione (client)** e **l'ID directory (tenant)** per un secondo momento.
1. Selezionare **Personalizzazione**. In **URL pagina iniziale**immettere l'URL dell'app del servizio app e selezionare **Salva**.
1. Selezionare Esporre un**set** **di API** > . Incollare l'URL dell'app del servizio app e selezionare **Salva**.

   > [!NOTE]
   > Questo valore è **l'URI dell'ID applicazione** della registrazione dell'app. Se l'app Web richiede l'accesso a un'API nel cloud, è necessario **l'URI dell'ID applicazione** dell'app Web quando si configura la risorsa del servizio app cloud. È possibile usare questa opzione, ad esempio, se si vuole che il servizio cloud conceda in modo esplicito l'accesso all'app Web.

1. Selezionare **Aggiungi un ambito**.
   1. In **Nome ambito**immettere *user_impersonation*.
   1. Nelle caselle di testo immettere il nome e la descrizione dell'ambito di consenso che si desidera vengano visualizzati dagli utenti nella pagina del consenso. Ad esempio, immettere *Accedi all'app*. 
   1. Selezionare **Aggiungi ambito**.
1. (Facoltativo) Per creare un segreto client, selezionare **Certificati & segreti** > **Nuovo segreto** > client**Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Non verrà più visualizzato.
1. (Facoltativo) Per aggiungere più **URL di risposta,** selezionare **Autenticazione**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Abilitare Azure Active Directory nell'app del servizio appEnable Azure Active Directory in your App Service app

1. Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app. 
1. Nel riquadro sinistro, in **Impostazioni**, selezionare **Autenticazione/Autorizzazione** > **attivata**.
1. (Facoltativo) Per impostazione predefinita, l'autenticazione del servizio app consente l'accesso non autenticato all'app. Per applicare l'autenticazione utente, impostare **Azione da eseguire quando la richiesta non è autenticata** su Accedi con Azure Active **Directory**.
1. In **Provider di autenticazione** fare clic su **Azure Active Directory**.
1. In **modalità di gestione**selezionare **Avanzate** e configurare l'autenticazione del servizio app in base alla tabella seguente:

    |Campo|Descrizione|
    |-|-|
    |ID client| Utilizzare **l'ID applicazione (client)** della registrazione dell'app. |
    |ID emittente| Usare `https://login.microsoftonline.com/<tenant-id>`e sostituire * \<>id-tenant* con l'ID **directory (tenant)** della registrazione dell'app. |
    |Segreto client (facoltativo)| Usa il segreto client generato nella registrazione dell'app.|
    |Gruppi di destinatari di token consentiti| Se si tratta di un'app cloud o server e si desidera consentire i token di autenticazione da un'app Web, aggiungere qui **l'URI ID applicazione** dell'app Web. **L'ID client** configurato viene *sempre* considerato implicitamente come un gruppo di destinatari consentito. |

2. Selezionare **OK** e quindi **Salva**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'app del servizio app.

## <a name="configure-a-native-client-application"></a>Configurare un'applicazione client nativa

È possibile registrare i client nativi per consentire l'autenticazione utilizzando una libreria client, ad esempio **Active Directory Authentication Library**.

1. Nel [portale di Azure]selezionare**Registrazioni** > app **Active Directory** > **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** immettere un **Nome** per la registrazione dell'app.
1. In **URI di reindirizzamento**selezionare Client pubblico `<app-url>/.auth/login/aad/callback` **(mobile & desktop)** e digitare l'URL . Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Per un'applicazione Windows, usare invece il [SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) del pacchetto come URI.
1. Selezionare **Crea**.
1. Dopo aver creato la registrazione dell'app, copiare il valore di **ID applicazione (client).**
1. Selezionare **Autorizzazioni** > API**Aggiungere un'autorizzazione** > **API**personali .
1. Selezionare la registrazione dell'app creata in precedenza per l'app del servizio app. Se non vedi la registrazione dell'app, assicurati di aver aggiunto l'ambito **user_impersonation** in Creare una [registrazione dell'app in Azure AD per l'app del servizio app.](#register)
1. Selezionare **user_impersonation**, quindi **Aggiungi autorizzazioni**.

Ora è stata configurata un'applicazione client nativa che può accedere all'applicazione dei servizi app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Portale di Azure]: https://portal.azure.com/
