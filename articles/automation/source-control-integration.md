---
title: Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132919"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrazione del controllo del codice sorgente in Automazione di Azure

 L'integrazione del controllo del codice sorgente in Automazione di Azure supporta la sincronizzazione a direzione singola dal repository del controllo del codice sorgente. Il controllo del codice sorgente consente di mantenere aggiornati i runbook nell'account di automazione con gli script nel repository del controllo del codice sorgente GitHub o Azure Repos. Questa funzionalità semplifica la promozione del codice testato nell'ambiente di sviluppo all'account di automazione di produzione.
 
 L'integrazione del controllo del codice sorgente consente di collaborare facilmente con il team, tenere traccia delle modifiche ed eseguire il rollback alle versioni precedenti dei runbook. Ad esempio, il controllo del codice sorgente consente di sincronizzare rami diversi nel controllo del codice sorgente con gli account di automazione di sviluppo, test e produzione. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="source-control-types"></a>Tipi di controllo del codice sorgente

Automazione di Azure supporta tre tipi di controllo del codice sorgente:Azure Automation supports three types of source control:

* GitHub
* Azure Repos (Git)Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Prerequisiti

* Un repository del controllo del codice sorgente (GitHub o Azure Repos)
* Un [account RunAs](manage-runas-account.md)
* I [moduli di Azure più](automation-update-azure-modules.md) recenti `Az.Accounts` nell'account di `AzureRM.Profile`automazione, incluso il modulo (Az module equivalente di )

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti con l'account di automazione dell'utente e vengono fatturati alla stessa velocità degli altri processi di automazione.

## <a name="configuring-source-control"></a>Configurazione del controllo del codice sorgente

In questa sezione viene illustrato come configurare il controllo del codice sorgente per l'account di automazione. È possibile usare il portale di Azure o PowerShell.You can use either the Azure portal or PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Configurare il controllo del codice sorgente nel portale di AzureConfigure source control in Azure portal

Usare questa procedura per configurare il controllo del codice sorgente usando il portale di Azure.Use this procedure to configure source control using the Azure portal.

1. Nell'account di automazione selezionare **Controllo del codice sorgente** e fare clic su **Aggiungi**.

    ![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

2. Scegliere **Tipo di controllo del codice sorgente**, quindi fare clic su **Autentica**. 

3. Viene visualizzata una finestra del browser in cui viene richiesto di accedere. Seguire le istruzioni visualizzate per completare l'autenticazione.

4. Nella pagina Riepilogo controllo del codice sorgente utilizzare i campi per compilare le proprietà del controllo del codice sorgente definite di seguito. Al termine, fare clic su **Salva**. 

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Nome del controllo del codice sorgente     | Nome descrittivo per il controllo del codice sorgente. Questo nome deve contenere solo lettere e numeri.        |
    |Tipo di controllo del codice sorgente     | Tipo di meccanismo di controllo del codice sorgente. Le opzioni disponibili sono:</br> GitHub</br>- Ripos di Azure (Git)</br> - Ripos di Azure (TFVC)        |
    |Archivio     | Nome del repository o del progetto. Vengono recuperati i primi 200 repository. Per cercare un repository, digitare il nome nel campo e fare clic su **Cerca su GitHub**.|
    |Ramo     | Diramare da cui estrarre i file di origine. La destinazione del ramo non è disponibile per il tipo di controllo del codice sorgente TFVC.          |
    |Percorso della cartella     | Cartella contenente i runbook da sincronizzare, ad esempio **/Runbooks**. Vengono sincronizzati solo i runbook nella cartella specificata. La ricorsione non è supportata.        |
    |Sincronizzazione automatica<sup>1</sup>     | Impostazione che attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente.        |
    |Pubblica runbook     | Se i runbook vengono pubblicati automaticamente dopo la sincronizzazione dal controllo del codice sorgente e Off in caso contrario, l'impostazione di On if runbooks.           |
    |Descrizione     | Testo che specifica dettagli aggiuntivi sul controllo del codice sorgente.        |

    <sup>1</sup> Per abilitare la sincronizzazione automatica durante la configurazione dell'integrazione del controllo del codice sorgente con Azure Repos, è necessario essere un amministratore di progetto.1 To enable Auto Sync when configuring source control integration with Azure Repos, you must be a Project Administrator.

   ![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> L'account di accesso per il repository del controllo del codice sorgente potrebbe essere diverso dall'account di accesso per il portale di Azure.The login for your source control repository might be different from your login for the Azure portal. Assicurarsi di aver effettuato l'accesso con l'account corretto per il repository del controllo del codice sorgente durante la configurazione del controllo del codice sorgente. In caso di dubbi, aprire una nuova scheda nel browser, disconnettersi da **dev.azure.com**, **visualstudio.com**o **github.com**e provare a riconnettersi al controllo del codice sorgente.

### <a name="configure-source-control-in-powershell"></a>Configurare il controllo del codice sorgente in PowerShellConfigure source control in PowerShell

È anche possibile usare PowerShell per configurare il controllo del codice sorgente in Automazione di Azure.You can also use PowerShell to configure source control in Azure Automation. Per utilizzare i cmdlet di PowerShell per questa operazione, è necessario un token di accesso personale (PAT). Utilizzare il cmdlet [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) per creare la connessione al controllo del codice sorgente. Questo cmdlet richiede una stringa sicura per il PAT. Per informazioni su come creare una stringa sicura, vedere [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Le sottosezioni seguenti illustrano la creazione di PowerShell della connessione del controllo del codice sorgente per GitHub, Azure Repos (Git) e Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Creare una connessione al controllo del codice sorgente per GitHubCreate source control connection for GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Creare una connessione del controllo del codice sorgente per Azure Repos (Git)Create source control connection for Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) usa un URL che accede a **dev.azure.com** anziché **visualstudio.com,** usato in formati precedenti. Il formato `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` dell'URL precedente è deprecato ma ancora supportato. Il nuovo formato è preferito.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Creare una connessione del controllo del codice sorgente per Azure Repos (TFVC)Create source control connection for Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) usa un URL che accede a **dev.azure.com** anziché **visualstudio.com**, usato nei formati precedenti. Il formato `https://<accountname>.visualstudio.com/<projectname>/_versionControl` dell'URL precedente è deprecato ma ancora supportato. Il nuovo formato è preferito.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Autorizzazioni per i token di accesso personale (PAT)

Il controllo del codice sorgente richiede alcune autorizzazioni minime per i PAT. The following subsections contain the minimum permissions required for GitHub and Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Autorizzazioni PAT minime per GitHub

Nella tabella seguente vengono definite le autorizzazioni PAT minime necessarie per GitHub. Per ulteriori informazioni sulla creazione di un PAT in GitHub, vedere Creazione di un token di [accesso personale per la riga](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)di comando .

|Scope  |Descrizione  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Accedere allo stato del commit         |
|`repo_deployment`      | Accedere allo stato della distribuzione         |
|`public_repo`     | Accedere ai repository pubblici         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Scrivere gli hook del repository         |
|`read:repo_hook`|Leggere gli hook del repository|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Autorizzazioni DI BASE minime per i repository di AzureMinimum PAT permissions for Azure Repos

The following list defines the minimum PAT permissions required for Azure Repos. Per altre informazioni sulla creazione di un PAT in Azure Repos, vedere [Autenticare l'accesso con token](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)di accesso personali.

| Scope  |  Tipo di accesso  |
|---------| ----------|
| `Code`      | Lettura  |
| `Project and team` | Lettura |
| `Identity` | Lettura     |
| `User profile` | Lettura     |
| `Work items` | Lettura    |
| `Service connections` | Lettura, query, gestione<sup>1</sup>    |

<sup>1</sup> `Service connections` L'autorizzazione è necessaria solo se è stata abilitata la sincronizzazione automatica.

## <a name="synchronizing"></a>Sincronizzazione in corso

Attenersi alla seguente procedura per la sincronizzazione con il controllo del codice sorgente. 

1. Selezionare l'origine dalla tabella nella pagina Controllo di origine. 

2. Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione. 

3. Visualizzare lo stato del processo di sincronizzazione corrente o di quelli precedenti facendo clic sulla scheda **Sincronizza processi.** 

4. Nel menu a discesa **Controllo del codice** sorgente selezionare un meccanismo di controllo del codice sorgente.

    ![Stato sincronizzazione](./media/source-control-integration/sync-status.png)

5. Facendo clic su un processo è possibile visualizzarne l'output. Di seguito è riportato un esempio di output di un processo di sincronizzazione di un controllo del codice sorgente.

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. È disponibile una registrazione aggiuntiva selezionando **Tutti i registri** nella pagina Riepilogo processo di sincronizzazione del controllo del codice sorgente. Queste voci di log aggiuntive consentono di risolvere i problemi che potrebbero verificarsi quando si utilizza il controllo del codice sorgente.

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi da un repository del controllo del codice sorgente:

1. Aprire **Controllo del codice sorgente** in Impostazioni **account** nell'account di automazione.

2. Selezionare il meccanismo di controllo del codice sorgente da rimuovere. 

3. Nella pagina Riepilogo del Controllo del codice sorgente fare clic su **Elimina**.

## <a name="handling-encoding-issues"></a>Gestione dei problemi di codifica

Se più persone modificano i runbook nel repository del controllo del codice sorgente utilizzando editor diversi, possono verificarsi problemi di codifica. Per ulteriori informazioni su questa situazione, vedere [Cause comuni dei problemi di codifica](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Aggiornamento del PAT

Attualmente, è possibile utilizzare il portale di Azure per aggiornare il PAT nel controllo del codice sorgente. Quando il PAT è scaduto o revocato, è possibile aggiornare il controllo del codice sorgente con un nuovo token di accesso in uno dei modi seguenti:

* Utilizzare [l'API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Utilizzare il cmdlet [Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui tipi di runbook e sui relativi vantaggi e limitazioni, vedere [Tipi di runbook di Automazione di Azure.To](automation-runbook-types.md)learn more about runbook types and their advantages and limitations, see Azure Automation runbook types .
