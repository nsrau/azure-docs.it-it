---
title: Distribuire un ruolo di lavoro ibrido per runbook di Linux in Automazione di Azure
description: Questo articolo descrive come installare un ruolo di lavoro ibrido per Runbook di automazione di Azure per eseguire manuali operativi in computer basati su Linux nel Data Center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: 07c04274829530942bab0e3267200f6524c4ddc7
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95793413"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Linux

È possibile usare la funzionalità user Hybrid Runbook Worker di automazione di Azure per eseguire manuali operativi direttamente nel computer Azure o non Azure, inclusi i server registrati con i [server abilitati per Azure Arc](../azure-arc/servers/overview.md). Dal computer o dal server che ospita il ruolo, è possibile eseguire manuali operativi direttamente e in base alle risorse nell'ambiente per gestire tali risorse locali.

Il ruolo di lavoro ibrido per runbook di Linux esegue i runbook come un utente speciale a cui possono essere garantiti privilegi elevati per eseguire i comandi che richiedono l'elevazione. Automazione di Azure archivia e gestisce manuali operativi e li recapita a uno o più computer designati. Questo articolo descrive come installare il ruolo di lavoro ibrido per runbook in un computer Linux, come rimuovere il ruolo di lavoro e come rimuovere un gruppo di ruoli di lavoro ibridi per runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre di quanto segue.

### <a name="a-log-analytics-workspace"></a>Area di lavoro Log Analytics

Il ruolo di lavoro ibrido per Runbook dipende da un'area di lavoro di monitoraggio di Azure Log Analytics per installare e configurare il ruolo. È possibile crearlo tramite [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), tramite [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)o nel [portale di Azure](../azure-monitor/learn/quick-create-workspace.md).

Se non si dispone di un'area di lavoro Log Analytics di monitoraggio di Azure, vedere le [indicazioni sulla progettazione dei log di monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md) prima di creare l'area di lavoro

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Il ruolo di lavoro ibrido per Runbook richiede l' [agente di log Analytics](../azure-monitor/platform/log-analytics-agent.md) per il sistema operativo Linux supportato. Per i server o i computer ospitati all'esterno di Azure, è possibile installare l'agente di Log Analytics usando i [server abilitati per Azure Arc](../azure-arc/servers/overview.md).

>[!NOTE]
>Dopo aver installato l'agente di Log Analytics per Linux, è consigliabile non modificare le autorizzazioni della `sudoers.d` cartella o della relativa proprietà. L'autorizzazione sudo è obbligatoria per l'account **nxautomation** , ovvero il contesto utente in cui è in esecuzione il ruolo di lavoro ibrido per Runbook. Le autorizzazioni non devono essere rimosse. La limitazione di questo problema a determinate cartelle o comandi può comportare una modifica sostanziale.
>

### <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

La funzionalità Hybrid Runbook Worker supporta le distribuzioni seguenti. Si presuppone che tutti i sistemi operativi siano x64. x86 non è supportato per alcun sistema operativo.

* Amazon Linux da 2012,09 a 2015,09
* CentOS Linux 5, 6 e 7
* Oracle Linux 5, 6 e 7
* Server Red Hat Enterprise Linux 5, 6 e 7
* Debian GNU/Linux 6, 7 e 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS e 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="minimum-requirements"></a>Requisiti minimi

I requisiti minimi per un sistema Linux e un ruolo di lavoro ibrido per Runbook utente sono i seguenti:

* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

| **Pacchetto obbligatorio** | **Descrizione** | **Versione minima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Libreria GNU C| 2.5-12 |
|Openssl| Librerie OpenSSL | 1.0 (sono supportati TLS 1.1 e TLS 1.2)|
|Curl | Client Web cURL | 7.15.5|
|Python-ctypes | Python 2.x è obbligatorio |
|PAM | Moduli di autenticazione modulare|
| **Pacchetto facoltativo** | **Descrizione** | **Versione minima**|
| PowerShell Core | Per eseguire PowerShell manuali operativi, è necessario installare PowerShell core. Vedere [Installazione di PowerShell Core in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) per informazioni su come installarlo. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Aggiunta di un computer a un gruppo di ruolo di lavoro ibrido per Runbook

È possibile aggiungere il computer di lavoro a un gruppo di ruolo di lavoro ibrido per Runbook in uno degli account di automazione. Per i computer che ospitano il ruolo di lavoro ibrido per Runbook di sistema gestito da Gestione aggiornamenti, possono essere aggiunti a un gruppo di lavoro ibrido per Runbook. È tuttavia necessario usare lo stesso account di automazione sia per Gestione aggiornamenti che per l'appartenenza al gruppo di lavoro ibrido per Runbook.

>[!NOTE]
>Automazione di Azure [Gestione aggiornamenti](update-management/update-mgmt-overview.md) installa automaticamente il ruolo di lavoro ibrido per Runbook di sistema in un computer Azure o non Azure abilitato per l'gestione aggiornamenti. Questo ruolo di lavoro, tuttavia, non è registrato con alcun gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione. Per eseguire il manuali operativi in questi computer, è necessario aggiungerli a un gruppo di lavoro ibrido per Runbook. Seguire il passaggio 4 nella sezione [installare un ruolo di lavoro ibrido per Runbook di Linux](#install-a-linux-hybrid-runbook-worker) per aggiungerlo a un gruppo.

## <a name="supported-linux-hardening"></a>Protezione avanzata Linux supportata

Gli elementi seguenti non sono ancora supportati:

* CIS

## <a name="supported-runbook-types"></a>Tipi di runbook supportati

I ruoli di lavoro ibridi per Runbook di Linux supportano un set limitato di tipi Runbook in automazione di Azure, descritti nella tabella seguente.

|Tipo Runbook | Supportato |
|-------------|-----------|
|Python 2 |Sì |
|PowerShell |Sì<sup>1</sup> |
|Flusso di lavoro PowerShell |No |
|Grafico |No |
|Grafico del flusso di lavoro di PowerShell |No |

<sup>1</sup> PowerShell manuali operativi richiede l'installazione di PowerShell core nel computer Linux. Vedere [Installazione di PowerShell Core in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) per informazioni su come installarlo.

### <a name="network-configuration"></a>Configurazione di rete

Per i requisiti di rete per il ruolo di lavoro ibrido per Runbook, vedere [configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installare un ruolo di lavoro ibrido per runbook di Linux

Per installare e configurare un ruolo di lavoro ibrido per Runbook di Linux, seguire questa procedura.

1. Abilitare la soluzione automazione di Azure nell'area di lavoro di Log Analytics eseguendo il comando seguente in un prompt dei comandi di PowerShell con privilegi elevati o in Cloud Shell nel [portale di Azure](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Distribuire l'agente di Log Analytics nel computer di destinazione.

    * Per le macchine virtuali di Azure, installare l'agente Log Analytics per Linux usando l' [estensione della macchina virtuale per Linux](../virtual-machines/extensions/oms-linux.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente. È possibile usare un modello di Azure Resource Manager, l'interfaccia della riga di comando di Azure o criteri di Azure per assegnare il criterio di [distribuzione agente log Analytics per le VM *Linux* o *Windows*](../governance/policy/samples/built-in-policies.md#monitoring) . Una volta installato l'agente, è possibile aggiungere il computer a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione.

    * Per i computer non Azure è possibile installare l'agente di Log Analytics usando i [server abilitati per Azure Arc](../azure-arc/servers/overview.md). I server abilitati per Arc supportano la distribuzione dell'agente di Log Analytics usando i metodi seguenti:

        - Uso del framework delle estensioni VM.

            Questa funzionalità in Azure Arc Enabled Server consente di distribuire l'estensione di macchina virtuale Log Analytics Agent in un server Windows e/o Linux non Azure. Le estensioni di macchina virtuale possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti da server abilitati per Arc:

            - Il[portale di Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - L'[interfaccia della riga di comando di Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Modelli di gestione risorse](../azure-arc/servers/manage-vm-extensions-template.md) di Azure

        - Uso di criteri di Azure.

            Con questo approccio, è possibile usare i criteri di [distribuzione di log Analytics Agent di Azure Policy to Linux o Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) per controllare se nel server abilitato per Arc è installato log Analytics Agent. Se l'agente non è installato, lo distribuisce automaticamente utilizzando un'attività di correzione. In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../governance/policy/samples/built-in-initiatives.md#monitoring) per installare e configurare l'agente di log Analytics.

        Si consiglia di installare l'agente di Log Analytics per Windows o Linux usando criteri di Azure.

    > [!NOTE]
    > Per gestire la configurazione dei computer che supportano il ruolo di lavoro ibrido per runbook con desired state Configuration (DSC), è necessario aggiungere i computer come nodi DSC.

    > [!NOTE]
    > L'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) deve essere presente con le autorizzazioni sudo corrispondenti durante l'installazione di un ruolo di lavoro ibrido per runbook di Linux. Se si prova a installare il ruolo di lavoro e l'account non è presente o non ha le autorizzazioni appropriate, l'installazione non riesce.

3. Verificare che l'agente stia segnalando l'area di lavoro.

    L'agente di Log Analytics per Linux connette i computer a un'area di lavoro Log Analytics di monitoraggio di Azure. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per Runbook.

    Quando l'agente si è connesso correttamente all'area di lavoro Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati di heartbeat all'area di lavoro.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nei risultati della ricerca verranno visualizzati i record heartbeat per il computer, a indicare che sono connessi e segnalati al servizio. Per impostazione predefinita, ogni agente trasmette un record di heartbeat all'area di lavoro assegnata.

4. Eseguire il comando seguente per aggiungere il computer a un gruppo di ruolo di lavoro ibrido per Runbook, specificando i valori per i parametri `-w` ,, `-k` `-g` e `-e` .

    È possibile ottenere le informazioni necessarie per i parametri `-k` e `-e` dalla pagina **chiavi** nell'account di automazione. Selezionare **chiavi** nella sezione **Impostazioni account** nella parte sinistra della pagina.

    ![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Per il `-e` parametro, copiare il valore di **URL**.

    * Per il `-k` parametro, copiare il valore della **chiave di accesso primaria**.

    * Per il `-g` parametro, specificare il nome del gruppo di lavoro ibrido per Runbook a cui deve essere aggiunto il nuovo ruolo di lavoro ibrido per Runbook di Linux. Se questo gruppo è già presente nell'account di automazione, viene aggiunto al computer corrente. Se il gruppo non esiste, viene creato con tale nome.

    * Per il `-w` parametro, specificare l'ID dell'area di lavoro log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Verificare la distribuzione dopo il completamento dello script. Dalla pagina dei **gruppi di ruoli di lavoro ibridi per Runbook** nell'account di automazione, nella scheda **gruppo Runbook Worker ibrido utente** viene visualizzato il gruppo nuovo o esistente e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco della pagina, dal menu a sinistra scegliere ruoli di **lavoro ibridi**. Nella pagina ruoli di **lavoro ibridi** è possibile visualizzare tutti i membri del gruppo elencati.

    > [!NOTE]
    > Se si usa l'estensione della macchina virtuale Log Analytics per Linux per una macchina virtuale di Azure, è consigliabile impostare `autoUpgradeMinorVersion` su `false` come le versioni di aggiornamento automatico possono causare problemi con il ruolo di lavoro ibrido per Runbook. Per informazioni su come aggiornare manualmente l'estensione, vedere [Distribuzione dell'interfaccia della riga di comando di Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Disattivare la convalida della firma

Per impostazione predefinita, i ruoli di lavoro ibridi per runbook di Linux richiedono la convalida della firma. Se si esegue un runbook senza firma in un ruolo di lavoro, viene visualizzato un errore `Signature validation failed`. Per disattivare la convalida della firma, eseguire il comando seguente. Sostituire il secondo parametro con l'ID dell'area di lavoro Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per Runbook

È possibile usare il comando `ls /var/opt/microsoft/omsagent` nel ruolo di lavoro ibrido per runbook per ottenere l'ID dell'area di lavoro. Viene creata una cartella denominata con l'ID dell'area di lavoro.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Questo script non rimuove l'agente di Log Analytics per Linux dal computer. Rimuove solo la funzionalità e la configurazione del ruolo di lavoro ibrido per runbook.

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo di computer Linux ibrido per Runbook, è possibile usare gli stessi passaggi di un gruppo di lavoro ibrido di Windows. Vedere [Rimuovere un gruppo di ruoli di lavoro ibridi](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi dei ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook - Linux](troubleshoot/hybrid-runbook-worker.md#linux).
