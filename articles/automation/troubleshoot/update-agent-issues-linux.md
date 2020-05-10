---
title: Risoluzione dei problemi dell'agente di aggiornamento di Linux in automazione di Azure Gestione aggiornamenti
description: Informazioni su come risolvere i problemi con l'agente di Windows Update di Linux usando la soluzione Gestione aggiornamenti.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: a4082ddfd8c092a6f9223a0894f21bc734b6efb6
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997022"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Risolvere i problemi dell'agente di aggiornamento di Linux

Ci possono essere diversi motivi per cui il computer non viene visualizzato come pronto (integro) in Gestione aggiornamenti. È possibile verificare l'integrità di un agente di lavoro ibrido per Runbook di Linux per determinare il problema sottostante. Di seguito sono riportati i tre stati di conformità per un computer:

* Pronto: il ruolo di lavoro ibrido per Runbook è stato distribuito e l'ultimo è stato rilevato meno di un'ora fa.
* Disconnesso: il ruolo di lavoro ibrido per Runbook è stato distribuito e l'ultimo è stato rilevato più di un'ora fa.
* Non configurato: il ruolo di lavoro ibrido per Runbook non è stato trovato o non è stato completato il caricamento.

> [!NOTE]
> È possibile che si verifichi un lieve ritardo tra il portale di Azure visualizzato e lo stato corrente di un computer.

Questo articolo illustra come eseguire lo strumento di risoluzione dei problemi per i computer Azure dalla portale di Azure e dai computer non Azure nello [scenario offline](#troubleshoot-offline). 

> [!NOTE]
> Lo script di risoluzione dei problemi non instrada attualmente il traffico attraverso un server proxy, se ne è stato configurato uno.

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Per le macchine virtuali di Azure, selezionare il collegamento **risoluzione dei problemi** nella colonna **Aggiorna conformità agente** nel portale per aprire la pagina risoluzione dei problemi dell'agente di aggiornamento. Per i computer non Azure, il collegamento riporta a questo articolo. Per risolvere i problemi relativi a un computer non Azure, vedere le istruzioni nella sezione "risoluzione dei problemi offline".

![Pagina elenco VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> I controlli richiedono che la macchina virtuale sia in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzata **l'opzione avvia la macchina virtuale** .

Nella pagina risoluzione dei problemi dell'agente di aggiornamento selezionare **Esegui controlli** per avviare lo strumento di risoluzione dei problemi. Lo strumento di risoluzione dei problemi usa il [comando Run](../../virtual-machines/linux/run-command.md) per eseguire uno script nel computer per verificare le dipendenze. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Pagina della risoluzione dei problemi](../media/update-agent-issues-linux/troubleshoot-page.png)

Al termine dei controlli, i risultati vengono restituiti nella finestra. Le sezioni di controllo forniscono informazioni su ciò che ogni controllo cerca.

![Pagina dei controlli dell'agente di aggiornamento](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per Runbook esegue uno dei sistemi operativi seguenti.

|Sistema operativo  |Note  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede ' yum ' per restituire i dati di sicurezza, che CentOS non è disponibile.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlli di integrità dei servizi dell'agente di monitoraggio

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Questo controllo garantisce che sia installato l'agente Log Analytics per Linux. Per istruzioni su come installarlo, vedere [Installare l'agente per Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Stato agente Log Analytics

Questo controllo garantisce che l'agente di Log Analytics per Linux sia in esecuzione. Se l'agente non è in esecuzione, è possibile eseguire il comando seguente per tentare di riavviarlo. Per ulteriori informazioni sulla risoluzione dei problemi relativi all'agente, vedere la pagina relativa alla [risoluzione dei problemi di Linux Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Questo controllo determina se l'agente invia report a più aree di lavoro. Multihoming non è supportato da Gestione aggiornamenti.

### <a name="hybrid-runbook-worker"></a>Ruolo di lavoro ibrido per runbook

Questo controllo verifica se l'agente di Log Analytics per Linux ha il pacchetto di lavoro ibrido per Runbook. Questo pacchetto è necessario per il funzionamento di Gestione aggiornamenti. Per altre informazioni, vedere [l'agente di log Analytics per Linux non è in esecuzione](hybrid-runbook-worker.md#oms-agent-not-running).

Gestione aggiornamenti Scarica i pacchetti del ruolo di lavoro ibrido per Runbook dall'endpoint Operations. Pertanto, se il ruolo di lavoro ibrido per Runbook non è in esecuzione e l' [endpoint operativo](#operations-endpoint) non riesce, l'aggiornamento potrebbe non riuscire.

### <a name="hybrid-runbook-worker-status"></a>Stato del ruolo di lavoro ibrido per runbook

Questo controllo garantisce che il ruolo di lavoro ibrido per runbook sia in esecuzione nel computer. I processi nell'esempio seguente dovrebbero essere presenti se il ruolo di lavoro ibrido per Runbook è in esecuzione correttamente.


```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="general-internet-connectivity"></a>Connettività Internet generale

Questo controllo verifica che il computer abbia accesso a Internet.

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se il ruolo di lavoro ibrido per Runbook può comunicare correttamente con automazione di Azure nell'area di lavoro Log Analytics.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco di indirizzi e porte da aprire, vedere [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente di Log Analytics può comunicare correttamente con il servizio dati di runtime del processo.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco di indirizzi e porte da aprire, vedere [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Endpoint di Log Analytics 1

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Endpoint di Log Analytics 2

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Endpoint di Log Analytics 3

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare la risoluzione dei problemi offline per i ruoli di lavoro ibridi per runbook eseguendo lo script in locale. Lo script Python, [update_mgmt_health_check. py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6), è reperibile in Script Center. Di seguito è riportato un esempio dell'output di questo script:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi relativi ai ruoli di lavoro ibridi per Runbook, vedere risolvere i problemi dei ruoli di [lavoro ibridi per Runbook](hybrid-runbook-worker.md)
