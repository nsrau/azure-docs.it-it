---
title: Risoluzione dei problemi di scalabilità automatica di Azure
description: Rilevamento di problemi con la scalabilità automatica di Azure usata in Service Fabric, macchine virtuali, app Web e servizi cloud.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 14e2f79383aa45125ce6955f3611a75ece1df10d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152944"
---
# <a name="troubleshooting-azure-autoscale"></a>Risoluzione dei problemi di scalabilità automatica di Azure
 
La scalabilità automatica di monitoraggio di Azure consente di ottenere la quantità corretta di risorse in esecuzione per gestire il carico dell'applicazione. Consente di aggiungere risorse per gestire gli incrementi di carico e di risparmiare denaro rimuovendo le risorse inattive. È possibile applicare la scalabilità in base a una pianificazione, a una data/ora fissa o a una metrica della risorsa scelta. Per altre informazioni, vedere [Panoramica della scalabilità](autoscale-overview.md)automatica.

Il servizio di scalabilità automatica fornisce le metriche e i log per comprendere quali azioni di ridimensionamento si sono verificate e la valutazione delle condizioni che hanno portato a tali azioni. È possibile trovare risposte a domande come:

- Perché il servizio è stato scalato in orizzontale o in?
- Perché il servizio non è stato ridimensionato?
- Perché un'azione di scalabilità automatica ha esito negativo?
- Perché un'azione di scalabilità automatica richiede tempo per la scalabilità?
  
## <a name="autoscale-metrics"></a>Metriche di scalabilità automatica

La scalabilità automatica fornisce [quattro metriche](metrics-supported.md#microsoftinsightsautoscalesettings) per comprenderne il funzionamento. 

- **Valore della metrica osservato** : valore della metrica su cui si è scelto di eseguire l'azione di ridimensionamento, come visualizzato o calcolato dal motore di scalabilità automatica. Poiché una singola impostazione di scalabilità automatica può avere più regole e quindi più origini metriche, è possibile filtrare usando "metrica origine" come dimensione.
- **Soglia metrica** : soglia impostata per eseguire l'azione di ridimensionamento. Poiché una singola impostazione di scalabilità automatica può avere più regole e quindi più origini metriche, è possibile filtrare usando la "regola metrica" come dimensione.
- **Capacità osservata** : numero attivo di istanze della risorsa di destinazione come visualizzato dal motore di scalabilità automatica.
- **Azioni di ridimensionamento avviate** : numero di azioni di scalabilità orizzontale e di riduzione delle prestazioni avviate dal motore di scalabilità automatica. È possibile filtrare in base alle azioni di scalabilità orizzontale e orizzontale.

È possibile usare il [Esplora metriche](metrics-getting-started.md) per eseguire il grafico delle metriche sopra elencate in un'unica posizione. Il grafico dovrebbe mostrare:

  - metrica effettiva
  - metrica come visualizzato/calcolato dal motore di scalabilità automatica
  - soglia per un'azione di ridimensionamento
  - modifica della capacità 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Esempio 1: analisi di una regola di scalabilità automatica semplice 

È presente una semplice impostazione di scalabilità automatica per un set di scalabilità di macchine virtuali che:

- scalabilità orizzontale quando la percentuale media di CPU di un set è superiore al 70% per 10 minuti 
- viene ridimensionato quando la percentuale di CPU del set è inferiore al 5% per più di 10 minuti. 

Esaminiamo le metriche dal servizio di scalabilità automatica.
 
![Esempio di CPU percentuale del set di scalabilità di macchine virtuali](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Esempio di CPU percentuale del set di scalabilità di macchine virtuali](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Figura 1a-percentuale di metrica della CPU per il set di scalabilità di macchine virtuali e la metrica del valore metrica osservato per l'impostazione di scalabilità automatica***

![Soglia metrica e capacità osservata](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figura 1B-soglia metrica e capacità osservata***

Nella figura 1b la **soglia della metrica** (linea blu chiaro) per la regola di scalabilità orizzontale è 70.  La **capacità osservata** (linea blu scuro) Mostra il numero di istanze attive, che è attualmente 3. 

> [!NOTE]
> Per visualizzare la soglia di scalabilità orizzontale e la regola di scalabilità orizzontale (riduzione), è necessario filtrare la **soglia della metrica** per la regola del trigger della dimensione scale out (aumento). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Esempio 2: scalabilità automatica avanzata per un set di scalabilità di macchine virtuali

È presente un'impostazione di scalabilità automatica che consente la scalabilità orizzontale di una risorsa del set di scalabilità di macchine virtuali in base ai **flussi in uscita**della metrica. Si noti che l'opzione **divide metrica per numero di istanze** per la soglia della metrica è selezionata. 

La regola dell'azione di ridimensionamento è: 

Se il valore del **flusso in uscita per istanza** è maggiore di 10, il servizio di scalabilità automatica deve scalare in orizzontale di un'istanza. 

In questo caso, il valore della metrica osservato del motore di scalabilità automatica viene calcolato come valore effettivo della metrica diviso per il numero di istanze. Se il valore della metrica osservato è inferiore alla soglia, non viene avviata alcuna azione di scalabilità orizzontale. 
 
![Esempio di grafici di metriche di scalabilità automatica del set di scalabilità di macchine virtuali](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Esempio di grafici di metriche di scalabilità automatica del set di scalabilità di macchine virtuali](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Figura 2: esempio di grafici delle metriche di scalabilità automatica del set di scalabilità di macchine virtuali***

Nella figura 2 è possibile visualizzare due grafici di metrica. 

Il grafico in alto mostra il valore effettivo della metrica dei **flussi in uscita** . Il valore effettivo è 6. 

Il grafico nella parte inferiore mostra alcuni valori. 
 - Il **valore della metrica osservata** (blu chiaro) è 3 perché sono presenti 2 istanze attive e 6 divise per 2 è 3. 
 - La **capacità osservata** (viola) Mostra il numero di istanze visualizzato dal motore di scalabilità automatica. 
 - La **soglia della metrica** (verde chiaro) è impostata su 10. 

Se sono presenti più regole di azione di ridimensionamento, è possibile usare la suddivisione o l'opzione **Aggiungi filtro** nel grafico di Esplora metriche per esaminare la metrica in base a un'origine o una regola specifica. Per ulteriori informazioni sulla suddivisione di un grafico delle metriche, vedere [Advanced Features of Metric Charts-spliting](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Esempio 3: informazioni sugli eventi di scalabilità automatica

Nella schermata delle impostazioni di scalabilità automatica passare alla scheda **cronologia di esecuzione** per visualizzare le azioni di scala più recenti. La scheda Mostra anche la modifica della **capacità osservata** nel tempo. Per altre informazioni su tutte le azioni di scalabilità automatica, incluse le operazioni di aggiornamento/eliminazione delle impostazioni di scalabilità automatica, visualizzare il log attività e filtrare in base alle operazioni di scalabilità automatica.

![Cronologia di esecuzione delle impostazioni di scalabilità automatica](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Ridimensionare automaticamente i log delle risorse

Come per qualsiasi altra risorsa di Azure, il servizio di scalabilità automatica fornisce i [log delle risorse](resource-logs-overview.md). Esistono due categorie di log.

- **Valutazioni di scalabilità** automatica: il motore di scalabilità automatica registra le voci di log per ogni singola valutazione di condizione ogni volta che esegue un controllo.  La voce include i dettagli sui valori osservati delle metriche, le regole valutate e se la valutazione ha generato o meno un'azione di scalabilità.

- **Azioni** di scalabilità automatica: il motore registra gli eventi di azione di ridimensionamento avviati dal servizio di scalabilità automatica e i risultati delle operazioni di ridimensionamento (esito positivo, esito negativo e percentuale di ridimensionamento che si sono verificati nel servizio di scalabilità automatica).

Come per qualsiasi servizio supportato da monitoraggio di Azure, è possibile usare [le impostazioni di diagnostica](diagnostic-settings.md) per indirizzare questi log:

- per l'analisi dettagliata dell'area di lavoro Log Analytics
- a hub eventi e quindi a strumenti non di Azure
- nell'account di archiviazione di Azure per l'archiviazione  

![Impostazioni di diagnostica di ridimensionamento automatico](media/autoscale-troubleshoot/diagnostic-settings.png)

L'immagine precedente mostra le impostazioni di diagnostica di ridimensionamento automatico portale di Azure. Qui è possibile selezionare la scheda log di diagnostica e abilitare la raccolta e il routing dei log. È anche possibile eseguire la stessa azione usando l'API REST, l'interfaccia della riga di comando, PowerShell, i modelli Gestione risorse per le impostazioni di diagnostica scegliendo il tipo di risorsa *Microsoft. Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Risoluzione dei problemi usando i log di scalabilità automatica 

Per una migliore esperienza di risoluzione dei problemi, è consigliabile instradare i log ai log di monitoraggio di Azure (Log Analytics) tramite un'area di lavoro quando si crea l'impostazione di scalabilità automatica. Questo processo è illustrato nell'immagine della sezione precedente. È possibile convalidare le valutazioni e le azioni di scalabilità migliori utilizzando Log Analytics.

Dopo aver configurato i log di scalabilità automatica da inviare all'area di lavoro Log Analytics, è possibile eseguire le query seguenti per controllare i log. 

Per iniziare, provare questa query per visualizzare i log di valutazione di scalabilità automatica più recenti:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

In alternativa, provare a eseguire la query seguente per visualizzare i log delle azioni di scala più recenti:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Per queste domande, usare le sezioni seguenti. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Si è verificata un'azione di ridimensionamento non prevista

Eseguire prima di tutto la query per l'azione di ridimensionamento per trovare l'azione di ridimensionamento a cui si è interessati. Se si tratta dell'azione di ridimensionamento più recente, utilizzare la query seguente:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selezionare il campo CorrelationId nel log delle azioni di scalabilità. Usare CorrelationId per trovare il log di valutazione appropriato. Eseguendo la query seguente vengono visualizzate tutte le regole e le condizioni valutate che portano a tale azione di ridimensionamento.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Quale profilo ha causato un'azione di ridimensionamento?

Si è verificata un'azione ridimensionata, ma sono presenti regole e profili sovrapposti ed è necessario tenere traccia dell'azione che ha causato l'azione. 

Trovare l'ID correlazione dell'azione di ridimensionamento, come illustrato nell'esempio 1, e quindi eseguire la query sui log di valutazione per ulteriori informazioni sul profilo.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

L'intera valutazione del profilo può essere riconosciuta anche meglio utilizzando la query seguente

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Non è stata eseguita un'azione di ridimensionamento

È prevista un'azione di ridimensionamento che non si è verificata. Potrebbe non essere presente alcun evento di azione di ridimensionamento o log.

Verificare la metrica di scalabilità automatica se si usa una regola di scalabilità basata sulla metrica. È possibile che il **valore della metrica osservato** o la **capacità osservata** non siano quelli previsti e pertanto la regola di ridimensionamento non è stata attivata. Verranno comunque visualizzate le valutazioni, ma non una regola di scalabilità orizzontale. È anche possibile che il tempo di raffreddamento mantenga un'azione di ridimensionamento. 

 Esaminare i log di valutazione di scalabilità automatica durante il periodo di tempo in cui si prevede che l'azione di ridimensionamento venga eseguita. Esaminare tutte le valutazioni effettuate e il motivo per cui si è deciso di non attivare un'azione di ridimensionamento.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Azione di ridimensionamento non riuscita

Potrebbe verificarsi un caso in cui il servizio di scalabilità automatica ha eseguito l'azione di ridimensionamento, ma il sistema ha deciso di non ridimensionare o di completare l'azione di ridimensionamento. Utilizzare questa query per individuare le azioni di ridimensionamento non riuscite.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Creare regole di avviso per ricevere notifiche sulle azioni di scalabilità automatica o sugli errori. È anche possibile creare regole di avviso per ricevere notifiche sugli eventi di scalabilità automatica.

## <a name="schema-of-autoscale-resource-logs"></a>Schema dei log delle risorse di scalabilità automatica

Per altre informazioni, vedere [ridimensionamento automatico dei log delle risorse](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Passaggi successivi
Leggere le informazioni sulle [procedure consigliate per la scalabilità](autoscale-best-practices.md)automatica. 