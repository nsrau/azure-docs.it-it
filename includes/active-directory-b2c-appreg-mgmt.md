---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: baf39c1fe72703d8ebc18b03bae1c963536d7ced
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475191"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza di **applicazioni** corrente o la nuova esperienza di **registrazioni app (anteprima)** unificata. [Scopri di più sull'esperienza di anteprima](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure Active Directory** (*non* Azure ad B2C). In alternativa, selezionare **tutti i servizi** e quindi cercare e selezionare **Azure Active Directory**.
1. In **Gestisci**selezionare **registrazioni app (legacy)** .
1. Selezionare **Registrazione nuova applicazione**.
1. Immettere un nome per l'applicazione. Ad esempio, *managementapp1*.
1. Per **tipo di applicazione**selezionare **app Web/API**.
1. Immettere un URL valido nell' **URL di accesso**. Ad esempio, `https://localhost`. Non è necessario che l'endpoint sia raggiungibile, ma deve essere un URL valido.
1. Selezionare **Crea**.
1. Registrare l' **ID applicazione** visualizzato nella pagina Panoramica dell' **app registrata** . Questo valore verrà usato in un passaggio successivo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **registrazioni app (anteprima)** , quindi selezionare **nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *managementapp1*.
1. Selezionare **account solo in questa directory aziendale**.
1. In **autorizzazioni**deselezionare la casella *di controllo Concedi il consenso dell'amministratore per le autorizzazioni OpenID e offline_access* .
1. Selezionare **Registra**.
1. Registrare l' **ID dell'applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questo valore verrà usato in un passaggio successivo.