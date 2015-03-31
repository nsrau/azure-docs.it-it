<properties 
	pageTitle="Migrazione di federazioni" 
	description="Illustra i passaggi per eseguire la migrazione di un'app esistente compilata con la funzionalità Federazioni al modello di scalabilità elastica." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Migrazione di federazioni 

La funzionalità Federazioni del database SQL di Azure verrà ritirata insieme alle edizioni Web/Business nel settembre 2015. Dopo il ritiro, le applicazioni che usano la funzionalità Federazioni cesseranno di funzionare. Per garantire una migrazione corretta, è fortemente consigliabile avviare le iniziative di migrazione al più presto, in modo da disporre di tempo sufficiente per la pianificazione e l'esecuzione. Questo documento fornisce il contesto, gli esempi e un'introduzione all'utilità di migrazione federazioni che illustra come eseguire correttamente la migrazione di un'applicazione corrente con federazioni alle [API della versione di anteprima di Scalabilità elastica del database SQL di Azure](http://go.microsoft.com/?linkid=9862592). Vengono illustrate in dettaglio le procedure suggerite per eseguire la migrazione di un'applicazione con federazioni senza alcuno spostamento dei dati.

La migrazione di un'applicazione con federazioni esistente a una che usa le API di Scalabilità elastica comporta tre passaggi principali.

1. [Creare il Gestore mappe partizioni da una radice di federazione] 
2. [Modificare l'applicazione esistente]
3. [Disattivare i membri di federazione esistenti]
    

### Lo strumento di migrazione di esempio
Per semplificare questo processo è stata creata un'[utilità di migrazione federazioni](http://go.microsoft.com/?linkid=9862613). L'utilità esegue i passaggi 1 e 3. 

## <a name="create-shard-map-manager"></a>Creare il Gestore mappe partizioni da una radice di federazione
Il primo passaggio per la migrazione di un'applicazione con federazioni consiste nel clonare i metadati di una radice di federazione nei costrutti di un gestore delle mappe partizioni. 

![Clone the federation root to the shard map manager][1]
 
Iniziare con un'applicazione con federazioni esistente in un ambiente di test.
 
Usare l'**utilità di migrazione federazioni** per clonare i metadati della radice di federazione nei costrutti del [Gestore mappe partizioni](http://go.microsoft.com/?linkid=9862595) di Scalabilità elastica. Analogamente a una radice di federazione, il database di gestione delle mappe partizioni è un database autonomo che contiene le mappe partizioni (ovvero le federazioni), i riferimenti alle partizioni (ovvero i membri di federazione) e i rispettivi mapping di intervalli. 

La clonazione della radice di federazione nel Gestore mappe partizioni consiste nella copia e nella traduzione dei metadati. I metadati nella radice di federazione non vengono modificati. Tenere presente che la clonazione della radice di federazione mediante l'utilità di migrazione federazioni è un'operazione legata a un momento specifico e che le eventuali modifiche alla radice di federazione o alle mappe partizioni non saranno rispecchiate nell'archivio dati corrispondente. Se durante il test delle nuove API vengono apportate modifiche alla radice di federazione, è possibile usare l'utilità di migrazione federazioni per aggiornare le mappe partizioni in modo da rappresentare lo stato corrente. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## Modificare l'applicazione esistente 

Dopo aver creato il Gestore mappe partizioni e aver registrato i membri di federazione e gli intervalli nel Gestore mappe partizioni (mediante l'utilità di migrazione), è possibile modificare l'applicazione con federazioni esistente per l'uso delle API di Scalabilità elastica. Come illustrato nella figura precedente, le connessioni dell'applicazione tramite le API di Scalabilità elastica verranno instradate mediante il Gestore mappe partizioni ai membri di federazione appropriati (che ora sono anche partizioni). Il mapping dei membri di federazione al Gestore mappe partizioni consente l'esecuzione simultanea di due versioni di un'applicazione: una con federazioni e una che usa Scalabilità elastica, per verificare la funzionalità.   

Durante la migrazione dell'applicazione, sarà necessario apportare due modifiche essenziali all'applicazione esistente.


#### Modifica 1: Creare un'istanza di un oggetto Gestore mappe partizioni 

A differenza delle federazioni, le API di Scalabilità elastica interagiscono con il Gestore mappe partizioni attraverso la classe **ShardMapManager**. Per creare un'istanza di un oggetto **ShardMapManager** e una mappa partizioni, procedere come segue:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Modifica 2: Instradare le connessioni alla partizione appropriata 

Con le federazioni, la connessione a uno specifico membro di federazione viene stabilita mediante il comando USE FEDERATION, come illustrato di seguito:  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Con le API di scalabilità elastica, viene stabilita una connessione a una specifica partizione mediante il [routing dipendente dai dati](./sql-database-elastic-scale-data-dependent-routing.md) con il metodo **OpenConnectionForKey** per la classe **RangeShardMap**. 

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

I passaggi descritti in questa sezione sono necessari, ma potrebbero non affrontare tutti i possibili scenari di migrazione. Per altre informazioni, vedere la [panoramica concettuale sulla scalabilità elastica](./sql-database-elastic-scale-introduction.md) e la [guida di riferimento sulle API](http://go.microsoft.com/?linkid=9862604).

## Disattivare i membri di federazione esistenti 

![Switch out the federation members for the shards][3]

Dopo aver modificato l'applicazione con l'inclusione delle API di scalabilità elastica, l'ultimo passaggio per la migrazione di un'applicazione con federazioni consiste nel disattivare i membri di federazione usando il comando **SWITCH OUT**. Per altre informazioni, vedere in MSDN la documentazione di riferimento su [ALTER FEDERATION (database SQL di Azure](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx). Il risultato finale dell'esecuzione del comando **SWITCH OUT** su uno specifico membro di federazione è la rimozione di tutti i metadati e i vincoli della federazione. Il membro di federazione diventerà un normale database SQL di Azure, non diverso da qualsiasi altro.  

Si noti che l'esecuzione del comando **SWITCH OUT** su un membro di federazione è un'operazione a senso unico e non può essere annullata. Dopo l'esecuzione non sarà più possibile aggiungere il database risultante a una federazione, né usare i comandi USING FEDERATION su di esso. 

Per eseguire la disattivazione di un membro di federazione, al comando ALTER FEDERATION è stato aggiunto un argomento SWITCH OUT.  Anche se si può eseguire il comando su singoli membri di federazione, l'utilità di migrazione federazioni consente di eseguire a livello di codice l'iterazione tra tutti i membri di federazione. 

Dopo la disattivazione di tutti i membri di federazione esistenti, la migrazione dell'applicazione è stata completata.  
L'utilità di migrazione federazioni offre la possibilità di: 

1.    Creare un clone della radice di federazione in un Gestore mappe partizioni.  Si può scegliere di inserire il Gestore mappe partizioni esistente in un nuovo database SQL di Azure (scelta consigliata) o nel database radice di federazione esistente.
2.    Eseguire il comando SWITCH OUT su tutti i membri di una federazione.


## Confronto delle funzionalità  
Anche se la scalabilità elastica offre molte funzionalità aggiuntive (ad esempio [esecuzione di query su più partizioni](./sql-database-elastic-scale-multishard-querying.md), [divisione e unione di partizioni](./sql-database-elastic-scale-overview-split-and-merge.md), [elasticità di partizionamento](./sql-database-elastic-scale-elasticity.md), [memorizzazione nella cache sul lato client](./sql-database-elastic-scale-shard-map-management.md)e molte altre), esistono alcune importanti funzionalità delle federazioni non supportate in Scalabilità elastica.
  

- L'uso di **FILTERING=ON**. Attualmente Scalabilità elastica non supporta i filtri a livello di riga. Una possibile soluzione consiste nell'inserire la logica di filtro nella query eseguita sulla partizione, come illustrato di seguito: 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

Restituisce lo stesso risultato di:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- La funzionalità di **suddivisione** di Scalabilità elastica non è completamente online. Durante un'operazione di suddivisione, ogni singolo shardlet viene portato offline per la durata dello spostamento.
- La funzionalità di suddivisione di Scalabilità elastica richiede la gestione manuale dello schema e del provisioning del database.

## Considerazione aggiuntive

* Nell'autunno 2015 le edizioni Web e Business e la funzionalità Federazioni verranno ritirate.  Come parte della migrazione di un'applicazione con federazioni, è consigliabile eseguire test di prestazioni sulle edizioni Basic, Standard e Premium. 

* Eseguendo l'istruzione SWITCH OUT su un membro di federazione, il database risultante potrà sfruttare tutte le funzionalità del database SQL di Azure (ad esempio nuove edizioni, backup, recupero temporizzato, controllo e così via) 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[Creare il Gestore mappe partizioni da una radice di federazione]:#create-shard-map-manager
[Modificare l'applicazione esistente]:#Modify-the-Existing-Application
[Disattivare i membri di federazione esistenti]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png

<!--HONumber=47-->
