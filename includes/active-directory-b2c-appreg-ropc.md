---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 35ddec2d605e17a1bb91b338e4e5402c6d47db57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474775"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza di **applicazioni** corrente o la nuova esperienza di **registrazioni app (anteprima)** unificata. [Scopri di più sull'esperienza di anteprima](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *ROPC_Auth_app*.
1. Per **Native Client**, selezionare **Sì**.
1. Lasciare gli altri valori così come sono, quindi selezionare **Crea**.
1. Registrare l' **ID applicazione** per l'uso in un passaggio successivo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **registrazioni app (anteprima)** , quindi selezionare **nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *ROPC_Auth_app*.
1. Lasciare gli altri valori così come sono, quindi selezionare **Register (registra**).
1. Registrare l' **ID dell'applicazione (client)** per l'uso in un passaggio successivo.
1. In **Gestisci**selezionare **autenticazione**.
1. Selezionare **prova la nuova esperienza** (se visualizzata).
1. In **tipo di client predefinito**selezionare **Sì** per gestire l'applicazione come client pubblico. Questa impostazione è obbligatoria per il flusso ROPC.
1. Selezionare **Salva**.