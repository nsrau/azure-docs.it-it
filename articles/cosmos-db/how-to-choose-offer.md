---
title: Come scegliere tra la scalabilità manuale e automatica in Azure Cosmos DB
description: Scopri come scegliere tra la velocità effettiva con provisioning standard (manuale) e la velocità effettiva con provisioning automatico per il tuo carico di lavoro.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: f7fd40c48f94b4337c5ec342499203f83763299b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909931"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Come scegliere tra la velocità effettiva con provisioning standard (manuale) e la velocità effettiva con provisioning a scalabilità automatica 

Azure Cosmos DB supporta due tipi o offerte di velocità effettiva con provisioning: standard (manuale) e a scalabilità automatica. Entrambi i tipi di velocità effettiva sono adatti per carichi di lavoro cruciali che richiedono scalabilità e prestazioni elevate e sono supportati dagli stessi contratti di servizio Azure Cosmos DB in merito a velocità effettiva, disponibilità, latenza e coerenza.

Questo articolo descrive come scegliere tra la velocità effettiva con provisioning standard (manuale) e la velocità effettiva con provisioning a scalabilità automatica per un carico di lavoro. 

## <a name="overview-of-provisioned-throughput-types"></a>Panoramica dei tipi di velocità effettiva con provisioning
Prima di illustrare la differenza tra standard (manuale) e a scalabilità automatica, è importante comprendere il ruolo della velocità effettiva con provisioning in Azure Cosmos DB. 

Quando si usa la velocità effettiva con provisioning, si imposta la velocità effettiva, misurata in unità richiesta al secondo (UR/s), necessaria per il carico di lavoro in corso. Il servizio effettua quindi il provisioning della capacità necessaria per supportare i requisiti di velocità effettiva. Ogni operazione di database eseguita sul servizio, ad esempio letture, scritture e query, userà una determinata quantità di unità richiesta (UR). Altre informazioni sulle [unità richiesta](request-units.md).

La tabella seguente illustra un confronto generale tra standard (manuale) e a scalabilità automatica.

|Descrizione|Standard (manuale)|Autoscale|
|-------------|------|-------|
|Ideale per|Carichi di lavoro con traffico stabile o prevedibile|Carichi di lavoro con traffico variabile o imprevedibile. Vedere i [casi d'uso di scalabilità automatica](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Funzionamento|Viene effettuato il provisioning di una quantità di UR/s `T` statiche nel tempo, a meno che non vengano modificate manualmente. Ogni secondo, è possibile usare una velocità effettiva fino a `T` UR/s. <br/><br/>Se, ad esempio, si imposta il parametro Standard (manuale) su 400 UR/s, la velocità effettiva resterà pari a 400 UR/s.|È possibile impostare la quantità massima di UR/s `Tmax` che non si vuole venga superata dal sistema. Il sistema ridimensiona automaticamente la velocità effettiva `T` in modo che `0.1* Tmax <= T <= Tmax`. <br/><br/>Se, ad esempio, si imposta una scalabilità automatica massima di 4000 UR/s, il sistema rispetterà una scalabilità compresa tra 400 e 4000 UR/s.|
|Scenari di utilizzo|Quando si preferisce gestire manualmente la capacità di velocità effettiva (UR/s) e impostare autonomamente il valore di scalabilità.<br/><br/>In presenza di un uso elevato e coerente di UR/s con provisioning. Se per tutte le ore di un mese si imposta un valore di UR/s con provisioning `T` e si usa la quantità massima per il 66% delle ore o più, si stima che si risparmi scegliendo la velocità effettiva con provisioning standard (manuale).<br/><br/>Questa supposizione si basa sul confronto tra l'impostazione di `T` in modalità standard (manuale) e l'impostazione della stessa quantità `Tmax` in modalità scalabilità automatica. |Quando si preferisce che Azure Cosmos DB gestisca la scalabilità e la capacità della velocità effettiva (UR/s), in base all'utilizzo.<br/><br/>In presenza di un uso di UR/s variabile o difficile da stimare. Se per tutte le ore di un mese si imposta un valore di UR/s con scalabilità automatica massima di `Tmax` e la quantità massima `Tmax` viene usata per meno del 66% delle ore, si stima che si risparmi scegliendo la scalabilità automatica.<br/><br/>Questa supposizione si basa sul confronto tra l'impostazione della velocità effettiva a scalabilità automatica `Tmax` e l'impostazione della stessa quantità `T` in modalità standard (manuale).|
|Modello di fatturazione|Per le UR/s con provisioning, la fatturazione viene eseguita su base oraria, indipendentemente dal numero di UR utilizzate.<br/><br/>Esempio: <li>Provisioning di 400 UR/s</li><li>Ora 1: nessuna richiesta</li><li>Ora 2: richieste per un valore di 400 UR/s</li><br/><br/>Per entrambe le ore 1 e 2, verranno fatturate 400 UR/s alla [tariffa standard (manuale)](https://azure.microsoft.com/pricing/details/cosmos-db/).|La fatturazione viene eseguita su base oraria, per il più elevato numero di UR/s a cui il sistema è giunto nel corso dell'ora. <br/><br/>Esempio: <li>Provisioning con scalabilità automatica massima di UR/s pari a 4000 UR/s (con scalabilità compresa tra 400 e 4000 UR/s)</li><li>Ora 1: il sistema raggiunge il valore massimo di 3500 UR/s</li><li>Ora 2: il sistema raggiunge il valore minimo di 400 UR/s (sempre il 10% di `Tmax`), a causa di assenza di utilizzo</li><br/><br/>Verranno addebitate 3500 UR/s nell'ora 1 e 400 UR/s nell'ora 2 alla [tariffa della velocità effettiva con provisioning a scalabilità automatica](https://azure.microsoft.com/pricing/details/cosmos-db/). La velocità di scalabilità automatica per UR/s è 1,5 * tariffa standard (manuale).
|Cosa accade se si supera la quantità di UR/s con provisioning|Il numero di UR/s rimane statico alla quantità sottoposta a provisioning. Per eventuali richieste che consumano un numero di UR al secondo superiore alla quantità di cui è stato effettuato il provisioning viene forzata una limitazione della velocità, con una risposta che suggerisce un tempo di attesa prima di riprovare. Se necessario, è possibile aumentare o diminuire manualmente il numero di UR/s.| Il sistema aumenta il numero di UR/s fino alla quantità massima di UR con scalabilità automatica. Per eventuali richieste che consumano un numero di UR al secondo superiore alla quantità massima con scalabilità automatica precedentemente impostata, viene forzata una limitazione della velocità, con una risposta che suggerisce un tempo di attesa prima di riprovare.|

## <a name="understand-your-traffic-patterns"></a>Panoramica sui modelli di traffico

### <a name="new-applications"></a>Nuove applicazioni ###

Se si sta compilando una nuova applicazione e non si conosce ancora il modello di traffico, è possibile iniziare dal punto di ingresso di UR/s (o numero minimo di UR/s) per evitare l'overprovisioning nella fase iniziale. Se invece si ha un'applicazione di piccole dimensioni che non richiede una particolare scalabilità, è preferibile effettuare il provisioning solo del numero minimo di UR/s (punto di ingresso) per ottimizzare i costi. Per le piccole applicazioni con un basso traffico previsto, è anche possibile prendere in considerazione la modalità di capacità senza [Server](throughput-serverless.md) .

Se si prevede di usare lo standard (manuale) o la scalabilità automatica, è necessario prendere in considerazione quanto segue:

Se si seleziona la velocità effettiva con provisioning standard (manuale) al punto di ingresso di 400 UR/s, non sarà possibile usare più di 400 UR/s, a meno che non si modifichi manualmente la velocità effettiva. Verranno quindi addebitate 400 UR/s alla tariffa oraria della velocità effettiva con provisioning standard (manuale).

Se invece si seleziona la velocità effettiva con provisioning a scalabilità automatica fino a 4000 UR/s, la risorsa verrà ridimensionata su un valore compreso tra 400 e 4000 UR/s. Poiché la tariffa di fatturazione della velocità effettiva a scalabilità automatica per UR/s è pari a 1,5 volte la tariffa standard (manuale), per le ore in cui il sistema è rimasto al punto di ingresso di 400 UR/s, l'importo della fattura sarebbe superiore rispetto a quello che risulterebbe impostando manualmente il provisioning a 400 UR/s. Con la scalabilità automatica, tuttavia, se in un momento qualsiasi si verifica un picco di traffico dell'applicazione, è possibile usare fino a 4000 UR/s senza che sia necessario alcun intervento da parte dell'utente. In generale, quindi, è consigliabile prendere in considerazione il vantaggio di poter usare in qualsiasi momento il numero massimo di UR/s con una tariffa pari ad appena 1,5 volte la tariffa standard.

Per stimare i requisiti di velocità effettiva, usare lo [strumento di calcolo della capacità](estimate-ru-with-capacity-planner.md) di Azure Cosmos DB. 

### <a name="existing-applications"></a>Applicazioni esistenti ###

Se si ha un'applicazione esistente per cui è stata applicata la velocità effettiva con provisioning standard (manuale), è possibile usare le [metriche di Monitoraggio di Azure](../azure-monitor/insights/cosmosdb-insights-overview.md) per determinare se il modello di traffico in uso è adatto alla scalabilità automatica. 

Per prima cosa, individuare la [metrica di utilizzo delle unità richiesta normalizzate](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) del database o del contenitore. L'utilizzo normalizzato è una misura della capacità attualmente in uso della velocità effettiva con provisioning (manuale). Più il numero si avvicina al 100%, maggiore sarà la quantità di UR/s con provisioning interamente usati. [Altre informazioni](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) sulle metriche.

Determinare quindi il modo in cui l'utilizzo normalizzato varia nel tempo. Trovare l'utilizzo normalizzato più elevato per ogni ora. Calcolare quindi l'utilizzo normalizzato medio in tutte le ore. Se si nota che l'utilizzo medio è inferiore al 66%, è consigliabile abilitare la scalabilità automatica nel database o nel contenitore. Al contrario, se l'utilizzo medio è superiore al 66%, è consigliabile mantenere la velocità effettiva con provisioning standard (manuale).

> [!TIP]
> Se l'account è configurato per l'uso di scritture in più aree e ha più di un'area, la frequenza per 100 ur/sec è la stessa sia per la scalabilità manuale che per quella automatica. Ciò significa che l'abilitazione della scalabilità automatica non comporta costi aggiuntivi, indipendentemente dall'utilizzo. Di conseguenza, è sempre consigliabile usare la scalabilità automatica con scritture in più aree quando si dispone di più di un'area, per sfruttare i vantaggi offerti dal pagamento solo per le UR/sec a cui si applica la scalabilità dell'applicazione. Se si hanno Scritture in più aree e un'area, usare l'utilizzo medio per determinare se la scalabilità automatica provocherà risparmi sui costi. 

#### <a name="examples"></a>Esempi

Verranno ora esaminati due carichi di lavoro di esempio diversi e verranno analizzati se sono adatti per la velocità effettiva di scalabilità manuale o automatica. Per illustrare l'approccio generale, verranno analizzate tre ore di cronologia per determinare la differenza di costo tra l'uso della scalabilità manuale e automatica. Per i carichi di lavoro di produzione, è consigliabile usare da 7 a 30 giorni di cronologia (o più, se disponibile) per stabilire un modello di utilizzo di Ur/s.

> [!NOTE]
> Tutti gli esempi illustrati in questo documento sono basati sul prezzo di un account Azure Cosmos distribuito in un'area non governativa negli Stati Uniti. I prezzi e il calcolo variano a seconda dell'area in uso, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB per le ultime informazioni sui prezzi.

Presupposti:
- Si supponga di avere attualmente una velocità effettiva manuale di 30.000 UR/sec. 
- La nostra area è configurata con le Scritture in una singola area, con una sola area. In presenza di più aree, il costo orario viene moltiplicato per il numero di aree.
- Usa le tariffe pubbliche per i prezzi manuali ($0,008 USD per 100 ur/sec all'ora) e la velocità effettiva di scalabilità automatica ($0,012 USD per 100 ur/s all'ora) negli account di scrittura a area singola. Per informazioni dettagliate, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) . 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Esempio 1: carico di lavoro variabile (consigliato per la scalabilità automatica)

Prima di tutto, viene esaminato il consumo di ur normalizzato. Questo carico di lavoro ha un traffico variabile, con un consumo di ur normalizzato compreso tra il 6% e il 100%. Ci sono picchi occasionali al 100% difficili da stimare, ma molte ore con utilizzo ridotto. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Carico di lavoro con consumo di unità richiesta normalizzato del traffico variabile tra il 6% e il 100% per tutte le ore":::

Confrontare il costo del provisioning di 30.000 UR/s manuale della velocità effettiva, anziché impostare la scalabilità automatica max ur/s su 30.000 (con scalabilità compresa tra 3000 e 30.000 UR/sec). 

Analizziamo ora la cronologia. Si supponga di avere l'utilizzo descritto nella tabella seguente. L'utilizzo medio nelle tre ore è pari al 39%. Poiché le medie del consumo di unità richiesta normalizzate sono inferiori al 66%, viene risparmiata usando la scalabilità automatica. 

Si noti che nell'ora 1, quando si usa il 6% di utilizzo, la scalabilità automatica fattura le UR/s per il 10% del numero massimo di ur/sec, che corrisponde al valore minimo per ogni ora. Sebbene il costo della scalabilità automatica possa essere superiore alla velocità effettiva manuale in determinate ore, a condizione che l'utilizzo medio sia inferiore al 66% per tutte le ore, la scalabilità automatica sarà più economica in generale.

|Periodo di tempo  | Utilizzo |Unità di scalabilità automatica fatturate/sec  |Opzione 1: manuale 30.000 UR/sec  | Opzione 2: ridimensionamento automatico tra 3000-30.000 UR/sec |
|---------|---------|---------|---------|---------|
|Ora 1  | 6%  |     3000  |  30.000 * 0,008/100 = $2,40        |   3000 * 0,012/100 = $0,36      |
|Ora 2  | 100%  |     30.000    |  30.000 * 0,008/100 = $2,40       |  30.000 * 0,012/100 = $3,60      |
|Ora 3 |  11%  |     3300    |  30.000 * 0,008/100 = $2,40       |    3300 * 0,012/100 = $0,40     |
|**Totale**   |  |        |  $7,20       |    $4,36 (39% di risparmio)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Esempio 2: carico di lavoro fisso (velocità effettiva manuale consigliata)

Questo carico di lavoro ha un traffico costante, con un consumo di ur normalizzato compreso tra 72% e 100%. Con il provisioning di 30.000 UR/s, questo significa che l'utilizzo è compreso tra 21.600 e 30.000 UR/sec.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Carico di lavoro con consumo di unità richiesta normalizzato del traffico variabile tra il 6% e il 100% per tutte le ore":::

Confrontare il costo del provisioning di 30.000 UR/s manuale della velocità effettiva, anziché impostare la scalabilità automatica max ur/s su 30.000 (con scalabilità compresa tra 3000 e 30.000 UR/sec).

Si supponga di avere la cronologia di utilizzo come descritto nella tabella. Il nostro utilizzo medio in queste tre ore è pari al 88%. Poiché le medie di consumo delle unità richiesta normalizzate sono superiori al 66%, viene risparmiata usando la velocità effettiva manuale.

In generale, se l'utilizzo medio in tutte le 730 ore in un mese è maggiore del 66%, verrà risparmiato usando la velocità effettiva manuale. 

| Periodo di tempo | Utilizzo |Unità di scalabilità automatica fatturate/sec  |Opzione 1: manuale 30.000 UR/sec  | Opzione 2: ridimensionamento automatico tra 3000-30.000 UR/sec |
|---------|---------|---------|---------|---------|
|Ora 1  | 72%  |     21.600   |  30.000 * 0,008/100 = $2,40        |   21600 * 0,012/100 = $2,59      |
|Ora 2  | 93%  |     28.000    |  30.000 * 0,008/100 = $2,40       |  28.000 * 0,012/100 = $3,36       |
|Ora 3 |  100%  |     30.000    |  30.000 * 0,008/100 = $2,40       |    30.000 * 0,012/100 = $3,60     |
|**Totale**   |  |        |  $7,20       |    $9,55     |

> [!TIP]
> Con la velocità effettiva standard (manuale) impostata, è possibile usare la metrica di utilizzo normalizzato per stimare le UR/s effettive che sarebbe possibile usare se si passasse alla scalabilità automatica. Moltiplicare l'utilizzo normalizzato in un punto nel tempo per il numero di UR/s con provisioning standard (manuale). Se, ad esempio, è stato effettuato il provisioning di 5000 UR/s e l'utilizzo normalizzato è pari al 90%, il consumo di UR/s sarà pari a 0,9 * 5000 = 4500 UR/s. Se si nota che il modello di traffico è variabile, ma si è al di sopra o al di sotto del limite di provisioning, è possibile abilitare la scalabilità automatica e quindi modificare di conseguenza il numero massimo di UR/s con scalabilità automatica.

#### <a name="how-to-calculate-average-utilization"></a>Come calcolare l'utilizzo medio
Ridimensionamento automatico fatture per le UR/sec più elevate a in un'ora. Quando si analizza il consumo di unità richiesta normalizzate nel tempo, è importante usare l'utilizzo più elevato per ora durante il calcolo della media. 

Per calcolare la media dell'utilizzo più elevato tra tutte le ore:
1. Impostare l' **aggregazione** sulla metrica di utilizzo di Noramlized ur su **Max** .
1. Selezionare la **granularità dell'ora** su 1 ora.
1. Passare a **Opzioni grafico** .
1. Selezionare l'opzione grafico a barre. 
1. In **Condividi** selezionare l'opzione **Scarica in Excel** . Dal foglio di calcolo generato calcolare l'utilizzo medio in tutte le ore. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Carico di lavoro con consumo di unità richiesta normalizzato del traffico variabile tra il 6% e il 100% per tutte le ore":::

## <a name="measure-and-monitor-your-usage"></a>Misurare e monitorare l'utilizzo
Dopo aver scelto il tipo di velocità effettiva, è necessario monitorare l'applicazione a lungo termine e apportare eventuali modifiche in base alle esigenze. 

Se si sceglie la scalabilità automatica, usare Monitoraggio di Azure per visualizzare il numero massimo di UR/s con provisioning a scalabilità automatica ( **velocità effettiva massima a scalabilità automatica** ) e il numero di UR/s a cui si trova attualmente il sistema ( **velocità effettiva con provisioning** ). Di seguito è riportato un esempio di carico di lavoro variabile o imprevedibile per il quale viene usata la scalabilità automatica. Se non è presente traffico, il sistema ridimensiona il numero di UR/s alla quantità minima del 10% rispetto al numero massimo di UR/s, che in questo caso sono, rispettivamente, 5000 UR/s e 50.000 UR/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Carico di lavoro con consumo di unità richiesta normalizzato del traffico variabile tra il 6% e il 100% per tutte le ore":::

> [!NOTE]
> Se si usa la velocità effettiva con provisioning standard (manuale), la metrica **Velocità effettiva sottoposta a provisioning** fa riferimento all'impostazione definita dall'utente. Se si usa la velocità effettiva con provisioning a scalabilità automatica, la metrica si riferisce al valore di UR/s a cui si trova attualmente il sistema.

## <a name="next-steps"></a>Passaggi successivi
* Usare il [calcolatore UR](https://cosmos.azure.com/capacitycalculator/) per stimare la velocità effettiva per i nuovi carichi di lavoro.
* Usare [Monitoraggio di Azure](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) per monitorare i carichi di lavoro esistenti.
* Informazioni su come [effettuare il provisioning della velocità effettiva con scalabilità automatica in un database o un contenitore di Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Vedere le [domande frequenti sulla scalabilità automatica](autoscale-faq.md).