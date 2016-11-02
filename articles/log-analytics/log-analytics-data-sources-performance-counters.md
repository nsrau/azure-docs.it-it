<properties 
   pageTitle="Contatori delle prestazioni di Windows e Linux in Log Analytics | Microsoft Azure"
   description="I contatori delle prestazioni vengono raccolti da Log Analytics per analizzare le prestazioni degli agenti Windows e Linux.  Questo articolo descrive come configurare la raccolta di contatori delle prestazioni per gli agenti Windows e Linux, i cui dettagli vengono archiviati nel repository OMS, e come analizzarli nel portale di OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />


# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Origini dati per le prestazioni di Windows e Linux in Log Analytics 

I contatori delle prestazioni in Windows e Linux forniscono informazioni dettagliate sulle prestazioni di componenti hardware, sistemi operativi e applicazioni.  Log Analytics può raccogliere i contatori delle prestazioni a intervalli frequenti per l'analisi NRT (quasi in tempo reale) e l'aggregazione dei dati sulle prestazioni per l'analisi e la creazione di report più a lungo termine.

![Contatori delle prestazioni](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurazione dei contatori delle prestazioni

Configurare i contatori delle prestazioni nel [menu Dati in Impostazioni di Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Quando si configurano i contatori delle prestazioni di Windows o Linux per la prima volta per una nuova area di lavoro OMS, è possibile creare rapidamente numerosi contatori comuni.  Viene visualizzato l'elenco dei contatori con le caselle di controllo corrispondenti.  Assicurarsi di aver selezionato tutti i contatori da creare e quindi fare clic su **Aggiungi i contatori delle prestazioni selezionati**.

![Configurare i contatori delle prestazioni di Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Seguire questa procedura per aggiungere un nuovo contatore delle prestazioni Windows da raccogliere.

1. Digitare il nome del contatore nella casella di testo, usando il formato *oggetto(istanza)\contatore*.  Quando si inizia a digitare, viene visualizzato un elenco di contatori comuni corrispondenti.  È possibile selezionare un contatore nell'elenco o digitare quello desiderato.  Per restituire tutte le istanze per un contatore specifico, specificare *oggetto\contatore*. 
2. Fare clic su **+** o premere **INVIO** per aggiungere il contatore all'elenco.
3. Quando si aggiunge un contatore, per **Intervallo di campionamento** verrà usato il valore predefinito di 10 secondi.  Per ridurre i requisiti di spazio di archiviazione dei dati sulle prestazioni raccolti, è possibile impostare questa opzione su un valore più alto, fino a un massimo di 1800 secondi (30 minuti).
4. Dopo aver aggiunto i contatori, fare clic sul pulsante **Salva** nella parte superiore della schermata per salvare la configurazione.

![Configurare i contatori delle prestazioni di Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Seguire questa procedura per aggiungere un nuovo contatore delle prestazioni Linux da raccogliere.

1. Per impostazione predefinita, viene eseguito automaticamente il push di tutte le modifiche di configurazione in tutti gli agenti.  Per gli agenti Linux, viene inviato un file di configurazione all'agente di raccolta dati Fluentd.  Per modificare questo file manualmente in ogni agente Linux, deselezionare la casella *Applica la configurazione seguente alle macchine virtuali Linux*.
2. Digitare il nome del contatore nella casella di testo, usando il formato *oggetto(istanza)\contatore*.  Quando si inizia a digitare, viene visualizzato un elenco di contatori comuni corrispondenti.  È possibile selezionare un contatore nell'elenco o digitare quello desiderato.  
2. Fare clic su **+** o premere **INVIO** per aggiungere il contatore all'elenco di altri contatori per l'oggetto.
3. Tutti i contatori per un oggetto usano lo stesso **intervallo di campionamento**.  Il valore predefinito è 10 secondi.  Per ridurre i requisiti di spazio di archiviazione dei dati sulle prestazioni raccolti, è possibile impostare questa opzione su un valore più alto, fino a un massimo di 1800 secondi (30 minuti).
4. Dopo aver aggiunto i contatori, fare clic sul pulsante **Salva** nella parte superiore della schermata per salvare la configurazione.

## <a name="data-collection"></a>Raccolta dei dati

Log Analytics raccoglierà tutti i contatori delle prestazioni specificati in base all'intervallo di campionamento definito in tutti gli agenti in cui è installato il contatore.  I dati non vengono aggregati e i dati non elaborati sono disponibili in tutte le visualizzazioni di ricerca log per la durata specificata dalla sottoscrizione di OMS.


## <a name="performance-record-properties"></a>Proprietà dei record delle prestazioni

Il tipo dei record delle prestazioni è **Perf** e le proprietà sono elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Computer         | Computer da cui è stato raccolto l'evento. |
| CounterName      | Nome del contatore delle prestazioni. |
| CounterPath      | Percorso completo del contatore nel formato \\\\\<Computer>\\oggetto(istanza)\\contatore. |
| CounterValue     | Valore numerico del contatore.  |
| InstanceName     | Nome dell'istanza dell'evento.  Vuoto se l'istanza non è presente. |
| ObjectName       | Nome dell'oggetto prestazioni |
| SourceSystem  | Tipo di agente da cui sono stati raccolti i dati. <br> OpsManager: agente Windows, con connessione diretta o SCOM <br>  Linux – Tutti gli agenti Linux  <br>  AzureStorage: Diagnostica di Azure |
| TimeGenerated       | Data e ora di campionamento dei dati. |


## <a name="sizing-estimates"></a>Stime di dimensionamento

 Secondo una stima approssimativa per la raccolta di un determinato contatore a intervalli di 10 secondi, le dimensioni equivalgono a circa 1 MB al giorno per istanza.  Con la formula seguente è quindi possibile stimare i requisiti di spazio di archiviazione per un contatore specifico.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Ricerche di record delle prestazioni nei log

La tabella seguente mostra alcuni esempi di ricerche nei log che recuperano i record delle prestazioni.

| Query | Descrizione |
|:--|:--|
| Type=Perf | Tutti i dati sulle prestazioni |
| Type=Perf Computer="Computer" | Tutti i dati sulle prestazioni da un computer specifico |
| Type=Perf CounterName="Lunghezza corrente coda del disco" | Tutti i dati sulle prestazioni da un contatore specifico |
| Type=Perf (ObjectName=Processor) CounterName="% tempo processore" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU  by Computer | Utilizzo medio della CPU per tutti i computer |
| Type=Perf (CounterName="% tempo processore") &#124;  measure max(Max) by Computer | Utilizzo massimo della CPU per tutti i computer |
| Type=Perf ObjectName=LogicalDisk CounterName="Lunghezza corrente coda del disco" Computer="Nome computer" &#124; measure Avg(Average) by InstanceName | Lunghezza media della coda del disco corrente per tutte le istanze di un computer specifico |
| Type=Perf CounterName="Trasferimenti disco/sec" &#124; measure percentile95(Average) by Computer | 95° percentile di trasferimenti disco al secondo per tutti i computer |
| Type=Perf CounterName="% tempo processore" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR | Utilizzo orario medio della CPU per tutti i computer |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR | 70° percentile orario di ogni contatore percentuale % per un computer specifico |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR | Utilizzo CPU orario medio, minimo, massimo e 75° percentile per un computer specifico |

## <a name="viewing-performance-data"></a>Visualizzazione dei dati sulle prestazioni

Quando si esegue una ricerca nei log per i dati sulle prestazioni, per impostazione predefinita viene aperta la visualizzazione **Log** .  Per visualizzare i dati sotto forma di grafico, fare clic su **Metriche**.  Per una visualizzazione grafica dettagliata, fare clic su **+** accanto a un contatore.  

![Visualizzazione Metriche compressa](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Se l'intervallo di tempo selezionato è di 6 ore o meno, il grafico viene aggiornato ogni pochi secondi.  I dati in tempo reale vengono visualizzati sul lato destro del grafico in azzurro.

![Visualizzazione Metriche espansa con dati dinamici](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Per aggregare i dati sulle prestazioni in una ricerca log, vedere [On-demand metric aggregation and visualization in OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/) (Aggregazione metrica su richiesta e visualizzazione in OMS).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [ricerche nei log](log-analytics-log-searches.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
- Esportare i dati raccolti in [Power BI](log-analytics-powerbi.md) per altre opzioni di visualizzazione e analisi.


<!--HONumber=Oct16_HO2-->


