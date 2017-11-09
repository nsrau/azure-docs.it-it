| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per sottoscrizione | 200<sup>1</sup> |
| Capacità massima dell'account di archiviazione | 500 TiB<sup>2</sup> |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione GRS/ZRS<sup>4</sup> abilitata, 20 Gbps per LRS<sup>2</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS/ZRS<sup>4</sup> abilitata, 30 Gbps per LRS<sup>2</sup> |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) | 5 Gbps con archiviazione GRS/ZRS<sup>4</sup> abilitata, 10 Gbps per LRS<sup>2</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS/ZRS<sup>4</sup> abilitata, 15 Gbps per LRS<sup>2</sup> |

<sup>1</sup>Sono inclusi gli account di archiviazione Standard e Premium. Se sono necessari più di 200 account di archiviazione, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione. 

<sup>2</sup>Per superare i limiti degli account di archiviazione standard in termini di capacità, ingresso/uscita e frequenza delle richieste, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà la richiesta e potrà approvare limiti più elevati caso per caso.

<sup>3</sup> Limitato solo dai valori massimi per il traffico in ingresso/uscita dell'account. Il *traffico in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.  

<sup>4</sup>Le opzioni di ridondanza di Archiviazione di Azure includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona. Disponibile solo per i BLOB in blocchi. 
* **LRS**: archiviazione con ridondanza locale. 