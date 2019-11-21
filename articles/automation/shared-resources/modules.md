---
title: Manage Modules in Azure Automation
description: This article describes how to manage modules in Azure Automation
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 492dd182c782b0f6375c2f857cfa4921b065c546
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231583"
---
# <a name="manage-modules-in-azure-automation"></a>Manage Modules in Azure Automation

Azure Automation provides the ability to import PowerShell modules into your Automation Account to be used by the PowerShell based runbooks. These modules can be custom modules you've created, from the PowerShell Gallery, or the AzureRM and Az modules for Azure. When you create an Automation Account, some modules are imported by default.

## <a name="import-modules"></a>Importare i moduli

There are multiple ways that you can import a module into your Automation Account. The following sections show the different ways to import a module.

> [!NOTE]
> The max path of a file in a module to be used in Azure Automation is 140 characters. Any path over 140 characters will not be able to be imported into the PowerShell session with `Import-Module`.

### <a name="powershell"></a>PowerShell

You can use the [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) to import a module into your Automation Account. The cmdlet takes a url to a module zip package.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portale di Azure

In the Azure portal, navigate to your Automation Account and select **Modules** under **Shared Resources**. Click **+ Add a module**. Select a **.zip** file that contains your module and click **Ok** to start to import process.

### <a name="powershell-gallery"></a>PowerShell Gallery

Modules from the PowerShell gallery can either be imported from the [PowerShell Gallery](https://www.powershellgallery.com) directly or from your Automation Account.

To import a module from the PowerShell Gallery, go to https://www.powershellgallery.com and search for the module you want to import. Click **Deploy to Azure Automation** on the **Azure Automation** tab under **Installation Options**. Verrà aperto il portale di Azure. On the **Import** page, select your Automation Account and click **OK**.

![PowerShell Gallery import module](../media/modules/powershell-gallery.png)

You can also import modules from the PowerShell Gallery directly from your Automation Account. In your Automation Account, select **Modules** under **Shared Resources**. On the modules page click **Browse gallery**, then search the PowerShell Gallery for a module. Select the module you want to import and click **Import**. On the **Import** page, click **OK** to start the import process.

![PowerShell Gallery import from Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Delete modules

If you have issues with a module or you need to roll back to a previous version of a module, you can delete it from your Automation Account. You can not delete the original version of the [default modules](#default-modules) that are imported when you create an Automation Account. If the module you want to delete is a newer version of one of the [default modules](#default-modules) installed, it will roll-back to the version that was installed with your Automation Account. Otherwise, any module you delete from your Automation Account will be removed.

### <a name="azure-portal"></a>Portale di Azure

In the Azure portal, navigate to your Automation Account and select **Modules** under **Shared Resources**. Select the module you want to remove. On the **Module** page, clcick **Delete**. If this module is one of the [default modules](#default-modules), it will be rolled back to the version that was present when the Automation Account was created.

### <a name="powershell"></a>PowerShell

To remove a module through PowerShell, run the following command:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Internal cmdlets

The following is a listing of cmdlets in the internal `Orchestrator.AssetManagement.Cmdlets` module that is imported into every Automation Account. These cmdlets are accessible in your runbooks and DSC configurations and allow you to interact with your assets within your Automation Account. Additionally, the internal cmdlets allow you to retrieve secrets from encrypted **Variable** values, **Credentials**, and encrypted **Connection** fields. The Azure PowerShell cmdlets are not able to retrieve these secrets. These cmdlets do not require you to implicitly connect to Azure when using them, such as using a Run As Account to authenticate to Azure.

|name|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Add a connection type to your module

You can provide a custom [connection type](../automation-connections.md) for you to use in your Automation Account by adding an optional file to your module. This file is a metadata file specifying an Azure Automation connection type to be used with the module's cmdlets in your Automation Account. To achieve this, you must first know how to author a PowerShell module. For more information on module authoring, see [How to Write a PowerShell Script Module](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Use a custom connection in the Azure portal](../media/modules/connection-create-new.png)

To add an Azure Automation connection type, your module must contain a file with the name `<ModuleName>-Automation.json` that specifies the connection type properties. The json file is placed within the module folder of your compressed .zip file. This file contains the fields of a connection that is required to connect to the system or service the module represents. The configuration ends up creating a connection type in Azure Automation. Using this file you can set the field names, types, and whether the fields should be encrypted or optional, for the connection type of the module. The following example is a template in the json file format that defines a username and password property:

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

## <a name="module-best-practices"></a>Module best practices

Questi moduli di PowerShell possono essere importati in Automazione di Azure per rendere i cmdlet e le risorse DSC disponibili per l'uso rispettivamente in runbook e configurazioni DSC. In background Automazione di Azure archivia questi moduli e durante l'esecuzione del processo del runbook e del processo di compilazione DSC li carica nelle sandbox di Automazione di Azure in cui i runbook vengono eseguiti e le configurazioni DSC compilate. Tutte le risorse DSC dei moduli vengono automaticamente inserite nel server di pull di Automation DSC. In questo modo i computer possono eseguirne il pull quando applicano le configurazioni DSC.

We recommend you consider the following when you author a PowerShell module for use in Azure Automation:

* Do NOT include a version folder within the .zip package.  This issue is less of a concern for runbooks but will cause an issue with the State Configuration service.  Azure Automation will create the version folder automatically when the module is distributed to nodes managed by DSC, and if a version folder exists you will end up with two instances.  Example folder structure for a  DSC module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire alcune informazioni della Guida per i cmdlet in modo che gli utenti possano visualizzare informazioni sul relativo uso con il cmdlet **Get-Help** . The following example shows how to define a synopsis and help URI for in a .psm1 module file:

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

  Specificando queste informazioni, viene visualizzata la Guida usando il cmdlet **Get-Help** nella console di PowerShell. This description is also displayed in the Azure portal.

  ![Guida del modulo di integrazione](../media/modules/module-activity-description.png)

* If the module connects to an external service, it should contain a [connection type](#add-a-connection-type-to-your-module). Ogni cmdlet del modulo, inoltre, deve poter accettare un oggetto connessione (ovvero un'istanza del tipo di connessione) come parametro. Users map parameters of the connection asset to the cmdlet's corresponding parameters each time they call a cmdlet. Based on the runbook example above, it uses an example Contoso connection asset called ContosoConnection to access Contoso resources and return data from the external service.

  In the following example, the fields are mapped to the UserName and Password properties of a `PSCredential` object and then passed to the cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  An easier and better way to approach this behavior is directly passing the connection object to the cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  È possibile abilitare un comportamento simile a quello dell'esempio precedente per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché soltanto i campi connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. The parameter set `UserAccount`, is used to pass the connection field properties. `ConnectionObject` lets you pass the connection straight through.

* Define the output type for all cmdlets in the module. Definendo un tipo di output per un cmdlet, IntelliSense in fase di progettazione consente di determinare le proprietà di output del cmdlet da usare durante la creazione. È particolarmente utile durante la creazione grafica di runbook di automazione, in cui la conoscenza in fase di progettazione è essenziale per facilitare l'esperienza dell'utente con il modulo.

Add `[OutputType([<MyOutputType>])]` where MyOutputType is a valid type. To learn more about OutputType, see [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). The following code is an example of adding `OutputType` to a cmdlet:

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

* Assicurarsi che tutti i cmdlet del modulo siano senza stato. Multiple runbook jobs can simultaneously run in the same AppDomain and the same process and sandbox. If there is any state shared on those levels, jobs can affect each other. This behavior can lead to intermittent and hard to diagnose issues.  Di seguito è riportato un esempio della soluzione da non adottare.

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

* The module should be fully contained in an xcopy-able package. Azure Automation modules are distributed to the Automation sandboxes when runbooks need to execute. I moduli devono funzionare in modo indipendente dall'host in cui vengono eseguiti. Dovrebbe essere possibile comprimere un pacchetto del modulo e spostarlo, facendo in modo che funzioni normalmente dopo l'importazione nell'ambiente PowerShell di un altro host. Perché ciò avvenga, il modulo non deve dipendere da file all'esterno della cartella del modulo, ovvero della cartella che viene compressa quando il modulo è importato in Automazione di Azure. Il modulo non deve inoltre dipendere da eventuali impostazioni del Registro di sistema univoche di un host, come quelle definite quando viene installato un prodotto. All files in the module should have a path fewer than 140 characters. Any paths over 140 characters will cause issues importing your runbook. Se questa procedura consigliata non viene seguita, il modulo non potrà essere usato in Automazione di Azure.  

* Se si fa riferimento ai [moduli Az di Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nel modulo, assicurarsi che non venga fatto riferimento anche a `AzureRM`. Il modulo `Az` non può essere usato in combinazione con i moduli `AzureRM`. `Az` è supportato nei runbook, ma non viene importato per impostazione predefinita. Per saperne di più sui moduli `Az` e sulle considerazioni di cui tenere conto, vedere [Supporto per i moduli Az in Automazione di Azure](../az-modules.md).

## <a name="default-modules"></a>Default modules

The following table lists the modules that are imported by default when an Automation Account is created. The modules listed below can have newer versions of them imported, but the original version can not be removed from your Automation Account even if you delete a newer version of them.

|Module name|Versione|
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
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell)
