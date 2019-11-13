---
title: Monitorare Azure Site Recovery con i log di monitoraggio di Azure
description: Informazioni su come monitorare Azure Site Recovery con i log di monitoraggio di Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b5bf568e03d4949b8798dd2e0f4c2d8cbcbbe0c7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73936083"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorare Site Recovery con i log di Monitoraggio di Azure

Questo articolo descrive come monitorare i computer replicati da Azure [Site Recovery](site-recovery-overview.md), usando i [log di monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md)e [log Analytics](../azure-monitor/log-query/log-query-overview.md).

I log di monitoraggio di Azure offrono una piattaforma di dati di log che raccoglie attività e log di diagnostica, oltre ad altri dati di monitoraggio. Nei log di monitoraggio di Azure è possibile usare Log Analytics per scrivere e testare le query di log e per analizzare in modo interattivo i dati di log. È possibile visualizzare ed eseguire query sui risultati del log e configurare gli avvisi per eseguire azioni in base ai dati monitorati.

Per Site Recovery, è possibile usare i log di monitoraggio di Azure per eseguire le operazioni seguenti:

- **Monitorare l'integrità e lo stato di Site Recovery**. Ad esempio, è possibile monitorare lo stato di replica, lo stato del failover di test, gli eventi Site Recovery, gli obiettivi del punto di ripristino (RPOs) per i computer protetti e i tassi di modifica dei dati e del disco.
- **Configurare gli avvisi per Site Recovery**. Ad esempio, è possibile configurare avvisi per l'integrità del computer, lo stato del failover di test o lo stato del processo Site Recovery.

L'uso dei log di monitoraggio di Azure con Site Recovery è supportato per la replica da **Azure ad Azure** e **da server fisici/VM VMware ad Azure** .

> [!NOTE]
> I registri dei dati di varianza e i log della velocità di caricamento sono disponibili solo per le VM di Azure che eseguono la replica in un'area di Azure secondaria.

## <a name="before-you-start"></a>Prima di iniziare

Ecco gli elementi necessari:

- Almeno un computer protetto in un insieme di credenziali di servizi di ripristino.
- Area di lavoro Log Analytics per archiviare i log di Site Recovery. Informazioni sulla configurazione di un'area [di](../azure-monitor/learn/quick-create-workspace.md) lavoro.
- Conoscenza di base di come scrivere, eseguire e analizzare le query del log in Log Analytics. [Altre informazioni](../azure-monitor/log-query/get-started-portal.md).

Prima di iniziare, è consigliabile esaminare le [domande di monitoraggio più comuni](monitoring-common-questions.md) .

## <a name="configure-site-recovery-to-send-logs"></a>Configurare Site Recovery per inviare i log

1. Nell'insieme di credenziali fare clic su **impostazioni di diagnostica** > **Aggiungi impostazione di diagnostica**.

    ![Selezione registrazione diagnostica](./media/monitoring-log-analytics/add-diagnostic.png)

2. In **impostazioni di diagnostica**specificare un nome e selezionare la casella **Invia a log Analytics**.
3. Selezionare la sottoscrizione dei log di monitoraggio di Azure e l'area di lavoro Log Analytics.
4. Selezionare **diagnostica di Azure** nell'interruttore.
5. Dall'elenco log selezionare tutti i log con il prefisso **AzureSiteRecovery**. Fare quindi clic su **OK**.

    ![Selezionare l'area di lavoro](./media/monitoring-log-analytics/select-workspace.png)

I log Site Recovery iniziano a essere inseriti in una tabella (**AzureDiagnostics**) nell'area di lavoro selezionata.


## <a name="query-the-logs---examples"></a>Eseguire una query sui log-esempi

È possibile recuperare i dati dai log usando le query di log scritte con il [linguaggio di query kusto](../azure-monitor/log-query/get-started-queries.md). In questa sezione vengono forniti alcuni esempi di query comuni che è possibile utilizzare per il monitoraggio Site Recovery.

> [!NOTE]
> Alcuni esempi utilizzano **replicationProviderName_s** impostato su **A2A**. In questo modo vengono recuperate le macchine virtuali di Azure replicate in un'area di Azure secondaria usando Site Recovery. In questi esempi, è possibile sostituire **A2A** con **InMageAzureV2**se si vuole recuperare macchine virtuali VMware locali o server fisici replicati in Azure usando Site Recovery.


### <a name="query-replication-health"></a>Stato replica query

Questa query traccia un grafico a torta per lo stato di replica corrente di tutte le macchine virtuali di Azure protette, suddiviso in tre stati: normale, avviso o critico.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Versione di query Mobility Service

Questa query traccia un grafico a torta per le macchine virtuali di Azure replicate con Site Recovery, suddivise dalla versione dell'agente di mobilità in esecuzione.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Tempo RPO query

Questa query traccia un grafico a barre delle macchine virtuali di Azure replicate con Site Recovery, suddivise in base all'obiettivo del punto di ripristino (RPO): meno di 15 minuti, tra 15-30 minuti e più di 30 minuti.

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

![RPO query](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Eseguire query Site Recovery processi

Questa query recupera tutti i processi di Site Recovery (per tutti gli scenari di ripristino di emergenza), attivati nelle ultime 72 ore e il relativo stato di completamento.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Eventi Site Recovery query

Questa query recupera tutti gli eventi Site Recovery (per tutti gli scenari di ripristino di emergenza) generati nelle ultime 72 ore, insieme alla relativa gravità. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Stato del failover di test di query (grafico a torta)

Questa query traccia un grafico a torta per lo stato del failover di test delle macchine virtuali di Azure replicate con Site Recovery.

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

### <a name="query-test-failover-state-table"></a>Stato del failover di test della query (tabella)

Questa query traccia una tabella per lo stato del failover di test delle macchine virtuali di Azure replicate con Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>RPO macchina virtuale query

Questa query traccia un grafico di tendenza che tiene traccia del RPO di una macchina virtuale di Azure specifica (ContosoVM123) per le ultime 72 ore.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![RPO macchina virtuale query](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>Frequenza di modifica dei dati delle query (varianza) per una macchina virtuale

> [!NOTE] 
> Le informazioni sulla varianza sono disponibili solo per le macchine virtuali di Azure che eseguono la replica in un'area di Azure secondaria.

Questa query traccia un grafico di tendenza per una macchina virtuale di Azure specifica (ContosoVM123), che tiene traccia della frequenza di modifica dei dati (byte scritti al secondo) e della velocità di caricamento dei dati. 

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
![Modifica dati query](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Riepilogo del ripristino di emergenza di query (da Azure ad Azure)

Questa query traccia una tabella di riepilogo per le macchine virtuali di Azure replicate in un'area di Azure secondaria.  Mostra il nome della macchina virtuale, lo stato di replica e protezione, RPO, lo stato del failover di test, la versione dell'agente di mobilità, eventuali errori di replica attivi e il percorso di origine.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Riepilogo del ripristino di emergenza per le query (server VMware/fisici)

Questa query traccia una tabella di riepilogo per le macchine virtuali VMware e i server fisici replicati in Azure.  Mostra il nome del computer, lo stato di replica e protezione, RPO, lo stato del failover di test, la versione dell'agente di mobilità, eventuali errori di replica attivi e il server di elaborazione pertinente.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Configurare gli avvisi-esempi

È possibile configurare Site Recovery avvisi in base ai dati di monitoraggio di Azure. [Altre](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) informazioni sulla configurazione degli avvisi del log. 

> [!NOTE]
> Alcuni esempi utilizzano **replicationProviderName_s** impostato su **A2A**. Questo consente di impostare gli avvisi per le macchine virtuali di Azure replicate in un'area di Azure secondaria. In questi esempi, è possibile sostituire **A2A** con **InMageAzureV2** se si vuole impostare avvisi per macchine virtuali VMware locali o server fisici replicati in Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Più computer in uno stato critico

Configurare un avviso se più di 20 macchine virtuali di Azure replicate entrano in uno stato critico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Per l'avviso impostare **valore soglia** su 20.

### <a name="single-machine-in-a-critical-state"></a>Singolo computer in uno stato critico

Configurare un avviso se una macchina virtuale di Azure replicata specifica entra in uno stato critico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Per l'avviso, impostare **valore soglia** su 1.

### <a name="multiple-machines-exceed-rpo"></a>Più computer superano RPO

Configurare un avviso se il RPO per più di 20 macchine virtuali di Azure supera 30 minuti.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Per l'avviso impostare **valore soglia** su 20.

### <a name="single-machine-exceeds-rpo"></a>Il computer singolo supera RPO

Configurare un avviso se il RPO per una singola macchina virtuale di Azure supera i 30 minuti.

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
Per l'avviso, impostare **valore soglia** su 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Il failover di test per più computer è superiore a 90 giorni

Configurare un avviso se l'ultimo failover di test riuscito è più di 90 giorni, per più di 20 macchine virtuali. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Per l'avviso impostare **valore soglia** su 20.

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
Per l'avviso, impostare **valore soglia** su 1.

### <a name="site-recovery-job-fails"></a>Errore Site Recovery processo

Configurare un avviso se un processo di Site Recovery (in questo caso il processo di riprotezione) ha esito negativo per qualsiasi scenario di Site Recovery, durante l'ultimo giorno. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Per l'avviso, impostare **valore soglia** su 1 e **periodo** su 1440 minuti per verificare gli errori nell'ultimo giorno.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [sul](site-recovery-monitor-and-troubleshoot.md) monitoraggio Site Recovery incorporato.
