---
title: Eseguire il debug di moduli Python per Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per sviluppare e compilare un modulo Python per Azure IoT Edge ed eseguirne il debug
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0dfe096bb3a2a2116ead2423f53a5e44c8f02630
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567520"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Usare Visual Studio Code per sviluppare un modulo Python per Azure IoT Edge ed eseguirne il debug

È possibile trasformare la logica di business in moduli per Azure IoT Edge. Questo articolo illustra come usare Visual Studio Code (VS Code) come strumento principale per sviluppare ed eseguire il debug di moduli Python.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si usi un computer o una macchina virtuale Windows o Linux come computer di sviluppo. È possibile anche simulare il dispositivo IoT Edge nel computer di sviluppo con il daemon di sicurezza di IoT Edge.

> [!NOTE]
> Questo articolo sul debug illustra come collegare un processo a un contenitore di modulo ed eseguirne il debug con VS Code. È possibile eseguire il debug di moduli Python solo in contenitori Linux amd64. Se non si ha ancora familiarità con le funzionalità di debug di Visual Studio Code, vedere le [informazioni sul debug](https://code.visualstudio.com/Docs/editor/debugging). 

Poiché questo articolo usa Visual Studio Code come strumento di sviluppo principale, installare VS Code. Aggiungere quindi le estensioni necessarie:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Estensione Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) per l'installazione di pacchetti Python (generalmente incluso con l'installazione di Python).
* Installare **Cookiecutter** con il comando riportato di seguito
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

Per creare un modulo, è necessario Docker per creare l'immagine del modulo e un registro contenitori in cui memorizzare l'immagine del modulo:
* [Docker Community Edition](https://docs.docker.com/install/) nel computer di sviluppo. 
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/) o [hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Per prototipi e test è possibile usare un registro Docker locale anziché un registro nel cloud. 

Per testare il modulo in un dispositivo, è necessario un hub IoT attivo con almeno un dispositivo IoT Edge. Per usare il computer come dispositivo IoT Edge, seguire i passaggi nella guida introduttiva per [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Creare un nuovo modello di soluzione

Eseguire questi passaggi per creare un modulo IoT Edge basato su Azure IoT Python SDK tramite Visual Studio Code e l'estensione Azure IoT Edge. Viene creata prima una soluzione e quindi viene generato il primo modulo in tale soluzione. Ogni soluzione può contenere più di un modulo. 

1. In Visual Studio Code selezionare **Visualizza** > **Terminale integrato**.

2. Selezionare **Visualizza** > **Riquadro comandi**. 

3. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge).

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Passare alla cartella in cui si vuole creare la nuova soluzione. Scegliere **Seleziona cartella**. 

5. Immettere un nome per la soluzione. 

6. Selezionare **Modulo Python** come modello per il primo modulo nella soluzione.

7. Immettere un nome per il modulo. Scegliere un nome univoco all'interno del registro contenitori. 

8. Specificare il nome del repository di immagini del modulo. VS Code popola automaticamente il nome del modulo con il valore **localhost:5000**. Sostituire tale valore con le proprie informazioni di registro. Se per il test si usa un registro Docker locale, **localhost** è corretto. Se si usa Registro contenitori di Azure, specificare il server di accesso indicato nelle impostazioni del registro. Il server di accesso ha un nome simile a **\<nome registro\>.azurecr.io**. Sostituire solo la parte localhost della stringa, non eliminare il nome del modulo. 

   ![Specificare il repository di immagini Docker](./media/how-to-develop-c-module/repository.png)

VS Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra.

Nella soluzione sono presenti quattro elementi: 
* Una cartella **.vscode** contenente le configurazioni di debug.
* Una cartella **modules** contenente le sottocartelle di ogni modulo. A questo punto è presente un solo elemento. È possibile tuttavia aggiungere altri elemento nel riquadro comandi con il comando **Azure IoT Edge: Aggiungi modulo Edge IoT**. 
* Un file con estensione **env** in cui sono elencate le variabili di ambiente. Se Registro contenitori di Azure è il registro, si avranno un nome utente e una password per Registro contenitori di Azure. 

   > [!NOTE]
   > Il file dell'ambiente viene creato solo se si specifica un repository di immagini per il modulo. Se sono state accettate le impostazioni predefinite di localhost per testare ed eseguire il debug in locale, non è necessario dichiarare le variabili di ambiente. 

* Un file **deployment.template.json** in cui sono elencati il nuovo modulo e un modulo **tempSensor** di esempio che simula i dati utilizzabili per il test. Per altre informazioni su come funzionano i manifesti di distribuzione, vedere [Informazioni su come usare i manifesti della distribuzione per distribuire moduli e definire route](module-composition.md). 

## <a name="develop-your-module"></a>Sviluppare il modulo

Il codice del modulo Python predefinito in dotazione con la soluzione si trova in **modules** > [nome del modulo] > **main.c**. Il modulo e il file deployment.template.json sono impostati in modo che sia possibile compilare la soluzione, inviarla al registro del contenitore e distribuirla in un dispositivo per avviare il test senza toccare alcun codice. Il modulo viene compilato solo per accettare l'input da un'origine (in questo caso, il modulo tempSensor che simula i dati) e collegarlo all'hub IoT. 

Quando è il momento di personalizzare il modello Python con il codice personalizzato, usare gli [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) per compilare i moduli che rispondono alle esigenze chiave delle soluzioni IoT quali sicurezza, gestione dei dispositivi e affidabilità. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilare e distribuire il modulo per il debug

In ogni cartella di modulo sono presenti diversi file Docker per tipi di contenitore differenti. Usare uno di questi file che terminano con l'estensione **debug** per compilare il modulo per il test. Attualmente i moduli Python supportano solo il debug in contenitori Linux amd64. 

1. In VS Code passare al file `deployment.template.json`. Aggiornare l'URL dell'immagine del modulo aggiungendo **.debug** alla fine.

2. Nel modulo Python sostituire createOptions in **deployment.template.json** con il contenuto seguente e salvare il file: 
    
    ```json
    "createOptions": "{\"ExposedPorts\":{\"5678/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"5678/tcp\":[{\"HostPort\":\"5678\"}]}}}"
    ```

3. Passare a `main.py` e aggiungere il codice seguente dopo la sezione import
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. Aggiungere la riga di codice seguente al callback da sottoporre a debug

    ```python
    ptvsd.break_into_debugger()
    ```

   Se ad esempio si vuole eseguire il debug del metodo `receive_message_callback`. È possibile inserire la riga di codice come illustrato di seguito.

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. Nel riquadro comandi di VS Code immettere ed eseguire il comando **Azure IoT Edge: Build and Push IoT Edge solution** (Azure IoT Edge: Compila ed esegui il push della soluzione IoT Edge).
3. Selezionare il file `deployment.template.json` per la soluzione dal riquadro comandi. 
4. In Device Explorer dell'hub IoT di Azure fare doppio clic su un ID del dispositivo IoT Edge. Quindi selezionare **Create deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 
5. Aprire la cartella **config** della soluzione. Selezionare il file `deployment.json`. Scegliere **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge). 

Si noterà quindi che la distribuzione è stata creata correttamente con un ID distribuzione in un terminale integrato di Visual Studio Code.

Controllare lo stato del contenitore nello strumento di esplorazione di Visual Studio Code o tramite il comando `docker ps` nel terminale.

## <a name="start-debugging-python-module-in-vs-code"></a>Avviare il debug del modulo Python in Visual Studio Code
Visual Studio Code consente di mantenere le informazioni di configurazione del debug in un file `launch.json` che si trova nella cartella `.vscode` nell'area di lavoro. Il file `launch.json` è stato generato durante la creazione di una nuova soluzione IoT Edge e viene aggiornato ogni volta che si aggiunge un nuovo modulo con il supporto per il debug. 

1. Passare alla visualizzazione di debug di VS Code. Selezionare il file di configurazione del debug per il modulo. Il nome dell'opzione di debug deve essere simile a **Debug remoto NomeModulo (Python)**

2. Accedere a `main.c`. Aggiungere un punto di interruzione nel metodo di callback in cui si è inserito `ptvsd.break_into_debugger()`.

3. Selezionare **Avvia debug** o premere **F5**. Selezionare il processo a cui collegarsi.

4. Nella visualizzazione di debug di Visual Studio Code è possibile vedere le variabili nel pannello di sinistra. 

L'esempio precedente illustra come eseguire il debug di moduli IoT Edge Python nei contenitori. Sono state aggiunte le porte esposte nel contenitore del modulo createOptions. Al termine del debug dei moduli Python, è consigliabile rimuovere le porte esposte per predisporre i moduli IoT Edge per l'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato il modulo, vedere le informazioni su come [distribuire i moduli Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md).

Per sviluppare moduli per i dispositivi IoT Edge, [Comprendere e usare gli SDK per l'hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md).
