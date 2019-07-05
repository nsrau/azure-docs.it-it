---
title: Ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione e l'uso di Hybrid Runbook Workers, una funzionalità di Automazione di Azure che si può usare per eseguire i runbook nei computer del centro dati locale o del provider di servizi cloud.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fceeed47ee77207e00ebfc619226ecbb5956bc3d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478520"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizzare le risorse nel centro dati o nel cloud usando i ruoli di lavoro ibridi per runbook

I runbook in Automazione di Azure potrebbero non avere accesso alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nella piattaforma cloud di Azure. È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer assegnati.

Questa funzionalità è illustrata nell'immagine seguente:

![Panoramica del ruolo di lavoro ibrido per runbook](media/automation-hybrid-runbook-worker/automation.png)

Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente. Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito. Ogni ruolo di lavoro del gruppo esegue il polling di Automazione di Azure per vedere se sono disponibili processi. Se è disponibile un processo, il primo ruolo di lavoro che raggiunge il processo lo ottiene. Il tempo di elaborazione della coda processi dipende dal profilo hardware e dal carico del ruolo di lavoro ibrido. Non è possibile scegliere un computer di lavoro specifico. I ruoli di lavoro ibridi per runbook mancano di molti dei limiti presenti invece in sandbox di Azure, ad esempio non hanno gli stessi limiti di spazio su disco, memoria o socket di rete. I ruoli di lavoro ibridi per runbook sono limitati soltanto dalle risorse del ruolo di lavoro ibrido per runbook stesso. Inoltre, a differenza dai sandbox di Azure, i ruoli di lavoro ibridi per runbook non hanno il limite di tempo di [condivisione equa](automation-runbook-execution.md#fair-share) di 180 minuti. Per altre informazioni sui limiti del servizio per i sandbox di Azure e i ruoli di lavoro ibridi per runbook, vedere la pagina sui [limiti](../azure-subscription-service-limits.md#automation-limits) del processo.

## <a name="install-a-hybrid-runbook-worker"></a>Installare un ruolo di lavoro ibrido per runbook

Il processo per installare un ruolo di lavoro ibrido per runbook dipende del sistema operativo. La tabella seguente contiene collegamenti ai metodi che è possibile usare per l'installazione.

Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, è possibile usare due metodi. Il metodo consigliato consiste nell'usare un runbook di Automazione per automatizzare completamente il processo necessario di configurazione di un computer Windows. Il secondo metodo prevede l'esecuzione di una procedura dettagliata per installare e configurare il ruolo manualmente. Per i computer Linux si esegue uno script Python per installare l'agente nel computer.

|OS  |Tipi distribuzione  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuale](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC. Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).
>
>Se si abilita la [soluzione Gestione aggiornamenti](automation-update-management.md), qualsiasi computer connesso all'area di lavoro Azure Log Analytics viene automaticamente configurato come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione. Tuttavia, il computer non viene registrato con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione. È possibile aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

Prima di iniziare la distribuzione di un ruolo di lavoro ibrido per runbook, rivedere le [informazioni per la pianificazione della rete](#network-planning). Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione di runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.

È possibile aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.
## <a name="remove-a-hybrid-runbook-worker"></a>Rimuovere un ruolo di lavoro ibrido per runbook

È possibile rimuovere uno o più ruoli di lavoro ibridi per runbook da un gruppo o è possibile rimuovere il gruppo, a seconda dei requisiti. Per rimuovere un ruolo di lavoro ibrido per runbook da un computer locale, usare la procedura seguente:

1. Nel portale di Azure passare all'account di Automazione.
2. In **Impostazioni Account** selezionare **Chiavi** e prendere nota dei valori di **URL** e **Chiave di accesso primaria**. Queste informazioni saranno necessarie per il passaggio successivo.

### <a name="windows"></a>Windows

Aprire una sessione di PowerShell in modalità amministratore ed eseguire il comando seguente. Per un log dettagliato del processo di rimozione, usare l'opzione **-Verbose** .

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Per rimuovere le macchine non aggiornate dal gruppo di ruoli di lavoro ibridi, usare il parametro facoltativo `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

È possibile usare il comando `ls /var/opt/microsoft/omsagent` in ruolo di lavoro ibrido per runbook per ottenere l'ID area di lavoro. È presente una cartella nella directory in cui il nome della cartella è l'ID dell'area di lavoro.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Questo codice non rimuove Microsoft Monitoring Agent dal computer, ma solo la funzionalità e la configurazione del ruolo di lavoro ibrido per runbook.

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo, è innanzitutto necessario rimuovere il ruolo di lavoro ibrido per runbook da ogni computer membro del gruppo usando la procedura descritta in precedenza. Quindi usare la procedura seguente per rimuovere il gruppo:

1. Nel portale di Azure aprire l'account di automazione.
2. In **Automazione processi** selezionare **Gruppi di lavoro ibridi**. Selezionare il gruppo che si vuole eliminare. Viene visualizzata la pagina delle proprietà per quel gruppo.

   ![Pagina Proprietà](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Nella pagina delle proprietà del gruppo selezionato selezionare **Elimina**. Compare un messaggio che chiede di confermare. Selezionare **Sì** se si è certi di voler continuare.

   ![Messaggio di conferma](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

## <a name="network-planning"></a>Configurare la rete

### <a name="hybrid-worker-role"></a>Ruolo di lavoro ibrido

Per Hybrid Runbook Workers per connettersi e registrarsi con automazione di Azure, deve avere accesso al numero di porta e gli URL descritti in questa sezione. Questo accesso è in primo piano per la [porte e agli URL necessari per Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) per connettersi a log di monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se si usa un server proxy per la comunicazione tra l'agente e il servizio automazione di Azure, verificare che le risorse appropriate siano accessibili. Il timeout per le richieste di lavoro ibrido per Runbook e i servizi di automazione è 30 secondi. Dopo 3 tentativi di richiesta avrà esito negativo. Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso. Se si usa il gateway Log Analytics come proxy, assicurarsi che sia configurato per i ruoli di lavoro ibridi. Per istruzioni su come eseguire questa procedura, vedere [Configurare il gateway Log Analytics per i ruoli di lavoro ibridi per runbook di Automazione](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

La porta e gli URL seguenti sono necessari affinché il ruolo di lavoro ibrido per runbook comunichi con Automazione:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita.
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

È consigliabile usare gli indirizzi elencati quando si definiscono eccezioni. Per gli indirizzi IP è possibile scaricare gli [intervalli di indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area:

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Stati Uniti occidentali 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asia sudorientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia orientale |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione.
>
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana.
>
> È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

### <a name="update-management"></a>Gestione degli aggiornamenti

Oltre agli indirizzi e alle porte standard che il ruolo di lavoro ibrido per runbook richiede, per Gestione aggiornamenti sono necessari gli indirizzi specifici seguenti. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per informazioni su come risolvere i problemi del ruolo di lavoro ibrido per runbook, vedere [Troubleshooting Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#general) (Risoluzione dei problemi dei ruoli di lavoro ibridi per runbook)

