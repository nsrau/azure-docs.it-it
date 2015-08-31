Risorsa|Limite predefinito
---|---
Numero massimo di account di archiviazione per sottoscrizione|100<sup>1</sup>
TB per account di archiviazione|500 TB
Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione|L'unico limite è quello relativo alla capacità dell'account di archiviazione (500 TB)
Dimensione massima di un singolo contenitore BLOB o di una singola tabella o coda|500 TB
Numero massimo di blocchi in un BLOB in blocchi|50\.000
Dimensione massima di un blocco di un BLOB in blocchi|4 MB
Dimensione massima di un BLOB in blocchi|50\.000 x 4 MB (circa 195 GB) 
Dimensione massima di un BLOB di pagine |1 TB
Dimensione massima di un'entità di tabella|1 MB
Numero massimo di proprietà di un'entità di tabella|252
Dimensione massima di un messaggio in una coda|64 KB
Dimensione massima di una condivisione file|5 TB
Dimensione massima di un file in una condivisione file|1 TB
Numero massimo di file in una condivisione file|L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB)
Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione|L'unico limite è quello relativo alla capacità dell'account di archiviazione (500 TB)
Max 8 KB IOPS per disco permanente (macchina virtuale di livello base)|300<sup>2</sup>
Max 8 KB IOPS per disco permanente (macchina virtuale di livello Standard)|500<sup>2</sup>
Frequenza di richiesta totale (presupponendo oggetti con dimensione di 1 KB) per account di archiviazione|Fino a 20.000 IOPS, entità al secondo o messaggi al secondo
Velocità effettiva da raggiungere per BLOB singolo|Fino a 60 MB al secondo o fino 500 richieste al secondo
Velocità effettiva da raggiungere per coda singola (messaggi di 1 KB)|Fino a 2000 messaggi al secondo
Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KB)|Fino a 2000 entità al secondo
Velocità effettiva da raggiungere per singolo file|Fino a 60 MB al secondo
Numero massimo in entrata<sup>3</sup> per account di archiviazione (aree USA)|10 Gbps se GRS/ZRS<sup>4</sup> è abilitato, 20 Gbps per LRS
Numero massimo in uscita<sup>3</sup> per account di archiviazione (aree USA)|20 Gbps se GRS/ZRS<sup>4</sup> è abilitato, 30 Gbps per LRS
Numero massimo in entrata<sup>3</sup> per account di archiviazione (aree europee e asiatiche)|5 Gbps se GRS/ZRS<sup>4</sup> è abilitato, 10 Gbps per LRS
Numero massimo in uscita<sup>3</sup> per account di archiviazione (aree europee e asiatiche)|10 Gbps se GRS/ZRS<sup>4</sup> è abilitato, 15 Gbps per LRS

<sup>1</sup>Se sono necessari più di 100 account di archiviazione, contattare il [supporto Azure](http://azure.microsoft.com/support/faq/) per assistenza.

<sup>2</sup>Il limite di velocità richiesta totale per un account di archiviazione è 20.000 IOPS. Se una macchina virtuale utilizza il numero massimo di IOPS per disco, per evitare la limitazione possibili, assicurarsi che il numero totale di IOPS in tutti i dischi rigidi virtuali delle macchine virtuali non superi il limite di account di archiviazione (20.000 IOPS).

È possibile calcolare approssimativamente il numero di dischi a elevato utilizzati supportato da un singolo account di archiviazione del limite della transazione. Per una VM di livello base, il numero massimo di dischi a elevato utilizzati è su 66 (20.000/300 di IOPS per disco) e per una macchina virtuale di livello Standard, ad esempio, è circa 40 (IOPS 20.000/500 per disco). Si noti tuttavia che l'account di archiviazione può supportare un numero maggiore di dischi, se non sono tutti molto utilizzato nello stesso momento.

<sup>3</sup>Si intendono in *entrata* tutti i dati (richieste) inviati a un account di archiviazione. Si intendono in *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.

<sup>4</sup>GRS fa riferimento al servizio di archiviazione con ridondanza geografica. ZRS fa riferimento al servizio di archiviazione con ridondanza della zona ed è disponibile solo per i BLOB in blocchi. LRS fa riferimento al servizio di archiviazione con ridondanza locale.

<!---HONumber=August15_HO8-->