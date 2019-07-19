---
title: Archiviare BLOB in blocchi nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Informazioni sulle funzionalità di suddivisione in livelli e di durata (TTL), vedere operazioni di archiviazione BLOB supportate e connettersi all'account di archiviazione BLOB.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c90a0351c8c71f4fcafa58a422cc3566a0b29b03
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850087"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge (anteprima)

L'archiviazione BLOB di Azure in IoT Edge offre una soluzione di archiviazione [BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) nei dispositivi perimetrali. Un modulo di archiviazione BLOB nel dispositivo IoT Edge si comporta come un servizio BLOB in blocchi di Azure, ma i BLOB in blocchi vengono archiviati localmente nel dispositivo IoT Edge. È possibile accedere ai BLOB usando gli stessi metodi di archiviazione di Azure SDK o bloccare le chiamate API BLOB che già utilizzate.

Questo modulo include le funzionalità **deviceToCloudUpload** e **deviceAutoDelete** .
> [!NOTE]
> L'archiviazione BLOB di Azure in IoT Edge è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Guarda il video per un'introduzione rapida
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** è una funzionalità configurabile che consente di caricare automaticamente i dati dall'archiviazione BLOB locale in Azure con supporto intermittente per la connettività Internet. Consente di:

- Attivare o disattivare la funzionalità deviceToCloudUpload.
- Scegliere l'ordine in cui i dati vengono copiati in Azure, ad esempio NewestFirst o OldestFirst.
- Specificare l'account di archiviazione di Azure in cui si desidera caricare i dati.
- Specificare i contenitori che si vuole caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione.
- Scegliere la possibilità di eliminare immediatamente i BLOB, al termine del caricamento nell'archiviazione cloud
- Eseguire il caricamento di BLOB completi `Put Blob` (operazione using) e il caricamento a `Put Block` livello `Put Block List` di blocco (usando le operazioni e).

Questo modulo usa il caricamento a livello di blocco, quando il BLOB è costituito da blocchi. Ecco alcuni scenari comuni:

- L'applicazione aggiorna alcuni blocchi di un BLOB caricato in precedenza. questo modulo carica solo i blocchi aggiornati e non l'intero BLOB.
- Il modulo sta caricando un BLOB e la connessione Internet scompare, quando la connettività è di nuovo carica solo i blocchi rimanenti e non l'intero BLOB.

Se si verifica una chiusura imprevista di un processo, ad esempio l'interruzione dell'alimentazione, durante il caricamento di un BLOB, verranno caricati di nuovo tutti i blocchi che erano dovuti al caricamento, quando il modulo tornerà online.

**deviceAutoDelete** è una funzionalità configurabile in cui il modulo Elimina automaticamente i BLOB dalla risorsa di archiviazione locale quando scade la durata specificata (espressa in minuti). Consente di:

- Attivare o disattivare la funzionalità deviceAutoDelete.
- Specificare l'intervallo di tempo in minuti (deleteAfterMinutes) dopo il quale i BLOB verranno eliminati automaticamente.
- Scegliere la possibilità di mantenere il BLOB mentre è in corso il caricamento in caso di scadenza del valore deleteAfterMinutes.

Scenari in cui dati quali video, immagini, dati finanziari, dati ospedalieri o dati che devono essere archiviati localmente, successivamente che possono essere elaborati localmente o trasferiti nel cloud sono esempi validi per l'uso di questo modulo.

Questo articolo illustra i concetti relativi all'archiviazione BLOB di Azure in IoT Edge contenitore che esegue un servizio BLOB sul dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

- È possibile usare il computer di sviluppo o una macchina virtuale come dispositivo IoT Edge seguendo la procedura descritta nella Guida introduttiva per i [dispositivi](quickstart.md) [Linux](quickstart-linux.md) o Windows.

- L'archiviazione BLOB di Azure nel modulo di IoT Edge supporta le configurazioni di dispositivo seguenti:

  | Sistema operativo | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian-stretch | No | Sì | No |  
  | Ubuntu Server 16.04 | Sì | No | Sì (disponibile per l' [installazione](how-to-install-iot-edge-linux-arm.md#install-a-specific-version) con [Azure IOT Edge 1.0.8-RC1 e versioni successive](https://github.com/Azure/azure-iotedge/releases)) |
  | Ubuntu Server 18.04 | Sì | No | Sì (disponibile per l' [installazione](how-to-install-iot-edge-linux-arm.md#install-a-specific-version) con [Azure IOT Edge 1.0.8-RC1 e versioni successive](https://github.com/Azure/azure-iotedge/releases)) |
  | Windows 10 Internet Internet (Enterprise), Build 17763 | Sì | No | No |
  | Windows Server 2019, Build 17763 | Sì | No | No |
  

Risorse cloud:

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Proprietà deviceToCloudUpload e deviceAutoDelete

Usare le proprietà desiderate per impostare deviceToCloudUploadProperties e deviceAutoDeleteProperties. Possono essere impostati durante la distribuzione o modificati in un secondo momento modificando il modulo gemello senza la necessità di ridistribuire. Si consiglia di selezionare "modulo gemello" `reported configuration` per `configurationValidation` e per assicurarsi che i valori vengano propagati correttamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Il nome di questa impostazione è`deviceToCloudUploadProperties`

| Campo | Valori possibili | Spiegazione | Variabile di ambiente |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Per impostazione predefinita `false`, è impostato su. Se si desidera attivarlo, impostarlo su`true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Consente di scegliere l'ordine in cui i dati vengono copiati in Azure. Per impostazione predefinita, è impostato `OldestFirst`su. L'ordine è determinato dall'ora dell'Ultima modifica del BLOB | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`è una stringa di connessione che consente di specificare l'account di archiviazione di Azure in cui si desidera caricare i dati. Specificare `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Aggiungi EndpointSuffix appropriati di Azure in cui verranno caricati i dati, che variano in base a Global Azure, Government Azure e Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Consente di specificare i nomi dei contenitori che si vuole caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione. Se non si specifica il nome del contenitore di destinazione, il nome del contenitore verrà assegnato `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`automaticamente come. È possibile creare stringhe di modello per il nome del contenitore di destinazione, vedere la colonna valori possibili. <br>*% h-> nome dell'hub Internet (3-50 caratteri). <br>*% d-> IoT Edge ID dispositivo (da 1 a 129 caratteri). <br>*% m-> nome del modulo (da 1 a 64 caratteri). <br>*% c-> nome del contenitore di origine (da 3 a 63 caratteri). <br><br>Le dimensioni massime del nome del contenitore sono di 63 caratteri, assegnando automaticamente il nome del contenitore di destinazione se la dimensione del contenitore supera i 63 caratteri. ogni sezione (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) verrà tagliata in 15 caratteri. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Per impostazione predefinita, è impostato `false`su. Quando è impostato su `true`, i dati verranno eliminati automaticamente al termine del caricamento nell'archiviazione cloud | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Il nome di questa impostazione è`deviceAutoDeleteProperties`

| Campo | Valori possibili | Spiegazione | Variabile di ambiente |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Per impostazione predefinita `false`, è impostato su. Se si desidera attivarlo, impostarlo su`true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Specificare l'intervallo di tempo in minuti. Il modulo eliminerà automaticamente i BLOB dalla risorsa di archiviazione locale quando questo valore scade | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Per impostazione predefinita, è impostato `true`su e manterrà il BLOB mentre viene caricato nell'archiviazione cloud se deleteAfterMinutes scade. È possibile impostarlo su `false` e eliminerà i dati non appena scade deleteAfterMinutes. Nota: Affinché questa proprietà funzioni, uploadOn deve essere impostato su true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Configurare i file di log

Per informazioni sulla configurazione dei file di log per il modulo, vedere le [procedure consigliate](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)per la produzione.

## <a name="connect-to-your-blob-storage-module"></a>Connettere il modulo di archiviazione BLOB

È possibile usare il nome dell'account e la chiave dell'account configurati per il modulo per accedere all'archiviazione BLOB nel dispositivo IoT Edge.

Specificare il dispositivo IoT Edge come endpoint BLOB per qualsiasi richiesta di archiviazione effettuata. È possibile [Creare una stringa di connessione per un endpoint di archiviazione esplicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando le informazioni del dispositivo IoT Edge e il nome dell'account che è stato configurato.

- Per i moduli distribuiti nello stesso dispositivo in cui è in esecuzione l'archivio BLOB di Azure nel modulo IoT Edge, l'endpoint BLOB è: `http://<module name>:11002/<account name>`.
- Per i moduli esterni o le applicazioni in esecuzione in un dispositivo diverso rispetto a quello in cui è in esecuzione l'archivio BLOB di Azure nel modulo IoT Edge, a seconda della configurazione della rete, in modo che il traffico dati dal modulo esterno o dall'applicazione possa raggiungere il dispositivo eseguendo l'archivio BLOB di Azure nel modulo IoT Edge, l'endpoint BLOB è uno dei seguenti:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Esempi di avvio rapido di archiviazione BLOB di Azure

La documentazione di archiviazione BLOB di Azure include il codice di esempio della Guida introduttiva in diverse lingue. È possibile eseguire questi esempi per testare l'archiviazione BLOB di Azure in IoT Edge modificando l'endpoint BLOB per la connessione al modulo di archiviazione BLOB locale.

Gli esempi di avvio rapido seguenti usano linguaggi supportati anche da IoT Edge, quindi è possibile distribuirli come moduli IoT Edge insieme al modulo di archiviazione BLOB:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Connettersi alla risorsa di archiviazione locale con Azure Storage Explorer

È possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per connettersi all'account di archiviazione locale.

1. Scaricare e installare Azure Storage Explorer

1. Connettersi ad archiviazione di Azure usando una stringa di connessione

1. Specificare la stringa di connessione:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Eseguire i passaggi per la connessione.

1. Creare un contenitore all'interno dell'account di archiviazione locale

1. Iniziare a caricare i file come BLOB in blocchi.
   > [!NOTE]
   > Questo modulo non supporta i BLOB di pagine.

1. È possibile scegliere di connettere gli account di archiviazione di Azure in cui si stanno caricando i dati. Offre un'unica visualizzazione per l'account di archiviazione locale e per l'account di archiviazione di Azure

## <a name="supported-storage-operations"></a>Operazioni di archiviazione supportate

I moduli di archiviazione BLOB in IoT Edge usano gli stessi SDK di archiviazione di Azure e sono coerenti con la versione 2017-04-17 dell'API di archiviazione di Azure per gli endpoint BLOB in blocchi. Le versioni successive dipendono dalle esigenze dei clienti.

Poiché non tutte le operazioni di archiviazione BLOB di Azure sono supportate dall'archiviazione BLOB di Azure in IoT Edge, in questa sezione viene elencato lo stato di ciascuna di esse.

### <a name="account"></a>Account

Supportate:

- Elencare i contenitori

Non supportate:

- Ottenere e impostare le proprietà del servizio BLOB
- Richiedere un BLOB preliminare
- Ottenere le statistiche del servizio BLOB
- Ottenere le informazioni sull'account

### <a name="containers"></a>Contenitori

Supportate:

- Creare ed eliminare un contenitore
- Ottenere i metadati ele proprietà del contenitore
- Elencare i BLOB
- Ottenere e impostare il contenitore ACL
- Impostare i metadati del contenitore

Non supportate:

- Lease del contenitore

### <a name="blobs"></a>Blobs

Supportate:

- Inserire, ottenere ed eliminare BLOB
- Ottenere e impostare le proprietà BLOB
- Ottenere e impostare i metadati BLOB

Non supportate:

- Lease del BLOB
- Snapshot di BLOB
- Copiare e interrompere la copia di BLOB
- Annullare l'eliminazione di BLOB
- Impostare il livello di BLOB

### <a name="block-blobs"></a>BLOB in blocchi

Supportate:

- Inserire il blocco
- Inserire e ottenere l'elenco del blocco

Non supportate:

- Inserire il blocco dall'URL

## <a name="release-notes"></a>Note sulla versione

Di seguito sono riportate le [Note sulla versione nell'hub Docker](https://hub.docker.com/_/microsoft-azure-blob-storage) per questo modulo

## <a name="feedback"></a>Commenti e suggerimenti

Il feedback è importante per rendere questo modulo e le sue funzionalità utili e facili da usare. Condividi i tuoi commenti e facci scoprire come possiamo migliorare.

Puoi contattarci all'indirizzoabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla [distribuzione di archiviazione BLOB di Azure in IOT Edge](how-to-deploy-blob.md)
