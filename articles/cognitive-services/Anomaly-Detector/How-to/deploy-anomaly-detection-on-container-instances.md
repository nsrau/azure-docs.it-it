---
title: Esegui il contenitore del rilevamento anomalie nelle istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore di rilevatori di anomalie in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716359"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuire un contenitore di rilevatori di anomalie in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [Rilevamento anomalie](../anomaly-detector-container-howto.md) dei servizi cognitivi in [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura illustra la creazione di una risorsa del rilevatore di anomalie. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

È necessario prima completare e inviare il [modulo di richiesta del contenitore del rilevatore di anomalie](https://aka.ms/adcontainer) per richiedere l'accesso al contenitore.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [installare ed eseguire i contenitori](../anomaly-detector-container-configuration.md) per il pull dell'immagine del contenitore ed eseguire il contenitore
* Rivedere [Configurare i contenitori](../anomaly-detector-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* [Altre informazioni sul servizio API del rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
