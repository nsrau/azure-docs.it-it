
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Per ridurre i costi, è possibile sospendere e riprendere le risorse di calcolo su richiesta. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno. Mentre il database è sospeso, non verranno addebitate DWU.

Quando si sospende un database:

* Le risorse di calcolo e memoria vengono restituite al pool di risorse disponibili nel data center.
* I costi delle DWU sono pari a zero per la durata della pausa.
* L'archivio dati non è interessato e i dati rimangano invariati. 
* SQL Data Warehouse annulla tutte le operazioni in esecuzione o in coda.

<!---HONumber=AcomDC_0427_2016-->