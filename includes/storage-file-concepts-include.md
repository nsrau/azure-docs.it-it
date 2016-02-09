## Che cos'è l'archiviazione file di Azure?

L'archiviazione file offre un'archiviazione condivisa per le applicazioni che usano il protocollo SMB 2.1 o SMB 3.0 standard. Le macchine virtuali e i servizi cloud di Microsoft Azure possono condividere dati file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati file in una condivisione tramite l'API dell'archiviazione file.

Le applicazioni in esecuzione nelle macchine virtuali o nei servizi cloud di Azure possono montare una condivisione di archiviazione file per accedere a dati file, come se si montasse una condivisione SMB tipica per un'applicazione desktop. Non ci sono limiti per le macchine virtuali o i servizi cloud di Azure che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Poiché una condivisione di archiviazione file è una condivisione file standard in Azure utilizzando un protocollo SMB, le applicazioni in esecuzione in Azure possono accedere ai dati nella condivisione tramite le API di I/O del file. Gli sviluppatori possono quindi riutilizzare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. I professionisti IT possono usare i cmdlet di PowerShell per creare, montare e gestire condivisioni di archiviazione file nell'ambito delle attività di amministrazione per le applicazioni Azure. In questa guida vengono illustrati esempi per entrambi i casi.

Di seguito sono riportati gli usi più comuni per il servizio di archiviazione file:

- Migrazione di applicazioni locali basata sui file di condivisione per l'esecuzione in macchine virtuali o servizi cloud di Azure, senza costi di riscrittura
- Archiviazione di impostazioni delle applicazioni condivise, ad esempio nei file di configurazione
- Archiviazione di dati di diagnostica, ad esempio log, metriche e dump di arresto anomalo del sistema, in un percorso condiviso 
- Archiviazione di strumenti e utilità necessari per lo sviluppo o la gestione di macchine virtuali o servizi cloud di Azure

## Concetti relativi all'archiviazione file

L'archiviazione file contiene i seguenti componenti:

![files-concepts][files-concepts]

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/storage-scalability-targets.md).

-   **Condivisione:** una condivisione di archiviazione file è una condivisione file SMB di Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino a una capacità di 5 TB di condivisione del file.

-   **Directory:** una gerarchia di directory facoltativa.

-	**File:** un file nella condivisione. Le dimensioni massime di un file possono estendersi fino a 1 TB.

-   **Formato URL:** i file sono indirizzabili usando il formato di URL seguente: https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`
    
    L'URL di esempio seguente può essere usato per indirizzare uno dei file nel diagramma precedente: `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Per dettagli su come denominare condivisioni, directory e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](http://msdn.microsoft.com/library/azure/dn167011.aspx).

[files-concepts]: ./media/storage-file-concepts-include/files-concepts.png

<!---HONumber=AcomDC_0204_2016-->