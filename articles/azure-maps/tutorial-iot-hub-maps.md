---
title: "Esercitazione: Implementare l'analisi spaziale di IoT | Mappe di Microsoft Azure"
description: Integrare l'hub IoT con le API del servizio Mappe di Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cf1d732391f86bec9c0ec2de1e6bace2e808bb19
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318927"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Esercitazione: Implementare l'analisi spaziale di IoT con Mappe di Azure

Il rilevamento e l'acquisizione di eventi pertinenti che si verificano nello spazio e nel tempo sono uno scenario comune di IoT, ad esempio per gestione della flotta, rintracciamento asset, mobilità e applicazioni per città intelligenti. Questa esercitazione illustra un modello di soluzione per l'uso delle API di Mappe di Azure. Gli eventi pertinenti vengono acquisiti dall'hub IoT, usando il modello di sottoscrizione degli eventi fornito da Griglia di eventi.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un hub IoT.
> * Caricare l'area del recinto virtuale nel servizio dati di Mappe di Azure usando l'API Caricamento dati.
> * Creare una funzione in Funzioni di Azure, implementando la logica di business basata sull'analisi spaziale di Mappe di Azure.
> * Sottoscrivere gli eventi di telemetria del dispositivo IoT dalla funzione di Azure tramite la Griglia di eventi.
> * Filtrare gli eventi di telemetria usando il routing dei messaggi dell'hub IoT.
> * Creare un account di archiviazione per archiviare i dati dell'evento pertinente.
> * Simulare un dispositivo IoT nel veicolo.
    

## <a name="use-case"></a>Caso d'uso

La soluzione mostra uno scenario in cui una società di autonoleggio prevede di monitorare e registrare gli eventi per le automobili noleggiate. Le società di autonoleggio in genere noleggiano le automobili in un'area geografica specifica e devono tenere traccia degli spostamenti dei veicoli. Le istanze di un'automobile che esce dall'area geografica scelta devono essere registrate. La registrazione dei dati garantisce che le norme, le tariffe e altri aspetti aziendali vengano gestiti correttamente.

In questo caso d'uso, le auto a noleggio sono dotate di dispositivi IoT che inviano dati di telemetria all'hub IoT a intervalli regolari. La telemetria include la posizione corrente e indica se il motore dell'automobile è in funzione o meno. Lo schema della posizione del dispositivo è conforme allo [schema Plug and Play per i dati geospaziali](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Lo schema di telemetria del dispositivo dell'auto a noleggio è simile al seguente:

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

Verranno usati i dati di telemetria dei dispositivi nel veicolo per raggiungere l'obiettivo e quindi applicare le regole di geofencing e infine rispondere ogni volta che viene ricevuto un evento che indica che l'auto è stata spostata. A tale scopo, si procederà alla sottoscrizione degli eventi di telemetria del dispositivo dall'hub IoT tramite Griglia di eventi. 

Esistono diversi modi per effettuare la sottoscrizione di Griglia di eventi e in questa esercitazione si userà il servizio Funzioni di Azure. Funzioni di Azure reagisce agli eventi pubblicati in Griglia di eventi. Implementa inoltre la logica di business degli autonoleggi, basata sull'analisi spaziale di Mappe di Azure. 

Il codice all'interno della funzione di Azure controlla se il veicolo ha lasciato il recinto virtuale. Se il veicolo ha lasciato il recinto virtuale la funzione di Azure raccoglie informazioni aggiuntive, ad esempio l'indirizzo associato alla posizione corrente. La funzione implementa anche la logica per archiviare i dati degli eventi significativi in un archivio BLOB di dati che consente di fornire una descrizione delle circostanze dell'evento. 

Le circostanze dell'evento possono essere utili per la società di autonoleggio e per il cliente dell'autonoleggio. Il diagramma seguente offre una panoramica di alto livello del sistema.

 
  <center>

  ![Panoramica del sistema](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

La figura seguente rappresenta l'area del recinto virtuale evidenziata in blu. Il percorso del veicolo a noleggio è indicato da una linea verde.

  ![Percorso recinto virtuale](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Prerequisiti 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per completare i passaggi di questa esercitazione, è necessario prima creare un gruppo di risorse nel portale di Azure. Per creare un gruppo di risorse, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Gruppi di risorse**.
    
   ![Gruppi di risorse](./media/tutorial-iot-hub-maps/resource-group.png)

3. In **Gruppi di risorse** selezionare **Aggiungi**.
    
   ![Aggiungere il gruppo di risorse](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Immettere i valori di proprietà seguenti:
    * **Sottoscrizione:** Selezionare la sottoscrizione di Azure.
    * **Gruppo di risorse:** immettere "ContosoRental" come nome del gruppo di risorse.
    * **Area:** selezionare un'area per il gruppo di risorse.  

    ![Dettagli del gruppo di risorse](./media/tutorial-iot-hub-maps/resource-details.png)

    Selezionare **Rivedi e crea** e quindi **Crea** nella pagina successiva.

### <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure 

Per implementare la logica di business basata sull'analisi spaziale di Mappe di Azure è necessario creare un account Mappe di Azure nel gruppo di risorse creato. Creare una sottoscrizione dell'account Mappe di Azure nel piano tariffario S1 seguendo le istruzioni riportate in [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps). Per ottenere la chiave primaria per l'account, seguire la procedura illustrata in [Ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per registrare i dati degli eventi, verrà creato un account di archiviazione **v2storage** per utilizzo generico che consente l'accesso a tutti i servizi di Archiviazione di Azure: BLOB, file, code, tabelle e dischi.  Per archiviare i dati come BLOB, è necessario inserire questo account di archiviazione nel gruppo di risorse "ContosoRental". Per creare un account di archiviazione, seguire le istruzioni in [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Successivamente, sarà necessario creare un contenitore per archiviare i BLOB. A tale scopo, seguire questa procedura:

1. Nell'account di archiviazione passare a Contenitori.

    ![blob](./media/tutorial-iot-hub-maps/blobs.png)

2. Fare clic sul pulsante contenitore in alto a sinistra e denominare il contenitore "contoso-rental-logs", quindi fare clic su "OK".

    ![contenitore BLOB](./media/tutorial-iot-hub-maps/blob-container.png)

3. Passare al pannello **Chiavi di accesso** nell'account di archiviazione e copiare il nome dell'account e la chiave di accesso, che saranno necessari in un passaggio successivo.

    ![chiavi di accesso](./media/tutorial-iot-hub-maps/access-keys.png)


A questo punto, sono stati creati un account di archiviazione e un contenitore per la registrazione dei dati degli eventi. Successivamente, verrà creato un hub IoT.

### <a name="create-an-iot-hub"></a>Creare un hub IoT

L'hub IoT è un servizio gestito nel cloud. Un hub IoT funge da hub messaggi centralizzato per la comunicazione bidirezionale tra l'applicazione IoT e i dispositivi gestiti dall'applicazione. Per instradare i messaggi di telemetria del dispositivo a una Griglia di eventi, creare un hub IoT all'interno del gruppo di risorse "ContosoRental". Configurare l'integrazione di una route di messaggi in cui i messaggi verranno filtrati in base allo stato del motore dell'automobile. I messaggi di telemetria del dispositivo verranno inviati a Griglia di eventi anche ogni volta che l'auto è in movimento.

> [!Note] 
> La funzionalità dell'hub IoT per la pubblicazione di eventi di telemetria del dispositivo nella Griglia di eventi è in anteprima pubblica. Le funzionalità di anteprima pubblica sono disponibili in tutte le aree, ad eccezione degli **Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale, Azure per enti pubblici, Azure Cina 21ViaNet** e **Azure Germania**. 

Per creare un hub IoT, seguire la procedura descritta nella sezione [Creare un hub IoT](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registrare un dispositivo 

Per connettersi all'hub IoT, è necessario registrare un dispositivo. Per registrare un dispositivo con l'hub IoT, seguire questa procedura:

1. Nell'hub IoT, fare clic sul pannello "Dispositivi IoT" e quindi su "Nuovo".

    ![aggiungere dispositivo](./media/tutorial-iot-hub-maps/add-device.png)

2. Nella pagina Crea un dispositivo assegnare un nome al dispositivo IoT e fare clic su "Salva".
    
    ![registrare dispositivo](./media/tutorial-iot-hub-maps/register-device.png)

3. Salvare la **stringa di connessione primaria** del dispositivo per usarla in un passaggio successivo in sostituzione di un segnaposto.

    ![aggiungere dispositivo](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Caricare il recinto virtuale

Per [caricare il recinto virtuale](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) nel servizio Mappe di Azure con l'API Caricamento dati di Mappe di Azure si userà l'[applicazione Postman](https://www.getpostman.com). Verrà registrato ogni evento in cui l'auto si trova al di fuori di questo recinto.

Aprire l'app Postman e seguire questi passaggi per caricare il recinto virtuale usando Mappe di Azure e l'API Caricamento dati.  

1. Nell'app Postman, fare clic su New (Nuovo) | Create New (Crea nuovo) e selezionare Request (Richiesta). Immettere un nome richiesta per Upload geofence data (Carica dati recinto virtuale), selezionare una raccolta o una cartella per il salvataggio, quindi fare clic su Salva (Save).

    ![Caricare i recinti virtuali usando Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selezionare il metodo HTTP POST nella scheda Builder (Generatore) e immettere l'URL seguente per effettuare una richiesta POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Il valore "geojson" del parametro `dataFormat` nel percorso URL rappresenta il formato dei dati caricati.

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare per l'URL della richiesta POST. Sostituire il valore di subscription-key con la chiave di Mappe di Azure.
   
    ![Parametri chiave-valore di Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Fare clic su **Body** (Corpo), quindi selezionare il formato di input **raw** (non elaborato) e scegliere **JSON (application/text)** come formato di input nell'elenco a discesa. Aprire il file di dati JSON [qui](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copiare il codice JSON nella sezione Body come dati da caricare e quindi fare clic su **Send**.
    
    ![pubblicare dati](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Esaminare le intestazioni della risposta. Una volta completata la richiesta, l'intestazione **Location** conterrà l'URI di stato per controllare lo stato corrente della richiesta di caricamento. L'URI di stato avrà il formato seguente. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copiare l'URI dello stato e aggiungervi un parametro `subscription-key`. Assegnare il valore della chiave di sottoscrizione dell'account Mappe di Azure al parametro `subscription-key`. Il formato dell'URI di stato deve essere simile a quello riportato di seguito e `{Subscription-key}` deve essere sostituito con la chiave di sottoscrizione.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Per ottenere l'`udId`, aprire una nuova scheda nell'app Postman e selezionare il metodo GET HTTP nella scheda Builder (Generatore) ed effettuare una richiesta GET nell'URI di stato. Se il caricamento dei dati è riuscito, si riceverà un udId nel corpo della risposta. Copiare l'udId per un uso successivo.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Successivamente, verrà creata una funzione di Azure nel gruppo di risorse "ContosoRental" e quindi verrà configurata una route di messaggi nell'hub IoT per filtrare i messaggi di telemetria del dispositivo.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Creare una funzione di Azure e aggiungere una sottoscrizione di Griglia di eventi

Funzioni di Azure è un servizio di calcolo serverless che consente di eseguire codice su richiesta senza dover gestire l'infrastruttura di calcolo o effettuare il provisioning in modo esplicito. Per altre informazioni su Funzioni di Azure, vedere la documentazione sulle [funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

La logica implementata nella funzione usa i dati di posizione provenienti dai dati di telemetria del dispositivo nel veicolo per la valutazione dello stato del recinto virtuale. Quando un veicolo specifico si sposta all'esterno del recinto virtuale, la funzione raccoglie altre informazioni, ad esempio l'indirizzo della posizione, tramite l'[API di ricerca di indirizzi inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Questa API converte una coordinata di posizione specificata in una via e numero civico. 

Tutte le informazioni dell'evento pertinenti vengono quindi conservate nell'archivio BLOB. Il passaggio 5 seguente indica il codice eseguibile che implementa tale logica. Seguire questa procedura per creare una funzione di Azure che invia i log di dati al contenitore BLOB nell'account di archiviazione BLOB e vi aggiunge una sottoscrizione di Griglia di eventi.

1. Nel dashboard del portale di Azure selezionare Crea una risorsa. Selezionare **Calcolo** nell'elenco dei tipi di risorse disponibili e quindi selezionare **App per le funzioni**.

    ![creare risorsa](./media/tutorial-iot-hub-maps/create-resource.png)

2. Nella pagina di creazione dell'**App per le funzioni** assegnare un nome all'app per le funzioni. In **Gruppo di risorse** selezionare **Usa esistente** e selezionare "ContosoRental" nell'elenco a discesa. Selezionare ".NET Core" come stack di runtime. In **Hosting**, per **Account di archiviazione**, selezionare il nome dell'account di archiviazione di un passaggio precedente. Nel passaggio precedente l'account di archiviazione è stato denominato **v2storage**.  Quindi, selezionare **Rivedi e crea**.
    
    ![creare app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Esaminare i dettagli dell'app per le funzioni e selezionare "Crea".

3. Una volta creata l'app, è necessario aggiungervi una funzione. Passare all'app per le funzioni. Per aggiungere una funzione, fare clic su **Nuova funzione** e scegliere **Nel portale** come ambiente di sviluppo. Selezionare quindi **Continua**.

    ![creare funzione](./media/tutorial-iot-hub-maps/function.png)

4. Scegliere **Altri modelli** e fare clic su **Termina e visualizza i modelli**. 

5. Selezionare il modello con un **trigger Griglia di eventi di Azure**. Installare le estensioni se richiesto, assegnare un nome alla funzione e fare clic su **Crea**.

    ![modello di funzione](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    Le opzioni **Trigger hub di eventi di Azure** e **Trigger griglia di eventi di Azure** hanno icone simili. Assicurarsi di selezionare **Trigger griglia di eventi di Azure**.

6. Copiare il [codice C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) nella funzione.
 
7. Nello script C# sostituire i parametri seguenti. Fare clic su **Salva**. Non fare ancora clic su **Esegui**
    * Sostituire il valore **SUBSCRIPTION-KEY** con la chiave di sottoscrizione primaria dell'account Mappe di Azure.
    * Sostituire l'**UDID** con l'udid del recinto virtuale caricato. 
    * La funzione **CreateBlobAsync** nello script crea un BLOB per ogni evento nell'account di archiviazione dati. Sostituire **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** con la chiave di accesso dell'account di archiviazione, il nome dell'account e il contenitore di archiviazione dati.

10. Fare clic su **Aggiungi sottoscrizione di Griglia di eventi**.
    
    ![aggiungere griglia di eventi](./media/tutorial-iot-hub-maps/add-egs.png)

11. Immettere i dettagli della sottoscrizione e in **DETTAGLI SOTTOSCRIZIONE EVENTO** assegnare un nome alla sottoscrizione di venti. Per Schema evento scegliere "Schema griglia di eventi". In **DETTAGLI ARGOMENTO** selezionare "account hub IoT di Azure" come tipo di argomento. Scegliere la stessa sottoscrizione usata per la creazione del gruppo di risorse, selezionare "ContosoRental" come "Gruppo di risorse" e scegliere l'hub IoT creato come "Risorsa". Selezionare **Telemetria dei dispositivi** come tipo di evento. Dopo aver scelto queste opzioni, il "Tipo di argomento" verrà modificato automaticamente in "Hub IoT".

    ![sottoscrizione griglia di eventi](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrare gli eventi usando il routing dei messaggi dell'hub IoT

Dopo aver aggiunto una sottoscrizione di Griglia di eventi alla funzione di Azure, sarà possibile visualizzare una route di messaggi predefinita a Griglia di eventi nel pannello **Routing messaggi** dell'hub IoT. Il routing dei messaggi consente di instradare tipi di dati diversi a vari endpoint. Ad esempio, è possibile eseguire il routing dei messaggi di telemetria del dispositivo, degli eventi del ciclo di vita del dispositivo e degli eventi di modifica del dispositivo gemello. Per altre informazioni sul routing dei messaggi dell'hub IoT, vedere [Usare il routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![route GE hub](./media/tutorial-iot-hub-maps/hub-route.png)

Nello scenario di esempio, si filtrano tutti i messaggi in cui il veicolo a noleggio è in movimento. Per pubblicare eventi di telemetria del dispositivo simili nella Griglia di eventi, si userà la query di routing per filtrare gli eventi in cui la proprietà `Engine` è **"ON"** . Esistono diversi modi per eseguire query sui messaggi da dispositivo IoT a cloud; per altre informazioni sulla sintassi di routing dei messaggi, vedere [Routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Per creare una query di routing, fare clic sulla route **RouteToEventGrid** e sostituire la **Query di routing** con **"Engine ='ON'"** e fare clic su **Salva**. A questo punto, l'hub IoT pubblica solo i dati di telemetria del dispositivo in cui il motore è in funzione.

![filtro GE hub](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Inviare dati di telemetria all'hub IoT

Quando la funzione di Azure è operativa, i dati di telemetria vengono inviati all'hub IoT, che li instraderà a Griglia di eventi. Si userà un'applicazione C# per simulare i dati di posizione per un dispositivo interno al veicolo di un'auto a noleggio. Per eseguire l'applicazione, è necessario .NET Core SDK 2.1.0 o versione successiva nel computer di sviluppo. Seguire questa procedura per inviare i dati di telemetria simulati all'hub IoT.

1. Scaricare il progetto C# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation). 

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

  ![Output del terminale](./media/tutorial-iot-hub-maps/terminal.png)

Se si apre ora il contenitore di archiviazione BLOB, dovrebbe essere possibile visualizzare quattro BLOB per le posizioni in cui il veicolo si trovava al di fuori del recinto virtuale.

![Accedere al BLOB](./media/tutorial-iot-hub-maps/blob.png)

La mappa seguente mostra quattro punti in cui il veicolo si trovava al di fuori del recinto virtuale, registrati a intervalli di tempo regolari.

![mappa violazione](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Passaggi successivi

Per esplorare le API di Mappe di Azure usate in questa esercitazione, vedere:

* [Get Search Address Reverse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Get Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Per un elenco completo delle API REST di Mappe di Azure, vedere:

* [API REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Per altre informazioni su Plug and Play IoT, vedere:

* [Plug and Play IoT](https://docs.microsoft.com/azure/iot-pnp)

Per ottenere un elenco di dispositivi Microsoft Azure Certified per IoT, visitare:

* [Dispositivi Azure Certified](https://catalog.azureiotsolutions.com/)

Per altre informazioni su come inviare i dati di telemetria da dispositivo a cloud e viceversa, vedere:

* [Inviare dati di telemetria da un dispositivo](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
