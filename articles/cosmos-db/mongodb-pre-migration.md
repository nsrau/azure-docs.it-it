---
title: Passaggi di pre-migrazione per la migrazione dei dati all'API Azure Cosmos DB per MongoDB
description: Questo documento fornisce una panoramica dei prerequisiti per la migrazione dei dati da MongoDB a Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445206"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di pre-migrazione per la migrazione dei dati da MongoDB all'API di Azure Cosmos DB per MongoDB

Prima di eseguire la migrazione dei dati da MongoDB (in locale o nel cloud (IaaS)) all'API di Azure Cosmos DB per MongoDB, è necessario:

1. [Creare un account di Azure Cosmos DB](#create-account)
2. [Stimare la velocità effettiva necessaria per i carichi di lavoro](#estimate-throughput)
3. [Scegliere una chiave di partizione ottimale per i dati](#partitioning)
4. [Informazioni sui criteri di indicizzazione che è possibile impostare sui dati](#indexing)

Se sono già stati completati i prerequisiti precedenti per la migrazione, vedere la pagina relativa alla migrazione [dei dati di MongoDB all'API Azure Cosmos DB per MongoDB](../dms/tutorial-mongodb-cosmos-db.md) per le istruzioni di migrazione dei dati effettive. In caso contrario, in questo documento vengono fornite le istruzioni per gestire questi prerequisiti. 

## <a id="create-account"></a>Creazione di un account Azure Cosmos DB 

Prima di avviare la migrazione, è necessario [creare un account Azure Cosmos usando l'API di Azure Cosmos DB per MongoDB](create-mongodb-dotnet.md). 

Al momento della creazione dell'account, è possibile scegliere le impostazioni per distribuire i dati a [livello globale](distribute-data-globally.md) . È anche possibile abilitare le Scritture in più aree (o la configurazione multimaster), che consente a ciascuna delle aree di essere un'area di scrittura e di lettura.

![Creazione dell'account](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>Stimare la velocità effettiva necessaria per i carichi di lavoro

Prima di avviare la migrazione usando il [servizio migrazione del database (DMS)](../dms/dms-overview.md), è necessario stimare la quantità di velocità effettiva per il provisioning per i database e le raccolte di Azure Cosmos.

È possibile eseguire il provisioning della velocità effettiva in uno di questi casi:

- Raccolta

- Database

> [!NOTE]
> È anche possibile disporre di una combinazione dei precedenti, in cui alcune raccolte in un database possono avere una velocità effettiva con provisioning dedicata e altre possono condividere la velocità effettiva. Per informazioni dettagliate, vedere la pagina [impostazione della velocità effettiva in un database e in un contenitore](set-throughput.md) .
>

È innanzitutto necessario decidere se si desidera effettuare il provisioning della velocità effettiva a livello di database o di raccolta oppure una combinazione di entrambi. In generale, è consigliabile configurare una velocità effettiva dedicata a livello di raccolta. Il provisioning della velocità effettiva a livello di database consente alle raccolte nel database di condividere la velocità effettiva con provisioning. Con la velocità effettiva condivisa, tuttavia, non esiste alcuna garanzia per una velocità effettiva specifica in ogni singola raccolta e non si ottengono prestazioni prevedibili per una raccolta specifica.

Se non si è certi della quantità di velocità effettiva da dedicare a ogni singola raccolta, è possibile scegliere la velocità effettiva a livello di database. È possibile considerare la velocità effettiva con provisioning configurata nel database di Azure Cosmos come equivalente logico rispetto a quella della capacità di calcolo di una VM MongoDB o di un server fisico, ma più conveniente con la possibilità di ridimensionare in modo elastico. Per altre informazioni, vedere [provisioning della velocità effettiva nei contenitori e nei database di Azure Cosmos](set-throughput.md).

Se si effettua il provisioning della velocità effettiva a livello di database, tutte le raccolte create all'interno del database devono essere create con una chiave di partizione/partizione. Per altre informazioni sul partizionamento, vedere [partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md). Se non si specifica una chiave di partizione/partizione durante la migrazione, il servizio migrazione del database di Azure popola automaticamente il campo della chiave di partizionamento con un attributo *_id* generato automaticamente per ogni documento.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Numero ottimale di unità richiesta (UR) per il provisioning

In Azure Cosmos DB, il provisioning della velocità effettiva viene eseguito in anticipo e viene misurato in unità richiesta (UR) al secondo. Se si dispone di carichi di lavoro che eseguono MongoDB in una macchina virtuale o in locale, è possibile considerare le UR come un'astrazione semplice per le risorse fisiche, ad esempio per le dimensioni di una macchina virtuale o di un server locale e le risorse che possiedono, ad esempio memoria, CPU e IOPs. 

Diversamente dalle macchine virtuali o dai server locali, le UR sono facili da scalare in qualsiasi momento. È possibile modificare il numero di UR di cui è stato effettuato il provisioning in pochi secondi e viene addebitato solo il numero massimo di UR di cui viene effettuato il provisioning per un periodo di un'ora specificato. Per altre informazioni, vedere [Unità richiesta in Azure Cosmos DB](request-units.md).

Di seguito sono riportati i fattori chiave che influiscono sul numero di ur richieste:
- **Dimensione elemento (ad esempio, documento)** : con l'aumentare delle dimensioni di un elemento o di un documento, aumenta anche il numero di ur usate per la lettura o la scrittura dell'elemento o del documento.
- **Conteggio proprietà elemento**: presupponendo l' [indicizzazione predefinita](index-overview.md) di tutte le proprietà, il numero di ur utilizzate per scrivere un elemento aumenta man mano che aumenta il conteggio delle proprietà dell'elemento. È possibile ridurre il consumo delle unità richiesta per le operazioni di scrittura [limitando il numero di proprietà indicizzate](index-policy.md).
- **Operazioni simultanee**: le unità richiesta utilizzate variano anche in base alla frequenza con cui vengono eseguite operazioni CRUD diverse (ad esempio Scritture, letture, aggiornamenti, eliminazioni) e query più complesse. È possibile usare [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) per restituire le esigenze di concorrenza dei dati MongoDB correnti.
- **Modelli di query**: la complessità di una query influiscono sul numero di unità richiesta utilizzate dalla query.

Se si esportano file JSON con [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) e si comprende il numero di operazioni di scrittura, lettura, aggiornamento ed eliminazione eseguite al secondo, è possibile usare il [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) per stimare il numero iniziale di UR di cui eseguire il provisioning. Capacity Planner non determina il costo di query più complesse. Quindi, se si dispone di query complesse sui dati, verranno utilizzate le UR aggiuntive. Il calcolatore presuppone inoltre che tutti i campi siano indicizzati e che venga utilizzata la coerenza della sessione. Il modo migliore per comprendere il costo delle query consiste nel migrare i dati (o i dati di esempio) a Azure Cosmos DB, [connettersi all'endpoint del Cosmos DB](connect-mongodb-account.md) ed eseguire una query di esempio dalla shell MongoDB usando il comando `getLastRequestStastistics` per ottenere l'addebito della richiesta, che restituirà il numero di ur utilizzate:

`db.runCommand({getLastRequestStatistics: 1})`

Tramite questo comando verrà restituito un documento JSON simile al seguente:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Dopo aver compreso il numero di ur utilizzato da una query e le esigenze di concorrenza per la query, è possibile modificare il numero di ur con provisioning. L'ottimizzazione delle UR non è un evento unico: è necessario ottimizzare continuamente o aumentare il numero di unità richiesta di cui è stato effettuato il provisioning, a seconda se non si prevede un traffico intenso, anziché un carico di lavoro elevato o l'importazione di dati.

## <a id="partitioning"></a>Scegliere la chiave di partizione
Il partizionamento è uno dei punti chiave da considerare prima di eseguire la migrazione a un database distribuito a livello globale, ad esempio Azure Cosmos DB. Azure Cosmos DB usa il partizionamento per ridimensionare i singoli contenitori di un database per soddisfare le esigenze di scalabilità e prestazioni dell'applicazione. Nel partizionamento gli elementi in un contenitore sono divisi in subset distinti denominati partizioni logiche. Per informazioni dettagliate e consigli sulla scelta della chiave di partizione corretta per i dati, vedere la [sezione scelta di una chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indicizzare i dati
Per impostazione predefinita, Azure Cosmos DB indicizza tutti i campi dati al momento dell'inserimento. È possibile modificare i [criteri di indicizzazione](index-policy.md) in Azure Cosmos DB in qualsiasi momento. In realtà, è spesso consigliabile disattivare l'indicizzazione durante la migrazione dei dati e quindi riattivarla quando i dati sono già in Cosmos DB. Per altre informazioni sull'indicizzazione, vedere la sezione [indicizzazione in Azure Cosmos DB](index-overview.md) . 

Il [servizio migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db.md) esegue automaticamente la migrazione delle raccolte MongoDB con indici univoci. Tuttavia, gli indici univoci devono essere creati prima della migrazione. Azure Cosmos DB non supporta la creazione di indici univoci, quando sono già presenti dati nelle raccolte. Per altre informazioni, vedere [Chiavi univoche in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire la migrazione dei dati di MongoDB a Cosmos DB usando il servizio migrazione del database.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisioning della velocità effettiva nei contenitori e nei database di Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Partizionamento in Azure Cosmos DB)
* [Distribuzione globale in Azure Cosmos DB](distribute-data-globally.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
