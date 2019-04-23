---
title: Passaggi di pre-migrazione per migrazioni di dati da MongoDB all'API di Azure Cosmos DB per MongoDB
description: Questo documento offre una panoramica dei prerequisiti per la migrazione dei dati da MongoDB a Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013907"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di pre-migrazione per migrazioni di dati da MongoDB all'API di Azure Cosmos DB per MongoDB

Prima di migrare i dati da MongoDB (in locale o nel cloud (IaaS)) all'API di Azure Cosmos DB per MongoDB, è necessario:

1. [Creare un account di Azure Cosmos DB](#create-account)
2. [Stimare la velocità effettiva necessaria per i carichi di lavoro](#estimate-throughput)
3. [Selezionare una chiave di partizione ottimale per i tuoi dati](#partitioning)
4. [Comprendere i criteri di indicizzazione che è possibile impostare sui dati](#indexing)

Se si sono già completato i prerequisiti sopra per la migrazione, vedere la [i dati di MongoDB di eseguire la migrazione all'API di Azure Cosmos DB per MongoDB](../dms/tutorial-mongodb-cosmos-db.md) per le istruzioni di migrazione dei dati effettivi. In caso contrario, questo documento fornisce istruzioni per gestire questi prerequisiti. 

## <a id="create-account"></a> Creare un account Azure Cosmos DB 

Prima di iniziare la migrazione, è necessario [creare un account Azure Cosmos tramite API di Azure Cosmos DB per MongoDB](create-mongodb-dotnet.md). 

Al momento della creazione di account, è possibile scegliere le impostazioni per [globalmente distribuire](distribute-data-globally.md) dei dati. È inoltre la possibilità di scritture in più aree di abilitare (o configurazione multimaster), che consente a ognuna delle aree del sia un'operazione di scrittura e area di lettura.

![Creazione dell'account](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Stimare le esigenze di velocità effettiva per i carichi di lavoro

Prima di avviare la migrazione usando il [Database Migration Service (DMS)](../dms/dms-overview.md), è necessario stimare la quantità di velocità effettiva di effettuare il provisioning per Azure Cosmos database e delle raccolte.

In entrambi, è possibile eseguire il provisioning della velocità effettiva:

- Raccolta

- Database

> [!NOTE]
> È anche possibile avere una combinazione delle precedenti, in cui alcune raccolte in un database possono avere dedicati provisioning della velocità effettiva e altri utenti possono condividere la velocità effettiva. Per informazioni dettagliate, vedere la [impostare la velocità effettiva su un database e un contenitore](set-throughput.md) pagina.
>

È innanzitutto necessario decidere se si desidera eseguire il provisioning di database o velocità effettiva a livello di raccolta o una combinazione di entrambi. In generale, è consigliabile configurare una velocità effettiva dedicata a livello di raccolta. Provisioning della velocità effettiva a livello di database consente alle raccolte nel database di condividere la velocità effettiva con provisioning. Con una velocità effettiva condivisa, tuttavia, non c'è garanzia per una determinata velocità per ogni raccolta di singoli e non si ottengono prestazioni prevedibili su una raccolta specifica.

Se non si è certi sulla velocità effettiva deve essere dedicato a ogni singola raccolta, è possibile scegliere la velocità effettiva a livello di database. È possibile considerare la velocità effettiva con provisioning configurata nel database di Azure Cosmos come equivalente logico a quella della capacità di calcolo di una VM di MongoDB o un server fisico, ma più conveniente grazie alla possibilità di ridimensionare in modo. Per altre informazioni, vedere [velocità effettiva di provisioning nel database e i contenitori di Azure Cosmos](set-throughput.md).

Se si esegue il provisioning della velocità effettiva a livello di database, è necessario creare tutte le raccolte create all'interno del database con una chiave di partizione/partizione. Per altre informazioni sul partizionamento, vedere [partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md). Se non si specifica una chiave di partizione/partizione durante la migrazione, il servizio migrazione del Database popola automaticamente il campo chiave di partizione con un *ID* attributo che viene generato automaticamente per ogni documento.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Numero ottimale di unità richiesta (UR) per eseguire il provisioning

In Azure Cosmos DB, la velocità effettiva viene eseguito il provisioning in anticipo e viene misurata in unità richiesta (UR) al secondo. Se hai carichi di lavoro che esegue MongoDB in una macchina virtuale o in locale, pensare di unità come un'astrazione di semplice per le risorse fisiche, ad esempio per le dimensioni di una macchina virtuale o locale, un server e le risorse di cui dispongono, ad esempio, memoria, CPU, numero di IOPs. 

A differenza delle macchine virtuali o nei server locali, unità riservate sono facili da scalabilità orizzontale e verticale in qualsiasi momento. È possibile modificare il numero di UR sottoposte a provisioning in pochi secondi e verrà addebitato solo il numero massimo di unità riservate viene effettuato il provisioning per un determinato periodo di un'ora. Per altre informazioni, vedere [Unità richiesta in Azure Cosmos DB](request-units.md).

Di seguito sono fattori chiave che influiscono sul numero di unità richiesta necessarie:
- **Dimensioni dell'elemento (ad esempio, documento)**: Man mano che aumenta le dimensioni di un elemento o documento, il numero di unità richiesta usate per leggere o scrivere l'elemento o documento anche aumenta.
- **Numero di proprietà degli elementi**: Supponendo che il [indicizzazione predefinita](index-overview.md) su tutte le proprietà, il numero di unità richiesta utilizzate per scrivere un elemento aumenta man mano che aumenta il numero di proprietà elemento. È possibile ridurre il consumo delle unità richiesta per le operazioni di scrittura dal [limitando il numero di proprietà indicizzate](index-policy.md).
- **Operazioni simultanee**: Richiedere unità usate dipende anche dalla frequenza con cui operazioni CRUD diverse (ad esempio, operazioni di scrittura, letture, aggiornamenti, eliminazioni) e vengono eseguite query più complesse. È possibile usare [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) per restituire le esigenze di concorrenza dei dati di MongoDB corrente.
- **Modelli di query**: La complessità di una query influisce sul numero di unità richiesta usato dalla query.

Se si esportano il file JSON usando [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) e comprendere come molte operazioni di scrittura, letture, aggiornamenti ed eliminazioni che si verificano al secondo, è possibile utilizzare il [capacity planner di Azure Cosmos DB](https://www.documentdb.com/capacityplanner) per stimare il numero iniziale per effettuare il provisioning di UR. Lo strumento capacity planner non vengono presi nel costo di query più complesse. Pertanto, se sono presenti query complesse sui dati, verranno utilizzate ulteriori unità di richiesta. Il calcolatore presuppone inoltre che tutti i campi sono indicizzati, e la coerenza di sessione viene usata. Il modo migliore per comprendere il costo delle query consiste nell'eseguire la migrazione di dati (o i dati di esempio) per Azure Cosmos DB [connettersi all'endpoint di Cosmos DB](connect-mongodb-account.md) ed eseguire una query di esempio dalla Shell di MongoDB tramite il `getLastRequestStastistics` comando per ottenere il addebito richiesta, che genera un numero di unità richiesta consumate:

`db.runCommand({getLastRequestStatistics: 1})`

Questo comando restituirà un documento JSON simile al seguente:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Dopo aver compreso il numero di unità richiesta consumate da una query e le esigenze di concorrenza per tale query, è possibile modificare il numero di unità riservate sottoposte a provisioning. Ottimizzazione delle unità richiesta non è un evento singolo: continuamente è consigliabile ottimizzare o aumentare le unità richiesta con provisioning, a seconda che si sono non è previsto un traffico elevato, a differenza di un carico di lavoro o l'importazione di dati.

## <a id="partitioning"></a>Scegliere la chiave di partizione
Il partizionamento è un aspetto essenziale di considerazione prima della migrazione a un Database distribuito a livello globale come Azure Cosmos DB. Azure Cosmos DB Usa il partizionamento per la scalabilità di singoli contenitori in un database per soddisfare le esigenze di scalabilità e prestazioni dell'applicazione. Il partizionamento, gli elementi in un contenitore sono suddivisi in sottoinsiemi distinti denominati partizioni logiche. Per informazioni dettagliate e indicazioni su come scegliere la chiave di partizione corretta per i tuoi dati, vedere la [scegliendo una sezione chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indicizzazione dei dati
Per impostazione predefinita, Azure Cosmos DB indicizza tutti i campi di dati durante l'inserimento. È possibile modificare il [criteri di indicizzazione](index-policy.md) in Azure Cosmos DB in qualsiasi momento. In effetti, spesso è consigliabile disattivare l'indicizzazione durante la migrazione dei dati e quindi riattivarla quando i dati sono già in Cosmos DB. Per altri dettagli sull'indicizzazione, è possibile leggere altre informazioni, vedere la [indicizzazione in Azure Cosmos DB](index-overview.md) sezione. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) esegue automaticamente la migrazione delle raccolte di MongoDB con gli indici univoci. Tuttavia, gli indici univoci devono essere creati prima della migrazione. Azure Cosmos DB non supporta la creazione di indici univoci, se ne è già i dati nelle raccolte. Per altre informazioni, vedere [Chiavi univoche in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire la migrazione dei dati di MongoDB a Cosmos DB con il servizio migrazione del Database.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Velocità effettiva di provisioning nel database e i contenitori di Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Partizionamento in Azure Cosmos DB)
* [Distribuzione globale in Azure Cosmos DB](distribute-data-globally.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
