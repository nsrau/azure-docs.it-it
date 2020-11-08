---
title: Esegui il contenitore del rilevamento anomalie nelle istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore di rilevatori di anomalie in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: 3979bb82bfa055cc2a134bf3119097c452ffb855
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364123"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuire un contenitore di rilevatori di anomalie in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [Rilevamento anomalie](../anomaly-detector-container-howto.md) dei servizi cognitivi in [istanze di contenitore](../../../container-instances/index.yml)di Azure. Questa procedura illustra la creazione di una risorsa del rilevatore di anomalie. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [installare ed eseguire i contenitori](../anomaly-detector-container-configuration.md) per il pull dell'immagine del contenitore ed eseguire il contenitore
* Rivedere [Configurare i contenitori](../anomaly-detector-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* [Altre informazioni sul servizio API del rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)