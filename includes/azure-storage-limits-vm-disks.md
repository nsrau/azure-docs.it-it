Una macchina virtuale di Azure supporta il collegamento di un numero di dischi dati. In questo articolo vengono descritti gli obiettivi di scalabilità e prestazioni per i dischi dati di una macchina virtuale. Usare questi obiettivi per stabilire il numero e il tipo di dischi necessari per soddisfare i requisiti di capacità e prestazioni. 

> [!IMPORTANT]
> Per prestazioni ottimali, limitare il numero di dischi a elevato uso collegati alla macchina virtuale per evitare una possibile limitazione. Se l'uso di tutti i dischi collegati non è elevato nello stesso momento, la macchina virtuale può supportare un numero maggiore di dischi.

* **Per Azure Managed Disks:** il limite per i dischi gestiti è in base all'area e al tipo di disco. Il limite massimo e il limite predefinito sono 10.000 dischi gestiti per area e per tipo di disco per una sottoscrizione. In un'area, ad esempio, è possibile creare fino a 10.000 dischi gestiti Standard e 10.000 dischi gestiti Premium per sottoscrizione.

    Gli snapshot gestiti e le immagini sono inclusi nel calcolo del limite di dischi gestiti.

* **Per gli account di archiviazione standard:** un account di archiviazione standard con una frequenza totale massima di richieste di 20.000 IOPS. Il numero totale di IOPS in tutti i dischi della macchina virtuale in un account di archiviazione standard non deve superare questo limite.
  
    È possibile calcolare approssimativamente il numero di dischi a elevato utilizzo supportato da un singolo account di archiviazione standard in base al limite della frequenza di richieste. Per una VM di livello Basic, ad esempio, il numero massimo di dischi a elevato uso è pari a circa 66 (20.000/300 IOPS per disco) e per una macchina virtuale di livello Standard è pari a circa 40 (20.000/500 IOPS per disco). 

* **Per gli account di archiviazione Premium:** un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

