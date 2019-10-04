---
title: Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 52fcd0d928ecbce5c617ff6a27175fccb8fd96f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990249"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrazione del controllo del codice sorgente in Automazione di Azure

Il controllo del codice sorgente consente di tenere aggiornati gli manuali operativi nell'account di automazione con gli script in GitHub o Azure Repos repository del controllo del codice sorgente. Il controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti dei runbook. Il controllo del codice sorgente, ad esempio, consente di sincronizzare diversi rami nel controllo del codice sorgente per gli account di automazione di sviluppo, test o produzione. Questo rende facile alzare di livello il codice che è stato testato nell'ambiente di sviluppo a produzione di account di automazione. L'integrazione del controllo del codice sorgente con automazione supporta la sincronizzazione a direzione singola dal repository del controllo del codice sorgente.

Automazione di Azure supporta tre tipi di controllo del codice sorgente:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Prerequisiti

* Un repository del controllo del codice sorgente (GitHub o Azure Repos)
* Un [account RunAs](manage-runas-account.md)
* Assicurarsi di avere i [moduli di Azure più recenti](automation-update-azure-modules.md) nell'account di automazione

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti nell'account di automazione dell'utente e vengono fatturati alla stessa tariffa degli altri processi di automazione.

## <a name="configure-source-control---azure-portal"></a>Configurare il controllo del codice sorgente-portale di Azure

Nell'account di automazione selezionare **controllo del codice sorgente** e fare clic su **+ Aggiungi**

![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

Scegliere **Tipo di controllo del codice sorgente** e fare clic su **Autentica**. Viene visualizzata una finestra del browser in cui viene chiesto di eseguire l'accesso, seguire le istruzioni per completare l'autenticazione.

Inserire le informazioni nella pagina **Riepilogo del Controllo del codice sorgente** e fare clic su **Salva**. La tabella seguente fornisce una descrizione dei campi disponibili.

|Proprietà  |Descrizione  |
|---------|---------|
|Nome del controllo del codice sorgente     | Nome descrittivo per il controllo del codice sorgente. *Questo nome deve contenere solo lettere e numeri.*        |
|Tipo di controllo del codice sorgente     | Specifica del tipo di controllo del codice sorgente. Le opzioni disponibili sono:</br> GitHub</br>Azure Repos (git)</br> Azure Repos (TFVC)        |
|Repository     | Nome del repository o del progetto. Vengono restituiti i primi 200 repository. Per cercare un repository, digitare il nome nel campo e fare clic **su Cerca in GitHub**.|
|Branch     | Ramo da cui eseguire il pull dei file di origine. La destinazione del ramo non è disponibile per il tipo di controllo del codice sorgente TFVC.          |
|Percorso cartella     | Cartella che contiene i runbook da sincronizzare. Esempio: /Runbooks </br>*Vengono sincronizzati solo manuali operativi nella cartella specificata. La ricorsione non è supportata.*        |
|Sincronizzazione automatica<sup>1</sup>     | Attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente         |
|Pubblica runbook     | Se impostato **su on**, dopo la sincronizzazione di manuali operativi dal controllo del codice sorgente verranno pubblicati automaticamente.         |
|DESCRIZIONE     | Campo di testo in cui fornire altri dettagli        |

<sup>1</sup> per abilitare la sincronizzazione automatica quando si configura l'integrazione del controllo del codice sorgente con Azure Repos, è necessario essere un amministratore di progetto.

![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> L'account di accesso per il repository del controllo del codice sorgente può essere diverso dall'account di accesso per il portale di Azure. Quando si configura il controllo del codice sorgente, verificare di avere eseguito l'accesso con l'account corretto per il repository del controllo del codice sorgente. Se c'è qualche dubbio, aprire una nuova scheda nel browser e disconnettersi da visualstudio.com o github.com e provare di nuovo a connettersi al controllo del codice sorgente.

## <a name="configure-source-control---powershell"></a>Configurare il controllo del codice sorgente-PowerShell

È anche possibile usare PowerShell per configurare il controllo del codice sorgente in automazione di Azure. Per configurare il controllo del codice sorgente con i cmdlet di PowerShell, è necessario un token di accesso personale (PAT). Usare [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) per creare la connessione al controllo del codice sorgente. Il cmdlet richiede una stringa sicura del token di accesso personale. per informazioni su come creare una stringa sicura, vedere [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Autorizzazioni dei token di accesso personale

Controllo del codice sorgente richiede alcune autorizzazioni minime per i token di accesso personale. Le tabelle seguenti contengono le autorizzazioni minime necessarie per GitHub e Azure Repos.

#### <a name="github"></a>GitHub

Per altre informazioni sulla creazione di un token di accesso personale in GitHub, vedere [creazione di un token di accesso personale per la riga di comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Ambito  |DESCRIZIONE  |
|---------|---------|
|**repo**     |         |
|repo:status     | Accedere allo stato del commit         |
|repo_deployment      | Accedere allo stato della distribuzione         |
|public_repo     | Accedere ai repository pubblici         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Scrivere gli hook del repository         |
|read:repo_hook|Leggere gli hook del repository|

#### <a name="azure-repos"></a>Azure Repos

Per altre informazioni sulla creazione di un token di accesso personale in Azure Repos, vedere [autenticare l'accesso con i token di accesso personali](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Ambito  |
|---------|
|Codice (lettura)     |
|Progetto e team (lettura)|
|Identità (lettura)      |
|Profilo utente (lettura)     |
|Elementi di lavoro (lettura)    |
|Connessioni al servizio (lettura, query e gestione)<sup>1</sup>    |

<sup>1</sup> l'autorizzazione connessioni al servizio è necessaria solo se è stata abilitata la sincronizzazione automatica.

## <a name="syncing"></a>Sincronizzazione

Consente di selezionare l'origine della tabella nella pagina **controllo del codice sorgente** . Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione.

È possibile visualizzare lo stato del processo di sincronizzazione corrente o di quelli precedenti facendo clic sulla scheda **Processi di sincronizzazione**. Nell'elenco a discesa **Controllo del codice sorgente** selezionare un controllo del codice sorgente.

![Stato sincronizzazione](./media/source-control-integration/sync-status.png)

Facendo clic su un processo è possibile visualizzarne l'output. Di seguito è riportato un esempio di output di un processo di sincronizzazione di un controllo del codice sorgente.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

Per la registrazione aggiuntiva è possibile selezionare **tutti i log** nella pagina Riepilogo del processo di sincronizzazione del **controllo del codice sorgente** . Queste voci di log aggiuntive possono aiutare a risolvere i problemi che possono verificarsi quando si usa il controllo del codice sorgente.

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi da un repository del controllo del codice sorgente, aprire il **controllo del codice sorgente** in **Impostazioni account** nell'account di automazione.

Selezionare il controllo del codice sorgente da rimuovere. Nella pagina **Riepilogo del Controllo del codice sorgente** fare clic su **Elimina**.

## <a name="encoding"></a>Codifica

Se più persone modificano manuali operativi nel repository del controllo del codice sorgente con editor diversi, è possibile che si verifichino problemi di codifica. Questa situazione può causare caratteri non corretti nella Runbook. Per ulteriori informazioni, vedere [cause comuni dei problemi di codifica](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>Aggiornamento del token di accesso

Attualmente, non è possibile aggiornare il token di accesso nel controllo del codice sorgente dal portale. Dopo che il token di accesso personale è scaduto o revocato, è possibile aggiornare il controllo del codice sorgente con un nuovo token di accesso nei modi seguenti:

* Tramite l' [API REST](https://docs.microsoft.com/en-us/rest/api/automation/sourcecontrol/update).
* Tramite il cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
