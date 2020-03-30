---
title: 'ONNX: inferenza trasversale ad alta perf'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Open Neural Network Exchange (ONNX) consente di ottimizzare l'inferenza del modello di apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270173"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: creare e accelerare i modelli di Machine Learning

Informazioni su come usare [Open Neural Network Exchange](https://onnx.ai) (ONNX) consente di ottimizzare l'inferenza del modello di apprendimento automatico. L'inferenza, o punteggio del modello, è la fase in cui il modello distribuito viene usato per la stima, in genere nei dati di produzione. 

L'ottimizzazione dei modelli di apprendimento automatico per l'inferenza (o il punteggio del modello) è difficile poiché è necessario ottimizzare il modello e la libreria di inferenza per sfruttare al meglio le funzionalità hardware. Il problema diventa estremamente difficile se si desidera ottenere prestazioni ottimali su diversi tipi di piattaforme (cloud/edge, CPU/GPU, ecc.), dal momento che ognuno ha diverse capacità e caratteristiche. La complessità aumenta se si dispone di modelli da una varietà di framework che devono essere eseguiti su una varietà di piattaforme. È molto dispendioso in termini di tempo per ottimizzare tutte le diverse combinazioni di framework e hardware. È necessaria una soluzione per allenarsi una volta nel framework preferito e funzionare in qualsiasi punto del cloud o del perimetro. È qui che entra in gioco ONNX.

Microsoft e una comunità di partner hanno creato ONNX come standard aperto per la rappresentazione dei modelli di apprendimento automatico. I modelli di [molti framework,](https://onnx.ai/supported-tools) tra cui TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet e MATLAB, possono essere esportati o convertiti nel formato STANDARD ONNX. Una volta che i modelli sono in formato ONNX, possono essere eseguiti su una varietà di piattaforme e dispositivi.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) è un motore di inferenza ad alte prestazioni per la distribuzione di modelli ONNX nell'ambiente di produzione. È ottimizzato sia per il cloud che per edge e funziona su Linux, Windows e Mac. Scritto in C, ha anche le API C, Python e C. ONNX Runtime fornisce supporto per tutte le specifiche ONNX-ML e si integra anche con gli acceleratori su hardware diversi, ad esempio TensorRT su GPU NVidia.

Il runtime ONNX viene usato in servizi Microsoft su larga scala come Bing, Office e Servizi cognitivi. Gli aumenti delle prestazioni dipendono da una serie di fattori, ma questi servizi Microsoft hanno visto un __aumento medio delle prestazioni di 2 volte sulla CPU__. ONNX Runtime viene utilizzato anche come parte di Windows ML su centinaia di milioni di dispositivi. È possibile usare il runtime con Azure Machine Learning.You can use the runtime with Azure Machine Learning. Utilizzando ONNX Runtime, è possibile trarre vantaggio dalle ottimizzazioni di livello di produzione estesa, test e miglioramenti continui.

[![Diagramma di flusso ONNX che mostra formazione, convertitori e distribuzione](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

È possibile ottenere modelli ONNX in diversi modi:
+ Eseguire il training di un nuovo modello ONNX in Azure Machine Learning (vedere esempi nella parte inferiore di questo articolo)Train a new ONNX model in Azure Machine Learning (see examples at the bottom of this article)
+ Convertire il modello esistente da un altro formato a ONNX (vedere le [esercitazioni)](https://github.com/onnx/tutorials) 
+ Ottenere un modello ONNX pre-addestrato dallo zoo del [modello ONNX](https://github.com/onnx/models) (vedere esempi nella parte inferiore di questo articolo)
+ Generare un modello personalizzato di ONNX dal [Servizio visione artificiale personalizzato di Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Molti modelli, tra cui la classificazione delle immagini, il rilevamento degli oggetti e l'elaborazione del testo, possono essere rappresentati come modelli ONNX. Tuttavia alcuni modelli potrebbero non essere in grado di essere convertiti correttamente. Se si verifica questa situazione, si prega di presentare un problema nel GitHub del rispettivo convertitore che è stato utilizzato. È possibile continuare a utilizzare il modello di formato esistente fino a quando il problema non viene risolto.

## <a name="deploy-onnx-models-in-azure"></a>Distribuire i modelli ONNX in Azure

Con Azure Machine Learning è possibile distribuire, gestire e monitorare i modelli ONNX. Usando il [flusso di lavoro di distribuzione](concept-model-management-and-deployment.md) standard e ONNX Runtime, è possibile creare un endpoint REST ospitato nel cloud. Vedere quaderni di esempio Jupyter alla fine di questo articolo per provarlo da soli. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installare e utilizzare ONNX Runtime con Python

I pacchetti Python per ONNX Runtime sono disponibili su [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Leggere [i requisiti di sistema](https://github.com/Microsoft/onnxruntime#system-requirements) prima dell'installazione. 

 Per installare ONNX Runtime per Python, utilizzare uno dei seguenti comandi: 
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
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Per informazioni di riferimento complete sull'API di Python, vedere la [documentazione di riferimento su ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Esempi

Per alcuni notebook di esempio che creano e distribuiscono modelli ONNX, vedere [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Altre informazioni

Per altre informazioni su ONNX o per contribuire al progetto:
+ [Sito Web del progetto ONNX](https://onnx.ai)
+ [Codice ONNX su GitHub](https://github.com/onnx/onnx)

Per altre informazioni su ONNX Runtime o per contribuire al progetto:
+ [Repository GitHub di ONNX Runtime](https://github.com/Microsoft/onnxruntime)


