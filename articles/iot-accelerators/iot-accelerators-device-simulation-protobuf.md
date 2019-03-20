---
title: Usare Protocol Buffers con Simulazione dispositivi - Azure | Microsoft Docs
description: In questa guida pratica viene descritto come usare Protocol Buffers per serializzare i dati di telemetria inviati dall'acceleratore della soluzione Simulazione dispositivi.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 9657cda8b0f3a19d02ebf1907116235b88f4cb82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111515"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serializzare i dati di telemetria con i Protocol Buffers

Protocol Buffers è un formato di serializzazione binaria per dati strutturati. Protobuf è progettato per migliorare semplicità e prestazioni con l'obiettivo di raggiungere dimensioni inferiori e una velocità superiore rispetto al linguaggio XML.

Simulazione dispositivi supporta la versione **proto3** del linguaggio di Protocol Buffers.

Poiché Protobuf richiede codice compilato per serializzare i dati, è necessario creare una versione personalizzata di Simulazione dispositivi.

I passaggi nella presente guida pratica illustrato come eseguire queste operazioni:

1. Preparare un ambiente di sviluppo
1. Specificare l'uso del formato Protobuf in un modello di dispositivo
1. Definire il formato Protobuf
1. Generare classi Protobuf
1. Test in locale

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura illustrata in questa guida pratica, sono necessari:

* Visual Studio Code. È possibile scaricare [Visual Studio Code per Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. È possibile scaricare [.NET Core per Mac, Linux e Windows](https://www.microsoft.com/net/download).
* Postman. È possibile scaricare [Postman per Mac, Windows o Linux](https://www.getpostman.com/apps).
* Un [hub IoT distribuito nella sottoscrizione di Azure in uso](../iot-hub/iot-hub-create-through-portal.md). Per completare la procedura descritta in questa guida, è necessaria la stringa di connessione dell'hub IoT. È possibile ottenere la stringa di connessione nel portale di Azure.
* Un [database Cosmos DB distribuito nella sottoscrizione di Azure](../cosmos-db/create-sql-api-dotnet.md#create-a-database-account) che usi l'API SQL e che sia configurato per [coerenza assoluta](../cosmos-db/manage-account.md). Per completare la procedura descritta in questa guida, è necessaria la stringa di connessione del database Cosmos DB. È possibile ottenere la stringa di connessione nel portale di Azure.
* Un [account di archiviazione di Azure distribuito nella sottoscrizione di Azure](../storage/common/storage-quickstart-create-account.md). Per completare la procedura descritta in questa guida, è necessaria la stringa di connessione dell'account di archiviazione. È possibile ottenere la stringa di connessione nel portale di Azure.

## <a name="prepare-your-development-environment"></a>Preparare l'ambiente di sviluppo

Completare le attività seguenti per preparare l'ambiente di sviluppo:

* Scaricare il codice sorgente per il microservizio di simulazione dispositivi.
* Scaricare il codice sorgente per il microservizio di adattatore di archiviazione.
* Eseguire il microservizio di adattatore di archiviazione in locale.

Le istruzioni presenti in questo articolo presuppongono che si usi Windows. Se si usa un altro sistema operativo,può essere necessario modificare alcuni percorsi di file e alcuni comandi in base all'ambiente.

### <a name="download-the-microservices"></a>Scaricare i microservizi

Scaricare e decomprimere i [microservizi di monitoraggio remoto](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) da GitHub in un percorso appropriato nel computer locale. Questo repository include il microservizio di adattatore di archiviazione necessario per questa procedura.

Scaricare e decomprimere il [microservizio di simulazione dispositivi](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) da GitHub in un percorso appropriato nel computer locale.

### <a name="run-the-storage-adapter-microservice"></a>Eseguire il microservizio di adattatore di archiviazione

Aprire la cartella **remote-monitoring-services-dotnet-master\storage-adapter** in Visual Studio Code. Fare clic su un pulsante **Ripristina** per risolvere le dipendenze non risolte.

Aprire il file **.vscode/launch.json** e assegnare la stringa di connessione di Cosmos DB alla variabile di ambiente **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING**.

> [!NOTE]
> Quando si esegue localmente il microservizio nel computer, il suo funzionamento richiede comunque un'istanza di Cosmos DB in Azure.

Per eseguire il microservizio di adattatore di archiviazione in locale, fare clic su **Debug \> Avvia debug**.

Nella finestra **Terminale** in Visual Studio Code viene visualizzato un output del microservizio in esecuzione che include un URL per il controllo dell'integrità del servizio Web: <http://127.0.0.1:9022/v1/status>. Quando si passa a questo indirizzo, lo stato deve essere "OK: Attivo e".

Lasciare il microservizio di adattatore di archiviazione in esecuzione nell'istanza corrente di Visual Studio Code fino al completamento dei passaggi successivi.

## <a name="define-your-device-model"></a>Definire il modello di dispositivo

Aprire la cartella **device-simulation-dotnet-master** scaricata da GitHub in una nuova istanza di Visual Studio Code. Fare clic su un pulsante **Ripristina** per risolvere le dipendenze non risolte.

In questa guida pratica si creerà un nuovo modello di dispositivo per un registro delle risorse:

1. Creare un nuovo file di modello di dispositivo chiamato **assettracker-01.json** nella cartella **Services\data\devicemodels**.

1. Definire la funzionalità del dispositivo nel file **assettracker-01.json** del modello di dispositivo. La sezione dei dati di telemetria del modello di dispositivo Protobuf deve:

   * Includere il nome della classe Protobuf generata per il dispositivo. La sezione seguente illustra come generare questa classe.
   * Specificare Protobuf come formato del messaggio.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Creare script di comportamenti del dispositivo

Scrivere lo script che definisce il comportamento del dispositivo. Per altre informazioni, vedere [Creare un dispositivo simulato avanzato](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definire il formato Protobuf

Quando si dispone di un modello di dispositivo ed è stato determinato il formato del messaggio, è possibile creare un file **proto**. Nel file **proto** aggiungere:

* Un oggetto `csharp_namespace` che corrisponde alla proprietà **ClassName** nel modello del dispositivo.
* Un messaggio per ogni struttura dei dati da serializzare.
* Un nome e un tipo per ogni campo nel messaggio.

1. Creare un nuovo file denominato **assettracker.proto** nella cartella **Services\Models\Protobuf\proto**.

1. Definire sintassi, spazio dei nomi e schema del messaggio nel file **proto** come segue:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

Gli indicatori `=1` e `=2` in ogni elemento specificano un tag univoco usato dal campo nella codifica binaria. I numeri da 1 a 15 richiedono un byte in meno da codificare rispetto ai numeri superiori.

## <a name="generate-the-protobuf-class"></a>Generare la classe Protobuf

Se si dispone di un file **proto**, il passaggio successivo consiste nel generare le classi necessarie per leggere e scrivere i messaggi. Per completare questo passaggio, è necessario il compilatore **Protoc** di Protobuf.

1. [Scaricare il compilatore Protobuf da GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Eseguire il compilatore specificando la directory di origine, la directory di destinazione e il nome del file **proto**. Ad esempio: 

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Questo comando genera un file **Assettracker.cs** nella cartella **Services\Models\Protobuf**.

## <a name="test-protobuf-locally"></a>Testare Protobuf in locale

In questa sezione viene testato il dispositivo registro di risorse creato in locale nelle sezioni precedenti.

### <a name="run-the-device-simulation-microservice"></a>Eseguire il microservizio di simulazione dispositivi

Aprire il file **.vscode/launch.json** e assegnare quanto segue:

* Stringa di connessione dell'hub IoT alla variabile di ambiente **PCS\_IOTHUB\_CONNSTRING**.
* Stringa di connessione dell'account di archiviazione alla variabile di ambiente **PCS\_AZURE\_STORAGE\_ACCOUNT**.
* Stringa di connessione di Cosmos DB alla variabile di ambiente **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING**.

Aprire il file **WebService/Properties/launchSettings.json** e assegnare quanto segue:

* Stringa di connessione dell'hub IoT alla variabile di ambiente **PCS\_IOTHUB\_CONNSTRING**.
* Stringa di connessione dell'account di archiviazione alla variabile di ambiente **PCS\_AZURE\_STORAGE\_ACCOUNT**.
* Stringa di connessione di Cosmos DB alla variabile di ambiente **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING**.

Aprire il file **WebService\appsettings.ini** e modificare le impostazioni nel modo seguente:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Configurare la soluzione in modo da includere i nuovi file del modello di dispositivo

Per impostazione predefinita, i file JSON e JS del nuovo modello di dispositivo non verranno copiati all'interno della soluzione creata. È necessario includerli esplicitamente.

Aggiungere una voce al file **services\services.csproj** per ogni file da includere. Ad esempio: 

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Per eseguire il microservizio in locale, fare clic su **Debug \> Avvia debug**.

Nella finestra **Terminale** di Visual Studio Code viene visualizzato l'output del microservizio in esecuzione.

Lasciare il microservizio di simulazione dispositivi in esecuzione nell'istanza corrente di Visual Studio Code fino al completamento dei passaggi successivi.

### <a name="set-up-a-monitor-for-device-events"></a>Configurare un monitor per gli eventi del dispositivo

In questa sezione si usa l'interfaccia della riga di comando di Azure per configurare un monitor degli eventi per visualizzare i dati di telemetria inviati dai dispositivi connessi all'hub IoT.

Lo script seguente presuppone che il nome dell'hub IoT sia **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lasciare il monitor degli eventi in esecuzione mentre si testano i dispositivi simulati.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Creare una simulazione con il tipo di dispositivo registro di risorse

In questa sezione si usa lo strumento Postman per richiedere al microservizio Simulazione dispositivi di eseguire una simulazione tramite il tipo di dispositivo registro di risorse. Postman è uno strumento che consente di inviare richieste REST a un servizio Web.

Per configurare Postman

1. Aprire Postman nel computer locale.

1. Fare clic su **File \> Importa**. Fare clic su **Scegli file**.

1. Selezionare **Azure IoT Device Simulation solution accelerator.postman\_collection** e **Azure IoT Device Simulation solution accelerator.postman\_environment** e fare clic su **Apri**.

1. Espandere **Azure IoT Device Simulation solution accelerator** (Acceleratore soluzione di simulazione dispositivi IoT Azure) per visualizzare le richieste che è possibile inviare.

1. Fare clic su **No Environment** (Nessun ambiente) e selezionare **Azure IoT Device Simulation solution accelerator** (Acceleratore soluzione di simulazione dispositivi IoT Azure).

Sono ora disponibili una raccolta e un ambiente caricati nell'area di lavoro di Postman che è possibile usare per interagire con il microservizio di simulazione dispositivi.

Per configurare ed eseguire la simulazione

1. Nella raccolta Postman selezionare **Create asset tracker simulation** (Crea simulazione registro di risorse) e quindi fare clic su **Send** (Invia). Questa richiesta crea quattro istanze del tipo di dispositivo registro di risorse simulato.

1. L'output del monitor degli eventi nell'interfaccia della riga di comando di Azure mostra i dati di telemetria dei dispositivi simulati.

Per arrestare la simulazione, selezionare la richiesta **Stop simulation** (Arresta simulazione) in Postman e quindi fare clic su **Send** (Invia).

### <a name="clean-up-resources"></a>Pulire le risorse

È possibile arrestare i due microservizi in esecuzione in locale nelle istanze di Visual Studio Code (**Debug \> Arresta debug**).

Se le istanze dell'hub IoT e di Cosmos DB non sono più necessarie, eliminarle dalla sottoscrizione di Azure per evitare eventuali addebiti.

## <a name="iot-hub-support"></a>Supporto dell'hub IoT

Molte funzionalità dell'hub IoT non supportano Protobuf o altri formati binari in modalità nativa. Non è ad esempio possibile instradare in base al payload dei messaggi perché l'hub IoT non sarà in grado di elaborarlo. È tuttavia possibile instradare in base alle intestazioni dei messaggi.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come personalizzare Simulazione dispositivi per usare Protobuf per l'invio di dati di telemetria, verrà ora illustrato come [distribuire un'immagine personalizzata nel cloud](iot-accelerators-device-simulation-deploy-image.md).
