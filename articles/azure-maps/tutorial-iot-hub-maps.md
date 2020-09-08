---
title: "Esercitazione: Implementare l'analisi spaziale di IoT con Mappe di Microsoft Azure"
description: Integrare l'hub IoT con le API del servizio Mappe di Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4150464b5c59b631afea0c788b1e351dee5185f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299417"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Esercitazione: Implementare l'analisi spaziale di IoT con Mappe di Azure

Il rilevamento e l'acquisizione di eventi pertinenti che si verificano nello spazio e nel tempo sono uno scenario comune di IoT, ad esempio per gestione della flotta, rintracciamento asset, mobilità e applicazioni per città intelligenti. Questa esercitazione illustra una soluzione che tiene traccia degli spostamenti di auto a noleggio usando le API di Mappe di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione di Azure per registrare i dati di monitoraggio delle auto.
> * Caricare un recinto virtuale nel servizio dati di Mappe di Azure usando l'API Caricamento dati.
> * Creare un hub IoT e registrare un dispositivo.
> * Creare una funzione in Funzioni di Azure, implementando la logica di business basata sull'analisi spaziale di Mappe di Azure.
> * Sottoscrivere gli eventi di telemetria del dispositivo IoT dalla funzione di Azure tramite la Griglia di eventi.
> * Filtrare gli eventi di telemetria usando il routing dei messaggi dell'hub IoT.

## <a name="prerequisites"></a>Prerequisiti

1. Accedere al [portale di Azure](https://portal.azure.com).

2. [Creare un account di Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md).

4. [Creare un gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). In questa esercitazione si assegnerà il nome *ContosoRental* al gruppo di risorse, ma è possibile scegliere qualsiasi nome.

5. Scaricare il [progetto C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="use-case-rental-car-tracking"></a>Caso d'uso: monitoraggio delle auto a noleggio

Questa esercitazione illustra lo scenario seguente: Una società di noleggio auto vuole registrare le informazioni sulla posizione, la distanza percorsa e lo stato corrente delle auto a noleggio. La società desidera anche archiviare queste informazioni ogni volta che un'auto esce dall'area geografica autorizzata corretta.

In questo caso d'uso, le auto a noleggio sono dotate di dispositivi IoT che inviano dati di telemetria all'hub IoT a intervalli regolari. La telemetria include la posizione corrente e indica se il motore dell'automobile è in funzione o meno. Lo schema della posizione del dispositivo è conforme allo [schema Plug and Play per i dati geospaziali](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Lo schema di telemetria del dispositivo dell'auto a noleggio è simile al codice JSON seguente:

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

In questa esercitazione verrà monitorato solo un veicolo. Dopo aver configurato i servizi di Azure sarà necessario scaricare il [progetto C# rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) per eseguire il simulatore di veicoli. L'intero processo, dall'evento all'esecuzione della funzione, viene riepilogato nei passaggi seguenti:

1. Il dispositivo nel veicolo invia i dati di telemetria all'hub IoT.

2. Se il motore dell'auto è in funzione, l'hub IoT pubblica i dati di telemetria in Griglia di eventi.

3. Viene attivata una funzione di Azure in seguito alla sottoscrizione degli eventi di telemetria del dispositivo.

4. La funzione registrerà le coordinate della posizione del dispositivo del veicolo, l'ora dell'evento e l'ID del dispositivo. Userà quindi l'[API spaziale Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) per determinare se l'auto è uscita dal recinto virtuale. Se si trova al di fuori dei confini del recinto virtuale, la funzione archivia i dati sulla posizione ricevuti dall'evento nel contenitore BLOB. Inoltre, la funzione esegue una query di [ricerca di indirizzi inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per tradurre le coordinate della posizione in un indirizzo e archiviarlo con il resto dei dati sulla posizione del dispositivo.

Il diagramma seguente offre una panoramica di alto livello del sistema.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Panoramica del sistema":::

Nella figura seguente l'area del recinto virtuale è evidenziata in blu. L'itinerario dell'auto a noleggio è indicato da una linea verde.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Percorso recinto virtuale":::

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Per archiviare i dati di monitoraggio delle violazioni delle auto, verrà creato un [account di archiviazione per utilizzo generico v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) nel gruppo di risorse. Se non è stato creato un gruppo di risorse, seguire le istruzioni riportate in [Creare un gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). In questa esercitazione al gruppo di risorse verrà assegnato il nome *ContosoRental*.

Per creare un account di archiviazione, seguire le istruzioni in [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). In questa esercitazione verrà usato il nome *contosorentalstorage* per l'account di archiviazione, ma è possibile assegnare un nome qualsiasi.

Dopo aver creato l'account di archiviazione, sarà necessario creare un contenitore per archiviare i dati di registrazione.

1. Tornare all'account di archiviazione appena creato. Fare clic sul collegamento **Contenitori** nella sezione Informazioni di base.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Contenitori per l'archiviazione BLOB":::

2. Fare clic sul pulsante **+Contenitore** nell'angolo superiore sinistro. Verrà visualizzato un pannello sul lato destro del browser. Assegnare il nome *contoso-rental-logs* al contenitore e fare clic su **Crea**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Creare un contenitore BLOB":::

3. Passare al pannello **Chiavi di accesso** nell'account di archiviazione e copiare i valori per **Nome account di archiviazione** e **Chiave** nella sezione **key1**. Entrambi i valori verranno usati nella sezione [Creare una funzione di Azure e aggiungere una sottoscrizione di Griglia di eventi](#create-an-azure-function-and-add-an-event-grid-subscription).

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Copiare il nome e la chiave dell'account di archiviazione":::

## <a name="upload-a-geofence"></a>Caricare un recinto virtuale

A questo punto si userà l'[app Postman](https://www.getpostman.com) per [caricare il recinto virtuale](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) nel servizio Mappe di Azure. Il recinto virtuale definisce l'area geografica autorizzata per il veicolo a noleggio.  Per determinare se un'auto è uscita dall'area del recinto virtuale, verrà usato il recinto virtuale nella funzione di Azure.

Aprire l'app Postman e seguire questa procedura per caricare il recinto virtuale usando l'API Caricamento dati di Mappe di Azure.  

1. Aprire l'app Postman. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta).  Assegnare un nome alla raccolta e selezionare **Create** (Crea).

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo HTTP **POST** nella scheda del generatore e immettere l'URL seguente per caricare il recinto virtuale nell'API Caricamento dati. Assicurarsi di sostituire `{subscription-key}` con la chiave di sottoscrizione primaria.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Il valore "geojson" del parametro `dataFormat` nel percorso URL rappresenta il formato dei dati caricati.

4. Fare clic su **Body** (Corpo), quindi selezionare il formato di input **raw** (non elaborato) e scegliere **JSON** come formato di input nell'elenco a discesa. Aprire il file di dati JSON [qui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copiare il codice JSON nella sezione del corpo. Fare clic su **Send**.

5. Fare clic sul pulsante blu **Send** (Invia) e attendere l'elaborazione della richiesta. Al completamento della richiesta, passare alla scheda di risposta **Headers** (Intestazioni). Copiare il valore della chiave **Location** (Posizione), ovvero lo `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Per controllare lo stato della chiamata API, creare una richiesta HTTP **GET** sullo `status URL`. È necessario accodare la chiave di sottoscrizione primaria all'URL per l'autenticazione. La richiesta **GET** dovrebbe essere simile all'URL seguente:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Creare un hub IoT di Azure

L'hub IoT di Azure consente la comunicazione bidirezionale sicura e affidabile tra un'applicazione IoT e i dispositivi gestiti.  In questo scenario si vogliono ottenere informazioni dal dispositivo nel veicolo per determinare la posizione dell'auto a noleggio. In questa sezione verrà creato un hub IoT all'interno del gruppo di risorse *ContosoRental*. L'hub IoT sarà responsabile della pubblicazione degli eventi di telemetria del dispositivo.

> [!NOTE]
> La funzionalità dell'hub IoT per la pubblicazione di eventi di telemetria del dispositivo in Griglia di eventi è in anteprima pubblica. Le funzionalità di anteprima pubblica sono disponibili in tutte le aree, ad eccezione degli **Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale, Azure per enti pubblici, Azure Cina 21ViaNet** e **Azure Germania**.

Per creare un hub IoT nel gruppo di risorse *ContosoRental*, seguire la procedura in [Creare un hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Registrare un dispositivo nell'hub IoT

I dispositivi non possono connettersi all'hub IoT, a meno che non siano registrati nel registro delle identità dell'hub IoT. In questo scenario verrà creato un singolo dispositivo con il nome *InVehicleDevice*. Per creare e registrare il dispositivo nell'hub IoT, seguire la procedura descritta in [Registrare un nuovo dispositivo nell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Assicurarsi di copiare la **stringa di connessione primaria** del dispositivo, perché verrà usata in un passaggio successivo.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Creare una funzione di Azure e aggiungere una sottoscrizione di Griglia di eventi

Funzioni di Azure è un servizio di calcolo serverless che consente di eseguire piccole parti di codice ("funzioni") senza dover gestire l'infrastruttura di calcolo o effettuare il provisioning in modo esplicito. Per altre informazioni su Funzioni di Azure, vedere la documentazione di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Una funzione viene "attivata" da un determinato evento. In questo scenario verrà creata una funzione attivata da un trigger di Griglia di eventi. Si crea la relazione tra il trigger e la funzione creando una sottoscrizione per gli eventi di telemetria del dispositivo dell'hub IoT. Quando si verifica un evento di telemetria del dispositivo, la funzione verrà chiamata come endpoint e riceverà i dati rilevanti per il [dispositivo registrato in precedenza nell'hub IoT](#register-a-device-in-iot-hub).

Il codice di script C# contenuto nella funzione può essere visualizzato [qui](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Verrà ora configurata la funzione di Azure.

1. Nel dashboard del portale di Azure fare clic su **Crea una risorsa**. Digitare **App per le funzioni** nella casella di testo di ricerca. Fare clic su **App per le funzioni**. Scegliere **Crea**.

2. Nella pagina di creazione dell'**App per le funzioni** assegnare un nome all'app per le funzioni. In **Gruppo di risorse** selezionare *ContosoRental* nell'elenco a discesa.  Selezionare *.NET Core* come **Stack di runtime**. Fare clic su **Avanti: Hosting >** nella parte inferiore della pagina.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Creare un'app per le funzioni":::

3. Per **Account di archiviazione** selezionare l'account di archiviazione creato in [Creare un account di archiviazione di Azure](#create-an-azure-storage-account). Fare clic su **Rivedi e crea**.

4. Verificare i dettagli dell'app per le funzioni e fare clic su **Crea**.

5. Dopo aver creato l'app verrà aggiunta una funzione. Passare all'app per le funzioni. Fare clic nel riquadro **Funzioni**. Fare clic su **+Aggiungi** nella parte superiore della pagina. Verrà visualizzato il pannello del modello di funzione. Scorrere verso il basso nel pannello. Fare clic su **Trigger Griglia di eventi di Azure**.

     >[!WARNING]
    > I modelli **Trigger hub di eventi di Azure** e **Trigger Griglia di eventi di Azure** hanno nomi simili. Assicurarsi di fare clic sul modello **Trigger Griglia di eventi di Azure**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Creare una funzione":::

6. Assegnare un nome alla funzione. In questa esercitazione verrà usato il nome *GetGeoFunction*, ma è possibile usare qualsiasi nome. Fare clic su **Crea funzione**.

7. Fare clic sul riquadro **Codice e test** nel menu a sinistra. Copiare e incollare lo [script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) nella finestra del codice.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Copiare/incollare il codice nella finestra della funzione":::

8. Nello script C# sostituire i parametri seguenti. Fare clic su **Save**. Non fare ancora clic su **Test/Esegui**.
    * Sostituire il valore **SUBSCRIPTION_KEY** con la chiave di sottoscrizione primaria dell'account Mappe di Azure.
    * Sostituire **UDID** con il valore `udid` del recinto virtuale caricato in [Caricare un recinto virtuale](#upload-a-geofence).
    * La funzione **CreateBlobAsync** nello script crea un BLOB per ogni evento nell'account di archiviazione dati. Sostituire **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** con la chiave di accesso dell'account di archiviazione, il nome dell'account e il contenitore di archiviazione dati. Questi valori sono stati generati quando è stato creato l'account di archiviazione in [Creare un account di archiviazione di Azure](#create-an-azure-storage-account).

9. Fare clic sul riquadro **Integrazione** nel menu a sinistra. Fare clic su **Trigger Griglia di eventi** nel diagramma. Digitare un nome per il trigger, ad esempio *eventCarTelemetry*, e fare clic su **Crea sottoscrizione di Griglia di eventi**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Aggiungere una sottoscrizione di eventi":::

10. Immettere i dettagli della sottoscrizione. Assegnare un nome alla sottoscrizione dell'evento. Selezionare *Schema griglia di eventi* per *Schema evento*. Per **Tipi di argomento** selezionare *Account hub IoT di Azure*. Per **Gruppo di risorse** selezionare il gruppo di risorse creato all'inizio di questa esercitazione. Per **Risorsa** selezionare l'hub IoT creato in [Creare un hub IoT di Azure](#create-an-azure-iot-hub). Per **Filtra per tipo di evento** selezionare *Telemetria dei dispositivi*. Dopo aver scelto queste opzioni, l'impostazione di **Tipo di argomento** verrà modificata automaticamente in *Hub IoT*. Per **Nome dell'argomento del sistema** è possibile usare lo stesso nome della risorsa.  Infine, fare clic su **Seleziona endpoint** nella sezione **Dettagli endpoint**. Accettare tutte le impostazioni e fare clic su **Conferma selezione**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Crea sottoscrizione di eventi":::

11. Rivedere le impostazioni. Verificare che l'endpoint specifichi la funzione creata all'inizio di questa sezione. Scegliere **Crea**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Creare la conferma della sottoscrizione dell'evento":::

12. A questo punto si è di nuovo nel pannello **Modifica trigger**. Fare clic su **Save**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrare gli eventi usando il routing dei messaggi dell'hub IoT

Quando si aggiunge una sottoscrizione di Griglia di eventi alla funzione di Azure, viene creata automaticamente una route di messaggistica nell'hub IoT specificato. Il routing dei messaggi consente di instradare tipi di dati diversi a vari endpoint. Ad esempio, è possibile eseguire il routing dei messaggi di telemetria del dispositivo, degli eventi del ciclo di vita del dispositivo e degli eventi di modifica del dispositivo gemello. Per altre informazioni sul routing dei messaggi dell'hub IoT, vedere [Usare il routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Routing dei messaggi nell'hub IoT":::

Nello scenario di esempio si vogliono ricevere messaggi solo quando l'auto a noleggio è in viaggio. Verrà creata una query di routing per filtrare gli eventi in cui la proprietà `Engine` è uguale a **"ON"** . Per creare una query di routing, fare clic sulla route **RouteToEventGrid** e sostituire la **Query di routing** con **"Engine ='ON'"** e fare clic su **Salva**. A questo punto, l'hub IoT pubblica solo i dati di telemetria del dispositivo in cui il motore è in funzione.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtrare i messaggi di routing":::

>[!TIP]
>Esistono diversi modi per eseguire query sui messaggi da dispositivo IoT a cloud; per altre informazioni sulla sintassi di routing dei messaggi, vedere [Routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Inviare dati di telemetria all'hub IoT

Quando la funzione di Azure è operativa, i dati di telemetria possono essere inviati all'hub IoT, che li instraderà a Griglia di eventi. Si userà un'applicazione C# per simulare i dati di posizione per un dispositivo interno al veicolo di un'auto a noleggio. Per eseguire l'applicazione, è necessario .NET Core SDK 2.1.0 o versione successiva nel computer di sviluppo. Seguire questa procedura per inviare i dati di telemetria simulati all'hub IoT.

1. Se non è già stato fatto, scaricare il progetto C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

2. Aprire il file simulatedCar.cs in un editor di testo di propria scelta e sostituire il valore di `connectionString` con quello salvato durante la registrazione del dispositivo, quindi salvare le modifiche apportate al file.

3. Verificare che .NET Core SDK sia installato nel computer. Nella finestra del terminale locale, passare alla cartella radice del progetto C# ed eseguire i comandi seguenti per installare i pacchetti necessari per l'applicazione del dispositivo simulato:

    ```cmd/sh
    dotnet restore
    ```

4. Nello stesso terminale eseguire il comando seguente per compilare ed eseguire l'applicazione di simulazione dell'auto a noleggio:

    ```cmd/sh
    dotnet run
    ```

  Il terminale locale dovrebbe essere simile a quello riportato di seguito.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Output del terminale":::

Se si apre ora il contenitore di archiviazione BLOB, è possibile visualizzare quattro BLOB per le posizioni in cui il veicolo si trovava al di fuori del recinto virtuale.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Visualizzare i di BLOB all'interno del contenitore":::

La mappa seguente mostra quattro punti di posizione del veicolo al di fuori del recinto virtuale. Ogni posizione è stata registrata a intervalli di tempo regolari.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Mappa delle violazioni":::

## <a name="explore-azure-maps-and-iot"></a>Esplorare Mappe di Azure e IoT

Per esplorare le API di Mappe di Azure usate in questa esercitazione, vedere:

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Per un elenco completo delle API REST di Mappe di Azure, vedere:

* [API REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Per altre informazioni su Plug and Play IoT, vedere:

* [Plug and Play IoT](https://docs.microsoft.com/azure/iot-pnp)

Per ottenere un elenco di dispositivi Microsoft Azure Certified per IoT, visitare:

* [Dispositivi Azure Certified](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come inviare i dati di telemetria da dispositivo a cloud e viceversa, vedere:

> [!div class="nextstepaction"]
> [Inviare dati di telemetria da un dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
