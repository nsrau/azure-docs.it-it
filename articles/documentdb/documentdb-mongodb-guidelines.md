<properties 
	pageTitle="Visualizzare l'anteprima delle linee guida sullo sviluppo per account DocumentDB con supporto del protocollo per MongoDB | Microsoft Azure" 
	description="Informazioni su come visualizzare l'anteprima delle linee guida sullo sviluppo per account DocumentDB con supporto del protocollo per MongoDB, ora disponibile in anteprima." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# Visualizzare l'anteprima delle linee guida sullo sviluppo per account DocumentDB con supporto del protocollo per MongoDB

È possibile comunicare con Azure DocumentDB tramite uno dei [driver](https://docs.mongodb.org/ecosystem/drivers/) open source del client MongoDB. Il supporto del protocollo per MongoDB presuppone che i driver del client MongoDB comunichino con un endpoint server MongoDB 2.6 o versione successiva. DocumentDB supporta tale approccio aderendo al [protocollo](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/), di MongoDB versione 2.6. Si noti che il protocollo di rete versione 3.2 è quasi completamente supportato, ma alcune esperienze client, ad esempio le sessioni della shell di MongoDB versione 3.2, potrebbero indicare che "verrà effettuato il passaggio alla modalità legacy".

DocumentDB supporta le funzioni principali dell'API MongoDB, per eseguire operazioni CRUD (Create, Read, Update and Delete) sui dati, oltre ad eseguire query nel database. Le funzionalità implementate sono state classificate in base alle esigenze di piattaforme, framework, strumenti e modelli di applicazione comuni.

## Raccolte

> [AZURE.IMPORTANT] DocumentDB usa velocità effettiva riservata a livello di raccolta per offrire prestazioni garantite e prevedibili. Le raccolte in DocumentDB sono quindi entità fatturabili.

Le prenotazioni di prestazioni vengono applicate a livello di raccolta, in modo che le applicazioni possano adattare le prestazioni al livello minimo di contenitori di dati nel sistema. Il costo di una raccolta è quindi determinato dalla velocità effettiva con provisioning della raccolta, misurata in unità richiesta al secondo, e dallo spazio di archiviazione totale utilizzato, misurato in gigabyte. È possibile regolare la velocità effettiva con provisioning per tutta la durata di una raccolta, per adattarla alle diverse esigenze di elaborazione e ai modelli di accesso dell'applicazione. Per altre informazioni, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

DocumentDB con supporto del protocollo per raccolte di MongoDB viene creato per impostazione predefinita al piano tariffario Standard con 1.000 RU/s di velocità effettiva con provisioning. È possibile modificare la velocità effettiva con provisioning di ogni raccolta, come illustrato in [Modifica dei livelli di prestazioni tramite il portale di Azure](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal).

## Operazioni CRUD

Le operazioni di inserimento, lettura, aggiornamento, sostituzione ed eliminazione di MongoDB sono completamente supportate. È incluso il supporto per gli aggiornamenti di campi, matrici, bit per bit e di isolamento, come indicato nella [specifica dell'operatore di aggiornamento](https://docs.mongodb.org/manual/reference/operator/update/) di MongoDB. Per gli operatori di aggiornamento che richiedono più modifiche dei documenti, DocumentDB offre semantica ACID completa con isolamento dello snapshot.

## Operazioni di query

DocumentDB supporta la grammatica completa di query di MongoDB con alcune eccezioni. Le query eseguite sul set di [tipi BSON](https://docs.mongodb.org/manual/reference/bson-types/) compatibili con JSON sono supportate ed è disponibile anche il supporto per il formato di data e ora di MongoDB. Per le query che richiedono operatori specifici di tipo non JSON, DocumentDB supporta tipi di dati GUID. La tabella seguente fornisce informazioni sugli aspetti supportati e non supportati della grammatica di query di MongoDB.

## Aggregazione

DocumentDB non supporta la pipeline di aggregazione di MongoDB o le operazioni Map-Reduce. La pipeline di aggregazione viene in genere usata per elaborare i documenti tramite un insieme in più fasi di filtri e trasformazioni, ad esempio la corrispondenza e il raggruppamento dei risultati. DocumentDB supporta in modalità nativa le funzioni definite dall'utente e le stored procedure di JavaScript. DocumentDB può essere inoltre usato con facilità come origine e come sink per processi Hive, Pig e MapReduce mediante Azure HDInsight tramite il [connettore Hadoop](documentdb-run-hadoop-with-hdinsight.md) di DocumentDB.

## Funzionalità del portale
L'esperienza del portale di Azure per gli account abilitati al protocollo di MongoDB è leggermente diversa rispetto all'esperienza del portale per gli account DocumentDB standard. È prevista l'espansione dell'esperienza, ma sono necessari [commenti e suggerimenti](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience) degli utenti in merito alle funzionalità del portale che potrebbero risultare più utili.

## Matrice di supporto


### Operazioni CRUD e di query

Funzionalità|Supportato|Supporto previsto|Non supportate 
---|---|---|---
Inserimento|InsertOne| | 
 |InsertMany| | 
 |Inserimento| | 
Aggiornamento|UpdateOne| | 
 |UpdateMany| | 
 |Aggiornamento| | 
Aggiornamento del campo|$inc, $mul, $rename, $set, $unset, $min, $max|$currentDate| 
Aggiornamento della matrice| |-tutto-| 
Bit per bit| |-tutto-| 
Isolamento| |-tutto-| 
Replace|ReplaceOne| |
Elimina|DeleteOne | |
 |DeleteMany| | 
 |Rimuovere| | 
BulkWrite| |bulkWrite()| 
Confronto|-tutto-| | 
Logico|-tutto-| | 
Query sugli elementi| |-tutto-| 
Versione di valutazione|$mod, $regex |$text, $where| 
GeoSpatial|2dsphere, 2d, polygon|Tutti gli altri elementi| 
Array|$all, $size, $elemMatch|| 
Bit per bit| |-tutto-| 
Commento|-tutto-| | 
Proiezione| |-tutto-| 


### Comandi del database

Funzionalità|Supportato|Supporto previsto|Non supportate 
---|---|---|---
Aggregazione|Numero| |aggregate, distinct, group, mapreduce
GeoSpatial| |-tutto-| 
Query e scrittura|find, insert, update, delete, getLastError, getMore, findAndModify| |Eval, parallelCollectionScan, getPrevError, resetError
Cache QueryPlan| | |-tutto-
Autenticazione|getnonce, logout, authenticate| |Copydbgetnone, authschemaUpgrade
User Management| | |-tutto-
Gestione ruoli| | |-tutto-
Replica| | |-tutto-
Amministrazione|createIndex, listIndexes, dropIndexes, connectionStatus, reIndex| |Altri comandi. Per gli indici, nessun supporto per Unique, expireAfterSeconds, storageEngine, weights, default\_language, textIndexVersion, min, max, bucketSize
Diagnostic|listDatabases, collStats, dbStats| |Tutti gli altri elementi

## Passaggi successivi

- Informazioni su come [usare MongoChef](documentdb-mongodb-mongochef.md) con un account DocumentDB con supporto del protocollo per MongoDB.
- Esplorare DocumentDB con supporto del protocollo per trovare [esempi](documentdb-mongodb-samples.md) di MongoDB.

 

<!---HONumber=AcomDC_0824_2016-->