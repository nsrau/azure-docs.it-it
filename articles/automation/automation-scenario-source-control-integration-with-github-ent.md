---
title: Integrazione del controllo del codice sorgente di Automazione di Azure con GitHub Enterprise | Documentazione Microsoft
description: Descrive nei dettagli come configurare l'integrazione con GitHub Enterprise per il controllo del codice sorgente dei runbook di Automazione.
services: automation
documentationCenter: 
authors: eslesar
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: 2b3ca90382a1c4fb6446799ad539ab8ee603aefc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Scenario di Automazione di Azure - Integrazione del controllo del codice sorgente di Automazione con GitHub Enterprise

Automazione supporta attualmente l'integrazione del controllo del codice sorgente che consente di associare i runbook dell'account di Automazione a un repository di controllo del codice sorgente su GitHub.  Tuttavia, i clienti che hanno distribuito [GitHub Enterprise](https://enterprise.github.com/home) per supportare le procedure DevOps vogliono anche usarlo per gestire il ciclo di vita dei runbook sviluppati per automatizzare i processi aziendali e le operazioni di gestione dei servizi.  

In questo scenario è necessario un computer Windows nel data center configurato come un ruolo di lavoro ibrido per runbook con i moduli di Azure Resource Manager e gli strumenti Git installati.  Il computer con ruolo di lavoro ibrido avrà un clone del repository Git locale.  Quando il runbook viene eseguito nel ruolo di lavoro ibrido, la directory Git viene sincronizzata e il contenuto del file runbook viene importato nell'account di Automazione.

Questo articolo descrive come eseguire questa configurazione nell'ambiente di Automazione di Azure. Si inizierà configurando Automazione con le credenziali di sicurezza, i runbook necessari per supportare questo scenario e la distribuzione di un ruolo di lavoro ibrido per runbook nel data center per eseguire i runbook e accedere al repository GitHub Enterprise al fine di sincronizzare i runbook con l'account di Automazione.  


## <a name="getting-the-scenario"></a>Come ottenere lo scenario

Questo scenario è costituito da due runbook di PowerShell che è possibile importare direttamente dalla [raccolta di runbook](automation-runbook-gallery.md) nel portale di Azure o scaricare da [PowerShell Gallery](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbook

Runbook | Descrizione| 
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook esporta un certificato RunAs da un account di Automazione in un ruolo di lavoro ibrido, in modo che i runbook nel ruolo di lavoro possano eseguire l'autenticazione con Azure per importare i runbook nell'account di Automazione.| 
Sync-LocalGitFolderToAutomationAccount | Runbook sincronizza la cartella Git locale nel computer ibrido e quindi importa i file runbook (con estensione ps1) nell'account di Automazione.|

### <a name="credentials"></a>Credenziali

Credenziali | Descrizione|
-----------|------------|
GitHRWCredential | Asset credenziali che viene creato per contenere il nome utente e la password per un utente dotato delle autorizzazioni per il ruolo di lavoro ibrido.|

## <a name="installing-and-configuring-this-scenario"></a>Installazione e configurazione dello scenario

### <a name="prerequisites"></a>Prerequisiti

1. Il runbook Sync-LocalGitFolderToAutomationAccount esegue l'autenticazione usando l'[account RunAs di Azure](automation-sec-configure-azure-runas-account.md). 

2. È inoltre necessaria un'area di lavoro di Microsoft Operations Management Suite (OMS) in cui sia abilitata e configurata la soluzione Automazione di Azure.  Se non si ha un'area di lavoro associata all'account di Automazione usato per installare e configurare questo scenario, viene creata e configurata automaticamente quando si esegue lo script **New-OnPremiseHybridWorker.ps1** dal ruolo di lavoro ibrido per runbook.        

    > [!NOTE]
    > Attualmente, l'integrazione di Automazione con OMS è supportata solo nelle aree seguenti: **Australia sud-orientale**, **Stati Uniti orientali 2**, **Asia sud-orientale** ed **Europa occidentale**. 

3. Un computer che può essere usato come ruolo di lavoro ibrido per runbook dedicato che ospiterà anche il software GitHub e manterrà i file runbook (*runbook*.ps1) in una directory di origine nel file system per la sincronizzazione tra GitHub e l'account di Automazione.

### <a name="import-and-publish-the-runbooks"></a>Importare e pubblicare i runbook

Per importare i runbook *Export-RunAsCertificateToHybridWorker* e *Sync-LocalGitFolderToAutomationAccount* dalla raccolta di runbook dell'account di Automazione nel portale di Azure, seguire le procedure descritte in [Importare un runbook dalla raccolta di runbook](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Pubblicare i runbook dopo che sono stati importati correttamente nell'account di Automazione.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Distribuire e configurare un ruolo di lavoro ibrido per runbook

Se un ruolo di lavoro ibrido per runbook non è già stato distribuito nel data center, è necessario esaminare i requisiti e seguire i passaggi di installazione automatica utilizzando la procedura descritta in [Funzionalità Hybrid Runbook Workers di Automazione di Azure](automation-hybrid-runbook-worker.md#automated-deployment).  Dopo aver installato il ruolo di lavoro ibrido nel computer, eseguire i passaggi seguenti per completare la configurazione in modo da supportare questo scenario.

1. Accedere al computer che ospita il ruolo di lavoro ibrido per runbook con un account dotato di diritti amministrativi locali e creare una directory in cui inserire i file runbook di Git.  Clonare il repository Git interno nella directory.
2. Se non si dispone di un account RunAs o si vuole crearne uno nuovo dedicato a questo scopo, dal portale di Azure passare agli account di Automazione, selezionare l'account di Automazione e creare un [asset credenziali](automation-credentials.md) contenente il nome utente e la password per un utente dotato delle autorizzazioni per il ruolo di lavoro ibrido.  
3. Dall'account di Automazione [modificare il runbook](automation-edit-textual-runbook.md) **Export-RunAsCertificateToHybridWorker** e cambiare il valore della variabile *$Password* con una password complessa.  Dopo aver modificato il valore, fare clic su **Pubblica** per pubblicare la versione bozza del runbook. 
5. Avviare il runbook **Export-RunAsCertificateToHybridWorker** e, nel pannello **Avvia runbook**, in **Impostazioni di esecuzione**, selezionare l'opzione **Ruolo di lavoro ibrido** quindi, nell'elenco a discesa, selezionare il gruppo ruolo di lavoro ibrido creato in precedenza per questo scenario.  

    Viene così esportato un certificato nel ruolo di lavoro ibrido, in modo che i runbook del ruolo di lavoro possano eseguire l'autenticazione con Azure usando la connessione RunAs per gestire le risorse di Azure (in particolare, per questo scenario, importare i runbook nell'account di Automazione).

4. Dall'account di Automazione selezionare il gruppo ruolo di lavoro ibrido creato in precedenza, [specificare un account RunAs](automation-hrw-run-runbooks.md#runas-account) per il gruppo e scegliere l'asset credenziali creato.  Questo assicura che il runbook Sync possa eseguire i comandi Git. 
5. Avviare il runbook **Sync-LocalGitFolderToAutomationAccount**, fornire i seguenti valori richiesti per i parametri di input e, nel pannello **Avvia runbook**, in **Impostazioni di esecuzione**, selezionare l'opzione **Ruolo di lavoro ibrido** quindi, nell'elenco a discesa, selezionare il gruppo ruolo di lavoro ibrido creato in precedenza per questo scenario:
    * *ResourceGroupName*: nome del gruppo di risorse associato all'account di Automazione
    * *AutomationAccountName* : nome dell'account di Automazione.
    * *GitPath*: cartella o file locale del ruolo di lavoro ibrido per runbook configurato per ricevere le ultime modifiche da Git

    In questo modo la cartella Git locale nel computer con ruolo di lavoro ibrido verrà sincronizzata e quindi i file con estensione .ps1 saranno importati dalla directory di origine all'account di Automazione.

    ![Avviare Sync-LocalGitFolderToAutomationAccount Runbook](media/automation-scenario-source-control-integration-with-github-ent/start-runbook-synclocalgitfoldertoautoacct.png)<br>

7. Visualizzare i dettagli di riepilogo dei processi per il runbook nel pannello **Runbook** nel proprio account di Automazione e, successivamente, selezionare il riquadro **Processi**.  Verificare che la procedura è stata completata con successo selezionando il riquadro **Tutti i log** ed esaminando il flusso di registrazione dettagliato.  

## <a name="next-steps"></a>Passaggi successivi

-  Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
-  Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
