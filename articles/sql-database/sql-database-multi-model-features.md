---
title: Funzionalità multimodello del database SQL di Azure | Microsoft Docs
description: Il database SQL di Azure consente di usare più modelli di dati nello stesso database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 84efdb0297a2dc69497baee5fb746fb51d02b1b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939525"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Funzionalità multimodello del database SQL di Azure

I database multimodello consentono di archiviare e usare i dati rappresentati in più formati di dati, come ad esempio dati relazionali, grafi, documenti JSON/XML, coppie chiave-valore e così via.

## <a name="when-to-use-multi-model-capabilities"></a>Quando usare le funzionalità di multi-modello

Il database SQL di Azure è progettato per funzionare con il modello relazionale che offre le prestazioni migliori nella maggior parte dei casi per un'ampia gamma di applicazioni per utilizzo generico. Tuttavia, il database SQL di Azure non è limitato ai soli dati relazionali. Il database SQL di Azure consente di usare un'ampia gamma di formati non relazionali strettamente integrati nel modello relazionale.
È opportuno considerare con funzionalità multi-modello di Database SQL di Azure nei casi seguenti:
- Si dispone di alcune informazioni o le strutture che meglio si adattano per i modelli di NoSQL e si non vuole usare database NoSQL separato.
- La maggior parte dei dati è ideale per il modello relazionale e necessità di modellare alcune parti dei dati in stile NoSQL.
- Si desidera sfruttare avanzato linguaggio Transact-SQL per eseguire query e analizzare sia relazionali e dati NoSQL e integrarla con un'ampia gamma di strumenti e applicazioni che possono usare il linguaggio SQL.
- Si desidera applicare le funzionalità di database, ad esempio [tecnologie in memoria](sql-database-in-memory.md) per migliorare le prestazioni dell'analisi o l'elaborazione di strucutres i dati NoSQL, usare [la replica transazionale](sql-database-managed-instance-transactional-replication.md) o[repliche leggibili](sql-database-read-scale-out.md) creare copie dei dati in altra posizione e l'offload di alcuni carichi di lavoro analitici dal database primario.

## <a name="overview"></a>Panoramica

SQL di Azure offre le seguenti funzionalità multimodello:
- Le [funzionalità del grafo](#graph-features) consentono di rappresentare i dati come set di nodi e archi e di usare query Transact-SQL standard migliorate con operatore del grafo `MATCH` per eseguire query sui dati del grafico.
- Le [funzionalità JSON](#json-features) consentono di inserire documenti JSON in tabelle, trasformare i dati relazionali in documenti JSON e viceversa. È possibile usare il linguaggio Transact-SQL standard migliorato con le funzioni JSON per l'analisi dei documenti e usare indici non cluster, indici columnstore o tabelle ottimizzate per la memoria per ottimizzare le query.
- [Funzionalità spaziali](#spatial-features) consente di archiviare i dati geometrici e geografici, indicizzarli utilizzando gli indici spaziali e recuperare i dati usando le query spaziali.
- Le [funzionalità XML](#xml-features) consentono di archiviare e indicizzare i dati XML nel database e usare operazioni XQuery o XPath native per usare i dati XML. Il database SQL di Azure è dotato di un motore di query XML incorporato che elabora i dati XML.
- Le [coppie chiave-valore](#key-value-pairs) non sono esplicitamente supportate come funzionalità speciali poiché le coppie chiave-valore possono essere modellate in modo nativo come tabelle di due colonne.

  > [!Note]
  > È possibile usare espressioni di percorso JSON, espressioni XQuery o XPath, funzioni spaziali ed espressioni query-grafo nella stessa query Transact-SQL per accedere ai dati archiviati nel database. Inoltre, qualsiasi strumento o linguaggio di programmazione in grado di eseguire query Transact-SQL può usare quell'interfaccia di query per accedere ai dati multimodello. Questa è la differenza principale rispetto ai database multimodello, come ad esempio [Azure Cosmos DB](/azure/cosmos-db/) che fornisce API specializzate per diversi modelli di dati.

Nelle sezioni seguenti, è possibile trovare altre informazioni sulle funzionalità multimodello più importanti del database SQL di Azure.

## <a name="graph-features"></a>Funzionalità del grafo

Il database SQL di Azure offre funzionalità di database a grafo per modellare le relazioni molti a molti nel database. Un grafo è una raccolta di nodi (o vertici) e archi (o relazioni). Un nodo rappresenta un'entità (ad esempio, una persona o un'organizzazione) e un arco rappresenta una relazione tra i due nodi che collega (ad esempio, mi piace o amici). Ecco alcune funzionalità che rendono univoco un database a grafo:
- Gli archi o relazioni sono entità di prima classe in un Database a grafo e possono avere attributi o proprietà associate ad essi.
- Un singolo arco può collegare in modo flessibile più nodi in un Database a grafo.
- È possibile esprimere facilmente criteri di ricerca e query di navigazione a più hop.
- È possibile esprimere facilmente chiusura transitiva e query polimorfiche.

Le relazioni del grafo e le funzionalità di query del grafo sono integrate in Transact-SQL e usufruiscono dei vantaggi dell'uso di SQL Server come sistema fondamentale di gestione dei database.
[Elaborazione del grafico](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) è la funzionalità principale del motore di database di SQL Server, pertanto è possibile trovare altre informazioni su Elaborazione del grafico in tale sede.

### <a name="when-to-use-a-graph-capability"></a>Quando usare una funzionalità del grafo

Non esiste nulla che possa essere ottenuto da un database a grafo, che non possa essere ottenuto usando un database relazionale. Tuttavia, un database a grafo può rendere più semplice esprimere determinate query. La decisione di scegliere uno di essi può essere basata sui fattori seguenti:

- Dati gerarchici modello in cui un solo nodo può avere più elementi padre, per cui non è possibile usare HierarchyId
- Il modello ha un'applicazione che dispone di relazioni molti a molti complesse; con l'evoluzione dell'applicazione, vengono aggiunte nuove relazioni.
- È necessario analizzare relazioni e dati interconnessi.

## <a name="json-features"></a>Funzionalità JSON

Il database SQL di Azure consente di analizzare ed eseguire query sui dati rappresentati in formato JavaScript Object Notation [(JSON)](https://www.json.org/) e di esportare i dati relazionali come testo JSON.

JSON è un formato di dati diffuso usato per scambiare dati nelle moderne applicazioni Web e per dispositivi mobili. JSON è usato anche per archiviare dati semistrutturati nei file di log o nei database NoSQL come [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Molti servizi Web REST restituiscono risultati formattati come testo JSON o accettano dati formattati come JSON. La maggior parte dei servizi di Azure come [Ricerca di Azure](https://azure.microsoft.com/services/search/), [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) e [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) presentano endpoint REST che restituiscono o usano JSON.

Il database SQL di Azure consente di lavorare facilmente con dati JSON e integrare il database con i servizi moderni. Il database SQL di Azure fornisce le funzioni seguenti per lavorare con i dati JSON:

![Funzioni di JSON](./media/sql-database-json-features/image_1.png)

Se si dispone di testo JSON, è possibile estrarre dati da JSON o verificare che JSON sia formattato correttamente usando le funzioni predefinite [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). La funzione [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) consente di aggiornare il valore all'interno del testo JSON. Per query e analisi più avanzate, la funzione [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) può trasformare un array di oggetti JSON in un set di righe. È possibile eseguire qualsiasi query SQL sul set di risultati restituito. Infine, è disponibile una clausola [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) che consente di formattare i dati archiviati nelle tabelle relazionali come testo JSON.

Per altre informazioni, vedere l'articolo su come [usare i dati JSON nel database SQL di Azure](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) è una funzionalità principale del motore di database di SQL Server, pertanto è possibile trovare altre informazioni sulla funzionalità JSON in tale sede.

### <a name="when-to-use-a-json-capability"></a>Quando usare una funzionalità JSON

In alcuni scenari specifici è possibile usare i modelli di documento invece dei modelli relazionali:
- L'elevata normalizzazione dello schema non conferisce vantaggi significativi poiché si accede contemporaneamente a tutti i campi di oggetti oppure non si aggiornano mai le parti normalizzate degli oggetti. Tuttavia, il modello normalizzato aumenta la complessità delle query a causa del numero elevato di tabelle che è necessario unire per ottenere i dati.
- Si sta operando con le applicazioni che usano documenti JSON come modelli di dati o di comunicazione in modo nativo e non si desiderano introdurre livelli aggiuntivi che vadano a trasformare i dati relazionali in JSON e viceversa.
- È necessario semplificare il modello di dati denormalizzando le tabelle figlio o i modelli Entità-Oggetto-Valore.
- È necessario caricare o esportare i dati archiviati in formato JSON senza uno strumento aggiuntivo che analizzi i dati.

## <a name="spatial-features"></a>Funzionalità spaziali

I dati spaziali rappresentano informazioni sulla posizione fisica e sulla forma degli oggetti geometrici. Questi oggetti possono essere posizioni dei punti oppure oggetti più complessi, ad esempio paesi, strade o laghi.

Il database SQL di Azure supporta due tipi di dati spaziali: il tipo di dati geometria e il tipo di dati geografia.
- Il tipo geometria rappresenta i dati in un sistema di coordinate Euclideo (piano).
- Il tipo geografia rappresenta i dati in un sistema di coordinate terrestri.

Vi sono diversi oggetti spaziali che possono essere usati nel database SQL di Azure, come ad esempio [punto](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [poligono](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)e così via.

Il database SQL di Azure fornisce inoltre [indici spaziali](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) specializzati che possono essere usati per migliorare le prestazioni delle query spaziali.

[Supporto spaziale](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) è una funzionalità principale del motore di database di SQL Server, pertanto è possibile trovare altre informazioni sulla funzionalità spaziale in tale sede.

## <a name="xml-features"></a>Funzionalità XML

SQL Server offre una potente piattaforma per lo sviluppo di applicazioni complete per la gestione di dati semistrutturati. Il supporto per XML è integrato in tutti i componenti in SQL Server e include quanto segue:

- Il tipo di dati xml. È possibile archiviare in modo nativo i valori XML in una colonna di tipo di dati xml che può essere tipizzata in base a una raccolta di schemi XML oppure lasciata non tipizzata. È possibile indicizzare la colonna XML.
- La possibilità di specificare una query XQuery sui dati XML archiviati in colonne e variabili di tipo xml. È possibile usare le funzionalità di XQuery in qualsiasi query Transact-SQL che acceda a qualsiasi modello di dati usato nel database.
- Indicizzare automaticamente tutti gli elementi in documenti XML usando [indice XML primario](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) oppure specificare i percorsi esatti da indicizzare usando [indice XML secondario](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET che consente il caricamento bulk di dati XML.
- Trasformare i dati relazionali in formato XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) è una funzionalità principale del motore di database di SQL Server, pertanto è possibile trovare altre informazioni sulla funzionalità XML in tale sede.

### <a name="when-to-use-an-xml-capability"></a>Quando usare una funzionalità XML

In alcuni scenari specifici è possibile usare i modelli di documento invece dei modelli relazionali:
- L'elevata normalizzazione dello schema non conferisce vantaggi significativi poiché si accede contemporaneamente a tutti i campi di oggetti oppure non si aggiornano mai le parti normalizzate degli oggetti. Tuttavia, il modello normalizzato aumenta la complessità delle query a causa del numero elevato di tabelle che è necessario unire per ottenere i dati.
- Si sta operando con le applicazioni che usano documenti XML come modelli di dati o di comunicazione in modo nativo e non si desiderano introdurre livelli aggiuntivi che vadano a trasformare i dati relazionali in XML e viceversa.
- È necessario semplificare il modello di dati denormalizzando le tabelle figlio o i modelli Entità-Oggetto-Valore.
- È necessario caricare o esportare i dati archiviati in formato XML senza uno strumento aggiuntivo che analizzi i dati.

## <a name="key-value-pairs"></a>Coppie chiave-valore

I database SQL di Azure non hanno tipi o strutture specializzati che supportano le coppie chiave-valore, poiché le strutture di chiave-valore possono essere rappresentate in modo nativo come tabelle relazionali standard:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

È possibile personalizzare questa struttura chiave-valore per soddisfare le esigenze senza alcun vincolo. Ad esempio, il valore può essere documento XML anziché tipo `nvarchar(max)`, se il valore è documento JSON, è possibile inserire il vincolo `CHECK` che verifica la validità del contenuto JSON. È possibile inserire un numero qualsiasi di valori correlati a una chiave nelle colonne aggiuntive, aggiungere colonne calcolate e indici per semplificare e ottimizzare l'accesso ai dati, definire la tabella come tabella solo schema ottimizzata per la memoria per ottenere prestazioni migliori e così via.

Vedere [come BWin sta usando OLTP in memoria per ottenere prestazioni e ridimensionamento senza precedenti](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) per la soluzione di memorizzazione nella cache ASP.NET che ha raggiunto 1.200.000 batch al secondo, per un esempio di come il modello relazionale può essere usato in modo efficace nella pratica come soluzione per coppia chiave-valore.

## <a name="next-steps"></a>Passaggi successivi
Le funzionalità multimodello nei database SQL di Azure sono anche le funzionalità principali del motore di database di SQL Server condivise tra database SQL di Azure e SQL Server. Per informazioni dettagliate su queste funzionalità, visitare le pagine di documentazione sul database relazionale SQL:

* [Elaborazione del grafico](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dati JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Supporto spaziale](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Dati XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
