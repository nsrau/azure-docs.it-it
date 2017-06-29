
## <a name="about-vhds"></a>Informazioni sui dischi rigidi virtuali

I dischi rigidi virtuali utilizzati in Azure sono file con estensione .vhd archiviati come BLOB di pagine in un account di archiviazione Standard o Premium in Azure. Per ulteriori dettagli sui BLOB di pagine, vedere [Informazioni sui BLOB in blocchi e sui BLOB di pagine](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Per informazioni su Archiviazione Premium, vedere l'articolo relativo alle [prestazioni elevate di Archiviazione Premium e macchine virtuali di Azure](../articles/storage/storage-premium-storage.md).

Azure supporta il formato di disco fisso VHD. In caso di formato fisso, il disco logico viene disposto in modo lineare all'interno del file, in modo che l'offset del disco X venga archiviato in corrispondenza dell'offset del BLOB X. In un piè di pagina alla fine del BLOB vengono descritte le proprietà del disco rigido virtuale. Spesso, con il formato fisso si verifica uno spreco di spazio poiché nella maggior parte dei dischi sono presenti intervalli inutilizzati di grandi dimensioni. Tuttavia, in Azure i file con estensione vhd vengono archiviati in un formato di tipo sparse, pertanto si ottengono contemporaneamente i vantaggi sia dei dischi fissi sia di quelli dinamici. Per ulteriori dettagli, vedere [Introduzione ai dischi rigidi virtuali](https://technet.microsoft.com/library/dd979539.aspx).

Tutti i file con estensione .vhd in Azure che si desidera utilizzare come origine per creare dischi o immagini sono di sola lettura. Quando viene creato un disco o un'immagine, Azure crea copie dei file con estensione .vhd. Queste copie possono essere lette oppure lette e scritte, a seconda di come viene utilizzato il disco rigido virtuale.

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco per la macchina virtuale che è una copia del file con estensione .vhd di origine. Per impedire eliminazioni accidentali, Azure inserisce un lease su qualsiasi file con estensione .vhd di origine utilizzato per creare un'immagine, un disco del sistema operativo o un disco dati.

Prima di poter eliminare un file con estensione .vhd di origine, sarà necessario rimuovere il lease eliminando il disco o l'immagine. Per eliminare un file con estensione .vhd utilizzato da una macchina virtuale come disco del sistema operativo, è possibile eliminare la macchina virtuale, il disco del sistema operativo e il file con estensione .vhd di origine in una sola volta eliminando la macchina virtuale e tutti i dischi associati. Tuttavia, l'eliminazione di un file con estensione vhd che rappresenta l'origine di un disco dati richiede l'esecuzione di diversi passaggi in un determinato ordine: scollegare innanzitutto il disco dalla macchina virtuale, quindi eliminare il disco e infine eliminare il file con estensione vhd.

> [!WARNING]
> Se si elimina un file con estensione .vdh di origine dalla memoria o l'account di archiviazione, Microsoft non può recuperare tali dati.
> 

## <a name="types-of-disks"></a>Tipi di dischi 

Durante la creazione di dischi è possibile scegliere tra due livelli di prestazioni per l'archiviazione: Archiviazione Standard e Archiviazione Premium. I dischi possono essere di due tipi, gestiti e non gestiti, e possono risiedere in entrambi i livelli di prestazioni.  

### <a name="standard-storage"></a>Archiviazione standard 

Archiviazione Standard è supportata da unità disco rigido e offre un'archiviazione conveniente con buone prestazioni. Per Archiviazione Standard è possibile scegliere tra archiviazione replicata in locale in un data center o con ridondanza geografica con i data center primario e secondario. Per altre informazioni sulla replica dell'archiviazione, vedere [Replica di Archiviazione di Azure](../articles/storage/storage-redundancy.md). 

Per altre informazioni sull'uso di Archiviazione Standard con dischi di macchina virtuale, vedere l'articolo relativo ad [Archiviazione Standard e dischi](../articles/storage/storage-standard-storage.md).

### <a name="premium-storage"></a>Archiviazione Premium 

Archiviazione Premium è supportata da unità SSD e offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con elevato numero di operazioni di I/O. È possibile usare Archiviazione Premium con macchine virtuali di Azure serie DS, DSv2, GS, Ls o FS. Per altre informazioni, vedere [Archiviazione Premium](../articles/storage/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Dischi non gestiti

I dischi non gestiti sono quelli di tipo tradizionale usati dalle macchine virtuali. Con tali dischi è possibile creare un account di archiviazione personale e specificarlo durante la creazione del disco. Assicurarsi di non inserire troppi dischi nello stesso account di archiviazione, per non superare gli [obiettivi di scalabilità](../articles/storage/storage-scalability-targets.md) dell'account di archiviazione, ad esempio 20.000 IOPS, limitando di conseguenza le macchine virtuali. Con i dischi non gestiti è necessario ottimizzare l'uso di uno o più account di archiviazione per ottenere migliori prestazioni dalle macchine virtuali.

### <a name="managed-disks"></a>Dischi gestiti 

Il servizio Managed Disks gestisce in background le operazioni di creazione e gestione dell'account di archiviazione ed elimina la necessità di preoccuparsi dei limiti di scalabilità dell'account di archiviazione. È sufficiente specificare le dimensioni del disco e il livello di prestazioni, Standard o Premium, e Azure si occuperà della creazione e della gestione del disco. Anche se si aggiungono dischi o si ridimensiona la macchina virtuale, non è necessario preoccuparsi dello spazio di archiviazione usato. 

È anche possibile gestire le immagini personalizzate in un unico account di archiviazione per ogni area di Azure e usarle per creare centinaia di macchine virtuali nella stessa sottoscrizione. Per altre informazioni sul servizio Managed Disks, vedere [Azure Managed Disks Overview](../articles/storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).

È consigliabile usare Azure Managed Disks per le nuove macchine virtuali e convertire i dischi non gestiti esistenti in dischi gestiti, per sfruttare le numerose funzionalità disponibili in Managed Disks.

### <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente permette di scegliere mettendo a confronto i livelli Premium e Standard per i dischi gestiti e quelli non gestiti.

|    | Disco Premium di Azure | Disco Standard di Azure |
|--- | ------------------ | ------------------- |
| Tipo di disco | Unità a stato solido (SSD) | Unità disco rigido (HDD)  |
| Panoramica  | Supporto per dischi SSD a prestazioni elevate e bassa latenza per le macchine virtuali che eseguono carichi di lavoro con elevato numero di operazioni di I/O o ambienti di produzione host cruciali | Supporto per dischi HDD convenienti per scenari di macchine virtuali di sviluppo e test |
| Scenario  | Carichi di lavoro di produzione su cui influiscono le prestazioni | Sviluppo e test, non critico, <br>ad accesso non frequente |
| Dimensione disco | P4: 32 GB<br>P6: 64 GB<br>P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB<br>P40: 2048 GB<br>P50: 4095 GB | Dischi non gestiti: tra 1 GB e 4 TB (4095 GB) <br><br>Dischi gestiti:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB <br>S40: 2048 GB<br>S50: 4095 GB| 
| Velocità effettiva massima per disco | 250 MB/s | 60 MB/s | 
| Numero massimo di operazioni di I/O al secondo per disco | 7500 IOPS | 500 IOPS | 

