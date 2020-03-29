---
title: Eseguire il servizio Azure Kubernetes - Analisi del testoRun Azure Kubernetes Service - Text Analytics
titleSuffix: Azure Cognitive Services
description: Distribuire l'immagine del contenitore Analisi di testo nel servizio Azure Kubernetes e testarla in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 3264ec5a83277e6bb4befad46cd1337175e911c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383433"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Distribuire un contenitore Analisi del testo nel servizio Azure KubernetesDeploy a Text Analytics container to Azure Kubernetes Service

Informazioni su come distribuire l'immagine del contenitore [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) di Servizi cognitivi di Azure nel servizio Azure Kubernetes.Learn how to deploy the Azure Cognitive Services Text Analytics container image to Azure Kubernetes Service (AKS). Questa procedura illustra come creare una risorsa Analisi del testo, come creare un'immagine di analisi del sentiment associata e come esercitare questa orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale. Non usare Azure Cloud Shell. Sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un editor di testo, ad esempio [Visual Studio Code](https://code.visualstudio.com/download).
* [L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) di Azure installata.
* La [CLI Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installato.
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con questo contenitore:
    * **Risorsa Analisi del testo** di Azure solo con F0 o piani tariffari standard.
    * **Risorsa Servizi cognitivi** di Azure con il piano tariffario S0.Azure Cognitive Services resource with the S0 pricing tier.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analisi del sentimento](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Passaggi successivi

* Usare più [contenitori di Servizi cognitiviUse more Cognitive Services containers](../../cognitive-services-container-support.md)
* Usare il [servizio di analisi del testo connessoUse the Text Analytics Connected Service](../vs-text-connected-service.md)
