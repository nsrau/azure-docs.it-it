---
title: Funzionalità multimodello
description: Microsoft Azure SQL consente di utilizzare più modelli di dati nello stesso database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 59a709a206eb29b875272674ee19e414023cc37f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073310"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Funzionalità multimodello del database SQL di Azure & SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

I database multimodello consentono di archiviare e usare i dati rappresentati in più formati di dati, ad esempio dati relazionali, grafici, documenti JSON/XML, coppie chiave-valore e così via.

## <a name="when-to-use-multi-model-capabilities"></a>Quando usare le funzionalità multimodello

La [famiglia di prodotti SQL di Azure](azure-sql-iaas-vs-paas-what-is-overview.md) è progettata per funzionare con il modello relazionale che offre le migliori prestazioni nella maggior parte dei casi per un'ampia gamma di applicazioni per utilizzo generico. Tuttavia, la famiglia di prodotti SQL di Azure non è limitata ai soli dati relazionali. La famiglia di prodotti SQL di Azure consente di usare un'ampia gamma di formati non relazionali che sono strettamente integrati nel modello relazionale.
È consigliabile usare le funzionalità multimodello della famiglia di prodotti SQL di Azure nei casi seguenti:

- Sono disponibili alcune informazioni o strutture più adatte per i modelli NoSQL e non si vuole usare un database NoSQL separato.
- La maggior parte dei dati è adatta per il modello relazionale ed è necessario modellare alcune parti dei dati nello stile NoSQL.
- Si desidera sfruttare il linguaggio Transact-SQL avanzato per eseguire query e analizzare i dati relazionali e NoSQL e integrarli con un'ampia gamma di strumenti e applicazioni in grado di utilizzare il linguaggio SQL.
- Si desidera applicare funzionalità di database quali le [tecnologie in memoria](in-memory-oltp-overview.md) per migliorare le prestazioni dell'analisi o dell'elaborazione delle strutture di dati NoSQL, utilizzare la [replica transazionale](managed-instance/replication-transactional-overview.md) o le [repliche leggibili](database/read-scale-out.md) per creare una copia dei dati in un'altra posizione ed eseguire l'offload di alcuni carichi di lavoro analitici dal database primario.

## <a name="overview"></a>Panoramica

La famiglia di prodotti SQL di Azure offre le seguenti funzionalità multimodello:

- Le [funzionalità del grafo](#graph-features) consentono di rappresentare i dati come set di nodi e archi e di usare query Transact-SQL standard migliorate con operatore del grafo `MATCH` per eseguire query sui dati del grafico.
- Le [funzionalità JSON](#json-features) consentono di inserire documenti JSON in tabelle, trasformare i dati relazionali in documenti JSON e viceversa. È possibile usare il linguaggio Transact-SQL standard migliorato con le funzioni JSON per l'analisi dei documenti e usare indici non cluster, indici columnstore o tabelle ottimizzate per la memoria per ottimizzare le query.
- [Funzionalità spaziali](#spatial-features) consente di archiviare i dati geometrici e geografici, indicizzarli utilizzando gli indici spaziali e recuperare i dati usando le query spaziali.
- Le [funzionalità XML](#xml-features) consentono di archiviare e indicizzare i dati XML nel database e usare operazioni XQuery o XPath native per usare i dati XML. La famiglia di prodotti SQL di Azure include un motore di query XML incorporato specializzato che elabora i dati XML.
- Le [coppie chiave-valore](#key-value-pairs) non sono supportate in modo esplicito come funzionalità speciali perché le coppie chiave-valore possono essere modellate in modo nativo come tabelle a due colonne.

  > [!Note]
  > È possibile usare espressioni di percorso JSON, espressioni XQuery o XPath, funzioni spaziali ed espressioni query-grafo nella stessa query Transact-SQL per accedere ai dati archiviati nel database. Inoltre, qualsiasi strumento o linguaggio di programmazione in grado di eseguire query Transact-SQL può usare quell'interfaccia di query per accedere ai dati multimodello. Questa è la differenza principale rispetto ai database multimodello, come ad esempio [Azure Cosmos DB](/azure/cosmos-db/) che fornisce API specializzate per diversi modelli di dati.

Nelle sezioni seguenti sono disponibili informazioni sulle più importanti funzionalità multimodello della famiglia di prodotti SQL di Azure.

## <a name="graph-features"></a>Funzionalità del grafo

La famiglia di prodotti SQL di Azure offre funzionalità di database a grafo per modellare relazioni molti-a-molti nel database. Un grafo è una raccolta di nodi (o vertici) e archi (o relazioni). Un nodo rappresenta un'entità (ad esempio, una persona o un'organizzazione) e un arco rappresenta una relazione tra i due nodi che collega (ad esempio, mi piace o amici). Ecco alcune funzionalità che rendono univoco un database a grafo:

- Gli archi o relazioni sono entità di prima classe in un Database a grafo e possono avere attributi o proprietà associate ad essi.
- Un singolo arco può collegare in modo flessibile più nodi in un Database a grafo.
- È possibile esprimere facilmente criteri di ricerca e query di navigazione a più hop.
- È possibile esprimere facilmente chiusura transitiva e query polimorfiche.

Le [relazioni Graph e le funzionalità di query Graph](/sql/relational-databases/graphs/sql-graph-overview) sono integrate in Transact-SQL e ricevono i vantaggi derivanti dall'utilizzo del motore di database SQL Server come sistema di gestione di database di base.

### <a name="when-to-use-a-graph-capability"></a>Quando usare una funzionalità del grafo

Non esiste nulla che possa essere ottenuto da un database a grafo, che non possa essere ottenuto usando un database relazionale. Tuttavia, un database a grafo può rendere più semplice esprimere determinate query. La decisione di scegliere uno di essi può essere basata sui fattori seguenti:

- Dati gerarchici modello in cui un solo nodo può avere più elementi padre, per cui non è possibile usare HierarchyId
- Il modello ha un'applicazione che dispone di relazioni molti a molti complesse; con l'evoluzione dell'applicazione, vengono aggiunte nuove relazioni.
- È necessario analizzare relazioni e dati interconnessi.

## <a name="json-features"></a>Funzionalità JSON

La famiglia di prodotti SQL di Azure consente di analizzare ed eseguire query sui dati rappresentati nel formato JavaScript Object Notation [(JSON)](https://www.json.org/) ed esportare i dati relazionali come testo JSON.

JSON è un formato di dati diffuso usato per scambiare dati nelle moderne applicazioni Web e per dispositivi mobili. JSON è usato anche per archiviare dati semistrutturati nei file di log o nei database NoSQL come [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Molti servizi Web REST restituiscono risultati formattati come testo JSON oppure accettano dati formattati come JSON. La maggior parte dei servizi di Azure, ad esempio [azure ricerca cognitiva](https://azure.microsoft.com/services/search/), [archiviazione di Azure](https://azure.microsoft.com/services/storage/)e [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hanno endpoint REST che restituiscono o utilizzano JSON.


La famiglia di prodotti SQL di Azure consente di usare facilmente i dati JSON e di integrare il database con i servizi moderni e fornisce le funzioni seguenti per l'uso dei dati JSON:

![Funzioni di JSON](./media/multi-model-features/image_1.png)

Se si dispone di testo JSON, è possibile estrarre dati da JSON o verificare che JSON sia formattato correttamente usando le funzioni predefinite [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) e [ISJSON](/sql/t-sql/functions/isjson-transact-sql). La funzione [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) consente di aggiornare il valore all'interno del testo JSON. Per query e analisi più avanzate, la funzione [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) può trasformare un array di oggetti JSON in un set di righe. È possibile eseguire qualsiasi query SQL sul set di risultati restituito. Infine, è disponibile una clausola [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) che consente di formattare i dati archiviati nelle tabelle relazionali come testo JSON.

Per altre informazioni, vedere [come usare i dati JSON](database/json-features.md).
[JSON](/sql/relational-databases/json/json-data-sql-server) è una funzionalità di base del motore di database SQL Server.

### <a name="when-to-use-a-json-capability"></a>Quando usare una funzionalità JSON

In alcuni scenari specifici è possibile usare i modelli di documento invece dei modelli relazionali:

- L'elevata normalizzazione dello schema non conferisce vantaggi significativi poiché si accede contemporaneamente a tutti i campi di oggetti oppure non si aggiornano mai le parti normalizzate degli oggetti. Tuttavia, il modello normalizzato aumenta la complessità delle query a causa del numero elevato di tabelle che è necessario unire per ottenere i dati.
- Si sta operando con le applicazioni che usano documenti JSON come modelli di dati o di comunicazione in modo nativo e non si desiderano introdurre livelli aggiuntivi che vadano a trasformare i dati relazionali in JSON e viceversa.
- È necessario semplificare il modello di dati denormalizzando le tabelle figlio o i modelli Entità-Oggetto-Valore.
- È necessario caricare o esportare i dati archiviati in formato JSON senza uno strumento aggiuntivo che analizzi i dati.

## <a name="spatial-features"></a>Funzionalità spaziali

I dati spaziali rappresentano informazioni sulla posizione fisica e sulla forma degli oggetti geometrici. Questi oggetti possono essere percorsi di punti o più oggetti complessi, ad esempio paesi/aree geografiche, strade o laghi.

 I due tipi di dati spaziali supportati sono i seguenti: 

- Il tipo geometria rappresenta i dati in un sistema di coordinate Euclideo (piano).
- Il tipo geografia rappresenta i dati in un sistema di coordinate terrestri.

Nella famiglia di prodotti SQL di Azure è possibile usare diversi oggetti spaziali che consentono di analizzare ed eseguire query sui dati rappresentati in formato JavaScript Object Notation [(JSON)](https://www.json.org/) ed esportare i dati relazionali come testo JSON.
ad esempio [Point](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring), [Polygon](/sql/relational-databases/spatial/polygon)e così via.

La famiglia di prodotti SQL di Azure fornisce anche [indici spaziali](/sql/relational-databases/spatial/spatial-indexes-overview) specializzati che possono essere usati per migliorare le prestazioni delle query spaziali.

Il [supporto spaziale](/sql/relational-databases/spatial/spatial-data-sql-server) è una funzionalità di base del motore di database SQL Server.

## <a name="xml-features"></a>Funzionalità XML

Il motore di database di SQL Server offre una potente piattaforma per lo sviluppo di applicazioni complete per la gestione di dati semistrutturati. Il supporto per XML è integrato in tutti i componenti del motore di database e include quanto segue:

- Il tipo di dati xml. È possibile archiviare in modo nativo i valori XML in una colonna di tipo di dati xml che può essere tipizzata in base a una raccolta di schemi XML oppure lasciata non tipizzata. È possibile indicizzare la colonna XML.
- La possibilità di specificare una query XQuery sui dati XML archiviati in colonne e variabili di tipo xml. È possibile usare le funzionalità di XQuery in qualsiasi query Transact-SQL che acceda a qualsiasi modello di dati usato nel database.
- Indicizzare automaticamente tutti gli elementi in documenti XML usando [indice XML primario](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) oppure specificare i percorsi esatti da indicizzare usando [indice XML secondario](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET che consente il caricamento bulk di dati XML.
- Trasformare i dati relazionali in formato XML.

[XML](/sql/relational-databases/xml/xml-data-sql-server) è una funzionalità di base del motore di database SQL Server.

### <a name="when-to-use-an-xml-capability"></a>Quando usare una funzionalità XML

In alcuni scenari specifici è possibile usare i modelli di documento invece dei modelli relazionali:

- L'elevata normalizzazione dello schema non conferisce vantaggi significativi poiché si accede contemporaneamente a tutti i campi di oggetti oppure non si aggiornano mai le parti normalizzate degli oggetti. Tuttavia, il modello normalizzato aumenta la complessità delle query a causa del numero elevato di tabelle che è necessario unire per ottenere i dati.
- Si sta operando con le applicazioni che usano documenti XML come modelli di dati o di comunicazione in modo nativo e non si desiderano introdurre livelli aggiuntivi che vadano a trasformare i dati relazionali in XML e viceversa.
- È necessario semplificare il modello di dati denormalizzando le tabelle figlio o i modelli Entità-Oggetto-Valore.
- È necessario caricare o esportare i dati archiviati in formato XML senza uno strumento aggiuntivo che analizzi i dati.

## <a name="key-value-pairs"></a>Coppie chiave-valore

La famiglia di prodotti SQL di Azure non dispone di strutture o tipi specializzati che supportano le coppie chiave-valore poiché le strutture chiave-valore possono essere rappresentate in modo nativo come tabelle relazionali standard:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

È possibile personalizzare questa struttura chiave-valore per soddisfare le esigenze senza alcun vincolo. Ad esempio, il valore può essere documento XML anziché tipo `nvarchar(max)`, se il valore è documento JSON, è possibile inserire il vincolo `CHECK` che verifica la validità del contenuto JSON. È possibile inserire un numero qualsiasi di valori correlati a una chiave nelle colonne aggiuntive, aggiungere colonne calcolate e indici per semplificare e ottimizzare l'accesso ai dati, definire la tabella come tabella solo schema ottimizzata per la memoria per ottenere prestazioni migliori e così via.

Vedere [come BWin sta usando OLTP in memoria per ottenere prestazioni e ridimensionamento senza precedenti](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) per la soluzione di memorizzazione nella cache ASP.NET che ha raggiunto 1.200.000 batch al secondo, per un esempio di come il modello relazionale può essere usato in modo efficace nella pratica come soluzione per coppia chiave-valore.

## <a name="next-steps"></a>Passaggi successivi

Le funzionalità multimodello della famiglia di prodotti SQL di Azure rappresentano anche le funzionalità di SQL Server base del motore di database condivise tra la famiglia di prodotti SQL di Azure. Per informazioni dettagliate su queste funzionalità, visitare le pagine di documentazione sul database relazionale SQL:

- [Elaborazione del grafico](/sql/relational-databases/graphs/sql-graph-overview)
- [Dati JSON](/sql/relational-databases/json/json-data-sql-server)
- [Supporto spaziale](/sql/relational-databases/spatial/spatial-data-sql-server)
- [Dati XML](/sql/relational-databases/xml/xml-data-sql-server)
