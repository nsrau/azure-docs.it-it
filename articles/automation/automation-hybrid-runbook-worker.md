---
title: Panoramica del ruolo di lavoro ibrido per Runbook in automazione di Azure
description: Questo articolo fornisce una panoramica del ruolo di lavoro ibrido per Runbook, una funzionalità di automazione di Azure che è possibile usare per eseguire manuali operativi nei computer del Data Center locale o del provider di servizi cloud.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 0b36651a40267ec3ea8bfe7285c5f6c5d5c31562
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871649"
---
# <a name="hybrid-runbook-worker-overview"></a>Panoramica del ruolo di lavoro ibrido per runbook

I runbook in Automazione di Azure potrebbero non avere accesso alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nella piattaforma cloud di Azure. È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer assegnati.

Questa funzionalità è illustrata nell'immagine seguente:

![Panoramica del ruolo di lavoro ibrido per runbook](media/automation-hybrid-runbook-worker/automation.png)

Un ruolo di lavoro ibrido per Runbook può eseguire il sistema operativo Windows o Linux. Per il monitoraggio, è necessario usare monitoraggio di Azure e un agente Log Analytics per il sistema operativo supportato. Per altre informazioni, vedere [Monitoraggio di Azure](automation-runbook-execution.md#azure-monitor).

Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente. Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata. Ogni computer può ospitare un ruolo di lavoro ibrido che invia report a un account di automazione. 

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito. Ogni ruolo di lavoro del gruppo esegue il polling di Automazione di Azure per vedere se sono disponibili processi. Se è disponibile un processo, il primo ruolo di lavoro che raggiunge il processo lo ottiene. Il tempo di elaborazione della coda processi dipende dal profilo hardware e dal carico di lavoro ibrido. Non è possibile scegliere un computer di lavoro specifico. 

Usare un ruolo di lavoro ibrido per Runbook invece di una [sandbox di Azure](automation-runbook-execution.md#runbook-execution-environment) perché non ha molti [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) di sandbox su spazio su disco, memoria o socket di rete. I limiti di un ruolo di lavoro ibrido sono correlati solo alle risorse del ruolo di lavoro. 

> [!NOTE]
> I ruoli di lavoro ibridi per Runbook non sono limitati dal limite di tempo di [condivisione equa](automation-runbook-execution.md#fair-share) di Azure sandbox. 

## <a name="hybrid-runbook-worker-installation"></a>Installazione del ruolo di lavoro ibrido per Runbook

Il processo di installazione di un ruolo di lavoro ibrido per Runbook dipende dal sistema operativo. La tabella seguente definisce i tipi di distribuzione.

|Sistema operativo  |Tipi distribuzione  |
|---------|---------|
|Windows     | [Automatizzata](automation-windows-hrw-install.md#automated-deployment)<br>[Manuale](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Il metodo di installazione consigliato prevede l'uso di un Runbook di automazione di Azure per automatizzare completamente il processo di configurazione di un computer Windows. Il secondo metodo consiste nel seguire una procedura dettagliata per installare e configurare manualmente il ruolo. Per i computer Linux si esegue uno script Python per installare l'agente nel computer.

## <a name="network-configuration"></a><a name="network-planning"></a>Configurazione di rete

Per la connessione e la registrazione con automazione di Azure, il ruolo di lavoro ibrido per Runbook deve avere accesso al numero di porta e agli URL descritti in questa sezione. Il ruolo di lavoro deve inoltre avere accesso alle [porte e agli URL necessari per](../azure-monitor/platform/agent-windows.md) la connessione di log Analytics Agent all'area di lavoro log Analytics di monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Per il ruolo di lavoro ibrido per Runbook sono necessari la porta e gli URL seguenti:

* Porta: solo TCP 443 necessari per l'accesso a Internet in uscita
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Si consiglia di utilizzare gli indirizzi elencati durante la definizione delle [eccezioni](automation-runbook-execution.md#exceptions). Per gli indirizzi IP, è possibile scaricare gli [intervalli IP del data center Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

### <a name="dns-records-per-region"></a>Record DNS per area

Se si dispone di un account di automazione definito per un'area specifica, è possibile limitare la comunicazione del ruolo di lavoro ibrido per Runbook a tale data center regionale. La tabella seguente indica il record DNS per ogni area.

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

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft. Un file di indirizzi IP aggiornato viene pubblicato settimanalmente. 

Il file di indirizzi IP elenca gli intervalli di indirizzi IP usati nei Data Center Microsoft Azure. Include gli intervalli di calcolo, SQL e archiviazione e riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana.

È consigliabile scaricare il nuovo file di indirizzi IP ogni settimana. e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. 

> [!NOTE]
> Se si usa Azure ExpressRoute, tenere presente che il file di indirizzi IP viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure nella prima settimana di ogni mese.

### <a name="proxy-server-use"></a>Utilizzo server proxy

Se si usa un server proxy per la comunicazione tra automazione di Azure e l'agente di Log Analytics, assicurarsi che le risorse appropriate siano accessibili. Il timeout per le richieste provenienti dal ruolo di lavoro ibrido per Runbook e dai servizi di automazione è 30 secondi. Dopo tre tentativi, una richiesta ha esito negativo. 

### <a name="firewall-use"></a>Uso del firewall

Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso. Se si usa il gateway Log Analytics come proxy, assicurarsi che sia configurato per i ruoli di lavoro ibridi per Runbook. Vedere [configurare il gateway log Analytics per i ruoli di lavoro ibridi di automazione](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Gestione aggiornamenti sul ruolo di lavoro ibrido per Runbook

Quando [Gestione aggiornamenti](automation-update-management.md) di automazione di Azure è abilitata, tutti i computer connessi all'area di lavoro log Analytics vengono configurati automaticamente come ruolo di lavoro ibrido per Runbook. Ogni ruolo di lavoro può supportare manuali operativi per la gestione degli aggiornamenti. 

Un computer configurato in questo modo non è registrato con nessun gruppo di lavoro ibrido per Runbook già definito nell'account di automazione. È possibile aggiungere il computer a un gruppo di ruolo di lavoro ibrido per Runbook, ma è necessario usare lo stesso account sia per Gestione aggiornamenti che per l'appartenenza al gruppo di lavoro ibrido per Runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per Runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Indirizzi Gestione aggiornamenti per il ruolo di lavoro ibrido per Runbook

Per gli indirizzi e le porte standard necessari per il ruolo di lavoro ibrido per Runbook, Gestione aggiornamenti necessari gli indirizzi nella tabella seguente. Per la comunicazione con questi indirizzi viene utilizzata la porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="state-configuration-dsc-on-hybrid-runbook-worker"></a>Configurazione dello stato (DSC) sul ruolo di lavoro ibrido per Runbook

È possibile eseguire la funzionalità di [configurazione dello stato (DSC)](automation-dsc-overview.md) in un ruolo di lavoro ibrido per Runbook. Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per Runbook, è necessario aggiungere i server come nodi DSC. Per ulteriori informazioni sull'onboarding, vedere [onboarding machines for Management by state Configuration (DSC)](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Manuali operativi in un ruolo di lavoro ibrido per Runbook

È possibile che si disponga di manuali operativi per gestire le risorse nel computer locale o di eseguire le risorse nell'ambiente locale in cui viene distribuito un ruolo di lavoro ibrido per Runbook. In questo caso, è possibile scegliere di eseguire il manuali operativi nel ruolo di lavoro ibrido anziché in un account di automazione. I manuali operativi eseguiti in un ruolo di lavoro ibrido per Runbook sono identici a quelli eseguiti nell'account di automazione. Vedere [eseguire manuali operativi in un ruolo di lavoro ibrido per Runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Processi di lavoro ibrido per Runbook

I processi di lavoro ibrido per Runbook vengono eseguiti con l'account di **sistema** locale in Windows o l' [account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per Runbook in modo diverso rispetto ai processi eseguiti in sandbox di Azure. Vedere [ambiente di esecuzione Runbook](automation-runbook-execution.md#runbook-execution-environment).

Se il computer host del ruolo di lavoro ibrido per Runbook viene riavviato, qualsiasi processo Runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per manuali operativi del flusso di lavoro PowerShell. Quando un processo del Runbook viene riavviato più di tre volte, viene sospeso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni Runbook per un ruolo di lavoro ibrido per Runbook

Poiché accedono a risorse non di Azure, manuali operativi in esecuzione in un ruolo di lavoro ibrido per Runbook non possono usare il meccanismo di autenticazione usato in genere da manuali operativi per l'autenticazione alle risorse di Azure. Un Runbook fornisce l'autenticazione per le risorse locali o configura l'autenticazione usando [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti manuali operativi.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per Runbook, vedere [risoluzione dei problemi di Runbook](troubleshoot/hybrid-runbook-worker.md#general).