---
title: Usare Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive come sincronizzare il controllo del codice sorgente di Automazione di Azure con altri repository.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: eea4de106fe566b55ae30330d4c9d101f7126bbf
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229619"
---
# <a name="use-source-control-integration"></a>Usare l'integrazione del controllo del codice sorgente

 L'integrazione del controllo del codice sorgente in Automazione di Azure supporta la sincronizzazione a direzione singola dal repository del controllo del codice sorgente. Il controllo del codice sorgente consente di mantenere i runbook inclusi nell'account di Automazione aggiornati con gli script del repository del controllo del codice sorgente Azure Repos o GitHub. Con questa funzionalità è facile alzare di livello il codice testato nell'ambiente di sviluppo per l'account di Automazione di produzione.
 
 L'integrazione del controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, rilevare le modifiche ed eseguire il rollback a versioni precedenti dei runbook. Il controllo del codice sorgente, ad esempio, consente di sincronizzare diversi rami nel controllo del codice sorgente per gli account di Automazione di sviluppo, test e produzione. 

## <a name="source-control-types"></a>Tipi di controllo del codice sorgente

Automazione di Azure supporta tre tipi di controllo del codice sorgente:

* GitHub
* Azure Repos (Git)
* Azure Repos (Controllo della versione di Team Foundation)

## <a name="prerequisites"></a>Prerequisiti

* Un repository di controllo del codice sorgente (GitHub o Azure Repos)
* Un [account RunAs](manage-runas-account.md)
* I [moduli di Azure più recenti](automation-update-azure-modules.md) nell'account di Automazione, incluso il modulo `Az.Accounts`, ovvero il modulo Az equivalente di `AzureRM.Profile`

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti nell'account di Automazione dell'utente e vengono fatturati alla stessa tariffa degli altri processi di automazione.

## <a name="configure-source-control"></a>Configurare il controllo del codice sorgente

Questa sezione descrive come configurare il controllo del codice sorgente per l'account di Automazione. È possibile usare il portale di Azure o PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Configurare il controllo del codice sorgente nel portale di Azure

Usare questa procedura per configurare il controllo del codice sorgente usando il portale di Azure.

1. Nell'account di Automazione selezionare **Controllo del codice sorgente** e fare clic su **Aggiungi**.

    ![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

2. Scegliere **Tipo di controllo del codice sorgente** e fare clic su **Autentica**. 

3. Viene visualizzata una finestra del browser in cui si chiede di effettuare l'accesso. Seguire le istruzioni per completare l'autenticazione.

4. Nella pagina Riepilogo del Controllo del codice sorgente usare i campi per specificare le proprietà del controllo del codice sorgente definite di seguito. Al termine, fare clic su **Salva**. 

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Nome del controllo del codice sorgente     | Nome descrittivo per il controllo del codice sorgente. Il nome deve contenere solo lettere e numeri.        |
    |Tipo di controllo del codice sorgente     | Tipo di meccanismo del controllo del codice sorgente. Le opzioni disponibili sono:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (Controllo della versione di Team Foundation)        |
    |Archivio     | Nome del repository o del progetto. Vengono recuperati i primi 200 repository. Per cercare un repository, digitare il nome nel campo e fare clic su **Search on GitHub** (Cerca in GitHub).|
    |Ramo     | Ramo da cui eseguire il pull dei file di origine. La selezione della destinazione del ramo non è disponibile per il controllo del codice sorgente di tipo Controllo della versione di Team Foundation.          |
    |Percorso della cartella     | Cartella che contiene i runbook da sincronizzare, ad esempio **/Runbooks**. Vengono sincronizzati solo i runbook presenti nella cartella specificata. La ricorsione non è supportata.        |
    |Sincronizzazione automatica<sup>1</sup>     | Impostazione che attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente.        |
    |Pubblica runbook     | Impostazione "Attivato" se i runbook vengono pubblicati automaticamente dopo la sincronizzazione dal controllo del codice sorgente e "Disattivato" in caso contrario.           |
    |Descrizione     | Testo che specifica altri dettagli sul controllo del codice sorgente.        |

    <sup>1</sup> Per abilitare la sincronizzazione automatica quando si configura l'integrazione del controllo del codice sorgente con Azure Repos, è necessario essere un amministratore di progetto.

   ![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> L'account di accesso per il repository del controllo del codice sorgente può essere diverso dall'account di accesso per il portale di Azure. Quando si configura il controllo del codice sorgente, verificare di avere eseguito l'accesso con l'account corretto per il repository del controllo del codice sorgente. In caso di dubbi, aprire una nuova scheda nel browser, disconnettersi da **dev.azure.com**, **visualstudio.com** o **github.com** e provare a riconnettersi al controllo del codice sorgente.

### <a name="configure-source-control-in-powershell"></a>Configurare il controllo del codice sorgente in PowerShell

È anche possibile usare PowerShell per configurare il controllo del codice sorgente in Automazione di Azure. Per usare i cmdlet di PowerShell per questa operazione, è necessario un token di accesso personale. Usare il cmdlet [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0) per creare la connessione al controllo del codice sorgente. Questo cmdlet richiede una stringa sicura per il token di accesso personale. Per informazioni su come creare una stringa sicura, vedere [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Le sottosezioni seguenti illustrano la creazione della connessione al controllo del codice sorgente con PowerShell per GitHub, Azure Repos (Git) e Azure Repos (controllo della versione di Team Foundation). 

#### <a name="create-source-control-connection-for-github"></a>Creare una connessione al controllo del codice sorgente per GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Creare una connessione al controllo del codice sorgente per Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) usa un URL che accede a **dev.azure.com** anziché **visualstudio.com**, usato nei formati precedenti. Il formato dell'URL precedente `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` è deprecato, ma è ancora supportato. È preferibile il nuovo formato.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Creare una connessione al controllo del codice sorgente per Azure Repos (controllo della versione di Team Foundation)

> [!NOTE]
> Azure Repos (controllo della versione di Team Foundation) usa un URL che accede a **dev.azure.com** anziché **visualstudio.com**, usato nei formati precedenti. Il formato dell'URL precedente `https://<accountname>.visualstudio.com/<projectname>/_versionControl` è deprecato, ma è ancora supportato. È preferibile il nuovo formato.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Autorizzazioni dei token di accesso personale

Il controllo del codice sorgente richiede alcune autorizzazioni minime per i token di accesso personale. Le sottosezioni seguenti contengono le autorizzazioni minime necessarie per GitHub e Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Autorizzazioni minime per il token di accesso personale per GitHub

La tabella seguente definisce le autorizzazioni minime per il token di accesso personale necessarie per GitHub. Per altre informazioni sulla creazione di un token di accesso personale in GitHub, vedere [Creazione di un token di accesso personale per la riga di comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Scope  |Descrizione  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Accedere allo stato del commit         |
|`repo_deployment`      | Accedere allo stato della distribuzione         |
|`public_repo`     | Accedere ai repository pubblici         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Scrivere gli hook del repository         |
|`read:repo_hook`|Leggere gli hook del repository|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Autorizzazioni minime per il token di accesso personale per Azure Repos

L'elenco seguente definisce le autorizzazioni minime per il token di accesso personale necessarie per Azure Repos. Per altre informazioni sulla creazione di un token di accesso personale in Azure Repos, vedere [Autenticare l'accesso con i token di accesso personali](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page).

| Scope  |  Tipo di accesso  |
|---------| ----------|
| `Code`      | Lettura  |
| `Project and team` | Lettura |
| `Identity` | Lettura     |
| `User profile` | Lettura     |
| `Work items` | Lettura    |
| `Service connections` | Lettura, query, gestione<sup>1</sup>    |

<sup>1</sup> L'autorizzazione `Service connections` è necessaria solo se è stata abilitata la sincronizzazione automatica.

## <a name="synchronize-with-source-control"></a>Eseguire la sincronizzazione con il controllo del codice sorgente

Seguire questa procedura per eseguire la sincronizzazione con il controllo del codice sorgente. 

1. Selezionare l'origine dalla tabella nella pagina Controllo del codice sorgente. 

2. Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione. 

3. Visualizzare lo stato del processo di sincronizzazione corrente o di quelli precedenti facendo clic sulla scheda **Processi di sincronizzazione**. 

4. Nel menu a discesa **Controllo del codice sorgente** selezionare un meccanismo di controllo del codice sorgente.

    ![Stato sincronizzazione](./media/source-control-integration/sync-status.png)

5. Facendo clic su un processo è possibile visualizzarne l'output. Di seguito è riportato un esempio di output di un processo di sincronizzazione di un controllo del codice sorgente.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

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

6. Per la registrazione aggiuntiva è possibile selezionare **Tutti i log** nella pagina Riepilogo dei processi di sincronizzazione del Controllo del codice sorgente. Queste voci di log aggiuntive possono contribuire a risolvere i problemi che possono verificarsi quando si usa il controllo del codice sorgente.

## <a name="disconnect-source-control"></a>Disconnettersi dal controllo del codice sorgente

Per disconnettersi da un repository del controllo del codice sorgente:

1. Aprire **Controllo del codice sorgente** in **Impostazioni account** nell'account di Automazione.

2. Selezionare il meccanismo di controllo del codice sorgente da rimuovere. 

3. Nella pagina Riepilogo del Controllo del codice sorgente fare clic su **Elimina**.

## <a name="handle-encoding-issues"></a>Gestire i problemi di codifica

Se più persone modificano i runbook nel repository del controllo del codice sorgente usando editor diversi, possono verificarsi problemi di codifica. Per altre informazioni su questa situazione, vedere [Cause più comuni dei problemi di codifica](/powershell/scripting/components/vscode/understanding-file-encoding?view=powershell-7#common-causes-of-encoding-issues).

## <a name="update-the-pat"></a>Aggiornare il token di accesso personale

Non è al momento possibile usare il portale di Azure per aggiornare il token di accesso personale nel controllo del codice sorgente. Quando il token di accesso personale è scaduto o revocato, è possibile aggiornare il controllo del codice sorgente con un nuovo token di accesso in uno dei modi seguenti:

* Usare l'[API REST](/rest/api/automation/sourcecontrol/update).
* Usare il cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol).

## <a name="next-steps"></a>Passaggi successivi

* Per l'integrazione del controllo del codice sorgente in Automazione di Azure, vedere [Automazione di Azure: Integrazione del controllo del codice sorgente in Automazione di Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Per l'integrazione del controllo del codice sorgente dei runbook con Visual Studio Online, vedere [Automazione di Azure: Integrazione del controllo del codice sorgente dei runbook con Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
