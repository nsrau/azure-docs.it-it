---
title: Compilare e distribuire un modello di classificazione delle immagini tramite Azure Machine Learning Package per Visione artificiale.
description: Informazioni su come compilare, sottoporre a training, testare e distribuire un modello di classificazione delle immagini con visione artificiale usando Azure Machine Learning Package per Visione artificiale.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6b7f73573cb1465b89e54e30894b3549153e4acb
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888433"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Compilare e distribuire modelli di classificazione delle immagini con Azure Machine Learning

In questo articolo si apprende come usare **Azure Machine Learning Package per Visione artificiale**  (AMLPCV) per sottoporre a training, testare e distribuire un modello di classificazione delle immagini. 

Un numero elevato di problemi nell'ambito della visione artificiale possono essere risolti con la classificazione delle immagini. Questi problemi includono la creazione di modelli che rispondono a domande come:
+ _È presente un oggetto nell'immagine? Ad esempio "cane", "automobile", "nave" e così via_
+ _Quale classe di gravità di malattia oculare si evince dalla scansione della retina di questo paziente?_

Quando si compila e si distribuisce questo modello con Azure Machine Learning Package per Visione artificiale, si segue questa procedura:
1. Creazione del set di dati
2. Visualizzazione e annotazione delle immagini
3. Aumento dell'immagine
4. Definizione di un modello DNN (Deep Neural Network, Rete neurale profonda)
5. Training del classificatore
6. Valutazione e visualizzazione
7. Distribuzione del servizio Web
8. Test di carico del servizio Web

Viene usato [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) come framework di Deep Learning, il training viene eseguito localmente in un computer con tecnologica GPU, ad esempio la [VM per l'analisi scientifica dei dati di Deep Learning](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), e la distribuzione usa l'interfaccia della riga di comando di operazionalizzazione di Azure Machine Learning.

Vedere la [documentazione di riferimento del pacchetto](https://aka.ms/aml-packages/vision) per informazioni dettagliate su ogni modulo e classe.

## <a name="prerequisites"></a>prerequisiti

1. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

1. È necessario che gli account e l'applicazione seguenti siano configurati e installati:
   - Un account Sperimentazione di Azure Machine Learning 
   - Un account di Gestione modelli di Azure Machine Learning
   - Azure Machine Learning Workbench installato

   Se questi tre elementi non sono stati ancora creati o installati, seguire le istruzioni dell'articolo [Guida introduttiva: Installare e iniziare a usare i servizi di Azure Machine Learning](../service/quickstart-installation.md). 

1. È necessario che sia installato Azure Machine Learning Package per Visione artificiale. Vedere [qui le istruzioni su come installare questo pacchetto](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Dati e notebook di esempio

### <a name="get-the-jupyter-notebook"></a>Ottenere il notebook di Jupyter

Scaricare il notebook per eseguire gli esempi descritti qui.

> [!div class="nextstepaction"]
> [Scaricare il notebook di Jupyter](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Caricare i dati di esempio

L'esempio seguente usa un set di dati costituito da 63 immagini di articoli per la tavola. Ogni immagine è etichettata come appartenente a una di quattro classi diverse (bowl, cup, cutlery, plate). Il numero di immagini in questo esempio è limitato per un'esecuzione rapida. Nella pratica dovrebbero essere presenti almeno 100 immagini per ogni classe. Tutte le immagini si trovano in *"../sample_data/imgs_recycling/"*, nelle sottodirectory denominate "bowl", "cup", "cutlery" e "plate".

![Set di dati di Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix
import matplotlib.pyplot as plt

from classification.notebook.ui_utils.ui_annotation import AnnotationUI
from classification.notebook.ui_utils.ui_results_viewer import ResultsUI
from classification.notebook.ui_utils.ui_precision_recall import PrecisionRecallUI

%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)
```



## <a name="create-a-dataset"></a>Creare un set di dati

Dopo aver importato le dipendenze e impostato il contesto di archiviazione, è possibile creare l'oggetto dataset.

Per creare tale oggetto con Azure Machine Learning Package per Visione artificiale, specificare la directory radice delle immagini nel disco locale. Questa directory deve seguire la stessa struttura generale del set di dati di articoli per la tavola, ovvero contenere sottodirectory con le immagini effettive:
- root
    - etichetta 1
    - etichetta 2
    - ...
    - etichetta n
  
Il training di un modello di classificazione delle immagini per un set di dati diverso è semplice quanto la modifica del percorso radice `dataset_location` nel codice seguente per puntare a immagini differenti.


```python
# Root image directory
dataset_location = os.path.abspath("classification/sample_data/imgs_recycling")

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

L'oggetto dataset fornisce funzionalità per scaricare le immagini usando l'[API Ricerca immagini Bing](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Sono supportati due tipi di query di ricerca: 
+ Query di testo normale
+ Query di URL immagine

All'interno di un file di testo con codifica JSON è necessario fornire queste query unitamente all'etichetta della classe. Ad esempio: 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

È anche necessario creare in modo esplicito un oggetto di contesto per contenere la chiave dell'API Ricerca immagini Bing. Ciò richiede una sottoscrizione per l'API Ricerca immagini Bing.

## <a name="visualize-and-annotate-images"></a>Visualizzare e annotare le immagini

È possibile visualizzare le immagini e correggere le etichette nell'oggetto dataset con il widget seguente. 

Se si verifica l'errore "Widget Javascript not detected" (Javascript widget non rilevato), eseguire questo comando per risolverlo:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Set di dati di Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Immagini di realtà aumentata

Il modulo [`augmentation` ](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) offre funzionalità per l'aumento di un oggetto dataset con tutte le trasformazioni descritte nella raccolta [imgaug](https://github.com/aleju/imgaug). Le trasformazioni delle immagini possono essere raggruppate in una singola pipeline, in tal caso tutte le trasformazioni nella pipeline vengono applicate simultaneamente a ogni immagine. 

Se si preferisce applicare diversi passaggi di aumento separati oppure in qualsiasi modo diverso, è possibile definire più pipeline e passarle alla funzione *augment_dataset*. Per altre informazioni ed esempi di aumento di un'immagine, vedere la [documentazione di imgaug](https://github.com/aleju/imgaug).

L'aggiunta di immagini di realtà aumentata al set di training è particolarmente utile per i set di dati di piccole dimensioni. Poiché il processo di training DNN è più lento a causa del maggior numero di immagini di training, è consigliabile iniziare la sperimentazione senza aumento.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Definire i modelli DNN

Con questo pacchetto sono supportati i modelli Deep Neural Network con training preliminare seguenti: 
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

Questi modelli DNN possono essere usati come classificatori oppure come utilità di funzioni. 

Altre informazioni sulle reti sono disponibili [qui](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), mentre un'introduzione di base al Transfer Learning è disponibile [qui](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

I parametri predefiniti di classificazione delle immagini per questo pacchetto sono risoluzione di 224 x 224 pixel e DNN Resnet-18. Questi parametri sono stati selezionati per funzionare in una vasta gamma di attività. L'accuratezza può essere spesso migliorata, ad esempio, aumentando la risoluzione dell'immagine a 500 x 500 pixel e/o selezionando un modello di maggiore profondità (Resnet 50). La modifica dei parametri può tuttavia comportare un aumento significativo dei tempi di training. Vedere l'articolo su [come migliorare l'accuratezza](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Training del classificatore

È possibile scegliere uno dei metodi seguenti per la rete neurale profonda (DNN) con training preliminare.

  - **Perfezionamento della rete neurale profonda**, che insegna alla rete neurale profonda a eseguire la classificazione direttamente. Anche se il training della rete neurale profonda è lento, produce in genere risultati ottimali perché tutti i pesi della rete possono essere migliorati durante il training per fornire la migliore accuratezza.

  - **Definizione delle caratteristiche della rete neurale profonda**, che esegue la rete neurale profonda così com'è per ottenere una rappresentazione dimensionale inferiore di un'immagine (512, 2048 o 4096 float). Tale rappresentazione viene quindi usata come input per il training di un classificatore separato. Dato che la rete neurale profonda rimane invariata, questo approccio è molto più veloce rispetto al perfezionamento, tuttavia l'accuratezza è inferiore. Il training di un classificatore esterno, ad esempio una SVM lineare (come illustrato nel codice seguente), può fornire una base solida e contribuire alla comprensione della fattibilità di un problema.
  
TensorBoard può essere usato per visualizzare lo stato di avanzamento del training. Per attivare TensorBoard:
1. Aggiungere il parametro `tensorboard_logdir=PATH` come illustrato nel codice seguente
1. Avviare il client TensorBoard usando il comando `tensorboard --logdir=PATH` in una nuova console.
1. Aprire un Web browser all'indirizzo richiesto da TensorBoard, per impostazione predefinita localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Valutare e visualizzare le prestazioni del modello

È possibile valutare le prestazioni del modello con training su un set di dati di test indipendente usando il modulo di valutazione. Le metriche di valutazione calcolate includono:
 
+ Accuratezza (per impostazione predefinita con media delle classi)
+ Curva PR
+ Curva ROC
+ Area sotto curva
+ Matrice di confusione


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Set di dati di Azure Machine Learning](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Set di dati di Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operazionalizzazione: distribuzione e utilizzo

L'operazionalizzazione è il processo di pubblicazione di modelli e codice come servizi Web e l'utilizzo di questi servizi per produrre risultati aziendali. 

Dopo aver eseguito il training del modello, è possibile distribuire tale modello per l'utilizzo come servizio Web tramite l'[interfaccia della riga di comando di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). I modelli possono essere distribuiti nel computer locale o in un cluster del servizio contenitore di Azure (ACS). Con il servizio contenitore di Azure è possibile ridimensionare il servizio Web manualmente oppure usare la funzionalità di scalabilità automatica.

**Accedere tramite l'interfaccia della riga di comando di Azure**

Usando un account di [Azure](https://azure.microsoft.com/) con una sottoscrizione valida, accedere con il seguente comando dell'interfaccia della riga di comando:
<br>`az login`

+ Per passare a un'altra sottoscrizione di Azure, usare il comando:
<br>`az account set --subscription [your subscription name]`

+ Per visualizzare l'account di gestione del modello corrente, usare il comando:
  <br>`az ml account modelmanagement show`

**Creare e impostare l'ambiente di distribuzione del cluster**

L'ambiente di distribuzione deve essere impostato una sola volta. Se l'ambiente di distribuzione non è ancora disponibile, configurarlo ora seguendo [queste istruzioni](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Per visualizzare l'ambiente di distribuzione attivo, usare questo comando dell'interfaccia della riga di comando:
<br>`az ml env show`
   
Comando di esempio dell'interfaccia della riga di comando per creare e impostare l'ambiente di distribuzione

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Gestire servizi Web e distribuzioni

È possibile usare le API seguenti per distribuire i modelli come servizi Web e gestire i servizi Web e le distribuzioni.

|Attività|API|
|----|----|
|Creare un oggetto di distribuzione|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Distribuire il servizio Web|`deploy_obj.deploy()`|
|Assegnare un punteggio all'immagine|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Eliminare il servizio Web|`deploy_obj.delete()`|
|Creare l'immagine Docker senza servizio Web|`deploy_obj.build_docker_image()`|
|Elencare la distribuzione esistente|`AMLDeployment.list_deployment()`|
|Eliminare se il servizio esiste con il nome della distribuzione|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Documentazione dell'API:** vedere la [documentazione di riferimento del pacchetto](https://aka.ms/aml-packages/vision) per informazioni dettagliate su ogni modulo e classe.

**Informazioni di riferimento sull'interfaccia della riga di comando:** per operazioni più avanzate correlate alla distribuzione, vedere le [informazioni di riferimento sull'interfaccia della riga di comando per la gestione dei modelli](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Gestione delle distribuzioni nel portale di Azure**: è possibile tracciare e gestire le distribuzioni nel [portale di Azure](https://ms.portal.azure.com/). Dal portale di Azure trovare la pagina dell'account di Gestione modelli di Machine Learning usando il relativo nome. Passare quindi alla pagina dell'account di Gestione modelli > Gestione modelli > Servizi.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Utilizzare il servizio Web 

Dopo aver distribuito il modello come servizio Web, è possibile assegnare un punteggio alle immagini con il servizio Web usando uno dei metodi seguenti:

- Assegnare direttamente un punteggio al servizio Web con l'oggetto di distribuzione usando `deploy_obj.score_image(image_path_or_url)`

- Usare l'URL dell'endpoint del servizio e la chiave del servizio (nessuna per la distribuzione locale) con: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Formulare le richieste HTTP per assegnare direttamente un punteggio all'endpoint del servizio Web. Questa opzione è destinata agli utenti avanzati.

### <a name="score-with-existing-deployment-object"></a>Assegnare un punteggio con l'oggetto di distribuzione esistente

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Assegnare un punteggio con l'URL dell'endpoint di servizio e la chiave del servizio

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Assegnare un punteggio all'endpoint direttamente con la richiesta HTTP

Il codice di esempio seguente formula la richiesta HTTP direttamente in Python. Questa operazione può essere tuttavia eseguita in altri linguaggi di programmazione.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Analizzare il risultato serializzato dal servizio Web

L'output dal servizio Web è una stringa JSON. È possibile analizzare la stringa JSON con diverse classi di modello DNN.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Machine Learning Package per Visione artificiale vedere questi articoli:

+ Informazioni su come [migliorare l'accuratezza di questo modello](how-to-improve-accuracy-for-computer-vision-models.md).

+ Vedere le [informazioni generali sul pacchetto e le istruzioni su come installarlo](https://aka.ms/aml-packages/vision).

+ Esplorare la [documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) relativa a questo pacchetto.

+ Vedere le informazioni su [altri pacchetti Python per Azure Machine Learning](reference-python-package-overview.md).
