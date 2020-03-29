---
title: Eseguire il contenitore Anomaly Detector nelle istanze del contenitore di AzureRun Anomaly Detector Container in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Rilevatore di anomalie in un'istanza del contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716359"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Distribuire un contenitore Anomaly Detector nelle istanze del contenitore di AzureDeploy an Anomaly Detector container to Azure Container Instances

Informazioni su come distribuire il contenitore [Rilevatore anomalie](../anomaly-detector-container-howto.md) servizi cognitivi in [Istanze del contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. In questa procedura viene illustrata la creazione di una risorsa Rilevatore anomalie. Quindi viene illustrato il pull dell'immagine contenitore associata. Infine, evidenziamo la possibilità di esercitare l'orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione degli sviluppatori dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

È innanzitutto necessario completare e inviare il modulo di [richiesta del rilevatore](https://aka.ms/adcontainer) di anomalie per richiedere l'accesso al contenitore.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Installare ed eseguire contenitori](../anomaly-detector-container-configuration.md) per estrarre l'immagine del contenitore ed eseguire il contenitoreReview Install and run containers for pulling the container image and run the container
* Esaminare [Configurare i contenitori](../anomaly-detector-container-configuration.md) per le impostazioni di configurazioneReview Configure containers for configuration settings
* [Ulteriori informazioni sul servizio API Anomaly Detector](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
