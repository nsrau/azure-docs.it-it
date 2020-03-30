---
title: Monitor Azure Site Recovery with Azure Monitor Logs
description: Informazioni su come monitorare Azure Site Recovery con i log di monitoraggio di Azure (Log Analytics)Learn how to monitor Azure Site Recovery with Azure Monitor Logs (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133425"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorare Site Recovery con i log di Monitoraggio di Azure

In questo articolo viene descritto come monitorare i computer replicati da [Azure Site Recovery](site-recovery-overview.md), usando i log di Monitoraggio di [Azure](../azure-monitor/platform/data-platform-logs.md)e [Log Analytics](../azure-monitor/log-query/log-query-overview.md).

I log di monitoraggio di Azure forniscono una piattaforma di log che raccoglie log di attività e diagnostica, insieme ad altri dati di monitoraggio. In Registri di monitoraggio di Azure è possibile usare Log Analytics per scrivere e testare le query di log e analizzare in modo interattivo i dati di log. È possibile visualizzare e eseguire query sui risultati del log e configurare avvisi per eseguire azioni in base ai dati monitorati.

Per Site Recovery, è possibile Azure Monitor Logs per eseguire le operazioni seguenti:For Site Recovery, you can Azure Monitor Logs to help you do the following:

- **Monitorare lo stato e l'integrità**di Site Recovery . Ad esempio, è possibile monitorare l'integrità della replica, testare lo stato del failover, gli eventi di Site Recovery, gli obiettivi dei punti di ripristino (RPO) per le macchine protette e la frequenza di modifica del disco/dati.
- **Impostare avvisi per Site Recovery**. Ad esempio, è possibile configurare gli avvisi per l'integrità della macchina, lo stato di failover del test o lo stato del processo di Site Recovery.For example, you can configure alerts for machine health, test failover status, or Site Recovery job status.

L'uso dei log di monitoraggio di Azure con Site Recovery è supportato per la replica da **Azure ad Azure** e da VMware VM/server fisico alla replica di Azure.Using Azure Monitor Logs with Site Recovery is supported for Azure to Azure replication, and **VMware VM/physical server to Azure** replication.

> [!NOTE]
> Per ottenere i log dei dati di varianza e i registri delle frequenze di caricamento per VMware e i computer fisici, è necessario installare un agente di monitoraggio Microsoft nel server di elaborazione. Questo agente invia i log dei computer di replica all'area di lavoro. Questa funzionalità è disponibile solo per la versione dell'agente per dispositivi mobili 9.30 in poi.

## <a name="before-you-start"></a>Prima di iniziare

Ecco gli elementi necessari:

- Almeno un computer protetto in un insieme di credenziali di Servizi di ripristino.
- Area di lavoro di Log Analytics per archiviare i log di Site Recovery. [Ulteriori informazioni sulla](../azure-monitor/learn/quick-create-workspace.md) configurazione di un'area di lavoro.
- Una conoscenza di base di come scrivere, eseguire e analizzare le query di log in Log Analytics.A basic understanding of how to write, run, and analyze log queries in Log Analytics. [Scopri di più](../azure-monitor/log-query/get-started-portal.md).

Si consiglia di [esaminare](monitoring-common-questions.md) le domande di monitoraggio comuni prima di iniziare.

## <a name="configure-site-recovery-to-send-logs"></a>Configurare Site Recovery per l'invio di log

1. Nell'insieme di credenziali fare clic su **Impostazioni** > di**diagnostica Aggiungi impostazione diagnostica**.

    ![Selezionare la registrazione diagnostica](./media/monitoring-log-analytics/add-diagnostic.png)

2. In **Impostazioni di diagnostica**specificare un nome e selezionare la casella Invia a Log **Analytics**.
3. Selezionare la sottoscrizione Log di monitoraggio di Azure e l'area di lavoro Log Analytics.Select the Azure Monitor Logs subscription, and the Log Analytics workspace.
4. Selezionare **Diagnostica di Azure** nell'interruttore.
5. Nell'elenco dei log selezionare tutti i log con il prefisso **AzureSiteRecovery**. Fare quindi clic su **OK**.

    ![Selezionare l'area di lavoro](./media/monitoring-log-analytics/select-workspace.png)

I log di Site Recovery iniziano a essere inseriti in una tabella (**AzureDiagnostics**) nell'area di lavoro selezionata.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Configurare l'agente di monitoraggio Microsoft nel server di elaborazione per l'invio di i registri della frequenza di varianza e caricamentoConfigure Microsoft monitoring agent on the Process Server to send churn and upload rate logs

È possibile acquisire le informazioni sulla frequenza di varianza dei dati e le informazioni sulla velocità di caricamento dei dati di origine per i computer VMware/fisici in locale. A tale scopo, è necessario installare un agente di monitoraggio Microsoft nel server di elaborazione.

1. Passare all'area di lavoro Log Analytics e fare clic su **Impostazioni avanzate**.
2. Fare clic sulla pagina **Origini connesse** e selezionare ulteriormente **Server Windows**.
3. Scaricare l'agente Windows (64 bit) sul server di elaborazione. 
4. [Ottenere l'ID e la chiave dell'area di lavoro](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Configurare l'agente per l'utilizzo di TLS 1.2Configure agent to use TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Completare l'installazione dell'agente](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) fornendo l'ID e la chiave dell'area di lavoro ottenuti.
7. Una volta completata l'installazione, accedere all'area di lavoro di Log Analytics e fare clic su **Impostazioni avanzate**. Passare alla pagina **Dati** e fare clic su **Contatori delle prestazioni di Windows**. 
8. Fare clic su **''** per aggiungere i seguenti due contatori con intervallo di campionamento di 300 secondi:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

I dati relativi alla varianza e alla velocità di caricamento inizieranno a essere inseriti nell'area di lavoro.


## <a name="query-the-logs---examples"></a>Interrogare i log - esempi

I dati vengono recuperati dai log utilizzando query di log scritte con il linguaggio di [query Kusto](../azure-monitor/log-query/get-started-queries.md). In questa sezione vengono forniti alcuni esempi di query comuni che è possibile utilizzare per il monitoraggio di Site Recovery.

> [!NOTE]
> Alcuni degli esempi utilizzano **replicationProviderName_s** impostata su **A2A**. In questo modo vengono recuperate le macchine virtuali di Azure replicate in un'area secondaria di Azure usando Site Recovery.This retrieves Azure VMs that are replicated to a secondary Azure region using Site Recovery. In questi esempi, è possibile sostituire **A2A** con **InMageAzureV2,** se si desidera recuperare le macchine virtuali VMware locali o i server fisici replicati in Azure tramite Site Recovery.


### <a name="query-replication-health"></a>Integrità della replica delle query

Questa query traccia un grafico a torta per l'integrità della replica corrente di tutte le macchine virtuali di Azure protette, suddivise in tre stati: Normale, Avviso o Critico.This query plots a pie chart for the current replication health of all protected Azure VMs, broken down into three states: Normal, Warning, or Critical.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Versione del servizio Per dispositivi mobili query

Questa query traccia un grafico a torta per le macchine virtuali di Azure replicato con Site Recovery, suddiviso in base alla versione dell'agente Mobility in esecuzione.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Tempo rPO query

Questa query traccia un grafico a barre di macchine virtuali di Azure replicato con Site Recovery, suddiviso per obiettivo del punto di ripristino (RPO): meno di 15 minuti, tra 15-30 minuti e più di 30 minuti.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Query RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Eseguire query sui processi di Site Recovery

Questa query recupera tutti i processi di Site Recovery (per tutti gli scenari di ripristino di emergenza), attivati nelle ultime 72 ore e il relativo stato di completamento.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Eventi di ripristino del sito di query

Questa query recupera tutti gli eventi di Site Recovery (per tutti gli scenari di ripristino di emergenza) generati nelle ultime 72 ore, insieme alla loro gravità. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Stato di failover del test di query (grafico a torta)Query test failover state (pie chart)

Questa query traccia un grafico a torta per lo stato di failover di test delle macchine virtuali di Azure replicate con Site Recovery.This query plots a pie chart for the test failover status of Azure VMs replicated with Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Stato di failover del test di query (tabella)Query test failover state (table)

Questa query traccia una tabella per lo stato di failover di test delle macchine virtuali di Azure replicate con Site Recovery.This query plots a table for the test failover status of Azure VMs replicated with Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Macchina di query RPO

Questa query traccia un grafico delle tendenze che tiene traccia dell'RPO di una macchina virtuale di Azure specifica (ContosoVM123) nelle ultime 72 ore.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Macchina di query RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Query data change rate (churn) and upload rate for an Azure VM

Questa query traccia un grafico delle tendenze per una macchina virtuale di Azure specifica (ContosoVM123), che rappresenta la frequenza di modifica dei dati (byte scritti al secondo) e la frequenza di caricamento dei dati. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Modifica dei dati delle query](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Frequenza di modifica dei dati di query (varianza) e velocità di caricamento per un VMware o un computer fisico

> [!Note]
> Assicurarsi di configurare l'agente di monitoraggio nel server di elaborazione per recuperare questi log. Fare riferimento [ai passaggi per configurare l'agente di monitoraggio](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Questa query traccia un grafico delle tendenze per un disco su disco **specifico0** di un elemento replicato **win-9r7sfh9qlru**, che rappresenta la velocità di modifica dei dati (byte scritti al secondo) e la velocità di caricamento dei dati. È possibile trovare il nome del disco nel pannello **Dischi** dell'elemento replicato nell'insieme di credenziali dei servizi di ripristino. Il nome dell'istanza da utilizzare nella query è il nome DNS del computer seguito da _ e dal nome del disco, come in questo esempio.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Server di elaborazione invia questi dati ogni 5 minuti nell'area di lavoro di Log Analytics. Questi punti dati rappresentano la media calcolata per 5 minuti.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Query disaster recovery summary (Azure to Azure)

Questa query traccia una tabella di riepilogo per le macchine virtuali di Azure replicate in un'area di Azure secondaria.  Vengono visualizzati il nome della macchina virtuale, lo stato di replica e protezione, l'RPO, lo stato del failover di test, la versione dell'agente Mobility, gli eventuali errori di replica attivi e il percorso di origine.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Riepilogo del ripristino di emergenza delle query (VMware/server fisici)

Questa query traccia una tabella di riepilogo per le macchine virtuali VMware e i server fisici replicati in Azure.This query plots a summary table for VMware VMs and physical servers replicated to Azure.  Mostra il nome del computer, lo stato di replica e protezione, RPO, lo stato di failover del test, la versione dell'agente Mobility, eventuali errori di replica attivi e il server di elaborazione pertinente.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Impostare gli avvisi - esempi

È possibile configurare gli avvisi di Site Recovery in base ai dati di Monitoraggio di Azure.You can set up Site Recovery alerts based on Azure Monitor data. [Ulteriori informazioni](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) sulla configurazione degli avvisi di log. 

> [!NOTE]
> Alcuni degli esempi utilizzano **replicationProviderName_s** impostata su **A2A**. In questo modo vengono impostati gli avvisi per le macchine virtuali di Azure replicate in un'area secondaria di Azure.This sets alerts for Azure VMs that are replicated to a secondary Azure region. In questi esempi è possibile sostituire A2A con InMageAzureV2 se si desidera impostare avvisi per le macchine virtuali VMware locali o i server fisici replicati in Azure.In these examples, you can replace **A2A** with **InMageAzureV2** if you want to set alerts for on-premises VMware VMs or physical servers replicated to Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Più macchine in uno stato criticoMultiple machines in a critical state

Configurare un avviso se più di 20 macchine virtuali di Azure replicate entrano in uno stato Critico.Set up an alert if more than 20 replicated Azure VMs go into a Critical state.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Per l'avviso, impostare Valore soglia su 20.For the alert, set **Threshold value** to 20.

### <a name="single-machine-in-a-critical-state"></a>Macchina singola in uno stato critico

Configurare un avviso se una macchina virtuale di Azure replicata specifica entra in uno stato Critico.Set up an alert if a specific replicated Azure VM goes into a Critical state.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Per l'avviso, impostare Valore soglia su 1.For the alert, set **Threshold value** to 1.

### <a name="multiple-machines-exceed-rpo"></a>Più macchine superano RPO

Configurare un avviso se l'RPO per più di 20 macchine virtuali di Azure supera i 30 minuti.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Per l'avviso, impostare Valore soglia su 20.For the alert, set **Threshold value** to 20.

### <a name="single-machine-exceeds-rpo"></a>La macchina singola supera rPO

Configurare un avviso se l'RPO per una singola macchina virtuale di Azure supera i 30 minuti.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Per l'avviso, impostare Valore soglia su 1.For the alert, set **Threshold value** to 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Il failover di test per più computer supera i 90 giorni

Configurare un avviso se l'ultimo failover di test riuscito è stato superiore a 90 giorni, per più di 20 macchine virtuali. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Per l'avviso, impostare Valore soglia su 20.For the alert, set **Threshold value** to 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Il failover di test per un singolo computer supera i 90 giorni

Configurare un avviso se l'ultimo failover di test riuscito per una macchina virtuale specifica è stato più di 90 giorni fa.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Per l'avviso, impostare Valore soglia su 1.For the alert, set **Threshold value** to 1.

### <a name="site-recovery-job-fails"></a>Processo di site recovery non riesce

Impostare un avviso se un processo di Site Recovery (in questo caso il processo di riprotezione) ha esito negativo per qualsiasi scenario di Site Recovery, durante l'ultimo giorno. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Per l'avviso, impostare **Valore soglia** su 1 e **Periodo** su 1440 minuti per controllare gli errori nell'ultimo giorno.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul](site-recovery-monitor-and-troubleshoot.md) monitoraggio di Site Recovery integrato.
