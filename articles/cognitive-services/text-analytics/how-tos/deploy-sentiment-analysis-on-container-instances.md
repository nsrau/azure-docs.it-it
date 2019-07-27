---
title: Eseguire istanze di contenitore di Azure-Analisi del testo
titleSuffix: Azure Cognitive Services
description: Distribuire i contenitori di analisi del testo con l'immagine di analisi del sentimento, l'istanza di contenitore di Azure e testarla in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552536"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Distribuire un contenitore Analisi del sentiment in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) di servizi cognitivi con l'immagine analisi del sentiment nelle [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura esemplifica la creazione di una risorsa Analisi del testo, la creazione di un'immagine Analisi del sentiment associata e la possibilità di eseguire questa orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

## <a name="prerequisites"></a>Prerequisiti

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Passaggi successivi 

* Usare altri [contenitori di Servizi cognitivi](../../cognitive-services-container-support.md)
* Usare il [servizio analisi del testo connesso](../vs-text-connected-service.md)
