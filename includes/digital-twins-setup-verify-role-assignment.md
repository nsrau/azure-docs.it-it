---
author: baanders
description: file di inclusione per la verifica dell'assegnazione di ruolo nell'installazione di gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098107"
---
Un modo per verificare che l'assegnazione di ruolo sia stata configurata correttamente consiste nel visualizzare le assegnazioni di ruolo per l'istanza di Azure Digital Twins nell'portale di Azure. Dalla pagina del portale delle [istanze di Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)selezionare il nome dell'istanza che si vuole controllare. Visualizzare quindi tutti i ruoli assegnati in controllo di *accesso (IAM) > assegnazioni di ruolo*. L'utente dovrebbe essere visualizzato nell'elenco con un ruolo di proprietario dei dispositivi *gemelli digitali di Azure (anteprima)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="Visualizzazione delle assegnazioni di ruolo per un'istanza di dispositivi gemelli digitali di Azure in portale di Azure":::