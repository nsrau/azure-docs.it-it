---
title: Panoramica dei ruoli di lavoro ibridi per runbook di Automazione di Azure
description: Questo articolo fornisce una panoramica dei ruoli di lavoro ibridi per runbook, che è possibile usare per eseguire runbook su computer nel data center locale o nel provider di servizi cloud.
services: automation
ms.subservice: process-automation
ms.date: 09/14/2020
ms.topic: conceptual
ms.openlocfilehash: 3c88d21c6ad17c613c5d708bf697ae8717c9ec91
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075264"
---
# <a name="hybrid-runbook-worker-overview"></a>Panoramica del ruolo di lavoro ibrido per runbook

I runbook in Automazione di Azure potrebbero non avere accesso alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nella piattaforma cloud di Azure. È possibile usare la funzionalità Hybrid Runbook Worker di automazione di Azure per eseguire manuali operativi direttamente sul computer che ospita il ruolo e sulle risorse nell'ambiente per gestire tali risorse locali. Manuali operativi vengono archiviati e gestiti in automazione di Azure e quindi recapitati a uno o più computer assegnati.

Questa funzionalità è illustrata nell'immagine seguente:

![Panoramica del ruolo di lavoro ibrido per runbook](media/automation-hybrid-runbook-worker/automation.png)

Un ruolo di lavoro ibrido per Runbook può essere eseguito nel sistema operativo Windows o Linux. Dipende dal [log Analytics agente](../azure-monitor/platform/log-analytics-agent.md) che invia report a un' [area di lavoro log Analytics](../azure-monitor/platform/design-logs-deployment.md)di monitoraggio di Azure. L'area di lavoro non solo consente di monitorare il computer per il sistema operativo supportato, ma anche di scaricare i componenti necessari per il ruolo di lavoro ibrido per Runbook.

Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente. Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata. Ogni computer può ospitare un solo ruolo di lavoro ibrido che invia report a un unico account di Automazione.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito. Ogni ruolo di lavoro del gruppo esegue il polling di Automazione di Azure per vedere se sono disponibili processi. Se è disponibile un processo, il primo ruolo di lavoro che raggiunge il processo lo ottiene. Il tempo di elaborazione della coda processi dipende dal profilo hardware e dal carico del ruolo di lavoro ibrido. Non è possibile scegliere un computer di lavoro specifico.

Usare un ruolo di lavoro ibrido per runbook invece di una [sandbox di Azure](automation-runbook-execution.md#runbook-execution-environment) perché non ha molti dei limiti delle [sandbox](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) relativi a spazio su disco, memoria o socket di rete. I limiti di un ruolo di lavoro ibrido sono correlati solo alle risorse del ruolo di lavoro.

> [!NOTE]
> I ruoli di lavoro ibridi per runbook non sono limitati dai tempi di [condivisione dinamica](automation-runbook-execution.md#fair-share) previsti per le sandbox di Azure.

## <a name="hybrid-runbook-worker-installation"></a>Installazione di un ruolo di lavoro ibrido per runbook

Il processo per installare un ruolo di lavoro ibrido per runbook dipende del sistema operativo. La tabella seguente definisce i tipi di distribuzione.

|Sistema operativo  |Tipi distribuzione  |
|---------|---------|
|Windows     | [Automatizzata](automation-windows-hrw-install.md#automated-deployment)<br>[Manuale](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Il metodo di installazione consigliato prevede l'uso di un Runbook di automazione di Azure per automatizzare completamente il processo di configurazione di un computer Windows. Se ciò non è possibile, è possibile seguire una procedura dettagliata per installare e configurare manualmente il ruolo. Per i computer Linux si esegue uno script Python per installare l'agente nel computer.

## <a name="network-planning"></a><a name="network-planning"></a>Pianificazione della rete

Per potersi connettere e registrarsi ad Automazione di Azure, il ruolo di lavoro ibrido per runbook deve avere accesso al numero di porta e agli URL descritti in questa sezione. Il ruolo di lavoro deve anche avere accesso [alle porte e agli URL necessari perché l'agente di Analisi dei log](../azure-monitor/platform/agent-windows.md) possa connettersi all'area di lavoro di Analisi dei log di Monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

La porta e gli URL seguenti sono necessari al ruolo di lavoro ibrido per runbook:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita
* URL globale: `*.azure-automation.net`
* URL globale di US Gov Virginia: `*.azure-automation.us`
* Servizio Agent: `https://<workspaceId>.agentsvc.azure-automation.net`

Se di dispone di un account di Automazione di Azure definito per un'area specifica, è possibile limitare la comunicazione del ruolo di lavoro ibrido per runbook a tale data center regionale. Esaminare i [record DNS usati da automazione di Azure](how-to/automation-region-dns-records.md) per i record DNS necessari.

### <a name="proxy-server-use"></a>Uso del server proxy

Se si usa un server proxy per la comunicazione tra automazione di Azure e i computer che eseguono l'agente di Log Analytics, assicurarsi che le risorse appropriate siano accessibili. Il timeout per le richieste provenienti dal ruolo di lavoro ibrido per runbook e dai servizi di Automazione è di 30 secondi. Dopo tre tentativi, la richiesta ha esito negativo.

### <a name="firewall-use"></a>Uso del firewall

Se si usa un firewall per limitare l'accesso a Internet, è necessario configurarlo per consentire l'accesso. Se si usa il gateway di Analisi dei log come proxy, assicurarsi che sia configurato per i ruoli di lavoro ibridi per runbook. Vedere [configurare il gateway log Analytics per i ruoli di lavoro ibridi per Runbook di automazione](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Tag di servizio

Automazione di Azure supporta i tag del servizio rete virtuale di Azure, a partire dal tag del servizio [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md#security-rules) o nel firewall di [Azure](../firewall/service-tags.md). I tag di servizio possono essere usati al posto di indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio **GuestAndHybridManagement**  nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio di automazione. Questo tag di servizio non supporta l'abilitazione di un controllo più granulare limitando gli intervalli IP a un'area specifica.

Il tag di servizio per il servizio automazione di Azure fornisce solo gli indirizzi IP usati per gli scenari seguenti:

* Attivare webhook dall'interno della rete virtuale
* Consenti ai ruoli di lavoro ibridi per Runbook o agli agenti di configurazione dello stato nella VNet di comunicare con il servizio di automazione

>[!NOTE]
>Il tag di servizio **GuestAndHybridManagement** attualmente non supporta l'esecuzione di processi Runbook in una sandbox di Azure, solo direttamente in un ruolo di lavoro ibrido per Runbook.

## <a name="support-for-impact-level-5-il5"></a>Supporto per il livello di Impact 5 (IL5)

Il ruolo di lavoro ibrido per Runbook di automazione di Azure può essere usato in Azure per enti pubblici per supportare carichi di lavoro di livello 5 di Impact in una delle due configurazioni seguenti:

* [Macchina virtuale isolata](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Quando vengono distribuiti, utilizzano l'intero host fisico per tale macchina virtuale, fornendo il livello di isolamento necessario per supportare i carichi di lavoro IL5.

* [Host dedicati di Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure.

>[!NOTE]
>L'isolamento di calcolo tramite il ruolo di lavoro ibrido per Runbook è disponibile per i cloud commerciali di Azure e per enti pubblici statunitensi. 

## <a name="update-management-on-hybrid-runbook-worker"></a>Gestione aggiornamenti nei ruoli di lavoro ibridi per runbook

Quando [Gestione aggiornamenti](update-management/update-mgmt-overview.md) di automazione di Azure è abilitata, tutti i computer connessi all'area di lavoro log Analytics vengono configurati automaticamente come ruolo di lavoro ibrido per Runbook. Ogni ruolo di lavoro può supportare runbook specifici per la gestione degli aggiornamenti.

Un computer configurato in questo modo non è registrato con nessun gruppo di lavoro ibrido per Runbook già definito nell'account di automazione. È possibile aggiungere il computer a un gruppo di ruolo di lavoro ibrido per Runbook, ma è necessario usare lo stesso account sia per Gestione aggiornamenti che per l'appartenenza al gruppo di lavoro ibrido per Runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Indirizzi di Gestione aggiornamenti per i ruoli di lavoro ibridi per runbook

Oltre agli indirizzi e alle porte standard richiesti per il ruolo di lavoro ibrido per Runbook, Gestione aggiornamenti dispone di requisiti di configurazione di rete aggiuntivi descritti nella sezione relativa alla [pianificazione della rete](update-management/update-mgmt-overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration di Automazione di Azure in un ruolo di lavoro ibrido per runbook

È possibile eseguire [State Configuration di Automazione di Azure](automation-dsc-overview.md) in un ruolo di lavoro ibrido per runbook. Per gestire la configurazione di server che supportano il ruolo di lavoro ibrido per runbook, è necessario aggiungere i server come nodi DSC. Vedere [Abilitare computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbook in un ruolo di lavoro ibrido per runbook

È possibile che si disponga di manuali operativi per gestire le risorse nel computer locale o di eseguire le risorse nell'ambiente locale in cui viene distribuito un ruolo di lavoro ibrido per Runbook. In questo caso, è possibile scegliere di eseguire i runbook nel ruolo di lavoro ibrido anziché in un account di Automazione. I runbook eseguiti in un ruolo di lavoro ibrido per runbook hanno una struttura identica a quelli eseguiti nell'account di Automazione. Vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Processi per i ruoli di lavoro ibridi per runbook

Tenere presente che i processi per i ruoli di lavoro ibridi per runbook vengono eseguiti con l'account di **sistema** locale in Windows o con l'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per runbook in modo diverso rispetto ai processi eseguiti all'interno di sandbox di Azure. Vedere [Ambiente di esecuzione dei runbook](automation-runbook-execution.md#runbook-execution-environment).

In caso di riavvio del computer host con il ruolo di lavoro ibrido per runbook, qualsiasi processo di runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell. Se un processo di runbook viene riavviato più di tre volte, viene sospeso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook

Poiché accedono a risorse non di Azure, i runbook eseguiti in un ruolo di lavoro ibrido per runbook non possono usare il meccanismo di autenticazione in genere usato per l'autenticazione dei runbook in risorse di Azure. Il runbook può fornire la propria autenticazione alle risorse locali o configurare l'autenticazione mediante [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

## <a name="view-hybrid-runbook-workers"></a>Visualizza i ruoli di lavoro ibridi per Runbook

Una volta abilitata la funzionalità Gestione aggiornamenti nei server o nelle macchine virtuali Windows, è possibile inventariare l'elenco di gruppi di ruoli di lavoro ibridi per Runbook di sistema nel portale di Azure. È possibile visualizzare fino a 2.000 processi di lavoro nel portale selezionando il **gruppo Tab System Hybrid Workers** nell'opzione **gruppo Hybrid Workers** dal riquadro a sinistra per l'account di automazione selezionato.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Pagina gruppi di lavoro ibridi del sistema di account di automazione" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Se si dispone di più di 2.000 ruoli di lavoro ibridi, per ottenere un elenco di tutti i ruoli, è possibile eseguire lo script di PowerShell seguente:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).