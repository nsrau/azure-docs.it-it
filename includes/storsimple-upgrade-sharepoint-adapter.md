
### Aggiornare SharePoint 2010 a SharePoint 2013 e quindi installare l'adattatore StorSomple per SharePoint

>[AZURE.IMPORTANT]Tutti i file precedentemente spostati in una risorsa di archiviazione esterna tramite RBS non saranno disponibili fino al termine dell’aggiornamento e fino a quando la funzionalità RBS non viene riabilitata. Per limitare l'impatto sugli utenti, eseguire l'aggiornamento o la reinstallazione in un periodo di manutenzione pianificata.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
#### Per eseguire l'aggiornamento di SharePoint 2010 a SharePoint 2013 e quindi installare l'adattatore

1. Nella farm di SharePoint 2010, annotare il percorso di archivio BLOB per i BLOB esternalizzati e i database del contenuto per cui RBS è abilitato. 

2. Installare e configurare la nuova farm di SharePoint 2013.

3. Spostare database, applicazioni, raccolte siti, dalla farm SharePoint 2010 alla nuova farm di SharePoint 2013. Per istruzioni, vedere la Panoramica del processo di aggiornamento a SharePoint 2013.

4. Installare l’adattatore StorSimple per SharePoint sulla nuova farm. Andare a[Installare l'adattatore StorSimple per SharePoint](#install-the-storsimple-adapter-for-sharepoint)per le procedure.

5. Utilizzando le informazioni annotate nel passaggio 1, abilitare RBS per lo stesso set di database del contenuto e fornire lo stesso percorso di archivio BLOB utilizzato nell'installazione di SharePoint 2010. Andare a[Configurare RBS](#configure-rbs)per le procedure. Dopo aver completato questo passaggio, i file esternalizzati in precedenza dovrebbero essere accessibili dalla nuova farm.

### Aggiornare l'adattatore StorSimple per SharePoint

>[AZURE.IMPORTANT]È consigliabile pianificare l'aggiornamento affinché si verifichi durante un’operazione di manutenzione pianificata per i motivi seguenti:
>
>- I contenuti esternalizzati in precedenza non saranno disponibili fino a quando l'adattatore viene reinstallato.
>
>- Qualsiasi contenuto caricato nel sito dopo avere disinstallato la versione precedente dell'adattatore StorSimple per SharePoint, ma prima di installare la nuova versione, verrà archiviato nel database del contenuto. È necessario spostare tale contenuto nel dispositivo StorSimple dopo aver installato il nuovo adattatore.


#### Per aggiornare l'adattatore StorSimple per SharePoint 

1. Disinstallare la versione precedente dell'adattatore StorSimple per SharePoint.

    >[AZURE.NOTE]RBS verrà disabilitato automaticamente nei database del contenuto. I BLOB esistenti rimarranno nel dispositivo StorSimple. Poiché RBS è disabilitato e i BLOB non sono stati migrati nuovamente ai database del contenuto, le richieste relative a tali BLOB avranno esito negativo.
 
2. Installare il nuovo adattatore StorSimple per SharePoint Il nuovo adattatore riconoscerà automaticamente i database del contenuto precedentemente abilitati o disabilitati per RBS e utilizzerà le impostazioni precedenti.

<!---HONumber=July15_HO5-->