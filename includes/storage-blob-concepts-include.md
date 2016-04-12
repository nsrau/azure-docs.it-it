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

![Blob1][Blob1]

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

-   **Contenitore:** un contenitore è un raggruppamento di un set di BLOB. Tutti i BLOB devono trovarsi in un contenitore. In un account può esistere un numero illimitato di contenitori. In un contenitore può essere archiviato un numero illimitato di BLOB.

-   **BLOB:** file di qualsiasi tipo e dimensione. Archiviazione di Azure offre tre tipi di BLOB: i BLOB in blocchi, i BLOB di pagine e i BLOB di accodamento.
    
	I *BLOB in blocchi*sono ideali per l'archiviazione di file di testo o file binari, ad esempio i documenti e i file multimediali. I *BLOB di accodamento*sono simili ai BLOB in blocchi in quanto sono costituiti da blocchi, ma sono ottimizzati per le operazioni di accodamento, in modo che siano utili per gli scenari di registrazione. Un singolo BLOB in blocchi o BLOB di accodamento può contenere fino a 50.000 blocchi da al massimo 4 MB ciascuno, per una dimensione totale leggermente superiore a 195 GB (4 MB X 50.000).
    
	I *BLOB di pagine *possono avere un dimensione di al massimo 1 TB e sono più efficienti per operazioni di lettura/scrittura frequenti. Le Macchine virtuali di Azure utilizzano i BLOB di pagine come dischi del sistema operativo e dati.

	Per altre informazioni sui BLOB, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](https://msdn.microsoft.com/library/azure/ee691964.aspx).


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=AcomDC_0406_2016-->