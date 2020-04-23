---
title: Gestire i moduli in Automazione di Azure
description: Questo articolo descrive come gestire i moduli in automazione di Azure.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 995c87ca6f091e9ccf0b82af831bbf43ff17846f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100839"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in Automazione di Azure

Automazione di Azure consente di importare i moduli di PowerShell per abilitare i cmdlet in manuali operativi e nelle risorse DSC nelle configurazioni DSC. I moduli usati in automazione di Azure includono:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Modulo `Orchestrator.AssetManagement.Cmdlets` interno per l'agente di log Analytics per Windows
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Altri moduli di PowerShell
* Moduli personalizzati creati 

Quando si crea un account di automazione, automazione di Azure importa alcuni moduli per impostazione predefinita. Vedere [moduli predefiniti](#default-modules).

Quando automazione di Azure esegue i processi di compilazione Runbook e DSC, carica i moduli in sandbox in cui è possibile eseguire manuali operativi e le configurazioni DSC possono essere compilate. Automazione inserisce automaticamente anche eventuali risorse DSC nei moduli nel server di pull DSC. I computer possono effettuare il pull delle risorse quando applicano le configurazioni DSC.

>[!NOTE]
>Assicurarsi di importare solo i moduli effettivamente necessari per le configurazioni manuali operativi e DSC. Non è consigliabile importare la radice AZ Module perché include molti altri moduli che potrebbero non essere necessari, che possono causare problemi di prestazioni. Importare invece singoli moduli, ad esempio AZ. Compute.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Moduli predefiniti

La tabella seguente elenca i moduli importati da automazione di Azure per impostazione predefinita quando si crea l'account di automazione. L'automazione può importare versioni più recenti di questi moduli. Tuttavia, non è possibile rimuovere la versione originale dall'account di automazione, anche se si elimina una versione più recente. Si noti che questi moduli predefiniti includono diversi moduli AzureRM. 

I moduli AZ. Automation sono preferiti nelle configurazioni manuali operativi e DSC. Tuttavia, automazione di Azure non importa automaticamente il modulo AZ radice in tutti gli account di automazione nuovi o esistenti. Per ulteriori informazioni sull'utilizzo di questi moduli, vedere la pagina relativa [alla migrazione a AZ Modules](#migrating-to-az-modules).

> [!NOTE]
> Non è consigliabile modificare i moduli e manuali operativi negli account di automazione che contengono la [soluzione avvio/arresto di macchine virtuali durante gli orari di minore attività in automazione di Azure](../automation-solution-vm-management.md).

|Nome del modulo|Versione|
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
| Agente di orchestrazione. AssetManagement. Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="internal-cmdlets"></a>Cmdlet interni

La tabella seguente definisce i cmdlet interni supportati dal `Orchestrator.AssetManagement.Cmdlets` modulo. Usare questi cmdlet nelle configurazioni manuali operativi e DSC per interagire con gli asset di Azure nell'account di automazione. I cmdlet sono progettati per essere usati al posto dei cmdlet di Azure PowerShell per recuperare i segreti da variabili crittografate, credenziali e connessioni crittografate. 

>[!NOTE]
>I cmdlet interni sono disponibili solo quando si esegue manuali operativi nell'ambiente sandbox di Azure o in un ruolo di lavoro ibrido per Runbook Windows. 

|Nome|Descrizione|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Si noti che i cmdlet interni differiscono per la denominazione dei cmdlet AZ e AzureRM. I nomi dei cmdlet interni non contengono parole come "Azure" o "AZ" nel sostantivo, ma usano la parola "automazione". È consigliabile usare i cmdlet AZ o AzureRM durante l'esecuzione di Runbook in una sandbox di Azure o in un ruolo di lavoro ibrido di Windows. Richiedono un minor numero di parametri ed eseguiti nel contesto del processo già in esecuzione.

Usare i cmdlet AZ o AzureRM per modificare le risorse di automazione di Azure al di fuori del contesto di un Runbook. In questi casi, è necessario connettersi in modo implicito ad Azure quando si usano i cmdlet, come quando si usa un account RunAs per l'autenticazione in Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Moduli che supportano Get-AutomationPSCredential

Per lo sviluppo locale con Azure Automation Authoring Toolkit, il `Get-AutomationPSCredential` cmdlet fa parte dell'assembly [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Per il funzionamento di Azure con il contesto di automazione, il `Orchestrator.AssetManagement.Cmdlets`cmdlet si trova in. Per altre informazioni sull'uso delle credenziali in automazione di Azure, vedere [asset delle credenziali in automazione di Azure](credentials.md).

Si noti `Get-AutomationPsCredential` che restituisce `PSCredential` un oggetto, che è previsto dalla maggior parte dei cmdlet di PowerShell che funzionano con le credenziali. In genere, è consigliabile usare questo cmdlet anziché il cmdlet [Get-AzAutomationCredential](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationcredential?view=azps-3.8.0) . `Get-AzAutomationCredential`Recupera un oggetto [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) contenente i metadati relativi alla credenziale. Queste informazioni non sono in genere utili per passare a un altro cmdlet.

## <a name="migrating-to-az-modules"></a>Migrazione a moduli Az

Quando si usa il comando AZ Modules in automazione di Azure, è necessario prendere in considerazione diversi aspetti:

* Non è consigliabile eseguire i moduli AzureRM e AZ Modules nello stesso account di automazione, in quanto è garantito che causi problemi. Vedere [eseguire la migrazione di Azure PowerShell da AzureRM a AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

* Assicurarsi di testare attentamente tutte le configurazioni manuali operativi e DSC in un account di automazione separato prima di importare i moduli AZ. 

* L'importazione di un modulo AZ nell'account di automazione non comporta l'importazione automatica del modulo nella sessione di PowerShell utilizzata da manuali operativi. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

    * Quando un Runbook richiama un cmdlet da un modulo
    * Quando un Runbook importa il modulo in modo esplicito con il cmdlet [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
    * Quando un Runbook importa un altro modulo dipendente

Dopo aver completato la migrazione dei moduli, non provare a avviare manuali operativi usando i moduli AzureRM nell'account di automazione. È inoltre consigliabile non importare o aggiornare i moduli AzureRM nell'account. Si consideri l'account migrato a AZ. Automation e si operi solo con AZ modules.

>[!IMPORTANT]
>Quando si crea un nuovo account di automazione, automazione di Azure installa i moduli AzureRM per impostazione predefinita. È comunque possibile aggiornare l'esercitazione manuali operativi con i cmdlet di AzureRM. Tuttavia, non è consigliabile eseguire questi manuali operativi.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Arrestare e depianificare tutti i manuali operativi che usano i moduli AzureRM

Per assicurarsi che non vengano eseguiti manuali operativi esistenti che usano moduli AzureRM, arrestare e rimuovere la pianificazione di tutti i manuali operativi interessati usando il cmdlet [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . È importante esaminare ogni pianificazione separatamente per assicurarsi che sia possibile ripianificarla in futuro per la manuali operativi, se necessario.

```powershell
Get-AzureRmAutomationSchedule -AutomationAccountName "Contoso17" -Name "DailySchedule08" -ResourceGroupName "ResourceGroup01" 
Remove-AzureRmAutomationSchedule -AutomationAccountName "Contoso17" -Name "DailySchedule08" -ResourceGroupName "ResourceGroup01"
```

### <a name="import-the-az-modules"></a>Importare moduli Az

Questa sezione illustra come importare i moduli AZ nel portale di Azure. Ricordarsi di importare solo i moduli AZ necessari, non l'intero modulo AZ. Automation. Poiché [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) è una dipendenza per gli altri moduli AZ, assicurarsi di importare questo modulo prima di qualsiasi altro.

1. Dall'account di automazione selezionare **moduli** in **risorse condivise**. 
2. Fare clic su **Esplora raccolta** per aprire la pagina Esplora raccolta.  
3. Nella barra di ricerca immettere il nome del modulo, ad esempio `Az.Accounts`. 
4. Nella pagina modulo di PowerShell fare clic su **Importa** per importare il modulo nell'account di automazione.

    ![Importa i moduli nell'account di automazione](../media/modules/import-module.png)

Questo processo di importazione può essere eseguito anche tramite il [PowerShell Gallery](https://www.powershellgallery.com) cercando il modulo da importare. Dopo aver individuato il modulo, selezionarlo, scegliere la scheda **automazione di Azure** e fare clic su **Distribuisci in automazione di Azure**.

![Importare moduli direttamente dalla raccolta](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testare i runbook

Dopo aver importato i moduli AZ nell'account di automazione, è possibile iniziare a modificare il manuali operativi per usare i nuovi moduli. La maggior parte dei cmdlet ha lo stesso nome dei moduli AzureRM, ad eccezione del fatto che il prefisso AzureRm è stato modificato in AZ. Per un elenco di moduli che non seguono questa convenzione di denominazione, vedere l' [elenco delle eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un modo per testare la modifica di un Runbook per usare i nuovi cmdlet consiste nell'usare `Enable-AzureRmAlias -Scope Process` all'inizio di Runbook. Aggiungendo questo comando al Runbook, lo script può essere eseguito senza modifiche. 

## <a name="authoring-modules"></a>Creazione di moduli

Si consiglia di seguire le considerazioni in questa sezione quando si crea un modulo di PowerShell da usare in automazione di Azure.

### <a name="references-to-azurerm-and-az"></a>Riferimenti a AzureRM e AZ

Se si fa riferimento ai moduli AZ nel modulo, assicurarsi che non vi siano anche riferimenti ai moduli AzureRM. Non è possibile usare contemporaneamente entrambi i set di moduli. 

### <a name="version-folder"></a>Cartella della versione

Non includere una cartella Version nel pacchetto **zip** per il modulo.  Questo problema è meno problematico per manuali operativi, ma causa un problema con il servizio di configurazione dello stato (DSC). Automazione di Azure crea automaticamente la cartella della versione quando il modulo viene distribuito ai nodi gestiti da DSC. Se esiste una cartella della versione, vengono rilevate due istanze. Di seguito è riportato un esempio di struttura di cartelle per un modulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informazioni della Guida

Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire le informazioni della Guida per i cmdlet usando `Get-Help` il cmdlet. Nell'esempio seguente viene illustrato come definire un riepilogo e un URI della Guida in un file di modulo con **estensione psm1** .

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

  Fornendo queste informazioni, il testo della Guida `Get-Help` viene visualizzato tramite il cmdlet nella console di PowerShell. Questo testo viene visualizzato anche nell'portale di Azure.

  ![Guida del modulo di integrazione](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo di connessione

Se il modulo si connette a un servizio esterno, definire un [tipo di connessione](#adding-a-connection-type-to-your-module). Ogni cmdlet del modulo deve accettare un oggetto Connection (un'istanza del tipo di connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset di connessione ai parametri corrispondenti del cmdlet ogni volta che chiamano un cmdlet. Il seguente esempio di Runbook, basato sull'esempio nella sezione precedente, usa un asset di connessione contoso denominato `ContosoConnection` per accedere alle risorse di Contoso e restituire i dati dal servizio esterno. In questo esempio, i campi vengono mappati alle `UserName` proprietà `Password` e di un `PSCredential` oggetto e quindi passati al cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Un modo più semplice e migliore per affrontare questo comportamento è passare direttamente l'oggetto connessione al cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  È possibile abilitare un comportamento simile per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché solo i campi di connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set `UserAccount` di parametri viene utilizzato per passare le proprietà dei campi della connessione. `ConnectionObject`consente di passare direttamente la connessione.

### <a name="output-type"></a>Tipo di output

Definire il tipo di output per tutti i cmdlet nel modulo. La definizione di un tipo di output per un cmdlet consente a IntelliSense in fase di progettazione di determinare le proprietà di output del cmdlet durante la creazione. Questa pratica è particolarmente utile durante la creazione di Runbook grafici, per cui le informazioni in fase di progettazione sono fondamentali per un'esperienza utente semplice con il modulo.

Aggiungere `[OutputType([<MyOutputType>])]` Where `MyOutputType` è un tipo valido. Per altre informazioni su `OutputType`, vedere [About functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è un esempio di aggiunta `OutputType` a un cmdlet:

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

### <a name="cmdlet-state"></a>Stato del cmdlet

Rendere tutti i cmdlet nel modulo senza stato. Più processi Runbook possono essere eseguiti contemporaneamente nello stesso `AppDomain` processo e nella stessa sandbox. Se è presente uno stato condiviso in questi livelli, i processi possono interessare l'uno con l'altro. Questo comportamento può causare problemi intermittenti e difficili da diagnosticare. Ecco un esempio delle operazioni da eseguire:

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

Verificare che il modulo sia completamente contenuto in un pacchetto che può essere copiato tramite Xcopy. I moduli di automazione di Azure vengono distribuiti nelle sandbox di automazione durante l'esecuzione di manuali operativi. I moduli devono funzionare indipendentemente dall'host che li esegue. 

Si dovrebbe essere in grado di comprimere e spostare un pacchetto del modulo e di fare in modo che funzioni come di consueto quando viene importato nell'ambiente PowerShell di un altro host. Per eseguire questa operazione, verificare che il modulo non dipenda da alcun file esterno alla cartella del modulo compresso quando il modulo viene importato in automazione di Azure. 

Il modulo non deve dipendere da impostazioni univoche del registro di sistema in un host. Esempi sono le impostazioni che vengono eseguite quando un prodotto è installato. 

### <a name="module-file-paths"></a>Percorsi dei file di modulo

Verificare che tutti i file del modulo includano percorsi con meno di 140 caratteri. Qualsiasi percorso con lunghezza superiore a 140 caratteri provoca problemi di importazione manuali operativi. Automazione di Azure non può importare un file con una dimensione del percorso superiore a 140 caratteri `Import-Module`nella sessione di PowerShell con.

## <a name="importing-modules"></a>Importazione di moduli

Questa sezione definisce diversi modi in cui è possibile importare un modulo nell'account di automazione. 

### <a name="import-modules-in-azure-portal"></a>Importazione di moduli in portale di Azure

Per importare un modulo nel portale di Azure:

1. Passare all'account di Automazione.
2. Selezionare **moduli** in **risorse condivise**.
3. Fare clic su **Aggiungi modulo**. 
4. Selezionare il file **zip** contenente il modulo.
5. Fare clic su **OK** per avviare l'importazione del processo.

### <a name="import-modules-using-powershell"></a>Importare moduli con PowerShell

È possibile usare il cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) per importare un modulo nell'account di automazione. Il cmdlet accetta un URL per un pacchetto con estensione zip del modulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

È anche possibile usare lo stesso cmdlet per importare un modulo direttamente da PowerShell Gallery. Assicurarsi di acquisire `ModuleName` e `ModuleVersion` da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importa moduli da PowerShell Gallery

È possibile importare moduli di [PowerShell Gallery](https://www.powershellgallery.com) direttamente dalla raccolta o dal proprio account di automazione.

Per importare un modulo direttamente dalla PowerShell Gallery:

1. Passare a https://www.powershellgallery.com e cercare il modulo da importare.
2. Fare clic su **Distribuisci in automazione di Azure** nella scheda **automazione di Azure** in **Opzioni di installazione**. Questa azione consente di aprire il portale di Azure. 
3. Nella pagina Importa selezionare l'account di automazione e fare clic su **OK**.

![Modulo di importazione PowerShell Gallery](../media/modules/powershell-gallery.png)

Per importare un modulo PowerShell Gallery direttamente dall'account di automazione:

1. Selezionare **moduli** in **risorse condivise**. 
2. Nella pagina moduli fare clic su **Esplora raccolta**, quindi cercare un modulo nella raccolta. 
3. Selezionare il modulo da importare e fare clic su **Importa**. 
4. Nella pagina Importa fare clic su **OK** per avviare il processo di importazione.

![Importazione PowerShell Gallery da portale di Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Eliminazione di moduli

Se si verificano problemi con un modulo o è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminarlo dall'account di automazione. Non è possibile eliminare le versioni originali dei [moduli predefiniti](#default-modules) che vengono importati quando si crea un account di automazione. Se il modulo da eliminare è una versione più recente di uno dei [moduli predefiniti](#default-modules), viene eseguito il rollback alla versione installata con l'account di automazione. In caso contrario, viene rimosso qualsiasi modulo eliminato dall'account di automazione.

### <a name="delete-modules-in-azure-portal"></a>Elimina i moduli in portale di Azure

Per rimuovere un modulo nel portale di Azure:

1. Passare all'account di automazione e selezionare **moduli** in **risorse condivise**. 
2. Selezionare il modulo che si desidera rimuovere. 
3. Nella pagina **modulo** selezionare **Elimina**. Se il modulo è uno dei [moduli predefiniti](#default-modules), viene eseguito il rollback alla versione esistente al momento della creazione dell'account di automazione.

### <a name="delete-modules-using-powershell"></a>Eliminare moduli con PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire il comando seguente:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Aggiunta di un tipo di connessione al modulo

È possibile specificare un [tipo di connessione](../automation-connections.md) personalizzato da usare nell'account di automazione aggiungendo un file di metadati facoltativo al modulo. Questo file specifica un tipo di connessione di automazione di Azure da usare con i cmdlet del modulo nell'account di automazione. Per altre informazioni sulla creazione di un modulo di PowerShell, vedere [come scrivere un modulo di script di PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Usare una connessione personalizzata nel portale di Azure](../media/modules/connection-create-new.png)

Il file che specifica le proprietà del tipo di connessione è denominato ** &lt;ModuleName&gt;-Automation. JSON** ed è disponibile nella cartella del modulo del file **zip** compresso. Questo file contiene i campi di una connessione necessari per la connessione al sistema o al servizio rappresentato dal modulo. La configurazione consente la creazione di un tipo di connessione in automazione di Azure. Con questo file è possibile impostare i nomi dei campi, i tipi e se i campi sono crittografati o facoltativi per il tipo di connessione del modulo. L'esempio seguente è un modello nel formato di file **JSON** che definisce le proprietà di nome utente e password:

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

* Per ulteriori informazioni sull'utilizzo dei moduli di Azure PowerShell, vedere [Introduzione a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [scrittura di un modulo di Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
