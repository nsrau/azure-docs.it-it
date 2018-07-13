---
title: Rilevamento anomalie in un'app Python - Servizi cognitivi Microsoft | Microsoft Docs
description: Esplorare un notebook Python che usa l'API Rilevamento anomalie in Servizi cognitivi Microsoft. Inviare i punti dati originali all'API e ottenere i punti dei valori previsti e delle anomalie.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376145"
---
# <a name="anomaly-detection-python-application"></a>Rilevamento anomalie in un'app Python

Questa esercitazione illustra come usare l'API Rilevamento anomalie in Python e come visualizzare i risultati usando librerie comuni. Si userà Jupyter per svolgere l'esercitazione e si useranno per prova i propri dati con la propria chiave di sottoscrizione. Per informazioni su come iniziare a usare i notebook Jupyter interattivi, fare riferimento alla [documentazione di Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>prerequisiti

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Sottoscrivere Rilevamento anomalie e ottenere una chiave di sottoscrizione 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Scaricare il codice di esempio

1. Passare al [notebook dell'esercitazione in GitHub](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Fare clic sul pulsante verde per clonare o scaricare l'esercitazione. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Apertura del notebook dell'esercitazione in Jupyter

1. Aprire un prompt dei comandi e passare alla cartella python-sample.
2. Eseguire il comando Jupyter notebook dal prompt dei comandi per avviare Jupyter.
3. Nella finestra di Jupyter fare clic su <em>Anomaly Detection API Example.ipynb</em> per aprire il notebook dell'esercitazione.   

## <a name="running-the-tutorial"></a>Esecuzione dell'esercitazione

Per usare il notebook, è necessaria una chiave di sottoscrizione per l'API Rilevamento anomalie. Visitare la pagina di sottoscrizione per iscriversi. Nella pagina di accesso usare il proprio account Microsoft per accedere; si attiverà la sottoscrizione e si otterranno le chiavi dell'API. Dopo aver completato il processo di iscrizione, incollare la chiave nella sezione Variables del notebook (riportata sotto). Può essere usata indifferentemente la chiave primaria o quella secondaria. Assicurarsi di racchiudere la chiave tra virgolette perché venga interpretata come una stringa.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
