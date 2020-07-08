---
title: Automazione di Azure - Panoramica di Gestione aggiornamenti
description: Questo articolo fornisce una panoramica della funzionalità Gestione aggiornamenti che implementa gli aggiornamenti per computer Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 06/23/2020
ms.topic: conceptual
ms.openlocfilehash: 86116e4aa76b376331e25719d128fc733c3257ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85316399"
---
# <a name="update-management-overview"></a>Panoramica di Gestione aggiornamenti

È possibile usare Gestione aggiornamenti in Automazione di Azure per gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux in Azure, in ambienti locali e in altri ambienti cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

È possibile abilitare Gestione aggiornamenti per le VM nei modi seguenti:

* Dall'[account di Automazione di Azure](automation-onboard-solutions-from-automation-account.md) per uno o più computer di Azure.
* Manualmente per computer non Azure.
* Per una singola VM di Azure dalla pagina Macchina virtuale nel portale di Azure. Questo scenario è disponibile per macchine virtuali [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management).
* Per [più VM di Azure](manage-update-multi.md) mediante la selezione delle VM dalla pagina Macchina virtuale nel portale di Azure.

> [!NOTE]
> Gestione aggiornamenti richiede il collegamento di un'area di lavoro Log Analytics all'account di Automazione. Per un elenco completo delle aree supportate, vedere [Mapping dell'area di lavoro di Azure](how-to/region-mappings.md). I mapping a livello di area non influiscono sulla possibilità di gestire le VM in un'area separata rispetto all'account di Automazione.

È disponibile un [modello di Azure Resource Manager](automation-update-management-deploy-template.md) per semplificare la distribuzione di Gestione aggiornamenti in un account di Automazione nuovo o esistente e in un'area di lavoro Log Analytics nella sottoscrizione.

> [!NOTE]
> Non è possibile usare un computer configurato con Gestione aggiornamenti per l'esecuzione di script personalizzati da Automazione di Azure. Questo computer può eseguire solo lo script di aggiornamento firmato da Microsoft.

## <a name="about-update-management"></a>Informazioni su Gestione aggiornamenti

I computer gestiti da Gestione aggiornamenti usano le configurazioni seguenti per le valutazioni e le distribuzioni degli aggiornamenti:

* Agente di Log Analytics per Windows o Linux
* PowerShell DSC (Desired State Configuration) per Linux
* Ruolo di lavoro ibrido per runbook di Automazione
* Microsoft Update o Windows Server Update Services (WSUS) per computer Windows

Il diagramma seguente illustra il modo in cui Gestione aggiornamenti valuta e applica gli aggiornamenti della sicurezza a tutti i computer Windows Server e Linux connessi in un'area di lavoro:

![Flusso di lavoro di Gestione aggiornamenti](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gestione aggiornamenti può essere usato per la distribuzione nativa di computer in più sottoscrizioni nello stesso tenant.

Dopo il rilascio di un pacchetto, la visualizzazione della patch per i computer Linux per la valutazione richiede tra 2 e 3 ore. Per i computer Windows, la visualizzazione della patch per la valutazione dopo il rilascio richiede tra 12 e 15 ore.

Quando un computer ha completato l'analisi di conformità degli aggiornamenti, l'agente inoltra le informazioni in blocco ai log di Monitoraggio di Azure. In un computer Windows l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita.

Oltre all'analisi pianificata, l'analisi della conformità degli aggiornamenti viene avviata entro 15 minuti dal momento del riavvio dell'agente di Log Analytics e prima e dopo l'installazione degli aggiornamenti.

Per un computer Linux, l'analisi della conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente di Log Analytics viene riavviato, viene avviata un'analisi della conformità entro 15 minuti.

Gestione aggiornamenti genera report sullo stato di aggiornamento del computer in base all'origine configurata per la sincronizzazione. Se il computer Windows è configurato per l'invio di report a WSUS, in base a quando WSUS ha eseguito l'ultima sincronizzazione con Microsoft Update i risultati possono differire da quanto visualizzato da Microsoft Update. Questo comportamento è lo stesso per i computer Linux configurati per l'invio di report a un repository locale anziché a un repository pubblico.

> [!NOTE]
> Per inviare correttamente un report al servizio, Gestione aggiornamenti richiede l'abilitazione di determinati URL e porte. Per altre informazioni su questi requisiti, vedere [Configurazione della rete](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning).

È possibile distribuire e installare gli aggiornamenti software nei computer che richiedono gli aggiornamenti creando una distribuzione pianificata. Gli aggiornamenti classificati come facoltativi non sono inclusi nell'ambito della distribuzione per i computer Windows. Nell'ambito della distribuzione vengono inclusi solo gli aggiornamenti obbligatori.

La distribuzione pianificata definisce i computer di destinazione che ricevono gli aggiornamenti applicabili, specificando in modo esplicito determinati computer o selezionando un [gruppo di computer](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups) basato sulle ricerche log di un set specifico di computer oppure su una [query di Azure](automation-update-management-query-logs.md) che seleziona dinamicamente le VM di Azure in base ai criteri specificati. Questi gruppi sono diversi dalla [configurazione dell'ambito](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting), che viene usata per controllare la selezione dei computer di destinazione che ricevono la configurazione per abilitare Gestione aggiornamenti. Questo approccio impedisce ai computer di eseguire e segnalare la conformità degli aggiornamenti e di installare gli aggiornamenti obbligatori.

Durante la definizione di una distribuzione, si specifica anche una pianificazione per approvare e impostare un periodo di tempo durante il quale è possibile installare gli aggiornamenti. Questo periodo viene definito finestra di manutenzione. Un intervallo di 20 minuti della finestra di manutenzione viene riservato per i riavvii, presupponendo che sia necessario un riavvio e che sia stata selezionata l'opzione di riavvio appropriata. Se l'applicazione di patch richiede più tempo del previsto e sono disponibili meno di 20 minuti nella finestra di manutenzione, non verrà eseguito il riavvio.

Gli aggiornamenti vengono installati da runbook in Automazione di Azure. Questi runbook non richiedono alcuna configurazione e non possono essere visualizzati. Quando si crea una distribuzione degli aggiornamenti, viene creata una pianificazione che avvia un runbook di aggiornamento master alla data e ora specificata per i computer inclusi. Il runbook master avvia un runbook figlio in ogni agente per installare gli aggiornamenti necessari.

Alla data e ora specificate nella distribuzione degli aggiornamenti, i computer di destinazione eseguono la distribuzione in parallelo. Prima dell'installazione viene eseguita un'analisi per verificare che gli aggiornamenti siano ancora necessari. Per i computer client WSUS, la distribuzione degli aggiornamenti ha esito negativo se gli aggiornamenti non sono approvati in WSUS.

Non è consentito avere un computer registrato per Gestione aggiornamenti in più di un'area di lavoro Log Analytics (definito anche multihoming).

## <a name="clients"></a>Client

### <a name="supported-client-types"></a>Tipi di client supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per la valutazione degli aggiornamenti e l'applicazione di patch. L'applicazione di patch richiede un ruolo di lavoro ibrido per runbook. Per informazioni sui requisiti del ruolo di lavoro ibrido per runbook, vedere [Distribuire un ruolo di lavoro ibrido per runbook di Windows](automation-windows-hrw-install.md) e [Distribuire un ruolo di lavoro ibrido per runbook di Linux](automation-linux-hrw-install.md).

> [!NOTE]
> La valutazione degli aggiornamenti dei computer Linux è supportata solo in determinate aree, come elencato nella [tabella di mapping](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings) dell'account di Automazione e dell'area di lavoro Log Analytics. 

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2019 (Data center/Data center Core/Standard)<br><br>Windows Server 2016 (Data center/Data center Core/Standard)<br><br>Windows Server 2012 R2(Data center/Standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2 (RTM e SP1 Standard)| Gestione aggiornamenti supporta le valutazioni e l'applicazione di patch per questo sistema operativo. Il ruolo di [lavoro ibrido per Runbook](automation-windows-hrw-install.md) è supportato per Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede `yum` per restituire i dati sulla sicurezza che non sono disponibili nelle release RTM di CentOS. Per altre informazioni sull'applicazione di patch basata sulla classificazione in CentOS, vedere [Classificazioni degli aggiornamenti in Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

> [!NOTE]
> I set di scalabilità di macchine virtuali di Azure possono essere gestiti tramite Gestione aggiornamenti. Gestione aggiornamenti viene applicato alle istanze stesse, non all'immagine di base. Sarà necessario pianificare gli aggiornamenti in modo incrementale, affinché non vengano aggiornate contemporaneamente tutte le istanze. È possibile aggiungere nodi per i set di scalabilità di macchine virtuali seguendo la procedura disponibile in [Aggiungere un computer non di Azure a Rilevamento modifiche e inventario](automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory).

### <a name="unsupported-client-types"></a>Tipi di client non supportati

La tabella seguente elenca i sistemi operativi non supportati:

|Sistema operativo  |Note  |
|---------|---------|
|Client Windows     | I sistemi operativi client, ad esempio Windows 7 e Windows 10, non sono supportati.<br> Per Desktop virtuale Windows di Azure il metodo consigliato<br> per la gestione degli aggiornamenti è [Windows Update per le aziende](https://docs.microsoft.com/windows/deployment/update/waas-manage-updates-wufb) per la gestione delle patch del computer client Windows 10. |
|Windows Server 2016 Nano Server     | Non supportato.       |
|Nodi del Servizio Azure Kubernetes | Non supportato. Usare il processo di applicazione di patch illustrato in [Applicare gli aggiornamenti di sicurezza e kernel ai nodi Linux nel servizio Azure Kubernetes](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisiti per i client

Le informazioni seguenti illustrano i requisiti per il client specifici per un sistema operativo. Per altro materiale sussidiario, vedere [Pianificazione della rete](#ports). Per informazioni sui requisiti dei client per TLS 1,2, vedere [tls 1,2 Enforcement for Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Gli agenti Windows devono essere configurati per comunicare con un server WSUS o devono avere accesso a Microsoft Update. Per informazioni su come installare l'agente di Log Analytics per Windows, vedere [Connettere computer Windows a Monitoraggio di Azure](../log-analytics/log-analytics-windows-agent.md).

Gestione aggiornamenti può essere usato con Microsoft Endpoint Configuration Manager. Per altre informazioni sugli scenari di integrazione, vedere [Integrare Gestione aggiornamenti con Windows Endpoint Configuration Manager](updatemgmt-mecmintegration.md). L'[agente di Log Analytics per Windows](../azure-monitor/platform/agent-windows.md) è necessario per i server Windows gestiti dai siti nell'ambiente di Configuration Manager. 

Per impostazione predefinita, le VM di Windows distribuite da Azure Marketplace ricevono aggiornamenti automatici dal servizio Windows Update. Questo comportamento non cambia quando si aggiungono VM Windows all'area di lavoro. Se gli aggiornamenti non vengono gestiti attivamente con Gestione aggiornamenti, è applicabile il comportamento predefinito, ovvero gli aggiornamenti vengono applicati automaticamente.

> [!NOTE]
> È possibile modificare Criteri di gruppo in modo che i riavvii del computer possano essere eseguiti solo dall'utente, non dal sistema. I computer gestiti possono rimanere bloccati se Gestione aggiornamenti non ha i diritti necessari per riavviare il computer senza l'interazione manuale da parte dell'utente. Per altre informazioni, vedere [Configurare le impostazioni di Criteri di gruppo per gli aggiornamenti automatici](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Per Linux, il computer deve avere accesso a un repository degli aggiornamenti, pubblico o privato. Per interagire con Gestione aggiornamenti è necessario TLS 1.1 o TLS 1.2. Gestione aggiornamenti non supporta l'agente di Log Analytics per Linux configurato per l'invio di report a più di un'area di lavoro di Log Analytics. È inoltre necessario che nel computer sia installato Python 2.x.

> [!NOTE]
> La valutazione degli aggiornamenti dei computer Linux è supportata solo in alcune aree. Vedere la [tabella di mapping](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings) dell'account di Automazione e dell'area di lavoro Log Analytics. 

Per ottenere informazioni su come installare l'agente di Log Analytics per Linux e scaricare la versione più recente, vedere l'[agente di Log Analytics per Linux](../azure-monitor/platform/agent-linux.md). 

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux (RHEL) su richiesta e disponibili in Azure Marketplace vengono registrate per accedere al servizio [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) distribuito in Azure. Altre distribuzioni di Linux devono essere aggiornate dal repository di file online della distribuzione mediante i metodi supportati della distribuzione.

## <a name="permissions"></a>Autorizzazioni

Per creare e gestire le distribuzioni degli aggiornamenti, sono necessarie autorizzazioni specifiche. Per informazioni su queste autorizzazioni, vedere [Controllo degli accessi in base al ruolo - Gestione aggiornamenti](automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Componenti di Gestione aggiornamenti

Gestione aggiornamenti usa le risorse descritte in questa sezione. Queste risorse vengono aggiunte automaticamente all'account di Automazione quando si abilita Gestione aggiornamenti.

### <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi

Dopo l'abilitazione di Gestione aggiornamenti, qualsiasi computer Windows direttamente connesso all'area di lavoro Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per runbook per supportare i runbook che supportano Gestione aggiornamenti.

Ogni computer Windows gestito da Gestione aggiornamenti è elencato nella pagina dei gruppi di ruoli di lavoro ibridi come gruppo di ruoli di lavoro ibridi per il sistema per l'account di Automazione. I gruppi usano la convenzione di denominazione `Hostname FQDN_GUID`. Non è possibile applicare runbook a questi gruppi nell'account. Se si prova, il tentativo avrà esito negativo. Questi gruppi sono destinati solo al supporto di Gestione aggiornamenti.

È possibile aggiungere i computer Windows a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, se si usa lo stesso account sia per Gestione aggiornamenti che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="management-packs"></a>Management Pack

Se il gruppo di gestione di Operations Manager è [connesso all'area di lavoro Log Analytics](../azure-monitor/platform/om-agents.md), in Operations Manager verranno installati i Management Pack seguenti. Questi Management Pack vengono installati anche per Gestione aggiornamenti nei computer Windows con connessione diretta. Non è necessario configurare o gestire questi Management Pack.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Se è presente un gruppo di gestione di Operations Manager 1807 o 2019 connesso a un'area di lavoro Log Analytics con agenti configurati nel gruppo di gestione per raccogliere dati di log, sarà necessario eseguire l'override del parametro `IsAutoRegistrationEnabled` e impostarlo su True nella regola **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Per altre informazioni sugli aggiornamenti ai Management Pack, vedere [Connettere Operations Manager ai log di Monitoraggio di Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Per consentire a Gestione aggiornamenti di gestire completamente i computer con l'agente di Log Analytics, è necessario aggiornare l'agente di Log Analytics per Windows o l'agente di Log Analytics per Linux. Per informazioni su come aggiornare l'agente, vedere [How to upgrade an Operations Manager agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents) (Come aggiornare un agente di Operations Manager). Negli ambienti che usano Operations Manager è necessario eseguire System Center Operations Manager 2012 R2 UR 14 o versioni successive.

## <a name="data-collection"></a>Raccolta dati

### <a name="supported-sources"></a>Origini supportate

La tabella seguente descrive le origini connesse supportate da Gestione aggiornamenti:

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso.<br/><br/>Non è necessaria una connessione diretta dall'agente Operations Manager ai log di Monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Gestione aggiornamenti analizza i computer gestiti per individuare i dati usando le regole seguenti. La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

* Ogni computer Windows - Gestione aggiornamenti esegue l'analisi due volte al giorno per ogni computer. Ogni 15 minuti viene eseguita una query nell'API Windows per rilevare la data/ora dell'ultimo aggiornamento e determinare se lo stato è cambiato. Se lo stato è cambiato, Gestione aggiornamenti avvia un'analisi della conformità.

* Ogni computer Linux - Gestione aggiornamenti esegue un'analisi ogni ora.

L'utilizzo medio dei dati da parte dei log di Monitoraggio di Azure per un computer che usa Gestione aggiornamenti è di circa 25 MB al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, in base all'ambiente in uso. È consigliabile monitorare l'ambiente per tenere traccia dell'utilizzo esatto. Per altre informazioni per analizzare l'utilizzo dei dati, vedere [Gestire l'utilizzo e il costo](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Pianificazione della rete

I seguenti indirizzi sono necessari e specifici per Gestione aggiornamenti. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

Per i computer Windows è necessario consentire anche il traffico verso eventuali endpoint richiesti da Windows Update. Un elenco aggiornato degli endpoint necessari è disponibile in [Problemi correlati a HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se si usa un [server di Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) locale, è necessario consentire anche il traffico verso il server specificato nella [chiave di WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Per computer Red Hat Linux, vedere [Gli indirizzi IP per i server di distribuzione di contenuti RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) per informazioni sugli endpoint necessari. Per altre distribuzioni di Linux, vedere la documentazione del provider.

Per altre informazioni sulle porte necessarie per il ruolo di lavoro ibrido per runbook, vedere [Indirizzi di Gestione aggiornamenti per il ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

È consigliabile usare gli indirizzi elencati quando si definiscono eccezioni. Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana con gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Seguire le istruzioni disponibili in [Connettere i computer senza accesso a Internet](../azure-monitor/platform/gateway.md) per configurare i computer senza accesso a Internet.

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

La tabella seguente definisce le classificazioni supportate da Gestione aggiornamenti per gli aggiornamenti di Windows. 

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

La tabella seguente definisce le classificazioni supportate per gli aggiornamenti di Linux.

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti non critici per loro natura o che non sono aggiornamenti della sicurezza.        |

>[!NOTE]
>La classificazione degli aggiornamenti per i computer Linux è disponibile solo se usata nelle aree del cloud pubblico di Azure supportate. Quando si usa Gestione aggiornamenti nelle seguenti aree del cloud nazionale:
>* Azure US Government
>* 21Vianet in Cina
>
> non esiste alcuna classificazione degli aggiornamenti di Linux e vengono segnalati nella categoria **altri aggiornamenti** . Gestione aggiornamenti usa i dati pubblicati dalle distribuzioni supportate, in particolare i file [Oval](https://oval.mitre.org/) (Open vulnerabili and Assessment Language) rilasciati. Poiché l'accesso a Internet è limitato da questi cloud nazionali, Gestione aggiornamenti non può accedere ai file e utilizzarli.

Per Linux, Gestione aggiornamenti è in grado di distinguere tra gli aggiornamenti critici e quelli della sicurezza nel cloud, visualizzando i dati di valutazione dovuti all'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non mette a disposizione queste informazioni nella versione RTM. Se i computer CentOS sono configurati in modo da restituire dati sulla sicurezza per il comando seguente, Gestione aggiornamenti è in grado di applicare patch in base alle classificazioni.

```bash
sudo yum -q --security check-update
```

Attualmente non è supportato alcun metodo per abilitare la disponibilità dei dati di classificazione nativi in CentOS. In questa fase viene offerto il miglior supporto possibile ai clienti che hanno abilitato autonomamente questa funzionalità.

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-security. In Red Hat Enterprise Linux 7 il plug-in fa già parte di yum e non è necessario eseguire alcuna installazione supplementare. Per altre informazioni, vedere questo [file di caratteristiche del caso](https://access.redhat.com/solutions/10021) su Red Hat.

## <a name="integrate-update-management-with-configuration-manager"></a>Integrare Gestione aggiornamenti con Configuration Manager

I clienti che hanno investito in Microsoft Endpoint Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software. Per informazioni su come integrare Gestione aggiornamenti con Configuration Manager, vedere [Integrare Gestione aggiornamenti con Windows Endpoint Configuration Manager](updatemgmt-mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Aggiornamenti di terze parti in Windows

Gestione aggiornamenti si basa sul repository di aggiornamento configurato in locale per aggiornare i sistemi di Windows supportati, ovvero WSUS o Windows Update. Strumenti come [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) consentono di importare e pubblicare aggiornamenti personalizzati con WSUS. Questo scenario consente a Gestione aggiornamenti di aggiornare i computer che usano Configuration Manager come repository degli aggiornamenti del software di terze parti. Per informazioni su come configurare Updates Publisher, vedere [Installare Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

È disponibile un [modello di Azure Resource Manager](automation-update-management-deploy-template.md) per semplificare la distribuzione di Gestione aggiornamenti in un account di Automazione nuovo o esistente e in un'area di lavoro Log Analytics di Monitoraggio di Azure nella sottoscrizione. Non configura l'ambito dei computer che devono essere gestiti. Questa operazione viene eseguita come passaggio separato dopo l'uso del modello.

Di seguito sono elencate le modalità per abilitare Gestione aggiornamenti e selezionare i computer da gestire:

* [Da una macchina virtuale](automation-onboard-solutions-from-vm.md)
* [Dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md)
* [Da un account di Automazione di Azure](automation-onboard-solutions.md)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso di Gestione aggiornamenti, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).

* Esaminare le domande più comuni su Gestione aggiornamenti nelle [Domande frequenti di Automazione di Azure](automation-faq.md).
