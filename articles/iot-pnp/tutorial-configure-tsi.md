---
title: Usare Time Series Insights per archiviare e analizzare i dati di telemetria del dispositivo Plug and Play IoT di Azure | Microsoft Docs
description: Configurare un ambiente di Time Series Insights e connettere l'hub IoT per visualizzare e analizzare i dati di telemetria dai dispositivi IoT Plug and Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422264"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Esercitazione: Creare e connettersi a Time Series Insights Gen2 per archiviare, visualizzare e analizzare i dati di telemetria del dispositivo IoT Plug and Play

In questa esercitazione si apprenderà come creare e configurare correttamente un ambiente [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) per l'integrazione con la soluzione IoT Plug and Play. Con TSI è possibile eseguire operazioni di raccolta, archiviazione, query e visualizzazione di dati di serie temporale a livello di Internet delle cose.

Per prima cosa si eseguirà il provisioning di un ambiente TSI e si connetterà l'hub IoT come origine evento di streaming. Si procederà quindi alla sincronizzazione dei modelli per creare il modello Time Series dell'ambiente TSI in base ai file del modello di esempio [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) usati per i dispositivo termoregolatore e termostato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per evitare la necessità di installare l'interfaccia della riga di comando di Azure in locale, è possibile usare la Azure Cloud Shell per configurare i servizi cloud.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Selezione ID serie temporale

Durante il provisioning dell'ambiente TSI, verrà richiesto di selezionare un ID serie temporale. La selezione dell'ID serie temporale appropriato è fondamentale perché la proprietà non è modificabile e non può essere cambiata dopo che è stata impostata. Scegliere un ID serie temporale è come scegliere una chiave di partizione per un database. In genere, l'ID TS deve essere il nodo foglia del modello di risorse. In altre parole, in genere è opportuno selezionare la proprietà ID della risorsa o del sensore più granulare che emette i dati di telemetria.

Per selezionare l'ID TS, l'utente di IoT Plug and Play deve valutare la presenza di [componenti](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) nei modelli di dispositivo. 

![Selezione ID TS](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Se si sta eseguendo l'avvio rapido e il dispositivo hub IoT remoto rappresenta il [termostato](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), usare `iot-hub-connection-device-id` come ID TS.

* Se è stata eseguita una delle esercitazioni per il [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) multicomponente, usare una chiave composta nella sezione seguente, scritta come `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Effettuare il provisioning dell'ambiente Azure Time Series Insights Gen2

Il comando seguente consente di:

* Creare un account di archiviazione di Azure per l'[archivio ad accesso sporadico](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store) del proprio ambiente, progettato per la conservazione a lungo termine e l'analisi dei dati cronologici.
  * Sostituire `mytsicoldstore` con un nome univoco per l'account.
* Creare un ambiente di Azure Time Series Insights Gen2, inclusa l'archiviazione ad accesso frequente con un periodo di conservazione di 7 giorni, e un archivio ad accesso sporadico per la conservazione infinita. 
  * Sostituire `my-tsi-env` con un nome univoco per l'ambiente TSI 
  * Sostituire `my-pnp-resourcegroup` con il nome del gruppo di risorse usato durante la configurazione
  * Sostituire `my-ts-id-property` con il valore della proprietà ID TS basato sui criteri di selezione sopra indicati

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

A questo punto si configurerà l'hub IoT creato in precedenza come [origine evento](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources) dell'ambiente. Quando l'origine evento è connessa, TSI inizierà a indicizzare gli eventi dall'hub, a partire dal primo evento nella coda.

Per prima cosa, creare un gruppo di consumer univoco nell'hub IoT per l'ambiente TSI. Sostituire `my-pnp-hub` con il nome dell'hub IoT usato in precedenza.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Connettere l'hub IoT. Sostituire `my-pnp-resourcegroup`, `my-pnp-hub` e `my-tsi-env` con i valori rispettivi.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com) e selezionare l'ambiente di Time Series Insights appena creato. Visitare lo *strumento di esplorazione di Time Series Insights* mostrato nella panoramica dell'istanza.

![Pagina di panoramica del portale](./media/tutorial-configure-tsi/view-environment.png)

Nella finestra di esplorazione dovrebbero essere visualizzati i due termostati in "Tutte le gerarchie". A questo punto, è possibile curare il modello Time Series in base al modello di dispositivo.

![Visualizzazione Explorer 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Sincronizzazione dei modelli tra DTDL (Digital Twins Definition Language) e Time Series Insights Gen2

A questo punto, si traslerà il modello di dispositivo DTDL nel modello di risorse in Azure Time Series Insights (TSI). Il modello Time Series di TSI è uno strumento di modellazione semantica per la contestualizzazione dei dati all'interno di TSI. Il modello Time Series include tre componenti principali:

* [Istanze di modello Time Series](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Le istanze sono rappresentazioni virtuali delle serie temporali stesse. Le istanze verranno identificate in modo univoco dall'ID TS.
* [Gerarchie di modelli Time Series](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). Le gerarchie organizzano le istanze specificando i nomi e le relazioni delle proprietà.
* [Tipi di modelli Time Series](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). I tipi consentono di definire [variabili](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) o formule per eseguire calcoli. I tipi sono associati a un'istanza specifica.

> [!NOTE]
> Gli esempi seguenti sono relativi al TemperatureController multicomponente.

### <a name="define-your-types"></a>Definire i tipi

È possibile iniziare a inserire i dati in Azure Time Series Insights Gen2 senza avere un modello predefinito. Quando riceve i dati di telemetria, TSI proverà a risolvere automaticamente le istanze di serie temporali in base al valore della proprietà ID TS. A tutte le istanze verrà assegnato il *Tipo predefinito*. È necessario creare manualmente un nuovo tipo per rappresentare i modelli. L'immagine seguente illustra un metodo semplice per sincronizzare un modello DTDL e un tipo TSM:

![Tipo da DTDL a TSM](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* L'identificatore di modello di dispositivo gemello (DTMI) diventerà l'ID tipo
* Il nome del tipo può essere il nome del modello o il nome visualizzato
* La descrizione del modello diventa la descrizione del tipo
* Per ogni componente di telemetria con uno schema numerico viene creata almeno una variabile di tipo. 
  * Per le variabili è possibile usare solo tipi di dati numerici, ma se un valore viene inviato come stringa analizzabile, ad esempio `"0"`, è possibile usare una funzione di [conversione](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) come `toDouble`
* Il nome della variabile può essere il nome dei dati di telemetria o il nome visualizzato
* Quando si definisce l'espressione TSX (Time Series Expression) variabile, fare riferimento al nome dei dati di telemetria in transito e al relativo tipo di dati.

> [!NOTE]
> Questo esempio mostra solo due variabili, ovvero un'aggregazione e un valore numerico, ma ogni tipo può averne fino a 100. Variabili diverse possono fare riferimento allo stesso valore di telemetria per eseguire calcoli differenti in base alle esigenze. Per l'elenco completo di filtri, aggregati e funzioni scalari, vedere la documentazione della [sintassi dell'espressione TSX di Time Series Insights Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

Aprire l'editor di testo preferito e salvare il file JSON seguente nell'unità locale:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Nello strumento di esplorazione di Time Series Insights passare alla scheda Model (Modello) facendo clic sull'icona del modello a sinistra. Fare clic su **Types** (Tipi) e quindi su **Upload JSON** (Carica JSON):

![Caricamento](./media/tutorial-configure-tsi/upload-type.png)

Selezionare **Choose file** (Scegli file), selezionare il file JSON salvato in precedenza, quindi fare clic su **Upload** (Carica)

Verrà visualizzato il tipo di termostato appena definito.

### <a name="optional---create-a-hierarchy"></a>Facoltativo: creare una gerarchia

Facoltativamente, è possibile creare una gerarchia per organizzare i due componenti del termostato sotto il rispettivo elemento padre TemperatureController.

Fare clic su *Gerarchie* e selezionare *Aggiungi una gerarchia*. Immettere `Device Fleet` come nome e creare un unico livello denominato `Device Name`, quindi fare clic su *Salva*.

![Aggiungere una gerarchia](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Assegnare le istanze al tipo corretto

Si modificherà quindi il tipo delle istanze che, facoltativamente, potranno essere aggiunte alla gerarchia

Selezionare la scheda *Istanze* e fare clic sull'icona *Modifica* a destra.

![Modificare le istanze](./media/tutorial-configure-tsi/edit-instance.png)

Fare clic sull'elenco a discesa Tipo e selezionare `Thermostat`. 

![Modificare il tipo di istanza](./media/tutorial-configure-tsi/change-type.png)

Se è stata creata una gerarchia, selezionare *Campi di istanza* e selezionare la casella `Device Fleet`. Immettere `Temperature Controller` come valore del dispositivo padre, quindi fare clic su *Salva*.

![Assegnare alla gerarchia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Ripetere i passaggi precedenti per il secondo termostato.

### <a name="view-your-data"></a>Visualizzare i dati

Tornare al riquadro dei grafici ed espandere la flotta di dispositivi e TemperatureController. Fare clic su thermostat1, selezionare la variabile `Temperature` e quindi fare clic su *Aggiungi* per tracciare il valore. Eseguire la stessa operazione per thermostat2.

![Modificare il tipo di istanza per thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle varie opzioni di creazione di grafici, inclusi i controlli di ridimensionamento intervallo e asse Y, vedere la documentazione dello [strumento di esplorazione di Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels).

* Per una panoramica approfondita del modello Time Series dell'ambiente, vedere [questo](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) articolo.

* Per approfondire le API di query e la sintassi delle espressioni della serie temporale, [vedere qui](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




