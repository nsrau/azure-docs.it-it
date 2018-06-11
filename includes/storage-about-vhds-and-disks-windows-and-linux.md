---
title: File di inclusione
description: File di inclusione
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f64645db782b055e1c544f257149411f29fc99d7
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806316"
---
## <a name="about-vhds"></a>Informazioni sui dischi rigidi virtuali
I dischi rigidi virtuali utilizzati in Azure sono file con estensione .vhd archiviati come BLOB di pagine in un account di archiviazione Standard o Premium in Azure. Per ulteriori dettagli sui BLOB di pagine, vedere [Informazioni sui BLOB in blocchi e sui BLOB di pagine](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Per informazioni su Archiviazione Premium, vedere l'articolo relativo alle [prestazioni elevate di Archiviazione Premium e macchine virtuali di Azure](../articles/virtual-machines/windows/premium-storage.md).

Azure supporta il formato di disco fisso VHD. In caso di formato fisso, il disco logico viene disposto in modo lineare all'interno del file, in modo che l'offset del disco X venga archiviato in corrispondenza dell'offset del BLOB X. In un piè di pagina alla fine del BLOB vengono descritte le proprietà del disco rigido virtuale. Con il formato fisso si verifica spesso uno spreco di spazio poiché nella maggior parte dei dischi sono presenti intervalli inutilizzati di grandi dimensioni. Tuttavia, in Azure i file con estensione vhd vengono archiviati in un formato di tipo sparse, pertanto si ottengono contemporaneamente i vantaggi sia dei dischi fissi sia di quelli dinamici. Per altre informazioni, vedere la [Guida introduttiva ai dischi rigidi virtuali](https://technet.microsoft.com/library/dd979539.aspx).

Tutti i file VHD in Azure che si intende usare come origine per creare dischi o immagini sono di sola lettura, ad eccezione dei file con estensione vhd caricati o copiati in Archiviazione di Azure dall'utente che possono essere di lettura/scrittura o di sola lettura. Quando si crea un disco o un'immagine, Azure crea copie dei file con estensione .vhd di origine. Queste copie possono essere lette oppure lette e scritte, a seconda di come viene utilizzato il disco rigido virtuale.

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco per la macchina virtuale che è una copia del file con estensione .vhd di origine. Per impedire eliminazioni accidentali, Azure inserisce un lease su qualsiasi file con estensione .vhd di origine utilizzato per creare un'immagine, un disco del sistema operativo o un disco dati.

Prima di poter eliminare un file con estensione .vhd di origine, sarà necessario rimuovere il lease eliminando il disco o l'immagine. Per eliminare un file con estensione .vhd utilizzato da una macchina virtuale come disco del sistema operativo, è possibile eliminare la macchina virtuale, il disco del sistema operativo e il file con estensione .vhd di origine in una sola volta eliminando la macchina virtuale e tutti i dischi associati. Tuttavia, l'eliminazione di un file con estensione vhd che rappresenta l'origine di un disco dati richiede l'esecuzione di diversi passaggi in un determinato ordine: scollegare innanzitutto il disco dalla macchina virtuale, quindi eliminare il disco e infine eliminare il file con estensione vhd.

> [!WARNING]
> Se si elimina un file con estensione .vdh di origine dalla memoria o l'account di archiviazione, Microsoft non può recuperare tali dati.

## <a name="types-of-disks"></a>Tipi di dischi 
I dischi di Azure sono stati progettati per il 99,999% di disponibilità. I dischi di Azure hanno offerto in modo costante una durabilità di livello aziendale, con una percentuale di frequenza di errori annualizzata pari a ZERO, ovvero la migliore del settore.

Durante la creazione di dischi è possibile scegliere tra tre livelli di prestazioni per l'archiviazione: dischi SSD Premium, SSD Standard (anteprima) e archiviazione HDD Standard. Ci sono inoltre due tipi di dischi, gestiti e non gestiti.

### <a name="standard-hdd-disks"></a>Dischi HDD Standard
I dischi HDD Standard sono basati su unità disco rigido e offrono una conveniente soluzione di archiviazione. L'archiviazione HDD Standard consente la replica locale in un data center oppure la ridondanza geografica con un data center primario e uno secondario. Per altre informazioni sulla replica dell'archiviazione, vedere [Replica di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md). 

Per altre informazioni sull'uso di dischi HDD Standard, vedere [Archiviazione Standard e dischi](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks-preview"></a>Dischi SSD Standard (anteprima)
I dischi SSD Standard sono progettati per lo stesso tipo di carichi di lavoro dei dischi HDD Standard, ma offrono prestazioni più coerenti e una maggiore affidabilità rispetto ai dischi HDD. I dischi SSD Standard combinano elementi dei dischi SSD Premium e dei dischi HDD Standard per offrire una soluzione conveniente più adatta per le applicazioni, ad esempio i server Web, che non richiedono un numero elevato di operazioni di I/O al secondo sui dischi. Quando disponibili, i dischi SSD Standard rappresentano l'opzione di distribuzione consigliata per la maggior parte dei carichi di lavoro. I dischi SSD Standard sono disponibili solo come dischi gestiti e durante l'anteprima sono disponibili solo in [aree specifiche](../articles/virtual-machines/windows/faq-for-disks.md) e con il tipo di resilienza di archiviazione con ridondanza locale.

### <a name="premium-ssd-disks"></a>Dischi SSD Premium 
I dischi SSD Premium sono basati su unità SSD e offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con un elevato numero di operazioni di I/O. In genere, è possibile usare i dischi SSD Premium con dimensioni che includono una "s" nel nome della serie. Sono ad esempio disponibili la serie Dv3 e la serie Dsv3. La serie Dsv3 può essere usata con i dischi SSD Premium.  Per altre informazioni, vedere [Archiviazione Premium](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Dischi non gestiti
I dischi non gestiti sono quelli di tipo tradizionale usati dalle macchine virtuali. Con tali dischi è possibile creare un account di archiviazione personale e specificarlo durante la creazione del disco. Assicurarsi di non inserire troppi dischi nello stesso account di archiviazione, per non superare i [limiti di scalabilità](../articles/storage/common/storage-scalability-targets.md) dell'account di archiviazione, ad esempio 20.000 operazioni di I/O al secondo, con la conseguente limitazione delle macchine virtuali. Con i dischi non gestiti è necessario ottimizzare l'uso di uno o più account di archiviazione per ottenere migliori prestazioni dalle macchine virtuali.

### <a name="managed-disks"></a>Dischi gestiti 
Il servizio Managed Disks gestisce in background le operazioni di creazione e gestione dell'account di archiviazione ed elimina la necessità di preoccuparsi dei limiti di scalabilità dell'account di archiviazione. È sufficiente specificare le dimensioni del disco e il livello di prestazioni, Standard o Premium, e Azure si occuperà della creazione e della gestione del disco. Durante l'aggiunta di dischi o il ridimensionamento della macchina virtuale non è necessario preoccuparsi dello spazio di archiviazione usato. 

È anche possibile gestire le immagini personalizzate in un unico account di archiviazione per ogni area di Azure e usarle per creare centinaia di macchine virtuali nella stessa sottoscrizione. Per altre informazioni sui dischi gestiti, vedere [Azure Managed Disks overview](../articles/virtual-machines/windows/managed-disks-overview.md) (Panoramica di Azure Managed Disks).

È consigliabile usare Azure Managed Disks per le nuove macchine virtuali e convertire i dischi non gestiti esistenti in dischi gestiti, per sfruttare le numerose funzionalità disponibili in Managed Disks.

### <a name="disk-comparison"></a>Confronto dei dischi
La tabella seguente mette a confronto dischi HDD Standard, SSD Standard e SSD Premium per i dischi gestiti e non gestiti, per aiutare a scegliere quali usare.

|    | Disco Premium di Azure |Disco SSD Standard di Azure (anteprima)| Disco HDD Standard di Azure 
|--- | ------------------ | ------------------------------- | ----------------------- 
| Tipo di disco | Unità a stato solido (SSD) | Unità a stato solido (SSD) | Unità disco rigido (HDD)  
| Panoramica  | Supporto per dischi SSD a prestazioni elevate e bassa latenza per le macchine virtuali che eseguono carichi di lavoro con elevato numero di operazioni di I/O o ambienti di produzione host cruciali |Affidabilità e prestazioni più coerenti rispetto ai dischi HDD. Ottimizzato per carichi di lavoro con un basso numero di operazioni di I/O al secondo| Disco conveniente basato su unità disco rigido per l'accesso non frequente
| Scenario  | Carichi di lavoro di produzione su cui influiscono le prestazioni |Server Web, applicazioni aziendali usate poco di frequente e sviluppo/test| Backup, carichi di lavoro non critici, accesso poco frequente 
| Dimensione disco | P4: 32 GiB (solo dischi gestiti)<br>P6: 64 GiB (solo dischi gestiti)<br>P10: 128 GiB<br>P15: 256 GiB (solo dischi gestiti)<br>P20: 512 GiB<br>P30: 1024 GiB<br>P40: 2048 GiB<br>P50: 4095 GiB |Solo dischi gestiti:<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB | Dischi non gestiti: 1 GiB - 4 TiB (4095 GiB) <br><br>Dischi gestiti:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB
| Velocità effettiva massima per disco | 250 MiB/s | Fino a 60 MiB/s | Fino a 60 MiB/s 
| Numero massimo di operazioni di I/O al secondo per disco | 7500 IOPS | Fino a 500 operazioni di I/O al secondo | Fino a 500 operazioni di I/O al secondo 