---
title: Simulazione dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come usare il simulatore di dispositivi con la soluzione preconfigurata di monitoraggio remoto.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0bf1cff4058bfe46b54f3f0b6836ede3e04ed5dd
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="test-your-solution-with-simulated-devices"></a>Testare la soluzione con dispositivi simulati

Questa esercitazione illustra come personalizzare il microservizio del simulatore di dispositivi nella soluzione preconfigurata di monitoraggio remoto. Per illustrare le funzionalità del simulatore di dispositivi, questa esercitazione usa due scenari nell'applicazione IoT Contoso.

Nel primo scenario Contoso vuole testare un nuovo dispositivo lampadina intelligente. Per eseguire i test, si crea un nuovo dispositivo simulato con le caratteristiche seguenti:

*Proprietà*

| NOME                     | Valori                      |
| ------------------------ | --------------------------- |
| Colore                    | White (Bianco), Red (Rosso), Blue (Blu)            |
| Brightness (Luminosità)               | Da 0 a 100                    |
| Estimated remaining life (Durata rimanente stimata) | Conto alla rovescia da 10.000 ore |

*Telemetria*

La tabella seguente mostra i dati trasmessi dalla lampadina al cloud come flusso di dati:

| NOME   | Valori      |
| ------ | ----------- |
| Status | "on", "off" |
| Temperatura | Gradi F |
| online | true, false |

> [!NOTE]
> Il valore di dati di telemetria **online** è obbligatorio per tutti i tipi simulati.

*Metodi*

La tabella seguente mostra le azioni supportate dal nuovo dispositivo:

| NOME        |
| ----------- |
| Switch on (Accendi)   |
| Switch off (Spegni)  |

*Stato iniziale*

La tabella seguente mostra lo stato iniziale del dispositivo:

| NOME                     | Valori |
| ------------------------ | -------|
| Initial color (Colore iniziale)            | Bianco  |
| Initial brightness (Luminosità iniziale)       | 75     |
| Initial remaining life (Durata rimanente iniziale)   | 10.000 |
| Initial telemetry status (Stato telemetria iniziale) | "on"   |
| Initial telemetry temperature (Temperatura telemetria iniziale) | 200   |

Nel secondo scenario si aggiunge un nuovo tipo di telemetria al dispositivo **Chiller** (Refrigeratore) esistente di Contoso.

Questa esercitazione mostra come usare il simulatore di dispositivi con la soluzione preconfigurata di monitoraggio remoto:

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Creare un nuovo tipo di dispositivo
> * Simulare il comportamento del dispositivo personalizzato
> * Aggiungere un nuovo tipo di dispositivo al dashboard
> * Inviare dati di telemetria personalizzati da un tipo di dispositivo esistente

## <a name="prerequisites"></a>prerequisiti

Per eseguire questa esercitazione, è necessario quanto segue:

* Un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure. Se la soluzione di monitoraggio remoto non è stata ancora distribuita, è necessario completare l'esercitazione [Distribuire la soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

* Visual Studio 2017. Se Visual Studio 2017 non è installato, è possibile scaricare l'edizione gratuita [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/).

* Estensione di Visual Studio [Cloud Explorer per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview).

* Un account nell'[hub Docker](https://hub.docker.com/). Per iniziare, è possibile registrarsi gratuitamente.

* [Git](https://git-scm.com/downloads) installato nel computer desktop.

## <a name="prepare-your-development-environment"></a>Preparare l'ambiente di sviluppo

Completare le attività seguenti per preparare l'ambiente di sviluppo per l'aggiunta di un nuovo dispositivo simulato alla soluzione di monitoraggio remoto:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Configurare l'accesso SSH alla macchina virtuale della soluzione in Azure

Al momento della creazione della soluzione di monitoraggio remoto in [www.azureiotsuite.com](https://www.azureiotsuite.com), è stato scelto un nome per la soluzione. Questo nome diventa il nome del gruppo di risorse di Azure che contiene le varie risorse distribuite che la soluzione usa. I comandi seguenti usano un gruppo di risorse denominato **Contoso-01**. Sostituire **Contoso-01** con il nome del gruppo di risorse da usare.

I comandi seguenti usano il comando `az` dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). È possibile installare l'interfaccia della riga di comando di Azure 2.0 nel computer di sviluppo oppure usare [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) nel [portale di Azure](http://portal.azure.com). L'interfaccia della riga di comando di Azure 2.0 è preinstallata in Cloud Shell.

1. Per verificare il nome del gruppo di risorse che contiene le risorse di monitoraggio remoto, eseguire il comando seguente:

    ```sh
    az group list | grep "name"
    ```

    Questo comando elenca tutti i gruppi di risorse nella sottoscrizione. L'elenco dovrebbe includere un gruppo di risorse con lo stesso nome della soluzione di monitoraggio remoto in uso.

1. Per impostare il gruppo di risorse come gruppo predefinito per i comandi successivi, eseguire il comando seguente usando il nome del gruppo di risorse al posto di **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Per elencare le risorse nel gruppo di risorse, eseguire il comando seguente:

    ```sh
    az resource list -o table
    ```

    Prendere nota dei nomi della macchina virtuale e del gruppo di sicurezza di rete. Questi valori verranno usati nei passaggi successivi.

1. Per abilitare l'accesso SSH alla macchina virtuale, eseguire il comando seguente usando il nome del gruppo di sicurezza di rete del passaggio precedente:

    ```sh
    az network nsg rule create --name SSH --nsg-name your-network-security-group --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Per visualizzare l'elenco di regole in ingresso per la rete, eseguire questo comando:

    ```sh
    az network nsg rule list --nsg-name Contoso-01-nsg -o table
    ```

1. Per modificare la password della macchina virtuale sostituendola con una password nota, eseguire il comando seguente. Usare il nome della macchina virtuale annotato in precedenza e una password di propria scelta:

    ```sh
    az vm user update --name your-vm-name --username azureuser --password your-password
    ```
1. Per trovare l'indirizzo IP pubblico della macchina virtuale, usare il comando seguente e prendere nota dell'indirizzo IP pubblico:

    ```sh
    az vm list-ip-addresses --name your-vm-name
    ```

1. È ora possibile usare SSH per connettersi alla macchina virtuale. Il comando `ssh` è preinstallato in Cloud Shell. Usare l'indirizzo IP pubblico del passaggio precedente e, quando richiesto, la password configurata per la macchina virtuale:

    ```sh
    ssh azureuser@public-ip-address
    ```

    È ora possibile accedere alla shell nella macchina virtuale che esegue i contenitori Docker nella soluzione di monitoraggio remoto. Per visualizzare i contenitori in esecuzione, usare questo comando:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Trovare le stringhe di connessione del servizio

Nell'esercitazione viene usata una soluzione di Visual Studio che si connette ai servizi Cosmos DB e hub IoT della soluzione. La procedura seguente illustra un modo semplice per trovare i valori della stringa di connessione necessari:

1. Per trovare la stringa di connessione di Cosmos DB, eseguire il comando seguente nella sessione SSH connessa alla macchina virtuale:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Prendere nota della stringa di connessione. Questo valore verrà usato più avanti nell'esercitazione.

1. Per trovare la stringa di connessione dell'hub IoT, eseguire il comando seguente nella sessione SSH connessa alla macchina virtuale:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Prendere nota della stringa di connessione. Questo valore verrà usato più avanti nell'esercitazione.

> [!NOTE]
> È anche possibile trovare le stringhe di connessione nel portale di Azure oppure usando il comando `az`.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Arrestare il servizio di simulazione di dispositivi nella macchina virtuale

Quando si modifica il servizio di simulazione di dispositivi, è possibile eseguirlo in locale per testare le modifiche. Prima di eseguire il servizio di simulazione di dispositivi in locale, è necessario arrestare l'istanza in esecuzione nella macchina virtuale, come indicato di seguito:

1. Per trovare l'**ID contenitore** del servizio **device-simulation**, eseguire il comando seguente nella sessione SSH connessa alla macchina virtuale:

    ```sh
    docker ps
    ```

    Prendere nota dell'ID contenitore del servizio **device-simulation**.

1. Per arrestare il contenitore **device-simulation**, eseguire questo comando:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Clonare i repository GitHub

In questa esercitazione si usano i progetti di Visual Studio **device-simulation** e **storage-adapter**. È possibile clonare i repository di codice sorgente da GitHub. Eseguire questo passaggio nel computer di sviluppo locale in cui è installato Visual Studio:

1. Aprire un prompt dei comandi e passare alla cartella in cui si vuole salvare la copia dei repository GitHub di **device-simulation** e **storage-adapter**.

1. Per clonare la versione .NET del repository di **device-simulation**, eseguire questo comando:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Il servizio di simulazione di dispositivi nella soluzione di monitoraggio remoto consente di apportare modifiche ai tipi di dispositivi simulati predefiniti e di creare nuovi tipi di dispositivi simulati. È possibile usare i tipi di dispositivi personalizzati per testare il comportamento della soluzione di monitoraggio remoto prima di connettere i dispositivi fisici.

1. Per clonare la versione .NET del repository di **storage-adapter**, eseguire questo comando:

    ```cmd
    git clone https://github.com/Azure/storage-adapter.git
    ```

    Il servizio di simulazione di dispositivi usa il servizio adattatore di archiviazione per la connessione al servizio Cosmos DB in Azure. La soluzione di monitoraggio remoto archivia i dati di configurazione del dispositivo simulato in un database Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Eseguire il servizio adattatore di archiviazione in locale

Il servizio di simulazione di dispositivi usa il servizio adattatore di archiviazione per la connessione al database Cosmos DB della soluzione. Se si esegue il servizio di simulazione di dispositivi in locale, è necessario eseguire anche il servizio adattatore di archiviazione in locale. La procedura seguente illustra come eseguire il servizio adattatore di archiviazione da Visual Studio:

1. In Visual Studio aprire il file di soluzione **pcs-storage-adapter.sln** nel clone locale del repository di **storage-adapter**.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WebService**, scegliere **Proprietà** e quindi scegliere **Debug**.

1. Nella sezione **Variabili di ambiente** modificare il valore della variabile **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** in modo che corrisponda alla stringa di connessione di Cosmos DB annotata in precedenza. Salvare quindi le modifiche.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WebService**, scegliere **Debug** e quindi scegliere **Avvia nuova istanza**.

1. Il servizio viene avviato in locale e apre `http://localhost:9022/v1/status` nel browser predefinito. Verificare che il valore di **Stato** sia "OK: Alive and well" (OK: Attivo e funzionante).

1. Lasciare il servizio adattatore di archiviazione in esecuzione in locale fino a quando non è stata completata l'esercitazione.

A questo punto è tutto pronto per iniziare ad aggiungere un nuovo tipo di dispositivo simulato alla soluzione di monitoraggio remoto.

## <a name="create-a-simulated-device-type"></a>Creare un tipo di dispositivo simulato

Il modo più semplice per creare un nuovo tipo di dispositivo nel servizio di simulazione di dispositivi consiste nel copiare e modificare un tipo esistente. I passaggi seguenti mostrano come copiare il dispositivo **Chiller** (Refrigeratore) predefinito per creare un nuovo dispositivo **Lightbulb** (Lampadina):

1. In Visual Studio aprire il file di soluzione **device-simulation.sln** nel clone locale del repository di **device-simulation**.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulationAgent**, scegliere **Proprietà** e quindi scegliere **Debug**.

1. Nella sezione **Variabili di ambiente** modificare il valore della variabile **PCS\_IOTHUB\_CONNSTRING** in modo che corrisponda alla stringa di connessione dell'hub IoT annotata in precedenza. Salvare quindi le modifiche.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione **device-simulation** e scegliere **Imposta progetti di avvio**. Scegliere **Progetto di avvio singolo** e selezionare **SimulationAgent**. Fare quindi clic su **OK**.

1. Per ogni tipo di dispositivo sono presenti un file modello JSON e gli script associati nella cartella **Services/data/devicemodels**. In Esplora soluzioni copiare i file relativi a **Chiller** (Refrigeratore) per creare i file relativi a **Lightbulb** (Lampadina) come illustrato nella tabella seguente:

    | Sorgente                      | Destination                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definire le caratteristiche del nuovo tipo di dispositivo

Il file **lightbulb-01.json** definisce le caratteristiche del tipo, ad esempio i dati di telemetria generati e i metodi supportati. La procedura seguente aggiorna il file **lightbulb-01.json** per definire il dispositivo **Lightbulb** (Lampadina):

1. Nel file **lightbulb-01.json** aggiornare i metadati del dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Nel file **lightbulb-01.json** aggiornare la definizione di simulazione come illustrato nel frammento di codice seguente:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. Nel file **lightbulb-01.json** aggiornare le proprietà del tipo di dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Nel file **lightbulb-01.json** aggiornare le definizioni di telemetria del tipo di dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. Nel file **lightbulb-01.json** aggiornare i metodi del tipo di dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Salvare il file **lightbulb-01.json**.

### <a name="simulate-custom-device-behavior"></a>Simulare il comportamento del dispositivo personalizzato

Il file **scripts/lightbulb-01-state.js** definisce il comportamento di simulazione del tipo **Lightbulb** (Lampadina). La procedura seguente aggiorna il file **scripts/lightbulb-01-state.js** per definire il comportamento del dispositivo **Lightbulb** (Lampadina):

1. Modificare la definizione di stato nel file **scripts/lightbulb-01-state.js** come illustrato nel frammento di codice seguente:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Aggiungere una funzione **flip** dopo la funzione **vary** con la definizione seguente:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Modificare la funzione **main** per implementare il comportamento come illustrato nel frammento di codice seguente:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Salvare il file **scripts/lightbulb-01-state.js**.

Il file **scripts/SwitchOn-method.js** implementa il metodo **Switch On** in un dispositivo **Lightbulb** (Lampadina). La procedura seguente aggiorna il file **scripts/SwitchOn-method.js**:

1. Modificare la definizione di stato nel file **scripts/SwitchOn-method.js** come illustrato nel frammento di codice seguente:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Per accendere la lampadina, modificare la funzione **main** come indicato di seguito:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Salvare il file **scripts/SwitchOn-method.js**.

1. Creare una copia del file **scripts/SwitchOn-method.js** denominata **scripts/SwitchOff-method.js**.

1. Per spegnere la lampadina, modificare la funzione **main** nel file **scripts/SwitchOff-method.js** come indicato di seguito:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Salvare il file **scripts/SwitchOff-method.js**.

1. In Esplora soluzioni selezionare a turno ciascuno dei quattro nuovi file. Nella finestra **Proprietà** per ogni file verificare che l'opzione **Copia nella directory di output** sia impostata su **Copia se più recente**.

### <a name="configure-the-device-simulation-service"></a>Configurare il servizio di simulazione di dispositivi

Per limitare il numero di dispositivi simulati che si connettono alla soluzione durante il test, configurare il servizio per eseguire un singolo dispositivo refrigeratore e un singolo dispositivo lampadina. I dati di configurazione sono archiviati nell'istanza di Cosmos DB nel gruppo di risorse della soluzione. Per modificare i dati di configurazione, usare la vista **Cloud Explorer** in Visual Studio:

1. Per aprire la vista **Cloud Explorer** in Visual Studio, scegliere **Visualizza** e quindi **Cloud Explorer**.

1. Per trovare il documento di configurazione della simulazione, in **Cerca risorse** immettere **simualtions.1**.

1. Fare doppio clic sul documento **simulations.1** per aprirlo e modificarlo.

1. Nel valore **Data** individuare la matrice **DeviceModels** simile al frammento di codice seguente:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Per definire un singolo dispositivo simulato refrigeratore e un singolo dispositivo simulato lampadina, sostituire la matrice **DeviceModels** con il codice seguente:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Salvare le modifiche nel documento **simulations.1**.

> [!NOTE]
> È anche possibile usare Esplora dati di Cosmos DB nel portale di Azure per modificare il documento **simulations.1**.

### <a name="test-the-lightbulb-device-type-locally"></a>Testare il tipo di dispositivo lampadina in locale

A questo punto si è pronti per testare il nuovo tipo di lampadina simulata eseguendo il progetto di simulazione di dispositivi in locale.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **SimulationAgent**, scegliere **Debug** e quindi scegliere **Avvia nuova istanza**.

1. Per controllare che i due dispositivi simulati siano connessi all'hub IoT, aprire il portale di Azure nel browser.

1. Passare all'hub IoT nel gruppo di risorse che contiene la soluzione di monitoraggio remoto.

1. Nella sezione **Monitoraggio** scegliere **Metriche**. Verificare quindi che il numero indicato per **Dispositivi connessi** sia due:

    ![Numero di dispositivi connessi](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. Nel browser passare al **dashboard** per la soluzione di monitoraggio remoto. Nel pannello di telemetria nel **dashboard** selezionare **temperature** (temperatura). La temperatura per i due dispositivi simulati viene visualizzata nel grafico:

    ![Dati di telemetria sulla temperatura](media/iot-suite-remote-monitoring-test/telemetry.png)

La simulazione del dispositivo lampadina è ora in esecuzione in locale. Il passaggio successivo consiste nel distribuire il codice del simulatore aggiornato nella macchina virtuale che esegue i microservizi di monitoraggio remoto in Azure.

Prima di continuare, è possibile arrestare il debug dei progetti di simulazione di dispositivi e di adattatore di archiviazione in Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Distribuire il simulatore aggiornato nel cloud

I microservizi nella soluzione di monitoraggio remoto vengono eseguiti in contenitori Docker. I contenitori sono ospitati nella macchina virtuale della soluzione in Azure. In questa sezione verrà illustrato come:

* Creare una nuova immagine Docker di simulazione di dispositivi.
* Caricare l'immagine nel repository dell'hub Docker.
* Importare l'immagine nella macchina virtuale della soluzione.

I passaggi seguenti presuppongono la presenza di un repository denominato **lightbulb** nell'account dell'hub Docker.

1. In Visual Studio, nel progetto **device-simulation**, aprire il file **solution\scripts\docker\build.cmd**.

1. Modificare la riga che imposta la variabile di ambiente **DOCKER_IMAGE** specificando il nome del repository dell'hub Docker:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Salvare la modifica.

1. In Visual Studio, nel progetto **device-simulation**, aprire il file **solution\scripts\docker\publish.cmd**.

1. Modificare la riga che imposta la variabile di ambiente **DOCKER_IMAGE** specificando il nome del repository dell'hub Docker:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Salvare la modifica.

1. Aprire un prompt dei comandi come amministratore. Passare quindi alla cartella **scripts\docker** nel clone del repository GitHub di **device-simulation**.

1. Per creare l'immagine Docker, eseguire questo comando:

    ```cmd
    build.cmd
    ```

1. Per accedere all'account dell'hub Docker, eseguire questo comando:

    ```cmd
    docker login
    ```

1. Per caricare la nuova immagine nell'account dell'hub Docker, eseguire questo comando:

    ```cmd
    publish.cmd
    ```

1. Per verificare il caricamento, passare a [https://hub.docker.com/](https://hub.docker.com/). Individuare il repository **lightbulb** e scegliere **Details** (Dettagli). Scegliere quindi **Tags** (Tag):

    ![Hub Docker](media/iot-suite-remote-monitoring-test/dockerhub.png)

    Gli script hanno aggiunto il tag **testing** all'immagine.

1. Usare SSH per connettersi alla macchina virtuale della soluzione in Azure. Passare quindi alla cartella **App** e modificare il file **docker-compose.yaml**:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Modificare la voce per il servizio di simulazione di dispositivi in modo da usare l'immagine Docker:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Salvare le modifiche.

1. Per riavviare tutti i servizi con le nuove impostazioni, eseguire questo comando:

    ```sh
    sudo ./start.sh
    ```

1. Per controllare il file di log dal nuovo contenitore di simulazione di dispositivi, eseguire il comando seguente per trovare l'ID contenitore:

    ```sh
    docker ps
    ```

    Eseguire quindi il comando seguente usando l'ID contenitore:

    ```sh
    docker logs {container ID}
    ```

A questo punto, sono stati completati i passaggi per distribuire una versione aggiornata del servizio di simulazione di dispositivi nella soluzione di monitoraggio remoto.

Nel browser passare al **dashboard** per la soluzione di monitoraggio remoto. Nel pannello di telemetria nel **dashboard** selezionare **temperature** (temperatura). La temperatura per i due dispositivi simulati viene visualizzata nel grafico:

![Dati di telemetria sulla temperatura](media/iot-suite-remote-monitoring-test/telemetry.png)

Nella pagina **Devices** (Dispositivi) è possibile effettuare il provisioning delle istanze del nuovo tipo:

![Visualizzare l'elenco di simulazioni disponibili](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

È possibile visualizzare i dati di telemetria del dispositivo simulato:

![Visualizzare i dati di telemetria della lampadina](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

È possibile chiamare i metodi **SwitchOn** e **SwitchOff** nel dispositivo:

![Chiamare i metodi per la lampadina](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Aggiungere un nuovo tipo di telemetria

Questa sezione descrive come modificare un tipo di dispositivo simulato esistente per supportare un nuovo tipo di telemetria.

### <a name="locate-the-chiller-device-type-files"></a>Individuare i file per il tipo di dispositivo Chiller (Refrigeratore)

I passaggi seguenti mostrano come trovare i file che definiscono il dispositivo **Chiller** (Refrigeratore) predefinito:

1. Se non è già stato fatto, usare il comando seguente per clonare il repository GitHub **device-simulation** nel computer locale:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Per ogni tipo di dispositivo sono presenti un file modello JSON e gli script associati nella cartella `data/devicemodels`. I file che definiscono il tipo di dispositivo **Chiller** (Refrigeratore) simulato sono:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Specificare il nuovo tipo di telemetria

I passaggi seguenti mostrano come aggiungere un nuovo tipo **Internal Temperature** (Temperatura interna) al tipo di dispositivo **Chiller** (Refrigeratore):

1. Aprire il file **chiller-01.json**.

1. Aggiornare il valore di **SchemaVersion** come indicato di seguito:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Nella sezione **InitialState** aggiungere le due definizioni seguenti:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Nella matrice **Telemetry** aggiungere la definizione seguente:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Salvare il file **chiller-01.json**.

1. Aprire il file **scripts/chiller-01-state.js**.

1. Aggiungere i campi seguenti alla variabile **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aggiungere la riga seguente alla funzione **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Salvare il file **scripts/chiller-01-state.js**.

### <a name="test-the-chiller-device-type"></a>Testare il tipo di dispositivo Chiller (Refrigeratore)

Per testare il tipo di dispositivo **Chiller** (Refrigeratore) aggiornato, eseguire prima di tutto una copia locale del servizio **device-simulation** per verificare che il tipo di dispositivo si comporti come previsto. Dopo avere eseguito il test e il debug del tipo di dispositivo aggiornato in locale, è possibile ricreare il contenitore e ridistribuire il servizio **device-simulation** in Azure.

Quando si esegue il servizio **device-simulation** in locale, i dati di telemetria vengono inviati alla soluzione di monitoraggio remoto. Nella pagina **Devices** (Dispositivi) è possibile effettuare il provisioning delle istanze del tipo aggiornato.

Per testare le modifiche ed eseguirne il debug in locale, vedere la sezione precedente [Testare il tipo di dispositivo lampadina in locale](#test-the-lightbulb-device-type-locally).

Per distribuire il servizio di simulazione di dispositivi aggiornato nella macchina virtuale della soluzione in Azure, vedere la sezione precedente [Distribuire il simulatore aggiornato nel cloud](#deploy-the-updated-simulator-to-the-cloud).

Nella pagina **Devices** (Dispositivi) è possibile effettuare il provisioning delle istanze del tipo aggiornato:

![Aggiungere il refrigeratore aggiornato](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

È possibile visualizzare il nuovo tipo di telemetria **Internal temperature** (Temperatura interna) del dispositivo simulato.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Creare un nuovo tipo di dispositivo
> * Simulare il comportamento del dispositivo personalizzato
> * Aggiungere un nuovo tipo di dispositivo al dashboard
> * Inviare dati di telemetria personalizzati da un tipo di dispositivo esistente

A questo punto si è appreso come personalizzare il servizio di simulazione di dispositivi. Il passaggio successivo consigliato consiste nell'apprendere come [connettere un dispositivo fisico alla soluzione di monitoraggio remoto](iot-suite-connecting-devices-node.md).

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->