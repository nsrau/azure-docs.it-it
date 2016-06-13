<properties 
	pageTitle="Unità richiesta in DocumentDB | Microsoft Azure" 
	description="Informazioni su come comprendere, specificare e stimare i requisiti relativi alle unità richiesta in DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

#Unità richiesta in DocumentDB
Ora disponibile: [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner) di DocumentDB. Per altre informazioni, vedere [Stima delle esigenze di velocità effettiva](documentdb-request-units.md#estimating-throughput-needs).

![Calcolatore della velocità effettiva][5]

##Introduzione
Questo articolo fornisce una panoramica delle unità richiesta in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

-	Cosa sono le unità richiesta e gli addebiti richiesta?
-	Come è possibile specificare la capacità delle unità richiesta per una raccolta?
-	Come si possono stimare le esigenze relative alle unità richiesta per l'applicazione?
-	Cosa accade se si supera la capacità delle unità richiesta per una raccolta?


##Unità richiesta e addebiti richiesta
DocumentDB offre prestazioni veloci e prevedibili, *riservando* risorse per soddisfare le esigenze a livello di velocità effettiva dell'applicazione. Poiché i modelli di carico e accesso dell'applicazione cambiano nel tempo, DocumentDB consente di aumentare o diminuire facilmente la quantità di velocità effettiva riservata disponibile per l'applicazione.

Con DocumentDB la velocità effettiva riservata è specificata in termini di elaborazione di unità richiesta al secondo. Si possono considerare le unità richiesta come una specie di valuta della velocità effettiva, secondo cui si *riserva* una quantità garantita di unità richiesta disponibili al secondo per l'applicazione. Ogni operazione in DocumentDB, ovvero scrittura di un documento, esecuzione di una query, aggiornamento di un documento, utilizza CPU, memoria e operazioni di I/O al secondo. In altre parole, ogni operazione comporta un *addebito richiesta* espresso in *unità richiesta*. La conoscenza dei fattori che influiscono sugli addebiti delle unità richiesta, insieme ai requisiti di velocità effettiva dell'applicazione, consente di eseguire l'applicazione nel modo più economicamente conveniente possibile.

##Specifica della capacità delle unità richiesta
Quando si crea una raccolta di DocumentDB, si specifica il numero di unità richiesta al secondo da riservare per la raccolta. Dopo aver creato la raccolta, l'intera allocazione di unità richiesta specificata viene riservata all'uso da parte della raccolta. A ogni raccolta è garantita la disponibilità di caratteristiche di velocità effettiva dedicate e isolate.

È importante notare che DocumentDB opera sulla base di un modello di prenotazione. Vale a dire che viene fatturata la quantità di velocità effettiva *riservata* per la raccolta, indipendentemente dalla quantità di tale velocità effettiva *usata* attivamente. Tenere tuttavia presente che man mano che i modelli di carico, dati e utilizzo dell'applicazione cambiano, è possibile aumentare e ridurre facilmente la quantità di unità richiesta riservate usando gli SDK di DocumentDB o il [portale di Azure](https://portal.azure.com). Per altre informazioni sull'aumento o sulla riduzione della velocità effettiva, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

##Considerazioni sulle unità richiesta
Quando si stima il numero di unità richiesta da riservare per la raccolta di DocumentDB, è importante considerare le variabili seguenti:

- **Dimensioni del documento**. Con l'aumento delle dimensioni del documento aumentano anche le unità utilizzate per leggere o scrivere i dati.
- **Numero di proprietà del documento**. presupponendo l'indicizzazione predefinita di tutte le proprietà, le unità utilizzate per scrivere un documento aumenteranno con l'aumento del numero delle proprietà.
- **Coerenza dei dati**. se si usano i livelli di coerenza dei dati Assoluta o Obsolescenza associata, verranno utilizzate ulteriori unità per leggere i documenti.
- **Proprietà indicizzate**. I criteri di indicizzazione in ogni raccolta determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità richiesta limitando il numero di proprietà indicizzate o abilitando l'indicizzazione differita.
- **Indicizzazione del documento**. Per impostazione predefinita ogni documento viene indicizzato automaticamente, ma se si sceglie di non indicizzare alcuni documenti si utilizzeranno meno unità di richiesta.
- **Modelli di query**. La complessità di una query influisce sulla quantità di unità richiesta utilizzate per un'operazione. Il numero di predicati, la natura dei predicati, le proiezioni, il numero di funzioni definite dall'utente e le dimensioni del set di dati di origine sono tutti fattori che incidono sul costo delle operazioni di query.
- **Utilizzo di script**. Come le query, le stored procedure e i trigger utilizzano le unità richiesta in base alla complessità delle operazioni da eseguire. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.

##Stima delle esigenze di velocità effettiva
Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Una singola unità richiesta rappresenta la capacità di elaborazione necessaria per leggere, tramite collegamento automatico o ID, un singolo documento JSON da 1 KB costituito da 10 valori di proprietà univoci, escluse le proprietà di sistema. Una richiesta di creazione (inserimento), sostituzione o eliminazione dello stesso documento utilizzerà più capacità di elaborazione del servizio e quindi più unità richiesta.

> [AZURE.NOTE] La base di 1 unità richiesta per un documento da 1 KB corrisponde a una semplice operazione GET tramite collegamento automatico o ID del documento.

###Usare il calcolatore di unità richiesta
Per semplificare l'ottimizzazione delle stime di velocità effettiva da parte dei clienti, è disponibile un [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner), che consente di stimare i requisiti relativi alle unità richiesta per operazioni tipiche, incluse le seguenti:

- Creazioni di documenti (scritture)
- Letture di documenti
- Eliminazioni di documenti

L'uso dello strumento è molto semplice:

1. Caricare uno o più documenti JSON rappresentativi.

	![Caricare documenti nel calcolatore di unità richiesta][2]

2. Immettere il numero di operazioni di creazione, lettura ed eliminazione dei documenti necessarie (al secondo).

	![Immettere i requisiti relativi alla velocità effettiva nel calcolatore di unità richiesta][3]

3. Fare clic su Calcola ed esaminare i risultati.

	![Risultati del calcolatore di unità richiesta][4]

>[AZURE.NOTE]Se sono presenti tipi di documenti che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, caricare un campione di ogni *tipo* di documento tipico nello strumento e quindi calcolare i risultati.

###Usare l'intestazione della risposta di addebito della richiesta di DocumentDB
Ogni risposta dal servizio DocumentDB include un'intestazione personalizzata (x-ms-request-charge) che contiene le unità richiesta utilizzate per la richiesta. Questa intestazione è accessibile anche tramite gli SDK di DocumentDB. In .NET SDK, RequestCharge è una proprietà dell'oggetto ResourceResponse. Per quanto riguarda le query, Esplora Query di DocumentDB nel portale di Azure fornisce informazioni sull'addebito richiesta per le query eseguite.

![Analisi degli addebiti delle unità richiesta in Esplora Query][1]

Con questa premessa, un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un documento rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo. Assicurarsi di misurare e includere anche le query tipiche e l'utilizzo di script di DocumentDB.

>[AZURE.NOTE]Se sono presenti tipi di documenti che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di documento tipico.

Ad esempio:

1. Registrare l'addebito delle unità richiesta di creazione (inserimento) di un documento tipico. 
2. Registrare l'addebito delle unità richiesta di lettura di un documento tipico.
3. Registrare l'addebito delle unità richiesta di aggiornamento di un documento tipico.
3. Registrare l'addebito delle unità richiesta di query tipiche su documenti comuni.
4. Registrare l'addebito delle unità richiesta di tutti gli script personalizzati (stored procedure, trigger, funzioni definite dall'utente) utilizzati dall'applicazione.
5. Calcolare le unità richiesta necessarie in base al numero stimato di operazioni che si prevede di eseguire al secondo.

##Esempio di stima delle unità richiesta
Considerare il documento da ~1 KB seguente:

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

>[AZURE.NOTE]I documenti sono minimizzati in DocumentDB, quindi le dimensioni del documento precedente calcolate dal sistema sono leggermente inferiori a 1 KB.


La tabella seguente mostra gli addebiti delle unità richiesta approssimativi per le operazioni tipiche su questo documento. L'addebito delle unità richiesta approssimativo presuppone che il livello di coerenza dell'account sia impostato su "Sessione" e che tutti i documenti siano indicizzati automaticamente:

Operazione|Addebito delle unità richiesta 
---|---
Creare un documento|~15 unità richiesta 
Leggere un documento|~1 unità richiesta
Eseguire query su un documento in base all'ID|~2,5 unità richiesta

Questa tabella mostra anche gli addebiti approssimativi delle unità richiesta per le query tipiche usate nell'applicazione:

Query|Addebito delle unità richiesta|Numero di documenti restituiti
---|---|--- 
Selezionare alimenti in base all'ID|~2,5 unità richiesta|1 
Selezionare alimenti in base al produttore|~7 unità richiesta|7
Selezionare per gruppo di alimenti e ordinare in base al peso|~70 unità richiesta|100
Selezionare i primi 10 alimenti in un gruppo di alimenti|~10 unità richiesta|10

>[AZURE.NOTE]Gli addebiti delle unità richiesta variano in base al numero di documenti restituiti.

Con queste informazioni è possibile stimare i requisiti relativi alle unità richiesta per questa applicazione, dato il numero di operazioni e le query previste al secondo:

Operazione/query|Numero stimato al secondo|Unità richiesta necessarie 
---|---|--- 
Creare un documento|10|150 
Leggere un documento|100|100 
Selezionare alimenti in base al produttore|25|175 
Selezionare per gruppo di alimenti|10|700 
Selezionare i primi 10|15|Totale 150|155|1275

In questo caso, è previsto un requisito di velocità effettiva medio di 1.275 unità richiesta/secondo. Arrotondando alle 100 più vicine, si dovrà effettuare il provisioning di 1.300 unità richiesta/secondo per la raccolta dell'applicazione.

##Superamento dei limiti della velocità effettiva riservata
Tenere presente che il consumo delle unità richiesta è valutato in base a una frequenza al secondo. Per le applicazioni che superano il livello di unità di richiesta con provisioning per una raccolta, le richieste a tale raccolta saranno limitate fino al ritorno del livello sotto il valore riservato. Nel caso di una limitazione, il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione x-ms-retry-after-ms, che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta.

	HTTP Status 429
	Status Line: RequestRateTooLarge
	x-ms-retry-after-ms :100

Se si usano le query LINQ e .NET SDK per client, non è quasi mai necessario gestire questa eccezione, perché la versione corrente di .NET SDK per client rileva la risposta in modo implicito, rispetta l'intestazione retry-after specificata dal server e ripete la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo riuscirà.

Se più client operano collettivamente al di sopra della frequenza delle richieste, il comportamento di ripetizione dei tentativi predefinito potrebbe non essere sufficiente e il client genererà una DocumentClientException con codice di stato 429 per l'applicazione. In casi come questo, ai può valutare la possibilità di gestire la logica e il comportamento di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione o di aumentare la velocità effettiva riservata per la raccolta.

##Passaggi successivi

Per altre informazioni sulla velocità effettiva riservata con Azure DocumentDB, vedere queste risorse:
 
- [Prezzi di DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestire la capacità e le prestazioni di DocumentDB](documentdb-manage.md) 
- [Modellazione dei dati in DocumentDB](documentdb-modeling-data.md)
- [Livelli di prestazioni in DocumentDB](documentdb-partition-data.md)

Per altre informazioni su DocumentDB, vedere la [documentazione](https://azure.microsoft.com/documentation/services/documentdb/) relativa ad Azure DocumentDB.

Per informazioni sulle attività iniziali relative al test delle prestazioni e della scalabilità con DocumentDB, vedere [Test delle prestazioni e della scalabilità con Azure DocumentDB](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png

<!---HONumber=AcomDC_0601_2016-->