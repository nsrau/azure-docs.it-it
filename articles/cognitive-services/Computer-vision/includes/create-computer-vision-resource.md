---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876087"
---
## <a name="create-an-computer-vision-resource"></a>Creare una risorsa Visione artificiale

1. Accedi al [portale di Azure](https://portal.azure.com).
1. Fare clic su [crea **visione artificiale** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) risorsa.
1. Immettere tutte le impostazioni richieste:

    |Impostazione|valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Una volta creata, passare alla relativa pagina.
1. Per informazioni `{ENDPOINT_URI}` dettagliate `{API_KEY}`, vedere [raccolta di parametri](../computer-vision-how-to-install-containers.md#gathering-required-parameters) configurati e.
