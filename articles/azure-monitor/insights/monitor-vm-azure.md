---
title: Monitorare le macchine virtuali di Azure con monitoraggio di Azure
description: Viene descritto come raccogliere e analizzare i dati di monitoraggio dalle macchine virtuali in Azure tramite monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283940"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitoraggio di macchine virtuali di Azure con monitoraggio di Azure
Questo articolo descrive come usare monitoraggio di Azure per raccogliere e analizzare i dati di monitoraggio dalle macchine virtuali di Azure per mantenerne l'integrità. Le macchine virtuali possono essere monitorate per la disponibilità e le prestazioni con monitoraggio di Azure come qualsiasi [altra risorsa di Azure](monitor-azure-resource.md), ma sono univoche da altre risorse, dal momento che è necessario monitorare anche il sistema operativo guest e i carichi di lavoro in esecuzione al suo interno. 

> [!NOTE]
> Questo articolo fornisce una panoramica completa dei concetti e delle opzioni per il monitoraggio delle macchine virtuali in monitoraggio di Azure. Per iniziare a monitorare rapidamente le macchine virtuali senza concentrarsi sui concetti sottostanti, vedere [Guida introduttiva: monitorare una macchina virtuale di Azure con monitoraggio di Azure](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Differenze rispetto ad altre risorse di Azure
Il [monitoraggio delle risorse di Azure con monitoraggio di Azure](monitor-azure-resource.md) descrive i dati di monitoraggio generati dalle risorse di Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e inviare avvisi su questi dati. È possibile raccogliere e agire sugli stessi dati di monitoraggio delle macchine virtuali di Azure con le differenze seguenti:

- Le [metriche della piattaforma](../platform/data-platform-metrics.md) vengono raccolte automaticamente per le macchine virtuali, ma solo per l' [host macchina virtuale](#monitoring-data). È necessario un agente per raccogliere i dati sulle prestazioni dal sistema operativo guest. 
- Le macchine virtuali non generano [log delle risorse](../platform/platform-logs-overview.md) che forniscono informazioni dettagliate sulle operazioni eseguite all'interno di una risorsa di Azure. Usare un agente per raccogliere i dati di log dal sistema operativo guest.
- È possibile creare [le impostazioni di diagnostica](../platform/diagnostic-settings.md) per una macchina virtuale per inviare le metriche della piattaforma ad altre destinazioni, ad esempio l'archiviazione e hub eventi, ma non è possibile configurare queste impostazioni di diagnostica nel portale di Azure. 

## <a name="monitoring-data"></a>Dati di monitoraggio
Le macchine virtuali in Azure in Azure generano [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) illustrate nel diagramma seguente.

![Panoramica](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host macchina virtuale
Le macchine virtuali in Azure generano i dati seguenti per l'host della macchina virtuale, come per le altre risorse di Azure, come descritto in [monitoraggio dei dati](monitor-azure-resource.md#monitoring-data).

- [Metriche della piattaforma](../platform/data-platform-metrics.md) : valori numerici che vengono raccolti automaticamente a intervalli regolari e descrivono un aspetto di una risorsa in un determinato momento. Vengono raccolte le metriche della piattaforma per l'host macchina virtuale, ma è necessaria l'estensione di diagnostica per raccogliere le metriche per il sistema operativo guest.
- [Log attività](../platform/platform-logs-overview.md) : fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (il piano di gestione). Per una macchina virtuale, sono incluse informazioni quali l'avvio e le modifiche apportate alla configurazione.


### <a name="guest-operating-system"></a>Sistema operativo guest
Per raccogliere dati dal sistema operativo guest di una macchina virtuale, è necessario un agente, che viene eseguito localmente in ogni macchina virtuale e invia i dati a monitoraggio di Azure. Sono disponibili più agenti per monitoraggio di Azure, ognuno dei quali raccoglie dati diversi e scrive i dati in posizioni diverse. Ottenere un confronto dettagliato dei diversi agenti in [Panoramica degli agenti di monitoraggio di Azure](../platform/agents-overview.md). 

- [Agente di log Analytics](../platform/agents-overview.md#log-analytics-agent) : disponibile per le macchine virtuali in Azure, in altri ambienti cloud e in locale. Raccoglie i dati nei log di monitoraggio di Azure. Supporta Monitoraggio di Azure per le macchine virtuali e soluzioni di monitoraggio. Si tratta dello stesso agente usato per System Center Operations Manager.
- [Dependency Agent](../platform/agents-overview.md#dependency-agent) : raccoglie i dati relativi ai processi in esecuzione nella macchina virtuale e alle relative dipendenze. Si basa sull'agente di Log Analytics per trasmettere i dati in Azure e supporta le soluzioni Monitoraggio di Azure per le macchine virtuali, Mapping dei servizi e Wire Data 2.0.
- [Estensione di diagnostica di Azure](../platform/agents-overview.md#azure-diagnostics-extension) : disponibile solo per le macchine virtuali di monitoraggio di Azure. Consente di raccogliere i dati in più posizioni, ma principalmente di raccogliere i dati sulle prestazioni Guest nelle metriche di monitoraggio di Azure per le macchine virtuali Windows.
- [Agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) : raccoglie i dati sulle prestazioni dalle macchine virtuali Linux nelle metriche di monitoraggio di Azure.


## <a name="configuration-requirements"></a>Requisiti di configurazione
Per abilitare tutte le funzionalità di monitoraggio di Azure per il monitoraggio di una macchina virtuale, è necessario raccogliere i dati di monitoraggio dall'host della macchina virtuale e dal sistema operativo guest sia alle [metriche di monitoraggio](../platform/data-platform-logs.md) di Azure sia ai log di [monitoraggio di Azure](../platform/data-platform-logs.md). La tabella seguente elenca la configurazione che è necessario eseguire per abilitare questa raccolta. È possibile scegliere di non eseguire tutti questi passaggi in base ai requisiti specifici.

| Passaggio di configurazione | Azioni completate | Funzionalità abilitate |
|:---|:---|:---|
| Nessuna configurazione | -Metriche della piattaforma host raccolte per le metriche.<br>-Log attività raccolto. | -Esplora metriche per l'host.<br>-Avvisi di metrica per l'host.<br>-Avvisi del log attività. |
| [Abilita Monitoraggio di Azure per le macchine virtuali](#enable-azure-monitor-for-vms) | -Log Analytics agente installato.<br>-Agente di dipendenza installato.<br>-Dati sulle prestazioni Guest raccolti nei log.<br>-Dettagli di processi e dipendenze raccolti nei log. | -Grafici delle prestazioni e cartelle di lavoro per i dati sulle prestazioni Guest.<br>-Log query per i dati sulle prestazioni Guest.<br>-Registra gli avvisi per i dati sulle prestazioni Guest.<br>-Mappa delle dipendenze. |
| [Installare l'estensione di diagnostica e l'agente di Telegraf](#enable-diagnostics-extension-and-telegraf-agent) | -Dati sulle prestazioni Guest raccolti nelle metriche. | -Esplora metriche per Guest.<br>-Avvisi di metrica per Guest.  |
| [Configurare Log Analytics area di lavoro](#configure-log-analytics-workspace) | -Eventi raccolti dal Guest. | -Log query per gli eventi Guest.<br>-Registra gli avvisi per gli eventi Guest. |
| [Crea l'impostazione di diagnostica per la macchina virtuale](#collect-platform-metrics-and-activity-log) | -Metriche della piattaforma raccolte nei log.<br>-Log attività raccolto nei log. | -LOQ query per le metriche host.<br>-Registra gli avvisi per le metriche host.<br>-Log query per il log attività.

Ognuno di questi passaggi di configurazione è descritto nelle sezioni seguenti.

### <a name="enable-azure-monitor-for-vms"></a>Abilita Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](vminsights-overview.md) è una [conoscenza](insights-overview.md) di monitoraggio di Azure, lo strumento principale per il monitoraggio delle macchine virtuali in monitoraggio di Azure. Fornisce il valore aggiuntivo seguente sulle funzionalità di monitoraggio standard di Azure.

- Onboarding semplificato dell'agente di Log Analytics e dell'agente di dipendenza per abilitare il monitoraggio di un sistema operativo guest di macchina virtuale e di carichi di lavoro. 
- Grafici delle prestazioni e cartelle di lavoro di tendenza predefinite che consentono di analizzare le metriche delle prestazioni di base dal sistema operativo guest della macchina virtuale.
- Mappa delle dipendenze che consente di visualizzare i processi in esecuzione in ogni macchina virtuale e i componenti interconnessi con altri computer e origini esterne.

![Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/vminsights-01.png)

![Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/vminsights-02.png)


Abilitare Monitoraggio di Azure per le macchine virtuali dall'opzione **Insights** nel menu macchina virtuale del portale di Azure. Per informazioni dettagliate e altri metodi di configurazione, vedere [Enable monitoraggio di Azure per le macchine virtuali Overview](vminsights-enable-overview.md) .

![Abilita Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurare Log Analytics area di lavoro
L'agente Log Analytics usato da Monitoraggio di Azure per le macchine virtuali invia i dati a un' [area di lavoro log Analytics](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). È possibile abilitare la raccolta di dati aggiuntivi relativi alle prestazioni, eventi e altri dati di monitoraggio dall'agente configurando l'area di lavoro Log Analytics. È necessario configurarlo solo una volta, poiché tutti gli agenti che si connettono all'area di lavoro scaricheranno automaticamente la configurazione e inizieranno immediatamente a raccogliere i dati definiti. 

È possibile accedere alla configurazione per l'area di lavoro direttamente da Monitoraggio di Azure per le macchine virtuali selezionando **configurazione area di lavoro** dall' **inizio**. Fare clic sul nome dell'area di lavoro per aprire il relativo menu.

![Configurazione dell'area di lavoro](media/monitor-vm-azure/workspace-configuration.png)

Selezionare **Impostazioni avanzate** dal menu area di lavoro e quindi **dati** per configurare le origini dati. Per gli agenti Windows selezionare **registri eventi di Windows** e aggiungere registri eventi comuni, ad esempio *sistema* e *applicazione*. Per gli agenti Linux selezionare **syslog** e aggiungere le funzionalità comuni, ad esempio *Kern* e *daemon*. Per un elenco delle origini dati disponibili e per informazioni dettagliate sulla relativa configurazione, vedere [origini dati degli agenti in monitoraggio di Azure](../platform/agent-data-sources.md) . 

![Configurare gli eventi](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> È possibile configurare i contatori delle prestazioni da raccogliere dalla configurazione dell'area di lavoro, ma può essere ridondante con i contatori delle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali. Monitoraggio di Azure per le macchine virtuali raccoglie il set più comune di contatori con una frequenza di una volta al minuto. Configurare solo i contatori delle prestazioni che devono essere raccolti dall'area di lavoro se si desidera raccogliere i contatori non già raccolti da Monitoraggio di Azure per le macchine virtuali o se si dispone di query esistenti che utilizzano dati sulle prestazioni.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Abilitare l'estensione di diagnostica e l'agente Telegraf
Monitoraggio di Azure per le macchine virtuali si basa sull'agente Log Analytics che raccoglie i dati in un'area di lavoro Log Analytics. Questo supporta [più funzionalità di monitoraggio di Azure](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , ad esempio [query di log](../log-query/log-query-overview.md), [avvisi del log](../platform/alerts-log.md)e [cartelle di lavoro](../platform/workbooks-overview.md). L' [estensione diagnostica](../platform/diagnostics-extension-overview.md) raccoglie i dati sulle prestazioni dal sistema operativo guest delle macchine virtuali Windows ad archiviazione di Azure e, facoltativamente, invia i dati sulle prestazioni alle [metriche di monitoraggio di Azure](../platform/data-platform-metrics.md). Per le macchine virtuali Linux, l' [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) è necessario per inviare dati alle metriche di Azure.  Questo Abilita altre funzionalità di monitoraggio di Azure, ad esempio gli [avvisi](../platform/alerts-metric.md)di metriche e [Esplora metriche](../platform/metrics-getting-started.md) . È anche possibile configurare l'estensione di diagnostica per inviare eventi e dati sulle prestazioni all'esterno di monitoraggio di Azure tramite hub eventi di Azure.

Installare l'estensione di diagnostica per una singola macchina virtuale Windows nel portale di Azure dall'opzione **impostazioni di diagnostica** nel menu VM. Selezionare l'opzione per abilitare **monitoraggio di Azure** nella scheda **sink** . Per abilitare l'estensione da un modello o da una riga di comando per più macchine virtuali, vedere [installazione e configurazione](../platform/diagnostics-extension-overview.md#installation-and-configuration). A differenza dell'agente di Log Analytics, i dati da raccogliere sono definiti nella configurazione per l'estensione in ogni macchina virtuale.

![Impostazione di diagnostica](media/monitor-vm-azure/diagnostic-setting.png)

Vedere [installare e configurare Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) per informazioni dettagliate sulla configurazione degli agenti di Telegraf in macchine virtuali Linux. L'opzione di menu **impostazioni di diagnostica** è disponibile per Linux, ma consente solo di inviare dati ad archiviazione di Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Raccolta delle metriche della piattaforma e del log attività
È possibile visualizzare le metriche della piattaforma e il log attività raccolti per ogni host macchina virtuale nell'portale di Azure. Raccogliere questi dati nella stessa area di lavoro Log Analytics come Monitoraggio di Azure per le macchine virtuali per analizzarli con gli altri dati di monitoraggio raccolti per la macchina virtuale. Questa raccolta è configurata con un' [impostazione di diagnostica](../platform/diagnostic-settings.md). Raccogliere il log attività con un' [impostazione di diagnostica per la sottoscrizione](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Raccogliere le metriche della piattaforma con un'impostazione di diagnostica per la macchina virtuale. A differenza di altre risorse di Azure, non è possibile creare un'impostazione di diagnostica per una macchina virtuale nell'portale di Azure ma è necessario usare [un altro metodo](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell). Gli esempi seguenti illustrano come raccogliere le metriche per una macchina virtuale tramite PowerShell e l'interfaccia della riga di comando.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure 
Una volta configurata la raccolta dei dati di monitoraggio per una macchina virtuale, sono disponibili più opzioni per accedervi nel portale di Azure:

- Usare il menu **monitoraggio di Azure** per accedere ai dati di tutte le risorse monitorate. 
- Usare Monitoraggio di Azure per le macchine virtuali per il monitoraggio di set di macchine virtuali su larga scala.
- Analizzare i dati per una singola macchina virtuale dal relativo menu nel portale di Azure. Nella tabella seguente sono elencate diverse opzioni per il monitoraggio del menu macchine virtuali.

![Monitoraggio nel portale di Azure](media/monitor-vm-azure/monitor-menu.png)

| Opzione di menu | Descrizione |
|:---|:---|
| Panoramica | Visualizza le [metriche della piattaforma](../platform/data-platform-metrics.md) per l'host macchina virtuale. Fare clic su un grafico per usare questi dati in [Esplora metriche](../platform/metrics-getting-started.md). |
| Log attività | Voci del [log attività](../platform/activity-log-view.md) filtrate per la macchina virtuale corrente. |
| Informazioni dettagliate | Apre [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md) con la mappa per la macchina virtuale corrente selezionata. |
| Avvisi | Visualizza gli [avvisi](../platform/alerts-overview.md) per la macchina virtuale corrente.  |
| Metriche | Aprire [Esplora metriche](../platform/metrics-getting-started.md) con l'ambito impostato sulla macchina virtuale corrente. |
| Impostazioni di diagnostica | Abilitare e configurare l' [estensione di diagnostica](../platform/diagnostics-extension-overview.md) per la macchina virtuale corrente. |
| Elementi consigliati di Advisor | Suggerimenti per la macchina virtuale corrente da [Azure Advisor](/azure/advisor/). |
| Log | Aprire [log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) con l' [ambito](../log-query/scope.md) impostato sulla macchina virtuale corrente. |
| Monitoraggio connessione | Aprire [Network Watcher monitoraggio connessione](../../network-watcher/connection-monitor-preview.md) per monitorare le connessioni tra la macchina virtuale corrente e altre macchine virtuali. |


## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche
È possibile analizzare le metriche per le macchine virtuali usando Esplora metriche aprendo le **metriche** dal menu della macchina virtuale. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione ad Azure Esplora metriche](../platform/metrics-getting-started.md) . 

Esistono due spazi dei nomi usati dalle macchine virtuali per le metriche:

| Spazio dei nomi | Descrizione |
|:---|:---|
| Host macchina virtuale | Metriche host raccolte automaticamente per tutte le macchine virtuali di Azure. Elenco dettagliato delle metriche in [Microsoft. Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Guest macchina virtuale | Metriche del sistema operativo guest raccolte dalle macchine virtuali con estensione di diagnostica installate e configurate per l'invio al sink di monitoraggio di Azure. |

![Metriche](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisi dei dati di log
Macchine virtuali di Azure raccoglie i dati seguenti nei log di monitoraggio di Azure. 

Monitoraggio di Azure per le macchine virtuali Abilita la raccolta di un set predeterminato di contatori delle prestazioni che vengono scritti nella tabella *InsightsMetrics* . Si tratta della stessa tabella usata da [monitoraggio di Azure per i contenitori](container-insights-overview.md). 

| Origine dati | Requisiti | Tabelle |
|:---|:---|:---|
| Monitoraggio di Azure per le macchine virtuali | Abilitare in ogni macchina virtuale. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Per informazioni dettagliate [, vedere come eseguire query sui log da monitoraggio di Azure per le macchine virtuali](vminsights-log-search.md) . |
| Log attività | Impostazione di diagnostica per la sottoscrizione. | AzureActivity |
| Metriche host | Impostazione di diagnostica per la macchina virtuale. | AzureMetrics |
| Origini dati del sistema operativo guest | Abilitare Log Analytics Agent e configurare le origini dati. | Vedere la documentazione per ogni origine dati. |


> [!NOTE]
> I dati sulle prestazioni raccolti dall'agente di Log Analytics scrivono *nella tabella delle* prestazioni mentre monitoraggio di Azure per le macchine virtuali lo raccoglieranno nella tabella *InsightsMetrics* . Si tratta degli stessi dati, ma le tabelle hanno una struttura diversa. Se sono presenti query basate su *prestazioni*, è necessario riscrivere il per usare *InsightsMetrics*.


## <a name="alerts"></a>Avvisi
Gli [avvisi](../platform/alerts-overview.md) in monitoraggio di Azure notificano in modo proattivo quando si trovano condizioni importanti nei dati di monitoraggio e potenzialmente avviano un'azione, ad esempio l'avvio di un'app per la logica o la chiamata di un webhook. Le regole di avviso definiscono la logica utilizzata per determinare quando è necessario creare un avviso. Monitoraggio di Azure raccoglie i dati usati dalle regole di avviso, ma è necessario creare regole per definire le condizioni di avviso nella sottoscrizione di Azure.

Nelle sezioni seguenti vengono descritti i tipi di regole di avviso e le raccomandazioni su quando è necessario utilizzare ciascuna di esse. Questa raccomandazione è basata sulle funzionalità e sui costi del tipo di regola di avviso. Per informazioni sui prezzi degli avvisi, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regole di avviso del log attività
[Le regole di avviso del log attività](../platform/alerts-activity-log.md) vengono attivate quando viene creata una voce corrispondente a determinati criteri nel log attività. Non hanno alcun costo, quindi dovrebbero essere la prima scelta se la logica necessaria è nel log attività. 

La risorsa di destinazione per gli avvisi del log attività può essere una macchina virtuale specifica, tutte le macchine virtuali in un gruppo di risorse o tutte le macchine virtuali in una sottoscrizione.

Ad esempio, creare un avviso se una macchina virtuale critica viene arrestata selezionando la *macchina virtuale* di spegnimento per il nome del segnale.

![Avviso del log attività](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regole di avviso per le metriche
[Le regole di avviso delle metriche](../platform/alerts-metric.md) vengono attivate quando un valore della metrica supera una soglia. È possibile definire un valore soglia specifico o consentire a monitoraggio di Azure di determinare in modo dinamico una soglia in base ai dati cronologici.  Usare gli avvisi delle metriche ogni volta che è possibile con i dati delle metriche, perché costano meno e sono più reattivi rispetto alle regole di avviso del log Si tratta anche di un significato con stato che si risolverà quando la metrica scende sotto la soglia.

La risorsa di destinazione per gli avvisi del log attività può essere una macchina virtuale specifica o tutte le macchine virtuali in un gruppo di risorse.

Ad esempio, per creare un avviso quando il processore di una macchina virtuale supera un valore specifico, creare una regola di avviso per la metrica usando la *percentuale di CPU* come tipo di segnale. Impostare un valore soglia specifico o consentire a monitoraggio di Azure di impostare una soglia dinamica. 

![Avviso metrica](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Avvisi relativi ai log
Le [regole di avviso del log](../platform/alerts-log.md) vengono attivate quando i risultati di una query di log pianificata corrispondono a determinati criteri. Gli avvisi per le query di log sono le più dispendiose e meno reattive delle regole di avviso, ma hanno accesso ai dati più diversi e possono eseguire una logica complessa che non può essere eseguita dalle altre regole di avviso. 

La risorsa di destinazione per una query di log è un'area di lavoro Log Analytics. Filtrare per computer specifici nella query.

Per creare, ad esempio, un avviso che controlla se le macchine virtuali in un determinato gruppo di risorse sono offline, usare la query seguente, che restituisce un record per ogni computer che non ha raggiunto un heartbeat negli ultimi dieci minuti. Usare una soglia di 1 che viene attivata se almeno un computer ha un heartbeat mancante.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Avviso log](media/monitor-vm-azure/log-alert-01.png)

Per creare un avviso se si verifica un numero eccessivo di accessi non riusciti in qualsiasi macchina virtuale Windows nella sottoscrizione, utilizzare la query seguente che restituisce un record per ogni evento di accesso non riuscito nell'ultima ora. Utilizzare una soglia impostata sul numero di accessi non riusciti consentiti. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Avviso log](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) consente il monitoraggio granulare dei carichi di lavoro nelle macchine virtuali. Vedere la [Guida al monitoraggio del cloud](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) per un confronto tra le piattaforme di monitoraggio e diverse strategie di implementazione.

Se si dispone di un ambiente SCOM esistente che si intende usare, è possibile integrarlo con monitoraggio di Azure per fornire funzionalità aggiuntive. L'agente di Log Analytics usato da monitoraggio di Azure è identico a quello usato per SCOM, in modo che le macchine virtuali monitorate invii i dati a entrambi. È comunque necessario aggiungere l'agente a Monitoraggio di Azure per le macchine virtuali e configurare l'area di lavoro per raccogliere dati aggiuntivi come specificato in precedenza, ma le macchine virtuali possono continuare a eseguire i Management Pack esistenti in un ambiente SCOM senza alcuna modifica.

Di seguito sono riportate le funzionalità di monitoraggio di Azure che aumentano le funzionalità di SCOM esistenti:

- Usare Log Analytics per analizzare in modo interattivo i dati di log e delle prestazioni.
- Usare gli avvisi del log per definire le condizioni di avviso tra più macchine virtuali e usare tendenze a lungo termine che non sono possibili usando gli avvisi in SCOM.   

Per informazioni dettagliate sulla connessione del gruppo di gestione di SCOM esistente all'area di lavoro Log Analytics, vedere [connettere Operations Manager a monitoraggio di Azure](../platform/om-agents.md) .


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come analizzare i dati nei log di monitoraggio di Azure usando le query di log.](../log-query/get-started-queries.md)
* [Informazioni sugli avvisi con le metriche e i log in monitoraggio di Azure.](../platform/alerts-overview.md)

