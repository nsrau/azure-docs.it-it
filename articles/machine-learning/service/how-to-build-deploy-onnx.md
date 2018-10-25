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
ms.openlocfilehash: 190b7fff24c9d6b3dee86471b56ad68c962e51ce
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116879"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX e Azure Machine Learning | Creare e distribuire modelli interoperativi di AI

Il formato [Open Neural Network Exchange](http://onnx.ai) (ONNX) è uno standard aperto per la rappresentazione di modelli di machine learning. ONNX è supportato da una [community di partner](http://onnx.ai/supported-tools), tra cui Microsoft, che crea strumenti e Framework compatibili. Microsoft si impegna per il raggiungimento di un’intelligenza artificiale aperta e interoperabile che consenta ai data scientist e agli sviluppatori di:

+ Usare un framework per creare ed eseguire il training di modelli
+ Distribuire modelli multipiattaforma con integrazioni minime

Microsoft supporta ONNX in tutti i relativi prodotti tra cui Azure e Windows per raggiungere i propri obiettivi.  

## <a name="why-choose-onnx"></a>Perché scegliere ONNX?
L'interoperabilità che si ottiene con ONNX consente di ottenere più velocemente idee eccezionali nell’ambiente di produzione. Con ONNX, i data scientist possono scegliere il framework preferito per il processo. Analogamente, gli sviluppatori possono dedicare meno tempo alla preparazione per la produzione di modelli e distribuirli attraverso il cloud e i dispositivi periferici.  

È possibile creare modelli ONNX da molti framework, tra cui PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, SciKit-Learn e altri ancora.

È inoltre disponibile un ecosistema di strumenti per visualizzare e accelerare i modelli ONNX. Per gli scenari comuni sono inoltre disponibili vari modelli con training preliminare ONNX.

[I modelli ONNX possono essere distribuiti](#deploy) nel cloud usando Azure Machine Learning e il Runtime ONNX. Inoltre, possono essere distribuiti nei dispositivi Windows 10 usando [Windows ML](https://docs.microsoft.com/windows/ai/). È anche possibile distribuirli in altre piattaforme usando convertitori recuperabili dalla community ONNX. 

[ ![diagramma di flusso ONNX che mostra la formazione, i convertitori e la distribuzione](media/concept-onnx/onnx.png) ] (./media/concept-onnx/onnx.png#lightbox)

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
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx converter](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost e libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

È possibile reperire l'elenco più recente del Framework supportati e i convertitori nel [sito dedicato alle esercitazioni di ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Distribuire i modelli ONNX in Azure

Con il servizio di Azure Machine Learning, è possibile distribuire, gestire e monitorare i propri modelli ONNX. Usando lo standard [flusso di lavoro di distribuzione](concept-model-management-and-deployment.md) e Runtime ONNX, è possibile creare un endpoint REST ospitato nel cloud. Vedere esempio completo di un blocco appunti di Jupyter alla fine di questo articolo per provarlo personalmente. 

### <a name="install-and-configure-the-onnx-runtime"></a>Installare e configurare Runtime ONNX

Runtime ONNX è un motore a inferenza ad alte prestazioni per i modelli ONNX. Viene fornito con un'API di Python e fornisce l'accelerazione dell'hardware in CPU e GPU. Attualmente supporta i modelli ONNX 1.2 e viene eseguito in Linux Ubuntu 16.04. Entrambi i pacchetti per [CPU](https://pypi.org/project/onnxruntime) e [GPU](https://pypi.org/project/onnxruntime-gpu) sono disponibili in [PyPi.org](https://pypi.org).

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

Per informazioni di riferimento complete sull'API, vedere i [documenti di riferimento per Runtime ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Esempio di passaggi di distribuzione

Ecco un esempio per la distribuzione di un modello ONNX:

1. Inizializzare l'area di lavoro del servizio Azure Machine Learning. Per informazioni su come creare uno spazio di lavoro vedere [questa guida introduttiva](quickstart-get-started.md).

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

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Distribuire il modello ONNX con Azure Machine Learning a:
   + Istanze di contenitore di Azure (ACI): [Informazioni](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS): [Informazioni](how-to-deploy-to-aks.md)


## <a name="examples"></a>Esempi
 
I notebook seguenti illustrano come creare e distribuire modelli ONNX con Azure Machine Learning: 
+ `/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb` 
+ `/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb`
+ `/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb`

I notebook seguenti illustrano come distribuire modelli ONNX esistenti con Azure Machine Learning: 
+ [onnx/onnx-inference-mnist.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-mnist.ipynb) 
+ [onnx/onnx-inference-emotion-recognition.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-emotion-recognition.ipynb)
 
Ottenere questi notebook:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Altre informazioni

Per altre informazioni su ONNX o per contribuire al progetto:
+ [Sito Web del progetto ONNX](http://onnx.ai)

+ [Codice ONNX su GitHub](https://github.com/onnx/onnx)
