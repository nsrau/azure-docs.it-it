## Informazioni sull'archiviazione di accodamento

L'archiviazione di accodamento di Azure è un servizio per l'archiviazione di grandi numeri di messaggi a cui è possibile accedere da qualsiasi posto del mondo tramite chiamate autenticate con HTTP o HTTPS. Un singolo messaggio della coda può essere grande fino a 64 KB e una coda può contenere milioni di messaggi, fino al limite di capacità totale di un account di archiviazione. Un account di archiviazione può includere fino a 500 TB di dati relativi a BLOB, code e tabelle. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

Di seguito sono riportati gli utilizzi più comuni per il servizio di archiviazione di accodamento.

-   <span>Creazione di un backlog di lavoro da elaborare in modo asincrono</span>
-   Recapito dei messaggi da un ruolo Web di Azure a un ruolo di lavoro di Azure

## Concetti del servizio di accodamento

Il servizio di accodamento contiene i componenti seguenti:

![Coda1](./media/storage-queue-concepts-include/queue1.png)


- **Formato dell'URL:** è possibile fare riferimento alle code usando il formato di URL seguente:   
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
L'URL seguente fa riferimento a una delle code nel diagramma:  
	http://myaccount.queue.core.windows.net/imagesToDownload

-**Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

- **Coda:** una coda contiene un insieme di messaggi. Tutti i messaggi devono essere inseriti in una coda.

- **Messaggio:** un messaggio, in qualsiasi formato, con dimensione massima di 64 KB.



<!--HONumber=49-->