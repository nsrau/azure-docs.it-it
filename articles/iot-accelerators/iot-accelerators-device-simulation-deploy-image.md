---
title: Distribuire un'immagine personalizzata di simulazione dispositivi - Azure | Microsoft Docs
description: Questa guida descrive in modo dettagliato come distribuire un'immagine Docker personalizzata della soluzione di simulazione dispositivi in Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61448401"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Distribuire un'immagine Docker di simulazione dispositivi

È possibile modificare la soluzione di simulazione dispositivi per aggiungere funzionalità personalizzate. L'articolo [Serialize telemetry using Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) (Serializzare i dati di telemetria con Protocol Buffers) illustra come aggiungere un dispositivo personalizzato alla soluzione che usa Protocol Buffers (Protobuf) per inviare dati di telemetria. Dopo aver testato le modifiche in locale, il passaggio successivo consiste nel distribuire le modifiche all'istanza di simulazione dispositivi in Azure. Per completare questa attività, è necessario creare e distribuire un'immagine Docker contenente il servizio modificato.

I passaggi nella presente guida pratica illustrato come eseguire queste operazioni:

1. Preparare un ambiente di sviluppo
1. Generare una nuova immagine Docker
1. Configurare la simulazione dispositivi per usare la nuova immagine Docker
1. Eseguire una simulazione usando la nuova immagine

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, sono necessari gli elementi seguenti:

* Un'istanza distribuita del [servizio di simulazione dispositivi](quickstart-device-simulation-deploy.md).
* Docker. Scaricare [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) per la piattaforma in uso.
* Un [account dell'hub Docker](https://hub.docker.com/) in cui è possibile caricare le immagini Docker. Nell'account dell'hub Docker creare un repository pubblico denominato **device-simulation**.
* Una [soluzione di simulazione dispositivi](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) modificata e testata sul computer locale. È possibile ad esempio modificare la soluzione descritta in [Serialize telemetry using Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) (Serializzare i dati di telemetria con Protocol Buffers).
* Una shell in grado di eseguire SSH. Se si installa Git per Windows, è possibile usare la shell **bash** che è parte dell'installazione. È anche possibile usare [Azure Cloud Shell](https://shell.azure.com/).

Le istruzioni presenti in questo articolo presuppongono che si usi Windows. Se si usa un altro sistema operativo,può essere necessario modificare alcuni percorsi di file e alcuni comandi in base all'ambiente.

## <a name="create-a-new-docker-image"></a>Creare una nuova immagine Docker

Per distribuire le proprie modifiche nel servizio di simulazione dispositivi, è necessario modificare gli script di compilazione e distribuzione nella cartella **scripts\docker** per caricare i contenitori nell'account docker-hub.

### <a name="modify-the-docker-scripts"></a>Modificare gli script di Docker

Modificare gli script **build.cmd**, **publish.cmd** e **run.cmd** di Docker nella cartella **scripts\docker** in base alle informazioni del repository Docker in uso. Questi passaggi presuppongono che sia stato creato un repository pubblico denominato **device-simulation**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Aggiornare il file **docker-compose.yml** nel modo seguente:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Configurare la soluzione in modo da includere altri nuovi file

Se si aggiungono nuovi file di modello di dispositivo, è necessario includerli in modo esplicito nella soluzione. Aggiungere una voce al file **services/services.csproj** per ogni file aggiuntivo da includere. Se ad esempio è stata completata la procedura dettagliata descritta in [Serialize telemetry using Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) (Serializzare i dati di telemetria con Protocol Buffers), aggiungere le voci seguenti:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generare nuove immagini Docker ed eseguire il push nell'hub Docker

Pubblicare la nuova immagine Docker nell'hub Docker usando l'account docker-hub:

1. Aprire un prompt dei comandi e passare alla copia locale del repository di simulazione dispositivi.

1. Passare alla cartella **docker**:

    ```cmd
    cd scripts\docker
    ```

1. Eseguire questo comando per creare l'immagine Docker:

    ```cmd
    build.cmd
    ```

1. Eseguire questo comando per pubblicare l'immagine Docker nel repository dell'hub Docker. Accedere a Docker con le credenziali dell'hub Docker:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aggiornare il servizio

Per aggiornare il contenitore di simulazione dispositivi per usare l'immagine personalizzata, completare i passaggi seguenti:

* Usare SSH per connettersi alla macchina virtuale che ospita l'istanza di simulazione dispositivi. Usare l'indirizzo IP e la password annotati nella sezione precedente:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Passare alla directory **/app**:

    ```sh
    cd /app
    ```

* Modificare il file **docker-compose.yml**:

    ```sh
    sudo nano docker-compose.yml
    ```

    Modificare l'**immagine** per puntare dall'immagine **device-simulation** personalizzata caricata nel repository dell'hub Docker:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Salvare le modifiche.

* Eseguire questo comando per riavviare i microservizi:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Eseguire la simulazione

È ora possibile eseguire una simulazione usando la soluzione di simulazione dispositivi personalizzata:

1. Avviare l'interfaccia utente Web della Simulazione dispositivi dagli [acceleratori di soluzioni IoT di Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Usare l'interfaccia utente Web per configurare ed eseguire una simulazione. Se in precedenza non è stata completata la procedura dettagliata descritta in [Serialize telemetry using Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) (Serializzare i dati di telemetria con Protocol Buffers), è possibile usare il modello di dispositivo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver è appreso come distribuire un'immagine personalizzata di simulazione dispositivi, è possibile apprendere come [usare un hub IoT esistente con l'acceleratore della soluzione di simulazione dispositivi](iot-accelerators-device-simulation-choose-hub.md).