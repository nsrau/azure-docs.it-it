---
title: Gestire i prezzi e il volume di dati per Azure Application Insights | Microsoft Docs
description: Gestire volumi di dati di telemetria e monitorare i costi in Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gestire volumi di dati e prezzi in Application Insights

I prezzi per [Azure Application Insights][start] dipendono dal volume di dati per ogni applicazione. Ogni risorsa di Application Insights viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure.

Esistono due piani tariffari. Per impostazione predefinita viene applicato il piano Basic. In questo piano sono incluse le stesse funzionalità del piano Enterprise senza costi aggiuntivi e la fatturazione viene effettuata principalmente in base al volume di dati inseriti. Se si usa Operations Management Suite, è preferibile scegliere il piano Enterprise, che prevede un addebito per ogni nodo e limiti giornalieri per il volume di dati. In questo piano la fatturazione viene quindi effettuata in base ai dati inseriti oltre il limite previsto.

Per domande sulla determinazione dei prezzi per Application Insights, è possibile pubblicare una domanda nel [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Piani tariffari

Per i prezzi correnti nella valuta e nell'area locali, vedere la [pagina sui prezzi di Application Insights][pricing].

### <a name="basic-plan"></a>Piano Basic

Il piano Basic è quello applicato per impostazione predefinita quando viene creata una nuova risorsa di Application Insights e rappresenta una soluzione ottimale per tutti i clienti tranne quelli che dispongono di una sottoscrizione di Operations Management Suite.

* Nel piano Basic vengono applicati addebiti in base al volume dei dati: il numero di byte dei dati di telemetria ricevuti da Application Insights. Il volume di dati viene misurato come le dimensioni del pacchetto di dati JSON non compresso inviato dall'applicazione e ricevuto da Application Insights.
Per i [dati tabulari importati in Log Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), il volume dei dati viene misurato come dimensione non compressa dei file inviati ad Application Insights.
* I dati [Live Metrics Stream](app-insights-live-stream.md) non vengono conteggiati ai fini della determinazione del prezzo.
* Dal mese di aprile 2018 il piano Basic include l'opzione di [esportazione continua](app-insights-export-telemetry.md) e il [connettore di Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) senza costi aggiuntivi.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Piano Enterprise e diritti della sottoscrizione di Operations Management Suite

Come [annunciato di recente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), i clienti che acquistano Microsoft Operations Management Suite E1 ed E2 possono ottenere Application Insights Enterprise come componente aggiuntivo senza sostenere altri costi. Nello specifico, ogni unità di Operations Management Suite E1 ed E2 include il diritto a 1 nodo del piano Enterprise di Application Insights. Come descritto di seguito in modo più dettagliato, in ogni nodo di Application Insights può essere inserito giornalmente un volume di dati fino a un massimo di 200 MB, esclusi i dati di Log Analytics, con un periodo di conservazione di 90 giorni senza costi aggiuntivi. Poiché questo piano è applicabile solo ai clienti con una sottoscrizione di Operations Management Suite, per i clienti privi della sottoscrizione non viene visualizzata un'opzione per la selezione di questo piano.

> [!NOTE]
> Per ottenere questo diritto, le risorse di Application Insights devono essere disponibili nel piano tariffario Enterprise. Questo diritto si applica solo ai nodi, quindi le risorse di Application Insights nel piano Basic non otterranno alcun vantaggio. Si noti che questo diritto non sarà visibile nei costi stimati indicati nella pagina *Utilizzo e costi stimati*. Inoltre, se si sposta una sottoscrizione nel nuovo modello dei prezzi di monitoraggio di Azure del mese di aprile 2018, il piano Basic sarà l'unico disponibile. Non è pertanto consigliabile eseguire questa operazione se si ha una sottoscrizione di Operations Management Suite.

Per altre informazioni sul piano Enterprise, vedere la [pagina dei dettagli sui prezzi del piano Enterprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Test Web in più passaggi

È prevista una tariffa aggiuntiva per i [test Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Quanto scritto si riferisce ai test Web che eseguono una sequenza di azioni.

Non è prevista una tariffa separata per le "verifiche ping" di una singola pagina. I dati di telemetria delle verifiche ping e delle verifiche in più fasi incorrono in costi insieme agli altri dati di telemetria provenienti dall'app.

## <a name="review-pricing-plans-and-estimate-costs"></a>Esaminare i piani tariffari e stimare i costi

Application Insights offre informazioni per comprendere con facilità i piani tariffari disponibili e i possibili costi associati in base a modelli di utilizzo recente. Iniziare aprendo la pagina **Utilizzo e costi stimati** nella risorsa di Application Insights nel portale di Azure:

![Scegliere Prezzi.](./media/app-insights-pricing/pricing-001.png)

**a.** Esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati (dopo qualsiasi [campionamento](app-insights-sampling.md)) da server e app client e dai test di disponibilità.

**b.** I costi per i [test Web in più passaggi](app-insights-monitor-web-app-availability.md#multi-step-web-tests) vengono addebitati separatamente. Non sono inclusi i test di disponibilità semplici, il cui costo viene addebitato con il volume di dati.

**c.** Visualizzare le tendenze del volume di dati relative all'ultimo mese.

**d.** Abilitare il [campionamento](app-insights-sampling.md) per inserimento dati. 

**e.** Configurare il limite giornaliero del volume di dati.

Gli addebiti di Application Insights vengono aggiunti alla fatturazione di Azure. È possibile visualizzare i dettagli della fattura Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions). 

![Nel menu laterale scegliere Fatturazione.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Velocità dati
Esistono tre modi per limitare il volume di invio dei dati:

* **Campionamento:** questo meccanismo può ridurre la quantità di dati di telemetria inviati da server e app client, con una distorsione minima delle metriche. Si tratta dello strumento principale per ottimizzare la quantità di dati. Altre informazioni sulle [funzionalità di campionamento](app-insights-sampling.md). 
* **Limite giornaliero:** quando si crea una risorsa di Application Insights nel portale di Azure, il limite è impostato sul valore di 100 GB al giorno. Quando si crea una risorsa di Application Insights da Visual Studio, il valore predefinito è ridotto (solo 32,3 MB al giorno) al fine di semplificare le attività di test. In questo caso è opportuno che l'utente aumenti il limite giornaliero prima di distribuire l'applicazione nell'ambiente di produzione. Il limite massimo è 1000 GB al giorno, a meno che non sia stato richiesto un valore massimo più alto per un'applicazione con traffico elevato. Prestare attenzione quando si imposta il limite giornaliero perché lo scopo deve essere quello di **non raggiungere mai il limite massimo giornaliero** per evitare di perdere dati per il resto del giorno e di essere sempre in grado di monitorare l'applicazione. Per modificarlo, usare l'opzione Limite di utilizzo volume giornaliero dalla pagina Utilizzo e costi stimati (vedere di seguito). È stata rimossa la restrizione per alcuni tipi di sottoscrizione con un credito che non poteva essere usato per Application Insights. In precedenza, se alla sottoscrizione era associato un limite di spesa, nella finestra di dialogo relativa al limite giornaliero erano presenti istruzioni per rimuoverlo e abilitare l'aumento del limite oltre 32,3 MB al giorno.
* **Limitazione:** la velocità dei dati non supera i 32.000 eventi al secondo, in base a una media calcolata a intervalli di 1 minuto.

*Cosa accade se l'app supera la velocità di limitazione?*

* Il volume dei dati inviati dall'app viene valutato ogni minuto. Se il valore supera la velocità media al secondo calcolata nel minuto, il server rifiuta alcune richieste. L'SDK esegue il buffering dei dati e quindi tenta di inviare di nuovo, distribuendo una sovratensione nell'arco di diversi minuti. Se l'app continua a inviare dati che eccedono la limitazione, alcuni di questi verranno eliminati. (Gli SDK di ASP.NET, Java e JavaScript ritentano l'invio, mentre altri SDK potrebbero semplicemente eliminare i dati sottoposti a limitazione). In caso di avvenuta limitazione, verrà visualizzata una notifica che avviserà che ciò si è verificato.

*Come è possibile conoscere la quantità di dati inviati dall'app?*

* Aprire la pagina **Utilizzo e costi stimati** per visualizzare il grafico dei volumi di dati giornalieri. 
* Altrimenti, in Esplora metriche, aggiungere un nuovo grafico e selezionare **Volume punti dati** come metrica. Attivare il raggruppamento in base a **Tipo di dati**.

## <a name="to-reduce-your-data-rate"></a>Per ridurre la velocità dei dati
Ecco alcune operazioni da eseguire per ridurre il volume di dati:

* Utilizzare [Campionamento](app-insights-sampling.md). Questa tecnologia riduce la frequenza dei dati senza deviare le metriche e senza compromettere la possibilità di spostarsi tra elementi correlati nella Ricerca. Nelle app server funziona automaticamente.
* [Limitare il numero di chiamate Ajax che possono essere segnalate](app-insights-javascript.md#detailed-configuration) in ogni visualizzazione pagina o disattivare la creazione di report Ajax.
* Disattivare i moduli di raccolta non necessari [modificando il file ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ad esempio, è possibile che i contatori delle prestazioni o dati sulle dipendenze siano non essenziali.
* Suddividere i dati di telemetria per separare le chiavi di strumentazione. 
* Pre-aggregare metriche. Se sono state inserite chiamate a TrackMetric nell'applicazione, è possibile ridurre il traffico utilizzando l'overload che accetta il calcolo della media e la deviazione standard di un batch di misurazioni. In alternativa è possibile usare un [pacchetto di pre-aggregazione](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Gestione del volume di dati massimo giornaliero

È possibile usare il limite di utilizzo di volume giornaliero per limitare i dati raccolti, ma se il limite viene raggiunto, si verificherà una perdita di tutti i dati di telemetria inviati dall'applicazione per il resto del giorno. **Non è consigliabile** che l'applicazione raggiunga il limite giornaliero, in quanto una volta raggiunto non si riuscirà più a rilevare l'integrità e le prestazioni dell'applicazione in uso.

Usare invece il [campionamento](app-insights-sampling.md) per ottimizzare il volume di dati al livello desiderato e usare il limite giornaliero solo come ultima opzione, nel caso in cui l'applicazione inizia a inviare volumi di dati di telemetria più elevati in modo imprevisto.

Per modificare il limite giornaliero, nella sezione Configura della risorsa di Application Insights, dalla pagina **Utilizzo e costi stimati**, fare clic su **Limite di utilizzo giornaliero**.

![Regolazione del limite di uso del volume di dati di telemetria giornalieri](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Campionamento
Il [campionamento](app-insights-sampling.md) consente di ridurre la frequenza con cui i dati di telemetria vengono inviati all'app, pur mantenendo la possibilità di trovare gli eventi correlati durante le ricerche di diagnostica e il conteggio corretto degli eventi.

Il campionamento consente di ridurre in modo efficace i costi e di non superare la quota mensile. L'algoritmo di campionamento conserva gli elementi correlati ai dati di telemetria, in modo che, quando si usa la ricerca, ad esempio, è possibile trovare la richiesta correlata a una particolare eccezione. L'algoritmo mantiene inoltre i conteggi corretti e consente di visualizzare in Esplora metriche i valori corretti della frequenza delle richieste, della frequenza delle eccezioni e di altri contatori.

Sono disponibili diversi tipi di campionamento.

* Il [campionamento adattivo](app-insights-sampling.md) è l'opzione predefinita per ASP.NET SDK e si adatta automaticamente al volume dei dati di telemetria inviati dall'app. Il campionamento viene eseguito automaticamente nell'SDK dell'app Web riducendo il traffico dei dati di telemetria sulla rete. 
* Il *campionamento per inserimento* è un'alternativa che opera nel punto in cui i dati di telemetria provenienti dall'app raggiungono il servizio Application Insights. Non ha alcun effetto sul volume dei dati di telemetria inviati dall'app, ma riduce tuttavia il volume mantenuto dal servizio. È possibile usarlo per ridurre la quota che i dati di telemetria usano da browser e altri SDK.

Per impostare il campionamento per inserimento, specificare il controllo nella finestra di dialogo dei prezzi:

![Dalla finestra di dialogo Utilizzo e costi stimati fare clic su Campionamento dei dati e selezionare una frazione di campionamento.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> La finestra di dialogo Campionamento dei dati controlla solo il valore del campionamento per inserimento. Non riflette la frequenza di campionamento applicata da Application Insights SDK nell'app. Se i dati di telemetria in ingresso sono già stati campionati nell'SDK, il campionamento per inserimento non verrà applicato.
>

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analisi](app-insights-analytics.md) simile alla seguente:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

In ogni record conservato, `itemCount` indica il numero di record originali che rappresenta, uguale a 1 + il numero di record precedenti scartati. 

## <a name="automation"></a>Automazione

È possibile scrivere uno script per impostare il piano tariffario tramite Azure Resource Manager. [Informazioni](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Riepilogo dei limiti

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Campionamento](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
