---
title: Eseguire istanze di contenitore di Azure-Analisi del testo
titleSuffix: Azure Cognitive Services
description: Distribuire i contenitori di analisi del testo nell'istanza di contenitore di Azure e testarli in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383510"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Distribuire un contenitore Analisi del testo in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [analisi del testo][install-and-run-containers] di servizi cognitivi in [istanze di contenitore][container-instances]di Azure. Questa procedura esemplifica la creazione di una risorsa Analisi del testo, la creazione di un'immagine Analisi del sentiment associata e la possibilità di eseguire questa orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>prerequisiti

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Analisi del sentiment](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Passaggi successivi 

* Usare altri [contenitori di Servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [servizio analisi del testo connesso](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances