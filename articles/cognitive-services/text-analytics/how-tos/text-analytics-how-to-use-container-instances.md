---
title: Eseguire le istanze del contenitore di Azure - Analisi del testoRun Azure Container Instances - Text Analytics
titleSuffix: Azure Cognitive Services
description: Distribuire i contenitori di analisi di testo nell'istanza del contenitore di Azure e testarla in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383510"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Distribuire un contenitore Analisi del testo nelle istanze del contenitore di AzureDeploy a Text Analytics container to Azure Container Instances

Informazioni su come distribuire il contenitore [Analisi del testo][install-and-run-containers] di Servizi cognitivi in [Istanze del contenitore][container-instances]di Azure. Questa procedura esemplifica la creazione di una risorsa Analisi del testo, la creazione di un'immagine di analisi del sentiment associata e la possibilità di esercitare questa orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione degli sviluppatori dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analisi del sentimento](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Passaggi successivi 

* Usare più [contenitori di Servizi cognitiviUse more Cognitive Services Containers](../../cognitive-services-container-support.md)
* Usare il [servizio di analisi del testo connessoUse the Text Analytics Connected Service](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances