---
title: Archiviare BLOB in blocchi nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Comprendere le funzionalità di suddivisione in livelli e time-to-live, vedere operazioni di archiviazione di blob supportati e connettersi all'account di archiviazione blob.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bb6cd43c77c31874115250d13f8d4067b3db7b36
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67804978"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge (anteprima)

L'archiviazione BLOB di Azure in IoT Edge offre una soluzione di archiviazione [BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) nei dispositivi perimetrali. Un modulo di archiviazione BLOB nel dispositivo IoT Edge si comporta come un servizio BLOB in blocchi di Azure, ma i BLOB in blocchi vengono archiviati localmente nel dispositivo IoT Edge. È possibile accedere ai BLOB usando gli stessi metodi di archiviazione di Azure SDK o bloccare le chiamate API BLOB che già utilizzate.

Questo modulo viene fornito con **deviceToCloudUpload** e **deviceAutoDelete** funzionalità.
> [!NOTE]
> L'archiviazione BLOB di Azure in IoT Edge è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Guarda il video di introduzione rapida
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** è una funzionalità configurabile che consente di caricare automaticamente i dati dall'archiviazione di blob locale in Azure con supporto per la connettività internet intermittenti. Consente di:

- Attivare on/off la funzionalità deviceToCloudUpload.
- Scegliere l'ordine in cui vengono copiati i dati in Azure, ad esempio NewestFirst o OldestFirst.
- Specificare l'account di archiviazione di Azure a cui si vuole che i dati caricati.
- Specificare i contenitori che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione.
- Scegliere la possibilità di eliminare i BLOB immediatamente, dopo aver completato il caricamento nell'archiviazione cloud
- Completa il caricamento di blob (tramite `Put Blob` operazione) e il caricamento a livello di blocco (usando `Put Block` e `Put Block List` operazioni).

Questo modulo Usa il caricamento a livello di blocco, quando il blob è costituito da blocchi. Ecco alcuni degli scenari comuni:

- L'applicazione aggiorna alcuni blocchi di un blob caricato in precedenza, questo modulo consente di caricare solo i blocchi aggiornati e non l'intero blob.
- Il modulo è caricamento blob e la connessione internet viene chiuso, quando la connettività è tornare nuovamente che carica solo i rimanenti blocchi e non l'intero blob.

Se si verifica una chiusura imprevista del processo (ad esempio, interruzione dell'alimentazione) durante il caricamento di un blob, tutti i blocchi che sono stati scadenza per il caricamento verranno caricati anche in questo caso, quando il modulo ritorna in linea.

**deviceAutoDelete** è una funzionalità configurabile in cui il modulo Elimina automaticamente i BLOB dalla risorsa di archiviazione locale quando scade la durata specificata (espresso in minuti). Consente di:

- Attivare on/off la funzionalità deviceAutoDelete.
- Specificare il tempo in minuti (deleteAfterMinutes) dopo che i BLOB verranno eliminati automaticamente.
- Scegliere la possibilità di mantenere il blob durante il caricamento se valore deleteAfterMinutes scade.

Gli scenari in cui i dati come video, immagini, i dati finanziari, dati ospedale o tutti i dati devono essere archiviati in locale, in seguito che potrebbero essere elaborati in locale o trasferiti nel cloud sono ottimi esempi per usare questo modulo.

Questo articolo illustra i concetti correlati all'archiviazione Blob di Azure nel contenitore di IoT Edge che esegue un servizio blob nel dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

- È possibile usare il computer di sviluppo o di una macchina virtuale come un dispositivo IoT Edge seguendo i passaggi descritti nella Guida introduttiva per [Linux](quickstart-linux.md) oppure [dispositivi di Windows](quickstart.md).

- L'archiviazione BLOB di Azure nel modulo di IoT Edge supporta le configurazioni di dispositivo seguenti:

  | Sistema operativo | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian-stretch | No | Sì | No |  
  | Ubuntu Server 16.04 | Sì | No | Yes |
  | Ubuntu Server 18.04 | Sì | No | Sì |
  | Windows 10 IoT Enterprise, compilazione 17763 | Sì | No | No |
  | Windows Server 2019, compilazione 17763 | Sì | No | No |
  

Risorse cloud:

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>proprietà deviceToCloudUpload e deviceAutoDelete

Usare le proprietà desiderate per impostare deviceToCloudUploadProperties e deviceAutoDeleteProperties. Può essere impostati durante la distribuzione o modificate in un secondo momento modificando il dispositivo gemello del modulo senza la necessità di ridistribuire. È consigliabile controllare "Modulo gemello" per `reported configuration` e `configurationValidation` per assicurarsi che i valori vengano propagati correttamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Il nome di questa impostazione è `deviceToCloudUploadProperties`

| Campo | Valori possibili | Spiegazione | Variabile di ambiente |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Per impostazione predefinita è impostata su `false`, se si desidera attivarla, impostare su `true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Consente di scegliere l'ordine in cui vengono copiati i dati in Azure. Per impostazione predefinita è impostata su `OldestFirst`. L'ordine è determinato dall'ora dell'ultima modifica del Blob | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` è una stringa di connessione che consente di specificare l'account di archiviazione di Azure a cui si vuole che i dati caricata. Specificare `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Aggiungere appropriato EndpointSuffix di Azure in cui verranno caricati i dati, varia per Azure globale, Azure per enti pubblici e Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Consente di specificare i nome del contenitore che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione. Se non si specifica il nome del contenitore di destinazione, verrà assegnato automaticamente il nome del contenitore come `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. È possibile creare stringhe di modello per il nome di contenitore di destinazione, consultare la colonna di valori possibili. <br>* %h -> nome dell'Hub IoT (da 3 a 50 caratteri). <br>* %d -> ID dispositivo di IoT Edge (1 per i 129 caratteri). <br>* %m -> nome del modulo (da 1 a 64 caratteri). <br>* %c -> nome del contenitore di origine (da 3 a 63 caratteri). <br><br>Dimensione massima del nome del contenitore è 63 caratteri, mentre assegnare automaticamente il nome del contenitore di destinazione se la dimensione del contenitore supera 63 caratteri che annullerà ogni sezione (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) per 15 personaggi. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Per impostazione predefinita è impostata su `false`. Quando è impostata su `true`, eliminerà automaticamente i dati di termine del caricamento nell'archiviazione cloud | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Il nome di questa impostazione è `deviceAutoDeleteProperties`

| Campo | Valori possibili | Spiegazione | Variabile di ambiente |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Per impostazione predefinita è impostata su `false`, se si desidera attivarla, impostare su `true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Specificare il tempo in minuti. Il modulo eliminerà automaticamente i BLOB dall'archivio locale alla scadenza di questo valore | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Per impostazione predefinita è impostata su `true`, e manterrà il blob durante il caricamento nell'archiviazione cloud se deleteAfterMinutes scade. È possibile impostarlo `false` ed eliminerà i dati appena deleteAfterMinutes scade. Nota: Per questa proprietà funzioni uploadOn deve essere impostata su true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Configurare i file di log

Per informazioni sulla configurazione del file di log per il modulo, vedere questi [consigliate produzione](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Connettere il modulo di archiviazione BLOB

È possibile usare il nome dell'account e la chiave dell'account configurati per il modulo per accedere all'archiviazione BLOB nel dispositivo IoT Edge.

Specificare il dispositivo IoT Edge come endpoint BLOB per qualsiasi richiesta di archiviazione effettuata. È possibile [Creare una stringa di connessione per un endpoint di archiviazione esplicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando le informazioni del dispositivo IoT Edge e il nome dell'account che è stato configurato.

- Per i moduli che vengono distribuiti nel dispositivo stesso come in cui viene eseguito l'archiviazione Blob di Azure nel modulo di IoT Edge, l'endpoint blob è: `http://<module name>:11002/<account name>`.
- Per i moduli esterni o le applicazioni in esecuzione in un dispositivo diverso da in cui è in esecuzione, l'archiviazione Blob di Azure nel modulo di IoT Edge quindi a seconda delle impostazioni di rete, in modo che il traffico di dati dalla tua applicazione o del modulo esterno può raggiungere il dispositivo esegue l'archiviazione Blob di Azure nel modulo di IoT Edge, l'endpoint blob è uno di:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Esempi di avvio rapido nell'archivio Blob di Azure

La documentazione di archiviazione Blob di Azure include il codice di esempio di Guida introduttiva in diverse lingue. È possibile eseguire questi esempi per archiviazione Blob di Azure in IoT Edge di test modificando l'endpoint blob per connettere il modulo di archiviazione blob locali.

Gli esempi di avvio rapido seguente usano lingue sono supportate anche da IoT Edge, in modo che è possibile distribuirle come moduli di IoT Edge con il modulo di archiviazione blob:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Connessione a risorsa di archiviazione locale con Azure Storage Explorer

È possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per connettersi all'account di archiviazione locale.

1. Scaricare e installare Azure Storage Explorer

1. Connettersi ad archiviazione di Azure usando una stringa di connessione

1. Specificare una stringa di connessione: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Seguire i passaggi per la connessione.

1. Creare contenitori all'interno dell'account di archiviazione locale

1. Avvio del caricamento del file come BLOB in blocchi.
   > [!NOTE]
   > Questo modulo non supporta i BLOB di pagine.

1. È possibile scegliere di connettere gli account di archiviazione di Azure in cui si siano caricando i dati. Offre una visualizzazione singola per l'account di archiviazione locale e account di archiviazione di Azure

## <a name="supported-storage-operations"></a>Operazioni di archiviazione supportate

Moduli di archiviazione BLOB in IoT Edge usano gli stessi SDK di archiviazione di Azure e sono coerenti con la versione 2017-04-17 dell'API di archiviazione di Azure per gli endpoint di blob di blocco. Le versioni successive dipendono dalle esigenze dei clienti.

Poiché non tutte le operazioni di archiviazione Blob di Azure sono supportate da archiviazione Blob di Azure in dispositivi perimetrali IoT, in questa sezione elenca lo stato di ognuno.

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

Di seguito sono le [note sulla versione in hub docker](https://hub.docker.com/_/microsoft-azure-blob-storage) per questo modulo

## <a name="feedback"></a>Commenti e suggerimenti

I tuoi commenti sono importanti per rendere questo modulo e le relative funzionalità utili e facile da usare. Per condividere commenti e suggerimenti e facci sapere come possiamo migliorare.

È possibile contattare Microsoft all'indirizzo absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [distribuire archiviazione Blob di Azure in IoT Edge](how-to-deploy-blob.md)
