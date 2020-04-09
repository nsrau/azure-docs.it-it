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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876087"
---
## <a name="create-an-computer-vision-resource"></a>Creare una risorsa Visione artificialeCreate an Computer Vision resource

1. Accedere al portale di [Azure](https://portal.azure.com).
1. Fare clic su Crea risorsa [ **Visione artificiale.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Immettere tutte le impostazioni richieste:

    |Impostazione|valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Una volta creata, passare alla relativa pagina.
1. Raccogliere `{ENDPOINT_URI}` i `{API_KEY}`parametri configurati e , vedere [La raccolta dei parametri necessari](../computer-vision-how-to-install-containers.md#gathering-required-parameters) per i dettagli.
