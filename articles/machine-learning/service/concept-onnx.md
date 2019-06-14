---
title: Prestazioni elevate, cross-platform inferenza con ONNX
titleSuffix: Azure Machine Learning service
description: Scopri ONNX e Runtime per accelerare i modelli ONNX
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: f1eca5bdd81a384efe04f769ebd12be9d91fc78a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849736"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: Creare e accelerare i modelli di Machine Learning

Informazioni su come l'uso di [scambio di rete neurale Open](https://onnx.ai) (ONNX) può aiutare a ottimizzare l'inferenza del modello di machine learning. Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione. 

Poiché è necessario ottimizzare il modello e la libreria di inferenza per sfruttare al meglio le funzionalità hardware, è difficili ottimizzare modelli di machine learning per inferenza (o modello di punteggio). Il problema diventa estremamente difficile se si desidera ottenere prestazioni ottimali su diversi tipi di piattaforme (cloud/Microsoft edge, CPU/GPU e così via), poiché ogni uno ha le caratteristiche e funzionalità diverse. La complessità aumenta se si dispone di modelli da un'ampia gamma di Framework che dovranno essere eseguiti su una vasta gamma di piattaforme. È molto tempo per ottimizzare le diverse combinazioni di Framework e hardware. È necessaria una soluzione per il training di una volta il Framework preferito e possono essere eseguiti ovunque nel cloud o edge. Si tratta di ONNX entra in gioco.

Microsoft e una community di partner creati ONNX con uno standard aperto per la rappresentazione di modelli di machine learning. Dai modelli [molti framework](https://onnx.ai/supported-tools) tra cui TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet e MATLAB possono essere esportati o convertiti nel formato ONNX standard. Una volta che i modelli sono nel formato ONNX, possono essere eseguiti su un'ampia gamma di piattaforme e dispositivi.

[Runtime ONNX](https://github.com/Microsoft/onnxruntime) è un motore a inferenza ad alte prestazioni per la distribuzione di modelli ONNX nell'ambiente di produzione. È ottimizzato per i cloud e rete perimetrale e funziona su Linux, Windows e Mac. Scritto in C++, dispone anche di C, Python, e C# le API. ONNX Runtime fornisce il supporto per tutte le specifiche ONNX-ML e si integra anche con gli acceleratori su hardware differente, ad esempio TensorRT su GPU NVidia.

Il Runtime ONNX viene usato nei servizi di Microsoft su larga scala, ad esempio Bing, Office e servizi cognitivi. Miglioramenti delle prestazioni dipendono da numerosi fattori, ma questi servizi Microsoft hanno visto un' __calcolare la Media 2 volte le prestazioni sulla CPU__. Runtime ONNX viene usato anche come parte di Machine Learning Windows su centinaia di milioni di dispositivi. È possibile usare il runtime con i servizi di Azure Machine Learning. Usando ONNX Runtime, è possibile trarre vantaggio dalle ottimizzazioni a livello di produzione complete, miglioramenti, in corso e di test.

[![Diagramma di flusso ONNX con training, convertitori di tipi e la distribuzione](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

È possibile ottenere modelli ONNX in diversi modi:
+ Eseguire il training di un nuovo modello ONNX nel servizio Azure Machine Learning (vedere gli esempi nella parte inferiore di questo articolo)
+ Convertire un modello esistente da un altro formato in ONNX (vedere la [esercitazioni](https://github.com/onnx/tutorials)) 
+ Ottenere un modello con training preliminare ONNX dal [Zoo di modello ONNX](https://github.com/onnx/models) (vedere gli esempi nella parte inferiore di questo articolo)
+ Generare un modello personalizzato di ONNX dal [Servizio visione artificiale personalizzato di Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Molti modelli tra cui classificazione delle immagini, rilevamento di oggetti e l'elaborazione di testo possono essere rappresentati come i modelli ONNX. Alcuni modelli tuttavia potrebbero non essere in grado di convertire correttamente. Se si verifica questa situazione, inviare una segnalazione in GitHub del rispettivo convertitore che è stato usato. È possibile continuare a usare il modello di formato esistente fino a quando non viene risolto il problema.

## <a name="deploy-onnx-models-in-azure"></a>Distribuire i modelli ONNX in Azure

Con il servizio di Azure Machine Learning, è possibile distribuire, gestire e monitorare i propri modelli ONNX. Usando il [flusso di lavoro di distribuzione](concept-model-management-and-deployment.md) standard e ONNX Runtime, è possibile creare un endpoint REST ospitato nel cloud. Vedere i notebook di Jupyter di esempio alla fine di questo articolo per provarlo subito per se stessi. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installare e usare Runtime ONNX con Python

Sono disponibili in pacchetti Python per Runtime ONNX [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Leggi [requisiti di sistema](https://github.com/Microsoft/onnxruntime#system-requirements) prima dell'installazione. 

 Per installare il Runtime ONNX per Python, usare uno dei seguenti comandi: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
``` 

Per chiamare ONNX Runtime nello script di Python, usare:    
```python   
import onnxruntime  
session = onnxruntime.InferenceSession("path to model") 
``` 

La documentazione che accompagna il modello in genere indica gli input e gli output per usare il modello. È anche possibile usare uno strumento di visualizzazione, ad esempio [Netron](https://github.com/lutzroeder/Netron) per visualizzare il modello. Runtime ONNX consente inoltre di eseguire query su metadati del modello, input e output:    
```python   
session.get_modelmeta() 
first_input_name = session.get_inputs()[0].name 
first_output_name = session.get_outputs()[0].name   
``` 

Per l'inferenza del modello, usare `run` e inviare l'elenco di input e output che da restituire (lasciare vuoto se si desiderano tutti gli elementi) e una mappa dei valori di input. Il risultato è un elenco degli output.  
```python   
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
results = session.run([], {"input1": indata1, "input2": indata2})   
``` 

Per informazioni di riferimento complete sull'API di Python, vedere la [documentazione di riferimento su ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Esempi

Per alcuni notebook di esempio che creano e distribuiscono modelli ONNX, vedere [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Altre informazioni

Per altre informazioni su ONNX o per contribuire al progetto:
+ [Sito Web del progetto ONNX](https://onnx.ai)
+ [Codice ONNX su GitHub](https://github.com/onnx/onnx)

Per altre informazioni su ONNX Runtime o per contribuire al progetto:
+ [Repository GitHub di ONNX Runtime](https://github.com/Microsoft/onnxruntime)


