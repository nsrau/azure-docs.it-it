---
title: Monitorare le macchine virtuali di Azure con Monitoraggio di Azure
description: Descrive come raccogliere e analizzare i dati di monitoraggio generati dalle macchine virtuali di Azure tramite Monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 294c93242a3fee5db14f5919ebb367aebcca3a80
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326189"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitoraggio delle macchine virtuali di Azure con Monitoraggio di Azure
Questo articolo descrive come usare Monitoraggio di Azure per raccogliere e analizzare i dati di monitoraggio generati dalle macchine virtuali di Azure e mantenerne l'integrità. Con Monitoraggio di Azure è possibile monitorare la disponibilità e le prestazioni delle macchine virtuali come per qualsiasi [altra risorsa di Azure](monitor-azure-resource.md). Le macchine virtuali sono però diverse rispetto ad altre risorse perché è necessario monitorare anche il sistema operativo guest e i carichi di lavoro in esecuzione in esso. 

> [!NOTE]
> Questo articolo offre una panoramica completa dei concetti e delle opzioni per il monitoraggio delle macchine virtuali in Monitoraggio di Azure. Per iniziare a monitorare rapidamente le macchine virtuali senza soffermarsi sui concetti di base, vedere [Avvio rapido: Monitorare una macchina virtuale di Azure con Monitoraggio di Azure](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Differenze rispetto ad altre risorse di Azure
L'articolo [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](monitor-azure-resource.md) descrive i dati di monitoraggio generati dalle risorse di Azure e l'uso delle funzionalità di Monitoraggio di Azure per analizzare e creare avvisi relativi a tali dati. È possibile raccogliere e usare gli stessi dati di monitoraggio generati dalle macchine virtuali di Azure con le differenze seguenti:

-  Le [metriche della piattaforma](../platform/data-platform-metrics.md) vengono raccolte automaticamente per le macchine virtuali, ma solo per l'[host macchina virtuale](#monitoring-data). Per raccogliere i dati sulle prestazioni dal sistema operativo guest è necessario un agente. 
- Le macchine virtuali non generano [log delle risorse](../platform/platform-logs-overview.md) che includono informazioni dettagliate sulle operazioni eseguite all'interno di una risorsa di Azure. Per raccogliere i dati dei log dal sistema operativo guest è necessario un agente.
- È possibile creare [impostazioni di diagnostica](../platform/diagnostic-settings.md) in modo che una macchina virtuale invii le metriche della piattaforma ad altre destinazioni, ad esempio all'archiviazione e agli hub eventi, ma non è possibile configurare queste impostazioni di diagnostica nel portale di Azure. 

## <a name="monitoring-data"></a>Dati di monitoraggio
Le macchine virtuali in Azure generano [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) , come illustrato nella figura seguente.

![Panoramica](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host macchina virtuale
Le macchine virtuali in Azure generano i dati seguenti per l'host macchina virtuale, analogamente ad altre risorse di Azure, come descritto in [Dati di monitoraggio](monitor-azure-resource.md#monitoring-data).

- [Metriche della piattaforma](../platform/data-platform-metrics.md): valori numerici che vengono raccolti automaticamente a intervalli regolari e che descrivono un aspetto specifico di una risorsa in un determinato momento. Le metriche della piattaforma vengono raccolte per l'host macchina virtuale. Per raccogliere le metriche per il sistema operativo guest, è invece necessaria l'estensione di diagnostica.
- [Log attività](../platform/platform-logs-overview.md): offre informazioni dettagliate sulle operazioni eseguite in ogni risorsa di Azure nella sottoscrizione dall'esterno (piano di gestione). Per una macchina virtuale, include ad esempio informazioni sul momento in cui è stata avviata e su eventuali modifiche apportate alla configurazione.


### <a name="guest-operating-system"></a>Sistema operativo guest
Per raccogliere i dati dal sistema operativo guest di una macchina virtuale, è necessario un agente, che viene eseguito localmente in ogni macchina virtuale e invia i dati a Monitoraggio di Azure. Per Monitoraggio di Azure sono disponibili più agenti, ognuno dei quali raccoglie dati diversi e li scrive in posizioni diverse. Per un confronto dettagliato sui diversi agenti, vedere [Panoramica degli agenti di Monitoraggio di Azure](../platform/agents-overview.md). 

- [Agente di Log Analytics](../platform/agents-overview.md#log-analytics-agent): disponibile per le macchine virtuali in Azure, in altri ambienti cloud e locali. Raccoglie i dati e li invia ai log di Monitoraggio di Azure. Supporta Monitoraggio di Azure per le macchine virtuali e soluzioni di monitoraggio. Si tratta dello stesso agente usato per System Center Operations Manager.
- [Dependency Agent](../platform/agents-overview.md#dependency-agent): raccoglie i dati relativi ai processi in esecuzione nella macchina virtuale e le relative dipendenze. Si basa sull'agente di Log Analytics per trasmettere i dati in Azure e supporta le soluzioni Monitoraggio di Azure per le macchine virtuali, Mapping dei servizi e Wire Data 2.0.
- [Estensione Diagnostica di Azure](../platform/agents-overview.md#azure-diagnostics-extension): disponibile solo per le macchine virtuali di Monitoraggio di Azure. Consente di raccogliere i dati in più posizioni, ma viene principalmente usata per raccogliere i dati sulle prestazioni guest nelle metriche di Monitoraggio di Azure per macchine virtuali Windows.
- [Agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md): raccoglie i dati sulle prestazioni dalle macchine virtuali Linux nelle metriche di Monitoraggio di Azure.


## <a name="configuration-requirements"></a>Requisiti di configurazione
Per abilitare tutte le funzionalità di Monitoraggio di Azure per il monitoraggio di una macchina virtuale, è necessario raccogliere i dati di monitoraggio dall'host macchina virtuale e dal sistema operativo guest sia nelle [metriche di Monitoraggio di Azure](../platform/data-platform-logs.md) sia nei [log di Monitoraggio di Azure](../platform/data-platform-logs.md). La tabella seguente elenca la configurazione necessaria per abilitare questa raccolta. È possibile scegliere di non eseguire tutti questi passaggi in base alle esigenze specifiche.

| Passaggio di configurazione | Azioni completate | Funzionalità abilitate |
|:---|:---|:---|
| Nessuna configurazione | - Raccolta delle metriche della piattaforma host nelle metriche.<br>- Raccolta del log attività. | - Esplora metriche per host.<br>- Avvisi per le metriche per host.<br>- Avvisi del log attività. |
| [Abilita Monitoraggio di Azure per le macchine virtuali](#enable-azure-monitor-for-vms) | - Installazione dell'agente di Log Analytics.<br>- Installazione di Dependency Agent.<br>- Raccolta dei dati sulle prestazioni guest nei log.<br>- Raccolta dei dettagli sui processi e sulle dipendenze nei log. | - Grafici sulle prestazioni e cartelle di lavoro per i dati sulle prestazioni guest.<br>- Query su log per i dati sulle prestazioni guest.<br>- Avvisi del log per i dati sulle prestazioni guest.<br>- Mapping delle dipendenze. |
| [Installare l'estensione di diagnostica e l'agente Telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Raccolta dei dati sulle prestazioni guest nelle metriche. | - Esplora metriche per guest.<br>- Avvisi per le metriche per guest.  |
| [Configurare un'area di lavoro Log Analytics](#configure-log-analytics-workspace) | - Raccolta degli eventi da guest. | - Query su log per gli eventi guest.<br>- Avvisi del log per gli eventi guest. |
| [Creare l'impostazione di diagnostica per la macchina virtuale](#collect-platform-metrics-and-activity-log) | - Raccolta delle metriche della piattaforma nei log.<br>- Raccolta del log attività nei log. | - Query sul log per le metriche host.<br>- Avvisi del log per le metriche host.<br>- Query su log per il log attività.

Ognuno di questi passaggi di configurazione viene descritto nelle sezioni seguenti.

### <a name="enable-azure-monitor-for-vms"></a>Abilita Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](vminsights-overview.md) è una [funzionalità](insights-overview.md) di Monitoraggio di Azure. È lo strumento principale per il monitoraggio delle macchine virtuali in Monitoraggio di Azure. Rispetto alle funzionalità standard di Monitoraggio di Azure, offre il valore aggiuntivo descritto di seguito.

- Onboarding semplificato dell'agente di Log Analytics e di Dependency Agent per abilitare il monitoraggio di un sistema operativo guest e dei carichi di lavoro di una macchina virtuale. 
- Grafici sulle prestazioni di tendenza e cartelle di lavoro predefiniti che consentono di analizzare le metriche delle prestazioni di base dal sistema operativo guest della macchina virtuale.
- Mapping delle dipendenze che consente di visualizzare i processi in esecuzione in ogni macchina virtuale e i componenti interconnessi con altri computer e origini esterne.

![Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/vminsights-01.png)

![Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/vminsights-02.png)


Abilitare Monitoraggio di Azure per le macchine virtuali dall'opzione **Informazioni dettagliate** nel menu della macchina virtuale del portale di Azure. Per informazioni dettagliate e altri metodi di configurazione, vedere [Panoramica sull'abilitazione di Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).

![Abilita Monitoraggio di Azure per le macchine virtuali](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurare un'area di lavoro Log Analytics
L'agente di Log Analytics usato da Monitoraggio di Azure per le macchine virtuali invia i dati a un'[area di lavoro Log Analytics](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). È possibile abilitare la raccolta di dati aggiuntivi su prestazioni, eventi e altri dati di monitoraggio dall'agente configurando l'area di lavoro Log Analytics. È necessario configurarla solo una volta, poiché tutti gli agenti che si connettono all'area di lavoro scaricheranno automaticamente la configurazione e inizieranno immediatamente a raccogliere i dati definiti. 

È possibile accedere alla configurazione dell'area di lavoro direttamente da Monitoraggio di Azure per le macchine virtuali selezionando **Configurazione dell'area di lavoro** da **Attività iniziali**. Fare clic sul nome dell'area di lavoro per aprire il relativo menu.

![Configurazione dell'area di lavoro](media/monitor-vm-azure/workspace-configuration.png)

Per configurare le origini dati, selezionare **Impostazioni avanzate** dal menu dell'area di lavoro, quindi scegliere **Dati**. Per gli agenti Windows, selezionare **Log eventi Windows** e aggiungere i log eventi comuni, ad esempio *Sistema* e *Applicazione*. Per gli agenti Linux, selezionare **Syslog** e aggiungere le strutture comuni, ad esempio *Crenatura* e *Daemon*. Per un elenco delle origini dati disponibili e informazioni dettagliate sulla configurazione, vedere [Origini dati degli agenti in Monitoraggio di Azure](../platform/agent-data-sources.md). 

![Configurare gli eventi](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> È possibile configurare i contatori delle prestazioni per eseguirne la raccolta dalla configurazione dell'area di lavoro. Questa configurazione può però risultare ridondante rispetto ai contatori delle prestazioni raccolte da Monitoraggio di Azure per le macchine virtuali. Monitoraggio di Azure per le macchine virtuali raccoglie il set di contatori più comuni con una frequenza di una volta al minuto. Configurare solo i contatori delle prestazioni che devono essere raccolti dall'area di lavoro se si vuole raccogliere i contatori non ancora raccolti da Monitoraggio di Azure per le macchine virtuali o se esistono query esistenti che usano i dati sulle prestazioni.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Abilitare l'estensione di diagnostica e l'agente Telegraf
Monitoraggio di Azure per le macchine virtuali si basa sull'agente di Log Analytics che raccoglie i dati in un'area di lavoro Log Analytics. Supporta [più funzionalità di Monitoraggio di Azure](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs), ad esempio [query su log](../log-query/log-query-overview.md), [avvisi del log](../platform/alerts-log.md) e [cartelle di lavoro](../platform/workbooks-overview.md). L'[estensione di diagnostica](../platform/diagnostics-extension-overview.md) raccoglie i dati sulle prestazioni dal sistema operativo guest delle macchine virtuali Windows in Archiviazione di Azure e, facoltativamente, invia i dati sulle prestazioni alle [metriche di Monitoraggio di Azure](../platform/data-platform-metrics.md). Per le macchine virtuali Linux, è necessario l'[agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) per inviare i dati alle metriche di Azure.  Vengono così abilitate altre funzionalità di Monitoraggio di Azure, ad esempio [Esplora metriche](../platform/metrics-getting-started.md) e gli [ avvisi per le metriche](../platform/alerts-metric.md). È anche possibile configurare l'estensione di diagnostica per inviare eventi e dati sulle prestazioni all'esterno di Monitoraggio di Azure tramite Hub eventi di Azure.

Installare l'estensione di diagnostica per un'unica macchina virtuale Windows nel portale di Azure dall'opzione **Impostazioni di diagnostica** nel menu della macchina virtuale. Selezionare l'opzione per abilitare **Monitoraggio di Azure** nella scheda **Sink**. Per abilitare l'estensione da un modello o da una riga di comando per più macchine virtuali, vedere [Installazione e configurazione](../platform/diagnostics-extension-overview.md#installation-and-configuration). A differenza dell'agente di Log Analytics, i dati da raccogliere sono definiti nella configurazione dell'estensione in ogni macchina virtuale.

![Impostazione di diagnostica](media/monitor-vm-azure/diagnostic-setting.png)

Per informazioni dettagliate sulla configurazione degli agenti Telegraf in macchine virtuali Linux, vedere [Installare e configurare Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf). L'opzione di menu **Impostazioni di diagnostica** è disponibile per Linux, ma consente solo di inviare i dati ad Archiviazione di Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Raccogliere le metriche della piattaforma e il log attività
È possibile visualizzare le metriche della piattaforma e il log attività raccolti per ogni host macchina virtuale nel portale di Azure. Raccogliere questi dati nella stessa area di lavoro Log Analytics affinché Monitoraggio di Azure per le macchine virtuali possa analizzarli insieme agli altri dati di monitoraggio raccolti per la macchina virtuale. Questa raccolta viene configurata con un'[impostazione di diagnostica](../platform/diagnostic-settings.md). Raccogliere il log attività con un'[impostazione di diagnostica per la sottoscrizione](../platform/diagnostic-settings.md#create-in-azure-portal).

Raccogliere le metriche della piattaforma con un'impostazione di diagnostica per la macchina virtuale. A differenza di altre risorse di Azure, non è possibile creare un'impostazione di diagnostica per una macchina virtuale nel portale di Azure. È invece necessario usare [un altro metodo](../platform/diagnostic-settings.md#create-using-powershell). Gli esempi seguenti illustrano come raccogliere le metriche per una macchina virtuale usando sia PowerShell sia l'interfaccia della riga di comando.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure 
Dopo aver configurato la raccolta dei dati di monitoraggio per una macchina virtuale, è possibile accedervi da più opzioni nel portale di Azure:

- Usare il menu di **Monitoraggio di Azure** per accedere ai dati di tutte le risorse monitorate. 
- Usare Monitoraggio di Azure per le macchine virtuali per il monitoraggio di set di macchine virtuali su larga scala.
- Analizzare i dati per un'unica macchina virtuale dal relativo menu nel portale di Azure. La tabella seguente elenca diverse opzioni di monitoraggio nel menu delle macchine virtuali.

![Monitoraggio nel portale di Azure](media/monitor-vm-azure/monitor-menu.png)

| Opzione di menu | Descrizione |
|:---|:---|
| Panoramica | Visualizza le [metriche della piattaforma](../platform/data-platform-metrics.md) per l'host macchina virtuale. Fare clic su un grafico per usare questi dati in [Esplora metriche](../platform/metrics-getting-started.md). |
| Log attività | Voci del [log attività](../platform/activity-log.md#view-the-activity-log) filtrate in base alla macchina virtuale corrente. |
| Informazioni dettagliate | Apre [Monitoraggio di Azure per le macchine virtuali](./vminsights-overview.md) con il mapping per la macchina virtuale corrente selezionata. |
| Avvisi | Visualizza gli [avvisi](../platform/alerts-overview.md) per la macchina virtuale corrente.  |
| Metriche | Aprire [Esplora metriche](../platform/metrics-getting-started.md) con l'ambito impostato sulla macchina virtuale corrente. |
| Impostazioni di diagnostica | Consente di abilitare e configurare l'[estensione di diagnostica](../platform/diagnostics-extension-overview.md) per la macchina virtuale corrente. |
| Elementi consigliati di Advisor | Raccomandazioni di [Azure Advisor](../../advisor/index.yml) per la macchina virtuale corrente. |
| Log | Aprire [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) con l'[ambito](../log-query/scope.md) impostato sulla macchina virtuale corrente. |
| Monitoraggio connessione | Apre [Monitoraggio connessione di Network Watcher](../../network-watcher/connection-monitor-preview.md) per monitorare le connessioni tra la macchina virtuale corrente e altre macchine virtuali. |


## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche
È possibile analizzare le metriche per le macchine virtuali tramite Esplora metriche, aprendo **Metrica** dal menu della macchina virtuale. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../platform/metrics-getting-started.md). 

Le macchine virtuali usano tre spazi dei nomi per le metriche:

| Spazio dei nomi | Descrizione | Requisito |
|:---|:---|:---|
| Host macchina virtuale | Metriche host raccolte automaticamente per tutte le macchine virtuali di Azure. Elenco dettagliato delle metriche disponibile in [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Raccolta automatica senza necessità di configurazione. |
| Guest (versione classica) | Set limitato di dati sulle prestazioni dell'applicazione e del sistema operativo guest. Disponibile in Esplora metriche. Non sono disponibili altre funzionalità di Monitoraggio di Azure, ad esempio gli avvisi sulle metriche.  | Installazione dell'[estensione di diagnostica](../platform/diagnostics-extension-overview.md). I dati vengono letti da Archiviazione di Azure.  |
| Guest macchina virtuale | Dati sulle prestazioni del sistema operativo guest e delle applicazioni disponibili per tutte le funzionalità di Monitoraggio di Azure che usano le metriche. | Per Windows, [installazione dell'estensione di diagnostica](../platform/diagnostics-extension-overview.md) e abilitazione del sink di Monitoraggio di Azure. Per Linux, [Installazione dell'agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md). |

![Metriche](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisi dei dati di log
Le macchine virtuali di Azure raccolgono i dati seguenti nei log di Monitoraggio di Azure. 

Monitoraggio di Azure per le macchine virtuali abilita la raccolta di un set predeterminato di contatori delle prestazioni scritti nella tabella *InsightsMetrics*. Si tratta della stessa tabella usata da [Monitoraggio di Azure per i contenitori](container-insights-overview.md). 

| Origine dati | Requisiti | Tabelle |
|:---|:---|:---|
| Monitoraggio di Azure per le macchine virtuali | Eseguire l'abilitazione in ogni macchina virtuale. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Vedere [Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali](vminsights-log-search.md) per informazioni dettagliate. |
| Log attività | Impostazione di diagnostica per la sottoscrizione. | AzureActivity |
| Metriche host | Impostazione di diagnostica per la macchina virtuale. | AzureMetrics |
| Origini dati del sistema operativo guest | Abilitare l'agente di Log Analytics e configurare le origini dati. | Vedere la documentazione per ogni origine dati. |


> [!NOTE]
> I dati sulle prestazioni raccolti dall'agente di Log Analytics vengono scritti nella tabella *Perf* mentre Monitoraggio di Azure per le macchine virtuali raccoglie i dati nella tabella *InsightsMetrics*. I dati sono gli stessi, ma le tabelle hanno una struttura diversa. Se le query esistenti si basano sulla tabella *Perf*, sarà necessario riscriverle perché possano usare la tabella *InsightsMetrics*.


## <a name="alerts"></a>Avvisi
Gli [avvisi](../platform/alerts-overview.md) in Monitoraggio di Azure inviano una notifica in modo proattivo quando rilevano condizioni importanti nei dati di monitoraggio e, se possibile avviano un'azione, ad esempio avviano un'app per la logica o chiamano un webhook. Le regole di avviso definiscono la logica usata per determinare quando è necessario creare un avviso. Monitoraggio di Azure raccoglie i dati usati dalle regole di avviso. È tuttavia necessario creare regole per definire le condizioni di avviso nella sottoscrizione di Azure.

Le sezioni seguenti descrivono i tipi di regole di avviso e le raccomandazioni relativamente a ogni utilizzo. Le raccomandazioni si basano sulle funzionalità e sui costi del tipo di regola di avviso. Per informazioni sui prezzi degli avvisi, vedere [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regole di avviso del log attività
Le [regole di avviso del log attività](../platform/alerts-activity-log.md) vengono attivate quando si crea una voce corrispondente a determinati criteri nel log attività. Queste regole sono gratuite, quindi dovrebbero essere la prima scelta se la logica necessaria si trova nel log attività. 

La risorsa di destinazione per gli avvisi del log attività può essere una macchina virtuale specifica, tutte le macchine virtuali in un gruppo di risorse o tutte le macchine virtuali in una sottoscrizione.

Ad esempio, creare un avviso se una macchina virtuale critica viene arrestata selezionando l'opzione *Spegni la macchina virtuale* per il nome del segnale.

![Avviso del log attività](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regole di avviso per le metriche
Le [regole di avviso per le metriche](../platform/alerts-metric.md) vengono attivate quando un valore della metrica supera una soglia. È possibile definire un valore soglia specifico o consentire a Monitoraggio di Azure di determinare in modo dinamico una soglia in base ai dati cronologici.  Usare gli avvisi per le metriche ogni volta che i dati delle metriche lo consentono, poiché sono meno costosi e rispondono meglio rispetto alle regole di avviso del log. Sono avvisi con stato, quindi saranno risolti quando la metrica scenderà sotto la soglia.

La risorsa di destinazione per gli avvisi del log attività può essere una macchina virtuale specifica o tutte le macchine virtuali in un gruppo di risorse.

Ad esempio, per creare un avviso quando il processore di una macchina virtuale supera un valore specifico, creare una regola di avviso per le metriche usando *Percentuale CPU* come tipo di segnale. Impostare un valore soglia specifico o consentire a Monitoraggio di Azure di impostare una soglia dinamica. 

![Avviso per le metriche](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Avvisi relativi ai log
Le [regole di avviso del log](../platform/alerts-log.md) vengono attivate quando i risultati di una query sui log pianificata corrispondono a determinati criteri. Gli avvisi per le query sui log sono i più costosi e i meno reattivi tra le regole di avviso, ma hanno accesso ai dati più diversificati e possono eseguire una logica complessa che non può essere attuata dalle altre regole di avviso. 

La risorsa di destinazione per una query sui log è un'area di lavoro Log Analytics. Filtrare i computer specifici nella query.

Ad esempio, per creare un avviso che controlla se una delle macchine virtuali in un determinato gruppo di risorse è offline, usare la query seguente, che restituisce un record per ogni computer con heartbeat mancante negli ultimi dieci minuti. Usare una soglia di 1 che si attiva se almeno un computer ha un heartbeat mancante.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Avviso del log](media/monitor-vm-azure/log-alert-01.png)

Per creare un avviso se si verifica un numero eccessivo di accessi non riusciti in una qualsiasi macchina virtuale Windows nella sottoscrizione, usare la query seguente, che restituisce un record per ogni evento di accesso non riuscito nell'ultima ora. Usare una soglia impostata sul numero di accessi non riusciti consentiti. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Avviso del log](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) consente il monitoraggio granulare dei carichi di lavoro nelle macchine virtuali. Per un confronto delle piattaforme di monitoraggio e delle diverse strategie di implementazione, vedere [Guida al monitoraggio del cloud](/azure/cloud-adoption-framework/manage/monitor/).

Se si vuole continuare a usare un ambiente SCOM esistente, è possibile integrarlo con Monitoraggio di Azure per offrire funzionalità aggiuntive. L'agente di Log Analytics usato da Monitoraggio di Azure è identico a quello usato per SCOM. Le macchine virtuali monitorate invieranno quindi i dati a entrambi. È comunque necessario aggiungere l'agente a Monitoraggio di Azure per le macchine virtuali e configurare l'area di lavoro per raccogliere dati aggiuntivi come specificato in precedenza. Le macchine virtuali potranno comunque continuare a eseguire i Management Pack esistenti in un ambiente SCOM senza apportare modifiche.

Di seguito sono riportate le funzionalità di Monitoraggio di Azure che migliorano le funzionalità di SCOM esistenti:

- Usare Log Analytics per analizzare in modo interattivo i dati del log e i dati sulle prestazioni.
- Usare gli avvisi del log per definire condizioni di avviso in più macchine virtuali e usare tendenze a lungo termine che non sono possibili con gli avvisi in SCOM.   

Per informazioni dettagliate sulla connessione del gruppo di gestione di SCOM esistente all'area di lavoro Log Analytics, vedere [Connettere Operations Manager a Monitoraggio di Azure](../platform/om-agents.md).


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come analizzare i dati nei log di Monitoraggio di Azure usando le query su log.](../log-query/get-started-queries.md)
* [Informazioni sugli avvisi su metriche e log in Monitoraggio di Azure.](../platform/alerts-overview.md)

