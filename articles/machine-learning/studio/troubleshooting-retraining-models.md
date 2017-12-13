---
title: Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning | Documentazione Microsoft
description: Identificare e correggere i problemi comuni rilevati durante la ripetizione del training del modello per un servizio Web di Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.author: garye
ms.openlocfilehash: 1e5327ad135d9bc8881354679dc3f1b8a472cad3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning
## <a name="retraining-overview"></a>Panoramica sulla ripetizione del training
Un esperimento predittivo distribuito come servizio Web di assegnazione dei punteggi è un modello statico. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. 

Per una procedura dettagliata completa del processo di ripetizione del training di un servizio Web classico, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Processo di ripetizione del training
Quando è necessario ripetere il training del servizio Web, si devono aggiungere alcune parti:

* Un servizio Web distribuito dall'esperimento di training. L'esperimento deve avere un modulo **Web service output** (Output servizio Web) collegato all'output del modulo **Train Model** (Modello di training).  
  
    ![Collegare l'output del servizio Web al modello di training.][image1]
* Un nuovo endpoint aggiunto al servizio Web di assegnazione dei punteggi.  È possibile aggiungere l'endpoint a livello di codice usando il codice di esempio a cui si fa riferimento nell'argomento Ripetere il training dei modelli di Machine Learning a livello di codice o tramite il portale dei servizi Web di Azure Machine Learning.

È quindi possibile usare il codice C# di esempio dalla pagina della guida dell'API del servizio Web di training per ripetere il training del modello. Una volta valutati e approvati i risultati, si aggiorna il servizio Web di assegnazione di punteggi del modello con training usando il nuovo endpoint aggiunto.

Quando tutto è pronto, i passaggi principali da eseguire per ripetere il training del modello sono i seguenti:

1. Chiamare il servizio Web di training: la chiamata è per il servizio Esecuzione batch, non per il servizio di richiesta-risposta. È possibile usare il codice C# di esempio nella pagina della guida dell'API per eseguire la chiamata. 
2. Trovare i valori di *BaseLocation*, *RelativeLocation* e *SasBlobToken*: questi valori vengono restituiti nell'output dalla chiamata al servizio Web di training. 
   ![Visualizzazione dell'output dell'esempio di ripetizione del training e dei valori di BaseLocation, RelativeLocation e SasBlobToken.][image6]
3. Aggiornare l'endpoint aggiunto dal servizio Web di assegnazione dei punteggi con il nuovo modello con training: usando il codice di esempio fornito in Ripetere il training dei modelli di Machine Learning a livello di codice, aggiornare il nuovo endpoint aggiunto al modello di assegnazione dei punteggi con il nuovo modello con training dal servizio Web di training.

## <a name="common-obstacles"></a>Ostacoli comuni
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verificare di avere l'URL PATCH corretto
L'URL PATCH in uso deve essere quello associato al nuovo endpoint di assegnazione dei punteggi aggiunto al servizio Web di assegnazione dei punteggi. È possibile ottenere l'URL PATCH in due modi:

**Opzione 1: a livello di codice**

Per ottenere l'URL PATCH corretto:

1. Eseguire il codice di esempio [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2. Dall'output di AddEndpoint, trovare il valore *HelpLocation* e copiare l'URL.
   
   ![HelpLocation nell'output dell'esempio addEndpoint.][image2]
3. Incollare l'URL in un browser per passare a una pagina contenente i collegamenti della guida per il servizio Web.
4. Fare clic sul collegamento **Aggiorna risorsa** per aprire la pagina della guida per l'applicazione di patch.

**Opzione 2: usare il portale dei servizi Web di Azure Machine Learning**

1. Accedere al [portale dei servizi Web di Azure Machine Learning](https://services.azureml.net/).
2. Fare clic su **Servizi Web** o **Servizi Web classici** nella parte superiore.
4. Fare clic sul servizio Web di assegnazione dei punteggi in uso (se il nome predefinito del servizio Web non è stato modificato, il nome termina in "[Scoring Exp]".).
5. Fare clic su **+Nuovo**.
6. Una volta aggiunto l'endpoint, fare clic sul nome dell'endpoint.
7. Sotto all'URL **patch** fare clic su **API Help** (Guida API) per aprire la pagina della Guida relativa all'applicazione di patch.

> [!NOTE]
> Se l'endpoint è stato aggiunto al servizio Web di training invece che al servizio Web predittivo, quando si fa clic sul collegamento **Aggiorna risorsa** si riceve un messaggio di errore che indica che la funzionalità non è supportata o non è disponibile in questo contesto. Questo servizio Web non dispone di alcuna risorsa aggiornabile. Ci scusiamo per l'inconveniente e stiamo lavorando per migliorare questo flusso di lavoro.
> 
> 

La pagina della guida di PATCH contiene l'URL PATCH da usare e fornisce il codice di esempio che è possibile usare per chiamarlo.

![URL della patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Verificare di aggiornare l'endpoint di assegnazione dei punteggi corretto
* Non applicare la patch al servizio Web di training: l'operazione di applicazione della patch deve essere eseguita sul servizio Web di assegnazione dei punteggi.
* Non applicare la patch all'endpoint predefinito nel servizio Web: l'operazione di applicazione della patch deve essere eseguita sul nuovo endpoint del servizio Web di assegnazione dei punteggi aggiunto.

Per verificare in quale servizio Web si trova l'endpoint, visitare il portale dei servizi Web. 

> [!NOTE]
> Assicurarsi di aggiungere l'endpoint al servizio Web predittivo, non al servizio Web di training. Se sono stati distribuiti correttamente sia un servizio Web di training che uno predittivo, verranno visualizzati due servizi Web elencati separatamente. Il servizio Web predittivo deve terminare con "[predictive exp.]".
> 
> 

1. Accedere al [portale dei servizi Web di Azure Machine Learning](https://services.azureml.net/).
2. Fare clic su **Servizi Web** o **Servizi Web classici**.
3. Selezionare il servizio Web predittivo.
4. Verificare che il nuovo endpoint sia stato aggiunto al servizio Web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Verificare che l'area di lavoro sia nella stessa area del servizio web
1. Accedere a [Machine Learning Studio](https://studio.azureml.net/).
2. Nella parte superiore fare clic nell'elenco di riepilogo a discesa delle aree di lavoro.

   ![Interfaccia utente dell'area di Machine Learning.][image4]

3. Verificare l'area in cui si trova l'area di lavoro.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
