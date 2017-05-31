---
title: "Unità richiesta e stima della velocità effettiva: Azure Cosmos DB | Microsoft Docs"
description: "Informazioni su come comprendere, specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB."
services: cosmosdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: Azure Cosmos DB
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: syamk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: af039d210ca1eb59bc7ffcc81fc01aa9ccd3640f
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB
Ora disponibile: [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner) di Azure Cosmos DB. Per altre informazioni, vedere [Stima delle esigenze di velocità effettiva](documentdb-request-units.md#estimating-throughput-needs).

![Calcolatore della velocità effettiva][5]

## <a name="introduction"></a>Introduzione
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è il database multimodello distribuito a livello globale di Microsoft. Con Azure Cosmos DB non è necessario affittare macchine virtuali, distribuire software o monitorare database. Azure Cosmos DB è gestito e monitorato costantemente dai migliori tecnici Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di elevata qualità. Permette di accedere ai dati usando le API preferite, perché supporta in modalità nativa [SQL di DocumentDB](https://docs.microsoft.com/azure/documentdb/documentdb-sql-query) (documenti), MongoDB (documenti), [Archiviazione tabelle di Azure](https://azure.microsoft.com/services/storage/tables/) (chiave-valore) e [Gremlin](https://tinkerpop.apache.org/gremlin.html) (grafi). La valuta di Azure Cosmos DB è costituita dalle unità richiesta (UR). Con le unità richiesta, non è necessario riservare capacità di lettura o scrittura né effettuare il provisioning di CPU, memoria e operazioni di I/O al secondo.

Azure Cosmos DB supporta una serie di API con operazioni diverse, che vanno dalla semplice lettura e scrittura alle query per grafi più complesse. Poiché non tutte le richieste sono uguali, viene loro assegnata una quantità normalizzata di **unità richiesta** in base alla quantità di calcolo necessaria per servire la richiesta. Il numero di unità richiesta per un'operazione è deterministico ed è possibile tenere traccia del numero di unità richiesta utilizzate da qualsiasi operazione in Azure Cosmos DB tramite un'intestazione della risposta. 

Per prestazioni prevedibili, è necessario riservare una velocità effettiva per unità di 100 UR al secondo. Per ogni blocco da 100 UR/sec è possibile collegare un blocco da 1.000 UR/min. Unire il provisioning al secondo e quello al minuto permette di evitare la necessità di effettuare il provisioning per picco e di risparmiare fino al 75% dei costi rispetto a qualsiasi servizio basato unicamente sul provisioning al secondo.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:  

* Cosa sono le unità richiesta e gli addebiti richiesta?
* Come è possibile specificare la capacità delle unità richiesta per una raccolta?
* Come si possono stimare le esigenze relative alle unità richiesta per l'applicazione?
* Cosa accade se si supera la capacità delle unità richiesta per una raccolta?

Azure Cosmos DB è un database multimodello. Si noti che in questo articolo l'API di documento è detta raccolta/documento, l'API Graph è detta grafo/nodo e l'API di tabella è detta tabella/entità. Per la velocità effettiva nell'articolo vengono usati i concetti generali di contenitore/elemento.

## <a name="request-units-and-request-charges"></a>Unità richiesta e addebiti richiesta
Azure Cosmos DB offre prestazioni veloci e prevedibili, *riservando* risorse per soddisfare le esigenze a livello di velocità effettiva dell'applicazione.  Poiché i modelli di carico e accesso dell'applicazione cambiano nel tempo, Azure Cosmos DB consente di aumentare o diminuire facilmente la quantità di velocità effettiva riservata disponibile per l'applicazione.

Con Azure Cosmos DB, la velocità effettiva riservata è specificata in termini di unità richiesta elaborate al secondo o al minuto (componente aggiuntivo).  Si possono considerare le unità richiesta come una specie di valuta della velocità effettiva, secondo cui si *riserva* una quantità garantita di unità richiesta disponibili al secondo o al minuto per l'applicazione.  Ogni operazione in Azure Cosmos DB, ovvero scrittura di un documento, esecuzione di una query, aggiornamento di un documento, utilizza CPU, memoria e operazioni di I/O al secondo.  In altre parole, ogni operazione comporta un *addebito richiesta* espresso in *unità richiesta*.  La conoscenza dei fattori che influiscono sugli addebiti delle unità richiesta, insieme ai requisiti di velocità effettiva dell'applicazione, consente di eseguire l'applicazione nel modo più economicamente conveniente possibile. Anche lo strumento Esplora query è molto utile per testare gli elementi di base di una query.

Per iniziare è consigliabile guardare il video riportato di seguito, in cui Aravind Ramachandran illustra le unità richiesta e le prestazioni prevedibili con Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Specifica della capacità in unità richiesta in Azure Cosmos DB
Quando si crea una nuova raccolta, tabella o grafo, si specifica il numero di unità richiesta al secondo (UR/sec) da riservare. È anche possibile decidere se abilitare le unità richiesta al minuto, che permettono di decuplicare la capacità che è possibile ottenere al secondo, ma al minuto. In base alla velocità effettiva di cui è stato effettuato il provisioning, Azure Cosmos DB alloca partizioni fisiche per ospitare la raccolta e suddivide/ribilancia la crescita dei dati nelle partizioni.

In Azure Cosmos DB è necessario specificare una chiave di partizione quando viene effettuato il provisioning di una raccolta con almeno 2.500 unità richiesta. La chiave di partizione è necessaria anche per ridimensionare la velocità effettiva della raccolta oltre le 2.500 unità richiesta in futuro. È quindi consigliabile configurare una [chiave di partizione](documentdb-partition-data.md) durante la creazione di un contenitore, indipendentemente dalla velocità effettiva iniziale. Dato che potrebbe essere necessario suddividere i dati in più partizioni, occorre scegliere una chiave di partizione con una cardinalità elevata, da centinaia a milioni di valori distinti, in modo che Azure Cosmos DB possa ridimensionare la raccolta, la tabella o il grafo e le richieste in modo uniforme. 

> [!NOTE]
> Una chiave di partizione è un limite logico, non un limite fisico. Non è quindi necessario limitare il numero di valori distinti per le chiavi di partizioni. È in effetti consigliabile avere un numero maggiore di valori distinti per le chiavi di partizione, perché Azure Cosmos DB offre un numero maggiore di opzioni per il bilanciamento del carico.

Ecco un frammento di codice per la creazione di una raccolta con 3.000 unità richiesta al secondo usando .NET SDK:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure Cosmos DB usa un modello di prenotazione per la velocità effettiva, ovvero viene fatturata la quantità di velocità effettiva *riservata*, indipendentemente dalla quantità di tale velocità effettiva *usata* attivamente. A mano a mano che i modelli di carico, dati e utilizzo dell'applicazione cambiano, è possibile aumentare e ridurre facilmente la quantità di unità richiesta riservate usando gli SDK o il [portale di Azure](https://portal.azure.com).

Viene eseguito il mapping di ogni raccolta, tabella o grafo a una risorsa `Offer` in Azure Cosmos DB, che include metadati sulla velocità effettiva di cui è stato effettuato il provisioning. È possibile modificare la velocità effettiva allocata esaminando l'offerta corrispondente relativa alla risorsa per un contenitore, quindi aggiornarla con il nuovo valore per la velocità effettiva. Ecco un frammento di codice per la modifica della velocità effettiva di una raccolta fino a 5.000 unità richiesta al secondo usando .NET SDK:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

La modifica della velocità effettiva non influisce sulla disponibilità del contenitore. La nuova velocità effettiva riservata viene in genere applicata entro pochi secondi, in corrispondenza dell'applicazione della nuova velocità effettiva.

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta
Quando si stima il numero di unità richiesta da riservare per il contenitore di Azure Cosmos DB, è importante considerare le variabili seguenti:

* **Dimensioni dell'elemento**. Con l'aumento delle dimensioni aumentano anche le unità utilizzate per leggere o scrivere i dati.
* **Numero di proprietà dell'elemento**. Presupponendo l'indicizzazione predefinita di tutte le proprietà, le unità utilizzate per scrivere un documento, un nodo o un'entità aumentano con l'aumento del numero delle proprietà.
* **Coerenza dei dati**. Se si usano i livelli di coerenza dei dati Assoluta o Con obsolescenza associata, verranno utilizzate ulteriori unità per leggere gli elementi.
* **Proprietà indicizzate**. I criteri di indicizzazione in ogni contenitore determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità richiesta limitando il numero di proprietà indicizzate o abilitando l'indicizzazione differita.
* **Indicizzazione del documento**. Per impostazione predefinita ogni elemento viene indicizzato automaticamente, ma se si sceglie di non indicizzare alcuni elementi si utilizzeranno meno unità di richiesta.
* **Modelli di query**. La complessità di una query influisce sulla quantità di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, le proiezioni, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.
* **Utilizzo di script**.  Come le query, le stored procedure e i trigger utilizzano le unità richiesta in base alla complessità delle operazioni da eseguire. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.

## <a name="estimating-throughput-needs"></a>Stima delle esigenze di velocità effettiva
Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Una singola unità richiesta rappresenta la capacità di elaborazione necessaria per leggere, tramite collegamento automatico o ID, un singolo elemento da 1 KB costituito da 10 valori di proprietà univoci, escluse le proprietà di sistema. Una richiesta di creazione (inserimento), sostituzione o eliminazione dello stesso elemento utilizzerà più capacità di elaborazione del servizio e quindi più unità richiesta.   

> [!NOTE]
> La base di 1 unità richiesta per un elemento da 1 KB corrisponde a una semplice operazione GET tramite collegamento automatico o ID dell'elemento.
> 
> 

La tabella riportata di seguito mostra il numero di unità richiesta di cui effettuare il provisioning per tre dimensioni dell'elemento diverse, ovvero 1 KB, 4 KB e 64 KB, e due livelli di prestazioni diversi, ovvero 500 letture al secondo + 100 scritture al secondo e 500 letture al secondo + 500 scritture al secondo. La coerenza dei dati è stata configurata come Sessione e i criteri di indicizzazione sono stati impostati su Nessuno.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Dimensioni dell'elemento</strong></p></td>
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
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3.000 UR/sec</p></td>
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

### <a name="use-the-request-unit-calculator"></a>Usare il calcolatore di unità richiesta
Per semplificare l'ottimizzazione delle stime di velocità effettiva da parte dei clienti, è disponibile un [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner) , che consente di stimare i requisiti relativi alle unità richiesta per operazioni tipiche, incluse le seguenti:

* Creazione di elementi (scrittura)
* Lettura di elementi
* Eliminazione di elementi
* Aggiornamento di elementi

Lo strumento include anche il supporto per la stima delle esigenze di archiviazione dei dati in base agli elementi di esempio specificati.

L'uso dello strumento è molto semplice:

1. Caricare uno o più elementi rappresentativi.
   
    ![Caricare elementi nel calcolatore di unità richiesta][2]
2. Per stimare i requisiti di archiviazione, immettere il numero totale di elementi che si prevede di archiviare.
3. Immettere il numero di operazioni di creazione, lettura, aggiornamento ed eliminazione degli elementi necessarie (al secondo). Per stimare gli addebiti delle unità richiesta per le operazioni di aggiornamento di elementi, caricare una copia dell'elemento di esempio usato nel passaggio 1 precedente che include aggiornamenti di campi tipici.  Ad esempio, se gli aggiornamenti di elementi modificano in genere due proprietà denominate lastLogin e userVisits, è sufficiente copiare l'elemento di esempio, aggiornare i valori per queste due proprietà e caricare l'elemento copiato.
   
    ![Immettere i requisiti relativi alla velocità effettiva nel calcolatore di unità richiesta][3]
4. Fare clic su Calcola ed esaminare i risultati.
   
    ![Risultati del calcolatore di unità richiesta][4]

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, caricare un campione di ogni *tipo* di elemento tipico nello strumento e quindi calcolare i risultati.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Usare l'intestazione della risposta di addebito della richiesta di Azure Cosmos DB
Ogni risposta dal servizio Azure Cosmos DB include un'intestazione personalizzata (`x-ms-request-charge`) che contiene le unità richiesta utilizzate per la richiesta. Questa intestazione è accessibile anche tramite gli SDK di DocumentDB. In .NET SDK, RequestCharge è una proprietà dell'oggetto ResourceResponse.  Per quanto riguarda le query, Esplora query di Azure Cosmos DB nel portale di Azure fornisce informazioni sull'addebito per le richieste relative alle query eseguite.

![Analisi degli addebiti delle unità richiesta in Esplora Query][1]

Con questa premessa, un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un elemento rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo.  Assicurarsi di misurare e includere anche le query tipiche e l'utilizzo di script di Azure Cosmos DB.

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di elemento tipico.
> 
> 

ad esempio:

1. Registrare l'addebito delle unità richiesta di creazione (inserimento) di un elemento tipico. 
2. Registrare l'addebito delle unità richiesta di lettura di un elemento tipico.
3. Registrare l'addebito delle unità richiesta di aggiornamento di un elemento tipico.
4. Registrare l'addebito delle unità richiesta di query tipiche su elementi comuni.
5. Registrare l'addebito delle unità richiesta di tutti gli script personalizzati (stored procedure, trigger, funzioni definite dall'utente) utilizzati dall'applicazione.
6. Calcolare le unità richiesta necessarie in base al numero stimato di operazioni che si prevede di eseguire al secondo.

### <a id="GetLastRequestStatistics"></a>Usare il comando dell'API per MongoDB
L'API per MongoDB supporta un comando personalizzato, *getLastRequestStatistics*, per il recupero degli addebiti per le richieste per operazioni specificate.

Ad esempio, in Mongo Shell eseguire l'operazione di cui si vuole verificare l'addebito per le richieste.
```
> db.sample.find()
```

Eseguire quindi il comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Con questa premessa, un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un elemento rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo.

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di elemento tipico.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>Usare le metriche del portale dell'API per MongoDB
Il modo più semplice per ottenere una stima valida degli addebiti per le unità richiesta per il database dell'API per MongoDB consiste nell'usare le metriche del [portale di Azure](https://portal.azure.com). I grafici *Numero di richieste* e *Richiedi addebito* consentono di ottenere una stima del numero di unità richiesta utilizzate da ogni operazione e dal numero di unità richiesta utilizzate una rispetto all'altra.

![Metriche del portale dell'API per MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>Esempio di stima delle unità richiesta
Considerare il documento da ~1 KB seguente:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> I documenti sono minimizzati in Azure Cosmos DB, quindi le dimensioni del documento precedente calcolate dal sistema sono leggermente inferiori a 1 KB.
> 
> 

La tabella seguente mostra gli addebiti delle unità richiesta approssimativi per le operazioni tipiche su questo elemento. L'addebito delle unità richiesta approssimativo presuppone che il livello di coerenza dell'account sia impostato su "Sessione" e che tutti gli elementi siano indicizzati automaticamente:

| Operazione | Addebito delle unità richiesta |
| --- | --- |
| Creare elemento |~15 unità richiesta |
| Leggere l'elemento |~1 unità richiesta |
| Eseguire query sull'elemento in base all'ID |~2,5 unità richiesta |

Questa tabella mostra anche gli addebiti approssimativi delle unità richiesta per le query tipiche usate nell'applicazione:

| Query | Addebito delle unità richiesta | Numero di elementi restituiti |
| --- | --- | --- |
| Selezionare alimenti in base all'ID |~2,5 unità richiesta |1 |
| Selezionare alimenti in base al produttore |~7 unità richiesta |7 |
| Selezionare per gruppo di alimenti e ordinare in base al peso |~70 unità richiesta |100 |
| Selezionare i primi 10 alimenti in un gruppo di alimenti |~10 unità richiesta |10 |

> [!NOTE]
> Gli addebiti delle unità richiesta variano in base al numero di elementi restituiti.
> 
> 

Con queste informazioni è possibile stimare i requisiti relativi alle unità richiesta per questa applicazione, dato il numero di operazioni e le query previste al secondo:

| Operazione/query | Numero stimato al secondo | Unità richiesta necessarie |
| --- | --- | --- |
| Creare elemento |10 |150 |
| Leggere l'elemento |100 |100 |
| Selezionare alimenti in base al produttore |25 |175 |
| Selezionare per gruppo di alimenti |10 |700 |
| Selezionare i primi 10 |15 |Totale 150 |

In questo caso, è previsto un requisito di velocità effettiva medio di 1.275 unità richiesta/secondo.  Arrotondando alle 100 più vicine, si dovrà effettuare il provisioning di 1.300 unità richiesta/secondo per la raccolta dell'applicazione.

## <a id="RequestRateTooLarge"></a> Superamento dei limiti della velocità effettiva riservata in Azure Cosmos DB
Tenere presente che il consumo delle unità richiesta è valutato in base a una frequenza al secondo, se l'uso delle unità richiesta al minuto è disabilitato oppure il budget non lo consente. Per le applicazioni che superano il livello di unità richiesta di cui è stato effettuato il provisioning per un contenitore, le richieste saranno limitate fino al ritorno del livello sotto il valore riservato. Nel caso di una limitazione, il server termina preventivamente la richiesta con RequestRateTooLargeException (codice di stato HTTP 429) e restituisce l'intestazione x-ms-retry-after-ms, che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se si usano le query LINQ e .NET SDK per client, non è quasi mai necessario gestire questa eccezione, perché la versione corrente di .NET SDK per client rileva la risposta in modo implicito, rispetta l'intestazione retry-after specificata dal server e ripete la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se più client operano collettivamente al di sopra della frequenza delle richieste, il comportamento di ripetizione dei tentativi predefinito potrebbe non essere sufficiente e il client genererà una DocumentClientException con codice di stato 429 per l'applicazione. In casi come questo, si può valutare la possibilità di gestire la logica e il comportamento di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione o di aumentare la velocità effettiva riservata per il contenitore.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Superamento dei limiti della velocità effettiva riservata nell'API per MongoDB
Le applicazioni che superano il livello di unità di richiesta con provisioning per una raccolta saranno limitate fino al ritorno del livello sotto il valore riservato. In caso di limitazione, il back-end terminerà preventivamente la richiesta con un codice errore *16500*, ovvero *Troppe richieste*. Per impostazione predefinita, l'API per MongoDB ripeterà automaticamente i tentativi fino a 10 volte prima di restituire un codice errore di tipo *Troppe richieste*. Se si riceve un numero eccessivo di codici errore di tipo *Troppe richieste*, è possibile prendere in considerazione l'aggiunta del comportamento di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione oppure l'[aumento della velocità effettiva riservata per la raccolta](documentdb-set-throughput.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla velocità effettiva riservata con i database Azure Cosmos DB, vedere queste risorse:

* [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Partizionamento dei dati in Azure Cosmos DB](documentdb-partition-data.md)

Per altre informazioni su Azure Cosmos DB, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/services/co/). 

Per iniziare a testare la scalabilità e le prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](documentdb-performance-testing.md).

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
[6]: ./media/documentdb-request-units/api-for-mongodb-metrics.png

