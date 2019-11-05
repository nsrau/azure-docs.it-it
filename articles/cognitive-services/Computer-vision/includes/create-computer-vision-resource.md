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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499086"
---
## <a name="create-an-computer-vision-resource"></a>Creare una risorsa Visione artificiale

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Fare clic su [crea **visione artificiale** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) risorsa.
1. Immettere tutte le impostazioni obbligatorie:

    |Impostazione|Valore|
    |--|--|
    |Name|Nome desiderato (2-64 caratteri)|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata|
    |Percorso|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`-piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere la creazione della risorsa. Dopo averla creata, passare alla pagina delle risorse.
1. Raccogliere `{ENDPOINT_URI}` e `{API_KEY}`configurati. per informazioni dettagliate, vedere [raccolta dei parametri obbligatori](../computer-vision-how-to-install-containers.md#gathering-required-parameters) .
