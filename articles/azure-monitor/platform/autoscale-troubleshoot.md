---
title: Risoluzione dei problemi di scalabilità automatica di AzureTroubleshooting Azure autoscale
description: Tenere traccia dei problemi relativi alla scalabilità automatica di Azure usati in Service Fabric, Macchine virtuali, app Web e servizi cloud.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751344"
---
# <a name="troubleshooting-azure-autoscale"></a>Risoluzione dei problemi di scalabilità automatica di AzureTroubleshooting Azure autoscale
 
La scalabilità automatica di Monitoraggio di Azure consente di disporre della quantità di risorse in esecuzione corretta per gestire il carico sull'applicazione. Consente di aggiungere risorse per gestire gli aumenti di carico e anche risparmiare denaro rimuovendo le risorse che sono inattive. È possibile eseguire la scalabilità in base a una pianificazione, data e ora fissa o metrica della risorsa scelta. Per ulteriori informazioni, vedere [Cenni preliminari sulla scalabilità automatica](autoscale-overview.md).

Il servizio di scalabilità automatica fornisce metriche e log per comprendere quali azioni di scalabilità si sono verificate e la valutazione delle condizioni che hanno portato a tali azioni. Puoi trovare le risposte a domande come:

- Perché il mio servizio è scalato orizzontalmente o in
- Perché il mio servizio non è scalato?
- Perché un'azione di scalabilità automatica ha avuto esito negativo?
- Perché un'azione di scalabilità automatica richiede tempo per la scalabilità?
  
## <a name="autoscale-metrics"></a>Metriche di scalabilità automatica

La scalabilità automatica fornisce [quattro metriche](metrics-supported.md#microsoftinsightsautoscalesettings) per comprenderne il funzionamento. 

- **Valore metrico osservato:** valore della metrica su cui si è scelto di eseguire l'azione di scalabilità, come visto o calcolato dal motore di scalabilità automatica. Poiché una singola impostazione di scalabilità automatica può avere più regole e quindi più origini metriche, è possibile filtrare usando "origine metrica" come dimensione.
- **Soglia metrica:** la soglia impostata per eseguire l'azione di scalabilità. Poiché una singola impostazione di scalabilità automatica può avere più regole e quindi più origini metriche, è possibile filtrare utilizzando "regola metrica" come dimensione.
- **Capacità osservata:** numero attivo di istanze della risorsa di destinazione visualizzate dal motore di scalabilità automatica.
- **Azioni di scalabilità avviate** - Il numero di azioni di aumento e riduzione delle istanze avviate dal motore di scalabilità automatica. È possibile filtrare in base alla scalabilità orizzontale rispetto alla scalabilità verticale nelle azioni.

È possibile utilizzare [Esplora metriche](metrics-getting-started.md) per tracciare le metriche di cui sopra in un'unica posizione. Il grafico dovrebbe mostrare:

  - la metrica effettiva
  - la metrica vista/calcolata dal motore di scalabilità automatica
  - la soglia per un'azione di scala
  - il cambiamento di capacità 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Esempio 1 - Analisi di una regola di scalabilità automatica sempliceExample 1 - Analyzing a simple autoscale rule 

Abbiamo una semplice impostazione di scalabilità automatica per un set di scalabilità di macchine virtuali che:We have a simple autoscale setting for a virtual machine scale set that:

- scalabilità orizzontale quando la percentuale media di CPU di un set è superiore al 70% per 10 minuti 
- quando la percentuale di CPU del set è inferiore al 5% per più di 10 minuti. 

Esaminiamo le metriche dal servizio di scalabilità automatica.
 
![Esempio di percentuale di percentuale di percentuale di set di scalabilità delle macchine virtuali](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Esempio di percentuale di percentuale di percentuale di set di scalabilità delle macchine virtuali](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Figura 1a - Metrica percentuale della CPU per il set di scalabilità di macchine virtuali e la metrica Valore metrico osservato per la scalabilità automatica***

![Soglia metrica e capacità osservata](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figura 1b - Soglia metrica e capacità osservata***

Nella figura 1b, la **soglia metrica** (linea blu chiaro) per la regola di scalabilità orizzontale è 70.  La **Capacità Osservata** (linea blu scuro) mostra il numero di istanze attive, che attualmente è 3. 

> [!NOTE]
> È necessario filtrare la **soglia metrica** in base alla regola di scalabilità orizzontale (aumento) della regola di attivazione metrica per visualizzare la soglia di scalabilità orizzontale e in base alla regola di scalabilità verticale (diminuzione). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Esempio 2 - Scalabilità automatica avanzata per un set di scalabilità di macchine virtualiExample 2 - Advanced autoscaling for a virtual machine scale set

Abbiamo un'impostazione di scalabilità automatica che consente a una risorsa del set di scalabilità di macchine virtuali di scalare orizzontalmente in base alla propria metrica **Flussi in uscita**. Si noti che viene selezionata l'opzione **di divisione della metrica per numero di istanze** per la soglia della metrica. 

La regola dell'azione di scalabilità è:The scale action rule is: 

Se il valore di **Flusso in uscita per istanza** è maggiore di 10, il servizio di scalabilità automatica deve aumentare di 1 istanza. 

In questo caso, il valore della metrica osservata dal motore di scalabilità automatica viene calcolato come valore della metrica effettiva diviso per il numero di istanze. Se il valore della metrica osservata è inferiore alla soglia, non viene avviata alcuna azione di scalabilità orizzontale. 
 
![Esempio di grafici delle metriche di scalabilità automatica del set di scalabilità automatica delle macchine virtualiVirtual machine scale set autoscale metrics charts example](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Esempio di grafici delle metriche di scalabilità automatica del set di scalabilità automatica delle macchine virtualiVirtual machine scale set autoscale metrics charts example](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Figura 2 - Esempio di grafici di metriche di scalabilità automatica del set di scalabilità automatica delle macchine virtualiFigure 2 - Virtual machine scale set autoscale metrics charts example***

Nella figura 2 è possibile visualizzare due grafici metrici. 

Il grafico nella parte superiore mostra il valore effettivo della metrica **Flussi in uscita.** Il valore effettivo è 6. 

Il grafico in basso mostra alcuni valori. 
 - Il **valore Metrica osservata** (azzurro) è 3 perché sono presenti 2 istanze attive e 6 divise per 2. 
 - La **capacità osservata** (viola) mostra il numero di istanze visualizzato dal motore di scalabilità automatica. 
 - La **soglia metrica** (verde chiaro) è impostata su 10.The Metric Threshold (light green) is set to 10. 

Se sono presenti più regole di azione di scalabilità, è possibile usare la divisione o l'opzione **Aggiungi filtro** nel grafico Esplora metriche per esaminare la metrica in base a un'origine o a una regola specifica. Per ulteriori informazioni sulla suddivisione di un grafico metrico, vedere [Funzionalità avanzate dei grafici metrici - divisioneFor](metrics-charts.md#apply-splitting-to-a-chart) more information on splitting a metric chart, see Advanced features of metric charts - splitting

## <a name="example-3---understanding-autoscale-events"></a>Esempio 3 - Informazioni sugli eventi di scalabilità automaticaExample 3 - Understanding autoscale events

Nella schermata delle impostazioni di scalabilità automatica passare alla scheda **Cronologia di** esecuzione per visualizzare le azioni di scalabilità più recenti. La scheda mostra anche la modifica in **Capacità osservata** nel tempo. Per ulteriori informazioni su tutte le azioni di scalabilità automatica, incluse operazioni quali le impostazioni di scalabilità automatica/aggiornamento, visualizzare il log attività e filtrare in base alle operazioni di scalabilità automatica.

![Cronologia di esecuzione delle impostazioni di scalabilità automaticaAutoscale settings run history](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Scalabilità automatica dei registri risorseAutoscale Resource Logs

Come qualsiasi altra risorsa di Azure, il servizio di scalabilità automatica fornisce [log delle risorse.](platform-logs-overview.md) Esistono due categorie di registri.

- **Valutazioni di scalabilità** automatica: il motore di scalabilità automatica registra le voci di log per ogni singola valutazione della condizione ogni volta che esegue un controllo.  La voce include dettagli sui valori osservati delle metriche, le regole valutate e se la valutazione ha generato un'azione di ridimensionamento o meno.

- **Azioni di scalabilità automatica:** il motore registra gli eventi azione di scalabilità implementata dal servizio di scalabilità automatica e i risultati di tali azioni di scalabilità (esito positivo, errore e quantità di scalabilità osservata dal servizio di scalabilità automatica).

Come con qualsiasi servizio supportato da Monitoraggio di Azure, è possibile usare le impostazioni di diagnostica per instradare questi log:As with any Azure Monitor supported service, you can use [Diagnostic Settings](diagnostic-settings.md) to route these logs:

- nell'area di lavoro di Log Analytics per informazioni dettagliate su analisi
- Hub eventi e quindi a strumenti non Azure
- all'account di archiviazione di Azure per l'archiviazione  

![Impostazioni di diagnostica con scalabilità automaticaAutoscale Diagnostic Settings](media/autoscale-troubleshoot/diagnostic-settings.png)

L'immagine precedente mostra le impostazioni di diagnostica di scalabilità automatica del portale di Azure.The previous picture shows the Azure portal autoscale diagnostic settings. È possibile selezionare la scheda Registri diagnostica/risorse e abilitare la raccolta e il routing dei log. È anche possibile eseguire la stessa azione usando l'API REST, l'interfaccia della riga di comando, PowerShell, i modelli di Resource Manager per impostazioni di diagnostica scegliendo il tipo di risorsa come *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Risoluzione dei problemi tramite la scalabilità automatica 

Per un'esperienza di risoluzione dei problemi ottimale, è consigliabile instradare i log ai log di Monitoraggio di Azure (Log Analytics) tramite un'area di lavoro quando si crea l'impostazione di scalabilità automatica. Questo processo è mostrato nell'immagine nella sezione precedente. È possibile convalidare le valutazioni e ridimensionare meglio le azioni usando Log Analytics.You can validate the evaluations and scale actions better using Log Analytics.

Dopo aver configurato i log di scalabilità automatica da inviare all'area di lavoro di Log Analytics, è possibile eseguire le query seguenti per controllare i log. 

Per iniziare, provare questa query per visualizzare i log di valutazione della scalabilità automatica più recenti:To get started, try this query to view the most recent autoscale evaluation logs:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

In alternativa, provare la query seguente per visualizzare i log delle azioni di scalabilità più recenti:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Utilizzare le sezioni seguenti per queste domande. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Si è verificata un'azione di scala che non mi aspettavo

Eseguire innanzitutto la query per l'azione di scalabilità per trovare l'azione di scalabilità a cui si è interessati. Se si tratta dell'azione di scalabilità più recente, utilizzare la query seguente:If it is the latest scale action, use the following query:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selezionare il campo CorrelationId dal log delle azioni di scalabilità. Usare il CorrelationId per trovare il log di valutazione corretto. Eseguendo la query seguente verranno visualizzate tutte le regole e le condizioni valutate che portano a tale azione di scalabilità.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Quale profilo ha causato un'azione di scalabilità?

Si è verificata un'azione in scala, ma sono presenti regole e profili sovrapposti ed è necessario tenere traccia dei quali hanno causato l'azione. 

Trovare il correlationId dell'azione di scalabilità (come spiegato nell'esempio 1) e quindi eseguire la query nei log di valutazione per ulteriori informazioni sul profilo.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

L'intera valutazione del profilo può anche essere compresa meglio utilizzando la seguente query

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Non si è verificata un'azione di scala

Mi aspettavo un'azione di scala e non si è verificato. Potrebbero non essere presenti eventi di azione di scalabilità o registri.

Esaminare le metriche di scalabilità automatica se si usa una regola di scalabilità basata su metriche. È possibile che il **valore della metrica Osservata** o Capacità osservata non siano quelli previsti e pertanto la regola di scala non è stata **attivata.** Le valutazioni continueranno a essere visualizzate, ma non una regola di scalabilità orizzontale. È anche possibile che il tempo di recupero abbia impedito l'esecuzione di un'azione di scala. 

 Esaminare i log di valutazione della scalabilità automatica durante il periodo di tempo previsto per l'esecuzione dell'azione di scalabilità. Esaminare tutte le valutazioni effettuate e il motivo per cui ha deciso di non attivare un'azione su larga scala.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Azione di scalabilità non riuscita

Potrebbe esserci un caso in cui il servizio di scalabilità automatica ha eseguito l'azione di scalabilità, ma il sistema ha deciso di non ridimensionare o non è riuscito a completare l'azione di scalabilità. Utilizzare questa query per trovare le azioni di scalabilità non riuscite.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Creare regole di avviso per ricevere notifiche su azioni o errori di scalabilità automatica. È inoltre possibile creare regole di avviso per ricevere una notifica sugli eventi di scalabilità automatica.

## <a name="schema-of-autoscale-resource-logs"></a>Schema dei log delle risorse di scalabilità automaticaSchema of autoscale resource logs

Per altre informazioni, vedere Log [delle risorse di scalabilità automaticaFor](autoscale-resource-log-schema.md) more information, see autoscale resource logs

## <a name="next-steps"></a>Passaggi successivi
Leggere le informazioni sulle [procedure consigliate per la scalabilità automatica](autoscale-best-practices.md). 
