---
title: Archiviare BLOB in blocchi nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Comprendere le funzionalità di suddivisione in livelli e time-to-live, vedere operazioni di archiviazione di blob supportati e connettersi all'account di archiviazione blob.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991476"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge (anteprima)

L'archiviazione BLOB di Azure in IoT Edge offre una soluzione di archiviazione [BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) nei dispositivi perimetrali. Un modulo di archiviazione BLOB nel dispositivo IoT Edge si comporta come un servizio BLOB in blocchi di Azure, ma i BLOB in blocchi vengono archiviati localmente nel dispositivo IoT Edge. È possibile accedere ai BLOB usando gli stessi metodi di archiviazione di Azure SDK o bloccare le chiamate API BLOB che già utilizzate.

> [!NOTE]
> L'archiviazione BLOB di Azure in IoT Edge è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo modulo viene fornito con **suddivisione in livelli** e **time-to-live** funzionalità.

> [!NOTE]
> Attualmente la suddivisione in livelli e le funzionalità di time-to-live sono disponibili solo in Linux AMD64 e Linux ARM32.

**La suddivisione in livelli** è una funzionalità configurabile che consente di caricare automaticamente i dati dall'archiviazione di blob locale in Azure con supporto per la connettività internet intermittenti. Consente di:

- Acceso/spento attivare la funzionalità di suddivisione in livelli
- Scegliere l'ordine in cui vengono copiati i dati in Azure, ad esempio NewestFirst o OldestFirst
- Specificare l'account di archiviazione di Azure a cui si vuole che i dati caricati.
- Specificare i contenitori che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione.
- Completa la suddivisione in livelli di blob (tramite `Put Blob` operazione) e bloccare la suddivisione in livelli a livello (usando `Put Block` e `Put Block List` operazioni).

Questo modulo Usa blocchi a livello la suddivisione in livelli, quando il blob è costituito da blocchi. Ecco alcuni degli scenari comuni:

- L'applicazione aggiorna alcuni blocchi di un blob caricato in precedenza, questo modulo consente di caricare solo i blocchi aggiornati e non l'intero blob.
- Il modulo è caricamento blob e la connessione internet viene chiuso, quando la connettività è tornare nuovamente che carica solo i rimanenti blocchi e non l'intero blob.

Se si verifica una chiusura imprevista del processo (ad esempio, interruzione dell'alimentazione) durante il caricamento di un blob, tutti i blocchi che sono stati scadenza per il caricamento verranno caricati anche in questo caso, quando il modulo ritorna in linea.

**Time-to-live** (durata TTL) è una funzionalità configurabile in cui il modulo Elimina automaticamente i BLOB dalla risorsa di archiviazione locale quando scade la durata specificata (espresso in minuti). Durata (TTL) consente di:

- Acceso/spento attivare la funzionalità di suddivisione in livelli
- Specificare la durata (TTL) in pochi minuti

Gli scenari in cui i dati come video, immagini, i dati finanziari, dati ospedale o tutti i dati devono essere archiviati in locale, in seguito che potrebbero essere elaborati in locale o trasferiti nel cloud sono ottimi esempi per usare questo modulo.

Questo articolo fornisce istruzioni per la distribuzione di un'archiviazione BLOB di Azure nel contenitore di IoT Edge che esegue un servizio BLOB nel dispositivo IoT Edge.

> [!NOTE]
> I termini "auto-la suddivisione in livelli" e "auto-scadenza" usata nel video sono stati sostituiti da "suddivisione in livelli" e "time-to-live".

Guarda il video di introduzione rapida
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

- È possibile usare il computer di sviluppo o di una macchina virtuale come un dispositivo IoT Edge seguendo i passaggi descritti nella Guida introduttiva per [Linux](quickstart-linux.md) oppure [dispositivi di Windows](quickstart.md).

- L'archiviazione BLOB di Azure nel modulo di IoT Edge supporta le configurazioni di dispositivo seguenti:

  | Sistema operativo | Architettura |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Core (aggiornamento di ottobre) | AMD64 |
  | Windows 10 IoT Enterprise (aggiornamento di ottobre) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

Risorse cloud:

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure.

## <a name="tiering-and-time-to-live-properties"></a>Proprietà di suddivisione in livelli e time-to-live

Usare le proprietà desiderate per impostare la suddivisione in livelli e le proprietà time-to-live. Può essere impostati durante la distribuzione o modificate in un secondo momento modificando il dispositivo gemello del modulo senza la necessità di ridistribuire. È consigliabile controllare "Modulo gemello" per `reported configuration` e `configurationValidation` per assicurarsi che i valori vengano propagati correttamente.

### <a name="tiering-properties"></a>Proprietà di suddivisione in livelli

Il nome di questa impostazione è `tieringSettings`

| Campo | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| tieringOn | true, false | Per impostazione predefinita è impostata su `false`, se si desidera attivarla, impostare su `true`|
| backlogPolicy | NewestFirst, OldestFirst | Consente di scegliere l'ordine in cui vengono copiati i dati in Azure. Per impostazione predefinita è impostata su `OldestFirst`. L'ordine è determinato dall'ora dell'ultima modifica del Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` è una stringa di connessione che consente di specificare l'account di archiviazione di Azure a cui si vuole che i dati caricata. Specificare `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Aggiungere appropriato EndpointSuffix di Azure in cui verranno caricati i dati, varia per Azure globale, Azure per enti pubblici e Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Consente di specificare i nome del contenitore che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione. Se non si specifica il nome del contenitore di destinazione, verrà assegnato automaticamente il nome del contenitore come `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. È possibile creare stringhe di modello per il nome di contenitore di destinazione, consultare la colonna di valori possibili. <br>* %h -> nome dell'Hub IoT (da 3 a 50 caratteri). <br>* %d -> ID dispositivo di IoT (1 per i 129 caratteri). <br>* %m -> nome del modulo (da 1 a 64 caratteri). <br>* %c -> nome del contenitore di origine (da 3 a 63 caratteri). <br><br>Dimensione massima del nome del contenitore è 63 caratteri, mentre assegnare automaticamente il nome del contenitore di destinazione se la dimensione del contenitore supera 63 caratteri che annullerà ogni sezione (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) e 15 caratteri. |

### <a name="time-to-live-properties"></a>Proprietà time-to-live

Il nome di questa impostazione è `ttlSettings`

| Campo | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| ttlOn | true, false | Per impostazione predefinita è impostata su `false`, se si desidera attivarla, impostare su `true`|
| timeToLiveInMinutes | `<minutes>` | Specificare la durata (TTL) in pochi minuti. Il modulo eliminerà automaticamente i BLOB dall'archivio locale alla scadenza di TTL |

## <a name="configure-log-files"></a>Configurare i file di log

Per informazioni sulla configurazione del file di log per il modulo, vedere questi [consigliate produzione](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Connettere il modulo di archiviazione BLOB

È possibile usare il nome dell'account e la chiave dell'account configurati per il modulo per accedere all'archiviazione BLOB nel dispositivo IoT Edge.

Specificare il dispositivo IoT Edge come endpoint BLOB per qualsiasi richiesta di archiviazione effettuata. È possibile [Creare una stringa di connessione per un endpoint di archiviazione esplicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando le informazioni del dispositivo IoT Edge e il nome dell'account che è stato configurato.

- Per i moduli che vengono distribuiti nel dispositivo stesso come in cui viene eseguito l'archiviazione Blob di Azure nel modulo di IoT Edge, l'endpoint blob è: `http://<module name>:11002/<account name>`.
- Per i moduli che vengono distribuiti in un dispositivo diverso da in cui è in esecuzione, l'archiviazione Blob di Azure nel modulo di IoT Edge quindi a seconda del programma di installazione il blob di endpoint fa parte di:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Esempi di avvio rapido nell'archivio Blob di Azure

La documentazione dell'archiviazione BLOB di Azure include guide introduttive che forniscono il codice di esempio in linguaggi diversi. È possibile eseguire questi esempi per archiviazione Blob di Azure in IoT Edge di test modificando l'endpoint blob per la connessione per il modulo di archiviazione blob.

Le seguenti guide introduttive utilizzano linguaggi che sono supportati anche da IoT Edge, in modo che sia possibile distribuirle come moduli di IoT Edge con il modulo di archiviazione BLOB:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Connessione a risorsa di archiviazione locale con Azure Storage Explorer

È possibile usare **Azure Storage Explorer** per connettersi all'account di archiviazione locale. Questa funzionalità è disponibile solo con [Azure Storage Explorer versione 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Si potrebbero verificare errori durante l'esecuzione di questa procedura, ad esempio l'aggiunta di una connessione a un account di archiviazione locale o la creazione di contenitori nell'account di archiviazione locale. Per ignorare e aggiornare.

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
- Elenca BLOB
- Ottenere e impostare il contenitore ACL
- Impostare i metadati del contenitore

Non supportate:

- Lease del contenitore

### <a name="blobs"></a>BLOB

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

## <a name="feedback"></a>Commenti e suggerimenti

I tuoi commenti sono importanti per rendere questo modulo e le relative funzionalità utili e facile da usare. Per condividere commenti e suggerimenti e facci sapere come possiamo migliorare.

È possibile contattare Microsoft all'indirizzo absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [distribuire archiviazione Blob di Azure in IoT Edge](how-to-deploy-blob.md)
