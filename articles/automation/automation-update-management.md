---
title: Gestione aggiornamenti in automazione di Azure
description: Questo articolo descrive la funzionalità Gestione aggiornamenti che consente di gestire gli aggiornamenti per i computer Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: ca5d8c35aea06143e058aade473282a038212605
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872160"
---
# <a name="update-management-in-azure-automation"></a>Gestione aggiornamenti in automazione di Azure

È possibile usare Gestione aggiornamenti in automazione di Azure per gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux in Azure, negli ambienti locali e in altri ambienti cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

È possibile abilitare Gestione aggiornamenti per le macchine virtuali (VM) utilizzando i metodi seguenti:

* Dall' [account di automazione di Azure](automation-onboard-solutions-from-automation-account.md) per uno o più computer Azure.
* Manualmente per computer non Azure.
* Per una singola VM di Azure dalla pagina della macchina virtuale nella portale di Azure. Questo scenario è disponibile per le macchine virtuali [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .
* Per [più macchine virtuali di Azure](manage-update-multi.md) , selezionarle nella pagina macchine virtuali della portale di Azure.

> [!NOTE]
> Gestione aggiornamenti richiede il collegamento di un'area di lavoro Log Analytics all'account di automazione. Per un elenco definitivo delle aree supportate, vedere [mapping dell'area di lavoro di Azure](how-to/region-mappings.md). I mapping dell'area non influiscono sulla possibilità di gestire le macchine virtuali in un'area separata dall'account di automazione.

È disponibile un [modello di Azure Resource Manager](automation-update-management-deploy-template.md) per semplificare la distribuzione di gestione aggiornamenti a un account di automazione nuovo o esistente e log Analytics area di lavoro nella sottoscrizione.

> [!NOTE]
> Non è possibile usare un computer configurato con Gestione aggiornamenti per eseguire script personalizzati da automazione di Azure. Questo computer può eseguire solo lo script di aggiornamento firmato da Microsoft. 

## <a name="update-management-overview"></a>Panoramica di Gestione aggiornamenti

I computer gestiti da Gestione aggiornamenti utilizzano le configurazioni seguenti per eseguire la valutazione e aggiornare le distribuzioni:

* Agente di Log Analytics per Windows o Linux
* PowerShell DSC (Desired State Configuration) per Linux
* Ruolo di lavoro ibrido per runbook di Automazione
* Microsoft Update o Windows Server Update Services (WSUS) per computer Windows

Il diagramma seguente illustra il modo in cui Gestione aggiornamenti valuta e applica gli aggiornamenti della sicurezza a tutti i computer Windows Server e Linux connessi in un'area di lavoro:

![Flusso di lavoro Gestione aggiornamenti](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gestione aggiornamenti può essere usato per l'onboarding nativo di computer in più sottoscrizioni nello stesso tenant.

Dopo il rilascio di un pacchetto, sono necessarie da 2 a 3 ore per la visualizzazione della patch per i computer Linux per la valutazione. Per i computer Windows, sono necessarie da 12 a 15 ore per la visualizzazione della patch per la valutazione dopo che è stata rilasciata.

Quando un computer completa un'analisi per la conformità degli aggiornamenti, l'agente trasmette le informazioni in blocco ai log di monitoraggio di Azure. In un computer Windows, l'analisi di conformità viene eseguita ogni 12 ore per impostazione predefinita.

Oltre alla pianificazione dell'analisi, l'analisi per la conformità degli aggiornamenti viene avviata entro 15 minuti dal riavvio dell'agente di Log Analytics, prima dell'installazione dell'aggiornamento e dopo l'installazione dell'aggiornamento.

Per un computer Linux, l'analisi di conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente di Log Analytics viene riavviato, un'analisi di conformità viene avviata entro 15 minuti.

Gestione aggiornamenti segnala la modalità di aggiornamento del computer in base all'origine con la quale è stata configurata la sincronizzazione. Se il computer Windows è configurato per la segnalazione a WSUS, a seconda del momento in cui WSUS è stato sincronizzato con Microsoft Update, i risultati potrebbero essere diversi da quelli Microsoft Update visualizzati. Questo comportamento è lo stesso per i computer Linux configurati per segnalare a un repository locale anziché a un repository pubblico.

> [!NOTE]
> Per inviare correttamente un report al servizio, Gestione aggiornamenti richiede l'abilitazione di determinati URL e porte. Per altre informazioni su questi requisiti, vedere [configurazione di rete](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning).

È possibile distribuire e installare gli aggiornamenti software nei computer che richiedono gli aggiornamenti creando una distribuzione pianificata. Gli aggiornamenti classificati come facoltativi non sono inclusi nell'ambito di distribuzione per i computer Windows. Nell'ambito della distribuzione vengono inclusi solo gli aggiornamenti obbligatori.

La distribuzione pianificata definisce quali computer di destinazione ricevono gli aggiornamenti applicabili. Questa operazione viene eseguita specificando esplicitamente determinati computer o selezionando un [gruppo di computer](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups) basato sulle ricerche nei log di un set specifico di computer (o su una [query di Azure](automation-update-management-query-logs.md) che seleziona dinamicamente le VM di Azure in base ai criteri specificati). Questi gruppi si differenziano dalla [configurazione dell'ambito](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting), che consente di controllare la destinazione dei computer che ricevono la configurazione per abilitare Gestione aggiornamenti. Ciò impedisce l'esecuzione e la creazione di report di conformità degli aggiornamenti e l'installazione degli aggiornamenti necessari approvati.

Durante la definizione di una distribuzione, è inoltre necessario specificare una pianificazione per approvare e impostare un periodo di tempo durante il quale è possibile installare gli aggiornamenti. Questo periodo è denominato finestra di manutenzione. Un intervallo di 20 minuti della finestra di manutenzione è riservato per i riavvii, presupponendo che uno sia necessario ed è stata selezionata l'opzione di riavvio appropriato. Se l'applicazione di patch richiede più tempo del previsto e sono presenti meno di 20 minuti nella finestra di manutenzione, non si verificherà un riavvio.

Gli aggiornamenti vengono installati da runbook in Automazione di Azure. Non è possibile visualizzare questi manuali operativi e non richiedono alcuna configurazione. Quando viene creata una distribuzione degli aggiornamenti, viene creata una pianificazione che avvia un Runbook di aggiornamento Master all'ora specificata per i computer inclusi. Il runbook master avvia un runbook figlio in ogni agente per installare gli aggiornamenti necessari.

Alla data e all'ora specificate nella distribuzione degli aggiornamenti, i computer di destinazione eseguono la distribuzione in parallelo. Prima dell'installazione viene eseguita un'analisi per verificare che gli aggiornamenti siano ancora necessari. Per i computer client WSUS, se gli aggiornamenti non sono approvati in WSUS, la distribuzione degli aggiornamenti ha esito negativo.
Il computer registrato per Gestione aggiornamenti in più di un'area di lavoro Log Analytics (noto anche come multihoming) non è supportato.

## <a name="clients"></a>Client

### <a name="supported-client-types"></a>Tipi di client supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per le valutazioni degli aggiornamenti. L'applicazione di patch richiede un ruolo di lavoro ibrido per Runbook. Per informazioni sui requisiti del ruolo di lavoro ibrido per Runbook, vedere distribuire un ruolo di lavoro [ibrido per Runbook Windows](automation-windows-hrw-install.md) e distribuire un ruolo di lavoro ibrido per [Runbook Linux](automation-linux-hrw-install.md)

> [!NOTE]
> La valutazione degli aggiornamenti dei computer Linux è supportata solo in determinate aree, come elencato nella [tabella dei mapping](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)dell'account di automazione e dell'area di lavoro log Analytics. 

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2019 (Datacenter/datacenter core/standard)<br><br>Windows Server 2016 (Datacenter/datacenter core/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM e SP1 Standard)| Gestione aggiornamenti supporta solo le valutazioni per questo sistema operativo. L'applicazione di patch non è supportata perché il ruolo di [lavoro ibrido per Runbook](automation-windows-hrw-install.md) non è supportato per Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux richiedono l'accesso a un repository di aggiornamenti. L'applicazione di patch basata sulla `yum` classificazione richiede la restituzione dei dati di sicurezza che CentOS non ha nelle versioni RTM. Per altre informazioni sull'applicazione di patch basata sulla classificazione su CentOS, vedere [classificazioni degli aggiornamenti in Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux richiedono l'accesso a un repository di aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux richiedono l'accesso a un repository di aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux richiedono l'accesso a un repository di aggiornamenti.         |

> [!NOTE]
> I set di scalabilità di macchine virtuali di Azure possono essere gestiti tramite Gestione aggiornamenti. Gestione aggiornamenti funziona sulle istanze stesse e non sull'immagine di base. È necessario pianificare gli aggiornamenti in modo incrementale, in modo che non tutte le istanze di VM vengano aggiornate contemporaneamente.
> È possibile aggiungere nodi per i set di scalabilità di macchine virtuali attenendosi alla procedura descritta in [onboarding a computer non Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Nella tabella seguente sono elencati i sistemi operativi non supportati:

|Sistema operativo  |Note  |
|---------|---------|
|Client Windows     | I sistemi operativi client, ad esempio Windows 7 e Windows 10, non sono supportati.        |
|Windows Server 2016 Nano Server     | Non supportata.       |
|Nodi del servizio Azure Kubernetes | Non supportata. Usare il processo di applicazione di patch descritto in [applicare aggiornamenti di sicurezza e kernel ai nodi Linux in Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisiti per i client

Le informazioni seguenti descrivono i requisiti del client specifici del sistema operativo. Per ulteriori indicazioni, vedere [pianificazione della rete](#ports).

#### <a name="windows"></a>Windows

Gli agenti Windows devono essere configurati per comunicare con un server WSUS o richiedere l'accesso a Microsoft Update. Per informazioni su come installare l'agente di Log Analytics per Windows, vedere [connettere computer Windows a monitoraggio di Azure](../log-analytics/log-analytics-windows-agent.md).

È possibile usare Gestione aggiornamenti con Configuration Manager endpoint Microsoft. Per altre informazioni sugli scenari di integrazione, vedere [integrare Configuration Manager con gestione aggiornamenti](updatemgmt-mecmintegration.md#configuration). L' [agente di log Analytics per Windows](../azure-monitor/platform/agent-windows.md) è necessario per i server Windows gestiti da siti nell'ambiente Configuration Manager. 

Per impostazione predefinita, le macchine virtuali Windows distribuite da Azure Marketplace sono impostate per ricevere aggiornamenti automatici dal servizio Windows Update. Questo comportamento non cambia quando si aggiungono macchine virtuali Windows all'area di lavoro. Se non si gestiscono attivamente gli aggiornamenti tramite Gestione aggiornamenti, viene applicato il comportamento predefinito (per applicare automaticamente gli aggiornamenti).

> [!NOTE]
> È possibile modificare Criteri di gruppo in modo che il riavvio del computer possa essere eseguito solo dall'utente e non dal sistema. I computer gestiti possono restare bloccati se Gestione aggiornamenti non dispone dei diritti necessari per riavviare il computer senza interazione manuale da parte dell'utente. Per ulteriori informazioni, vedere [configurare le impostazioni di criteri di gruppo per aggiornamenti automatici](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Per Linux, il computer richiede l'accesso a un repository di aggiornamento, privato o pubblico. Per interagire con Gestione aggiornamenti è necessario TLS 1.1 o TLS 1.2. Gestione aggiornamenti non supporta un agente Log Analytics per Linux configurato per la segnalazione a più di un'area di lavoro Log Analytics. Nel computer deve essere installato anche Python 2. x.

> [!NOTE]
> La valutazione degli aggiornamenti dei computer Linux è supportata solo in determinate aree. Vedere la [tabella mapping](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)dell'account di automazione e dell'area di lavoro log Analytics. 

Per informazioni su come installare l'agente di Log Analytics per Linux e scaricare la versione più recente, vedere [log Analytics Agent per Linux](../azure-monitor/platform/agent-linux.md). 

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux su richiesta (RHEL) disponibili in Azure Marketplace vengono registrate per accedere a [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) distribuito in Azure. Tutte le altre distribuzioni di Linux devono essere aggiornate dal repository di file online della distribuzione usando i metodi supportati dalla distribuzione.

## <a name="permissions"></a>Autorizzazioni

Per creare e gestire le distribuzioni degli aggiornamenti, sono necessarie autorizzazioni specifiche. Per informazioni su queste autorizzazioni, vedere [accesso basato sui ruoli-Gestione aggiornamenti](automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Componenti Gestione aggiornamenti

Gestione aggiornamenti usa le risorse descritte in questa sezione. Queste risorse vengono aggiunte automaticamente all'account di automazione quando si Abilita Gestione aggiornamenti.

### <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi

Dopo aver abilitato Gestione aggiornamenti, qualsiasi computer Windows direttamente connesso all'area di lavoro di Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per Runbook per supportare i manuali operativi che supportano Gestione aggiornamenti.

Ogni computer Windows gestito da Gestione aggiornamenti viene elencato nel riquadro gruppi di lavoro ibridi come gruppo di lavoro ibrido di sistema per l'account di automazione. I gruppi utilizzano la `Hostname FQDN_GUID` convenzione di denominazione. Non è possibile applicare runbook a questi gruppi nell'account. Se si prova, il tentativo ha esito negativo. Questi gruppi sono destinati a supportare solo Gestione aggiornamenti.

È possibile aggiungere il computer Windows a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione per supportare manuali operativi di automazione se si usa lo stesso account per Gestione aggiornamenti e l'appartenenza al gruppo di ruolo di lavoro ibrido per Runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="management-packs"></a>Management Pack

Se il gruppo di gestione Operations Manager è [connesso a un'area di lavoro log Analytics](../azure-monitor/platform/om-agents.md), i Management Pack seguenti verranno installati in Operations Manager. Questi Management Pack vengono installati anche per Gestione aggiornamenti nei computer Windows direttamente connessi. Non è necessario configurare o gestire questi Management Pack.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Se si dispone di un gruppo di gestione Operations Manager 1807 o 2019 connesso a un'area di lavoro Log Analytics con gli agenti configurati nel gruppo di gestione per raccogliere i dati `IsAutoRegistrationEnabled` di log, è necessario eseguire l'override del parametro e impostarlo su true nella regola **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Per ulteriori informazioni sugli aggiornamenti dei Management Pack, vedere [connettere Operations Manager ai log di monitoraggio di Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Per Gestione aggiornamenti la gestione completa dei computer con l'agente di Log Analytics, è necessario eseguire l'aggiornamento all'agente di Log Analytics per Windows o all'agente di Log Analytics per Linux. Per informazioni su come aggiornare l'agente, vedere [How to upgrade an Operations Manager agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents) (Come aggiornare un agente di Operations Manager). Negli ambienti che usano Operations Manager è necessario eseguire System Center Operations Manager 2012 R2 UR 14 o versione successiva.

## <a name="data-collection"></a>Raccolta dati

### <a name="supported-sources"></a>Origini supportate

La tabella seguente descrive le origini connesse supportate da Gestione aggiornamenti:

| Origine connessa | Supportato | Description |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso.<br/><br/>Non è necessaria una connessione diretta dall'agente Operations Manager ai log di monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Gestione aggiornamenti analizza i computer gestiti per i dati utilizzando le regole seguenti. Il dashboard può richiedere da 30 minuti a 6 ore per visualizzare i dati aggiornati dai computer gestiti.

* Ogni computer Windows Gestione aggiornamenti esegue un'analisi due volte al giorno per ogni computer. Ogni 15 minuti esegue una query sull'API Windows per l'ora dell'ultimo aggiornamento per determinare se lo stato è stato modificato. Se lo stato è cambiato, Gestione aggiornamenti avvia un'analisi di conformità.

* Ogni Gestione aggiornamenti del computer Linux esegue una scansione ogni ora.

L'utilizzo medio dei dati da parte dei log di monitoraggio di Azure per un computer che usa Gestione aggiornamenti è di circa 25 MB al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, a seconda dell'ambiente. Si consiglia di monitorare l'ambiente per tenere traccia dell'utilizzo esatto. Per ulteriori informazioni sull'analisi dell'utilizzo dei dati, vedere [gestire l'utilizzo e i costi](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Pianificazione della rete

I seguenti indirizzi sono necessari e specifici per Gestione aggiornamenti. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Per i computer Windows, è necessario consentire il traffico anche a tutti gli endpoint necessari per Windows Update. È possibile trovare un elenco aggiornato degli endpoint necessari nei [problemi relativi a http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se si dispone di un [server di Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)locale, è necessario consentire anche il traffico al server specificato nella [chiave WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Per i computer Red Hat Linux, vedere [indirizzi IP per i server per la distribuzione di contenuti di RHUI per gli](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) endpoint richiesti. Per altre distribuzioni Linux, vedere la documentazione del provider.

Per altre informazioni sulle porte necessarie per il ruolo di lavoro ibrido per Runbook, vedere [indirizzi gestione aggiornamenti per il ruolo di lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Si consiglia di utilizzare gli indirizzi elencati durante la definizione delle eccezioni. Per gli indirizzi IP è possibile scaricare [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato settimanalmente e riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Seguire le istruzioni riportate in [connettere i computer senza accesso a Internet](../azure-monitor/platform/gateway.md) per configurare i computer che non hanno accesso a Internet.

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

La tabella seguente definisce le classificazioni che Gestione aggiornamenti supporta per gli aggiornamenti di Windows. 

|Classificazione  |Description  |
|---------|---------|
|Aggiornamenti critici     | Un aggiornamento per un problema specifico che risolve un bug critico non correlato alla sicurezza.        |
|Aggiornamenti per la sicurezza     | Un aggiornamento per un problema specifico del prodotto correlato alla sicurezza.        |
|Aggiornamenti cumulativi     | Un set cumulativo di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Un set cumulativo di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Un aggiornamento per un virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consente di completare una o più attività.        |
|Aggiornamenti     | Un aggiornamento di un'applicazione o un file attualmente installati.        |

La tabella successiva definisce le classificazioni supportate per gli aggiornamenti di Linux.

|Classificazione  |Description  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti che non sono critici per natura o che non sono aggiornamenti della sicurezza.        |

Per Linux, Gestione aggiornamenti possibile distinguere tra gli aggiornamenti critici e gli aggiornamenti della sicurezza nel cloud, visualizzando i dati di valutazione a causa dell'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non ha queste informazioni disponibili nella versione RTM. Se i computer CentOS sono configurati per restituire i dati di sicurezza per il comando seguente, Gestione aggiornamenti possibile applicare una patch in base alle classificazioni.

```bash
sudo yum -q --security check-update
```

Attualmente non è disponibile alcun metodo supportato per abilitare la classificazione nativa: disponibilità dei dati in CentOS. A questo punto, è disponibile solo il supporto per i clienti che potrebbero avere abilitato questa funzionalità autonomamente. 

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-Security. In Red Hat Enterprise Linux 7 il plug-in fa già parte di yum e non è necessario installare nulla. Per ulteriori informazioni, vedere l' [articolo della Knowledge](https://access.redhat.com/solutions/10021)base di Red Hat.

## <a name="integrate-update-management-with-configuration-manager"></a>Integrare Gestione aggiornamenti con Configuration Manager

I clienti che hanno investito in Microsoft endpoint Configuration Manager per la gestione di PC, server e dispositivi mobili si affidano anche al livello di attendibilità e maturità delle Configuration Manager per la gestione degli aggiornamenti software. Per informazioni su come integrare Gestione aggiornamenti con Configuration Manager, vedere [integrare Configuration Manager con gestione aggiornamenti](updatemgmt-mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Aggiornamenti di terze parti in Windows

Gestione aggiornamenti si basa sul repository degli aggiornamenti configurato localmente per aggiornare i sistemi Windows supportati, ovvero WSUS o Windows Update. Strumenti come [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) consentono di importare e pubblicare aggiornamenti personalizzati con WSUS. Questo scenario consente Gestione aggiornamenti di aggiornare i computer che usano Configuration Manager come repository di aggiornamento con software di terze parti. Per informazioni su come configurare Updates Publisher, vedere [Installare Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

È disponibile un [modello di gestione risorse](automation-update-management-deploy-template.md) di Azure che consente di distribuire Gestione aggiornamenti a un account di automazione nuovo o esistente e al monitoraggio di Azure log Analytics area di lavoro nella sottoscrizione. Non configura l'ambito dei computer che devono essere gestiti, questo viene eseguito come passaggio separato dopo l'uso del modello.

Ecco i modi in cui è possibile abilitare Gestione aggiornamenti e selezionare i computer da gestire:

* [Da una macchina virtuale](automation-onboard-solutions-from-vm.md).
* [Dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md).
* [Da un account di automazione di Azure](automation-onboard-solutions.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande [frequenti](automation-faq.md) su automazione di Azure per esaminare le domande frequenti su Gestione aggiornamenti.
