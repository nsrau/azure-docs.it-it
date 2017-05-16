L'importo fatturato per l'uso di Archiviazione di Azure dipende dall'account di archiviazione. I costi di archiviazione sono determinati in base ai fattori seguenti: area/posizione, tipo di account, capacità di archiviazione, schema di replica, transazioni di archiviazione e uscita dei dati.

* L'area si riferisce all'area geografica in cui si trova l'account.
* Il tipo di account si riferisce all'uso di un account di archiviazione per utilizzo generico o a un account di archiviazione BLOB. Con un account di archiviazione BLOB, anche il livello di accesso determina il modello di fatturazione per l'account.
* La capacità di archiviazione si riferisce alla quantità di unità di servizio dell'account di archiviazione in uso per l'archiviazione dei dati.
* La replica determina il numero di copie dei dati conservate contemporaneamente e le posizioni di archiviazione.
* Le transazioni si riferiscono a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.
* L'uscita dei dati si riferisce ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione non in esecuzione nella stessa area accede ai dati nell'account di archiviazione, viene addebitata una quota per l'uscita dei dati. Per i servizi di Azure è possibile raggruppare dati e servizi nello stesso data center per ridurre o evitare del tutto gli addebiti per il trasferimento dei dati in uscita.

La pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) fornisce informazioni dettagliate sui prezzi in base a tipo di account, capacità di archiviazione, replica e transazioni. Nella pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) per stimare i costi.

