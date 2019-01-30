---
title: Distribuzione IoT di dispositivi simulati personalizzati - Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire dispositivi simulati personalizzati all'acceleratore di soluzioni Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 8491acfd28524ee3bbbd06c788afb668c40c98b9
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450569"
---
# <a name="deploy-a-new-simulated-device"></a>Distribuire un nuovo dispositivo simulato

Gli acceleratori di soluzioni Monitoraggio remoto e Simulazione dispositivi consentono entrambi di definire i propri dispositivi simulati. Questo articolo illustra come distribuire un tipo di dispositivo chiller personalizzato e un tipo di dispositivo nuova lampadina nell'acceleratore di soluzioni Monitoraggio remoto.

I passaggi descritti in questo articolo si basano sul presupposto che sia stata completata la guida pratica [Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md) (Creare e testare un nuovo dispositivo simulato) e si disponga dei file che definiscono i tipi di dispositivi chiller personalizzato e nuova lampadina.

La guida pratica illustrata come:

1. Usare SSH per accedere al file system della macchina virtuale che ospita l'acceleratore di soluzioni Monitoraggio remoto.

1. Configurare Docker per caricare i modelli di dispositivo da una posizione esterna al contenitore Docker.

1. Eseguire l'acceleratore di soluzioni Monitoraggio remoto usando i file di modello di dispositivo personalizzati.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica, sono necessari gli elementi seguenti:

- Un'istanza distribuita dell'[acceleratore di soluzioni Monitoraggio remoto](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Una shell **bash** locale per eseguire i comandi `ssh` e `scp`. In Windows un modo semplice per installare **bash** consiste nell'installare [git](https://git-scm.com/download/win).
- I file di modello dei dispositivi personalizzati, come quelli descritti in [Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md) (Creare e testare un nuovo dispositivo simulato).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurare Docker

In questa sezione si configura Docker per caricare i file di modello dei dispositivi dalla cartella **/tmp/devicemodels** nella macchina virtuale anziché dall'interno del contenitore Docker. Eseguire i comandi di questa sezione in una shell **bash** sul computer locale:

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
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_IOTHUB_CONNSTRING
        - PCS_STORAGEADAPTER_WEBSERVICE_URL=http://storageadapter:9022/v1
        - PCS_AUTH_ISSUER
        - PCS_AUTH_AUDIENCE
        - PCS_AUTH_REQUIRED
        - PCS_CORS_WHITELIST
        - PCS_APPLICATION_SECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Salvare le modifiche.

1. Copiare i file di modello di dispositivo esistenti dal contenitore alla nuova posizione. In primo luogo trovare l'ID contenitore per il contenitore di simulazione dispositivi:

    ```sh
    docker ps
    ```

    Quindi copiare i file di modello di dispositivo nella cartella **tmp** della macchina virtuale. Il comando seguente presuppone che l'ID contenitore sia c378d6878407: sostituire questo valore con l'ID del contenitore della simulazione dispositivi:

    ```sh
    docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
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

È ora possibile usare i modelli di dispositivo personalizzati nella soluzione Monitoraggio remoto:

1. Avviare il dashboard Monitoraggio remoto dagli [acceleratori di soluzioni IoT di Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Usare la pagina **Dispositivi** per aggiungere i dispositivi simulati. Quando si aggiunge un nuovo dispositivo simulato, i nuovi modelli di dispositivo sono disponibili per essere selezionati.

1. È possibile usare il dashboard per visualizzare i dati di telemetria dei dispositivi e chiamare i metodi di dispositivo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di esplorare la soluzione ulteriormente, mantenere distribuito l'acceleratore di soluzione Monitoraggio remoto.

Se l'acceleratore di soluzione non è più necessario, eliminarlo dalla pagina [Soluzioni di cui è stato effettuato il provisioning](https://www.azureiotsolutions.com/Accelerators#dashboard) selezionandolo e facendo clic su **Elimina soluzione**.

## <a name="next-steps"></a>Passaggi successivi

Questa guida ha illustrato come distribuire modelli di dispositivo personalizzati nell'acceleratore di soluzioni Monitoraggio remoto. Il passaggio successivo consigliato consiste nell'apprendere come [connettere un dispositivo reale alla soluzione di monitoraggio remoto](iot-accelerators-connecting-devices-node.md).
