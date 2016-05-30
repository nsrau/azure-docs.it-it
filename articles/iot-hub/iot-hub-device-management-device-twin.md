<properties
	pageTitle="Dispositivi gemelli di Gestione dei dispositivi dell'hub IoT | Microsoft Azure"
	description="Esercitazione relativa alla funzionalità Gestione dei dispositivi dell'hub IoT di Azure che descrive come usare i dispositivi gemelli."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Esercitazione: Come usare il dispositivo gemello con C# (anteprima)

[AZURE.INCLUDE [iot-hub-device-management-twin-selector](../../includes/iot-hub-device-management-twin-selector.md)]
## Introduzione

Gestione dei dispositivi dell'hub Iot di Azure include il dispositivo gemello, una rappresentazione lato server di un dispositivo fisico. Il diagramma seguente mostra i componenti del dispositivo gemello.

![][img-twin]

Questa esercitazione si concentrerà sulle proprietà del dispositivo. Per informazioni sugli altri componenti, vedere [Panoramica di Gestione dei dispositivi dell'hub IoT di Azure][lnk-dm-overview].

Le proprietà del dispositivo sono costituite da un dizionario predefinito di proprietà che descrivono il dispositivo fisico. Il dispositivo fisico è il dispositivo master di ogni proprietà del dispositivo e costituisce l'archivio autorevole di ogni valore corrispondente. Una rappresentazione 'che risulterà infine coerente' di queste proprietà è archiviata nel dispositivo gemello nel cloud. La coerenza e l'aggiornamento sono soggetti alle impostazioni di sincronizzazione, descritte sotto. Tra gli esempi di proprietà del dispositivo sono inclusi la versione del firmware, il livello della batteria e il nome del produttore.

## Sincronizzazione delle proprietà del dispositivo

Il dispositivo fisico è l'origine autorevole delle proprietà del dispositivo. I valori selezionati nel dispositivo fisico sono automaticamente sincronizzati nel dispositivo gemello nell'hub IoT tramite il modello di *osservazione/notifica* descritto dalla specifica [LWM2M][lnk-lwm2m].

Quando il dispositivo fisico si connette all'hub IoT, avvia le *osservazioni* delle proprietà del dispositivo selezionate. Il dispositivo fisico *notifica* quindi all'hub IoT le modifiche apportate alle proprietà del dispositivo. Per implementare l'isteresi, il valore di **pmin**, ovvero il tempo minimo tra le notifiche, è impostato su 5 minuti. Questo significa che per ogni proprietà il dispositivo fisico non invia all'hub IoT una notifica più di una volta ogni 5 minuti, anche se si è verificata una modifica. Per garantire l'aggiornamento, **pmax**, ovvero il tempo massimo tra le notifiche, è impostato su 6 ore. Questo significa che per ogni proprietà il dispositivo fisico invia all'hub IoT una notifica almeno una volta ogni 6 ore, anche se la proprietà non ha subito modifiche in tale periodo.

Quando il dispositivo fisico si disconnette, la sincronizzazione viene arrestata e viene riavviata quando il dispositivo si riconnette al servizio. Per verificare lo stato di aggiornamento, controllare l'ora dell'ultimo aggiornamento per una proprietà.

L'elenco completo delle proprietà del dispositivo osservate automaticamente è riportato sotto:

![][img-observed]


## Esecuzione dell'esempio del dispositivo gemello

L'esempio seguente estende la funzionalità dell'esercitazione [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started]. Inizia dall'esecuzione di due dispositivi simulati diversi e usa il dispositivo gemello per leggere e modificare le proprietà in un dispositivo simulato.

### Prerequisiti 

Prima di eseguire questo esempio, è necessario aver completato le procedure illustrate in [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started]. I dispositivi simulati devono quindi essere in esecuzione. Se il processo è già stato completato in precedenza, riavviare i dispositivi simulati adesso.

### Avvio dell'esempio

Per avviare l'esempio, è necessario eseguire il processo **DeviceTwin.exe**. Questo processo legge le proprietà del dispositivo dal dispositivo gemello e dal dispositivo fisico. Modifica anche una proprietà del dispositivo nel dispositivo fisico. Per avviare l'esempio, seguire questa procedura:

1.  Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla cartella **azure-iot-sdks\\csharp\\service\\samples\\bin**.  

2.  Eseguire `DeviceTwin.exe <IoT Hub Connection String>`.

Nella finestra della riga di comando verrà visualizzato l'output che mostra l'uso del dispositivo gemello. Processo eseguito dall'esempio:

1.  Stampa tutte le proprietà del dispositivo in un dispositivo gemello.

2.  Lettura approfondita: esegue la lettura della proprietà del dispositivo relativa al livello della batteria dal dispositivo fisico (3 volte).

3.  Scrittura approfondita: esegue la scrittura della proprietà del dispositivo **Timezone** nel dispositivo fisico.

4.  Lettura approfondita: esegue la lettura della proprietà del dispositivo **Timezone** dal dispositivo fisico per verificare se è stata modificata.

### Lettura superficiale

Esistono alcune differenze tra letture *superficiali* e letture/scritture *approfondite*. Una lettura superficiale restituisce il valore della proprietà richiesta dal dispositivo gemello archiviato nell'hub IoT di Azure. Si tratterà del valore ottenuto dall'operazione di notifica precedente. Non è possibile eseguire una scrittura superficiale perché il dispositivo fisico è l'origine autorevole delle proprietà del dispositivo. Una lettura superficiale è un'operazione di lettura della proprietà dal dispositivo gemello:

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].Value.ToString();
```

Per determinare lo stato di aggiornamento di questi valori, controllare l'ora dell'ultimo aggiornamento:

```
device.DeviceProperties[DevicePropertyNames.BatteryLevel].LastUpdatedTime.ToString();
```

Allo stesso modo, è possibile eseguire la lettura delle proprietà del servizio, che sono archiviate esclusivamente nel dispositivo gemello e non sono sincronizzate con il dispositivo.

### Lettura approfondita

Una lettura approfondita avvia un processo del dispositivo per eseguire la lettura della proprietà richiesta dal dispositivo fisico. I processi del dispositivo sono stati illustrati in [Panoramica di Gestione dei dispositivi IoT di Azure][lnk-dm-overview] e descritti in dettaglio in [Esercitazione: Come usare processi del dispositivo per aggiornare il firmware del dispositivo][lnk-dm-jobs]. La lettura approfondita consente di ottenere un valore più aggiornato per la proprietà del dispositivo, dal momento che l'aggiornamento non è limitato dall'intervallo di notifica. Il processo invia un messaggio al dispositivo fisico e aggiorna il dispositivo gemello con il valore più recente solo per la proprietà specificata. Non aggiorna l'intero dispositivo gemello.

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyReadAsync(Guid.NewGuid().ToString(), deviceId, propertyToRead);
```

Non è possibile eseguire una lettura approfondita sulle proprietà del servizio o i tag, perché non sono sincronizzati con il dispositivo.

### Scrittura approfondita

Per modificare una proprietà del dispositivo accessibile in scrittura, è possibile eseguire una scrittura approfondita che avvia un processo del dispositivo per scrivere il valore nel dispositivo fisico. Non tutte le proprietà del dispositivo sono accessibili in scrittura. Per un elenco completo, vedere l'appendice A dell'articolo sull'[introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure][lnk-dm-library].

Il processo invia un messaggio al dispositivo fisico richiedendo l'aggiornamento della proprietà specificata. Il dispositivo gemello non viene aggiornato immediatamente al termine del processo. Sarà necessario attendere fino all'intervallo di notifica successivo. Al termine della sincronizzazione, sarà possibile visualizzare la modifica nel dispositivo gemello eseguendo una lettura superficiale.

```
JobResponse jobResponse = await deviceJobClient.ScheduleDevicePropertyWriteAsync(Guid.NewGuid().ToString(), deviceId, propertyToSet, setValue); TODO
```

### Dettagli di implementazione del simulatore di dispositivi

Si esamineranno ora le operazioni da eseguire sul lato dispositivo per implementare il modello di osservazione/notifica e le operazioni di lettura/scrittura approfondite.

Poiché la sincronizzazione delle proprietà del dispositivo è gestita completamente tramite la libreria client di Gestione dei dispositivi dell'hub IoT di Azure, è sufficiente chiamare l'API per impostare la proprietà del dispositivo, in questo esempio il livello della batteria, a intervalli regolari. Quando il servizio esegue una lettura approfondita, viene restituito l'ultimo valore impostato. Quando il servizio esegue una lettura approfondita, viene chiamato questo metodo Set. In **iotdm\_simple\_sample.c** è disponibile un esempio:

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

Invece di usare il metodo Set, è anche possibile implementare un callback. Per altre informazioni su questa opzione, vedere l'[introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure][lnk-dm-library].

## Passaggi successivi

Per altre informazioni sulle funzionalità di Gestione dei dispositivi dell'hub IoT di Azure, vedere le esercitazioni seguenti:

- [Come trovare dispositivi gemelli tramite query][lnk-tutorial-queries]

- [Come usare processi del dispositivo per aggiornare il firmware del dispositivo][lnk-dm-jobs]

- Nelle librerie client di Gestione dei dispositivi è disponibile un esempio completo che usa un [dispositivo Intel Edison][lnk-edison].

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample

<!---HONumber=AcomDC_0518_2016-->