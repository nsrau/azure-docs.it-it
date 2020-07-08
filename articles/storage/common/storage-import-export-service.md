---
title: Uso di Importazione/Esportazione di Azure per trasferire i dati da e verso Archiviazione di Azure | Documentazione Microsoft
description: Informazioni su come creare processi di importazione ed esportazione nel portale di Azure per trasferire dati da e verso Archiviazione di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a43637071ec1a9962c8aa1b2262e07354293f12b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512437"
---
# <a name="what-is-azure-importexport-service"></a>Che cos'è il servizio Importazione/Esportazione di Azure?

Il servizio Importazione/Esportazione di Azure viene usato per importare in modo sicuro grandi quantità di dati nell'archivio BLOB di Azure e in File di Azure tramite la spedizione delle unità disco a un data center di Azure. È anche possibile usare questo servizio per trasferire i dati dall'archivio BLOB di Azure a unità disco per la spedizione al sito locale. È possibile importare dati da uno o più dischi nell'Archivio Blob di Azure o in File di Azure.

Specificare le unità disco e trasferire dati con il servizio Importazione/Esportazione di Azure. È anche possibile usare le unità disco fornite da Microsoft.

Per trasferire i dati usando i dischi forniti da Microsoft, è possibile usare [Azure Data Box Disk](../../databox/data-box-disk-overview.md) per importare dati in Azure. Microsoft spedisce fino a 5 dischi SSD (Solid State Disk) crittografati con una capacità di 40 TB totali per ordine al data center del cliente tramite un corriere locale. È possibile configurare rapidamente i dischi, copiarvi i dati su una connessione USB 3.0 e rispedirli ad Azure. Per altre informazioni, vedere [Panoramica di Azure Data Box Disk](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Casi d'uso di importazione/esportazione di Azure

È consigliabile usare il servizio Importazione/Esportazione di Azure quando l'upload o il download dei dati attraverso la rete è troppo lento o l'acquisizione di maggiore larghezza di banda di rete comporta costi proibitivi. Usare questo servizio negli scenari seguenti:

* **Migrazione di dati al cloud**: spostamento di grandi quantità di dati in Azure in modo veloce e a costi contenuti.
* **Distribuzione del contenuto**: inviare rapidamente i dati ai siti dei clienti.
* **Backup**: eseguire il backup dei dati locali da archiviare in archiviazione di Azure.
* **Ripristino di dati**: ripristino di una grande quantità di dati archiviati nell'archivio perché vengano recapitati al percorso locale.

## <a name="importexport-components"></a>Componenti del servizio Importazione/Esportazione

Il servizio Importazione/Esportazione usa i componenti seguenti:

* **Servizio Importazione/Esportazione**: questo servizio disponibile nel portale di Azure consente all'utente di creare e tenere traccia dei processi di importazione (upload) ed esportazione (download) di dati.  

* **Strumento WAImportExport**: si tratta di uno strumento da riga di comando che esegue le operazioni seguenti:
  * Prepara l'unità disco da spedire per l'importazione.
  * Facilita la copia dei dati nell'unità.
  * Crittografa i dati nell'unità con BitLocker a 256 bit AES. È possibile utilizzare una protezione con chiave esterna per proteggere la chiave di BitLocker.
  * Genera i file journal dell'unità usati durante la creazione dell'importazione.
  * Identifica i numeri di unità necessarie per i processi di esportazione.

> [!NOTE]
> Lo strumento WAImportExport è disponibile in due versioni, 1 e 2. È consigliabile usare:
>
> * La versione 1 per l'importazione/esportazione nell'archivio BLOB di Azure.
> * La versione 2 per l'importazione dei dati in File di Azure.
>
> Lo strumento WAImportExport è compatibile solo con il sistema operativo Windows a 64 bit. Per versioni specifiche di sistemi operativi supportate, vedere [Azure Import/Export requirements](storage-import-export-requirements.md#supported-operating-systems) (Requisiti per Importazione/Esportazione di Azure).

* **Dischi**: è possibile spedire unità SSD (Solid-State Drive) o unità disco rigido (HDD) al data center di Azure. Per la creazione di un processo di importazione, si spediscono le unità disco contenenti i dati. Per la creazione di un processo di esportazione, si spediscono unità vuote al data center di Azure. Per i tipi di disco specifici, vedere i [tipi di disco supportati](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Come funziona il servizio Importazione/Esportazione?

Il servizio Importazione/Esportazione di Azure consente il trasferimento dei dati in BLOB di Azure e in File di Azure mediante la creazione di processi. Per creare i processi, usare il portale di Azure o l'API REST di Azure Resource Manager. Ogni processo è associato a un singolo account di archiviazione.

I processi possono essere di importazione o di esportazione. Un processo di importazione consente di importare dati in BLOB di Azure o in File di Azure, mentre il processo di esportazione consente l'esportazione dei dati da BLOB di Azure. Per un processo di importazione, si spediscono le unità contenenti i dati. Quando si crea un processo di esportazione, si spediscono unità vuote a un data center di Azure. In ogni caso, è possibile spedire fino a 10 unità disco per ogni processo.

### <a name="inside-an-import-job"></a>Analisi di un processo di importazione

In generale, un processo di importazione prevede i passaggi seguenti:

1. Determinare i dati da importare, il numero di unità necessarie e la posizione del BLOB di destinazione per i dati nell'archiviazione di Azure.
2. Usare lo strumento WAImportExport per copiare i dati nelle unità disco. Crittografare i dischi con BitLocker.
3. Creare un processo di importazione nell'account di archiviazione di destinazione nel portale di Azure. Caricare i file journal dell'unità.
4. Specificare l'indirizzo del mittente e il numero di account del vettore per la restituzione delle unità.
5. Spedire le unità disco all'indirizzo di spedizione fornito durante la creazione del processo.
6. Aggiornare il numero di tracciabilità della consegna nei dettagli del processo di importazione e inviare il processo di importazione.
7. Le unità vengono ricevute ed elaborate presso il data center di Azure.
8. Le unità vengono restituite usando l'account del vettore all'indirizzo mittente specificato nel processo di importazione.

> [!NOTE]
> Per le spedizioni locali (entro data center paese/area geografica), condividere un account del vettore nazionale.
>
> Per le spedizioni all'estero (fuori data center paese/area geografica), condividere un account International Carrier.

 ![Figura 1: importazione del flusso di processo](./media/storage-import-export-service/importjob.png)

Per istruzioni dettagliate per l'importazione dei dati, vedere:

* [Import data into Azure Blobs](storage-import-export-data-to-blobs.md) (Importare dati in BLOB di Azure)
* [Import data into Azure Files](storage-import-export-data-to-files.md) (Importare dati in File di Azure)

### <a name="inside-an-export-job"></a>Analisi di un processo di esportazione

> [!IMPORTANT]
> Il servizio supporta solo l'esportazione di BLOB di Azure. L'esportazione di file di Azure non è supportata.

In generale, un processo di esortazione prevede i passaggi seguenti:

1. Determinare i dati da esportare, il numero di unità necessarie e i percorsi dei BLOB di origine o dei contenitori dei dati nell'archivio BLOB.
2. Creare un processo di esportazione nell'account di archiviazione di origine nel portale di Azure.
3. Specificare i percorsi dei BLOB di origine o dei contenitori per i dati da esportare.
4. Specificare l'indirizzo del mittente e il numero di account del vettore per la restituzione delle unità.
5. Spedire le unità disco all'indirizzo di spedizione fornito durante la creazione del processo.
6. Aggiornare il numero di tracciabilità della consegna nei dettagli del processo di esportazione e inviare il processo di esportazione.
7. Le unità vengono ricevute ed elaborate presso il data center di Azure.
8. Le unità vengono crittografate con BitLocker e le chiavi sono disponibili tramite il portale di Azure.  
9. Le unità vengono restituite usando l'account del vettore all'indirizzo mittente specificato nel processo di importazione.

> [!NOTE]
> Per le spedizioni locali (entro data center paese/area geografica), condividere un account del vettore nazionale.
>
> Per le spedizioni all'estero (fuori data center paese/area geografica), condividere un account International Carrier.
  
 ![Figura 2: esportazione del flusso di processo](./media/storage-import-export-service/exportjob.png)

Per istruzioni dettagliate sull'esportazione dei dati, vedere [Export data from Azure Blobs](storage-import-export-data-from-blobs.md) (Esportare dati da BLOB di Azure).

## <a name="region-availability"></a>Aree di disponibilità

Il servizio Importazione/Esportazione di Azure supporta la copia dei dati da e verso tutti gli account di archiviazione di Azure. È possibile spedire le unità disco a una delle località seguenti. Se l'account di archiviazione è in una località di Azure non specificata di seguito, quando si crea il processo viene proposta una località di spedizione alternativa.

### <a name="supported-shipping-locations"></a>Località di spedizione supportate

|Paese/Area geografica  |Paese/Area geografica  |Paese/Area geografica  |Paese/Area geografica  |
|---------|---------|---------|---------|
|Stati Uniti orientali    | Europa settentrionale        | India centrale        |US Gov Iowa         |
|Stati Uniti occidentali     |Europa occidentale         | India meridionale        | US DoD (area orientale)        |
|Stati Uniti orientali 2    | Asia orientale        |  India occidentale        | US DoD (area centrale)        |
|Stati Uniti occidentali 2     | Asia sud-orientale        | Canada centrale        | Cina orientale         |
|Stati Uniti centrali     | Australia orientale        | Canada orientale        | Cina settentrionale        |
|Stati Uniti centro-settentrionali     |  Australia sud-orientale       | Brasile meridionale        | Regno Unito meridionale        |
|Stati Uniti centro-meridionali     | Giappone occidentale        |Corea centrale         | Germania centrale        |
|Stati Uniti centro-occidentali     |  Giappone orientale       | US Gov Virginia        | Germania nord-orientale        |

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

I dati nell'unità vengono crittografati tramite Crittografia unità BitLocker AES a 256 bit. che protegge i dati mentre sono in transito.

Per i processi di importazione, le unità vengono crittografate in due modi.  

* Specificare l'opzione quando si usa il file *dataset.csv* durante l'esecuzione dello strumento WAImportExport per la preparazione delle unità.

* Abilitare la crittografia BitLocker manualmente nell'unità. Specificare la chiave di crittografia nel file *driveset.csv* durante l'esecuzione dello strumento da riga di comando WAImportExport per la preparazione dell'unità. La chiave di crittografia di BitLocker può essere ulteriormente protetta usando una protezione con chiave esterna (nota anche come chiave gestita da Microsoft) o una chiave gestita dal cliente. Per ulteriori informazioni, vedere come [utilizzare una chiave gestita dal cliente per proteggere la chiave BitLocker](storage-import-export-encryption-key-portal.md).

Per i processi di esportazione, dopo la copia dei dati nelle unità, il servizio applica la crittografia BitLocker all'unità prima di rispedirla all'utente. La chiave di crittografia viene indicata all'utente tramite il portale di Azure. È necessario sbloccare l'unità usando lo strumento WAImporExport usando la chiave.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Prezzi

**Tariffa di gestione delle unità**

Esiste una tariffa di gestione per ogni unità elaborata come parte del processo di importazione o esportazione. Vedere i dettagli in [Prezzi di Importazione/Esportazione](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Costi di spedizione**

Quando si spediscono unità ad Azure, si paga il costo di spedizione al vettore. Il costo della spedizione delle unità restituite da Microsoft all'utente viene addebitato sull'account del vettore specificato al momento della creazione del processo.

**Costi di transazione**

L' [addebito per le transazioni di archiviazione standard](https://azure.microsoft.com/pricing/details/storage/) si applica durante l'importazione e l'esportazione dei dati. Gli addebiti in uscita standard sono applicabili anche quando i dati vengono esportati dalla risorsa di archiviazione di Azure. Per ulteriori informazioni sui costi in uscita, vedere prezzi per il [trasferimento dei dati.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare il servizio Importazione/Esportazione per:

* [Import data into Azure Blobs](storage-import-export-data-to-blobs.md) (Importare dati in BLOB di Azure)
* [Export data from Azure Blobs](storage-import-export-data-from-blobs.md) (Esportare dati da BLOB di Azure)
* [Import data into Azure Files](storage-import-export-data-to-files.md) (Importare dati in File di Azure)
