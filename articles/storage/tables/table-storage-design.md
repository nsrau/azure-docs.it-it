---
title: Progettare tabelle scalabili ed efficienti in Archiviazione tabelle di Azure. | Microsoft Docs
description: Progettare tabelle scalabili ed efficienti in Archiviazione tabelle di Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: c5b18bce9d0cf78569d0c2fa02ad14c96ad09bd1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237775"
---
# <a name="design-scalable-and-performant-tables"></a>Progettare tabelle scalabili ad alte prestazioni

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Per progettare tabelle scalabili ed efficienti, è necessario tenere in considerazione fattori come le prestazioni, la scalabilità e il costo. Se in precedenza sono stati progettati schemi per database relazionali, queste considerazioni saranno già state fatte, ma, pur essendoci alcune analogie tra il modello di archiviazione del servizio tabelle di Azure e i modelli relazionali, ci sono anche molte importanti differenze. Queste differenze in genere danno origine a progettazioni diverse che potrebbero sembrare poco plausibili o sbagliate a chi ha familiarità con i database relazionali, ma che invece hanno perfettamente senso se la progettazione è finalizzata a un archivio di chiavi/valori NoSQL, come il servizio tabelle di Azure. Molte differenze di progettazione rispecchiano il fatto che il servizio tabelle è progettato per supportare applicazioni con scalabilità cloud che possono contenere miliardi di entità (dette righe nella terminologia dei database relazionali) di dati o per set di dati che devono supportare volumi di transazioni elevati. È quindi necessario pensare in modo diverso alla modalità di archiviazione dei dati e comprendere il funzionamento del servizio tabelle. Un archivio dati NoSQL ben progettato offre alla soluzione una scalabilità decisamente più elevata e a un costo inferiore rispetto a una soluzione che usa un database relazionale. Questa guida illustra proprio questi argomenti.  

## <a name="about-the-azure-table-service"></a>Informazioni sul servizio tabelle di Azure
Questa sezione evidenzia alcune funzionalità chiave del servizio tabelle, di particolare importanza per la progettazione a livello di prestazioni e scalabilità. Se non si ha familiarità con Archiviazione di Azure e con il servizio tabelle, prima di proseguire con la lettura di questo articolo, vedere [Introduzione ad Archiviazione di Azure](../../storage/common/storage-introduction.md) e [Introduzione all'archiviazione tabelle di Azure con .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md). Anche se l'argomento principale di questa guida è il servizio tabelle, sono incluse alcune informazioni sui servizi di accodamento e BLOB di Azure e su come sia possibile usarli con il servizio tabelle.  

Cos'è il servizio tabelle? Come indica il nome stesso, il servizio tabelle usa un formato tabulare per archiviare i dati. In base alla terminologia standard, ogni riga della tabella rappresenta un'entità le cui diverse proprietà sono archiviate nelle colonne. Ogni entità ha una coppia di chiavi che la identificano in modo univoco e una colonna di tipo timestamp usata dal servizio tabelle per tenere traccia dell'ultimo aggiornamento dell'entità. Il timestamp viene applicato automaticamente e non è possibile sovrascriverlo manualmente con un valore arbitrario. Il servizio tabelle usa il timestamp dell’ultima modifica (LMT, Last Modified Timestamp) per gestire la concorrenza ottimistica.  

> [!NOTE]
> Le operazioni delle API REST del servizio tabelle restituiscono anche un valore **ETag** derivato dal timestamp LMT. In questo documento i termini ETag e LMT vengono usati in modo intercambiabile perché si riferiscono agli stessi dati sottostanti.  
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
<td>Jun</td>
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


Per il momento, i dati sembrano simili a una tabella di un database relazionale. Le principali differenze sono le colonne obbligatorie e la possibilità di archiviare più tipi di entità nella stessa tabella. Inoltre ogni proprietà definita dall'utente, come **FirstName** o **Age** ha un tipo di dati, ad esempio un valore intero o una stringa, proprio come una colonna in un database relazionale. Anche se diversamente da un database relazionale, essendo il servizio tabelle privo di schema, una proprietà non deve avere lo stesso tipo di dati in ogni entità. Per archiviare tipi di dati complessi in una sola proprietà, è necessario usare un formato serializzato come JSON o XML. Per altre informazioni sul servizio tabelle, ad esempio sui tipi di dati supportati, sugli intervalli di date supportate, sulle regole di denominazione e sui limiti di dimensioni, vedere [Understanding the Table Service Data Model](https://msdn.microsoft.com/library/azure/dd179338.aspx) (Informazioni sul modello di dati del servizio tabelle).

La scelta di **PartitionKey** e **RowKey** è fondamentale per la progettazione ottimale di una tabella. Ogni entità archiviata in una tabella deve avere una combinazione univoca di **PartitionKey** e **RowKey**. Come con le chiavi in una tabella di database relazionale, i valori di **PartitionKey** e **RowKey** vengono indicizzati per creare un indice cluster che consenta le ricerche veloci. Tuttavia, il servizio tabelle non crea indici secondari, quindi **PartitionKey** e **RowKey** sono le uniche proprietà indicizzate. Alcuni dei modelli descritti in [Modelli di progettazione tabella](table-storage-design-patterns.md) illustrano come è possibile aggirare questa apparente limitazione.  

Una tabella è costituita da una o più partizioni e molte delle decisioni relative alla progettazione riguarderanno la scelta di un valore appropriato per **PartitionKey** e **RowKey** per poter ottimizzare la soluzione. Una soluzione può essere costituita da una singola tabella contenente tutte le entità organizzate in partizioni, ma normalmente una soluzione comprende più tabelle. Le tabelle permettono di organizzare in modo logico le entità e di gestire l'accesso ai dati con gli elenchi di controllo di accesso. Inoltre è possibile eliminare un'intera tabella con una sola operazione di archiviazione.  

## <a name="table-partitions"></a>Partizioni della tabella
Il nome account, il nome tabella e **PartitionKey** insieme identificano la partizione nel servizio di archiviazione in cui il servizio tabelle archivia l'entità. Oltre a far parte dello schema di indirizzamento per le entità, le partizioni definiscono un ambito per le transazioni (vedere più avanti [Transazioni di gruppi di entità](#entity-group-transactions)) e formano le basi del ridimensionamento del servizio tabelle. Per altre informazioni sulle partizioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../../storage/common/storage-scalability-targets.md).  

Nel servizio tabelle un solo nodo gestisce una o più partizioni complete e il servizio è scalabile grazie al bilanciamento dinamico del carico delle partizioni tra i nodi. Se un nodo è in condizioni di carico, il servizio tabelle può *dividere* in più nodi l'intervallo di partizioni gestite da quel nodo. Quando il traffico diminuisce, il servizio può *unire* nuovamente in un solo nodo gli intervalli di partizioni dai nodi inattivi.  

Per altre informazioni sui dettagli interni del servizio tabelle, in particolare sulla gestione delle partizioni con il servizio tabelle, vedere il documento relativo all’ [Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

## <a name="entity-group-transactions"></a>Transazioni dei gruppi di entità
Nel servizio tabelle, le transazioni di gruppi di entità (EGT, Entity Group Transaction) sono il solo meccanismo predefinito per eseguire aggiornamenti atomici tra più entità. Le transazioni EGT sono chiamate anche *transazioni batch*. Tali transazioni possono essere usate solo con le entità archiviate nella stessa partizione (ovvero che condividono la stessa chiave di partizione in una tabella specifica). Ogni volta che è necessario un comportamento transazionale atomico tra più entità, è quindi necessario assicurarsi che le entità siano nella stessa partizione. Per questo motivo spesso si tengono tipi diversi di entità nella stessa tabella (e partizione) e non si usa una tabella per ogni tipo di entità. Una sola EGT può agire al massimo su 100 entità.  Se si inviano più transazioni EGT simultanee per l'elaborazione, è importante garantire che tali transazioni non vengano applicate a entità che sono comuni tra le transazioni EGT, altrimenti l'elaborazione potrebbe subire ritardi.

Le transazioni EGT richiedono anche la valutazione di un potenziale compromesso nella progettazione. L'uso di un maggior numero di partizioni aumenta infatti la scalabilità dell'applicazione, in quanto Azure ha maggiori opportunità di bilanciamento del carico delle richieste tra i nodi. Ma l'uso di più partizioni potrebbe limitare la capacità dell'applicazione di eseguire transazioni atomiche e mantenere la coerenza assoluta per i dati. Ci sono inoltre specifici obiettivi di scalabilità a livello di partizione, che potrebbero limitare la velocità effettiva delle transazioni prevista per un singolo nodo. Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione di Azure e il servizio tabelle, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Considerazioni sulla capacità
La tabella seguente descrive alcuni valori chiave da tenere presenti quando si progetta una soluzione di servizio tabelle:  

| Capacità totale di un account di archiviazione di Azure | 500 TB |
| --- | --- |
| Numero di tabelle in un account di archiviazione di Azure |Limitato solo dalla capacità dell'account di archiviazione |
| Numero di partizioni in una tabella |Limitato solo dalla capacità dell'account di archiviazione |
| Numero di entità in una partizione |Limitato solo dalla capacità dell'account di archiviazione |
| Dimensioni di una singola entità |Fino a 1 MB con un massimo di 255 proprietà (incluse **PartitionKey**, **RowKey** e **Timestamp**) |
| Dimensioni di **PartitionKey** |Stringa con dimensioni fino a 1 KB. |
| Dimensioni di **RowKey** |Stringa con dimensioni fino a 1 KB. |
| Dimensioni di una transazione di gruppi di entità |Una transazione può includere al massimo 100 entità e le dimensioni del payload devono essere inferiori a 4 MB. Una transazione EGT può aggiornare una sola entità per volta. |

Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Considerazioni sul costo
Anche se l'archiviazione tabelle è relativamente poco costosa, è consigliabile includere le stime dei costi sia per l'utilizzo della capacità che per la quantità di transazioni nella valutazione di una soluzione di servizio tabelle. In molti scenari, tuttavia, l'archiviazione di dati denormalizzati o duplicati per migliorare le prestazioni o la scalabilità della soluzione costituisce un approccio valido. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Passaggi successivi

- [Modelli di progettazione tabella](table-storage-design-patterns.md)
- [Modellazione di relazioni](table-storage-design-modeling.md)
- [Progettazione per le query](table-storage-design-for-query.md)
- [Crittografia dei dati di tabella](table-storage-design-encrypt-data.md)
- [Progettazione per la modifica dei dati](table-storage-design-for-modification.md)