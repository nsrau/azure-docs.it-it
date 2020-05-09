---
title: Progettare Azure Cosmos DB tabelle per la scalabilità e le prestazioni
description: "Guida alla progettazione dell'archiviazione tabelle di Azure: tabelle scalabili ed efficienti in Azure Cosmos DB e archiviazione tabelle di Azure"
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: fcae1ed9064d38457ede73c675afb75ce4872fe6
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611782"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guida alla progettazione di tabelle di archiviazione tabelle di Azure: tabelle scalabili ed efficienti

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Per progettare tabelle scalabili ed efficienti, è necessario prendere in considerazione diversi fattori, inclusi i costi. Se in precedenza sono stati progettati schemi per i database relazionali, queste considerazioni risulteranno familiari. Sebbene esistano alcune analogie tra Archiviazione tabelle di Azure e i modelli relazionali, esistono tuttavia anche molte differenze importanti. Queste differenze danno in genere origine a progettazioni diverse che potrebbero sembrare poco plausibili o errate a chi ha familiarità con i database relazionali, ma che invece hanno senso se la progettazione è finalizzata a un archivio di chiavi/valori NoSQL, come l'Archiviazione tabelle.

L'archiviazione tabelle è progettata per supportare applicazioni con scalabilità cloud che possono contenere miliardi di entità ("righe" nella terminologia dei database relazionali) o per set di dati che devono supportare volumi di transazioni elevati. È quindi necessario valutare in modo diverso le modalità di archiviazione dei dati e comprendere il funzionamento dell'archiviazione tabelle. Un archivio dati NoSQL ben progettato può permettere la scalabilità di una soluzione molto più (e a un costo inferiore) rispetto a una soluzione che usa un database relazionale. Questa guida illustra proprio questi argomenti.  

## <a name="about-azure-table-storage"></a>Informazioni sull'archiviazione tabelle di Azure
In questa sezione vengono illustrate alcune delle principali funzionalità dell'archiviazione tabelle che sono particolarmente rilevanti per la progettazione di prestazioni e scalabilità. Se non si ha familiarità con archiviazione di Azure e archiviazione tabelle, vedere [Introduzione a archiviazione di Microsoft Azure](../storage/common/storage-introduction.md) e iniziare a [usare l'archiviazione tabelle di Azure con .NET](table-storage-how-to-use-dotnet.md) prima di leggere la parte restante di questo articolo. Sebbene l'obiettivo di questa guida sia l'archiviazione tabelle, include alcune discussioni sull'archiviazione code di Azure e l'archiviazione BLOB di Azure e su come usarle insieme all'archiviazione tabelle in una soluzione.  

Archiviazione tabelle utilizza un formato tabulare per archiviare i dati. In base alla terminologia standard, ogni riga della tabella rappresenta un'entità le cui diverse proprietà sono archiviate nelle colonne. Ogni entità dispone di una coppia di chiavi per identificarla in modo univoco e di una colonna di tipo timestamp utilizzata dall'archiviazione tabelle per rilevare la data dell'ultimo aggiornamento dell'entità. Il campo timestamp viene aggiunto automaticamente e non è possibile sovrascrivere manualmente il timestamp con un valore arbitrario. L'archiviazione tabelle utilizza questo timestamp dell'Ultima modifica (LMT) per gestire la concorrenza ottimistica.  

> [!NOTE]
> Le operazioni dell'API REST di archiviazione tabelle `ETag` restituiscono anche un valore che deriva da LMT. In questo documento, i termini ETag e LMT vengono usati in modo interscambiabile, in quanto fanno riferimento agli stessi dati sottostanti.  
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
<th>Posta elettronica</th>
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
<th>Posta elettronica</th>
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
<th>Posta elettronica</th>
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


Fino a questo punto, questo progetto è simile a una tabella in un database relazionale. Le differenze principali sono le colonne obbligatorie e la possibilità di archiviare più tipi di entità nella stessa tabella. Inoltre, ogni proprietà definita dall'utente, ad esempio **FirstName** o **Age**, ha un tipo di dati, ad esempio Integer o String, come una colonna in un database relazionale. A differenza di un database relazionale, tuttavia, la natura senza schema dell'archiviazione tabelle indica che una proprietà non deve avere lo stesso tipo di dati per ogni entità. Per archiviare tipi di dati complessi in una sola proprietà, è necessario usare un formato serializzato come JSON o XML. Per altre informazioni, vedere informazioni sul [modello di dati di archiviazione tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).

La scelta di `PartitionKey` e `RowKey` è fondamentale per la progettazione di una tabella ottimale. Ogni entità archiviata in una tabella deve avere una combinazione `PartitionKey` univoca `RowKey`di e. Come per le chiavi in una tabella di database relazionale `PartitionKey` , `RowKey` i valori e vengono indicizzati per creare un indice cluster che consente ricerche rapide. L'archiviazione tabelle, tuttavia, non crea indici secondari, quindi queste sono le uniche due proprietà indicizzate. alcuni dei modelli descritti in seguito mostrano come ovviare a questa limitazione apparente.  

Una tabella è costituita da una o più partizioni e molte delle decisioni di progettazione effettuate saranno relative alla scelta di un idoneo `PartitionKey` e `RowKey` all'ottimizzazione della soluzione. Una soluzione può essere costituita da una sola tabella che contiene tutte le entità organizzate in partizioni, ma in genere una soluzione dispone di più tabelle. Le tabelle consentono di organizzare logicamente le entità e consentono di gestire l'accesso ai dati tramite gli elenchi di controllo di accesso. È possibile eliminare un'intera tabella usando una singola operazione di archiviazione.  

### <a name="table-partitions"></a>Partizioni della tabella
Il nome dell'account, il nome della `PartitionKey` tabella e insieme identificano la partizione all'interno del servizio di archiviazione in cui l'archiviazione tabelle archivia l'entità. Oltre a far parte dello schema di indirizzamento per le entità, le partizioni definiscono un ambito per le transazioni (vedere la sezione più avanti in questo articolo, [le transazioni del gruppo di entità](#entity-group-transactions)) e costituiscono la base per la scalabilità dell'archiviazione tabelle. Per ulteriori informazioni sulle partizioni di tabella, vedere [elenco di controllo delle prestazioni e della scalabilità per l'archiviazione tabelle](../storage/tables/storage-performance-checklist.md).  

Nell'archivio tabelle, un singolo nodo esegue una o più partizioni complete e il servizio viene ridimensionato in modo dinamico tramite il bilanciamento del carico delle partizioni tra i nodi. Se un nodo è sotto carico, l'archivio tabelle può suddividere l'intervallo di partizioni gestite da tale nodo su nodi diversi. Quando si utilizza il traffico, l'archivio tabelle può unire gli intervalli di partizione dai nodi non interattiva a un singolo nodo.  

Per ulteriori informazioni sui dettagli interni dell'archiviazione tabelle e in particolare sulla modalità di gestione delle partizioni, vedere [archiviazione di Microsoft Azure: un servizio di archiviazione cloud a disponibilità elevata con coerenza](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)assoluta.  

### <a name="entity-group-transactions"></a>Transazioni di gruppi di entità
Nell'archivio tabelle, le transazioni del gruppo di entità (transazioni EGT) sono l'unico meccanismo incorporato per l'esecuzione di aggiornamenti atomici tra più entità. Transazioni EGT sono anche denominate *transazioni batch*. Transazioni EGT possono funzionare solo sulle entità archiviate nella stessa partizione (condividendo la stessa chiave di partizione in una determinata tabella), quindi ogni volta che è necessario un comportamento transazionale atomico tra più entità, assicurarsi che tali entità si trovino nella stessa partizione. Si tratta spesso di un motivo per mantenere più tipi di entità nella stessa tabella (e partizione) e non utilizzare più tabelle per tipi di entità diversi. Una sola EGT può agire al massimo su 100 entità.  Se si inviano più transazioni EGT simultanee per l'elaborazione, è importante assicurarsi che tali transazioni EGT non funzionino sulle entità comuni tra transazioni EGT. In caso contrario, si rischia di ritardare l'elaborazione.

Le transazioni EGT richiedono anche la valutazione di un potenziale compromesso nella progettazione. L'uso di più partizioni aumenta la scalabilità dell'applicazione, perché Azure offre più opportunità per il bilanciamento del carico delle richieste tra i nodi. Questo potrebbe tuttavia limitare la capacità dell'applicazione di eseguire transazioni atomiche e mantenere la coerenza assoluta per i dati. Ci sono inoltre specifici obiettivi di scalabilità a livello di partizione, che potrebbero limitare la velocità effettiva delle transazioni prevista per un singolo nodo.

Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione di Azure, vedere [obiettivi di scalabilità per gli account di archiviazione standard](../storage/common/scalability-targets-standard-account.md). Per ulteriori informazioni sugli obiettivi di scalabilità per l'archiviazione tabelle, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione tabelle](../storage/tables/scalability-targets.md). Le sezioni successive di questa guida illustrano diverse strategie di progettazione che aiutano a gestire compromessi come questo e illustrano il modo migliore per scegliere la chiave di partizione in base ai requisiti specifici dell'applicazione client.  

### <a name="capacity-considerations"></a>Considerazioni sulla capacità
Nella tabella seguente sono inclusi alcuni dei valori chiave da tenere presente quando si progetta una soluzione di archiviazione tabelle:  

| Capacità totale di un account di archiviazione di Azure | 500 TB |
| --- | --- |
| Numero di tabelle in un account di archiviazione di Azure |Limitato solo dalla capacità dell'account di archiviazione. |
| Numero di partizioni in una tabella |Limitato solo dalla capacità dell'account di archiviazione. |
| Numero di entità in una partizione |Limitato solo dalla capacità dell'account di archiviazione. |
| Dimensioni di una singola entità |Fino a 1 MB, con un massimo di 255 proprietà, tra cui `PartitionKey`, `RowKey`e `Timestamp`. |
| Dimensioni del`PartitionKey` |Stringa con dimensioni massime di 1 KB. |
| Dimensioni del`RowKey` |Stringa con dimensioni massime di 1 KB. |
| Dimensioni di una transazione del gruppo di entità |Una transazione può includere al massimo 100 entità e il payload deve avere una dimensione inferiore a 4 MB. Una transazione EGT può aggiornare una sola entità per volta. |

Per ulteriori informazioni, vedere informazioni [sul modello di dati del servizio tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerazioni sul costo
L'archiviazione tabelle è relativamente economica, ma è necessario includere stime dei costi sia per l'utilizzo della capacità sia per la quantità di transazioni come parte della valutazione di qualsiasi soluzione che utilizza l'archiviazione tabelle. In molti scenari, tuttavia, l'archiviazione di dati denormalizzati o duplicati per migliorare le prestazioni o la scalabilità della soluzione è un approccio valido da intraprendere. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Linee guida per la progettazione di tabelle
Questi elenchi riepilogano alcune delle linee guida principali da tenere presenti quando si progettano le tabelle. Questa guida li indirizza in modo più dettagliato in un secondo momento. Queste linee guida sono diverse dalle linee guida che vengono in genere seguite per la progettazione di database relazionali.  

Progettazione dell'archiviazione tabelle in modo che sia possibile *leggere* in modo efficiente:

* **Progettazione per le query nelle applicazioni con intensa attività di lettura.** Quando si progettano le tabelle, è opportuno valutare le query, in particolare quelle sensibili alla latenza, che verranno eseguite prima di considerare come aggiornare le entità. Ciò comporta in genere una soluzione efficiente e ad alte prestazioni.  
* **Specificare sia `PartitionKey` che `RowKey` nelle query.** Le query di tipo *punto* sono le query più efficienti nell'archiviazione tabelle.  
* **Prendere in considerazione l'archiviazione di copie duplicate delle entità.** Poiché l'archiviazione tabelle è economica, è consigliabile archiviare più volte la stessa entità (con chiavi diverse) per consentire query più efficienti.  
* **Considerare la denormalizzazione dei dati.** Poiché l'archiviazione tabelle è economica, è consigliabile denormalizzare i dati. Ad esempio, archiviare le entità di riepilogo in modo che le query per aggregare i dati debbano accedere a una singola entità.  
* **Usare valori chiave composti.** Le uniche chiavi disponibili sono `PartitionKey` e. `RowKey` Ad esempio, per abilitare percorsi alternativi per l'accesso con chiave alle entità, ad esempio, utilizzare valori chiave composti.  
* **Usare la proiezione di query.**  È possibile ridurre la quantità di dati trasferiti tramite la rete usando query che selezionano solo i campi necessari.  

Progettazione dell'archiviazione tabelle in modo che sia efficace per la *scrittura* :  

* **Non creare partizioni a caldo.**  Scegliere chiavi che consentono di distribuire le richieste tra più partizioni in qualsiasi momento.  
* **Evitare picchi di traffico.** Distribuire il traffico in un periodo di tempo ragionevole ed evitare picchi di traffico.
* **Non creare necessariamente una tabella separata per ogni tipo di entità.**  Quando è necessario eseguire transazioni atomiche tra diversi tipi di entità, è possibile archiviare questi tipi di entità nella stessa partizione della stessa tabella.
* **Considerare la velocità effettiva massima che è necessario raggiungere.** È necessario conoscere gli obiettivi di scalabilità per l'archiviazione tabelle e assicurarsi che la progettazione non ne provochi il superamento.  

Più avanti in questa guida, verranno illustrati alcuni esempi che inseriscono tutti questi principi in pratica.  

## <a name="design-for-querying"></a>Progettazione per le query
L'archiviazione tabelle può essere a elevato utilizzo di lettura, a elevato utilizzo di scrittura o a una combinazione dei due. Questa sezione Considera la progettazione per supportare le operazioni di lettura in modo efficiente. Una progettazione che supporta in modo efficiente le operazioni di lettura è in genere efficiente anche nelle operazioni di scrittura. Tuttavia, esistono considerazioni aggiuntive per la progettazione per supportare le operazioni di scrittura. Questi argomenti vengono descritti nella sezione successiva, [progettazione per la modifica dei dati](#design-for-data-modification).

Un valido punto di partenza per consentire di leggere i dati in modo efficiente consiste nel richiedere "quali query dovranno essere eseguite dall'applicazione per recuperare i dati necessari?"  

> [!NOTE]
> Con l'archiviazione tabelle, è importante fare in modo che la progettazione venga corretta in anticipo, perché è difficile e costosa modificarla in un secondo momento. Ad esempio, in un database relazionale è spesso possibile risolvere i problemi di prestazioni semplicemente aggiungendo indici a un database esistente. Questa opzione non è disponibile con l'archiviazione tabelle.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Come scegliere `PartitionKey` e `RowKey` influire sulle prestazioni delle query
Negli esempi seguenti si presuppone che l'archiviazione tabelle memorizzi le entità Employee con la struttura seguente (la maggior parte `Timestamp` degli esempi omette la proprietà per maggiore chiarezza):  

| Nome colonna | Tipo di dati |
| --- | --- |
| `PartitionKey`(Nome del reparto) |string |
| `RowKey`(ID dipendente) |string |
| `FirstName` |string |
| `LastName` |string |
| `Age` |Integer |
| `EmailAddress` |string |

Ecco alcune linee guida generali per la progettazione di query di archiviazione tabelle. La sintassi di filtro usata negli esempi seguenti è dall'API REST di archiviazione tabelle. Per altre informazioni, vedere [query Entities](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Una *query a punti* è la ricerca più efficiente da usare ed è consigliata per ricerche con volumi elevati o ricerche che richiedono la latenza più bassa. Una query di questo tipo può usare gli indici per individuare in modo efficiente una singola `PartitionKey` entità `RowKey` specificando entrambi i valori e. Ad esempio: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Il secondo migliore è una *query di intervallo*. USA i `PartitionKey`filtri, e su un intervallo di `RowKey` valori per restituire più di un'entità. Il `PartitionKey` valore identifica una partizione specifica e i `RowKey` valori identificano un subset delle entità in quella partizione. Ad esempio: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* La terza migliore è un' *analisi della partizione*. USA i `PartitionKey`filtri, e su un'altra proprietà non chiave e potrebbe restituire più di un'entità. Il `PartitionKey` valore identifica una partizione specifica e i valori di proprietà selezionano per un subset delle entità in quella partizione. Ad esempio: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Una *scansione di tabella* non include `PartitionKey`il e non è efficiente perché cerca tutte le partizioni che costituiscono la tabella per le entità corrispondenti. Esegue un'analisi della tabella indipendentemente dal fatto che il filtro usi o meno `RowKey`. Ad esempio: `$filter=LastName eq 'Jones'`.  
* Le query di `PartitionKey` archiviazione tabelle di Azure che restituiscono più entità `RowKey` vengono ordinate e ordinate. Per evitare di ricorrere alle entità nel client, scegliere un `RowKey` oggetto che definisce l'ordinamento più comune. I risultati della query restituiti dal API Tabella di Azure in Azure Cosmos DB non sono ordinati in base alla chiave di partizione o alla chiave di riga. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](table-api-faq.md#table-api-vs-table-storage).

L'uso di un "**or**" per specificare un filtro `RowKey` in base ai valori produce un'analisi della partizione e non viene considerato come una query di intervallo. Evitare pertanto query che utilizzano filtri come: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Per esempi di codice lato client che usano la libreria client di archiviazione per eseguire query efficienti, vedere:  

* [Eseguire una query a punti usando la libreria client di archiviazione](#run-a-point-query-by-using-the-storage-client-library)
* [Recuperare più entità tramite LINQ](#retrieve-multiple-entities-by-using-linq)
* [Proiezione lato server](#server-side-projection)  

Per esempi di codice lato client che possono gestire più tipi di entità archiviati nella stessa tabella, vedere:  

* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Scegliere un oggetto appropriato`PartitionKey`
La scelta di `PartitionKey` deve bilanciare la necessità di consentire l'uso di transazioni EGT (per garantire la coerenza) rispetto alla necessità di distribuire le entità tra più partizioni (per garantire una soluzione scalabile).  

In un'unica posizione, è possibile archiviare tutte le entità in un'unica partizione. Questo potrebbe tuttavia limitare la scalabilità della soluzione e impedire che l'archiviazione tabelle possa bilanciare il carico delle richieste. Nell'altra parte, è possibile archiviare un'entità per partizione. Si tratta di un'elevata scalabilità e consente all'archiviazione tabelle di bilanciare il carico delle richieste, ma impedisce di utilizzare le transazioni del gruppo di entità.  

Un ideale `PartitionKey` consente di usare query efficienti e dispone di partizioni sufficienti per garantire la scalabilità della soluzione. In genere, si noterà che le entità avranno una proprietà adatta che distribuisce le entità tra partizioni sufficienti.

> [!NOTE]
> Ad esempio, in un sistema in cui sono archiviate informazioni su utenti `UserID` o dipendenti, può `PartitionKey`essere un valido. È possibile che siano presenti più entità che usano `UserID` un particolare come chiave di partizione. Ogni entità che archivia i dati relativi a un utente viene raggruppata in un'unica partizione. Queste entità sono accessibili tramite transazioni EGT, garantendo al tempo stesso la scalabilità elevata.
> 
> 

È possibile scegliere tra ulteriori considerazioni relative alla `PartitionKey` modalità di inserimento, aggiornamento ed eliminazione delle entità. Per ulteriori informazioni, vedere [progettazione per la modifica dei dati](#design-for-data-modification) più avanti in questo articolo.  

### <a name="optimize-queries-for-table-storage"></a>Ottimizzare le query per l'archiviazione tabelle
L'archiviazione tabelle indicizza automaticamente le entità usando i `PartitionKey` valori `RowKey` e in un singolo indice cluster. Questo è il motivo per cui le query punto sono le più efficienti da usare. Tuttavia, non sono presenti indici diversi dall'indice cluster in `PartitionKey` e. `RowKey`

Molte progettazioni devono soddisfare alcuni requisiti per abilitare la ricerca di entità in base a più criteri, Ad esempio, individuazione delle entità Employee in base all'indirizzo di posta elettronica, all'ID del dipendente o al cognome. I modelli seguenti nella sezione [modelli di progettazione tabelle](#table-design-patterns) soddisfano questi tipi di requisiti. Vengono inoltre descritti i modi per aggirare il fatto che l'archiviazione tabelle non fornisce indici secondari.  

* [Modello di indice secondario intra-Partition](#intra-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .  
* [Modello di indice secondario tra partizioni](#inter-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori diversi `RowKey` in partizioni separate o in tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .  
* [Modello di entità index](#index-entities-pattern): mantenere le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

### <a name="sort-data-in-table-storage"></a>Ordinare i dati nell'archiviazione tabelle

L'archiviazione tabelle restituisce i risultati della query ordinati in ordine crescente, `PartitionKey` in base a `RowKey`e quindi da.

> [!NOTE]
> I risultati della query restituiti dal API Tabella di Azure in Azure Cosmos DB non sono ordinati in base alla chiave di partizione o alla chiave di riga. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](table-api-faq.md#table-api-vs-table-storage).

Le chiavi nell'archivio tabelle sono valori di stringa. Per assicurarsi che i valori numerici siano ordinati correttamente, è necessario convertirli in una lunghezza fissa e riempirli con zeri. Se, ad esempio, il valore ID dipendente usato come `RowKey` è un valore intero, è necessario convertire l'id dipendente **123** in **00000123**. 

In molte applicazioni è necessario usare i dati ordinandoli in modo diverso, ad esempio ordinando i dipendenti per nome o per data di assunzione. I modelli seguenti nella sezione [modelli di progettazione tabella](#table-design-patterns) si rivolgono alla modalità di ordinamento alternativo per le entità:  

* [Modello di indice secondario intra-Partition](#intra-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .  
* [Modello di indice secondario tra partizioni](#inter-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori diversi `RowKey` in partizioni separate in tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .
* [Modello](#log-tail-pattern)della parte finale del log: recuperare le *n* entità aggiunte più di recente a una partizione `RowKey` , usando un valore che ordina in ordine di data e ora inverso.  

## <a name="design-for-data-modification"></a>Progettazione per la modifica dei dati
Questa sezione esamina le considerazioni relative alla progettazione per ottimizzare inserimenti, aggiornamenti ed eliminazioni. In alcuni casi, è necessario valutare il compromesso tra le progettazioni ottimizzate per l'esecuzione di query su progetti che ottimizzano la modifica dei dati. Questa valutazione è analoga a quanto avviene nelle progettazioni per i database relazionali (anche se le tecniche per la gestione dei compromessi di progettazione sono diverse in un database relazionale). Nella sezione [modelli di progettazione tabelle](#table-design-patterns) vengono descritti alcuni modelli di progettazione dettagliati per l'archiviazione tabelle e vengono evidenziati alcuni di questi compromessi. In pratica, si noterà che molte progettazioni ottimizzate per l'esecuzione di query sulle entità funzionano bene anche per la modifica delle entità.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione
Per aggiornare o eliminare un'entità, è necessario essere in grado di identificarla usando i `PartitionKey` valori `RowKey` e. In questo senso, la scelta di `PartitionKey` e `RowKey` per modificare le entità deve seguire criteri simili a quelli scelti per supportare le query di punti. Si desidera identificare le entità nel modo più efficiente possibile. Non si vuole usare una partizione inefficiente o un'analisi di tabella per individuare un'entità per individuare i `PartitionKey` valori e `RowKey` necessari per aggiornarli o eliminarli.  

I modelli seguenti nella sezione [modelli di progettazione tabella](#table-design-patterns) si rivolgono a ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione:  

* [Modello di eliminazione volume elevato](#high-volume-delete-pattern): consentire l'eliminazione di un volume elevato di entità archiviando tutte le entità per l'eliminazione simultanea nella relativa tabella separata. Per eliminare le entità, eliminare la tabella.  
* [Modello di serie di dati](#data-series-pattern): consente di archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  
* [Modello di entità Wide](#wide-entities-pattern): usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  
* [Modello di entità di grandi dimensioni](#large-entities-pattern): usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Assicurare la coerenza nelle entità archiviate
L'altro aspetto importante che influisce sulla scelta delle chiavi per ottimizzare la modifica dei dati è come assicurare la coerenza usando le transazioni atomiche. È possibile usare una transazione EGT solo per agire sulle entità archiviate nella stessa partizione.  

I modelli seguenti nella sezione [modelli di progettazione tabella](#table-design-patterns) si rivolgono alla gestione della coerenza:  

* [Modello di indice secondario intra-Partition](#intra-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .  
* [Modello di indice secondario tra partizioni](#inter-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori diversi `RowKey` in partizioni separate o in tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .  
* [Modello di transazioni con coerenza](#eventually-consistent-transactions-pattern)finale: consentire un comportamento coerente tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.
* [Modello di entità index](#index-entities-pattern): mantenere le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  
* [Modello di denormalizzazione](#denormalization-pattern): combinare i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con una singola query di tipo punto.  
* [Modello di serie di dati](#data-series-pattern): consente di archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

Per ulteriori informazioni, vedere [transazioni di gruppi di entità](#entity-group-transactions) più avanti in questo articolo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Verificare che la capacità della progettazione per modifiche efficienti consenta query efficienti
In molti casi, una progettazione per query efficienti consente modifiche efficienti, ma è consigliabile valutare sempre se questa condizione si applica a uno specifico scenario. Alcuni dei modelli nella sezione modelli di [Progettazione tabella](#table-design-patterns) valutano in modo esplicito i compromessi tra l'esecuzione di query e la modifica delle entità ed è sempre necessario prendere in considerazione il numero di ogni tipo di operazione.  

I modelli seguenti nella sezione [modelli di progettazione tabella](#table-design-patterns) affrontano i compromessi tra la progettazione per query efficienti e la progettazione per una modifica efficace dei dati:  

* [Modello di chiave composta](#compound-key-pattern): usare `RowKey` i valori composti per consentire a un client di cercare dati correlati con una singola query di tipo punto.  
* [Modello](#log-tail-pattern)della parte finale del log: recuperare le *n* entità aggiunte più di recente a una partizione `RowKey` , usando un valore che ordina in ordine di data e ora inverso.  

## <a name="encrypt-table-data"></a>Crittografare i dati delle tabelle
La libreria client di archiviazione di Azure .NET supporta la crittografia delle proprietà dell'entità stringa per le operazioni di inserimento e sostituzione. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono convertite nuovamente in stringhe dopo la decrittografia.    

Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Specificare un `EncryptProperty` attributo (per le entità poco che derivano `TableEntity`da) o specificare un resolver di crittografia nelle opzioni di richiesta. Un resolver di crittografia è un delegato che accetta una chiave di partizione, una chiave di riga e un nome di proprietà e restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client utilizza queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura nella rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografare la proprietà A; in caso contrario, crittografare le proprietà A e B). Non è necessario fornire queste informazioni durante la lettura o l'esecuzione di query sulle entità.

L'Unione non è attualmente supportata. Poiché un subset di proprietà potrebbe essere stato crittografato in precedenza utilizzando una chiave diversa, è sufficiente unire le nuove proprietà e aggiornare i metadati comporterà la perdita di dati. Per eseguire l'Unione è necessario effettuare chiamate al servizio aggiuntive per leggere l'entità preesistente dal servizio o utilizzare una nuova chiave per ogni proprietà. Nessuno di questi è adatto per motivi di prestazioni.     

Per informazioni sulla crittografia dei dati della tabella, vedere [crittografia lato client e Azure Key Vault per archiviazione di Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relazioni tra modelli
La compilazione di modelli di dominio è un passaggio chiave della progettazione di sistemi complessi. Si usa in genere il processo di modellazione per identificare le entità e le relazioni tra di esse, per comprendere il dominio aziendale e informare la progettazione del sistema. Questa sezione è incentrata sul modo in cui è possibile tradurre alcuni dei tipi di relazione comuni presenti nei modelli di dominio per la progettazione dell'archiviazione tabelle. Il processo di mapping da un modello di dati logico a un modello di dati fisico basato su NoSQL è diverso da quello utilizzato durante la progettazione di un database relazionale. La progettazione di database relazionali presuppone in genere un processo di normalizzazione dei dati ottimizzato per ridurre al minimo la ridondanza. Tale progettazione presuppone inoltre una funzionalità di query dichiarativa che astrae l'implementazione del funzionamento del database.  

### <a name="one-to-many-relationships"></a>Relazioni uno a molti
Le relazioni uno a molti tra gli oggetti del dominio aziendale sono frequenti: ad esempio, un reparto ha più dipendenti. Esistono diversi modi per implementare relazioni uno-a-molti nell'archivio tabelle, ognuna con vantaggi e svantaggi che potrebbero essere rilevanti per lo scenario specifico.  

Si consideri l'esempio di un'azienda multinazionale di grandi dimensioni con decine di migliaia di reparti e entità Employee. Ogni reparto ha molti dipendenti e ogni dipendente è associato a un reparto specifico. Un approccio consiste nell'archiviare entità del reparto e dei dipendenti separate, come le seguenti:  

![Immagine che mostra un'entità Department e un'entità Employee][1]

In questo esempio viene illustrata una relazione uno-a-molti implicita tra i tipi `PartitionKey` , in base al valore. Ogni reparto può avere più dipendenti.  

Questo esempio mostra anche un'entità reparto e le relative entità dipendente nella stessa partizione. È possibile scegliere di usare partizioni, tabelle o persino account di archiviazione diversi per i diversi tipi di entità.  

Un approccio alternativo consiste nel denormalizzare i dati e archiviare solo le entità Employee con dati di reparto denormalizzati, come illustrato nell'esempio seguente. In questo scenario specifico, questo approccio denormalizzato potrebbe non essere il migliore se si dispone di un requisito per poter modificare i dettagli di un responsabile del reparto. A tale scopo, è necessario aggiornare tutti i dipendenti del reparto.  

![Rappresentazione grafica dell'entità Employee][2]

Per ulteriori informazioni, vedere il [Modello di denormalizzazione](#denormalization-pattern) più avanti in questa guida.  

Nella tabella seguente vengono riepilogati i vantaggi e gli svantaggi di ciascuno degli approcci per l'archiviazione di entità Employee e Department con una relazione uno-a-molti. Si consiglia inoltre di valutare la frequenza con cui si prevede di eseguire varie operazioni. Potrebbe essere accettabile avere una progettazione che includa un'operazione costosa se tale operazione viene eseguita raramente.  

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
<li>Potrebbe essere necessario recuperare sia un dipendente che un'entità Department per alcune attività del client.</li>
<li>Le operazioni di archiviazione vengono eseguite nella stessa partizione. Con volumi di transazioni elevati, questo può causare un hotspot.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto usando un transazione EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipi di entità distinti, partizioni, tabelle o account di archiviazione diversi</td>
<td>
<ul>
<li>È possibile aggiornare un'entità reparto o un'entità dipendente con un'unica operazione.</li>
<li>Con volumi di transazioni elevati, questo può contribuire a suddividere il carico tra più partizioni.</li>
</ul>
</td>
<td>
<ul>
<li>Potrebbe essere necessario recuperare sia un dipendente che un'entità Department per alcune attività del client.</li>
<li>Non è possibile usare transazioni EGT per mantenere la coerenza quando si aggiorna o si inserisce o si elimina un dipendente e si aggiorna un reparto. ad esempio quando si aggiorna un conteggio dipendenti in un'entità reparto.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto usando un transazione EGT.</li>
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
<li>Se è necessario aggiornare le informazioni del reparto, può essere costoso mantenere la coerenza, per cui è necessario aggiornare tutti i dipendenti di un reparto.</li>
</ul>
</td>
</tr>
</table>

Il modo in cui si scelgono tra queste opzioni e quali vantaggi e svantaggi sono più significativi dipende dagli scenari specifici dell'applicazione. Ad esempio, con quale frequenza si modificano le entità Department? Per tutte le query dei dipendenti sono necessarie informazioni aggiuntive sul reparto? Quanto sono vicini i limiti di scalabilità per le partizioni o l'account di archiviazione?  

### <a name="one-to-one-relationships"></a>Relazioni uno a uno
I modelli di dominio possono includere relazioni uno-a-uno tra le entità. Se è necessario implementare una relazione uno-a-uno nell'archivio tabelle, è inoltre necessario scegliere come collegare le due entità correlate quando è necessario recuperarle entrambe. Questo collegamento può essere implicito, in base a una convenzione nei valori chiave oppure esplicito, archiviando un collegamento sotto forma di `PartitionKey` valori e `RowKey` in ogni entità nell'entità correlata. Per informazioni utili a stabilire se archiviare le entità correlate nella stessa partizione, vedere la sezione [Relazioni uno a molti](#one-to-many-relationships).  

Sono inoltre disponibili considerazioni sull'implementazione che potrebbero consentire di implementare relazioni uno-a-uno nell'archivio tabelle:  

* Gestione di entità di grandi dimensioni (per altre informazioni, vedere [modello di entità di grandi dimensioni](#large-entities-pattern)).  
* Implementazione dei controlli di accesso (per altre informazioni, vedere [controllare l'accesso con firme di accesso condiviso](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Join nel client
Sebbene esistano modi per modellare le relazioni nell'archivio tabelle, non dimenticare che i due motivi principali per l'uso dell'archiviazione tabelle sono la scalabilità e le prestazioni. Se si ritiene che si stiano modellando molte relazioni che compromettono le prestazioni e la scalabilità della soluzione, è necessario chiedersi se è necessario creare tutte le relazioni tra i dati nella progettazione della tabella. Si potrebbe essere in grado di semplificare la progettazione e migliorare la scalabilità e le prestazioni della soluzione, se si lascia che l'applicazione client esegua tutti i join necessari.  

Se, ad esempio, si dispone di tabelle di piccole dimensioni che contengono dati che non cambiano spesso, è possibile recuperare i dati una sola volta e memorizzarli nella cache sul client. Questo consente di evitare round trip ripetuti per il recupero degli stessi dati. Negli esempi esaminati in questa guida, il set di reparti in un'organizzazione di piccole dimensioni è probabilmente ridotto e raramente modificato. Questo rende un candidato ideale per i dati che un'applicazione client può scaricare una sola volta e memorizzare nella cache come dati di ricerca.  

### <a name="inheritance-relationships"></a>Relazioni di ereditarietà
Se l'applicazione client utilizza un set di classi che fanno parte di una relazione di ereditarietà per rappresentare le entità aziendali, è possibile salvare facilmente tali entità nell'archivio tabelle. È ad esempio possibile che nell'applicazione client sia definito il seguente set di classi, dove `Person` è una classe astratta.

![Diagramma delle relazioni di ereditarietà][3]

È possibile salvare in maniera permanente le istanze delle due classi concrete nell'archivio tabelle `Person` utilizzando una singola tabella. Usare le entità che hanno un aspetto simile al seguente:  

![Grafica che mostra l'entità cliente e l'entità Employee][4]

Per ulteriori informazioni sull'utilizzo di più tipi di entità nella stessa tabella del codice client, vedere la pagina relativa all' [utilizzo di tipi di entità eterogenei](#work-with-heterogeneous-entity-types) più avanti in questa guida. In questa sezione sono disponibili esempi su come riconoscere il tipo di entità nel codice del client.  

## <a name="table-design-patterns"></a>Modelli di progettazione tabella
Nelle sezioni precedenti si è appreso come ottimizzare la progettazione della tabella sia per il recupero dei dati di entità tramite query sia per l'inserimento, l'aggiornamento e l'eliminazione di dati di entità. Questa sezione descrive alcuni modelli appropriati per l'uso con l'archiviazione tabelle. Inoltre, verrà illustrato come è possibile affrontare praticamente alcuni dei problemi e dei compromessi generati in precedenza in questa guida. Il diagramma seguente riepiloga le relazioni tra i diversi modelli:  

![Diagramma di modelli di progettazione tabelle][5]

La mappa dei criteri evidenzia alcune relazioni tra i modelli (blu) e gli anti-pattern (arancione) documentati in questa guida. Ovviamente esistono molti altri modelli utili. Uno degli scenari chiave per l'archiviazione tabelle, ad esempio, consiste nell'usare il [modello di vista materializzata](https://msdn.microsoft.com/library/azure/dn589782.aspx) del modello di separazione di responsabilità per le [query di comando](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archiviare più copie di ogni entità usando valori diversi `RowKey` (nella stessa partizione). Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` . Gli aggiornamenti tra le copie possono essere mantenuti coerenti usando transazioni EGT.  

#### <a name="context-and-problem"></a>Contesto e problema
L'archivio tabelle indicizza automaticamente le entità usando `PartitionKey` i `RowKey` valori e. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente usando questi valori. Ad esempio, usando la struttura di tabella seguente, un'applicazione client può usare una query di punto per recuperare una singola entità Employee usando il nome del reparto e l'ID del `PartitionKey` dipendente `RowKey` (i valori e). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.

![Rappresentazione grafica dell'entità Employee][6]

Se si vuole anche trovare un'entità dipendente in base al valore di un'altra proprietà, ad esempio indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Questo perché l'archiviazione tabelle non fornisce indici secondari. Non è inoltre possibile richiedere un elenco di dipendenti ordinati in un ordine diverso rispetto `RowKey` all'ordine.  

#### <a name="solution"></a>Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità, ciascuna copia usando un valore `RowKey` diverso. Se si archivia un'entità con le strutture seguenti, è possibile recuperare in modo efficiente le entità Employee in base all'indirizzo di posta elettronica o all'ID dipendente. I valori di prefisso `RowKey`per `empid_`, e `email_` consentono di eseguire una query per un singolo dipendente o per un intervallo di dipendenti, usando un intervallo di indirizzi di posta elettronica o ID dipendente.  

![Grafico che mostra l'entità Employee con valori RowKey variabili][7]

I due criteri di filtro seguenti (uno che cerca in base all'ID del dipendente e uno che cerca in base all'indirizzo di posta elettronica) specificano entrambe le query Point:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223)  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se si esegue una query per un intervallo di entità Employee, è possibile specificare un intervallo ordinato in ordine ID dipendente oppure un intervallo ordinato in ordine di indirizzo di posta elettronica. Eseguire una `RowKey`query per le entità con il prefisso appropriato in.  

* Per trovare tutti i dipendenti del reparto vendite con un ID dipendente compreso tra 000100 e 000199, usare: $filter = (PartitionKey EQ ' Sales ') e (RowKey GE ' empid_000100') e (RowKey le ' empid_000199')  
* Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con la lettera "a", usare: $filter = (PartitionKey EQ ' Sales ') e (RowKey GE ' email_a ') e (RowKey lt ' email_b ')  
  
La sintassi di filtro usata negli esempi precedenti è dall'API REST di archiviazione tabelle. Per altre informazioni, vedere [query Entities](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* L'uso dell'archiviazione tabelle è relativamente economico, quindi il sovraccarico dei costi di archiviazione dei dati duplicati non dovrebbe essere un problema importante. Tuttavia, è consigliabile valutare sempre il costo della progettazione in base ai requisiti di archiviazione previsti e aggiungere solo entità duplicate per supportare le query che l'applicazione client eseguirà.  
* Poiché le entità di indice secondario vengono archiviate nella stessa partizione delle entità originali, assicurarsi di non superare gli obiettivi di scalabilità per una singola partizione.  
* Per mantenere la coerenza tra entità duplicate è possibile usare transazioni ETG, che consentono di aggiornare le due copie dell'entità in modo atomico. A questo scopo è necessario archiviare tutte le copie di un'entità nella stessa partizione. Per ulteriori informazioni, vedere [utilizzare le transazioni del gruppo di entità](#entity-group-transactions).  
* Il valore utilizzato per `RowKey` deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* La spaziatura interna dei `RowKey` valori numerici in (ad esempio, l'ID dipendente 000223) consente l'ordinamento e il filtro corretti in base ai limiti superiore e inferiore.  
* Non è necessariamente necessario duplicare tutte le proprietà dell'entità. Se, ad esempio, le query che cercano le entità usando l'indirizzo di posta elettronica in `RowKey` non hanno mai bisogno dell'età del dipendente, queste entità possono avere la struttura seguente:

  ![Rappresentazione grafica dell'entità Employee][8]

* In genere, è preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query, anziché usare una query per individuare un'entità e un'altra per cercare i dati necessari.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando:

- L'applicazione client deve recuperare le entità usando un'ampia gamma di chiavi diverse.
- Il client deve recuperare le entità in ordinamenti diversi.
- È possibile identificare ogni entità usando un'ampia gamma di valori univoci.

Tuttavia, assicurarsi di non superare i limiti di scalabilità della partizione quando si eseguono ricerche di entità usando i diversi `RowKey` valori.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per indice secondario interpartizione](#inter-partition-secondary-index-pattern)
* [Modello per chiave composta](#compound-key-pattern)
* [Transazioni di gruppi di entità](#entity-group-transactions)
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archiviare più copie di ogni entità usando valori diversi `RowKey` in partizioni separate o in tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi usando valori diversi `RowKey` .  

#### <a name="context-and-problem"></a>Contesto e problema
L'archivio tabelle indicizza automaticamente le entità usando `PartitionKey` i `RowKey` valori e. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente usando questi valori. Ad esempio, usando la struttura di tabella seguente, un'applicazione client può usare una query di punto per recuperare una singola entità Employee usando il nome del reparto e l'ID del `PartitionKey` dipendente `RowKey` (i valori e). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.  

![Rappresentazione grafica dell'entità Employee][9]

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Questo perché l'archiviazione tabelle non fornisce indici secondari. Non è inoltre possibile richiedere un elenco di dipendenti ordinati in un ordine diverso rispetto `RowKey` all'ordine.  

Si prevede un volume elevato di transazioni per queste entità e si vuole ridurre al minimo il rischio di limitazione della frequenza di archiviazione delle tabelle per il client.  

#### <a name="solution"></a>Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità, ciascuna copia con valori `PartitionKey` e `RowKey` diversi. Se si archivia un'entità con le strutture seguenti, è possibile recuperare in modo efficiente le entità Employee in base all'indirizzo di posta elettronica o all'ID dipendente. I valori di prefisso `PartitionKey`per `empid_`, e `email_` consentono di identificare l'indice che si desidera utilizzare per una query.  

![Grafico che mostra l'entità Employee con indice primario e entità Employee con indice secondario][10]

I due criteri di filtro seguenti (uno che cerca in base all'ID del dipendente e uno che cerca in base all'indirizzo di posta elettronica) specificano entrambe le query Point:  

* $filter=(PartitionKey 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se si esegue una query per un intervallo di entità Employee, è possibile specificare un intervallo ordinato in ordine ID dipendente oppure un intervallo ordinato in ordine di indirizzo di posta elettronica. Eseguire una `RowKey`query per le entità con il prefisso appropriato in.  

* Per trovare tutti i dipendenti del reparto vendite con un ID dipendente compreso tra **000100** e **000199**, ordinati in ordine ID dipendente, usare: $Filter = (PartitionKey EQ ' empid_Sales ') e (RowKey GE ' 000100') e (RowKey le ' 000199')  
* Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con "a", ordinati in ordine di indirizzo di posta elettronica, usare: $filter = (PartitionKey EQ ' email_Sales ') e (RowKey GE ' a') e (RowKey lt ' b ')  

Si noti che la sintassi di filtro usata negli esempi precedenti è dall'API REST di archiviazione tabelle. Per altre informazioni, vedere [query Entities](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra le entità duplicate, è possibile usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per gestire le entità di indice primario e secondario.  
* L'uso dell'archiviazione tabelle è relativamente economico, quindi il sovraccarico dei costi di archiviazione di dati duplicati non dovrebbe essere un problema importante. Tuttavia, valutare sempre il costo della progettazione in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che l'applicazione client eseguirà.  
* Il valore utilizzato per `RowKey` deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* La spaziatura interna dei `RowKey` valori numerici in (ad esempio, l'ID dipendente 000223) consente l'ordinamento e il filtro corretti in base ai limiti superiore e inferiore.  
* Non è necessariamente necessario duplicare tutte le proprietà dell'entità. Se, ad esempio, le query che cercano le entità usando l'indirizzo di posta elettronica in `RowKey` non hanno mai bisogno dell'età del dipendente, queste entità possono avere la struttura seguente:
  
  ![Grafico che mostra l'entità Employee con indice secondario][11]
* In genere, è preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query, anziché usare una query per individuare un'entità usando l'indice secondario e un altro per cercare i dati necessari nell'indice primario.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando:

- L'applicazione client deve recuperare le entità usando un'ampia gamma di chiavi diverse.
- Il client deve recuperare le entità in ordinamenti diversi.
- È possibile identificare ogni entità usando un'ampia gamma di valori univoci.

Usare questo modello quando si vuole evitare il superamento dei limiti di scalabilità della partizione quando si eseguono ricerche di entità usando i `RowKey` diversi valori.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern)  
* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Modello per transazioni con coerenza finale
abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.  

#### <a name="context-and-problem"></a>Contesto e problema
Le transazioni ETG consentono l'esecuzione di transazioni atomiche tra più entità che condividono la stessa chiave di partizione. Per motivi di prestazioni e scalabilità, è possibile scegliere di archiviare entità con requisiti di coerenza in partizioni separate o in un sistema di archiviazione separato. In uno scenario di questo tipo, non è possibile usare transazioni EGT per mantenere la coerenza. Ad esempio, potrebbe essere necessario mantenere la coerenza finale tra:  

* Entità archiviate in due partizioni diverse nella stessa tabella, in tabelle diverse o in account di archiviazione diversi.  
* Un'entità archiviata nell'archivio tabelle e un BLOB archiviato nell'archivio BLOB.  
* Un'entità archiviata nell'archivio tabelle e un file in un file system.  
* Un'entità archiviata nell'archivio tabelle, ma indicizzata con ricerca cognitiva di Azure.  

#### <a name="solution"></a>Soluzione
Usando le code di Azure, è possibile implementare una soluzione che offre coerenza finale tra due o più partizioni o sistemi di archiviazione.

Per illustrare questo approccio, si supponga di avere il requisito di poter archiviare le entità Employee precedenti. Le precedenti entità Employee vengono raramente sottoposte a query e devono essere escluse da qualsiasi attività che gestisca i dipendenti correnti. Per implementare questo requisito, è possibile archiviare i dipendenti attivi nella tabella **corrente** e i dipendenti precedenti della tabella di **archiviazione** . Per archiviare un dipendente è necessario eliminare l'entità dalla tabella **corrente** e aggiungere l'entità alla tabella di **archiviazione** .

Tuttavia, non è possibile usare un transazione EGT per eseguire queste due operazioni. Per evitare il rischio che, a causa di un errore, un'entità venga visualizzata in entrambe le tabelle o in nessuna di esse, l'operazione di archiviazione deve garantire la coerenza finale. Il diagramma seguente illustra in sequenza i passaggi di questa operazione.  

![Diagramma della soluzione ai fini della coerenza finale][12]

Un client avvia l'operazione di archiviazione inserendo un messaggio in una coda di Azure (in questo esempio, per archiviare #456 dei dipendenti). Un ruolo di lavoro esegue il polling della coda per individuare i nuovi messaggi. Quando ne trova uno, legge il messaggio e lascia una copia nascosta nella coda. Successivamente, il ruolo di lavoro recupera una copia dell'entità dalla tabella dei dipendenti **Correnti**, inserisce una copia nella tabella dei dipendenti **Archiviati** e quindi elimina l'originale dalla tabella dei dipendenti **Correnti**. Infine, se nei passaggi precedenti non si sono verificati errori, il ruolo di lavoro elimina il messaggio nascosto dalla coda.  

In questo esempio, il passaggio 4 del diagramma inserisce il dipendente nella tabella di **archiviazione** . Può aggiungere il dipendente a un BLOB nell'archivio BLOB o un file in un file system.  

#### <a name="recover-from-failures"></a>Ripristino da errori
È importante che le operazioni nei passaggi 4-5 nel diagramma siano *idempotente* nel caso in cui il ruolo di lavoro debba riavviare l'operazione di archiviazione. Se si usa l'archiviazione tabelle, per il passaggio 4 è necessario usare un'operazione di inserimento o sostituzione. per il passaggio 5, è consigliabile usare un'operazione "Elimina se esistente" nella libreria client che si sta usando. Se si usa un altro sistema di archiviazione, è necessario usare un'operazione idempotente appropriata.  

Se il ruolo di lavoro non completa mai il passaggio 6 nel diagramma, dopo un timeout il messaggio viene nuovamente visualizzato nella coda pronto affinché il ruolo di lavoro tenti di rielaborarlo. Il ruolo di lavoro può controllare il numero di volte in cui un messaggio nella coda è stato letto e, se necessario, contrassegnarlo come messaggio "non elaborabile" per l'analisi inviando il messaggio a una coda separata. Per ulteriori informazioni sulla lettura dei messaggi della coda e la verifica del numero di rimozione dalla coda, vedere [Get messages](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alcuni errori dall'archiviazione tabelle e dall'archiviazione code sono errori temporanei e l'applicazione client deve includere la logica di ripetizione dei tentativi appropriata per gestirli.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione non fornisce l'isolamento delle transazioni. Un client, ad esempio, potrebbe leggere le tabelle **correnti** e di **archiviazione** quando il ruolo di lavoro era compreso tra i passaggi 4-5 nel diagramma e visualizzare una visualizzazione non coerente dei dati. Alla fine i dati saranno coerenti.  
* È necessario assicurarsi che i passaggi 4-5 siano idempotente per garantire la coerenza finale.  
* È possibile ridimensionare la soluzione usando più code e istanze del ruolo di lavoro.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si desidera garantire la coerenza finale tra entità esistenti in tabelle o partizioni diverse. È possibile estendere questo modello per garantire la coerenza finale per le operazioni nell'archiviazione tabelle e nell'archiviazione BLOB e in altre origini dati di archiviazione non di Azure, ad esempio un database o la file system.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Unione o sostituzione](#merge-or-replace)  

> [!NOTE]
> Se l'isolamento delle transazioni è importante per la soluzione, provare a riprogettare le tabelle per consentire l'uso di transazioni EGT.  
> 
> 

### <a name="index-entities-pattern"></a>Modello per entità di indice
mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

#### <a name="context-and-problem"></a>Contesto e problema
L'archivio tabelle indicizza automaticamente le entità usando `PartitionKey` i `RowKey` valori e. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente utilizzando una query di punto. Ad esempio, usando la struttura di tabella seguente, un'applicazione client può recuperare in modo efficiente una singola entità Employee usando il nome del reparto e l'ID `PartitionKey` del `RowKey`dipendente (e).  

![Rappresentazione grafica dell'entità Employee][13]

Se si vuole anche recuperare un elenco di entità Employee in base al valore di un'altra proprietà non univoca, ad esempio il cognome, è necessario usare un'analisi della partizione meno efficiente. Questa analisi rileva le corrispondenze, anziché usare un indice per cercarle direttamente. Questo perché l'archiviazione tabelle non fornisce indici secondari.  

#### <a name="solution"></a>Soluzione
Per abilitare la ricerca in base al cognome con la struttura di entità precedente, è necessario gestire gli elenchi di ID dipendente. Se si desidera recuperare le entità Employee con un cognome specifico, ad esempio Jones, è necessario innanzitutto individuare l'elenco di ID dipendente per i dipendenti con Jones come cognome e recuperare tali entità Employee. Sono disponibili tre opzioni principali per archiviare gli elenchi di ID dipendente:  

* Usare l'archiviazione BLOB.  
* Creare entità di indice nella stessa partizione delle entità dipendente.  
* Creare entità di indice in una tabella o una partizione separata.  

Opzione 1: usare l'archiviazione BLOB  

Creare un BLOB per ogni cognome univoco e in ogni BLOB archiviare un elenco dei valori `PartitionKey` (Department) e `RowKey` (ID dipendente) per i dipendenti con il cognome. Quando si aggiunge o si elimina un dipendente, verificare che il contenuto del BLOB pertinente sia coerente con le entità Employee.  

Opzione 2: creare entità di indice nella stessa partizione  

Usare le entità index che archiviano i dati seguenti:  

![Grafico che mostra l'entità Employee, con una stringa contenente un elenco di ID dipendente con lo stesso cognome][14]

La `EmployeeIDs` proprietà contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in `RowKey`.  

I passaggi seguenti illustrano il processo da seguire quando si aggiunge un nuovo dipendente. In questo esempio viene aggiunto un dipendente con ID 000152 e cognome Jones nel reparto vendite:  

1. Recuperare l'entità index con un `PartitionKey` valore "Sales" e il `RowKey` valore "Jones". Salvare il valore ETag dell'entità per usarlo nel passaggio 2.  
2. Creare una transazione del gruppo di entità, ovvero un'operazione batch, che inserisca la nuova entità`PartitionKey` Employee (valore "Sales `RowKey` " e value "000152") e aggiorni l'entità`PartitionKey` index (valore "Sales `RowKey` " e value "Jones"). Il transazione EGT esegue questa operazione aggiungendo il nuovo ID dipendente all'elenco nel campo EmployeeIDs. Per ulteriori informazioni su transazioni EGT, vedere [transazioni del gruppo di entità](#entity-group-transactions).  
3. Se il transazione EGT ha esito negativo a causa di un errore di concorrenza ottimistica (ovvero, un altro utente ha modificato l'entità index), è necessario ricominciare al passaggio 1.  

È possibile usare un approccio simile per eliminare un dipendente se si usa la seconda opzione. Modificare il cognome di un dipendente è leggermente più complesso, perché è necessario eseguire un transazione EGT che aggiorna tre entità: l'entità Employee, l'entità index per il cognome precedente e l'entità index per il nuovo cognome. Prima di apportare qualsiasi modifica, è necessario recuperare ogni entità, per recuperare i valori ETag che è possibile utilizzare per eseguire gli aggiornamenti utilizzando la concorrenza ottimistica.  

I passaggi seguenti illustrano il processo da seguire quando è necessario cercare tutti i dipendenti con un cognome specifico in un reparto. In questo esempio vengono cercati tutti i dipendenti con il cognome Jones nel reparto vendite:  

1. Recuperare l'entità index con un `PartitionKey` valore "Sales" e il `RowKey` valore "Jones".  
2. Analizza l'elenco di ID dipendente nel `EmployeeIDs` campo.  
3. Se sono necessarie informazioni aggiuntive su ognuno di questi dipendenti (ad esempio, gli indirizzi di posta elettronica), recuperare ogni entità Employee usando `PartitionKey` il valore "Sales" e `RowKey` i valori dell'elenco dei dipendenti ottenuti nel passaggio 2.  

Opzione 3: creare entità di indice in una partizione o in una tabella separata  

Per questa opzione, usare entità di indice che archiviano i dati seguenti:  

![Grafico che mostra l'entità Employee, con una stringa contenente un elenco di ID dipendente con lo stesso cognome][15]

La `EmployeeIDs` proprietà contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in `RowKey`.  

Non è possibile usare transazioni EGT per mantenere la coerenza, poiché le entità di indice si trovano in una partizione separata rispetto alle entità Employee. Verificare che le entità di indice siano coerenti con le entità Employee.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione richiede almeno due query per recuperare le entità corrispondenti: una per eseguire una query sulle entità di indice per ottenere `RowKey` l'elenco di valori e quindi le query per recuperare ogni entità nell'elenco.  
* Poiché una singola entità ha una dimensione massima di 1 MB, l'opzione 2 e l'opzione 3 nella soluzione presuppongono che l'elenco di ID dipendente per un determinato cognome non sia mai superiore a 1 MB. Se è probabile che l'elenco di ID dipendente superi le dimensioni di 1 MB, usare l'opzione 1 e archiviare i dati dell'indice nell'archivio BLOB.  
* Se si usa l'opzione 2 (usando transazioni EGT per gestire l'aggiunta e l'eliminazione di dipendenti e la modifica del cognome di un dipendente), è necessario valutare se il volume delle transazioni affronterà i limiti di scalabilità in una determinata partizione. In tal caso, è consigliabile prendere in considerazione una soluzione coerente (opzione 1 o 3). Questi utilizzano le code per gestire le richieste di aggiornamento e consentono di archiviare le entità di indice in una partizione separata dalle entità Employee.  
* Con l'opzione 2 di questa soluzione si presuppone che si desideri cercare in base al cognome all'interno di un reparto. Si desidera, ad esempio, recuperare un elenco di dipendenti il cui cognome è Jones nel reparto vendite. Se si vuole essere in grado di cercare tutti i dipendenti con il cognome Jones nell'intera organizzazione, usare l'opzione 1 o l'opzione 3.
* È possibile implementare una soluzione basata su coda che offre coerenza finale. Per altri dettagli, vedere il [modello di transazioni con coerenza](#eventually-consistent-transactions-pattern)finale.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Utilizzare questo modello quando si desidera cercare un set di entità che condividono tutti un valore di proprietà comune, ad esempio tutti i dipendenti con il cognome Jones.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Modello di denormalizzazione
combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.  

#### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale, in genere si normalizzano i dati per rimuovere la duplicazione che si verifica quando le query recuperano dati da più tabelle. Se si normalizzano i dati nelle tabelle di Azure, è necessario eseguire più round trip dal client al server per recuperare i dati correlati. Con la struttura di tabella seguente, ad esempio, sono necessari due round trip per recuperare i dettagli per un reparto. Un viaggio recupera l'entità Department che include l'ID del Manager e la seconda corsa recupera i dettagli del Manager in un'entità Employee.  

![Rappresentazione grafica dell'entità Department e dell'entità Employee][16]

#### <a name="solution"></a>Soluzione
Anziché archiviare i dati in due entità separate, denormalizzare i dati e conservare una copia dei dettagli sul manager nell'entità reparto. Ad esempio:  

![Rappresentazione grafica dell'entità denormalizzata e combinata del reparto][17]

Con le entità Department archiviate con queste proprietà, è ora possibile recuperare tutti i dettagli necessari su un reparto usando una query Point.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Archiviare alcuni dati due volte comporta un aumento dei costi. Il vantaggio in merito alle prestazioni risultante da un minor numero di richieste all'archiviazione tabelle in genere supera l'aumento marginale dei costi di archiviazione. Questo costo è parzialmente offset in base a una riduzione del numero di transazioni necessarie per recuperare i dettagli di un reparto.  
* È necessario mantenere la coerenza tra le due entità in cui sono archiviate le informazioni sui manager. È possibile gestire il problema di coerenza utilizzando transazioni EGT per aggiornare più entità in un'unica transazione atomica. In questo caso, l'entità Department e l'entità Employee per Department Manager vengono archiviati nella stessa partizione.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario cercare spesso informazioni correlate. Questo modello riduce il numero di query che il client deve eseguire per recuperare i dati necessari.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Modello per chiave composta
Usare valori `RowKey` composti per consentire a un client di cercare dati correlati con una singola query di un punto.  

#### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale è naturale usare i join nelle query per restituire le parti di dati correlate al client in un'unica query. Ad esempio, si può usare l'ID dipendente per cercare un elenco di entità correlate che contengono i dati relativi alle prestazioni e alle valutazioni per tale dipendente.  

Si supponga di archiviare le entità Employee nell'archivio tabelle usando la struttura seguente:  

![Rappresentazione grafica dell'entità Employee][18]

È anche necessario archiviare i dati cronologici relativi alle revisioni e alle prestazioni per ogni anno che il dipendente ha lavorato per la propria organizzazione ed è necessario essere in grado di accedere a queste informazioni in base all'anno. Una possibilità consiste nel creare un'altra tabella di archiviazione delle entità con la struttura seguente:  

![Rappresentazione grafica dell'entità di revisione dei dipendenti][19]

Con questo approccio, è possibile scegliere di duplicare alcune informazioni, ad esempio il nome e il cognome, nella nuova entità per consentire di recuperare i dati con una singola richiesta. Tuttavia, non è possibile mantenere la coerenza assoluta perché non è possibile usare un transazione EGT per aggiornare le due entità in modo atomico.  

#### <a name="solution"></a>Soluzione
Archiviare un nuovo tipo di entità nella tabella originale usando le entità con la struttura seguente:  

![Rappresentazione grafica dell'entità Employee con chiave composta][20]

Si noti che `RowKey` il è ora una chiave composta, costituita dall'ID del dipendente e dall'anno dei dati di revisione. In questo modo è possibile recuperare le prestazioni del dipendente ed esaminare i dati con una singola richiesta per una singola entità.  

L'esempio seguente illustra come recuperare tutti i dati di valutazione per uno specifico dipendente (ad esempio il dipendente 000123 del reparto vendite):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È consigliabile usare un carattere separatore adatto che semplifica l'analisi del `RowKey` valore, ad esempio **000123_2012**.  
* Si sta anche archiviando questa entità nella stessa partizione di altre entità che contengono dati correlati per lo stesso dipendente. Ciò significa che è possibile usare transazioni EGT per mantenere la coerenza assoluta.
* È necessario considerare la frequenza con cui si esegue una query sui dati per determinare se questo modello è appropriato. Ad esempio, se si accede raramente ai dati di revisione e i dati principali del dipendente spesso, è consigliabile mantenerli come entità separate.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare una o più entità correlate su cui si eseguono query frequenti.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Modello della parte finale del log
Recuperare le entità *n* aggiunte più di recente a una partizione usando un `RowKey` valore che ordina in ordine di data e ora inverso.  

> [!NOTE]
> I risultati della query restituiti dal API Tabella di Azure in Azure Cosmos DB non sono ordinati in base alla chiave di partizione o alla chiave di riga. Pertanto, anche se questo modello è adatto per l'archiviazione tabelle, non è adatto per Azure Cosmos DB. Per un elenco dettagliato delle differenze tra le funzionalità, vedere [differenze tra API tabella in Azure Cosmos DB e nell'archiviazione tabelle di Azure](table-api-faq.md#table-api-vs-table-storage).

#### <a name="context-and-problem"></a>Contesto e problema
Un requisito comune è poter recuperare le entità create più di recente, ad esempio le ultime dieci note di rimborso spese inviate da un dipendente. Le query di tabella `$top` supportano un'operazione di query per restituire le prime *n* entità da un set. Non esiste un'operazione di query equivalente per la restituzione delle ultime *n* entità di un set.  

#### <a name="solution"></a>Soluzione
Archiviare le entità usando un oggetto `RowKey` che ordina naturalmente in ordine di data/ora inverso, quindi la voce più recente è sempre la prima nella tabella.  

Ad esempio, per poter recuperare le ultime dieci note di rimborso spese inviate da un dipendente, è possibile usare un valore di tick inverso derivato dal valore di data/ora corrente. Nell'esempio di codice C# riportato di seguito viene illustrato un modo per creare un valore di "cicli invertiti `RowKey` " adatto per un oggetto che ordina dal più recente al meno recente:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

È possibile tornare al valore data/ora usando il codice seguente:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

La query sulla tabella ha un aspetto simile al seguente:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per assicurarsi che il valore della stringa venga ordinato come previsto, è necessario riempire il valore del segno inverso con zeri iniziali.  
* È necessario tenere presenti gli obiettivi di scalabilità a livello di partizione. Prestare attenzione a non creare partizioni hot spot.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario accedere alle entità in ordine di data/ora inverso o quando è necessario accedere alle entità aggiunte più di recente.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Anti-modello prepend/append](#prepend-append-anti-pattern)  
* [Recupera entità](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Modello di eliminazione volume elevato
Consentire l'eliminazione di un volume elevato di entità archiviando tutte le entità per l'eliminazione simultanea nella relativa tabella separata. Per eliminare le entità, eliminare la tabella.  

#### <a name="context-and-problem"></a>Contesto e problema
Molte applicazioni eliminano vecchi dati non più necessari a un'applicazione client o che l'applicazione ha archiviato in un altro supporto di archiviazione. Questi dati vengono in genere identificati in base a una data. Ad esempio, si ha la necessità di eliminare i record di tutte le richieste di accesso che hanno più di 60 giorni.  

Una possibile progettazione consiste nell'usare la data e l'ora della richiesta di accesso in `RowKey`:  

![Rappresentazione grafica dell'entità di tentativi di accesso][21]

Questo approccio evita gli hotspot di partizionamento, perché l'applicazione può inserire ed eliminare entità di accesso per ogni utente in una partizione separata. Tuttavia, questo approccio può essere costoso e dispendioso in termini di tempo se si dispone di un numero elevato di entità. Prima di tutto, è necessario eseguire un'analisi di tabella per identificare tutte le entità da eliminare ed è quindi necessario eliminare ogni entità precedente. Si noti che è possibile ridurre il numero di round trip al server necessari per eliminare le entità precedenti raggruppando più richieste di eliminazione in EGT.  

#### <a name="solution"></a>Soluzione
Usare una tabella separata per ogni giorno di tentativi di accesso. È possibile utilizzare la progettazione delle entità precedente per evitare hotspot durante l'inserimento di entità. L'eliminazione di entità obsolete ora è semplicemente una questione di eliminazione di una tabella ogni giorno (una singola operazione di archiviazione), anziché trovare ed eliminare centinaia e migliaia di singole entità di accesso ogni giorno.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Il progetto supporta altri modi in cui l'applicazione userà i dati, ad esempio la ricerca di entità specifiche, il collegamento ad altri dati o la generazione di informazioni aggregate?  
* La progettazione consente di evitare hotspot durante l'inserimento di nuove entità?  
* Se si vuole riutilizzare lo stesso nome di tabella dopo l'eliminazione, prevedere un ritardo. È consigliabile usare sempre nomi di tabella univoci.  
* Si prevede una limitazione della frequenza quando si usa per la prima volta una nuova tabella, mentre l'archiviazione tabelle apprende i modelli di accesso e distribuisce le partizioni tra i nodi. È necessario considerare la frequenza con cui è necessario creare nuove tabelle.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si dispone di un volume elevato di entità che è necessario eliminare contemporaneamente.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)
* [Modificare entità](#modify-entities)  

### <a name="data-series-pattern"></a>Modello di serie di dati
Archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

#### <a name="context-and-problem"></a>Contesto e problema
Spesso un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente. L'applicazione potrebbe, ad esempio, registrare il numero di messaggi immediati che ogni dipendente invia ogni ora e quindi usare queste informazioni per tracciare il numero di messaggi inviati da ogni utente nelle 24 ore precedenti. Una progettazione potrebbe essere l'archiviazione di 24 entità per ogni dipendente:  

![Rappresentazione grafica dell'entità Message stats][22]

Con questa progettazione è possibile individuare e aggiornare l'entità da aggiornare per ogni dipendente ogni volta che l'applicazione deve aggiornare il valore del numero di messaggi. Tuttavia, per recuperare le informazioni allo scopo di tracciare un grafico dell'attività per le 24 ore precedenti, è necessario recuperare 24 entità.  

#### <a name="solution"></a>Soluzione
Usare la progettazione seguente con una proprietà separata per archiviare il numero di messaggi per ogni ora:  

![Rappresentazione grafica dell'entità Message stats con proprietà separate][23]

Con questa progettazione è possibile usare un'operazione di unione per aggiornare il numero di messaggi per un dipendente per un'ora specifica. A questo punto, è possibile recuperare tutte le informazioni necessarie per tracciare il grafico usando una richiesta per una singola entità.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Se la serie di dati completa non rientra in una singola entità (un'entità può avere fino a 252 proprietà), usare un archivio dati alternativo, ad esempio un BLOB.  
* Se si dispone di più client che aggiornano contemporaneamente un'entità, utilizzare l' **ETag** per implementare la concorrenza ottimistica. Se si dispone di molti client, è possibile che si verifichi una contesa elevata.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario aggiornare e recuperare una serie di dati associata a una singola entità.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello di entità di grandi dimensioni](#large-entities-pattern)  
* [Unione o sostituzione](#merge-or-replace)  
* [Modello di transazioni coerenti](#eventually-consistent-transactions-pattern) con la fine (se si archiviano le serie di dati in un BLOB)  

### <a name="wide-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  

#### <a name="context-and-problem"></a>Contesto e problema
Una singola entità non può contenere più di 252 proprietà (escluse le proprietà di sistema obbligatorie) e non è in grado di archiviare più di 1 MB di dati in totale. In un database relazionale, in genere si aggirano i limiti relativi alle dimensioni di una riga aggiungendo una nuova tabella e imponendo una relazione 1 a 1 tra di essi.  

#### <a name="solution"></a>Soluzione
Tramite l'archiviazione tabelle è possibile archiviare più entità per rappresentare un singolo oggetto business di grandi dimensioni con più di 252 proprietà. Se ad esempio si desidera archiviare un conteggio del numero di messaggi IM inviati da ogni dipendente per gli ultimi 365 giorni, è possibile utilizzare la progettazione seguente che utilizza due entità con schemi diversi:  

![Rappresentazione grafica dell'entità Message stats con RowKey 01 e l'entità Message stats con RowKey 02][24]

Per apportare una modifica che richiede l'aggiornamento di entrambe le entità per mantenerle sincronizzate tra loro, è possibile usare una transazione EGT. Diversamente, è possibile usare una singola operazione di unione per aggiornare il numero di messaggi per un giorno specifico. Per recuperare tutti i dati per un singolo dipendente, è necessario recuperare entrambe le entità. Questa operazione può essere eseguita con due richieste efficienti che utilizzano `PartitionKey` sia un `RowKey` che un valore.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Quando si decide come implementare questo modello, considerare quanto segue:  

* Il recupero di un'entità logica completa richiede almeno due transazioni di archiviazione, una per recuperare ogni entità fisica.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare entità le cui dimensioni o il cui numero di proprietà superano i limiti per una singola entità nell'archivio tabelle.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)
* [Unione o sostituzione](#merge-or-replace)

### <a name="large-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

#### <a name="context-and-problem"></a>Contesto e problema
Una singola entità non può archiviare in totale più di 1 MB di dati. Se una o più delle proprietà archiviano valori che determinano il superamento di tale valore da parte della dimensione totale dell'entità, non è possibile archiviare l'intera entità nell'archivio tabelle.  

#### <a name="solution"></a>Soluzione
Se l'entità supera 1 MB di dimensioni perché una o più proprietà contengono una grande quantità di dati, è possibile archiviare i dati nell'archivio BLOB e quindi archiviare l'indirizzo del BLOB in una proprietà dell'entità. Ad esempio, è possibile archiviare la foto di un dipendente nell'archivio BLOB e archiviare un collegamento alla foto nella `Photo` proprietà dell'entità Employee:  

![Grafico che mostra l'entità Employee con stringa per la foto che punta all'archivio BLOB][25]

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra l'entità nell'archivio tabelle e i dati nell'archiviazione BLOB, usare il [modello di transazioni coerente](#eventually-consistent-transactions-pattern) per la gestione delle entità.
* Il recupero di un'entità completa richiede almeno due transazioni di archiviazione: una per recuperare l'entità e un'altra per recuperare i dati BLOB.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare entità le cui dimensioni superano i limiti per una singola entità nell'archivio tabelle.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello di entità di grandi dimensioni](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Anti-modello prepend/append
Quando si dispone di un volume elevato di inserimenti, aumentare la scalabilità distribuendo gli inserimenti tra più partizioni.  

#### <a name="context-and-problem"></a>Contesto e problema
L'anteposizione o l'aggiunta di entità alle entità archiviate determina in genere l'aggiunta da parte dell'applicazione di nuove entità alla prima o ultima partizione di una sequenza di partizioni. In questo caso, tutti gli inserimenti in un determinato momento avvengono nella stessa partizione, creando un hotspot. In questo modo si impedisce l'archiviazione di tabelle da inserimenti di bilanciamento del carico tra più nodi e, probabilmente, l'applicazione raggiungerà gli obiettivi di scalabilità per la partizione. Si consideri ad esempio il caso di un'applicazione che registra l'accesso alla rete e alle risorse da parte dei dipendenti. Una struttura di entità come la seguente può comportare la partizione dell'ora corrente diventando un hotspot, se il volume delle transazioni raggiunge l'obiettivo di scalabilità per una singola partizione:  

![Rappresentazione grafica dell'entità Employee][26]

#### <a name="solution"></a>Soluzione
La struttura di entità alternativa seguente evita un hotspot in una determinata partizione, in quanto l'applicazione registra gli eventi:  

![Grafico che mostra l'entità Employee con RowKey compounding anno, mese, giorno, ora e ID evento][27]

Si noti che in questo esempio l' `PartitionKey` oggetto `RowKey` e sono chiavi composte. `PartitionKey` USA sia l'ID reparto che il dipendente per distribuire la registrazione tra più partizioni.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La struttura chiave alternativa che evita la creazione di partizioni critiche negli inserimenti supporta in modo efficiente le query effettuate dall'applicazione client?  
* Il volume previsto di transazioni significa che è probabile che si raggiungano gli obiettivi di scalabilità per una singola partizione e che vengano limitate dall'archiviazione tabelle?  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Evitare l'anti-pattern Prepend/Append quando è probabile che il volume delle transazioni provochi la limitazione della frequenza per l'archiviazione tabelle quando si accede a una partizione a caldo.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello della parte finale del log](#log-tail-pattern)  
* [Modificare entità](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Anti-modello dei dati di log
In genere, è consigliabile usare l'archiviazione BLOB anziché l'archiviazione tabelle per archiviare i dati di log.  

#### <a name="context-and-problem"></a>Contesto e problema
Un caso d'uso comune per i dati di log consiste nel recuperare una selezione di voci di log per un intervallo di data/ora specifico. Si desidera, ad esempio, trovare tutti i messaggi di errore e critici registrati dall'applicazione tra 15:04 e 15:06 in una data specifica. Non si desidera utilizzare la data e l'ora del messaggio del log per determinare la partizione in cui si salvano le entità di log. Ciò comporta una partizione a caldo perché in un determinato momento tutte le entità di log condividono lo stesso `PartitionKey` valore (vedere l' [anti-pattern Prepend/Append](#prepend-append-anti-pattern)). Ad esempio, lo schema di entità seguente per un messaggio di log produce una partizione a caldo, perché l'applicazione scrive tutti i messaggi di log nella partizione per la data e l'ora correnti:  

![Rappresentazione grafica dell'entità messaggio del log][28]

In questo esempio, `RowKey` include la data e l'ora del messaggio del log per assicurarsi che i messaggi di log siano ordinati in base all'ordine di data/ora. Include `RowKey` anche un ID messaggio, nel caso in cui più messaggi di log condividono la stessa data e ora.  

Un altro approccio consiste nell'usare `PartitionKey` un oggetto che assicura che l'applicazione scriva i messaggi in un intervallo di partizioni. Se, ad esempio, l'origine del messaggio del log fornisce un modo per distribuire i messaggi tra più partizioni, è possibile utilizzare lo schema di entità seguente:  

![Rappresentazione grafica dell'entità messaggio del log][29]

Tuttavia, il problema con questo schema è che per recuperare tutti i messaggi di log per un intervallo di tempo specifico, è necessario eseguire la ricerca in ogni partizione della tabella.

#### <a name="solution"></a>Soluzione
Nella sezione precedente è stato evidenziato il problema di provare a utilizzare l'archiviazione tabelle per archiviare le voci di log e sono state suggerite due progettazioni non soddisfacenti. Una soluzione ha portato a una partizione a caldo con il rischio di una riduzione delle prestazioni di scrittura dei messaggi di log. L'altra soluzione ha comportato una riduzione delle prestazioni delle query, a causa della necessità di analizzare ogni partizione nella tabella per recuperare i messaggi di log per un intervallo di tempo specifico. L'archiviazione BLOB offre una soluzione migliore per questo tipo di scenario e questo è il modo in cui analisi archiviazione di Azure archivia i dati di log raccolti.  

Questa sezione illustra in che modo analisi archiviazione archivia i dati di log nell'archivio BLOB, come illustrato in questo approccio all'archiviazione dei dati in genere in base all'intervallo.  

Analisi archiviazione archivia i messaggi di log in un formato delimitato in più BLOB. Il formato delimitato semplifica l'analisi dei dati nel messaggio di log da parte di un'applicazione client.  

Analisi archiviazione usa una convenzione di denominazione per i BLOB che consente di individuare i BLOB (o BLOB) che contengono i messaggi di log per i quali si esegue la ricerca. Ad esempio, un BLOB denominato "Queue/2014/07/31/1800/000001. log" contiene messaggi di log relativi al servizio di Accodamento per l'ora a partire da 18:00 il 31 luglio 2014. "000001" indica che si tratta del primo file di log per il periodo. Analisi archiviazione registra inoltre i timestamp del primo e dell'ultimo messaggio di log archiviati nel file, come parte dei metadati del BLOB. L'API per l'archiviazione BLOB consente di individuare i BLOB in un contenitore in base al prefisso del nome. Per individuare tutti i BLOB contenenti dati di log delle code per l'ora a partire da 18:00, è possibile usare il prefisso "Queue/2014/07/31/1800".  

Analisi archiviazione memorizza nel buffer i messaggi di log internamente, quindi aggiorna periodicamente il BLOB appropriato o ne crea uno nuovo con il batch più recente di voci di log. In questo modo si riduce il numero di scritture che deve eseguire nell'archivio BLOB.  

Se si sta implementando una soluzione simile nella propria applicazione, considerare come gestire il compromesso tra affidabilità e costi e scalabilità. In altre parole, valutare l'effetto della scrittura di ogni voce di log nell'archivio BLOB non appena si verifica, rispetto al buffering degli aggiornamenti nell'applicazione e scrittura in archiviazione BLOB in batch.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come archiviare i dati di log, considerare quanto segue:  

* Se si crea una struttura di tabella che evita potenziali partizioni sensibili, è possibile che non si riesca ad accedere ai dati di log in modo efficiente.  
* Per elaborare i dati di log, spesso un client deve caricare molti record.  
* Sebbene i dati di log siano spesso strutturati, l'archiviazione BLOB potrebbe essere una soluzione migliore.  

### <a name="implementation-considerations"></a>Considerazioni sull'implementazione
Questa sezione illustra alcune considerazioni da tenere presente quando si implementano i modelli descritti nelle sezioni precedenti. Nella maggior parte di questa sezione vengono usati esempi scritti in C# che usano la libreria client di archiviazione (versione 4.3.0 al momento della stesura di questo documento).  

### <a name="retrieve-entities"></a>Recupera entità
Come descritto nella sezione [Progettazione per le query](#design-for-querying), la query più efficiente è una query di tipo punto. Tuttavia, in alcuni scenari potrebbe essere necessario recuperare più entità. Questa sezione descrive alcuni approcci comuni per il recupero di entità tramite la libreria client di archiviazione.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Eseguire una query a punti usando la libreria client di archiviazione
Il modo più semplice per eseguire una query a punti consiste nell'usare l'operazione **Retrieve** Table. Come illustrato nel frammento di codice C# seguente, questa operazione recupera un'entità con `PartitionKey` un valore di "Sales" e un `RowKey` valore di "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Si noti che questo esempio prevede che l'entità recuperata sia di tipo `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recuperare più entità tramite LINQ
È possibile recuperare più entità usando LINQ con la libreria client di archiviazione e specificando una query con una clausola **where** . Per evitare l'analisi di una tabella, è sempre necessario `PartitionKey` includere il valore nella clausola WHERE e, se possibile `RowKey` , il valore per evitare analisi di tabelle e partizioni. L'archiviazione tabelle supporta un set limitato di operatori di confronto (maggiore di, maggiore di o uguale a, minore di, minore di o uguale a, uguale a e diverso da) da utilizzare nella clausola WHERE. Il frammento di codice C# seguente trova tutti i dipendenti il cui cognome inizia con "B" ( `RowKey` presupponendo che archivia il cognome) nel reparto vendite `PartitionKey` (presupponendo che il nome del reparto venga archiviato):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Si noti che la query specifica sia `RowKey` un che `PartitionKey` un per garantire prestazioni migliori.  

Nell'esempio di codice seguente viene illustrata la funzionalità equivalente usando l'API Fluent (per altre informazioni sulle API Fluent in generale, vedere [procedure consigliate per la progettazione di un'API Fluent](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> L'esempio nidifica più `CombineFilters` metodi per includere le tre condizioni di filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperare un numero elevato di entità da una query
Una query ottimale restituisce una singola entità in base a `PartitionKey` un valore e `RowKey` a un valore. Tuttavia, in alcuni scenari è possibile che si disponga di un requisito per restituire molte entità dalla stessa partizione o anche da molte partizioni. È sempre necessario eseguire test completi delle prestazioni dell'applicazione in tali scenari.  

Una query sull'archiviazione tabelle può restituire un massimo di 1.000 entità alla volta ed essere eseguita per un massimo di cinque secondi. L'archiviazione tabelle restituisce un token di continuazione per consentire all'applicazione client di richiedere il set successivo di entità, se si verifica una delle condizioni seguenti:

- Il set di risultati contiene più di 1.000 entità.
- La query non è stata completata entro cinque secondi.
- La query supera il limite della partizione. 

Per ulteriori informazioni sul funzionamento dei token di continuazione, vedere [timeout e paginazione delle query](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se si usa la libreria client di archiviazione, è in grado di gestire automaticamente i token di continuazione quando restituisce entità dall'archiviazione tabelle. L'esempio di codice C# seguente, ad esempio, gestisce automaticamente i token di continuazione se l'archiviazione tabelle li restituisce in una risposta:  

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

Usando i token di continuazione in modo esplicito è possibile controllare quando l'applicazione recupera il successivo segmento di dati. Se, ad esempio, l'applicazione client consente agli utenti di spostarsi tra le entità archiviate in una tabella, un utente potrebbe decidere di non eseguire il paging di tutte le entità recuperate dalla query. L'applicazione userà solo un token di continuazione per recuperare il segmento successivo quando l'utente ha terminato il paging di tutte le entità nel segmento corrente. Questo approccio offre diversi vantaggi:  

* È possibile limitare la quantità di dati da recuperare dall'archivio tabelle e spostarli sulla rete.  
* È possibile eseguire operazioni di I/O asincrone in .NET.  
* È possibile serializzare il token di continuazione nell'archivio permanente, in modo da poter continuare in caso di arresto anomalo dell'applicazione.  

> [!NOTE]
> Un token di continuazione restituisce in genere un segmento contenente 1.000 entità, sebbene possa contenere meno elementi. Questa situazione si verifica anche se si limita il numero di voci restituite da una query usando **Take** per restituire le prime n entità che corrispondono ai criteri di ricerca. L'archiviazione tabelle potrebbe restituire un segmento contenente meno di n entità, insieme a un token di continuazione per consentire il recupero delle entità rimanenti.  
> 
> 

Il codice C# seguente illustra come modificare il numero di entità restituite all'interno di un segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Proiezione lato server
Una singola entità può avere fino a 255 proprietà e dimensioni fino a 1 MB. Quando si esegue una query sulla tabella e si recuperano le entità, potrebbero non essere necessarie tutte le proprietà ed è possibile evitare di trasferire i dati inutilmente (per ridurre la latenza e i costi). È possibile usare la proiezione lato server per trasferire solo le proprietà necessarie. Nell'esempio seguente viene recuperata `Email` solo la proprietà ( `PartitionKey`insieme `RowKey`a `Timestamp`,, `ETag`e) dalle entità selezionate dalla query.  

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

Si noti come `RowKey` il valore sia disponibile anche se non è incluso nell'elenco di proprietà da recuperare.  

### <a name="modify-entities"></a>Modificare entità
La libreria client di archiviazione consente di modificare le entità archiviate nell'archiviazione tabelle inserendo, eliminando e aggiornando le entità. È possibile usare transazioni EGT per raggruppare più operazioni di inserimento, aggiornamento ed eliminazione, per ridurre il numero di round trip necessari e migliorare le prestazioni della soluzione.  

Eccezioni generate quando la libreria client di archiviazione esegue un transazione EGT in genere include l'indice dell'entità che ha causato l'esito negativo del batch. Ciò è utile quando si esegue il debug di codice che usa le transazioni EGT.  

È inoltre opportuno considerare l'influenza della progettazione sul modo in cui l'applicazione gestisce le operazioni di concorrenza e aggiornamento.  

#### <a name="managing-concurrency"></a>Gestione della concorrenza
Per impostazione predefinita, l'archiviazione tabelle implementa le verifiche della concorrenza ottimistica a livello di singole entità per le operazioni di inserimento, Unione ed eliminazione, sebbene sia possibile per un client forzare l'archiviazione tabelle per ignorare questi controlli. Per ulteriori informazioni, vedere [gestione della concorrenza in archiviazione di Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Unione o sostituzione
Il `Replace` metodo della `TableOperation` classe sostituisce sempre l'entità completa nell'archivio tabelle. Se non si include una proprietà nella richiesta quando tale proprietà è presente nell'entità archiviata, la richiesta rimuove tale proprietà dall'entità archiviata. A meno che non si voglia rimuovere una proprietà in modo esplicito da un'entità archiviata, è necessario includere ogni proprietà nella richiesta.  

È possibile utilizzare il `Merge` metodo della `TableOperation` classe per ridurre la quantità di dati inviati all'archiviazione tabelle quando si desidera aggiornare un'entità. Il `Merge` metodo sostituisce qualsiasi proprietà nell'entità archiviata con i valori di proprietà dell'entità inclusa nella richiesta. Questo metodo lascia inalterate le proprietà nell'entità archiviata che non sono incluse nella richiesta. Questa operazione è utile se si dispone di entità di grandi dimensioni ed è necessario solo aggiornare un numero ridotto di proprietà in una richiesta.  

> [!NOTE]
> I `*Replace` metodi `Merge` e hanno esito negativo se l'entità non esiste. In alternativa, è possibile usare i `InsertOrReplace` metodi e `InsertOrMerge` per creare una nuova entità, se non esiste.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Usare tipi di entità eterogenei
L'archiviazione tabelle è un archivio tabelle *senza schema* . Ciò significa che una singola tabella può archiviare entità di più tipi, offrendo una grande flessibilità nella progettazione. L'esempio seguente illustra una tabella che archivia entità dipendente ed entità reparto:  

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
<th>Posta elettronica</th>
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
<th>Posta elettronica</th>
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
<th>Posta elettronica</th>
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

Ogni entità deve avere `PartitionKey`ancora valori `RowKey`, e `Timestamp` , ma può avere qualsiasi set di proprietà. Non è inoltre necessario indicare il tipo di un'entità, a meno che non si scelga di archiviare le informazioni in un punto qualsiasi. Esistono due opzioni per identificare il tipo di entità:  

* Anteporre il tipo di entità a `RowKey` o eventualmente `PartitionKey`a. Ad esempio, `EMPLOYEE_000123` o `DEPARTMENT_SALES` come `RowKey` valori.  
* Usare una proprietà separata per registrare il tipo di entità, come illustrato nella tabella seguente.  

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
<th>Posta elettronica</th>
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
<th>Posta elettronica</th>
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
<th>Posta elettronica</th>
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

La prima opzione, anteposto al tipo di entità `RowKey`, è utile se è possibile che due entità di tipi diversi abbiano lo stesso valore di chiave. Inoltre, raggruppa entità dello stesso tipo insieme nella partizione.  

Le tecniche descritte in questa sezione sono particolarmente rilevanti per la discussione sulle[relazioni di ereditarietà](#inheritance-relationships).  

> [!NOTE]
> Considerare l'inclusione di un numero di versione nel valore del tipo di entità, per consentire alle applicazioni client di sviluppare oggetti POCO e utilizzare versioni diverse.  
> 
> 

La restante parte di questa sezione descrive alcune delle funzionalità della libreria client di archiviazione che semplificano l'uso di più tipi di entità nella stessa tabella.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recuperare tipi di entità eterogenei
Se si usa la libreria client di archiviazione, sono disponibili tre opzioni per l'uso di più tipi di entità.  

Se si conosce il tipo dell'entità archiviata con valori `RowKey` e `PartitionKey` specifici, è possibile specificare il tipo di entità quando si recupera l'entità. Questa operazione è stata illustrata nei due esempi precedenti per il recupero `EmployeeEntity`di entità di tipo: [eseguire una query a punti usando la libreria client di archiviazione](#run-a-point-query-by-using-the-storage-client-library) e [recuperare più entità usando LINQ](#retrieve-multiple-entities-by-using-linq).  

La seconda opzione consiste nell'usare il `DynamicTableEntity` tipo, ovvero un contenitore di proprietà, anziché un tipo di entità poco concreto. Questa opzione può anche migliorare le prestazioni, perché non è necessario serializzare e deserializzare l'entità in tipi .NET. Il codice C# seguente recupera potenzialmente più entità di tipi diversi dalla tabella, ma restituisce tutte le entità come `DynamicTableEntity` istanze. Viene quindi utilizzata la `EntityType` proprietà per determinare il tipo di ogni entità:  

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

Per recuperare altre proprietà, è necessario usare il `TryGetValue` metodo sulla `Properties` proprietà della `DynamicTableEntity` classe.  

Una terza opzione consiste nel combinare utilizzando il `DynamicTableEntity` tipo e un' `EntityResolver` istanza. Ciò consente di risolvere a più tipi POCO nella stessa query. In questo esempio, il `EntityResolver` delegato usa la `EntityType` proprietà per distinguere tra i due tipi di entità restituiti dalla query. Il `Resolve` metodo usa il `resolver` delegato per risolvere `DynamicTableEntity` le istanze `TableEntity` di in istanze di.  

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
Non è necessario che il tipo di un'entità venga eliminato e si conosca sempre il tipo di un'entità quando viene inserita. Tuttavia, è possibile usare il `DynamicTableEntity` tipo per aggiornare un'entità senza conoscerne il tipo e senza usare una classe di entità poco. Nell'esempio di codice seguente viene recuperata una singola entità e viene `EmployeeCount` verificato che la proprietà esista prima di aggiornarla.  

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

### <a name="control-access-with-shared-access-signatures"></a>Controllare l'accesso con le firme di accesso condiviso
È possibile usare i token di firma di accesso condiviso per consentire alle applicazioni client di modificare (ed eseguire query) direttamente sulle entità di tabella, senza la necessità di eseguire l'autenticazione direttamente con l'archiviazione tabelle. In genere, l'uso di SAS nell'applicazione comporta tre vantaggi principali:  

* Non è necessario distribuire la chiave dell'account di archiviazione a una piattaforma non sicura (ad esempio un dispositivo mobile) per consentire a tale dispositivo di accedere e modificare le entità nell'archivio tabelle.  
* È possibile eseguire l'offload di alcune delle operazioni eseguite dai ruoli Web e di lavoro per la gestione delle entità. È possibile eseguire l'offload ai dispositivi client, ad esempio computer degli utenti finali e dispositivi mobili.  
* È possibile assegnare a un client un set di autorizzazioni vincolato e limitato al tempo, ad esempio consentendo l'accesso in sola lettura a risorse specifiche.  

Per altre informazioni sull'uso di token SAS con l'archiviazione tabelle, vedere [uso delle firme di accesso condiviso (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Tuttavia, è comunque necessario generare i token di firma di accesso condiviso che concedono un'applicazione client alle entità nell'archivio tabelle. Eseguire questa operazione in un ambiente con accesso sicuro alle chiavi dell'account di archiviazione. In genere, è possibile usare un ruolo Web o di lavoro per generare i token delle firme di accesso condiviso e distribuirli alle applicazioni client che richiedono l'accesso alle entità. Poiché la generazione e la distribuzione dei token delle firme di accesso condiviso ai client comportano comunque un sovraccarico, è consigliabile valutare il modo migliore di ridurre tale sovraccarico, soprattutto in scenari con volumi elevati.  

È possibile generare un token di firma di accesso condiviso che concede l'accesso a un subset delle entità in una tabella. Per impostazione predefinita, si crea un token di firma di accesso condiviso per un'intera tabella. È anche possibile specificare che il token SAS conceda l'accesso a un intervallo di `PartitionKey` valori o a un intervallo di `PartitionKey` valori e. `RowKey` È possibile scegliere di generare i token di firma di accesso condiviso per i singoli utenti del sistema, in modo che il token di firma di accesso condiviso di ogni utente consenta di accedere solo alle proprie entità nell'archivio tabelle.  

### <a name="asynchronous-and-parallel-operations"></a>Operazioni asincrone e parallele
A condizione che le richieste vengano distribuite in più partizioni, è possibile migliorare la velocità effettiva e la velocità di risposta del client usando le query parallele o asincrone.
Ad esempio, si potrebbero avere due o più istanze del ruolo di lavoro che accedono alle tabelle in parallelo. È possibile fare in modo che i singoli ruoli di lavoro siano responsabili di determinati set di partizioni o semplicemente avere più istanze del ruolo di lavoro, ognuna delle quali può accedere a tutte le partizioni di una tabella.  

All'interno di un'istanza del client, è possibile migliorare la velocità effettiva eseguendo operazioni di archiviazione in modo asincrono. La libreria client di archiviazione semplifica la scrittura di query e modifiche asincrone. Ad esempio, è possibile iniziare con il metodo sincrono che recupera tutte le entità in una partizione, come illustrato nel codice C# seguente:  

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

È possibile modificare facilmente questo codice in modo che la query venga eseguita in modo asincrono, come indicato di seguito:  

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

* La firma del metodo include ora `async` il modificatore e restituisce `Task` un'istanza.  
* Anziché chiamare il `ExecuteSegmented` metodo per recuperare i risultati, il metodo ora chiama il `ExecuteSegmentedAsync` metodo. Il metodo utilizza il `await` modificatore per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare questo metodo più volte, con valori diversi per il `department` parametro. Ogni query viene eseguita su un thread separato.  

Non è presente alcuna versione asincrona del `Execute` metodo nella `TableQuery` classe, perché l' `IEnumerable` interfaccia non supporta l'enumerazione asincrona.  

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

È possibile modificare facilmente questo codice in modo che l'aggiornamento venga eseguito in modo asincrono, come indicato di seguito:  

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

* La firma del metodo include ora `async` il modificatore e restituisce `Task` un'istanza.  
* Anziché chiamare il `Execute` metodo per aggiornare l'entità, il metodo ora chiama il `ExecuteAsync` metodo. Il metodo utilizza il `await` modificatore per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare più metodi asincroni come questo e ogni chiamata al metodo viene eseguita su un thread separato.  


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

