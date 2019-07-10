---
title: Eseguire istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire i contenitori di analitica di testo con l'immagine di analisi del sentiment, l'istanza di contenitore di Azure ed eseguirne il test in un web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9f174d54fcc74eed613eb69412bc0e515f15897b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711725"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Distribuire un contenitore di analisi del Sentiment in istanze di contenitore di Azure

Informazioni su come distribuire i servizi cognitivi [testo Analitica](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) contenitore con l'immagine di analisi del Sentiment in Azure [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/). Questa procedura esemplifica la creazione di una risorsa di Analitica di testo, la creazione di un'immagine di analisi del Sentiment associata e la possibilità di provare questa orchestrazione delle due da un browser. Uso di contenitori può essere spostato l'attenzione degli sviluppatori dalla gestione dell'infrastruttura per invece concentrarsi sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Passaggi successivi 

* Usare altri [contenitori di Servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [Analitica testo servizio connesso](../vs-text-connected-service.md)
