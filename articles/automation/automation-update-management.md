---
title: Soluzione Gestione aggiornamenti in Azure
description: Questo articolo descrive come usare la soluzione Gestione aggiornamenti di Azure per gestire gli aggiornamenti per i computer Windows e Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 66acb1284f0814eec91715284259272a065dbae2
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690901"
---
# <a name="update-management-solution-in-azure"></a>Soluzione Gestione aggiornamenti in Azure

È possibile usare la soluzione Gestione aggiornamenti in automazione di Azure per gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux in Azure, negli ambienti locali e in altri provider di servizi cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

È possibile abilitare Gestione aggiornamenti per le macchine virtuali (VM) direttamente dall'account di automazione di Azure. Per informazioni, vedere [gestire gli aggiornamenti per più macchine virtuali](manage-update-multi.md). È anche possibile abilitare Gestione aggiornamenti per una VM dalla pagina della macchina virtuale nel portale di Azure. Questo scenario è disponibile per le macchine virtuali [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

> [!NOTE]
> La soluzione Gestione aggiornamenti richiede il collegamento di un'area di lavoro Log Analytics all'account di automazione. Per un elenco definitivo delle aree supportate, vedere [mapping dell'area di lavoro di Azure](./how-to/region-mappings.md). I mapping dell'area non influiscono sulla possibilità di gestire le macchine virtuali in un'area separata dall'account di automazione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Panoramica della soluzione

I computer gestiti da Gestione aggiornamenti utilizzano le configurazioni seguenti per eseguire la valutazione e aggiornare le distribuzioni:

* Microsoft Monitoring Agent (MMA) per Windows o Linux
* PowerShell DSC (Desired State Configuration) per Linux
* Ruolo di lavoro ibrido per runbook di Automazione
* Microsoft Update o Windows Server Update Services (WSUS) per computer Windows

Il diagramma seguente illustra il modo in cui la soluzione valuta e applica gli aggiornamenti della sicurezza a tutti i computer Windows Server e Linux connessi in un'area di lavoro:

![Flusso del processo di Gestione aggiornamenti](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gestione aggiornamenti può essere usato per l'onboarding nativo di computer in più sottoscrizioni nello stesso tenant.

Dopo il rilascio di un pacchetto, sono necessarie da 2 a 3 ore per la visualizzazione della patch per i computer Linux per la valutazione. Per i computer Windows, sono necessarie da 12 a 15 ore per la visualizzazione della patch per la valutazione dopo che è stata rilasciata.

Quando un computer completa un'analisi per la conformità degli aggiornamenti, l'agente trasmette le informazioni in blocco ai log di monitoraggio di Azure. In un computer Windows l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita.

Oltre all'analisi pianificata, l'analisi della conformità degli aggiornamenti viene avviata entro 15 minuti dal momento del riavvio di MMA e prima e dopo l'installazione degli aggiornamenti.

Per un computer Linux, l'analisi di conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente MMA viene riavviato, viene avviata un'analisi della conformità entro 15 minuti.

La soluzione genera report sullo stato di aggiornamento del computer in base all'origine configurata per la sincronizzazione. Se il computer Windows è configurato per la segnalazione a WSUS, a seconda del momento in cui WSUS è stato sincronizzato con Microsoft Update, i risultati potrebbero essere diversi da quelli Microsoft Update visualizzati. Questo comportamento è lo stesso per i computer Linux configurati per l'invio di report a un repository locale anziché a un repository pubblico.

> [!NOTE]
> Per inviare correttamente un report al servizio, Gestione aggiornamenti richiede l'abilitazione di determinati URL e porte. Per altre informazioni su questi requisiti, vedere l'articolo sulla [pianificazione della rete per i ruoli di lavoro ibridi](automation-hybrid-runbook-worker.md#network-planning).

È possibile distribuire e installare gli aggiornamenti software nei computer che richiedono gli aggiornamenti creando una distribuzione pianificata. Gli aggiornamenti classificati come *facoltativi* non sono inclusi nell'ambito della distribuzione per i computer Windows. Nell'ambito della distribuzione vengono inclusi solo gli aggiornamenti obbligatori.

La distribuzione pianificata definisce quali computer di destinazione ricevono gli aggiornamenti applicabili. Questa operazione viene eseguita specificando esplicitamente determinati computer o selezionando un [gruppo di computer](../azure-monitor/platform/computer-groups.md) basato sulle ricerche nei log di un set specifico di computer (o su una [query di Azure](automation-update-management-query-logs.md) che seleziona dinamicamente le VM di Azure in base ai criteri specificati). Questi gruppi si differenziano dalla [configurazione dell'ambito](../azure-monitor/insights/solution-targeting.md), che viene utilizzata solo per determinare quali computer ottengono i Management Pack che abilitano la soluzione.

È inoltre possibile specificare una pianificazione per approvare e impostare un periodo di tempo durante il quale è possibile installare gli aggiornamenti. Questo periodo è denominato finestra di manutenzione. Un intervallo di 20 minuti della finestra di manutenzione è riservato per i riavvii, presupponendo che uno sia necessario ed è stata selezionata l'opzione di riavvio appropriato. Se l'applicazione di patch richiede più tempo del previsto e sono presenti meno di 20 minuti nella finestra di manutenzione, non si verificherà un riavvio.

Gli aggiornamenti vengono installati da runbook in Automazione di Azure. Non è possibile visualizzare questi manuali operativi e non richiedono alcuna configurazione. Quando viene creata una distribuzione degli aggiornamenti, viene creata una pianificazione che avvia un Runbook di aggiornamento Master all'ora specificata per i computer inclusi. Il runbook master avvia un runbook figlio in ogni agente per installare gli aggiornamenti necessari.

Alla data e ora specificate nella distribuzione degli aggiornamenti, i computer di destinazione eseguono la distribuzione in parallelo. Prima dell'installazione viene eseguita un'analisi per verificare che gli aggiornamenti siano ancora necessari. Per i computer client WSUS, se gli aggiornamenti non sono approvati in WSUS, la distribuzione degli aggiornamenti ha esito negativo.

Il computer registrato per Gestione aggiornamenti in più di un'area di lavoro di Log Analytics (multihoming) non è supportato.

## <a name="clients"></a>Client

### <a name="supported-client-types"></a>Tipi di client supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per le valutazioni degli aggiornamenti. L'applicazione di patch richiede un ruolo di lavoro ibrido per Runbook. Per informazioni sui requisiti del ruolo di lavoro ibrido per Runbook, vedere le guide all'installazione per l'installazione di un ruolo [di lavoro](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)ibrido per [Runbook](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2019 (Datacenter/datacenter core/standard)<br><br>Windows Server 2016 (Datacenter/datacenter core/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM e SP1 Standard)||
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede `yum` per restituire i dati di sicurezza che CentOS non ha nelle versioni RTM. Per altre informazioni sull'applicazione di patch basata sulla classificazione su CentOS, vedere [classificazioni degli aggiornamenti in Linux](#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

> [!NOTE]
> I set di scalabilità di macchine virtuali di Azure possono essere gestiti tramite Gestione aggiornamenti. Gestione aggiornamenti funziona sulle istanze stesse e non sull'immagine di base. È necessario pianificare gli aggiornamenti in modo incrementale, in modo che non tutte le istanze di VM vengano aggiornate contemporaneamente.
> È possibile aggiungere nodi per i set di scalabilità di macchine virtuali attenendosi alla procedura descritta in [onboarding a computer non Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipi di client non supportati

Nella tabella seguente sono elencati i sistemi operativi non supportati:

|Sistema operativo  |Note  |
|---------|---------|
|Client Windows     | I sistemi operativi client, ad esempio Windows 7 e Windows 10, non sono supportati.        |
|Windows Server 2016 Nano Server     | Non supportati.       |
|Nodi del servizio Azure Kubernetes | Non supportati. Usare il processo di applicazione di patch descritto in [applicare aggiornamenti di sicurezza e kernel ai nodi Linux in Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisiti per i client

Le informazioni seguenti descrivono i requisiti del client specifici del sistema operativo. Per ulteriori indicazioni, vedere [pianificazione della rete](#ports).

#### <a name="windows"></a>Windows

Gli agenti Windows devono essere configurati per comunicare con un server WSUS oppure devono avere accesso a Microsoft Update.

Gestione aggiornamenti può essere usato con System Center Configuration Manager. Per altre informazioni sugli scenari di integrazione, vedere [Integrare System Center Configuration Manager con Gestione aggiornamenti](oms-solution-updatemgmt-sccmintegration.md#configuration). L'[agente Windows](../azure-monitor/platform/agent-windows.md) è obbligatorio. L'agente viene installato automaticamente se si sta caricando una macchina virtuale di Azure.

Per impostazione predefinita, le macchine virtuali Windows distribuite da Azure Marketplace sono impostate per ricevere aggiornamenti automatici dal servizio Windows Update. Questo comportamento non cambia quando si aggiunge questa soluzione o si aggiungono macchine virtuali Windows all'area di lavoro. Se gli aggiornamenti non vengono gestiti attivamente con questa soluzione, è applicabile il comportamento predefinito, ovvero gli aggiornamenti vengono applicati automaticamente.

> [!NOTE]
> Un utente può modificare Criteri di gruppo in modo che il riavvio del computer possa essere eseguito solo dall'utente e non dal sistema. I computer gestiti possono restare bloccati se Gestione aggiornamenti non dispone dei diritti necessari per riavviare il computer senza interazione manuale da parte dell'utente.
>
> Per ulteriori informazioni, vedere [configurare le impostazioni di criteri di gruppo per aggiornamenti automatici](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Per Linux, il computer deve avere accesso a un repository degli aggiornamenti. Il repository degli aggiornamenti può essere privato o pubblico. Per interagire con Gestione aggiornamenti è necessario TLS 1.1 o TLS 1.2. Un agente Log Analytics per Linux configurato per la segnalazione a più di un'area di lavoro Log Analytics non è supportato con questa soluzione. Nel computer deve essere installato anche Python 2. x.

Per informazioni su come installare l'agente di Log Analytics per Linux e scaricare la versione più recente, vedere [log Analytics Agent per Linux](https://github.com/microsoft/oms-agent-for-linux). Per informazioni su come installare l'agente di Log Analytics per Windows, vedere [connettere computer Windows a monitoraggio di Azure](../log-analytics/log-analytics-windows-agent.md).

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux su richiesta (RHEL) disponibili in Azure Marketplace vengono registrate per accedere a [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) distribuito in Azure. Tutte le altre distribuzioni di Linux devono essere aggiornate dal repository di file online della distribuzione usando i metodi supportati della distribuzione.

## <a name="permissions"></a>autorizzazioni

Per creare e gestire le distribuzioni degli aggiornamenti, sono necessarie autorizzazioni specifiche. Per informazioni su queste autorizzazioni, vedere [accesso basato sui ruoli-Gestione aggiornamenti](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componenti della soluzione

La soluzione è costituita dalle risorse seguenti. Le risorse vengono aggiunte all'account di Automazione. Sono agenti con connessione diretta o fanno parte di un gruppo di gestione connesso a Operations Manager.

### <a name="hybrid-worker-groups"></a>Gruppi di ruoli di lavoro ibridi

Dopo aver abilitato questa soluzione, qualsiasi computer Windows direttamente connesso all'area di lavoro Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione.

Ogni computer Windows gestito dalla soluzione è elencato nella pagina dei **gruppi di ruoli di lavoro ibridi** come **gruppo di ruoli di lavoro ibridi per il sistema** per l'account di Automazione. Le soluzioni usano la convenzione di denominazione *FQDN_GUID nome host* . Non è possibile applicare runbook a questi gruppi nell'account. Se si prova, il tentativo ha esito negativo. Questi gruppi sono destinati solo al supporto della soluzione di gestione.

È possibile aggiungere i computer Windows a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, se si usa lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="management-packs"></a>Management Pack

Se il gruppo di gestione di System Center Operations Manager è connesso all'area di lavoro Log Analytics, in Operations Manager verranno installati i Management Pack seguenti. Questi Management Pack vengono installati anche su computer Windows direttamente connessi dopo l'aggiunta della soluzione. Non è necessario configurare o gestire questi Management Pack.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Si supponga di disporre di un gruppo di gestione Operations Manager 1807 o 2019 con gli agenti configurati a livello di gruppo di gestione per associarli a un'area di lavoro. Per visualizzare la soluzione corrente, è necessario eseguire l'override di **IsAutoRegistrationEnabled** su **true** nella regola **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Per altre informazioni sulla modalità di aggiornamento dei Management Pack per le soluzioni, vedere [connettere Operations Manager ai log di monitoraggio di Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Per i sistemi con l'agente Operations Manager: affinché un agente sia completamente gestito da Gestione aggiornamenti, l'agente deve essere aggiornato a MMA. Per informazioni su come aggiornare l'agente, vedere [How to upgrade an Operations Manager agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents) (Come aggiornare un agente di Operations Manager). Negli ambienti che usano Operations Manager è necessario eseguire System Center Operations Manager 2012 R2 UR 14 o versione successiva.

## <a name="data-collection"></a>Raccolta dei dati

### <a name="supported-agents"></a>Agenti supportati

La tabella seguente descrive le origini connesse supportate da questa soluzione:

| Origine connessa | Supportato | Description |
| --- | --- | --- |
| Agenti Windows |SÌ |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |SÌ |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |SÌ |La soluzione raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso.<br/><br/>Non è necessaria una connessione diretta dall'agente Operations Manager ai log di monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Per ogni computer Windows gestito viene eseguita un'analisi due volte al giorno. Ogni 15 minuti viene chiamata l'API Windows per eseguire una query su data/ora dell'ultimo aggiornamento e determinare se lo stato è cambiato. Se lo stato è cambiato, viene avviata un'analisi della conformità.

Un'analisi viene eseguita ogni ora per ogni computer Linux gestito.

La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

L'utilizzo medio dei dati da parte dei log di monitoraggio di Azure per un computer che usa Gestione aggiornamenti è di circa 25 megabyte (MB) al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, a seconda dell'ambiente. Si consiglia di monitorare l'ambiente per tenere traccia dell'utilizzo esatto.

## <a name="ports"></a>Pianificazione della rete

I seguenti indirizzi sono necessari e specifici per Gestione aggiornamenti. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure per enti pubblici  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Per i computer Windows, è necessario consentire il traffico anche a tutti gli endpoint necessari per Windows Update. È possibile trovare un elenco aggiornato degli endpoint necessari nei [problemi relativi a http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se si dispone di un [server di Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)locale, è necessario consentire anche il traffico al server specificato nella [chiave WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Per i computer Red Hat Linux, vedere [indirizzi IP per i server per la distribuzione di contenuti di RHUI per gli](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) endpoint richiesti. Per altre distribuzioni Linux, vedere la documentazione del provider.

Per altre informazioni sulle porte richieste dal ruolo di lavoro ibrido per runbook, vedere [Porte del ruolo di lavoro ibrido](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Si consiglia di utilizzare gli indirizzi elencati durante la definizione delle eccezioni. Per gli indirizzi IP è possibile scaricare [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato settimanalmente e riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Seguire le istruzioni riportate in [connettere i computer senza accesso a Internet](../azure-monitor/platform/gateway.md) per configurare i computer che non hanno accesso a Internet.

## <a name="view-update-assessments"></a>Visualizza valutazioni aggiornamenti

Selezionare **Gestione aggiornamenti** nell'account di Automazione per visualizzare lo stato dei computer.

Questa visualizzazione contiene informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate. Nella colonna **conformità** è possibile visualizzare l'ultima volta in cui il computer è stato valutato. Nella colonna **aggiornamento disponibilità agenti** è possibile verificare l'integrità dell'agente di aggiornamento. Se si verifica un problema, selezionare il collegamento per passare alla documentazione per la risoluzione dei problemi che consente di risolvere il problema.

Per eseguire una ricerca log che restituisce informazioni sul computer, l'aggiornamento o la distribuzione, selezionare l'elemento corrispondente nell'elenco. Si apre il riquadro **Ricerca log** con una query per l'elemento selezionato:

![Visualizzazione predefinita di Gestione aggiornamenti](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Sono disponibili informazioni relative al numero di computer che richiedono l'aggiornamento e al sistema operativo, oltre a un collegamento per accedere ad altre informazioni. Il riquadro **Ricerca log** visualizza altri dettagli sugli aggiornamenti.

![Aggiornamenti mancanti](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

Nelle tabelle che seguono sono riportate le classificazioni degli aggiornamenti in Gestione aggiornamenti, con una definizione per ogni classificazione.

### <a name="windows"></a>Windows

|Classificazione  |Description  |
|---------|---------|
|Aggiornamenti critici     | Un aggiornamento per un problema specifico che risolve un bug critico non correlato alla sicurezza.        |
|Aggiornamenti della sicurezza     | Un aggiornamento per un problema specifico del prodotto correlato alla sicurezza.        |
|Aggiornamenti cumulativi     | Un set cumulativo di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Un set cumulativo di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Un aggiornamento per un virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consente di completare una o più attività.        |
|Aggiornamenti     | Un aggiornamento di un'applicazione o un file attualmente installati.        |

### <a name="linux-2"></a>Linux

|Classificazione  |Description  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti che non sono critici per natura o che non sono aggiornamenti della sicurezza.        |

Per Linux, Gestione aggiornamenti possibile distinguere tra gli aggiornamenti critici e gli aggiornamenti della sicurezza nel cloud, visualizzando i dati di valutazione a causa dell'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non ha queste informazioni disponibili nella versione RTM. Se i computer CentOS sono configurati per restituire i dati di sicurezza per il comando seguente, Gestione aggiornamenti possibile applicare una patch in base alle classificazioni.

```bash
sudo yum -q --security check-update
```

Attualmente non è disponibile alcun metodo supportato per abilitare la classificazione nativa: disponibilità dei dati in CentOS. A questo punto, viene fornito solo il supporto per il massimo sforzo ai clienti che potrebbero avere abilitato questa funzionalità autonomamente.

## <a name="integrate-with-system-center-configuration-manager"></a>Integrazione con System Center Configuration Manager

I clienti che hanno investito in System Center Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software. Configuration Manager fa parte del ciclo di gestione degli aggiornamenti software (SUM).

Per informazioni su come integrare la soluzione di gestione con System Center Configuration Manager, vedere [Integrare System Center Configuration Manager con Gestione aggiornamenti](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Patch di terze parti in Windows

Gestione aggiornamenti si basa sul repository di aggiornamenti configurato localmente per applicare patch ai sistemi Windows supportati. Si tratta di WSUS o Windows Update. Strumenti come [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) consentono di pubblicare gli aggiornamenti personalizzati in Windows Server Update Services. Questo scenario consente Gestione aggiornamenti di applicare patch ai computer che usano System Center Configuration Manager come repository di aggiornamento con software di terze parti. Per informazioni su come configurare Updates Publisher, vedere [Installare Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Applicazione di patch ai computer Linux

Le sezioni seguenti illustrano i potenziali problemi relativi all'applicazione di patch a computer Linux.

### <a name="unexpected-os-level-upgrades"></a>Aggiornamenti imprevisti a livello di sistema operativo

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, gli aggiornamenti a livello di sistema operativo possono verificarsi tramite pacchetti. Questo può causare esecuzioni di Gestione aggiornamenti in cui viene modificato il numero di versione del sistema operativo. Poiché Gestione aggiornamenti usa gli stessi metodi di aggiornamento dei pacchetti che userebbe un amministratore in locale nel computer Linux, questo comportamento è intenzionale.

Per evitare di aggiornare la versione del sistema operativo tramite Gestione aggiornamenti esecuzioni, usare la funzionalità di **esclusione** .

In Red Hat Enterprise Linux il nome del pacchetto da escludere è redhat-release-server.x86_64.

![Pacchetti da escludere per Linux](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Non vengono applicate le patch critiche/di sicurezza

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione consente di filtrare gli aggiornamenti applicati al computer che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, alcuni aggiornamenti possono essere contrassegnati Gestione aggiornamenti con un effetto di sicurezza, anche se il computer locale non dispone di tali informazioni. Di conseguenza, se si applicano aggiornamenti critici a un computer Linux, potrebbero essere presenti aggiornamenti che non sono contrassegnati come aventi un effetto di sicurezza sul computer e pertanto gli aggiornamenti non vengono applicati. Tuttavia, Gestione aggiornamenti possibile che il computer venga segnalato come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti tramite classificazione aggiornamenti non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, la selezione di *solo* **altri aggiornamenti** come la classificazione può causare l'installazione di alcuni aggiornamenti della sicurezza anche se sono necessari prima gli aggiornamenti della sicurezza relativi a zypper (gestione pacchetti) o alle relative dipendenze. Si tratta di una limitazione di zypper. In alcuni casi, potrebbe essere necessario eseguire di nuovo la distribuzione degli aggiornamenti. Per verificare, controllare il log di aggiornamento.

### <a name="multi-tenant"></a>Distribuzioni di aggiornamenti tra tenant

Se si dispone di computer in un altro tenant di Azure per Gestione aggiornamenti che è necessario applicare patch, è necessario usare la soluzione alternativa seguente per prepararli. È possibile usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) con l'opzione `-ForUpdate` per creare una pianificazione e usare il cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passare le macchine nell'altro tenant al parametro `-NonAzureComputer`. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Abilitare Gestione aggiornamenti

Per avviare l'applicazione di patch ai sistemi, è necessario abilitare la soluzione Gestione aggiornamenti. Esistono molti modi per eseguire l'onboarding dei computer in Gestione aggiornamenti. Per eseguire l'onboarding della soluzione si consigliano i metodi supportati seguenti:

* [Da una macchina virtuale](automation-onboard-solutions-from-vm.md)
* [Dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md)
* [Dall'account di Automazione](automation-onboard-solutions-from-automation-account.md)
* [Con un runbook di Automazione di Azure](automation-onboard-solutions.md)

## <a name="next-steps"></a>Passaggi successivi

Usare l'esercitazione seguente per informazioni su come gestire gli aggiornamenti per le macchine virtuali Windows:

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-update-management.md)

* Usare le ricerche log nei [log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare i dati dettagliati sugli aggiornamenti.
* [Creare avvisi](automation-tutorial-update-management.md#configure-alerts) per lo stato di distribuzione degli aggiornamenti.

* Per informazioni su come interagire con Gestione aggiornamenti tramite l'API REST, vedere [configurazioni degli aggiornamenti software](/rest/api/automation/softwareupdateconfigurations).
* Per informazioni su come risolvere i problemi di Gestione aggiornamenti, vedere [risoluzione dei problemi gestione aggiornamenti](troubleshoot/update-management.md).
