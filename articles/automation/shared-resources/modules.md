---
title: Gestire i moduli nell'automazione di AzureManage Modules in Azure Automation
description: Questo articolo descrive come gestire i moduli in Automazione di AzureThis article describes how to manage modules in Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278337"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli nell'automazione di AzureManage Modules in Azure Automation

Automazione di Azure offre la possibilità di importare moduli di PowerShell nell'account di automazione per essere usati dai runbook basati su PowerShell.Azure Automation provides the ability to import PowerShell modules into your Automation Account to be used by the PowerShell based runbooks. Questi moduli possono essere moduli personalizzati creati, da PowerShell Gallery o dai moduli AzureRM e Az per Azure.These modules can be custom modules you've created, from the PowerShell Gallery, or the AzureRM and Az modules for Azure. Quando si crea un account di automazione, alcuni moduli vengono importati per impostazione predefinita.

## <a name="import-modules"></a>Importare i moduli

Esistono diversi modi per importare un modulo nell'account di automazione. Nelle sezioni seguenti vengono illustrati i diversi modi per importare un modulo.

> [!NOTE]
> Il percorso massimo di un file in un modulo da usare in Automazione di Azure è 140 caratteri. Qualsiasi percorso superiore a 140 caratteri non sarà in grado `Import-Module`di essere importato nella sessione di PowerShell con .

### <a name="powershell"></a>PowerShell

È possibile usare [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) per importare un modulo nell'account di automazione. Il cmdlet accetta un URL per un pacchetto zip del modulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

È anche possibile usare lo stesso cmdlet per importare direttamente un modulo da PowerShell Gallery.You can also use the same cmdlet to import a module from PowerShell Gallery directly. Assicurarsi di acquisire **ModuleName** e **ModuleVersion** da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure passare all'account di automazione e selezionare **Moduli** in **Risorse condivise.** Fare clic su **Aggiungi un modulo**. Selezionare un file **.zip** contenente il modulo e fare clic **su OK** per iniziare a importare il processo.

### <a name="powershell-gallery"></a>PowerShell Gallery

I moduli della raccolta PowerShell possono essere importati direttamente da [PowerShell Gallery](https://www.powershellgallery.com) o dall'account di automazione.

Per importare un modulo da PowerShell https://www.powershellgallery.com Gallery, passare a E cercare il modulo da importare. Fare clic su **Distribuisci in Automazione di Azure** nella scheda **Automazione di Azure** in Opzioni di **installazione**. Verrà aperto il portale di Azure. Nella pagina **Importa** selezionare l'account di automazione e fare clic su **OK**.

![Modulo di importazione di PowerShell Gallery](../media/modules/powershell-gallery.png)

È anche possibile importare moduli da PowerShell Gallery direttamente dall'account di automazione. Nell'account di automazione selezionare **Moduli** in **Risorse condivise**. Nella pagina dei moduli fare clic su **Sfoglia raccolta**, quindi cercare un modulo in PowerShell Gallery. Selezionare il modulo che si desidera importare e fare clic su **Importa**. Nella pagina **Importa** fare clic **su OK** per avviare il processo di importazione.

![Importazione della raccolta di PowerShell dal portale di AzurePowerShell Gallery import from Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Eliminare i moduli

In caso di problemi con un modulo o se è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminarlo dall'account di automazione. Non è possibile eliminare la versione originale dei [moduli predefiniti](#default-modules) importati quando si crea un account di automazione. Se il modulo che si desidera eliminare è una versione più recente di uno dei [moduli predefiniti](#default-modules) installati, verrà eseguito il rollback alla versione installata con l'account di automazione. In caso contrario, qualsiasi modulo eliminato dall'account di automazione verrà rimosso.

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure passare all'account di automazione e selezionare **Moduli** in **Risorse condivise.** Selezionare il modulo che si desidera rimuovere. Nella pagina **Modulo** selezionare **Elimina**. Se questo modulo è uno dei [moduli predefiniti,](#default-modules)verrà eseguito il rollback alla versione presente al momento della creazione dell'account di automazione.

### <a name="powershell"></a>PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire il comando seguente:To remove a module through PowerShell, run the following command:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlet interni

Di seguito è riportato un `Orchestrator.AssetManagement.Cmdlets` elenco di cmdlet nel modulo interno importato in ogni account di automazione. Questi cmdlet sono accessibili nei runbook e nelle configurazioni DSC e consentono di interagire con le risorse all'interno dell'account di automazione. Inoltre, i cmdlet interni consentono di recuperare i segreti dai valori **variabili** crittografati, **Credenziali**e **campi connessione** crittografata. I cmdlet di Azure PowerShell non sono in grado di recuperare questi segreti. Questi cmdlet non richiedono la connessione implicita ad Azure quando vengono usati, ad esempio usando un account RunAs per l'autenticazione in Azure.These cmdlets do not require you to implicitly connect to Azure when using them, such as using a RunAs Account to authenticate to Azure.

>[!NOTE]
>Questi cmdlet interni sono disponibili in un runbook worker ibrido di Windows, non sono disponibili in un ruolo di lavoro ibrido Linux.These internal cmdlets are available on a Windows Hybrid Runbook Worker, they are not available on a Linux Hybrid Runbook Worker. Usare i moduli [di AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) o [Az](../az-modules.md) corrispondenti per i runbook in esecuzione direttamente nel computer o per le risorse nell'ambiente. 
>

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

È possibile fornire un [tipo di connessione](../automation-connections.md) personalizzato da utilizzare nell'account di automazione aggiungendo un file facoltativo al modulo. Questo file è un file di metadati che specifica un tipo di connessione di Automazione di Azure da usare con i cmdlet del modulo nell'account di automazione. A tale scopo, è innanzitutto necessario sapere come creare un modulo di PowerShell.To achieve this, you must first know how to author a PowerShell module. Per ulteriori informazioni sulla creazione di moduli, vedere Come scrivere un modulo di script di [PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Usare una connessione personalizzata nel portale di AzureUse a custom connection in the Azure portal](../media/modules/connection-create-new.png)

Per aggiungere un tipo di connessione di Automazione `<ModuleName>-Automation.json` di Azure, il modulo deve contenere un file con il nome che specifica le proprietà del tipo di connessione. Il file json viene inserito nella cartella del modulo del file compresso .zip. Questo file contiene i campi di una connessione necessaria per connettersi al sistema o al servizio rappresentato dal modulo. La configurazione finisce per creare un tipo di connessione in Automazione di Azure.The configuration ends up creating a connection type in Azure Automation. Utilizzando questo file è possibile impostare i nomi dei campi, i tipi e se i campi devono essere crittografati o facoltativi per il tipo di connessione del modulo. L'esempio seguente è un modello nel formato di file json che definisce una proprietà nome utente e password:The following example is a template in the json file format that defines a username and password property:

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

Questi moduli di PowerShell possono essere importati in Automazione di Azure per rendere i cmdlet e le risorse DSC disponibili per l'uso rispettivamente in runbook e configurazioni DSC. In background Automazione di Azure archivia questi moduli e durante l'esecuzione del processo del runbook e del processo di compilazione DSC li carica nelle sandbox di Automazione di Azure in cui i runbook vengono eseguiti e le configurazioni DSC compilate. Tutte le risorse DSC dei moduli vengono automaticamente inserite nel server di pull di Automation DSC. In questo modo i computer possono eseguirne il pull quando applicano le configurazioni DSC.

Quando si crea un modulo di PowerShell per l'uso in Automazione di Azure, è consigliabile considerare quanto segue:We recommend you consider the following when you author a PowerShell module for use in Azure Automation:

* NON includere una cartella di versione all'interno del pacchetto .zip.  Questo problema è meno di un problema per i runbook, ma causerà un problema con il servizio di configurazione dello stato.  Automazione di Azure creerà automaticamente la cartella della versione quando il modulo viene distribuito ai nodi gestiti da DSC e se esiste una cartella di versione si avranno due istanze.  Esempio di struttura di cartelle per un modulo DSC:Example folder structure for a DSC module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire alcune informazioni della Guida per i cmdlet in modo che gli utenti possano visualizzare informazioni sul relativo uso con il cmdlet **Get-Help** . L'esempio seguente mostra come definire una sinossi e l'URI della Guida per in un file di modulo con estensione psm1:The following example shows how to define a synopsis and help URI for in a .psm1 module file:

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

  Specificando queste informazioni, viene visualizzata la Guida usando il cmdlet **Get-Help** nella console di PowerShell. Questa descrizione viene visualizzata anche nel portale di Azure.This description is also displayed in the Azure portal.

  ![Guida del modulo di integrazione](../media/modules/module-activity-description.png)

* Se il modulo si connette a un servizio esterno, deve contenere un [tipo di connessione](#add-a-connection-type-to-your-module). Ogni cmdlet del modulo, inoltre, deve poter accettare un oggetto connessione (ovvero un'istanza del tipo di connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset di connessione ai parametri corrispondenti del cmdlet ogni volta che chiamano un cmdlet. In base all'esempio di runbook precedente, usa un asset di connessione Contoso di esempio denominato ContosoConnection per accedere alle risorse di Contoso e restituire dati dal servizio esterno.

  Nell'esempio seguente, i campi vengono mappati alle proprietà `PSCredential` UserName e Password di un oggetto e quindi passati al cmdlet.

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

  È possibile abilitare un comportamento simile a quello dell'esempio precedente per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché soltanto i campi connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set `UserAccount`di parametri , viene utilizzato per passare le proprietà del campo di connessione. `ConnectionObject`consente di passare la connessione direttamente attraverso.

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

* Il modulo deve essere completamente contenuto in un pacchetto xcopy-able. I moduli di automazione di Azure vengono distribuiti ai Sandbox di automazione quando è necessario eseguire i runbook. I moduli devono funzionare in modo indipendente dall'host in cui vengono eseguiti. Dovrebbe essere possibile comprimere un pacchetto del modulo e spostarlo, facendo in modo che funzioni normalmente dopo l'importazione nell'ambiente PowerShell di un altro host. Perché ciò avvenga, il modulo non deve dipendere da file all'esterno della cartella del modulo, ovvero della cartella che viene compressa quando il modulo è importato in Automazione di Azure. Il modulo non deve inoltre dipendere da eventuali impostazioni del Registro di sistema univoche di un host, come quelle definite quando viene installato un prodotto. Tutti i file nel modulo devono avere un percorso inferiore a 140 caratteri. Tutti i percorsi superiori a 140 caratteri causeranno problemi di importazione del runbook. Se questa procedura consigliata non viene seguita, il modulo non potrà essere usato in Automazione di Azure.  

* Se si fa riferimento ai [moduli Az di Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nel modulo, assicurarsi che non venga fatto riferimento anche a `AzureRM`. Il modulo `Az` non può essere usato in combinazione con i moduli `AzureRM`. `Az` è supportato nei runbook, ma non viene importato per impostazione predefinita. Per saperne di più sui moduli `Az` e sulle considerazioni di cui tenere conto, vedere [Supporto per i moduli Az in Automazione di Azure](../az-modules.md).

## <a name="default-modules"></a>Moduli predefiniti

Nella tabella seguente sono elencati i moduli importati per impostazione predefinita quando viene creato un account di automazione. I moduli elencati di seguito possono avere versioni più recenti di essi importati, ma la versione originale non può essere rimossa dal tuo account di automazione anche se ne elimini una versione più recente.

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

* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell)
