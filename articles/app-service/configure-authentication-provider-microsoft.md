---
title: Configurare l'autenticazione di Microsoft
description: Informazioni su come configurare l'autenticazione dell'account Microsoft come provider di identità per il servizio app o l'app Funzioni di Azure.Learn how to configure Microsoft Account authentication as an identity provider for your App Service or Azure Functions app.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: ea745aa00f8990a2d1232a19780fdc70e2f78996
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437958"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Configurare il servizio app o l'app Funzioni di Azure per usare l'account di accesso MicrosoftConfigure your App Service or Azure Functions app to use Microsoft Account login

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento illustra come configurare il servizio app di Azure o funzioni di Azure per usare AAD per supportare gli account di accesso personali dell'account Microsoft.This topic shows you how to configure Azure App Service or Azure Functions to use AAD to support personal Microsoft account logins.

> [!NOTE]
> Sia gli account Microsoft personali che gli account dell'organizzazione utilizzano il provider di identità AAD. Al momento non è possibile configurare questo provider di identità per supportare entrambi i tipi di accesso.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrare l'app con l'account Microsoft

1. Passare a Registrazioni app nel portale di Azure.Go to [**App registrations**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in the Azure portal. Se necessario, accedi con il tuo account Microsoft.
1. Selezionare **Nuova registrazione**, quindi immettere il nome di un'applicazione.
1. In **Tipi di account supportati**selezionare Account in qualsiasi directory **dell'organizzazione (Qualsiasi directory di Azure AD - Multitenant) e account Microsoft personali (ad esempio Skype, Xbox)**
1. In **URI di reindirizzamento**selezionare `https://<app-domain-name>/.auth/login/aad/callback` **Web**e quindi immettere . Sostituisci il * \<nome del dominio-app>* con il nome di dominio dell'app.  Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assicurarsi di utilizzare lo schema HTTPS nell'URL.

1. Selezionare **Registra**.
1. Copiare **l'ID applicazione (client)**. Sarà necessario più avanti.
1. Nel riquadro sinistro selezionare **Certificati & segreti** > **Nuovo segreto client**. Immettere una descrizione, selezionare la durata di validità e selezionare **Aggiungi**.
1. Copiare il valore visualizzato nella pagina **Certificati & segreti.** Dopo aver lasciato la pagina, non verrà più visualizzata.

    > [!IMPORTANT]
    > Il valore segreto client (password) è una credenziale di sicurezza importante. Non condividerla con altri e non distribuirla all'interno di un'applicazione client.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Aggiungere le informazioni dell'account Microsoft all'applicazione del servizio app

1. Passare all'applicazione nel portale di [Azure].
1. Selezionare **Autenticazione** > **impostazioni/Autorizzazione**e assicurarsi che **l'autenticazione** del servizio app sia **attivata.**
1. In **Provider di autenticazione** fare clic su **Azure Active Directory**. Selezionare **Avanzate** in **Modalità di gestione**. Incollare l'ID applicazione (client) e il segreto client ottenuti in precedenza. Utilizzare **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** per il campo **URL autorità emittente.**
1. Selezionare **OK**.

   Il servizio app fornisce l'autenticazione, ma non limita l'accesso autorizzato al contenuto e alle API del sito. È necessario autorizzare gli utenti nel codice dell'app.

1. (Facoltativo) Per limitare l'accesso agli utenti dell'account Microsoft, impostare **Azione da eseguire quando la richiesta non è autenticata** su Accedi con Azure Active **Directory**. Quando imposti questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza inoltre tutte le richieste non autenticate per utilizzare AAD per l'autenticazione. Si noti che, poiché l'URL dell'autorità **emittente** è stato configurato per l'utilizzo del tenant dell'account Microsoft, solo gli sconti personali verranno autenticati correttamente.

   > [!CAUTION]
   > Limitare l'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderabili per le app che dispongono di una home page disponibile pubblicamente, come in molte applicazioni a pagina singola. Per tali applicazioni, **consenti richieste anonime (nessuna azione)** potrebbe essere preferibile in modo che l'applicazione avvia manualmente l'autenticazione stessa. Per ulteriori informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva**.

È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portale di Azure]: https://portal.azure.com/
