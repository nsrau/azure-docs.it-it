---
title: Ottimizzazione del costo della velocità effettiva in Azure Cosmos DB
description: Questo articolo illustra come ottimizzare i costi della velocità effettiva per i dati archiviati in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 443bf5694515720b1b865c310e70ca9c45add262
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465589"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB

Offrendo il modello di velocità effettiva con provisioning, Azure Cosmos DB garantisce prestazioni prevedibili su qualsiasi scala. Riservando la velocità effettiva o effettuandone il provisioning in anticipo, si elimina l'effetto"noisy neighbor" sulle prestazioni. Se si specifica la quantità esatta di velocità effettiva necessaria, Azure Cosmos DB garantisce la velocità effettiva configurata, supportata dal contratto di servizio.

È possibile iniziare con una velocità effettiva minima di 400 UR/sec e aumentare le prestazioni fino a decine di milioni di richieste al secondo o ancora di più. Ogni richiesta emessa al database o al contenitore di Azure Cosmos, ad esempio una richiesta di lettura, di scrittura o di query oppure stored procedure, ha un costo corrispondente che viene dedotto dalla velocità effettiva con provisioning. Se si effettua il provisioning di 400 UR/sec e si emette una query che costa 40 UR, sarà possibile emettere 10 query di questo tipo al secondo. Eventuali richieste superiori saranno soggette a un limite di velocità e dovranno essere ripetute. Se si usano driver del client, è supportata la logica di ripetizione automatica.

È possibile effettuare il provisioning della velocità effettiva nei database o nei contenitori e ciascuna strategia contribuisce a risparmiare sui costi in base allo scenario.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Ottimizzare attraverso il provisioning della velocità effettiva a diversi livelli

* Se si effettua il provisioning della velocità effettiva in un database, tutti i contenitori, ad esempio insiemi/tabelle/grafici all'interno del database possono condividere la velocità effettiva in base al carico. La velocità effettiva riservata a livello di database viene condivisa in modo non uniforme a seconda del carico di lavoro in un set specifico di contenitori.

* Se si effettua il provisioning della velocità effettiva in un contenitore, è garantita la velocità effettiva per tale contenitore, supportata dal contratto di servizio. La scelta di una chiave di partizione logica è fondamentale per la distribuzione uniforme del carico tra tutte le partizioni logiche di un contenitore. Per altri dettagli, vedere gli articoli su [partizionamento](partitioning-overview.md) e [scalabilità orizzontale](partition-data.md).

Di seguito sono riportate alcune linee guida per scegliere una strategia di velocità effettiva con provisioning:

**Considerare di effettuare il provisioning della velocità effettiva in un database Azure Cosmos DB (contenente un set di contenitori) se**:

1. Si dispone di qualche dozzina di contenitori Azure Cosmos e si desidera condividere la velocità effettiva in tutti o in alcuni di loro. 

2. Si sta eseguendo la migrazione da un database a tenant singolo che è stato progettato per l'esecuzione sulle macchine virtuali ospitate su IaaS o locali, ad esempio database relazionali o NoSQL, in Azure Cosmos DB. E se si dispone di numerosi insiemi/tabelle/grafici e non si desidera apportare modifiche al modello di dati. Si noti che, se durante la migrazione da un database locale non si sta aggiornando il modello di dati, potrebbe essere necessario compromettere alcuni dei vantaggi offerti da Azure Cosmos DB. È consigliabile ripetere sempre l'accesso al modello di dati per ottenere il massimo in termini di prestazioni e anche per ottimizzare i costi. 

3. Si desidera assorbire i picchi non pianificati nei carichi di lavoro grazie alla velocità effettiva in pool a livello di database soggetto a picchi imprevisti nel carico di lavoro. 

4. Anziché impostare la velocità effettiva specifica per i singoli contenitori, si è interessati a ottenere la velocità effettiva aggregata in un set di contenitori all'interno del database.

**Considerare di effettuare il provisioning della velocità effettiva in un singolo contenitore se:**

1. Si dispone di alcuni contenitori Azure Cosmos. Poiché Azure Cosmos DB è indipendente dallo schema, un contenitore può contenere elementi con schemi eterogenei e non richiede ai clienti di creare più tipi di contenitori, uno per ogni entità. È sempre un'opzione da considerare se si raggruppano, ad esempio, 10-20 contenitori separati in un singolo contenitore. Con un minimo di 400 UR per i contenitori, potrebbe essere più conveniente il pooling di tutti i 10-20 contenitori in un singolo contenitore. 

2. Si vuole controllare la velocità effettiva in un contenitore specifico e ottenere la velocità effettiva garantita per un determinato contenitore, supportata dal contratto di servizio.

**Considerare una combinazione delle due strategie illustrate sopra:**

1. Come accennato in precedenza, Azure Cosmos DB consente di combinare le due strategie precedenti, pertanto ora è possibile avere alcuni contenitori nel database Azure Cosmos, che può condividere la velocità effettiva con provisioning nel database, nonché alcuni contenitori all'interno dello stesso database, che possono avere quantità dedicate di velocità effettiva con provisioning. 

2. È possibile applicare le strategie illustrate sopra per produrre una configurazione ibrida, dove entrambi i livelli del database hanno una velocità effettiva con provisioning mentre alcuni contenitori hanno una velocità effettiva dedicata.

Come illustrato nella tabella seguente, a seconda della scelta dell'API, è possibile effettuare il provisioning della velocità effettiva con livelli diversi di granularità.

|API|Per la velocità effettiva **condivisa**, configurare |Per la velocità effettiva **dedicata**, configurare |
|----|----|----|
|API SQL|Database|Contenitore|
|API Azure Cosmos DB per MongoDB|Database|Raccolta|
|API Cassandra|Keyspace|Tabella|
|API Gremlin|Account di database|Grafico|
|API di tabella|Account di database|Tabella|

Con il provisioning della velocità effettiva a livelli diversi, è possibile ottimizzare i costi in base alle caratteristiche del carico di lavoro. Come accennato in precedenza, è possibile aumentare o ridurre la velocità effettiva con provisioning in modo programmatico e in qualsiasi momento per i singoli contenitori o in modo collettivo in un set di contenitori. Il ridimensionamento elastico della velocità effettiva con il cambiare del carico di lavoro consente di pagare solo la velocità effettiva che è stata configurata. Se il contenitore o un set di contenitori viene distribuito tra più aree, la velocità effettiva configurata nel contenitore o in un set di contenitori è garantita come disponibile in tutte le aree.

## <a name="optimize-with-rate-limiting-your-requests"></a>Ottimizzare impostando limiti di velocità per le richieste

Per i carichi di lavoro che non sono sensibili alla latenza, è possibile effettuare il provisioning di una velocità effettiva inferiore e consentire all'applicazione di gestire l'impostazione di limiti quando la velocità effettiva reale supera la velocità effettiva con provisioning. Il server termina preventivamente la richiesta con RequestRateTooLarge (codice di stato HTTP 429) e restituisce l'intestazione `x-ms-retry-after-ms`, che indica la quantità di tempo, in millisecondi, che l'utente deve attendere prima di eseguire di nuovo la richiesta. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logica di ripetizione negli SDK 

Gli SDK nativi (.NET/.NET Core, Java, Node.js e Python) intercettano implicitamente questa risposta, rispettano l'intestazione retry-after specificata dal server e ripetono la richiesta. A meno che all'account non accedano contemporaneamente più client, il tentativo successivo avrà esito positivo.

Se si dispone di più client che operano complessivamente a una velocità costantemente superiore a quella della richiesta, il numero di tentativi predefinito attualmente impostato su 9 potrebbe non essere sufficiente. In tal caso, il client genera `DocumentClientException` con codice di stato 429 per l'applicazione. Il numero predefinito di ripetizioni dei tentativi può essere modificato impostando `RetryOptions` nell'istanza di ConnectionPolicy. Per impostazione predefinita, l'eccezione DocumentClientException con codice di stato 429 viene restituita dopo un tempo di attesa cumulativo di 30 secondi, se la richiesta continua a funzionare al di sopra della frequenza delle richieste. Ciò si verifica anche quando il numero di ripetizioni dei tentativi corrente è inferiore al numero massimo di tentativi, indipendentemente dal fatto che si tratti del valore predefinito 9 o di un valore definito dall'utente. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryAtte) è impostato su 3, quindi in questo caso, se un'operazione di richiesta viene sottoposta a limiti di velocità superando la velocità effettiva riservata per la raccolta, l'operazione viene ripetuta tre volte prima di generare l'eccezione per l'applicazione. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) è impostato su 60, quindi in questo caso, se la ripetizione cumulativa attende un tempo espresso in secondi, dal momento che la prima richiesta supera 60 secondi, viene generata l'eccezione.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategia di partizionamento e costi della velocità effettiva con provisioning

Una buona strategia di partizionamento è importante per ottimizzare i costi in Azure Cosmos DB. Assicurarsi che non ci sia asimmetria nelle partizioni esposte tramite le metriche di archiviazione. Assicurarsi che non ci sia asimmetria nella velocità effettiva per una partizione esposta tramite le metriche di velocità effettiva. Assicurarsi che non ci sia asimmetria per particolari chiavi di partizione. Le chiavi dominanti nello spazio di archiviazione vengono esposte tramite le metriche, tuttavia la chiave dipenderà dal modello di accesso dell'applicazione. È consigliabile riflettere sulla chiave di partizione logica corretta. Una buona chiave di partizione dovrebbe avere le caratteristiche seguenti:

* Scegliere una chiave di partizione che distribuisca il carico di lavoro in modo uniforme tra tutte le partizioni e in modo uniforme nel tempo. In altre parole, non è necessario avere alcune chiavi con la maggioranza dei dati e alcune chiavi con meno o nessun dato. 

* Scegliere una chiave di partizione che consenta la distribuzione uniforme dei modelli di accesso nelle partizioni logiche. Il carico di lavoro è ragionevolmente uniforme in tutte le chiavi. In altre parole, la maggior parte del carico di lavoro non deve essere focalizzata su poche chiavi specifiche. 

* Scegliere una chiave di partizione con una vasta gamma di valori. 

L'idea di base è quella di suddividere i dati e l'attività nel contenitore tra i set di partizioni logiche, in modo che le risorse per l'archiviazione e la velocità effettiva dei dati possano essere distribuite tra le partizioni logiche. I candidati per le chiavi di partizione possono includere le proprietà usate di frequente come filtro nelle query. Le query possono essere indirizzate in modo efficiente includendo la chiave di partizione nel predicato del filtro. Con questa strategia di partizionamento, l'ottimizzazione della velocità effettiva con provisioning risulterà molto più semplice. 

### <a name="design-smaller-items-for-higher-throughput"></a>Progettare elementi più piccoli per una velocità effettiva superiore 

L'addebito per le richieste, ovvero il costo di elaborazione delle richieste, per un'operazione specifica è direttamente correlato alle dimensioni dell'elemento. Le operazioni su elementi di grandi dimensioni costano più delle operazioni su elementi più piccoli. 

## <a name="data-access-patterns"></a>Modelli di accesso ai dati 

È sempre una buona pratica suddividere i dati in categorie logiche basate sulla frequenza con cui si accede ai dati. Con la suddivisione in categorie di dati ad accesso frequente, medio o sporadico, è possibile perfezionare lo spazio di archiviazione utilizzato e la velocità effettiva richiesta. A seconda della frequenza di accesso, è possibile inserire i dati in contenitori separati (ad esempio, tabelle, grafici e insiemi) e perfezionare la velocità effettiva con provisioning per soddisfare le esigenze di quel segmento di dati. 

Inoltre, se si usa Azure Cosmos DB e si sa che non verranno effettuate ricerche per determinati valori di dati oppure vi si accederà raramente, è necessario archiviare i valori compressi di tali attributi. Questo metodo garantisce un risparmio in termini di spazio di archiviazione, spazio degli indici e velocità effettiva con provisioning, inoltre comporta costi inferiori.

## <a name="optimize-by-changing-indexing-policy"></a>Ottimizzare modificando i criteri di indicizzazione 

Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutte le proprietà di ogni record. Ciò consente di semplificare lo sviluppo e garantire prestazioni eccellenti in molti tipi diversi di query ad hoc. Se si dispone di record di grandi dimensioni con migliaia di proprietà, potrebbe non essere utile corrispondere il costo della velocità effettiva per indicizzare ogni proprietà, in particolare se si eseguono query solo in 10 o 20 di queste proprietà. Man mano che ci si avvicina a un carico di lavoro specifico, è consigliabile perfezionare i criteri di indicizzazione. I dettagli completi sui criteri di indicizzazione di Azure Cosmos DB sono disponibili [qui](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitoraggio della velocità effettiva con provisioning e utilizzata 

È possibile monitorare il numero totale di UR con provisioning, il numero di richieste soggette a limite di velocità e il numero di UR utilizzate nel portale di Azure. L'immagine seguente mostra un esempio di metrica di utilizzo:

![Monitorare le unità richieste nel portale di Azure](./media/optimize-cost-throughput/monitoring.png)

È anche possibile impostare avvisi per verificare se il numero di richieste soggette a limite di velocità supera una soglia specifica. Per altri dettagli, vedere l'articolo [Come monitorare Azure Cosmos DB](use-metrics.md). Questi avvisi possono inviare un messaggio di posta elettronica agli amministratori dell'account o chiamare un webhook HTTP personalizzato o una funzione di Azure per aumentare automaticamente la velocità effettiva con provisioning. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Ridimensionare la velocità effettiva in modo elastico e su richiesta 

Poiché si paga la velocità effettiva con provisioning, è possibile combinarla con le proprie esigenze per evitare di incorrere in costi per la velocità effettiva non utilizzata. È possibile aumentare o ridurre la velocità effettiva con provisioning in qualsiasi momento in base alle esigenze.  

* Il monitoraggio dell'utilizzo di UR e la proporzione di richieste soggette a limiti di velocità possono rivelare che non è necessario un provisioning costante della velocità effettiva per un intero giorno o un'intera settimana. Ad esempio, di notte o durante il fine settimana il traffico potrebbe essere inferiore. Usando il portale di Azure, gli SDK nativi di Azure Cosmos DB o l'API REST, è possibile ridimensionare la velocità effettiva con provisioning in qualsiasi momento. L'API REST di Azure Cosmos DB fornisce gli endpoint per aggiornare in modo programmatico il livello di prestazioni dei contenitori, semplificando la regolazione della velocità effettiva dal codice in base all'ora del giorno o al giorno della settimana. L'operazione viene eseguita senza alcun tempo di inattività e in genere ha effetto in meno di un minuto. 

* Uno dei casi in cui è necessario ridimensionare la velocità effettiva è quando si inseriscono dati in Azure Cosmos DB, ad esempio durante la migrazione dei dati. Dopo aver completato la migrazione, è possibile ridurre la velocità effettiva con provisioning per gestire lo stato stazionario della soluzione.  

* Tenere presente che il livello di granularità per la fatturazione è di un'ora, quindi non si risparmierà denaro se si modifica la velocità effettiva con provisioning più spesso di un'ora alla volta.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Determinare la velocità effettiva necessaria per un nuovo carico di lavoro 

Per determinare la velocità effettiva con provisioning per un nuovo carico di lavoro, è possibile usare la procedura seguente: 

1. Eseguire una valutazione iniziale approssimativa usando Capacity Planner e regolare le stime con l'aiuto di Azure Cosmos Explorer nel portale di Azure. 

2. È consigliabile creare i contenitori con una velocità effettiva superiore al previsto e poi ridurla in base alle esigenze. 

3. È consigliabile usare uno degli SDK nativi di Azure Cosmos DB per trarre vantaggio dalla ripetizione automatica dei tentativi quando le richieste vengono sottoposte a limiti di velocità. Se si lavora su una piattaforma non supportata e si usa l'API REST di Cosmos DB, implementare il criterio di ripetizione usando l'intestazione `x-ms-retry-after-ms`. 

4. Assicurarsi che il codice dell'applicazione supporti normalmente il caso quando tutti i tentativi hanno esito negativo. 

5. È possibile configurare gli avvisi dal portale di Azure per ottenere notifiche per i limiti di velocità. È possibile iniziare con limiti conservativi, ad esempio 10 richieste soggette a limiti di velocità negli ultimi 15 minuti, quindi passare a regole più severe dopo aver identificato l'utilizzo effettivo. È consentito impostare limiti di velocità occasionali, perché dimostrano che sono in uso, il che è esattamente ciò che si desidera eseguire. 

6. Usare il monitoraggio per comprendere il modello di traffico, in modo da poter riflettere sull'esigenza di regolare in modo dinamico il provisioning della velocità effettiva nel corso del giorno o della settimana. 

7. Monitorare regolarmente il rapporto tra velocità effettiva con provisioning e utilizzata, per assicurasi di non aver effettuato il provisioning di una quantità di contenitori e database superiore rispetto a quanto richiesto. Un controllo di sicurezza valido consiste nel disporre di una velocità effettiva con provisioning leggermente superiore.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Procedure consigliate per ottimizzare la velocità effettiva con provisioning 

La procedura seguente consente di rendere le soluzioni altamente scalabili ed economicamente vantaggiose quando si usa Azure Cosmos DB.  

1. Se il provisioning della velocità effettiva tra contenitori e database è notevolmente superiore, esaminare il rapporto tra UR con provisioning e UR utilizzate e perfezionare i carichi di lavoro.  

2. Un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito di UR associato all'esecuzione di operazioni tipiche rispetto a un database o a un contenitore di Azure Cosmos rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo. Assicurarsi di misurare e includere anche le query tipiche e il loro utilizzo. Per informazioni su come stimare in modo programmatico o con il portale i costi di UR per le query, vedere [Ottimizzare il costo delle query](online-backup-and-restore.md). 

3. Un altro modo per ottenere le operazioni e i relativi costi in UR è abilitando Log Analytics, che fornirà la suddivisione operazione/durata e l'addebito per la richiesta. Azure Cosmos DB offre l'addebito per la richiesta per ogni operazione, in modo che ogni addebito per le operazioni possa essere archiviato dalla risposta e quindi usato per l'analisi. 

4. È possibile aumentare e ridurre la velocità effettiva con provisioning in modo elastico in base alle esigenze di soddisfare le richieste del carico di lavoro. 

5. È possibile aggiungere e rimuovere aree associate all'account Azure Cosmos come si desidera e controllare i costi. 

6. Assicurarsi che la distribuzione di dati e carichi di lavoro nelle partizioni logiche dei contenitori sia uniforme. Se la distribuzione delle partizioni non è uniforme, il provisioning della velocità effettiva potrebbe essere superiore al valore richiesto. Se si individua la presenza di una distribuzione asimmetrica, si consiglia di ridistribuire il carico di lavoro in modo uniforme tra le partizioni o di ripartizionare i dati. 

7. Se si dispone di numerosi contenitori e questi contenitori non richiedono i contratti di servizio, è possibile usare l'offerta basata su database per i casi in cui i contratti di servizio per la velocità effettiva per contenitore non sono applicabili. È possibile identificare i contenitori di Azure Cosmos da migrare all'offerta di velocità effettiva a livello di database e poi eseguire la migrazione usando una soluzione basata sul feed di modifiche. 

8. È consigliabile usare il "livello gratuito di Cosmos DB" (gratuito per un anno), provare Cosmos DB (fino a tre aree) o scaricare l'emulatore di Cosmos DB per gli scenari di sviluppo/test. Queste opzioni per sviluppo/test consentono di ridurre notevolmente i costi.  

9. È possibile eseguire ottimizzazioni dei costi specifiche in base al carico di lavoro, ad esempio aumentando le dimensioni dei batch, bilanciando il carico delle operazioni di lettura in varie aree e deduplicando i dati, se applicabile.

10. Con la capacità riservata di Azure Cosmos DB, è possibile ottenere sconti significativi fino al 65% per tre anni. Il modello di capacità riservata Azure Cosmos DB è un impegno anticipato sulle unità richieste necessarie nel tempo. Gli sconti sono suddivisi in modelli tali che lo sconto aumenta con l'aumentare delle unità richieste che vengono usate in un lungo periodo. Questi sconti vengono applicati immediatamente. Il costo per eventuali UR superiori ai valori con provisioning viene addebitato in base al costo della capacità non riservata. Per altri dettagli, vedere [Capacità riservata di Cosmos DB](cosmos-db-reserved-capacity.md). Prendere in considerazione l'eventualità di acquistare capacità riservata per ridurre ulteriormente i costi della velocità effettiva con provisioning.  

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare ad altre informazioni sull'ottimizzazione dei costi in Azure Cosmos DB con gli articoli seguenti:

* Altre informazioni sull'[Ottimizzazione di sviluppo e test](optimize-dev-test.md)
* Altre informazioni su [come comprendere la fatturazione di Azure Cosmos DB](understand-your-bill.md)
* Altre informazioni sull'[ottimizzazione dei costi di archiviazione](optimize-cost-storage.md)
* Altre informazioni sull'[ottimizzazione del costo delle operazioni di lettura e scrittura](optimize-cost-reads-writes.md)
* Altre informazioni sull'[ottimizzazione del costo delle query](optimize-cost-queries.md)
* Altre informazioni sull'[ottimizzazione dei costi degli account Azure Cosmos multiarea](optimize-cost-regions.md)

