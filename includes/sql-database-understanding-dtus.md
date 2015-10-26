L'unità di velocità effettiva Database (DTU) è l'unità di misura nel Database SQL che rappresenta la potenza relativa di un database per l’elaborazione delle transazioni date le limitazioni dell'hardware che determina il database. La DTU è un concetto simile alla potenza di un'automobile. Descrive le prestazioni relative di un database a un altro. Come per la potenza di un'automobile, di quanti più database dispone la DTU più potenza ha. La potenza è una funzione di numerosi e diversi fattori: peso, capacità, turbo del motore, sistema di scarico. Analogamente, la DTU è una funzione della quantità e del tipo di memoria, del calcolo e del disco che può utilizzare il database. È possibile leggere i dettagli sulla modalità di definizione della DTU nella [Panoramica del Benchmark](https://msdn.microsoft.com/library/azure/dn741327.aspx)).

Esempio: un database di base ha 5 DTU, vale a dire che ha una capacità di molto inferiore per la velocità effettiva delle transazioni rispetto a un database Premium P11 che ha 1750 DTU.

![DTU singolo database per serie e livello](./media/sql-database-understanding-dtus/single_db_dtus.png)

Il valore di DTU per singoli database si traduce direttamente in eDTU per i database elastici. Ad esempio, un database in un pool di database elastici Basic offre fino a 5 eDTU. Ovvero la stessa prestazione di un database singolo Basic. La differenza è che il database elastico non utilizzerà eDTU dal pool fino a quando necessario.

![Pool elastici per serie](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Un semplice esempio: Si prenda in considerazione un pool di database elastici Basic con 1000 DTU ed inserire 800 database in esso. Finché vengono utilizzati solo 200 degli 800 database in qualsiasi momento (5 DTU X 200 = 1000), non si raggiungerà la capacità del pool, e le prestazioni del database non perderanno qualità.. Questo esempio è semplificato per maggiore chiarezza. La matematica reale è un po' più complessa. Il portale esegue i calcoli matematici per l'utente e fornisce un'indicazione in base all'utilizzo cronologico di database. Vedere [Considerazioni sul prezzo e sulle prestazioni per un pool di database elastici](../articles/sql-database/sql-database-elastic-pool-guidance.md) per informazioni sulle indicazioni o per eseguire i calcoli matematici personalmente.

<!---HONumber=Oct15_HO3-->