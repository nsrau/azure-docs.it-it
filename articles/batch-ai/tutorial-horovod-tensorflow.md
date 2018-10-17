---
title: Esercitazione - Training distribuito con Azure Batch per intelligenza artificiale e Horovod | Microsoft Docs
description: Esercitazione - Come eseguire il training di un modello distribuito con Horovod usando il servizio Azure Batch per intelligenza artificiale e l'interfaccia della riga di comando di Azure.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: de19b20865127fd37cd7bc1ac854288a95a68091
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058137"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Esercitazione: Eseguire il training di un modello distribuito con Horovod

In questa esercitazione viene eseguito il training di un modello di apprendimento avanzato distribuito tramite l'esecuzione in parallelo su più nodi in un cluster Batch per intelligenza artificiale. Batch per intelligenza artificiale è un servizio gestito per il training di modelli di apprendimento automatico e di intelligenza artificiale su larga scala su cluster di GPU di Azure. 

Questa esercitazione presenta un flusso di lavoro di Batch per intelligenza artificiale comune e illustra come interagire con le risorse di Batch per intelligenza artificiale tramite l'interfaccia della riga di comando di Azure. Gli argomenti trattati includono:

> [!div class="checklist"]
> * Impostare un'area di lavoro, un esperimento e un cluster Batch per intelligenza artificiale
> * Configurare una condivisione file di Azure per input e output
> * Eseguire in parallelo un modello di apprendimento avanzato usando Horovod
> * Inviare un processo di training
> * Monitorare il processo
> * Recuperare i risultati del training

In questa esercitazione viene modificato un modello di rilevamento degli oggetti affinché venga eseguito in parallelo con [Horovod](https://github.com/uber/horovod). Il modello esegue il training sul [set di dati CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) delle immagini. Il processo di training viene eseguito in un cluster che contiene 4 GPU e 24 CPU virtuali. Il completamento del processo può richiedere circa 60 minuti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.38 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Perché usare Horovod?

Horovod è un framework di training distribuito per Tensorflow, Keras e PyTorch e viene usato in questa esercitazione. Horovod consente di convertire uno script di training progettato per essere eseguito su un singolo GPU in uno che può essere eseguito in modo efficiente in un sistema distribuito, usando solo poche righe di codice.

Oltre a Horovod, Batch per intelligenza artificiale supporta il training distribuito con altri framework open source diffusi. Verificare le condizioni di licenza dei framework usati prima di eseguire il training dei modelli in fase di produzione.

## <a name="prepare-the-batch-ai-environment"></a>Preparare l'ambiente di Batch per intelligenza artificiale

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Usare il comando `az group create` per creare un gruppo di risorse denominato `batchai.horovod` nell'area `eastus`. Il gruppo di risorse viene usato per distribuire le risorse di Batch per intelligenza artificiale.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Creare un'area di lavoro

Creare un'area di lavoro Batch per intelligenza artificiale usando il comando `az batchai workspace create`. Un'area di lavoro è una raccolta di primo livello di altre risorse di Batch per intelligenza artificiale. Il comando seguente crea un'area di lavoro Batch per intelligenza artificiale denominata `batchaidev` nel gruppo di risorse.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Creare un esperimento

Un esperimento di Batch per intelligenza artificiale consente di raggruppare uno o più processi che vengono gestiti e su cui si eseguono query come gruppo. Il comando `cifar` seguente crea un esperimento denominato `az batchai experiment create` nell'area di lavoro e nel gruppo di risorse.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Configurare un cluster GPU

Configurare quindi un cluster GPU per eseguire l'esperimento. Batch per intelligenza artificiale offre una gamma flessibile di opzioni per personalizzare i cluster per esigenze specifiche.

Il comando `nc6cluster` seguente crea un cluster a 4 nodi denominato `az batchai cluster create` nell'area di lavoro e nel gruppo di risorse. Per impostazione predefinita, la VM nel cluster esegue un'immagine di Ubuntu Server progettata per ospitare applicazioni basate su contenitori. I nodi del cluster in questo esempio usano la dimensione `Standard_NC6`, che contiene un processore GPU NVIDIA Tesla K80.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Per visualizzare lo stato del cluster, eseguire il comando `az batchai cluster show`. Il provisioning completo del cluster richiede in genere alcuni minuti.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

Nelle prime fasi di creazione del cluster, questo si trova nello stato `resizing`. Proseguire con i passaggi successivi mentre lo stato del cluster viene modificato. Il cluster è pronto per eseguire il processo di training quando lo stato è `steady` e i nodi sono `idle`. Ad esempio: 

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Configurare l'archiviazione

Usare il comando `az storage account create` per creare un account di archiviazione in cui archiviare lo script e l'output del training.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Nell'account creare una condivisione file di Azure denominata `myshare` usando il comando `az storage share create`:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

In pratica, è possibile usare questa stessa risorsa di archiviazione con più processi ed esperimenti. Per organizzare al meglio le attività, creare una directory all'interno della condivisione file per archiviare i file correlati a questo specifico esperimento. Il comando `az storage directory create` seguente consente di creare una directory denominata `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

Il passaggio successivo consiste nel preparare lo script di training effettivo, che viene quindi caricato nella directory appena creata.

## <a name="create-the-training-script"></a>Creare uno script di training

In questo esperimento viene eseguito uno script Python leggermente modificato per l'esecuzione di un modello di rilevamento degli oggetti in parallelo usando Horovod. Il [modello originale](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) usa Keras con un back-end TensorFlow. 

In una directory di lavoro nella shell, usare l'editor di testo preferito per creare un file denominato `cifar_cnn_distributed.py` con il contenuto seguente. Le modifiche al codice sorgente originale sono commentate con il prefisso `HOROVOD`.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Come mostrato nell'esempio, per abilitare il training distribuito usando il framework Horovod sono necessarie solo alcune modifiche al modello. 

Tenere presente che questo script usa un modello e un set di dati relativamente piccoli a scopo dimostrativo, pertanto questo modello distribuito non necessariamente mostrerà un miglioramento significativo delle prestazioni. Per osservare le potenzialità effettive del training distribuito, è consigliabile usare un modello e un set di dati di dimensioni maggiori.

## <a name="upload-the-training-script"></a>Scaricare lo script di training

Quando lo script è pronto, caricarlo nella directory di condivisione file creata in precedenza. Il comando `az storage file upload` seguente carica lo script dalla directory di lavoro locale al percorso appropriato.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Inviare il processo di training

Dopo aver completato i passaggi precedenti, creare un processo di training. In Batch per intelligenza artificiale, usare un file `job.json` per definire i parametri di esecuzione del processo. Usando l'editor di testo preferito, creare un file di configurazione del processo chiamato `job.json` con il contenuto seguente.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Descrizione delle proprietà:

| Proprietà | DESCRIZIONE |
| --------- | --------- |
| `nodeCount` | Il numero di nodi da dedicare al processo. In questo caso, il processo viene eseguito in parallelo su `4` nodi. |
| `horovodSettings` | Il campo `pythonScriptFilePath` definisce il percorso allo script Horovod, che si trova nella directory `cifar` creata in precedenza. Il campo `commandLineArgs` indica gli argomenti della riga di comando per l'esecuzione dello script. Per questo esperimento, l'unico argomento richiesto è la directory in cui salvare il modello. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` è il percorso in cui è stata montata la condivisione file. | 
| `stdOutErrPathPrefix` | Il percorso in cui archiviare gli output e i log del processo, che per questo esempio è la stessa directory `cifar`. |
| `jobPreparation` | Eventuali istruzioni speciali per la preparazione dell'ambiente di esecuzione del processo. Questo script richiede l'installazione dei pacchetti MPI e Horovod indicati. |
| `containerSettings` | Impostazioni per il contenitore in cui viene eseguito il processo. Questo processo usa un contenitore Docker compilato con `tensorflow`.

Con la configurazione, creare il processo usando il comando `az batchai job create`. Il comando seguente mette in coda un nuovo processo denominato `cifar_distributed` usando tutte le risorse impostate finora. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Se i nodi sono attualmente occupati, potrebbe essere necessario qualche minuto prima dell'esecuzione del processo. Usare il comando `az batchai job show` per visualizzare lo stato di esecuzione del processo.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Visualizzare il training distribuito

Dopo l'avvio dell'esecuzione del processo, usare di nuovo il comando `az batchai cluster show` per eseguire una query sullo stato dei nodi del cluster. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

L'output dovrebbe essere simile al seguente, che mostra i quattro nodi in esecuzione. Questo risultato indica che tutti e quattro i nodi sono attualmente usati nel training distribuito.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Monitorare il processo

### <a name="list-output-files"></a>Elencare i file di output

Durante l'esecuzione del processo, usare il comando `az batchai job file list` per elencare i file di output generati dal processo.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

Pr questo specifico esperimento, l'output dovrebbe essere simile al seguente. L'output completo del processo viene registrato in `stdout.txt` mentre `stderr.txt` restituisce eventuali errori verificatisi durante l'esecuzione. Gli altri file sono gli output, gli errori e i log di preparazione dei processi corrispondenti a ogni singolo nodo.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Trasmettere un file di output

Usare il comando `az batchai job file stream` per trasmettere il contenuto di un file. L'esempio seguente trasmette il log di output principale.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

Durante l'esecuzione del processo, il comando trasmette l'output standard del processo di training, e appare simile al seguente.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

Lo script esegue il training in 25 periodi o viene eseguito il pass-through al set di dati del training. Il completamento di questo processo richiede circa 60 minuti. 

## <a name="retrieve-the-results"></a>Recuperare i risultati

Dopo il corretto completamento dello script, la precisione della convalida dovrebbe essere del 70-75%; il modello con training viene salvato nella condivisione file in `cifar/saved_models/keras_cifar10_trained_model.h5`. 

In genere, il training del modello fa parte di un flusso di lavoro più grande. Ad esempio, è possibile esporre il modello con training in un'altra applicazione. Per scaricare il modello con training in locale, usare il comando `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dei processi in esecuzione, per risparmiare sui costi di elaborazione è consigliabile eseguire il downgrade di tutti i cluster a `0 nodes`, così da non ricevere addebiti per i periodi di inattività. Usare il comando `az batchai cluster resize` seguente. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Per eseguire i processi, è possibile eseguire un successivo ridimensionamento a 1 o più nodi. 

Se non si prevede di usare in futuro l'area di lavoro e l'account di archiviazione, eliminare il gruppo di risorse usando il comando `az group delete`. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse che ne fanno parte.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso a:

> [!div class="checklist"]
> * Impostare un'area di lavoro, un esperimento e un cluster Batch per intelligenza artificiale
> * Configurare una condivisione file di Azure per input e output
> * Eseguire un modello in parallelo usando Horovod
> * Inviare un processo di training
> * Monitorare il processo
> * Recuperare i risultati del training

Per esempi d'uso di Batch per intelligenza artificiale con diversi framework, vedere le recipe su GitHub.

> [!div class="nextstepaction"]
> [Recipe di Batch per intelligenza artificiale](https://github.com/Azure/BatchAI/tree/master/recipes)
