## <a name="what-is"> </a>Informazioni sull'archiviazione BLOB

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate
di dati non strutturati, come dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite
HTTP o HTTPS. L'archiviazione BLOB può essere usata per esporre dati pubblicamente a livello mondiale o
archiviare privatamente i dati delle applicazioni.

Quelli di seguito sono gli impieghi più comuni dell'archiviazione BLOB:

-   Invio di immagini o documenti direttamente in un browser
-   Archiviazione di file per l'accesso distribuito
-   Flussi audio e video
-   Esecuzione di backup e ripristini di emergenza sicuri
-   Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato
    in Azure

## <a name="concepts"> </a>Concetti

Il servizio BLOB è composto dai componenti seguenti:

![BLOB1][BLOB1]

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito
     esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][Obiettivi di scalabilità e prestazioni per Archiviazione di Azure].

-   **Contenitore:** un contenitore consente di raggruppare un insieme di BLOB.
    Tutti i BLOB devono trovarsi in un contenitore. In un account può esistere un
    numero illimitato di contenitori. In un contenitore si può archiviare un numero
    illimitato di BLOB.

-   **BLOB:** file di qualsiasi tipo e dimensione. Vi sono due tipi di BLOB che
    possono essere archiviati in Archiviazione di Azure: BLOB di pagine e BLOB in blocchi.
    La maggior parte dei file sono BLOB in blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB
    di dimensione. In questa esercitazione vengono usati BLOB in blocchi. I BLOB di pagine sono un altro tipo di BLOB,
    possono raggiungere dimensioni fino a 1 TB e risultano più efficienti quando
    vi sono intervalli di byte all'interno del file soggetti a modifiche frequenti. Per altre informazioni sui
    BLOB, vedere [Informazioni su BLOB in blocchi e BLOB di pagine][Informazioni su BLOB in blocchi e BLOB di pagine].

-   **Formato dell'URL:** è possibile fare riferimento ai BLOB usando il formato di URL
    seguente:
    http://`<storage account>`.blob.core.windows.net/`<container>`/`<blob>`

    Si può ad esempio usare il seguente URL per indirizzare uno dei BLOB nel
    diagramma precedente:
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`

  [BLOB1]: ./media/howto-blob-storage/blob1.jpg
  [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure]: http://msdn.microsoft.com/it-it/library/dn249410.aspx
