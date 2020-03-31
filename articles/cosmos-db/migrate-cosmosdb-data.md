---
title: Eseguire la migrazione di centinaia di terabyte di dati ad Azure Cosmos DB
description: Questo documento descrive come migrare 100 byte di dati in Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880212"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Eseguire la migrazione di centinaia di terabyte di dati ad Azure Cosmos DB 

Con Azure Cosmos DB è possibile archiviare terabyte di dati. È possibile eseguire una migrazione dei dati su larga scala per spostare il carico di lavoro di produzione in Azure Cosmos DB. Questo articolo descrive i problemi relativi allo spostamento dei dati su larga scala in Azure Cosmos DB e presenta lo strumento che consente di risolvere le problematiche ed eseguire la migrazione dei dati in Azure Cosmos DB. In questo case study il cliente ha usato l'API SQL per Cosmos DB.  

Prima di eseguire la migrazione dell'intero carico di lavoro in Azure Cosmos DB, è possibile eseguire la migrazione di un subset di dati per convalidare alcuni aspetti come la scelta della chiave di partizione, le prestazioni delle query e la modellazione dei dati. Dopo aver convalidato la prova del concetto, è possibile spostare l'intero carico di lavoro in Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Strumenti per la migrazione dei dati 

Le strategie di migrazione di Azure Cosmos DB attualmente differiscono in base alla scelta dell'API e alle dimensioni dei dati. Per eseguire la migrazione di set di dati più piccoli, ovvero per la convalida della modellazione dei dati, delle prestazioni delle query, della scelta della chiave di partizione e così via, è possibile scegliere [l'Utilità di migrazione dati](import-data.md) o il [connettore database Cosmos](../data-factory/connector-azure-cosmos-db.md)di Azure Data Factory. Se si ha familiarità con Spark, è anche possibile scegliere di usare il [connettore Azure Cosmos DB Spark](spark-connector.md) per eseguire la migrazione dei dati.

## <a name="challenges-for-large-scale-migrations"></a>Sfide per migrazioni su larga scala 

Gli strumenti esistenti per la migrazione dei dati in Azure Cosmos DB presentano alcune limitazioni che diventano particolarmente evidenti su larga scala:The existing tools for migrating data to Azure Cosmos DB have some limitations that become especially apparent at large scales:

 * Funzionalità di **scalabilità orizzontale limitata:** per eseguire la migrazione al più rapidamente possibile di terabyte di dati in Azure Cosmos DB e per utilizzare in modo efficace l'intera velocità effettiva di cui è stato eseguito il provisioning, i client di migrazione devono avere la possibilità di aumentare la scalabilità indefinitamente.  

* **Mancanza di rilevamento dello stato di avanzamento e check-point:** è importante tenere traccia dello stato di avanzamento della migrazione e tenere sotto controllo durante la migrazione di set di dati di grandi dimensioni. In caso contrario, qualsiasi errore che si verifica durante la migrazione interromperà la migrazione ed è necessario avviare il processo da zero. Non sarebbe produttivo riavviare l'intero processo di migrazione quando il 99% di esso è già stato completato.  

* **Mancanza di coda di messaggi non recapitabili**: All'interno di set di dati di grandi dimensioni, in alcuni casi potrebbero esserci problemi con parti dei dati di origine. Inoltre, potrebbero verificarsi problemi temporanei con il client o la rete. Uno di questi casi non dovrebbe causare l'esito negativo dell'intera migrazione. Anche se la maggior parte degli strumenti di migrazione dispone di solide funzionalità di ripetizione dei tentativi che proteggono da problemi intermittenti, non è sempre sufficiente. Ad esempio, se le dimensioni di meno dello 0,01% dei documenti di dati di origine sono maggiori di 2 MB, la scrittura del documento avrà esito negativo in Azure Cosmos DB. Idealmente, è utile per lo strumento di migrazione mantenere questi documenti "non riusciti" in un'altra coda di messaggi non recapitabili, che può essere elaborata dopo la migrazione. 

Molte di queste limitazioni vengono corrette per strumenti come Azure Data factory, servizi di migrazione dei dati di Azure.Many of these limitations are being fixed for tools like Azure Data factory, Azure Data Migration services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Strumento personalizzato con libreria dell'esecutore in blocco 

Le sfide descritte nella sezione precedente, possono essere risolte utilizzando uno strumento personalizzato che può essere facilmente scalato orizzontalmente tra più istanze ed è resiliente agli errori temporanei. Inoltre, lo strumento personalizzato può sospendere e riprendere la migrazione in corrispondenza di vari checkpoint. Azure Cosmos DB fornisce già la [libreria dell'esecutore bulk](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) che incorpora alcune di queste funzionalità. Ad esempio, la libreria dell'esecutore bulk dispone già della funzionalità per gestire gli errori temporanei e può scalare orizzontalmente i thread in un singolo nodo in modo da utilizzare circa 500 K RU per nodo. La libreria dell'executor bulk partiziona anche il set di dati di origine in micro-batch che vengono gestiti in modo indipendente come forma di checkpoint.  

Lo strumento personalizzato utilizza la libreria dell'esecutore bulk e supporta la scalabilità orizzontale su più client e per tenere traccia degli errori durante il processo di inserimento. Per usare questo strumento, i dati di origine devono essere partizionati in file distinti in Azure Data Lake Storage (ADLS) in modo che diversi ruoli di migrazione possano prelevare ogni file e inserirli in Azure Cosmos DB. Lo strumento personalizzato utilizza una raccolta separata, che archivia i metadati sullo stato di avanzamento della migrazione per ogni singolo file di origine in ADLS e tiene traccia degli eventuali errori ad essi associati.  

L'immagine seguente descrive il processo di migrazione utilizzando questo strumento personalizzato. Lo strumento è in esecuzione in un set di macchine virtuali e ogni macchina virtuale esegue una query sulla raccolta di rilevamenti in Azure Cosmos DB per acquisire un lease in una delle partizioni di dati di origine. Al termine di questa operazione, la partizione dei dati di origine viene letta dallo strumento e inserita in Azure Cosmos DB usando la libreria dell'esecutore bulk. Successivamente, la raccolta di rilevamento viene aggiornata per registrare lo stato di avanzamento dell'inserimento dei dati e gli eventuali errori rilevati. Dopo l'elaborazione di una partizione di dati, lo strumento tenta di eseguire una query per la successiva partizione di origine disponibile. Continua a elaborare la partizione di origine successiva fino a quando non viene eseguita la migrazione di tutti i dati. Il codice sorgente per lo strumento è disponibile [qui](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion).  

 
![Installazione dello strumento di migrazione](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

La raccolta di rilevamento contiene documenti, come illustrato nell'esempio seguente. Verranno visualizzati tali documenti uno per ogni partizione nei dati di origine.  Ogni documento contiene i metadati per la partizione dei dati di origine, ad esempio la posizione, lo stato della migrazione e gli errori (se presenti):Each document contains the metadata for the source data partition such as its location, migration status, and errors (if any):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Prerequisiti per la migrazione dei datiPrerequisites for data migration 

Prima dell'inizio della migrazione dei dati, è necessario considerare alcuni prerequisiti:Before the data migration starts, there are a few prerequisites to consider:  

#### <a name="estimate-the-data-size"></a>Stimare le dimensioni dei dati:  

Le dimensioni dei dati di origine potrebbero non essere mappate esattamente alle dimensioni dei dati in Azure Cosmos DB. Alcuni documenti di esempio dall'origine possono essere inseriti per verificare le dimensioni dei dati in Azure Cosmos DB. A seconda delle dimensioni del documento di esempio, è possibile stimare la dimensione totale dei dati in Azure Cosmos DB dopo la migrazione. 

Ad esempio, se ogni documento dopo la migrazione in Azure Cosmos DB è di circa 1 KB e se sono presenti circa 60 miliardi di documenti nel set di dati di origine, significa che le dimensioni stimate in Azure Cosmos DB sarebbero vicine a 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Pre-creare contenitori con abbastanza RU: 

Anche se Azure Cosmos DB ridimensiona automaticamente l'archiviazione, non è consigliabile iniziare dalle dimensioni più piccole del contenitore. I contenitori più piccoli hanno una minore disponibilità di velocità effettiva, il che significa che il completamento della migrazione richiederebbe molto più tempo. È invece utile creare i contenitori con la dimensione finale dei dati (come stimato nel passaggio precedente) e assicurarsi che il carico di lavoro di migrazione stia consumando completamente la velocità effettiva di cui è stato eseguito il provisioning.  

Nel passaggio precedente. Poiché la dimensione dei dati è stata stimata intorno ai 60 TB, è necessario un contenitore di almeno 2,4 M DI RU per contenere l'intero set di dati.  

 

#### <a name="estimate-the-migration-speed"></a>Stimare la velocità di migrazione: 

Supponendo che il carico di lavoro di migrazione possa utilizzare l'intera velocità effettiva di cui è stato eseguito il provisioning, il provisioning in tutto fornirà una stima della velocità di migrazione. Continuando l'esempio precedente, sono necessarie 5 RU per scrivere un documento da 1 KB nell'account API SQL del database Cosmos di Azure.Continue the previous example, 5 RUs are required for writing a 1-KB document to Azure Cosmos DB SQL API account.  2,4 milioni di RU consentirebbero un trasferimento di 480.000 documenti al secondo (o 480 MB/s). Ciò significa che la migrazione completa di 60 TB richiederà 125.000 secondi o circa 34 ore.  

Nel caso in cui si desideri che la migrazione venga completata entro un giorno, è necessario aumentare la velocità effettiva di cui è stato eseguito il provisioning a 5 milioni di RU. 

 

#### <a name="turn-off-the-indexing"></a>Disattivare l'indicizzazione:  

Poiché la migrazione deve essere completata il prima possibile, è consigliabile ridurre al minimo il tempo e le RU impiegate per la creazione di indici per ognuno dei documenti ingeriti.  Azure Cosmos DB indicizza automaticamente tutte le proprietà, è utile ridurre al minimo l'indicizzazione a pochi termini selezionati o disattivarla completamente per il corso della migrazione. È possibile disattivare i criteri di indicizzazione del contenitore modificando indexingMode in none come illustrato di seguito:You can turn off the container's indexing policy by changing the indexingMode to none as shown below:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Al termine della migrazione, è possibile aggiornare l'indicizzazione.  

## <a name="migration-process"></a>Processo di migrazione 

Dopo aver completato i prerequisiti, è possibile eseguire la migrazione dei dati con i passaggi seguenti:After the prerequisites are completed, you can migrate data with the following steps:  

1. Importare innanzitutto i dati dall'origine in Archiviazione BLOB di Azure.First import the data from source to Azure Blob Storage. Per aumentare la velocità di migrazione, è utile eseguire il parallelizeing tra partizioni di origine distinte. Prima di avviare la migrazione, il set di dati di origine deve essere partizionato in file di dimensioni inferiori a 200 MB.   

2. La libreria dell'executor bulk può aumentare le prestazioni, per usare 500.000 RU in una singola macchina virtuale client. Poiché la velocità effettiva disponibile è 5 milioni di RU, è necessario eseguire il provisioning di 10 macchine virtuali Ubuntu 16.04 (Standard_D32_v3) nella stessa area in cui si trova il database Cosmos di Azure.Since the available throughput is 5 million RUs, 10 Ubuntu 16.04 VMs (Standard_D32_v3) should be provisioned in the same region where your Azure Cosmos database is located. È necessario preparare queste macchine virtuali con lo strumento di migrazione e il relativo file di impostazioni.  

3. Eseguire il passaggio della coda in una delle macchine virtuali client. Questo passaggio crea la raccolta di rilevamento, che analizza il contenitore ADLS e crea un documento di rilevamento dello stato di avanzamento per ogni file di partizione del set di dati di origine.  

4. Eseguire quindi il passaggio di importazione in tutte le macchine virtuali client. Ognuno dei client può assumere la proprietà di una partizione di origine e inserire i dati in Azure Cosmos DB. Una volta completato e aggiornato il relativo stato nella raccolta di rilevamento, i client possono quindi eseguire una query per la successiva partizione di origine disponibile nella raccolta di rilevamento.  

5. Questo processo continua fino a quando non è stato ingerito l'intero set di partizioni di origine. Una volta elaborate tutte le partizioni di origine, lo strumento deve essere rieseguito in modalità di correzione degli errori nella stessa raccolta di rilevamento. Questo passaggio è necessario per identificare le partizioni di origine che devono essere rielaborate a causa di errori.  

6. Alcuni di questi errori potrebbero essere dovuti a documenti non corretti nei dati di origine. Questi devono essere identificati e risolti. Successivamente, è necessario eseguire nuovamente il passaggio di importazione sulle partizioni non riuscite per reingest loro. 

Una volta completata la migrazione, è possibile verificare che il numero di documenti in Azure Cosmos DB sia uguale al numero di documenti nel database di origine. In questo esempio, la dimensione totale in Azure Cosmos DB è stata fino a 65 terabyte. Dopo la migrazione, l'indicizzazione può essere attivata in modo selettivo e le RU possono essere abbassate al livello richiesto dalle operazioni del carico di lavoro.

## <a name="contact-the-azure-cosmos-db-team"></a>Contattare il team di Azure Cosmos DBContact the Azure Cosmos DB team
Sebbene sia possibile seguire questa guida per eseguire correttamente la migrazione di set di dati di grandi dimensioni in Azure Cosmos DB, per le migrazioni su larga scala, è consigliabile contattare il team del prodotto Azure Cosmos DB per convalidare la modellazione dei dati e una revisione generale dell'architettura. In base al set di dati e al carico di lavoro, il team del prodotto può anche suggerire altre ottimizzazioni delle prestazioni e dei costi che potrebbero essere applicabili all'utente. Per contattare il team di Azure Cosmos DB per assistenza con migrazioni su larga scala, è possibile aprire un ticket di supporto sotto il tipo di problema "Advisory generale" e "Migrazioni di grandi dimensioni) " come illustrato di seguito.

![Argomento di supporto per la migrazione](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, provare le applicazioni di esempio che utilizzano la libreria dell'esecutore in blocco in [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* La libreria dell'executor bulk è integrata nel connettore Cosmos DB Spark, per altre informazioni, vedere l'articolo connettore Azure Cosmos DB Spark.The bulk executor library is integrated into the Cosmos DB Spark connector, to learn more, see [Azure Cosmos DB Spark connector](spark-connector.md) article.  
* Contattare il team del prodotto Azure Cosmos DB aprendo un ticket di supporto nel sottotipo di errore "Advisory General Advisory" e nel sottotipo di errore "Migrazioni di grandi dimensioni) per ulteriore assistenza con migrazioni su larga scala. 
