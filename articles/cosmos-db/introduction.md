---
title: Introduzione ad Azure Cosmos DB
description: Informazioni su Azure Cosmos DB. Questo database multimodello distribuito a livello globale è pensato per garantire bassa latenza, scalabilità elastica, disponibilità elevata e offre il supporto nativo per i dati NoSQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: overview
ms.date: 03/18/2019
ms.author: sngun
ms.openlocfilehash: eec94217cc62f74a5075412aafcf85bf67346bf4
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294366"
---
# <a name="welcome-to-azure-cosmos-db"></a>Introduzione ad Azure Cosmos DB

Le applicazioni moderne devono garantire velocità di risposta elevate ed essere sempre online. Per ottenere bassa latenza e disponibilità elevata, è necessario che le istanze di queste applicazioni siano distribuite in data center vicini agli utenti. Le applicazioni devono rispondere in tempo reale alle importanti modifiche nell'utilizzo negli orari di punta, archiviare volumi sempre maggiori di dati e renderli disponibili agli utenti in millisecondi.

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. Con un semplice clic su un pulsante, Cosmos DB garantisce la scalabilità elastica e indipendente della velocità effettiva e delle risorse di archiviazione tra un numero qualsiasi di aree di Azure del mondo. È possibile ridimensionare in modo elastico la velocità effettiva e le risorse di archiviazione e sfruttare i vantaggi dell'accesso rapido ai dati con latenze inferiori al millisecondo usando l'API preferita tra SQL, MongoDB, Cassandra, Tables o Gremlin. Cosmos DB offre [contratti di servizio](https://aka.ms/acdbsla) completi per garantire velocità effettiva, latenza, disponibilità e coerenza, qualcosa che nessun altro servizio di database offre.

È possibile [provare Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure, gratuitamente e senza impegno.

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB è il servizio di database Microsoft distribuito a livello globale con scalabilità orizzontale elastica, bassa latenza garantita, cinque modelli di coerenza e contratti di servizio completi garantiti.](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Vantaggi principali

### <a name="turnkey-global-distribution"></a>Distribuzione globale chiavi in mano

Cosmos DB permette di creare applicazioni a reattività e disponibilità elevata in tutto il mondo. Cosmos DB replica in modo trasparente i dati ovunque gli utenti si trovino, in modo che possano interagire con una replica dei dati più vicina a loro.

Cosmos DB consente di aggiungere o rimuovere tutte le aree di Azure all'account Cosmos in qualsiasi momento, facendo clic su un pulsante. Cosmos DB replicherà senza problemi i dati in tutte le aree associate all'account Cosmos mentre l'applicazione continua a offrire la disponibilità elevata grazie alla funzionalità *multihoming* del servizio. Per altre informazioni, consultare l'articolo sulla [distribuzione globale](distribute-data-globally.md).

### <a name="always-on"></a>Always On

Grazie alla stretta integrazione con l'infrastruttura di Azure e la [replica multimaster trasparente](global-dist-under-the-hood.md), Cosmos DB offre la [disponibilità elevata al 99,999%](high-availability.md) sia in lettura che in scrittura. Cosmos DB offre anche la capacità richiamare a livello di codice (o attraverso il portale) il failover a livello di area dell'account Cosmos. Questa funzionalità assicura che l'applicazione sia progettata per il failover in caso di emergenza locale.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Scalabilità elastica della velocità effettiva e dell'archiviazione in tutto il mondo

Progettato con partizionamento orizzontale trasparente e replica multimaster, Cosmos DB offre una scalabilità elastica senza precedenti per le tue operazioni di lettura e di scrittura in tutto il mondo. In modo elastico, è possibile aumentare le prestazioni da migliaia a centinaia di milioni di richieste al secondo in tutto il mondo con una singola chiamata API, pagando solo per la velocità effettiva (e l'archiviazione) necessaria. Questa funzionalità consente di gestire i picchi imprevisti di carichi di lavoro senza dover eseguire il provisioning eccessivo. Per altre informazioni, vedere come eseguire il [partizionamento in Cosmos DB](partitioning-overview.md), il [provisioning della velocità effettiva per contenitori e database](set-throughput.md) e il [ridimensionamento della velocità effettiva sottoposta a provisioning a livello globale](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Bassa latenza garantita al 99° percentile, in tutto il mondo

Con Cosmos DB è possibile creare applicazioni a reattività elevata su scala globale. Grazie a questo innovativo protocollo di replica multimaster e al [motore di database ottimizzato per la scrittura e privo di latch](index-policy.md), Cosmos DB garantisce latenze inferiori ai 10 ms per operazioni di lettura e di scrittura (indicizzate) al 99° percentile in tutto il mondo. Questa capacità consente l'inserimento di grandi quantità di dati e l'esecuzione di query estremamente veloci per app a reattività elevata.

### <a name="precisely-defined-multiple-consistency-choices"></a>Più opzioni di coerenza definite con precisione

Quando si sviluppano applicazioni distribuite globalmente in Cosmos DB, non è più necessario [scegliere un compromesso tra coerenza, disponibilità, latenza e velocità effettiva](consistency-levels-tradeoffs.md). Il protocollo di replica multimaster di Cosmos DB è stato progettato attentamente per offrire [cinque opzioni di coerenza ben definite](consistency-levels.md) - *coerenza assoluta*, *con decadimento ristretto*, *di sessione*, *con prefisso coerente* ed *eventuale* - per un modello di programmazione intuitivo con bassa latenza e disponibilità elevata per l'applicazione distribuita a livello globale.

### <a name="no-schema-or-index-management"></a>Nessuna gestione di schemi o indici

La sincronizzazione dello schema del database e degli indici con uno schema dell'applicazione è particolarmente complicata per le app distribuite globalmente. Con Cosmos DB, non occorre gestire schemi o indici. Il motore di database è completamente indipendente dallo schema.  Dal momento che non è necessaria alcuna gestione di indici e schemi, non è neanche necessario preoccuparsi dei tempi di inattività dell'applicazione durante la migrazione degli schemi. Cosmos DB [indicizza automaticamente tutti i dati](index-policy.md) e le query vengono eseguite velocemente.

### <a name="battle-tested-database-service"></a>Servizio di database testato sul campo

Cosmos DB è un servizio di base in Azure. Da quasi un decennio, Cosmos DB viene usato da molti prodotti Microsoft per le applicazioni critiche su scala globale, tra cui Skype, Xbox, Office 365, Azure e molti altri. Oggi, Cosmos DB è uno dei servizi in più rapida crescita in Azure, usato da molti clienti esterni e da applicazioni strategiche che richiedono scalabilità elastica, distribuzione globale chiavi in mano, replica multimaster per bassa latenza e disponibilità elevata in lettura e scrittura.

### <a name="ubiquitous-regional-presence"></a>Presenza in tutte le aree

Cosmos DB è disponibile in tutte le aree di Azure in tutto il mondo, tra cui oltre 54 aree nel cloud pubblico, [Azure Cina 21Vianet](https://www.azure.cn/en-us/), Azure Germania, Azure per enti pubblici e Azure per enti pubblici per DOD (Department of Defense). Vedere la [presenza a livello di area di Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Soluzione protetta per impostazione predefinita e pronta per l'azienda

Cosmos DB è certificato per un [vasta gamma di standard di conformità](compliance.md). In più, tutti i dati in Cosmos DB vengono crittografati quando sono inattivi e in transito. Cosmos DB fornisce l'autorizzazione a livello di riga ed è conforme agli standard di sicurezza restrittivi.

### <a name="significant-tco-savings"></a>Notevoli risparmi sul costo totale di proprietà

Dal momento che Cosmos DB è un servizio completamente gestito, non è più necessario gestire ed eseguire complesse distribuzioni tra più data center e aggiornamenti del software del database, pagare il supporto tecnico, le licenze o la manutenzione oppure effettuare il provisioning del database per carichi di lavoro di picco. Per altre informazioni, vedere [Ottimizzare i costi con Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Contratti di servizio completi leader del settore

Cosmos DB è il primo e unico servizio che offre [contratti di servizio leader del settore completi](https://azure.microsoft.com/support/legal/sla/cosmos-db/) con disponibilità elevata al 99,999%, latenza di lettura e scrittura al 99° percentile, velocità effettiva garantita e coerenza.

### <a name="globally-distributed-operational-analytics-with-spark"></a>Analisi operativa distribuita a livello globale con Spark

È possibile eseguire [Spark](spark-connector.md) direttamente nei dati archiviati in Cosmos DB. Questa funzionalità consente di eseguire analisi operative di bassa latenza su scala globale senza conseguenze sui carichi di lavoro transazionali in funzione direttamente in Cosmos DB. Per altre informazioni, vedere [Analisi operativa distribuita a livello globale](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-nosql-apis"></a>Sviluppare applicazioni in Cosmos DB usando API NoSQL comuni

Cosmos DB offre una vasta gamma di API da usare con i dati archiviati nel database Cosmos. Per impostazione predefinita, [è possibile usare SQL](how-to-sql-query.md) (un'API core) per eseguire query sul database Cosmos. Cosmos DB implementa anche le API per [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) e [Archiviazione tabelle di Azure](table-introduction.md). È possibile indirizzare i driver (e gli strumenti) dei client per le API NoSQL di uso comune, ad esempio MongoDB, Cassandra, Gremlin, direttamente al database Cosmos. Grazie al supporto dei protocolli di collegamento delle API NoSQL di uso comune, Cosmos DB permette di:

* Migrare facilmente l'applicazione in Cosmos DB, mantenendo al tempo stesso parti significative della logica dell'applicazione.
* Mantenere l'applicazione portabile e continuare a rimanere indipendenti dal fornitore di cloud.
* Ottenere un servizio cloud completamente gestito con contratti di servizio leader del settore e supportati finanziariamente per le API NoSQL più comuni. 
* Ridimensionare in modo elastico la velocità effettiva e le risorse di archiviazione per i database in base alle proprie esigenze e pagare solo quello che è necessario. Ciò comporta risparmi significativi sui costi.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluzioni che traggono vantaggio da Azure Cosmos DB

Qualsiasi [applicazione Web, per dispositivi mobili, di gioco e IoT](use-cases.md) che debba gestire un numero molto elevato di dati, letture e scritture su scala [globale](distribute-data-globally.md) con risposte quasi in tempo reale per una varietà di dati può trarre beneficio dalle caratteristiche di disponibilità elevata, velocità effettiva elevata, bassa latenza e coerenza ottimizzabile [garantite](https://azure.microsoft.com/support/legal/sla/cosmos-db/) di Cosmos DB. Informazioni su come usare Azure Cosmos DB per compilare [IoT e dati telematici](use-cases.md#iot-and-telematics), [vendite e marketing](use-cases.md#retail-and-marketing), [giochi](use-cases.md#gaming) e [applicazioni Web e per dispositivi mobili](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui concetti di base di Cosmos DB per la [distribuzione globale chiavi in mano](distribute-data-globally.md), il [partizionamento](partitioning-overview.md) e il [provisioning della velocità effettiva](request-units.md).

Per un'introduzione ad Azure Cosmos DB, fare riferimento alle guide introduttive seguenti:

* [Introduzione all'API SQL di Azure Cosmos DB](create-sql-api-dotnet.md)
* [Come iniziare a usare l'API di Azure Cosmos DB per MongoDB](create-mongodb-nodejs.md)
* [Introduzione all'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introduzione all'API Gremlin di Azure Cosmos DB](create-graph-dotnet.md)
* [Introduzione all'API Tabelle di Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
