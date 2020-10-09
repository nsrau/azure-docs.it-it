---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 65fb406fa4f52f19b4d8e9dc4e3895207ffe6336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793643"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *nativeapp1*.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa o qualsiasi provider di identità**.
1. In **URI di reindirizzamento** usare l'elenco a discesa per selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)** .
1. Immettere un URI di reindirizzamento con uno schema univoco. Ad esempio: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti alcune considerazioni importanti:
    * **Sviluppo** Per l'uso in ambiente di sviluppo, è possibile impostare l'URI di reindirizzamento su `http://localhost` e Azure AD B2C rispetterà qualsiasi porta nella richiesta. Se l'URI registrato contiene una porta, Azure AD B2C userà solo quella porta. Ad esempio, se l'URI di reindirizzamento registrato è `http://localhost`, l'URI di reindirizzamento nella richiesta può essere `http://localhost:<randomport>`. Se l'URI di reindirizzamento registrato è `http://localhost:8080`, l'URI di reindirizzamento nella richiesta deve essere `http://localhost:8080`.
    * **Univoco**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se l'utente sceglie in modo non corretto, l'accesso ha esito negativo.
    * **Completezza**: l'URI di reindirizzamento deve avere sia uno schema che un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//oauth/` funziona mentre `//oauth` ha esito negativo. Non includere caratteri speciali nell'URI, ad esempio, i caratteri di sottolineatura.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
2. Selezionare **Registra**.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *nativeapp1*.
1. Per **Client nativo**, selezionare **Sì**.
1. Immettere un **URI di reindirizzamento personalizzato** con uno schema univoco. Ad esempio: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:
    * **Univoco**: lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se l'utente sceglie in modo non corretto, l'accesso ha esito negativo.
    * **Completezza**: l'URI di reindirizzamento deve avere sia uno schema che un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//oauth/` funziona mentre `//oauth` ha esito negativo. Non includere caratteri speciali nell'URI, ad esempio, i caratteri di sottolineatura.
1. Selezionare **Crea**.