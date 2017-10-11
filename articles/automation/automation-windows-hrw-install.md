---
title: Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook sui computer Windows nel data center locale o nell'ambiente cloud.
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 67cf84250ecb6d5b254ed048a5cad66405ea5997
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---

# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Come distribuire un ruolo di lavoro ibrido per runbook di Windows

I runbook in Automazione di Azure non possono accedere alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nel cloud di Azure.  La funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure consente di eseguire runbook direttamente sul computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati.  

Questa funzionalità è illustrata nell'immagine seguente:<br>

![Panoramica di Hybrid Runbook Workers](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Per una panoramica tecnica delle considerazioni sulla distribuzione e sui ruoli di lavoro ibrido per runbook, vedere [Cenni preliminari sull'architettura di automazione](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi

Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente.  Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook, è necessario specificare il gruppo in cui verrà eseguito.  I membri del gruppo determinano il ruolo di lavoro che gestirà la richiesta.  Non è possibile scegliere un computer di lavoro specifico.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installazione del ruolo di lavoro ibrido per runbook di Windows 

Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, sono disponibili due metodi.  Il metodo consigliato consiste nell'usare un runbook di Automazione per automatizzare completamente il processo necessario per configurare un computer Windows.  Il secondo metodo prevede l'esecuzione di una procedura dettagliata per installare e configurare il ruolo manualmente.  

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC.  Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).        
Se si abilita la [soluzione di gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md), qualsiasi computer Windows connesso all'area di lavoro OMS viene automaticamente configurato come ruolo di lavoro ibrido per runbook per supportare i runbook che fanno parte di questa soluzione.  Non viene però eseguita la registrazione con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione.  È possibile aggiungere il computer a un gruppo ruolo di lavoro ibrido per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo ruolo di lavoro ibrido per runbook.  Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.  

Esaminare le informazioni seguenti per quanto riguarda i [requisiti hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e le [informazioni per preparare la rete](automation-offering-get-started.md#network-planning) prima di iniziare la distribuzione di un ruolo di lavoro ibrido per runbook.  Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.  
 
### <a name="automated-deployment"></a>Distribuzione automatizzata

Seguire questa procedura per automatizzare l'installazione e la configurazione del ruolo di lavoro ibrido di Windows.  

1. Scaricare lo script *New-OnPremiseHybridWorker.ps1* da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente e copiarlo nel ruolo di lavoro.  

    Per l'esecuzione dello script *New-OnPremiseHybridWorker.ps1* sono necessari i parametri seguenti:

  * *AutomationAccountName* (obbligatorio) - nome dell'account di Automazione.  
  * *ResourceGroupName* (obbligatorio) - nome del gruppo di risorse associato all'account di Automazione.  
  * *HybridGroupName* (obbligatorio) - nome di un gruppo di ruoli di lavoro ibridi per runbook che si specifica come destinazione per i runbook che supportano questo scenario. 
  *  *SubscriptionID* (obbligatorio) - ID della sottoscrizione di Azure in cui si trova l'account di Automazione.
  *  *WorkspaceName* (facoltativo) - nome dell'area di lavoro OMS.  Se non si ha un'area di lavoro OMS, lo script ne crea e configura una.  

     > [!NOTE]
     > Le uniche aree di Automazione supportate per l'integrazione con OMS sono attualmente: **Australia sud-orientale**, **Stati Uniti orientali 2**, **Asia sud-orientale** ed **Europa occidentale**.  Se l'account di Automazione non si trova in una di queste aree, lo script crea un'area di lavoro OMS ma avvisa l'utente che non sarà possibile eseguire il collegamento.
     >
2. Nel computer in uso avviare **Windows PowerShell** dalla schermata **Start** in modalità amministratore.  
3. Dalla shell della riga di comando di PowerShell passare alla cartella che contiene lo script scaricato ed eseguirlo modificando i valori per i parametri *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*.

     > [!NOTE] 
     > Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script.  È **necessario** accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Verrà chiesto di accettare di installare **NuGet** e di eseguire l'autenticazione con le credenziali di Azure.<br><br> ![Esecuzione dello script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Dopo il completamento dello script, il pannello Gruppi di ruoli di lavoro ibridi mostrerà il nuovo gruppo e il numero di membri oppure, nel caso di un gruppo esistente, il numero di membri verrà incrementato.  È possibile selezionare il gruppo nell'elenco nel pannello **Gruppi di ruoli di lavoro ibridi** e selezionare il riquadro **Ruoli di lavoro per runbook**.  Nel pannello **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.  

### <a name="manual-deployment"></a>Distribuzione manuale 

Eseguire i primi due passaggi una volta per l'ambiente di automazione e quindi ripetere i passaggi rimanenti per ogni computer di lavoro.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Creare l'area di lavoro di Operations Management Suite

Se non si ha ancora un'area di lavoro di Operations Management Suite, crearne una seguendo le istruzioni in [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md). Se già si dispone di un'area di lavoro, è possibile usarla.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Aggiungere la soluzione di automazione all'area di lavoro di Operations Management Suite

Le soluzioni aggiungono funzionalità a Operations Management Suite.  La soluzione di automazione aggiunge funzionalità per Automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook.  Quando si aggiunge la soluzione all'area di lavoro, i componenti del ruolo di lavoro vengono automaticamente propagati al computer dell'agente che verrà installato nel passaggio successivo.

Per aggiungere la soluzione [Automazione](../log-analytics/log-analytics-add-solutions.md) all'area di lavoro di Operations Management Suite, seguire le istruzioni contenute nell'articolo relativo a **come aggiungere una soluzione tramite la raccolta soluzioni** .

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installare Microsoft Monitoring Agent

Microsoft Monitoring Agent connette i computer a Operations Management Suite.  Quando si installa l'agente nel computer locale e lo si connette all'area di lavoro, viene eseguito automaticamente il download dei componenti necessari per il ruolo di lavoro ibrido per runbook.

Per installare l'agente nel computer locale, seguire le istruzioni contenute in [Connettere computer Windows a Log Analytics](../log-analytics/log-analytics-windows-agents.md).  È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Dopo che l'agente si è connesso a Operations Management Suite, viene elencato nella scheda **Origini connesse** del riquadro **Impostazioni** di Operations Management Suite.  È possibile verificare che l'agente abbia scaricato correttamente la soluzione di automazione se include una cartella **AzureAutomationFiles** in C:\Programmi\Microsoft Monitoring Agent\Agent.  Per verificare la versione del ruolo di lavoro ibrido per runbook, passare a C:\Programmi\Microsoft Monitoring Agent\Agent\AzureAutomation\ e prendere nota della sottocartella \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installare l'ambiente runbook e connettersi ad Automazione di Azure

Quando si aggiunge un agente a Operations Management Suite, la soluzione di automazione esegue il push del modulo **HybridRegistration** di PowerShell che contiene il cmdlet **Add-HybridRunbookWorker**.  È possibile usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo in Automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Eseguire quindi il cmdlet **Add-HybridRunbookWorker** con la sintassi seguente:

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

È possibile ottenere le informazioni necessarie per questo cmdlet dal pannello **Gestisci chiavi** del portale di Azure.  Aprire il pannello selezionando l'opzione **Chiavi** nel pannello **Impostazioni** nell'account di Automazione.

![Panoramica di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** è il nome del gruppo di computer di lavoro runbook ibridi. Se il gruppo già esiste nell'account di automazione, il computer corrente vi verrà aggiunto direttamente.  Se il gruppo ancora non esiste, verrà aggiunto.
* **EndPoint** è il valore del campo **URL** del pannello **Gestisci chiavi**.
* **Token** è il valore **Chiave di accesso primaria** nel pannello **Gestisci chiavi**.  

Usare l'opzione **-Verbose** con **Add-HybridRunbookWorker** per ricevere informazioni dettagliate sull'installazione.

#### <a name="5-install-powershell-modules"></a>5. Installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure.  Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locali, pertanto è necessario installarli manualmente.  L'unica eccezione è rappresentata dal modulo Azure, che viene installato per impostazione predefinita, garantendo l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale della funzionalità ruolo di lavoro ibrido per runbook è gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse.  Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere il [relativo articolo](http://msdn.microsoft.com/library/dd878350.aspx) .  I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente PSModulePath, in modo da venire importati automaticamente dal ruolo di lavoro ibrido.  Per altre informazioni, vedere [Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx) (Modifica del percorso di installazione di PSModulePath). 

## <a name="troubleshooting"></a>Risoluzione dei problemi 

Il ruolo di lavoro ibrido per runbook dipende da Microsoft Monitoring Agent per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:  

1. Il ruolo di lavoro ibrido è protetto da proxy o firewall.  
    Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulla porta 443.  

2. Il computer in cui è in esecuzione il ruolo di lavoro ibrido non soddisfa i [requisiti hardware](automation-offering-get-started.md#hybrid-runbook-worker).  
    I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi per poter ospitare questa funzionalità. In caso contrario, a seconda dell'utilizzo di risorse di altri processi in background e dei conflitti causati dai runbook durante l'esecuzione, il computer diverrà sovraccarico e causerà ritardi o interruzioni del processo del runbook.
   Verificare che il computer designato per svolgere il ruolo di lavoro ibrido per runbook soddisfi i requisiti hardware minimi.  In caso affermativo, monitorare l'utilizzo della CPU e della memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows.  In caso di utilizzo eccessivo della CPU o memoria, potrebbe essere necessario aggiornare o aggiungere altri processori o aumentare la memoria per risolvere il collo di bottiglia della risorsa e quindi l'errore. In alternativa, selezionare una risorsa di calcolo diversa in grado di supportare i requisiti minimi e scalare quando le esigenze del carico di lavoro indicano la necessità di un aumento.
    
3. Il servizio Microsoft Monitoring Agent non è in esecuzione.  
    Se il servizio Microsoft Monitoring Agent di Windows non è in esecuzione, il ruolo di lavoro ibrido per runbook non è in grado di comunicare con Automazione di Azure.  Verificare l'esecuzione dell'agente immettendo il comando seguente in PowerShell: `get-service healthservice`.  Se il servizio viene arrestato, immettere il comando seguente in PowerShell per avviare il servizio: `start-service healthservice`.  

4. Nel log eventi **Log applicazioni e servizi\Operazioni**, viene visualizzato l'evento 4502 ed EventMessage contenente **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** con la seguente descrizione: *il certificato presentato dal servizio <wsid>.oms.opinsights.azure.com non è stato rilasciato da un'autorità di certificazione usata per i servizi Microsoft. Contattare l'amministratore di rete per verificare che il proxy che intercetta la comunicazione TLS/SSL sia in esecuzione. L'articolo KB3126513 contiene altre informazioni sulla risoluzione dei problemi di connettività.*
    Questo può essere dovuto dal proxy o dal firewall di rete che blocca la comunicazione a Microsoft Azure.  Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulle porte 443.

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  È possibile verificare se sono presenti eventuali eventi di avviso o errore scritti nel log eventi **Log applicazioni e servizi\Microsoft-SMA\Operazioni** e **Log applicazioni e servizi\Gestore operazioni** che indicano un problema di connettività o altro problema che interessa il caricamento del ruolo nell'Automazione di Azure durante l'esecuzione di operazioni normali.  

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Remove Azure Automation Hybrid Runbook Workers](automation-remove-hrw.md) (Rimuovere i ruoli di lavoro ibridi per runbook di Automazione di Azure)
