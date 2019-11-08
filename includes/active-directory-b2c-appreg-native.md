---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0b5c5fdddeea961858a2c3b8cd69c365bb28f26e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799910"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **Applicazioni** corrente o la nuova esperienza **Registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *nativeapp1*.
1. Per **Client nativo**, selezionare **Sì**.
1. Immettere un **URI di reindirizzamento personalizzato** con uno schema univoco. Ad esempio: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:
    * **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se l'utente sceglie in modo non corretto, l'accesso ha esito negativo.
    * **Completezza**: l'URI di reindirizzamento deve avere sia uno schema che un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//oauth/` funziona mentre `//oauth` ha esito negativo. Non includere caratteri speciali nell'URI, ad esempio, i caratteri di sottolineatura.
1. Selezionare **Create** (Crea).

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **Nome** per l'applicazione. Ad esempio, *nativeapp1*.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa o qualsiasi provider di identità**.
1. In **URI di reindirizzamento** usare l'elenco a discesa per selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)** .
1. Immettere un URI di reindirizzamento con uno schema univoco. Ad esempio: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:
    * **Univocità**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se l'utente sceglie in modo non corretto, l'accesso ha esito negativo.
    * **Completezza**: l'URI di reindirizzamento deve avere sia uno schema che un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//oauth/` funziona mentre `//oauth` ha esito negativo. Non includere caratteri speciali nell'URI, ad esempio, i caratteri di sottolineatura.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.