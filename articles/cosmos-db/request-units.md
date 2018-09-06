---
title: 'Unità richiesta e stima della velocità effettiva: Azure Cosmos DB | Microsoft Docs'
description: Informazioni su come comprendere, specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rimman
ms.openlocfilehash: 66beeb2cc724f75d17a4c155f1cdb888153e8fbf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286766"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è il database multimodello distribuito a livello globale di Microsoft. Con Azure Cosmos DB non è necessario affittare macchine virtuali, distribuire software o monitorare database. Azure Cosmos DB è gestito e monitorato costantemente dai migliori tecnici Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di elevata qualità. È possibile accedere ai dati tramite le API di propria scelta, come le API [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md) e [tabella](table-introduction.md) e il grafo tramite l'[API Gremlin](graph-introduction.md). Tutte le API sono supportate in modo nativo. 

La valuta di Azure Cosmos DB è costituita dall'*unità richiesta (UR)*. Con le unità richiesta, non è necessario riservare capacità di lettura/scrittura né effettuare il provisioning di CPU, memoria e operazioni di I/O al secondo. Azure Cosmos DB supporta varie API che dispongono di operazioni diverse, che vanno dalla semplice lettura e scrittura alle query per grafi più complesse. Poiché non tutte le richieste sono uguali, viene loro assegnata una quantità normalizzata di unità richiesta in base alla quantità di calcolo necessaria per servire la richiesta. Il numero di unità richiesta per un'operazione è deterministico. È possibile tenere traccia del numero di unità richiesta usate da qualsiasi operazione nel database di Azure Cosmos tramite un'intestazione di risposta. 

Per prestazioni prevedibili, riservare una velocità effettiva in unità di 100 UR/secondo. È possibile [eseguire una stima delle esigenze di velocità effettiva](request-units.md#estimating-throughput-needs) usando il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner) di Azure Cosmos DB.

![Calcolatore della velocità effettiva][5]

Alla fine della lettura, si avranno le risposte alle domande seguenti:

* Cosa sono le unità richiesta e gli addebiti richiesta in Azure Cosmos DB?
* Come è possibile specificare la capacità delle unità richiesta per un contenitore o un set di contenitori in Azure Cosmos DB?
* Come si possono stimare le esigenze relative alle unità richiesta per l'applicazione?
* Cosa succede se si supera la capacità delle unità richiesta per un contenitore o un set di contenitori in Azure Cosmos DB?

Poiché Azure Cosmos DB è un database multimodello, è importante tenere presente che questo articolo è applicabile a tutti i modelli di dati e alle API di Azure Cosmos DB. Questo articolo usa termini generici quali *contenitore* per fare riferimento in modo generico a una raccolta o un grafico e *elemento* per fare riferimento in modo generico a una tabella, documento, nodo o entità.

## <a name="request-units-and-request-charges"></a>Unità richiesta e addebiti richiesta

Azure Cosmos DB offre prestazioni veloci e prevedibili, riservando risorse per soddisfare le esigenze a livello di velocità effettiva dell'applicazione. Il carico dell'applicazione e i modelli di accesso cambiano nel tempo. Azure Cosmos DB consente di aumentare o diminuire agevolmente la quantità di velocità effettiva disponibile per l'applicazione.

Con Azure Cosmos DB, la velocità effettiva riservata è specificata in termini di unità richiesta elaborata al secondo. Le unità richiesta possono essere considerate come valuta effettiva. Si riservano diverse unità richiesta garantite perché siano disponibili al secondo per l'applicazione. Ogni operazione in Azure Cosmos DB, tra cui scrittura di un documento, esecuzione di una query e aggiornamento di un documento, usa CPU, memoria e operazioni di I/O al secondo. In altre parole, ogni operazione comporta un addebito richiesta espresso in unità richiesta. Quando si riconoscono i fattori che influiscono sugli addebiti delle unità richiesta e i requisiti di velocità effettiva dell'applicazione, è possibile eseguire l'applicazione nel modo più economicamente conveniente possibile. 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolamento della velocità effettiva nei database distribuiti a livello globale

Se si replica il database in più di un'area, Azure Cosmos DB implementa l'isolamento della velocità effettiva per assicurare che l'uso delle unità richiesta in un'area non abbia ripercussioni sull'uso delle unità richiesta in un'altra area. Ad esempio, se si eseguono un'operazione di scrittura di dati in un'area e un'operazione di lettura di dati in un'altra area, le unità richiesta usate per eseguire l'operazione di scrittura nell'area A non vengono detratte dalle unità richiesta usate per l'operazione di lettura eseguita nell'area B. Le unità richiesta non vengono suddivise tra le aree in cui è stata eseguita la distribuzione del database. Ogni area in cui il database viene replicato dispone di tutte le unità richiesta per le quali è stato eseguito il provisioning. Per altre informazioni sulla replica globale, vedere [Come distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta
Quando si stima il numero di unità richiesta di cui eseguire il provisioning, è importante considerare le variabili seguenti:

* **Dimensioni dell'elemento**. Con l'aumento delle dimensioni, aumenta anche il numero di unità richiesta usate per leggere o scrivere i dati.
* **Numero di proprietà dell'elemento**. Supponendo l'indicizzazione predefinita di tutte le proprietà, le unità usate per scrivere un documento, un nodo o un'entità aumentano man mano che aumenta il numero delle proprietà.
* **Coerenza dei dati**. Se si usano modelli di coerenza dei dati come Assoluta o Decadimento ristretto, verranno usate unità richiesta aggiuntive per leggere gli elementi.
* **Proprietà indicizzate**. I criteri di indicizzazione in ogni contenitore determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità richiesta per operazioni di scrittura limitando il numero di proprietà indicizzate o abilitando l'indicizzazione differita.
* **Indicizzazione del documento**. Per impostazione predefinita, ogni elemento viene automaticamente indicizzato. Se si sceglie di non indicizzare alcuni elementi, è possibile usare un numero inferiore di unità richiesta.
* **Modelli di query**. La complessità di una query influisce sulla quantità di unità richiesta usate per un'operazione. Il numero di risultati di query, il numero di predicati, la natura dei predicati, il numero di funzioni definite dall'utente, le dimensioni dei dati di origine e le proiezioni sono tutti fattori che incidono sul costo delle operazioni di query.
* **Utilizzo di script**. Come le query, le stored procedure e i trigger utilizzano le unità richiesta in base alla complessità delle operazioni da eseguire. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.

## <a name="estimating-throughput-needs"></a>Stima delle esigenze di velocità effettiva
Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Una singola unità richiesta rappresenta la capacità di elaborazione necessaria per leggere, tramite collegamento automatico o ID, un singolo elemento da 1 KB costituito da 10 valori di proprietà univoci, escluse le proprietà di sistema. Una richiesta di creazione (inserimento), sostituzione o eliminazione dello stesso elemento userà più capacità di elaborazione del servizio e quindi richiederà più unità richiesta. 

> [!NOTE]
> La base di 1 unità richiesta per un elemento da 1 KB corrisponde a una semplice operazione GET tramite collegamento automatico o ID dell'elemento.
> 
> 

La tabella riportata di seguito mostra il numero di unità richiesta di cui effettuare il provisioning per elementi con tre dimensioni diverse, ovvero 1 KB, 4 KB e 64 KB, e due livelli di prestazioni diversi, ovvero 500 letture al secondo + 100 scritture al secondo e 500 letture al secondo + 500 scritture al secondo. In questo esempio, la coerenza dei dati è impostata come **Sessione** e i criteri di indicizzazione sono impostati su **Nessuno**.

| Dimensioni dell'elemento | Letture al secondo | Scritture al secondo | Unità richiesta
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1.000 UR/sec
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3.000 UR/sec
| 4 KB | 500 | 100 | (500 * 1,3) + (100 * 7) = 1.350 UR/sec
| 4 KB | 500 | 500 | (500 * 1,3) + (500 * 7) = 4.150 UR/sec
| 64 KB | 500 | 100 | (500 * 10) + (100 * 48) = 9.800 UR/sec
| 64 KB | 500 | 500 | (500 * 10) + (500 * 48) = 29.000 UR/sec


### <a name="use-the-request-unit-calculator"></a>Usare il calcolatore di unità richiesta
Per ottimizzare le stime della velocità effettiva, è possibile usare una [calcolatrice di unità richiesta](https://www.documentdb.com/capacityplanner) basata sul web. La calcolatrice può aiutare a stimare i requisiti dell'unità richiesta per operazioni tipiche, tra cui:

* Creazione di elementi (scrittura)
* Lettura di elementi
* Eliminazione di elementi
* Aggiornamento di elementi

Lo strumento include anche il supporto per la stima delle esigenze di archiviazione dei dati in base agli elementi di esempio specificati.

Per usare lo strumento:

1. Caricare uno o più elementi rappresentativi, ad esempio un documento JSON.
   
    ![Caricare elementi nel calcolatore di unità richiesta][2]
2. Per stimare i requisiti di archiviazione, immettere il numero totale di elementi (ad esempio, documenti, righe o vertici) che si prevede di archiviare.
3. Immettere il numero di operazioni di creazione, lettura, aggiornamento ed eliminazione necessarie (al secondo). Per stimare gli addebiti delle unità richiesta per le operazioni di aggiornamento di elementi, caricare una copia dell'elemento di esempio usato nel passaggio 1 che include aggiornamenti di campi tipici. Ad esempio, se gli aggiornamenti di elementi modificano in genere due proprietà denominate *lastLogin* e *userVisits*, copiare un elemento di esempio, aggiornare i valori per queste due proprietà e successivamente caricare l'elemento copiato.
   
    ![Immettere i requisiti relativi alla velocità effettiva nel calcolatore di unità richiesta][3]
4. Selezionare **Calcola**, quindi esaminare i risultati.
   
    ![Risultati del calcolatore di unità richiesta][4]

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, caricare un campione di ogni *tipo* di elemento tipico nello strumento e quindi calcolare i risultati.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Usare l'intestazione della risposta di addebito della richiesta di Azure Cosmos DB
Ogni risposta dal servizio Azure Cosmos DB include un'intestazione personalizzata (`x-ms-request-charge`) che contiene le unità richiesta usate per una determinata richiesta. È possibile accedere a questa intestazione anche tramite gli SDK di Azure Cosmos DB. In .NET SDK, **RequestCharge** è una proprietà dell'oggetto **ResourceResponse**. Per quanto riguarda le query, Esplora dati di Azure Cosmos DB disponibile nel portale di Azure offre informazioni sugli addebiti per le richieste relative alle query eseguite. Per informazioni su come ottenere e impostare la velocità effettiva usando diverse API multimodello, vedere l'articolo [Impostare e ottenere la velocità effettiva per i contenitori di Azure Cosmos DB](set-throughput.md).

Un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un elemento rappresentativo usato dall'applicazione. Quindi, stimare il numero di operazioni che si prevede di eseguire ogni secondo. Assicurarsi di misurare e includere anche le query tipiche e l'utilizzo di script di Azure Cosmos DB.

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di elemento tipico.
> 
> 

Ad esempio, di seguito vengono riportate le operazioni che è possibile eseguire:

1. Registrare l'addebito delle unità richiesta di creazione (inserimento) di un elemento tipico. 
2. Registrare l'addebito delle unità richiesta di lettura di un elemento tipico.
3. Registrare l'addebito delle unità richiesta di aggiornamento di un elemento tipico.
4. Registrare l'addebito delle unità richiesta di query tipiche su elementi comuni.
5. Registrare l'addebito delle unità richiesta di tutti gli script personalizzati (stored procedure, trigger, funzioni definite dall'utente) usati dall'applicazione.
6. Calcolare le unità richiesta necessarie in base al numero stimato di operazioni che si prevede di eseguire al secondo.

## <a name="a-request-unit-estimate-example"></a>Esempio di stima delle unità richiesta
Si consideri il seguente documento, delle dimensioni approssimative di 1 kB:

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
> I documenti sono minimizzati in Azure Cosmos DB, quindi le dimensioni del documento precedente calcolate dal sistema sono leggermente inferiori a 1 kB.
> 
> 

La tabella seguente mostra gli addebiti dell'unità richiesta approssimativi per le operazioni tipiche su questo elemento. (L'addebito di unità richiesta approssimativo presuppone che il livello di coerenza account sia impostato su **Sessione** e che tutti gli elementi siano indicizzati automaticamente.)

| Operazione | Addebito delle unità richiesta |
| --- | --- |
| Creare elemento |~15 unità richiesta |
| Leggere l'elemento |~1 unità richiesta |
| Eseguire query sull'elemento in base all'ID |~2,5 unità richiesta |

La tabella seguente mostra gli addebiti approssimativi delle unità richiesta per le query tipiche usate nell'applicazione:

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

Con queste informazioni è possibile stimare i requisiti relativi alle unità richiesta per questa applicazione, dato il numero di operazioni e query previste al secondo:

| Operazione/query | Numero stimato al secondo | Unità richiesta desiderate |
| --- | --- | --- |
| Creare elemento |10 |150 |
| Leggere l'elemento |100 |100 |
| Selezionare alimenti in base al produttore |25 |175 |
| Selezionare per gruppo di alimenti |10 |700 |
| Selezionare i primi 10 |15 |Totale 150 |

Si prevede in questo caso un requisito di velocità effettiva medio di 1.275 unità richiesta al secondo. Arrotondando a 100 unità più vicine, si dovrà effettuare il provisioning di 1300 unità richiesta al secondo per il contenitore dell'applicazione o per un set di contenitori.

## <a id="RequestRateTooLarge"></a> Superamento dei limiti della velocità effettiva riservata in Azure Cosmos DB
L'utilizzo di unità richiesta viene valutato con una tariffa al secondo. Per le applicazioni che superano la frequenza di unità richiesta con provisioning, la frequenza delle richieste viene limitata fino al ritorno della frequenza sotto il livello di velocità effettiva con provisioning. Quando la frequenza di una richiesta è limitata, il server la chiude in modo preventivo con `RequestRateTooLargeException` (codice di stato HTTP 429) e restituisce l'`x-ms-retry-after-ms` intestazione. L'intestazione indica il tempo, in millisecondi, che l'utente deve attendere prima di riprovare la richiesta.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se si usano le query .NET Client SDK e LINQ, la maggior parte delle volte non è necessario gestire questa eccezione. La versione corrente del Software Development Kit .NET Client cattura implicitamente questa risposta, rispetta l'intestazione retry-after specificata dal server e riprova automaticamente la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se più client operano collettivamente al di sopra della frequenza delle richieste, il comportamento di ripetizione dei tentativi predefinito potrebbe essere insufficiente e il client genererà una `DocumentClientException` con codice di stato 429 per l'applicazione. In casi come questo, si può valutare la possibilità di gestire la logica e il comportamento di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione o di aumentare la velocità effettiva di cui è stato eseguito il provisioning per il contenitore o per il set di contenitori.

## <a name="next-steps"></a>Passaggi successivi
 
- Informazioni su come [impostare e ottenere velocità effettiva in Azure Cosmos DB](set-throughput.md) tramite il portale di Azure e gli SDK.
- Informazioni su [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
- Per altre informazioni sulla velocità effettiva riservata con i database Azure Cosmos DB, vedere [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [Partizionamento dei dati in Azure Cosmos DB](partition-data.md).
- Per altre informazioni su Azure Cosmos DB, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png

