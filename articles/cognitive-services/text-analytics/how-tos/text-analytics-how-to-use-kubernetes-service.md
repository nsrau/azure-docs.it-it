---
title: Eseguire il servizio Azure Kubernetes-Analisi del testo
titleSuffix: Azure Cognitive Services
description: Distribuire l'immagine del contenitore Analisi del testo nel servizio Azure Kubernetes e testarla in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 744be742d9f638df14f9d6720c9dad9678685af0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017866"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Distribuire un contenitore Analisi del testo al servizio Azure Kubernetes

Informazioni su come distribuire i servizi cognitivi di Azure [analisi del testo](./text-analytics-how-to-install-containers.md) immagine del contenitore in Azure Kubernetes Service (AKS). In questa procedura viene illustrato come creare una risorsa Analisi del testo, come creare un'immagine di analisi del sentimento associata e come eseguire questa orchestrazione dei due da un browser. L'uso di contenitori può spostare l'attenzione dalla gestione dell'infrastruttura al invece di concentrarsi sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale. Non usare Azure Cloud Shell. Sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services) prima di iniziare.
* Un editor di testo, ad esempio [Visual Studio Code](https://code.visualstudio.com/download).
* INTERFACCIA della riga di comando di [Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) installata.
* INTERFACCIA della riga di comando di [Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installata.
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con questo contenitore:
    * Risorsa **analisi del testo di Azure** con solo piani tariffari F0 o standard.
    * Risorsa **Servizi cognitivi di Azure** con il piano tariffario s0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Estrazione frasi chiave](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analisi del sentiment](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

**_

## <a name="next-steps"></a>Passaggi successivi

_ Usare più [contenitori di servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [servizio analisi del testo connesso](../index.yml)