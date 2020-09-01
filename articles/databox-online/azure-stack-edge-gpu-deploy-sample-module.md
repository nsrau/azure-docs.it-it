---
title: Distribuire il modulo GPU sul dispositivo GPU Azure Stack Edge | Microsoft Docs
description: Viene descritto come abilitare il calcolo e rendere il dispositivo Azure Stack Edge pronto per il calcolo tramite l'interfaccia utente locale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 5af86001d46bf194c9b61f325052a4cde0d86d5e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254560"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-gpu-device"></a>Distribuire un modulo Internet delle cose abilitate per la GPU nel dispositivo GPU Azure Stack Edge

Questo articolo descrive come distribuire una GPU abilitata IoT Edge modulo sul dispositivo GPU Azure Stack Edge. 

In questo articolo vengono illustrate le operazioni seguenti:
  - Preparare Azure Stack Edge per eseguire un modulo GPU.
  - Scaricare e installare il codice di esempio da un repository git.
  - Compilare la soluzione e generare un manifesto di distribuzione.
  - Distribuire la soluzione nel dispositivo Azure Stack Edge.
  - Monitorare l'output del modulo.


## <a name="about-sample-module"></a>Informazioni sul modulo di esempio

Il modulo di esempio GPU in questo articolo include il codice di esempio PyTorch e TensorFlow benchmarking per la CPU rispetto alla GPU.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di avere:

- È possibile accedere a un dispositivo Azure Stack Edge a 1 nodo abilitato per GPU. Questo dispositivo viene attivato con una risorsa in Azure. Vedere [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Il calcolo è stato configurato in questo dispositivo. Eseguire la procedura descritta in [esercitazione: configurare il calcolo nel dispositivo Azure stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md).
- Un Container Registry di Azure (ACR). Passare al pannello **chiavi di accesso** e prendere nota del server di accesso di ACR, il nome utente e la password. Per altre informazioni, vedere [Guida introduttiva: creare un registro contenitori privato usando il portale di Azure](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- Le risorse di sviluppo seguenti in un client Windows:
    - [INTERFACCIA della riga di comando di Azure 2,0 o versione successiva](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). È possibile che sia necessario creare un account per scaricare e installare il software.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Estensione Azure IOT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).    
    - [Estensione Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - PIP per l'installazione di pacchetti Python (in genere inclusi nell'installazione di Python)

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

1. Passare ad [Azure Intelligent Edge Patterns in Azure Samples](https://github.com/azure-samples/azure-intelligent-edge-patterns). Clonare o scaricare il file zip per il codice. 

    ![Scaricare il file zip](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Estrarre i file dal file zip. È anche possibile clonare gli esempi.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Compila e Distribuisci modulo

1. Aprire la cartella **GpuReferenceModules** in Visual Studio Code.

    ![Apri GPUReferenceModules in VS Code](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Aprire il *deployment.template.js* e identificare i parametri a cui fa riferimento per il registro contenitori. Nel file seguente vengono usati CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD e CONTAINER_REGISTRY_NAME.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Creare un nuovo file. Compilare i valori per i parametri del registro contenitori (usare quelli identificati nel passaggio precedente) come indicato di seguito: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Di seguito è riportato un file con *estensione ENV* di esempio:
    
    ![Creare e salvare un file con estensione ENV](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Salvare il file come *. env* nella cartella **SampleSolution**

5. Per accedere a Docker, immettere il comando seguente nel Visual Studio Code terminale integrato. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Passare alla sezione **chiavi di accesso** del registro contenitori nel portale di Azure. Copiare e utilizzare il nome del registro di sistema, la password e il server di accesso.

    ![Chiavi di accesso nel registro contenitori](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Una volta specificate le credenziali, l'accesso ha esito positivo.

    ![Accesso riuscito](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Eseguire il push dell'immagine nel registro contenitori di Azure. In Esplora VS Code selezionare e fare clic con il pulsante destro del mouse sul file **deployment.template.js** e quindi scegliere **compila e push IOT Edge soluzione**. 

    ![Compilare ed eseguire il push della soluzione IoT Edge](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Se le estensioni Python e Python non sono installate, verranno installate quando si compila e si esegue il push della soluzione. Tuttavia, ciò comporta tempi di compilazione più lunghi. 

    Al termine di questo passaggio, verrà visualizzato il modulo nel registro contenitori.

    ![Modulo nel registro contenitori](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Per creare un manifesto di distribuzione, fare clic con il pulsante destro del mouse sul **deployment.template.js** e quindi selezionare **genera IOT Edge manifesto della distribuzione**. 

    ![Generate IoT Edge deployment manifest (Genera manifesto della distribuzione di IoT Edge)](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    La notifica informa il percorso in cui è stato generato il manifesto di distribuzione. Il manifesto è il `deployment.amd64.json` file generato nella cartella **config** . 

8. Selezionare il **deployment.amd64.js** nel file nella cartella **config** , quindi scegliere **Crea distribuzione per singolo dispositivo**. Non usare il **deployment.template.jssu** file. 

    ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    Nella finestra **output** dovrebbe essere visualizzato un messaggio in cui la distribuzione è riuscita.

    ![Distribuzione riuscita nell'output](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>Monitorare il modulo  

1. Nel riquadro comandi di VS Code eseguire **Azure IoT Hub: Select IoT Hub** (Hub IoT di Azure: Seleziona l'hub IoT).

2. Scegliere la sottoscrizione e l'hub IoT che contiene il dispositivo IoT Edge che si vuole configurare. In questo caso, selezionare la sottoscrizione usata per distribuire il dispositivo Azure Stack Edge e selezionare il dispositivo IoT Edge creato per il dispositivo Azure Stack Edge. Questo errore si verifica quando si configura il calcolo tramite il portale di Azure nei passaggi precedenti.

3. In Esplora VS Code espandere la sezione Hub Azure. In **dispositivi**dovrebbe essere visualizzato il dispositivo IOT Edge corrispondente al dispositivo Azure stack Edge. 

    1. Selezionare il dispositivo, fare clic con il pulsante destro del mouse e scegliere **Avvia monitoraggio endpoint evento predefinito**.
  
        ![Avviare il monitoraggio](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Passare a **dispositivi > moduli** e dovrebbe essere visualizzato il **modulo GPU** in esecuzione.

        ![Modulo nell'hub Internet](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. Il terminale VS Code dovrebbe anche visualizzare gli eventi dell'hub Internet come output di monitoraggio per il dispositivo Azure Stack Edge.

        ![Monitoraggio dell'output](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        È possibile notare che il tempo impiegato per eseguire lo stesso set di operazioni (5000 iterazioni della trasformazione forma) dalla GPU è molto inferiore rispetto a quello per la CPU.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [configurare GPU per l'uso di un modulo](azure-stack-edge-j-series-configure-gpu-modules.md).
