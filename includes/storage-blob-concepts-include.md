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

-   **BLOB:** file di qualsiasi tipo e dimensione. Vi sono due tipi di BLOB che possono essere archiviati in Archiviazione di Azure: BLOB di pagine e BLOB in blocchi. La maggior parte dei file sono BLOB in blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB di dimensione. In questa esercitazione vengono utilizzati BLOB in blocchi. I BLOB di pagine, di altro tipo, possono raggiungere dimensioni fino a 1 TB e risultano più efficienti quando all'interno di un file vi sono intervalli di byte soggetti a modifiche frequenti. Per ulteriori informazioni sui BLOB, vedere [Informazioni su BLOB in blocchi e BLOB di pagine](https://msdn.microsoft.com/library/azure/ee691964.aspx).

## Assegnazione di nome e riferimento a contenitori e BLOB

È possibile indirizzare un BLOB nell'account di archiviazione utilizzando il formato di URL seguente:
   
    http://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>  
      
Di seguito viene riportato un esempio di URL che indirizza a uno dei BLOB nel diagramma precedente:

    http://sally.blob.core.windows.net/movies/MOV1.AVI

### Regole di denominazione dei contenitori

Il nome di un contenitore deve essere un nome DNS valido. Inoltre, deve essere conforme alle regole seguenti:

- Il nome di un contenitore deve essere composto solo da minuscole.
- I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).
- Ciascun carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero: nei nomi dei contenitori non sono consentiti trattini consecutivi.
- La lunghezza dei nomi dei contenitori deve essere compresa tra 3 e 63 caratteri.

### Regole di denominazione dei BLOB

Il nome di un BLOB deve essere conforme alle regole seguenti:

- Il nome di un BLOB può contenere qualsiasi combinazione di caratteri.
- La lunghezza del nome di un BLOB deve essere di almeno un carattere e non può superare 1.024 caratteri.
- I nomi di BLOB distinguono tra maiuscole e minuscole.
- I caratteri URL riservati devono essere preceduti da un carattere di escape.
- Il numero di segmenti di tracciato che comprendono il nome del BLOB non può superare 254. Un segmento di tracciato è la stringa compresa tra caratteri consecutivi di delimitazione (ad esempio, la barra "/") che corrisponde al nome di una directory virtuale.

Il servizio BLOB si basa su uno schema di archiviazione semplice. È possibile creare una gerarchia virtuale specificando un delimitatore di carattere o stringa all'interno del nome del BLOB. Ad esempio, nell'elenco seguente vengono mostrati alcuni nomi di BLOB validi e univoci:

	/a
	/a.txt
	/a/b
	/a/b.txt

È possibile utilizzare il carattere di delimitazione per elencare i BLOB in modo gerarchico.


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=July15_HO4-->