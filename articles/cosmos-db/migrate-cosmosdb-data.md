---
title: Eseguire la migrazione di centinaia di terabyte di dati ad Azure Cosmos DB
description: Questo documento descrive come è possibile eseguire la migrazione di centinaia di terabyte di dati in Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: bharathb
ms.openlocfilehash: 6092b3aac2b0282a795d89730266e72179b34e8a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648904"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Eseguire la migrazione di centinaia di terabyte di dati ad Azure Cosmos DB 

Azure Cosmos DB possibile archiviare terabyte di dati. È possibile eseguire una migrazione dei dati su larga scala per spostare il carico di lavoro di produzione in Azure Cosmos DB. Questo articolo descrive i problemi relativi allo spostare i dati su larga scala per Azure Cosmos DB e introduce lo strumento che consente di risolvere i problemi ed eseguire la migrazione dei dati al Azure Cosmos DB. In questo case study il cliente usava l'API SQL Cosmos DB.  

Prima di eseguire la migrazione dell'intero carico di lavoro in Azure Cosmos DB, è possibile eseguire la migrazione di un subset di dati per convalidare alcuni aspetti come la scelta della chiave di partizione, le prestazioni delle query e la modellazione dei dati. Dopo aver convalidato il modello di prova, è possibile spostare l'intero carico di lavoro in Azure Cosmos DB.  

È anche possibile usare il [programma Bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/) per accelerare la compilazione o la migrazione delle applicazioni su Azure Cosmos DB. Nell'ambito di questo programma, gli ingegneri del team di Azure Cosmos DB verranno assegnati al progetto e aiuteranno a eseguire la migrazione dei dati Azure Cosmos DB. Fai clic sul pulsante seguente per iscriverti al programma Cosmos DB bootstrap:

> [!div class="nextstepaction"]
> [Programma di bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/)

## <a name="tools-for-data-migration"></a>Strumenti per la migrazione dei dati 

Le strategie di migrazione Azure Cosmos DB attualmente variano in base alla scelta dell'API e alla dimensione dei dati. Per eseguire la migrazione di set di dati più piccoli: per la convalida della modellazione dei dati, delle prestazioni delle query, della scelta della chiave di partizione Azure Data Factory e così via, è possibile scegliere l'utilità [Azure Cosmos DB di](../data-factory/connector-azure-cosmos-db.md) [migrazione dati](import-data.md) o il connettore Se si ha familiarità con Spark, è anche possibile scegliere di usare il [connettore spark Azure Cosmos DB](spark-connector.md) per eseguire la migrazione dei dati.

## <a name="challenges-for-large-scale-migrations"></a>Problemi di migrazione su larga scala 

Gli strumenti esistenti per la migrazione dei dati a Azure Cosmos DB presentano alcune limitazioni che diventano particolarmente evidenti a grandi scale:

 * **Funzionalità di scalabilità orizzontale limitate**: Per eseguire la migrazione di terabyte di dati in Azure Cosmos DB il più rapidamente possibile e per utilizzare efficacemente l'intera velocità effettiva con provisioning, è necessario che i client della migrazione siano in grado di eseguire la scalabilità orizzontale per un periodo illimitato.  

* **Mancanza di rilevamento dello stato di avanzamento e di selezione**: È importante tenere traccia dello stato di avanzamento della migrazione e selezionare il segno di spunta durante la migrazione di set di dati di grandi dimensioni. In caso contrario, qualsiasi errore che si verifica durante la migrazione arresterà la migrazione e sarà necessario avviare il processo da zero. Non sarebbe produttivo riavviare l'intero processo di migrazione quando il 99% di esso è già stato completato.  

* **Mancanza di coda di**messaggi non recapitabili: Nei set di dati di grandi dimensioni, in alcuni casi potrebbero verificarsi problemi con parti dei dati di origine. Potrebbero inoltre verificarsi problemi temporanei relativi al client o alla rete. Uno di questi casi non dovrebbe provocare l'esito negativo dell'intera migrazione. Sebbene la maggior parte degli strumenti di migrazione disponga di potenti funzionalità di ripetizione dei tentativi che proteggono da problemi intermittenti, non è sempre sufficiente. Se, ad esempio, la dimensione inferiore al 0,01% dei documenti dati di origine è superiore a 2 MB, la scrittura del documento avrà esito negativo in Azure Cosmos DB. Idealmente, è utile che lo strumento di migrazione manterrà i documenti ' non riusciti ' in un'altra coda di messaggi non recapitabili, che può essere elaborata dopo la migrazione. 

Molti di questi limiti sono corretti per strumenti come Azure Data Factory, servizi di migrazione dei dati di Azure. 

## <a name="custom-tool-with-bulk-executor-library"></a>Strumento personalizzato con la libreria dell'executor bulk 

Per risolvere i problemi descritti nella sezione precedente, è possibile usare uno strumento personalizzato che può essere facilmente scalato orizzontalmente in più istanze ed è resiliente agli errori temporanei. Inoltre, lo strumento personalizzato può sospendere e riprendere la migrazione in vari checkpoint. Azure Cosmos DB fornisce già la [libreria di esecuzioni bulk](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) che incorpora alcune di queste funzionalità. Ad esempio, la libreria dell'executor bulk dispone già della funzionalità per gestire gli errori temporanei e può scalare in orizzontale i thread in un singolo nodo per utilizzare circa 500 unità richiesta per nodo. La libreria dell'esecutore bulk esegue anche il partizionamento del set di dati di origine in micro batch gestiti in modo indipendente come una forma di checkpoint.  

Lo strumento personalizzato usa la libreria dell'esecutore bulk e supporta la scalabilità orizzontale tra più client e per tenere traccia degli errori durante il processo di inserimento. Per utilizzare questo strumento, i dati di origine devono essere partizionati in file distinti in Azure Data Lake Storage (ADLS) in modo che i diversi ruoli di lavoro della migrazione possano acquisire ogni file e inserirli in Azure Cosmos DB. Lo strumento personalizzato si avvale di una raccolta separata che archivia i metadati relativi all'avanzamento della migrazione per ogni singolo file di origine in ADLS e tiene traccia degli eventuali errori associati.  

Nell'immagine seguente viene descritto il processo di migrazione utilizzando questo strumento personalizzato. Lo strumento è in esecuzione in un set di macchine virtuali e ogni macchina virtuale esegue una query sulla raccolta di rilevamento in Azure Cosmos DB per acquisire un lease in una delle partizioni di dati di origine. Al termine di questa operazione, la partizione dei dati di origine viene letta dallo strumento e inserita in Azure Cosmos DB tramite la libreria dell'executor di massa. Successivamente, la raccolta di rilevamento viene aggiornata per registrare lo stato di inserimento dei dati e gli eventuali errori rilevati. Dopo l'elaborazione di una partizione di dati, lo strumento tenta di eseguire una query per la successiva partizione di origine disponibile. Continua a elaborare la partizione di origine successiva fino a quando non viene eseguita la migrazione di tutti i dati. Il codice sorgente per lo strumento è disponibile [qui](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion).  

 
![Installazione dello strumento di migrazione](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

La raccolta di rilevamento contiene documenti, come illustrato nell'esempio seguente. Questi documenti vengono visualizzati uno per ogni partizione nei dati di origine.  Ogni documento contiene i metadati per la partizione dei dati di origine, ad esempio il percorso, lo stato di migrazione ed eventuali errori:  

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
 

## <a name="prerequisites-for-data-migration"></a>Prerequisiti per la migrazione dei dati 

Prima di avviare la migrazione dei dati, è necessario prendere in considerazione alcuni prerequisiti:  

#### <a name="estimate-the-data-size"></a>Stima delle dimensioni dei dati:  

Le dimensioni dei dati di origine potrebbero non corrispondere esattamente alle dimensioni dei dati in Azure Cosmos DB. È possibile inserire alcuni documenti di esempio dell'origine per verificarne le dimensioni dei dati in Azure Cosmos DB. A seconda delle dimensioni del documento di esempio, è possibile stimare le dimensioni totali dei dati in Azure Cosmos DB post-migrazione. 

Ad esempio, se ogni documento dopo la migrazione in Azure Cosmos DB è di circa 1 KB e se sono presenti circa 60 miliardi documenti nel set di dati di origine, significa che le dimensioni stimate in Azure Cosmos DB saranno vicine a 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Pre-creare i contenitori con un numero di ur sufficiente: 

Sebbene Azure Cosmos DB scalare automaticamente l'archiviazione, non è consigliabile iniziare dalla dimensione minima del contenitore. I contenitori più piccoli hanno una disponibilità della velocità effettiva inferiore, il che significa che la migrazione richiede molto più tempo per il completamento. È invece utile creare i contenitori con le dimensioni finali dei dati (come previsto nel passaggio precedente) e assicurarsi che il carico di lavoro di migrazione stia usando completamente la velocità effettiva con provisioning.  

Nel passaggio precedente. Poiché la dimensione dei dati è stimata circa 60 TB, è necessario un contenitore di almeno 2,4 M ur per gestire l'intero set di dati.  

 

#### <a name="estimate-the-migration-speed"></a>Stimare la velocità di migrazione: 

Supponendo che il carico di lavoro di migrazione possa usare l'intera velocità effettiva con provisioning, il provisioning in tutto il provisioning garantisce una stima della velocità di migrazione. Continuando con l'esempio precedente, sono necessari 5 UR per la scrittura di un documento da 1 KB per Azure Cosmos DB account API SQL.  2,4 milioni ur consentiva il trasferimento di 480.000 documenti al secondo (o 480 MB/s). Ciò significa che la migrazione completa di 60 TB importerà 125.000 secondi o circa 34 ore.  

Se si vuole che la migrazione venga completata entro un giorno, è necessario aumentare la velocità effettiva con provisioning a 5 milioni ur. 

 

#### <a name="turn-off-the-indexing"></a>Disattivare l'indicizzazione:  

Dal momento che la migrazione deve essere completata il prima possibile, è consigliabile ridurre al minimo il tempo e le UR per la creazione di indici per ogni documento inserito.  Azure Cosmos DB indicizza automaticamente tutte le proprietà, vale la pena ridurre al minimo l'indicizzazione in base a alcuni termini selezionati oppure disattivarlo completamente per il corso della migrazione. È possibile disattivare i criteri di indicizzazione del contenitore modificando indexingMode in None, come illustrato di seguito:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Al termine della migrazione, è possibile aggiornare l'indicizzazione.  

## <a name="migration-process"></a>Processo di migrazione 

Una volta completati i prerequisiti, è possibile eseguire la migrazione dei dati con i passaggi seguenti:  

1. Importare prima i dati dall'origine all'archivio BLOB di Azure. Per aumentare la velocità di migrazione, è utile parallelizzare tra partizioni di origine distinte. Prima di avviare la migrazione, il set di dati di origine deve essere partizionato in file con dimensioni circa 200 MB.   

2. È possibile aumentare le prestazioni della libreria dell'esecutore bulk per utilizzare 500.000 UR in una singola macchina virtuale client. Poiché la velocità effettiva disponibile è 5 milioni ur, è necessario eseguire il provisioning di 10 macchine virtuali Ubuntu 16,04 (Standard_D32_v3) nella stessa area in cui si trova il database di Azure Cosmos. È necessario preparare queste VM con lo strumento di migrazione e il relativo file di impostazioni.  

3. Eseguire il passaggio della coda in una delle macchine virtuali client. Questo passaggio crea la raccolta di rilevamento che analizza il contenitore ADLS e crea un documento di rilevamento dello stato di avanzamento per ognuno dei file di partizione del set di dati di origine.  

4. Eseguire quindi il passaggio di importazione in tutte le VM client. Ogni client può assumere la proprietà di una partizione di origine e inserire i dati in Azure Cosmos DB. Una volta completato e il relativo stato viene aggiornato nella raccolta di rilevamento, i client possono quindi eseguire una query per la successiva partizione di origine disponibile nella raccolta di rilevamento.  

5. Questo processo continua fino a quando non viene inserito l'intero set di partizioni di origine. Una volta elaborate tutte le partizioni di origine, è necessario eseguire nuovamente lo strumento in modalità di correzione degli errori nella stessa raccolta di rilevamento. Questo passaggio è necessario per identificare le partizioni di origine che devono essere rielaborate a causa di errori.  

6. Alcuni di questi errori potrebbero essere causati da documenti non corretti nei dati di origine. Questi devono essere identificati e corretti. Quindi, è necessario eseguire di nuovo il passaggio di importazione sulle partizioni non riuscite per ricaricarle. 

Una volta completata la migrazione, è possibile verificare che il numero di documenti in Azure Cosmos DB corrisponda al numero di documenti nel database di origine. In questo esempio, le dimensioni totali in Azure Cosmos DB si sono rivelate a 65 terabyte. Dopo la migrazione, l'indicizzazione può essere attivata in modo selettivo e le UR possono essere ridotte al livello richiesto dalle operazioni del carico di lavoro.

## <a name="contact-the-azure-cosmos-db-team"></a>Contattare il team di Azure Cosmos DB
Sebbene sia possibile seguire questa guida per eseguire correttamente la migrazione di set di dati di grandi dimensioni a Azure Cosmos DB per migrazioni su larga scala, è consigliabile contattare il team del prodotto Azure Cosmos DB per convalidare la modellazione dei dati e una revisione dell'architettura generale. A seconda del set di dati e del carico di lavoro, il team del prodotto può anche suggerire altre ottimizzazioni delle prestazioni e dei costi che potrebbero essere applicabili all'utente. Per contattare il team di Azure Cosmos DB per ottenere assistenza sulle migrazioni su larga scala, è possibile aprire un ticket di supporto con il tipo di problema "General Advisory" e "Large (TB +) Migrations", come illustrato di seguito.

![Argomento del supporto per la migrazione](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni, provare le applicazioni di esempio che utilizzano la libreria Executor in blocco in [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* Per altre informazioni, [Azure Cosmos DB](spark-connector.md) vedere l'articolo relativo al connettore Spark per la libreria di esecuzioni bulk in Cosmos DB.  
* Per ulteriori informazioni sulle migrazioni su larga scala, contattare il team del prodotto Azure Cosmos DB aprendo un ticket di supporto con il tipo di problema "generale consultivo" e "migrazioni di grandi dimensioni (TB +)". 
* Usare il [programma Bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/) per accelerare la compilazione o la migrazione delle applicazioni su Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Programma di bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/)
