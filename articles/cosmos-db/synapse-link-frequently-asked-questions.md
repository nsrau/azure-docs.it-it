---
title: Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB
description: Risposte alle domande frequenti su Collegamento a Synapse per Azure Cosmos DB in aree come fatturazione, archivio analitico, sicurezza e durata (TTL) nell'archivio analitico.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e65838074260a4d4a9c9668fa6ac4aa2a403929b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037559"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB

Collegamento ad Azure Synapse per Azure Cosmos DB crea una stretta integrazione tra Azure Cosmos DB e Azure Synapse Analytics. Consente ai clienti di eseguire analisi quasi in tempo reale sui dati operativi con l'isolamento completo delle prestazioni dai carichi di lavoro transazionali e senza una pipeline ETL. Questo articolo contiene le risposte alle domande frequenti su Collegamento a Synapse per Azure Cosmos DB.

## <a name="general-faq"></a>Domande frequenti di carattere generale

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Collegamento a Synapse è supportato per tutte le API di Azure Cosmos DB?
Nella versione di anteprima pubblica, Collegamento a Synapse è supportato solo per l'API SQL (Core) di Azure Cosmos DB. Il supporto per le API di Cosmos DB per Mongo DB e API Cassandra è attualmente in fase di anteprima controllata.  Per richiedere l'accesso all'anteprima controllata, contattare il [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Collegamento ad Azure Synapse è supportato per account Azure Cosmos in più aree?
Sì, per gli account Azure Cosmos in più aree, anche i dati archiviati nell'archivio analitico vengono distribuiti globalmente. Sia nelle singole aree di scrittura (master singolo) che in più aree di scrittura (multimaster), le query analitiche eseguite da Azure Synapse Analytics possono essere gestite dall'area locale più vicina.

Quando si pianifica la configurazione di un account Azure Cosmos in più aree con supporto dell'archivio analitico, è consigliabile aggiungere tutte le aree necessarie al momento della creazione.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>È possibile scegliere di abilitare Collegamento a Synapse solo per una specifica area e non per tutte quelle incluse nella configurazione di un account in più aree?
Nella versione di anteprima, se Collegamento a Synapse è abilitato per un account in più aree, l'archivio analitico viene creato in tutte le aree. I dati sottostanti sono ottimizzati per la velocità effettiva e per la coerenza transazionale nell'archivio transazionale.

### <a name="is-backup-and-restore-supported-for-synapse-link-enabled-accounts"></a>Il backup e il ripristino sono supportati per gli account abilitati per il collegamento sinapsi?
In anteprima, per gli account di database abilitati per il collegamento sinapsi, il backup e il ripristino di contenitori non sono supportati. Se si dispone di carichi di lavoro di produzione che richiedono funzionalità di backup e ripristino, è consigliabile non abilitare il collegamento sinapsi per gli account di database. 

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>È possibile disabilitare la funzionalità Collegamento a Synapse per un account Azure Cosmos?
Attualmente, dopo aver abilitato la funzionalità Collegamento a Synapse a livello di account, non è possibile disabilitarla.  Per disattivare la funzionalità, è necessari eliminare l'account Azure Cosmos e crearne uno nuovo.

Tenere presente che se si abilita la funzionalità Collegamento a Synapse a livello di account, **non** si verificano implicazioni in termini di fatturazione, ma non saranno disponibili contenitori abilitati per l'archivio analitico.

## <a name="azure-cosmos-db-analytical-store"></a>Archivio analitico di Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>È possibile abilitare l'archivio analitico nei contenitori esistenti?
Attualmente, l'archivio analitico può essere abilitato solo per i nuovi contenitori (in account nuovi ed esistenti).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>È possibile disabilitare l'archivio analitico dopo averlo abilitato durante la creazione dei contenitore Azure Cosmos ?
Attualmente, l'archivio analitico non può essere disabilitato dopo che è stato abilitato durante la creazione di un contenitore Azure Cosmos.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>L'archivio analitico è supportato per i contenitori Azure Cosmos con velocità effettiva con provisioning a scalabilità automatica?
Sì, l'archivio analitico può essere abilitato nei contenitori con velocità effettiva con provisioning a scalabilità automatica.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Si verificano effetti sulle unità richiesta con provisioning dell'archivio transazionale di Azure Cosmos DB?
Azure Cosmos DB garantisce l'isolamento delle prestazioni tra carichi di lavoro transazionali e analitici. L'abilitazione dell'archivio analitico in un contenitore non influirà sulle UR/s con provisioning nell'archivio transazionale di Azure Cosmos DB. I costi delle transazioni (lettura e scrittura) e dell'archiviazione per l'archivio analitico verranno addebitati separatamente. Per altri dettagli, vedere i [prezzi dell'archivio analitico di Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing).

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Le operazioni di eliminazione e aggiornamento eseguite nell'archivio transazionale si riflettono nell'archivio analitico? 
Sì, le eliminazioni e gli aggiornamenti dei dati nell'archivio transazionale si riflettono nell'archivio analitico. È possibile configurare la durata (TTL) nel contenitore per includere i dati cronologici, in modo che l'archivio analitico conservi tutte le versioni degli elementi che soddisfano i criteri di TTL. Per altri dettagli, vedere la [panoramica della durata (TTL) dell'analisi](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>È possibile connettersi all'archivio analitico da motori di analisi diversi da Azure Synapse Analytics?
È possibile accedere ed eseguire query sull'archivio analitico solo con i vari runtime forniti da Azure Synapse Analytics. È possibile eseguire analisi e query sull'archivio analitico usando:

* Synapse Spark con supporto completo per Scala, Python, SparkSQL e C#. Synapse Spark è fondamentale per gli scenari di ingegneria dei dati e data science
* SQL serverless con linguaggio T-SQL e supporto per strumenti di BI familiari, ad esempio Power BI Premium e così via.

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>È possibile connettersi all'archivio analitico da Synapse SQL con provisioning?
Al momento non è possibile accedere all'archivio analitico da Synapse SQL con provisioning.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>È possibile eseguire il writeback dei risultati di aggregazione delle query da Synapse all'archivio analitico?
L'archivio analitico è di sola lettura nei contenitori Azure Cosmos. Non è quindi possibile eseguire il writeback dei risultati di aggregazione nell'archivio analitico, ma è possibile scriverli nell'archivio transazionale di Azure Cosmos DB di un altro contenitore, che in seguito può essere sfruttato come livello di servizio.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>La replica con sincronizzazione automatica dall'archivio transazionale all'archivio analitico è asincrona o sincrona e quali sono le latenze? 
La replica è asincrona e attualmente la latenza prevista è di circa 2 minuti.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Esistono scenari in cui gli elementi dell'archivio transazionale non vengono propagati automaticamente nell'archivio analitico?
Se specifici elementi del contenitore violano lo [schema ben definito per l'analisi](analytical-store-introduction.md#analytical-schema), non verranno inclusi nell'archivio analitico. Se sono presenti scenari bloccati dallo schema ben definito per l'analisi, inviare un messaggio di posta elettronica al [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) per assistenza.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>È possibile partizionare i dati nell'archivio analitico in modo diverso rispetto all'archivio transazionale?
I dati nell'archivio analitico vengono partizionati in base al partizionamento orizzontale delle partizioni dell'archivio transazionale. Attualmente, non è possibile scegliere una strategia di partizionamento diversa per l'archivio analitico.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>È possibile personalizzare o sostituire il modo in cui i dati transazionali vengono trasformati in formato a colonne nell'archivio analitico?
Attualmente, non è possibile trasformare i dati quando vengono propagati automaticamente dall'archivio transazionale all'archivio analitico. Se sono presenti scenari bloccati da questa limitazione, inviare un messaggio di posta elettronica al [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Durata (TTL) dei dati analitici

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>La durata dei dati analitici è supportata sia a livello di contenitore che di elemento?
Attualmente, il TTL per i dati analitici può essere configurato solo a livello di contenitore e non è previsto il supporto per impostarlo a livello di elemento.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Dopo aver impostato il TTL dei dati analitici a livello di contenitore in un contenitore Azure Cosmos DB, è possibile cambiarne il valore in seguito?
Sì, il TTL dei dati analitici può essere aggiornato a qualsiasi valore valido. Per informazioni più dettagliate sul TTL dei dati analitici, vedere l'articolo [Durata (TTL) dei dati analitici](analytical-store-introduction.md#analytical-ttl).

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>È possibile aggiornare o eliminare un elemento dall'archivio analitico dopo che è scaduto nell'archivio transazionale?
Tutti gli aggiornamenti e le eliminazioni transazionali vengono copiati nell'archivio analitico, ma se l'elemento è stato eliminato dall'archivio transazionale, non può essere aggiornato nell'archivio analitico. Per altre informazioni, vedere l'articolo [Durata (TTL) dei dati analitici](analytical-store-introduction.md#analytical-ttl).

## <a name="billing"></a>Fatturazione

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Qual è il modello di fatturazione di Collegamento a Synapse per Azure Cosmos DB?
L'[archivio analitico di Azure Cosmos DB](analytical-store-introduction.md) è disponibile in anteprima pubblica senza alcun addebito fino al 30 agosto 2020. Synapse Spark e Synapse SQL prevedono la fatturazione [a consumo del servizio Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Sicurezza

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Come si esegue l'autenticazione con l'archivio analitico?
L'autenticazione con l'archivio analitico è identica a quella di un archivio transazionale. Per un database specifico, è possibile eseguire l'autenticazione con la chiave master o di sola lettura. È possibile sfruttare il servizio collegato in Synapse Studio per evitare di incollare le chiavi di Azure Cosmos DB nei notebook di Spark. L'accesso a questo servizio collegato è disponibile per chiunque abbia accesso all'area di lavoro.

## <a name="synapse-run-times"></a>Runtime di Synapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Quali sono i runtime di Synapse attualmente supportati per l'accesso all'archivio analitico di Azure Cosmos DB?

|Runtime di Synapse |Supporto corrente |
|---------|---------|
|Pool di Synapse Spark | Lettura, scrittura (tramite archivio transazionale), tabella, visualizzazione temporanea |
|Server SQL sinapsi    | Lettura, visualizzazione (anteprima controllata)  |
|Synapse SQL con provisioning   |  Non disponibile |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Le tabelle sinapsi Spark vengono sincronizzate con le tabelle SQL senza server sinapsi nello stesso modo in cui funzionano con Azure Data Lake?
Questa funzionalità non è attualmente disponibile.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>È possibile eseguire Structured Streaming di Spark dall'archivio analitico?
Attualmente il supporto di Structured Streaming di Spark per Azure Cosmos DB viene implementato tramite la funzionalità del feed di modifiche dell'archivio transazionale e non è ancora previsto nell'archivio analitico.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>In Synapse Studio come si riconosce se si è connessi a un contenitore Azure Cosmos DB con l'archivio analitico abilitato?
Un contenitore Azure Cosmos DB abilitato con l'archivio analitico presenta l'icona seguente:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Icona del contenitore Azure Cosmos DB abilitato con l'archivio analitico":::

Un contenitore dell'archivio transazione sarà rappresentato dall'icona seguente:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Icona del contenitore Azure Cosmos DB abilitato con l'archivio transazionale":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Come si passano le credenziali di Azure Cosmos DB da Synapse Studio?
Attualmente, le credenziali di Azure Cosmos DB vengono passate durante la creazione del servizio collegato dall'utente che ha accesso ai database Azure Cosmos DB. L'accesso a tale archivio è disponibile per altri utenti che hanno accesso all'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [vantaggi di Collegamento a Synapse](synapse-link.md#synapse-link-benefits)

* Informazioni sull'[integrazione tra Collegamento a Synapse e Azure Cosmos DB](synapse-link.md#synapse-link-integration).
