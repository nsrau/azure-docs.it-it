---
title: Distribuire modelli nei dispositivi IoT Edge dal servizio Azure Machine Learning | Microsoft Docs
description: Informazioni su come distribuire un modello dal servizio Azure Machine Learning nei dispositivi Azure IoT Edge. La distribuzione di un modello in un dispositivo periferico consente di valutare i dati nel dispositivo invece di inviarli al cloud e attendere una risposta.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: dc6119bdca850a71064795a80f3087c15189a2e5
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710002"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Preparare la distribuzione di modelli nei dispositivi IoT Edge

Questo documento illustra come usare il servizio Azure Machine Learning per preparare un modello con training da distribuire in un dispositivo Azure IoT Edge.

Un dispositivo Azure IoT Edge è un dispositivo Linux o Windows che esegue il runtime di Azure IoT Edge. È possibile distribuire i modelli di apprendimento automatico in tali dispositivi come moduli di IoT Edge. La distribuzione di un modello in un dispositivo IoT Edge consente al dispositivo di usare il modello direttamente invece di inviare i dati al cloud per l'elaborazione. Si ottengono tempi di risposta più rapidi e un trasferimento dei dati inferiore.

Prima di distribuire un modello in un dispositivo periferico, usare la procedura descritta in questo documento per preparare il modello e il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://aka.ms/AMLfree) prima di iniziare.

* Un'area di lavoro del servizio Azure Machine Learning. Per crearne una, usare la procedura descritta nel documento [Iniziare a usare il servizio Azure Machine Learning](quickstart-get-started.md).

* Un ambiente di sviluppo. Per altre informazioni, vedere il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

* Un [hub IoT di Azure](../../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure. 

* Un modello con training. Per un esempio di esecuzione del training di un modello, vedere il documento [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md). Nel [repository GitHub di AI Toolkit per Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) è disponibile un modello con training preliminare.

## <a name="prepare-the-iot-device"></a>Preparare il dispositivo IoT

Per informazioni su come registrare il dispositivo e installare il runtime di IoT, attenersi ai passaggi indicati nel documento [Guida introduttiva: Distribuire il primo modulo IoT Edge in un dispositivo Linux x64](../../iot-edge/quickstart-linux.md).

## <a name="register-the-model"></a>Registrare il modello

I moduli di Azure IoT Edge sono basati su immagini del contenitore. Per distribuire il modello in un dispositivo IoT Edge, eseguire la procedura seguente per registrare il modello in un'area di lavoro del servizio Azure Machine Learning e creare un'immagine Docker. 

1. Inizializzare l'area di lavoro e caricare il file config.json:

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. Registrare il modello nell'area di lavoro. Sostituire il testo predefinito con percorso, nome, tag e descrizione del modello:

    > [!IMPORTANT]
    > Se è stato usato Azure Machine Learning per il training del modello, è possibile che quest'ultimo sia già registrato nell'area di lavoro. In questo caso, ignorare questo passaggio. Per visualizzare l'elenco dei modelli registrati nell'area di lavoro, usare `Model.list(ws)`.

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Recuperare il modello registrato: 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Creare un'immagine Docker

1. Creare uno **script di assegnazione dei punteggi** denominato `score.py`. Questo file viene usato per eseguire il modello nell'immagine. Deve includere le funzioni seguenti:

    * La funzione `init()` che carica il modello in un oggetto globale. Questa funzione viene eseguita una sola volta all'avvio del contenitore Docker. 

    * La funzione `run(input_data)` usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON, ma sono supportati anche altri formati.

    Per un esempio, vedere l'[esercitazione sulla classificazione delle immagini](tutorial-deploy-models-with-aml.md#make-script).

1. Creare un **file di ambiente** denominato `myenv.yml`. Questo file è una specifica di ambiente Conda ed elenca tutte le dipendenze necessarie allo script e al modello. Per un esempio, vedere l'[esercitazione sulla classificazione delle immagini](tutorial-deploy-models-with-aml.md#make-myenv).

1. Configurare l'immagine Docker usando i file `score.py` e `myenv.yml`:
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Creare l'immagine usando la configurazione del modello e dell'immagine:

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    Per creare l'immagine possono essere necessari circa 5 minuti.

## <a name="get-the-container-registry-credentials"></a>Acquisire le credenziali del registro contenitori

Azure IoT usa le credenziali per il registro contenitori in cui il servizio Azure Machine Learning archivia le immagini docker. Usare la procedura seguente per ottenere le credenziali:

1. Accedere al [portale di Azure](https://portal.azure.com/signin/index).

1. Passare all'area di lavoro del servizio Azure Machine Learning e selezionare __Panoramica__. Per passare alle impostazioni del registro contenitori selezionare il collegamento __Registro__.

    ![Immagine della voce nel registro contenitori](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. Una volta aperto il registro contenitori, selezionare **Chiavi di accesso** e quindi abilitare l'utente amministratore.

    ![Immagine della schermata delle chiavi di accesso](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Salvare i valori del server di accesso, del nome utente e della password. 

   Queste credenziali sono necessarie per consentire ai dispositivi IoT edge l'accesso alle immagini nel registro contenitori privato.

## <a name="next-steps"></a>Passaggi successivi

La preparazione per la distribuzione è stata completata. A questo punto, eseguire la procedura descritta nel documento [Distribuire i moduli di Azure IoT Edge dal portale di Azure](../../iot-edge/how-to-deploy-modules-portal.md) per eseguire la distribuzione nel dispositivo periferico. Quando si configura il __Registro delle impostazioni di sistema__ del dispositivo, usare le credenziali configurate in precedenza.
