---
title: ONNX e Azure Machine Learning | Creare e distribuire modelli
description: Informazioni su ONNX e sull'uso di Azure Machine Learning per la creazione e la distribuzione i modelli ONNX
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: f453fff59abc1441b2fb16049f130d2c19460083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970807"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX e Azure Machine Learning | Creare e distribuire modelli interoperativi di AI

Il formato [Open Neural Network Exchange](http://onnx.ai) (ONNX) è uno standard aperto per la rappresentazione di modelli di machine learning. ONNX è supportato da una [community di partner](http://onnx.ai/supported-tools), tra cui Microsoft, che crea strumenti e Framework compatibili. Microsoft si impegna per il raggiungimento di un’intelligenza artificiale aperta e interoperabile che consenta ai data scientist e agli sviluppatori di:

+ Usare un framework per creare ed eseguire il training di modelli
+ Distribuire modelli multipiattaforma con integrazioni minime

Microsoft supporta ONNX in tutti i relativi prodotti tra cui Azure e Windows per raggiungere i propri obiettivi.  

## <a name="why-choose-onnx"></a>Perché scegliere ONNX?
L'interoperabilità che si ottiene con ONNX consente di ottenere più velocemente idee eccezionali nell’ambiente di produzione. Con ONNX, i data scientist possono scegliere il framework preferito per il processo. Analogamente, gli sviluppatori possono dedicare meno tempo alla preparazione per la produzione di modelli e distribuirli attraverso il cloud e i dispositivi periferici.  

È possibile esportare i modelli ONNX da molti framework, tra cui PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet e ML.Net. I convertitori sono disponibili per altri framework, ad esempio TensorFlow, Keras, SciKit-Learn e altri ancora.

È inoltre disponibile un ecosistema di strumenti per visualizzare e accelerare i modelli ONNX. Per gli scenari comuni sono inoltre disponibili vari modelli con training preliminare ONNX.

[I modelli ONNX possono essere distribuiti](#deploy) nel cloud usando Azure Machine Learning e il Runtime ONNX. Inoltre, possono essere distribuiti nei dispositivi Windows 10 usando [Windows ML](https://docs.microsoft.com/windows/ai/). È anche possibile distribuirli in altre piattaforme usando convertitori recuperabili dalla community ONNX. 

[ ![diagramma di flusso ONNX che mostra la formazione, i convertitori e la distribuzione](media/concept-onnx/onnx.png) ] (./media/concept-onnx/onnx.png#lightbox)

## <a name="create-onnx-models-in-azure"></a>Creare i modelli ONNX in Azure

È possibile creare modelli ONNX in diversi modi:
+ Eseguire il training di un modello nel servizio Azure Machine Learning e convertirlo o esportarlo in ONNX (vedere l'esempio nella parte inferiore di questo articolo)

+ Ottenere un modello ONNX di cui è stato precedentemente eseguito il training dallo [zoo dei modelli di ONNX](https://github.com/onnx/models)

+ Generare un modello personalizzato di ONNX dal [Servizio visione artificiale personalizzato di Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="exportconvert-your-models-to-onnx"></a>Esportazione/conversione dei modelli in ONNX

È inoltre possibile convertire i modelli esistenti in ONNX.
+ Per i modelli **PyTorch**, provare [il blocco appunti Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)

+ Per i modelli **Microsoft Cognitive Toolkit (CNTK)**, provare [il blocco appunti Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)

+ Per i modelli **Chainer**, provare [il blocco appunti Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)

+ Per i modelli **MXNet**, provare [il blocco appunti Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)

+ Per i modelli **TensorFlow**, usare il [convertitore tensorflow onnx](https://github.com/onnx/tensorflow-onnx).

+ Per i modelli **Keras**, **ScitKit-Learn**, **CoreML**, **XGBoost** e **libSVM**, convertire in ONNX usando il pacchetto [WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools).

È possibile reperire l'elenco più recente del Framework supportati e i convertitori nel [sito dedicato alle esercitazioni di ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Distribuire i modelli ONNX in Azure

Con il servizio di Azure Machine Learning, è possibile distribuire, gestire e monitorare i propri modelli ONNX. Usando lo standard [flusso di lavoro di distribuzione](concept-model-management-and-deployment.md) e Runtime ONNX, è possibile creare un endpoint REST ospitato nel cloud. Vedere esempio completo di un blocco appunti di Jupyter alla fine di questo articolo per provarlo personalmente. 

### <a name="install-and-configure-the-onnx-runtime"></a>Installare e configurare Runtime ONNX

Runtime ONNX è un motore a inferenza ad alte prestazioni per i modelli ONNX. Viene fornito con un'API di Python e fornisce l'accelerazione dell'hardware in CPU e GPU. Attualmente supporta i modelli ONNX 1.2 e viene eseguito in Linux Ubuntu 16.04.

Per installare Runtime ONNX, usare:
```python
pip install onnxruntime
```

Per chiamare Runtime ONNX nello script di Python, usare:
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

Per un riferimento completo sull’API, vedere la [documentazione](https://docs.microsoft.com/en-us/python/api/overview/azure/main?view=azure-onnx-py).

### <a name="example-deployment-steps"></a>Esempio di passaggi di distribuzione

Ecco un esempio per la distribuzione di un modello ONNX:

1. Inizializzare la propria area di lavoro di Azure Machine Learning. Per informazioni su come creare uno spazio di lavoro vedere [questa guida introduttiva](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Registrazione di un modello con Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Creare un'immagine con il modello e le eventuali dipendenze.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Il file `score.py` contiene la logica di punteggio e deve essere incluso nell'immagine. Questo file viene usato per eseguire il modello nell'immagine. Vedere questa [esercitazione](tutorial-deploy-models-with-aml.md#create-scoring-script) per le istruzioni su come creare uno script dei punteggi. Di seguito viene mostrato un file di esempio per un modello ONNX:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Il file `myenv.yml` descrive le dipendenze richieste per l'immagine. Vedere questa [esercitazione](tutorial-deploy-models-with-aml.md#create-environment-file) per le istruzioni su come creare un file di ambiente, come questo file di esempio:

   ```
   name: myenv
   channels:
     - defaults
   dependencies:
     - pip:
       - onnxruntime
       - azureml-core
   ```

4. Distribuire il modello ONNX con Azure Machine Learning a:
   + Istanze di contenitore di Azure (ACI): [Informazioni](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS): [Informazioni](how-to-deploy-to-aks.md)


## <a name="examples"></a>Esempi
 
I blocco appunti seguenti illustrano come distribuire i modelli ONNX con Azure Machine Learning: 
+ `/onnx/onnx-inference-mnist.ipynb`
 
+ `/onnx/onnx-inference-emotion-recognition.ipynb`
 
Per ottenere questo blocco appunti:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Altre informazioni

Per altre informazioni su ONNX o per contribuire al progetto:
+ [Sito Web del progetto ONNX](http://onnx.ai)

+ [Codice ONNX su GitHub](https://github.com/onnx/onnx)
