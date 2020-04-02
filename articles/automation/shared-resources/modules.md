---
title: Gestire i moduli in Automazione di AzureManage modules in Azure Automation
description: Questo articolo descrive come gestire i moduli in Automazione di Azure.This article describes how to manage modules in Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548929"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in Automazione di AzureManage modules in Azure Automation

Automazione di Azure consente l'importazione di moduli di PowerShell nell'account di automazione per essere usata dai runbook basati su PowerShell.Azure Automation allows the import of PowerShell modules into your Automation account to be used by PowerShell-based runbooks. Questi moduli possono essere moduli personalizzati creati, moduli da PowerShell Gallery o moduli AzureRM e Az per Azure.These modules can be custom modules you've created, modules from the PowerShell Gallery, or the AzureRM and Az modules for Azure. Quando si crea un account di automazione, alcuni moduli vengono importati per impostazione predefinita.

## <a name="import-modules"></a>Importare i moduli

Esistono diversi modi per importare un modulo nel tuo account Automation. Nelle sezioni seguenti vengono illustrati i diversi modi per importare un modulo.

> [!NOTE]
> La dimensione massima del percorso per un file in un modulo usato in Automazione di Azure è 140 caratteri. L'automazione non può importare un file con dimensioni del `Import-Module`percorso superiori a 140 caratteri nella sessione di PowerShell con .

### <a name="powershell"></a>PowerShell

È possibile usare il cmdlet [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) per importare un modulo nell'account di automazione. Il cmdlet accetta un URL per un pacchetto .zip del modulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

È anche possibile usare lo stesso cmdlet per importare direttamente un modulo da PowerShell Gallery.You can also use the same cmdlet to import a module from PowerShell Gallery directly. Assicurarsi di `ModuleName` `ModuleVersion` afferrare e da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portale di Azure

Per importare un modulo nel portale di Azure:To import a module in the Azure portal:

1. Passare all'account di Automazione.
2. Selezionare **Moduli** in **Risorse condivise**.
3. Fare clic su **Aggiungi modulo**. 
4. Selezionare il file **.zip** che contiene il modulo.
5. Fare clic **su OK** per avviare l'importazione del processo.

### <a name="powershell-gallery"></a>PowerShell Gallery

È possibile importare i moduli di [PowerShell Gallery](https://www.powershellgallery.com) direttamente dalla raccolta o dall'account di automazione.

Per importare un modulo direttamente da PowerShell Gallery:To import a module directly from the PowerShell Gallery:

1. Vai https://www.powershellgallery.com a e cerca il modulo da importare.
2. Fare clic su **Distribuisci in Automazione di Azure** nella scheda **Automazione di Azure** in Opzioni di **installazione**. Verrà aperto il portale di Azure. 
3. Nella pagina Importa selezionare l'account di automazione e fare clic su **OK**.

![Modulo di importazione di PowerShell Gallery](../media/modules/powershell-gallery.png)

Per importare un modulo di PowerShell Gallery direttamente dall'account di automazione:To import a PowerShell Gallery module directly from your Automation account:

1. Selezionare **Moduli** in **Risorse condivise**. 
2. Nella pagina Moduli fare clic su **Sfoglia raccolta**, quindi cercare un modulo nella raccolta. 
3. Selezionare il modulo da importare e fare clic su **Importa**. 
4. Nella pagina Importa fare clic **su OK** per avviare il processo di importazione.

![Importazione della raccolta di PowerShell dal portale di AzurePowerShell Gallery import from Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Eliminare i moduli

In caso di problemi con un modulo o se è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminarlo dall'account di Automazione. Non è possibile eliminare le versioni originali dei [moduli predefiniti](#default-modules) importati quando si crea un account di automazione. Se il modulo da eliminare è una versione più recente di uno dei [moduli predefiniti,](#default-modules)viene ripristinato alla versione installata con l'account Automation. In caso contrario, qualsiasi modulo eliminato dall'account Automation viene rimosso.

### <a name="azure-portal"></a>Portale di Azure

Per rimuovere un modulo nel portale di Azure:To remove a module in the Azure portal:

1. Passare all'account di automazione e selezionare **Moduli** in **Risorse condivise**. 
2. Selezionare il modulo che si desidera rimuovere. 
3. Nella pagina **Modulo** selezionare **Elimina**. Se questo modulo è uno dei [moduli predefiniti,](#default-modules)viene ripristinato alla versione esistente al momento della creazione dell'account di automazione.

### <a name="powershell"></a>PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire il comando seguente:To remove a module through PowerShell, run the following command:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlet interni

Nella tabella seguente sono elencati i cmdlet nel modulo interno `Orchestrator.AssetManagement.Cmdlets` importato in ogni account di automazione. Questi cmdlet sono accessibili nei runbook e nelle configurazioni DSC e consentono di interagire con le risorse all'interno dell'account Di automazione. Inoltre, i cmdlet interni consentono di recuperare i segreti da variabili crittografate, credenziali e connessioni crittografate. I cmdlet di Azure PowerShell non sono in grado di recuperare questi segreti. Questi cmdlet non richiedono la connessione implicita ad Azure quando vengono usati, come quando si usa un account RunAs per l'autenticazione in Azure.These cmdlets do not require you to implicitly connect to Azure when using them, as when using them, as when using a RunAs Account to authenticate to Azure.

>[!NOTE]
>Questi cmdlet interni sono disponibili in un ruolo di lavoro ibrido di Windows, ma non in un ruolo di lavoro per runbook ibrido Linux.These internal cmdlets are available on a Windows Hybrid Runbook Worker, but not on a Linux Hybrid Runbook Worker. Usare i cmdlet del [modulo](../az-modules.md) [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) o Az corrispondenti per i runbook in esecuzione direttamente nel computer o per le risorse nell'ambiente. 

|Nome|Descrizione|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Aggiungere un tipo di connessione al modulo

È possibile fornire un [tipo di connessione](../automation-connections.md) personalizzato da utilizzare nell'account di automazione aggiungendo un file di metadati facoltativo al modulo. Questo file specifica un tipo di connessione di Automazione di Azure da usare con i cmdlet del modulo nell'account di automazione. A tale scopo, è innanzitutto necessario sapere come creare un modulo di PowerShell.To achieve this, you must first know how to author a PowerShell module. Vedere Come scrivere un modulo di script di [PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

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

## <a name="module-best-practices"></a>Procedure consigliate per i moduli

È possibile importare moduli di PowerShell in Automazione di Azure per rendere disponibili i cmdlet all'interno dei runbook e delle relative risorse DSC disponibili nelle configurazioni DSC. Dietro le quinte, Automazione di Azure archivia questi moduli. Al processo di runbook e al tempo di esecuzione del processo di compilazione DSC, l'automazione li carica nelle sandbox di Automazione di Azure in cui vengono eseguiti i runbook e vengono compilate le configurazioni DSC. Tutte le risorse DSC dei moduli vengono automaticamente inserite nel server di pull di Automation DSC. Le macchine possono estrarle quando applicano configurazioni DSC.

Quando si crea un modulo di PowerShell per l'uso in Automazione di Azure, è consigliabile considerare quanto segue:We recommend that you consider the following when you author a PowerShell module for use in Azure Automation:

* NON includere una cartella di versione all'interno del pacchetto **.zip.**  Questo problema è meno di un problema per i runbook, ma causa un problema con il servizio di configurazione dello stato. Automazione di Azure crea automaticamente la cartella della versione quando il modulo viene distribuito ai nodi gestiti da DSC. Se esiste una cartella di versione, si finisce con due istanze. Di seguito è riportato un esempio di struttura di cartelle per un modulo DSC:Here is an example folder structure for a DSC module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire informazioni della `Get-Help` Guida per i cmdlet utilizzando il cmdlet. L'esempio seguente mostra come definire una sinossi e un URI della Guida in un file di modulo con estensione psm1:The following example shows how to define a synopsis and help URI in a **.psm1** module file:

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

* Se il modulo si connette a un servizio esterno, definire un [tipo di connessione](#add-a-connection-type-to-your-module). Ogni cmdlet nel modulo deve accettare un oggetto connessione (un'istanza di tale tipo di connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset di connessione ai parametri corrispondenti del cmdlet ogni volta che chiamano un cmdlet. In base all'esempio di runbook precedente, `ContosoConnection` usa un asset di connessione Contoso di esempio chiamato per accedere alle risorse di Contoso e restituire i dati dal servizio esterno.

  Nell'esempio seguente, i campi vengono `UserName` `Password` mappati `PSCredential` alle proprietà e di un oggetto e quindi passati al cmdlet.

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

  È possibile abilitare un comportamento simile per i cmdlet consentendo loro di accettare un oggetto connessione direttamente come parametro, anziché solo i campi di connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set `UserAccount`di parametri , viene utilizzato per passare le proprietà del campo di connessione. `ConnectionObject`consente di passare la connessione direttamente attraverso.

* Definire il tipo di output per tutti i cmdlet nel modulo. Definendo un tipo di output per un cmdlet, IntelliSense in fase di progettazione consente di determinare le proprietà di output del cmdlet da usare durante la creazione. È particolarmente utile durante la creazione grafica di runbook di automazione, in cui la conoscenza in fase di progettazione è essenziale per facilitare l'esperienza dell'utente con il modulo.

Aggiungere `[OutputType([<MyOutputType>])]` dove MyOutputType è un tipo valido. Per ulteriori informazioni su OutputType, vedere [Informazioni sulle funzioni OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è `OutputType` un esempio di aggiunta a un cmdlet:

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

* Assicurarsi che tutti i cmdlet del modulo siano senza stato. Più processi runbook possono essere eseguiti contemporaneamente nello stesso AppDomain e nello stesso processo e sandbox. Se esiste uno stato condiviso su tali livelli, i processi possono influire l'uno sull'altro. Questo comportamento può causare problemi intermittenti e difficili da diagnosticare.  Di seguito è riportato un esempio della soluzione da non adottare.

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

* Il modulo deve essere completamente contenuto in un pacchetto xcopy-able. I moduli di automazione di Azure vengono distribuiti ai Sandbox di automazione quando è necessario eseguire i runbook. I moduli devono funzionare in modo indipendente dall'host in cui vengono eseguiti. Dovrebbe essere possibile comprimere la compressione e spostare un pacchetto del modulo e farlo funzionare normalmente quando viene importato nell'ambiente PowerShell di un altro host. A tale scopo, il modulo non deve dipendere da file esterni alla cartella del modulo che viene compresso quando il modulo viene importato in Automazione di Azure.For this to happen, the module shouldn't depend on any files outside the module folder that is zipped up when the module is imported into Azure Automation. Il modulo non deve inoltre dipendere da eventuali impostazioni del Registro di sistema univoche di un host, come quelle definite quando viene installato un prodotto. Tutti i file nel modulo devono avere un percorso inferiore a 140 caratteri. Tutti i percorsi superiori a 140 caratteri causano problemi con l'importazione del runbook. Se non si segue questa procedura consigliata, il modulo non è utilizzabile in Automazione di Azure.If you don't follow this best practice, the module is not usable in Azure Automation.  

* Se si fa riferimento al [modulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nel modulo, `AzureRM`assicurarsi che non si fa riferimento anche a . Non è possibile `Az` utilizzare il modulo `AzureRM` insieme al modulo. `Az`è supportato nei runbook ma non viene importato per impostazione predefinita. Per informazioni `Az` sul modulo e sulle considerazioni da prendere in considerazione, vedere Supporto del [modulo Az in Automazione di Azure.](../az-modules.md)

## <a name="default-modules"></a>Moduli predefiniti

Nella tabella seguente sono elencati i moduli importati per impostazione predefinita quando viene creato un account di automazione. L'automazione può importare versioni più recenti di questi moduli. Tuttavia, non puoi rimuovere la versione originale dal tuo account Automation anche se elimini una versione più recente.

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

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla creazione di moduli di PowerShell, vedere Scrittura di un modulo di [Windows PowerShell](/powershell/scripting/developer/windows-powershell).
