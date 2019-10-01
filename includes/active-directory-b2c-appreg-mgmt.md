---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702175"
---
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure Active Directory** (*non* Azure ad B2C). In alternativa, selezionare **tutti i servizi** e quindi cercare e selezionare **Azure Active Directory**.
1. In **Gestisci**selezionare **registrazioni app (legacy)** .
1. Selezionare **Registrazione nuova applicazione**.
1. Immettere un nome per l'applicazione. Ad esempio, *managementapp1*.
1. Per **tipo di applicazione**selezionare **app Web/API**.
1. Immettere un URL valido nell' **URL di accesso**. Ad esempio `https://localhost`. Non è necessario che l'endpoint sia raggiungibile, ma deve essere un URL valido.
1. Selezionare **Create**.
1. Registrare l' **ID applicazione** visualizzato nella pagina Panoramica dell' **app registrata** . Questo valore verrà usato in un passaggio successivo.
