## Informazioni sull'archiviazione BLOB

Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. L'archiviazione BLOB può essere usata per esporre dati pubblicamente a livello mondiale o archiviare privatamente i dati delle applicazioni.

Quelli di seguito sono gli impieghi più comuni dell'archiviazione BLOB:

-   Invio di immagini o documenti direttamente in un browser
-   Archiviazione di file per l'accesso distribuito
-   Flussi audio e video
-   Esecuzione di backup e ripristini di emergenza sicuri
-   Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure

## Concetti del servizio BLOB

Il servizio BLOB è composto dai componenti seguenti:

![BLOB1][Blob1]

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

-   **Contenitore:** un contenitore è un raggruppamento di un set di BLOB. Tutti i BLOB devono trovarsi in un contenitore. In un account può esistere un numero illimitato di contenitori. In un contenitore può essere archiviato un numero illimitato di BLOB.

-   **BLOB:** file di qualsiasi tipo e dimensione. Vi sono due tipi di BLOB che possono essere archiviati in Archiviazione di Azure: BLOB di pagine e BLOB in blocchi. La maggior parte dei file sono BLOB in blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB di dimensione. In questa esercitazione vengono utilizzati BLOB in blocchi. I BLOB di pagine, di altro tipo, possono raggiungere dimensioni fino a 1 TB e risultano più efficienti quando all'interno di un file vi sono intervalli di byte soggetti a modifiche frequenti. Per ulteriori informazioni sui BLOB, vedere [Informazioni su BLOB in blocchi e BLOB di pagine](https://msdn.microsoft.com/library/azure/ee691964.aspx).

-   **Formato dell’URL:** è possibile fare riferimento ai BLOB usando il formato di URL seguente: http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`
      
    Il seguente URL di esempio potrebbe essere utilizzato per fare riferimento a uno dei BLOB nel diagramma sopra riportato: `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg


<!--HONumber=52-->
