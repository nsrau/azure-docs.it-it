---
title: Che cos'è l'archivio analitico di Azure Cosmos DB (anteprima)?
description: Informazioni sull'archivio transazionale (basato su righe) e sull'archivio analitico (basato su colonne) di Azure Cosmos DB. Vantaggi dell'archivio analitico, impatto sulle prestazioni per carichi di lavoro su larga scala e sincronizzazione automatica dei dati dall'archivio transazionale all'archivio analitico
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: 3b210ea558f857d017504d07e571e94e34c0d4f6
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037100"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Che cos'è l'archivio analitico di Azure Cosmos DB (anteprima)?

> [!IMPORTANT]
> L'archivio analitico di Azure Cosmos DB attualmente è disponibile in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'archivio analitico di Azure Cosmos DB è un archivio colonne completamente isolato per consentire l'analisi su larga scala dei dati operativi in Azure Cosmos DB, senza alcun impatto sui carichi di lavoro transazionali.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Problemi con l'analisi su larga scala dei dati operativi

I dati operativi multimodello in un contenitore Azure Cosmos DB vengono archiviati internamente in un "archivio transazionale" indicizzato basato su righe. Il formato dell'archivio righe è progettato per consentire letture e scritture transazionali rapide nei tempi di risposta dell'ordine dei millisecondi e query operative. Se le dimensioni del set di dati aumentano, le query analitiche complesse possono risultare costose in termini di velocità effettiva con provisioning sui dati archiviati in questo formato. L'utilizzo elevato della velocità effettiva con provisioning a sua volta influisce sulle prestazioni dei carichi di lavoro transazionali usati dalle applicazioni e dai servizi in tempo reale.

Tradizionalmente, per analizzare grandi quantità di dati, i dati operativi vengono estratti dall'archivio transazionale di Azure Cosmos DB e archiviati in un livello dati separato. Ad esempio, i dati vengono archiviati in un data warehouse o data lake in un formato appropriato. Questi dati vengono successivamente usati per analisi su larga scala e analizzati con un motore di calcolo come i cluster Apache Spark. Questa separazione dei livelli di archiviazione analitica e di calcolo dai dati operativi comporta una latenza aggiuntiva, perché le pipeline ETL (Extract, Transform, Load) vengono eseguite con minore frequenza per ridurre al minimo l'impatto potenziale sui carichi di lavoro transazionali.

Anche le pipeline ETL diventano complesse quando si gestiscono gli aggiornamenti ai dati operativi rispetto alla gestione dei soli dati operativi appena inseriti. 

## <a name="column-oriented-analytical-store"></a>Archivio analitico orientato alle colonne

L'archivio analitico di Azure Cosmos DB risolve i problemi di complessità e latenza che si verificano con le pipeline ETL tradizionali. L'archivio analitico di Azure Cosmos DB può sincronizzare automaticamente i dati operativi in un archivio colonne separato. Il formato dell'archivio colonne è adatto per l'esecuzione di query analitiche su larga scala in modo ottimizzato, con conseguente miglioramento della latenza di tali query.

Usando Collegamento ad Azure Synapse, è ora possibile creare soluzioni HTAP senza ETL collegandosi direttamente all'archivio analitico di Azure Cosmos DB da Synapse Analytics. Consente di eseguire analisi su larga scala in near real-time sui dati operativi.

## <a name="analytical-store-details"></a>Dettagli dell'archivio analitico

Quando si abilita l'archivio analitico in un contenitore Azure Cosmos DB, viene creato internamente un nuovo archivio colonne in base ai dati operativi presenti nel contenitore. Questo archivio colonne viene mantenuto separato dall'archivio transazionale orientato alle righe di tale contenitore. Gli inserimenti, gli aggiornamenti e le eliminazioni dei dati operativi vengono sincronizzati automaticamente con l'archivio analitico. Non è necessario il feed di modifiche o ETL per sincronizzare i dati.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Archivio colonne per carichi di lavoro analitici su dati operativi

I carichi di lavoro analitici in genere comportano aggregazioni e analisi sequenziali dei campi selezionati. Archiviando i dati in ordine column-major, l'archivio analitico consente di serializzare insieme un gruppo di valori per ogni campo. Questo formato riduce le operazioni di I/O al secondo necessarie per l'analisi o il calcolo delle statistiche su campi specifici. Migliora notevolmente i tempi di risposta delle query per le analisi su set di dati di grandi dimensioni. 

Ad esempio, se le tabelle operative hanno il formato seguente:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Tabella operativa di esempio" border="false":::

L'archivio righe salva in modo permanente i dati sopra riportati in un formato serializzato, per ogni riga, sul disco. Questo formato consente letture e scritture transazionali più veloci e query operative, ad esempio "restituire informazioni su Product1". Tuttavia, man mano che le dimensioni del set di dati aumentano e se si vogliono eseguire query analitiche complesse sui dati, può essere costoso. Se, ad esempio, si vogliono ottenere "le tendenze di vendita per un prodotto nella categoria denominata 'Equipment' in diverse business unit e mesi", è necessario eseguire una query complessa. Le analisi di grandi dimensioni in questo set di dati possono risultare costose in termini di velocità effettiva con provisioning e possono anche influire sulle prestazioni dei carichi di lavoro transazionali che alimentano le applicazioni e i servizi in tempo reale.

L'archivio analitico, che è un archivio colonne, è più adatto per tali query perché serializza insieme i campi di dati simili e riduce le operazioni di I/O al secondo del disco.

Nell'immagine seguente viene illustrato l'archivio righe transazionale rispetto all'archivio colonne analitico in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Archivio righe transazionale rispetto all'archivio colonne analitico in Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Prestazioni separate per carichi di lavoro analitici

Non vi è alcun impatto sulle prestazioni dei carichi di lavoro transazionali a causa di query analitiche, in quanto l'archivio analitico è separato dall'archivio transazionale.  Per l'archivio analitico non è necessario allocare unità richiesta separate (UR).

### <a name="auto-sync"></a>Sincronizzazione automatica

La sincronizzazione automatica si riferisce alla funzionalità completamente gestita di Azure Cosmos DB in cui le operazioni di inserimento, aggiornamento ed eliminazione dei dati operativi vengono sincronizzate automaticamente dall'archivio transazionale all'archivio analitico in near real-time entro 5 minuti.

La funzionalità di sincronizzazione automatica insieme all'archivio analitico offre i vantaggi principali seguenti:

#### <a name="scalability--elasticity"></a>Scalabilità ed elasticità

Grazie al partizionamento orizzontale, l'archivio transazionale di Azure Cosmos DB può dimensionare in modo elastico l'archiviazione e la velocità effettiva senza tempi di inattività. Il partizionamento orizzontale nell'archivio transazionale fornisce scalabilità ed elasticità nella sincronizzazione automatica per garantire la sincronizzazione dei dati nell'archivio analitico in near real-time. La sincronizzazione dei dati viene eseguita indipendentemente dalla velocità effettiva del traffico transazionale, sia che si tratti di 1.000 operazioni al secondo che di un milione di operazioni al secondo, e non influisce sulla velocità effettiva con provisioning nell'archivio transazionale. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Gestione automatica degli aggiornamenti dello schema

L'archivio transazionale di Azure Cosmos DB è senza schema e consente di eseguire l'iterazione sulle applicazioni transazionali senza dover gestire schemi o indici. Al contrario, l'archivio analitico di Azure Cosmos DB è schematizzato per ottimizzare le prestazioni delle query analitiche. Con la funzionalità di sincronizzazione automatica, Azure Cosmos DB gestisce l'inferenza dello schema sugli aggiornamenti più recenti dall'archivio transazionale.  Gestisce inoltre per impostazione predefinita la rappresentazione dello schema nell'archivio analitico, che include la gestione dei tipi di dati annidati.

Si tratta di un'evoluzione dello schema, in cui vengono aggiunte nuove proprietà nel tempo, e l'archivio analitico presenta automaticamente uno schema unificato di tutti gli schemi cronologici nell'archivio transazionale.

Se tutti i dati operativi in Azure Cosmos DB seguono uno schema ben definito per l'analisi, lo schema viene automaticamente dedotto e rappresentato correttamente nell'archivio analitico. Se lo schema ben definito per l'analisi, come definito di seguito, viene violato da alcuni elementi, questi non verranno inclusi nell'archivio analitico. Se sono presenti scenari bloccati a causa di uno schema ben definito per la definizione dell'analisi, inviare un messaggio di posta elettronica al [team di Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

Uno schema ben definito per l'analisi viene definito con le considerazioni seguenti:

* Una proprietà ha sempre lo stesso tipo in più elementi

  * Ad esempio, `{"a":123} {"a": "str"}` non ha uno schema ben definito perché `"a"` a volte è una stringa e a volte un numero. 
  
    In questo caso, l'archivio analitico registra il tipo di dati di `“a”` come tipo di dati di `“a”` nell'elemento che si verifica per primo per tutta la durata del contenitore. Gli elementi in cui il tipo di dati di `“a”` è diverso non verranno inclusi nell'archivio analitico.
  
    Questa condizione non è valida per le proprietà Null. Ad esempio, `{"a":123} {"a":null}` è ancora ben definito.

* I tipi di matrice devono contenere un solo tipo ripetuto

  * Ad esempio, `{"a": ["str",12]}` non è uno schema ben definito perché la matrice contiene una combinazione di tipi integer e stringa

* Esiste un massimo di 200 proprietà per ogni livello di annidamento di uno schema e un numero massimo di livelli di annidamento pari a 5

  * Un elemento con proprietà 201 al livello superiore non ha uno schema ben definito.

  * Anche un elemento con più di cinque livelli annidati nello schema non ha uno schema ben definito. Ad esempio, usare `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* I nomi delle proprietà sono univoci se confrontati senza distinzione tra maiuscole e minuscole

  * Gli elementi seguenti, ad esempio, non hanno uno schema ben definito `{"Name": "fred"} {"name": "john"}` – `"Name"` e `"name"` sono uguali se confrontati senza distinzione tra maiuscole e minuscole

### <a name="cost-effective-archival-of-historical-data"></a>Archiviazione conveniente di dati cronologici

La suddivisione in livelli dei dati si riferisce alla separazione dei dati tra le infrastrutture di archiviazione ottimizzate per diversi scenari. Migliorando in tal modo le prestazioni complessive e la convenienza dello stack di dati end-to-end. Con l'archivio analitico, Azure Cosmos DB supporta ora la suddivisione in livelli automatica dei dati dall'archivio transazionale all'archivio analitico con layout di dati diversi. Con l'archivio analitico ottimizzato in termini di costi di archiviazione rispetto all'archivio transazionale, consente di mantenere orizzonti molto più lunghi dei dati operativi per l'analisi cronologica.

Dopo aver abilitato l'archivio analitico, in base alle esigenze di conservazione dei dati dei carichi di lavoro transazionali, è possibile configurare la proprietà 'Transactional Store Time to Live (Transactional TTL)' in modo che i record vengano eliminati automaticamente dall'archivio transazionale dopo un determinato periodo di tempo. Analogamente, la proprietà 'Analytical Store Time To Live (Analytical TTL)' consente di gestire il ciclo di vita dei dati conservati nell'archivio analitico indipendentemente dall'archivio transazionale. Abilitando l'archivio analitico e configurando le proprietà TTL, è possibile suddividere in livelli i dati e definire il periodo di conservazione dei dati per i due archivi con facilità.

### <a name="global-distribution"></a>Distribuzione globale

Se si ha un account Azure Cosmos DB distribuito a livello globale, un archivio analitico abilitato per un contenitore sarà disponibile in tutte le aree di tale account.  Tutte le modifiche apportate ai dati operativi vengono replicate a livello globale in tutte le aree. È possibile eseguire efficacemente query analitiche sulla copia locale più vicina dei dati in Azure Cosmos DB.

### <a name="security"></a>Sicurezza

L'autenticazione con l'archivio analitico è identica a quella dell'archivio transazionale per un determinato database. Per l'autenticazione è possibile usare chiavi master o di sola lettura. È possibile sfruttare il servizio collegato in Synapse Studio per evitare di incollare le chiavi di Azure Cosmos DB nei notebook di Spark. L'accesso a questo servizio collegato è disponibili per chiunque abbia accesso all'area di lavoro.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Supporto di più runtime di Azure Synapse Analytics

L'archivio analitico è ottimizzato per offrire scalabilità, elasticità e prestazioni per carichi di lavoro analitici senza alcuna dipendenza dai runtime di calcolo. La tecnologia di archiviazione è gestita automaticamente per ottimizzare i carichi di lavoro analitici senza interventi manuali.

Separando il sistema di archiviazione analitico dal sistema di calcolo analitico, è possibile eseguire simultaneamente query sui dati nell'archivio analitico di Azure Cosmos DB da diversi runtime di analisi supportati da Azure Synapse Analytics. Attualmente, Synapse Analytics supporta Apache Spark e SQL serverless con l'archivio analitico di Azure Cosmos DB.

> [!NOTE]
> È possibile leggere dall'archivio analitico solo con il runtime di Synapse Analytics. È possibile scrivere di nuovo i dati nell'archivio transazionale come livello di gestione.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Prezzi

L'archivio analitico segue un modello di prezzi a consumo in base al quale viene addebitato il costo:

* Archiviazione: il volume dei dati conservati nell'archivio analitico ogni mese, inclusi i dati cronologici definiti dalla proprietà Analytical TTL.

* Operazioni di scrittura analitica: sincronizzazione completamente gestita degli aggiornamenti dei dati operativi nell'archivio analitico dall'archivio transazionale (sincronizzazione automatica)

* Operazioni di lettura analitica: le operazioni di lettura eseguite sull'archivio analitico dai runtime Spark e SQL serverless di Synapse Analytics.

> [!NOTE]
> L'archivio analitico di Azure Cosmos DB è disponibile in anteprima pubblica senza alcun addebito fino al 30 agosto 2020.

Il prezzo dell'archivio analitico è separato dal modello di prezzi dell'archivio transazioni. Non esiste alcun concetto di UR con provisioning nell'archivio analitico. Per informazioni dettagliate sul modello di prezzi per l'archivio analitico, vedere la [pagina dei prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

Per ottenere una stima dei costi di alto livello per abilitare l'archivio analitico in un contenitore di Azure Cosmos DB, è possibile usare lo [strumento di pianificazione della capacità di Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) e ottenere una stima dei costi di archiviazione analitica e delle operazioni di scrittura. I costi per le operazioni di lettura analitica dipendono dalle caratteristiche del carico di lavoro di analisi, ma come stima di alto livello, l'analisi di 1 TB di dati nell'archivio analitico comporta in genere 130.000 operazioni di lettura analitica con un costo pari a $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Durata (TTL) dei dati analitici

La durata (TTL) dei dati analitici indica per quanto tempo i dati devono essere conservati nell'archivio analitico, per un contenitore. 

Le operazioni di inserimento, aggiornamento ed eliminazione dei dati operativi vengono sincronizzate automaticamente dall'archivio transazionale all'archivio analitico, indipendentemente dalla configurazione della durata (TTL) dei dati transazionali. La conservazione di questi dati operativi nell'archivio analitico può essere controllata dal valore della durata (TTL) dei dati analitici a livello di contenitore, come specificato di seguito:

Il valore della durata (TTL) dei dati analitici in un contenitore viene impostato usando la proprietà `AnalyticalStoreTimeToLiveInSeconds`:

* Se il valore è impostato su "0", manca (o è impostato su Null): l'archivio analitico è disabilitato e i dati non vengono replicati dall'archivio transazionale all'archivio analitico

* Se presente e il valore è impostato su "-1": l'archivio analitico conserva tutti i dati cronologici, indipendentemente dalla conservazione dei dati nell'archivio transazionale. Questa impostazione indica che l'archivio analitico ha una conservazione infinita dei dati operativi

* Se presente e il valore è impostato su un numero positivo "n": gli elementi scadranno dall'archivio analitico "n" secondi dopo l'ora dell'ultima modifica nell'archivio transazionale. Questa impostazione può essere usata se si vuole conservare i dati operativi per un periodo di tempo limitato nell'archivio analitico, indipendentemente dalla conservazione dei dati nell'archivio transazionale.

Alcune informazioni da considerare:
*   Dopo aver abilitato l'archivio analitico con un valore della durata (TTL) dei dati analitici, è possibile aggiornarlo con un altro valore valido in un secondo momento. 
*   Mentre la durata (TTL) dei dati transazionali può essere impostata a livello di contenitore o di elemento, la durata (TTL) dei dati analitici può essere impostata solo a livello di contenitore.
*   È possibile ottenere un periodo di conservazione più lungo dei dati operativi nell'archivio analitico impostando la proprietà TTL dei dati analitici su un valore maggiore o uguale alla proprietà TTL dei dati transazionali a livello di contenitore.
*   L'archivio analitico può essere reso speculare all'archivio transazionale impostando la proprietà TTL dei dati analitici su un valore uguale alla proprietà TTL dei dati transazionali.

Per altre informazioni, vedere [come configurare la durata (TTL) dei dati analitici in un contenitore](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la documentazione seguente:

* [Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link.md)

* [Introduzione a Collegamento ad Azure Synapse per Azure Cosmos DB](configure-synapse-link.md)

* [Domande frequenti su Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casi d'uso di Collegamento ad Azure Synapse per Azure Cosmos DB](synapse-link-use-cases.md)
