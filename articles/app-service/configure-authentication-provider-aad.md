---
title: Configurare l'autenticazione di Azure Active Directory - Servizio app di Azure
description: Informazioni su come configurare l'autenticazione Azure Active Directory per l'app del servizio app.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: 8de464a00867dd397f28de1dc35cf264244f6905
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743263"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Configurare l'app del servizio app per l'uso dell'accesso di Azure Active Directory

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> A questo punto, AAD V2 (incluso MSAL) non è supportato per app Azure servizio e funzioni di Azure.
>

Questo articolo illustra come configurare app Azure servizio per usare Azure Active Directory come provider di autenticazione.

Si consiglia di configurare ogni app del servizio app con la propria registrazione, in modo che disponga delle autorizzazioni e del consenso. Si consiglia inoltre di usare registrazioni di app separate per slot di distribuzione distinti. In questo modo si evita la condivisione delle autorizzazioni tra ambienti, in modo che un problema nel nuovo codice che si sta testando non influisca sulla produzione.

## <a name="express"> </a>Configurazione con impostazioni rapide

1. Nel [portale di Azure] passare alla pagina dell'applicazione dei servizi app. Nel riquadro di spostamento a sinistra selezionare **Autenticazione/Autorizzazione**.
2. Se l'opzione **Autenticazione/Autorizzazione** non è abilitata, selezionare **Attivato**.
3. Selezionare **Azure Active Directory** e quindi **Rapida** in **Modalità di gestione**.
4. Selezionare **OK** per registrare l'applicazione dei servizi app in Azure Active Directory. Viene creata una nuova registrazione per l'app. Se invece si intende scegliere la registrazione di un'app esistente, fare clic su **Seleziona un'app esistente** e quindi cercare il nome di una registrazione di app creata in precedenza all'interno del tenant. Fare clic sulla registrazione di app per selezionarla e fare clic su **OK**. Quindi, fare clic su **OK** nella pagina Impostazioni di Azure Active Directory.
Per impostazione predefinita, il servizio app fornisce l'autenticazione ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.
5. Opzionale Per limitare l'accesso all'app solo agli utenti autenticati da Azure Active Directory, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per **accedere con Azure Active Directory**. Per poter usare questa funzione, tuttavia, è necessario che tutte le richieste vengano autenticate e che le richieste non autenticate vengano reindirizzate ad Azure Active Directory per l'autenticazione.

    > [!NOTE]
    > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina. Per queste applicazioni, **consentire le richieste anonime (nessuna azione)** può essere preferibile, con l'app che avvia manualmente l'accesso, come descritto [qui](overview-authentication-authorization.md#authentication-flow).
6. Fare clic su **Save**.

## <a name="advanced"> </a>Configurazione con impostazioni avanzate

È anche possibile specificare manualmente le impostazioni di configurazione, se il tenant Azure Active Directory che si vuole usare è diverso dal tenant con cui si accede ad Azure. Per completare la configurazione, è innanzitutto necessario creare una registrazione in Azure Active Directory. È quindi necessario fornire alcune informazioni di registrazione al servizio app.

### <a name="register"> </a>Creare una registrazione dell'app in Azure ad per l'app del servizio app

Quando si crea manualmente una registrazione dell'app, prendere nota di tre informazioni che sarà necessario in seguito durante la configurazione dell'app del servizio app: l'ID client, l'ID tenant e, facoltativamente, il segreto client e l'URI dell'ID applicazione.

1. Nella [portale di Azure]passare all'app del servizio app e prendere nota dell' **URL**dell'app. Verrà usato per configurare la registrazione dell'app Azure Active Directory.
1. Nel [portale di Azure]scegliere **Active Directory** > **registrazioni app** > **nuova registrazione**dal menu a sinistra. 
1. Nella pagina **registra un'applicazione** immettere un **nome** per la registrazione dell'app.
1. In **URI di reindirizzamento**selezionare **Web** e digitare l'URL dell'app del servizio app e aggiungere il percorso `/.auth/login/aad/callback`. Ad esempio `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Selezionare quindi **Crea**.
1. Dopo la creazione della registrazione dell'app, copiare l'ID **applicazione (client)** e l' **ID directory (tenant)** per un momento successivo.
1. Selezionare **personalizzazione**. In **URL della Home page**Digitare l'URL dell'app del servizio app e selezionare **Salva**.
1. Selezionare **esporre un set di API** > . Incollare l'URL dell'app del servizio app e selezionare **Salva**.

    > [!NOTE]
    > Questo valore è l' **URI dell'ID applicazione** della registrazione dell'app. Se si vuole avere un'app Web front-end per accedere a un'API back-end, ad esempio, e si vuole che il back-end conceda in modo esplicito l'accesso al front-end, è necessario l' **URI dell'ID applicazione** del *front-end* quando si configura la risorsa dell'app del servizio app di < C2 > back-end.
1. Selezionare **Aggiungi un ambito**. In **nome ambito**Digitare *user_impersonation*. Nelle caselle di testo digitare il nome dell'ambito di consenso e la descrizione che si desidera che gli utenti visualizzino nella pagina di consenso, ad esempio *Accedi all'app*. Al termine, fare clic su **Aggiungi ambito**.
1. Opzionale Per creare un segreto client, selezionare **certificati &**  > Secret**nuovo segreto** > client**Aggiungi**. Copiare il valore del segreto client visualizzato nella pagina. Quando ci si sposta, non verrà più visualizzato.
1. Opzionale Per aggiungere più **URL di risposta**, selezionare **autenticazione** nel menu.

### <a name="secrets"> </a>Aggiungere informazioni di Azure Active Directory all'applicazione dei servizi app

1. Nel [portale di Azure] passare alla pagina dell'applicazione dei servizi app. Nel menu a sinistra selezionare **autenticazione/autorizzazione**. Se la funzionalità autenticazione/autorizzazione non è abilitata, selezionare **on**. 
1. Opzionale Per impostazione predefinita, l'autenticazione del servizio app consente l'accesso non autenticato all'app. Per applicare l'autenticazione utente, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per l' **accesso con Azure Active Directory**.
1. In provider di autenticazione selezionare **Azure Active Directory**.
1. In **modalità di gestione**selezionare **Avanzate** e configurare l'autenticazione del servizio app in base alla tabella seguente:

    |Campo|Descrizione|
    |-|-|
    |ID client| Usare l' **ID applicazione (client)** della registrazione dell'app. |
    |ID autorità emittente| Usare `https://login.microsoftonline.com/<tenant-id>`e  *sostituire\<Tenant-ID >* con l' **ID di directory (tenant)** della registrazione dell'app. |
    |Segreto client (facoltativo)| Usare il segreto client generato nella registrazione dell'app.|
    |Destinatari token consentiti| Se si tratta di un'app *back-end* e si desidera consentire i token di autenticazione da un'app front-end, aggiungere qui l' **URI dell'ID applicazione** del *front* -end. |

    > [!NOTE]
    > L' **ID client** configurato viene *sempre* considerato in modo implicito come destinatari consentiti, indipendentemente dal modo in cui sono stati configurati i **destinatari dei token consentiti**.
1. Selezionare **OK**, quindi fare clic su **Salva**.

È ora possibile usare Azure Active Directory per l'autenticazione nell'applicazione dei servizi app.

## <a name="configure-a-native-client-application"></a>Configurare un'applicazione client nativa
È possibile registrare i client nativi se si desidera eseguire accessi utilizzando una libreria client come la **Active Directory Authentication Library**.

1. Nel [portale di Azure]scegliere **Active Directory** > **registrazioni app** > **nuova registrazione**dal menu a sinistra. 
1. Nella pagina **registra un'applicazione** immettere un **nome** per la registrazione dell'app.
1. In **URI di reindirizzamento**selezionare **client pubblico (Mobile & desktop)** e digitare l'URL dell'app del servizio app e aggiungere il percorso `/.auth/login/aad/callback`. Ad esempio `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Selezionare quindi **Crea**.

    > [!NOTE]
    > Per un'applicazione Windows, usare invece il [SID del pacchetto](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) come URI.
1. Dopo la creazione della registrazione dell'app, copiare il valore dell' **ID applicazione (client)** .
1. Dal menu a sinistra, selezionare **autorizzazioni** > API**Aggiungi un'autorizzazione** > **API personali**.
1. Selezionare la registrazione dell'app creata in precedenza per l'app del servizio app. Se la registrazione dell'app non viene visualizzata, verificare di aver aggiunto l'ambito **user_impersonation** in [creare una registrazione dell'app in Azure ad per l'app del servizio app](#register).
1. Selezionare **user_impersonation** e fare clic su **Aggiungi autorizzazioni**.

Ora è stata configurata un'applicazione client nativa che può accedere all'applicazione dei servizi app.

## <a name="related-content"></a>Contenuti correlati

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Portale di Azure]: https://portal.azure.com/
[alternative method]:#advanced
