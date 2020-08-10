---
title: Esportare i dati da IoT Central | Microsoft Docs
description: Come usare la nuova esportazione dei dati per esportare i dati di Internet delle cose in Azure e nelle destinazioni cloud personalizzate.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036556"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Esportare i dati delle cose nelle destinazioni cloud usando l'esportazione dei dati (anteprima)

> [!Note]
> Si sta cercando l'esportazione dei dati legacy? [Qui](./howto-export-data.md)è possibile trovare i documenti di esportazione dei dati. Per informazioni sulle differenze tra la nuova esportazione dei dati e l'esportazione dei dati legacy, vedere la [tabella di confronto](#comparison-of-legacy-data-export-and-new-data-export).

Questo articolo descrive come usare le nuove funzionalità di anteprima di esportazione dei dati in Azure IoT Central. È possibile usare questa funzionalità per esportare in modo continuativo i dati delle cose filtrate e arricchiti nei servizi cloud. È possibile usare l'esportazione dei dati per eseguire il push delle modifiche in tempo quasi reale ad altre parti della soluzione cloud per informazioni dettagliate, analisi e archiviazione a caldo. 

 Ad esempio, è possibile:
-   Esporta continuamente i dati di telemetria e le modifiche delle proprietà in formato JSON quasi in tempo reale
-   Filtrare i flussi di dati per esportare funzionalità specifiche che corrispondono alle condizioni personalizzate
-   Arricchire i flussi di dati con valori personalizzati e valori di proprietà dal dispositivo
-   Inviare questi dati a destinazioni quali hub eventi di Azure, bus di servizio di Azure, archiviazione BLOB di Azure e endpoint webhook

> [!Note]
> Quando si attiva l'esportazione dei dati, si ottengono solo i dati da quel momento in poi. Attualmente non è possibile recuperare i dati per un periodo di tempo in cui l'esportazione dei dati è disattivata. Per conservare dati più cronologici, attivare prima l'esportazione dei dati.

## <a name="prerequisites"></a>Prerequisiti

Per usare l'esportazione dei dati (anteprima), è necessario disporre di un'applicazione V3 ed è necessario disporre delle autorizzazioni di esportazione dei dati.

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

La destinazione di esportazione deve esistere prima di configurare l'esportazione dei dati. Di seguito sono indicati i tipi di destinazione disponibili:
  - Hub eventi di Azure
  - Coda del bus di servizio di Azure
  - Argomento del bus di servizio di Azure
  - Archiviazione BLOB di Azure
  - webhook

### <a name="create-an-event-hubs-destination"></a>Creare una destinazione di hub eventi

Se non si dispone di uno spazio dei nomi di hub eventi esistente da esportare in, attenersi alla seguente procedura:

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../../event-hubs/event-hubs-create.md).

2. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

3. Generare una chiave da usare in IoT Central per configurare l'esportazione dei dati. 
    - Fare clic sull'istanza di hub eventi creata. 
    - Fare clic su **Impostazioni/criteri di accesso condiviso**. 
    - Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** . 
    - Copiare la stringa di connessione primaria o secondaria. Questa operazione verrà utilizzata per configurare una nuova destinazione in IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Creare una destinazione di coda o argomento del bus di servizio

Se non si dispone di uno spazio dei nomi del bus di servizio esistente in cui eseguire l'esportazione, attenersi alla procedura seguente:

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Per creare una coda o un argomento in cui eseguire l'esportazione, passare allo spazio dei nomi del bus di servizio e selezionare **+ coda** o **+ argomento**.

3. Generare una chiave da usare in IoT Central per configurare l'esportazione dei dati. 
    - Fare clic sulla coda o sull'argomento creato. 
    - Fare clic su **Impostazioni/criteri di accesso condiviso**. 
    - Creare una nuova chiave o scegliere una chiave esistente con autorizzazioni di **invio** . 
    - Copiare la stringa di connessione primaria o secondaria. Questa operazione verrà utilizzata per configurare una nuova destinazione in IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Creare una destinazione di archiviazione BLOB di Azure

Se non si ha un account di archiviazione di Azure esistente da esportare, seguire questa procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](https://aka.ms/blobdocscreatestorageaccount) o [Azure Data Lake storage V2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). L'esportazione dei dati può scrivere solo i dati negli account di archiviazione che supportano i BLOB in blocchi. L'elenco seguente illustra i tipi di account di archiviazione compatibili noti:

    |Livello di prestazioni|Tipo di account|
    |-|-|
    |Standard|per utilizzo generico V2|
    |Standard|per utilizzo generico V1|
    |Standard|Archiviazione BLOB|
    |Premium|Archiviazione BLOB in blocchi|

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

3. Per generare una stringa di connessione per l'account di archiviazione, passare a **Impostazioni/chiavi di accesso**. Copiare una delle due stringhe di connessione.

### <a name="create-a-webhook-endpoint"></a>Creare un endpoint del webhook
È possibile fornire un endpoint HTTP disponibile pubblicamente per i dati da esportare in. È possibile creare un endpoint del webhook di test usando RequestBin. Tenere presente che RequestBin ha un limite di richieste set prima che le richieste vengano limitate.

1. Aprire [RequestBin](https://requestbin.net/).
2. Creare un nuovo RequestBin e copiare l'URL del contenitore.

## <a name="set-up-data-export"></a>Configurare l'esportazione dei dati

Ora che si dispone di una destinazione in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione dei dati.

1. Accedere all'applicazione IoT Central.

2. Nel riquadro sinistro selezionare **esportazione dati**.

    > [!Tip]
    > Se non viene visualizzata l' **esportazione dei dati** nel riquadro sinistro, non si dispone delle autorizzazioni per configurare l'esportazione dei dati nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+ nuova esportazione** . 

4. Immettere un nome visualizzato per la nuova esportazione e verificare che l'interruttore **abilitato** sia attivato per il flusso dei dati.

## <a name="1-choose-the-type-of-data-to-export"></a>1. scegliere il tipo di dati da esportare
È possibile scegliere di esportare in modo continuativo tipi diversi di dati. Di seguito sono riportati i tipi di dati supportati:

| Tipo di dati | Descrizione | Formato dati |
| :------------- | :---------- | :----------- |
|  Telemetria | Esporta messaggi di telemetria dai dispositivi quasi in tempo reale. Ogni messaggio esportato conterrà il contenuto completo del messaggio originale del dispositivo, normalizzato.   |  [Formato del messaggio di telemetria](#telemetry-format)   |
| Modifiche delle proprietà | Consente di esportare le modifiche apportate alle proprietà del dispositivo e del cloud in tempo quasi reale. Per le proprietà del dispositivo di sola lettura, le modifiche ai valori segnalati verranno esportate. Per le proprietà di lettura e scrittura, verranno esportati sia i valori segnalati che quelli desiderati. | [Formato del messaggio di modifica delle proprietà](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (facoltativo) aggiungere filtri
Aggiungere filtri per ridurre la quantità di dati esportati in base alle condizioni di filtro. Sono disponibili diversi tipi di filtri per ogni tipo di dati da esportare.

### <a name="telemetry-filters"></a>Filtri di telemetria
  - **Filtro funzionalità**: se si sceglie un elemento di telemetria nell'elenco a discesa, il flusso esportato conterrà solo i dati di telemetria che soddisfano la condizione di filtro. Se si sceglie un elemento della proprietà del dispositivo o del cloud nell'elenco a discesa, il flusso esportato conterrà solo i dati di telemetria dei dispositivi con proprietà che corrispondono alla condizione di filtro.
  - **Filtro proprietà messaggio**: i dispositivi che usano gli SDK per dispositivi sono autorizzati a inviare *proprietà del messaggio* o *proprietà dell'applicazione* in ogni messaggio di telemetria, che è un contenitore di coppie chiave-valore usato in genere per contrassegnare il messaggio con identificatori personalizzati. È possibile creare un filtro di proprietà dei messaggi digitando la chiave della proprietà del messaggio che si sta cercando e specificando una condizione. Verranno esportati solo i messaggi di telemetria con proprietà del messaggio che corrispondono alla condizione di filtro specificata. Sono supportati solo gli operatori di confronto tra stringhe (uguale a, non uguale a, Contains, non contiene, EXISTS, non esiste). [Altre informazioni sulle proprietà dell'applicazione](../../iot-hub/iot-hub-devguide-messages-construct.md)sono disponibili nella documentazione dell'hub Internet.

### <a name="property-changes-filters"></a>Filtri delle modifiche alle proprietà
**Filtro proprietà**: scegliere un elemento proprietà nell'elenco a discesa e il flusso esportato conterrà solo le modifiche alla proprietà selezionata che soddisfano la condizione di filtro.

## <a name="3-optional-add-enrichments"></a>3. (facoltativo) aggiungere arricchimenti
Aggiungere arricchimenti per arricchire i messaggi esportati con metadati aggiuntivi nelle coppie chiave-valore. Questi sono gli arricchimenti disponibili per i tipi di dati di dati di telemetria e di modifica delle proprietà:
  - **Stringa personalizzata**: aggiunge una stringa statica personalizzata a ogni messaggio. Immettere una chiave qualsiasi e immettere qualsiasi valore stringa.
  - **Property**: aggiunge la proprietà del dispositivo corrente segnalata o il valore della proprietà cloud a ogni messaggio. Immettere una chiave qualsiasi e scegliere una proprietà del dispositivo o del cloud. Se il messaggio esportato è da un dispositivo che non dispone della proprietà del dispositivo o del cloud specificata, il messaggio esportato non avrà tale arricchimento.

## <a name="4-add-destinations"></a>4. aggiungere destinazioni
Creare una nuova destinazione o aggiungere una destinazione già creata. 
  
1. Fare clic sul collegamento **Crea una nuova destinazione** . Specificare le informazioni seguenti:
    - **Nome destinazione**: il nome visualizzato della destinazione in IOT Central
    - **Tipo destinazione**: scegliere il tipo di destinazione. Se non è già stato fatto, [configurare la destinazione di esportazione](#set-up-export-destination)
    - Per hub eventi di Azure, coda o argomento del bus di servizio di Azure, incollare la stringa di connessione per la risorsa. 
    - Per archiviazione BLOB di Azure, incollare la stringa di connessione per la risorsa e immettere il nome del contenitore (con distinzione tra maiuscole e minuscole).
    - Per webhook incollare l'URL di callback per l'endpoint del webhook. 
    - Fare clic su **Crea**

2. Fare clic su **+ destinazione** e scegliere una destinazione dall'elenco a discesa. È possibile aggiungere fino a 5 destinazioni a un'unica esportazione.

3. Al termine della configurazione dell'esportazione, fare clic su **Salva**. Dopo alcuni minuti, i dati verranno visualizzati nelle destinazioni.

## <a name="export-contents-and-format"></a>Esporta contenuto e formato

### <a name="azure-blob-storage-destination"></a>Destinazione di archiviazione BLOB di Azure

I dati vengono esportati una volta al minuto, con ogni file contenente il batch di modifiche apportate dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON. I percorsi predefiniti nell'account di archiviazione sono:

- Telemetria: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modifiche alle proprietà: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Per esplorare i file esportati nella portale di Azure, passare al file e selezionare la scheda **modifica BLOB** .

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Hub eventi di Azure e destinazioni del bus di servizio di Azure

I dati vengono esportati in tempo quasi reale. I dati sono nel corpo del messaggio ed è in formato JSON codificato come UTF-8. 

Nel contenitore delle annotazioni o delle proprietà di sistema del messaggio è possibile trovare `iotcentral-device-id` ,, `iotcentral-application-id` `iotcentral-message-source` e `iotcentral-message-type` che hanno gli stessi valori dei campi corrispondenti nel corpo del messaggio.

### <a name="webhook-destination"></a>Destinazione webhook
Per le destinazioni webhook, i dati vengono esportati anche in tempo quasi reale. Il formato dei dati nel corpo del messaggio è identico a quello degli hub eventi e del bus di servizio.


## <a name="telemetry-format"></a>Formato di telemetria
Ogni messaggio esportato contiene una forma normalizzata del messaggio completo inviato dal dispositivo nel corpo del messaggio in formato JSON codificato come UTF-8. Le informazioni aggiuntive incluse in ogni messaggio includono:

- `applicationId`dell'app IoT Central
- `messageSource`che è la *telemetria* per l'esportazione dei dati di telemetria
- `deviceId`del dispositivo che ha inviato il messaggio di telemetria
- `schema`nome e versione dello schema del payload
- `templateId`ID del modello di dispositivo associato al dispositivo
- `enrichments`eventuali arricchimenti impostati nell'esportazione
- `messageProperties`sono le parti di dati aggiuntive inviate dal dispositivo insieme al messaggio. Questa operazione è nota anche come *proprietà dell'applicazione*. [per altre informazioni](../../iot-hub/iot-hub-devguide-messages-construct.md), vedere la documentazione sull'hub.

Per gli hub eventi e il bus di servizio, IoT Central Esporta rapidamente un nuovo messaggio dopo la ricezione del messaggio da un dispositivo.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto.

L'esempio seguente mostra un messaggio di telemetria esportato:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Formato modifiche proprietà

Ogni messaggio o record rappresenta una modifica a una proprietà del dispositivo o del cloud. Per le proprietà del dispositivo, solo le modifiche nel valore restituito vengono esportate come un messaggio separato. Le informazioni aggiuntive incluse nel messaggio esportato includono:

- `applicationId`dell'app IoT Central
- `messageSource`*Proprietà* per l'esportazione dei dati delle modifiche delle proprietà
- `messageType`che può essere *cloudPropertyChange* o *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`del dispositivo le cui proprietà sono state modificate
- `schema`nome e versione dello schema del payload
- `templateId`ID del modello di dispositivo associato al dispositivo
- `enrichments`eventuali arricchimenti impostati nell'esportazione

Per gli hub eventi e il bus di servizio, IoT Central Esporta i nuovi dati dei messaggi nell'hub eventi o nella coda o nell'argomento del bus di servizio quasi in tempo reale.

Per l'archiviazione BLOB, i messaggi vengono inviati in batch ed esportati una volta al minuto.

L'esempio seguente mostra un messaggio di modifica delle proprietà esportato ricevuto nell'archivio BLOB di Azure.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Confronto tra l'esportazione dei dati legacy e la nuova esportazione dei dati
Si tratta di una tabella in cui vengono evidenziate le differenze tra l'esportazione dei dati legacy e la nuova esportazione dei dati. [Qui](howto-export-data.md)è possibile ottenere informazioni sull'esportazione dei dati legacy.

| Funzionalità  | Esportazione dei dati legacy | Nuova esportazione dei dati |
| :------------- | :---------- | :----------- |
| Tipi di dati disponibili | Telemetria, dispositivi, modelli di dispositivo | Telemetria, modifiche delle proprietà |
| Filtro | nessuno | Dipende dal tipo di dati esportato. Per la telemetria, filtra per telemetria, proprietà del messaggio, valori delle proprietà |
| Arricchimenti | nessuno | Arricchire con una stringa personalizzata o un valore di proprietà nel dispositivo |
| Destinations | Hub eventi di Azure, code e argomenti del bus di servizio di Azure, archiviazione BLOB di Azure | Come per i webhook e l'esportazione di dati legacy| 
| App supportate | V2, V3 | Solo V3 |
| Limiti rilevanti | 5 esportazioni per app, 1 destinazione per esportazione | 10 esportazioni-connessioni di destinazione per app | 

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare la nuova esportazione dei dati, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come usare Analytics in IoT Central](./howto-create-analytics.md)
