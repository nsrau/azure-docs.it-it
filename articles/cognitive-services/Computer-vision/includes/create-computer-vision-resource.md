---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499086"
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
