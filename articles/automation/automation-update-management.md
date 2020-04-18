---
title: Soluzione Gestione aggiornamenti in Azure
description: Questo articolo descrive come usare la soluzione di gestione degli aggiornamenti di Azure per gestire gli aggiornamenti per i computer Windows e Linux.This article describes how to use the Azure Update Management solution to manage updates for your Windows and Linux machines.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617404"
---
# <a name="update-management-solution-in-azure"></a>Soluzione Gestione aggiornamenti in Azure

È possibile usare la **soluzione di gestione** degli aggiornamenti in Automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine Windows e Linux in Azure, in ambienti locali e in altri ambienti cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

È possibile abilitare Gestione aggiornamenti per le macchine virtuali usando i metodi seguenti:You can enable Update Management for virtual machines (VMs) using the following methods:

- [Dall'account di Automazione di Azure](automation-onboard-solutions-from-automation-account.md) per uno o più computer di Azure e manualmente per computer non Azure.From your Azure Automation account for one or more Azure machines and manually for non-Azure machines.

- Per una singola macchina virtuale di Azure dalla pagina Macchina virtuale nel portale di Azure.For a single Azure VM from the Virtual machine page in the Azure portal. Questo scenario è disponibile per le macchine virtuali Linux e [Windows.This](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) scenario is available for [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) and Windows VMs.

- Per più macchine virtuali di Azure selezionandole dalla pagina Macchine virtuali nel portale di Azure.For [multiple Azure VMs](manage-update-multi.md) by selecting them from the Virtual machines page in the Azure portal. 

> [!NOTE]
> La **soluzione di gestione degli aggiornamenti** richiede il collegamento di un'area di lavoro di Log Analytics all'account di automazione. Per un elenco definitivo delle aree supportate, vedere [Mapping dell'area di lavoro](./how-to/region-mappings.md)di Azure . I mapping delle aree non influiscono sulla possibilità di gestire le macchine virtuali in un'area separata dall'account di automazione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

È disponibile un modello di Azure [Resource Manager](automation-update-management-deploy-template.md) che consente di distribuire la soluzione di gestione degli **aggiornamenti** in un account di automazione nuovo o esistente e nell'area di lavoro di Log Analytics nella sottoscrizione.

## <a name="solution-overview"></a>Panoramica della soluzione

I computer gestiti da Gestione aggiornamenti utilizzano le configurazioni seguenti per eseguire la valutazione e per aggiornare le distribuzioni:

* Agente log Analytics per Windows o Linux
* PowerShell DSC (Desired State Configuration) per Linux
* Ruolo di lavoro ibrido per runbook di Automazione
* Microsoft Update o Windows Server Update Services (WSUS) per computer Windows

Il diagramma seguente illustra come la soluzione valuta e applica gli aggiornamenti della sicurezza a tutti i computer Windows Server e Linux connessi in un'area di lavoro:The following diagram illustrates how the solution assessee e applies security updates to all connected Windows Server and Linux machines in a workspace:

![Flusso del processo di Gestione aggiornamenti](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gestione aggiornamenti può essere usato per l'onboarding nativo di computer in più sottoscrizioni nello stesso tenant.

Dopo il rilascio di un pacchetto, sono necessarie da 2 a 3 ore prima che la patch venga visualizzata per le macchine Linux per la valutazione. Per le macchine Windows, sono necessarie da 12 a 15 ore perché la patch venga visualizzata per la valutazione dopo il rilascio.

Dopo che un computer ha completato un'analisi per la conformità degli aggiornamenti, l'agente inoltra le informazioni in blocco ai log di Monitoraggio di Azure.After a machine completes a scan for update compliance, the agent forwards the information in bulk to Azure Monitor logs. In un computer Windows, l'analisi di conformità viene eseguita ogni 12 ore per impostazione predefinita.

Oltre alla pianificazione dell'analisi, l'analisi della conformità degli aggiornamenti viene avviata entro 15 minuti dal riavvio dell'agente di Log Analytics, prima dell'installazione dell'aggiornamento e dopo l'installazione dell'aggiornamento.

Per una macchina Linux, l'analisi di conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente di Log Analytics viene riavviato, viene avviata un'analisi di conformità entro 15 minuti.

La soluzione indica il grado di aggiornamento del computer in base all'origine configurata per la sincronizzazione. Se il computer Windows è configurato per segnalare WSUS, a seconda di quando WSUS è stato sincronizzato l'ultima volta con Microsoft Update, i risultati potrebbero essere diversi da quelli visualizzati da Microsoft Update. Questo comportamento è lo stesso per i computer Linux configurati per la creazione di report in un repository locale anziché in un repository pubblico.

> [!NOTE]
> Per inviare correttamente un report al servizio, Gestione aggiornamenti richiede l'abilitazione di determinati URL e porte. Per altre informazioni su questi requisiti, vedere l'articolo sulla [pianificazione della rete per i ruoli di lavoro ibridi](automation-hybrid-runbook-worker.md#network-planning).

È possibile distribuire e installare gli aggiornamenti software nei computer che richiedono gli aggiornamenti creando una distribuzione pianificata. Gli aggiornamenti classificati come *facoltativi* non sono inclusi nell'ambito di distribuzione per i computer Windows.Updates classified as Optional aren't included in the deployment scope for Windows machines. Nell'ambito della distribuzione vengono inclusi solo gli aggiornamenti obbligatori.

La distribuzione pianificata definisce quali computer di destinazione ricevono gli aggiornamenti applicabili. A tale scopo, specifica in modo esplicito determinati computer o selezionando un gruppo di [computer](../azure-monitor/platform/computer-groups.md) basato su ricerche di log di un set specifico di computer (o su una query di [Azure](automation-update-management-query-logs.md) che seleziona dinamicamente le macchine virtuali di Azure in base a criteri specificati). Questi gruppi differiscono dalla [configurazione dell'ambito,](../azure-monitor/insights/solution-targeting.md)utilizzata solo per determinare quali computer ottengono i Management Pack che abilitano la soluzione.

È inoltre possibile specificare una pianificazione per l'approvazione e impostare un periodo di tempo durante il quale è possibile installare gli aggiornamenti. Questo periodo è chiamato finestra di manutenzione. Un intervallo di 20 minuti della finestra di manutenzione è riservato per i riavvii, presupponendo che ne sia necessario uno ed è stata selezionata l'opzione di riavvio appropriata. Se l'applicazione di patch richiede più tempo del previsto e la finestra di manutenzione ha meno di 20 minuti, non si verificherà un riavvio.

Gli aggiornamenti vengono installati da runbook in Automazione di Azure. Non è possibile visualizzare questi runbook e non richiedono alcuna configurazione. Quando viene creata una distribuzione di aggiornamento, viene creata una pianificazione che avvia un runbook di aggiornamento master all'ora specificata per i computer inclusi. Il runbook master avvia un runbook figlio in ogni agente per installare gli aggiornamenti necessari.

Alla data e all'ora specificate nella distribuzione dell'aggiornamento, i computer di destinazione eseguono la distribuzione in parallelo. Prima dell'installazione viene eseguita un'analisi per verificare che gli aggiornamenti siano ancora necessari. Per i computer client WSUS, se gli aggiornamenti non vengono approvati in WSUS, la distribuzione degli aggiornamenti non riesce.

La presenza di un computer registrato per la gestione degli aggiornamenti in più di un'area di lavoro di Log Analytics (nota anche come multihoming) non è supportata.

## <a name="clients"></a>Client

### <a name="supported-client-types"></a>Tipi di client supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per le valutazioni degli aggiornamenti. L'applicazione di patch richiede un ruolo di lavoro ibrido per runbook. Per informazioni sui requisiti di Hybrid Runbook Worker, vedere le guide all'installazione per l'installazione di un ruolo di lavoro ibrido di [Windows](automation-windows-hrw-install.md) e di un ruolo di lavoro [per runbook ibrido Linux.](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM e SP1 Standard)| Gestione aggiornamenti supporta solo l'esecuzione di valutazioni per questo sistema operativo, l'applicazione di patch non è supportata in quanto il ruolo di lavoro [ibrido per runbook](automation-windows-hrw-install.md) non è supportato per Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux richiedono l'accesso a un repository di aggiornamento. L'applicazione di `yum` patch basata sulla classificazione richiede la restituzione di dati di sicurezza che CentOS non ha nelle sue versioni RTM. Per ulteriori informazioni sull'applicazione di patch basata sulla classificazione in CentOS, vedere [Aggiornare le classificazioni in Linux.](automation-view-update-assessments.md#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux richiedono l'accesso a un repository di aggiornamento.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux richiedono l'accesso a un repository di aggiornamento.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux richiedono l'accesso a un repository di aggiornamento.         |

> [!NOTE]
> I set di scalabilità delle macchine virtuali di Azure possono essere gestiti tramite Gestione aggiornamenti. Gestione aggiornamenti funziona sulle istanze stesse e non sull'immagine di base. È necessario pianificare gli aggiornamenti in modo incrementale, in modo che non tutte le istanze della macchina virtuale vengano aggiornate contemporaneamente.
> È possibile aggiungere nodi per i set di scalabilità di macchine virtuali seguendo i passaggi descritti in [Onboard in un computer non Azure.](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Nella tabella seguente sono elencati i sistemi operativi non supportati:

|Sistema operativo  |Note  |
|---------|---------|
|Client Windows     | I sistemi operativi client, ad esempio Windows 7 e Windows 10, non sono supportati.        |
|Windows Server 2016 Nano Server     | Non supportato.       |
|Azure Kubernetes Service Nodes | Non supportato. Usare il processo di applicazione delle patch descritto in Applicare gli aggiornamenti della sicurezza e del kernel ai nodi Linux nel servizio Kubernetes di AzureUsare the patching process described in [Apply security and kernel updates to Linux nodes in Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisiti per i client

Le informazioni seguenti descrivono i requisiti client specifici del sistema operativo. Per ulteriori informazioni, vedere [Pianificazione della rete](#ports).

#### <a name="windows"></a>Windows

Gli agenti Windows devono essere configurati per comunicare con un server WSUS oppure richiedono l'accesso a Microsoft Update.

È possibile usare Gestione aggiornamenti con Configuration Manager.You can use Update Management with Configuration Manager. Per altre informazioni sugli scenari di integrazione, vedere [Integrare Configuration Manager con Gestione aggiornamenti.](oms-solution-updatemgmt-sccmintegration.md#configuration) [L'agente Windows di Log Analytics](../azure-monitor/platform/agent-windows.md) è obbligatorio. L'agente viene installato automaticamente se si esegue l'onboarding di una macchina virtuale di Azure.The agent is installed automatically if you're onboarding an Azure VM.

Per impostazione predefinita, le macchine virtuali Windows distribuite da Azure Marketplace sono impostate per ricevere gli aggiornamenti automatici dal servizio Windows Update.By default, Windows VMs that are deployed from the Azure Marketplace are set to receive automatic updates from Windows Update Service. Questo comportamento non cambia quando si aggiunge questa soluzione o si aggiungono macchine virtuali Windows all'area di lavoro. Se gli aggiornamenti non vengono gestiti attivamente con questa soluzione, è applicabile il comportamento predefinito, ovvero gli aggiornamenti vengono applicati automaticamente.

> [!NOTE]
> Un utente può modificare Criteri di gruppo in modo che i riavvii dei computer possano essere eseguiti solo dall'utente, non dal sistema. I computer gestiti possono bloccarsi se Gestione aggiornamenti non dispone dei diritti per riavviare il computer senza interazione manuale da parte dell'utente.
>
> Per ulteriori informazioni, vedere Configurare le impostazioni di [Criteri di gruppo per Aggiornamenti automatici](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Per Linux, il computer richiede l'accesso a un repository di aggiornamento. Il repository degli aggiornamenti può essere privato o pubblico. Per interagire con Gestione aggiornamenti è necessario TLS 1.1 o TLS 1.2. In questa soluzione non è supportato un agente Log Analytics agent per Linux configurato per la creazione di report in più aree di lavoro di Log Analytics. Nel computer deve essere installato anche Python 2.x.

Per informazioni su come installare l'agente Log Analytics per Linux e scaricare la versione più recente, vedere [Agente Log Analytics per Linux.](../azure-monitor/platform/agent-linux.md) Per informazioni su come installare l'agente log Analytics per Windows, vedere [Connettere computer Windows ad Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Le macchine virtuali create dalle immagini Red Hat Enterprise Linux (RHEL) su richiesta disponibili in Azure Marketplace vengono registrate per accedere [all'infrastruttura RHUI (Red Hat Update Infrastructure)](../virtual-machines/workloads/redhat/redhat-rhui.md) distribuita in Azure.VMs that were created from the on-demand Red Hat Enterprise Linux (RHEL) images that are available in the Azure Marketplace are registered to access the Red Hat Update Infrastructure (RHUI) that's deployed in Azure. Qualsiasi altra distribuzione Linux deve essere aggiornata dal repository di file online della distribuzione utilizzando i metodi supportati della distribuzione.

## <a name="permissions"></a>Autorizzazioni

Per creare e gestire le distribuzioni degli aggiornamenti, sono necessarie autorizzazioni specifiche. Per ulteriori informazioni su queste autorizzazioni, vedere [Accesso basato sui ruoli - Gestione aggiornamenti](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Componenti della soluzione

La soluzione è costituita dalle risorse seguenti. Queste risorse vengono aggiunte automaticamente all'account di automazione quando si abilita la soluzione. 

### <a name="hybrid-worker-groups"></a>Gruppi di ruoli di lavoro ibridi

Dopo aver abilitato questa soluzione, qualsiasi computer Windows connesso direttamente all'area di lavoro di Log Analytics viene configurato automaticamente come lavoratore ibrido per supportare i runbook inclusi in questa soluzione.

Ogni computer Windows gestito dalla soluzione è elencato nel riquadro Gruppi di lavoro ibridi come gruppo di lavoro ibrido di sistema per l'account di automazione. Le soluzioni `Hostname FQDN_GUID` utilizzano la convenzione di denominazione. Non è possibile applicare runbook a questi gruppi nell'account. Se si tenta, il tentativo ha esito negativo. Questi gruppi hanno lo scopo di supportare solo questa soluzione di gestione.

È possibile aggiungere il computer Windows a un gruppo Hybrid Runbook Worker nell'account di automazione per supportare i runbook di automazione se si usa lo stesso account sia per la soluzione che per l'appartenenza al gruppo Hybrid Runbook Worker. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="management-packs"></a>Management Pack

Se il gruppo di gestione di System Center Operations Manager è [connesso a un'area di lavoro](../azure-monitor/platform/om-agents.md)di Log Analytics , in Operations Manager vengono installati i seguenti Management Pack. Questi Management Pack vengono installati anche nei computer Windows connessi direttamente dopo l'aggiunta della soluzione. Non è necessario configurare o gestire questi Management Pack.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Se si dispone di un gruppo di gestione di Operations Manager 1807 o 2019 connesso a un'area `IsAutoRegistrationEnabled` di lavoro di Log Analytics con agenti configurati nel gruppo di gestione per raccogliere i dati di log, è necessario sostituire il parametro e impostarlo su True nella regola **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Per altre informazioni su come vengono aggiornati i Management Pack della soluzione, vedere [Connettere Operations Manager ai log](../azure-monitor/platform/om-agents.md)di Monitoraggio di Azure.

> [!NOTE]
> Affinché i computer con l'agente Operations Manager siano completamente gestiti da Gestione aggiornamenti, l'agente deve essere aggiornato all'agente di Log Analytics per Windows o all'agente di Log Analytics per Linux. Per informazioni su come aggiornare l'agente, vedere [How to upgrade an Operations Manager agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents) (Come aggiornare un agente di Operations Manager). Negli ambienti che utilizzano Operations Manager, è necessario eseguire System Center Operations Manager 2012 R2 UR 14 o versione successiva.

## <a name="data-collection"></a>Raccolta dati

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse supportate da questa soluzione:

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso.<br/><br/>Non è necessaria una connessione diretta dall'agente Operations Manager ai log di Monitoraggio di Azure.A direct connection from the Operations Manager agent to Azure Monitor logs isn't required. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Un'analisi viene eseguita due volte al giorno per ogni macchina Windows gestita. Ogni 15 minuti viene chiamata l'API Windows per eseguire una query su data/ora dell'ultimo aggiornamento e determinare se lo stato è cambiato. Se lo stato è cambiato, viene avviata un'analisi della conformità.

Viene eseguita una scansione ogni ora per ogni macchina Linux gestita.

La visualizzazione dei dati aggiornati dalle macchine gestite dal dashboard può richiedere da 30 minuti a 6 ore.

L'utilizzo medio dei dati da parte dei log di Monitoraggio di Azure per un computer che usa Gestione aggiornamenti è di circa 25 megabyte (MB) al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, a seconda dell'ambiente. È consigliabile monitorare l'ambiente per tenere traccia dell'utilizzo esatto. Per ulteriori informazioni sull'analisi dell'utilizzo dei dati, vedere [Gestire l'utilizzo e](../azure-monitor/platform/manage-cost-storage.md)i costi.

## <a name="network-planning"></a><a name="ports"></a>Pianificazione della rete

I seguenti indirizzi sono necessari e specifici per Gestione aggiornamenti. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Per i computer Windows, è inoltre necessario consentire il traffico verso qualsiasi endpoint richiesto da Windows Update. È possibile trovare un elenco aggiornato degli endpoint richiesti in [Problemi relativi a HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se si dispone di un [server Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)locale, è inoltre necessario consentire il traffico verso il server specificato nella chiave [WSUS.](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)

Per le macchine Linux Red Hat, vedere IP per i server di distribuzione dei [contenuti RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) per gli endpoint necessari. Per altre distribuzioni Linux, vedere la documentazione del provider.

Per ulteriori informazioni sulle porte necessarie per il ruolo Hybrid Runbook Worker, vedere [Hybrid Worker role ports](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Si consiglia di utilizzare gli indirizzi elencati quando si definiscono le eccezioni. Per gli indirizzi IP, è possibile scaricare [gli intervalli IP del data center di Microsoft Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Questo file viene aggiornato settimanalmente e riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Seguire le istruzioni in [Connettere computer senza accesso a Internet](../azure-monitor/platform/gateway.md) per configurare i computer che non dispongono dell'accesso a Internet.

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

Nelle tabelle che seguono sono riportate le classificazioni degli aggiornamenti in Gestione aggiornamenti, con una definizione per ogni classificazione.

### <a name="windows"></a>Windows

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici     | Un aggiornamento per un problema specifico che risolve un bug critico non correlato alla sicurezza.        |
|Aggiornamenti per la sicurezza     | Un aggiornamento per un problema specifico del prodotto correlato alla sicurezza.        |
|Aggiornamenti cumulativi     | Un set cumulativo di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Un set cumulativo di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Un aggiornamento per un virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consente di completare una o più attività.        |
|Aggiornamenti     | Un aggiornamento di un'applicazione o un file attualmente installati.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti che non sono di natura critica o che non sono aggiornamenti della sicurezza.        |

Per Linux, la gestione degli aggiornamenti è in grado di distinguere tra aggiornamenti critici e aggiornamenti della sicurezza nel cloud, visualizzando al contempo i dati di valutazione a causa dell'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non ha queste informazioni disponibili nella versione RTM. Se si dispone di macchine CentOS configurate per restituire i dati di sicurezza per il comando seguente, Gestione aggiornamenti può applicare patch in base alle classificazioni.

```bash
sudo yum -q --security check-update
```

Attualmente non è supportato alcun metodo per abilitare la disponibilità dei dati di classificazione nativi in CentOS.There's currently no supported method to enable native classification-data availability on CentOS. Al momento, viene fornito solo il supporto per il massimo sforzo ai clienti che potrebbero averabilitato questa operazione da soli. 

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-security. Su Red Hat Enterprise Linux 7, il plugin è già una parte di yum stesso, non c'è bisogno di installare nulla. Per ulteriori informazioni, vedere il seguente [articolo della Knowledge](https://access.redhat.com/solutions/10021)Base Red Hat .

## <a name="integrate-with-configuration-manager"></a>Integrazione con Configuration Manager

I clienti che hanno investito in Microsoft Endpoint Configuration Manager per la gestione di PC, server e dispositivi mobili si basano anche sulla forza e la maturità di Configuration Manager per aiutarli a gestire gli aggiornamenti software. Configuration Manager fa parte del ciclo di gestione degli aggiornamenti software (SUM).

Per informazioni su come integrare la soluzione di gestione con Configuration Manager, vedere [Integrare Configuration Manager con Gestione aggiornamenti.](oms-solution-updatemgmt-sccmintegration.md)

### <a name="third-party-updates-on-windows"></a>Aggiornamenti di terze parti in Windows

Gestione aggiornamenti si basa sul repository di aggiornamento configurato localmente per aggiornare i sistemi Windows supportati. Si tratta di WSUS o Windows Update. Strumenti come [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) consentono di importare e pubblicare aggiornamenti personalizzati con WSUS. Questo scenario consente alla gestione degli aggiornamenti di aggiornare i computer che usano Configuration Manager come repository di aggiornamento con software di terze parti. Per informazioni su come configurare Updates Publisher, vedere [Installare Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Abilitare la gestione degli aggiornamentiEnable Update Management

Per iniziare l'aggiornamento dei sistemi, è necessario abilitare la soluzione di gestione degli aggiornamenti. Di seguito sono riportati i metodi consigliati e supportati per l'onboarding della soluzione:

- [Da una macchina virtuale](automation-onboard-solutions-from-vm.md)
- [Dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md)
- [Dall'account di Automazione](automation-onboard-solutions-from-automation-account.md)
- [Con un runbook di Automazione di Azure](automation-onboard-solutions.md)
- [Con un modello di Azure Resource Manager](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Passaggi successivi

Esaminare le [domande frequenti](automation-faq.md) sull'automazione di Azure per esaminare le domande comuni su questa soluzione.
