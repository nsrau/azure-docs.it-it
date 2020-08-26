---
author: baanders
description: file di inclusione per la verifica dell'assegnazione di ruolo nell'installazione di gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864904"
---
Un modo per verificare che l'assegnazione di ruolo sia stata configurata correttamente consiste nel visualizzare le assegnazioni di ruolo per l'istanza di Azure Digital Twins nell' [portale di Azure](https://portal.azure.com). Passare all'istanza di Azure Digital Twins nel portale di Azure (è possibile cercarla nella pagina delle [istanze di Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) o cercarne il nome nella barra di ricerca del portale).

Visualizzare quindi tutti i ruoli assegnati in controllo di *accesso (IAM) > assegnazioni di ruolo*. L'utente dovrebbe essere visualizzato nell'elenco con un ruolo di proprietario dei dispositivi *gemelli digitali di Azure (anteprima)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Visualizzazione delle assegnazioni di ruolo per un'istanza di dispositivi gemelli digitali di Azure in portale di Azure":::