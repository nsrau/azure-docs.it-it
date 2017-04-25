| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per sottoscrizione |200<sup>1</sup> |
| TB per account di archiviazione |500 TB |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione |L'unico limite è quello relativo alla capacità dell'account di archiviazione (500 TB) |
| Dimensione massima di un singolo contenitore BLOB o di una singola tabella o coda |500 TB |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB in coda |50.000 |
| Dimensione massima di un blocco di un BLOB in blocchi |100 MB |
| Dimensione massima di un BLOB in blocchi |50.000 x 100 MB (circa 4,75 TB) |
| Dimensione massima di un blocco in un BLOB di aggiunta |4 MB |
| Dimensione massima di un BLOB di aggiunta |50.000 x 4 MB (circa 195 GB) |
| Dimensione massima di un BLOB di pagine |1 TB |
| Dimensione massima di un'entità di tabella |1 MB |
| Numero massimo di proprietà di un'entità di tabella |252 |
| Dimensione massima di un messaggio in una coda |64 KB |
| Dimensione massima di una condivisione file |5 TB |
| Dimensione massima di un file in una condivisione file |1 TB |
| Numero massimo di file in una condivisione file |L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB) |
| Numero massimo di operazioni di I/O al secondo per condivisione |1000 |
| Numero massimo di file in una condivisione file |L'unico limite è quello relativo alla capacità totale della condivisione file (5 TB) |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione |L'unico limite è quello relativo alla capacità dell'account di archiviazione (500 TB) |
| Numero massimo di criteri di accesso archiviati per ogni contenitore, condivisione di file, tabella o coda |5 |
| Frequenza massima di richieste per account di archiviazione |BLOB: 20.000 richieste al secondo per BLOB di qualsiasi dimensione valida (con i soli limiti di ingresso/uscita dell'account) <br />File: 1000 operazioni di I/O al secondo (con dimensione di 8 KB) per condivisione file <br />Code: 20.000 messaggi al secondo (supponendo una dimensione dei messaggi di 1 KB)<br />Tabelle: 20.000 transazioni al secondo (supponendo una dimensione delle entità di 1 KB) |
| Velocità effettiva da raggiungere per BLOB singolo |Fino a 60 MB al secondo o fino 500 richieste al secondo |
| Velocità effettiva da raggiungere per coda singola (messaggi di 1 KB) |Fino a 2000 messaggi al secondo |
| Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KB) |Fino a 2000 entità al secondo |
| Velocità effettiva da raggiungere per singola condivisione di file |Fino a 60 MB al secondo |
| Numero massimo in entrata <sup>2</sup> per account di archiviazione (aree US) |10 Gbps se GRS/ZRS<sup>3</sup> è abilitato, 20 Gbps per LRS |
| Numero massimo in uscita <sup>2</sup> per account di archiviazione (aree US) |20 Gbps se RA-GRS/GRS/ZRS<sup>3</sup> è abilitato, 30 Gbps per LRS |
| Traffico in ingresso massimo<sup>2</sup> per account di archiviazione (aree non degli Stati Uniti) |5 Gbps se GRS/ZRS<sup>3</sup> è abilitato, 10 Gbps per LRS |
| Traffico in uscita massimo<sup>2</sup> per account di archiviazione (aree non degli Stati Uniti) |10 Gbps se RA-GRS/GRS/ZRS<sup>3</sup> è abilitato, 15 Gbps per LRS |

<sup>1</sup>Sono inclusi gli account di archiviazione Standard e Premium. Se sono necessari più di 200 account di archiviazione, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione. 

<sup>2</sup>*Dati in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.  

<sup>3</sup>le opzioni di replica di archiviazione di Azure includono:

* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona. Disponibile solo per i BLOB in blocchi. 
* **LRS**: archiviazione con ridondanza locale. 

