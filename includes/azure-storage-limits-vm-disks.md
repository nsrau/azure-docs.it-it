Una macchina virtuale di Azure supporta il collegamento di un numero di dischi dati. Per ottenere prestazioni ottimali, è consigliabile limitare il numero di dischi a elevato utilizzo collegati alla macchina virtuale per evitare una possibile limitazione. Se non tutti i dischi presentano un utilizzo elevato nello stesso momento, l'account di archiviazione può supportare un numero maggiore di dischi.

* **Per Azure Managed Disks:** il limite per il numero di Managed Disks varia in base all'area geografica e al tipo di risorsa di archiviazione. Il valore predefinito, nonché limite massimo, è di 10.000 dischi per ogni sottoscrizione, ogni area e ogni tipo di risorsa di archiviazione. In una sottoscrizione e in un'area, ad esempio, è possibile creare fino a 10.000 Managed Disks Standard e 10.000 Managed Disks Premium. 

    Gli snapshot gestiti e le immagini sono inclusi nel calcolo per il limite di Managed Disks.

* **Per gli account di archiviazione standard:** un account di archiviazione standard con una frequenza totale massima di richieste di 20.000 IOPS. Il numero totale di IOPS in tutti i dischi della macchina virtuale in un account di archiviazione standard non deve superare questo limite.
  
    È possibile calcolare approssimativamente il numero di dischi a elevato utilizzo supportato da un singolo account di archiviazione standard in base al limite della frequenza di richieste. Per una VM di livello Basic, ad esempio, il numero massimo di dischi a elevato utilizzo è pari a circa 66 (20.000/300 IOPS per disco) e per una macchina virtuale di livello Standard è pari a circa 40 (20.000/500 IOPS per disco), come illustrato nella tabella seguente. 
* **Per gli account di archiviazione Premium:** un account di archiviazione Premium ha una velocità effettiva totale massima di 50 Gbps. La velocità effettiva totale in tutti i dischi della macchina virtuale non deve superare questo limite.

