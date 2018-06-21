---
title: Compilare e distribuire un modello di rilevamento degli oggetti tramite Azure Machine Learning Package per Visione artificiale.
description: Informazioni su come compilare, sottoporre a training, testare e distribuire un modello di rilevamento degli oggetti con visione artificiale usando Azure Machine Learning Package per Visione artificiale.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 62cc37d8c462d0fc1831de7b50a85738d6e63a17
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726682"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Compilare e distribuire modelli di rilevamento degli oggetti con Azure Machine Learning

Questo articolo descrive come usare **Azure Machine Learning Package per Visione artificiale** per sottoporre a training, testare e distribuire un modello di rilevamento degli oggetti [Faster R-CNN](https://arxiv.org/abs/1506.01497). 

Il rilevamento degli oggetti consente di risolvere numerosi problemi nell'ambito della visione artificiale. Questi problemi includono la creazione di modelli in grado di individuare un numero variabile di oggetti in un'immagine. 

Quando si compila e si distribuisce tale modello con questo pacchetto, si segue questa procedura:
1.  Creazione del set di dati
2.  Definizione di un modello DNN (Deep Neural Network, Rete neurale profonda)
3.  Training del modello
4.  Valutazione e visualizzazione
5.  Distribuzione del servizio Web
6.  Test di carico del servizio Web

In questo esempio viene usato TensorFlow come framework di Deep Learning, il training viene eseguito localmente in un computer con tecnologia GPU, ad esempio la [VM per la data science di Deep Learning](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), e la distribuzione usa l'interfaccia della riga di comando di operazionalizzazione di Azure Machine Learning.

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
> [Scaricare il notebook di Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Caricare i dati di esempio

Per questa demo, viene fornito un set di dati relativo ad alimentari da conservare in frigorifero, che comprende 30 immagini e 8 classi (uova, yogurt, ketchup, funghi, senape, arance, spremuta e acqua). Per ogni immagine JPG è disponibile un file XML di annotazione con un nome simile. 

La figura seguente illustra la struttura di cartelle consigliata. 

![struttura di cartelle](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Annotazione immagine

I percorsi degli oggetti con annotazioni sono necessari per il training e la valutazione di un rilevatore di oggetti. [LabelImg](https://tzutalin.github.io/labelImg) è uno strumento di annotazione open source che può essere usato per annotare le immagini. LabelImg scrive un file XML per ogni immagine in formato Pascal-VOC, che può essere letto da questo pacchetto. 

## <a name="storage-context"></a>Contesto di archiviazione
Il contesto di archiviazione viene usato per determinare dove vengono archiviati diversi file di output, ad esempio i file di modello DNN. Per altre informazioni, vedere la [documentazione di StorageContext](https://docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-ml-py-latest). Non è generalmente necessario impostare il contenuto di archiviazione in modo esplicito. Tuttavia, per evitare il limite di dimensioni di 25 MB del progetto Workbench, impostare la directory di output in modo che punti a una posizione all'esterno del progetto AML ("../../../../cvtk_output"). Rimuovere la directory "cvtk_output" quando non è più necessaria.


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.utils import detection_utils
from matplotlib import pyplot as plt

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Initialize the context object
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)

# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Creare un set di dati

Creare un set di dati CVTK costituito da un set di immagini, con le rispettive annotazioni del rettangolo di selezione. In questo esempio, vengono usate le immagini del frigorifero disponibili in "../sample_data/foods/training". Sono supportate solo immagini JPEG.


```python
image_folder = "../sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Definire un modello

In questo esempio viene usato il modello Faster R-CNN. Quando si definisce questo modello, è possibile specificare diversi parametri. Il significato di questi parametri, nonché dei parametri usati per il training (vedere la sezione successiva), è illustrato nella documentazione dell'API di CVTK o sul [sito Web relativo al rilevamento degli oggetti tramite Tensorflow](https://github.com/tensorflow/models/tree/master/research/object_detection). Altre informazioni sul modello Faster R-CNN sono disponibili tramite [questo collegamento](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Questo modello è basato su Fast R-CNN. Altre informazioni in proposito sono disponibili [qui](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Eseguire il training del modello

Come punto di partenza per il training viene usato il modello Faster R-CNN sottoposto a training tramite COCO con ResNet50. 

Per addestrare il rilevamento, il numero di passaggi di training nel codice è impostato su 350, in modo da eseguire il training più rapidamente (circa 5 minuti con una GPU). In pratica, impostare questo valore su un numero almeno 10 volte superiore a quello delle immagini nel set di training.

In questo esempio, il numero di passaggi di training del rilevatore è impostato su 350 per velocizzare il training. Tuttavia, come regola generale, impostare i passaggi su un numero almeno 10 volte superiore a quello delle immagini nel set di training.

Due parametri chiave per il training sono:
- Numero di passaggi per il training del modello, rappresentato dall'argomento num_seps. Ogni passaggio esegue il training del modello con un mini-batch di dimensioni pari a uno.
- Velocità di apprendimento, che può essere impostata tramite initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard può essere usato per visualizzare lo stato di avanzamento del training. Gli eventi di TensorBoard sono disponibili nella cartella specificata dall'attributo train_dir dell'oggetto modello. Per visualizzare TensorBoard, eseguire la procedura seguente:
1. Copiare il codice che inizia con "tensorboard --logdir" in una riga di comando ed eseguirlo. 
2. Copiare l'URL restituito dalla riga di comando in un Web browser per visualizzare TensorBoard. 

L'aspetto di TensorBoard dovrebbe essere simile allo screenshot seguente. Sono necessari alcuni minuti per il popolamento della cartella di training. Pertanto, se TensorBoard non viene visualizzato correttamente la prima volta, provare a ripetere i passaggi 1 e 2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Valutare il modello

Il metodo 'evaluate' viene usato per valutare il modello. Questa funzione richiede un oggetto ObjectDetectionDataset come input. Il set di dati di valutazione può essere creato tramite la stessa funzione usata per il set di dati di training. La metrica supportata è la precisione media, come definita per [PASCAL COV Challenge](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "../sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

I risultati della valutazione possono essere visualizzati in un formato pulito.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Analogamente, è possibile calcolare l'accuratezza del modello sul set di training. Questo consente di garantire che il training contribuisca a una soluzione efficace. Se il training ha avuto esito positivo, spesso l'accuratezza sul set di training è vicina al 100%.

I risultati della valutazione possono essere visualizzati anche in TensorBoard, inclusi i valori mAP e le immagini con i rettangoli di selezione stimati. Copiare il codice seguente in una finestra della riga di comando per avviare il client TensorBoard. In questo caso, viene usato il valore di porta 8008 per evitare conflitti con il valore predefinito 6006, che è stato usato per la visualizzazione dello stato del training.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Assegnare un punteggio a un'immagine

Dopo aver verificato le prestazioni del modello sottoposto a training, la funzione "score" dell'oggetto modello può essere usata per assegnare un punteggio a nuove immagini. È possibile visualizzare i punteggi restituiti con la funzione "visualize". 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = out_root_path + "/scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Salvare il modello

Il sottoposto a training può essere salvato su disco e caricato nuovamente in memoria, come illustrato negli esempi di codice seguenti.


```python
save_model_path = out_root_path + "/frozen_model/faster_rcnn.model" # Please save your model to outside of your AML workbench project folder because of the size limit of AML project
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Caricare il modello salvato per l'assegnazione dei punteggi

Per usare il modello salvato, caricare il modello in memoria con la funzione "load". È sufficiente eseguire il caricamento una sola volta. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Dopo il caricamento del modello, questo può essere usato per assegnare un punteggio a un'immagine o un elenco di immagini. Per una singola immagine, viene restituito un dizionario con chiavi come "detection_boxes", "detection_scores" e "num_detections". Se l'input è un elenco di immagini, viene restituito un elenco di dizionari, con un dizionario corrispondente a ogni immagine. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

È possibile visualizzare gli oggetti rilevati con punteggi superiori a 0,5, inclusi le etichette, i punteggi e le coordinate.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Visualizzare i punteggi come in precedenza.


```python
path_save = out_root_path + "/scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Operazionalizzazione: distribuzione e utilizzo

L'operazionalizzazione è il processo di pubblicazione di modelli e codice come servizi Web e l'utilizzo di questi servizi per produrre risultati aziendali. 

Dopo aver eseguito il training del modello, è possibile distribuire tale modello per l'utilizzo come servizio Web tramite l'[interfaccia della riga di comando di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). I modelli possono essere distribuiti nel computer locale o in un cluster del servizio contenitore di Azure (ACS). Con il servizio contenitore di Azure è possibile ridimensionare il servizio Web manualmente oppure usare la funzionalità di scalabilità automatica.

**Accedere con l'interfaccia della riga di comando di Azure**

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
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Utilizzare il servizio Web

Una volta creato il servizio Web, è possibile assegnare un punteggio alle immagini con il servizio Web distribuito. Sono disponibili diverse opzioni:

   - È possibile assegnare un punteggio direttamente dal servizio Web tramite l'oggetto di distribuzione con: deploy_obj.score_image(image_path_or_url) 
   - In alternativa, è possibile usare l'URL dell'endpoint di servizio e la chiave del servizio (None per la distribuzione locale) con: AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
   - Formulare le richieste HTTP per assegnare direttamente un punteggio all'endpoint del servizio Web (per utenti esperti).

### <a name="score-with-existing-deployment-object"></a>Assegnare un punteggio con l'oggetto di distribuzione esistente
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Assegnare un punteggio con l'URL dell'endpoint di servizio e la chiave del servizio
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Assegnare un punteggio all'endpoint direttamente con la richiesta HTTP
Il codice di esempio seguente formula la richiesta HTTP direttamente in Python. Questa operazione può essere eseguita in altri linguaggi di programmazione.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Analizzare il risultato serializzato dal servizio Web
Il risultato dal servizio Web è in una stringa JSON che può essere analizzata.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "../../../cvtk_output/scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Machine Learning Package per Visione artificiale vedere questi articoli:

+ Vedere le [informazioni generali sul pacchetto e le istruzioni su come installarlo](https://aka.ms/aml-packages/vision).

+ Esplorare la [documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) relativa a questo pacchetto.

+ Vedere le informazioni su [altri pacchetti Python per Azure Machine Learning](reference-python-package-overview.md).
