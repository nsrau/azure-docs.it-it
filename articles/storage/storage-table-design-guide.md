<properties
   pageTitle="Guida alla progettazione di tabelle di archiviazione di Azure | Microsoft Azure"
   description="Progettare tabelle scalabili ed efficienti in Archiviazione tabelle di Azure"
   services="storage"
   documentationCenter="na"
   authors="jasonnewyork" 
   manager="tadb"
   editor="tysonn"/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="03/18/2016"
   ms.author="jahogg"/>

# Guida alla progettazione della tabella di archiviazione di Azure: Progettazione scalabile e Tabelle ad alte prestazioni

## Panoramica

Per progettare tabelle scalabili ed efficienti, è necessario tenere in considerazione diversi fattori, come le prestazioni, la scalabilità e il costo. Se in precedenza si sono progettati schemi per i database relazionali, queste considerazioni saranno già state fatte, ma, pur essendoci alcune somiglianze tra il modello di archiviazione del servizio tabelle di Azure e i modelli relazionali, esistono anche molte importanti differenze. Queste differenze in genere danno origine a progettazioni molto diverse che potrebbero sembrare poco plausibili o sbagliate a chi ha familiarità con i database relazionali, ma che invece hanno perfettamente senso se la progettazione è finalizzata a un archivio di chiavi/valori NoSQL, come il servizio tabelle di Azure. Molte differenze di progettazione rispecchieranno il fatto che il servizio tabelle è progettato per supportare applicazioni con scalabilità cloud che possono contenere miliardi di entità (dette righe nella terminologia dei database relazionali) di dati o per set di dati che devono supportare volumi di transazioni molto elevati: quindi è necessario pensare in modo diverso all'archiviazione dei dati e conoscere il funzionamento del servizio tabelle. Un archivio dati NoSQL ben progettato offre alla soluzione una scalabilità decisamente più elevata (e a un costo inferiore) rispetto a una soluzione che usa un database relazionale. Questa guida illustra proprio questi argomenti.

## Informazioni sul servizio tabelle di Azure

Questa sezione evidenzia alcune funzionalità chiave del servizio tabelle, di particolare importanza per la progettazione a livello di prestazioni e scalabilità. Se non si ha familiarità con Archiviazione di Azure e con il servizio tabelle, prima di proseguire con la lettura di questo articolo, vedere [Introduzione ad Archiviazione di Microsoft Azure](storage-introduction.md) e [Introduzione all'archivio tabelle di Azure con .NET](storage-dotnet-how-to-use-tables.md). Anche se l'argomento principale di questa guida è il servizio tabelle, sono incluse alcune informazioni sui servizi di accodamento e BLOB di Azure e su come sia possibile usarli con il servizio tabelle in una soluzione.

Cos'è il servizio tabelle? Come indica il nome stesso, il servizio tabelle usa un formato tabulare per archiviare i dati. In base alla terminologia standard, ogni riga della tabella rappresenta un'entità le cui diverse proprietà sono archiviate nelle colonne. Ogni entità ha una coppia di chiavi che la identificano in modo univoco e una colonna di tipo timestamp usata dal servizio tabelle per tenere traccia dell'ultimo aggiornamento dell'entità. Questa operazione è automatica e non è possibile sovrascrivere manualmente il timestamp con un valore arbitrario. Il servizio tabelle usa il timestamp dell’ultima modifica (LMT, Last Modified Timestamp) per gestire la concorrenza ottimistica.

>[AZURE.NOTE] Le operazioni API REST del servizio tabelle restituiscono anche un valore **ETag** derivato dal timestamp LMT. In questo documento i termini ETag ed LMT verranno usati in modo intercambiabile perché si riferiscono agli stessi dati sottostanti.

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
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Department</td>
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


Per il momento, sembra molto simile a una tabella di un database relazionale. Le principali differenze sono le colonne obbligatorie e la possibilità di archiviare più tipi di entità nella stessa tabella. Inoltre ogni proprietà definita dall'utente, come **FirstName** o **Age** ha un tipo di dati, ad esempio integer o stringa, proprio come una colonna in un database relazionale. Anche se diversamente da un database relazionale, essendo il servizio tabelle privo di schema, una proprietà non deve avere lo stesso tipo di dati in ogni entità. Per archiviare tipi di dati complessi in una sola proprietà, è necessario usare un formato serializzato come JSON o XML. Per altre informazioni sul servizio tabelle, ad esempio sui tipi di dati supportati, sugli intervalli di date supportate, sulle regole di denominazione e sui limiti di dimensioni, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Come si vedrà, la scelta di**PartitionKey** e **RowKey** è fondamentale per la progettazione ottimale di una tabella. Ogni entità archiviata in una tabella deve avere una combinazione univoca di **PartitionKey** e **RowKey**. Come le chiavi in una tabella di database relazionale, i valori di **PartitionKey** e **RowKey** vengono indicizzati per creare un indice cluster che consenta di eseguire ricerche rapide. Il servizio tabelle non crea però indici secondari e dunque queste sono le due sole proprietà indicizzate. Alcuni dei modelli descritti più avanti mostrano come poter ovviare a questa apparente limitazione.

Una tabella è costituita da una o più partizioni e, come si vedrà, molte delle decisioni relative alla progettazione riguarderanno la scelta di un valore appropriato per **PartitionKey** e **RowKey** per poter ottimizzare la soluzione. Una soluzione può essere costituita da una sola tabella contenente tutte le entità organizzate in partizioni, ma normalmente una soluzione comprende più tabelle. Le tabelle permettono di organizzare in modo logico le entità e di gestire l'accesso ai dati con gli elenchi di controllo di accesso. Inoltre è possibile eliminare un'intera tabella con una sola operazione di archiviazione.

### Partizioni della tabella  
Il nome account, il nome tabella e **PartitionKey** insieme identificano la partizione nel servizio di archiviazione, in cui il servizio tabelle archivia l'entità. Oltre a far parte dello schema di indirizzamento per le entità, le partizioni definiscono un ambito per le transazioni (vedere più avanti [Transazioni di gruppi di entità](#entity-group-transactions)) Per altre informazioni sulle partizioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Nel servizio tabelle, un solo nodo gestisce una o più partizioni complete e il servizio scala bilanciando dinamicamente il carico delle partizioni tra i nodi. Se un nodo è in condizioni di carico, il servizio tabelle può *dividere* in più nodi l'intervallo di partizioni gestite da quel nodo. Quando il traffico diminuisce, il servizio può *unire* nuovamente in un solo nodo gli intervalli di partizioni dai nodi inattivi.

Per altre informazioni sui dettagli interni del servizio tabelle, in particolare sulla gestione delle partizioni con il servizio tabelle, vedere il documento relativo all’[Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

### Transazioni dei gruppi di entità
Nel servizio tabelle, le transazioni di gruppi di entità (EGT, Entity Group Transaction) sono il solo meccanismo predefinito per eseguire aggiornamenti atomici tra più entità. In alcuni documenti, le transazioni EGT sono chiamate anche *transazioni batch* Le transazioni EGT possono agire solo su entità archiviate nella stessa partizione (ovvero che condividono la stessa chiave di partizione in una determinata tabella), quindi, ogni volta che è necessario un comportamento transazionale atomico tra più entità, bisogna assicurarsi che tali entità siano nella stessa partizione. Per questo motivo spesso si tengono tipi diversi di entità nella stessa tabella (e partizione) e non si usa una tabella per ogni tipo di entità. Una sola EGT può agire al massimo su 100 entità. Se si inviano più EGTs simultanee per l'elaborazione è importante garantire che tali EGTs non vengono applicate a entità che sono comuni tra EGTs altrimenti l’elaborazione potrebbe subire dei ritardi.

Le transazioni EGT introducono anche un potenziale compromesso da tenere in considerazione durante la progettazione: se si usano più partizioni, la scalabilità dell'applicazione aumenta perché Azure ha più opportunità di bilanciare il carico delle richieste tra i nodi, ma questo potrebbe limitare la possibilità dell'applicazione di eseguire transazioni atomiche e di mantenere la coerenza assoluta per i dati. Esistono poi specifici obiettivi di scalabilità a livello di partizione, che potrebbero limitare la velocità effettiva delle transazioni prevista per un singolo nodo: per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione di Azure e il servizio tabelle, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md). Le sezioni successive di questa guida illustrano diverse strategie di progettazione che aiutano a gestire compromessi come questo e illustrano il modo migliore per scegliere la chiave di partizione in base ai requisiti specifici dell'applicazione client.

### Considerazioni sulla capacità
La tabella seguente include alcuni valori chiave da tenere presenti quando si progetta una soluzione di servizio tabelle:

|Capacità totale di un account di archiviazione di Azure|500 TB|
|------------------------------------------|------|
|Numero di tabelle in un account di archiviazione di Azure | Limitato solo dalla capacità dell'account di archiviazione |
|Numero di partizioni in una tabella | Limitato solo dalla capacità dell'account di archiviazione |
|Numero di entità in una partizione | Limitato solo dalla capacità dell'account di archiviazione|
|Dimensioni di una singola entità | Fino a 1 MB con un massimo di 255 proprietà (incluse **PartitionKey**, **RowKey**, e **Timestamp**) |
|Dimensioni di **PartitionKey** | Stringa con dimensioni fino a 1 KB. |
| Dimensioni di **RowKey** | Stringa con dimensioni fino a 1 KB. |
|Dimensioni di una transazione di gruppi di entità | Una transazione può includere al massimo 100 entità e le dimensioni del payload devono essere inferiori a 4 MB. Una transazione EGT può aggiornare una sola entità per volta. |

Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/library/azure/dd179338.aspx).

### Considerazioni sul costo  
Anche se l'archiviazione tabelle è relativamente poco costosa, è consigliabile includere le stime dei costi, sia per l'utilizzo della capacità che per la quantità di transazioni, nella valutazione delle soluzioni che usano il servizio tabelle. Tuttavia in molti scenari, l'archiviazione dei dati denormalizzati o duplicati per migliorare le prestazioni o la scalabilità della soluzione costituisce un valido approccio. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## Linee guida per la progettazione di tabelle  
Questi elenchi riepilogano alcune linee guida chiave che è necessario tenere presenti durante la progettazione delle, la guida li descriverà più nel dettaglio in seguito. Queste linee guida sono molto diverse dalle linee guida a cui in genere ci si attiene per la progettazione di database relazionali.

Progettazione di una soluzione di servizio tabelle efficiente nelle operazioni di *lettura*:

-	***Progettazione per le query nelle applicazioni con intensa attività di lettura.*** Quando si progettano le tabelle, considerare le query (soprattutto quelle sensibili alla latenza) che si eseguiranno prima di pensare a come si aggiorneranno le entità. Ciò comporta in genere una soluzione efficiente e ad alte prestazioni.  
-	***Specificare PartitionKey e RowKey nelle query.*** *Scegliere query* come queste sono le query più efficienti del servizio tabella.  
-	***Prendere in considerazione l'archiviazione di copie duplicate delle entità.*** Poiché l'archiviazione tabelle è economica, considerare la possibilità di archiviare la stessa entità più volte (con chiavi diverse) per consentire query più efficienti.  
-	***Considerare la denormalizzazione dei dati.*** L’archiviazione delle tabelle è economica, dunque è opportuno considerare la denormalizzazione dei dati. Ad esempio, archiviare le entità di riepilogo in modo che le query per aggregare i dati debbano accedere a una singola entità.  
-	***Usare valori chiave composti.*** Le sole chiavi a disposizione sono **PartitionKey** e **RowKey**. Ad esempio, per abilitare percorsi alternativi per l'accesso con chiave alle entità, ad esempio, utilizzare valori chiave composti.  
-	***Usare la proiezione di query.*** È possibile ridurre la quantità di dati trasferiti tramite la rete usando query che selezionano solo i campi necessari.  

Progettazione di una soluzione di servizio tabelle efficiente nelle operazioni di *scrittura*:

-	***Non creare partizioni critiche*** Scegliere chiavi che consentono di distribuire le richieste tra più partizioni in qualsiasi momento.  
-	***Evitare picchi di traffico.*** Contenere il traffico in un intervallo di tempo ragionevole ed evitare i picchi di traffico.
-	***Non necessariamente creare una tabella separata per ogni tipo di entità.*** Quando è necessario eseguire transazioni atomiche tra diversi tipi di entità, è possibile archiviare questi tipi di entità nella stessa partizione della stessa tabella.
-	***Considerare la velocità effettiva massima che è necessario raggiungere.*** È necessario tenere presenti gli obiettivi di scalabilità per il servizio tabelle e assicurarsi di non superarli con la progettazione.  

Questa guida contiene esempi in cui vengono messi in pratica tutti questi principi.

## Progettazione per le query  
Le soluzioni di servizio tabelle possono eseguire un'intensa attività di lettura, di scrittura o una combinazione di entrambe. Questa sezione è incentrata sugli aspetti da prendere in considerazione quando si progetta un servizio tabelle in grado di supportare in modo efficiente le operazioni di lettura. Una progettazione che supporta in modo efficiente le operazioni di lettura è in genere efficiente anche nelle operazioni di scrittura. Esistono però altri aspetti da considerare per una progettazione che supporti le operazioni di scrittura, come illustrato nella prossima sezione, [Progettazione per la modifica dei dati](#design-for-data-modification),

Quando si inizia a progettare una soluzione di servizio tabelle che consenta di leggere i dati in modo efficiente, è importante chiedersi quali query dovrà eseguire l'applicazione per recuperare i dati necessari dal servizio tabelle.

>[AZURE.NOTE] Con il servizio tabelle, è fondamentale realizzare una progettazione corretta fin dall'inizio perché cambiarla in seguito sarebbe difficile e costoso. Ad esempio, in un database relazionale spesso è possibile risolvere i problemi di prestazioni semplicemente aggiungendo degli indici a un database esistente, ma questa opzione non è applicabile al servizio tabelle.

Questa sezione è incentrata sui problemi chiave che è necessario affrontare quando si progettano le tabelle per le query. Gli argomenti trattati in questa sezione includono:

- [Come la scelta di PartitionKey e RowKey compromette le prestazioni delle query](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
- [Scelta di un valore PartitionKey appropriato](#choosing-an-appropriate-partitionkey)
- [Ottimizzazione delle query per il servizio tabelle](#optimizing-queries-for-the-table-service)
- [Ordinamento dei dati nel servizio tabelle](#sorting-data-in-the-table-service)

### Come la scelta di PartitionKey e RowKey compromette le prestazioni delle query  

I seguenti esempi presuppongono che nel servizio tabelle vengano archiviate entità dipendente con la struttura seguente (per maggiore chiarezza, nella maggior parte degli esempi viene omessa la proprietà **Timestamp**):

|*Nome colonna* |*Tipo di dati*|
|--------------|-----------|
|**PartitionKey** (nome del reparto)|Stringa|
|**RowKey** (ID dipendente)|Stringa|
|**FirstName**|String|
|**LastName**|Stringa|
|**Age**|Integer|
|**EmailAddress**|Stringa|

La sezione precedente [Panoramica del servizio tabelle di Azure](#overview) descrive alcune funzionalità chiave del servizio tabelle di Azure, che influiscono direttamente sulla progettazione della query. Se ne possono ricavare le seguenti linee guida generali per la progettazione di query del servizio tabelle. Si noti che la sintassi del filtro usata negli esempi seguenti proviene dall'API REST del servizio tabelle. Per altre informazioni, vedere [Query Entities](http://msdn.microsoft.com/library/azure/dd179421.aspx).

-	Una ***Query di tipo punto*** è il tipo di ricerca più efficiente da usare ed è consigliata per le ricerche con volumi elevati o per le ricerche che richiedono una latenza molto bassa. Una query di questo tipo può usare gli indici per trovare in modo molto efficiente una singola entità specificando entrambi i valori **PartitionKey** e **RowKey**. Ad esempio,
$filter=(PartitionKey eq 'Sales') e (RowKey eq '2')  
-	La seconda miglior ricerca è la ***query di intervallo*** che usa **PartitionKey** e applica il filtro a un intervallo di valori **RowKey** per restituire più di un'entità. Il valore **PartitionKey** identifica una partizione specifica e i valori **RowKey** identificano un subset delle entità in quella partizione. Ad esempio,
$filter=PartitionKey eq 'Sales' e RowKey ge 'S' e RowKey lt 'T'  
-	La terza miglior ricerca è l'***analisi della partizione*** che usa **PartitionKey** e applica il filtro a un'altra proprietà non chiave e che potrebbe restituire più di un'entità. Il valore **PartitionKey** identifica una partizione specifica e i valori della proprietà selezionano un subset delle entità in quella partizione. Ad esempio:
$filter=PartitionKey eq 'Sales' e LastName eq 'Smith'  
-	Una ***scansione di tabella*** non include **PartitionKey** ed è molto inefficiente perché cerca le entità corrispondenti in tutte le partizioni della tabella, una alla volta. Una scansione di tabella viene eseguita indipendentemente dal fatto che il filtro usi **RowKey** o meno. Ad esempio:
$filter = LastName eq 'Jones'  
-	Le query che restituiscono più entità le ordinano in base a **PartitionKey** e **RowKey**. Per non dover riordinare le entità nel client, scegliere un valore **RowKey** che definisca l'ordinamento più comune.  

Si noti che, se si usa "**or**" per specificare un filtro basato su valori **RowKey**, si ottiene un'analisi della partizione che non viene considerata come query di intervallo. Pertanto, è consigliabile evitare query che utilizzano filtri ad esempio:
$filter = PartitionKey eq "Sales" e (RowKey '121' o RowKey eq '322')

Per esempi di codice lato client che usano la libreria client di archiviazione per eseguire query efficienti, vedere:

-	[Esecuzione di una query di tipo punto mediante la libreria client di archiviazione](#executing-a-point-query-using-the-storage-client-library)
-	[Recupero di più entità usando LINQ](#retrieving-multiple-entities-using-linq)
-	[Proiezione lato server](#server-side-projection)  

Per esempi di codice lato client che possono gestire più tipi di entità archiviati nella stessa tabella, vedere:

-	[Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  

### Scelta di un valore PartitionKey appropriato  

La scelta di **PartitionKey** deve soddisfare sia la necessità di abilitare l'uso di transazioni EGT (per assicurare la coerenza) sia il requisito di distribuzione delle entità tra più partizioni (per assicurare una soluzione scalabile).

Da una parte, pur essendo possibile archiviare tutte le entità in una singola partizione, questo potrebbe limitare la scalabilità della soluzione e impedirebbe al servizio tabelle di bilanciare il carico delle richieste. D'altra parte, pur essendo possibile archiviare un'entità per partizione, ottenendo così la scalabilità elevata e consentendo al servizio tabelle di bilanciare il carico delle richieste, questo impedirebbe di usare le transazioni del gruppo di entità.

Il valore **PartitionKey** ideale consente di usare query efficienti e ha un numero sufficiente di partizioni per garantire la scalabilità della soluzione. Di solito le entità dispongono una proprietà apposita che le distribuisce in un numero sufficiente di partizioni.

>[AZURE.NOTE] Ad esempio, in un sistema che archivia le informazioni sugli utenti o i dipendenti, l'ID utente può essere un valore PartitionKey valido. È possibile avere più entità che utilizzano un ID utente specificato come chiave di partizione. Ogni entità che archivia i dati su un utente è raggruppata in una singola partizione e quindi queste entità sono accessibili tramite gruppi di entità, mantenendo la scalabilità elevata.

Gli altri aspetti da considerare per la scelta di **PartitionKey** riguardano l'inserimento, l'aggiornamento e l'eliminazione delle entità: vedere la sezione [Progettazione per la modifica dei dati](#design-for-data-modification) qui di seguito.

### Ottimizzazione delle query per il servizio tabelle  

Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey** in un singolo indice cluster. È per questo che le query di tipo punto sono le più efficienti da usare. Tuttavia, non esistono altri indici oltre a quello nell'indice cluster in **PartitionKey** e **RowKey**.

Molte progettazioni devono soddisfare alcuni requisiti per abilitare la ricerca di entità in base a più criteri, ad esempio trovare le entità dipendente in base a indirizzo di posta elettronica, ID dipendente o cognome. I modelli seguenti nella sezione [Modelli di progettazione tabelle](#table-design-patterns) soddisfano questi tipi di requisito e descrivono come ovviare al fatto che il servizio tabelle non fornisca indici secondari:

-	[Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori **RowKey** diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
-	[Modello per indice secondario intrapartizione](#inter-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
-	[Modello per entità di indice](#index-entities-pattern) - Mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

### Ordinamento dei dati nel servizio tabelle  

Il servizio tabelle restituisce le entità in ordine crescente in base a **PartitionKey** e quindi a **RowKey**. Queste chiavi sono valori stringa e, per essere certi che i valori numerici siano ordinati correttamente, è consigliabile convertirli in una lunghezza fissa aggiungendo degli zeri se necessario. Se, ad esempio, il valore dell'ID dipendente usato come **RowKey** è un valore integer, è consigliabile convertire l'ID dipendente **123** in **00000123**.

In molte applicazioni è necessario usare i dati ordinandoli in modo diverso, ad esempio ordinando i dipendenti per nome o per data di assunzione. I modelli seguenti nella sezione [Modelli di progettazione tabella](#table-design-patterns) descrivono come alternare l'ordinamento per le entità:

-	[Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori.  
-	[Modello per indice secondario intrapartizione](#inter-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori RowKey.
-	[Modello della parte finale del log](#log-tail-pattern) - Recupera le entità *n* aggiunte più di recente a una partizione in base a un valore **RowKey** che usa un ordinamento inverso di data e ora.  

## Progettazione per la modifica dei dati
Questa sezione esamina le considerazioni relative alla progettazione per ottimizzare inserimenti, aggiornamenti ed eliminazioni. In alcuni casi, sarà necessario valutare il compromesso tra progettazioni che ottimizzano le query e progettazioni che ottimizzano la modifica dei dati, come avviene per le progettazioni per i database relazionali (anche se le tecniche per gestire i compromessi tra progettazioni sono diverse in un database relazionale). La sezione [Modelli di progettazione tabelle](#table-design-patterns) descrive in dettaglio alcuni modelli di progettazione per il servizio tabelle ed evidenzia alcuni di questi compromessi. In pratica si vedrà che molte progettazioni ottimizzate per le query delle entità vanno bene anche per la modifica delle entità.

### Ottimizzazione delle prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione  

Per aggiornare o eliminare un'entità, è necessario poterla identificare usando i valori **PartitionKey** e **RowKey**. In questo senso la scelta di **PartitionKey** e **RowKey** per modificare le entità deve seguire criteri simili a quelli usati per la scelta di supportare le query di tipo punto, perché l'obiettivo è identificare le entità nel modo più efficiente possibile. Si vuole evitare di usare una scansione di tabella o di partizione inefficiente per trovare un'entità e poter individuare i valori**PartitionKey** e **RowKey** necessari per aggiornarla o eliminarla.

I modelli seguenti nella sezione [Modelli di progettazione tabelle](#table-design-patterns) consentono di ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione:

-	[Modello di eliminazione volume elevato](#high-volume-delete-pattern) - Abilita l'eliminazione di un volume elevato di entità mediante l'archiviazione di tutte le entità per l'eliminazione simultanea nella relativa tabella separata. Per eliminare le entità, eliminare la tabella.  
-	[Modello di serie di dati](#data-series-pattern) - Archivia serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  
-	[Modello di entità di grandi dimensioni](#wide-entities-pattern) - Usa più entità fisiche per archiviare entità logiche con più di 252 proprietà.  
-	[Modello di entità di grandi dimensioni](#large-entities-pattern) - Usa l'archiviazione BLOB per archiviare i valori di proprietà di grandi dimensioni.  

### Verifica della coerenza nelle entità archiviate  

L'altro aspetto importante che influisce sulla scelta delle chiavi per ottimizzare la modifica dei dati è come assicurare la coerenza usando le transazioni atomiche. È possibile usare una transazione EGT solo per agire sulle entità archiviate nella stessa partizione.

I modelli seguenti nella sezione [Modelli di progettazione tabella](#table-design-patterns) descrive la gestione della coerenza:

-	[Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori **RowKey** diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
-	[Modello per indice secondario intrapartizione](#inter-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
-	[Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) - Abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.
-	[Modello per entità di indice](#index-entities-pattern) - Mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  
-	[Modello di denormalizzazione](#denormalization-pattern) - Combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.  
-	[Modello di serie di dati](#data-series-pattern) - Archivia serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

Per informazioni sulle transazioni di gruppi di entità, vedere la sezione [Transazioni di gruppi di entità](#entity-group-transactions).

### Verifica della capacità della progettazione per modifiche efficienti di facilitare query efficienti  

In molti casi, una progettazione per query efficienti consente modifiche efficienti, ma è consigliabile valutare sempre se questa condizione si applica a uno specifico scenario. Alcuni dei modelli nella sezione [Modelli di progettazione tabelle](#table-design-patterns) valutano in modo esplicito i compromessi tra la query e la modifica delle entità. Inoltre è sempre consigliabile tenere in considerazione il numero di ogni tipo di operazione.

I seguenti modelli nella sezione [Modelli di progettazione tabelle](#table-design-patterns) considerano i compromessi tra la progettazione per query efficienti e la progettazione per una modifica efficiente dei dati:

-	[Modello per chiave composta](#compound-key-pattern) - Usa valori **RowKey** composti per consentire a un client di cercare dati correlati con una sola query di tipo punto.  
-	[Modello della parte finale del log](#log-tail-pattern) - Recupera le entità *n* aggiunte più di recente a una partizione in base a un valore **RowKey** che usa un ordinamento inverso di data e ora.  

## Crittografia dei dati di tabella    
     
La libreria client di Archiviazione di Azure per .NET supporta la crittografia di proprietà di entità stringa per le operazioni di inserimento e sostituzione. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono convertite nuovamente in stringhe dopo la decrittografia.

Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Questa operazione può essere eseguita specificando un attributo [EncryptProperty] (per le entità POCO che derivano da TableEntity) o un resolver di crittografia nelle opzioni di richiesta. Un resolver di crittografia è un delegato che accetta una chiave di partizione, una chiave di riga e un nome di proprietà e restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client utilizzerà queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura in rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografa la proprietà A; in caso contrario crittografa le proprietà A e B). Si noti che non è necessario fornire queste informazioni durante la lettura o la query su entità.

Si noti che l'unione non è attualmente supportata. Poiché un subset di proprietà potrebbe essere stato crittografato in precedenza utilizzando una chiave diversa, la semplice unione delle nuove proprietà e l’aggiornamento dei metadati comportano la perdita di dati. L'unione richiede chiamate a servizi aggiuntivi per la lettura dell’entità preesistente dal servizio o l’utilizzo di una nuova chiave per ogni proprietà, entrambe operazioni non idonee per motivi di prestazioni.

Per informazioni sulla crittografia dei dati di tabella, vedere [Crittografia lato client e insieme di credenziali delle chiavi di Azure per Archiviazione di Microsoft Azure](storage-client-side-encryption.md).

## Modellazione di relazioni  

La compilazione di modelli di dominio è un passaggio chiave della progettazione di sistemi complessi. Il processo di modellazione in genere viene usato per identificare le entità e le relazioni tra di esse, per poter comprendere il dominio aziendale e informare la progettazione del sistema. Questa sezione illustra come sia possibile convertire alcuni tipi comuni di relazione presenti nei modelli di dominio in progettazioni per il servizio tabelle. Il processo di mapping da un modello di dati logico a un modello di dati fisico basato su NoSQL è molto diverso da quello usato quando si progetta un database relazionale. La progettazione di database relazionali presuppone in genere un processo di normalizzazione dei dati ottimizzato per ridurre al minimo la ridondanza, oltre a una funzionalità di query dichiarativa che astrae l'implementazione per il funzionamento del database.

### Relazioni uno a molti  

Le relazioni uno a molti tra gli oggetti del dominio aziendale sono molto frequenti: ad esempio, un reparto ha più dipendenti. Esistono modi diversi per implementare le relazioni uno a molti nel servizio tabelle, ciascuno dei quali presenta pro e contro che potrebbero essere pertinenti a un particolare scenario.

Considerare l'esempio di una grande multinazionale con decine di migliaia di reparti ed entità dipendente, dove ogni reparto ha più dipendenti e ogni dipendente è associato a uno specifico reparto. Un approccio prevede l'archiviazione di entità reparto e dipendente separate, come queste:

![][1]

Questo esempio illustra una relazione uno a molti implicita tra i tipi basata sui valori **PartitionKey**. Ogni reparto può avere più dipendenti.

Questo esempio mostra anche un'entità reparto e le relative entità dipendente nella stessa partizione. È possibile scegliere di usare partizioni, tabelle o anche account di archiviazione diversi per ogni tipo di entità.

Un approccio alternativo prevede la denormalizzazione dei dati e l'archiviazione delle sole entità dipendente con i dati reparto denormalizzati, come illustrato nell'esempio seguente. In questo particolare scenario, l'approccio denormalizzato non sarà quello migliore se si deve essere in grado di cambiare i dettagli di un responsabile di reparto perché, per questa operazione, sarà necessario aggiornare ogni dipendente del reparto.

![][2]

Per ulteriori informazioni, vedere il [Modello di denormalizzazione](#denormalization-pattern) più avanti in questa guida.

La tabella seguente riepiloga i vantaggi e gli svantaggi di ogni approccio descritto sopra per l'archiviazione delle entità dipendente e reparto con una relazione uno a molti. Si consiglia inoltre di considerare la frequenza con cui si prevede di eseguire le diverse operazioni: una progettazione che include un'operazione dal costo elevato può essere accettabile se l'operazione non viene eseguita spesso.

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
<li>Le operazioni di archiviazione vengono eseguite nella stessa partizione. Con volumi di transazioni elevati, potrebbe risultarne un hotspot.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto con una transazione EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipi di entità distinti, partizioni o tabelle o account di archiviazione diversi</td>
<td>
<ul>
<li>È possibile aggiornare un'entità reparto o un'entità dipendente con un'unica operazione.</li>
<li>Con volumi di transazioni elevati, può essere più facile distribuire il carico tra più partizioni.</li>
</ul>
</td>
<td>
<ul>
<li>Per alcune attività client, potrebbe essere necessario recuperare sia un'entità dipendente che un'entità reparto.</li>
<li>Non è possibile usare le transazioni EGT per mantenere la coerenza quando si aggiorna/inserisce/elimina un dipendente e si aggiorna un reparto, ad esempio quando si aggiorna un conteggio dipendenti in un'entità reparto.</li>
<li>Non è possibile spostare un dipendente in un nuovo reparto con una transazione EGT.</li>
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
<li>Mantenere la coerenza potrebbe risultare costoso, se è necessario aggiornare le informazioni sui reparti, perché si dovrebbero aggiornare tutti i dipendenti di un reparto.</li>
</ul>
</td>
</tr>
</table>

Per scegliere tra queste opzioni e stabilire quali siano i vantaggi e gli svantaggi più importanti, è necessario considerare gli scenari specifici dell'applicazione. Ad esempio, ogni quanto si modificano le entità reparto, se tutte le query dei dipendenti richiedono informazioni aggiuntive sul reparto, quanto si è vicini ai limiti di scalabilità nelle partizioni o nell'account di archiviazione.

### Relazioni uno a uno  

I modelli di dominio possono includere relazioni uno a uno tra le entità. Se è necessario implementare una relazione uno a uno nel servizio tabelle, è necessario scegliere anche come collegare le due entità correlate quando è necessario recuperarle entrambe. Questo collegamento può essere implicito, ovvero basato su una convenzione nei valori chiave, o esplicito, ovvero basato sull'archiviazione di un collegamento all'entità correlata, sotto forma di valori **PartitionKey** e **RowKey** in ogni entità. Per informazioni utili a stabilire se archiviare le entità correlate nella stessa partizione, vedere la sezione [Relazioni uno a molti](#one-to-many-relationships).

Esistono anche alcune considerazioni sull'implementazione che potrebbero far decidere di implementare le relazioni uno a uno nel servizio tabelle:

-	Gestione di entità di grandi dimensioni (per altre informazioni, vedere [Modello di entità di grandi dimensioni](#large-entities-pattern)).  
-	Implementazione di controlli di accesso. Per altre informazioni, vedere [Controllo dell'accesso con le firme di accesso condiviso](#controlling-access-with-shared-access-signatures).  

### Join nel client  

Anche se esistono alcuni modi per modellare le relazioni nel servizio tabelle, è bene non dimenticare che i due principali motivi per usare il servizio tabelle sono la scalabilità e le prestazioni. Se ci si accorge che si stanno modellando troppe relazioni che compromettono le prestazioni e la scalabilità della soluzione, è consigliabile chiedersi se sia necessario compilare tutte le relazioni tra i dati nella progettazione tabelle. È possibile semplificare la progettazione e migliorare la scalabilità e le prestazioni della soluzione permettendo all'applicazione client di eseguire i join necessari.

Ad esempio, se si dispone di tabelle di piccole dimensioni che contengono dati che non cambiano molto spesso, è possibile recuperare i dati una sola volta e memorizzarli nella cache del client. Questo consente di evitare round trip ripetuti per il recupero degli stessi dati. Negli esempi esaminati in questa guida, il set di reparti di una piccola organizzazione sarà probabilmente di dimensioni ridotte e cambierà raramente. Si tratta di un caso ideale di dati che l'applicazione client può scaricare una sola volta e memorizzare nella cache come dati di ricerca.

### Relazioni di ereditarietà  

Se l'applicazione client usa un set di classi che fanno parte di una relazione di ereditarietà per rappresentare entità aziendali, è possibile rendere facilmente le entità persistenti nel servizio tabelle. Ad esempio, nell'applicazione client potrebbe essere definito il set di classi seguente, dove **Person** è una classe astratta.

![][3]

È possibile rendere persistenti le istanze delle due classi concrete nel servizio tabelle usando una singola tabella Persone con entità simili alle seguenti:

![][4]

Per altre informazioni sull'uso di più tipi di entità nella stessa tabella nel codice del client, vedere la sezione [Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types) più avanti in questa guida. In questa sezione sono disponibili esempi su come riconoscere il tipo di entità nel codice del client.

## Modelli di progettazione tabelle
Le sezioni precedenti illustrano in dettaglio come ottimizzare la progettazione della tabella sia per il recupero dei dati di entità mediante query che per l'inserimento, l'aggiornamento e l'eliminazione dei dati di entità. Questa sezione descrive alcuni modelli adatti all'uso con le soluzioni di servizio tabelle. Fornisce inoltre informazioni su come risolvere alcuni dei problemi e dei compromessi evidenziati in precedenza in questa guida. Il diagramma seguente contiene un riepilogo delle relazioni tra i diversi modelli:

![][5]

La mappa dei modelli nella figura precedente evidenzia alcune relazioni tra i modelli (blu) e gli anti-modelli (arancione) documentati in questa guida. Ovviamente esistono molti altri modelli utili. Ad esempio, uno degli scenari chiave per il servizio tabelle è l'uso del [modello di vista materializzata](https://msdn.microsoft.com/library/azure/dn589782.aspx) dal modello [Command Query Responsibility Segregation](https://msdn.microsoft.com/library/azure/jj554200.aspx) (CQRS).

### Modello per indice secondario intrapartizione
Archivia più copie di ogni entità usando valori **RowKey** diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi. Gli aggiornamenti tra copie possono essere mantenuti coerenti usando transazioni ETG.

#### Contesto e problema
Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey**. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente mediante questi valori. Ad esempio, usando la struttura della tabella riportata di seguito, un'applicazione client può usare una query di tipo punto per recuperare una singola entità dipendente attraverso il nome del reparto e l'ID del dipendente (i valori **PartitionKey** e **RowKey**). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.

![][6]

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Il motivo è che il servizio tabelle non fornisce indici secondari. Inoltre, non esiste un'opzione per richiedere un elenco di dipendenti ordinato in modo diverso rispetto all'ordine **RowKey**.

#### Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità usando per ogni copia un valore **RowKey** diverso. Se si archivia un'entità con le strutture riportate di seguito, è possibile recuperare in modo efficiente entità dipendente in base all'id dipendente o all’indirizzo di posta elettronica. I valori di prefisso il **RowKey**, "empid\_" e "email\_" consentono di eseguire una query per un singolo dipendente o un intervallo di dipendenti utilizzando un intervallo di indirizzi di posta elettronica o ID dipendente.

![][7]

I due criteri di filtro seguenti (uno che ricerca per ID dipendente e uno che ricerca per indirizzo di posta elettronica) specificano entrambi query di tipo punto:

-	$filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid\_000223)  
-	$filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se si esegue una query su un intervallo di entità dipendente, è possibile specificare un intervallo ordinato per ID dipendente o un intervallo ordinato per indirizzo di posta elettronica eseguendo la query sulle entità con il prefisso appropriato in **RowKey**.

-	Per trovare tutti i dipendenti nel reparto vendite con un id dipendente in uso nell'intervallo che va da 000100 a 000199 utilizzare: $filter = (PartitionKey eq "Sales") e (RowKey ge'empid\_000100') e (RowKey le 'empid\_000199')  
-	Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con la lettera "a" utilizzare: $filter = (PartitionKey eq "Sales") e (RowKey ge 'email\_a') e (RowKey It'email\_b')  

 Si noti che la sintassi del filtro usata negli esempi precedenti proviene dall'API REST del servizio tabelle. Per altre informazioni, vedere [Query Entities](http://msdn.microsoft.com/library/azure/dd179421.aspx).

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	L'uso dell'archiviazione tabelle è relativamente economico, pertanto l'aumento dei costi dovuto all'archiviazione di dati duplicati non dovrebbe rappresentare una preoccupazione. È però consigliabile valutare sempre il costo del progetto in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che verranno eseguite dall'applicazione client.  
-	Poiché le entità di indice secondario vengono archiviate nella stessa partizione delle entità originali, è necessario assicurarsi di non superare gli obiettivi di scalabilità delle singole partizioni.  
-	Per mantenere la coerenza tra entità duplicate è possibile usare transazioni ETG, che consentono di aggiornare le due copie dell'entità in modo atomico. A questo scopo è necessario archiviare tutte le copie di un'entità nella stessa partizione. Per altre informazioni, vedere la sezione [Transazioni di gruppi di entità](#entity-group-transactions).  
-	Il valore usato per **RowKey** deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
-	Il riempimento dei valori numerici in **RowKey** (ad esempio l'ID dipendente 000223) rende possibile l'ordinamento e il filtro corretto in base ai limiti superiori e inferiori.  
-	Non è necessario duplicare tutte le proprietà dell'entità. Ad esempio, se le query che eseguono la ricerca di entità usando l'indirizzo di posta elettronica in **RowKey** non hanno mai bisogno dell'età del dipendente, queste entità potrebbero avere la struttura seguente:

![][8]

-	In genere è preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query anziché usando una query per individuare un'entità e una seconda per cercare i dati richiesti.  

#### Quando usare questo modello  

Usare questo modello quando l'applicazione client deve recuperare le entità usando una serie di chiavi diverse, quando il client deve recuperare entità con criteri di ordinamento diversi e nei casi in cui è possibile identificare ogni entità attraverso una varietà di valori univoci. È però necessario assicurarsi che durante l'esecuzione di ricerche di entità con valori **RowKey** diversi non vengano superati i limiti di scalabilità della partizione.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Modello per indice secondario interpartizione](#inter-partition-secondary-index-pattern)
-	[Modello per chiave composta](#compound-key-pattern)
-	[Transazioni dei gruppi di entità](#entity-group-transactions)
-	[Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)

### Modello per indice secondario interpartizione
Archivia più copie di ogni entità usando valori **RowKey** diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.

#### Contesto e problema
Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey**. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente mediante questi valori. Ad esempio, usando la struttura della tabella riportata di seguito, un'applicazione client può usare una query di tipo punto per recuperare una singola entità dipendente attraverso il nome del reparto e l'ID del dipendente (i valori **PartitionKey** e **RowKey**). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.

![][9]

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Il motivo è che il servizio tabelle non fornisce indici secondari. Inoltre, non esiste un'opzione per richiedere un elenco di dipendenti ordinato in modo diverso rispetto all'ordine **RowKey**.

Si prevede un volume molto elevato di transazioni su queste entità e si vuole ridurre al minimo il rischio che il servizio tabelle esegua la limitazione del client.

#### Soluzione  
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità usando per ogni copia valori **PartitionKey** e **RowKey** diversi. Se si archivia un'entità con le strutture riportate di seguito, è possibile recuperare in modo efficiente entità dipendente in base all'id dipendente o all’indirizzo di posta elettronica. I valori di prefisso per **PartitionKey**, "empid\_" e "email\_" consentono di identificare quale indice usare per una query.

![][10]

I due criteri di filtro seguenti (uno che ricerca per ID dipendente e uno che ricerca per indirizzo di posta elettronica) specificano entrambi query di tipo punto:

-	$filter=(PartitionKey 'empid\_Sales') e (RowKey eq '000223')
-	$filter = (PartitionKey eq ' email\_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se si esegue una query su un intervallo di entità dipendente, è possibile specificare un intervallo ordinato per ID dipendente o un intervallo ordinato per indirizzo di posta elettronica eseguendo la query sulle entità con il prefisso appropriato in **RowKey**.

-	Per trovare tutti i dipendenti del reparto vendite con un ID dipendente nell'intervallo compreso tra **000100** e **000199** ordinati in base all'ID dipendente, usare: $filter=(PartitionKey eq 'empid\_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
-	Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con 'a' ordinato in base all’indirizzo di posta elettronica utilizzare: $filter = (PartitionKey eq ' email\_Sales') e (RowKey ge 'a') e (RowKey lt "b")  

Si noti che la sintassi del filtro usata negli esempi precedenti proviene dall'API REST del servizio tabelle. Per altre informazioni, vedere [Query Entities](http://msdn.microsoft.com/library/azure/dd179421.aspx).

#### Considerazioni e problemi  
Prima di decidere come implementare questo modello, considerare quanto segue:

-	Per mantenere la coerenza finale tra le entità duplicate, è possibile usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per gestire le entità di indice primario e secondario.  
-	L'uso dell'archiviazione tabelle è relativamente economico, pertanto l'aumento dei costi dovuto all'archiviazione di dati duplicati non dovrebbe rappresentare una preoccupazione. È però consigliabile valutare sempre il costo del progetto in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che verranno eseguite dall'applicazione client.  
-	Il valore usato per **RowKey** deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
-	Il riempimento dei valori numerici in **RowKey** (ad esempio l'ID dipendente 000223) rende possibile l'ordinamento e il filtro corretto in base ai limiti superiori e inferiori.  
-	Non è necessario duplicare tutte le proprietà dell'entità. Ad esempio, se le query che eseguono la ricerca di entità usando l'indirizzo di posta elettronica in **RowKey** non hanno mai bisogno dell'età del dipendente, queste entità potrebbero avere la struttura seguente:

	![][11]

-	In genere è preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query anziché usando una query per individuare un'entità mediante l'indice secondario e un'altra per cercare i dati richiesti nell'indice primario.

#### Quando usare questo modello  
Usare questo modello quando l'applicazione client deve recuperare le entità usando una serie di chiavi diverse, quando il client deve recuperare entità con criteri di ordinamento diversi e nei casi in cui è possibile identificare ogni entità attraverso una varietà di valori univoci. Usare questo modello quando si vuole evitare il superamento dei limiti di scalabilità della partizione durante l'esecuzione di ricerche di entità con i diversi valori **RowKey**.

#### Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
-	[Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern)  
-	[Modello per chiave composta](#compound-key-pattern)  
-	[Transazioni dei gruppi di entità](#entity-group-transactions)  
-	[Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  

### Modello per transazioni con coerenza finale  

abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.

#### Contesto e problema  

Le transazioni ETG consentono l'esecuzione di transazioni atomiche tra più entità che condividono la stessa chiave di partizione. Per motivi di scalabilità e prestazioni, si può scegliere di archiviare le entità con requisiti di coerenza in partizioni separate o in un sistema di archiviazione separato: in questo caso, non è possibile usare le transazioni ETG per mantenere la coerenza. Ad esempio, potrebbe essere necessario mantenere la coerenza finale tra:

-	Entità archiviate in due partizioni diverse nella stessa tabella, in tabelle diverse, in account di archiviazione diversi.  
-	Un'entità archiviata nel servizio tabelle e un BLOB archiviato nel servizio BLOB.  
-	Un'entità archiviata nel servizio tabelle e un file in un file system.  
-	Un'entità archiviata nel servizio tabelle, ma indicizzata mediante Ricerca di Azure.  

#### Soluzione  

Usando le code di Azure, è possibile implementare una soluzione che offre coerenza finale tra due o più partizioni o sistemi di archiviazione. Per illustrare questo approccio, si supponga di avere l'esigenza di archiviare le entità relative ai dipendenti precedenti. Queste entità sono raramente oggetto di query e devono essere escluse da tutte le attività associate ai dipendenti correnti. Per implementare questo requisito è necessario archiviare i dipendenti attivi nella tabella dei dipendenti **Correnti** e i dipendenti precedenti nella tabella dei dipendenti **Archiviati**. Per archiviare un dipendente è necessario eliminare l'entità dalla tabella dei dipendenti **Correnti** e aggiungerla a quella dei dipendenti **Archiviati**, ma non è possibile usare una transazione ETG per eseguire queste due operazioni. Per evitare il rischio che, a causa di un errore, un'entità venga visualizzata in entrambe le tabelle o in nessuna di esse, l'operazione di archiviazione deve garantire la coerenza finale. Il diagramma seguente illustra in sequenza i passaggi di questa operazione. Nel testo che segue sono disponibili maggiori dettagli per i percorsi di eccezione.

![][12]

Un client avvia l'operazione di archiviazione inserendo un messaggio in una coda di Azure, in questo esempio per l'archiviazione del dipendente 456. Un ruolo di lavoro esegue il polling della coda per individuare i nuovi messaggi. Quando ne trova uno, legge il messaggio e lascia una copia nascosta nella coda. Successivamente, il ruolo di lavoro recupera una copia dell'entità dalla tabella dei dipendenti **Correnti**, inserisce una copia nella tabella dei dipendenti **Archiviati** e quindi elimina l'originale dalla tabella dei dipendenti **Correnti**. Infine, se nei passaggi precedenti non si sono verificati errori, il ruolo di lavoro elimina il messaggio nascosto dalla coda.

In questo esempio, il passaggio 4 inserisce il dipendente nella tabella dei dipendenti **Archiviati**. Potrebbe aggiungere il dipendente a un BLOB nel servizio BLOB o un file in un file system.

#### Ripristino da errori  

È importante che le operazioni nei passaggi **4** e **5** siano *idempotenti* nei casi in cui il ruolo di lavoro deve riavviare l'operazione di archiviazione. Se si sta usando il servizio tabelle, per il passaggio **4** è consigliabile usare un'operazione "insert or replace"; per il passaggio **5** è consigliabile usare un'operazione "delete if exists" nella libreria client in uso. Se si sta usando un altro sistema di archiviazione, è consigliabile usare un'operazione idempotente appropriata.

Se il ruolo di lavoro non completa mai il passaggio **6**, dopo un timeout il messaggio ricompare nella coda, pronto per una nuova elaborazione da parte del ruolo di lavoro. Il ruolo di lavoro può controllare quante volte un messaggio nella coda è stato letto e, se necessario, contrassegnarlo come messaggio non elaborabile da analizzare inviandolo a una coda separata. Per altre informazioni sulla lettura dei messaggi in coda e la verifica del numero di rimozioni dalla coda, vedere [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx).

Alcuni errori del servizio tabelle e del servizio di accodamento sono temporanei e l'applicazione client deve includere la logica di ripetizione dei tentativi appropriata per gestirli.

#### Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:

-	Questa soluzione non prevede l'isolamento delle transazioni. Ad esempio, un client potrebbe leggere le tabelle dei dipendenti **Correnti** e **Archiviati** mentre il ruolo di lavoro è tra i passaggi **4** e **5** e ottenere una vista incoerente dei dati. Si noti che alla fine i dati saranno coerenti.  
-	È necessario assicurarsi che i passaggi 4 e 5 siano idempotenti per garantire la coerenza finale.  
-	È possibile ridimensionare la soluzione usando più code e istanze del ruolo di lavoro.  

#### Quando usare questo modello  
Usare questo modello quando si desidera garantire la coerenza finale tra entità esistenti in tabelle o partizioni diverse. È possibile estendere il modello per garantire la coerenza finale per le operazioni tra il servizio tabelle e il servizio BLOB e altre origini dati di archiviazione non Azure, quali database o file system.

#### Modelli correlati e informazioni aggiuntive  
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:
-	[Transazioni dei gruppi di entità](#entity-group-transactions)  
-	[Unione o sostituzione](#merge-or-replace)  

>[AZURE.NOTE] Se l'isolamento delle transazioni è importante per la soluzione, è consigliabile riprogettare le tabelle per consentire l'uso delle transazioni ETG.

### Modello per entità di indice:
mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.

#### Contesto e problema  

Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey**. Consente a un'applicazione client di recuperare un'entità in modo efficiente mediante una query di tipo punto. Ad esempio, usando la struttura della tabella riportata di seguito, un'applicazione client può recuperare in modo efficiente una singola entità dipendente usando il nome del reparto e l'ID del dipendente (i valori **PartitionKey** e **RowKey**).

![][13]

Se si desidera poter recuperare un elenco di entità dipendente anche in base al valore di un'altra proprietà non univoca, ad esempio il cognome, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza piuttosto che usare un indice per la ricerca diretta. Il motivo è che il servizio tabelle non fornisce indici secondari.

#### Soluzione  

Per attivare la ricerca per cognome con la struttura delle entità illustrata in precedenza, è necessario gestire elenchi di ID dipendente. Per recuperare le entità dipendente con un determinato cognome, ad esempio Jones, è necessario innanzitutto individuare l'elenco di ID relativi ai dipendenti con il cognome Jones e quindi recuperare tali entità dipendente. Per l'archiviazione dell'elenco di ID dipendente sono disponibili tre opzioni principali:

-	Usare l'archiviazione BLOB.  
-	Creare entità di indice nella stessa partizione delle entità dipendente.  
-	Creare entità di indice in una tabella o una partizione separata.  

<u>Opzione 1: usare l'archiviazione BLOB</u>

Per la prima opzione è necessario creare un BLOB per ogni cognome univoco e archiviare in ogni BLOB un elenco dei valori **PartitionKey** (reparto) e **RowKey** (ID dipendente) per i dipendenti con questo cognome. Quando si aggiunge o elimina un dipendente, è necessario verificare la coerenza finale tra il contenuto del BLOB pertinente e le entità dipendente.

<u>Opzione 2:</u> creare entità di indice nella stessa partizione

Per la seconda opzione, usare entità di indice che archiviano i dati seguenti:

![][14]

La proprietà **EmployeeIDs** contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in **RowKey**.

I passaggi seguenti illustrano il processo da seguire per aggiungere un nuovo dipendente se si usa la seconda opzione. In questo esempio si aggiunge al reparto vendite un dipendente con ID 000152 e cognome Jones:
1.	Recuperare l'entità di indice con il valore **PartitionKey** "Sales" e il valore **RowKey** "Jones". Salvare il valore ETag dell'entità per usarlo nel passaggio 2.  
2.	Creare una transazione del gruppo di entità che inserisca la nuova entità dipendente (con valore **PartitionKey** "Sales" e valore **RowKey** "000152") e aggiorni l'entità di indice (con valore **PartitionKey** "Sales" e valore **RowKey** "Jones") aggiungendo il nuovo ID dipendente all'elenco nel campo EmployeeIDs. Per informazioni sulle transazioni di gruppi di entità, vedere la sezione [Transazioni di gruppi di entità](#entity-group-transactions).  
3.	Se la transazione del gruppo di entità ha esito negativo a causa di un errore di concorrenza ottimistica (un altro utente ha appena modificato l'entità di indice), è necessario ricominciare dal passaggio 1.  

Se si usa la seconda opzione, è possibile adottare un approccio simile per l'eliminazione di un dipendente. Modificare il cognome del dipendente è un'operazione leggermente più complessa, in quanto è necessario eseguire una transazione del gruppo di entità che aggiorna tre entità: l'entità dipendente, l'entità di indice per il cognome precedente e l'entità di indice per il nuovo cognome. È necessario recuperare ogni entità prima di apportare qualsiasi modifica, per recuperare i valori ETag da usare in seguito per eseguire gli aggiornamenti usando la concorrenza ottimistica.

I passaggi seguenti illustrano il processo da seguire per cercare tutti i dipendenti di un reparto con un determinato cognome se si usa la seconda opzione. In questo esempio si cercano tutti i dipendenti del reparto vendite il cui cognome è Jones:

1.	Recuperare l'entità di indice con il valore **PartitionKey** "Sales" e il valore **RowKey** "Jones".  
2.	Analizzare l'elenco di ID dipendente nel campo EmployeeIDs.  
3.	Se sono necessarie informazioni aggiuntive su ognuno dei dipendenti (ad esempio gli indirizzi di posta elettronica), recuperare ognuna delle entità dipendente usando il valore **PartitionKey** "Sales" e i valori **RowKey** dall'elenco dei dipendenti ottenuti nel passaggio 2.  

<u>Opzione 3:</u> creare entità di indice in una tabella o una partizione separata

Per la terza opzione, usare entità di indice che archiviano i dati seguenti:

![][15]

La proprietà **EmployeeIDs** contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in **RowKey**.

Con la terza opzione non è possibile usare transazioni ETG per mantenere la coerenza, in quanto le entità di indice si trovano in una partizione separata rispetto alle entità dipendente. È necessario assicurarsi della coerenza finale tra le entità di indice e le entità dipendente.

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:
-	Questa soluzione richiede almeno due query per recuperare le entità corrispondenti: una sulle entità di indice per ottenere l'elenco di valori **RowKey** e altre query per il recupero di ogni entità dell'elenco.  
-	Poiché una singola entità ha una dimensione massima di 1 MB, le opzioni 2 e 3 della soluzione presuppongono che l'elenco di ID dipendente per qualsiasi cognome non sia mai più grande di 1 MB. Se è probabile che l'elenco di ID dipendente abbia dimensioni superiori a 1 MB, usare l'opzione 1 e archiviare i dati di indice nell'archiviazione BLOB.  
-	Se si usa l'opzione 2 (usando transazioni ETG per gestire l'aggiunta e l'eliminazione dei cognomi dei dipendenti) è necessario valutare se il volume delle transazioni raggiungerà i limiti di scalabilità in una determinata partizione. In tal caso, è opportuno considerare una soluzione con coerenza finale (opzione 1 o 3) che gestisca le richieste di aggiornamento mediante code e consenta di archiviare le entità di indice in una partizione separata rispetto alle entità dipendente.  
-	L'opzione 2 di questa soluzione presuppone che si vogliano effettuare ricerche in base al cognome all'interno di un reparto, ad esempio recuperare un elenco di dipendenti del reparto vendite il cui cognome è Jones. Se si desidera poter cercare tutti i dipendenti il cui cognome è Jones nell'intera organizzazione, usare l'opzione 1 o l'opzione 3.
-	È possibile implementare una soluzione basata su code che garantisce coerenza finale. Per altre informazioni, vedere [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern).  

#### Quando usare questo modello  

Usare questo modello quando si desidera cercare un set di entità che condividono un valore di proprietà comune, ad esempio tutti i dipendenti con il cognome Jones.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:
-	[Modello per chiave composta](#compound-key-pattern)  
-	[Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
-	[Transazioni dei gruppi di entità](#entity-group-transactions)  
-	[Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  

### Modello di denormalizzazione:  

combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.

#### Contesto e problema  

In un database relazionale, in genere i dati vengono normalizzati per rimuovere i risultati duplicati nelle query che recuperano dati da più tabelle. Se si normalizzano i dati nelle tabelle di Azure, è necessario eseguire più round trip dal client al server per recuperare i dati correlati. Con la struttura della tabella riportata di seguito, ad esempio, per recuperare i dettagli per un reparto sono necessari due round trip: uno per recuperare l'entità reparto che include l'ID del manager e una seconda richiesta per recuperare i dettagli sul manager in un'entità dipendente.

![][16]

#### Soluzione  

Anziché archiviare i dati in due entità separate, denormalizzare i dati e conservare una copia dei dettagli sul manager nell'entità reparto. Ad esempio:

![][17]

Archiviando le entità reparto con queste proprietà, è possibile recuperare tutti i dettagli necessari su un reparto mediante una query di tipo punto.

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	Archiviare alcuni dati due volte comporta un aumento dei costi. Il miglioramento delle prestazioni (risultante dal minor numero di richieste al servizio di archiviazione) in genere compensa l'incremento marginale dei costi di archiviazione, che per altro è parzialmente compensato dalla riduzione del numero di transazioni necessarie per recuperare i dettagli relativi a un reparto.  
-	È necessario mantenere la coerenza tra le due entità in cui sono archiviate le informazioni sui manager. Il problema della coerenza può essere gestito usando transazioni ETG per aggiornare più entità in una singola transazione atomica: in questo caso, l'entità reparto e l'entità dipendente per il responsabile del reparto vengono archiviate nella stessa partizione.  

#### Quando usare questo modello
Usare questo modello quando è necessario cercare spesso informazioni correlate. Questo modello riduce il numero di query che il client deve eseguire per recuperare i dati necessari.

#### Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:
-	[Modello per chiave composta](#compound-key-pattern)  
-	[Transazioni dei gruppi di entità](#entity-group-transactions)  
-	[Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)

### Modello per chiave composta  

Usa valori **RowKey** composti per consentire a un client di cercare dati correlati con una sola query di tipo punto.

#### Contesto e problema  

In un database relazionale è piuttosto normale usare join nelle query per restituire dati correlati al client in una singola query. Ad esempio, si può usare l'ID dipendente per cercare un elenco di entità correlate che contengono i dati relativi alle prestazioni e alle valutazioni per tale dipendente.

Si supponga di archiviare le entità dipendente nel servizio tabelle usando la struttura seguente:

![][18]

È inoltre necessario archiviare i dati cronologici relativi alle valutazioni e alle prestazioni per ogni anno che il dipendente ha lavorato presso l'organizzazione, nonché poter accedere a queste informazioni in base all'anno. Una possibilità consiste nel creare un'altra tabella di archiviazione delle entità con la struttura seguente:

![][19]

Si noti che con questo approccio è possibile decidere di duplicare alcune informazioni (ad esempio nome e cognome) nella nuova entità, in modo da poter recuperare i dati con una singola richiesta. Non è tuttavia possibile mantenere la coerenza assoluta, in quanto non si può usare una transazione EGT per aggiornare le entità in modo atomico.

#### Soluzione
Archiviare un nuovo tipo di entità nella tabella originale usando entità con la struttura seguente:

![][20]

Si noti che ora il valore **RowKey** è una chiave composta costituita dall'ID dipendente e dall'anno dei dati di valutazione, che consente di recuperare le prestazioni e le valutazioni del dipendente con una singola richiesta per una singola entità.

L'esempio seguente illustra come recuperare tutti i dati di valutazione per uno specifico dipendente (ad esempio il dipendente 000123 del reparto vendite):

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid\_000123') and (RowKey lt 'empid\_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments

#### Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:

-	È consigliabile usare un carattere separatore appropriato che semplifichi l'analisi del valore **RowKey**, ad esempio **000123\_2012**.  
-	Inoltre, si sta archiviando l'entità nella stessa partizione di altre entità che contengono dati correlati per lo stesso dipendente, dunque è possibile usare transazioni EGT per mantenere la coerenza assoluta.
-	Per determinare se questo modello è appropriato, considerare la frequenza con cui si eseguiranno query sui dati. Ad esempio, se si accederà raramente ai dati di valutazione e spesso ai dati principali sul dipendente, è consigliabile conservarli come entità separate.  

#### Quando usare questo modello  

Usare questo modello quando è necessario archiviare una o più entità correlate su cui si eseguono query frequenti.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Transazioni dei gruppi di entità](#entity-group-transactions)  
-	[Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  
-	[Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  

### Modello della parte finale del log  

recupera le *e*ntità aggiunte più di recente a una partizione in base a un valore **RowKey** che usa un ordinamento inverso di data e ora.

#### Contesto e problema  

Un requisito comune è poter recuperare le entità create più di recente, ad esempio le ultime dieci note di rimborso spese inviate da un dipendente. Le query sulle tabelle supportano un'operazione di query **$top** per restituire le prime *n* entità di un set. Non esiste un'operazione di query equivalente per la restituzione delle ultime n entità di un set.

#### Soluzione  

Archiviare le entità usando un valore **RowKey** che usa un ordinamento inverso di data e ora, in modo che la voce più recente sia sempre la prima della tabella.

Ad esempio, per poter recuperare le ultime dieci note di rimborso spese inviate da un dipendente, è possibile usare un valore di tick inverso derivato dal valore di data/ora corrente. L'esempio di codice C# seguente illustra un modo per creare un valore "invertedTicks" appropriato per un valore **RowKey** che ordina dal più recente al meno recente:

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`

Per tornare al valore di data e ora, usare il codice seguente:

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`

La query sulla tabella ha un aspetto simile al seguente:

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	È necessario aggiungere zeri iniziali al valore di tick inverso per assicurarsi che il valore di stringa venga ordinato come previsto.  
-	È necessario tenere presenti gli obiettivi di scalabilità a livello di partizione. Fare attenzione a non creare partizioni critiche.  

#### Quando usare questo modello  

Usare questo modello quando si desidera accedere alle entità in ordine di data/ora inverso o quando è necessario accedere alle entità aggiunte più di recente.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Anti-modello prepend/append](#prepend-append-anti-pattern)  
-	[Recupero di entità](#retrieving-entities)  

### Modello di eliminazione volume elevato  

Abilitare l'eliminazione di un volume elevato di entità mediante l'archiviazione di tutte le entità per l'eliminazione simultanea nella relativa tabella separata; per eliminare le entità, eliminare la tabella.

#### Contesto e problema  

Molte applicazioni eliminano vecchi dati non più necessari a un'applicazione client o che l'applicazione ha archiviato in un altro supporto di archiviazione. In genere questi dati vengono identificati da una data; è presente un requisito che prevede l'eliminazione dei record di tutte le richieste di accesso risalenti a oltre 60 giorni prima.

Una possibile progettazione consiste nell'usare la data e l'ora della richiesta di accesso in **RowKey**:

![][21]

Questo approccio evita hotspot di partizione perché l'applicazione può inserire ed eliminare entità di accesso per ogni utente in una partizione separata, ma può rivelarsi dispendioso in termini di denaro e tempo se si dispone di un numero elevato di entità perché è necessario innanzitutto eseguire un'analisi di tabella per identificare tutte le entità da eliminare e successivamente eliminare ogni entità precedente. Si noti che è possibile ridurre il numero di round trip al server necessari per eliminare le entità precedenti raggruppando più richieste di eliminazione nelle transazioni EGT.

#### Soluzione  

Usare una tabella separata per ogni giorno di tentativi di accesso. È possibile usare la progettazione di entità riportata sopra per evitare hotspot durante l'inserimento di entità; l'eliminazione di entità comporterà semplicemente l'eliminazione di una tabella al giorno (una singola operazione di archiviazione) invece della ricerca ed eliminazione di centinaia o migliaia di singole entità ogni giorno.

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	La progettazione supporta altre modalità di uso dei dati da parte dell'applicazione, come la ricerca di entità specifiche, il collegamento con altri dati o la generazione di informazioni aggregate?  
-	La progettazione consente di evitare hotspot durante l'inserimento di nuove entità?  
-	Se si vuole riutilizzare lo stesso nome di tabella dopo l'eliminazione, prevedere un ritardo. È consigliabile usare sempre nomi di tabella univoci.  
-	Prevedere una limitazione delle richieste quando si usa per la prima volta una nuova tabella mentre il servizio tabelle apprende i modelli di accesso e le partizioni vengono distribuite in nodi. È necessario considerare la frequenza con cui è necessario creare nuove tabelle.  

#### Quando usare questo modello  

Usare questo modello quando si dispone di un volume elevato di entità che è necessario eliminare contemporaneamente.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Transazioni dei gruppi di entità](#entity-group-transactions)
-	[Modifica di entità](#modifying-entities)  

### Modello di serie di dati  

Archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.

#### Contesto e problema  

Spesso un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente. L'applicazione potrebbe, ad esempio, registrare il numero di messaggi immediati che ogni dipendente invia ogni ora e quindi usare queste informazioni per tracciare il numero di messaggi inviati da ogni utente nelle 24 ore precedenti. Una progettazione potrebbe essere l'archiviazione di 24 entità per ogni dipendente:

![][22]

Con questa progettazione è possibile individuare e aggiornare l'entità da aggiornare per ogni dipendente ogni volta che l'applicazione deve aggiornare il valore del numero di messaggi. Tuttavia, per recuperare le informazioni allo scopo di tracciare un grafico dell'attività per le 24 ore precedenti, è necessario recuperare 24 entità.

#### Soluzione  

Usare la progettazione seguente con una proprietà separata per archiviare il numero di messaggi per ogni ora:

![][23]

Con questa progettazione è possibile usare un'operazione di unione per aggiornare il numero di messaggi per un dipendente per un'ora specifica. A questo punto, è possibile recuperare tutte le informazioni necessarie per tracciare il grafico usando una richiesta per una singola entità.

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:
-	Se la serie di dati completa non rientra in una singola entità (un'entità può contenere fino a 252 proprietà), usare un archivio dati alternativo, ad esempio un BLOB.  
-	Se sono presenti più client che aggiornano un'entità contemporaneamente, sarà necessario usare l'**ETag** per implementare la concorrenza ottimistica. Se si dispone di molti client, potrebbe verificarsi un conflitto elevato.  

#### Quando usare questo modello  

Usare questo modello quando è necessario aggiornare e recuperare una serie di dati associata a una singola entità.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Modello di entità di grandi dimensioni](#large-entities-pattern)  
-	[Unione o sostituzione](#merge-or-replace)  
-	[Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) (se si archiviano le serie di dati in un BLOB)  

### Modello di entità di grandi dimensioni  

Usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.

#### Contesto e problema  

Una singola entità può avere più di 252 proprietà (escludendo le proprietà di sistema obbligatorie) e non è possibile memorizzare più di 1 MB di dati in totale. In un database relazionale è in genere possibile aggirare gli eventuali limiti sulle dimensioni di una riga aggiungendo una nuova tabella e applicando una relazione 1 a 1 tra di esse.

#### Soluzione  

Usando il servizio tabelle, è possibile archiviare più entità per rappresentare un singolo oggetto aziendale di grandi dimensioni con più di 252 proprietà. Ad esempio, se si vuole archiviare un conteggio del numero di messaggi immediati inviati da ogni dipendente negli ultimi 365 giorni, è possibile usare la progettazione seguente che si avvale di due entità con schemi diversi:

![][24]

Per apportare una modifica che richiede l'aggiornamento di entrambe le entità per mantenerle sincronizzate tra loro, è possibile usare una transazione EGT. Diversamente, è possibile usare una singola operazione di unione per aggiornare il numero di messaggi per un giorno specifico. Per recuperare tutti i dati per un singolo dipendente, è necessario recuperare entrambe le entità, operazione che è possibile eseguire con due richieste efficienti che usano entrambe un valore **PartitionKey** e **RowKey**.

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	Il recupero di un'entità logica completa richiede almeno due transazioni di archiviazione, una per recuperare ogni entità fisica.  

#### Quando usare questo modello  

Usare questo modello quando è necessario archiviare entità le cui dimensioni o il cui numero di proprietà superano i limiti per una singola entità nel servizio tabelle.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Transazioni dei gruppi di entità](#entity-group-transactions)
-	[Unione o sostituzione](#merge-or-replace)

### Modello di entità di grandi dimensioni  

Usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.

#### Contesto e problema  

Una singola entità non può memorizzare più di 1 MB di dati in totale. Se una o più proprietà archiviano valori che causano il superamento delle dimensioni totali dell'entità, non è possibile archiviare l'intera entità nel servizio tabelle.

#### Soluzione  

Se l'entità supera le dimensioni di 1 MB perché una o più proprietà contengono una grande quantità di dati, è possibile archiviare i dati nel servizio BLOB e quindi archiviare l'indirizzo del BLOB in una proprietà nell'entità. Ad esempio, è possibile archiviare la foto di un dipendente nell'archiviazione BLOB e archiviare un collegamento a foto nella proprietà **Photo** dell'entità del dipendente:

![][25]

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	Per mantenere la coerenza finale tra l'entità nel servizio tabelle e i dati nel servizio BLOB, usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per mantenere le identità.
-	Il recupero di un'entità completa richiede almeno due transazioni di archiviazione: una per recuperare l'entità e un'altra per recuperare i dati BLOB.  

#### Quando usare questo modello  

Usare questo modello quando è necessario archiviare entità le cui dimensioni superano i limiti per una singola entità nel servizio tabelle.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
-	[Modello di entità di grandi dimensioni](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>
### Anti-modello prepend/append  

Quando si dispone di un volume elevato di inserimenti, aumentare la scalabilità suddividendoli tra più partizioni.

#### Contesto e problema  

L'anteposizione o l'aggiunta di entità alle entità archiviate determina in genere l'aggiunta da parte dell'applicazione di nuove entità alla prima o ultima partizione di una sequenza di partizioni. In questo caso, tutti gli inserimenti in un determinato momento vengono eseguiti nella stessa partizione, creando un hotspot che impedisce al servizio tabelle di bilanciare il carico degli inserimenti tra più nodi e causando il possibile raggiungimento degli obiettivi di scalabilità per partizione da parte dell'applicazione. Ad esempio, se si dispone di un'applicazione che registra l'accesso alla rete e alle risorse da parte dei dipendenti, la struttura dell'entità mostrata sotto potrebbe determinare la trasformazione della partizione dell'ora corrente in un hotspot se il volume delle transazioni raggiunge l'obiettivo di scalabilità per una singola partizione:

![][26]

#### Soluzione  

La struttura di un'entità alternativa seguente evita gli hotspot in qualsiasi partizione specifica quando l'applicazione effettua la registrazione di eventi:

![][27]

Si noti come in questo esempio entrambi i valori **PartitionKey** e **RowKey** siano chiavi composte. Il valore **PartitionKey** usa sia l'ID reparto che l'ID dipendente per distribuire la registrazione in più partizioni.

#### Considerazioni e problemi  

Prima di decidere come implementare questo modello, considerare quanto segue:

-	La struttura chiave alternativa che evita la creazione di partizioni critiche negli inserimenti supporta in modo efficiente le query effettuate dall'applicazione client?  
-	Il volume delle transazioni previste è indicativo della probabilità che si raggiungano gli obiettivi di scalabilità per una singola partizione e si sia limitati dal servizio di archiviazione?  

#### Quando usare questo modello  

Evitare l'anti-modello prepend/append quando il volume delle transazioni determinerà probabilmente una limitazione da parte del servizio di archiviazione quando si accede a una partizione critica.

#### Modelli correlati e informazioni aggiuntive  

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

-	[Modello per chiave composta](#compound-key-pattern)  
-	[Modello della parte finale del log](#log-tail-pattern)  
-	[Modifica di entità](#modifying-entities)  

### Anti-modello dei dati di log  

In genere è necessario usare il servizio BLOB invece del servizio tabelle per archiviare i dati di log.

#### Contesto e problema  

Un caso di utilizzo comune per i dati di log è il recupero di una selezione di voci di log per un intervallo specifico di data/ora. Ad esempio, per trovare tutti i messaggi di errore e critici registrati dall'applicazione tra le ore 15.04 e le ore 15.06 in una data specifica senza usare la data e l'ora del messaggio del registro per determinare la partizione in cui sono state salvate le entità, verrà creata una partizione critica perché in qualsiasi momento tutte le entità del log condividono lo stesso valore **PartitionKey**. Vedere la sezione [Anti-modello prepend/append](#prepend-append-anti-pattern). Ad esempio, lo schema di entità seguente per un messaggio di log determina una partizione critica perché l'applicazione scrive tutti i messaggi di log nella partizione per la data e l'ora correnti:

![][28]

In questo esempio il valore **RowKey** include la data e l'ora del messaggio di log per garantire che i messaggi di log vengano archiviati in ordine di data/ora e include un ID del messaggio nel caso in cui più messaggi di log condividano la stessa data e la stessa ora.

Un altro approccio prevede l'uso di un valore **PartitionKey** per assicurarsi che l'applicazione scriva i messaggi in un intervallo di partizioni. Ad esempio, se l'origine del messaggio di log consente di distribuire i messaggi in più partizioni, è possibile usare lo schema di entità seguente:

![][29]

Tuttavia, il problema con questo schema risiede nel fatto che per recuperare tutti i messaggi di log per un intervallo di tempo specifico è necessario cercare ogni partizione nella tabella.

#### Soluzione  

Nella sezione precedente è stato preso in esame il problema associato ai tentativi di usare il servizio tabelle per archiviare le voci di log e sono state proposte due progettazioni, entrambe insoddisfacenti. Una soluzione ha determinato una partizione critica che comporta il rischio di prestazioni insufficienti della scrittura dei messaggi di log; l'altra soluzione ha determinato prestazioni insufficienti delle query a causa del requisito di analizzare ogni partizione della tabella per recuperare i messaggi di log per un intervallo di tempo specifico. L'archiviazione BLOB offre una soluzione migliore per questo tipo di scenario ed è in questo modo che Analisi archiviazione di Azure archivia i dati di log raccolti.

Questa sezione illustra come Analisi archiviazione archivia i dati di log nell'archiviazione BLOB per esemplificare questo approccio all'archiviazione dei dati per la quale vengono in genere eseguite query per intervallo.

Analisi archiviazione archivia i messaggi di log in un formato delimitato in più BLOB. Il formato delimitato semplifica l'analisi dei dati nel messaggio di log da parte di un'applicazione client.

Analisi archiviazione usa una convenzione di denominazione per i BLOB che consente di localizzare uno BLOB o più BLOB che contengono i messaggi di log per i quali si sta effettuando la ricerca. Ad esempio, un BLOB denominato "queue/2014/07/31/1800/000001.log" contiene messaggi di log correlati al servizio di accodamento per l'ora che inizia alle 18.00 del 31 luglio 2014. "000001" indica che si tratta del primo file di log per il periodo. Analisi archiviazione registra inoltre i timestamp del primo e dell'ultimo messaggio di log archiviati nel file come parte dei metadati del BLOB. L'API per l'archiviazione BLOB consente di individuare i BLOB in un contenitore in base a un prefisso del nome: per individuare tutti i BLOB contenenti i dati di log della coda per l'ora che inizia alle 18.00, è possibile usare il prefisso "queue/2014/07/31/1800".

Analisi archiviazione esegue il buffer dei messaggi di log e quindi aggiorna periodicamente il BLOB appropriato o ne crea uno nuovo con il batch di voci di log più recente. Ciò riduce il numero di scritture che deve eseguire nel servizio BLOB.

Se si implementa una soluzione simile nella propria applicazione, è necessario considerare come gestire il compromesso tra affidabilità (scrittura di ogni voce di log nell'archiviazione BLOB quando questa si verifica) e il costo e LA scalabilità (buffering degli aggiornamenti dell'applicazione e relativa scrittura nell'archiviazione BLOB in blocchi).

#### Considerazioni e problemi  

Prima di decidere come archiviare i dati di log, considerare quanto segue:

-	Se si crea una progettazione di tabella che consente di evitare potenziali partizioni critiche, è possibile che non si possa accedere ai dati di log in modo efficiente.  
-	Per elaborare i dati di log, spesso un client deve caricare molti record.  
-	Nonostante i dati di log siano spesso strutturati, l'archiviazione BLOB può essere una soluzione migliore.  

### Considerazioni sull'implementazione  

Questa sezione illustra alcune considerazioni da tenere presente quando si implementano i modelli descritti nelle sezioni precedenti. Nella maggior parte di questa sezione vengono usati esempi scritti in C# che usano la libreria client di archiviazione (versione 4.3.0 al momento della stesura di questo documento).

### Recupero di entità  

Come descritto nella sezione [Progettazione per l'esecuzione di query](#design-for-querying), la query più efficiente è una query di tipo punto. Tuttavia, in alcuni scenari potrebbe essere necessario recuperare più entità. Questa sezione descrive alcuni approcci comuni al recupero di entità mediante la libreria client di archiviazione.

#### Esecuzione di una query di tipo punto mediante la libreria client di archiviazione  

Il modo più semplice per eseguire una query di tipo punto consiste nell'usare l'operazione di tabella **Retrieve** come illustrato nel frammento di codice C# seguente che recupera un'entità con un **PartitionKey** di valore "Sales" e un **RowKey** di valore "212":

	TableOperation retrieveOperation =
		TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
	var retrieveResult = employeeTable.Execute(retrieveOperation);
	if (retrieveResult.Result != null)
	{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
	}  

Si noti come in questo esempio l'entità recuperata prevista sia di tipo **EmployeeEntity**.

#### Recupero di più entità usando LINQ  

È possibile recuperare più entità usando LINQ con la libreria client di archiviazione e specificando una query con una clausola **where**. Per evitare un'analisi di tabella, è consigliabile includere sempre il valore **PartitionKey** nella clausola where e, se possibile, il valore **RowKey** per evitare analisi di tabelle e partizioni. Il servizio tabelle supporta un set limitato di operatori di confronto (maggiore di, maggiore di o uguale a, minore di, minore di o uguale a e non uguale a) da usare per determinare la clausola where. Il frammento di codice C# seguente consente di trovare tutti i dipendenti il cui cognome inizia con la lettera "B" (presupponendo che il valore **RowKey** archivi il cognome) del reparto vendite (supponendo che il valore **PartitionKey** archivi il nome del reparto):

	TableQuery<EmployeeEntity> employeeQuery =
  			employeeTable.CreateQuery<EmployeeEntity>();
	var query = (from employee in employeeQuery
                where employee.PartitionKey == "Sales" &&
                employee.RowKey.CompareTo("B") >= 0 &&
                employee.RowKey.CompareTo("C") < 0
                select employee).AsTableQuery();
	var employees = query.Execute();  

Si noti come la query specifichi sia un valore **RowKey** sia un valore **PartitionKey** per garantire prestazioni migliori.

L'esempio di codice seguente illustra la funzionalità equivalente usando l'API fluent (per altre informazioni sulle API fluent in generale, vedere l'articolo relativo alle [procedure consigliate per la progettazione di un’API fluent](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):

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


>[AZURE.NOTE] L'esempio annida più metodi **CombineFilters** per includere le tre condizioni di filtro.

#### Recupero di un numero elevato di entità da una query  

Una query ottimale restituisce una singola entità in base a un valore **PartitionKey** e a un valore **RowKey**. In alcuni scenari, tuttavia, potrebbe essere presente il requisito di restituire molte entità dalla stessa partizione o anche da più partizioni.

È sempre necessario eseguire test completi delle prestazioni dell'applicazione in tali scenari.

Una query sul servizio tabelle può restituire un massimo di 1.000 entità contemporaneamente e può essere eseguita per un massimo di cinque secondi. Se il set di risultati contiene più di 1.000 entità, nel caso in cui la query non venga completata entro cinque secondi, o se la query supera il limite della partizione, il servizio tabelle restituisce un token di continuazione per consentire all'applicazione client di richiedere il successivo set di entità. Per altre informazioni sul funzionamento dei token di continuazione, vedere [Timeout e paginazione delle query](http://msdn.microsoft.com/library/azure/dd135718.aspx).

La libreria client di archiviazione può gestire automaticamente i token di continuazione per l'utente mentre restituisce entità dal servizio tabelle. L'esempio di codice C# seguente che usa la libreria client di archiviazione gestisce automaticamente i token di continuazione se il servizio tabelle li restituisce in una risposta:

	string filter = TableQuery.GenerateFilterCondition(
  		"PartitionKey", QueryComparisons.Equal, "Sales");
	TableQuery<EmployeeEntity> employeeQuery =
  		new TableQuery<EmployeeEntity>().Where(filter);

	var employees = employeeTable.ExecuteQuery(employeeQuery);
	foreach (var emp in employees)
	{
  		...
	}  

Il codice C# seguente gestisce i token di continuazione in modo esplicito:

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

Usando i token di continuazione in modo esplicito è possibile controllare quando l'applicazione recupera il successivo segmento di dati. Ad esempio, se l'applicazione client consente agli utenti di spostarsi tra le entità archiviate in una tabella, un utente può decidere di non spostarsi tra tutte le entità recuperate dalla query in modo che l'applicazione usi solo un token di continuazione per recuperare il segmento successivo quando l'utente ha terminato il paging di tutte le entità nel segmento corrente. Questo approccio offre diversi vantaggi:

-	Consente di limitare la quantità di dati da recuperare dal servizio tabelle e da spostare tramite la rete.  
-	Consente di eseguire operazioni di I/O asincrone in .NET.  
-	Consente di serializzare il token di continuazione in un archivio permanente in modo da poter proseguire in caso di arresto anomalo dell'applicazione.  

>[AZURE.NOTE] Un token di continuazione in genere restituisce un segmento contenente al massimo 1.000 entità. Ciò avviene anche se si limita il numero di voci restituite da una query usando **Take** per restituire le prime n entità che corrispondono ai criteri di ricerca: il servizio tabelle può restituire un segmento contenente meno di n entità con un token di continuazione per consentire il recupero delle entità rimanenti.

Il codice C# seguente illustra come modificare il numero di entità restituite all'interno di un segmento:

	employeeQuery.TakeCount = 50;  

#### Proiezione lato server  

Una singola entità può avere fino a 255 proprietà e dimensioni fino a 1 MB. Quando si eseguono query sulla tabella e si recuperano entità, potrebbero non essere necessarie tutte le proprietà ed è possibile evitare di trasferire dati inutilmente (in modo da ridurre la latenza e i costi). È possibile usare la proiezione lato server per trasferire solo le proprietà necessarie. L'esempio seguente recupera solo la proprietà **Email** (insieme a **PartitionKey**, **RowKey**, **Timestamp** ed **ETag**) dalle entità selezionate dalla query.

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

Si noti come il valore **RowKey** è disponibile anche se non è stato incluso nell'elenco delle proprietà da recuperare.

### Modifica di entità  

La libreria client di archiviazione consente di modificare le entità archiviate nel servizio tabelle mediante l'inserimento, l'eliminazione e l'aggiornamento di entità. È possibile usare le transazioni EGT per eseguire in batch più operazioni di inserimento, aggiornamento ed eliminazione insieme allo scopo di ridurre il numero di round trip necessari e migliorare le prestazioni della soluzione.

Si noti che le eccezioni generate quando la libreria client di archiviazione esegue una transazione EGT in genere includono l'indice dell'entità che ha causato l'esito negativo del batch. Ciò è utile quando si esegue il debug di codice che usa le transazioni EGT.

È inoltre opportuno considerare l'influenza della progettazione sul modo in cui l'applicazione gestisce le operazioni di concorrenza e aggiornamento.

#### Gestione della concorrenza  

Per impostazione predefinita, il servizio tabelle implementa controlli di concorrenza ottimistica a livello di singole entità per le operazioni **Insert**, **Merge** e **Delete**, sebbene sia possibile per un client forzare il servizio tabelle in modo da ignorare questi controlli. Per altre informazioni sulla gestione della concorrenza nel servizio tabelle, vedere [Gestione della concorrenza nell'archiviazione di Microsoft Azure](storage-concurrency.md).

#### Unione o sostituzione  

Il metodo **Replace** della classe **TableOperation** sostituisce sempre l'entità completa nel servizio tabelle. Se non si include una proprietà nella richiesta quando tale proprietà è presente nell'entità archiviata, la richiesta rimuove la proprietà dall'entità archiviata. A meno che non si voglia rimuovere una proprietà in modo esplicito da un'entità archiviata, è necessario includere ogni proprietà nella richiesta.

È possibile usare il metodo **Merge** della classe **TableOperation** per ridurre la quantità di dati inviati al servizio tabelle quando si vuole aggiornare un'entità. Il metodo **Merge** sostituisce le eventuali proprietà nell'entità archiviata con i valori di proprietà dell'entità inclusa nella richiesta, ma lascia invariate le proprietà nell'entità archiviata che non sono incluse nella richiesta. Ciò è utile se si dispone di entità di grandi dimensioni e si desidera solo aggiornare un numero limitato di proprietà in una richiesta.

>[AZURE.NOTE] I metodi **Replace** e **Merge** non riescono se l'entità non esiste. In alternativa, se l'entità non esiste, è possibile usare i metodi **InsertOrReplace** e **InsertOrMerge** per creare una nuova entità.

### Uso di tipi di entità eterogenei  

Il servizio tabelle è un archivio di tabelle *senza schema*. Ciò significa che una singola tabella può archiviare le entità di più tipi, offrendo una grande flessibilità di progettazione. L'esempio seguente illustra una tabella che archivia entità dipendente ed entità reparto:

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

Si noti che ogni entità deve disporre comunque dei valori **PartitionKey**, **RowKey** e **Timestamp**, ma può avere qualsiasi set di proprietà. Inoltre, non esiste alcuna indicazione relativa al tipo di un'entità, a meno che non si scelga di memorizzare le informazioni in una posizione. Esistono due opzioni per identificare il tipo di entità:

-	Anteporre il tipo di entità per il valore **RowKey** (o eventualmente il valore **PartitionKey**). Ad esempio, **EMPLOYEE\_000123** o **DEPARTMENT\_SALES** come valori **RowKey**.  
-	Usare una proprietà separata per registrare il tipo di entità come illustrato nella tabella seguente.  

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
<td>Department</td>
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

La prima opzione che precede l'entità per il valore **RowKey** è utile se sussiste la possibilità che due entità di tipi diversi abbiano lo stesso valore di chiave. Inoltre, raggruppa entità dello stesso tipo insieme nella partizione.

Le tecniche descritte in questa sezione sono particolarmente rilevanti per la discussione sulle [Relazioni di ereditarietà](#inheritance-relationships) trattata all'inizio di questa Guida nella sezione [Modellazione di relazioni](#modelling-relationships).

>[AZURE.NOTE] È necessario considerare l'inclusione di un numero di versione nel valore del tipo di entità per consentire alle applicazioni client di sviluppare oggetti POCO e usare versioni diverse.

La restante parte di questa sezione descrive alcune delle funzionalità della libreria client di archiviazione che semplificano l'uso di più tipi di entità nella stessa tabella.

#### Recupero di tipi di entità eterogenei  

Se si usa la libreria client di archiviazione, sono disponibili tre opzioni per l'uso di più tipi di entità.

Se si conosce il tipo di entità archiviata con uno specifico valore **RowKey** e **PartitionKey**, è possibile specificare il tipo di entità quando si recupera l'entità, come illustrato nei due esempi precedenti dove viene eseguito il recupero di entità di tipo **EmployeeEntity**: [Esecuzione di una query di tipo punto usando la libreria client di archiviazione](#executing-a-point-query-using-the-storage-client-library) e [Recupero di più entità usando LINQ](#retrieving-multiple-entities-using-linq).

La seconda opzione prevede l'uso del tipo **DynamicTableEntity** (un contenitore di proprietà) anziché un tipo di entità POCO concreto (questa opzione può anche migliorare le prestazioni perché non richiede la serializzazione e la deserializzazione dell'entità nei tipi .NET). Il codice C# seguente può recuperare più entità di tipo diverso dalla tabella, ma restituisce tutte le entità come istanze **DynamicTableEntity**. Usa quindi la proprietà **EntityType** per determinare il tipo di ogni entità:

	string filter = 	TableQuery.CombineFilters(
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

Si noti che per recuperare le altre proprietà è necessario usare il metodo **TryGetValue** sulla proprietà **Properties** della classe **DynamicTableEntity**.

Una terza opzione prevede l'uso combinato del tipo **DynamicTableEntity** e di un'istanza **EntityResolver**. Ciò consente di risolvere a più tipi POCO nella stessa query. In questo esempio il delegato **EntityResolver** delegate usa la proprietà **EntityType** per distinguere i due tipi di entità restituite dalla query. Il metodo **Resolve** usa il delegato **resolver** per risolvere le istanze **DynamicTableEntity** alle istanze **TableEntity**.

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

#### Modifica dei tipi di entità eterogenei  

Per eliminare un'entità non è necessario conoscerne il tipo, che è comunque sempre noto quando l'entità viene inserita. Tuttavia, è possibile usare il tipo **DynamicTableEntity** per aggiornare un'entità senza conoscerne il tipo e senza usare una classe di entità POCO. L'esempio di codice seguente consente di recuperare una singola entità e controlla che la proprietà **EmployeeCount** esista prima di aggiornarla.

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

### Controllo dell'accesso con le firme di accesso condiviso  

È possibile usare i token delle firme di accesso condiviso (SAS) per consentire alle applicazioni client di modificare le entità di tabella, ed eseguire query sulle stesse, direttamente senza la necessità di eseguire l'autenticazione direttamente con il servizio tabelle. In genere, l'uso di SAS nell'applicazione comporta tre vantaggi principali:

-	Non è necessario distribuire la chiave dell'account di archiviazione in una piattaforma non sicura (ad esempio un dispositivo mobile) per consentire a tale dispositivo di accedere e modificare le entità nel servizio tabelle.  
-	È possibile scaricare una parte del lavoro eseguito dai ruoli Web e di lavoro nella gestione delle entità per i dispositivi client, ad esempio computer e dispositivi mobili degli utenti finali.  
-	È possibile assegnare un set vincolato e limitato nel tempo di autorizzazioni a un client (ad esempio, l'accesso di sola lettura a risorse specifiche).  

Per altre informazioni sull'uso di token di firme di accesso condiviso con il servizio tabelle, vedere [Firme di accesso condiviso, parte 1: Informazioni sul modello di firma di accesso condiviso](../storage-dotnet-shared-access-signature-part-1/).

Tuttavia, è comunque necessario generare i token delle firme di accesso condiviso che consentono a un'applicazione client di accedere alle entità nel servizio tabelle: questa operazione deve essere eseguita in un ambiente che dispone di un accesso sicuro alle chiavi dell'account di archiviazione. In genere, è possibile usare un ruolo Web o di lavoro per generare i token delle firme di accesso condiviso e distribuirli alle applicazioni client che richiedono l'accesso alle entità. Poiché la generazione e la distribuzione dei token delle firme di accesso condiviso ai client comportano comunque un sovraccarico, è consigliabile valutare il modo migliore di ridurre tale sovraccarico, soprattutto in scenari con volumi elevati.

È possibile generare un token delle firme di accesso condiviso che concede l'accesso a un sottoinsieme delle entità in una tabella. Per impostazione predefinita, un token delle firme di accesso condiviso viene creato per un'intera tabella, ma è anche possibile specificare che il token delle firme di accesso condiviso conceda l'accesso a un intervallo di valori **PartitionKey** o a un intervallo di valori **PartitionKey** e **RowKey**. Si potrebbe scegliere di generare token delle firme di accesso condiviso per i singoli utenti del sistema in modo che il token delle firme di accesso condiviso di ogni utente consenta di accedere solo alle proprie entità nel servizio tabelle.

### Operazioni asincrone e parallele  

A condizione che le richieste vengano distribuite in più partizioni, è possibile migliorare la velocità effettiva e la velocità di risposta del client usando le query parallele o asincrone. Ad esempio, si potrebbero avere due o più istanze del ruolo di lavoro che accedono alle tabelle in parallelo. Si potrebbero avere singoli ruoli di lavoro responsabili di specifici set di partizioni o semplicemente avere più istanze del ruolo di lavoro, ciascuna in grado di accedere a tutte le partizioni in una tabella.

All'interno di un'istanza del client, è possibile migliorare la velocità effettiva effettuando operazioni di archiviazione in modo asincrono. La libreria client di archiviazione semplifica la scrittura di query e modifiche asincrone. Ad esempio, è possibile iniziare con il metodo sincrono che recupera tutte le entità in una partizione come mostrato nel codice C# seguente:

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

È possibile modificare facilmente questo codice affinché la query venga eseguita in modo asincrono come segue:

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

In questo esempio asincrono è possibile visualizzare le modifiche seguenti dalla versione sincrona:

-	La firma del metodo include ora il modificatore **async** e restituisce un'istanza **Task**.  
-	Invece di chiamare il metodo **ExecuteSegmented** per recuperare i risultati, il metodo ora chiama il metodo **ExecuteSegmentedAsync** e usa il modificatore **await** per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare questo metodo più volte (con valori diversi per il parametro **department**) e ogni query verrà eseguita su un thread separato.

Si noti che non è presente alcuna versione asincrona del metodo **Execute** nella classe **TableQuery** perché l'interfaccia **IEnumerable** non supporta l'enumerazione asincrona.

È inoltre possibile inserire, aggiornare ed eliminare entità in modo asincrono. Nell'esempio C# seguente viene illustrato un metodo semplice e sincrono per inserire o sostituire un'entità dipendente:

	private static void SimpleEmployeeUpsert(CloudTable employeeTable,
  		EmployeeEntity employee)
	{
  		TableResult result = employeeTable
    		.Execute(TableOperation.InsertOrReplace(employee));
  		Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
	}  

È possibile modificare facilmente questo codice affinché l'aggiornamento venga eseguito in modo asincrono come segue:

	private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
  		EmployeeEntity employee)
	{
  		TableResult result = await employeeTable
    		.ExecuteAsync(TableOperation.InsertOrReplace(employee));
  		Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
	}  

In questo esempio asincrono è possibile visualizzare le modifiche seguenti dalla versione sincrona:

-	La firma del metodo include ora il modificatore **async** e restituisce un'istanza **Task**.  
-	Invece di chiamare il metodo **Execute** per aggiornare l'entità, il metodo ora chiama il metodo **ExecuteAsync** e usa il modificatore **await** per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare più metodi asincroni come questo e ogni chiamata al metodo verrà eseguita su un thread separato.


### Crediti
Vorremmo ringraziare i seguenti membri del team di Azure per il loro contributo: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah, Serdar Ozler e Tom Hollander di Microsoft DX.

I nostri ringraziamenti vanno inoltre ai Microsoft MVP seguenti per i preziosi commenti forniti durante i cicli di revisione: Igor Papirov e Edward Bakker.



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
 

<!---HONumber=AcomDC_0504_2016-->