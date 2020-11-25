---
title: Eseguire istanze di contenitore di Azure-Analisi del testo
titleSuffix: Azure Cognitive Services
description: Distribuire i contenitori di analisi del testo nell'istanza di contenitore di Azure e testarli in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: be43d04672dcefe368eb4052b4d1a929e25327ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009921"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Distribuire un contenitore Analisi del testo in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [analisi del testo][install-and-run-containers] di servizi cognitivi in [istanze di contenitore][container-instances]di Azure. Questa procedura esemplifica la creazione di una risorsa Analisi del testo, la creazione di un'immagine Analisi del sentiment associata e la possibilità di eseguire questa orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services).

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analisi del sentiment](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Analisi del testo per la sanità](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Passaggi successivi 

* Usare altri [contenitori di Servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [servizio analisi del testo connesso](../index.yml)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances