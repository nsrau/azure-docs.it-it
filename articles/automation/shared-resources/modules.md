---
title: Gestire i moduli in automazione di Azure
description: Questo articolo descrive come gestire i moduli in automazione di Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cbc6932c3bbe11f0c4def17097c1791cbb1687bf
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515899"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in automazione di Azure

Automazione di Azure offre la possibilità di importare i moduli di PowerShell nell'account di automazione per l'uso da parte di manuali operativi basato su PowerShell. Questi moduli possono essere moduli personalizzati creati dall'PowerShell Gallery o AzureRM e AZ modules for Azure. Quando si crea un account di automazione, alcuni moduli vengono importati per impostazione predefinita.

## <a name="import-modules"></a>Importare i moduli

Esistono diversi modi in cui è possibile importare un modulo nell'account di automazione. Le sezioni seguenti illustrano i diversi modi per importare un modulo.

> [!NOTE]
> Il percorso massimo di un file in un modulo da usare in automazione di Azure è 140 caratteri. Qualsiasi percorso con un massimo di 140 caratteri non potrà essere importato nella sessione di `Import-Module`PowerShell con.

### <a name="powershell"></a>PowerShell

È possibile usare [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) per importare un modulo nell'account di automazione. Il cmdlet accetta un URL per un pacchetto zip del modulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portale di Azure

Nella portale di Azure passare all'account di automazione e selezionare **moduli** in **risorse condivise**. Fare clic su **+ Aggiungi modulo**. Selezionare un file con **estensione zip** contenente il modulo e fare clic su **OK** per avviare l'importazione del processo.

### <a name="powershell-gallery"></a>PowerShell Gallery

I moduli di PowerShell Gallery possono essere importati dal [PowerShell Gallery](https://www.powershellgallery.com) direttamente o dall'account di automazione.

Per importare un modulo dal PowerShell Gallery, passare a https://www.powershellgallery.com e cercare il modulo che si vuole importare. Fare clic su **Distribuisci in automazione di Azure** nella scheda **automazione di Azure** in opzioni di **installazione**. Verrà aperto il portale di Azure. Nella pagina **Importa** selezionare l'account di automazione e fare clic su **OK**.

![Modulo di importazione PowerShell Gallery](../media/modules/powershell-gallery.png)

È anche possibile importare i moduli dal PowerShell Gallery direttamente dall'account di automazione. Nell'account di automazione selezionare **moduli** in **risorse condivise**. Nella pagina moduli fare clic su **Esplora raccolta**, quindi cercare un modulo nella PowerShell Gallery. Selezionare il modulo che si vuole importare e fare clic su **Importa**. Nella pagina **Importa** fare clic su **OK** per avviare il processo di importazione.

![Importazione PowerShell Gallery da portale di Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Elimina moduli

Se si verificano problemi con un modulo o è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminarlo dall'account di automazione. Non è possibile eliminare la versione originale dei [moduli predefiniti](#default-modules) che vengono importati quando si crea un account di automazione. Se il modulo che si vuole eliminare è una versione più recente di uno dei [moduli predefiniti](#default-modules) installati, verrà eseguito il rollback alla versione installata con l'account di automazione. In caso contrario, verranno rimossi tutti i moduli eliminati dall'account di automazione.

### <a name="azure-portal"></a>Portale di Azure

Nella portale di Azure passare all'account di automazione e selezionare **moduli** in **risorse condivise**. Selezionare il modulo che si desidera rimuovere. Nella pagina del **modulo** clcick **Delete**. Se questo modulo è uno dei [moduli predefiniti](#default-modules), verrà eseguito il rollback alla versione che era presente al momento della creazione dell'account di automazione.

### <a name="powershell"></a>PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire il comando seguente:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlet interni

Di seguito è riportato un elenco di cmdlet nel modulo interno `Orchestrator.AssetManagement.Cmdlets` che viene importato in ogni account di automazione. Questi cmdlet sono accessibili nelle configurazioni manuali operativi e DSC e consentono di interagire con gli asset nell'account di automazione. I cmdlet interni consentono inoltre di recuperare i segreti da valori di **variabili** crittografati, **credenziali**e campi di **connessione** crittografati. I cmdlet Azure PowerShell non sono in grado di recuperare questi segreti. Questi cmdlet non richiedono la connessione implicita ad Azure quando vengono usati, ad esempio l'uso di un account RunAs per l'autenticazione in Azure.

|Name|Descrizione|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Aggiungere un tipo di connessione al modulo

È possibile specificare un [tipo di connessione](../automation-connections.md) personalizzato da usare nell'account di automazione aggiungendo un file facoltativo al modulo. Si tratta di un file di metadati che specifica un tipo di connessione di automazione di Azure da usare con i cmdlet del modulo nell'account di automazione. Per ottenere questo risultato, è necessario innanzitutto saper creare un modulo di PowerShell. Per ulteriori informazioni sulla creazione di moduli, vedere [come scrivere un modulo di script di PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Usare una connessione personalizzata nel portale di Azure](../media/modules/connection-create-new.png)

Per aggiungere un tipo di connessione di automazione di Azure, il modulo deve contenere un file `<ModuleName>-Automation.json` con il nome che specifica le proprietà del tipo di connessione. Il file JSON si trova all'interno della cartella del modulo del file compresso con estensione zip. Questo file contiene i campi di una connessione necessaria per la connessione al sistema o al servizio rappresentato dal modulo. La configurazione finisce creando un tipo di connessione in automazione di Azure. Con questo file è possibile impostare i nomi dei campi, i tipi e se i campi devono essere crittografati o facoltativi per il tipo di connessione del modulo. L'esempio seguente è un modello nel formato di file JSON che definisce una proprietà nome utente e password:

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

Quando si crea un modulo di PowerShell per l'uso in automazione di Azure, è consigliabile tenere presente quanto segue:

* Non includere una cartella Version nel pacchetto zip.  Questo problema è meno problematico per manuali operativi, ma causerà un problema con il servizio di configurazione dello stato.  Automazione di Azure creerà automaticamente la cartella della versione quando il modulo viene distribuito ai nodi gestiti da DSC e, se esiste una cartella della versione, si otterranno due istanze.  Esempio di struttura di cartelle per un modulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire alcune informazioni della Guida per i cmdlet in modo che gli utenti possano visualizzare informazioni sul relativo uso con il cmdlet **Get-Help** . Nell'esempio seguente viene illustrato come definire un riepilogo e un URI della Guida per in un file di modulo con estensione psm1:

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

  Specificando queste informazioni, viene visualizzata la Guida usando il cmdlet **Get-Help** nella console di PowerShell. Questa descrizione viene visualizzata anche nella portale di Azure.

  ![Guida del modulo di integrazione](../media/modules/module-activity-description.png)

* Se il modulo si connette a un servizio esterno, deve contenere un [tipo di connessione](#add-a-connection-type-to-your-module). Ogni cmdlet del modulo, inoltre, deve poter accettare un oggetto connessione (ovvero un'istanza del tipo di connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset di connessione ai parametri corrispondenti del cmdlet ogni volta che chiamano un cmdlet. In base all'esempio precedente di Runbook, viene usato un asset di connessione Contoso di esempio denominato ContosoConnection per accedere alle risorse di Contoso e restituire i dati dal servizio esterno.

  Nell'esempio seguente viene eseguito il mapping dei campi alle proprietà username e password di un `PSCredential` oggetto e quindi viene passato al cmdlet.

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

  È possibile abilitare un comportamento simile a quello dell'esempio precedente per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché soltanto i campi connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set `UserAccount`di parametri viene utilizzato per passare le proprietà dei campi della connessione. `ConnectionObject`consente di passare direttamente la connessione.

* Definire il tipo di output per tutti i cmdlet nel modulo. Definendo un tipo di output per un cmdlet, IntelliSense in fase di progettazione consente di determinare le proprietà di output del cmdlet da usare durante la creazione. È particolarmente utile durante la creazione grafica di runbook di automazione, in cui la conoscenza in fase di progettazione è essenziale per facilitare l'esperienza dell'utente con il modulo.

Aggiungere `[OutputType([<MyOutputType>])]` where MyOutputType è un tipo valido. Per ulteriori informazioni su OutputType, vedere [informazioni sulle funzioni OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è un esempio di aggiunta `OutputType` a un cmdlet:

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

* Assicurarsi che tutti i cmdlet del modulo siano senza stato. Più processi Runbook possono essere eseguiti contemporaneamente nello stesso AppDomain e nello stesso processo e sandbox. Se è presente uno stato condiviso in questi livelli, i processi possono interessare l'uno con l'altro. Questo comportamento può causare problemi intermittenti e difficili da diagnosticare.  Di seguito è riportato un esempio della soluzione da non adottare.

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

* Il modulo deve essere completamente incluso in un pacchetto che può essere xcopy. I moduli di automazione di Azure vengono distribuiti nelle sandbox di automazione quando è necessario eseguire manuali operativi. I moduli devono funzionare in modo indipendente dall'host in cui vengono eseguiti. Dovrebbe essere possibile comprimere un pacchetto del modulo e spostarlo, facendo in modo che funzioni normalmente dopo l'importazione nell'ambiente PowerShell di un altro host. Perché ciò avvenga, il modulo non deve dipendere da file all'esterno della cartella del modulo, ovvero della cartella che viene compressa quando il modulo è importato in Automazione di Azure. Il modulo non deve inoltre dipendere da eventuali impostazioni del Registro di sistema univoche di un host, come quelle definite quando viene installato un prodotto. Tutti i file del modulo devono avere un percorso inferiore a 140 caratteri. Eventuali percorsi con una maggiore di 140 caratteri causeranno problemi durante l'importazione del Runbook. Se questa procedura consigliata non viene seguita, il modulo non potrà essere usato in Automazione di Azure.  

* Se si fa riferimento ai [moduli Az di Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nel modulo, assicurarsi che non venga fatto riferimento anche a `AzureRM`. Il modulo `Az` non può essere usato in combinazione con i moduli `AzureRM`. `Az` è supportato nei runbook, ma non viene importato per impostazione predefinita. Per saperne di più sui moduli `Az` e sulle considerazioni di cui tenere conto, vedere [Supporto per i moduli Az in Automazione di Azure](../az-modules.md).

## <a name="default-modules"></a>Moduli predefiniti

La tabella seguente elenca i moduli importati per impostazione predefinita quando viene creato un account di automazione. Nei moduli elencati di seguito possono essere importate versioni più recenti, ma non è possibile rimuovere la versione originale dall'account di automazione anche se si elimina una versione più recente.

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
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
