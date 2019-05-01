---
title: Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94912d5aa10ddd2e67c33bcbb416f007c85f105c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574117"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrazione del controllo del codice sorgente in Automazione di Azure

Controllo del codice sorgente consente di mantenere i runbook in automazione di account vengono aggiornati con gli script nel repository del controllo codice sorgente GitHub o archivi di Azure. Il controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti dei runbook. Il controllo del codice sorgente, ad esempio, consente di sincronizzare diversi rami nel controllo del codice sorgente per gli account di automazione di sviluppo, test o produzione. Questo rende facile alzare di livello il codice che è stato testato nell'ambiente di sviluppo a produzione di account di automazione. Integrazione del controllo codice sorgente con automazione supporta la sincronizzazione per direzione singola da repository di controllo del codice sorgente.

Automazione di Azure supporta tre tipi di controllo del codice sorgente:

* GitHub
* Repository di Azure (Git)
* Repository di Azure (TFVC)

## <a name="pre-requisites"></a>Prerequisiti

* Un repository del controllo codice sorgente (GitHub o archivi di Azure)
* Oggetto [Account runas](manage-runas-account.md)
* Assicurarsi di aver la [moduli Azure più recenti](automation-update-azure-modules.md) nell'Account di automazione

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti nell'account di automazione dell'utente e vengono fatturati alla stessa tariffa degli altri processi di automazione.

## <a name="configure-source-control---azure-portal"></a>Configurare controllo del codice sorgente - portale di Azure

All'interno dell'Account di automazione, selezionare **controllo del codice sorgente** e fare clic su **+ Aggiungi**

![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

Scegliere **Tipo di controllo del codice sorgente** e fare clic su **Autentica**. Una finestra del browser si apre e viene richiesto di accedere, seguire le istruzioni per completare l'autenticazione.

Inserire le informazioni nella pagina **Riepilogo del Controllo del codice sorgente** e fare clic su **Salva**. La tabella seguente fornisce una descrizione dei campi disponibili.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Nome del controllo del codice sorgente     | Un nome descrittivo per il controllo del codice sorgente. *Questo nome deve contenere solo lettere e numeri.*        |
|Tipo di controllo del codice sorgente     | Specifica del tipo di controllo del codice sorgente. Le opzioni disponibili sono:</br> GitHub</br>Repository di Azure (Git)</br> Repository di Azure (TFVC)        |
|Repository     | Nome del repository o del progetto. Vengono restituiti i primi 200 repository. Per cercare un repository, digitare il nome del campo e fare clic su **ricerca su GitHub**.|
|Ramo     | Ramo da cui eseguire il pull dei file di origine. Destinazione branch non è disponibile per il tipo di controllo sorgente TFVC.          |
|Percorso della cartella     | Cartella che contiene i runbook da sincronizzare. Esempio: /Runbooks </br>*Vengono sincronizzati solo i runbook nella cartella specificata. Non è supportata la ricorsione.*        |
|Auto Sync<sup>1</sup>     | Attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente         |
|Pubblica runbook     | Se impostato su **su**, dopo i runbook vengono sincronizzati dal controllo del codice sorgente verrà vengano pubblicati automaticamente.         |
|DESCRIZIONE     | Campo di testo in cui fornire altri dettagli        |

<sup>1</sup> per abilitare la sincronizzazione automatica durante la configurazione di integrazione del controllo codice sorgente con i repository di Azure, è necessario essere un amministratore del progetto.

![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> L'account di accesso per il repository di controllo del codice sorgente può differire l'account di accesso al portale di Azure. Assicurarsi di che essere connessi con l'account corretto per il repository di controllo del codice sorgente durante la configurazione di controllo del codice sorgente. Se c'è qualche dubbio, aprire una nuova scheda nel browser e disconnettersi da visualstudio.com o github.com e provare di nuovo a connettersi al controllo del codice sorgente.

## <a name="configure-source-control---powershell"></a>Configurare controllo del codice sorgente - PowerShell

È anche possibile usare PowerShell per configurare controllo del codice sorgente in automazione di Azure. Per configurare controllo del codice sorgente con i cmdlet di PowerShell, è necessario un token di accesso personale (PAT). Si utilizza il [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) per creare la connessione di controllo di origine. Il cmdlet richiede una stringa sicura del Token di accesso personale, per imparare a creare una stringa sicura, vedere [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Repository di Azure (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Repository di Azure (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Autorizzazioni dei token di accesso personale

Controllo del codice sorgente richiede alcune autorizzazioni minime per i token di accesso personale. Le tabelle seguenti contengono le autorizzazioni minime necessarie per GitHub e repository di Azure.

#### <a name="github"></a>GitHub

Per altre informazioni sulla creazione di un token di accesso personale in GitHub, visitare [creazione di un token di accesso personale per la riga di comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|`Scope`  |DESCRIZIONE  |
|---------|---------|
|**repo**     |         |
|repo:status     | Accedere allo stato del commit         |
|repo_deployment      | Accedere allo stato della distribuzione         |
|public_repo     | Accedere ai repository pubblici         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Scrivere gli hook del repository         |
|read:repo_hook|Leggere gli hook del repository|

#### <a name="azure-repos"></a>Azure Repos

Per altre informazioni sulla creazione di un token di accesso personale in archivi di Azure, visitare [autenticare l'accesso con token di accesso personali](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|`Scope`  |
|---------|
|Codice (lettura)     |
|Progetto e team (lettura)|
|Identità (lettura)      |
|Profilo utente (lettura)     |
|Elementi di lavoro (lettura)    |
|Connessioni al servizio (lettura, query e gestione)<sup>1</sup>    |

<sup>1</sup> l'autorizzazione di connessioni del servizio è solo necessario se è abilitato con sincraut.

## <a name="syncing"></a>Sincronizzazione

Selezionare l'origine della tabella nel **controllo del codice sorgente** pagina. Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione.

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

Registrazione aggiuntiva è disponibile selezionando **tutti i registri** nel **riepilogo processo di sincronizzazione del controllo origine** pagina. Queste voci di log aggiuntivi possono agevolare la risoluzione dei problemi che possono verificarsi quando si utilizza controllo del codice sorgente.

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi da un repository di controllo del codice sorgente, aprire **controllo del codice sorgente** sotto **impostazioni Account** nell'Account di automazione.

Selezionare il controllo del codice sorgente da rimuovere. Nella pagina **Riepilogo del Controllo del codice sorgente** fare clic su **Elimina**.

## <a name="encoding"></a>Codifica

Se più persone stanno modificando i runbook nel repository di controllo del codice sorgente con diversi editor, è probabile incontrare i problemi di codifica. Questa situazione può causare caratteri non validi nel runbook. Per altre informazioni, vedere [cause comuni di problemi di codifica](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
