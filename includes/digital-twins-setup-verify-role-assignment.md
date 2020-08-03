---
author: baanders
description: file di inclusione per la verifica dell'assegnazione di ruolo nell'installazione di gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405592"
---
Un modo per verificare che l'assegnazione di ruolo sia stata configurata correttamente consiste nel visualizzare le assegnazioni di ruolo per l'istanza di Azure Digital Twins nell' [portale di Azure](https://portal.azure.com). Passare alla pagina del portale delle [istanze di Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (è possibile usare questo collegamento o cercarla nella barra di ricerca del portale) e selezionare il nome dell'istanza che si vuole controllare. 

Visualizzare quindi tutti i ruoli assegnati in controllo di *accesso (IAM) > assegnazioni di ruolo*. L'utente dovrebbe essere visualizzato nell'elenco con un ruolo di proprietario dei dispositivi *gemelli digitali di Azure (anteprima)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Visualizzazione delle assegnazioni di ruolo per un'istanza di dispositivi gemelli digitali di Azure in portale di Azure":::