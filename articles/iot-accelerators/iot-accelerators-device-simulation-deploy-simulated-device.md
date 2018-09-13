---
title: Distribuzione IoT di dispositivi simulati personalizzati - Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire dispositivi simulati personalizzati nell'acceleratore di soluzioni Simulazione dispositivi.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346582"
---
# <a name="deploy-a-new-simulated-device"></a>Distribuire un nuovo dispositivo simulato

Gli acceleratori di soluzioni Monitoraggio remoto e Simulazione dispositivi consentono entrambi di definire i propri dispositivi simulati. Questo articolo illustra come distribuire un tipo di dispositivo chiller personalizzato e un tipo di dispositivo nuova lampadina nell'acceleratore di soluzioni Simulazione dispositivi.

I passaggi descritti in questo articolo si basano sul presupposto che sia stata completata la guida pratica [Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md) (Creare e testare un nuovo dispositivo simulato) e si disponga dei file che definiscono i tipi di dispositivi chiller personalizzato e nuova lampadina.

La guida pratica illustrata come:

1. Usare SSH per accedere al file system della macchina virtuale che ospita l'acceleratore di soluzioni Simulazione dispositivi.

1. Configurare Docker per caricare i modelli di dispositivo da una posizione esterna al contenitore Docker.

1. Eseguire una simulazione usando i file di modello dei dispositivi personalizzati.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per eseguire la procedura descritta in questa guida è necessaria una sottoscrizione di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica servono:

- Un'istanza distribuita dell'[acceleratore di soluzioni Simulazione dispositivi](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS).
- Una shell **bash** locale per eseguire i comandi `ssh` e `scp`. In Windows un modo semplice per installare **bash** consiste nell'installare [git](https://git-scm.com/download/win).
- I file di modello dei dispositivi personalizzati, come quelli descritti in [Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md) (Creare e testare un nuovo dispositivo simulato).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurare Docker

In questa sezione si configura Docker per caricare i file di modello dei dispositivi dalla cartella **/tmp/devicemodels** nella macchina virtuale anziché dall'interno del contenitore Docker. Eseguire i comandi di questa sezione in una shell **bash** sul computer locale:

1. Usare SSH per connettersi alla macchina virtuale in Azure dal computer locale. Il comando seguente si basa sul presupposto che l'indirizzo IP pubblico della macchina virtuale **vm-vikxv** sia **104.41.128.108**: sostituire questo valore con l'indirizzo IP pubblico della macchina virtuale della sezione precedente:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Seguire le istruzioni per accedere alla macchina virtuale con la password impostata nella sezione precedente.

1. Configurare il servizio di simulazione dispositivi per caricare i modelli di dispositivo dall'esterno del contenitore. Prima di tutto aprire il file di configurazione di Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Individuare le impostazioni per il contenitore **devicesimulation** e modificare l'impostazione **volumes** come mostrato nel frammento di codice seguente:

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    Salvare le modifiche.

1. Creare le cartelle per archiviare i file di script e JSON:

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    Mantenere aperta la finestra **bash** con la sessione SSH.

1. Copiare i file di modello dei dispositivi personalizzati nella macchina virtuale. Eseguire questo comando in un'altra shell **bash** nel computer in cui sono stati creati i modelli di dispositivo personalizzati. In primo luogo passare alla cartella locale contenente i file JSON dei modelli di dispositivo. I comandi seguenti si basano sul presupposto che l'indirizzo IP pubblico della macchina virtuale sia **104.41.128.108**: sostituire questo valore con l'indirizzo IP pubblico della macchina virtuale. Immettere la password della macchina virtuale quando richiesto:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Riavviare il contenitore Docker della simulazione dispositivi per usare i nuovi modelli di dispositivo. Eseguire i comandi seguenti nella shell **bash** con la sessione SSH aperta alla macchina virtuale:

    ```sh
    sudo /app/start.sh
    ```

    Se si vuole vedere lo stato dei contenitori Docker in esecuzione e i relativi ID contenitore, usare il comando seguente:

    ```sh
    docker ps
    ```

    Se si vuole vedere il log del contenitore della simulazione dispositivi, eseguire il comando seguente. Sostituire l'ID contenitore con l'ID del contenitore della simulazione dispositivi:

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Eseguire la simulazione

È ora possibile eseguire una simulazione usando i modelli di dispositivo personalizzati:

1. Avviare l'interfaccia utente Web della Simulazione dispositivi dagli [acceleratori di soluzioni IoT di Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Usare l'interfaccia utente Web per configurare ed eseguire una simulazione usando uno dei modelli di dispositivo personalizzati.

1. Quando si esegue una simulazione, fare clic su **View IoT Hub metrics in the Azure portal** (Visualizza le metriche di hub IoT nel portale di Azure) per monitorare la simulazione. In alternativa è possibile usare il comando seguente dell'interfaccia della riga di comando di Azure per monitorare il dispositivo per il traffico cloud. Sostituire il nome dell'hub IoT con il nome del proprio hub:

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di esplorare la soluzione ulteriormente, lasciare distribuito l'acceleratore della soluzione Simulazione dispositivi.

Se l'acceleratore di soluzione non è più necessario, eliminarlo dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsolutions.com/Accelerators#dashboard) selezionandolo e facendo clic su **Elimina soluzione**.

## <a name="next-steps"></a>Passaggi successivi

Questa guida ha illustrato come distribuire modelli di dispositivo personalizzati all'acceleratore di soluzioni Simulazione dispositivi. Si consiglia di approfondire le conoscenze sullo [schema del modello di dispositivo](iot-accelerators-device-simulation-device-schema.md).
