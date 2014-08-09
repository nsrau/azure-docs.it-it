
## <a name="what-is"> </a>Informazioni sull'archiviazione BLOB

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può raggiungere dimensioni di centinaia di gigabyte, mentre un singolo account di archiviazione può arrivare a contenere fino a 200 TB di BLOB, se creato a partire dall'8 giugno 2012. Gli account di archiviazione creati prima di tale data possono contenere fino a 100 TB di BLOB. Per informazioni sulla capacità dell'account di archiviazione, vedere
[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure][1].

Quelli di seguito sono gli impieghi più comuni dell'archiviazione BLOB:

* Invio di immagini o documenti direttamente in un browser
* Archiviazione di file per l'accesso distribuito
* Flussi audio e video
* Esecuzione di backup e ripristini di emergenza sicuri
* Archiviazione di dati a scopo di analisi da parte di un servizio
  locale o ospitato in Azure

L'archiviazione BLOB può essere utilizzata per esporre dati pubblicamente a livello mondiale o privatamente a scopo di archiviazione interna per le applicazioni.

## <a name="concepts"> </a>Concetti

Il servizio BLOB è composto dai componenti seguenti:

![BLOB1](./media/howto-blob-storage/blob1.jpg)

* **Account di archiviazione:** l'accesso ad Archiviazione di Azure
  viene eseguito esclusivamente tramite un account di archiviazione. Per
  informazioni sulla capacità dell'account di archiviazione, vedere
  [Obiettivi di scalabilità e prestazioni per Archiviazione di
  Azure][1].

* **Contenitore:** un contenitore è il raggruppamento di un set di BLOB.
  Tutti i BLOB devono trovarsi in un contenitore. In un account può
  esistere un numero illimitato di contenitori. In un contenitore può
  essere archiviato un numero illimitato di BLOB.

* **BLOB:** file di qualsiasi tipo e dimensione. Vi sono due tipi di
  BLOB che possono essere archiviati in Archiviazione di Azure: BLOB di
  pagine e BLOB in blocchi. La maggior parte dei file sono BLOB in
  blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB di
  dimensione. In questa esercitazione vengono utilizzati BLOB in
  blocchi. I BLOB dell'altro tipo, di pagine, possono raggiungere
  dimensioni fino a 1 TB e risultano più efficienti quando vi sono
  intervalli di byte all'interno del file soggetti a modifiche
  frequenti. Per ulteriori informazioni sui BLOB, vedere [Informazioni
  su BLOB in blocchi e BLOB di pagine][2].

* **Formato dell'URL:** i BLOB sono indirizzabili utilizzando il
  formato dell'URL seguente:   
   http://`<storage 
  account>`.blob.core.windows.net/`<container
 >`/`<blob>`
  
  Il seguente URL di esempio potrebbe essere utilizzato per indirizzare
  uno dei BLOB nel diagramma sopra riportato:  
   `http://sally.blob.core.windows.net/movies/MOV1.AVI`



[1]: http://msdn.microsoft.com/it-it/library/dn249410.aspx
[2]: http://msdn.microsoft.com/it-it/library/windowsazure/ee691964.aspx