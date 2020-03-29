---
title: Archiviare BLOB in blocchi nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Per informazioni sulla suddivisione in livelli e sulle funzionalità temporpere, vedere Operazioni di archiviazione BLOB supportate e connettersi all'account di archiviazione BLOB.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509819"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge

Archiviazione BLOB di Azure in IoT Edge offre una soluzione di archiviazione BLOB e [BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) in [blocco](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) sul perimetro. Un modulo di archiviazione BLOB nel dispositivo IoT Edge si comporta come un servizio BLOB di Azure, ad eccezione del fatto che i BLOB vengono archiviati in locale nel dispositivo IoT Edge.A blob storage module on your IoT Edge device behaves like an Azure blob service, except the blobs are stored locally on your IoT Edge device. È possibile accedere ai BLOB usando gli stessi metodi di Azure Storage SDK o le stesse chiamate API BLOB a cui si è già usati. Questo articolo illustra i concetti relativi all'archiviazione BLOB di Azure nel contenitore IoT Edge che esegue un servizio BLOB nel dispositivo IoT Edge.This article explains the concepts related to Azure Blob Storage on IoT Edge container that runs a blob service on your IoT Edge device.

Questo modulo è utile negli scenari:This module is useful in scenarios:

* dove i dati devono essere archiviati localmente fino a quando non possono essere elaborati o trasferiti al cloud. Questi dati possono essere video, immagini, dati finanziari, dati ospedalieri o qualsiasi altro dato non strutturato.
* quando i dispositivi si trovano in un luogo con connettività limitata.
* quando si desidera elaborare in modo efficiente i dati in modo locale per ottenere un accesso a bassa latenza ai dati, in modo che sia possibile rispondere alle emergenze il più rapidamente possibile.
* quando si desidera ridurre i costi della larghezza di banda ed evitare di trasferire terabyte di dati nel cloud. È possibile elaborare i dati in locale e inviare solo i dati elaborati al cloud.

Guarda il video per una rapida introduzione
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Questo modulo include le funzionalità **deviceToCloudUpload** e **deviceAutoDelete.**

**deviceToCloudUpload** è una funzionalità configurabile. Questa funzione carica automaticamente i dati dall'archiviazione BLOB locale in Azure con supporto intermittente per la connettività Internet. Consente di:

* Attivare/disattivare la funzionalità deviceToCloudUpload.
* Scegliere l'ordine in cui i dati vengono copiati in Azure, ad esempio NewestFirst o OldestFirst.Choose the order in which the data is copied to Azure like NewestFirst or OldestFirst.
* Specificare l'account di archiviazione di Azure in cui si vogliono caricare i dati.
* Specificare i contenitori da caricare in Azure.Specify the containers you want to upload to Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione.
* Scegliere la possibilità di eliminare immediatamente i BLOB al termine del caricamento nell'archiviazione cloudChoose the ability to delete the blobs immediately, after upload to cloud storage is finished
* Eseguire il caricamento `Put Blob` completo del BLOB (tramite `Put Block List` `Append Block` l'operazione) e il caricamento a livello di blocco (tramite `Put Block`operazioni e operazioni).

Questo modulo usa il caricamento a livello di blocco, quando il BLOB è costituito da blocchi. Ecco alcuni degli scenari comuni:

* L'applicazione aggiorna alcuni blocchi di un BLOB di blocchi caricato in precedenza o aggiunge nuovi blocchi a un BLOB di accodamento, questo modulo carica solo i blocchi aggiornati e non l'intero BLOB.
* Il modulo sta caricando BLOB e la connessione Internet scompare, quando la connettività è di nuovo carica solo i blocchi rimanenti e non l'intero BLOB.

Se si verifica una terminazione imprevista del processo (ad esempio un'interruzione dell'alimentazione) durante il caricamento di un BLOB, tutti i blocchi dovuti per il caricamento verranno caricati nuovamente una volta che il modulo torna online.

**deviceAutoDelete** è una funzionalità configurabile. Questa funzione elimina automaticamente i BLOB dall'archiviazione locale alla scadenza della durata specificata (misurata in minuti). Consente di:

* Attivare/disattivare la funzionalità DeviceAutoDelete.
* Specificare il tempo in minuti (deleteAfterMinutes) dopo il quale i BLOB verranno eliminati automaticamente.
* Scegliere la possibilità di mantenere il BLOB durante il caricamento in caso di scadenza del valore deleteAfterMinutes.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare la macchina di sviluppo o una macchina virtuale come dispositivo IoT Edge seguendo i passaggi della guida introduttiva per i dispositivi [Linux](quickstart-linux.md) o [Windows.](quickstart.md)

* Fare riferimento a [sistemi supportati da Azure IoT Edge](support.md#operating-systems) per un elenco delle architetture e dei sistemi operativi supportati. Il modulo Archiviazione BLOB di Azure in IoT Edge supporta le architetture seguenti:The Azure Blob Storage on IoT Edge module supports following architectures:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (anteprima)

Risorse cloud:

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload e deviceAutoDelete proprietà

Utilizzare le proprietà desiderate del modulo per impostare **deviceToCloudUploadProperties** e **deviceAutoDeleteProperties**. Le proprietà desiderate possono essere impostate durante la distribuzione o modificate in un secondo momento modificando il modulo gemello senza dover ridistribuire il modulo. Si consiglia di controllare il `reported configuration` `configurationValidation` "Module Twin" per e per assicurarsi che i valori vengano propagati correttamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties (proprietà deviceToCloudUploadProperties)

Il nome di `deviceToCloudUploadProperties`questa impostazione è . Se si usa il simulatore IoT Edge, impostare i valori sulle variabili di ambiente correlate per queste proprietà, disponibili nella sezione relativa alla spiegazione.

| Proprietà | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| uploadOn | true, false | Impostare `false` su per impostazione predefinita. Se si desidera attivare la funzione, `true`impostare questo campo su . <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Consente di scegliere l'ordine in cui i dati vengono copiati in Azure. Impostare `OldestFirst` su per impostazione predefinita. L'ordine è determinato dall'ora dell'ultima modifica del BLOB. <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`è una stringa di connessione che consente di specificare l'account di archiviazione in cui si vogliono caricare i dati. `Azure Storage Account Name`Specificare `Azure Storage Account Key` `End point suffix`, , . Aggiungere endpointSuffix appropriato di Azure in cui verranno caricati i dati, che varia per Global Azure, Government Azure e Microsoft Azure Stack.Add appropriate EndpointSuffix of Azure where data will be uploaded, it varies for Global Azure, Government Azure, and Microsoft Azure Stack. <br><br> È possibile scegliere di specificare qui la stringa di connessione SAS di Archiviazione di Azure.You can choose to specify Azure Storage SAS connection string here. Ma è necessario aggiornare questa proprietà alla scadenza. <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Consente di specificare i nomi dei contenitori che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione. Se non si specifica il nome del contenitore di `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`destinazione, verrà assegnato automaticamente il nome del contenitore come . È possibile creare stringhe modello per il nome del contenitore di destinazione, controllare la colonna dei valori possibili. <br>%h -> nome dell'hub IoT (3-50 caratteri). <br>%d ->ID dispositivo Edge IoT (da 1 a 129 caratteri). <br>%m -> Nome modulo (da 1 a 64 caratteri). <br>%c -> nome del contenitore di origine (da 3 a 63 caratteri). <br><br>La dimensione massima del nome del contenitore è di 63 caratteri, mentre l'assegnazione automatica del nome del contenitore di destinazione se la dimensione del contenitore supera i 63 caratteri, verrà ritagliato ogni sezione (IoTHubDeviceID, IotEdgeDeviceID, ModuleName, SourceContainerName) a 15 personaggi. <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload (eliminareDopocaricamento) | true, false | Impostare `false` su per impostazione predefinita. Quando è impostato `true`su , eliminerà automaticamente i dati al termine del caricamento nell'archiviazione cloud. <br><br> **AVVISO**: se si usano BLOB di accodamento, questa impostazione eliminerà i BLOB aggiunti dall'archiviazione locale dopo un caricamento riuscito e tutte le operazioni Future di Blocco di accodamento a tali BLOB avranno esito negativo. Utilizzare questa impostazione con cautela, non abilitare questa opzione se l'applicazione esegue operazioni di accodamento poco frequenti o non supporta le operazioni di aggiunta continua<br><br> Variabile di `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`ambiente: . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Il nome di `deviceAutoDeleteProperties`questa impostazione è . Se si usa il simulatore IoT Edge, impostare i valori sulle variabili di ambiente correlate per queste proprietà, disponibili nella sezione relativa alla spiegazione.

| Proprietà | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| deleteOn | true, false | Impostare `false` su per impostazione predefinita. Se si desidera attivare la funzione, `true`impostare questo campo su . <br><br> Variabile di ambiente: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes (eliminaDueminuti) | `<minutes>` | Specificare il tempo in minuti. Il modulo eliminerà automaticamente i BLOB dall'archiviazione locale alla scadenza di questo valore. <br><br> Variabile di ambiente: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading (mantieniWhileUploading) | true, false | Per impostazione predefinita `true`è impostato su , che manterrà il BLOB durante il caricamento nell'archiviazione cloud se deleteAfterMinutes scade. È possibile impostarlo `false` su ed eliminerà i dati non appena deleteAfterMinutes scade. Nota: affinché questa proprietà funzioni uploadOn deve essere impostata su true.  <br><br> **AVVISO**: se si usano BLOB di accodamento, questa impostazione eliminerà i BLOB aggiunti dall'archiviazione locale alla scadenza del valore e tutte le operazioni Future di Blocco di accodamento a tali BLOB avranno esito negativo. È possibile assicurarsi che il valore di scadenza sia sufficientemente grande per la frequenza prevista delle operazioni di accodamento eseguite dall'applicazione.<br><br> Variabile di ambiente: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Utilizzo della condivisione SMB come archivio locale

È possibile fornire la condivisione SMB come percorso di archiviazione locale, quando si distribuisce il contenitore Di Windows di questo modulo nell'host Windows.You can provide SMB share as your local storage path, when you deploy Windows container of this module on Windows host.

Assicurarsi che la condivisione SMB e il dispositivo IoT si trovino in domini reciprocamente attendibili.

È possibile `New-SmbGlobalMapping` eseguire il comando PowerShell per eseguire il mapping della condivisione SMB in locale nel dispositivo IoT che esegue Windows.You can run PowerShell command to map the SMB share locally on the IoT device running Windows.

Di seguito sono riportati i passaggi di configurazione:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Ad esempio:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Questo comando utilizzerà le credenziali per l'autenticazione con il server SMB remoto. Quindi, mappa il percorso di condivisione remota alla lettera di unità G: (può essere qualsiasi altra lettera di unità disponibile). Il dispositivo IoT ora ha il volume di dati mappato a un percorso nell'unità G:.

Assicurarsi che l'utente nel dispositivo IoT possa leggere/scrivere nella condivisione SMB remota.

Per la distribuzione `<storage mount>` il valore di può essere **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Concessione dell'accesso alla directory all'utente contenitore in LinuxGranting directory access to container user on Linux

Se è stato usato il [montaggio](https://docs.docker.com/storage/volumes/) del volume per l'archiviazione nelle opzioni di creazione per i contenitori Linux, non è necessario eseguire alcuna procedura aggiuntiva, ma se è stato usato il montaggio di [binding,](https://docs.docker.com/storage/bind-mounts/) questi passaggi sono necessari per eseguire correttamente il servizio.

Seguendo il principio del privilegio minimo per limitare i diritti di accesso per gli utenti alle autorizzazioni minime necessarie per eseguire il proprio lavoro, questo modulo include un utente (nome: absie, ID: 11000) e un gruppo di utenti (nome: absie, ID: 11000). Se il contenitore viene avviato come **root** (l'utente predefinito è **root**), il servizio verrà avviato come utente ad **disinvolgimento** con privilegi limitati.

Questo comportamento rende la configurazione delle autorizzazioni sul percorso host è fondamentale per il corretto funzionamento del servizio, altrimenti il servizio si arresterà in modo anomalo con errori di accesso negato. Il percorso utilizzato nell'associazione di directory deve essere accessibile dall'utente contenitore (ad esempio: absie 11000). È possibile concedere all'utente contenitore l'accesso alla directory eseguendo i comandi seguenti nell'host:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Ad esempio:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Se è necessario eseguire il servizio come utente diverso da **absie**, è possibile specificare l'ID utente personalizzato in createOptions nella proprietà "User" nel manifesto di distribuzione. In tal caso è necessario utilizzare `0`l'ID del gruppo radice o predefinito .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

A questo punto, concedere all'utente contenitore l'accesso alla directory

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Configurare i file di registro

Per informazioni sulla configurazione dei file di registro per il modulo, vedere queste [procedure consigliate](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)di produzione .

## <a name="connect-to-your-blob-storage-module"></a>Connettere il modulo di archiviazione BLOB

È possibile usare il nome dell'account e la chiave dell'account configurati per il modulo per accedere all'archiviazione BLOB nel dispositivo IoT Edge.

Specificare il dispositivo IoT Edge come endpoint BLOB per qualsiasi richiesta di archiviazione effettuata. È possibile [Creare una stringa di connessione per un endpoint di archiviazione esplicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando le informazioni del dispositivo IoT Edge e il nome dell'account che è stato configurato.

* Per i moduli distribuiti nello stesso dispositivo in cui è in esecuzione il modulo Archiviazione `http://<module name>:11002/<account name>`BLOB di Azure in Edge IoT, l'endpoint BLOB è: .
* Per i moduli o le applicazioni in esecuzione su un dispositivo diverso, è necessario scegliere l'endpoint corretto per la rete. A seconda della configurazione di rete, scegliere un formato di endpoint in modo che il traffico dati dal modulo esterno o dall'applicazione possa raggiungere il dispositivo che esegue Archiviazione BLOB di Azure nel modulo IoT Edge.Depending on your network setup, choose an endpoint format such as the data traffic from your external module or application can reach the device running the Azure Blob Storage on IoT Edge module. L'endpoint BLOB per questo scenario è uno dei:The blob endpoint for this scenario is one of:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Esempi di guida introduttiva di Archiviazione BLOB di AzureAzure Blob Storage quickstart samples

La documentazione di Archiviazione BLOB di Azure include codice di esempio delle guide introduttive in diverse lingue. È possibile eseguire questi esempi per testare Archiviazione BLOB di Azure in Edge Edge modificando l'endpoint BLOB per la connessione al modulo di archiviazione BLOB locale.

Gli esempi di guida introduttiva seguenti usano lingue supportate anche da Edge Edge, pertanto è possibile distribuirle come moduli IoT Edge insieme al modulo di archiviazione BLOB:The following quickstart samples use languages that are also supported by IoT Edge, so you could deploy them as IoT Edge modules alongside the blob storage module:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Le versioni precedenti a V2.1 di Python SDK presentano un problema noto in cui il modulo non restituisce l'ora di creazione del BLOB. A causa di questo problema, alcuni metodi come elenca BLOB non funzionano. Come soluzione alternativa, impostare in modo esplicito la versione dell'API nel client BLOB su '2017-04-17'. Esempio:  `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Esempio di aggiunta di BLOBAppend Blob Sample](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [invio](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Connettersi all'archiviazione locale con Azure Storage ExplorerConnect to your local storage with Azure Storage Explorer

È possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per connettersi all'account di archiviazione locale.

1. Scaricare e installare Azure Storage Explorer

1. Connettersi ad Archiviazione di Azure usando una stringa di connessioneConnect to Azure Storage using a connection string

1. Fornire la stringa di connessione:Provide connection string:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Passare attraverso i passaggi per connettersi.

1. Creare un contenitore all'interno dell'account di archiviazione localeCreate container inside your local storage account

1. Avviare il caricamento di file come BLOB di blocco o Aggiunta BLOB.
   > [!NOTE]
   > Questo modulo non supporta i BLOB di pagine.

1. È anche possibile scegliere di connettere gli account di archiviazione di Azure in Esplora archivi. Questa configurazione offre un'unica visualizzazione sia per l'account di archiviazione locale che per l'account di archiviazione di AzureThis configuration gives you a single view for both your local storage account and Azure storage account

## <a name="supported-storage-operations"></a>Operazioni di archiviazione supportate

I moduli di archiviazione BLOB in IoT Edge usano gli SDK di Archiviazione di Azure e sono coerenti con la versione 2017-04-17 dell'API di archiviazione di Azure per gli endpoint BLOB a blocchi.

Poiché non tutte le operazioni di Archiviazione BLOB di Azure sono supportate da Archiviazione BLOB di Azure in Edge Edge Edge, in questa sezione viene elencato lo stato di ognuna.

### <a name="account"></a>Account

Supportate:

* Elencare i contenitori

Non supportate:

* Ottenere e impostare le proprietà del servizio BLOB
* Richiedere un BLOB preliminare
* Ottenere le statistiche del servizio BLOB
* Ottenere le informazioni sull'account

### <a name="containers"></a>Contenitori

Supportate:

* Creare ed eliminare un contenitore
* Ottenere i metadati ele proprietà del contenitore
* Elencare i BLOB
* Ottenere e impostare il contenitore ACL
* Impostare i metadati del contenitore

Non supportate:

* Lease del contenitore

### <a name="blobs"></a>BLOB

Supportate:

* Inserire, ottenere ed eliminare BLOB
* Ottenere e impostare le proprietà BLOB
* Ottenere e impostare i metadati BLOB

Non supportate:

* Lease del BLOB
* Snapshot di BLOB
* Copiare e interrompere la copia di BLOB
* Annullare l'eliminazione di BLOB
* Impostare il livello di BLOB

### <a name="block-blobs"></a>BLOB in blocchi

Supportate:

* Inserire il blocco
* Inserire e ottenere l'elenco del blocco

Non supportate:

* Inserire il blocco dall'URL

### <a name="append-blobs"></a>BLOB di accodamento

Supportate:

* Aggiungi blocco

Non supportate:

* Aggiungi blocco dall'URL

## <a name="event-grid-on-iot-edge-integration"></a>Integrazione di Griglia di eventi su Edge IoT

> [!CAUTION]
> L'integrazione con Griglia di eventi su IoT Edge è in anteprima

Questo archivio BLOB di Azure nel modulo Edge IoT ora fornisce l'integrazione con Griglia di eventi in Edge Edge.This Azure Blob Storage on IoT Edge module now provides integration with Event Grid on IoT Edge. Per informazioni dettagliate su questa integrazione, vedere [l'esercitazione per distribuire i moduli, pubblicare eventi e verificare il recapito](../event-grid/edge/react-blob-storage-events-locally.md)degli eventi .

## <a name="release-notes"></a>Note sulla versione

Ecco le note sulla [versione nell'hub docker](https://hub.docker.com/_/microsoft-azure-blob-storage) per questo modulo

## <a name="feedback"></a>Commenti e suggerimenti

Il tuo feedback è importante per noi per rendere questo modulo e le sue caratteristiche utili e facili da usare. Condividi il tuo feedback e facci sapere come possiamo migliorare.

È possibile contattarci aabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire Archiviazione BLOB](how-to-deploy-blob.md) di Azure in Edge Edge

Rimani aggiornato con gli aggiornamenti e gli annunci recenti nel blog di Archiviazione BLOB di [Azure su IoT Edge](https://aka.ms/abs-iot-blogpost)
