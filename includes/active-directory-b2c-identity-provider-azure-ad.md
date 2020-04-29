---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678053"
---
## <a name="register-an-azure-ad-app"></a>Registrare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant del Azure AD aziendale (ad esempio, contoso.com). Selezionare il **filtro directory + sottoscrizione** nel menu in alto e quindi scegliere la directory che contiene il tenant Azure ad.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio: `Azure AD B2C App`.
1. Accetta la selezione predefinita degli **account in questa directory organizzativa solo** per questa applicazione.
1. Per l' **URI di reindirizzamento**accettare il valore di **Web**e immettere l'URL seguente in lettere minuscole, dove `your-B2C-tenant-name` viene sostituito con il nome del tenant Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selezionare **Registra**. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.
1. Selezionare **certificati & segreti**, quindi selezionare **nuovo segreto client**.
1. Immettere una **Descrizione** per il segreto, selezionare una scadenza e quindi selezionare **Aggiungi**. Registrare il **valore** del segreto da usare in un passaggio successivo.

### <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

Se si desidera ottenere le `family_name` attestazioni `given_name` e da Azure ad, è possibile configurare attestazioni facoltative per l'applicazione nell'interfaccia utente di portale di Azure o nel manifesto dell'applicazione. Per altre informazioni, vedere [How to provide optional Claims to your Azure ad app](/active-directory/develop/active-directory-optional-claims.md).

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **registrazioni app**.
1. Selezionare nell'elenco l'applicazione per la quale si desidera configurare attestazioni facoltative.
1. Nella sezione **Gestisci** selezionare **configurazione del token**.
1. Selezionare **Aggiungi attestazione facoltativa**.
1. Per il **tipo di token**selezionare **ID**.
1. Selezionare le attestazioni facoltative da `family_name` aggiungere `given_name`, e.
1. Fare clic su **Aggiungi**.