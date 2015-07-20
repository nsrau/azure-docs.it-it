
| Identificatore limite | Limite | Commenti |
|----------------- | ------|--------- |
| Numero massimo di credenziali dell'account di archiviazione | 64 | |
| Numero massimo di contenitori di volumi | 64 | |
| Numero massimo di volumi | 255 | |
| Numero massimo di modelli di larghezza di banda | 25 | |
| Numero massimo di pianificazioni per ogni modello di larghezza di banda | 168 | Una pianificazione per ogni ora, ogni giorno della settimana (24*7). |
| Dimensioni massime di un volume | 64 TB | |
| Numero massimo di connessioni iSCSI | 512 | |
| Numero massimo di connessioni iSCSI dagli iniziatori | 512 | |
| Numero massimo di record di controllo di accesso per dispositivo | 64 | |
| Numero massimo di volumi per criteri di backup | 24 | |
| Numero massimo di backup mantenuti per criteri di backup | 64 | |
| Numero massimo di pianificazioni per criteri di backup | 10 | |
| Numero massimo di snapshot di qualsiasi tipo che è possibile mantenere per volume | 256 | Sono inclusi gli snapshot locali e quelli cloud. |
| Numero massimo di snapshot che possono essere presenti in qualsiasi dispositivo | 10.000 | |
| Numero massimo di volumi che possono essere elaborati in parallelo per backup, ripristino o clonazione | 16 |<ul><li>Se sono presenti più di 16 volumi, verranno elaborati in sequenza man mano che gli slot di elaborazione diventano disponibili.</li><li>Non è possibile eseguire nuovi backup di un volume clonato o ripristinato fino a quando l'operazione non è completata.</li></ul>|
| Tempo di ripristino e clonazione | < 2 minuti | <ul><li>Il volume viene reso disponibile entro 2 minuti dell'operazione di ripristino o clonazione, indipendentemente dalle dimensioni del volume.</li><li>È possibile che inizialmente le prestazioni del volume siano più lente del normale, poiché la maggior parte dei dati e dei metadati si trova ancora nel cloud. Le prestazioni possono migliorare con il flusso dei dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. La velocità può dipendere dalla larghezza di banda di Internet nel cloud.</li><li>L'operazione di ripristino o clonazione è completa quando tutti i metadati si trovano nel dispositivo.</li><li>Non è possibile eseguire le operazioni di backup fino a quando l'operazione di ripristino o clonazione non è stata completata.|
| Disponibilità del ripristino di tipo thin | Ultimo failover | |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello SSD)* | 920/720 MB/s con una singola interfaccia di rete 10GbE | Fino a 2x con MPIO e due interfacce di rete. |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello HDD)* | 120/250 MB/s |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello Cloud)* | 11/41 MB/s | La velocità effettiva di lettura dipende dai client che generano e gestiscono una profondità della coda I/O sufficiente. |

&#42; La velocità effettiva massima per ciascun tipo di I/O è stata misurata con scenari di scrittura al 100% e scenari di lettura al 100%. La velocità effettiva potrebbe essere inferiore a seconda delle condizioni della rete e della combinazione I/O.

<!---HONumber=July15_HO2-->