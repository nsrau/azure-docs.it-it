---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678053"
---
## <a name="register-an-azure-ad-app"></a>Registrare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD dell'organizzazione (ad esempio, contoso.com). Nel menu superiore, selezionare il **filtro Directory e sottoscrizione** e quindi scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **Nome** per l'applicazione. Ad esempio: `Azure AD B2C App`.
1. Accettare la selezione predefinita **Account in questa directory dell'organizzazione solo** per questa applicazione.
1. Per l'URI di **reindirizzamento**, accettare il valore di **Web** `your-B2C-tenant-name` e immettere l'URL seguente in lettere minuscole, dove viene sostituito con il nome del tenant B2C di Azure AD.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Ad esempio: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selezionare **Registra**. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.
1. Selezionare **Certificati & segreti**, quindi Nuovo **segreto client**.
1. Immettere una **Descrizione** per il segreto, selezionare una scadenza e quindi selezionare **Aggiungi**. Registrare il **valore** del segreto per l'utilizzo in un passaggio successivo.

### <a name="configuring-optional-claims"></a>Configurazione di attestazioni facoltative

Se si vuole `family_name` ottenere `given_name` le attestazioni e di Azure AD, è possibile configurare attestazioni facoltative per l'applicazione nell'interfaccia utente del portale di Azure o nel manifesto dell'applicazione. Per altre informazioni, vedere [Come fornire attestazioni facoltative all'app Azure AD.](/active-directory/develop/active-directory-optional-claims.md)

1. Accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare **Azure Active Directory**.
1. Nella sezione **Gestisci** selezionare **Registrazioni app**.
1. Selezionare l'applicazione per cui si desidera configurare attestazioni facoltative nell'elenco.
1. Nella sezione **Gestisci** selezionare **Configurazione token**.
1. Selezionare **Aggiungi attestazione facoltativa**.
1. Per tipo **di token**, selezionare **ID**.
1. Selezionare le attestazioni `family_name` `given_name`facoltative da aggiungere e .
1. Scegliere **Aggiungi**.