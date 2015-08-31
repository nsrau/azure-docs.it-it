<properties 
    pageTitle="Migrazione di federazioni" 
    description="In questo articolo vengono illustrati i passaggi per eseguire la migrazione di un'app esistente compilata con la funzionalità Federazioni al modello del database elastico." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/14/2015" 
    ms.author="sidneyh"/>

# Migrazione di federazioni 

La funzionalità Federazioni del database SQL di Azure verrà ritirata insieme alle edizioni Web/Business nel settembre 2015. Dopo il ritiro, le applicazioni che usano la funzionalità Federazioni cesseranno di funzionare. Per garantire una migrazione corretta, è fortemente consigliabile avviare le iniziative di migrazione al più presto, in modo da disporre di tempo sufficiente per la pianificazione e l'esecuzione.

Se l'applicazione non è ancora pronta per l'uso senza federazioni, contattare il supporto tecnico Microsoft utilizzando le istruzioni indicate [qui](https://support.microsoft.com/kb/3087180).

Questo documento fornisce il contesto, gli esempi e un'introduzione all'utilità di migrazione federazioni che illustra come eseguire correttamente la migrazione di un'applicazione corrente con federazioni alle API della [libreria client del database elastico](http://go.microsoft.com/?linkid=9862592) per la partizione. Vengono illustrate in dettaglio le procedure suggerite per eseguire la migrazione di un'applicazione con federazioni senza alcuno spostamento dei dati.

La migrazione di un'applicazione con federazioni esistente a una che usa gli strumenti di database elastici comporta tre passaggi principali.

1. [Creare un Gestore mappe partizioni da una radice di federazione](#create-a-shard-map-manager-from-a-federation-root) 
2. [Modificare l'applicazione esistente](#modify-the-existing-application)
3. [Disattivare i membri di federazione esistenti](#switch-out-existing-federation-members)
    

### Lo strumento di migrazione di esempio
Per semplificare questo processo è stata creata un'[utilità di migrazione federazioni](http://go.microsoft.com/?linkid=9862613). L'utilità esegue i passaggi 1 e 3.

## Creare un Gestore mappe partizioni da una radice di federazione
Il primo passaggio per la migrazione di un'applicazione con federazioni consiste nel clonare i metadati di una radice di federazione nei costrutti di un gestore delle mappe partizioni.

![Creare un clone della radice di federazione in un Gestore mappe partizioni][1]
 
Iniziare con un'applicazione con federazioni esistente in un ambiente di test.
 
Usare l'**utilità di migrazione federazioni** per clonare i metadati della radice di federazione nei costrutti del [Gestore mappe partizioni](http://go.microsoft.com/?linkid=9862595) della libreria client del database elastico. Analogamente a una radice di federazione, il database di gestione delle mappe partizioni è un database autonomo che contiene le mappe partizioni (ovvero le federazioni), i riferimenti alle partizioni (ovvero i membri di federazione) e i rispettivi mapping di intervalli.

La clonazione della radice di federazione nel Gestore mappe partizioni consiste nella copia e nella traduzione dei metadati. I metadati nella radice di federazione non vengono modificati. Tenere presente che la clonazione della radice di federazione mediante l'utilità di migrazione federazioni è un'operazione legata a un momento specifico e che le eventuali modifiche alla radice di federazione o alle mappe partizioni non saranno rispecchiate nell'archivio dati corrispondente. Se durante il test delle nuove API vengono apportate modifiche alla radice di federazione, è possibile usare l'utilità di migrazione federazioni per aggiornare le mappe partizioni in modo da rappresentare lo stato corrente.

![Eseguire la migrazione dell'app esistente in modo che usi le API degli strumenti di database elastici][2]

## Modificare l'applicazione esistente 

Dopo aver creato il Gestore mappe partizioni e aver registrato i membri di federazione e gli intervalli nel Gestore mappe partizioni (mediante l'utilità di migrazione), è possibile modificare l'applicazione con federazioni esistente per l'uso la libreria client del database client. Come illustrato nella figura precedente, le connessioni dell'applicazione tramite tali API verranno instradate mediante il Gestore mappe partizioni ai membri di federazione appropriati (che ora sono anche partizioni). Il mapping dei membri di federazione al Gestore mappe partizioni consente l'esecuzione simultanea di due versioni di un'applicazione: una con federazioni e una che usa la libreria client del database elastico, per verificare la funzionalità.

Durante la migrazione dell'applicazione, sarà necessario apportare due modifiche essenziali all'applicazione esistente.


#### Modifica 1: creare un'istanza di un oggetto Gestore mappe partizioni 

A differenza delle federazioni, le API degli strumenti di database elastici interagiscono con il Gestore mappe partizioni attraverso la classe **ShardMapManager**. Per creare un'istanza di un oggetto **ShardMapManager** e una mappa partizioni, procedere come segue:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Modifica 2: instradare le connessioni alla partizione appropriata 

Con le federazioni, la connessione a uno specifico membro di federazione viene stabilita mediante il comando USE FEDERATION, come illustrato di seguito:

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Con le API degli strumenti di database elastici, la connessione a una specifica partizione viene stabilita mediante il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) con il metodo **OpenConnectionForKey** sulla classe **RangeShardMap**.

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

I passaggi descritti in questa sezione sono necessari, ma potrebbero non affrontare tutti i possibili scenari di migrazione. Per altre informazioni, vedere la [panoramica sugli strumenti di database elastici](sql-database-elastic-scale-introduction.md) e il [riferimento dell'API](http://go.microsoft.com/?linkid=9862604).

## Disattivare i membri di federazione esistenti 

![Disattivare i membri della federazione per le partizioni][3]

Dopo aver modificato l'applicazione con l'inclusione delle API degli strumenti di database elastici, l'ultimo passaggio per completare la migrazione di un'applicazione con federazioni consiste nel disattivare i membri di federazione usando il comando **SWITCH OUT**. Per altre informazioni, vedere in MSDN la documentazione di riferimento su [ALTER FEDERATION (database SQL di Azure](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx))). Il risultato finale dell'esecuzione del comando **SWITCH OUT** su uno specifico membro di federazione è la rimozione di tutti i metadati e i vincoli della federazione. Il membro di federazione diventerà un normale database SQL di Azure, non diverso da qualsiasi altro.

Si noti che l'esecuzione del comando **SWITCH OUT** su un membro di federazione è un'operazione a senso unico e non può essere annullata. Dopo l'esecuzione non sarà più possibile aggiungere il database risultante a una federazione, né usare i comandi USING FEDERATION su di esso.

Per eseguire la disattivazione di un membro di federazione, al comando ALTER FEDERATION è stato aggiunto un argomento SWITCH OUT. Anche se si può eseguire il comando su singoli membri di federazione, l'utilità di migrazione federazioni consente di eseguire a livello di codice l'iterazione tra tutti i membri di federazione.

Dopo la disattivazione di tutti i membri di federazione esistenti, la migrazione dell'applicazione è stata completata. Quando tutti i membri della federazione sono stati disattivati, si consiglia di eseguire un'operazione DROP FEDERATION nella radice. Tale operazione non influirà sui membri precedenti, ma consente al database radice di eseguire facilmente la migrazione completa della versione Web e Business.
  
L'utilità di migrazione federazioni offre la possibilità di:

1.    Creare un clone della radice di federazione in un Gestore mappe partizioni. Si può scegliere di inserire il Gestore mappe partizioni esistente in un nuovo database SQL di Azure (scelta consigliata) o nel database radice di federazione esistente.
2.    Eseguire il comando SWITCH OUT su tutti i membri di una federazione.


## Confronto tra le funzionalità

Anche se gli strumenti di database elastici offrono molte funzionalità aggiuntive (ad esempio [esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md), [suddivisione e unione di partizioni](sql-database-elastic-scale-overview-split-and-merge.md), [elasticità di partizionamento](sql-database-elastic-scale-elasticity.md), [memorizzazione nella cache lato client](sql-database-elastic-scale-shard-map-management.md) e molte altre), esistono alcune importanti funzionalità delle federazioni non supportate negli strumenti database elastici.
  
- L'uso di **FILTERING=ON**. In alternativa, si consiglia di usare la sicurezza a livello di riga per filtrare le righe. In modo analogo all'applicazione di filtri nelle federazione, la sicurezza a livello di riga aggiunge automaticamente un predicato a tutte le query di una tabella di partizione. Per ulteriori dettagli, vedere [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md). 
 
 In alternativa, creare la logica di filtro nella query eseguita sulla partizione. Ad esempio:

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 Restituisce lo stesso risultato di:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- La funzionalità di **suddivisione** degli strumenti di database elastici non è completamente online. Durante un'operazione di suddivisione, ogni singolo shardlet viene portato offline per la durata dello spostamento.
- La funzionalità di suddivisione degli strumenti di database elastici richiede la gestione manuale dello schema e del provisioning del database.

## Considerazione aggiuntive

* Nell'autunno 2015 le edizioni Web e Business e la funzionalità Federazioni verranno ritirate. Come parte della migrazione di un'applicazione con federazioni, è consigliabile eseguire test di prestazioni sulle edizioni Basic, Standard e Premium. 

* Eseguendo l'istruzione SWITCH OUT su un membro di federazione, il database risultante potrà sfruttare tutte le funzionalità del database SQL di Azure (ad esempio nuove edizioni, backup, recupero temporizzato, controllo e così via)

##Occorre più tempo per la migrazione? 
Se l'applicazione non è ancora pronta per l'uso senza federazioni, contattare il supporto tecnico Microsoft utilizzando le istruzioni indicate [qui](https://support.microsoft.com/kb/3087180).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!---HONumber=August15_HO8-->