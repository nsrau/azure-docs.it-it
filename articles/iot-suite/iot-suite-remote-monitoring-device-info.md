---
title: Metadati di informazioni sul dispositivo nella soluzione per il monitoraggio remoto | Documentazione Microsoft
description: Descrizione della soluzione preconfigurata per il monitoraggio remoto di Azure IoT e relativa architettura.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: f8fd452806a0a0b98cf8e434c9bd55700083a6c5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadati di informazioni sul dispositivo nella soluzione preconfigurata per il monitoraggio remoto

La soluzione preconfigurata per il monitoraggio remoto di Azure IoT Suite dimostra un approccio per la gestione dei metadati del dispositivo. Questo articolo delinea l'approccio adottato da questa soluzione per illustrare:

* Quali metadati del dispositivo vengono archiviati dalla soluzione.
* Come la soluzione gestisce i metadati del dispositivo.

## <a name="context"></a>Context

La soluzione preconfigurata per il monitoraggio remoto usa l'[hub IoT di Azure][lnk-iot-hub] per consentire ai dispositivi di inviare dati al cloud. La soluzione archivia le informazioni sui dispositivi in tre posizioni diverse:

| Percorso | Informazioni archiviate | Implementazione |
| -------- | ------------------ | -------------- |
| Registro delle identità | ID del dispositivo, chiavi di autenticazione, stato di abilitazione | Predefinito nell'hub IoT |
| Dispositivi gemelli | Metadati: proprietà segnalate, proprietà desiderate, tag | Predefinito nell'hub IoT |
| Cosmos DB | Cronologia dei comandi e dei metodi | Personalizzato per la soluzione |

L'hub IoT include un [registro delle identità dei dispositivi][lnk-identity-registry] per gestire l'accesso all'hub IoT e usa i [dispositivi gemelli][lnk-device-twin] per gestire i metadati del dispositivo. È disponibile anche un *registro dei dispositivi* specifico della soluzione di monitoraggio remoto, che archivia la cronologia dei comandi e dei metodi. La soluzione di monitoraggio remoto usa un database [Cosmos DB][lnk-docdb] per implementare un archivio personalizzato per la cronologia dei comandi e dei metodi.

> [!NOTE]
> La soluzione preconfigurata per il monitoraggio remoto mantiene il registro delle identità dei dispositivi sincronizzato con le informazioni disponibili nel database Cosmos DB. Entrambi usano lo stesso ID dispositivo per identificare in modo univoco ogni dispositivo connesso all'hub IoT.

## <a name="device-metadata"></a>Metadati del dispositivo

L'hub IoT gestisce un [dispositivo gemello][lnk-device-twin] per ogni dispositivo simulato e fisico connesso a una soluzione di monitoraggio remoto. La soluzione usa i dispositivi gemelli per gestire i metadati associati ai dispositivi. Un dispositivo gemello è un documento JSON gestito dall'hub IoT e la soluzione usa l'API dell'hub IoT per interagire con i dispositivi gemelli.

Un dispositivo gemello archivia tre tipi di metadati:

- Le *proprietà segnalate* vengono inviate a un hub IoT da un dispositivo. Nella soluzione di monitoraggio remoto, i dispositivi simulati inviano le proprietà all'avvio e in risposta a comandi e metodi di tipo **Change device state**. È possibile visualizzare le proprietà segnalate in **Elenco dispositivi** e **Dettagli dispositivo** nel portale della soluzione. Le proprietà segnalate sono di sola lettura.
- Le *proprietà desiderate* vengono recuperate dall'hub IoT dai dispositivi. Le modifiche di configurazione necessarie nel dispositivo sono a carico del dispositivo. Il dispositivo deve anche segnalare la modifica all'hub come proprietà segnalata. È possibile impostare un valore della proprietà desiderata tramite il portale della soluzione.
- I *tag* esistono solo nel dispositivo gemello e non vengono mai sincronizzati con un dispositivo. È possibile impostare i valori dei tag nel portale della soluzione e usarli per filtrare l'elenco di dispositivi. La soluzione usa un tag anche per identificare l'icona da visualizzare per un dispositivo nel portale della soluzione.

Le proprietà segnalate dai dispositivi simulati includono ad esempio il produttore, il numero di modello, la latitudine e la longitudine. I dispositivi simulati restituiscono anche l'elenco di metodi supportati come proprietà segnalata.

> [!NOTE]
> Il codice del dispositivo simulato usa le proprietà desiderate **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** soltanto per aggiornare le proprietà segnalate inviate all'hub IoT. Tutte le altre richieste di modifica delle proprietà desiderate vengono ignorate.

Un documento JSON di metadati di informazioni sul dispositivo archiviato nel database Cosmos DB del registro dei dispositivi ha la struttura seguente:

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> Le informazioni sul dispositivo possono includere anche metadati per descrivere la telemetria inviata dal dispositivo all'hub IoT. La soluzione per il monitoraggio remoto usa questi metadati di telemetria per personalizzare la visualizzazione della [telemetria dinamica][lnk-dynamic-telemetry] nel dashboard.

## <a name="lifecycle"></a>Ciclo di vita

Quando si crea per la prima volta un dispositivo nel portale della soluzione, la soluzione crea una voce nel database Cosmos DB per archiviare la cronologia dei comandi e dei metodi. A questo punto la soluzione crea anche una voce per il dispositivo nel registro delle identità dei dispositivi che genera le chiavi usate dal dispositivo per l'autenticazione con l'hub IoT. Viene creato anche un dispositivo gemello.

Quando un dispositivo si connette per la prima volta alla soluzione, invia proprietà segnalate e un messaggio informativo sul dispositivo I valori delle proprietà segnalate vengono salvati automaticamente nel dispositivo gemello. Le proprietà segnalate includono il produttore del modello, il numero di modello, il numero di serie e un elenco di metodi supportati. Il messaggio informativo sul dispositivo include un elenco dei comandi supportati dal dispositivo, incluse le informazioni sui parametri dei comandi. Quando la soluzione riceve il messaggio, aggiorna le informazioni sui dispositivi nel database Cosmos DB.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Visualizzare e modificare informazioni sul dispositivo nel portale della soluzione

Per impostazione predefinita, l'elenco di dispositivi nel portale della soluzione visualizza le proprietà del dispositivo seguenti come colonne: **Stato**, **ID dispositivo**, **Produttore**, **Numero modello**, **Numero di serie**, **Firmware**, **Piattaforma**, **Processore** e **RAM installata**. È possibile personalizzare le colonne facendo clic su **Editor di colonne**. Le proprietà **Latitudine** e **Longitudine** del dispositivo determinano la posizione nella mappa di Bing nel dashboard.

![Editor di colonne nell'elenco di dispositivi][img-device-list]

Nel riquadro **Dettagli dispositivo** nel portale della soluzione, è possibile modificare le proprietà desiderate e i tag. Le proprietà segnalate sono di sola lettura.

![Riquadro Dettagli dispositivo][img-device-edit]

È possibile usare il portale della soluzione per rimuovere un dispositivo dalla soluzione. Quando si rimuove un dispositivo, la soluzione rimuove la voce del dispositivo dal registro delle identità e quindi elimina il dispositivo gemello. La soluzione rimuove anche le informazioni correlate al dispositivo dal database Cosmos DB. Prima di poter rimuovere un dispositivo, è necessario disabilitarlo.

![Rimuovere un dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Elaborazione dei messaggi informativi sul dispositivo

I messaggi informativi sul dispositivo inviati da un dispositivo sono diversi dai messaggi di telemetria. I messaggi informativi sul dispositivo includono i comandi a cui un dispositivo può rispondere e l'eventuale cronologia dei comandi. L'hub IoT non conosce i metadati contenuti in un messaggio informativo sul dispositivo ed elabora il messaggio come qualsiasi altro messaggio inviato dal dispositivo al cloud. Nella soluzione per il monitoraggio remoto, un processo di [Analisi di flusso di Azure][lnk-stream-analytics] legge i messaggi dall'hub IoT. Il processo **DeviceInfo** di Analisi di flusso filtra i messaggi contenenti **"ObjectType": "DeviceInfo"** e li inoltra all'istanza dell'host **EventProcessorHost** in esecuzione in un processo Web. La logica nell'istanza di **EventProcessorHost** usa l'ID dispositivo per trovare il record di Cosmos DB per il dispositivo specifico e aggiorna il record.

> [!NOTE]
> Un messaggio informativo sul dispositivo è un messaggio standard inviato dal dispositivo al cloud. La soluzione distingue i messaggi informativi sul dispositivo dai messaggi di telemetria usando le query di Analisi di flusso di Azure.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver illustrato come personalizzare le soluzioni preconfigurate, è possibile esplorare alcune altre funzionalità e soluzioni preconfigurate di Suite IoT:

* [Panoramica della soluzione preconfigurata di manutenzione predittiva][lnk-predictive-overview]
* [Domande frequenti su IoT Suite][lnk-faq]
* [Sicurezza IoT sin dall'inizio][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
