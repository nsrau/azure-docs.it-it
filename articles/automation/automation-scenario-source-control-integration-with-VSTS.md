---
title: Integrazione di Automazione di Azure con controllo del codice sorgente di Visual Studio Team Services | Microsoft Docs
description: Questo scenario illustra l'impostazione dell'integrazione con un account di Automazione di Azure e controllo del codice sorgente di Visual Studio Team Services.
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: Azure PowerShell, VSTS, controllo del codice sorgente, automazione
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Scenario di Automazione di Azure - Integrazione del controllo del codice sorgente con Visual Studio Team Services

In questo scenario, è presente un progetto di Visual Studio Team Services che si usa per gestire i runbook di Automazione di Azure o le configurazioni DSC nel controllo del codice sorgente.
Questo articolo descrive come integrare VSTS con l'ambiente di Automazione di Azure in modo che venga eseguita l'integrazione continuata per ogni archiviazione.

## <a name="getting-the-scenario"></a>Come ottenere lo scenario

Questo scenario è costituito da due runbook di PowerShell che è possibile importare direttamente dalla [raccolta di runbook](automation-runbook-gallery.md) nel portale di Azure o scaricare da [PowerShell Gallery](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbook

Runbook | Descrizione| 
--------|------------|
Sincronizzazione VSTS | Importare runbook o configurazioni dal controllo del codice sorgente VSTS al termine di un'archiviazione. Se eseguito manualmente, importerà e pubblicherà tutti i runbook o le configurazioni nell'account di automazione.| 
Sincronizzazione VSTSGit | Importare runbook o configurazioni da VSTS nel controllo del codice sorgente Git al termine di un'archiviazione. Se eseguito manualmente, importerà e pubblicherà tutti i runbook o le configurazioni nell'account di automazione.|

### <a name="variables"></a>Variabili

Variabile | Descrizione|
-----------|------------|
VSToken | Protegge l'asset della variabile creato che contiene il token di accesso personale VSTS. È possibile imparare a creare un token di accesso personale VSTS nella [pagina di autenticazione VSTS](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview). 
## <a name="installing-and-configuring-this-scenario"></a>Installazione e configurazione dello scenario

Creare un [token di accesso personale](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview) in VSTS, che verrà usato per sincronizzare i runbook o le configurazioni nell'account di automazione.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Creare una [variabile protetta](automation-variables.md) nell'account di automazione per contenere il token di accesso personale in modo che il runbook possa eseguire l'autenticazione in VSTS e sincronizzare i runbook o le configurazioni nell'account di automazione. È possibile denominare questo token VSToken. 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importare il runbook che sincronizzerà i runbook o le configurazioni nell'account di automazione. È possibile usare il [runbook di esempio VSTS](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) oppure il [runbook di esempio VSTS con Git] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript) da PowerShellGallery.com a seconda se si usa il controllo del codice sorgente VSTS o VSTS con Git ed eseguire la distribuzione al proprio account di automazione.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

È ora possibile [pubblicare](automation-creating-importing-runbook.md#publishing-a-runbook) questo runbook in modo da creare un webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Creare un [webhook](automation-webhooks.md) per questo runbook di sincronizzazione VSTS e inserire i parametri come illustrato di seguito. Assicurarsi di copiare l'url del webhook, poiché sarà necessario per un hook del servizio in VSTS. VSAccessTokenVariableName è il nome (VSToken) della variabile protetta creata in precedenza per contenere il token di accesso personale. 

L'integrazione con VSTS (Sync-VSTS.ps1) richiederà i parametri seguenti.
### <a name="sync-vsts-parameters"></a>Parametri di sincronizzazione VSTS

. | Descrizione| 
--------|------------|
WebhookData | Questo file conterrà le informazioni di archiviazione inviate dall'hook del servizio VSTS. È necessario lasciare vuoto questo parametro.| 
ResourceGroup | Nome del gruppo di risorse in cui si trova l'account di automazione.|
AutomationAccountName | Nome dell'account di automazione che verrà sincronizzato con VSTS.|
VSFolder | Nome della cartella in VSTS, in cui sono presenti i runbook e le configurazioni.|
VSAccount | Nome dell'account di Visual Studio Team Services.| 
VSAccessTokenVariableName | Nome della variabile protetta (VSToken) che contiene il token di accesso personale VSTS.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Se si usa VSTS con GIT (Sync-VSTSGit.ps1), saranno necessari i parametri seguenti.

. | Descrizione|
--------|------------|
WebhookData | Questo file conterrà le informazioni di archiviazione inviate dall'hook del servizio VSTS. È necessario lasciare vuoto questo parametro.| ResourceGroup | Nome del gruppo di risorse in cui si trova l'account di automazione.|
AutomationAccountName | Nome dell'account di automazione che verrà sincronizzato con VSTS.|
VSAccount | Nome dell'account di Visual Studio Team Services.|
VSProject | Nome del progetto in VSTS, in cui sono presenti i runbook e le configurazioni.|
GitRepo | Nome del repository Git.|
GitBranch | Nome del branch nel repository Git VSTS.|
Cartella | Nome della cartella nel branch Git VSTS.|
VSAccessTokenVariableName | Nome della variabile protetta (VSToken) che contiene il token di accesso personale VSTS.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Creare un hook del servizio in VSTS per le archiviazioni alla cartella che attiva il webhook all'archiviazione del codice. Selezionare Web hook come servizio con cui eseguire l'integrazione quando si crea una nuova sottoscrizione. È possibile acquisire familiarità con gli hook del servizio nella [documentazione relativa agli hook del servizio VSTS](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

A questo punto si sarà in grado di eseguire tutte le archiviazioni dei runbook e delle configurazioni in VSTS e di sincronizzarle automaticamente nell'account di automazione.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Se si esegue manualmente questo runbook senza che sia attivato da VSTS, è possibile lasciare vuoto il parametro webhookdata in modo che sia eseguita una sincronizzazione completa dalla cartella VSTS specificata.

Se si desidera disinstallare lo scenario, rimuovere l'hook del servizio da VSTS, eliminare il runbook e la variabile VSToken.
