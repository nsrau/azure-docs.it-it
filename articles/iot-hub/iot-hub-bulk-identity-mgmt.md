<properties
 pageTitle="Importare o esportare le identità dei dispositivi dell'hub IoT| Microsoft Azure"
 description="Concetti e frammenti di codice .NET per la gestione in blocco delle identità dei dispositivi dell'hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Gestione in blocco delle identità dei dispositivi dell'hub IoT

Ogni hub IoT include un registro delle identità dei dispositivi che è possibile usare per creare risorse per i singoli dispositivi nel servizio, ad esempio una coda contenente messaggi da cloud a dispositivo in elaborazione, e per consentire l'accesso agli endpoint per il dispositivo. Questo articolo descrive come importare ed esportare in blocco le identità del dispositivo in/da un registro delle identità dei dispositivi.

Le operazioni di importazione ed esportazione vengono eseguite nel contesto di *processi* che consentono agli utenti di eseguire operazioni del servizio in blocco a fronte di un hub IoT.

La classe **RegistryManager** include i metodi **ExportDevicesAsync** e **ImportDevicesAsync** che usano il framework di **processi**. Questi metodi consentono di esportare, importare e sincronizzare un intero registro dei dispositivi dell'hub IoT.

## Informazioni sui processi

Le operazioni del registro delle identità dei dispositivi usano il sistema di gestione dei **processi** quando l'operazione:

*  Ha un tempo di esecuzione potenzialmente lungo rispetto alle operazioni di runtime standard, oppure
*  Restituisce all'utente una grande quantità di dati.

In questi casi, invece di avere una singola chiamata API in attesa o che blocca il risultato dell'operazione, quest'ultima crea in modo asincrono un **processo** per tale hub IoT e quindi restituisce immediatamente un oggetto **JobProperties**.

Il frammento di codice C# seguente mostra come creare un processo di esportazione:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

È quindi possibile usare la classe **RegistryManager** per eseguire query sullo stato del **processo** usando i metadati di **JobProperties** restituiti.

Il frammento di codice C# seguente mostra come eseguire il polling ogni cinque secondi per vedere se il processo ha terminato l'esecuzione:

```
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

## Esportare dispositivi

Usare il metodo **ExportDevicesAsync** per esportare un intero registro dei dispositivi di un hub IoT in un contenitore BLOB di [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/) con una [firma di accesso condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

Questo metodo consente di creare backup affidabili delle informazioni sui dispositivi in un contenitore BLOB che si controlla.

Il metodo **ExportDevicesAsync** richiede due parametri:

*  Una *stringa* che contiene un URI di un contenitore BLOB. Questo URI deve contenere un token di firma di accesso condiviso che concede l'accesso in scrittura al contenitore. Il processo crea un BLOB in blocchi in questo contenitore per archiviare i dati di esportazione del dispositivo serializzati. Il token di firma di accesso condiviso deve includere queste autorizzazioni:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  Oggetto *booleano* che indica se si vogliono escludere le chiavi di autenticazione dai dati di esportazione. Se **false**, le chiavi di autenticazione sono incluse nell'output di esportazione; in caso contrario, le chiavi vengono esportate come **null**.

I frammenti di codice C# seguenti illustrano come inizializzare un processo di esportazione che include chiavi di autenticazione di dispositivi nei dati di esportazione e quindi eseguire il polling del completamento:

```
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

Di seguito è riportato un esempio di dati di output:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se è necessario accedere ai dati nel codice, è possibile deserializzarli facilmente con la classe **ExportImportDevice**. Il frammento di codice C# seguente mostra come leggere le informazioni sul dispositivo esportate precedentemente in un BLOB in blocchi:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  È anche possibile usare il metodo **GetDevicesAsync** della classe **RegistryManager** per recuperare un elenco dei dispositivi. Questo approccio presenta tuttavia un limite rigido pari a un numero di 1000 oggetti dispositivo restituiti. Il caso d'uso previsto per il metodo **GetDevicesAsync** è destinato a scenari di sviluppo per facilitare il debug e non è consigliabile per i carichi di lavoro di produzione.

## Importare dispositivi

Il metodo **ImportDevicesAsync** nella classe **RegistryManager** consente di eseguire operazioni di importazione e sincronizzazione in blocco nel registro dei dispositivi di un hub IoT. In modo analogo al metodo **ExportDevicesAsync**, il metodo **ImportDevicesAsync** usa il framework **Job**.

Prestare attenzione quando si usa il metodo **ImportDevicesAsync** in quanto, oltre a eseguire il provisioning dei dispositivi nuovi nel registro delle identità dei dispositivi, può anche aggiornare ed eliminare dispositivi esistenti.

> [AZURE.WARNING]  Un'operazione di importazione non può essere annullata. È sempre consigliabile eseguire il backup dei dati esistenti usando il metodo **ExportDevicesAsync** in un altro contenitore BLOB, prima di apportare modifiche in blocco al registro delle identità dei dispositivi.

Il metodo **ImportDevicesAsync** usa due parametri:

*  Una *stringa* che contiene un URI di un contenitore BLOB di [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/) come *input* del processo. Questo URI deve contenere un token di firma di accesso condiviso che concede l'accesso in lettura al contenitore. Questo contenitore deve includere un BLOB con il nome **devices.txt** che contiene i dati serializzati del dispositivo da importare nel registro delle identità dei dispositivi. I dati di importazione devono contenere informazioni sul dispositivo nello stesso formato JSON usato dal processo **ExportImportDevice** quando viene creato il BLOB **devices.txt**. Il token di firma di accesso condiviso deve includere queste autorizzazioni:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  Una *stringa* che contiene un URI di un contenitore BLOB di [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/) come *output* del processo. Il processo crea un BLOB in blocchi in questo contenitore per archiviare qualsiasi informazione sull'errore dal **processo** di importazione completato. Il token di firma di accesso condiviso deve includere queste autorizzazioni:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  I due parametri possono puntare allo stesso contenitore BLOB. I parametri separati consentono semplicemente un maggiore controllo dei dati, perché il contenitore di output richiede autorizzazioni aggiuntive.

Il frammento di codice C# seguente mostra come avviare un processo di importazione:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## Importare il comportamento

È possibile usare il metodo **ImportDevicesAsync** per eseguire le operazioni in blocco seguenti nel registro delle identità dei dispositivi:

-   Registrazione in blocco di nuovi dispositivi
-   Eliminazioni in blocco dei dispositivi esistenti
-   Modifiche dello stato in blocco (abilitare o disabilitare dispositivi)
-   Assegnazione in blocco di nuove chiavi di autenticazione del dispositivo
-   Rigenerazione automatica in blocco di chiavi di autenticazione del dispositivo

È possibile eseguire una combinazione qualsiasi delle operazioni precedenti in un'unica chiamata **ImportDevicesAsync**. Ad esempio, è possibile registrare nuovi dispositivi ed eliminare o aggiornare contemporaneamente quelli esistenti. Insieme con il metodo **ExportDevicesAsync**, è possibile eseguire la migrazione completa di tutti i dispositivi da un hub IoT all'altro.

È possibile controllare il processo di importazione per dispositivo con la proprietà facoltativa **importMode** nei dati di serializzazione dell'importazione per ogni dispositivo. La proprietà **importMode** include le opzioni seguenti:

| importMode | Descrizione |
| -------- | ----------- |
| **createOrUpdate** | Se non esiste un dispositivo con l'**ID** specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, le informazioni esistenti vengono sovrascritte con i dati di input specificati senza tener conto del valore **ETag**. |
| **create** | Se non esiste un dispositivo con l'**ID** specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, viene scritto un errore nel file di log. |
| **update** | Se esiste già un dispositivo con l'**ID** specificato, le informazioni esistenti vengono sovrascritte con i dati di input specificati senza tener conto del valore **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. |
| **updateIfMatchETag** | Se esiste già un dispositivo con l'**ID** specificato, le informazioni esistenti vengono sovrascritte con i dati di input specificati solo se viene rilevata una corrispondenza con **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. <br/>In caso di mancata corrispondenza con **ETag**, viene scritto un errore nel file di log. |
| **createOrUpdateIfMatchETag** | Se non esiste un dispositivo con l'**ID** specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, le informazioni esistenti vengono sovrascritte con i dati di input specificati solo se viene rilevata una corrispondenza con **ETag**. <br/>In caso di mancata corrispondenza con **ETag**, viene scritto un errore nel file di log. |
| **delete** | Se esiste già un dispositivo con l'**ID** specificato, viene eliminato senza tener conto del valore **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. |
| **deleteIfMatchETag** | Se esiste già un dispositivo con l'**ID** specificato, viene eliminato solo se viene rilevata una corrispondenza con **ETag**. Se il dispositivo non esiste, viene scritto un errore nel file di log. <br/>In caso di mancata corrispondenza con ETag, viene scritto un errore nel file di log. |

> [AZURE.NOTE] Se i dati di serializzazione non definiscono in modo esplicito un flag **importMode** per un dispositivo, durante l'operazione di importazione l'impostazione predefinita è **createOrUpdate**.

## Importare dispositivi: esempio di provisioning dei dispositivi in blocco 

L'esempio di codice C# seguente illustra come generare più identità dei dispositivi che includono chiavi di autenticazione, scrivere le informazioni sul dispositivo in un BLOB in blocchi di Archiviazione di Azure e quindi importare i dispositivi nel registro delle identità dei dispositivi:

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
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

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
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

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
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

## Importare dispositivi: esempio di eliminazione in blocco

L'esempio di codice seguente illustra come eliminare i dispositivi aggiunti con l'esempio di codice precedente:

```
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

// Step 3: Call import using the same storage blob to delete all devices!
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

## Recupero dell'URI di firma di accesso condiviso del contenitore


Il codice di esempio seguente illustra come generare un [URI di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-2.md) con autorizzazioni di lettura, scrittura ed eliminazione per un contenitore BLOB:

```
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

## Passaggi successivi

In questo articolo si è appreso come eseguire operazioni in blocco sul registro delle identità dei dispositivi in un hub IoT. Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

- [Metriche di utilizzo][lnk-metrics]
- [Monitoraggio delle operazioni][lnk-monitor]
- [Gestire l'accesso all'hub IoT][lnk-itpro]

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0713_2016-->