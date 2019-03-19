---
title: Gestire i moduli in automazione di Azure
description: Questo articolo descrive come gestire i moduli in automazione di Azure
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905326"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in automazione di Azure

Automazione di Azure offre la possibilità di importare i moduli di PowerShell nell'Account di automazione da usare per i runbook di PowerShell basati su. Questi moduli possono essere creati, da PowerShell Gallery, i moduli personalizzati o i moduli AzureRM e Az per Azure.

## <a name="import-modules"></a>Importare i moduli

Esistono diversi modi, che è possibile importare un modulo nell'Account di automazione. Le sezioni seguenti illustrano i diversi modi per importare un modulo.

> [!NOTE]
> La lunghezza massima del percorso di un file in un modulo da usare in automazione di Azure è 140 caratteri. Qualsiasi percorso superiore a 140 caratteri non sarà in grado di essere importati nella sessione di PowerShell con `Import-Module`.

### <a name="powershell"></a>PowerShell

È possibile usare la [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) per importare un modulo nell'Account di automazione. Il cmdlet accetta un url a un pacchetto zip del modulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure, passare all'Account di automazione e selezionare **moduli** sotto **risorse condivise**. Fare clic su **+ Aggiungi un modulo**. Selezionare una **zip** file che contiene il modulo e fare clic su **Ok** a avvia il processo di importazione.

### <a name="powershell-gallery"></a>PowerShell Gallery

I moduli da PowerShell gallery possono essere importati le [PowerShell Gallery](https://www.powershellgallery.com) direttamente o dall'Account di automazione.

Per importare un modulo da PowerShell Gallery, passare a https://www.powershellgallery.com e cercare il modulo da importare. Fare clic su **Deploy to Azure Automation** nel **automazione di Azure** disponibile nella scheda **opzioni di installazione**. Questa azione apre il portale di Azure. Nel **importazione** pagina, selezionare l'Account di automazione e fare clic su **OK**.

![Modulo di importazione di PowerShell Gallery](../media/modules/powershell-gallery.png)

È anche possibile importare i moduli da PowerShell Gallery direttamente dall'Account di automazione. Nell'Account di automazione, selezionare **moduli** sotto **risorse condivise**. Nella pagina moduli, fare clic su **Esplora raccolta**. Verrà visualizzato il **Esplora raccolta** pagina. È possibile utilizzare questa pagina per la ricerca per un modulo di PowerShell Gallery. Selezionare il modulo che si desidera importare e fare clic su **importare**. Nel **importare** pagina, fare clic su **OK** per avviare il processo di importazione.

![Importazione di PowerShell Gallery dal portale di Azure](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>Cmdlet interni

Di seguito è riportato un elenco dei cmdlet inclusi nel interno `Orchestrator.AssetManagement.Cmdlets` modulo importato in ogni Account di automazione. Questi cmdlet sono accessibili nel runbook e configurazioni DSC e consentono di interagire con le risorse all'interno dell'Account di automazione. Inoltre, i cmdlet interni consentono di recuperare i segreti da crittografati **variabile** valori, **credenziali**e crittografati **connessione** campi. I cmdlet di PowerShell di Azure non sono in grado di recuperare questi segreti. Questi cmdlet non sono necessario connettersi ad Azure in modo implicito quando vengono utilizzati. Ciò è utile per scenari in cui si dispone di una connessione, ad esempio un Account runas che è necessario usare l'autenticazione ad Azure.

|NOME|DESCRIZIONE|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Aggiungere un tipo di connessione a un modulo

È possibile fornire una classe personalizzata [tipo di connessione](../automation-connections.md) da usare nell'Account di automazione tramite l'aggiunta di un file facoltativo al modulo. Questo file è un file di metadati che specifica un tipo di connessione di automazione di Azure da usare con i cmdlet del modulo nell'Account di automazione. A tale scopo è necessario conoscere prima di tutto per la creazione di un modulo di PowerShell. Per altre informazioni sulla creazione di modulo, vedere [come scrivere un modulo di Script di PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Usare una connessione personalizzata nel portale di Azure](../media/modules/connection-create-new.png)

Per aggiungere un tipo di connessione di automazione di Azure, il modulo deve contenere un file con il nome `<ModuleName>-Automation.json` che specifica le proprietà del tipo di connessione. Si tratta di un file JSON, che viene inserito all'interno della cartella del modulo del file ZIP compresso. Questo file contiene i campi di una connessione che è necessario connettersi al sistema o al servizio rappresentato dal modulo. Questa configurazione consente di creare un tipo di connessione in Automazione di Azure. Uso di questo file è possibile impostare i nomi dei campi, tipi, e se i campi devono essere crittografati o facoltativo, per il tipo di connessione del modulo. L'esempio seguente è un modello nel formato di file json che definisce una proprietà nome utente e password:

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

## <a name="module-best-practices"></a>Le procedure consigliate di modulo

Questi moduli di PowerShell possono essere importati in Automazione di Azure per rendere i cmdlet e le risorse DSC disponibili per l'uso rispettivamente in runbook e configurazioni DSC. In background Automazione di Azure archivia questi moduli e durante l'esecuzione del processo del runbook e del processo di compilazione DSC li carica nelle sandbox di Automazione di Azure in cui i runbook vengono eseguiti e le configurazioni DSC compilate. Tutte le risorse DSC dei moduli vengono automaticamente inserite nel server di pull di Automation DSC. In questo modo i computer possono eseguirne il pull quando applicano le configurazioni DSC.

È consigliabile che tenere presente quanto segue quando si crea un modulo di PowerShell da usare in automazione di Azure:

* Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire alcune informazioni della Guida per i cmdlet in modo che gli utenti possano visualizzare informazioni sul relativo uso con il cmdlet **Get-Help** . Nell'esempio seguente viene illustrato come definire un riepilogo e l'URI per in un file con estensione psm1 del modulo della Guida:

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

  Specificando queste informazioni, viene visualizzata la Guida usando il cmdlet **Get-Help** nella console di PowerShell. Questa descrizione viene visualizzata anche nel portale di Azure.

  ![Guida del modulo di integrazione](../media/modules/module-activity-description.png)

* Se il modulo si connette a un servizio esterno, deve contenere una [tipo di connessione](#add-a-connection-type-to-your-module). Ogni cmdlet del modulo, inoltre, deve poter accettare un oggetto connessione (ovvero un'istanza del tipo di connessione) come parametro. Ciò consente agli utenti di eseguire il mapping dei parametri dell'asset della connessione ai parametri del cmdlet corrispondenti ogni volta che chiamano un cmdlet. In base all'esempio di runbook precedente, Usa un asset di connessione di esempio Contoso chiamato ContosoConnection per accedere alle risorse di Contoso e restituire i dati dal servizio esterno.

  Nell'esempio seguente, i campi vengono mappati alle proprietà nome utente e Password di un `PSCredential` dell'oggetto e quindi passato al cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Un modo più semplice ed efficiente per raggiungere questo comportamento è passare direttamente l'oggetto connessione al cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  È possibile abilitare un comportamento simile a quello dell'esempio precedente per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché soltanto i campi connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set di parametri `UserAccount`, viene usato per passare la connessione delle proprietà di campo. `ConnectionObject` Consente di passare direttamente tramite la connessione.

* Definire il tipo di output per tutti i cmdlet nel modulo. Definendo un tipo di output per un cmdlet, IntelliSense in fase di progettazione consente di determinare le proprietà di output del cmdlet da usare durante la creazione. È particolarmente utile durante la creazione grafica di runbook di automazione, in cui la conoscenza in fase di progettazione è essenziale per facilitare l'esperienza dell'utente con il modulo.

  Ciò può essere ottenuta mediante l'aggiunta di `[OutputType([<MyOutputType>])]` dove MyOutputType è un tipo valido. Per altre informazioni su OutputType, vedere [sulle funzioni OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è un esempio di aggiunta `OutputType` a un cmdlet:

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

* Assicurarsi che tutti i cmdlet del modulo siano senza stato. Più processi del runbook possono eseguire contemporaneamente nello stesso AppDomain e stesso processo e ambiente sandbox. Se è presente qualsiasi stato condiviso a tali livelli, i processi possono interferiscano tra loro. Questo comportamento può causare intermittente e difficile da diagnosticare i problemi.  Di seguito è riportato un esempio della soluzione da non adottare.

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

* Il modulo deve essere interamente in un pacchetto in grado di xcopy. Moduli di automazione di Azure vengono distribuiti a sandbox di automazione quando devono eseguire i runbook. I moduli devono funzionare in modo indipendente dall'host in cui vengono eseguiti. Dovrebbe essere possibile comprimere un pacchetto del modulo e spostarlo, facendo in modo che funzioni normalmente dopo l'importazione nell'ambiente PowerShell di un altro host. Perché ciò avvenga, il modulo non deve dipendere da file all'esterno della cartella del modulo, ovvero della cartella che viene compressa quando il modulo è importato in Automazione di Azure. Il modulo non deve inoltre dipendere da eventuali impostazioni del Registro di sistema univoche di un host, come quelle definite quando viene installato un prodotto. Tutti i file del modulo devono avere un percorso di meno di 140 caratteri. Tutti i percorsi contenenti oltre 140 caratteri causerà problemi di importazione del runbook. Se questa procedura consigliata non viene seguita, il modulo non potrà essere usato in Automazione di Azure.  

* Se si fa riferimento ai [moduli Az di Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nel modulo, assicurarsi che non venga fatto riferimento anche a `AzureRM`. Il modulo `Az` non può essere usato in combinazione con i moduli `AzureRM`. `Az` è supportato nei runbook, ma non viene importato per impostazione predefinita. Per saperne di più sui moduli `Az` e sulle considerazioni di cui tenere conto, vedere [Supporto per i moduli Az in Automazione di Azure](../az-modules.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)