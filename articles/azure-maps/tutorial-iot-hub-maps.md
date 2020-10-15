---
title: "Esercitazione: Implementare l'analisi spaziale di IoT | Mappe di Microsoft Azure"
description: Esercitazione sull'integrazione dell'hub IoT con le API del servizio Mappe di Microsoft Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3eb405783b16d1bb7de27f6638dba394457601c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321833"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Esercitazione: Implementare l'analisi spaziale di IoT con Mappe di Azure

Il rilevamento e l'acquisizione di eventi pertinenti che si verificano nello spazio e nel tempo sono uno scenario comune di IoT, ad esempio per gestione della flotta, tracciamento di asset, mobilità e applicazioni per città intelligenti. Questa esercitazione illustra una soluzione che tiene traccia degli spostamenti di auto a noleggio usando le API di Mappe di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione di Azure per registrare i dati di monitoraggio delle auto.
> * Caricare un recinto virtuale nel servizio dati di Mappe di Azure usando l'API di caricamento dati.
> * Creare un hub nell'hub IoT di Azure e registrare un dispositivo.
> * Creare una funzione in Funzioni di Azure, implementando la logica di business basata sull'analisi spaziale di Mappe di Azure.
> * Sottoscrivere gli eventi di telemetria del dispositivo IoT dalla funzione di Azure tramite Griglia di eventi di Azure.
> * Filtrare gli eventi di telemetria usando il routing dei messaggi dell'hub IoT.

## <a name="prerequisites"></a>Prerequisiti

1. Accedere al [portale di Azure](https://portal.azure.com).

2. [Creare un account di Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione Per altre informazioni, vedere [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md).

4. [Creare un gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). In questa esercitazione si assegnerà il nome *ContosoRental* al gruppo di risorse, ma è possibile scegliere qualsiasi nome.

5. Scaricare il [progetto C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="use-case-rental-car-tracking"></a>Caso d'uso: monitoraggio delle auto a noleggio

Una società di noleggio auto vuole registrare le informazioni sulla posizione, la distanza percorsa e lo stato corrente dei veicoli. La società vuole anche archiviare queste informazioni ogni volta che un'auto esce dall'area geografica autorizzata corretta.

Le auto a noleggio sono dotate di dispositivi IoT che inviano regolarmente dati di telemetria all'hub IoT. La telemetria include la posizione corrente e indica se il motore dell'automobile è in funzione o meno. Lo schema della posizione del dispositivo è conforme allo [schema Plug and Play per i dati geospaziali](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Lo schema di telemetria del dispositivo dell'auto a noleggio è simile al codice JSON seguente:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

In questa esercitazione verrà monitorato solo un veicolo. Dopo aver configurato i servizi di Azure, è necessario scaricare il [progetto C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) per eseguire il simulatore di veicoli. L'intero processo, dall'evento all'esecuzione della funzione, viene riepilogato nei passaggi seguenti:

1. Il dispositivo nel veicolo invia i dati di telemetria all'hub IoT.

2. Se il motore dell'auto è in funzione, l'hub pubblica i dati di telemetria in Griglia di eventi.

3. Viene attivata una funzione di Azure in seguito alla sottoscrizione degli eventi di telemetria del dispositivo.

4. La funzione registra le coordinate della posizione del dispositivo del veicolo, l'ora dell'evento e l'ID del dispositivo. Usa quindi l'[API Get Geofence del servizio spaziale](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) per determinare se l'auto è uscita dal recinto virtuale. Se si trova al di fuori dei confini del recinto virtuale, la funzione archivia i dati sulla posizione ricevuti dall'evento in un contenitore BLOB. Inoltre, la funzione esegue una query di [ricerca di indirizzi inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per convertire le coordinate della posizione in un indirizzo e archiviarlo con il resto dei dati sulla posizione del dispositivo.

Il diagramma seguente offre una panoramica generale del sistema.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagramma della panoramica del sistema.":::

Nella figura seguente l'area del recinto virtuale è evidenziata in blu. L'itinerario dell'auto a noleggio è indicato da una linea verde.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Diagramma della panoramica del sistema.":::

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Per archiviare i dati di monitoraggio delle violazioni delle auto, creare un [account di archiviazione per utilizzo generico v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) nel gruppo di risorse. Se non è stato creato un gruppo di risorse, seguire le istruzioni riportate in [Creare un gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). In questa esercitazione al gruppo di risorse verrà assegnato il nome *ContosoRental*.

Per creare un account di archiviazione, seguire le istruzioni in [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). In questa esercitazione verrà usato il nome *contosorentalstorage* per l'account di archiviazione, ma è possibile assegnare un nome qualsiasi.

Dopo aver creato l'account di archiviazione,è necessario creare un contenitore in cui archiviare i dati di registrazione.

1. Passare all'account di archiviazione appena creato. Nella sezione **Informazioni di base** fare clic sul collegamento **Contenitori**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Diagramma della panoramica del sistema.":::

2. Nell'angolo in alto a sinistra selezionare **+ Contenitore**. Sul lato destro del browser viene visualizzato un pannello. Assegnare al contenitore il nome *contoso-rental-logs* e selezionare **Crea**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Diagramma della panoramica del sistema.":::

3. Passare al riquadro **Chiavi di accesso** nell'account di archiviazione e copiare i valori per **Nome account di archiviazione** e **Chiave** nella sezione **key1**. Entrambi i valori sono necessari nella sezione "Creare una funzione di Azure e aggiungere una sottoscrizione di Griglia di eventi".

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Diagramma della panoramica del sistema.":::

## <a name="upload-a-geofence"></a>Caricare un recinto virtuale

A questo punto usare l'[app Postman](https://www.getpostman.com) per [caricare il recinto virtuale](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) in Mappe di Azure. Il recinto virtuale definisce l'area geografica autorizzata per il veicolo a noleggio. Per determinare se un'auto è uscita dall'area del recinto virtuale, verrà usato il recinto virtuale nella funzione di Azure.

Seguire questa procedura per caricare il recinto virtuale usando l'API di caricamento dati di Mappe di Azure: 

1. Aprire l'app Postman e selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta). Assegnare un nome alla raccolta e selezionare **Create** (Crea).

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta) e immettere un nome per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo HTTP **POST** nella scheda del generatore e immettere l'URL seguente per caricare il recinto virtuale nell'API di caricamento dati. Assicurarsi di sostituire `{subscription-key}` con la chiave di sottoscrizione primaria.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Il valore `geojson` del parametro `dataFormat` nel percorso URL rappresenta il formato dei dati caricati.

4. Selezionare **Body** > **raw** (Corpo > non elaborato) per il formato di input e scegliere **JSON** nell'elenco a discesa. [Aprire il file di dati JSON](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copiare il codice JSON nella sezione del corpo. Selezionare **Send** (Invia).

5. Fare clic su **Send** (Invia) e attendere l'elaborazione della richiesta. Al completamento della richiesta, passare alla scheda **Headers** (Intestazioni) della risposta. Copiare il valore della chiave **Location** (Posizione), ovvero lo `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Per controllare lo stato della chiamata API, creare una richiesta HTTP **GET** sullo `status URL`. È necessario accodare la chiave di sottoscrizione primaria all'URL per l'autenticazione. La richiesta **GET** dovrebbe essere simile all'URL seguente:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it returns a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. Copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

L'hub IoT consente la comunicazione bidirezionale sicura e affidabile tra un'applicazione IoT e i dispositivi che gestisce. Per questa esercitazione, si vogliono ottenere informazioni dal dispositivo nel veicolo per determinare la posizione dell'auto a noleggio. In questa sezione viene creato un hub IoT all'interno del gruppo di risorse *ContosoRental*. Questo hub sarà responsabile della pubblicazione degli eventi di telemetria del dispositivo.

> [!NOTE]
> La funzionalità per la pubblicazione di eventi di telemetria del dispositivo in Griglia di eventi è attualmente in anteprima. È disponibile in tutte le aree, ad eccezione delle seguenti: Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale, Azure per enti pubblici, Azure Cina (21Vianet) e Azure Germania.

Per creare un hub IoT nel gruppo di risorse *ContosoRental*, seguire la procedura descritta in [Creare un hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Registrare un dispositivo nell'hub IoT

I dispositivi non possono connettersi all'hub IoT, a meno che non siano registrati nel registro delle identità dell'hub IoT. In questo caso verrà creato un singolo dispositivo con il nome *InVehicleDevice*. Per creare e registrare il dispositivo nell'hub IoT, seguire la procedura descritta in [Registrare un nuovo dispositivo nell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Assicurarsi di copiare la stringa di connessione primaria del dispositivo. Sarà necessario più avanti.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Creare una funzione e aggiungere una sottoscrizione di Griglia di eventi

Funzioni di Azure è un servizio di calcolo serverless che consente di eseguire piccole parti di codice ("funzioni") senza la necessità di effettuare esplicitamente il provisioning dell'infrastruttura di calcolo o di gestirla. Per altre informazioni, vedere [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Una funzione viene attivata da un determinato evento. Qui verrà creata una funzione attivata da un trigger di Griglia di eventi. Creare la relazione tra il trigger e la funzione creando una sottoscrizione per gli eventi di telemetria del dispositivo nell'hub IoT. Quando si verifica un evento di telemetria del dispositivo, la funzione viene chiamata come endpoint e riceve i dati appropriati per il dispositivo registrato precedentemente nell'hub IoT.

Ecco il [codice di script C# contenuto nella funzione](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Configurare ora la funzione di Azure.

1. Nel dashboard del portale di Azure selezionare **Crea una risorsa**. Digitare **App per le funzioni** nella casella di testo di ricerca. Selezionare **App per le funzioni** > **Crea**.

1. Nella pagina di creazione dell'**App per le funzioni** assegnare un nome all'app per le funzioni. In **Gruppo di risorse** selezionare **ContosoRental** nell'elenco a discesa. Selezionare **.NET Core** come **Stack di runtime**. Nella parte inferiore della pagina selezionare **Avanti: Hosting >** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Diagramma della panoramica del sistema.":::

1. Per **Account di archiviazione** selezionare l'account di archiviazione creato in [Creare un account di archiviazione di Azure](#create-an-azure-storage-account). Selezionare **Rivedi e crea**.

1. Esaminare i dettagli dell'app per le funzioni e selezionare **Crea**.

1. Dopo aver creato l'app, aggiungervi una funzione. Passare all'app per le funzioni. Selezionare il riquadro **Funzioni**. Nella parte superiore della pagina selezionare **+ Aggiungi**. Viene visualizzato il pannello del modello di funzione. Scorrere il pannello verso il basso e selezionare **Trigger Griglia di eventi di Azure**.

     >[!IMPORTANT]
    > I modelli **Trigger hub di eventi di Azure** e **Trigger Griglia di eventi di Azure** hanno nomi simili. Assicurarsi di selezionare il modello **Trigger Griglia di eventi di Azure**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Diagramma della panoramica del sistema.":::

1. Assegnare un nome alla funzione. In questa esercitazione verrà usato il nome *GetGeoFunction*, ma in generale è possibile usare un nome qualsiasi. Selezionare **Crea funzione**.

1. Nel menu a sinistra selezionare il riquadro **Codice e test**. Copiare e incollare lo [script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) nella finestra del codice.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Diagramma della panoramica del sistema.":::

1. Nel codice C# sostituire i parametri seguenti:
    * Sostituire il valore **SUBSCRIPTION_KEY** con la chiave di sottoscrizione primaria dell'account Mappe di Azure.
    * Sostituire **UDID** con il valore `udid` del recinto virtuale caricato in [Caricare un recinto virtuale](#upload-a-geofence).
    * La funzione `CreateBlobAsync` nello script crea un BLOB per ogni evento nell'account di archiviazione dati. Sostituire **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** con la chiave di accesso dell'account di archiviazione, il nome dell'account e il contenitore di archiviazione dati. Questi valori sono stati generati quando è stato creato l'account di archiviazione in [Creare un account di archiviazione di Azure](#create-an-azure-storage-account).

1. Nel menu a sinistra selezionare il riquadro **Integrazione**. Selezionare **Trigger Griglia di eventi** nel diagramma. Digitare un nome per il trigger, ad esempio *eventGridEvent*, quindi selezionare **Crea sottoscrizione di Griglia di eventi**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Diagramma della panoramica del sistema.":::

1. Immettere i dettagli della sottoscrizione. Assegnare un nome alla sottoscrizione dell'evento. Selezionare **Schema griglia di eventi** per **Schema evento**. Per **Tipi di argomento** selezionare **Account hub IoT di Azure**. Per **Gruppo di risorse** selezionare il gruppo di risorse creato all'inizio di questa esercitazione. Per **Risorsa** selezionare l'hub IoT creato nella sezione "Creare un hub IoT di Azure". Per **Filtra per tipo di evento** selezionare **Telemetria dei dispositivi**.

   Dopo aver scelto queste opzioni, l'impostazione di **Tipo di argomento** verrà modificata automaticamente in **Hub IoT**. Per **Nome dell'argomento del sistema** è possibile usare lo stesso nome della risorsa. Infine, nella sezione **Dettagli endpoint** selezionare **Seleziona endpoint**. Accettare tutte le impostazioni e selezionare **Conferma selezione**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Diagramma della panoramica del sistema.":::

1. Rivedere le impostazioni. Verificare che l'endpoint specifichi la funzione creata all'inizio di questa sezione. Selezionare **Create** (Crea).

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Diagramma della panoramica del sistema.":::

1. A questo punto si è di nuovo nel pannello **Modifica trigger**. Selezionare **Salva**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrare gli eventi usando il routing dei messaggi dell'hub IoT

Quando si aggiunge una sottoscrizione di Griglia di eventi alla funzione di Azure, viene creata automaticamente una route di messaggistica nell'hub IoT specificato. Il routing dei messaggi consente di instradare tipi di dati diversi a vari endpoint. Ad esempio, è possibile eseguire il routing dei messaggi di telemetria del dispositivo, degli eventi del ciclo di vita del dispositivo e degli eventi di modifica del dispositivo gemello. Per altre informazioni, vedere [Usare il routing di messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Diagramma della panoramica del sistema.":::

Nello scenario di esempio si vogliono ricevere messaggi solo quando l'auto a noleggio è in viaggio. Creare una query di routing per filtrare gli eventi in cui la proprietà `Engine` è uguale a **"ON"** . Per creare una query di routing, fare clic sulla route **RouteToEventGrid** e sostituire la **Query di routing** con **"Engine ='ON'"** . Selezionare quindi **Salva**. A questo punto, l'hub IoT pubblica solo i dati di telemetria del dispositivo in cui il motore è in funzione.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Diagramma della panoramica del sistema.":::

>[!TIP]
>Esistono vari modi per eseguire query sui messaggi tra il dispositivo IoT e il cloud. Per altre informazioni sulla sintassi del routing dei messaggi, vedere [Routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Inviare dati di telemetria all'hub IoT

Quando la funzione di Azure è in esecuzione, è possibile inviare dati di telemetria all'hub IoT, che li instraderà a Griglia di eventi. Usare un'applicazione C# per simulare i dati di posizione per un dispositivo interno a un'auto a noleggio. Per eseguire l'applicazione, è necessario che sia presente .NET Core SDK 2.1.0 o versione successiva nel computer di sviluppo. Seguire questa procedura per inviare i dati di telemetria simulati all'hub IoT:

1. Se non è già stato fatto, scaricare il progetto C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

2. Aprire il file `simulatedCar.cs` in un editor di testo a scelta e sostituire il valore di `connectionString` con quello salvato durante la registrazione del dispositivo. Salvare le modifiche apportate al file.

3. Verificare che .NET Core SDK sia installato nel computer. Nella finestra del terminale locale passare alla cartella radice del progetto C# ed eseguire i comandi seguenti per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

4. Nello stesso terminale eseguire il comando seguente per compilare ed eseguire l'applicazione di simulazione dell'auto a noleggio:

    ```cmd/sh
    dotnet run
    ```


  Il terminale locale dovrebbe essere simile a quello riportato di seguito.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Diagramma della panoramica del sistema.":::

Se si apre ora il contenitore di archiviazione BLOB, è possibile visualizzare quattro BLOB per le posizioni in cui il veicolo si trovava al di fuori del recinto virtuale.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Diagramma della panoramica del sistema.":::

La mappa seguente mostra quattro punti di posizione del veicolo al di fuori del recinto virtuale. Ogni posizione è stata registrata a intervalli di tempo regolari.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Diagramma della panoramica del sistema.":::

## <a name="explore-azure-maps-and-iot"></a>Esplorare Mappe di Azure e IoT

Per esplorare le API di Mappe di Azure usate in questa esercitazione, vedere:

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Per un elenco completo delle API REST di Mappe di Azure, vedere:

* [API REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

* [Plug and Play IoT](https://docs.microsoft.com/azure/iot-pnp)

Per ottenere un elenco di dispositivi Microsoft Azure Certified per IoT, visitare:

* [Dispositivi Azure Certified](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come inviare i dati di telemetria da dispositivo a cloud e viceversa, vedere:


> [!div class="nextstepaction"]
> [Inviare dati di telemetria da un dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)