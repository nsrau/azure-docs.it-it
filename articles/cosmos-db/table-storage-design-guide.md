---
title: Progettare tabelle di Azure Cosmos DB per la scalabilità e le prestazioni
description: "Guida alla progettazione dell'archiviazione tabelle di Azure: tabelle scalabili e con prestazioni avanzate in Azure Cosmos DB e in archiviazione tabelle di Azure"
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 05a469dbeb093c41b45be278aec42cc930223c72
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002177"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Guida alla progettazione delle tabelle per archiviazione tabelle di Azure: tabelle scalabili con prestazioni avanzate

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Per progettare tabelle scalabili ed efficienti, è necessario prendere in considerazione diversi fattori, inclusi i costi. Se in precedenza sono stati progettati schemi per i database relazionali, queste considerazioni risulteranno familiari. Sebbene esistano alcune analogie tra Archiviazione tabelle di Azure e i modelli relazionali, esistono tuttavia anche molte differenze importanti. Queste differenze danno in genere origine a progettazioni diverse che potrebbero sembrare poco plausibili o errate a chi ha familiarità con i database relazionali, ma che invece hanno senso se la progettazione è finalizzata a un archivio di chiavi/valori NoSQL, come l'Archiviazione tabelle.

Archiviazione tabelle è progettato per supportare applicazioni con scalabilità cloud che possono contenere miliardi di entità di dati, dette "righe" nella terminologia dei database relazionali, o per set di dati che devono supportare volumi di transazioni elevati. È quindi necessario considerare diversamente la modalità di archiviazione dei dati e comprendere il funzionamento di archiviazione tabelle. Un archivio dati NoSQL ben progettato offre alla soluzione una scalabilità decisamente più elevata e a un costo inferiore rispetto a una soluzione che usa un database relazionale. Questa guida illustra proprio questi argomenti.  

## <a name="about-azure-table-storage"></a>Informazioni su archiviazione tabelle di Azure
Questa sezione evidenzia alcune funzionalità chiave di archiviazione tabelle, di particolare importanza per la progettazione a livello di prestazioni e scalabilità. Se non si ha familiarità con Archiviazione di Azure e con archiviazione tabelle, prima di leggere questo articolo, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/common/storage-introduction.md) e [Introduzione all'archiviazione tabelle di Azure con .NET](table-storage-how-to-use-dotnet.md). Anche se l'argomento principale di questa guida è l'archiviazione tabelle, sono incluse alcune informazioni sull'archiviazione code e l'archiviazione BLOB di Azure e su come è possibile usarli con archiviazione tabelle in una soluzione.  

Archiviazione tabelle usa un formato tabulare per archiviare i dati. In base alla terminologia standard, ogni riga della tabella rappresenta un'entità le cui diverse proprietà sono archiviate nelle colonne. Ogni entità ha una coppia di chiavi che la identificano in modo univoco e una colonna timestamp usata da archiviazione tabelle per tenere traccia dell'ultimo aggiornamento dell'entità. Il campo timestamp viene aggiunto automaticamente e non è possibile sovrascriverlo manualmente con un valore arbitrario. Archiviazione tabelle usa il timestamp dell'ultima modifica per gestire la concorrenza ottimistica.  

> [!NOTE]
> Le operazioni delle API REST di archiviazione tabelle restituiscono anche un valore `ETag` derivato dal timestamp dell'ultima modifica. In questo documento i termini ETag e timestamp dell'ultima modifica vengono usati in modo intercambiabile perché si riferiscono agli stessi dati sottostanti.  
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


Fino ad ora questo progetto è simile alle tabelle in un database relazionale. Le principali differenze sono le colonne obbligatorie e la possibilità di archiviare più tipi di entità nella stessa tabella. Inoltre ogni proprietà definita dall'utente, come **FirstName** o **Age** ha un tipo di dati, ad esempio numero intero o stringa, proprio come una colonna in un database relazionale. A differenza di un database relazionale, tuttavia, essendo l'archiviazione tabelle priva di schema, una proprietà non deve avere lo stesso tipo di dati in ogni entità. Per archiviare tipi di dati complessi in una sola proprietà, è necessario usare un formato serializzato come JSON o XML. Per altre informazioni, vedere [Informazioni sul modello di dati di archiviazione tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).

La scelta di `PartitionKey` e `RowKey` è fondamentale per la progettazione ottimale di una tabella. Ogni entità archiviata in una tabella deve avere una combinazione univoca di `PartitionKey` e `RowKey`. Come con le chiavi in una tabella di database relazionale, i valori di `PartitionKey` e `RowKey` vengono indicizzati per creare un indice cluster che consenta di eseguire ricerche veloci. Archiviazione tabelle, tuttavia, non crea indici secondari, quindi queste sono le uniche due proprietà indicizzate. Alcuni criteri descritti più avanti mostrano come ovviare a questa apparente limitazione.  

Una tabella è costituita da una o più partizioni e molte decisioni relative alla progettazione riguarderanno la scelta di un valore appropriato per `PartitionKey` e `RowKey` per poter ottimizzare la soluzione. Una soluzione può essere costituita da una singola tabella contenente tutte le entità organizzate in partizioni, ma normalmente comprende più tabelle. Le tabelle consentono di organizzare in modo logico le entità e di gestire l'accesso ai dati tramite gli elenchi di controllo di accesso. È possibile eliminare un'intera tabella usando una sola operazione di archiviazione.  

### <a name="table-partitions"></a>Partizioni della tabella
Il nome dell'account, il nome tabella e `PartitionKey` insieme identificano la partizione nel servizio di archiviazione in cui archiviazione tabelle archivia l'entità. Oltre a far parte dello schema di indirizzamento per le entità, le partizioni definiscono un ambito per le transazioni (vedere più avanti la sezione [Transazioni di gruppi di entità](#entity-group-transactions) in questo articolo) e formano le basi per la scalabilità di archiviazione tabelle. Per altre informazioni sulle partizioni delle tabelle, vedere [Elenco di controllo di prestazioni e scalabilità di archiviazione tabelle](../storage/tables/storage-performance-checklist.md).  

In archiviazione tabelle un solo nodo gestisce una o più partizioni complete e il servizio è scalabile grazie al bilanciamento dinamico del carico delle partizioni tra i nodi. Se un nodo ha un carico insufficiente, archiviazione tabelle può suddividere l'intervallo di partizioni gestite da tale nodo su diversi nodi. Quando il traffico diminuisce, archiviazione tabelle può unire gli intervalli di partizione di nodi con carichi ridotti a un singolo nodo.  

Per altre informazioni sui dettagli interni di archiviazione tabelle, in particolare sulla gestione delle partizioni, vedere il documento [Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a disponibilità elevata con coerenza assoluta](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

### <a name="entity-group-transactions"></a>Transazioni di gruppi di entità
In archiviazione tabelle, le transazioni di gruppi di entità sono l'unico meccanismo predefinito per eseguire aggiornamenti atomici tra più entità. Le transazioni dei gruppi di entità sono chiamate anche *transazioni batch*. Le transazioni dei gruppi di entità possono agire solo su entità archiviate nella stessa partizione, ovvero che condividono la stessa chiave di partizione in una determinata tabella, quindi, ogni volta che è necessario un comportamento transazionale atomico tra più entità, bisogna assicurarsi che tali entità siano nella stessa partizione. Per questo motivo spesso si tengono tipi diversi di entità nella stessa tabella (e partizione) e non si usa una tabella multipla per ogni tipo di entità. Una sola EGT può agire al massimo su 100 entità.  Se si inviano più transazioni dei gruppi di entità simultanee per l'elaborazione, è importante garantire che tali transazioni non vengano usate con entità che sono comuni tra le transazioni dei gruppi di entità, altrimenti l'elaborazione potrebbe subire ritardi.

Le transazioni EGT richiedono anche la valutazione di un potenziale compromesso nella progettazione. L'uso di un maggior numero di partizioni aumenta la scalabilità dell'applicazione, in quanto Azure ha maggiori opportunità di bilanciamento del carico delle richieste tra i nodi. Questo, però, potrebbe limitare la capacità dell'applicazione di eseguire transazioni atomiche e mantenere la coerenza assoluta per i dati. Ci sono inoltre specifici obiettivi di scalabilità a livello di partizione, che potrebbero limitare la velocità effettiva delle transazioni prevista per un singolo nodo.

Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione di Azure, vedere [Obiettivi di scalabilità per gli account di archiviazione standard](../storage/common/scalability-targets-standard-account.md). Per altre informazioni sugli obiettivi di scalabilità per archiviazione tabelle, vedere [Obiettivi di scalabilità e prestazioni per archiviazione tabelle](../storage/tables/scalability-targets.md). Le sezioni successive di questa guida illustrano diverse strategie di progettazione che aiutano a gestire compromessi come questo e illustrano il modo migliore per scegliere la chiave di partizione in base ai requisiti specifici dell'applicazione client.  

### <a name="capacity-considerations"></a>Considerazioni sulla capacità
La tabella seguente include alcuni valori chiave da tenere presenti quando si progetta una soluzione di archiviazione tabelle:  

| Capacità totale di un account di archiviazione di Azure | 500 TB |
| --- | --- |
| Numero di tabelle in un account di archiviazione di Azure |Limitato solo dalla capacità dell'account di archiviazione. |
| Numero di partizioni in una tabella |Limitato solo dalla capacità dell'account di archiviazione. |
| Numero di entità in una partizione |Limitato solo dalla capacità dell'account di archiviazione. |
| Dimensioni di una singola entità |Fino a 1 MB con un massimo di 255 proprietà (inclusi `PartitionKey`, `RowKey` e `Timestamp`). |
| Dimensioni di `PartitionKey` |Stringa con dimensioni fino a 1 kB. |
| Dimensioni di `RowKey` |Stringa con dimensioni fino a 1 kB. |
| Dimensioni di una transazione di gruppi di entità |Una transazione può includere al massimo 100 entità e le dimensioni del payload devono essere inferiori a 4 MB. Una transazione EGT può aggiornare una sola entità per volta. |

Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerazioni sul costo
Anche se l'archiviazione tabelle è relativamente poco costosa, è consigliabile includere le stime dei costi, sia per l'utilizzo della capacità che per la quantità di transazioni, nella valutazione delle soluzioni che usano archiviazione tabelle. Tuttavia in molti scenari, l'archiviazione dei dati denormalizzati o duplicati per migliorare le prestazioni o la scalabilità della soluzione costituisce un valido approccio. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Linee guida per la progettazione di tabelle
Questi elenchi riepilogano alcune delle linee guida principali da tenere presenti quando si progettano le tabelle. I dettagli verranno illustrati più avanti. Queste linee guida sono diverse dalle linee guida a cui in genere ci si attiene per la progettazione di database relazionali.  

Progettazione di un'archiviazione tabelle efficiente nelle operazioni di *lettura*:

* **Progettazione per le query nelle applicazioni con intensa attività di lettura.** Quando si progettano le tabelle, considerare le query che si eseguiranno, soprattutto quelle sensibili alla latenza, prima di pensare a come si aggiorneranno le entità. Ciò comporta in genere una soluzione efficiente e ad alte prestazioni.  
* **Specificare sia `PartitionKey` che `RowKey` nelle query.** Le *query di puntamento* come queste sono le query più efficienti di archiviazione tabelle.  
* **Prendere in considerazione l'archiviazione di copie duplicate delle entità.** Poiché l'archiviazione tabelle è economica, considerare la possibilità di archiviare la stessa entità più volte (con chiavi diverse) per consentire query più efficienti.  
* **Considerare la denormalizzazione dei dati.** Poiché l'archiviazione delle tabelle è economica, considerare la denormalizzazione dei dati. Ad esempio, archiviare le entità di riepilogo in modo che le query per aggregare i dati debbano accedere a una singola entità.  
* **Usare valori chiave composti.** Le uniche chiavi disponibili sono `PartitionKey` e `RowKey`. Ad esempio, per abilitare percorsi alternativi per l'accesso con chiave alle entità, ad esempio, utilizzare valori chiave composti.  
* **Usare la proiezione di query.** È possibile ridurre la quantità di dati trasferiti tramite la rete usando query che selezionano solo i campi necessari.  

Progettazione di un'archiviazione tabelle efficiente nelle operazioni di *scrittura*:  

* **Non creare partizioni ad accesso frequente.** Scegliere chiavi che consentono di distribuire le richieste tra più partizioni in qualsiasi momento.  
* **Evitare picchi di traffico.** Distribuire il traffico in un intervallo di tempo ragionevole ed evitare i picchi di traffico.
* **Non creare necessariamente una tabella separata per ogni tipo di entità.** Quando è necessario eseguire transazioni atomiche tra diversi tipi di entità, è possibile archiviare questi tipi di entità nella stessa partizione della stessa tabella.
* **Considerare la velocità effettiva massima che è necessario raggiungere.** È necessario tenere presenti gli obiettivi di scalabilità per archiviazione tabelle e assicurarsi di non superarli con la progettazione.  

Più avanti, in questa guida, verranno illustrati degli esempi in cui vengono messi in pratica tutti questi principi.  

## <a name="design-for-querying"></a>Progettazione per le query
In archiviazione tabelle è possibile eseguire un'intensa attività di lettura, di scrittura o una combinazione di entrambe. Questa sezione illustra la progettazione per supportare le operazioni di lettura in modo efficiente. Una progettazione che supporta in modo efficiente le operazioni di lettura è in genere efficiente anche nelle operazioni di scrittura. Tuttavia, bisogna fare altre considerazioni durante la progettazione di supporto alle operazioni di scrittura. Queste considerazione verranno discusse nella sezione successiva [Progettazione per la modifica dei dati](#design-for-data-modification).

Per consentire una lettura dei dati efficiente, è consigliabile iniziare chiedendosi quali query dovrà eseguire l'applicazione per recuperare i dati necessari.  

> [!NOTE]
> Con archiviazione tabelle, è fondamentale realizzare una progettazione corretta fin dall'inizio perché cambiarla in seguito è complesso e costoso. In un database relazionale, ad esempio, spesso è possibile risolvere i problemi di prestazioni semplicemente aggiungendo degli indici a un database esistente, ma questa opzione non è applicabile ad archiviazione tabelle.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>In che modo la scelta di `PartitionKey` e `RowKey` influisce sulle prestazioni delle query
Gli esempi seguenti presuppongono che in archiviazione tabelle vengano archiviate entità dipendente con la struttura seguente (per maggiore chiarezza, nella maggior parte degli esempi viene omessa la proprietà `Timestamp`):  

| Nome colonna | Tipo di dati |
| --- | --- |
| `PartitionKey` (Nome del reparto) |string |
| `RowKey` (ID dipendente) |string |
| `FirstName` |string |
| `LastName` |string |
| `Age` |Integer |
| `EmailAddress` |string |

Ecco alcune linee guida generali per la progettazione delle query di archiviazione tabelle. La sintassi di filtro usata negli esempi seguenti deriva dall'API REST di archiviazione tabelle. Per altre informazioni, vedere [Entità di query](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Una *query di puntamento* è il tipo di ricerca più efficiente da usare ed è consigliata per le ricerche con volumi elevati o per le ricerche che richiedono una latenza molto bassa. Una query di questo tipo può usare gli indici per individuare in modo efficiente una singola entità specificando entrambi i valori per `PartitionKey` e `RowKey`. Ad esempio: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* La seconda query in termini di efficienza è la *query di intervallo*, che usa `PartitionKey` e applica un filtro in base a un intervallo di valori `RowKey` per restituire più di un'entità. Il valore di `PartitionKey` identifica una partizione specifica e i valori di `RowKey` identificano un subset delle entità in quella partizione. Ad esempio: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* La terza migliore query è l'*analisi della partizione*, che usa `PartitionKey` e applica un filtro in un'altra proprietà non principale e potrebbe restituire più di un'entità. Il valore `PartitionKey` identifica una partizione specifica e i valori della proprietà selezionano un subset delle entità in quella partizione. Ad esempio: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Una *scansione di tabella* non include `PartitionKey` ed è inefficiente perché cerca le entità corrispondenti in tutte le partizioni della tabella. Esegue una scansione di tabella indipendentemente dal fatto che il filtro usi `RowKey` o meno. Ad esempio: `$filter=LastName eq 'Jones'`.  
* Le query di archiviazione tabelle di Azure che restituiscono più entità vengono ordinate in base a `PartitionKey` e `RowKey`. Per non dover riordinare le entità nel client, scegliere un valore di `RowKey` che definisca l'ordinamento più comune. I risultati della query restituiti dall'API Tabella di Azure in Azure Cosmos DB non sono ordinati per chiave di riga o chiave di partizione. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](table-api-faq.md#table-api-vs-table-storage).

Se si usa "**or**" per specificare un filtro basato su valori di `RowKey`, si ottiene un'analisi della partizione che non viene considerata come query di intervallo. Evitare pertanto le query che usano filtri come: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Per esempi di codice lato client che usano la libreria client di archiviazione per eseguire query efficienti, vedere:  

* [Eseguire una query di puntamento mediante la libreria client di archiviazione](#run-a-point-query-by-using-the-storage-client-library)
* [Recuperare più entità usando LINQ](#retrieve-multiple-entities-by-using-linq)
* [Proiezione lato server](#server-side-projection)  

Per esempi di codice lato client che possono gestire più tipi di entità archiviati nella stessa tabella, vedere:  

* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Scegliere un valore di `PartitionKey` appropriato
La scelta di `PartitionKey` deve bilanciare la necessità di abilitare l'uso di transazioni di gruppi di entità, per garantire la coerenza, e il requisito di distribuzione delle entità tra più partizioni, per assicurare una soluzione scalabile.  

Da un lato, è possibile archiviare tutte le entità in un'unica partizione, ma questo potrebbe limitare la scalabilità della soluzione e impedire all'archiviazione tabelle di bilanciare il carico delle richieste. Dall'altro, è possibile archiviare un'entità per partizione. Questa è un'opzione con scalabilità elevata e consente all'archiviazione tabelle di bilanciare il carico delle richieste, ma impedisce all'utente di usare le transazioni di gruppi di entità.  

Un valore ideale di `PartitionKey` consente di usare query efficienti e dispone di una quantità di partizioni sufficiente a garantire la scalabilità della soluzione. Di solito le entità dispongono di una proprietà apposita che le distribuisce in un numero sufficiente di partizioni.

> [!NOTE]
> Ad esempio, in un sistema che archivia le informazioni sugli utenti o i dipendenti, `UserID` può essere un valore di `PartitionKey` valido. È possibile avere più entità che usano un `UserID` specifico come chiave di partizione. Ogni entità che archivia i dati relativi a un utente viene raggruppata in un'unica partizione. Queste entità sono accessibili tramite le transazioni dei gruppi di entità, garantendo al contempo una scalabilità elevata.
> 
> 

Gli altri aspetti da considerare per la scelta di `PartitionKey` riguardano l'inserimento, l'aggiornamento e l'eliminazione delle entità. Per altre informazioni, vedere più avanti, in questo articolo [Progettazione per la modifica dei dati](#design-for-data-modification).  

### <a name="optimize-queries-for-table-storage"></a>Ottimizzare le query per archiviazione tabelle
Archiviazione tabelle indicizza automaticamente le entità usando i valori di `PartitionKey` e `RowKey` in un unico indice cluster. Questo è il motivo per cui le query di puntamento sono le più efficienti da usare. Tuttavia, non esistono altri indici oltre a quello nell'indice cluster in `PartitionKey` e `RowKey`.

Molte progettazioni devono soddisfare alcuni requisiti per abilitare la ricerca di entità in base a più criteri, ad esempio individuare le entità dipendente in base a indirizzo di posta elettronica, ID dipendente o cognome. I criteri seguenti nella sezione [Schemi progettuali per la tabella](#table-design-patterns) descrivono questi tipi di requisiti. I criteri descrivono anche i modi per ovviare al fatto che archiviazione tabelle non offre indici secondari.  

* [Criterio per indice secondario intrapartizione](#intra-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori di `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.  
* [Criterio per indice secondario interpartizione](#inter-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori di `RowKey` diversi in partizioni o tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.  
* [Criterio per entità indice](#index-entities-pattern): mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

### <a name="sort-data-in-table-storage"></a>Ordinare i dati in archiviazione tabelle

Archiviazione tabelle restituisce i risultati delle query ordinati in ordine crescente, in base a `PartitionKey` e poi a `RowKey`.

> [!NOTE]
> I risultati della query restituiti dall'API Tabella di Azure in Azure Cosmos DB non sono ordinati per chiave di riga o chiave di partizione. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](table-api-faq.md#table-api-vs-table-storage).

Le chiavi in archiviazione tabelle sono valori di stringa. Per essere certi che i valori numerici siano ordinati correttamente, è consigliabile convertirli in una lunghezza fissa aggiungendo degli zeri. Se, ad esempio, il valore dell'ID dipendente usato come `RowKey` è un valore intero, è consigliabile convertire l'ID dipendente **123** in **00000123**. 

In molte applicazioni è necessario usare i dati ordinandoli in modo diverso, ad esempio ordinando i dipendenti per nome o per data di assunzione. I criteri seguenti nella sezione [Schemi progettuali per la tabella](#table-design-patterns) descrivono come alternare l'ordinamento per le entità:  

* [Criterio per indice secondario intrapartizione](#intra-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori di `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.  
* [Criterio per indice secondario interpartizione](#inter-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori di `RowKey` diversi in partizioni di tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.
* [Criterio della parte finale del log](#log-tail-pattern): recupera le entità *n* aggiunte più di recente a una partizione con un valore di `RowKey` che usa un ordinamento inverso di data e ora.  

## <a name="design-for-data-modification"></a>Progettazione per la modifica dei dati
Questa sezione esamina le considerazioni relative alla progettazione per ottimizzare inserimenti, aggiornamenti ed eliminazioni. In alcuni casi, è necessario valutare il compromesso tra le progettazioni che ottimizzano le query su progetti che ottimizzano la modifica dei dati. Questa valutazione è analoga a quanto avviene nelle progettazioni per i database relazionali, anche se le tecniche per la gestione dei compromessi di progettazione sono diverse in un database relazionale. La sezione [Schemi progettuali per la tabella](#table-design-patterns) descrive in dettaglio alcuni criteri di progettazione per l'archiviazione tabelle ed evidenzia alcuni compromessi. In pratica si vedrà che molte progettazioni ottimizzate per le query delle entità vanno bene anche per la modifica delle entità.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione
Per aggiornare o eliminare un'entità, è necessario poterla identificare usando i valori `PartitionKey` e `RowKey`. In questo senso, la scelta di `PartitionKey` e `RowKey` per la modifica delle entità deve seguire criteri simili a quelli usati nella scelta per supportare le query di puntamento. Lo scopo è identificare le entità nel modo più efficiente possibile. Si vuole evitare di usare una scansione di tabella o una partizione inefficiente per ricercare un'entità al fine di individuare i valori di `PartitionKey` e `RowKey` che è necessario aggiornare o eliminare.  

I criteri seguenti nella sezione [Schemi progettuali per la tabella](#table-design-patterns) consentono di ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione:  

* [Criterio di eliminazione di un volume elevato](#high-volume-delete-pattern): abilita l'eliminazione di un volume elevato di entità mediante l'archiviazione di tutte le entità per l'eliminazione simultanea nella relativa tabella separata. Per eliminare le entità, eliminare la tabella.  
* [Criterio di serie di dati](#data-series-pattern): Archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  
* [Criterio di entità di dimensioni molto grandi](#wide-entities-pattern): Usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  
* [Criterio di entità di grandi dimensioni](#large-entities-pattern): Usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Assicurare la coerenza nelle entità archiviate
L'altro aspetto importante che influisce sulla scelta delle chiavi per ottimizzare la modifica dei dati è come assicurare la coerenza usando le transazioni atomiche. È possibile usare una transazione EGT solo per agire sulle entità archiviate nella stessa partizione.  

I criteri seguenti nella sezione [Schemi progettuali per la tabella](#table-design-patterns) descrivono la gestione della coerenza:  

* [Criterio per indice secondario intrapartizione](#intra-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori di `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.  
* [Criterio per indice secondario interpartizione](#inter-partition-secondary-index-pattern): archivia più copie di ogni entità usando valori di `RowKey` diversi in partizioni o tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.  
* [Criterio per transazioni con coerenza finale](#eventually-consistent-transactions-pattern): abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.
* [Criterio per entità indice](#index-entities-pattern): mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  
* [Criterio di denormalizzazione](#denormalization-pattern): combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di puntamento.  
* [Criterio di serie di dati](#data-series-pattern): archivia serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

Per altre informazioni, vedere la sezione [Transazioni di gruppi di entità](#entity-group-transactions) più avanti, in questo articolo.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Verificare che la capacità della progettazione per modifiche efficienti consenta query efficienti
In molti casi, una progettazione per query efficienti consente modifiche efficienti, ma è consigliabile valutare sempre se questa condizione si applica a uno specifico scenario. Alcuni criteri nella sezione [Schemi progettuali per la tabella](#table-design-patterns) valutano in modo esplicito i compromessi tra la query e la modifica delle entità. È sempre consigliabile prendere in considerazione il numero di ogni tipo di operazione.  

I criteri seguenti nella sezione [Schemi progettuali per la tabella](#table-design-patterns) considerano i compromessi tra la progettazione per query efficienti e la progettazione per una modifica efficiente dei dati:  

* [Criterio per chiave composta](#compound-key-pattern): usa valori di `RowKey` composti per consentire a un client di cercare dati correlati con una sola query di puntamento.  
* [Criterio della parte finale del log](#log-tail-pattern): recupera le entità *n* aggiunte più di recente a una partizione con un valore di `RowKey` che usa un ordinamento inverso di data e ora.  

## <a name="encrypt-table-data"></a>Crittografare i dati delle tabelle
La libreria client di Archiviazione di Azure per .NET supporta la crittografia delle proprietà di entità stringa per le operazioni di inserimento e sostituzione. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono convertite nuovamente in stringhe dopo la decrittografia.    

Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Specificare un attributo `EncryptProperty` per le entità POCO che derivano da `TableEntity` oppure specificare un resolver di crittografia nelle opzioni di richiesta. Un resolver di crittografia è un delegato che partendo da una chiave di partizione, una chiave di riga e un nome di proprietà, restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client usa queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura in rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografa la proprietà A; in caso contrario crittografa le proprietà A e B). Non è necessario specificare queste informazioni durante la lettura o la query su entità.

Il merge non è attualmente supportato. Poiché un subset di proprietà potrebbe essere stato crittografato in precedenza con una chiave diversa, il semplice merge delle nuove proprietà e l'aggiornamento dei metadati comporta la perdita di dati. Il merge richiede chiamate a servizi aggiuntivi per la lettura dell'entità preesistente dal servizio o l'utilizzo di una nuova chiave per ogni proprietà. Entrambe le operazioni non sono idonee per motivi di prestazioni.     

Per informazioni sulla crittografia dei dati della tabella, vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relazioni tra i modelli
La compilazione di modelli di dominio è un passaggio chiave della progettazione di sistemi complessi. Il processo di modellazione, in genere, viene usato per identificare le entità e le relazioni tra di esse, per poter comprendere il dominio aziendale e informare la progettazione del sistema. Questa sezione illustra come convertire alcuni tipi comuni di relazioni presenti nei modelli di dominio in progettazioni per archiviazione tabelle. Il processo di mapping da un modello di dati logico a un modello di dati fisico basato su NoSQL è diverso da quello usato quando si progetta un database relazionale. La progettazione di database relazionali presuppone, in genere, un processo di normalizzazione dei dati ottimizzato per ridurre al minimo la ridondanza. Tale progettazione presuppone anche una funzionalità di query dichiarativa che riassume l'implementazione del funzionamento del database.  

### <a name="one-to-many-relationships"></a>Relazioni uno a molti
Le relazioni uno a molti tra gli oggetti del dominio aziendale sono frequenti: ad esempio, un reparto ha più dipendenti. Esistono modi diversi per implementare le relazioni uno a molti in archiviazione tabelle, ognuno dei quali presenta pro e contro che potrebbero essere rilevanti per un particolare scenario.  

Si consideri l'esempio di una grande azienda multinazionale con decine di migliaia di reparti ed entità dipendenti. Ogni reparto ha tanti dipendenti e ogni dipendente è associato a un reparto specifico. Un approccio prevede l'archiviazione di entità reparto e dipendente separate, come segue:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Immagine che mostra un'entità reparto e un'entità dipendente":::

Questo esempio illustra una relazione uno a molti implicita tra i tipi, basata sul valore `PartitionKey`. Ogni reparto può avere più dipendenti.  

Questo esempio mostra anche un'entità reparto e le relative entità dipendente nella stessa partizione. È possibile scegliere di usare partizioni, tabelle o anche account di archiviazione diversi per ogni tipo di entità.  

Un approccio alternativo prevede la denormalizzazione dei dati e l'archiviazione delle sole entità dipendente con i dati di reparto denormalizzati, come illustrato nell'esempio seguente. In questo particolare scenario, l'approccio denormalizzato potrebbe non essere quello più adatto se si deve essere in grado di cambiare i dettagli di un responsabile di reparto. A tale scopo, sarà necessario aggiornare ogni dipendente del reparto.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Immagine dell'entità dipendente":::

Per ulteriori informazioni, vedere il [Modello di denormalizzazione](#denormalization-pattern) più avanti in questa guida.  

La tabella seguente riepiloga i pro e i contro di ogni approccio per l'archiviazione delle entità dipendente e reparto con una relazione uno a molti. Considerare anche la frequenza con cui si prevede di eseguire varie operazioni. Potrebbe essere accettabile includere nella progettazione un'operazione costosa, se tale operazione viene eseguita raramente.  

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
<li>Per alcune attività client, potrebbe essere necessario recuperare sia un'entità dipendente che un'entità reparto.</li>
<li>Le operazioni di archiviazione vengono eseguite nella stessa partizione. Con volumi di transazioni elevati, potrebbero generare un'area sensibile.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto con una transazione dei gruppi di entità.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipi di entità distinti, partizioni, tabelle o account di archiviazione diversi</td>
<td>
<ul>
<li>È possibile aggiornare un'entità reparto o un'entità dipendente con un'unica operazione.</li>
<li>Con volumi di transazioni elevati, può essere più facile distribuire il carico tra più partizioni.</li>
</ul>
</td>
<td>
<ul>
<li>Per alcune attività client, potrebbe essere necessario recuperare sia un'entità dipendente che un'entità reparto.</li>
<li>Non è possibile usare le transazioni dei gruppi di entità per mantenere la coerenza quando si aggiorna/inserisce/elimina un dipendente e si aggiorna un reparto. ad esempio quando si aggiorna un conteggio dipendenti in un'entità reparto.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto con una transazione dei gruppi di entità.</li>
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
<li>Mantenere la coerenza può risultare costoso, se è necessario aggiornare le informazioni sui reparti, perché si dovrebbero aggiornare tutti i dipendenti di un reparto.</li>
</ul>
</td>
</tr>
</table>

Per scegliere tra queste opzioni e per stabilire quali siano i pro e i contro più importanti, è necessario considerare gli scenari specifici dell'applicazione. Ad esempio, con quale frequenza si modificano le entità reparto? Le informazioni aggiuntive sul reparto sono necessarie per tutte le query dei dipendenti? Quanto sono vicini i limiti di scalabilità nelle partizioni o nell'account di archiviazione?  

### <a name="one-to-one-relationships"></a>Relazioni uno a uno
I modelli di dominio possono includere relazioni uno a uno tra le entità. Se si deve implementare una relazione uno a uno in archiviazione tabelle, è necessario scegliere anche come collegare le due entità correlate quando bisogna recuperarle entrambe. Questo collegamento può essere implicito, ovvero basato su una convenzione nei valori chiave, o esplicito, ovvero basato sull'archiviazione di un collegamento sotto forma di valori `PartitionKey` e `RowKey` di ogni entità per ogni entità correlata. Per informazioni utili a stabilire se archiviare le entità correlate nella stessa partizione, vedere la sezione [Relazioni uno a molti](#one-to-many-relationships).  

Alcune considerazioni sull'implementazione potrebbero far decidere di implementare le relazioni uno a uno in archiviazione tabelle:  

* Gestione di entità di grandi dimensioni. Per altre informazioni, vedere [Criterio di entità di grandi dimensioni](#large-entities-pattern).  
* Implementazione di controlli di accesso. Per altre informazioni, vedere [Controllo dell'accesso con le firme di accesso condiviso](#control-access-with-shared-access-signatures).  

### <a name="join-in-the-client"></a>Join nel client
Anche se esistono alcuni modi per modellare le relazioni in archiviazione tabelle, ricordare che i due principali motivi per usare archiviazione tabelle sono la scalabilità e le prestazioni. Se ci si accorge che si stanno modellando troppe relazioni che compromettono le prestazioni e la scalabilità della soluzione, è consigliabile chiedersi se sia necessario compilare tutte le relazioni tra i dati nella progettazione tabelle. È possibile semplificare la progettazione e migliorare la scalabilità e le prestazioni della soluzione permettendo all'applicazione client di eseguire i join necessari.  

Ad esempio, se si dispone di tabelle di piccole dimensioni che contengono dati che non cambiano spesso, è possibile recuperare i dati una sola volta e memorizzarli nella cache del client. Questo consente di evitare round trip ripetuti per il recupero degli stessi dati. Negli esempi esaminati in questa guida, il set di reparti in una piccola organizzazione è probabilmente ridotto e viene modificato raramente. Questo lo rende un candidato ideale per i dati che vengono scaricati una sola volta tramite un'applicazione client e memorizzati nella cache come dati di ricerca.  

### <a name="inheritance-relationships"></a>Relazioni di ereditarietà
Se l'applicazione client usa un set di classi che fanno parte di una relazione di ereditarietà per rappresentare le entità aziendali, è facile rendere permanenti le entità nel servizio tabelle. Ad esempio, nell'applicazione client potrebbe essere definito il set di classi seguente, dove `Person` è una classe astratta.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Diagramma delle relazioni di ereditarietà":::

È possibile salvare in modo permanente le istanze delle due classi concrete in archiviazione tabelle usando una sola tabella `Person`. Usare le entità che sono simili alle seguenti:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Immagine che mostra le entità cliente e dipendente":::

Per altre informazioni sull'uso di più tipi di entità nella stessa tabella del codice client, vedere [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types) più avanti in questa guida. In questa sezione sono disponibili esempi su come riconoscere il tipo di entità nel codice del client.  

## <a name="table-design-patterns"></a>Modelli di progettazione tabella
Nelle sezioni precedenti è stato illustrato come ottimizzare la progettazione della tabella sia per il recupero dei dati di entità mediante query che per l'inserimento, l'aggiornamento e l'eliminazione dei dati di entità. Questa sezione descrive alcuni criteri adatti all'uso con archiviazione tabelle. Descrive anche come risolvere praticamente alcuni problemi e i compromessi evidenziati in precedenza in questa guida. Il diagramma seguente contiene un riepilogo delle relazioni tra i diversi criteri:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="Diagramma degli schemi progettuali per la tabella":::

La mappa dei criteri evidenzia alcune relazioni tra i criteri (blu) e gli anti-criteri (arancione) documentati in questa guida. Ovviamente esistono molti altri modelli utili. Ad esempio, uno degli scenari chiave per archiviazione tabelle è l'uso del [criterio di vista materializzata](https://msdn.microsoft.com/library/azure/dn589782.aspx) del criterio [separazione di responsabilità per query e comandi](https://msdn.microsoft.com/library/azure/jj554200.aspx).  

### <a name="intra-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archivia più copie di ogni entità usando valori di `RowKey` diversi nella stessa partizione. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi. È possibile mantenere la coerenza negli aggiornamenti tra copie usando le transazioni dei gruppi di entità.  

#### <a name="context-and-problem"></a>Contesto e problema
Archiviazione tabelle indicizza automaticamente le entità usando i valori `PartitionKey` e `RowKey`. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente usando questi valori. Ad esempio, usando la struttura della tabella seguente, un'applicazione client può usare una query di puntamento per recuperare una singola entità dipendente attraverso il nome del reparto e l'ID dipendente, ovvero i valori `PartitionKey`e `RowKey`. Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Immagine dell'entità dipendente":::

Se si desidera trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Il motivo è che archiviazione tabelle non rende disponibili gli indici secondari. Inoltre, non esiste un'opzione per richiedere un elenco di dipendenti ordinato in modo diverso rispetto all'ordine `RowKey`.  

#### <a name="solution"></a>Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità usando per ogni copia un valore `RowKey` diverso. Se si archivia un'entità con le strutture seguenti, è possibile recuperare in modo efficiente entità dipendente in base all'ID dipendente o all'indirizzo di posta elettronica. I valori di prefisso per `RowKey`, `empid_` e `email_` consentono di eseguire query per un singolo dipendente o un intervallo di dipendenti usando un intervallo di indirizzi di posta elettronica o di ID dipendente.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Immagine che mostra l'entità dipendente con valori RowKey diversi":::

I due criteri di filtro seguenti, uno che ricerca per ID dipendente e uno che ricerca per indirizzo di posta elettronica, specificano entrambi le query di puntamento:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223)  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se si esegue una query su un intervallo di entità dipendente, è possibile specificare un intervallo ordinato per ID dipendente o un intervallo ordinato per indirizzo di posta elettronica. Eseguire una query per le entità con il prefisso appropriato in `RowKey`.  

* Per trovare tutti i dipendenti nel reparto vendite con un ID dipendente compreso tra 000100 e 000199 usare: $filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000100') and (RowKey le 'empid_000199')  
* Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con la lettera "a" usare: $filter=(PartitionKey eq 'Sales') and (RowKey ge 'email_a') and (RowKey lt 'email_b')  
  
La sintassi di filtro usata negli esempi precedenti deriva dall'API REST di archiviazione tabelle. Per altre informazioni, vedere [Entità di query](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* L'uso di archiviazione tabelle è relativamente economico, pertanto l'aumento dei costi dovuto all'archiviazione di dati duplicati non dovrebbe rappresentare un problema. È però consigliabile valutare sempre il costo del progetto in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che verranno eseguite dall'applicazione client.  
* Poiché le entità indice secondario vengono archiviate nella stessa partizione delle entità originali, assicurarsi di non superare gli obiettivi di scalabilità delle singole partizioni.  
* Per mantenere la coerenza tra entità duplicate è possibile usare transazioni ETG, che consentono di aggiornare le due copie dell'entità in modo atomico. A questo scopo è necessario archiviare tutte le copie di un'entità nella stessa partizione. Per altre informazioni, vedere la sezione [Usare transazioni di gruppi di entità](#entity-group-transactions).  
* Il valore usato per `RowKey` deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* Il riempimento dei valori numerici in `RowKey` (ad esempio per l'ID dipendente 000223) rende possibile applicare l'ordinamento e il filtro corretto in base ai limiti superiori e inferiori.  
* Non è necessario duplicare tutte le proprietà dell'entità. Se ad esempio le query che eseguono la ricerca di entità usando l'indirizzo di posta elettronica in `RowKey` non richiedono mai l'età del dipendente, queste entità potrebbero avere la struttura seguente:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Immagine dell'entità dipendente":::

* È in genere preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query anziché usare una query per individuare un'entità e un'altra query per cercare i dati richiesti.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando:

- L'applicazione client deve recuperare le entità usando chiavi diverse.
- Il client deve recuperare le entità in ordinamenti diversi.
- È possibile identificare ogni entità usando diversi valori univoci.

È però necessario assicurarsi che, durante l'esecuzione di ricerche di entità con valori di `RowKey` diversi, non vengano superati i limiti di scalabilità della partizione.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per indice secondario interpartizione](#inter-partition-secondary-index-pattern)
* [Modello per chiave composta](#compound-key-pattern)
* [Transazioni di gruppi di entità](#entity-group-transactions)
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archivia più copie di ogni entità usando valori di `RowKey` diversi in partizioni o tabelle separate. Questo consente ricerche rapide ed efficienti e ordinamenti alternativi con valori di `RowKey` diversi.  

#### <a name="context-and-problem"></a>Contesto e problema
Archiviazione tabelle indicizza automaticamente le entità usando i valori `PartitionKey` e `RowKey`. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente usando questi valori. Ad esempio, usando la struttura della tabella seguente, un'applicazione client può usare una query di puntamento per recuperare una singola entità dipendente attraverso il nome del reparto e l'ID dipendente, ovvero i valori `PartitionKey`e `RowKey`. Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Rappresentazione grafica dell'entità Employee":::[9]

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Il motivo è che archiviazione tabelle non rende disponibili gli indici secondari. Inoltre, non esiste un'opzione per richiedere un elenco di dipendenti ordinato in modo diverso rispetto all'ordine `RowKey`.  

Si prevede un volume elevato di transazioni per queste entità e si vuole ridurre al minimo il rischio che la velocità di archiviazione tabelle limiti il client.  

#### <a name="solution"></a>Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità usando per ogni copia un valore di `PartitionKey` e `RowKey` diverso. Se si archivia un'entità con le strutture seguenti, è possibile recuperare in modo efficiente entità dipendente in base all'ID dipendente o all'indirizzo di posta elettronica. I valori di prefisso per `PartitionKey`, `empid_` e `email_` consentono di identificare l'indice da usare per una query.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Immagine che mostra l'entità dipendente con indice primario e l'entità dipendente con indice secondario":::

I due criteri di filtro seguenti, uno che ricerca per ID dipendente e uno che ricerca per indirizzo di posta elettronica, specificano entrambi le query di puntamento:  

* $filter=(PartitionKey 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se si esegue una query su un intervallo di entità dipendente, è possibile specificare un intervallo ordinato per ID dipendente o un intervallo ordinato per indirizzo di posta elettronica. Eseguire una query per le entità con il prefisso appropriato in `RowKey`.  

* Per trovare tutti i dipendenti del reparto vendite con un ID dipendente nell'intervallo compreso tra **000100** e **000199** ordinati in base all'ID dipendente, usare: $filter=(PartitionKey eq 'empid_Sales') and (RowKey ge '000100') and (RowKey le '000199')  
* Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con "a" ordinati in base all'indirizzo di posta elettronica usare: $filter=(PartitionKey eq 'email_Sales') and (RowKey ge 'a') and (RowKey lt 'b')  

Si noti che la sintassi di filtro usata negli esempi precedenti deriva dall'API REST di archiviazione tabelle. Per altre informazioni, vedere [Entità di query](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra le entità duplicate, è possibile usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per gestire le entità di indice primario e secondario.  
* L'uso dell'archiviazione tabelle è relativamente economico, pertanto l'aumento dei costi dovuto all'archiviazione di dati duplicati non dovrebbe rappresentare un problema. È però consigliabile valutare sempre il costo del progetto in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che verranno eseguite dall'applicazione client.  
* Il valore usato per `RowKey` deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* Il riempimento dei valori numerici in `RowKey` (ad esempio per l'ID dipendente 000223) rende possibile applicare l'ordinamento e il filtro corretto in base ai limiti superiori e inferiori.  
* Non è necessario duplicare tutte le proprietà dell'entità. Se ad esempio le query che eseguono la ricerca di entità usando l'indirizzo di posta elettronica in `RowKey` non richiedono mai l'età del dipendente, queste entità potrebbero avere la struttura seguente:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="Immagine che mostra l'entità dipendente con indice secondario":::

* In genere è preferibile archiviare i dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query anziché usare una query per individuare un'entità mediante l'indice secondario e un'altra per cercare i dati richiesti nell'indice primario.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando:

- L'applicazione client deve recuperare le entità usando chiavi diverse.
- Il client deve recuperare le entità in ordinamenti diversi.
- È possibile identificare ogni entità usando diversi valori univoci.

Usare questo criterio quando si vuole evitare il superamento dei limiti di scalabilità della partizione durante l'esecuzione di ricerche di entità con i diversi valori di `RowKey`.  

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
Le transazioni ETG consentono l'esecuzione di transazioni atomiche tra più entità che condividono la stessa chiave di partizione. Per motivi di scalabilità e prestazioni, si può scegliere di archiviare le entità con requisiti di coerenza in partizioni separate o in un sistema di archiviazione separato. In uno scenario di questo tipo, non è possibile usare le transazioni dei gruppi di entità per mantenere la coerenza. Ad esempio, potrebbe essere necessario mantenere la coerenza finale tra:  

* Entità archiviate in due partizioni diverse nella stessa tabella, in tabelle diverse o in account di archiviazione diversi.  
* Un'entità archiviata in archiviazione tabelle e un BLOB archiviato in archiviazione BLOB.  
* Un'entità archiviata in archiviazione tabelle e un file in un file system.  
* Un'entità archiviata in archiviazione tabelle, ma indicizzata con Ricerca cognitiva di Azure.  

#### <a name="solution"></a>Soluzione
Usando le code di Azure, è possibile implementare una soluzione che offre coerenza finale tra due o più partizioni o sistemi di archiviazione.

Per illustrare questo approccio, si supponga di dover archiviare le entità relative ai dipendenti precedenti. Queste entità sono raramente oggetto di query e devono essere escluse da tutte le attività associate ai dipendenti attuali. Per implementare questo requisito è necessario archiviare i dipendenti attivi nella tabella dei dipendenti **Correnti** e i dipendenti precedenti nella tabella **Archivio**. Per archiviare un dipendente è necessario eliminare l'entità dalla tabella **Correnti** e aggiungerla alla tabella **Archivio**.

Tuttavia, non è possibile usare una transazione dei gruppi di entità per eseguire queste due operazioni. Per evitare il rischio che, a causa di un errore, un'entità venga visualizzata in entrambe le tabelle o in nessuna di esse, l'operazione di archiviazione deve garantire la coerenza finale. Il diagramma seguente illustra in sequenza i passaggi di questa operazione.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="Diagramma della soluzione ai fini della coerenza finale":::

Un client avvia l'operazione di archiviazione inserendo un messaggio in una coda di Azure, in questo esempio per l'archiviazione del dipendente 456. Un ruolo di lavoro esegue il polling della coda per individuare i nuovi messaggi. Quando ne trova uno, legge il messaggio e lascia una copia nascosta nella coda. Successivamente, il ruolo di lavoro recupera una copia dell'entità dalla tabella dei dipendenti **Correnti**, inserisce una copia nella tabella dei dipendenti **Archiviati** e quindi elimina l'originale dalla tabella dei dipendenti **Correnti**. Infine, se nei passaggi precedenti non si sono verificati errori, il ruolo di lavoro elimina il messaggio nascosto dalla coda.  

In questo esempio, il passaggio 4 del diagramma inserisce il dipendente nella tabella **Archivio**. Potrebbe aggiungere il dipendente a un BLOB in archiviazione BLOB o un file in un file system.  

#### <a name="recover-from-failures"></a>Ripristino da errori
È importante che le operazioni nei passaggi 4-5 del diagramma siano *idempotenti* nei casi in cui il ruolo di lavoro deve riavviare l'operazione di archiviazione. Se si sta usando archiviazione tabelle, per il passaggio 4 usare un'operazione "insert or replace"; per il passaggio 5, usare un'operazione "delete if exists" nella libreria client in uso. Se si sta usando un altro sistema di archiviazione, usare un'operazione idempotente appropriata.  

Se il ruolo di lavoro non completa mai il passaggio 6 nel diagramma, dopo un timeout il messaggio ricompare nella coda, pronto per una nuova elaborazione da parte del ruolo di lavoro. Il ruolo di lavoro può controllare il numero di volte in cui un messaggio nella coda è stato letto e, se necessario, contrassegnarlo come messaggio non elaborabile da analizzare inviandolo a una coda separata. Per altre informazioni sulla lettura dei messaggi in coda e la verifica del numero di rimozioni dalla coda, vedere [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alcuni errori di archiviazione tabelle e archiviazione code sono temporanei e l'applicazione client deve includere la logica di ripetizione dei tentativi appropriata per gestirli.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione non prevede l'isolamento delle transazioni. Ad esempio, un client potrebbe leggere le tabelle dei dipendenti **Correnti** e **Archivio** mentre il ruolo di lavoro è tra i passaggi 4-5 del diagramma e ottenere una vista incoerente dei dati. Alla fine i dati saranno coerenti.  
* È necessario assicurarsi che i passaggi 4-5 siano idempotenti per garantire la coerenza finale.  
* È possibile ridimensionare la soluzione usando più code e istanze del ruolo di lavoro.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si desidera garantire la coerenza finale tra entità esistenti in tabelle o partizioni diverse. È possibile estendere il criterio per garantire la coerenza finale per le operazioni tra archiviazione tabelle e archiviazione BLOB e altre origini dati di archiviazione non Azure, quali un database o un file system.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Unione o sostituzione](#merge-or-replace)  

> [!NOTE]
> Se l'isolamento delle transazioni è importante per la soluzione, è consigliabile riprogettare le tabelle per consentire l'uso delle transazioni di gruppi di entità.  
> 
> 

### <a name="index-entities-pattern"></a>Modello per entità di indice
mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

#### <a name="context-and-problem"></a>Contesto e problema
Archiviazione tabelle indicizza automaticamente le entità usando i valori `PartitionKey` e `RowKey`. Consente a un'applicazione client di recuperare un'entità in modo efficiente mediante una query di puntamento. Ad esempio, usando la struttura della tabella seguente, un'applicazione client può recuperare in modo efficiente una singola entità dipendente con il nome del reparto e l'ID dipendente, ovvero i valori `PartitionKey` e `RowKey`.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Immagine dell'entità dipendente":::

Se si desidera poter recuperare un elenco di entità dipendente anche in base al valore di un'altra proprietà non univoca, ad esempio il cognome, è necessario usare un'analisi della partizione meno efficiente. Questa analisi rileva le corrispondenze piuttosto che usare un indice per la ricerca diretta. Il motivo è che archiviazione tabelle non rende disponibili gli indici secondari.  

#### <a name="solution"></a>Soluzione
Per consentire la ricerca per cognome con la struttura delle entità precedente, è necessario gestire elenchi di ID dipendente. Per recuperare le entità dipendente con un determinato cognome, ad esempio Jones, è necessario innanzitutto individuare l'elenco di ID relativi ai dipendenti con il cognome Jones e quindi recuperare tali entità dipendente. Per l'archiviazione degli elenchi di ID dipendente sono disponibili tre opzioni principali:  

* Usare l'archiviazione BLOB.  
* Creare entità di indice nella stessa partizione delle entità dipendente.  
* Creare entità di indice in una tabella o una partizione separata.  

Opzione 1: Usare l'archivio BLOB  

Creare un BLOB per ogni cognome univoco e archiviare in ogni BLOB un elenco dei valori `PartitionKey` (reparto) e `RowKey` (ID dipendente) per i dipendenti con questo cognome. Quando si aggiunge o elimina un dipendente, verificare la coerenza finale tra il contenuto del BLOB pertinente e le entità dipendente.  

Opzione 2: creare entità di indice nella stessa partizione  

Usare le entità indice che archiviano i dati seguenti:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Immagine che mostra un'entità dipendente con una stringa contenente un elenco di ID dipendente con lo stesso cognome":::

La proprietà `EmployeeIDs` contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in `RowKey`.  

I passaggi seguenti illustrano il processo da seguire per aggiungere un nuovo dipendente. In questo esempio si aggiunge al reparto vendite un dipendente con ID 000152 e cognome Jones:  

1. Recuperare l'entità indice con il valore di `PartitionKey` "Sales" e il valore di `RowKey` "Jones". Salvare il valore ETag dell'entità per usarlo nel passaggio 2.  
2. Creare una transazione di gruppi di entità, ovvero un'operazione batch, che inserisca la nuova entità dipendente, con valore di `PartitionKey` "Sales" e valore di `RowKey` "000152", e aggiorni l'entità indice, con valore di `PartitionKey` "Sales" e valore di `RowKey` "Jones". La transazione di gruppi di entità esegue questa operazione aggiungendo il nuovo ID dipendente all'elenco nel campo EmployeeIDs. Per altre informazioni sulle transazioni di gruppi di entità, vedere [Transazioni di gruppi di entità](#entity-group-transactions).  
3. Se la transazione di gruppi di entità ha esito negativo a causa di un errore di concorrenza ottimistica, vale a dire che un altro utente ha modificato l'entità indice, è necessario ricominciare dal passaggio 1.  

Se si usa la seconda opzione, è possibile adottare un approccio simile all'eliminazione di un dipendente. Modificare il cognome di un dipendente è un'operazione leggermente più complessa, in quanto è necessario eseguire una transazione di gruppi di entità che aggiorna tre entità: l'entità dipendente, l'entità indice per il cognome precedente e l'entità indice per il nuovo cognome. È necessario recuperare ogni entità prima di apportare una modifica, per recuperare i valori ETag da usare in seguito per eseguire gli aggiornamenti con la concorrenza ottimistica.  

I passaggi seguenti illustrano il processo da seguire per cercare tutti i dipendenti di un reparto con un determinato cognome. In questo esempio si cercano tutti i dipendenti del reparto vendite il cui cognome è Jones:  

1. Recuperare l'entità indice con il valore di `PartitionKey` "Sales" e il valore di `RowKey` "Jones".  
2. Analizzare l'elenco di ID dipendente nel campo `EmployeeIDs`.  
3. Se sono necessarie informazioni aggiuntive su ognuno dei dipendenti, ad esempio gli indirizzi di posta elettronica, recuperare ognuna delle entità dipendente usando il valore di `PartitionKey` "Sales" e i valori di `RowKey` dall'elenco di dipendenti ottenuto nel passaggio 2.  

Opzione 3: creare entità di indice in una tabella o in una partizione separata  

Per questa opzione, usare le entità di indice che archiviano i dati seguenti:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="Immagine che mostra un'entità dipendente con una stringa contenente un elenco di ID dipendente con lo stesso cognome":::

La `EmployeeIDs` proprietà contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in `RowKey` e `PartitionKey` .  

Non è possibile usare transazioni di gruppi di entità per mantenere la coerenza, in quanto le entità indice si trovano in una partizione separata rispetto alle entità dipendente. Verificare la coerenza finale tra le entità indice e le entità dipendente.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione richiede almeno due query per recuperare le entità corrispondenti: una sulle entità indice per ottenere l'elenco di valori di `RowKey` e altre query per il recupero di ogni entità dell'elenco.  
* Poiché una singola entità ha una dimensione massima di 1 MB, le opzioni 2 e 3 della soluzione presuppongono che l'elenco di ID dipendente per un determinato cognome non sia mai più grande di 1 MB. Se è probabile che l'elenco di ID dipendente abbia dimensioni superiori a 1 MB, usare l'opzione 1 e archiviare i dati di indice nell'archiviazione BLOB.  
* Se si usa l'opzione 2, ovvero si usano le transazioni di gruppi di entità per gestire l'aggiunta e l'eliminazione dei dipendenti e si modifica il cognome di un dipendente, è necessario valutare se il volume delle transazioni raggiungerà i limiti di scalabilità in una determinata partizione. In questo caso, prendere in considerazione una soluzione con coerenza finale (opzione 1 o 3), che usa le code per gestire le richieste di aggiornamento e consente di archiviare le entità indice in una partizione separata dalle entità dipendente.  
* L'opzione 2 di questa soluzione presuppone la ricerca per cognome all'interno di un reparto. Ad esempio, si vuole recuperare un elenco di dipendenti il cui cognome è Jones nel reparto vendite. Se si desidera poter cercare tutti i dipendenti il cui cognome è Jones nell'intera organizzazione, usare l'opzione 1 oppure l'opzione 3.
* È possibile implementare una soluzione basata su code che offre coerenza finale. Per altre informazioni dettagliate, vedere [Criterio per transazioni con coerenza finale](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo criterio quando si desidera cercare un set di entità che condividono un valore della proprietà comune, ad esempio tutti i dipendenti con il cognome Jones.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Modello di denormalizzazione
combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.  

#### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale, in genere i dati vengono normalizzati per rimuovere i duplicati che si generano quando le query recuperano dati da più tabelle. Se si normalizzano i dati nelle tabelle di Azure, è necessario eseguire più round trip dal client al server per recuperare i dati correlati. Con la struttura di tabella seguente, ad esempio, sono necessari due round trip per recuperare i dettagli di un reparto. Uno recupera l'entità reparto che include l'ID del responsabile e l'altra recupera i dettagli del responsabile in un'entità dipendente.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Immagine dell'entità reparto e dell'entità dipendente":::

#### <a name="solution"></a>Soluzione
Anziché archiviare i dati in due entità separate, denormalizzare i dati e conservare una copia dei dettagli sul manager nell'entità reparto. Ad esempio:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="Immagine di un'entità reparto denormalizzata e combinata":::

Archiviando le entità reparto con queste proprietà, è possibile recuperare tutti i dettagli necessari relativi a un reparto mediante una query di puntamento.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Archiviare alcuni dati due volte comporta un aumento dei costi. Il vantaggio in termini di prestazioni risultante da un minor numero di richieste all'archiviazione tabelle, in genere, è maggiore rispetto all'aumento marginale dei costi di archiviazione. Questo costo è parzialmente compensato da una riduzione del numero di transazioni necessarie per recuperare i dettagli di un reparto.  
* È necessario mantenere la coerenza tra le due entità in cui sono archiviate le informazioni sui manager. È possibile gestire il problema di coerenza usando transazioni di gruppi di entità per aggiornare più entità in un'unica transazione atomica. In questo caso, l'entità reparto e l'entità dipendente per il responsabile del reparto vengono archiviate nella stessa partizione.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario cercare spesso informazioni correlate. Questo modello riduce il numero di query che il client deve eseguire per recuperare i dati necessari.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Modello per chiave composta
Usa valori di `RowKey` composti per consentire a un client di cercare dati correlati con una sola query di puntamento.  

#### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale è normale usare join nelle query per restituire dati correlati al client in una singola query. Ad esempio, si può usare l'ID dipendente per cercare un elenco di entità correlate che contengono i dati relativi alle prestazioni e alle valutazioni per tale dipendente.  

Si supponga di archiviare le entità dipendente in archiviazione tabelle usando la struttura seguente:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Immagine dell'entità dipendente":::

È anche necessario archiviare i dati cronologici relativi alle valutazioni e alle prestazioni per ogni anno in cui il dipendente ha lavorato presso l'organizzazione, nonché poter accedere a queste informazioni in base all'anno. Una possibilità consiste nel creare un'altra tabella di archiviazione delle entità con la struttura seguente:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Immagine dell'entità revisione dipendente":::

Con questo approccio è possibile decidere di duplicare alcune informazioni, ad esempio nome e cognome, nella nuova entità, in modo da poter recuperare i dati con una singola richiesta. Non è tuttavia possibile mantenere la coerenza assoluta, in quanto non si può usare una transazione di gruppi di entità per aggiornare le entità in modo atomico.  

#### <a name="solution"></a>Soluzione
Archiviare un nuovo tipo di entità nella tabella originale usando entità con la struttura seguente:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Immagine dell'entità dipendente con chiave composta":::

Si noti che `RowKey` è ora una chiave composta, costituita dall'ID dipendente e dall'anno dei dati di revisione. Questo consente di recuperare le prestazioni del dipendente e di esaminare i dati con una singola richiesta per una singola entità.  

L'esempio seguente illustra come recuperare tutti i dati di valutazione per uno specifico dipendente (ad esempio il dipendente 000123 del reparto vendite):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È consigliabile usare un carattere separatore appropriato che semplifichi l'analisi del valore `RowKey`, ad esempio **000123_2012**.  
* Si sta archiviando l'entità nella stessa partizione di altre entità che contengono dati correlati per lo stesso dipendente, dunque è possibile usare transazioni di gruppi di entità per mantenere la coerenza assoluta.
* Per determinare se questo criterio è appropriato, considerare la frequenza con cui si eseguiranno query sui dati. Ad esempio, se si accede raramente ai dati di revisione e spesso ai dati principali sul dipendente, è consigliabile conservarli come entità separate.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare una o più entità correlate su cui si eseguono query frequenti.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)  
* [Usare tipi di entità eterogenei](#work-with-heterogeneous-entity-types)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Modello della parte finale del log
Consente di recuperare le entità *n* aggiunte più di recente a una partizione con un valore di `RowKey` che usa un ordinamento inverso di data e ora.  

> [!NOTE]
> I risultati della query restituiti dall'API Tabella di Azure in Azure Cosmos DB non sono ordinati per chiave di riga o chiave di partizione. Di conseguenza, questo criterio è adatto per l'archiviazione tabelle e non per Azure Cosmos DB. Per un elenco dettagliato delle differenze di funzionalità, consultare le [differenze tra l'API Tabella in Azure Cosmos DB e archiviazione tabelle di Azure](table-api-faq.md#table-api-vs-table-storage).

#### <a name="context-and-problem"></a>Contesto e problema
Un requisito comune è poter recuperare le entità create più di recente, ad esempio le ultime dieci note di rimborso spese inviate da un dipendente. Le query sulla tabella supportano un'operazione di query `$top` per restituire le prime *n* entità da un set. Non esiste un'operazione di query equivalente che restituisca le ultime *n* entità in un set.  

#### <a name="solution"></a>Soluzione
Archiviare le entità usando un valore di `RowKey` che usa naturalmente un ordinamento inverso di data e ora, in modo che la voce più recente sia sempre la prima della tabella.  

Ad esempio, per poter recuperare le ultime dieci note di rimborso spese inviate da un dipendente, è possibile usare un valore di tick inverso derivato dal valore di data/ora corrente. L'esempio di codice C# seguente illustra un modo per creare un valore "invertedTicks" appropriato per un valore di `RowKey` che ordina dal più recente al meno recente:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Per tornare al valore di data e ora, usare il codice seguente:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

La query sulla tabella ha un aspetto simile al seguente:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È necessario aggiungere zeri iniziali al valore di tick inverso per assicurarsi che il valore di stringa venga ordinato come previsto.  
* È necessario tenere presenti gli obiettivi di scalabilità a livello di partizione. Fare attenzione a non creare partizioni di aree sensibili.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo criterio quando si desidera accedere alle entità in ordine di data/ora inverso o quando è necessario accedere alle entità aggiunte più di recente.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Anti-modello prepend/append](#prepend-append-anti-pattern)  
* [Recuperare le entità](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Modello di eliminazione volume elevato
Abilita l'eliminazione di un volume elevato di entità mediante l'archiviazione di tutte le entità per l'eliminazione simultanea nella relativa tabella separata. Per eliminare le entità, eliminare la tabella.  

#### <a name="context-and-problem"></a>Contesto e problema
Molte applicazioni eliminano vecchi dati non più necessari a un'applicazione client o che l'applicazione ha archiviato in un altro supporto di archiviazione. Questi dati vengono in genere identificati da una data. Può ad esempio essere richiesta l'eliminazione dei record di tutte le richieste di accesso risalenti a oltre 60 giorni prima.  

Una possibile progettazione prevede l'uso della data e dell'ora della richiesta di accesso in `RowKey`:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Immagine dell'entità tentativo di accesso":::

Questo approccio evita aree sensibili della partizione perché l'applicazione può inserire ed eliminare entità accesso per ogni utente in una partizione separata. Tuttavia, può essere dispendioso in termini di denaro e tempo se si dispone di un numero elevato di entità. Prima di tutto, è necessario eseguire una scansione di tabella per identificare tutte le entità da eliminare; a questo punto è necessario eliminare ogni entità precedente. Si noti che è possibile ridurre il numero di round trip al server necessari per eliminare le entità precedenti raggruppando più richieste di eliminazione in EGT.  

#### <a name="solution"></a>Soluzione
Usare una tabella separata per ogni giorno di tentativi di accesso. È possibile usare la progettazione di entità riportata sopra per evitare aree sensibili durante l'inserimento di entità. L'eliminazione di entità meno recenti comporterà semplicemente l'eliminazione di una tabella al giorno (una singola operazione di archiviazione) invece della ricerca ed eliminazione di centinaia o migliaia di singole entità accesso ogni giorno.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La progettazione supporta altre modalità di uso dei dati da parte dell'applicazione, come la ricerca di entità specifiche, il collegamento con altri dati o la generazione di informazioni aggregate?  
* La progettazione consente di evitare hotspot durante l'inserimento di nuove entità?  
* Se si vuole riutilizzare lo stesso nome di tabella dopo l'eliminazione, prevedere un ritardo. È consigliabile usare sempre nomi di tabella univoci.  
* Prevedere una limitazione della velocità quando si usa per la prima volta una nuova tabella mentre archiviazione tabelle apprende i criteri di accesso e distribuisce le partizioni nei nodi. È necessario considerare la frequenza con cui è necessario creare nuove tabelle.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si dispone di un volume elevato di entità che è necessario eliminare contemporaneamente.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)
* [Modificare le entità](#modify-entities)  

### <a name="data-series-pattern"></a>Modello di serie di dati
Archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

#### <a name="context-and-problem"></a>Contesto e problema
Spesso un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente. L'applicazione potrebbe, ad esempio, registrare il numero di messaggi immediati che ogni dipendente invia ogni ora e quindi usare queste informazioni per tracciare il numero di messaggi inviati da ogni utente nelle 24 ore precedenti. Una progettazione potrebbe essere l'archiviazione di 24 entità per ogni dipendente:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="Immagine dell'entità statistica messaggio":::

Con questa progettazione è possibile individuare e aggiornare l'entità da aggiornare per ogni dipendente ogni volta che l'applicazione deve aggiornare il valore del numero di messaggi. Tuttavia, per recuperare le informazioni allo scopo di tracciare un grafico dell'attività per le 24 ore precedenti, è necessario recuperare 24 entità.  

#### <a name="solution"></a>Soluzione
Usare la progettazione seguente con una proprietà separata per archiviare il numero di messaggi per ogni ora:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Immagine che mostra le entità statistiche messaggio con proprietà separate":::

Con questa progettazione è possibile usare un'operazione di unione per aggiornare il numero di messaggi per un dipendente per un'ora specifica. A questo punto, è possibile recuperare tutte le informazioni necessarie per tracciare il grafico usando una richiesta per una singola entità.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Se la serie di dati completa non rientra in una singola entità (un'entità può contenere fino a 252 proprietà), usare un archivio dati alternativo, ad esempio un BLOB.  
* Se sono presenti più client che aggiornano un'entità contemporaneamente, usare **ETag** per implementare la concorrenza ottimistica. Se si dispone di molti client, potrebbe verificarsi un forte conflitto.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario aggiornare e recuperare una serie di dati associata a una singola entità.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello di entità di grandi dimensioni](#large-entities-pattern)  
* [Unione o sostituzione](#merge-or-replace)  
* [Criterio per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) (se si archiviano le serie di dati in un BLOB)  

### <a name="wide-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  

#### <a name="context-and-problem"></a>Contesto e problema
Una singola entità può avere più di 252 proprietà (escludendo le proprietà di sistema obbligatorie) e non può archiviare più di 1 MB di dati in totale. In un database relazionale è in genere possibile aggirare eventuali limiti sulle dimensioni di una riga aggiungendo una nuova tabella e applicando una relazione uno a uno tra di esse.  

#### <a name="solution"></a>Soluzione
Usando archiviazione tabelle, è possibile archiviare più entità per rappresentare un singolo oggetto aziendale di grandi dimensioni con più di 252 proprietà. Ad esempio, se si vuole archiviare un conteggio del numero di messaggi istantanei inviati da ogni dipendente negli ultimi 365 giorni, è possibile usare la progettazione seguente che si avvale di due entità con schemi diversi:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Immagine che mostra entità statistiche messaggio con Rowkey 01 ed entità statistiche messaggio con Rowkey 02":::

Per apportare una modifica che richiede l'aggiornamento di entrambe le entità per mantenerle sincronizzate tra loro, è possibile usare una transazione EGT. Diversamente, è possibile usare una singola operazione di unione per aggiornare il numero di messaggi per un giorno specifico. Per recuperare tutti i dati per un singolo dipendente, è necessario recuperare entrambe le entità. È possibile farlo con due richieste efficienti che usano sia un valore `PartitionKey` che un valore `RowKey`.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Quando si decide come implementare questo criterio, considerare quanto segue:  

* Il recupero di un'entità logica completa richiede almeno due transazioni di archiviazione, una per recuperare ogni entità fisica.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo criterio quando è necessario archiviare entità le cui dimensioni o il cui numero di proprietà superano i limiti per una singola entità in archiviazione tabelle.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni di gruppi di entità](#entity-group-transactions)
* [Unione o sostituzione](#merge-or-replace)

### <a name="large-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

#### <a name="context-and-problem"></a>Contesto e problema
Una singola entità non può archiviare più di 1 MB di dati in totale. Se una o più proprietà archiviano valori per cui le dimensioni totali dell'entità superano questo valore, non è possibile archiviare l'intera entità in archiviazione tabelle.  

#### <a name="solution"></a>Soluzione
Se l'entità supera le dimensioni di 1 MB perché una o più proprietà contengono una grande quantità di dati, è possibile archiviare i dati in archiviazione BLOB e quindi archiviare l'indirizzo del BLOB in una proprietà dell'entità. Ad esempio, è possibile archiviare la foto di un dipendente nell'archiviazione BLOB e archiviare un collegamento alla foto nella proprietà `Photo` dell'entità dipendente:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="Immagine che mostra l'entità dipendente con stringa per una foto che punta all'archiviazione BLOB":::

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra l'entità in archiviazione tabelle e i dati in archiviazione BLOB, usare il [Criterio per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per mantenere le identità.
* Il recupero di un'entità completa richiede almeno due transazioni di archiviazione: una per recuperare l'entità e un'altra per recuperare i dati BLOB.  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo criterio quando è necessario archiviare entità le cui dimensioni superano i limiti per una singola entità in archiviazione tabelle.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello di entità di grandi dimensioni](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Anti-modello prepend/append
Quando si dispone di un volume elevato di inserimenti, aumentare la scalabilità suddividendoli tra più partizioni.  

#### <a name="context-and-problem"></a>Contesto e problema
L'anteposizione o l'aggiunta di entità alle entità archiviate determina in genere l'aggiunta da parte dell'applicazione di nuove entità alla prima o ultima partizione di una sequenza di partizioni. In questo caso, tutti gli inserimenti in un determinato momento avvengono nella stessa partizione, creando un'area sensibile. Questo impedisce all'archiviazione tabelle di eseguire il bilanciamento del carico degli inserimenti tra più nodi e, probabilmente, consente all'applicazione di raggiungere gli obiettivi di scalabilità per la partizione. Si consideri ad esempio il caso di un'applicazione che registra l'accesso alla rete e alle risorse dei dipendenti. Una struttura dell'entità come la seguente può fare in modo che la partizione delle ore corrente diventi un'area sensibile, se il volume delle transazioni raggiunge l'obiettivo di scalabilità per una singola partizione:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="Immagine dell'entità dipendente":::

#### <a name="solution"></a>Soluzione
La struttura di un'entità alternativa seguente evita le aree sensibili in qualsiasi partizione specifica quando l'applicazione effettua la registrazione di eventi:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Immagine che mostra l'entità dipendente con RowKey composto da anno, mese, giorno, ora e ID evento":::

Si noti come in questo esempio entrambi i valori di `PartitionKey` e `RowKey` siano chiavi composte. Il valore di `PartitionKey` usa sia l'ID reparto che l'ID dipendente per distribuire la registrazione in più partizioni.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La struttura chiave alternativa che evita la creazione di partizioni critiche negli inserimenti supporta in modo efficiente le query effettuate dall'applicazione client?  
* Il volume delle transazioni previste è indicativo della probabilità che si raggiungano gli obiettivi di scalabilità per una singola partizione e si sia limitati da archiviazione tabelle?  

#### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Evitare l'anti-criterio di anteposizione/aggiunta quando il volume delle transazioni determinerà probabilmente una limitazione della velocità di archiviazione tabelle quando si accede con frequenza a una partizione.  

#### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello della parte finale del log](#log-tail-pattern)  
* [Modificare le entità](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Anti-modello dei dati di log
In genere è necessario usare l'archiviazione BLOB invece di archiviazione tabelle per archiviare i dati di log.  

#### <a name="context-and-problem"></a>Contesto e problema
Un caso d'uso comune per i dati di log consiste nel recuperare una selezione di voci di log per un intervallo di data/ora specifico. Si desidera, ad esempio, trovare tutti i messaggi di errore e critici registrati dall'applicazione tra le 15:04 e le 15:06 in una data specifica. Non si vuole usare la data e l'ora del messaggio di log per determinare la partizione in cui si salvano le entità log. Ciò comporta una partizione ad accesso frequente perché in un determinato momento tutte le entità log condividono lo stesso valore di `PartitionKey`. Vedere l'anti-criterio di [anteposizione/aggiunta](#prepend-append-anti-pattern). Ad esempio, lo schema di entità seguente per un messaggio di log determina una partizione ad accesso frequente perché l'applicazione scrive tutti i messaggi di log nella partizione per la data e l'ora correnti:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="Immagine dell'entità messaggio di log":::

In questo esempio, `RowKey` include la data e l'ora del messaggio di log per assicurarsi che i messaggi di log siano ordinati in base a data/ora. `RowKey` include anche un ID messaggio, nel caso in cui più messaggi di log condividano la stessa data e ora.  

Un altro approccio prevede l'uso di un valore di `PartitionKey` che garantisca che l'applicazione scriva i messaggi in un intervallo di partizioni. Ad esempio, se l'origine del messaggio di log consente di distribuire i messaggi in più partizioni, è possibile usare lo schema di entità seguente:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="Immagine dell'entità messaggio di log":::

Tuttavia, il problema di questo schema è che per recuperare tutti i messaggi di log per un intervallo di tempo specifico è necessario cercare ogni partizione nella tabella.

#### <a name="solution"></a>Soluzione
La sezione precedente ha illustrato il problema associato ai tentativi di usare archiviazione tabelle per archiviare le voci di log e ha proposto due progettazioni, entrambe insoddisfacenti. Una soluzione ha portato a una partizione ad accesso frequente con il rischio di avere prestazioni di scrittura dei messaggi di log scarse. L'altra soluzione ha determinato prestazioni delle query scarse a causa del requisito di analizzare ogni partizione della tabella per recuperare i messaggi di log per un intervallo di tempo specifico. L'archiviazione BLOB offre una soluzione migliore per questo tipo di scenario ed è in questo modo che Analisi archiviazione di Azure archivia i dati di log raccolti.  

Questa sezione illustra come Analisi archiviazione archivia i dati di log nell'archiviazione BLOB per esemplificare questo approccio all'archiviazione dei dati sui quali vengono in genere eseguite query per intervallo.  

Analisi archiviazione archivia i messaggi di log in un formato delimitato in più BLOB. Il formato delimitato semplifica l'analisi dei dati nel messaggio di log da parte di un'applicazione client.  

Analisi archiviazione usa una convenzione di denominazione per i BLOB che consente di individuare uno o più BLOB contenenti i messaggi di log per i quali si sta effettuando la ricerca. Ad esempio, un BLOB denominato "queue/2014/07/31/1800/000001.log" contiene messaggi di log correlati al servizio di accodamento per l'ora che inizia alle 18.00 del 31 luglio 2014. "000001" indica che si tratta del primo file di log per il periodo. Analisi archiviazione registra anche i timestamp del primo e dell'ultimo messaggio di log archiviati nel file come parte dei metadati del BLOB. L'API dell'archiviazione BLOB consente di individuare i BLOB in un contenitore in base al prefisso del nome. Per individuare tutti i BLOB contenenti dati di log delle code per l'ora a partire dalle 18:00, è possibile usare il prefisso "queue/2014/07/31/1800".  

Analisi archiviazione memorizza nel buffer internamente i messaggi di log e quindi aggiorna periodicamente il BLOB appropriato o ne crea uno nuovo con il batch di voci di log più recente. Ciò riduce il numero di scritture che deve eseguire nell'archiviazione BLOB.  

Se si sta implementando una soluzione simile nella propria applicazione, considerare come gestire il compromesso tra affidabilità, costi e scalabilità. In altre parole, valutare l'effetto della scrittura di ogni voce di log nell'archiviazione BLOB non appena questa avviene, rispetto al buffering degli aggiornamenti nell'applicazione e alla scrittura nell'archiviazione BLOB in batch.  

#### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come archiviare i dati di log, considerare quanto segue:  

* Se si crea una progettazione di tabella che consente di evitare potenziali partizioni ad accesso frequente, è possibile che non si possa accedere ai dati di log in modo efficiente.  
* Per elaborare i dati di log, spesso un client deve caricare molti record.  
* Nonostante i dati di log siano spesso strutturati, l'archiviazione BLOB potrebbe essere una soluzione migliore.  

### <a name="implementation-considerations"></a>Considerazioni sull'implementazione
Questa sezione illustra alcune considerazioni da tenere presente quando si implementano i modelli descritti nelle sezioni precedenti. Nella maggior parte di questa sezione vengono usati esempi scritti in C# che usano la libreria client di archiviazione (versione 4.3.0 al momento della stesura di questo documento).  

### <a name="retrieve-entities"></a>Recuperare le entità
Come descritto nella sezione [Progettazione per le query](#design-for-querying), la query più efficiente è una query di tipo punto. Tuttavia, in alcuni scenari potrebbe essere necessario recuperare più entità. Questa sezione descrive alcuni approcci comuni al recupero di entità mediante la libreria client di archiviazione.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Eseguire una query di puntamento mediante la libreria client di archiviazione
Il modo più semplice per eseguire una query di puntamento è usare l'operazione di tabella **Retrieve**. Come illustrato nel frammento di codice C# seguente, questa operazione recupera un'entità con un valore per `PartitionKey` "Sales" e per `RowKey` "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Si noti come in questo esempio l'entità recuperata prevista sia di tipo `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Recuperare più entità usando LINQ
È possibile recuperare più entità usando LINQ con la libreria client di archiviazione e specificando una query con la clausola **where**. Per evitare una scansione di tabella, è consigliabile includere sempre il valore `PartitionKey` nella clausola where e, se possibile, il valore `RowKey` per evitare analisi di tabelle e partizioni. Archiviazione tabelle supporta un set limitato di operatori di confronto, maggiore di, maggiore di o uguale a, minore di, minore di o uguale a e non uguale a, da usare per determinare la clausola where. Il frammento di codice C# seguente consente di trovare tutti i dipendenti il cui cognome inizia con la lettera "B", presupponendo che il valore `RowKey` archivi il cognome, del reparto vendite, supponendo che il valore `PartitionKey` archivi il nome del reparto:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Si noti come la query specifichi sia un valore di `RowKey` che un valore di `PartitionKey` per garantire prestazioni migliori.  

L'esempio di codice seguente illustra la funzionalità equivalente usando l'API Fluent (per altre informazioni sulle API Fluent in generale, vedere l'articolo relativo alle [procedure consigliate per la progettazione di un'API Fluent](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> L'esempio annida più metodi `CombineFilters` per includere le tre condizioni di filtro.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Recuperare un numero elevato di entità da una query
Una query ottimale restituisce una singola entità in base a un valore di `PartitionKey` e un valore di `RowKey`. In alcuni scenari, tuttavia, potrebbe essere presente il requisito di restituire molte entità della stessa partizione o anche di più partizioni. È sempre necessario eseguire test completi delle prestazioni dell'applicazione in tali scenari.  

Una query su archiviazione tabelle può restituire un massimo di 1.000 entità contemporaneamente e può essere eseguita per un massimo di cinque secondi. Archiviazione tabelle restituisce un token di continuazione per consentire all'applicazione client di richiedere il set successivo di entità, se si verifica una delle condizioni seguenti:

- Il set di risultati contiene più di 1.000 entità.
- La query non è stata completata entro cinque secondi.
- La query supera il limite della partizione. 

Per altre informazioni sul funzionamento dei token di continuazione, vedere [Timeout e paginazione delle query](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

La libreria client di archiviazione può gestire automaticamente i token di continuazione per l'utente in quanto restituisce entità di archiviazione tabelle. L'esempio di codice C# seguente gestisce automaticamente i token di continuazione se archiviazione tabelle li restituisce in una risposta:  

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

Usando i token di continuazione in modo esplicito è possibile controllare quando l'applicazione recupera il successivo segmento di dati. Se, ad esempio, l'applicazione client consente agli utenti di sfogliare le entità archiviate in una tabella, un utente potrebbe decidere di non sfogliare tutte le entità recuperate dalla query. L'applicazione userà solo un token di continuazione per recuperare il segmento successivo quando l'utente ha terminato il paging di tutte le entità nel segmento corrente. Questo approccio offre diversi vantaggi:  

* È possibile limitare la quantità di dati da recuperare da archiviazione tabelle e da spostare nella rete.  
* È possibile eseguire operazioni di I/O asincrone in .NET.  
* È possibile serializzare il token di continuazione in un archivio permanente in modo da poter proseguire in caso di arresto anomalo dell'applicazione.  

> [!NOTE]
> Un token di continuazione in genere restituisce un segmento contenente al massimo 1.000 entità, sebbene possa contenerne un numero minore. Questa situazione si verifica anche se si limita il numero di voci restituite da una query usando **Take** per restituire le prime n entità che corrispondono ai criteri di ricerca. Archiviazione tabelle potrebbe restituire un segmento contenente un numero inferiore di n entità, insieme a un token di continuazione per consentire all'utente di recuperare le entità rimanenti.  
> 
> 

Il codice C# seguente illustra come modificare il numero di entità restituite all'interno di un segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Proiezione lato server
Una singola entità può avere fino a 255 proprietà e dimensioni fino a 1 MB. Quando si eseguono query sulla tabella e si recuperano entità, potrebbero non essere necessarie tutte le proprietà ed è possibile evitare di trasferire dati inutilmente, in modo da ridurre la latenza e i costi. È possibile usare la proiezione lato server per trasferire solo le proprietà necessarie. L'esempio seguente recupera solo la proprietà `Email`, insieme a `PartitionKey`, `RowKey`, `Timestamp` e `ETag`, dalle entità selezionate dalla query.  

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

Si noti che il valore `RowKey` è disponibile anche se non è incluso nell'elenco delle proprietà da recuperare.  

### <a name="modify-entities"></a>Modificare entità
La libreria client di archiviazione consente di modificare le entità archiviate in archiviazione tabelle mediante l'inserimento, l'eliminazione e l'aggiornamento di entità. È possibile usare le transazioni di gruppi di entità per raggruppare in batch più operazioni di inserimento, aggiornamento ed eliminazione allo scopo di ridurre il numero di round trip necessari e migliorare le prestazioni della soluzione.  

Le eccezioni generate quando la libreria client di archiviazione esegue una transazione di gruppi di entità, in genere, includono l'indice dell'entità che ha causato l'esito negativo del batch. Ciò è utile quando si esegue il debug di codice che usa le transazioni EGT.  

È inoltre opportuno considerare l'influenza della progettazione sul modo in cui l'applicazione gestisce le operazioni di concorrenza e aggiornamento.  

#### <a name="managing-concurrency"></a>Gestione della concorrenza
Per impostazione predefinita, archiviazione tabelle implementa i controlli di concorrenza ottimistica a livello di singole entità per le operazioni di inserimento, unione ed eliminazione, sebbene sia possibile per un client forzare archiviazione tabelle in modo da ignorare questi controlli. Per altre informazioni, vedere [Gestione della concorrenza in Archiviazione di Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Unione o sostituzione
Il metodo `Replace` della classe `TableOperation` sostituisce sempre l'entità completa in archiviazione tabelle. Se non si include una proprietà nella richiesta quando tale proprietà è presente nell'entità archiviata, la richiesta rimuove la proprietà dall'entità archiviata. A meno che non si voglia rimuovere una proprietà in modo esplicito da un'entità archiviata, è necessario includere ogni proprietà nella richiesta.  

È possibile usare il metodo `Merge` della classe `TableOperation` per ridurre la quantità di dati inviati ad archiviazione tabelle quando si vuole aggiornare un'entità. Il metodo `Merge` sostituisce tutte le proprietà nell'entità archiviata con i valori della proprietà dell'entità inclusa nella richiesta. Questo metodo lascia inalterate tutte le proprietà nell'entità archiviata che non sono incluse nella richiesta. Ciò è utile se si dispone di entità di grandi dimensioni e si desidera solo aggiornare un numero limitato di proprietà in una richiesta.  

> [!NOTE]
> I metodi `*Replace` e `Merge` non riescono se l'entità non esiste. In alternativa, se l'entità non esiste, è possibile usare i metodi `InsertOrReplace` e `InsertOrMerge` per creare una nuova entità.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Usare tipi di entità eterogenei
Archiviazione tabelle è un archivio di tabelle *senza schema*. Ciò significa che una singola tabella può archiviare entità di più tipi, offrendo una grande flessibilità di progettazione. L'esempio seguente illustra una tabella che archivia entità dipendente ed entità reparto:  

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

Ogni entità deve avere ancora valori `PartitionKey`, `RowKey` e `Timestamp`, ma può avere un qualsiasi set di proprietà. Per di più, non esiste alcuna indicazione relativa al tipo di un'entità, a meno che non si scelga di archiviare le informazioni in una posizione. Esistono due opzioni per identificare il tipo di entità:  

* Anteporre il tipo di entità per il valore `RowKey` o eventualmente il valore `PartitionKey`. Ad esempio, `EMPLOYEE_000123` o `DEPARTMENT_SALES` come valori di `RowKey`.  
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

La prima opzione, ovvero l'anteposizione del tipo di entità per `RowKey`, è utile se sussiste la possibilità che due entità di tipi diversi abbiano lo stesso valore di chiave. Inoltre, raggruppa entità dello stesso tipo insieme nella partizione.  

Le tecniche descritte in questa sezione sono particolarmente rilevanti per la discussione sulle [relazioni di ereditarietà](#inheritance-relationships).  

> [!NOTE]
> Si consideri di includere un numero di versione nel valore del tipo di entità per consentire alle applicazioni client di sviluppare oggetti POCO e usare versioni diverse.  
> 
> 

La restante parte di questa sezione descrive alcune delle funzionalità della libreria client di archiviazione che semplificano l'uso di più tipi di entità nella stessa tabella.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Recupero di tipi di entità eterogenei
Se si usa la libreria client di archiviazione, l'uso di più tipi di entità può avvenire in tre modalità.  

Se si conosce il tipo di entità archiviata con valori di `RowKey` e `PartitionKey` specifici, è possibile specificare il tipo di entità quando si recupera l'entità. Questa modalità è stata illustrata nei due esempi precedenti che recuperano le entità di tipo `EmployeeEntity`: [Eseguire una query di puntamento mediante la libreria client di archiviazione](#run-a-point-query-by-using-the-storage-client-library) e [Recuperare più entità usando LINQ](#retrieve-multiple-entities-by-using-linq).  

La seconda modalità consiste nell'usare il tipo di `DynamicTableEntity`, ovvero un elenco proprietà, anziché un tipo di entità POCO concreto. Questa opzione può persino migliorare le prestazioni, perché non è necessario serializzare e deserializzare l'entità in tipi .NET. Il codice C# seguente può recuperare più entità di tipo diverso dalla tabella, ma restituisce tutte le entità come istanze `DynamicTableEntity`. Usa quindi la proprietà `EntityType` per determinare il tipo di ogni entità:  

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

Per recuperare altre proprietà, è necessario usare il metodo `TryGetValue` sulla proprietà `Properties` della classe `DynamicTableEntity`.  

Una terza modalità consiste nel combinare l'uso del tipo `DynamicTableEntity` e di un'istanza `EntityResolver`. Ciò consente di risolvere a più tipi POCO nella stessa query. In questo esempio il delegato `EntityResolver` usa la proprietà `EntityType` per distinguere i due tipi di entità restituiti dalla query. Il metodo `Resolve` usa il delegato `resolver` per risolvere le istanze `DynamicTableEntity` in istanze `TableEntity`.  

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
Per eliminare un'entità non è necessario conoscerne il tipo, che è comunque sempre noto quando l'entità viene inserita. Tuttavia, è possibile usare il tipo `DynamicTableEntity` per aggiornare un'entità senza conoscerne il tipo e senza usare una classe di entità POCO. L'esempio di codice seguente consente di recuperare una singola entità e controlla che la proprietà `EmployeeCount` esista prima di aggiornarla.  

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

### <a name="control-access-with-shared-access-signatures"></a>Controllo dell'accesso con le firme di accesso condiviso
È possibile usare i token della firma di accesso condiviso (SAS) per consentire alle applicazioni client di modificare ed eseguire query sulle entità di tabella direttamente, senza la necessità di eseguire l'autenticazione direttamente con archiviazione tabelle. In genere, l'uso di SAS nell'applicazione comporta tre vantaggi principali:  

* Non è necessario distribuire la chiave dell'account di archiviazione in una piattaforma non sicura, ad esempio un dispositivo mobile, per consentire a tale dispositivo di accedere e modificare le entità in archiviazione tabelle.  
* È possibile ripartire le operazioni eseguite dai ruoli Web e di lavoro durante la gestione delle entità. È possibile ripartire il carico di lavoro ai dispositivi client, ad esempio computer degli utenti finali e dispositivi mobili.  
* È possibile assegnare un set vincolato e limitato nel tempo di autorizzazioni a un client, ad esempio, l'accesso di sola lettura a risorse specifiche.  

Per altre informazioni sull'uso dei token SAS con archiviazione tabelle, vedere [Uso delle firme di accesso condiviso (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Tuttavia, è comunque necessario generare i token SAS che concedono un'applicazione client alle entità in archiviazione tabelle. Eseguire questa operazione in un ambiente con accesso protetto alle chiavi dell'account di archiviazione. In genere, è possibile usare un ruolo Web o di lavoro per generare i token delle firme di accesso condiviso e distribuirli alle applicazioni client che richiedono l'accesso alle entità. Poiché la generazione e la distribuzione dei token delle firme di accesso condiviso ai client comportano comunque un sovraccarico, è consigliabile valutare il modo migliore di ridurre tale sovraccarico, soprattutto in scenari con volumi elevati.  

È possibile generare un token SAS che concede l'accesso a un subset di entità in una tabella. Per impostazione predefinita, si crea un token SAS per un'intera tabella. Tuttavia, è anche possibile specificare che il token SAS conceda l'accesso a un intervallo di valori `PartitionKey` o a un intervallo di valori `PartitionKey` e `RowKey`. Si potrebbe scegliere di generare token SAS per i singoli utenti del sistema in modo che il token SAS di ogni utente consenta di accedere solo alle proprie entità in archiviazione tabelle.  

### <a name="asynchronous-and-parallel-operations"></a>Operazioni asincrone e parallele
A condizione che le richieste vengano distribuite in più partizioni, è possibile migliorare la velocità effettiva e la velocità di risposta del client usando le query parallele o asincrone.
Ad esempio, si potrebbero avere due o più istanze del ruolo di lavoro che accedono alle tabelle in parallelo. Si potrebbero avere singoli ruoli di lavoro responsabili di specifici set di partizioni o semplicemente avere più istanze del ruolo di lavoro, ciascuna in grado di accedere a tutte le partizioni di una tabella.  

All'interno di un'istanza del client, è possibile migliorare la velocità effettiva eseguendo operazioni di archiviazione in modo asincrono. La libreria client di archiviazione semplifica la scrittura di query e modifiche asincrone. Ad esempio, è possibile iniziare con il metodo sincrono che recupera tutte le entità in una partizione, come mostrato nel codice C# seguente:  

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

È possibile modificare facilmente questo codice affinché la query venga eseguita in modo asincrono come segue:  

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

* La firma del metodo include ora il modificatore `async` e restituisce un'istanza `Task`.  
* Anziché chiamare il metodo `ExecuteSegmented` per recuperare i risultati, il metodo chiama ora il metodo `ExecuteSegmentedAsync`. Il metodo usa il modificatore `await` per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare questo metodo più volte, con valori diversi per il parametro `department`. Ogni query verrà eseguita su un thread separato.  

Non esiste una versione asincrona del metodo `Execute` nella classe `TableQuery` perché l'interfaccia `IEnumerable` non supporta l'enumerazione asincrona.  

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

È possibile modificare facilmente questo codice affinché l'aggiornamento venga eseguito in modo asincrono, come segue:  

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

* La firma del metodo include ora il modificatore `async` e restituisce un'istanza `Task`.  
* Anziché chiamare il metodo `Execute` per aggiornare l'entità, il metodo chiama ora il metodo `ExecuteAsync`. Il metodo usa il modificatore `await` per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare più metodi asincroni come questo e ogni chiamata al metodo viene eseguita su un thread separato.  

