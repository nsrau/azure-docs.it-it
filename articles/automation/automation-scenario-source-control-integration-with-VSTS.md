---
title: Integrazione di Automazione di Azure con controllo del codice sorgente di Azure DevOps Services
description: Questo scenario illustra l'impostazione dell'integrazione con un account di Automazione di Azure e controllo del codice sorgente di Azure DevOps Services.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: Azure PowerShell, Azure DevOps Services, controllo del codice sorgente, automazione
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801438"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Scenario di Automazione di Azure - Integrazione del controllo del codice sorgente di Automazione con Azure DevOps

> [!NOTE]
> È disponibile una nuova esperienza per il controllo del codice sorgente. Per altre informazioni sulla nuova esperienza, vedere [Controllo del codice sorgente (anteprima)](source-control-integration.md).

In questo scenario è presente un progetto di Azure DevOps che si usa per gestire i runbook di Automazione di Azure o le configurazioni DSC nel controllo del codice sorgente.

Questo articolo descrive come integrare Azure DevOps con l'ambiente di Automazione di Azure in modo che venga eseguita l'integrazione continua per ogni archiviazione.

## <a name="getting-the-scenario"></a>Come ottenere lo scenario

Questo scenario è costituito da due runbook di PowerShell che è possibile importare direttamente dalla [raccolta di runbook](automation-runbook-gallery.md) nel portale di Azure o scaricare da [PowerShell Gallery](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbook

Runbook | DESCRIZIONE|
--------|------------|
Sincronizzazione VSTS | Importare runbook o configurazioni dal controllo del codice sorgente di Azure DevOps al termine di un'archiviazione. Se eseguito manualmente, importerà e pubblicherà tutti i runbook o le configurazioni nell'account di automazione.| 
Sincronizzazione VSTSGit | Importare runbook o configurazioni da Azure DevOps nel controllo del codice sorgente Git al termine di un'archiviazione. Se eseguito manualmente, importerà e pubblicherà tutti i runbook o le configurazioni nell'account di automazione.|

### <a name="variables"></a>variables

Variabile | DESCRIZIONE|
-----------|------------|
VSToken | Protegge l'asset della variabile creato che contiene il token di accesso personale di Azure DevOps. È possibile imparare a creare un token di accesso personale di Azure DevOps nella [pagina di autenticazione di Azure DevOps](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="installing-and-configuring-this-scenario"></a>Installazione e configurazione dello scenario

Creare un [token di accesso personale](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) in Azure DevOps, che verrà usato per sincronizzare i runbook o le configurazioni nell'account di automazione.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Creare una [variabile protetta](automation-variables.md) nell'account di automazione per contenere il token di accesso personale in modo che il runbook possa eseguire l'autenticazione in Azure DevOps e sincronizzare i runbook o le configurazioni nell'account di Automazione. È possibile denominare questo token VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importare il runbook che sincronizzerà i runbook o le configurazioni nell'account di automazione. È possibile usare il [runbook di esempio di Azure DevOps](https://www.powershellgallery.com/packages/Sync-VSTS) oppure il [runbook di esempio di Azure DevOps con Git](https://www.powershellgallery.com/packages/Sync-VSTSGit) da [PowerShell Gallery](https://www.powershellgallery.com), a seconda che si usi il controllo del codice sorgente di Azure DevOps o di Azure DevOps con Git, ed eseguire la distribuzione al proprio account di automazione.

![PowerShell Gallery](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

È ora possibile [pubblicare](automation-creating-importing-runbook.md#publishing-a-runbook) questo runbook in modo da creare un webhook.

![Runbook Pulish](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Creare un [webhook](automation-webhooks.md) per questo runbook di sincronizzazione VSTS e inserire i parametri come illustrato di seguito. Assicurarsi di copiare l'URL del webhook perché sarà necessario per un hook del servizio in Azure DevOps. VSAccessTokenVariableName è il nome (VSToken) della variabile protetta creata in precedenza per contenere il token di accesso personale.

L'integrazione con Azure DevOps (Sync-VSTS.ps1) richiederà i parametri seguenti:

### <a name="sync-vsts-parameters"></a>Parametri di sincronizzazione VSTS

Parametro | DESCRIZIONE|
--------|------------|
WebhookData | Questo file contiene le informazioni di archiviazione inviate dall'hook del servizio Azure DevOps. È necessario lasciare vuoto questo parametro.| 
ResourceGroup | Nome del gruppo di risorse in cui si trova l'account di automazione.|
AutomationAccountName | Nome dell'account di automazione che verrà sincronizzato con Azure DevOps.|
VSFolder | Nome della cartella in Azure DevOps, in cui sono presenti i runbook e le configurazioni.|
VSAccount | Nome dell'organizzazione di Azure DevOps.|
VSAccessTokenVariableName | Nome della variabile protetta (VSToken) che contiene il token di accesso personale di Azure DevOps.|

![webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Se si usa Azure DevOps con GIT (Sync-VSTSGit.ps1), saranno necessari i parametri seguenti.

Parametro | DESCRIZIONE|
--------|------------|
WebhookData | Questo file conterrà le informazioni di archiviazione inviate dall'hook del servizio Azure DevOps. È necessario lasciare vuoto questo parametro.|
ResourceGroup | Nome del gruppo di risorse in cui si trova l'account di automazione.|
AutomationAccountName | Nome dell'account di automazione che verrà sincronizzato con Azure DevOps.|
VSAccount | Nome dell'organizzazione di Azure DevOps.|
VSProject | Nome del progetto in Azure DevOps, in cui sono presenti i runbook e le configurazioni.|
GitRepo | Nome del repository Git.|
GitBranch | Nome del ramo nel repository Git di Azure DevOps.|
Cartella | Nome della cartella nel ramo Git di Azure DevOps.|
VSAccessTokenVariableName | Nome della variabile protetta (VSToken) che contiene il token di accesso personale di Azure DevOps.|

![Webhook GIT](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Creare un hook del servizio in Azure DevOps per le archiviazioni alla cartella che attiva il webhook all'archiviazione del codice. Selezionare **Web hook** come servizio con cui eseguire l'integrazione quando si crea una nuova sottoscrizione. È possibile acquisire familiarità con gli hook del servizio nella [documentazione relativa agli hook del servizio Azure DevOps](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started).
![Host servizio](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

A questo punto sarà possibile eseguire tutte le archiviazioni dei runbook e delle configurazioni in Azure DevOps e sincronizzarle automaticamente nell'account di automazione.

![Sincronizzare l'output del runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Se si esegue manualmente questo runbook senza che sia attivato da Azure DevOps, è possibile lasciare vuoto il parametro webhookdata in modo che venga eseguita una sincronizzazione completa dalla cartella di Azure DevOps specificata.

Per disinstallare lo scenario, rimuovere l'hook del servizio da Azure DevOps, eliminare il runbook e la variabile VSToken.
