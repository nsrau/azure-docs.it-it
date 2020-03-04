---
title: Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eef67ca8111983adb4d9994894ba215240daee6f
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253738"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrazione del controllo del codice sorgente in Automazione di Azure

 L'integrazione del controllo del codice sorgente in automazione di Azure supporta la sincronizzazione a direzione singola dal repository del controllo del codice sorgente. Il controllo del codice sorgente consente di tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository di controllo del codice sorgente di GitHub o Azure Repos. Questa funzionalità semplifica la promozione del codice testato nell'ambiente di sviluppo per l'account di automazione di produzione.
 
 Grazie all'integrazione del controllo del codice sorgente, è possibile collaborare facilmente con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti di manuali operativi. Il controllo del codice sorgente, ad esempio, consente di sincronizzare diversi rami nel controllo del codice sorgente con gli account di automazione di sviluppo, test e produzione. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Tipi di controllo del codice sorgente

Automazione di Azure supporta tre tipi di controllo del codice sorgente:

* GitHub
* Azure Repos (git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Prerequisiti

* Un repository del controllo del codice sorgente (GitHub o Azure Repos)
* Un [account RunAs](manage-runas-account.md)
* I [moduli di Azure più recenti](automation-update-azure-modules.md) nell'account di automazione, incluso il modulo **AZ. Accounts** (AZ Module equivalente di AzureRM. profile)

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti con l'account di automazione dell'utente e vengono fatturati alla stessa tariffa di altri processi di automazione.

## <a name="configuring-source-control"></a>Configurazione del controllo del codice sorgente

Questa sezione descrive come configurare il controllo del codice sorgente per l'account di automazione. È possibile usare il portale di Azure o PowerShell.

### <a name="configure-source-control----azure-portal"></a>Configurare il controllo del codice sorgente--portale di Azure

Usare questa procedura per configurare il controllo del codice sorgente usando il portale di Azure.

1. Nell'account di automazione selezionare **controllo del codice sorgente** e fare clic su **+ Aggiungi**.

    ![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

2. Scegliere il **tipo di controllo del codice sorgente**, quindi fare clic su **autentica**. 

3. Viene visualizzata una finestra del browser in cui viene chiesto di eseguire l'accesso. Seguire le istruzioni per completare l'autenticazione.

4. Nella pagina di **Riepilogo del controllo del codice sorgente** usare i campi per inserire le proprietà del controllo del codice sorgente definite di seguito. Al termine, fare clic su **Salva**. 

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Nome del controllo del codice sorgente     | Nome descrittivo per il controllo del codice sorgente. Questo nome deve contenere solo lettere e numeri.        |
    |Tipo di controllo del codice sorgente     | Tipo di meccanismo di controllo del codice sorgente. Le opzioni disponibili sono:</br> GitHub</br>Azure Repos (git)</br> Azure Repos (TFVC)        |
    |Repository     | Nome del repository o del progetto. Vengono recuperati i primi 200 repository. Per cercare un repository, digitare il nome nel campo e fare clic **su Cerca in GitHub**.|
    |Ramo     | Ramo da cui eseguire il pull dei file di origine. La destinazione del ramo non è disponibile per il tipo di controllo del codice sorgente TFVC.          |
    |Percorso cartella     | Cartella che contiene il manuali operativi da sincronizzare, ad esempio/runbooks. Solo manuali operativi nella cartella specificata sono sincronizzati. La ricorsione non è supportata.        |
    |Sincronizzazione automatica<sup>1</sup>     | Impostazione che attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente.        |
    |Pubblica runbook     | Impostando su on se i manuali operativi vengono pubblicati automaticamente dopo la sincronizzazione dal controllo del codice sorgente e in caso contrario.           |
    |Descrizione     | Testo che specifica ulteriori dettagli sul controllo del codice sorgente.        |

    <sup>1</sup> per abilitare la sincronizzazione automatica quando si configura l'integrazione del controllo del codice sorgente con Azure Repos, è necessario essere un amministratore di progetto.

   ![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> L'account di accesso per il repository del controllo del codice sorgente può essere diverso dall'account di accesso per il portale di Azure. Quando si configura il controllo del codice sorgente, verificare di avere eseguito l'accesso con l'account corretto per il repository del controllo del codice sorgente. In caso di dubbi, aprire una nuova scheda nel browser, disconnettersi da visualstudio.com o github.com e provare a connettersi di nuovo al controllo del codice sorgente.

### <a name="configure-source-control----powershell"></a>Configurare il controllo del codice sorgente-PowerShell

È anche possibile usare PowerShell per configurare il controllo del codice sorgente in automazione di Azure. Per usare i cmdlet di PowerShell per questa operazione, è necessario un token di accesso personale (PAT). Usare il cmdlet [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) per creare la connessione al controllo del codice sorgente. Questo cmdlet richiede una stringa sicura per PAT. Per informazioni su come creare una stringa sicura, vedere [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Le sottosezioni seguenti illustrano la creazione di PowerShell per la connessione al controllo del codice sorgente per GitHub, Azure Repos (git) e Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Creare una connessione al controllo del codice sorgente per GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Crea connessione del controllo del codice sorgente per Azure Repos (git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Crea connessione del controllo del codice sorgente per Azure Repos (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Autorizzazioni per il token di accesso personale (PAT)

Il controllo del codice sorgente richiede alcune autorizzazioni minime per le carezze. Le sottosezioni seguenti contengono le autorizzazioni minime necessarie per GitHub e Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Autorizzazioni PAT minime per GitHub

La tabella seguente definisce le autorizzazioni PAT minime necessarie per GitHub. Per ulteriori informazioni sulla creazione di un PAT in GitHub, vedere [creazione di un token di accesso personale per la riga di comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Ambito  |Descrizione  |
|---------|---------|
|**repo**     |         |
|repo:status     | Accedere allo stato del commit         |
|repo_deployment      | Accedere allo stato della distribuzione         |
|public_repo     | Accedere ai repository pubblici         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Scrivere gli hook del repository         |
|read:repo_hook|Leggere gli hook del repository|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Autorizzazioni PAT minime per Azure Repos

Nell'elenco seguente sono definite le autorizzazioni PAT minime necessarie per Azure Repos. Per altre informazioni sulla creazione di un PAT in Azure Repos, vedere [autenticare l'accesso con i token di accesso personali](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Ambito  |  Tipo di accesso  |
|---------| ----------|
| Codice      | Lettura  |
| Progetto e team | Lettura |
| Identity | Lettura     |
| Profilo utente | Lettura     |
| Elementi di lavoro | Lettura    |
| Connessioni al servizio | Lettura, query, gestione<sup>1</sup>    |

<sup>1</sup> l'autorizzazione connessioni al servizio è necessaria solo se è stata abilitata la sincronizzazione automatica.

## <a name="synchronizing"></a>Sincronizzazione in corso

Eseguire le operazioni seguenti per eseguire la sincronizzazione con il controllo del codice sorgente. 

1. Consente di selezionare l'origine della tabella nella pagina **controllo del codice sorgente** . 

2. Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione. 

3. Per visualizzare lo stato del processo di sincronizzazione corrente o di quelli precedenti, fare clic sulla scheda **Sincronizza processi** . 

4. Nel menu a discesa **controllo del codice sorgente** selezionare un meccanismo di controllo del codice sorgente.

    ![Stato sincronizzazione](./media/source-control-integration/sync-status.png)

5. Facendo clic su un processo è possibile visualizzarne l'output. Di seguito è riportato un esempio di output di un processo di sincronizzazione di un controllo del codice sorgente.

    ```output
    ============================================================================

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

     =========================================================================

    ```

6. Per la registrazione aggiuntiva è possibile selezionare **tutti i log** nella pagina Riepilogo del processo di sincronizzazione del **controllo del codice sorgente** . Queste voci di log aggiuntive possono aiutare a risolvere i problemi che possono verificarsi quando si usa il controllo del codice sorgente.

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi da un repository del controllo del codice sorgente:

1. Aprire il **controllo del codice sorgente** in **Impostazioni account** nell'account di automazione.

2. Selezionare il meccanismo di controllo del codice sorgente da rimuovere. 

3. Nella pagina **Riepilogo del Controllo del codice sorgente** fare clic su **Elimina**.

## <a name="handling-encoding-issues"></a>Gestione dei problemi di codifica

Se più persone modificano manuali operativi nel repository del controllo del codice sorgente usando Editor diversi, possono verificarsi problemi di codifica. Per ulteriori informazioni su questa situazione, vedere [cause comuni dei problemi di codifica](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>Aggiornamento del PAT

Attualmente non è possibile usare la portale di Azure per aggiornare PAT nel controllo del codice sorgente. Quando PAT è scaduto o è stato revocato, è possibile aggiornare il controllo del codice sorgente con un nuovo token di accesso in uno dei modi seguenti:

* Usare l' [API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Usare il cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui tipi di Runbook e i relativi vantaggi e limitazioni, vedere [tipi di Runbook di automazione di Azure](automation-runbook-types.md).
