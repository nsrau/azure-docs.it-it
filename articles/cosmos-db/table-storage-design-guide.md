---
title: Progettare tabelle di database Cosmos di Azure per scalabilità e prestazioniDesign Azure Cosmos DB tables for scaling and performance
description: "Guida alla progettazione dell'archiviazione tabelle di Azure: tabelle scalabili e performanti in Azure Cosmos DB e Archiviazione tabelle di AzureAzure Table storage design guide: Scalable and performant tables in Azure Cosmos DB and Azure Table storage"
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246474"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guida alla progettazione delle tabelle di archiviazione tabelle di Azure: tabelle scalabili e performantiAzure Table storage table design guide: Scalable and performant tables

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Per progettare tabelle scalabili ed efficienti, è necessario prendere in considerazione diversi fattori, inclusi i costi. Se in precedenza sono stati progettati schemi per i database relazionali, queste considerazioni risulteranno familiari. Sebbene esistano alcune analogie tra Archiviazione tabelle di Azure e i modelli relazionali, esistono tuttavia anche molte differenze importanti. Queste differenze danno in genere origine a progettazioni diverse che potrebbero sembrare poco plausibili o errate a chi ha familiarità con i database relazionali, ma che invece hanno senso se la progettazione è finalizzata a un archivio di chiavi/valori NoSQL, come l'Archiviazione tabelle.

L'archiviazione tabelle è progettata per supportare applicazioni su scala cloud che possono contenere miliardi di entità ("righe" nella terminologia dei database relazionali) di dati o per set di dati che devono supportare volumi di transazioni elevati. È quindi necessario pensare in modo diverso su come archiviare i dati e comprendere il funzionamento dell'archiviazione tabelle. Un archivio dati NoSQL ben progettato può consentire alla soluzione di scalare molto ulteriormente (e a un costo inferiore) rispetto a una soluzione che utilizza un database relazionale. Questa guida illustra proprio questi argomenti.  

## <a name="about-azure-table-storage"></a>Informazioni sull'archiviazione tabelle di AzureAbout Azure Table storage
In questa sezione vengono evidenziate alcune delle funzionalità principali dell'archiviazione tabelle che sono particolarmente rilevanti per la progettazione per prestazioni e scalabilità. Se non si ha familiarità con Archiviazione di Azure e Archiviazione tabelle, vedere [Introduzione ad Archiviazione](../storage/common/storage-introduction.md) di Microsoft Azure e Introduzione all'archiviazione tabelle di [Azure tramite .NET](table-storage-how-to-use-dotnet.md) prima di leggere il resto di questo articolo. Anche se l'obiettivo di questa guida è l'archiviazione tabelle, include alcune discussioni sull'archiviazione delle code di Azure e sull'archiviazione BLOB di Azure e su come usarle insieme all'archiviazione tabelle in una soluzione.  

Archiviazione tabelle Usa un formato tabulare per archiviare i dati. In base alla terminologia standard, ogni riga della tabella rappresenta un'entità le cui diverse proprietà sono archiviate nelle colonne. Ogni entità ha una coppia di chiavi per identificarla in modo univoco e una colonna timestamp usata dall'archiviazione tabelle per tenere traccia dell'ultimo aggiornamento dell'entità. Il campo timestamp viene aggiunto automaticamente e non è possibile sovrascrivere manualmente il timestamp con un valore arbitrario. L'archiviazione tabelle usa questo timestamp dell'ultima modifica (LMT) per gestire la concorrenza ottimistica.  

> [!NOTE]
> Anche le operazioni dell'API REST di archiviazione tabelle restituiscono un valore che deriva da LMT.Table storage REST API operations also return an `ETag` value that it derives from the LMT. In questo documento, i termini ETag e LMT vengono utilizzati in modo intercambiabile, perché fanno riferimento agli stessi dati sottostanti.  
> 
> 

L'esempio seguente mostra la progettazione di una semplice tabella in cui archiviare le entità dei dipendenti e dei reparti. Molti degli esempi illustrati più avanti in questa guida si basano su questo tipo di progettazione semplice.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Giu</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>department</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Finora, questa progettazione è simile a una tabella in un database relazionale. Le differenze principali sono le colonne obbligatorie e la possibilità di archiviare più tipi di entità nella stessa tabella. Inoltre, ognuna delle proprietà definite dall'utente, ad esempio **FirstName** o **Age**, dispone di un tipo di dati, ad esempio integer o string, come una colonna in un database relazionale. A differenza di un database relazionale, tuttavia, la natura senza schema di archiviazione di tabelle significa che una proprietà non è necessario avere lo stesso tipo di dati in ogni entità. Per archiviare tipi di dati complessi in una sola proprietà, è necessario usare un formato serializzato come JSON o XML. Per ulteriori informazioni, vedere [Informazioni sul modello](https://msdn.microsoft.com/library/azure/dd179338.aspx)di dati di archiviazione tabelle .

La vostra `PartitionKey` `RowKey` scelta di ed è fondamentale per una buona progettazione della tavola. Ogni entità archiviata in una tabella `PartitionKey` `RowKey`deve avere una combinazione univoca di e . Come per le chiavi in `PartitionKey` una `RowKey` tabella di database relazionale, i valori e vengono indicizzati per creare un indice cluster che consente una ricerca rapida. Archiviazione tabelle, tuttavia, non crea indici secondari, pertanto queste sono le uniche due proprietà indicizzate (alcuni dei modelli descritti in seguito mostrano come risolvere questa limitazione apparente).  

Una tabella è costituita da una o più partizioni e molte delle `PartitionKey` decisioni `RowKey` di progettazione prese riguardano la scelta di una soluzione adatta e ottimizzata per la soluzione. Una soluzione può essere costituita da una sola tabella che contiene tutte le entità organizzate in partizioni, ma in genere una soluzione include più tabelle. Le tabelle consentono di organizzare logicamente le entità e di gestire l'accesso ai dati tramite elenchi di controllo di accesso. È possibile eliminare un'intera tabella usando una singola operazione di archiviazione.  

### <a name="table-partitions"></a>Partizioni della tabella
Il nome dell'account, `PartitionKey` il nome della tabella e insieme identificano la partizione all'interno del servizio di archiviazione in cui archiviazione tabelle archivia l'entità. Oltre a far parte dello schema di indirizzamento per le entità, le partizioni definiscono un ambito per le transazioni (vedere la sezione più avanti in questo articolo, [Transazioni del gruppo](#entity-group-transactions)di entità ) e costituiscono la base della scalabilità dell'archiviazione tabelle. Per ulteriori informazioni sulle partizioni di tabella, vedere Elenco di controllo [delle prestazioni e della scalabilità per l'archiviazione tabelle](../storage/tables/storage-performance-checklist.md).  

In Archiviazione tabelle, un singolo nodo esegue il servizio di una o più partizioni complete e il servizio viene ridimensionato bilanciando dinamicamente il carico delle partizioni tra i nodi. Se un nodo è sotto carico, Archiviazione tabelle può suddividere l'intervallo di partizioni servite da tale nodo in nodi diversi. Quando il traffico diminuisce, l'archiviazione tabelle può unire gli intervalli di partizione dai nodi non attivi in un singolo nodo.  

Per altre informazioni sui dettagli interni dell'archiviazione tabelle e in particolare sulla modalità di gestione delle partizioni, vedere Archiviazione di [Microsoft Azure: un servizio di archiviazione cloud a disponibilità elevata con coerenza elevata.](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

### <a name="entity-group-transactions"></a>Transazioni del gruppo di entità
Nell'archiviazione tabelle, le transazioni dei gruppi di entità (EGO) sono l'unico meccanismo incorporato per l'esecuzione di aggiornamenti atomici tra più entità. Gli ET sono anche detti *transazioni batch*. Gli EGM possono operare solo su entità archiviate nella stessa partizione (condividendo la stessa chiave di partizione in una determinata tabella), pertanto ogni volta che è necessario un comportamento transazionale atomico tra più entità, assicurarsi che tali entità si trovino nella stessa partizione. Questo è spesso un motivo per mantenere più tipi di entità nella stessa tabella (e partizione) e non usare più tabelle per tipi di entità diversi. Una sola EGT può agire al massimo su 100 entità.  Se si inviano più ET simultanei per l'elaborazione, è importante assicurarsi che tali EGO non operino su entità comuni tra gli ET. In caso contrario, si rischia di ritardare l'elaborazione.

Le transazioni EGT richiedono anche la valutazione di un potenziale compromesso nella progettazione. L'uso di più partizioni aumenta la scalabilità dell'applicazione, perché Azure offre maggiori opportunità per il bilanciamento del carico delle richieste tra i nodi. Tuttavia, ciò potrebbe limitare la capacità dell'applicazione di eseguire transazioni atomiche e mantenere una coerenza elevata per i dati. Ci sono inoltre specifici obiettivi di scalabilità a livello di partizione, che potrebbero limitare la velocità effettiva delle transazioni prevista per un singolo nodo.

Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione di Azure, vedere [Obiettivi di scalabilità per gli account di archiviazione standard.](../storage/common/scalability-targets-standard-account.md) Per ulteriori informazioni sugli obiettivi di scalabilità per l'archiviazione tabelle, vedere [Obiettivi di scalabilità e prestazioni per l'archiviazione tabelle](../storage/tables/scalability-targets.md). Le sezioni successive di questa guida illustrano diverse strategie di progettazione che aiutano a gestire compromessi come questo e illustrano il modo migliore per scegliere la chiave di partizione in base ai requisiti specifici dell'applicazione client.  

### <a name="capacity-considerations"></a>Considerazioni sulla capacità
La tabella seguente include alcuni dei valori chiave da tenere presenti quando si progetta una soluzione di archiviazione tabelle:The following table includes some of the key values to be aware of when you're designing a Table storage solution:  

| Capacità totale di un account di archiviazione di Azure | 500 TB |
| --- | --- |
| Numero di tabelle in un account di archiviazione di Azure |Limitato solo dalla capacità dell'account di archiviazione. |
| Numero di partizioni in una tabella |Limitato solo dalla capacità dell'account di archiviazione. |
| Numero di entità in una partizione |Limitato solo dalla capacità dell'account di archiviazione. |
| Dimensioni di una singola entità |Fino a 1 MB, con un massimo di `PartitionKey` `RowKey`255 proprietà (incluse le proprietà , e `Timestamp`). |
| Dimensione del`PartitionKey` |Una stringa di dimensioni fino a 1 KB. |
| Dimensione del`RowKey` |Una stringa di dimensioni fino a 1 KB. |
| Dimensioni di una transazione del gruppo di entità |Una transazione può includere al massimo 100 entità e il payload deve avere dimensioni inferiori a 4 MB. Una transazione EGT può aggiornare una sola entità per volta. |

Per ulteriori informazioni, vedere Informazioni sul modello di [dati del servizio tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerazioni sul costo
L'archiviazione tabelle è relativamente poco costosa, ma è consigliabile includere stime dei costi sia per l'utilizzo della capacità che per la quantità di transazioni come parte della valutazione di qualsiasi soluzione che usa l'archiviazione tabelle. In molti scenari, tuttavia, l'archiviazione di dati denormalizzati o duplicati per migliorare le prestazioni o la scalabilità della soluzione è un approccio valido da adottare. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Linee guida per la progettazione di tabelle
Questi elenchi riepilogano alcune delle linee guida chiave da tenere presenti quando si progettano le tabelle. Questa guida li affronta tutti in modo più dettagliato più avanti. Queste linee guida sono diverse dalle linee guida che in genere si seguono per la progettazione di database relazionali.  

Progettare l'archiviazione tabelle in modo che sia efficiente:Designing your Table storage to be *read* efficient:

* **Progettazione per le query nelle applicazioni con intensa attività di lettura.** Quando si progettano le tabelle, considerare le query (in particolare quelle sensibili alla latenza) che verranno eseguite prima di pensare a come aggiornare le entità. Ciò comporta in genere una soluzione efficiente e ad alte prestazioni.  
* **Specificare `PartitionKey` `RowKey` entrambi e nelle query.** *Le query punto* come queste sono le query di archiviazione tabelle più efficienti.  
* **Prendere in considerazione l'archiviazione di copie duplicate delle entità.** L'archiviazione tabelle è economica, pertanto è consigliabile archiviare la stessa entità più volte (con chiavi diverse) per consentire query più efficienti.  
* **Considerare la denormalizzazione dei dati.** L'archiviazione tabelle è economica, pertanto è consigliabile denormalizzare i dati. Ad esempio, archiviare le entità di riepilogo in modo che le query per aggregare i dati debbano accedere a una singola entità.  
* **Usare valori chiave composti.** Le uniche chiavi `PartitionKey` che `RowKey`hai sono e . Ad esempio, per abilitare percorsi alternativi per l'accesso con chiave alle entità, ad esempio, utilizzare valori chiave composti.  
* **Usare la proiezione di query.**  È possibile ridurre la quantità di dati trasferiti tramite la rete usando query che selezionano solo i campi necessari.  

Progettazione dell'archiviazione tabelle in modo che sia efficiente per la scrittura:Designing your Table storage to *be write* efficient:  

* **Non creare partizioni a caldo.**  Scegliere chiavi che consentono di distribuire le richieste tra più partizioni in qualsiasi momento.  
* **Evitare picchi di traffico.** Distribuire il traffico in un periodo di tempo ragionevole ed evitare picchi di traffico.
* **Non creare necessariamente una tabella separata per ogni tipo di entità.**  Quando è necessario eseguire transazioni atomiche tra diversi tipi di entità, è possibile archiviare questi tipi di entità nella stessa partizione della stessa tabella.
* **Considerare la velocità effettiva massima che è necessario raggiungere.** È necessario essere consapevoli degli obiettivi di scalabilità per l'archiviazione tabelle e assicurarsi che la progettazione non provochi il superamento della progettazione.  

Più avanti in questa guida, vedrai esempi che mettono in pratica tutti questi principi.  

## <a name="design-for-querying"></a>Progettazione per le query
L'archiviazione tabelle può essere un utilizzo intensivo della lettura, un utilizzo intensivo della scrittura o una combinazione dei due. In questa sezione viene considerato la progettazione per supportare le operazioni di lettura in modo efficiente. Una progettazione che supporta in modo efficiente le operazioni di lettura è in genere efficiente anche nelle operazioni di scrittura. Tuttavia, esistono considerazioni aggiuntive durante la progettazione per supportare le operazioni di scrittura. Questi vengono illustrati nella sezione successiva, [Progettazione per](#design-for-data-modification)la modifica dei dati .

Un buon punto di partenza per consentire la lettura efficiente dei dati consiste nel chiedere "Quali query dovrà eseguire l'applicazione per recuperare i dati necessari?"  

> [!NOTE]
> Con l'archiviazione tabelle, è importante ottenere il corretto design in anticipo, perché è difficile e costoso modificarlo in un secondo momento. Ad esempio, in un database relazionale, è spesso possibile risolvere i problemi di prestazioni semplicemente aggiungendo indici a un database esistente. Questa non è un'opzione con archiviazione tabelle.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>In che `PartitionKey` modo `RowKey` la scelta e l'influenza sulle prestazioni delle query
Gli esempi seguenti presuppongono che l'archiviazione tabelle memorizzi le entità `Timestamp` dipendente con la struttura seguente (la maggior parte degli esempi ometti la proprietà per maggiore chiarezza):  

| Nome colonna | Tipo di dati |
| --- | --- |
| `PartitionKey`(Nome reparto) |string |
| `RowKey`(ID dipendente) |string |
| `FirstName` |string |
| `LastName` |string |
| `Age` |Integer |
| `EmailAddress` |string |

Di seguito sono riportate alcune linee guida generali per la progettazione di query di archiviazione tabelle. La sintassi del filtro usata negli esempi seguenti è dall'API REST di archiviazione tabelle. Per ulteriori informazioni, consultate Eseguire query sulle [entità.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

* Una *query punto* è la ricerca più efficiente da utilizzare ed è consigliata per ricerche con volumi elevati o ricerche che richiedono la latenza più bassa. Tale query può utilizzare gli indici per individuare una singola `PartitionKey` entità `RowKey` in modo efficiente specificando i valori e . Ad esempio `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Il secondo migliore è una *query di intervallo*. Utilizza i `PartitionKey`filtri e e `RowKey` su un intervallo di valori per restituire più di un'entità. Il `PartitionKey` valore identifica una partizione specifica e i `RowKey` valori identificano un subset delle entità in tale partizione. Ad esempio `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Il terzo migliore è una *scansione della partizione*. Utilizza i `PartitionKey`filtri e e su un'altra proprietà non chiave e potrebbe restituire più di un'entità. Il `PartitionKey` valore identifica una partizione specifica e i valori delle proprietà selezionano per un subset delle entità in tale partizione. Ad esempio `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Un'analisi tabella* non include `PartitionKey`l'oggetto e non è efficiente perché cerca in tutte le partizioni che costituiscono la tabella tutte le entità corrispondenti. Esegue una scansione della tabella indipendentemente dal fatto `RowKey`che il filtro utilizzi o meno il file . Ad esempio `$filter=LastName eq 'Jones'`.  
* Query di archiviazione tabelle di Azure `PartitionKey` che `RowKey` restituiscono più entità ordinarle in base all'ordine. Per evitare di ricorrere alle entità `RowKey` nel client, scegliere un che definisce l'ordinamento più comune. I risultati delle query restituiti dall'API delle tabelle di Azure in Azure Cosmos DB non vengono ordinati in base alla chiave di partizione o alla chiave di riga. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

L'utilizzo**or**di un " o `RowKey` " per specificare un filtro basato sui valori comporta un'analisi della partizione e non viene considerato come una query di intervallo. Pertanto, evitare query che `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`utilizzano filtri come: .  

Per esempi di codice lato client che utilizzano la libreria client di archiviazione per eseguire query efficienti, vedere:For examples of client-side code that use the Storage Client Library to run efficient queries, see:  

* [Eseguire una query punto utilizzando la libreria client di archiviazioneRun a point query by using the Storage Client Library](#run-a-point-query-by-using-the-storage-client-library)
* [Recuperare più entità tramite LINQRetrieve multiple entities by using LINQ](#retrieve-multiple-entities-by-using-linq)
* [Proiezione lato server](#server-side-projection)  

Per esempi di codice lato client che possono gestire più tipi di entità archiviati nella stessa tabella, vedere:  

* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Scegliere un`PartitionKey`
La scelta `PartitionKey` di deve bilanciare la necessità di consentire l'uso di EGR (per garantire la coerenza) con la necessità di distribuire le entità tra più partizioni (per garantire una soluzione scalabile).  

A un estremo, è possibile archiviare tutte le entità in una singola partizione. Tuttavia, ciò potrebbe limitare la scalabilità della soluzione e impedireberebbe all'archiviazione tabelle di bilanciare il carico delle richieste. All'altro estremo, è possibile archiviare un'entità per partizione. Questo è altamente scalabile e consente l'archiviazione tabelle per bilanciare il carico delle richieste, ma impedisce di usare le transazioni del gruppo di entità.  

Un `PartitionKey` ideale consente di utilizzare query efficienti e dispone di partizioni sufficienti per garantire che la soluzione sia scalabile. In genere, si scoprirà che le entità diranno una proprietà adatta che distribuisce le entità tra partizioni sufficienti.

> [!NOTE]
> Ad esempio, in un sistema che memorizza informazioni su utenti o dipendenti, `UserID` può essere un buon file `PartitionKey`. È possibile avere diverse entità `UserID` che utilizzano una particolare chiave di partizione. Ogni entità che archivia i dati relativi a un utente viene raggruppata in una singola partizione. Queste entità sono accessibili tramite EGR, pur essendo altamente scalabili.
> 
> 

Nella scelta di `PartitionKey` queste entità sono disponibili alcune considerazioni relative alla modalità di inserimento, aggiornamento ed eliminazione delle entità. Per altre informazioni, vedere [Progettazione per la modifica dei dati](#design-for-data-modification) più avanti in questo articolo.  

### <a name="optimize-queries-for-table-storage"></a>Ottimizzare le query per l'archiviazione tabelleOptimize queries for Table storage
L'archiviazione tabelle indicizza automaticamente le `PartitionKey` `RowKey` entità usando i valori e in un singolo indice cluster. Questo è il motivo per cui le query punto sono i più efficienti da usare. Tuttavia, non sono presenti indici diversi da quello `PartitionKey` nell'indice cluster in e `RowKey`.

Molte progettazioni devono soddisfare alcuni requisiti per abilitare la ricerca di entità in base a più criteri, Ad esempio, individuare le entità dipendente in base a posta elettronica, ID dipendente o cognome. I modelli seguenti nella sezione Modelli di [progettazione tabelle](#table-design-patterns) riguardano questi tipi di requisiti. I modelli descrivono anche le modalità di utilizzo del fatto che l'archiviazione tabelle non fornisce indici secondari.  

* Modello di [indice secondario tra partizioni:](#intra-partition-secondary-index-pattern)archiviare `RowKey` più copie di ogni entità usando valori diversi (nella stessa partizione). In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`  
* [Modello di indice secondario tra](#inter-partition-secondary-index-pattern)partizioni: archiviare `RowKey` più copie di ogni entità usando valori diversi in partizioni separate o in tabelle separate. In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`  
* [Modello di entità indice:](#index-entities-pattern)gestire le entità di indice per abilitare ricerche efficienti che restituiscono elenchi di entità.  

### <a name="sort-data-in-table-storage"></a>Ordinare i dati nell'archiviazione tabelle

L'archiviazione tabelle restituisce i risultati delle `PartitionKey` query `RowKey`ordinati in ordine crescente, in base a e quindi in base a .

> [!NOTE]
> I risultati delle query restituiti dall'API delle tabelle di Azure in Azure Cosmos DB non vengono ordinati in base alla chiave di partizione o alla chiave di riga. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Le chiavi nell'archiviazione tabelle sono valori stringa. Per garantire che i valori numerici vengano ordinati correttamente, è necessario convertirli in una lunghezza fissa e riempirli con zeri. Ad esempio, se il valore ID `RowKey` dipendente utilizzato come valore intero è, è necessario convertire l'ID dipendente **123** in **00000123**. 

In molte applicazioni è necessario usare i dati ordinandoli in modo diverso, ad esempio ordinando i dipendenti per nome o per data di assunzione. I modelli seguenti nella sezione Modelli di [progettazione tabelle](#table-design-patterns) riguardano la modalità di ordinamento alternativo per le entità:  

* Modello di [indice secondario tra partizioni:](#intra-partition-secondary-index-pattern)archiviare `RowKey` più copie di ogni entità usando valori diversi (nella stessa partizione). In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`  
* [Modello di indice secondario tra](#inter-partition-secondary-index-pattern)partizioni: archiviare `RowKey` più copie di ogni entità usando valori diversi in partizioni separate in tabelle separate. In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`
* [Modello di coda del log](#log-tail-pattern): recuperare le *n* entità `RowKey` aggiunte più di recente a una partizione, utilizzando un valore che ordina in ordine di data e ora inverso.  

## <a name="design-for-data-modification"></a>Progettazione per la modifica dei dati
Questa sezione esamina le considerazioni relative alla progettazione per ottimizzare inserimenti, aggiornamenti ed eliminazioni. In alcuni casi, è necessario valutare il compromesso tra le progettazioni che ottimizzano per l'esecuzione di query su progettazioni che ottimizzano per la modifica dei dati. Questa valutazione è simile a quella che si fa nei progetti per i database relazionali (anche se le tecniche per la gestione dei compromessi di progettazione sono diverse in un database relazionale). La sezione Modelli di [progettazione Tabella](#table-design-patterns) descrive alcuni modelli di progettazione dettagliati per l'archiviazione tabelle ed evidenzia alcuni di questi compromessi. In pratica, si noterà che molte progettazioni ottimizzate per l'esecuzione di query sulle entità funzionano bene anche per la modifica delle entità.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione
Per aggiornare o eliminare un'entità, è necessario `PartitionKey` `RowKey` essere in grado di identificarla utilizzando i valori e . A questo proposito, `PartitionKey` `RowKey` la scelta di e per la modifica delle entità deve seguire criteri simili a quelli desiderati per supportare le query punto. Si desidera identificare le entità nel modo più efficiente possibile. Non si desidera utilizzare una partizione o una scansione tabelle inefficiente `PartitionKey` per `RowKey` individuare un'entità al fine di individuare i valori e necessari per aggiornarlo o eliminarlo.  

I modelli seguenti nella sezione Modelli di [progettazione tabelle](#table-design-patterns) riguardano l'ottimizzazione delle prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione:  

* Modello di eliminazione di [volumi elevati:](#high-volume-delete-pattern)abilitare l'eliminazione di un volume elevato di entità archiviando tutte le entità per l'eliminazione simultanea in una tabella separata. Eliminare le entità eliminando la tabella.  
* [Modello di serie](#data-series-pattern)di dati: archivia serie di dati complete in una singola entità per ridurre al minimo il numero di richieste che effettui.  
* [Modello di entità wide:](#wide-entities-pattern)usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  
* [Modello di entità di grandi dimensioni:](#large-entities-pattern)usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Assicurare la coerenza nelle entità archiviate
L'altro aspetto importante che influisce sulla scelta delle chiavi per ottimizzare la modifica dei dati è come assicurare la coerenza usando le transazioni atomiche. È possibile usare una transazione EGT solo per agire sulle entità archiviate nella stessa partizione.  

I modelli seguenti nella sezione Modelli di [progettazione tabelle](#table-design-patterns) riguardano la gestione della coerenza:The following patterns in the section Table design patterns address managing consistency:  

* Modello di [indice secondario tra partizioni:](#intra-partition-secondary-index-pattern)archiviare `RowKey` più copie di ogni entità usando valori diversi (nella stessa partizione). In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`  
* [Modello di indice secondario tra](#inter-partition-secondary-index-pattern)partizioni: archiviare `RowKey` più copie di ogni entità usando valori diversi in partizioni separate o in tabelle separate. In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`  
* [Modello di transazioni coerente alla fine:](#eventually-consistent-transactions-pattern)abilitare un comportamento coerente alla fine oltre i limiti delle partizioni o dei limiti del sistema di archiviazione usando le code di Azure.Eventually consistent transactions pattern : Enable eventually consistent behavior across partition boundaries or storage system boundaries by using Azure queues.
* [Modello di entità indice:](#index-entities-pattern)gestire le entità di indice per abilitare ricerche efficienti che restituiscono elenchi di entità.  
* [Modello di denormalizzazione](#denormalization-pattern): Combinare i dati correlati insieme in una singola entità, per consentire di recuperare tutti i dati necessari con una query a punto singolo.  
* [Modello di serie](#data-series-pattern)di dati: consente di archiviare serie di dati complete in una singola entità, per ridurre al minimo il numero di richieste.  

Per altre informazioni, vedere [Transazioni del gruppo](#entity-group-transactions) di entità più avanti in questo articolo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Verificare che la capacità della progettazione per modifiche efficienti consenta query efficienti
In molti casi, una progettazione per query efficienti consente modifiche efficienti, ma è consigliabile valutare sempre se questa condizione si applica a uno specifico scenario. Alcuni dei modelli nella sezione Modelli di progettazione delle tabelle [valutano](#table-design-patterns) in modo esplicito i compromessi tra l'esecuzione di query e la modifica delle entità ed è sempre necessario prendere in considerazione il numero di ogni tipo di operazione.  

I modelli seguenti nella sezione Modelli di [progettazione tabelle](#table-design-patterns) affrontano i compromessi tra la progettazione per query efficienti e la progettazione per una modifica efficiente dei dati:The following patterns in the section Table design patterns address trade-offs between designing for efficient queries and designing for efficient data modification:  

* [Modello di chiave composta:](#compound-key-pattern)usare valori composti per consentire a un client di cercare dati correlati con una query a punto singolo.Compound key pattern : Use compound `RowKey` values to enable a client to look up related data with a single point query.  
* [Modello di coda del log](#log-tail-pattern): recuperare le *n* entità `RowKey` aggiunte più di recente a una partizione, utilizzando un valore che ordina in ordine di data e ora inverso.  

## <a name="encrypt-table-data"></a>Crittografare i dati delle tabelle
La libreria client di Archiviazione di .NET Azure supporta la crittografia delle proprietà delle entità stringa per le operazioni di inserimento e sostituzione. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono riconvertite in stringhe dopo la decrittografia.    

Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Specificare `EncryptProperty` un attributo (per le `TableEntity`entità POCO che derivano da ) oppure specificare un resolver di crittografia nelle opzioni di richiesta. Un resolver di crittografia è un delegato che accetta una chiave di partizione, una chiave di riga e un nome di proprietà e restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client utilizza queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura in rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. Ad esempio, se X, crittografare le proprietà A; altrimenti crittografare le proprietà A e B. Non è necessario fornire queste informazioni durante la lettura o l'esecuzione di query sulle entità.

L'unione non è attualmente supportata. Poiché un sottoinsieme di proprietà potrebbe essere stato crittografato in precedenza utilizzando una chiave diversa, la semplice unione delle nuove proprietà e l'aggiornamento dei metadati comporterà la perdita di dati. L'unione richiede l'esecuzione di chiamate di servizio aggiuntive per leggere l'entità preesistente dal servizio o l'utilizzo di una nuova chiave per ogni proprietà. Nessuno di questi è adatto per motivi di prestazioni.     

Per informazioni sulla crittografia dei dati delle tabelle, vedere Crittografia lato client e Archiviazione chiave di [Azure per Archiviazione di Microsoft Azure.](../storage/common/storage-client-side-encryption.md)  

## <a name="model-relationships"></a>Relazioni tra modelli
La compilazione di modelli di dominio è un passaggio chiave della progettazione di sistemi complessi. In genere, si utilizza il processo di modellazione per identificare le entità e le relazioni tra di esse, come un modo per comprendere il dominio aziendale e informare la progettazione del sistema. Questa sezione illustra come è possibile convertire alcuni dei tipi di relazione comuni presenti nei modelli di dominio in progettazioni per l'archiviazione tabelle. Il processo di mapping da un modello di dati logico a un modello di dati fisico basato su NoSQL è diverso da quello utilizzato durante la progettazione di un database relazionale. La progettazione di database relazionali presuppone in genere un processo di normalizzazione dei dati ottimizzato per ridurre al minimo la ridondanza. Tale progettazione presuppone inoltre una funzionalità di query dichiarativa che astrae l'implementazione del funzionamento del database.  

### <a name="one-to-many-relationships"></a>Relazioni uno a molti
Le relazioni uno a molti tra gli oggetti del dominio aziendale sono frequenti: ad esempio, un reparto ha più dipendenti. Esistono diversi modi per implementare relazioni uno-a-molti nell'archiviazione tabelle, ognuna con pro e contro che potrebbero essere rilevanti per lo scenario specifico.  

Si consideri l'esempio di una grande multinazionale con decine di migliaia di dipartimenti ed entità dei dipendenti. Ogni reparto ha molti dipendenti e ogni dipendente è associato a un reparto specifico. Un approccio consiste nell'archiviare entità di reparto e dipendenti separate, ad esempio:  

![Immagine che mostra un'entità reparto e un'entità dipendente][1]

In questo esempio viene illustrata una relazione uno-a-molti implicita tra i tipi, in base al `PartitionKey` valore. Ogni reparto può avere più dipendenti.  

Questo esempio mostra anche un'entità reparto e le relative entità dipendente nella stessa partizione. È possibile scegliere di usare partizioni, tabelle o anche account di archiviazione diversi per i diversi tipi di entità.  

Un approccio alternativo consiste nel denormalizzare i dati e archiviare solo le entità dipendente con i dati del reparto denormalizzati, come illustrato nell'esempio seguente. In questo particolare scenario, questo approccio denormalizzato potrebbe non essere il migliore se si dispone di un requisito per essere in grado di modificare i dettagli di un responsabile di reparto. A tale scopo, è necessario aggiornare ogni dipendente del reparto.  

![Grafica dell'entità dipendente][2]

Per ulteriori informazioni, vedere il [Modello di denormalizzazione](#denormalization-pattern) più avanti in questa guida.  

Nella tabella seguente vengono riepilogati i vantaggi e gli svantaggi di ognuno degli approcci per l'archiviazione delle entità dipendente e reparto con una relazione uno-a-molti. È inoltre necessario considerare la frequenza con cui si prevede di eseguire varie operazioni. Potrebbe essere accettabile avere un progetto che include un'operazione costosa se tale operazione viene eseguita solo raramente.  

<table>
<tr>
<th>Approccio</th>
<th>Vantaggi</th>
<th>Svantaggi</th>
</tr>
<tr>
<td>Tipi di entità distinti, stessa partizione, stessa tabella</td>
<td>
<ul>
<li>È possibile aggiornare un'entità reparto con un'unica operazione.</li>
<li>È possibile usare una transazione EGT per mantenere la coerenza, se esiste un requisito che impone di modificare un'entità reparto quando si aggiorna/inserisce/elimina un'entità dipendente, ad esempio se si mantiene un conteggio dei dipendenti per ogni reparto.</li>
</ul>
</td>
<td>
<ul>
<li>Potrebbe essere necessario recuperare sia un dipendente che un'entità reparto per alcune attività client.</li>
<li>Le operazioni di archiviazione vengono eseguite nella stessa partizione. A volumi elevati di transazioni, ciò può comportare un hotspot.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto utilizzando un EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipi di entità distinti, partizioni, tabelle o account di archiviazione diversi</td>
<td>
<ul>
<li>È possibile aggiornare un'entità reparto o un'entità dipendente con un'unica operazione.</li>
<li>A volumi elevati di transazioni, ciò può contribuire a distribuire il carico tra più partizioni.</li>
</ul>
</td>
<td>
<ul>
<li>Potrebbe essere necessario recuperare sia un dipendente che un'entità reparto per alcune attività client.</li>
<li>Non è possibile utilizzare EGT per mantenere la coerenza quando si aggiorna/inserisce/elimina un dipendente e si aggiorna un reparto. ad esempio quando si aggiorna un conteggio dipendenti in un'entità reparto.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto utilizzando un EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizzazione in un solo tipo di entità</td>
<td>
<ul>
<li>È possibile recuperare tutte le informazioni necessarie con una sola richiesta.</li>
</ul>
</td>
<td>
<ul>
<li>Può essere costoso mantenere la coerenza se è necessario aggiornare le informazioni sul reparto (questo richiederebbe l'aggiornamento di tutti i dipendenti in un reparto).</li>
</ul>
</td>
</tr>
</table>

La scelta tra queste opzioni e quali dei pro e dei contro sono più significativi dipende dagli scenari di applicazione specifici. Ad esempio, con quale frequenza si modificano le entità di reparto? Tutte le query dei dipendenti necessitano di informazioni di reparto aggiuntive? Quanto è vicino ai limiti di scalabilità per le partizioni o l'account di archiviazione?  

### <a name="one-to-one-relationships"></a>Relazioni uno a uno
I modelli di dominio possono includere relazioni uno-a-uno tra entità. Se è necessario implementare una relazione uno-a-uno nell'archiviazione tabelle, è inoltre necessario scegliere come collegare le due entità correlate quando è necessario recuperarle entrambe. Questo collegamento può essere implicito, in base a una convenzione nei valori `PartitionKey` chiave, o esplicito, archiviando un collegamento sotto forma di e `RowKey` valori in ogni entità all'entità correlata. Per informazioni utili a stabilire se archiviare le entità correlate nella stessa partizione, vedere la sezione [Relazioni uno a molti](#one-to-many-relationships).  

Esistono anche considerazioni sull'implementazione che potrebbero portare a implementare relazioni uno-a-uno nell'archiviazione tabelle:There are also implementation considerations that might lead you to implement one-to-one relationships in Table storage:  

* Gestione di entità di grandi dimensioni (per ulteriori informazioni, vedere [Modello di entità di grandi dimensioni).](#large-entities-pattern)  
* Implementazione dei controlli di accesso (per ulteriori informazioni, vedere [Controllare l'accesso con le firme](#control-access-with-shared-access-signatures)di accesso condiviso).  

### <a name="join-in-the-client"></a>Join nel client
Sebbene esistano modi per modellare le relazioni nell'archiviazione tabelle, non dimenticare che i due motivi principali per l'utilizzo dell'archiviazione tabelle sono scalabilità e prestazioni. Se si stanno modellando molte relazioni che compromettono le prestazioni e la scalabilità della soluzione, è necessario chiedersi se è necessario compilare tutte le relazioni di dati nella progettazione della tabella. Potrebbe essere possibile semplificare la progettazione e migliorare la scalabilità e le prestazioni della soluzione, se si consente all'applicazione client di eseguire i join necessari.  

Ad esempio, se si dispone di tabelle di piccole dimensioni che contengono dati che non cambiano spesso, è possibile recuperare questi dati una sola volta e memorizzarli nella cache nel client. Questo consente di evitare round trip ripetuti per il recupero degli stessi dati. Negli esempi esaminati in questa guida, è probabile che il set di reparti di una piccola organizzazione sia piccolo e cambi raramente. Questo lo rende un buon candidato per i dati che un'applicazione client può scaricare una volta e memorizzare nella cache come dati di ricerca.  

### <a name="inheritance-relationships"></a>Relazioni di ereditarietà
Se l'applicazione client usa un set di classi che fanno parte di una relazione di ereditarietà per rappresentare le entità aziendali, è possibile rendere facilmente persistenti tali entità nell'archivio tabelle. Ad esempio, è possibile avere il seguente set di `Person` classi definito nell'applicazione client, dove è una classe astratta.

![Diagramma delle relazioni di ereditarietà][3]

È possibile rendere persistenti le istanze delle `Person` due classi concrete nell'archiviazione tabelle usando una singola tabella. Usare entità simili alle seguenti:  

![Immagine che mostra l'entità cliente e l'entità dipendente][4]

Per altre informazioni sull'uso di più tipi di entità nella stessa tabella nel codice client, vedere Usare i tipi di [entità eterogenei](#work-with-heterogeneous-entity-types) più avanti in questa guida. In questa sezione sono disponibili esempi su come riconoscere il tipo di entità nel codice del client.  

## <a name="table-design-patterns"></a>Modelli di progettazione tabella
Nelle sezioni precedenti è stato illustrato come ottimizzare la progettazione della tabella sia per il recupero dei dati di entità tramite query che per l'inserimento, l'aggiornamento e l'eliminazione dei dati dell'entità. In questa sezione vengono descritti alcuni modelli appropriati per l'utilizzo con l'archiviazione tabelle. Inoltre, vedrai come puoi praticamente risolvere alcuni dei problemi e dei compromessi sollevati in precedenza in questa guida. Il diagramma seguente riepiloga le relazioni tra i diversi modelli:  

![Diagramma dei modelli di progettazione delle tabelle][5]

La mappa dei modelli evidenzia alcune relazioni tra i motivi (blu) e gli anti-modelli (arancione) documentati in questa guida. Ovviamente esistono molti altri modelli utili. Ad esempio, uno degli scenari chiave per l'archiviazione tabelle consiste nell'utilizzare il modello di [visualizzazione materializzato](https://msdn.microsoft.com/library/azure/dn589782.aspx) dal modello di separazione della responsabilità delle query di [comando.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archiviare più copie di ogni `RowKey` entità usando valori diversi (nella stessa partizione). In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey` Gli aggiornamenti tra le copie possono essere mantenuti coerenti utilizzando EGET.  

#### <a name="context-and-problem"></a>Contesto e problema
L'archiviazione tabelle indicizza automaticamente `PartitionKey` le `RowKey` entità usando i valori e . Ciò consente a un'applicazione client di recuperare un'entità in modo efficiente utilizzando questi valori. Ad esempio, utilizzando la struttura della tabella seguente, un'applicazione client può utilizzare una query punto `PartitionKey` per `RowKey` recuperare una singola entità dipendente utilizzando il nome del reparto e l'ID dipendente (i valori e ). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.

![Grafica dell'entità dipendente][6]

Se si desidera trovare anche un'entità dipendente basata sul valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario utilizzare un'analisi della partizione meno efficiente per trovare una corrispondenza. Ciò è dovuto al fatto che l'archiviazione tabelle non fornisce indici secondari. Inoltre, non è possibile richiedere un elenco di dipendenti ordinati `RowKey` in un ordine diverso da quello dell'ordine.  

#### <a name="solution"></a>Soluzione
Per risolvere la mancanza di indici secondari, è possibile archiviare più copie `RowKey` di ogni entità, con ogni copia che usa un valore diverso. Se si archivia un'entità con le strutture seguenti, è possibile recuperare in modo efficiente le entità dipendente in base all'indirizzo di posta elettronica o all'ID dipendente. I valori `RowKey`del `empid_`prefisso per , e `email_` consentono di eseguire query per un singolo dipendente o per un intervallo di dipendenti utilizzando un intervallo di indirizzi di posta elettronica o ID dipendente.  

![Immagine che mostra l'entità dipendente con valori RowKey variabili][7]

I due criteri di filtro seguenti (uno cercato in base all'ID dipendente e uno in base all'indirizzo di posta elettronica) specificano entrambe le query punto:The following two filter criteria (one looking up by employee ID, and one looking up by email address), both specify point queries:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223)  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se si esegue una query per un intervallo di entità dipendente, è possibile specificare un intervallo ordinato in base all'ID dipendente o un intervallo ordinato in base all'indirizzo di posta elettronica. Eseguire una query per le `RowKey`entità con il prefisso appropriato nel file .  

* Per trovare tutti i dipendenti del reparto Vendite con un ID dipendente compreso tra 000100 e 000199, utilizzare: $filter empid_000199 empid_000100 .  
* Per trovare tutti i dipendenti del reparto Vendite con un indirizzo di posta elettronica che inizia con la lettera "a", utilizzare: $filter , (PartitionKey eq 'Sales') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
La sintassi del filtro usata negli esempi precedenti è dall'API REST di archiviazione tabelle. Per ulteriori informazioni, consultate Eseguire query sulle [entità.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* L'archiviazione tabelle è relativamente economica da usare, pertanto l'overhead di costo dovuto all'archiviazione di dati duplicati non dovrebbe essere un problema importante. Tuttavia, è consigliabile valutare sempre il costo della progettazione in base ai requisiti di archiviazione previsti e aggiungere solo entità duplicate per supportare le query che verranno eseguite dall'applicazione client.  
* Poiché le entità dell'indice secondario vengono archiviate nella stessa partizione delle entità originali, assicurarsi di non superare gli obiettivi di scalabilità per una singola partizione.  
* Per mantenere la coerenza tra entità duplicate è possibile usare transazioni ETG, che consentono di aggiornare le due copie dell'entità in modo atomico. A questo scopo è necessario archiviare tutte le copie di un'entità nella stessa partizione. Per ulteriori informazioni, vedere [Utilizzare le transazioni](#entity-group-transactions)del gruppo di entità .  
* Il valore utilizzato `RowKey` per l'oggetto deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* La spaziatura `RowKey` interna dei valori numerici in (ad esempio, l'ID dipendente 000223) consente l'ordinamento e il filtro corretti in base ai limiti superiore e inferiore.  
* Non è necessario duplicare tutte le proprietà dell'entità. Ad esempio, se le query che cercano le entità `RowKey` utilizzando l'indirizzo di posta elettronica nell'età del dipendente non è mai necessario, queste entità possono avere la struttura seguente:For example, if the queries that look up the entities by using the email address in the never need the employee's age, these entities can have the following structure:

  ![Grafica dell'entità dipendente][8]

* In genere, è preferibile archiviare i dati duplicati e assicurarsi di poter recuperare tutti i dati necessari con una singola query, anziché utilizzare una query per individuare un'entità e un'altra per cercare i dati necessari.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando:

- L'applicazione client deve recuperare le entità usando diverse chiavi.
- Il client deve recuperare le entità in ordinamenti diversi.
- È possibile identificare ogni entità utilizzando una varietà di valori univoci.

Tuttavia, assicurarsi di non superare i limiti di scalabilità della partizione `RowKey` quando si eseguono ricerche di entità utilizzando i valori diversi.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per indice secondario interpartizione](#inter-partition-secondary-index-pattern)
* [Modello per chiave composta](#compound-key-pattern)
* [Transazioni del gruppo di entità](#entity-group-transactions)
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archiviare più copie di ogni `RowKey` entità usando valori diversi in partizioni separate o in tabelle separate. In questo modo le ricerche veloci ed efficienti e gli ordinamenti alternativi utilizzando valori diversi. `RowKey`  

#### <a name="context-and-problem"></a>Contesto e problema
L'archiviazione tabelle indicizza automaticamente `PartitionKey` le `RowKey` entità usando i valori e . Ciò consente a un'applicazione client di recuperare un'entità in modo efficiente utilizzando questi valori. Ad esempio, utilizzando la struttura della tabella seguente, un'applicazione client può utilizzare una query punto `PartitionKey` per `RowKey` recuperare una singola entità dipendente utilizzando il nome del reparto e l'ID dipendente (i valori e ). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.  

![Grafica dell'entità dipendente][9]

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Ciò è dovuto al fatto che l'archiviazione tabelle non fornisce indici secondari. Inoltre, non è possibile richiedere un elenco di dipendenti ordinati `RowKey` in un ordine diverso da quello dell'ordine.  

Si prevede un volume elevato di transazioni per queste entità e si desidera ridurre al minimo il rischio che la velocità di archiviazione della tabella limiti il client.  

#### <a name="solution"></a>Soluzione
Per risolvere la mancanza di indici secondari, è possibile archiviare più copie `PartitionKey` `RowKey` di ogni entità, con ogni copia utilizzando valori e diversi. Se si archivia un'entità con le strutture seguenti, è possibile recuperare in modo efficiente le entità dipendente in base all'indirizzo di posta elettronica o all'ID dipendente. I valori `PartitionKey`del `empid_`prefisso per , e `email_` consentono di identificare l'indice che si desidera utilizzare per una query.  

![Immagine che mostra l'entità dipendente con l'indice primario e l'entità dipendente con indice secondario][10]

I due criteri di filtro seguenti (uno cercato in base all'ID dipendente e uno in base all'indirizzo di posta elettronica) specificano entrambe le query punto:The following two filter criteria (one looking up by employee ID, and one looking up by email address), both specify point queries:  

* $filter=(PartitionKey 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se si esegue una query per un intervallo di entità dipendente, è possibile specificare un intervallo ordinato in base all'ID dipendente o un intervallo ordinato in base all'indirizzo di posta elettronica. Eseguire una query per le `RowKey`entità con il prefisso appropriato nel file .  

* Per trovare tutti i dipendenti del reparto Vendite con ID dipendente compreso nell'intervallo da **000100** a **000199**, ordinati in base all'ID dipendente, utilizzare: $filter , (PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Per trovare tutti i dipendenti del reparto Vendite con un indirizzo di posta elettronica che inizia con "a", ordinato in base all'indirizzo di posta elettronica, utilizzare: $filter ' (PartitionKey eq 'email_Sales') e (RowKey ge 'a') e (RowKey lt 'b')  

Si noti che la sintassi del filtro utilizzata negli esempi precedenti proviene dall'API REST di archiviazione tabelle. Per ulteriori informazioni, consultate Eseguire query sulle [entità.](https://msdn.microsoft.com/library/azure/dd179421.aspx)  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra le entità duplicate, è possibile usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per gestire le entità di indice primario e secondario.  
* L'archiviazione tabelle è relativamente economica da usare, pertanto l'overhead di costo dovuto all'archiviazione dei dati duplicati non dovrebbe essere un problema principale. Tuttavia, valutare sempre il costo della progettazione in base ai requisiti di archiviazione previsti e aggiungere solo entità duplicate per supportare le query che verranno eseguite dall'applicazione client.  
* Il valore utilizzato `RowKey` per l'oggetto deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* La spaziatura `RowKey` interna dei valori numerici in (ad esempio, l'ID dipendente 000223) consente l'ordinamento e il filtro corretti in base ai limiti superiore e inferiore.  
* Non è necessario duplicare tutte le proprietà dell'entità. Ad esempio, se le query che cercano le entità `RowKey` utilizzando l'indirizzo di posta elettronica nell'età del dipendente non è mai necessario, queste entità possono avere la struttura seguente:For example, if the queries that look up the entities by using the email address in the never need the employee's age, these entities can have the following structure:
  
  ![Immagine che mostra l'entità dipendente con indice secondario][11]
* In genere, è preferibile archiviare i dati duplicati e assicurarsi di poter recuperare tutti i dati necessari con una singola query, anziché utilizzare una query per individuare un'entità utilizzando l'indice secondario e un'altra per cercare i dati necessari nell'indice primario.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando:

- L'applicazione client deve recuperare le entità usando diverse chiavi.
- Il client deve recuperare le entità in ordinamenti diversi.
- È possibile identificare ogni entità utilizzando una varietà di valori univoci.

Utilizzare questo modello quando si desidera evitare di superare i limiti di `RowKey` scalabilità della partizione quando si eseguono ricerche di entità utilizzando valori diversi.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern)  
* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni del gruppo di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Modello per transazioni con coerenza finale
abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.  

#### <a name="context-and-problem"></a>Contesto e problema
Le transazioni ETG consentono l'esecuzione di transazioni atomiche tra più entità che condividono la stessa chiave di partizione. Per motivi di prestazioni e scalabilità, è possibile decidere di archiviare le entità con requisiti di coerenza in partizioni separate o in un sistema di archiviazione separato. In uno scenario di questo tipo, non è possibile utilizzare EGR per mantenere la coerenza. Ad esempio, potrebbe essere necessario mantenere la coerenza finale tra:  

* Entità archiviate in due partizioni diverse nella stessa tabella, in tabelle diverse o in account di archiviazione diversi.  
* Entità archiviata nell'archiviazione tabelle e un BLOB archiviato nell'archiviazione BLOB.  
* Entità archiviata in Archiviazione tabelle e un file in un file system.  
* Entità archiviata nell'archiviazione tabelle, ma indicizzata tramite Ricerca cognitiva di Azure.An entity stored in Table storage, yet indexed by using Azure Cognitive Search.  

#### <a name="solution"></a>Soluzione
Usando le code di Azure, è possibile implementare una soluzione che offre coerenza finale tra due o più partizioni o sistemi di archiviazione.

Per illustrare questo approccio, si supponga di disporre di un requisito per poter archiviare le entità dipendente precedenti. Le entità dei dipendenti precedenti sono raramente interrogate e dovrebbero essere escluse da qualsiasi attività che si occupi dei dipendenti correnti. Per implementare questo requisito, archiviare i dipendenti attivi nella tabella **Corrente** ed ex dipendenti nella tabella **Archivio.** L'archiviazione di un dipendente richiede l'eliminazione dell'entità dalla tabella **Current** e l'aggiunta dell'entità alla tabella **Archive.**

Ma non è possibile utilizzare un EGT per eseguire queste due operazioni. Per evitare il rischio che, a causa di un errore, un'entità venga visualizzata in entrambe le tabelle o in nessuna di esse, l'operazione di archiviazione deve garantire la coerenza finale. Il diagramma seguente illustra in sequenza i passaggi di questa operazione.  

![Diagramma della soluzione ai fini della coerenza finale][12]

Un client avvia l'operazione di archiviazione inserendo un messaggio in una coda di Azure (in questo esempio, per archiviare i #456 dei dipendenti). Un ruolo di lavoro esegue il polling della coda per individuare i nuovi messaggi. Quando ne trova uno, legge il messaggio e lascia una copia nascosta nella coda. Successivamente, il ruolo di lavoro recupera una copia dell'entità dalla tabella dei dipendenti **Correnti**, inserisce una copia nella tabella dei dipendenti **Archiviati** e quindi elimina l'originale dalla tabella dei dipendenti **Correnti**. Infine, se nei passaggi precedenti non si sono verificati errori, il ruolo di lavoro elimina il messaggio nascosto dalla coda.  

In questo esempio, il passaggio 4 nel diagramma inserisce il dipendente nella tabella **Archive.** È possibile aggiungere il dipendente a un BLOB nell'archivio BLOB o a un file in un file system.  

#### <a name="recover-from-failures"></a>Ripristino da erroriRecover from failures
È importante che le operazioni nei passaggi da 4 a 5 del diagramma siano *idempotenti* nel caso in cui il ruolo di lavoro debba riavviare l'operazione di archiviazione. Se si usa l'archiviazione tabelle, per il passaggio 4 è necessario usare un'operazione di "inserimento o sostituzione"; per il passaggio 5, è necessario utilizzare un'operazione "delete if exists" nella libreria client in uso. Se si utilizza un altro sistema di archiviazione, è necessario utilizzare un'operazione idemtent appropriata.  

Se il ruolo di lavoro non completa mai il passaggio 6 nel diagramma, dopo un timeout il messaggio riappare nella coda pronto per il ruolo di lavoro per tentare di rielaborarlo. Il ruolo di lavoro può controllare quante volte un messaggio nella coda è stato letto e, se necessario, contrassegnarlo come messaggio "veleno" per l'analisi inviandolo a una coda separata. Per ulteriori informazioni sulla lettura dei messaggi della coda e sul controllo del numero di dequeue, vedere [Ottenere messaggi](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alcuni errori dall'archiviazione tabelle e dall'archiviazione delle code sono errori temporanei e l'applicazione client deve includere la logica di ripetizione dei tentativi appropriata per gestirli.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione non prevede l'isolamento delle transazioni. Ad esempio, un client potrebbe leggere le tabelle **Corrente** e **Archivio** quando il ruolo di lavoro era tra i passaggi da 4 a 5 nel diagramma e visualizzare una visualizzazione incoerente dei dati. Alla fine i dati saranno coerenti.  
* È necessario assicurarsi che i passaggi da 4 a 5 siano idempotenti per garantire la coerenza finale.  
* È possibile ridimensionare la soluzione usando più code e istanze del ruolo di lavoro.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si desidera garantire la coerenza finale tra entità esistenti in tabelle o partizioni diverse. È possibile estendere questo modello per garantire la coerenza finale per le operazioni tra l'archiviazione tabelle e l'archiviazione BLOB e altre origini dati non di Archiviazione di Azure, ad esempio un database o il file system.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni del gruppo di entità](#entity-group-transactions)  
* [Unione o sostituzione](#merge-or-replace)  

> [!NOTE]
> Se l'isolamento delle transazioni è importante per la soluzione, è consigliabile riprogettare le tabelle per consentire l'utilizzo di EGR.  
> 
> 

### <a name="index-entities-pattern"></a>Modello per entità di indice
mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

#### <a name="context-and-problem"></a>Contesto e problema
L'archiviazione tabelle indicizza automaticamente `PartitionKey` le `RowKey` entità usando i valori e . Ciò consente a un'applicazione client di recuperare un'entità in modo efficiente utilizzando una query punto. Ad esempio, utilizzando la struttura della tabella seguente, un'applicazione client può recuperare in `PartitionKey` modo `RowKey`efficiente una singola entità dipendente utilizzando il nome del reparto e l'ID dipendente (e ).  

![Grafica dell'entità dipendente][13]

Se si desidera anche essere in grado di recuperare un elenco di entità dipendente in base al valore di un'altra proprietà non univoca, ad esempio il cognome, è necessario utilizzare un'analisi della partizione meno efficiente. Questa scansione trova le corrispondenze, anziché utilizzare un indice per cercarle direttamente. Ciò è dovuto al fatto che l'archiviazione tabelle non fornisce indici secondari.  

#### <a name="solution"></a>Soluzione
Per abilitare la ricerca in base al cognome con la struttura di entità precedente, è necessario gestire elenchi di ID dipendente. Se si desidera recuperare le entità dipendente con un cognome specifico, ad esempio Jones, è necessario innanzitutto individuare l'elenco degli ID dipendente per i dipendenti con Jones come cognome e quindi recuperare tali entità dipendente. Sono disponibili tre opzioni principali per memorizzare gli elenchi degli ID dipendente:  

* Usare l'archiviazione BLOB.  
* Creare entità di indice nella stessa partizione delle entità dipendente.  
* Creare entità di indice in una tabella o una partizione separata.  

Opzione 1: Usare l'archiviazione BLOBOption 1: Use Blob storage  

Creare un BLOB per ogni cognome univoco e in `PartitionKey` ogni BLOB `RowKey` archiviare un elenco dei valori (reparto) e (ID dipendente) per i dipendenti che dispongono di tale cognome. Quando si aggiunge o si elimina un dipendente, assicurarsi che il contenuto del BLOB pertinente sia coerente con le entità dipendente.  

Opzione 2: Creare entità di indice nella stessa partizioneOption 2: Create index entities in the same partition  

Usare le entità di indice che archiviano i dati seguenti:Use index entities that store the following data:  

![Immagine che mostra l'entità dipendente, con una stringa contenente un elenco di ID dipendente con lo stesso cognome][14]

La `EmployeeIDs` proprietà contiene un elenco di ID dipendente per i `RowKey`dipendenti con il cognome memorizzato nel file .  

I passaggi seguenti illustrano il processo da seguire quando si aggiunge un nuovo dipendente. In questo esempio viene aggiunto un dipendente con ID 000152 e il cognome Jones nel reparto vendite:  

1. Recuperare l'entità `PartitionKey` di indice con un `RowKey` valore "Sales" e il valore "Jones". Salvare il valore ETag dell'entità per usarlo nel passaggio 2.  
2. Creare una transazione del gruppo di entità (ovvero un'operazione`PartitionKey` batch) che `RowKey` inserisce la nuova entità dipendente ( valore`PartitionKey` "Vendite" `RowKey` e valore "000152") e aggiorni l'entità di indice ( valore "Vendite" e valore "Jones"). L'EGT esegue questa operazione aggiungendo il nuovo ID dipendente all'elenco nel campo EmployeeIDs. Per ulteriori informazioni su ECT, vedere [Transazioni del gruppo](#entity-group-transactions)di entità .  
3. Se EGT ha esito negativo a causa di un errore di concorrenza ottimistica (vale a dire, un altro utente ha modificato l'entità di indice), è necessario ricominciare dal passaggio 1.  

È possibile utilizzare un approccio simile all'eliminazione di un dipendente se si utilizza la seconda opzione. La modifica del cognome di un dipendente è leggermente più complessa, perché è necessario eseguire un EGT che aggiorna tre entità: l'entità employee, l'entità index per il cognome precedente e l'entità index per il nuovo cognome. È necessario recuperare ogni entità prima di apportare modifiche, per recuperare i valori ETag che è quindi possibile utilizzare per eseguire gli aggiornamenti utilizzando la concorrenza ottimistica.  

Nei passaggi seguenti viene descritto il processo da seguire quando è necessario cercare tutti i dipendenti con un cognome specifico in un reparto. In questo esempio, stiamo cercando tutti i dipendenti con cognome Jones nel reparto vendite:  

1. Recuperare l'entità `PartitionKey` di indice con un `RowKey` valore "Sales" e il valore "Jones".  
2. Analizzare l'elenco degli ID `EmployeeIDs` dipendente nel campo.  
3. Se sono necessarie informazioni aggiuntive su ognuno di questi dipendenti (ad esempio `PartitionKey` i relativi indirizzi `RowKey` di posta elettronica), recuperare ognuna delle entità dipendente utilizzando il valore "Vendite" e i valori dall'elenco dei dipendenti ottenuti nel passaggio 2.  

Opzione 3: Creare entità di indice in una partizione o tabella separataOption 3: Create index entities in a separate partition or table  

Per questa opzione, usare le entità di indice che archiviano i dati seguenti:For this option, use index entities that store the following data:  

![Immagine che mostra l'entità dipendente, con una stringa contenente un elenco di ID dipendente con lo stesso cognome][15]

La `EmployeeIDs` proprietà contiene un elenco di ID dipendente per i `RowKey`dipendenti con il cognome memorizzato nel file .  

Non è possibile utilizzare ET per mantenere la coerenza, perché le entità di indice si trovano in una partizione separata dalle entità dipendente. Assicurarsi che le entità di indice siano alla fine coerenti con le entità dipendente.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione richiede almeno due query per recuperare le entità corrispondenti: una `RowKey` per eseguire una query sulle entità di indice per ottenere l'elenco di valori e quindi query per recuperare ogni entità nell'elenco.  
* Poiché una singola entità ha una dimensione massima di 1 MB, l'opzione 2 e l'opzione 3 nella soluzione presuppongono che l'elenco degli ID dipendente per un determinato cognome non sia mai superiore a 1 MB. Se è probabile che l'elenco degli ID dipendente abbia più di 1 MB, usare l'opzione 1 e archiviare i dati dell'indice nell'archivio BLOB.  
* Se si utilizza l'opzione 2 (utilizzando EGR per gestire l'aggiunta e l'eliminazione di dipendenti e la modifica del cognome di un dipendente), è necessario valutare se il volume delle transazioni si avvicinerà ai limiti di scalabilità in una determinata partizione. In questo caso, è consigliabile prendere in considerazione una soluzione coerente (opzione 1 o 3). Questi usano le code per gestire le richieste di aggiornamento e consentono di archiviare le entità di indice in una partizione separata dalle entità dipendente.  
* L'opzione 2 in questa soluzione presuppone che si desideri cercare in base al cognome all'interno di un reparto. Ad esempio, si desidera recuperare un elenco di dipendenti con un cognome Jones nel reparto vendite. Se si desidera essere in grado di cercare tutti i dipendenti con un cognome Jones in tutta l'organizzazione, utilizzare l'opzione 1 o l'opzione 3.
* È possibile implementare una soluzione basata su code che garantisca la coerenza finale. Per ulteriori dettagli, vedere il [modello di transazioni coerenti alla fine](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Utilizzare questo modello quando si desidera cercare un set di entità che condividono tutti un valore di proprietà comune, ad esempio tutti i dipendenti con il cognome Jones.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Transazioni del gruppo di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Modello di denormalizzazione
combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.  

#### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale, in genere si normalizzano i dati per rimuovere la duplicazione che si verifica quando le query recuperano dati da più tabelle. Se si normalizzano i dati nelle tabelle di Azure, è necessario eseguire più round trip dal client al server per recuperare i dati correlati. Ad esempio, con la struttura tabella seguente, sono necessari due round trip per recuperare i dettagli per un reparto. Un trip recupera l'entità reparto che include l'ID del responsabile e il secondo recidiva i dettagli del manager in un'entità dipendente.  

![Grafica dell'entità reparto e dell'entità dipendente][16]

#### <a name="solution"></a>Soluzione
Anziché archiviare i dati in due entità separate, denormalizzare i dati e conservare una copia dei dettagli sul manager nell'entità reparto. Ad esempio:  

![Grafica dell'entità del reparto denormalizzata e combinata][17]

Con le entità di reparto archiviate con queste proprietà, è ora possibile recuperare tutti i dettagli necessari su un reparto utilizzando una query punto.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Archiviare alcuni dati due volte comporta un aumento dei costi. Il vantaggio in termini di prestazioni derivante dal minor numero di richieste all'archiviazione tabelle in genere supera l'aumento marginale dei costi di archiviazione. Inoltre, questo costo è parzialmente compensato da una riduzione del numero di transazioni necessarie per recuperare i dettagli di un reparto.  
* È necessario mantenere la coerenza tra le due entità in cui sono archiviate le informazioni sui manager. È possibile gestire il problema di coerenza utilizzando EGR per aggiornare più entità in una singola transazione atomica. In questo caso, l'entità reparto e l'entità dipendente per il responsabile di reparto vengono archiviate nella stessa partizione.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario cercare spesso informazioni correlate. Questo modello riduce il numero di query che il client deve eseguire per recuperare i dati necessari.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni del gruppo di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Modello per chiave composta
Usare `RowKey` valori composti per consentire a un client di cercare dati correlati con una query a punto singolo.  

#### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale, è naturale usare i join nelle query per restituire parti di dati correlate al client in una singola query. Ad esempio, si può usare l'ID dipendente per cercare un elenco di entità correlate che contengono i dati relativi alle prestazioni e alle valutazioni per tale dipendente.  

Si supponga di archiviare le entità dipendente nell'archiviazione tabelle utilizzando la struttura seguente:  

![Grafica dell'entità dipendente][18]

È inoltre necessario archiviare i dati cronologici relativi alle revisioni e alle prestazioni per ogni anno in cui il dipendente ha lavorato per l'organizzazione e deve essere in grado di accedere a queste informazioni per anno. Una possibilità consiste nel creare un'altra tabella di archiviazione delle entità con la struttura seguente:  

![Grafica dell'entità di revisione dei dipendenti][19]

Con questo approccio, è possibile decidere di duplicare alcune informazioni (ad esempio nome e cognome) nella nuova entità, per consentire di recuperare i dati con una singola richiesta. Tuttavia, non è possibile mantenere la coerenza forte perché non è possibile utilizzare un EGT per aggiornare le due entità in modo atomico.  

#### <a name="solution"></a>Soluzione
Archiviare un nuovo tipo di entità nella tabella originale usando entità con la struttura seguente:Store a new entity type in your original table by using entities with the following structure:  

![Grafica dell'entità dipendente con chiave composta][20]

Si noti come il `RowKey` è ora una chiave composta, costituito dall'ID dipendente e l'anno dei dati di revisione. In questo modo è possibile recuperare le prestazioni del dipendente ed esaminare i dati con una singola richiesta per una singola entità.  

L'esempio seguente illustra come recuperare tutti i dati di valutazione per uno specifico dipendente (ad esempio il dipendente 000123 del reparto vendite):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È necessario utilizzare un carattere separatore `RowKey` adatto che semplifica l'analisi del valore, ad esempio **000123_2012**.  
* Questa entità viene archiviata anche nella stessa partizione di altre entità che contengono dati correlati per lo stesso dipendente. Ciò significa che è possibile utilizzare EGR per mantenere una forte coerenza.
* È necessario considerare la frequenza con cui si eseguono query sui dati per determinare se questo modello è appropriato. Ad esempio, se si accede raramente ai dati di revisione e i dati principali dei dipendenti spesso, è necessario mantenerli come entità separate.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare una o più entità correlate su cui si eseguono query frequenti.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni del gruppo di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Modello della parte finale del log
Recuperare le *n* entità aggiunte più di `RowKey` recente a una partizione utilizzando un valore che ordina in ordine di data e ora inverso.  

> [!NOTE]
> I risultati delle query restituiti dall'API delle tabelle di Azure in Azure Cosmos DB non vengono ordinati in base alla chiave di partizione o alla chiave di riga. Pertanto, mentre questo modello è adatto per l'archiviazione tabelle, non è adatto per Azure Cosmos DB. Per un elenco dettagliato delle differenze di funzionalità, vedere [Differenze tra l'API delle tabelle in Azure Cosmos DB e Archiviazione tabelle](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)di Azure.

#### <a name="context-and-problem"></a>Contesto e problema
Un requisito comune è poter recuperare le entità create più di recente, ad esempio le ultime dieci note di rimborso spese inviate da un dipendente. Le query `$top` di tabella supportano un'operazione di query per restituire le prime *n* entità da un set. Non esiste un'operazione di query equivalente per restituire le ultime *n* entità in un set.  

#### <a name="solution"></a>Soluzione
Archiviare le entità `RowKey` utilizzando un ordinamento ordinato naturalmente in ordine di data/ora inverso, in modo che la voce più recente sia sempre la prima nella tabella.  

Ad esempio, per poter recuperare le ultime dieci note di rimborso spese inviate da un dipendente, è possibile usare un valore di tick inverso derivato dal valore di data/ora corrente. Nell'esempio di codice Cè seguente viene illustrato un modo per `RowKey` creare un valore "segni di graduazione invertiti" adatti per un oggetto che ordina dal più recente al meno recente:The following C' code sample shows one way to create a suitable "inverted ticks" value for a that sorts from the most recent to the ldest:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

È possibile tornare al valore di data/ora utilizzando il codice seguente:You can get back to the date/time value by using the following code:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

La query sulla tabella ha un aspetto simile al seguente:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È necessario riempire il valore del segno di graduazione inverso con zeri iniziali, per garantire che il valore stringa venga ordinato come previsto.  
* È necessario tenere presenti gli obiettivi di scalabilità a livello di partizione. Prestare attenzione a non creare partizioni hot spot.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario accedere alle entità in ordine inverso di data/ora o quando è necessario accedere alle entità aggiunte più di recente.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Anti-modello prepend/append](#prepend-append-anti-pattern)  
* [Recuperare le entitàRetrieve entities](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Modello di eliminazione volume elevato
Abilitare l'eliminazione di un volume elevato di entità archiviando tutte le entità per l'eliminazione simultanea in una tabella separata. Eliminare le entità eliminando la tabella.  

#### <a name="context-and-problem"></a>Contesto e problema
Molte applicazioni eliminano vecchi dati non più necessari a un'applicazione client o che l'applicazione ha archiviato in un altro supporto di archiviazione. Tali dati vengono in genere identificati in base a una data. Ad esempio, è necessario eliminare i record di tutte le richieste di accesso che hanno più di 60 giorni.  

Una possibile progettazione consiste nell'utilizzare la data e `RowKey`l'ora della richiesta di accesso nel:  

![Grafica dell'entità del tentativo di accesso][21]

Questo approccio evita le aree sensibili della partizione, perché l'applicazione può inserire ed eliminare entità di accesso per ogni utente in una partizione separata. Tuttavia, questo approccio può essere costoso e dispendioso in termini di tempo se si dispone di un numero elevato di entità. In primo luogo, è necessario eseguire un'analisi della tabella per identificare tutte le entità da eliminare e quindi è necessario eliminare ogni entità precedente. Si noti che è possibile ridurre il numero di round trip al server necessari per eliminare le entità precedenti raggruppando più richieste di eliminazione in EGT.  

#### <a name="solution"></a>Soluzione
Usare una tabella separata per ogni giorno di tentativi di accesso. È possibile utilizzare la progettazione di entità precedente per evitare le aree sensibili quando si inseriscono entità. L'eliminazione di entità precedenti è ora semplicemente una questione di eliminazione di una tabella ogni giorno (una singola operazione di archiviazione), invece di trovare ed eliminare centinaia e migliaia di singole entità di accesso ogni giorno.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La progettazione supporta altri modi in cui l'applicazione utilizzerà i dati, ad esempio la ricerca di entità specifiche, il collegamento con altri dati o la generazione di informazioni aggregate?  
* La progettazione consente di evitare hotspot durante l'inserimento di nuove entità?  
* Se si vuole riutilizzare lo stesso nome di tabella dopo l'eliminazione, prevedere un ritardo. È consigliabile usare sempre nomi di tabella univoci.  
* Aspettatevi una certa limitazione della velocità quando si utilizza una nuova tabella per la prima volta, mentre archiviazione tabelle apprende i modelli di accesso e distribuisce le partizioni tra i nodi. È necessario considerare la frequenza con cui è necessario creare nuove tabelle.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si dispone di un volume elevato di entità che è necessario eliminare contemporaneamente.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni del gruppo di entità](#entity-group-transactions)
* [Modificare entità](#modify-entities)  

### <a name="data-series-pattern"></a>Modello di serie di dati
Archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

#### <a name="context-and-problem"></a>Contesto e problema
Spesso un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente. L'applicazione potrebbe, ad esempio, registrare il numero di messaggi immediati che ogni dipendente invia ogni ora e quindi usare queste informazioni per tracciare il numero di messaggi inviati da ogni utente nelle 24 ore precedenti. Una progettazione potrebbe essere l'archiviazione di 24 entità per ogni dipendente:  

![Grafica dell'entità Statistiche messaggio][22]

Con questa progettazione è possibile individuare e aggiornare l'entità da aggiornare per ogni dipendente ogni volta che l'applicazione deve aggiornare il valore del numero di messaggi. Tuttavia, per recuperare le informazioni allo scopo di tracciare un grafico dell'attività per le 24 ore precedenti, è necessario recuperare 24 entità.  

#### <a name="solution"></a>Soluzione
Usare la progettazione seguente, con una proprietà separata per archiviare il numero di messaggi per ogni ora:Use the following design, with a separate property to store the message count for each hour:  

![Immagine che mostra l'entità Statistiche messaggio con proprietà separate][23]

Con questa progettazione è possibile usare un'operazione di unione per aggiornare il numero di messaggi per un dipendente per un'ora specifica. A questo punto, è possibile recuperare tutte le informazioni necessarie per tracciare il grafico utilizzando una richiesta per una singola entità.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Se la serie di dati completa non rientra in una singola entità (un'entità può avere fino a 252 proprietà), usare un archivio dati alternativo, ad esempio un BLOB.  
* Se si dispone di più client che aggiornano un'entità contemporaneamente, utilizzare **l'ETag** per implementare la concorrenza ottimistica. Se si dispone di molti client, è possibile che si verifichi una contesa elevata.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario aggiornare e recuperare una serie di dati associata a una singola entità.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello di entità di grandi dimensioni](#large-entities-pattern)  
* [Unione o sostituzione](#merge-or-replace)  
* [Modello di transazioni coerente (se](#eventually-consistent-transactions-pattern) si archiviano le serie di dati in un BLOB)Eventually consistent transactions pattern (if you're storing the data series in a blob)  

### <a name="wide-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  

#### <a name="context-and-problem"></a>Contesto e problema
Una singola entità non può avere più di 252 proprietà (escluse le proprietà di sistema obbligatorie) e non può archiviare più di 1 MB di dati in totale. In un database relazionale, è in genere necessario aggirare eventuali limiti alle dimensioni di una riga aggiungendo una nuova tabella e applicando una relazione 1 a 1 tra di esse.  

#### <a name="solution"></a>Soluzione
Utilizzando l'archiviazione tabelle, è possibile archiviare più entità per rappresentare un singolo oggetto business di grandi dimensioni con più di 252 proprietà. Ad esempio, se si desidera archiviare un conteggio del numero di messaggi di messaggistica istantanea inviati da ogni dipendente negli ultimi 365 giorni, è possibile utilizzare la progettazione seguente che utilizza due entità con schemi diversi:  

![Immagine che mostra l'entità Statistiche messaggio con Rowkey 01 e l'entità statistiche messaggio con Rowkey 02][24]

Per apportare una modifica che richiede l'aggiornamento di entrambe le entità per mantenerle sincronizzate tra loro, è possibile usare una transazione EGT. Diversamente, è possibile usare una singola operazione di unione per aggiornare il numero di messaggi per un giorno specifico. Per recuperare tutti i dati per un singolo dipendente, è necessario recuperare entrambe le entità. È possibile eseguire questa operazione con `PartitionKey` due `RowKey` richieste efficienti che utilizzano sia un valore che un valore.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Si consideri il punto seguente quando si decide come implementare questo modello:Consider the following point when deciding how to implement this pattern:  

* Il recupero di un'entità logica completa richiede almeno due transazioni di archiviazione, una per recuperare ogni entità fisica.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare entità le cui dimensioni o il numero di proprietà superano i limiti per una singola entità nell'archiviazione tabelle.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni del gruppo di entità](#entity-group-transactions)
* [Unione o sostituzione](#merge-or-replace)

### <a name="large-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

#### <a name="context-and-problem"></a>Contesto e problema
Una singola entità non può archiviare più di 1 MB di dati in totale. Se una o più proprietà archiviano valori che fanno sì che la dimensione totale dell'entità superi questo valore, non è possibile archiviare l'intera entità nell'archiviazione tabelle.  

#### <a name="solution"></a>Soluzione
Se le dimensioni dell'entità superano 1 MB perché una o più proprietà contengono una grande quantità di dati, è possibile archiviare i dati nell'archivio BLOB e quindi archiviare l'indirizzo del BLOB in una proprietà nell'entità. Ad esempio, è possibile archiviare la foto di un dipendente nell'archivio BLOB e archiviare un collegamento alla foto nella `Photo` proprietà dell'entità dipendente:  

![Immagine che mostra l'entità dipendente con la stringa per Foto che punta all'archiviazione BLOB][25]

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere l'eventuale coerenza tra l'entità nell'archivio tabelle e i dati nell'archiviazione BLOB, usare il [modello Transazioni coerenti per](#eventually-consistent-transactions-pattern) gestire le entità.
* Il recupero di un'entità completa richiede almeno due transazioni di archiviazione: una per recuperare l'entità e un'altra per recuperare i dati BLOB.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare entità le cui dimensioni superano i limiti per una singola entità nell'archiviazione tabelle.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello di entità di grandi dimensioni](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Anti-modello prepend/append
Quando si dispone di un volume elevato di inserimenti, aumentare la scalabilità distesa gli inserimenti tra più partizioni.  

#### <a name="context-and-problem"></a>Contesto e problema
L'anteposizione o l'aggiunta di entità alle entità archiviate determina in genere l'aggiunta da parte dell'applicazione di nuove entità alla prima o ultima partizione di una sequenza di partizioni. In questo caso, tutti gli inserti in un determinato momento si svolgono nella stessa partizione, creando un hotspot. Ciò impedisce all'archiviazione tabelle di eseguire il bilanciamento del carico degli inserimenti tra più nodi e, probabilmente, l'applicazione raggiunge gli obiettivi di scalabilità per la partizione. Si consideri ad esempio il caso di un'applicazione che registra l'accesso alla rete e alle risorse da parte dei dipendenti. Una struttura di entità come la seguente può far in modo che la partizione dell'ora corrente diventi un'area sensibile, se il volume delle transazioni raggiunge l'obiettivo di scalabilità per una singola partizione:  

![Grafica dell'entità dipendente][26]

#### <a name="solution"></a>Soluzione
La struttura di entità alternativa seguente consente di evitare un'area sensibile in una partizione specifica, poiché l'applicazione registra gli eventi:The following alternative entity structure avoids a hotspot on any particular partition, as the application logs events:  

![Immagine che mostra l'entità dipendente con RowKey che combina l'anno, il mese, il giorno, l'ora e l'ID evento][27]

Si noti con `PartitionKey` questo `RowKey` esempio come entrambe le chiavi composte e sono. L'oggetto `PartitionKey` utilizza sia il reparto che l'ID dipendente per distribuire la registrazione tra più partizioni.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La struttura chiave alternativa che evita la creazione di partizioni critiche negli inserimenti supporta in modo efficiente le query effettuate dall'applicazione client?  
* Il volume di transazioni previsto significa che è probabile che si raggiungano gli obiettivi di scalabilità per una singola partizione e che si debbano limitare l'archiviazione tabelle?  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Evitare l'anti-modello di anteposizione/aggiunta quando è probabile che il volume delle transazioni comporterà una limitazione della velocità in base all'archiviazione della tabella quando si accede a una partizione a caldo.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello della parte finale del log](#log-tail-pattern)  
* [Modificare entità](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Anti-modello dei dati di log
In genere, è consigliabile usare l'archiviazione BLOB anziché l'archiviazione tabelle per archiviare i dati di log.  

#### <a name="context-and-problem"></a>Contesto e problema
Un caso d'uso comune per i dati di log consiste nel recuperare una selezione di voci di log per un intervallo di data/ora specifico. Ad esempio, si desidera trovare tutti i messaggi di errore e critici registrati dall'applicazione tra le 15:04 e le 15:06 in una data specifica. Non si desidera utilizzare la data e l'ora del messaggio di log per determinare la partizione in cui si salvano le entità. Ciò comporta una partizione a caldo perché in un determinato `PartitionKey` momento tutte le entità di log condivideranno lo stesso valore (vedere l'anti-modello [Prepend/append](#prepend-append-anti-pattern)). Ad esempio, lo schema di entità seguente per un messaggio di log genera una partizione a caldo, perché l'applicazione scrive tutti i messaggi di log nella partizione per la data e l'ora correnti:For example, the following entity schema for a log message results in a hot partition, because the application writes all log messages to the partition for the current date and hour:  

![Grafica dell'entità del messaggio di log][28]

In questo esempio, include `RowKey` la data e l'ora del messaggio di log per garantire che i messaggi di log vengano ordinati in base all'ordine di data/ora. Include `RowKey` anche un ID messaggio, nel caso in cui più messaggi di log condividano la stessa data e ora.  

Un altro approccio `PartitionKey` consiste nell'utilizzare un che assicura che l'applicazione scrive i messaggi in un intervallo di partizioni. Ad esempio, se l'origine del messaggio di log consente di distribuire i messaggi tra molte partizioni, è possibile utilizzare lo schema di entità seguente:  

![Grafica dell'entità del messaggio di log][29]

Tuttavia, il problema con questo schema è che per recuperare tutti i messaggi di log per un intervallo di tempo specifico, è necessario cercare ogni partizione nella tabella.

#### <a name="solution"></a>Soluzione
La sezione precedente ha evidenziato il problema di provare a utilizzare Archiviazione tabelle per archiviare le voci di registro e ha suggerito due progetti insoddisfacenti. Una soluzione ha portato a una partizione a caldo con il rischio di riduzione delle prestazioni durante la scrittura dei messaggi di log. L'altra soluzione ha comportato una riduzione delle prestazioni delle query, a causa della necessità di eseguire l'analisi di ogni partizione nella tabella per recuperare i messaggi di log per un intervallo di tempo specifico. L'archiviazione BLOB offre una soluzione migliore per questo tipo di scenario ed è in questo modo che l'analisi di Archiviazione di Azure archivia i dati di log raccolti.  

In questa sezione viene descritto come l'analisi dell'archiviazione archivia i dati di log nell'archivio BLOB, come esempio di questo approccio per l'archiviazione dei dati che in genere vengono interrogati in base all'intervallo.  

L'analisi dell'archiviazione archivia i messaggi di log in un formato delimitato in più BLOB. Il formato delimitato semplifica l'analisi dei dati nel messaggio di log da parte di un'applicazione client.  

L'analisi dell'archiviazione usa una convenzione di denominazione per i BLOB che consente di individuare il BLOB (o i BLOB) che contengono i messaggi di log per i quali si esegue la ricerca. Ad esempio, un BLOB denominato "queue/2014/07/31/1800/000001.log" contiene messaggi di log correlati al servizio di coda per l'ora a partire dalle 18:00 del 31 luglio 2014. "000001" indica che si tratta del primo file di log per il periodo. L'analisi dell'archiviazione registra anche i timestamp del primo e dell'ultimo messaggio di log archiviati nel file, come parte dei metadati del BLOB. L'API per l'archiviazione BLOB consente di individuare i BLOB in un contenitore in base a un prefisso del nome. Per individuare tutti i BLOB che contengono i dati del log della coda per l'ora a partire dalle 18:00, è possibile usare il prefisso "queue/2014/07/31/1800".  

L'analisi dell'archiviazione memorizza nel buffer i messaggi di log internamente e quindi aggiorna periodicamente il BLOB appropriato o ne crea uno nuovo con il batch più recente di voci di log. In questo modo si riduce il numero di scritture che deve eseguire nell'archiviazione BLOB.  

Se si implementa una soluzione simile nella propria applicazione, considerare come gestire il compromesso tra affidabilità e costi e scalabilità. In altre parole, valutare l'effetto della scrittura di ogni voce di log nell'archivio BLOB, rispetto al buffering degli aggiornamenti nell'applicazione e la loro scrittura nell'archiviazione BLOB in batch.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come archiviare i dati di log, considerare quanto segue:  

* Se si crea una struttura di tabella che evita potenziali partizioni a caldo, è possibile che non sia possibile accedere ai dati di log in modo efficiente.  
* Per elaborare i dati di log, spesso un client deve caricare molti record.  
* Anche se i dati di log sono spesso strutturati, l'archiviazione BLOB potrebbe essere una soluzione migliore.  

### <a name="implementation-considerations"></a>Considerazioni sull'implementazione
Questa sezione illustra alcune considerazioni da tenere presente quando si implementano i modelli descritti nelle sezioni precedenti. Nella maggior parte di questa sezione vengono usati esempi scritti in C# che usano la libreria client di archiviazione (versione 4.3.0 al momento della stesura di questo documento).  

### <a name="retrieve-entities"></a>Recuperare le entitàRetrieve entities
Come descritto nella sezione [Progettazione per le query](#design-for-querying), la query più efficiente è una query di tipo punto. Tuttavia, in alcuni scenari potrebbe essere necessario recuperare più entità. In questa sezione vengono descritti alcuni approcci comuni per il recupero delle entità tramite la libreria client di archiviazione.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Eseguire una query punto utilizzando la libreria client di archiviazioneRun a point query by using the Storage Client Library
Il modo più semplice per eseguire una query punto consiste nell'utilizzare l'operazione **Recupera** tabella. Come illustrato nel seguente frammento di codice c'è, questa operazione recupera un'entità con un `PartitionKey` valore "Sales" e un `RowKey` valore "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Si noti come questo esempio prevede che `EmployeeEntity`l'entità recuperata sia di tipo .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recuperare più entità tramite LINQRetrieve multiple entities by using LINQ
È possibile recuperare più entità utilizzando LINQ con La libreria client di archiviazione e specificando una query con una clausola **where.** Per evitare un'analisi della tabella, è necessario includere sempre `PartitionKey` `RowKey` il valore nella clausola where e, se possibile, il valore per evitare analisi di tabelle e partizioni. L'archiviazione tabelle supporta un set limitato di operatori di confronto (maggiore di, maggiore o uguale a, minore di, minore o uguale a, uguale e non uguale) da utilizzare nella clausola where. Il seguente frammento di codice in C'è trova tutti i `RowKey` dipendenti il cui cognome inizia con "B" (presupponendo che l'archivia il cognome) nel reparto Vendite (supponendo che memorizzi `PartitionKey` il nome del reparto):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Si noti come la `RowKey` query `PartitionKey` specifica sia a e a per garantire prestazioni migliori.  

L'esempio di codice seguente mostra funzionalità equivalenti usando l'API fluent (per altre informazioni sulle API fluenti in generale, vedere Procedure consigliate per la progettazione di [un'API fluent):](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> L'esempio annida più `CombineFilters` metodi per includere le tre condizioni di filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperare un numero elevato di entità da una queryRetrieve large numbers of entities from a query
Una query ottimale restituisce `PartitionKey` una singola `RowKey` entità in base a un valore e un valore. Tuttavia, in alcuni scenari potrebbe essere necessario restituire molte entità dalla stessa partizione o anche da molte partizioni. È sempre necessario eseguire test completi delle prestazioni dell'applicazione in tali scenari.  

Una query sull'archiviazione tabelle può restituire un massimo di 1.000 entità contemporaneamente ed essere eseguita per un massimo di cinque secondi. L'archiviazione tabelle restituisce un token di continuazione per consentire all'applicazione client di richiedere il set di entità successivo, se si verifica una delle condizioni seguenti:Table storage returns a continuation token to enable the client application to request the next set of entities, if any of the following are true:

- Il set di risultati contiene più di 1.000 entità.
- La query non è stata completata entro cinque secondi.
- La query supera il limite della partizione. 

Per ulteriori informazioni sul funzionamento dei token di continuazione, vedere [Timeout e impaginazione delle](https://msdn.microsoft.com/library/azure/dd135718.aspx)query .  

Se si usa la libreria client di archiviazione, può gestire automaticamente i token di continuazione quando restituisce le entità dall'archivio tabelle. Ad esempio, l'esempio di codice C 'c'è automaticamente gestisce i token di continuazione se archiviazione di tabella li restituisce in una risposta:For example, the following C's code sample automatically handles continuation tokens if Table storage returns them in a response:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Il codice C# seguente gestisce i token di continuazione in modo esplicito:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Usando i token di continuazione in modo esplicito è possibile controllare quando l'applicazione recupera il successivo segmento di dati. Ad esempio, se l'applicazione client consente agli utenti di scorrere le entità archiviate in una tabella, un utente potrebbe decidere di non scorrere tutte le entità recuperate dalla query. L'applicazione utilizzerebbe solo un token di continuazione per recuperare il segmento successivo quando l'utente ha terminato il paging di tutte le entità nel segmento corrente. Questo approccio offre diversi vantaggi:  

* È possibile limitare la quantità di dati da recuperare dall'archiviazione tabelle e che si sposta in rete.  
* È possibile eseguire operazioni di I/O asincrone in .NET.  
* È possibile serializzare il token di continuazione in un archivio permanente, in modo da poter continuare in caso di arresto anomalo dell'applicazione.  

> [!NOTE]
> Un token di continuazione restituisce in genere un segmento contenente 1.000 entità, anche se può contenere un numero inferiore. Ciò avviene anche se si limita il numero di voci restituite da una query utilizzando **Take** per restituire le prime n entità che corrispondono ai criteri di ricerca. L'archiviazione tabelle potrebbe restituire un segmento contenente meno di n entità, insieme a un token di continuazione che consente di recuperare le entità rimanenti.  
> 
> 

Il codice C# seguente illustra come modificare il numero di entità restituite all'interno di un segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Proiezione lato server
Una singola entità può avere fino a 255 proprietà e dimensioni fino a 1 MB. Quando si esegue una query sulla tabella e si recuperano le entità, è possibile che non siano necessarie tutte le proprietà ed è possibile evitare di trasferire i dati inutilmente (per ridurre la latenza e i costi). È possibile usare la proiezione lato server per trasferire solo le proprietà necessarie. Nell'esempio riportato `Email` di seguito `PartitionKey`viene `RowKey` `Timestamp`recuperata solo la proprietà (insieme a , , e `ETag`) dalle entità selezionate dalla query.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Si noti come il `RowKey` valore è disponibile anche se non è incluso nell'elenco delle proprietà da recuperare.  

### <a name="modify-entities"></a>Modificare entità
La libreria client di archiviazione consente di modificare le entità archiviate nell'archivio tabelle inserendo, eliminando e aggiornando le entità. È possibile utilizzare EGT per raggruppare più operazioni di inserimento, aggiornamento ed eliminazione, per ridurre il numero di round trip necessari e migliorare le prestazioni della soluzione.  

Eccezioni generate quando la libreria client di archiviazione esegue un EGT includono in genere l'indice dell'entità che ha causato l'esito negativo del batch. Ciò è utile quando si esegue il debug di codice che usa le transazioni EGT.  

È inoltre opportuno considerare l'influenza della progettazione sul modo in cui l'applicazione gestisce le operazioni di concorrenza e aggiornamento.  

#### <a name="managing-concurrency"></a>Gestione della concorrenza
Per impostazione predefinita, l'archiviazione tabelle implementa i controlli di concorrenza ottimistica a livello di singole entità per le operazioni di inserimento, unione ed eliminazione, anche se è possibile che un client imporiscano l'archiviazione tabelle per ignorare questi controlli. Per altre informazioni, vedere Gestione della concorrenza in Archiviazione di Microsoft Azure .For more information, see [Managing concurrency in Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Unione o sostituzione
Il `Replace` metodo `TableOperation` della classe sostituisce sempre l'entità completa nell'archiviazione tabelle. Se non si include una proprietà nella richiesta quando tale proprietà è presente nell'entità archiviata, la richiesta rimuove tale proprietà dall'entità archiviata. A meno che non si voglia rimuovere una proprietà in modo esplicito da un'entità archiviata, è necessario includere ogni proprietà nella richiesta.  

È possibile `Merge` utilizzare il `TableOperation` metodo della classe per ridurre la quantità di dati inviati all'archiviazione tabelle quando si desidera aggiornare un'entità. Il `Merge` metodo sostituisce tutte le proprietà nell'entità archiviata con i valori delle proprietà dell'entità inclusa nella richiesta. Questo metodo lascia intatte tutte le proprietà nell'entità archiviata che non sono incluse nella richiesta. Ciò è utile se si dispone di entità di grandi dimensioni ed è sufficiente aggiornare un numero ridotto di proprietà in una richiesta.  

> [!NOTE]
> I `*Replace` `Merge` metodi e hanno esito negativo se l'entità non esiste. In alternativa, è possibile `InsertOrReplace` `InsertOrMerge` utilizzare i metodi e che creano una nuova entità se non esiste.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Usare tipi di entità eterogenei
L'archiviazione tabelle è un archivio tabelle *senza schema.* Ciò significa che una singola tabella può archiviare entità di più tipi, offrendo una grande flessibilità nella progettazione. L'esempio seguente illustra una tabella che archivia entità dipendente ed entità reparto:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Ogni entità deve `PartitionKey` `RowKey`ancora `Timestamp` avere valori , e , ma può avere qualsiasi set di proprietà. Inoltre, non c'è nulla che indichi il tipo di un'entità a meno che non si scelga di archiviare tali informazioni da qualche parte. Esistono due opzioni per identificare il tipo di entità:  

* Anteporre il tipo di `RowKey` entità a `PartitionKey`(o eventualmente a ). Ad `EMPLOYEE_000123` esempio, `DEPARTMENT_SALES` `RowKey` o come valori.  
* Utilizzare una proprietà separata per registrare il tipo di entità, come illustrato nella tabella seguente.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

La prima opzione, che anteponendo `RowKey`il tipo di entità a , è utile se è possibile che due entità di tipi diversi abbiano lo stesso valore di chiave. Inoltre, raggruppa entità dello stesso tipo insieme nella partizione.  

Le tecniche descritte in questa sezione sono particolarmente rilevanti per la discussione sulle relazioni di[ereditarietà](#inheritance-relationships).  

> [!NOTE]
> È consigliabile includere un numero di versione nel valore del tipo di entità, per consentire alle applicazioni client di evolvere gli oggetti POCO e di lavorare con versioni diverse.  
> 
> 

La restante parte di questa sezione descrive alcune delle funzionalità della libreria client di archiviazione che semplificano l'uso di più tipi di entità nella stessa tabella.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recuperare tipi di entità eterogeneiRetrieve heterogeneous entity types
Se si usa la libreria client di archiviazione, sono disponibili tre opzioni per l'utilizzo di più tipi di entità.  

Se si conosce il tipo dell'entità archiviata con valori e `RowKey` `PartitionKey` valori specifici, è possibile specificare il tipo di entità quando si recupera l'entità. Questo è stato visto nei due esempi `EmployeeEntity`precedenti che recuperano entità di tipo: [eseguire una query punto utilizzando la libreria Client](#run-a-point-query-by-using-the-storage-client-library) di archiviazione e recuperare più entità utilizzando [LINQ](#retrieve-multiple-entities-by-using-linq).  

La seconda opzione consiste `DynamicTableEntity` nell'utilizzare il tipo (una borsa delle proprietà), anziché un tipo di entità POCO concreto. Questa opzione potrebbe anche migliorare le prestazioni, perché non è necessario serializzare e deserializzare l'entità in tipi .NET. Il codice C'è riportato di seguito potenzialmente recupera più entità di `DynamicTableEntity` tipi diversi dalla tabella, ma restituisce tutte le entità come istanze. Viene quindi `EntityType` utilizzata la proprietà per determinare il tipo di ogni entità:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Per recuperare altre proprietà, `TryGetValue` è necessario `Properties` utilizzare `DynamicTableEntity` il metodo sulla proprietà della classe .  

Una terza opzione consiste `DynamicTableEntity` nel combinare `EntityResolver` utilizzando il tipo e un'istanza. Ciò consente di risolvere a più tipi POCO nella stessa query. In questo esempio, il `EntityResolver` `EntityType` delegato utilizza la proprietà per distinguere tra i due tipi di entità restituiti dalla query. Il `Resolve` metodo `resolver` utilizza il `DynamicTableEntity` delegato `TableEntity` per risolvere le istanze nelle istanze.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Modificare i tipi di entità eterogenei
Non è necessario conoscere il tipo di un'entità per eliminarla e si conosce sempre il tipo di un'entità quando la si inserisce. Tuttavia, è `DynamicTableEntity` possibile utilizzare il tipo per aggiornare un'entità senza conoscerne il tipo e senza utilizzare una classe di entità POCO. Nell'esempio di codice seguente viene recuperata una singola entità e viene verificato che la `EmployeeCount` proprietà esista prima di aggiornarla.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Controllare l'accesso con le firme di accesso condivisoControl access with shared access signatures
È possibile usare i token di firma di accesso condiviso per consentire alle applicazioni client di modificare (ed eseguire query) le entità della tabella direttamente, senza la necessità di eseguire l'autenticazione direttamente con l'archiviazione tabelle. In genere, l'uso di SAS nell'applicazione comporta tre vantaggi principali:  

* Non è necessario distribuire la chiave dell'account di archiviazione a una piattaforma non sicura (ad esempio un dispositivo mobile) per consentire al dispositivo di accedere e modificare le entità in Archiviazione tabelle.  
* È possibile eseguire l'offload di parte del lavoro svolto dai ruoli Web e di lavoro nella gestione delle entità. È possibile eseguire l'offload ai dispositivi client, ad esempio i computer degli utenti finali e i dispositivi mobili.  
* È possibile assegnare un set di autorizzazioni vincolato e limitato nel tempo a un client, ad esempio consentire l'accesso in sola lettura a risorse specifiche.  

Per altre informazioni sull'uso dei token di firma di accesso condiviso con l'archiviazione tabelle, vedere [Uso delle firme](../storage/common/storage-dotnet-shared-access-signature-part-1.md)di accesso condiviso.  

Tuttavia, è comunque necessario generare i token di firma di accesso condiviso che concedono un'applicazione client alle entità nell'archiviazione tabelle. Eseguire questa operazione in un ambiente che dispone dell'accesso sicuro alle chiavi dell'account di archiviazione. In genere, è possibile usare un ruolo Web o di lavoro per generare i token delle firme di accesso condiviso e distribuirli alle applicazioni client che richiedono l'accesso alle entità. Poiché la generazione e la distribuzione dei token delle firme di accesso condiviso ai client comportano comunque un sovraccarico, è consigliabile valutare il modo migliore di ridurre tale sovraccarico, soprattutto in scenari con volumi elevati.  

È possibile generare un token di firma di accesso condiviso che concede l'accesso a un subset delle entità in una tabella. Per impostazione predefinita, si crea un token di firma di accesso condiviso per un'intera tabella. Ma è anche possibile specificare che il token di firma `PartitionKey` di accesso condiviso `PartitionKey` `RowKey` conceda l'accesso a un intervallo di valori o a un intervallo di valori . È possibile scegliere di generare token di firma di accesso condiviso per singoli utenti del sistema, in modo che il token di firma di accesso condiviso di ogni utente consenta loro di accedere solo alle proprie entità nell'archiviazione tabelle.  

### <a name="asynchronous-and-parallel-operations"></a>Operazioni asincrone e parallele
A condizione che le richieste vengano distribuite in più partizioni, è possibile migliorare la velocità effettiva e la velocità di risposta del client usando le query parallele o asincrone.
Ad esempio, si potrebbero avere due o più istanze del ruolo di lavoro che accedono alle tabelle in parallelo. È possibile avere singoli ruoli di lavoro responsabili di particolari set di partizioni o semplicemente avere più istanze del ruolo di lavoro, ognuna in grado di accedere a tutte le partizioni in una tabella.  

All'interno di un'istanza client, è possibile migliorare la velocità effettiva eseguendo le operazioni di archiviazione in modo asincrono. La libreria client di archiviazione semplifica la scrittura di query e modifiche asincrone. Ad esempio, è possibile iniziare con il metodo sincrono che recupera tutte le entità in una partizione, come illustrato nel seguente codice in C:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

È possibile modificare facilmente questo codice in modo che la query venga eseguita in modo asincrono, come indicato di seguito:You can easily modify this code so that the query runs asynchronously, as follows:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

In questo esempio asincrono è possibile visualizzare le modifiche seguenti dalla versione sincrona:  

* La firma del `async` metodo include ora `Task` il modificatore e restituisce un'istanza.  
* Anziché chiamare `ExecuteSegmented` il metodo per recuperare i `ExecuteSegmentedAsync` risultati, il metodo chiama ora il metodo . Il metodo `await` utilizza il modificatore per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare questo metodo più `department` volte, con valori diversi per il parametro. Ogni query viene eseguita in un thread separato.  

Non esiste alcuna `Execute` versione asincrona del metodo nella `TableQuery` classe , perché l'interfaccia `IEnumerable` non supporta l'enumerazione asincrona.  

È inoltre possibile inserire, aggiornare ed eliminare entità in modo asincrono. Nell'esempio C# seguente viene illustrato un metodo semplice e sincrono per inserire o sostituire un'entità dipendente:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

È possibile modificare facilmente questo codice in modo che l'aggiornamento venga eseguito in modo asincrono, come indicato di seguito:You can easily modify this code so that the update runs asynchronously, as follows:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In questo esempio asincrono è possibile visualizzare le modifiche seguenti dalla versione sincrona:  

* La firma del `async` metodo include ora `Task` il modificatore e restituisce un'istanza.  
* Anziché chiamare `Execute` il metodo per aggiornare l'entità, il metodo chiama ora il `ExecuteAsync` metodo . Il metodo `await` utilizza il modificatore per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare più metodi asincroni come questo e ogni chiamata di metodo viene eseguita in un thread separato.  


[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

