---
title: Panoramica dei ruoli di lavoro ibridi per runbook di Automazione di Azure
description: Questo articolo fornisce una panoramica dei ruoli di lavoro ibridi per runbook, che è possibile usare per eseguire runbook su computer nel data center locale o nel provider di servizi cloud.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835292"
---
# <a name="hybrid-runbook-worker-overview"></a>Panoramica del ruolo di lavoro ibrido per runbook

I runbook in Automazione di Azure potrebbero non avere accesso alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nella piattaforma cloud di Azure. È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer assegnati.

Questa funzionalità è illustrata nell'immagine seguente:

![Panoramica del ruolo di lavoro ibrido per runbook](media/automation-hybrid-runbook-worker/automation.png)

Un ruolo di lavoro ibrido per runbook può eseguire il sistema operativo Windows o Linux. Per il monitoraggio, è necessario usare Monitoraggio di Azure e un agente di Analisi dei log per il sistema operativo supportato. Per altre informazioni, vedere [Monitoraggio di Azure](automation-runbook-execution.md#azure-monitor).

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

Il metodo di installazione consigliato consiste nell'usare un runbook di Automazione di Azure per automatizzare completamente il processo necessario di configurazione di un computer Windows. Il secondo metodo prevede l'esecuzione di una procedura dettagliata per installare e configurare il ruolo manualmente. Per i computer Linux si esegue uno script Python per installare l'agente nel computer.

## <a name="network-planning"></a><a name="network-planning"></a>Pianificazione della rete

Per potersi connettere e registrarsi ad Automazione di Azure, il ruolo di lavoro ibrido per runbook deve avere accesso al numero di porta e agli URL descritti in questa sezione. Il ruolo di lavoro deve anche avere accesso [alle porte e agli URL necessari perché l'agente di Analisi dei log](../azure-monitor/platform/agent-windows.md) possa connettersi all'area di lavoro di Analisi dei log di Monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

La porta e gli URL seguenti sono necessari al ruolo di lavoro ibrido per runbook:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

È consigliabile usare gli indirizzi elencati quando si definiscono [eccezioni](automation-runbook-execution.md#exceptions). Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

### <a name="dns-records-per-region"></a>Record DNS per area

Se di dispone di un account di Automazione di Azure definito per un'area specifica, è possibile limitare la comunicazione del ruolo di lavoro ibrido per runbook a tale data center regionale. La tabella seguente indica il record DNS per ogni area.

| **Area** | **Record DNS** |
| --- | --- |
| Australia centrale |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australia orientale |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Asia sud-orientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Stati Uniti occidentali 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft. Una versione aggiornata del file degli indirizzi IP viene pubblicata ogni settimana. 

Il file degli indirizzi IP elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli di calcolo, SQL e archiviazione e riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana.

È consigliabile scaricare il nuovo file degli indirizzi IP ogni settimana. e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. 

> [!NOTE]
> Se si usa ExpressRoute, ricordare che il file degli indirizzi IP viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

### <a name="proxy-server-use"></a>Uso del server proxy

Se si usa un server proxy per la comunicazione tra Automazione di Azure e l'agente di Analisi dei log, verificare che le risorse appropriate siano accessibili. Il timeout per le richieste provenienti dal ruolo di lavoro ibrido per runbook e dai servizi di Automazione è di 30 secondi. Dopo tre tentativi, la richiesta ha esito negativo. 

### <a name="firewall-use"></a>Uso del firewall

Se si usa un firewall per limitare l'accesso a Internet, è necessario configurarlo per consentire l'accesso. Se si usa il gateway di Analisi dei log come proxy, assicurarsi che sia configurato per i ruoli di lavoro ibridi per runbook. Vedere [Configurare il gateway di Analisi dei log per i ruoli di lavoro ibridi per runbook di Automazione](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Gestione aggiornamenti nei ruoli di lavoro ibridi per runbook

Se si abilita la [Gestione aggiornamenti](automation-update-management.md) in Automazione di Azure, qualsiasi computer connesso all'area di lavoro Log Analytics viene automaticamente configurato come ruolo di lavoro ibrido per runbook. Ogni ruolo di lavoro può supportare runbook specifici per la gestione degli aggiornamenti. 

Un computer così configurato non viene registrato con gruppi di ruoli di lavoro ibridi per runbook già definiti nell'account di Automazione. È possibile aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook, ma è necessario usare lo stesso account sia per Gestione aggiornamenti che per l'appartenenza al gruppo di lavoro ibrido per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Indirizzi di Gestione aggiornamenti per i ruoli di lavoro ibridi per runbook

Oltre agli indirizzi e alle porte standard che il ruolo di lavoro ibrido per runbook richiede, per Gestione aggiornamenti sono necessari gli indirizzi specificati nella tabella seguente. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration di Automazione di Azure in un ruolo di lavoro ibrido per runbook

È possibile eseguire [State Configuration di Automazione di Azure](automation-dsc-overview.md) in un ruolo di lavoro ibrido per runbook. Per gestire la configurazione di server che supportano il ruolo di lavoro ibrido per runbook, è necessario aggiungere i server come nodi DSC. Vedere [Abilitare computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbook in un ruolo di lavoro ibrido per runbook

Alcuni runbook gestiscono risorse sul computer locale o vengono eseguiti in base a risorse nell'ambiente locale in cui sono stati distribuiti. In questo caso, è possibile scegliere di eseguire i runbook nel ruolo di lavoro ibrido anziché in un account di Automazione. I runbook eseguiti in un ruolo di lavoro ibrido per runbook hanno una struttura identica a quelli eseguiti nell'account di Automazione. Vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Processi per i ruoli di lavoro ibridi per runbook

Tenere presente che i processi per i ruoli di lavoro ibridi per runbook vengono eseguiti con l'account di **sistema** locale in Windows o con l'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per runbook in modo diverso rispetto ai processi eseguiti all'interno di sandbox di Azure. Vedere [Ambiente di esecuzione dei runbook](automation-runbook-execution.md#runbook-execution-environment).

In caso di riavvio del computer host con il ruolo di lavoro ibrido per runbook, qualsiasi processo di runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell. Se un processo di runbook viene riavviato più di tre volte, viene sospeso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook

Poiché accedono a risorse non di Azure, i runbook eseguiti in un ruolo di lavoro ibrido per runbook non possono usare il meccanismo di autenticazione in genere usato per l'autenticazione dei runbook in risorse di Azure. Il runbook può fornire la propria autenticazione alle risorse locali o configurare l'autenticazione mediante [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).