---
title: Processi del dispositivo in Gestione dei dispositivi dell'hub IoT | Microsoft Docs
description: Esercitazione relativa alla funzionalità Gestione dei dispositivi dell'hub IoT di Azure che descrive come usare i processi del dispositivo per eseguire operazioni come l'aggiornamento del firmware remoto.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# Esercitazione: Come usare processi del dispositivo per aggiornare il firmware del dispositivo (anteprima)
[!INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## Introduzione
Gestione dei dispositivi dell'hub IoT di Azure consente di interagire con dispositivi fisici tramite processi del dispositivo. Dopo aver identificato i dispositivi gemelli, ovvero la rappresentazione di servizio di un dispositivo fisico, è possibile interagire con il dispositivo fisico corrispondente usando processi del dispositivo. I processi del dispositivo consentono il coordinamento di processi complessi su più dispositivi. Questo processo può includere più passaggi e operazioni di lunga durata.

Al momento esistono sei tipi di processi del dispositivo forniti da Gestione dei dispositivi dell'hub IoT di Azure. Ne verranno aggiunti altri in futuro in base alle esigenze dei clienti.

* **Aggiornamento del firmware**: aggiorna il firmware, o l'immagine del sistema operativo, nel dispositivo fisico.
* **Riavvio**: riavvia il dispositivo fisico.
* **Ripristino delle impostazioni predefinite**: ripristina il firmware, o l'immagine del sistema operativo, a un'immagine di backup predefinita archiviata nel dispositivo.
* **Aggiornamento della configurazione**: configura l'agente client dell'hub IoT in esecuzione nel dispositivo fisico.
* **Lettura proprietà del dispositivo**: ottiene il valore più recente di una proprietà del dispositivo nel dispositivo fisico.
* **Scrittura proprietà del dispositivo:** modifica una proprietà del dispositivo nel dispositivo fisico.

Per informazioni dettagliate su come usare ognuno di questi processi, vedere la [documentazione dell'API][lnk-apidocs].

Per informazioni sullo stato dei processi che sono stati avviati, è possibile eseguire una query sulla cronologia processi. Per alcune query di esempio, vedere la [libreria di espressioni di query][lnk-query-samples].

## Uso di processi del dispositivo per eseguire l'aggiornamento del firmware: architettura
Il diagramma seguente illustra un processo del dispositivo per l'aggiornamento del firmware che interagisce con un singolo dispositivo fisico.

![][img-architecture]

Passaggi del processo del dispositivo per l'aggiornamento del firmware:

1. La soluzione IoT basata sul cloud avvia il processo del dispositivo per aggiornamento del firmware e fornisce l'URI per il percorso del pacchetto del firmware. È responsabilità della soluzione IoT inserire il pacchetto del firmware in una posizione da cui il dispositivo possa scaricarlo.
2. Quando il dispositivo fisico riceve questo URI, avvia automaticamente il download dall'URI specificato.
3. Il codice nel dispositivo riceve la richiesta dall'hub IoT e scarica il pacchetto del firmware dal percorso URI specificato.
4. Dopo aver ricevuto il messaggio di stato del dispositivo che il download è stato completato, il processo del dispositivo indica al dispositivo di applicare l'immagine del firmware scaricato.
5. Dopo che il dispositivo ha applicato l'immagine del firmware, si riavvia, si riconnette all'hub IoT e lo informa che è stato applicato il nuovo firmware e che il processo del dispositivo è quindi completato.

## Esecuzione dell'esempio di aggiornamento del firmware
L'esempio seguente estende la funzionalità dell'esercitazione [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started]. Una volta che i diversi dispositivi simulati sono in esecuzione, viene avviato un processo per aggiornare il firmware su ognuno di essi.

### Prerequisiti
Prima di eseguire questo esempio, è necessario aver completato le procedure illustrate in [Introduzione a Gestione dei dispositivi dell'hub IoT di Azure][lnk-get-started]. I dispositivi simulati devono quindi essere in esecuzione. Se il processo è già stato completato in precedenza, riavviare i dispositivi simulati adesso.

### Avvio dell'esempio
Per avviare l'esempio, è necessario eseguire il processo **FirmwareUpdate.exe**. Verrà avviato il processo di aggiornamento del firmware su tutti i dispositivi simulati. Per avviare l'esempio, seguire questa procedura:

1. Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla cartella **azure-iot-sdks\\csharp\\service\\samples\\bin**.
2. Eseguire `FirmwareUpdate.exe <IoT Hub Connection String>`

Verrà visualizzato l'output nella finestra della riga di comando che mostra sette processi del dispositivo che rilevano l'aggiornamento del firmware simulato sui sei dispositivi simulati.

### Avvio di un processo del dispositivo
In generale, i processi del dispositivo vengono avviati usando diversi metodi **Schedule&lt;JobName&gt;Async** sull'istanza di **JobClient**. Nell'esempio di aggiornamento del firmware viene chiamato il metodo **ScheduleFirmwareUpdateAsync**. Poiché si passa una matrice con 6 ID dispositivo, vengono avviati 7 processi del dispositivo, uno per ogni dispositivo da aggiornare e un processo del dispositivo padre usato per tenere traccia degli altri 6.

Nel frammento seguente viene avviato un processo di aggiornamento del firmware da **Program.cs** nel progetto **FirmwareUpdate**. La chiamata accetta una matrice di stringhe con valori **deviceId** come parametro, che rappresentano i dispositivi da aggiornare.

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### Esecuzione di query sulla cronologia processo
Nell'esempio viene visualizzato periodicamente un elenco di dispositivi che eseguono un processo del dispositivo. Al termine di un processo del dispositivo, il dispositivo associato non viene più visualizzato nell'elenco. La figura seguente è una schermata di **FirmwareUpdate.exe** in esecuzione:

![][img-output1]

L'elenco precedente viene generato eseguendo una query per tutti i processi attivi, come illustrato nel frammento di codice seguente da **Program.cs** del progetto **FirmwareUpdate**:

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

Una volta completato il processo del dispositivo padre, l'esempio genera un elenco di tutti i processi del dispositivo, come illustrato nella figura seguente: Il processo padre viene completato solo dopo il completamento di tutti i processi figlio associati. Nella schermata seguente il processo padre è l'ultimo processo nell'elenco e lo si capisce perché il valore di **ParentJobId** è **''**. Il processo padre include anche il campo **Motivo** impostato su una stringa che indica il risultato di aggregazione della query. In questo caso mostra che l'aggiornamento è riuscito su tutti e 6 i dispositivi.

![][img-output2]

L'elenco precedente viene generato eseguendo una query per tutti i processi ordinati in base all'ora di inizio. È possibile usare qualsiasi proprietà dell'oggetto **JobResponse**, illustrato di seguito, per eseguire query sulla cronologia dei processi del dispositivo.

![][img-properties]

Per altri esempi di query sulla cronologia dei processi del dispositivo vedere la [libreria di espressioni di query][lnk-query-samples].

### Dettagli di implementazione del simulatore di dispositivi
Nelle sezioni precedenti sono illustrati i dettagli di implementazione dell'aggiornamento del firmware dal punto di vista del servizio, ovvero come avviare un processo del dispositivo ed eseguire query per il visualizzare il relativo stato. A questo punto verrà illustrata l'implementazione corrispondente sul lato del dispositivo.

La libreria client di Gestione dei dispositivi dell'hub IoT di Azure gestisce la comunicazione tra il dispositivo e il servizio, quindi resta solo da implementare la logica specifica del dispositivo che prevede due parti:

* Implementare il processo di aggiornamento del firmware specifico del dispositivo: richiede di scrivere la logica specifica del dispositivo per scaricare il pacchetto del firmware e applicare l'aggiornamento nei callback appropriati elencati di seguito. Nell'esempio simulato questo processo viene implementato in [questo esempio][lnk-github-firmware]\:
  
  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```
* Informare il servizio del processo di aggiornamento del firmware: comporta la modifica dello stato di aggiornamento del firmware e dei campi del risultato di aggiornamento del firmware. A questo scopo chiamare le API **set\_firmwareupdate\_state** e **set\_firmwareupdate\_updateresult**. Nell'esempio simulato questo processo viene implementato in [questo esempio][lnk-github-firmware].

## Passaggi successivi
Per altre informazioni sulle funzionalità di Gestione dei dispositivi dell'hub IoT di Azure, vedere le esercitazioni seguenti:

* [Abilitare i dispositivi gestiti protetti da un gateway IoT][lnk-dm-gateway]
* [Introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure][lnk-library-c]
* Nella libreria client di Gestione dei dispositivi dell'hub IoT è disponibile un esempio completo che usa un [dispositivo Intel Edison][lnk-edison].

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

* [Progettare una soluzione][lnk-design]
* [Guida per sviluppatori][lnk-devguide]
* [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
* [Utilizzo del portale di Azure per gestire l'hub IoT][lnk-portal]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->