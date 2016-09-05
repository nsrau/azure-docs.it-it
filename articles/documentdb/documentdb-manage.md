<properties
	pageTitle="Archiviazione e prestazioni in DocumentDB | Microsoft Azure" 
	description="Informazioni sull'archiviazione dei dati e dei documenti in DocumentDB e sulla scalabilità di DocumentDB per soddisfare le esigenze di capacità dell'applicazione." 
	keywords="archiviazione documenti"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2016" 
	ms.author="mimig"/>  

# Informazioni sull'archiviazione e sul provisioning con prestazioni stimabili in DocumentDB
Azure DocumentDB è un servizio database NoSQL orientato ai documenti, scalabile e completamente gestito per i documenti in formato JSON. Grazie a DocumentDB, non sarà necessario affittare macchine virtuali, distribuire software o monitorare database. DocumentDB è gestito e monitorato costantemente da tecnici Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di elevata qualità.

È possibile iniziare a usare DocumentDB [creando un account database](documentdb-create-account.md) e un [database DocumentDB](documentdb-create-database.md) tramite il [portale di Azure](https://portal.azure.com/). I database DocumentDB sono offerti in unità di risorse di archiviazione e velocità effettiva basate su unità SSD. Il provisioning di queste unità di archiviazione viene effettuato [creando raccolte di database](documentdb-create-collection.md) nell'account database, ogni raccolta con una velocità effettiva riservata che può essere aumentata o ridotta in qualsiasi momento per soddisfare le esigenze dell'applicazione.

Se l'applicazione supera la velocità effettiva riservata per una o più raccolte, le richieste saranno limitate in base a ogni raccolta. Ciò significa che alcune richieste di applicazione possono avere esito positivo mentre altre possono essere limitate.

Questo articolo fornisce una panoramica delle risorse e delle metriche disponibili per gestire la capacità e pianificare l'archiviazione di dati.

## Account di database
I sottoscrittori di Azure possono effettuare il provisioning di uno o più account database di DocumentDB per gestire le risorse di database. Ogni sottoscrizione è associata a una singola sottoscrizione di Azure.

Gli account DocumentDB possono essere creati tramite il [portale di Azure](documentdb-create-account.md) oppure usando [un modello di Azure Resource Manager o l'interfaccia della riga di comando di Azure](documentdb-automation-resource-manager-cli.md).

## Database
Un singolo database di DocumentDB può includere risorse di archiviazione documenti praticamente illimitate, raggruppate in raccolte. Le raccolte garantiscono l'isolamento delle prestazioni. È possibile effettuare il provisioning di ogni raccolta con una velocità effettiva non condivisa con le altre raccolte nello stesso database o account. I database di DocumentDB hanno dimensioni flessibili e possono includere da pochi gigabyte a diversi terabyte di archiviazione documenti e velocità effettiva con provisioning basate su unità SSD. A differenza del database RDBMS tradizionale, l'ambito di un database in DocumentDB non è limitato a un unico computer e può estendersi a più computer o cluster.

Con DocumentDB, in caso di esigenza di scalabilità delle applicazioni, sarà possibile creare più raccolte o più database o entrambi. I database possono essere creati tramite il [portale di Azure](documentdb-create-database.md) o con uno dei [DocumentDB SDK](documentdb-dotnet-samples.md).

## Raccolte di database
Ogni database di DocumentDB può includere una o più raccolte. Le raccolte fungono da partizioni di dati a disponibilità elevata per l'elaborazione e archiviazione dei documenti. Ogni raccolta è in grado di archiviare documenti con schemi eterogenei. Le funzionalità di indicizzazione automatica e query di DocumentDB consentono di filtrare e recuperare documenti facilmente. Una raccolta fornisce l'ambito per l'archiviazione documenti e l'esecuzione di query. Una raccolta costituisce anche un dominio di transazione per tutti i documenti inclusi nella raccolta stessa. La velocità effettiva delle raccolte viene allocata in base al valore impostato nel portale di Azure o tramite l'SDK.

Le raccolte vengono partizionate automaticamente in uno o più server fisici da DocumentDB. Quando si crea una raccolta, è possibile specificare la velocità effettiva con provisioning in termini di unità richiesta al secondo e una proprietà chiave di partizione. Il valore di questa proprietà viene usato da DocumentDB per distribuire i documenti tra le partizioni e indirizzare le richieste come query. Il valore della chiave di partizione funge anche da limite della transazione per le stored procedure e i trigger. Ogni raccolta ha una quantità di velocità effettiva riservata che non viene condivisa con altre raccolte nello stesso account. È quindi possibile aumentare il numero di istanze dell'applicazione sia in termini di archiviazione che di velocità effettiva.

Le raccolte possono essere create tramite il [portale di Azure](documentdb-create-collection.md) o con uno dei [DocumentDB SDK](documentdb-sdk-dotnet.md).
 
## Unità di richiesta e operazioni di database

Quando si crea una raccolta, la velocità effettiva viene riservata in termini di [unità richiesta (UR)](documentdb-request-units.md) al secondo. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'**unità richiesta** come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione. Un'operazione di lettura di un documento di 1 KB utilizza 1 UR indipendentemente dal numero di elementi archiviati nella raccolta o dal numero di richieste simultanee in esecuzione contemporaneamente. Tutte le richieste a DocumentDB, incluse operazioni complesse come le query SQL, hanno un valore UR stimabile che può essere determinato in fase di sviluppo. Se si conoscono le dimensioni dei documenti e la frequenza di ogni operazione di lettura, scrittura e query per il supporto dell'applicazione, è possibile effettuare il provisioning dell'esatta quantità di velocità effettiva necessaria per rispondere alle esigenze dell'applicazione e aumentare o ridurre le prestazioni del database secondo necessità.

La velocità effettiva per ogni raccolta può essere riservata in blocchi di 100 unità richiesta al secondo, da poche centinaia a milioni di unità richiesta al secondo. È possibile regolare la velocità effettiva con provisioning per tutta la durata di una raccolta, per adattarla alle diverse esigenze di elaborazione e ai modelli di accesso dell'applicazione. Per altre informazioni, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

>[AZURE.IMPORTANT] Le raccolte sono entità fatturabili. Il costo è determinato dalla velocità effettiva con provisioning della raccolta, misurata in unità richiesta al secondo, e dallo spazio di archiviazione totale utilizzato, misurato in gigabyte.

Come prevedere quante unità richiesta utilizzerà una determinata operazione di inserimento o eliminazione oppure l'esecuzione di una query o una stored procedure? Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Un'operazione di lettura di un documento di 1 KB corrisponde a 1 UR, ma una richiesta di inserimento, sostituzione o eliminazione dello stesso documento userà più capacità di elaborazione del servizio e quindi più unità richiesta. Ogni risposta dal servizio include un'intestazione personalizzata, `x-ms-request-charge`, in cui sono riportate le unità richiesta usate per la richiesta. Questa intestazione è accessibile anche tramite gli [SDK](documentdb-sdk-dotnet.md). In .NET SDK, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) è una proprietà dell'oggetto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx). Per stimare le esigenze di velocità effettiva prima di eseguire una singola chiamata, è possibile usare [Capacity Planner](documentdb-request-units.md#estimating-throughput-needs).

>[AZURE.NOTE] La baseline di 1 unità richiesta per un documento da 1 KB corrisponde a una semplice operazione GET del documento con la [coerenza di sessione](documentdb-consistency-levels.md).

Ci sono diversi fattori che influiscono sulle unità di richiesta utilizzate per un'operazione in un account di database DocumentDB. Questi fattori includono:

- Dimensioni del documento. Con l'aumento delle dimensioni del documento aumentano anche le unità utilizzate per leggere o scrivere i dati.
- Numero delle proprietà. presupponendo l'indicizzazione predefinita di tutte le proprietà, le unità utilizzate per scrivere un documento aumenteranno con l'aumento del numero delle proprietà.
- Coerenza dei dati. se si usano i livelli di coerenza dei dati Assoluta o Obsolescenza associata, verranno utilizzate ulteriori unità per leggere i documenti.
- Proprietà indicizzate. I criteri di indicizzazione in ogni raccolta determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità di richiesta limitando il numero di proprietà indicizzate.
- Indicizzazione del documento. Per impostazione predefinita ogni documento viene indicizzato automaticamente, ma se si sceglie di non indicizzare alcuni documenti si utilizzeranno meno unità di richiesta.

Per altre informazioni, vedere [Unità richiesta in DocumentDB](documentdb-request-units.md).

La tabella riportata di seguito mostra il numero di unità richiesta di cui effettuare il provisioning per tre dimensioni di documento diverse, ovvero 1 KB, 4 KB e 64 KB, e due livelli di prestazioni diversi, ovvero 500 letture al secondo + 100 scritture al secondo e 500 letture al secondo + 500 scritture al secondo. La coerenza dei dati è stata configurata come Sessione e i criteri di indicizzazione sono stati impostati su Nessuno.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Dimensioni del documento</strong></p></td>
            <td valign="top"><p><strong>Letture al secondo</strong></p></td>
            <td valign="top"><p><strong>Scritture al secondo</strong></p></td>
            <td valign="top"><p><strong>Unità richiesta</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1.000 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3.000 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1.350 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4.150 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9.800 UR/sec</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29.000 UR/sec</p></td>
        </tr>
    </tbody>
</table>

Query, stored procedure e trigger utilizzeranno le unità di richiesta in base alla complessità delle operazioni eseguite. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.


## Scelta del livello di coerenza e velocità effettiva
La scelta del livello di coerenza predefinita influisce sulla velocità effettiva e sulla latenza. È possibile impostare il livello di coerenza predefinito sia a livello di codice che tramite il portale di Azure. È anche possibile eseguire l'override del livello di coerenza per singole richieste. Per impostazione predefinita, il livello di coerenza è impostato su **Sessione**, che fornisce letture/scritture costanti e legge le garanzie di scrittura. La coerenza delle sessioni è ideale per applicazioni incentrate sugli utenti e offre un compromesso ideale tra coerenza e prestazioni.

Per istruzioni sulla modifica del livello di coerenza nel portale di Azure, vedere [Come gestire un account DocumentDB](documentdb-manage-account.md#consistency). In alternativa, per altre informazioni sui livelli di coerenza, vedere [Uso dei livelli di coerenza](documentdb-consistency-levels.md).

## Archiviazione documenti con provisioning e sovraccarico dell'indice
DocumentDB supporta la creazione di raccolte a partizione singola e raccolte partizionate. Ogni partizione in DocumentDB supporta fino a 10 GB di spazio di archiviazione basato su SSD. I 10 GB di archiviazione documenti includono i documenti e l'archiviazione per l'indice. Per impostazione predefinita, una raccolta DocumentDB è configurata per l'indicizzazione automatica di tutti i documenti, senza richiedere esplicitamente indici o schemi secondari. In base alle applicazioni che utilizzano DocumentDB, il sovraccarico tipico dell’indice è compreso tra 2-20%. La tecnologia di indicizzazione usata da DocumentDB assicura che, indipendentemente dai valori delle proprietà, il sovraccarico dell'indice non supererà l'80% delle dimensioni dei documenti con impostazioni predefinite.

Per impostazione predefinita, tutti i documenti sono indicizzati automaticamente da DocumentDB. Se tuttavia si vuole ottimizzare il sovraccarico dell'indice, è possibile scegliere di rimuovere determinati documenti dall'indicizzazione al momento dell'inserimento o della sostituzione di un documento, come descritto in [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md). È possibile configurare una raccolta di DocumentDB in modo da escludere dall'indicizzazione tutti i documenti nella raccolta. È anche possibile configurare una raccolta DocumentDB in modo che indicizzi in modo selettivo solo determinate proprietà o alcuni percorsi con caratteri jolly dei documenti JSON, come descritto in [Personalizzazione dei criteri di indicizzazione di una raccolta](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). L'esclusione di proprietà o documenti migliora anche la velocità effettiva di scrittura, permettendo quindi di usare meno unità di richiesta.

## Passaggi successivi

Per altre informazioni sul funzionamento di DocumentDB, vedere [Partizionamento e scalabilità in Azure DocumentDB](documentdb-partition-data.md).

Per istruzioni su come monitorare i livelli di prestazioni nel portale di Azure, vedere [Monitorare le richieste, l'uso e l'archiviazione di DocumentDB](documentdb-monitor-accounts.md). Per altre informazioni sulla scelta dei livelli di prestazioni delle raccolte, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).
 

<!---HONumber=AcomDC_0824_2016-->