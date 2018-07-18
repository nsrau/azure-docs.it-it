---
title: Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook su computer Windows nel data center locale o nell'ambiente cloud.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e449e6e457c4fa568b5a4de5823014b4dcea82d0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064543"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati. In questo articolo viene illustrato come installare il ruolo di lavoro ibrido per runbook in un computer Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installazione del ruolo di lavoro ibrido per runbook di Windows

Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, è possibile usare due metodi. Il metodo consigliato consiste nell'usare un runbook di Automazione per automatizzare completamente il processo necessario di configurazione di un computer Windows. Il secondo metodo prevede l'esecuzione di una procedura dettagliata per installare e configurare il ruolo manualmente.

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC.

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Windows sono i seguenti:

* Windows Server 2012 o versioni successive.
* Windows PowerShell 4.0 o versioni successive ([scaricare WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Windows PowerShell 5.1 ([scaricare WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) è consigliato per la sua maggiore affidabilità.
* .NET Framework 4.6.2 o versioni successive.
* Due core.
* 4 GB di RAM.
* Porta 443 (in uscita).

Per altri requisiti di rete per il ruolo di lavoro ibrido per runbook, vedere la sezione [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).
Se si abilita la [soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md), qualsiasi computer Windows connesso all'area di lavoro di Azure Log Analytics viene automaticamente configurato come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione. Non viene però eseguita la registrazione con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione. 

È possibile aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

### <a name="automated-deployment"></a>Distribuzione automatizzata

Seguire questa procedura per automatizzare l'installazione e la configurazione del ruolo di lavoro ibrido di Windows:

1. Scaricare lo script New-OnPremiseHybridWorker.ps1 da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente. Copiare lo script sul ruolo di lavoro.

   Per l'esecuzione dello script New-OnPremiseHybridWorker.ps1 sono necessari i parametri seguenti:

   * *AutomationAccountName* (obbligatorio): nome dell'account di automazione.
   * *AAResourceGroupName* (obbligatorio): nome del gruppo di risorse associato all'account di automazione.
   * *OMSResourceGroupName* (facoltativo): nome del gruppo di risorse per l'area di lavoro di Operations Management Suite. Se il gruppo di risorse non è specificato, verrà usato *AAResourceGroupName*.
   * *HybridGroupName* (obbligatorio): nome di un gruppo di ruoli di lavoro ibridi per runbook specificato come destinazione per i runbook che supportano questo scenario.
   * *SubscriptionID* (obbligatorio): ID sottoscrizione di Azure in cui si trova l'account di Automazione.
   * *WorkspaceName* (facoltativo): nome dell'area di lavoro di Log Analytics. Se non si dispone di un'area di lavoro di Log Analytics, lo script ne crea e configura una.

     > [!NOTE]
     > Le uniche aree di Automazione supportate per l'integrazione con Log Analytics sono attualmente: **Australia sud-orientale**, **Stati Uniti orientali 2**, **Asia sud-orientale** ed **Europa occidentale**. Se l'account di automazione non si trova in una di queste aree, lo script crea un'area di lavoro di Log Analytics ma avvisa l'utente che non sarà possibile eseguire il collegamento.

1. Nel computer in uso aprire **Windows PowerShell** dalla schermata **Start** in modalità amministratore.
1. Dalla shell della riga di comando di PowerShell, passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*. Quindi, eseguire lo script.

     > [!NOTE]
     > Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È *necessario* accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Verrà chiesto di accettare di installare NuGet e di eseguire l'autenticazione con le credenziali di Azure.

1. Dopo il completamento dello script, nella pagina **Gruppi di ruoli di lavoro ibridi** vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo nell'elenco della pagina **Gruppi di ruoli di lavoro ibridi** e selezionare il riquadro **Ruoli di lavoro per runbook**. Nella pagina **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.

### <a name="manual-deployment"></a>Distribuzione manuale

Eseguire i primi due passaggi una volta per l'ambiente di automazione e quindi ripetere i passaggi rimanenti per ogni computer di lavoro.

#### <a name="1-create-a-log-analytics-workspace"></a>1. Creare un'area di lavoro di Log Analytics

Se non si ha ancora un'area di lavoro di Log Analytics, crearne una seguendo le istruzioni contenute in [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md). Se già si dispone di un'area di lavoro, è possibile usarla.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Aggiungere la soluzione Automazione all'area di lavoro di Log Analytics

Le soluzioni aggiungono funzionalità a Log Analytics. La soluzione di automazione aggiunge funzionalità per Automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook. Quando si aggiunge la soluzione all'area di lavoro, i componenti del ruolo di lavoro vengono automaticamente propagati al computer dell'agente che verrà installato nel passaggio successivo.

Per aggiungere la soluzione [Automazione](../log-analytics/log-analytics-add-solutions.md) all'area di lavoro di Log Analytics, seguire le istruzioni su **come aggiungere una soluzione tramite la raccolta soluzioni**.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installare Microsoft Monitoring Agent

Microsoft Monitoring Agent connette i computer a Log Analytics. Quando si installa l'agente nel computer locale e lo si connette all'area di lavoro, viene eseguito automaticamente il download dei componenti necessari per il ruolo di lavoro ibrido per runbook.

Per installare l'agente nel computer locale, seguire le istruzioni contenute in [Connettere computer Windows a Log Analytics](../log-analytics/log-analytics-windows-agent.md). È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Dopo che si è connesso a Log Analytics, l'agente viene elencato nella scheda **Origini connesse** della pagina **Impostazioni** di Log Analytics. È possibile verificare che l'agente abbia scaricato correttamente la soluzione di automazione se include una cartella **AzureAutomationFiles** in C:\Programmi\Microsoft Monitoring Agent\Agent. Per verificare la versione del ruolo di lavoro ibrido per runbook, passare a C:\Programmi\Microsoft Monitoring Agent\Agent\AzureAutomation\ e prendere nota della sottocartella \\*version*.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installare l'ambiente runbook e connettersi ad Automazione di Azure

Quando si aggiunge un agente a Log Analytics, la soluzione Automazione esegue il push del modulo **HybridRegistration** di PowerShell che contiene il cmdlet **Add-HybridRunbookWorker**. È possibile usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo in Automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Eseguire quindi il cmdlet **Add-HybridRunbookWorker** con la sintassi seguente:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

È possibile ottenere le informazioni necessarie per questo cmdlet dalla pagina **Gestisci chiavi** del portale di Azure. Aprire la pagina selezionando l'opzione **Chiavi** nella pagina **Impostazioni** dell'account di Automazione.

![Pagina "Gestisci chiavi"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** è il nome del gruppo Ruolo di lavoro ibrido per runbook. Se il gruppo esiste già nell'account di automazione, il computer corrente vi verrà aggiunto direttamente. Se questo gruppo non esiste, verrà aggiunto.
* **EndPoint** è il valore della voce **URL** nella pagina **Gestisci chiavi**.
* **Token** è la voce **CHIAVE DI ACCESSO PRIMARIA** nella pagina **Gestisci chiavi**.

Per ricevere informazioni dettagliate sull'installazione, usare l'opzione **-Verbose** con **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locali, pertanto è necessario installarli manualmente. L'unica eccezione è rappresentata dal modulo Azure, che viene installato per impostazione predefinita, garantendo l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale della funzionalità ruolo di lavoro ibrido per runbook è gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere il [relativo articolo](http://msdn.microsoft.com/library/dd878350.aspx). 

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente **PSModulePath**, in modo da venire importati automaticamente dal ruolo di lavoro ibrido. Per altre informazioni, vedere [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Modifica del percorso di installazione di PSModulePath).

## <a name="troubleshoot"></a>Risolvere problemi

Per informazioni su come risolvere i problemi del ruolo di lavoro ibrido per runbook, vedere [Troubleshooting Windows Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows) (Risoluzione dei problemi dei ruoli di lavoro ibridi per runbook di Windows)

Per altre procedure di risoluzione dei problemi con Gestione aggiornamenti, vedere [Gestione degli aggiornamenti - Risoluzione dei problemi](troubleshoot/update-management.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Rimuovere ruoli di lavoro ibridi di Automazione di Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).