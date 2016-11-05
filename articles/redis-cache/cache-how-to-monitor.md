---
title: Come monitorare Cache Redis di Azure | Microsoft Docs
description: Informazioni su come monitorare l'integrità e le prestazioni delle istanze di Cache Redis di Azure
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: ''

ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: sdanie

---
# Come monitorare Cache Redis di Azure
Cache Redis di Azure fornisce diverse opzioni per il monitoraggio delle istanze della cache. È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Questi strumenti permettono di monitorare l'integrità delle istanze della Cache Redis di Azure e semplificano la gestione delle applicazioni di memorizzazione nella cache.

Quando è abilitata la diagnostica della cache, le metriche per le istanze di Cache Redis di Azure vengono raccolte ogni 30 secondi circa e archiviate in modo da poter essere visualizzate nel grafico e valutate in base alle regole di avviso.

Le metriche della cache vengono raccolte utilizzando il comando [INFO](http://redis.io/commands/info) di Redis. Per ulteriori informazioni sui diversi valori INFO usati per ciascuna metrica della cache, vedere [Metriche disponibili e intervalli di report](#available-metrics-and-reporting-intervals).

Per visualizzare le metriche della cache, [sfogliare](cache-configure.md#configure-redis-cache-settings) l’istanza della cache nel [portale di Azure](https://portal.azure.com). L’accesso alle metriche per le istanze di Cache Redis di Azure viene eseguito nel pannello **Metriche Redis**.

![Metriche Redis][redis-cache-redis-metrics-blade]

> [!IMPORTANT]
> Se viene visualizzato il messaggio seguente nel pannello **Metriche Redis**, seguire i passaggi nella sezione [Abilitare la diagnostica della cache](#enable-cache-diagnostics) per abilitare la diagnostica della cache.
> 
> `Monitoring may not be enabled. Click here to turn on Diagnostics.`
> 
> 

Il pannello **Metriche Redis** dispone di grafici di **Monitoraggio** che consentono di visualizzare le metriche della cache. Ogni grafico può essere personalizzata aggiungendo o rimuovendo le metriche e modificando l'intervallo di report. Per visualizzare e configurare operazioni e avvisi, il pannello **Cache Redis**s presenta la sezione **Operazioni** in cui sono visualizzati **Eventi** e **Regole di avviso** della cache.

## Abilitare la diagnostica della cache
Cache Redis di Azure offre la possibilità di disporre di dati di diagnostica archiviati in un account di archiviazione, in modo che sia possibile utilizzare gli strumenti desiderati per accedere ed elaborare i dati direttamente. Per consentire la raccolta, l'archiviazione e la visualizzazione della diagnostica della cache nel portale di Azure, è necessario configurare un account di archiviazione. Le cache nella stessa area e nella stessa sottoscrizione condividono lo stesso account di archiviazione di diagnostica e quando viene modificata la configurazione, la modifica viene applicata a tutte le cache della sottoscrizione che si trovano in tale area.

Per abilitare e configurare la diagnostica della cache, passare al pannello **Cache Redis** per l'istanza della cache. Se la diagnostica non è ancora abilitate, viene visualizzato un messaggio anziché un grafico di diagnostica.

![Abilitare la diagnostica della cache][redis-cache-enable-diagnostics]

Fare clic sul messaggio per visualizzare il pannello **Metrica** e fare clic su **Impostazioni diagnostica** per abilitare e configurare le impostazioni di diagnostica per l'istanza del servizio cache.

![Impostazioni di diagnostica][redis-cache-diagnostic-settings]

![Configurare la diagnostica][redis-cache-configure-diagnostics]

Fare clic sul pulsante **Attiva** per abilitare la diagnostica della cache e visualizzare la configurazione della diagnostica.

Fare clic sulla freccia a destra di **Account di archiviazione** per selezionare un account di archiviazione per contenere i dati di diagnostica. Per prestazioni ottimali, selezionare un account di archiviazione nella stessa area della cache.

Dopo aver configurate le impostazioni di diagnostica, fare clic su **Salva** per salvare la configurazione. Si noti che per rendere effettive le modifiche potrebbero essere necessari alcuni minuti.

> [!IMPORTANT]
> Le cache nella stessa area e nella stessa sottoscrizione condividono le stesse impostazioni di archiviazione di diagnostica e, quando viene modificata la configurazione (abilitazione/disabilitazione della diagnostica o cambio dell'account di archiviazione), la modifica viene applicata a tutte le cache della sottoscrizione che si trovano in tale area.
> 
> 

Per visualizzare le metriche archiviate, esaminare le tabelle dell'account di archiviazione con i nomi che iniziano con `WADMetrics`. Per ulteriori informazioni sull'accesso alle metriche archiviate all'esterno del portale di Azure, vedere l’esempio relativo all’[accesso ai dati di monitoraggio di Cache Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).

> [!NOTE]
> Solo le metriche archiviate nell'account di archiviazione selezionato vengono visualizzate nel portale di Azure. Se si modificano gli account di archiviazione, i dati nell'account di archiviazione configurato in precedenza rimangono disponibili per il download, ma non vengono visualizzati nel portale di Azure.
> 
> 

## Metriche disponibili e intervalli di report
Le metriche della cache vengono segnalate mediante vari intervalli di report, tra cui **Ultima ora**, **Oggi**, **Ultima settimana** e **Personalizza**. Nel pannello **Metrica** per ogni grafico delle metriche vengono visualizzati i valori medi, minimi e massimi per ogni metrica nel grafico e alcune metriche visualizzano un totale per l'intervallo di report.

In ogni metrica sono incluse due versioni. Una metrica misura la prestazione per l'intera cache e per le cache che utilizzano [clustering](cache-how-to-premium-clustering.md), una seconda versione della metrica che include `(Shard 0-9)` nel nome misura la prestazione per una singola partizione in una cache. Se ad esempio una cache ha 4 partizioni, `Cache Hits` è la quantità totale di riscontri per l'intera cache e `Cache Hits (Shard 3)` rappresenta le occorrenze di tale partizionamento della cache.

> [!NOTE]
> Anche quando la cache è inattiva senza applicazioni client attive connesse, è possibile osservare una certa attività della cache, ad esempio client connessi, uso della memoria e operazioni eseguite. Questa attività è normale durante il funzionamento di un'istanza di Cache Redis di Azure.
> 
> 

| Metrica | Descrizione |
| --- | --- |
| Riscontri cache |Il numero di ricerche chiave riuscite durante l'intervallo di report specificato. Questo `keyspace_hits` è associato al comando [INFO](http://redis.io/commands/info) di Redis. |
| Mancati riscontri nella cache |Il numero di ricerche chiave non riuscite durante l'intervallo di report specificato. Questo `keyspace_misses` è associato al comando INFO di Redis. I mancati riscontri nella cache non indicano necessariamente un problema con la cache. Ad esempio, quando si utilizza il modello di programmazione cache-aside, un'applicazione esegue la ricerca di un elemento innanzitutto nella cache. Se l'elemento non è presente (mancato riscontro nella cache), viene recuperato dal database e aggiunto alla cache per la volta successiva. I mancati riscontri nella cache sono un comportamento normale per il modello di programmazione cache-aside. Se il numero di mancati riscontri nella cache è superiore al previsto, esaminare la logica dell'applicazione che consente di popolare ed eseguire le lettura dalla cache. Se gli elementi vengono rimossi dalla cache a causa della pressione della memoria, si possono verificare alcuni mancati riscontri nella cache, ma una metrica migliore da monitorare relativamente alla pressione della memoria sarebbe `Used Memory` o `Evicted Keys`. |
| Client connessi |Il numero di connessioni client alla cache durante l'intervallo di report specificato. Questo `connected_clients` è associato al comando INFO di Redis. Una volta raggiunto il [limite delle connessioni](cache-configure.md#default-redis-server-configuration), i successivi tentativi di connessione alla cache avranno esito negativo. Si noti che anche in assenza di applicazione client attive, è possibile che siano presenti alcune istanze di client connesse a causa di processi e connessioni interni. |
| Chiavi rimosse |Il numero di elementi rimossi dalla cache durante l'intervallo di report specificato a causa del limite `maxmemory`. Questo esegue il mapping a `evicted_keys` dal comando INFO di Redis. |
| Chiavi scadute |Il numero di elementi nella cache durante l'intervallo di report specificato. Questo valore esegue il mapping a `expired_keys` dal comando INFO di Redis. |
| Operazioni Get |Il numero di operazioni Get nella cache durante l'intervallo di report specificato. Questo valore è la somma dei seguenti valori del comando INFO di Redis (tutto): `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` e `cmdstat_getrange`, ed è equivalente alla somma dei riscontri e dei mancati riscontri nella cache durante l'intervallo di report. |
| Carico server Redis |La percentuale di cicli in cui il server di Redis è impegnato nell’elaborare e non inattivo in attesa di messaggi. Se il contatore raggiunge 100 significa che il server di Redis ha raggiunto un limite massimo delle prestazioni e la CPU non può elaborare il lavoro più velocemente. Se si osserva un elevato carico del server Redis si vedranno le eccezioni di timeout nel client. In tal caso, è necessario prendere in considerazione il dimensionamento o il partizionamento dei dati in più cache. |
| Operazioni Set |Il numero di operazioni Set nella cache durante l'intervallo di report specificato. Questo valore è la somma dei seguenti valori del comando INFO di Redis (tutto): `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` e `cmdstat_setnx`. |
| Totale operazioni |Numero totale di comandi elaborati dal server di cache durante l'intervallo di report specificato. Questo valore esegue il mapping a `total_commands_processed` dal comando INFO di Redis. Si noti che quando Cache Redis di Azure viene utilizzata esclusivamente per la pubblicazione o la sottoscrizione non saranno disponibili metriche per `Cache Hits`, `Cache Misses`, `Gets` o `Sets`, ma saranno disponibili metriche `Total Operations` che riflettono l'utilizzo della cache per le operazioni di pubblicazione/sottoscrizione. |
| Memoria utilizzata |La quantità di memoria cache utilizzata per le coppie chiave/valore nella cache in MB durante l'intervallo di report specificato. Questo valore esegue il mapping a `used_memory` dal comando INFO di Redis. Non include i metadati o la frammentazione. |
| Memoria utilizzata RSS |La quantità di memoria cache utilizzata in MB durante l'intervallo di report specificato, comprese la frammentazione e i metadati. Questo valore esegue il mapping a `used_memory_rss` dal comando INFO di Redis. |
| CPU |L'utilizzo della CPU del server Cache Redis di Azure come percentuale durante l'intervallo di report specificato. Questo valore è associato al contatore delle prestazioni `\Processor(_Total)\% Processor Time` del sistema operativo. |
| Lettura da cache |La quantità di dati letti dalla cache in megabyte al secondo (MB/s) durante l'intervallo di report specificato. Questo valore è derivato dalle schede di interfaccia di rete che supportano la macchina virtuale che ospita la cache, e non è specifico di Redis. **Questo valore corrisponde alla larghezza di banda della rete usata da questa cache. Se si desidera impostare avvisi per i limiti della larghezza di banda della rete lato server, usare questo contatore `Cache Read`. Per i limiti della larghezza di banda osservati nei diversi piani tariffari e nelle varie dimensioni della cache, vedere [questa tabella](cache-faq.md#cache-performance).** |
| Scrittura nella cache |La quantità di dati scritti nella cache in megabyte al secondo (MB/s) durante l'intervallo di report specificato. Questo valore è derivato dalle schede di interfaccia di rete che supportano la macchina virtuale che ospita la cache, e non è specifico di Redis. Questo valore corrisponde alla larghezza di banda della rete relativa ai dati inviati alla cache dal client. |

## Come visualizzare le metriche e personalizzare i grafici
È possibile visualizzare una panoramica delle metriche per la cache nel pannello **Metriche Redis**. Per accedere al pannello **Metriche Redis** selezionare **Tutte le impostazioni** > **Metriche Redis**.

![Metriche Redis][redis-cache-redis-metrics]

Il pannello **Metriche Redis** contiene i seguenti grafici.

| Grafico delle metriche Redis | Metriche visualizzate |
| --- | --- |
| Lettura della cache e scrittura della cache |Lettura da cache |
| Scrittura nella cache | |
| Client connessi |Client connessi |
| Riscontri e mancati riscontri |Riscontri cache |
| Mancati riscontri nella cache | |
| Totale comandi |Totale operazioni |
| Operazioni Get e operazioni Set |Operazioni Get |
| Operazioni Set | |
| Utilizzo di CPU |CPU |
| Utilizzo della memoria |Memoria utilizzata |
| Memoria utilizzata RSS | |
| Carico server Redis |Carico server |
| Conteggio chiavi |Totale chiavi |
| Chiavi rimosse | |
| Chiavi scadute | |

Per una visualizzazione più dettagliata delle metriche in un grafico specifico e per personalizzare il grafico, fare clic sul grafico nel pannello **Metriche Redis** per mostrare il relativo pannello **Metrica**.

![Blade delle metriche][redis-cache-metric-blade]

Gli eventuali avvisi impostati sulle metriche visualizzate da un grafico sono elencati nella parte inferiore del pannello **Metrica** per tale grafico.

Per aggiungere o rimuovere metriche o modificare l'intervallo di report, scegliere **Modifica grafico**.

Per aggiungere o rimuovere le metriche dal grafico, selezionare la casella di controllo accanto al nome della metrica. Per modificare l'intervallo del report, scegliere l'intervallo desiderato. Per modificare il **tipo di grafico**, selezionare lo stile desiderato. Dopo aver effettuate le modifiche desiderate, fare clic su **Salva**.

![Modifica grafico][redis-cache-edit-chart]

Quando si fa clic su **Salva** le modifiche vengono mantenute fino a quando si esce dal pannello **Metrica**. A un accesso successivo al blade, verranno visualizzate le metriche e l'intervallo di tempo originali. Per ulteriori informazioni sulla personalizzazione dei grafici, vedere [Monitorare la metrica di servizio](../azure-portal/insights-how-to-customize-monitoring.md).

Per visualizzare le metriche per un periodo di tempo specifico in un grafico, passare con il mouse su una delle barre o dei punti specifici del grafico corrispondenti al momento desiderato; verrà visualizzata la metrica per tale intervallo.

![Visualizza dettagli grafico][redis-cache-view-chart-details]

## Come monitorare una cache Premium con il clustering
Le cache Premium con il [clustering](cache-how-to-premium-clustering.md) abilitato possono avere fino a 10 partizioni. Ogni partizione ha le proprie metriche e queste metriche vengono aggregate per fornire metriche per la cache nel suo complesso. In ogni metrica sono incluse due versioni. Una metrica misura la prestazione per l'intera cache e una seconda versione della metrica che include `(Shard 0-9)` nel nome misura la prestazione per una singola partizione in una cache. Se ad esempio una cache ha 3 partizioni, `Cache Hits` è la quantità totale di riscontri per l'intera cache e `Cache Hits (Shard 2)` rappresenta le occorrenze di tale partizionamento della cache.

Ogni grafico di monitoraggio visualizza le metriche di primo livello per la cache insieme alle metriche per ciascuna partizione della cache.

![Monitoraggio][redis-cache-premium-monitor]

Passando il mouse sopra i punti dati, vengono visualizzati i dettagli del punto corrispondente nel tempo.

![Monitoraggio][redis-cache-premium-point-summary]

I valori maggiori in genere sono i valori aggregati per la cache, mentre i valori inferiori sono le singole metriche per la partizione. Si noti che in questo esempio sono presenti tre partizioni e i riscontri nella cache sono distribuiti uniformemente tra le partizioni.

![Monitoraggio][redis-cache-premium-point-shard]

Per vedere maggiori dettagli, fare clic sul grafico per visualizzare una visualizzazione espansa nel pannello **Metrica**.

![Monitoraggio][redis-cache-premium-chart-detail]

Per impostazione predefinita, ogni grafico include il contatore delle prestazioni della cache di primo livello, oltre ai contatori delle prestazioni per le singole partizioni. È possibile personalizzare queste nel pannello **Modifica grafico**.

![Monitoraggio][redis-cache-premium-edit]

Per altre informazioni sui contatori delle prestazioni disponibili, vedere [Metriche disponibili e intervalli di report](#available-metrics-and-reporting-intervals).

## Operazioni e avvisi
La sezione **Operazioni** del pannello **Cache Redis** presenta le sezioni **Eventi** e **Regole di avviso**.

![Operazioni][redis-cache-operations-events]

Per visualizzare un elenco di operazioni della cache recenti, fare clic sul grafico **eventi** per visualizzare il pannello **Eventi**. Tra gli esempi di operazioni sono inclusi il recupero e la rigenerazione delle chiavi di accesso e l’attivazione e la risoluzione delle regole di avviso. Per ulteriori informazioni su ciascun evento, fare clic sull'evento nel pannello **Eventi**.

Per ulteriori informazioni sugli eventi, vedere [Visualizzare eventi e log di controllo](../azure-portal/insights-debugging-with-events.md).

Nella sezione **Regole di avviso** viene visualizzato il numero di avvisi per l'istanza della cache. Una regola di avviso consente di monitorare l'istanza di cache e ricevere un messaggio di posta elettronica ogni volta che un determinato valore di metrica raggiunge la soglia definita nella regola.

Le regole di avviso vengono valutate ogni cinque minuti circa, e quando viene attivata una regola di avviso, vengono inviate eventuali notifiche configurate. Le attivazioni e le notifiche delle regole di avviso non vengono elaborate istantaneamente; potrebbe verificarsi un ritardo di alcuni minuti prima che una regola di avviso sia attivata e vengano inviate le notifiche.

Le regole di avviso possono essere visualizzate e impostate dal pannello **Metrica** per uno specifico grafico di monitoraggio o dal pannello **Regole di avviso**.

Le regole di avviso dispongono delle seguenti proprietà.

| Proprietà della regola di avviso | Descrizione |
| --- | --- |
| Risorsa |La risorsa viene valutata mediante la regola di avviso. Quando si crea una regola di avviso da una cache Redis, la cache è la risorsa. |
| Name |Nome che identifica in modo univoco la regola di avviso nell'istanza corrente della cache. |
| Descrizione |Descrizione facoltativa della regola di avviso. |
| Metrica |La metrica da monitorare tramite la regola di avviso. Per un elenco di metriche della cache, vedere le metriche disponibili e gli intervalli di report. |
| Condizione |L'operatore di condizione per la regola di avviso. È possibile scegliere tra: maggiore di, maggiore di o uguale a, minore di, minore di o uguale a |
| Soglia |Il valore utilizzato per il confronto con la metrica che utilizza l'operatore specificato dalla proprietà condizione. A seconda della metrica, questo valore potrebbe essere in byte al secondo, byte, % o conteggio. |
| Periodo |Specifica il periodo in cui viene utilizzato il valore medio della metrica per il confronto delle regole di avviso. Ad esempio, se il periodo è Nell'ultima ora, per il confronto viene utilizzato il valore medio della metrica nell'intervallo di ore precedenti. Se si desidera ricevere una notifica quando viene raggiunta la soglia a causa di un picco nell'attività, è indicato un periodo più breve. Per ricevere una notifica quando viene rilevata un’attività sostenuta superiore alla soglia, utilizzare un periodo più lungo. |
| Invio di un messaggio di posta elettronica al servizio e ai coamministratori |Se impostata su true, quando viene attivato l’avviso, viene inviato un messaggio di posta elettronica all’amministratore del servizio e al coamministratore. |
| E-mail dell’amministratore aggiuntivo |Indirizzo e-mail facoltativo per consentire a un amministratore aggiuntivo di ricevere una notifica quando viene attivato l'avviso. |

Viene inviata solo una notifica per attivazione delle regole di avviso. Quando viene superata la soglia per una regola e viene inviata la notifica, la regola non viene valutata nuovamente finché non scende al di sotto della soglia. Se la metrica successivamente supera la soglia, l'avviso viene riattivato e viene inviata una nuova notifica.

Per visualizzare tutte le regole di avviso per l’istanza della cache in uso, fare clic su **Regole di avviso** nel pannello **Cache Redis**. Per visualizzare solo le regole di avviso che utilizzano una metrica specifica, andare al pannello **Metrica** per il grafico che contiene tale metrica.

![Regole di avviso][redis-cache-alert-rules]

Per aggiungere una regola di avviso, fare clic su **Aggiungi avviso** dal pannello **Metrica** o dal pannello **Regole di avviso**.

Immettere i criteri della regola desiderati nel pannello **Aggiungi un avviso** e fare clic su **OK**.

![Aggiungi regola di avviso][redis-cache-add-alert]

> [!NOTE]
> Quando viene creata una regola di avviso facendo clic su **Aggiungi avviso** dal pannello **Metrica**, nell’elenco a discesa **Metrica** sono disponibili solo le metriche visualizzate sul grafico in tale pannello. Quando viene creata una regola di avviso facendo clic su **Aggiungi avviso** dal pannello **Regole di avviso**, nell’elenco a discesa **Metrica** sono disponibili tutte le metriche della cache.
> 
> 

Una volta salvata, una regola di avviso viene visualizzata nel pannello **Regole di avviso** nonché nel pannello **Metrica** per i grafici in cui viene visualizzata la metrica utilizzata nella regola di avviso. Per modificare una regola di avviso, fare clic sul nome della regola di avviso per visualizzare il pannello **Modifica regola**. Il pannello **Modifica regola** consente di modificare le proprietà della regola, eliminare o disabilitare la regola di avviso o riattivare la regola se è stata precedentemente disabilitata.

> [!NOTE]
> Affinché le modifiche apportate alle proprietà della regola vengano riflesse nel pannello **Regole di avviso** o nel pannello **Metrica** sono necessari alcuni minuti.
> 
> 

Quando viene attivata una regola di avviso, a seconda della configurazione della regola di avviso viene inviato un messaggio di posta elettronica e viene visualizzata un'icona di avviso in **Regole di avviso** nel pannello **Cache Redis**.

Una regola di avviso viene considerata da risolvere quando la condizione di avviso non restituisce più il valore true. Una volta risolta la condizione della regola di avviso, l'icona dell’avviso viene sostituita con un segno di spunta. Per informazioni dettagliate sulle attivazioni degli avvisi e sulle risoluzioni, fare clic su **Eventi** nel pannello **Cache Redis** per visualizzare gli eventi nel pannello **Eventi**.

Per ulteriori informazioni sugli avvisi in Azure, vedere [Ricevere notifiche di avviso](../azure-portal/insights-receive-alert-notifications.md).

## Metriche nel pannello Cache Redis
Il pannello **Cache Redis** visualizza le seguenti categorie di metriche.

* [Grafici di monitoraggio](#monitoring-charts)
* [Grafici di utilizzo](#usage-charts)

### Grafici di monitoraggio
La sezione **Monitoraggio** dispone dei grafici **Riscontri e mancati riscontri**, **Operazioni Get e operazioni Set**, **Connessioni**, e **Totale comandi**.

![Grafici di monitoraggio][redis-cache-monitoring-part]

Nel grafico **Monitoraggio** vengono visualizzate le metriche seguenti.

| Grafico di monitoraggio | Metriche della cache |
| --- | --- |
| Riscontri e mancati riscontri |Riscontri cache |
| Mancati riscontri nella cache | |
| Operazioni Get e operazioni Set |Operazioni Get |
| Operazioni Set | |
| Connessioni |Client connessi |
| Totale comandi |Totale operazioni |

Per informazioni sulla visualizzazione delle metriche e sulla personalizzazione dei singoli grafici in questa sezione, vedere la sezione [Come visualizzare le metriche e personalizzare i grafici delle metriche](#how-to-view-metrics-and-customize-charts) di seguito.

### Grafici di utilizzo
Nella sezione **Utilizzo** sono disponibili i grafici **Carico server Radis**, **Utilizzo della memoria**, **Larghezza di banda della rete** e **Utilizzo della CPU** nonché **Livello di prezzo** per l’istanza della cache.

![Grafici di utilizzo][redis-cache-usage-part]

In **Livello di prezzo** è possibile visualizzare il livello di prezzo della cache nonché [scalare](cache-how-to-scale.md) la cache a un livello di prezzo diverso.

Il grafico **Utilizzo** consente di visualizzare le metriche seguenti.

| Grafico di utilizzo | Metriche della cache |
| --- | --- |
| Carico server Redis |Carico server |
| Utilizzo della memoria |Memoria utilizzata |
| Larghezza di banda della rete |Scrittura nella cache |
| Utilizzo di CPU |CPU |

Per informazioni sulla visualizzazione delle metriche e sulla personalizzazione dei singoli grafici in questa sezione, vedere la sezione [Come visualizzare le metriche e personalizzare i grafici delle metriche](#how-to-view-metrics-and-customize-charts) di seguito.

<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png

<!---HONumber=AcomDC_0831_2016-->