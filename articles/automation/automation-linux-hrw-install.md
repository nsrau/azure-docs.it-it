---
title: Ruolo di lavoro ibrido per runbook Linux di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook sui computer Linux nel data center locale o nell'ambiente cloud.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267863"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Linux

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. Il ruolo di lavoro ibrido per runbook di Linux esegue i runbook come un utente speciale a cui possono essere garantiti privilegi elevati per eseguire i comandi che richiedono l'elevazione. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati.

Questo articolo illustra come installare il ruolo di lavoro ibrido per runbook in un computer Linux.

## <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

La funzionalità ruolo di lavoro ibrido per runbook supporta le distribuzioni seguenti:

* Amazon Linux da 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS e 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Installazione di un ruolo di lavoro ibrido per runbook di Linux

La procedura per installare e configurare manualmente un ruolo di lavoro ibrido per runbook in un computer Linux è semplice. È necessario abilitare la soluzione **Ruolo di lavoro ibrido per runbook di Automazione** nell'area di lavoro di Log Analytics di Azure e quindi eseguire un set di comandi per registrare il computer come ruolo di lavoro e aggiungerlo a un gruppo.

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Linux sono i seguenti:

* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="package-requirements"></a>Requisiti dei pacchetti

| **Pacchetto obbligatorio** | **Descrizione** | **Versione minima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Libreria GNU C| 2.5-12 |
|Openssl| Librerie OpenSSL | 0.9.8e o 1.0|
|Curl | Client Web cURL | 7.15.5|
|Python-ctypes | |
|PAM | Moduli di autenticazione modulare|
| **Pacchetto facoltativo** | **Descrizione** | **Versione minima**|
| PowerShell Core | Per eseguire i runbook di PowerShell, PowerShell deve essere installato. Vedere [installazione di PowerShell Core in Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) per informazioni su come installarlo.  | 6.0.0 |

### <a name="installation"></a>Installazione

Prima di procedere, prendere nota dell'area di lavoro di Log Analytics a cui è collegato l'account di Automazione. Prendere anche nota della chiave primaria per l'account di Automazione. È possibile trovare entrambi nel portale di Azure, selezionando l'account di Automazione, **Area di lavoro** per l'ID dell'area di lavoro e selezionando **Chiavi** per la chiave primaria. Per informazioni sulle porte e sugli indirizzi necessari per il ruolo di lavoro ibrido per runbook, vedere [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

1. Abilitare la soluzione **Ruolo di lavoro ibrido per runbook di Automazione** in Azure usando uno dei metodi seguenti:

   * Aggiungere la soluzione **Ruolo di lavoro ibrido per runbook di Automazione** alla sottoscrizione usando la procedura descritta in [Aggiungere soluzioni di gestione di Log Analytics di Azure all'area di lavoro](../log-analytics/log-analytics-add-solutions.md).
   * Eseguire il cmdlet seguente:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installare l'agente di Operations Management Suite per Linux usando il comando seguente. Sostituire \<WorkspaceID\> e \<WorkspaceKey\> con i valori appropriati dall'area di lavoro.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Eseguire il comando seguente modificando i valori per i parametri *-w*, *-k*, *-g* e *-e*. Per il parametro *-g* sostituire il valore con il nome del gruppo del ruolo di lavoro ibrido per runbook a cui deve essere aggiunto il nuovo ruolo di lavoro ibrido per runbook di Linux. Se il nome non esiste già nell'account di Automazione, viene eseguito un nuovo ruolo di lavoro ibrido per runbook con lo stesso nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Dopo il completamento del comando, nella pagina **Gruppi di ruoli di lavoro ibridi** nel portale di Azure vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo nell'elenco della pagina **Gruppi di ruoli di lavoro ibridi** e selezionare il riquadro **Ruoli di lavoro per runbook**. Nella pagina **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.

## <a name="turning-off-signature-validation"></a>Disattivazione della convalida della firma

Per impostazione predefinita, i ruoli di lavoro ibridi per runbook di Linux richiedono la convalida della firma. Se si esegue un runbook non firmato su un ruolo di lavoro, viene visualizzato un errore che indica "Convalida firma non riuscita". Per disattivare la convalida della firma, eseguire il comando seguente. Sostituire il secondo parametro con l'ID dell'area di lavoro di Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipi di runbook supportati

I ruoli di lavoro ibridi per runbook di Linux non supportano il set completo di tipi di runbook in Automazione di Azure.

I tipi di runbook seguenti possono essere usati in un ruolo di lavoro ibrido di Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > I runbook di PowerShell richiedono che PowerShell Core sia installato nel computer Linux. Vedere [Installazione di PowerShell Core in Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) per informazioni su come installarlo.

I tipi di runbook seguenti non possono essere usati in un ruolo di lavoro ibrido di Linux:

* Flusso di lavoro PowerShell
* Grafico
* Grafico del flusso di lavoro di PowerShell

## <a name="troubleshooting"></a>risoluzione dei problemi

Il ruolo di lavoro ibrido per runbook di Linux dipende dall'agente OMS per Linux per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore.

### <a name="the-oms-agent-for-linux-isnt-running"></a>L'agente di Operations Management Suite per Linux non è in esecuzione

Se l'agente di Operations Management Suite per Linux non è in esecuzione, il ruolo di lavoro ibrido per runbook di Linux non è in grado di comunicare con Automazione di Azure. Verificare che l'agente sia in esecuzione immettendo il comando `ps -ef | grep python`. 

L'output dovrebbe essere simile al seguente (processi Python con l'account utente **nxautomation**). Se la soluzione Gestione aggiornamenti o Automazione di Azure non è abilitata, nessuno dei processi seguenti verrà eseguito.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Vengono avviati i processi seguenti per un ruolo di lavoro ibrido per runbook di Linux. Si trovano tutti nella directory `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf**: questo è il processo di gestione del ruolo di lavoro. Viene avviato direttamente da Configurazione dello stato desiderato (DSC).

* **worker.conf**: questo è il processo del ruolo di lavoro ibrido registrato automaticamente. Viene avviato dal gestore del ruolo di lavoro. Questo processo viene usato da Gestione aggiornamenti ed è trasparente all'utente. Questo processo è presente solo se la soluzione Gestione aggiornamenti è abilitata nel computer.

* **diy/worker.conf**: questo è il processo del ruolo di lavoro ibrido registrato manualmente. Il processo del ruolo di lavoro ibrido registrato manualmente viene usato per eseguire i runbook dell'utente nel ruolo di lavoro ibrido per runbook. La differenza con il processo del ruolo di lavoro ibrido registrato automaticamente è solo che usa una configurazione diversa. Questo processo è presente solo se la soluzione Automazione di Azure è abilitata e il ruolo di lavoro ibrido di Linux registrato manualmente è registrato.

Se l'agente di Operations Management Suite per Linux non è in esecuzione, eseguire il comando seguente per avviare il servizio: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-doesnt-exist"></a>La classe specificata non esiste

Se viene visualizzato l'errore "La classe specificata non esiste" in `/var/opt/microsoft/omsconfig/omsconfig.log`, l'agente di Operations Management per Linux deve essere aggiornato. Eseguire il comando seguente per reinstallare l'agente OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Per altre procedure di risoluzione dei problemi con Gestione aggiornamenti, vedere [Gestione degli aggiornamenti - Risoluzione dei problemi](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Rimuovere ruoli di lavoro ibridi di Automazione di Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
