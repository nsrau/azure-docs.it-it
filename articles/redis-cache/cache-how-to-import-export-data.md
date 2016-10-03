<properties 
	pageTitle="Importare ed esportare dati in Cache Redis di Azure | Microsoft Azure" 
	description="Informazioni su come importare ed esportare dati da e verso l'archivio BLOB con le istanze di Cache Redis di Azure Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>  

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>  

# Importare ed esportare dati in Cache Redis di Azure

L'importazione/esportazione è un'operazione di gestione dati di Cache Redis di Azure che consente di importare o esportare dati da Cache Redis di Azure, importando o esportando uno snapshot del database di Cache Redis (RDB) da una cache Premium a un BLOB di pagine in un account di archiviazione di Azure. L'operazione Importa/Esporta consente di eseguire la migrazione tra diverse istanze di Cache Redis di Azure o di popolare la cache con i dati prima dell'uso.

Questo articolo è una guida all'importazione e all'esportazione dei dati con Cache Redis di Azure e include le risposte alle domande più frequenti.

>[AZURE.IMPORTANT] La funzionalità Importazione/Esportazione è disponibile in anteprima solo per le cache del [piano Premium](cache-premium-tier-intro.md).

## Importazione

È possibile usare l'importazione per spostare i file RDB compatibili con Redis da qualsiasi server Redis in esecuzione in qualsiasi cloud o ambiente, compresi i server Redis in esecuzione in Linux, Windows o in qualsiasi provider di cloud tra cui Amazon Web Services. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

>[AZURE.NOTE] Prima di avviare l'operazione di importazione, assicurarsi che il file o i file di database Redis (RDB) siano caricati nei BLOB di pagine in Archiviazione di Azure, nella stessa area e nella stessa sottoscrizione dell'istanza di Cache Redis di Azure. Per altre informazioni, vedere [Introduzione all'archivio BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md). Se il file RDB è stato esportato con la funzionalità [Esportazione di Cache Redis di Azure](#export), è già archiviato in un BLOB di pagine ed è pronto per l'importazione.

1. Per importare uno o più BLOB di cache esportati, [passare alla cache](cache-configure.md#configure-redis-cache-settings) nel Portale di Azure e fare clic su **Importa dati** nel pannello **Impostazioni** dell'istanza della cache.

    ![Importa dati][cache-import-data]

2. Fare clic su **Scegliere i BLOB** e selezionare l'account di archiviazione che contiene i dati da importare.

    ![Scegliere l'account di archiviazione][cache-import-choose-storage-account]

3. Fare clic sul contenitore che contiene i dati da importare.

    ![Scegliere il contenitore][cache-import-choose-container]

4. Selezionare uno o più BLOB da importare facendo clic sull'area a sinistra del nome del BLOB e quindi fare clic su **Seleziona**.

    ![Scegliere il BLOB][cache-import-choose-blobs]

5. Fare clic su **Importa** per avviare il processo di importazione.

    >[AZURE.IMPORTANT] Durante il processo di importazione la cache non è accessibile ai client della cache ed eventuali dati esistenti nella cache vengono eliminati.

    ![Importazione][cache-import-blobs]  

    È possibile controllare lo stato dell'operazione di importazione tramite le notifiche del Portale di Azure oppure visualizzando gli eventi nel [log di controllo](cache-configure.md#support-amp-troubleshooting-settings).

    ![Stato dell'importazione][cache-import-data-import-complete]  


## Esportazione

L'esportazione consente di esportare i dati memorizzati in Cache Redis di Azure in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella VM che ospita l'istanza del server Cache Redis di Azure e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

1. Per esportare il contenuto corrente della cache nell'archivio, [passare alla cache](cache-configure.md#configure-redis-cache-settings) nel Portale di Azure e fare clic su **Esporta dati** nel pannello **Impostazioni** dell'istanza della cache.

    ![Scegliere il contenitore di archiviazione][cache-export-data-choose-storage-container]

2. Fare clic su **Scegliere il contenitore di archiviazione** e selezionare l'account di archiviazione desiderato. L'account di archiviazione deve trovarsi nella stessa area e nella stessa sottoscrizione della cache.

    >[AZURE.IMPORTANT] La funzionalità Importazione/Esportazione è compatibile con i BLOB di pagine, supportati dagli account di archiviazione di Azure Resource Manager e della versione classica ma al momento non supportati dagli [account di archiviazione BLOB](../storage/storage-blob-storage-tiers.md#blob-storage-accounts).

    ![Account di archiviazione][cache-export-data-choose-account]

3. Scegliere il contenitore BLOB desiderato e fare clic su **Seleziona**. Per usare un nuovo contenitore, fare clic su **Aggiungi contenitore** per aggiungerlo prima e quindi selezionarlo dall'elenco.

    ![Scegliere il contenitore di archiviazione][cache-export-data-container]

4. Digitare un valore in **Prefisso nome BLOB** e fare clic su **Esporta** per avviare il processo di esportazione. Il prefisso del nome BLOB viene usato per i nomi dei file generati da questa operazione di esportazione.

    ![Esporta][cache-export-data]  

    È possibile controllare lo stato dell'operazione di esportazione tramite le notifiche del Portale di Azure oppure visualizzando gli eventi nel [log di controllo](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]  

    Durante il processo di esportazione le cache rimangono disponibili per l'uso.


## Domande frequenti su Importazione/Esportazione

Questa sezione contiene le domande frequenti relative alla funzionalità Importazione/Esportazione.

-	[Con quali piani tariffari è possibile usare Importazione/Esportazione?](#what-pricing-tiers-can-use-importexport)
-	[È possibile importare dati da qualsiasi server Redis?](#can-i-import-data-from-any-redis-server)
-	[La cache sarà disponibile durante un'operazione di Importazione/Esportazione?](#will-my-cache-be-available-during-an-importexport-operation)
-	[È possibile usare Importazione/Esportazione con il cluster Redis?](#can-i-use-importexport-with-redis-cluster)
-	[Come funziona Importazione/Esportazione con un'impostazione databases personalizzata?](#how-does-importexport-work-with-a-custom-databases-setting)
-	[Quali sono le differenze tra la funzionalità Importazione/Esportazione e la persistenza di Redis?](#how-is-importexport-different-from-redis-persistence)
-	[È possibile automatizzare la funzionalità Importazione/Esportazione con PowerShell, l'interfaccia della riga di comando o altri client di gestione?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-	[Durante l'operazione di Importazione/Esportazione è stato ricevuto un errore di timeout. Da cosa dipende il problema?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-	[Durante l'esportazione dei dati nell'archivio BLOB di Azure è stato visualizzato un errore. Che cosa è successo?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### Con quali piani tariffari è possibile usare Importazione/Esportazione?

La funzionalità Importazione/Esportazione è disponibile solo con il piano tariffario Premium.

### È possibile importare dati da qualsiasi server Redis?

Sì. Oltre a importare i dati esportati da istanze di Cache Redis di Azure, è possibile importare i file RDB da qualsiasi server Redis in esecuzione in qualsiasi cloud o ambiente, come Linux, Windows, o provider di cloud, come Amazon Web Services. A tale scopo, caricare il file RDB dal server Redis desiderato in un BLOB di pagine di un account di archiviazione di Azure e quindi importarlo nell'istanza di Cache Redis di Azure Premium. È ad esempio possibile importare i dati della cache di produzione e importarli in una cache usata in un ambiente di gestione temporanea per i test o la migrazione.

### La cache sarà disponibile durante un'operazione di Importazione/Esportazione?

-	**Esportazione**: durante un'operazione di esportazione le cache rimangono disponibili ed è possibile continuare a usarle.
-	**Importazione**: quando si avvia un'operazione di importazione le cache non sono più disponibili, ma tornano disponibili al termine dell'operazione.


### È possibile usare Importazione/Esportazione con il cluster Redis?

Sì. È inoltre possibile usare questa funzionalità tra una cache di cluster e una non di cluster. Poiché il Cluster Redis [supporta solo database 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), i dati nei database diversi da 0 non verranno importati. Quando si importano dati della cache di cluster, le chiavi vengono ridistribuite tra le partizioni del cluster.

### Come funziona Importazione/Esportazione con un'impostazione databases personalizzata?

Alcuni piani tariffari hanno [limiti di databases](cache-configure.md#databases) diversi, quindi esistono alcune considerazioni da tenere presente durante l'importazione se si è configurato un valore personalizzato per l'impostazione `databases` durante la creazione della cache.

-	Quando si esegue l'importazione in un piano tariffario con un limite di `databases` più basso del piano da cui è stata eseguita l'esportazione:
	-	Se si usa il numero predefinito di `databases` che è 16 per tutti i piani tariffari, non viene perso alcun dato.
	-	Se si usa un numero personalizzato di `databases` compreso nei limiti per il piano in cui si esegue l'importazione, non viene perso alcun dato.
	-	Se i dati esportati contenevano dati in un database che superano i limiti per il nuovo piano, i dati dei database più elevati non vengono importati.

### Quali sono le differenze tra la funzionalità Importazione/Esportazione e la persistenza di Redis?

La persistenza di Cache Redis di Azure consente di salvare in modo permanente in Archiviazione di Azure i dati archiviati in Redis. Quando si configura la persistenza, Cache Redis di Azure archivia uno snapshot della cache Redis in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, i dati della cache vengono ripristinati automaticamente usando lo snapshot più recente. Per altre informazioni, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).

La funzionalità Importazione/Esportazione consente di importare o esportare dati in Cache Redis di Azure, ma non di configurare il backup e il ripristino usando la persistenza di Redis.


### È possibile automatizzare la funzionalità Importazione/Esportazione con PowerShell, l'interfaccia della riga di comando o altri client di gestione?

Sì, per istruzioni relative a PowerShell vedere [To import a Redis cache](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) (Per importare una Cache Redis) e [To export a Redis cache](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache) (Per esportare una Cache Redis).



### Durante l'operazione di Importazione/Esportazione è stato ricevuto un errore di timeout. Da cosa dipende il problema?

Se il pannello **Importa dati** o **Esporta dati** rimane aperto per più di 15 minuti prima che l'operazione venga avviata, verrà visualizzato un errore simile al seguente.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Per risolvere il problema, avviare l'operazione di importazione o esportazione prima che scadano i 15 minuti.

### Durante l'esportazione dei dati nell'archivio BLOB di Azure è stato visualizzato un errore. Che cosa è successo?

La funzionalità Importazione/Esportazione funziona solo con file RDB archiviati come BLOB di pagine. Al momento non sono supportati altri tipi di BLOB, inclusi gli account di archiviazione BLOB con livelli di accesso frequente e non frequente.


## Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

-	[Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md)

  
<!-- IMAGES -->  
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png

<!---HONumber=AcomDC_0921_2016-->