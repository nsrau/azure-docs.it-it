---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529172"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **Applicazioni** corrente o la nuova esperienza **Registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *ROPC_Auth_app*.
1. Per **Client nativo**, selezionare **Sì**.
1. Lasciare gli altri valori così come sono, quindi selezionare **Crea**.
1. Prendere nota del valore di **ID APPLICAZIONE**, necessario in un passaggio successivo.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *ROPC_Auth_app*.
1. Lasciare gli altri valori così come sono, quindi selezionare **Register (registra**).
1. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.
1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **tipo di client predefinito**selezionare **Sì** per gestire l'applicazione come client pubblico. Questa impostazione è obbligatoria per il flusso ROPC.
1. Selezionare **Salva**.
1. Nel menu a sinistra selezionare **manifesto** per aprire l'editor manifesto. 
1. Impostare l'attributo **oauth2AllowImplicitFlow** su *true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Selezionare **Salva**.