---
title: Configurare l'autenticazione Microsoft
description: Informazioni su come configurare l'autenticazione con account Microsoft come provider di identità per un'app del servizio app o di Funzioni di Azure.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043253"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Configurare un'app del servizio app o di Funzioni di Azure per l'uso dell'account Microsoft come account di accesso

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento illustra come configurare Servizio app di Azure o Funzioni di Azure per usare AAD per supportare gli account Microsoft di accesso personali.

> [!NOTE]
> Sia gli account Microsoft personali che gli account aziendali usano il provider di identità AAD. Al momento non è possibile configurare questo provider di identità per supportare entrambi i tipi di account di accesso.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrare l'app con l'account Microsoft

1. Passare a [**Registrazioni app**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) nel portale di Azure. Se necessario, accedere con il proprio account Microsoft.
1. Selezionare **Nuova registrazione** e quindi immettere un nome per l'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione (qualsiasi directory di Azure AD - Multi-tenant) e account Microsoft personali (ad esempio, Skype, Xbox)** .
1. In **URI di reindirizzamento** selezionare **Web** e quindi immettere `https://<app-domain-name>/.auth/login/aad/callback`. Sostituire *\<app-domain-name>* con il nome di dominio dell'app.  Ad esempio: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assicurarsi di usare lo schema HTTPS nell'URL.

1. Selezionare **Registra** .
1. Copiare il valore di **ID applicazione (client)** . Sarà necessario più avanti.
1. Nel riquadro sinistro selezionare **Certificati e segreti** > **Nuovo segreto client** . Immettere una descrizione, selezionare la durata della validità e selezionare **Aggiungi** .
1. Copiare il valore visualizzato nella pagina **Certificati e segreti** . Una volta chiusa la pagina, il valore non verrà più mostrato.

    > [!IMPORTANT]
    > Il valore del segreto client (password) è un'importante credenziale di sicurezza. Non condividerla con altri e non distribuirla all'interno di un'applicazione client.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Aggiungere le informazioni dell'account Microsoft all'applicazione del servizio app

1. Passare all'applicazione nel [Azure portal].
1. Selezionare **Impostazioni** > **Autenticazione/Autorizzazione** e assicurarsi che l'opzione **Autenticazione servizio app** sia impostata su **Sì** .
1. In **Provider di autenticazione** fare clic su **Azure Active Directory** . Selezionare **Avanzate** in **Modalità di gestione** . Incollare l'ID dell'applicazione (client) e il segreto client ottenuti in precedenza. Usare **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** per il campo **URL autorità di certificazione** .
1. Selezionare **OK** .

   Il servizio app fornisce l'autenticazione, ma non limita l'accesso alle API e al contenuto del sito solo agli utenti autorizzati. È necessario autorizzare gli utenti nel codice dell'app.

1. Per limitare l'accesso agli utenti con account Microsoft, impostare **Azione da eseguire quando la richiesta non è autenticata** su **Accedi con Azure Active Directory** . Quando si imposta questa funzionalità, l'app richiede che tutte le richieste vengano autenticate. Reindirizza anche tutte le richieste non autenticate per l'uso di AAD per l'autenticazione. Si noti che poiché l'URL dell' **autorità emittente** è stato configurato per l'uso del tenant dell'account Microsoft, solo gli account personali avranno esito positivo.

   > [!CAUTION]
   > La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, il che potrebbe non essere opportuno per le app dotate di una home page disponibile pubblicamente, come nel caso di molte applicazioni a pagina singola. Per queste applicazioni, potrebbe essere preferibile scegliere **Consenti richieste anonime (nessuna azione)** in modo che l'app possa avviare l'autenticazione manualmente in autonomia. Per altre informazioni, vedere [Flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Selezionare **Salva** .

È ora possibile usare l'account Microsoft per l'autenticazione nell'app.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
