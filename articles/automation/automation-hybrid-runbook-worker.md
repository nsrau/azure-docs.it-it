---
title: Funzionalità Hybrid Runbook Workers di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione e l'uso di Hybrid Runbook Workers, una funzionalità di Automazione di Azure che consente di eseguire i runbook nei computer del centro dati locale o del provider cloud.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194622"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatizzare le risorse nel centro dati o nel cloud con i ruoli di lavoro ibrido per runbook

I runbook in Automazione di Azure potrebbe non riuscire ad accedere alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nel cloud di Azure. La funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure consente di eseguire runbook direttamente sul computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati.

Questa funzionalità è illustrata nell'immagine seguente:

![Panoramica di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi

Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente. Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito. Ogni ruolo di lavoro del gruppo esegue il polling di Automazione di Azure per vedere se sono disponibili processi. Se è disponibile un processo, il primo ruolo di lavoro che raggiunge il processo lo ottiene. Non è possibile scegliere un computer di lavoro specifico.

## <a name="installing-a-hybrid-runbook-worker"></a>Installazione di un ruolo di lavoro ibrido per runbook

Il processo per installare un ruolo di lavoro ibrido per runbook è diverso a seconda del sistema operativo. La tabella seguente riporta collegamenti a diversi metodi che è possibile usare per installare un ruolo di lavoro ibrido per runbook. Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, sono disponibili due metodi. Il metodo consigliato consiste nell'usare un runbook di Automazione per automatizzare completamente il processo necessario per configurare un computer Windows. Il secondo metodo prevede l'esecuzione di una procedura dettagliata per installare e configurare il ruolo manualmente. Per i computer Linux si esegue uno script Python per installare l'agente nel computer

|OS  |Tipi distribuzione  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuale](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC. Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).
>
>Se si abilita la [soluzione Gestione aggiornamenti](automation-update-management.md), qualsiasi computer connesso all'area di lavoro di Log Analytics viene automaticamente configurato come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione. Non viene però eseguita la registrazione con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione. È possibile aggiungere il computer a un gruppo ruolo di lavoro ibrido per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo ruolo di lavoro ibrido per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

Prima di iniziare la distribuzione di un ruolo di lavoro ibrido per runbook, rivedere le [informazioni per la pianificazione della rete](#network-planning). Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.

## <a name="removing-hybrid-runbook-worker"></a>Rimozione del ruolo di lavoro ibrido per runbook

È possibile rimuovere uno o più ruoli di lavoro ibrido per runbook da un gruppo o è possibile rimuovere il gruppo, a seconda dei requisiti. Per rimuovere un ruolo di lavoro ibrido per runbook da un computer locale, eseguire la procedura seguente:

1. Nel portale di Azure passare all'account di Automazione.
2. Nel pannello **Impostazioni** selezionare **Chiavi** e prendere nota dei valori per il campo **URL** e **Chiave di accesso primaria**. Queste informazioni saranno necessarie per il passaggio successivo.

### <a name="windows"></a>Windows

Aprire una sessione di PowerShell in modalità amministratore ed eseguire il comando seguente. Per un log dettagliato del processo di rimozione, usare l'opzione **-Verbose** .

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Per rimuovere le macchine non aggiornate dal gruppo di ruoli di lavoro ibridi, usare il parametro facoltativo `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Questa operazione non rimuove Microsoft Monitoring Agent dal computer, ma solo la funzionalità e la configurazione del ruolo di lavoro ibrido per runbook.

## <a name="remove-hybrid-worker-groups"></a>Rimuovere gruppi di ruoli di lavoro ibridi

Per rimuovere un gruppo, è innanzitutto necessario rimuovere il ruolo di lavoro ibrido per runbook da ogni computer membro del gruppo usando la procedura descritta in precedenza e quindi attenersi alla procedura seguente per rimuovere il gruppo.

1. Nel portale di Azure aprire l'account di automazione.
1. In **Automazione processi** selezionare **Gruppi di lavoro ibridi**. Selezionare il gruppo da eliminare. Dopo aver selezionato il gruppo specifico, viene visualizzata la pagina delle proprietà del **Gruppo di lavoro ibrido**.

   ![Pagina del gruppo di ruoli di lavoro ibridi per runbook](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Nella pagina delle proprietà del gruppo selezionato fare clic su **Elimina**. Viene visualizzato un messaggio che chiede di confermare l'azione. Selezionare **Sì** se si è certi che si vuole procedere.

   ![Finestra di dialogo di conferma dell'eliminazione del gruppo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

## <a name="network-planning"></a>Configurare la rete

### <a name="hybrid-worker-role"></a>Ruolo di lavoro ibrido

Per connettersi e registrarsi a Log Analytics, il ruolo di lavoro ibrido per runbook deve avere accesso al numero di porta e agli URL descritti in questa sezione, in aggiunta [alle porte e agli URL necessari per la connessione di Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) a Log Analytics.

Se si usa un server proxy per la comunicazione tra l'agente e il servizio Log Analytics, verificare che le risorse appropriate siano accessibili. Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso.

La porta e gli URL seguenti sono necessari affinché il ruolo di lavoro ibrido per runbook comunichi con Automazione:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita.
* URL globale: *.azure-automation.net
* URL globale di US Gov Virginia: *.azure-automation.us
* Servizio agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area.

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asia sudorientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Gli intervalli per le risorse di calcolo, SQL e di archiviazione sono inclusi nel file.
>
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana.
>
> È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

### <a name="update-management"></a>Gestione degli aggiornamenti

Oltre agli indirizzi e alle porte standard che il ruolo di lavoro ibrido per runbook richiede, per Gestione aggiornamenti sono necessari i seguenti indirizzi specifici. La comunicazione verso questi indirizzi avviene sulla porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>risoluzione dei problemi

Il ruolo di lavoro ibrido per runbook dipende da un agente per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Per Windows questo agente è Microsoft Monitoring Agent. Per Linux è l'agente di Operations Management Suite Agent per Linux. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Il ruolo di lavoro ibrido è protetto da proxy o firewall

Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulla porta 443.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>Il computer in cui è in esecuzione il ruolo di lavoro ibrido non soddisfa i requisiti hardware

I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi per poter ospitare questa funzionalità. In caso contrario, a seconda dell'uso di risorse di altri processi in background e dei conflitti causati dai runbook durante l'esecuzione, il computer diverrà sovraccarico e causerà ritardi o interruzioni del processo del runbook.

Verificare che il computer designato per svolgere il ruolo di lavoro ibrido per runbook soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo della CPU e della memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. In caso di utilizzo eccessivo della CPU o memoria, potrebbe essere necessario aggiornare o aggiungere altri processori o aumentare la memoria per risolvere il collo di bottiglia della risorsa e quindi l'errore. In alternativa, selezionare una risorsa di calcolo diversa in grado di supportare i requisiti minimi e scalare quando le esigenze del carico di lavoro indicano la necessità di un aumento.

Per altre informazioni sulla risoluzione dei problemi per uno specifico sistema operativo, vedere [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#troubleshooting) (Ruolo di lavoro ibrido per runbook in Linux) o [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#troubleshooting) (Ruolo di lavoro ibrido per runbook in Windows)

## <a name="next-steps"></a>Passaggi successivi

Esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.
