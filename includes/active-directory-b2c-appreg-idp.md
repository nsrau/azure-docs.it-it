---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 560e29be308277aedf8ac4e438f73d4ac5bc417f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475349"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza di **applicazioni** corrente o la nuova esperienza di **registrazioni app (anteprima)** unificata. [Scopri di più sull'esperienza di anteprima](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *testapp1*.
1. Per **app Web/API Web**, selezionare **Sì**.
1. Per **URL di risposta**immettere `https://jwt.ms`
1. Selezionare **Crea**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **registrazioni app (anteprima)** , quindi selezionare **nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *testapp1*.
1. Selezionare **account in qualsiasi directory organizzativa o provider di identità**.
1. In **URI di reindirizzamento**selezionare **Web**, quindi immettere `https://jwt.ms` nella casella di testo URL.
1. In **autorizzazioni**selezionare la casella *di controllo Concedi il consenso dell'amministratore per le autorizzazioni OpenID e offline_access* .
1. Selezionare **Registra**.

Al termine della registrazione dell'applicazione, abilitare il flusso di concessione implicita:

1. In **Gestisci**selezionare **autenticazione**.
1. Selezionare **prova la nuova esperienza** (se visualizzata).
1. In **concessione implicita**selezionare le caselle di controllo **token di accesso** e **token ID** .
1. Selezionare **Salva**.