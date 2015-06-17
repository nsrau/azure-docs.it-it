I dati dell'account di archiviazione vengono replicati per assicurarne la durabilità e l'elevata disponibilità, in modo da soddisfare il [contratto di servizio di Archiviazione di Azure](http://azure.microsoft.com/support/legal/sla/) anche in caso di errori hardware temporanei. Il servizio di archiviazione di Azure è distribuito in 15 aree geografiche in tutto il mondo e include il supporto per la replica dei dati tra aree geografiche. Sono disponibili varie opzioni per replicare i dati nell'account di archiviazione:

- Con *l'archiviazione con ridondanza locale* vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte all'interno di una singola struttura di una singola area. Questa opzione di replica protegge i dati dai normali errori hardware ma non dagli errori di una singola struttura.

	L'archiviazione con ridondanza locale viene offerta a tariffe scontate. Per la massima durabilità, è consigliabile usare l'archiviazione con ridondanza geografica descritta di seguito.

- Con *l'archiviazione con ridondanza della zona* vengono conservate tre copie dei dati. Tale tipo di archiviazione viene replicato per tre volte in due o tre strutture all'interno di una singola area o in due aree, fornendo una durabilità maggiore rispetto all'archiviazione con ridondanza locale. Questa opzione di replica assicura la durabilità dei dati all'interno di una singola area.
 
	L'archiviazione con ridondanza della zona fornisce un livello di durabilità maggiore rispetto all'archiviazione con ridondanza locale. Tuttavia, per la massima durabilità, è consigliabile usare l'archiviazione con ridondanza geografica descritta di seguito.

	> [AZURE.NOTE]L'archiviazione con ridondanza della zona è attualmente disponibile solo per i BLOB in blocchi. Tenere presente che dopo aver creato l'account di archiviazione e selezionato l'archiviazione con ridondanza della zona, non è possibile convertirlo per usarlo con un altro tipo di replica o viceversa.

- L'*archiviazione con ridondanza geografica* è abilitata per impostazione predefinita quando si crea l'account di archiviazione. Con tale tipo di archiviazione vengono conservate sei copie dei dati. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte in un'area secondaria situata a centinaia di chilometri di distanza dall'area primaria, fornendo il massimo livello di durabilità. In caso di errore nell'area primaria, il servizio di archiviazione di Azure eseguirà il failover all'area secondaria. L'archiviazione con ridondanza geografica assicura la durabilità dei dati in due aree distinte.

	> [AZURE.NOTE]Per la massima durabilità, è consigliabile usare l'archiviazione con ridondanza geografica rispetto all'archiviazione con ridondanza locale o della zona.

- *Archiviazione con ridondanza geografica e accesso in lettura*: garantisce tutti i vantaggi dell'archiviazione con ridondanza geografica e consente inoltre l'accesso in lettura ai dati nell'area geografica secondaria in caso di non disponibilità dell'area geografica principale. Questo tipo di archiviazione è consigliato per ottenere la massima disponibilità oltre alla durabilità dei dati.

Per informazioni dettagliate sulle opzioni di replica, vedere il [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/) e [Opzioni di ridondanza di Archiviazione di Azure](../articles/storage/storage-redundancy.md).
	
Le differenze di prezzo tra le varie opzioni di replica sono disponibili nella pagina [Prezzi di Archiviazione di Azure](http://azure.microsoft.com/pricing/details/storage/).

<!--HONumber=52-->
