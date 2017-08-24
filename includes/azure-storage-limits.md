| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per sottoscrizione |200<sup>1</sup> |
| Capacità massima dell'account di archiviazione |500 TB<sup>2</sup> |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione |Nessun limite |
| Dimensione massima di un singolo contenitore BLOB o di una singola tabella o coda |Uguale alla capacità massima dell'account di archiviazione |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB in coda |50.000 |
| Dimensione massima di un blocco di un BLOB in blocchi |100 MB |
| Dimensione massima di un BLOB in blocchi |50.000 x 100 MB (circa 4,75 TB) |
| Dimensione massima di un blocco in un BLOB di aggiunta |4 MB |
| Dimensione massima di un BLOB di aggiunta |50.000 x 4 MB (circa 195 GB) |
| Dimensione massima di un BLOB di pagine |8 TB |
| Dimensione massima di un'entità di tabella |1 MB |
| Numero massimo di proprietà di un'entità di tabella |252 |
| Dimensione massima di un messaggio in una coda |64 KB |
| Dimensione massima di una condivisione file |5 TB |
| Dimensione massima di un file in una condivisione file |1 TB |
| Numero massimo di file in una condivisione file |L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB) |
| Numero massimo di operazioni di I/O al secondo per condivisione |1000 |
| Numero massimo di file in una condivisione file |L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB) |
| Numero massimo di criteri di accesso archiviati per ogni contenitore, condivisione di file, tabella o coda |5 |
| Frequenza massima di richieste per account di archiviazione |BLOB: 20.000 richieste al secondo<sup>2</sup> per BLOB di qualsiasi dimensione valida (con i soli limiti di ingresso/uscita dell'account) <br />File: 1000 operazioni di I/O al secondo (con dimensione di 8 KB) per condivisione file <br />Code: 20.000 messaggi al secondo (supponendo una dimensione dei messaggi di 1 KB)<br />Tabelle: 20.000 transazioni al secondo (supponendo una dimensione delle entità di 1 KB) |
| Velocità effettiva da raggiungere per BLOB singolo |Fino a 60 MB al secondo o fino 500 richieste al secondo |
| Velocità effettiva da raggiungere per coda singola (messaggi di 1 KB) |Fino a 2000 messaggi al secondo |
| Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KB) |Fino a 2000 entità al secondo |
| Velocità effettiva da raggiungere per singola condivisione di file |Fino a 60 MB al secondo |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) |10 Gbps con archiviazione GRS/ZRS<sup>4</sup> abilitata, 20 Gbps per LRS<sup>2</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) |20 Gbps con archiviazione RA-GRS/GRS/ZRS<sup>4</sup> abilitata, 30 Gbps per LRS<sup>2</sup> |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) |5 Gbps con archiviazione GRS/ZRS<sup>4</sup> abilitata, 10 Gbps per LRS<sup>2</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) |10 Gbps con archiviazione RA-GRS/GRS/ZRS<sup>4</sup> abilitata, 15 Gbps per LRS<sup>2</sup> |

<sup>1</sup>Sono inclusi gli account di archiviazione Standard e Premium. Se sono necessari più di 200 account di archiviazione, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione. 

<sup>2</sup>Per superare i limiti degli account di archiviazione standard in termini di capacità, ingresso/uscita e frequenza delle richieste, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà la richiesta e potrà approvare limiti più elevati caso per caso.

<sup>3</sup>Traffico in *ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.  

<sup>4</sup>Le opzioni di replica di Archiviazione di Azure includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona. Disponibile solo per i BLOB in blocchi. 
* **LRS**: archiviazione con ridondanza locale. 


