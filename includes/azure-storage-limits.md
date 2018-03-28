| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per sottoscrizione | 200<sup>1</sup> |
| Capacità massima dell'account di archiviazione | 500 TiB<sup>2</sup> |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste per account di archiviazione | 20.000 richieste al secondo<sup>2</sup> |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione GRS/ZRS<sup>4</sup> abilitata, 20 Gbps per LRS<sup>2</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS/ZRS<sup>4</sup> abilitata, 30 Gbps per LRS<sup>2</sup> |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) | 5 Gbps con archiviazione GRS/ZRS<sup>4</sup> abilitata, 10 Gbps per LRS<sup>2</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS/ZRS<sup>4</sup> abilitata, 15 Gbps per LRS<sup>2</sup> |

<sup>1</sup>Sono inclusi gli account di archiviazione Standard e Premium. Se sono necessari più di 200 account di archiviazione, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione. 

<sup>2</sup> Se è necessario espandere i limiti dell'account di archiviazione, contattare il [supporto di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà la richiesta e potrà approvare limiti più elevati caso per caso. Gli account di archiviazione per utilizzo generico e BLOB supportano entrambi capacità incrementate, dati in ingresso/uscita e velocità della richiesta in base alla richiesta. Per i nuovi valori massimi per gli account di archiviazione BLOB, vedere [Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) (Annuncio di account di archiviazione di dimensioni maggiori a scalabilità più elevata).

<sup>3</sup> Limitato solo dai valori massimi per il traffico in ingresso/uscita dell'account. Il *traffico in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.  

<sup>4</sup>Le opzioni di ridondanza di Archiviazione di Azure includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona. Disponibile solo per i BLOB in blocchi. 
* **LRS**: archiviazione con ridondanza locale. 
