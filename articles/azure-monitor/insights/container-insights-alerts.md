---
title: Creare avvisi sulle prestazioni con Monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come creare avvisi personalizzati di Azure basati su query di log per l'utilizzo di memoria e CPU da Monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238840"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Come configurare avvisi in Monitoraggio di Azure per i problemi di prestazioni dei contenitori
Monitoraggio di Azure per i contenitori monitora le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. 

Questo articolo descrive come abilitare gli avvisi quando l'utilizzo di memoria e processore nei nodi del cluster supera la soglia definita.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Creare un avviso sull'utilizzo di CPU o memoria nel cluster
Per generare un avviso quando l'utilizzo di CPU o memoria è elevato per un cluster, si crea una regola di avviso Unità di misura della metrica basata su query di log specificate. La query confronta un valore datetime a quello attuale usando l'operatore `now` e torna indietro di un'ora. Tutte le date archiviate da Monitoraggio di Azure per i contenitori sono in formato UTC.  

Prima di iniziare, se non ha familiarità con gli avvisi in Monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure](../platform/alerts-overview.md). Per altre informazioni sugli avvisi usando le query di log, vedere [Avvisi del log in Monitoraggio di Azure](../platform/alerts-unified-log.md)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Monitoraggio** dal riquadro a sinistra nel portale di Azure. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.    
3. Nella scheda **Cluster monitorati** selezionare un cluster dall'elenco facendo clic sul relativo nome.
4. Nel riquadro a sinistra, sotto la sezione **Monitoraggio** selezionare **Log** per aprire la pagina dei log di Monitoraggio di Azure, che consente di scrivere ed eseguire query di Azure Log Analytics.
5. Nella pagina **Log** fare clic su **+ Nuova regola di avviso**.
6. Nella sezione **Condizione** fare clic sulla condizione predefinita per i log personalizzati **Ogni volta che l'operazione di ricerca log personalizzata è <logic undefined>** . Il tipo di segnale **ricerca log personalizzata** viene selezionato automaticamente, perché la creazione della regola di avviso è stata avviata direttamente dalla pagina dei log di Monitoraggio di Azure.  
7. Incollare una delle query di seguito nel campo **Query di ricerca**. La query seguente calcola l'utilizzo medio della CPU come media dell'utilizzo di CPU dei nodi membro ogni minuto.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    La query seguente calcola l'utilizzo medio di memoria come media dell'utilizzo di memoria dei nodi membro ogni minuto.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Configurare l'avviso con le informazioni seguenti:

    a. Nell'elenco a discesa **In base a** selezionare **Unità di misura della metrica**. Un'unità di misura della metrica creerà un avviso per ogni oggetto nella query con un valore che supera la soglia specificata.  
    b. Per **Condizione** selezionare **Maggiore di** e immettere **75** come **Soglia** baseline iniziale o immettere un valore che soddisfi i propri criteri.  
    c. Nella sezione **Attiva l'avviso in base a** selezionare **Violazioni consecutive**, quindi nell'elenco a discesa selezionare **Maggiore di** e immettere il valore **2**.  
    d. Nella sezione **Valutata in base a** modificare il valore **Periodo** in 60 minuti. La regola verrà eseguita ogni cinque minuti e restituirà i record creati nell'ultima ora a partire dall'ora corrente. Se si imposta il periodo di tempo su una finestra più ampia, si tiene conto del potenziale di latenza dei dati e si garantisce che la query restituisca dati per evitare un falso negativo in cui l'avviso non viene mai visualizzato. 

9. Fare clic su **Fine** per completare la regola di avviso.
10. Specificare un nome dell'avviso nel campo **Nome regola di avviso**. In **Descrizione** specificare i dettagli dell'avviso e selezionare una gravità appropriata nelle opzioni disponibili.
11. Per attivare immediatamente la regola di avviso alla creazione, accettare il valore predefinito di **Abilita regola alla creazione**.
12. Per l'ultimo passaggio occorre selezionare un nuovo **Gruppo di azioni**, che assicura che vengano eseguite le stesse azioni ogni volta che viene attivato un avviso e che può essere usato per ogni regola definita. Configurare in base al modo in cui il reparto IT o DevOps gestisce gli eventi imprevisti. 
13. Fare clic su **Crea regola di avviso** per completare la creazione della regola di avviso. L'esecuzione inizia immediatamente.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare alcuni [esempi di query di log](container-insights-analyze.md#search-logs-to-analyze-data) per informazioni sulle query predefinite o esempi per valutare e usare o personalizzare per altri scenari di avviso. 
* Per altre informazioni su come usare Monitoraggio di Azure e monitorare altri aspetti del cluster del servizio Azure Kubernetes, vedere [Visualizzare l'integrità del servizio Kubernetes di Azure](container-insights-analyze.md)
