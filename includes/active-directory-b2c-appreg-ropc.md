---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f8d8ffe8c1802a5cf67b7dc2b0fb071b572d76e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994359"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *ROPC_Auth_app*.
1. Lasciare gli altri valori così come sono e quindi selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.
1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Tipo di client predefinito** selezionare **Sì** per gestire l'applicazione come client pubblico. Questa impostazione è obbligatoria per il flusso ROPC.
1. Selezionare **Salva**.
1. Nel menu a sinistra, selezionare **Manifesto** per aprire l'editor del manifesto. 
1. Impostare l’attributo **oauth2AllowImplicitFlow** su *true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Selezionare **Salva**.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *ROPC_Auth_app*.
1. Per **Client nativo**, selezionare **Sì**.
1. Lasciare gli altri valori così come sono e quindi selezionare **Crea**.
1. Prendere nota del valore di **ID APPLICAZIONE**, necessario in un passaggio successivo.