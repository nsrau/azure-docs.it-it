---
title: Gestire i moduli in Automazione di Azure
description: Questo articolo descrive come gestire i moduli in Automazione di Azure.This article describes how to manage modules in Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770953"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in Automazione di Azure

Automazione di Azure consente di importare moduli di PowerShell per abilitare i cmdlet nei runbook e nelle risorse DSC nelle configurazioni DSC. I moduli usati nell'automazione di Azure includono:Modules used in Azure Automation include:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Modulo `Orchestrator.AssetManagement.Cmdlets` interno per l'agente di Log Analytics per Windows
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Altri moduli di PowerShell
* Moduli personalizzati creati 

Quando si crea un account di automazione, Automazione di Azure importa alcuni moduli per impostazione predefinita. Consultate [Moduli predefiniti.](#default-modules)

Quando Automazione di Azure esegue processi di compilazione runbook e DSC, carica i moduli in sandbox in cui è possibile eseguire i runbook e le configurazioni DSC possono essere compilate. L'automazione inoltre inserisce automaticamente tutte le risorse DSC nei moduli nel server di pull DSC. I computer possono estrarre le risorse quando applicano le configurazioni DSC.

>[!NOTE]
>Assicurarsi di importare solo i moduli effettivamente necessari per i runbook e le configurazioni DSC. Non importare il modulo di rollup, ad esempio Az.Automation, in ogni caso.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="default-modules"></a>Moduli predefiniti

Nella tabella seguente sono elencati i moduli importati per impostazione predefinita di Automazione di Azure quando si crea l'account di automazione. L'automazione può importare versioni più recenti di questi moduli. Tuttavia, non puoi rimuovere la versione originale dal tuo account Automation anche se elimini una versione più recente. Si noti che questi moduli predefiniti includono diversi moduli di AzureRM.Note that these default modules include several AzureRM modules. 

> [!NOTE]
> Non è consigliabile modificare moduli e runbook negli account di automazione che contengono soluzioni. 

|Nome modulo|Versione|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResourcesStateConfigCompositeResources | 1 |
| xDSCDomainjoin (aggiunta a xDSCDomainjoin) | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin (informazioni in remoto) | 1.1.0.0 |


I moduli Az.Automation sono preferiti nei runbook e nelle configurazioni DSC. Tuttavia, Automazione di Azure non importa automaticamente il modulo Az di rollup in account di automazione nuovi o esistenti. Per ulteriori informazioni sull'utilizzo di questi moduli, vedere [Migrazione ai moduli Az](#migrating-to-az-modules).

## <a name="internal-cmdlets"></a>Cmdlet interni

Nella tabella seguente vengono definiti i `Orchestrator.AssetManagement.Cmdlets` cmdlet interni supportati dal modulo. Usali nei runbook e nelle configurazioni DSC per interagire con gli asset di automazione all'interno dell'account Automation. I cmdlet sono progettati per recuperare segreti da variabili crittografate, credenziali e connessioni crittografate. 

> [!NOTE]
> I cmdlet di Azure PowerShell non possono ottenere i segreti degli asset che i cmdlet interni possono recuperare. 

|Nome|Descrizione|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Questi cmdlet interni sono disponibili in un ruolo di lavoro ibrido di Windows, ma non in un ruolo di lavoro per runbook ibrido Linux.These internal cmdlets are available on a Windows Hybrid Runbook Worker, but not on a Linux Hybrid Runbook Worker. Vengono eseguiti da una sandbox di Orchestrator, che viene utilizzata dal worker ibrido.  Il loro utilizzo non richiede una connessione implicita ad Azure, come quando si usa un account RunAs per l'autenticazione.

Anziché utilizzare i cmdlet interni, usare i cmdlet Az o AzureRM per runbook e configurazioni in esecuzione direttamente nel computer o per le risorse dell'ambiente. In questi casi, è necessario connettersi in modo implicito ad Azure quando si usano i cmdlet, come quando si usa un account RunAs per l'autenticazione in Azure.In these cases, you must implicitly connect to Azure when using the cmdlets, as when using a RunAs account to authenticate to Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Moduli che supportano Get-AutomationPSCredential

Per lo sviluppo locale utilizzando Azure `Get-AutomationPSCredential` Automation Authoring Toolkit, il cmdlet fa parte dell'assembly [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Per Azure che utilizza il contesto `Orchestrator.AssetManagement.Cmdlets`di automazione, il cmdlet è in . Per altre informazioni sull'uso delle credenziali in Automazione di Azure, vedere Asset delle credenziali in Automazione di Azure.To find out more about the use of credentials in Azure Automation, see [Credential assets in Azure Automation.](credentials.md)

## <a name="migrating-to-az-modules"></a>Migrazione a moduli Az

Esistono diversi aspetti da considerare quando si usano i moduli di Az in Automazione di Azure:There are several things to take into consideration when using the Az modules in Azure Automation:

* Le soluzioni di livello superiore dell'account Di automazione possono usare runbook e moduli. Pertanto, la modifica dei runbook o l'aggiornamento dei moduli può causare problemi con le soluzioni. È consigliabile testare attentamente tutti i runbook e le soluzioni in un account di automazione separato prima di importare i moduli Az.You should test all runbooks and solutions carefully in a separate Automation account before importing the Az modules. 

* Eventuali modifiche ai moduli possono influire negativamente sulla [soluzione Avvia/Arresta macchine virtuali durante la soluzione off-hours.](../automation-solution-vm-management.md) 

* L'importazione di un modulo Az nell'account di automazione non importa automaticamente il modulo nella sessione di PowerShell usata dai runbook. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

    * Quando un runbook richiama un cmdlet da un modulo
    * Quando un runbook importa il `Import-Module` modulo in modo esplicito con il cmdlet
    * Quando un runbook importa un altro modulo dipendente

Dopo aver completato la migrazione dei moduli, non provare ad avviare runbook usando i moduli di AzureRM nell'account di automazione. È inoltre consigliabile non importare o aggiornare i moduli di AzureRM nell'account. Si consideri l'account migrato in Az.Automation e operi solo con i moduli Az. 

>[!IMPORTANT]
>Quando si crea un nuovo account di automazione, Automazione di Azure installa i moduli di AzureRM per impostazione predefinita. È comunque possibile aggiornare i runbook dell'esercitazione con i cmdlet di AzureRM.You can still update the tutorial runbooks with AzureRM cmdlets. Tuttavia, è consigliabile non eseguire questi runbook.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Arrestare e annullare la pianificazione di tutti i runbook che usano i moduli di AzureRMStop and unschedule all runbooks that use AzureRM modules

Per assicurarsi di non eseguire i runbook esistenti che usano i moduli di AzureRM, arrestare e annullare la pianificazione di tutti i runbook interessati. È possibile visualizzare le pianificazioni esistenti e le pianificazioni da rimuovere eseguendo codice simile a questo esempio:You can see what schedules exist and which schedules to remove by running code similar to this example:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

È importante esaminare ogni pianificazione separatamente per assicurarsi di poterla riprogrammare in futuro per i runbook, se necessario.

### <a name="import-the-az-modules"></a>Importare moduli Az

Questa sezione spiega come importare i moduli Az nel portale di Azure.This section tells how to import the Az modules in the Azure portal. Ricordarsi di importare solo i moduli Az necessari, non l'intero modulo Az.Automation.Remember to import only the Az modules that you need, not the entire Az.Automation module. Poiché [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) è una dipendenza per gli altri moduli di Az, assicurarsi di importare questo modulo prima di qualsiasi altro.

1. Dall'account di automazione, selezionare **Moduli** in **Risorse condivise**. 
2. Fare clic su **Esplora raccolta** per aprire la pagina Esplora raccolta.  
3. Nella barra di ricerca immettere il `Az.Accounts`nome del modulo, ad esempio . 
4. Nella pagina Modulo PowerShell fare clic su **Importa** per importare il modulo nell'account di automazione.

    ![Importare moduli nell'account di automazione](../media/modules/import-module.png)

Questo processo di importazione può essere eseguito anche tramite [PowerShell Gallery](https://www.powershellgallery.com) cercando il modulo da importare. Dopo aver trovato il modulo, selezionarlo, scegliere la scheda **Automazione di Azure** e fare clic su **Distribuisci in Automazione di Azure**.

![Importare moduli direttamente dalla raccolta](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testare i runbook

Dopo aver importato i moduli Az nell'account di automazione, è possibile iniziare a modificare i runbook per utilizzare i nuovi moduli. La maggior parte dei cmdlet ha gli stessi nomi dei moduli di AzureRM, ad eccezione del fatto che il prefisso AzureRM (o AzureRm) è stato modificato in Az. Per un elenco dei moduli che non seguono questa convenzione di denominazione, vedere [elenco di eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un modo per testare la modifica di un runbook `Enable-AzureRmAlias -Scope Process` per utilizzare i nuovi cmdlet consiste nell'utilizzare all'inizio del runbook. Aggiungendo questo comando al runbook, lo script può essere eseguito senza modifiche.

## <a name="authoring-modules"></a>Moduli di authoring

È consigliabile seguire le considerazioni in questa sezione quando si crea un modulo di PowerShell per l'uso in Automazione di Azure.We recommend that you follow the considerations in this section when you author a PowerShell module for use in Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Riferimenti ad AzureRM e AzReferences to AzureRM and Az

Se si fa riferimento ai moduli di Az nel modulo, assicurarsi che non si faccia riferimento anche ai moduli di AzureRM.If referencing the Az modules in your module, ensure that you aren't also referencing the AzureRM modules. Non è possibile utilizzare entrambi i set di moduli contemporaneamente. 

### <a name="version-folder"></a>Cartella Versione

NON includere una cartella di versione nel pacchetto **.zip** per il modulo.  Questo problema è meno di un problema per i runbook, ma causa un problema con il servizio di configurazione dello stato (DSC). Automazione di Azure crea automaticamente la cartella della versione quando il modulo viene distribuito ai nodi gestiti da DSC. Se esiste una cartella di versione, si finisce con due istanze. Di seguito è riportato un esempio di struttura di cartelle per un modulo DSC:Here is an example folder structure for a DSC module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informazioni della Guida

Includere una sinossi, una descrizione e un URI della Guida per ogni cmdlet nel modulo. In PowerShell è possibile definire informazioni della `Get-Help` Guida per i cmdlet utilizzando il cmdlet. Nell'esempio seguente viene illustrato come definire una sinossi e un URI della Guida in un file di modulo **con estensione psm1.**

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fornendo queste informazioni viene `Get-Help` visualizzato il testo della Guida tramite il cmdlet nella console di PowerShell.Providing this information shows help text via the cmdlet in the PowerShell console. Questo testo viene visualizzato anche nel portale di Azure.This text is also displayed in the Azure portal.

  ![Guida del modulo di integrazione](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo di connessione

Se il modulo si connette a un servizio esterno, definire un [tipo di connessione](#adding-a-connection-type-to-your-module). Ogni cmdlet nel modulo deve accettare un oggetto connessione (un'istanza di tale tipo di connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset di connessione ai parametri corrispondenti del cmdlet ogni volta che chiamano un cmdlet. L'esempio di runbook seguente, basato sull'esempio nella `ContosoConnection` sezione precedente, usa un asset di connessione Contoso chiamato per accedere alle risorse Contoso e restituire dati dal servizio esterno. In questo esempio, i campi `UserName` vengono `Password` mappati `PSCredential` alle proprietà e di un oggetto e quindi passati al cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Un modo più semplice e migliore per affrontare questo comportamento consiste nel passare direttamente l'oggetto connessione al cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  È possibile abilitare un comportamento simile per i cmdlet consentendo loro di accettare un oggetto connessione direttamente come parametro, anziché solo i campi di connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set `UserAccount` di parametri viene utilizzato per passare le proprietà del campo di connessione. `ConnectionObject`consente di passare la connessione direttamente attraverso.

### <a name="output-type"></a>Tipo di output

Definire il tipo di output per tutti i cmdlet nel modulo. La definizione di un tipo di output per un cmdlet consente a IntelliSense in fase di progettazione di determinare le proprietà di output del cmdlet durante la creazione. Questa procedura è particolarmente utile durante la creazione di runbook grafici, per i quali la conoscenza in fase di progettazione è fondamentale per un'esperienza utente semplice con il modulo.

Aggiungere `[OutputType([<MyOutputType>])]` `MyOutputType` where è un tipo valido. Per ulteriori `OutputType`informazioni sulle , vedere [Informazioni sulle funzioni OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è `OutputType` un esempio di aggiunta a un cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Tipo di output di runbook grafici](../media/modules/runbook-graphical-module-output-type.png)

  Questo comportamento è simile alla funzionalità di "completamento automatico" dell'output di un cmdlet in PowerShell ISE, senza che sia necessario eseguirlo.

  ![PowerShell e IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stato cmdlet

Rendere senza stato tutti i cmdlet del modulo. Più processi runbook possono essere `AppDomain` eseguiti contemporaneamente nello stesso processo e nella stessa sandbox. Se esiste uno stato condiviso su tali livelli, i processi possono influire l'uno sull'altro. Questo comportamento può causare problemi intermittenti e difficili da diagnosticare. Ecco un esempio di cosa NON fare:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Dipendenza del modulo

Assicurarsi che il modulo sia completamente contenuto in un pacchetto che può essere copiato utilizzando xcopy. I moduli di automazione di Azure vengono distribuiti ai Sandbox di automazione durante l'esecuzione dei runbook. I moduli devono funzionare indipendentemente dall'host che li esegue. 

Dovrebbe essere possibile comprimere la compressione e spostare un pacchetto del modulo e farlo funzionare normalmente quando viene importato nell'ambiente PowerShell di un altro host. A tale scopo, assicurarsi che il modulo non dipenda da file esterni alla cartella del modulo compresso quando il modulo viene importato in Automazione di Azure.For this to happen, ensure that the module doesn't depend on any files outside the module folder that is zipped up when the module is imported into Azure Automation. 

Il modulo non deve dipendere da impostazioni del Registro di sistema univoche in un host. Esempi sono le impostazioni effettuate durante l'installazione di un prodotto. 

### <a name="module-file-paths"></a>Percorsi dei file dei moduli

Assicurarsi che tutti i file nel modulo abbiano percorsi con meno di 140 caratteri. Tutti i percorsi di lunghezza superiore a 140 caratteri causano problemi con l'importazione dei runbook. Automazione di Azure non è possibile importare un file con dimensioni `Import-Module`del percorso superiori a 140 caratteri nella sessione di PowerShell con .

## <a name="importing-modules"></a>Importazione di moduli

Questa sezione definisce diversi modi in cui è possibile importare un modulo nell'account di automazione. 

### <a name="import-modules-in-azure-portal"></a>Importare moduli nel portale di AzureImport modules in Azure portal

Per importare un modulo nel portale di Azure:To import a module in the Azure portal:

1. Passare all'account di Automazione.
2. Selezionare **Moduli** in **Risorse condivise**.
3. Fare clic su **Aggiungi modulo**. 
4. Selezionare il file **.zip** che contiene il modulo.
5. Fare clic **su OK** per avviare l'importazione del processo.

### <a name="import-modules-using-powershell"></a>Importare moduli tramite PowerShellImport modules using PowerShell

È possibile utilizzare il cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) per importare un modulo nell'account di automazione. Il cmdlet accetta un URL per un pacchetto .zip del modulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

È anche possibile usare lo stesso cmdlet per importare direttamente un modulo da PowerShell Gallery.You can also use the same cmdlet to import a module from PowerShell Gallery directly. Assicurarsi di `ModuleName` `ModuleVersion` afferrare e da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importare moduli da PowerShell Gallery

È possibile importare i moduli di [PowerShell Gallery](https://www.powershellgallery.com) direttamente dalla raccolta o dall'account di automazione.

Per importare un modulo direttamente da PowerShell Gallery:To import a module directly from the PowerShell Gallery:

1. Vai https://www.powershellgallery.com a e cerca il modulo da importare.
2. Fare clic su **Distribuisci in Automazione di Azure** nella scheda **Automazione di Azure** in Opzioni di **installazione**. Questa azione apre il portale di Azure.This action opens the Azure portal. 
3. Nella pagina Importa selezionare l'account di automazione e fare clic su **OK**.

![Modulo di importazione di PowerShell Gallery](../media/modules/powershell-gallery.png)

Per importare un modulo di PowerShell Gallery direttamente dall'account di automazione:To import a PowerShell Gallery module directly from your Automation account:

1. Selezionare **Moduli** in **Risorse condivise**. 
2. Nella pagina Moduli fare clic su **Sfoglia raccolta**, quindi cercare un modulo nella raccolta. 
3. Selezionare il modulo da importare e fare clic su **Importa**. 
4. Nella pagina Importa fare clic **su OK** per avviare il processo di importazione.

![Importazione della raccolta di PowerShell dal portale di AzurePowerShell Gallery import from Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Eliminazione di moduli

In caso di problemi con un modulo o se è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminarlo dall'account di Automazione. Non è possibile eliminare le versioni originali dei [moduli predefiniti](#default-modules) importati quando si crea un account di automazione. Se il modulo da eliminare è una versione più recente di uno dei [moduli predefiniti,](#default-modules)viene ripristinato alla versione installata con l'account Automation. In caso contrario, qualsiasi modulo eliminato dall'account Automation viene rimosso.

### <a name="delete-modules-in-azure-portal"></a>Eliminare moduli nel portale di AzureDelete modules in Azure portal

Per rimuovere un modulo nel portale di Azure:To remove a module in the Azure portal:

1. Passare all'account di automazione e selezionare **Moduli** in **Risorse condivise**. 
2. Selezionare il modulo che si desidera rimuovere. 
3. Nella pagina **Modulo** selezionare **Elimina**. Se questo modulo è uno dei [moduli predefiniti,](#default-modules)viene ripristinato alla versione esistente al momento della creazione dell'account di automazione.

### <a name="delete-modules-using-powershell"></a>Eliminare moduli tramite PowerShellDelete modules using PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire il comando seguente:To remove a module through PowerShell, run the following command:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Aggiunta di un tipo di connessione al modulo

È possibile fornire un [tipo di connessione](../automation-connections.md) personalizzato da utilizzare nell'account di automazione aggiungendo un file di metadati facoltativo al modulo. Questo file specifica un tipo di connessione di Automazione di Azure da usare con i cmdlet del modulo nell'account di automazione. Per altre informazioni sulla creazione di un modulo di PowerShell, vedere Come scrivere un modulo di script di [PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Usare una connessione personalizzata nel portale di AzureUse a custom connection in the Azure portal](../media/modules/connection-create-new.png)

Il file che specifica le proprietà del tipo di connessione è denominato ** &lt;&gt;ModuleName -Automation.json** e si trova nella cartella del modulo del file **compresso con estensione zip.** Questo file contiene i campi di una connessione necessari per connettersi al sistema o al servizio rappresentato dal modulo. La configurazione consente la creazione di un tipo di connessione in Automazione di Azure.The configuration allows creation of a connection type in Azure Automation. Utilizzando questo file è possibile impostare i nomi dei campi, i tipi e se i campi sono crittografati o facoltativi per il tipo di connessione del modulo. L'esempio seguente è un modello nel formato di file json che definisce le proprietà del nome utente e della password:The following example is a template in the **.json** file format that defines user name and password properties:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso dei moduli di Azure PowerShell, vedere Introduzione ad Azure PowerShell.For more information about using Azure PowerShell modules, see [Get started with Azure PowerShell.](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)
* Per ulteriori informazioni sulla creazione di moduli di PowerShell, vedere Scrittura di un modulo di [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
