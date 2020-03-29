---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523144"
---
## <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

1. Accedere al portale di [AzureSign](https://portal.azure.com) into the Azure portal
1. Fare clic su Crea risorsa [ **rilevatore di anomalie** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Immettere tutte le impostazioni richieste:

    |Impostazione|valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|
    |Casella di controllo di conferma anteprima (obbligatorio)|Se hai letto o meno l'avviso **Anteprima**|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Dopo la creazione, passare alla pagina delle risorse
1. Raccogliere `endpoint` configurato e una chiave API:Collect configured and an API key:

    |Scheda Risorsa nel portale|Impostazione|valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Sembra simile a`https://westus2.api.cognitive.microsoft.com/`|
    |**Chiavi**|API key|Copia 1 delle due chiavi. Si tratta di una stringa di 32 caratteri alfanumerici senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



