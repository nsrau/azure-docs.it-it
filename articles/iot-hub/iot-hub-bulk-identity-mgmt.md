---
title: "Importare o esportare le identità dei dispositivi dell'hub IoT di Azure | Microsoft Docs"
description: "Come usare Azure IoT SDK per servizi per eseguire operazioni in blocco sul registro delle identità per importare ed esportare le identità dei dispositivi. Le operazioni di importazione consentono di creare, aggiornare ed eliminare in blocco le identità dei dispositivi."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ad2c6d585eef5450f7f0912ffa4753fe80d86b37
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Gestire in blocco le identità dei dispositivi dell'hub IoT

Ogni hub IoT ha un registro delle identità che è possibile usare per creare le risorse per ogni dispositivo nel servizio. e per consentire di controllare gli accessi agli endpoint per il dispositivo. Questo articolo descrive come importare ed esportare in blocco le identità del dispositivo in/da un registro delle identità.

Le operazioni di importazione ed esportazione vengono eseguite nel contesto di *processi* che consentono di eseguire operazioni del servizio in blocco a fronte di un hub IoT.

La classe **RegistryManager** include i metodi **ExportDevicesAsync** e **ImportDevicesAsync** che usano il framework di **processi**. Questi metodi consentono di esportare, importare e sincronizzare un intero registro delle identità dell'hub IoT.

## <a name="what-are-jobs"></a>Informazioni sui processi

Le operazioni del registro delle identità usano il sistema di gestione dei **processi** quando l'operazione:

* Ha un tempo di esecuzione potenzialmente lungo rispetto alle operazioni di runtime standard.
* Restituisce all'utente una grande quantità di dati.

Invece di avere una singola chiamata API in attesa o che blocca il risultato dell'operazione, quest'ultima crea in modo asincrono un **processo** per tale hub IoT, quindi restituisce immediatamente un oggetto **JobProperties**.

Il frammento di codice C# seguente mostra come creare un processo di esportazione:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Per usare la classe il **RegistryManager** nel codice c#, aggiungere il pacchetto NuGet **Microsoft.Azure.Devices** al progetto. La classe **RegistryManager** si trova nello spazio dei nomi **Microsoft.Azure.Devices**.

È possibile usare la classe **RegistryManager** per eseguire query sullo stato del **processo** usando i metadati di **JobProperties** restituiti.

Il frammento di codice C# seguente mostra come eseguire il polling ogni cinque secondi per vedere se il processo ha terminato l'esecuzione:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Esportare dispositivi

Usare il metodo **ExportDevicesAsync** per esportare un intero registro delle identità di un hub IoT in un contenitore BLOB di [Archiviazione di Azure](../storage/index.md) con una [firma di accesso condiviso](../storage/common/storage-security-guide.md#data-plane-security).

Questo metodo consente di creare backup affidabili delle informazioni sui dispositivi in un contenitore BLOB che si controlla.

Il metodo **ExportDevicesAsync** richiede due parametri:

* Una *stringa* che contiene un URI di un contenitore BLOB. Questo URI deve contenere un token di firma di accesso condiviso che concede l'accesso in scrittura al contenitore. Il processo crea un BLOB in blocchi in questo contenitore per archiviare i dati di esportazione del dispositivo serializzati. Il token di firma di accesso condiviso deve includere queste autorizzazioni:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* Oggetto *booleano* che indica se si vogliono escludere le chiavi di autenticazione dai dati di esportazione. Se il valore è **false**, le chiavi di autenticazione sono incluse nell'output di esportazione. In caso contrario, le chiavi vengono esportate come **null**.

I frammenti di codice C# seguenti illustrano come inizializzare un processo di esportazione che include chiavi di autenticazione di dispositivi nei dati di esportazione e quindi eseguire il polling del completamento:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Il processo archivia l'output nel contenitore BLOB specificato come BLOB in blocchi con il nome **devices.txt**. I dati di output sono costituiti da dati del dispositivo serializzati in formato JSON, con un dispositivo per ogni riga.

Nell'esempio seguente vengono descritti i dati di output:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se un dispositivo contiene dati gemelli, allora anche i dati gemelli vengono esportati con i dati del dispositivo. Questo formato è illustrato nell'esempio seguente. Tutti i dati dalla riga di "twinETag" fino alla fine sono dati gemelli.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Se è necessario accedere ai dati nel codice, è possibile deserializzarli facilmente con la classe **ExportImportDevice** . Il frammento di codice C# seguente mostra come leggere le informazioni sul dispositivo esportate precedentemente in un BLOB in blocchi:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
> È anche possibile usare il metodo **GetDevicesAsync** della classe **RegistryManager** per recuperare un elenco dei dispositivi. Questo approccio presenta tuttavia un limite rigido pari a un numero di 1000 oggetti dispositivo restituiti. Il caso d'uso previsto per il metodo **GetDevicesAsync** è destinato a scenari di sviluppo per facilitare il debug e non è consigliabile per i carichi di lavoro di produzione.

## <a name="import-devices"></a>Importare dispositivi

Il metodo **ImportDevicesAsync** nella classe **RegistryManager** consente di eseguire operazioni di importazione e sincronizzazione in blocco nel registro delle identità di un hub IoT. In modo analogo al metodo **ExportDevicesAsync**, il metodo **ImportDevicesAsync** usa il framework **Job**.

Prestare attenzione quando si usa il metodo **ImportDevicesAsync** in quanto, oltre a eseguire il provisioning dei dispositivi nuovi nel registro delle identità, può anche aggiornare ed eliminare dispositivi esistenti.

> [!WARNING]
> Un'operazione di importazione non può essere annullata. Eseguire sempre il backup dei dati esistenti usando il metodo **ExportDevicesAsync** in un altro contenitore BLOB, prima di apportare modifiche in blocco al registro delle identità.

Il metodo **ImportDevicesAsync** usa due parametri:

* Una *stringa* che contiene un URI di un contenitore BLOB di [Archiviazione di Azure](../storage/index.md) come *input* del processo. Questo URI deve contenere un token di firma di accesso condiviso che concede l'accesso in lettura al contenitore. Questo contenitore deve includere un BLOB con il nome **devices.txt** che contiene i dati serializzati del dispositivo da importare nel registro delle identità. I dati di importazione devono contenere informazioni sul dispositivo nello stesso formato JSON usato dal processo **ExportImportDevice** quando viene creato il BLOB **devices.txt**. Il token di firma di accesso condiviso deve includere queste autorizzazioni:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* Una *stringa* che contiene un URI di un contenitore BLOB di [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/) da usare come *output* del processo. Il processo crea un BLOB in blocchi in questo contenitore per archiviare qualsiasi informazione sull'errore dal **processo**di importazione completato. Il token di firma di accesso condiviso deve includere queste autorizzazioni:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> I due parametri possono puntare allo stesso contenitore BLOB. I parametri separati consentono semplicemente un maggiore controllo dei dati, perché il contenitore di output richiede autorizzazioni aggiuntive.

Il frammento di codice C# seguente mostra come avviare un processo di importazione:

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Questo metodo può essere usato anche per importare i dati per il dispositivo gemello. Il formato per i dati di input è uguale al formato visualizzato nella sezione **ExportDevicesAsync**. In questo modo, è possibile reimportare i dati esportati. Il **$metadata** è facoltativo.

## <a name="import-behavior"></a>Importare il comportamento

È possibile usare il metodo **ImportDevicesAsync** per eseguire le operazioni in blocco seguenti nel registro delle identità:

* Registrazione in blocco di nuovi dispositivi
* Eliminazioni in blocco dei dispositivi esistenti
* Modifiche dello stato in blocco (abilitare o disabilitare dispositivi)
* Assegnazione in blocco di nuove chiavi di autenticazione del dispositivo
* Rigenerazione automatica in blocco di chiavi di autenticazione del dispositivo
* Aggiornamento bulk dei dati gemelli

È possibile eseguire una combinazione qualsiasi delle operazioni precedenti in un'unica chiamata **ImportDevicesAsync** . Ad esempio, è possibile registrare nuovi dispositivi ed eliminare o aggiornare contemporaneamente quelli esistenti. Insieme con il metodo **ExportDevicesAsync** , è possibile eseguire la migrazione completa di tutti i dispositivi da un hub IoT all'altro.

Se il file di importazione include metadati gemelli, questi metadati sovrascrivono i metadati gemelli esistenti. Se il file di importazione non include i metadati gemelli, allora solo i metadati `lastUpdateTime` vengono aggiornati usando l'ora corrente.

Usare la proprietà facoltativa **importMode** nei dati di serializzazione dell'importazione per ogni dispositivo per controllare il processo di importazione per dispositivo. La proprietà **importMode** include le opzioni seguenti:

| importMode | Descrizione |
| --- | --- |
| **createOrUpdate** |Se non esiste un dispositivo con l' **ID**specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, le informazioni esistenti vengono sovrascritte con i dati di input specificati senza tener conto del valore **ETag** . <br> L'utente può facoltativamente specificare i dati gemelli con i dati del dispositivo. L'ETag del gemello, se specificato, viene elaborato in modo indipendente dal valore etag del dispositivo. Se è presente una mancata corrispondenza con l'etag del gemello esistente, viene scritto un errore nel file di log. |
| **create** |Se non esiste un dispositivo con l' **ID**specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, viene scritto un errore nel file di log. <br> L'utente può facoltativamente specificare i dati gemelli con i dati del dispositivo. L'ETag del gemello, se specificato, viene elaborato in modo indipendente dal valore etag del dispositivo. Se è presente una mancata corrispondenza con l'etag del gemello esistente, viene scritto un errore nel file di log. |
| **update** |Se esiste già un dispositivo con l'**ID** specificato, le informazioni esistenti vengono sovrascritte con i dati di input specificati senza tener conto del valore **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. |
| **updateIfMatchETag** |Se esiste già un dispositivo con l'**ID** specificato, le informazioni esistenti vengono sovrascritte con i dati di input specificati solo se viene rilevata una corrispondenza con **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. <br/>In caso di mancata corrispondenza con **ETag** , viene scritto un errore nel file di log. |
| **createOrUpdateIfMatchETag** |Se non esiste un dispositivo con l' **ID**specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, le informazioni esistenti vengono sovrascritte con i dati di input specificati solo se viene rilevata una corrispondenza con **ETag** . <br/>In caso di mancata corrispondenza con **ETag** , viene scritto un errore nel file di log. <br> L'utente può facoltativamente specificare i dati gemelli con i dati del dispositivo. L'ETag del gemello, se specificato, viene elaborato in modo indipendente dal valore etag del dispositivo. Se è presente una mancata corrispondenza con l'etag del gemello esistente, viene scritto un errore nel file di log. |
| **delete** |Se esiste già un dispositivo con l'**ID** specificato, viene eliminato senza tener conto del valore **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. |
| **deleteIfMatchETag** |Se esiste già un dispositivo con l'**ID** specificato, viene eliminato solo se viene rilevata una corrispondenza con **ETag**. Se il dispositivo non esiste, viene scritto un errore nel file di log. <br/>In caso di mancata corrispondenza con ETag, viene scritto un errore nel file di log. |

> [!NOTE]
> Se i dati di serializzazione non definiscono in modo esplicito un flag **importMode** per un dispositivo, durante l'operazione di importazione l'impostazione predefinita è **createOrUpdate**.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importare dispositivi: esempio di provisioning dei dispositivi in blocco

Il campione di codice C# seguente illustra come generare più identità dei dispositivi che:

* Includono chiavi di autenticazione.
* Scrivono le informazioni del dispositivo in un BLOB in blocchi.
* Importano i dispositivi nel registro delle identità.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Importare dispositivi: esempio di eliminazione in blocco

L'esempio di codice seguente illustra come eliminare i dispositivi aggiunti con l'esempio di codice precedente:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Recuperare l'URI di firma di accesso condiviso del contenitore

Il codice di esempio seguente illustra come generare un [URI di firma di accesso condiviso](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) con autorizzazioni di lettura, scrittura ed eliminazione per un contenitore BLOB:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire operazioni in blocco sul registro delle identità in un hub IoT. Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Metriche di Hub IoT][lnk-metrics]
* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Edge][lnk-iotedge]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

