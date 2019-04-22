---
title: Creare e distribuire modelli ONNX interoperativi
titleSuffix: Azure Machine Learning service
description: Informazioni su ONNX e sull'uso di Azure Machine Learning per la creazione e la distribuzione i modelli ONNX
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: 33a93aa01499beb978f616f633588ba75e4b62a3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259185"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX e Azure Machine Learning: Creare e distribuire modelli di intelligenza artificiale interoperativi

Il formato [Open Neural Network Exchange](https://onnx.ai) (ONNX) è uno standard aperto per la rappresentazione di modelli di machine learning. ONNX è supportato da una [community di partner](https://onnx.ai/supported-tools), tra cui Microsoft, che crea strumenti e Framework compatibili. Microsoft si impegna per il raggiungimento di un’intelligenza artificiale aperta e interoperabile che consenta ai data scientist e agli sviluppatori di:

+ Usare un framework per creare ed eseguire il training di modelli
+ Distribuire modelli multipiattaforma con integrazioni minime

Microsoft supporta ONNX in tutti i relativi prodotti tra cui Azure e Windows per raggiungere i propri obiettivi.  

## <a name="why-choose-onnx"></a>Perché scegliere ONNX?

L'interoperabilità che si ottiene con ONNX consente di ottenere più velocemente idee eccezionali nell’ambiente di produzione. Con ONNX, i data scientist possono scegliere il framework preferito per il processo. Analogamente, gli sviluppatori possono dedicare meno tempo alla preparazione per la produzione di modelli e distribuirli attraverso il cloud e i dispositivi periferici.  

È possibile creare i modelli ONNX da molti Framework, tra cui PyTorch, Chainer, MXNet, a ML.Net, TensorFlow, Keras, SciKit-Learn, Microsoft Cognitive Toolkit e altro ancora.

È inoltre disponibile un ecosistema di strumenti per visualizzare e accelerare i modelli ONNX. Per gli scenari comuni sono inoltre disponibili vari modelli con training preliminare ONNX.

[I modelli ONNX possono essere distribuiti](#deploy) nel cloud usando Azure Machine Learning e ONNX Runtime. Inoltre, possono essere distribuiti nei dispositivi Windows 10 usando [Windows ML](https://docs.microsoft.com/windows/ai/). È anche possibile distribuirli in altre piattaforme usando convertitori recuperabili dalla community ONNX. 

[![Diagramma di flusso ONNX con training, convertitori di tipi e la distribuzione](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

È possibile ottenere modelli ONNX in diversi modi:
+ Ottenere un modello ONNX con training preliminare da [ONNX Model Zoo](https://github.com/onnx/models) (vedere l'esempio nella parte finale di questo articolo)
+ Generare un modello personalizzato di ONNX dal [Servizio visione artificiale personalizzato di Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Convertire un modello esistente dal formato originario a ONNX (vedere l'esempio nella parte finale di questo articolo) 
+ Eseguire il training di un nuovo modello ONNX nel servizio Azure Machine Learning (vedere l'esempio nella parte finale di questo articolo)

## <a name="saveconvert-your-models-to-onnx"></a>Salvare o convertire i propri modelli in ONNX

È possibile convertire i modelli esistenti o salvare nuovi modelli in ONNX alla fine del training.

|Framework per il modello|Strumento o esempio di conversione|
|-----|-------|
|PyTorch|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx converter](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost e libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|

È possibile reperire l'elenco più recente del Framework supportati e i convertitori nel [sito dedicato alle esercitazioni di ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Distribuire i modelli ONNX in Azure

Con il servizio di Azure Machine Learning, è possibile distribuire, gestire e monitorare i propri modelli ONNX. Usando il [flusso di lavoro di distribuzione](concept-model-management-and-deployment.md) standard e ONNX Runtime, è possibile creare un endpoint REST ospitato nel cloud. Vedere esempio completo di un blocco appunti di Jupyter alla fine di questo articolo per provarlo personalmente. 

### <a name="install-and-configure-onnx-runtime"></a>Installare e configurare ONNX Runtime

ONNX Runtime è un motore di inferenza open source a prestazioni elevate per i modelli ONNX. Fornisce l'accelerazione dell'hardware in CPU e GPU, con API disponibili per Python, C# e C. ONNX Runtime supporta i modelli ONNX 1.2+ e viene eseguito in Linux, Windows e Mac. I pacchetti Python sono disponibili in [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)), mentre il [pacchetto C#](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/) è disponibile in [Nuget.org](https://www.nuget.org). Per altre informazioni sul progetto, vedere [GitHub](https://github.com/Microsoft/onnxruntime). Leggi [requisiti di sistema](https://github.com/Microsoft/onnxruntime#system-requirements) prima dell'installazione.

Per installare ONNX Runtime per Python, usare:
```python
pip install onnxruntime
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

### <a name="example-deployment-steps"></a>Esempio di passaggi di distribuzione

Ecco un esempio per la distribuzione di un modello ONNX:

1. Inizializzare l'area di lavoro del servizio Azure Machine Learning. Se non è ancora, vedere l'articolo sulla [creare un'area di lavoro](setup-create-workspace.md).

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

   > [!TIP]
   > Nell'esempio precedente Usa l'immagine predefinita fornita dal servizio Azure Machine Learning. È anche possibile usare un'immagine personalizzata. Per altre informazioni, vedere la sezione relativa alla configurazione e alla registrazione di un'immagine nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#configureimage).

   Il file `score.py` contiene la logica di punteggio e deve essere incluso nell'immagine. Questo file viene usato per eseguire il modello nell'immagine. Vedere questa [esercitazione](tutorial-deploy-models-with-aml.md#create-scoring-script) per le istruzioni su come creare uno script dei punteggi. Di seguito viene mostrato un file di esempio per un modello ONNX:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global session
       model = Model.get_model_path(model_name = 'MyONNXModel')
       session = onnxruntime.InferenceSession(model)

   def preprocess(input_data_json):
       # convert the JSON data into the tensor input
       return np.array(json.loads(input_data_json)['data']).astype('float32')

   def postprocess(result):
       return np.array(result).tolist()

   def run(input_data_json):
       try:
           start = time.time()   # start timer
           input_data = preprocess(input_data_json)
           input_name = session.get_inputs()[0].name  # get the id of the first input of the model   
           result = session.run([], {input_name: input_data})
           end = time.time()     # stop timer
           return {"result": postprocess(result),
                   "time": end - start}
       except Exception as e:
           result = str(e)
           return {"error": result}
   ```

   Il file `myenv.yml` descrive le dipendenze richieste per l'immagine. Vedere questa [esercitazione](tutorial-deploy-models-with-aml.md#create-environment-file) per le istruzioni su come creare un file di ambiente, come questo file di esempio:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies.create(pip_packages=["numpy","onnxruntime","azureml-core"])

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Per distribuire il modello, vedere [Come e dove eseguire la distribuzione](how-to-deploy-and-where.md).


## <a name="examples"></a>Esempi

Per alcuni notebook di esempio che creano e distribuiscono modelli ONNX, vedere [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Altre informazioni

Per altre informazioni su ONNX o per contribuire al progetto:
+ [Sito Web del progetto ONNX](https://onnx.ai)

+ [Codice ONNX su GitHub](https://github.com/onnx/onnx)

Per altre informazioni su ONNX Runtime o per contribuire al progetto:
+ [Repository GitHub di ONNX Runtime](https://github.com/Microsoft/onnxruntime)


