---
title: Gestire i moduli in Automazione di Azure
description: Automazione di Azure consente di importare i moduli di PowerShell per abilitare i cmdlet in manuali operativi e nelle risorse DSC nelle configurazioni DSC.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84fdb5a9cf3c22048473cd00ee6f8e7ac36c9097
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864301"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in Automazione di Azure

Automazione di Azure consente di importare i moduli di PowerShell per abilitare i cmdlet in manuali operativi e nelle risorse DSC nelle configurazioni DSC. I moduli usati in automazione di Azure includono:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0).
* Altri moduli di PowerShell.
* Modulo `Orchestrator.AssetManagement.Cmdlets` interno.
* Moduli Python 2.
* Moduli personalizzati creati dall'utente.

Quando si crea un account di automazione, automazione di Azure importa alcuni moduli per impostazione predefinita. Vedere [moduli predefiniti](#default-modules).

Quando l'automazione esegue i processi di compilazione Runbook e DSC, carica i moduli in sandbox in cui è possibile eseguire manuali operativi e le configurazioni DSC possono essere compilate. Automazione inserisce automaticamente anche eventuali risorse DSC nei moduli nel server di pull DSC. I computer possono effettuare il pull delle risorse quando applicano le configurazioni DSC.

>[!NOTE]
>Assicurarsi di importare solo i moduli effettivamente necessari per le configurazioni manuali operativi e DSC. Non è consigliabile importare la radice AZ Module. Include molti altri moduli che potrebbero non essere necessari, che possono causare problemi di prestazioni. Importare invece singoli moduli, ad esempio AZ. Compute.

>[!NOTE]
>Questo articolo usa il Azure PowerShell AZ Module. È comunque possibile usare il modulo AzureRM. Per ulteriori informazioni sulla compatibilità AZ Module e AzureRM, vedere la pagina relativa all' [Introduzione del nuovo Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Moduli predefiniti

La tabella seguente elenca i moduli importati da automazione di Azure per impostazione predefinita quando si crea l'account di automazione. L'automazione può importare versioni più recenti di questi moduli. Tuttavia, non è possibile rimuovere la versione originale dall'account di automazione, anche se si elimina una versione più recente. Si noti che questi moduli predefiniti includono diversi moduli AzureRM. 

L'automazione non importa automaticamente il modulo AZ radice in tutti gli account di automazione nuovi o esistenti. Per ulteriori informazioni sull'utilizzo di questi moduli, vedere la pagina relativa [alla migrazione a AZ Modules](#migrating-to-az-modules).

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

## <a name="az-modules"></a>Moduli AZ

Per AZ. Automation, la maggior parte dei cmdlet hanno gli stessi nomi di quelli usati per i moduli AzureRM, ad eccezione del fatto che il prefisso *AzureRM* è stato modificato in *AZ*. Per un elenco dei moduli AZ che non seguono questa convenzione di denominazione, vedere l' [elenco delle eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Cmdlet interni

Automazione di Azure supporta il `Orchestrator.AssetManagement.Cmdlets` modulo interno per l'agente di log Analytics per Windows, installato per impostazione predefinita. Nella tabella seguente vengono definiti i cmdlet interni. Questi cmdlet sono progettati per essere usati al posto dei cmdlet di Azure PowerShell per interagire con le risorse condivise. Possono recuperare i segreti da variabili crittografate, credenziali e connessioni crittografate.

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

Si noti che i cmdlet interni differiscono per la denominazione dei cmdlet AZ e AzureRM. I nomi dei cmdlet interni non contengono parole come "Azure" o "AZ" nel sostantivo, ma usano l' *automazione*di Word. È consigliabile usare i cmdlet AZ o AzureRM durante l'esecuzione di Runbook in una sandbox di Azure o in un ruolo di lavoro ibrido per Runbook Windows. Richiedono un minor numero di parametri ed eseguiti nel contesto del processo già in esecuzione.

Usare i cmdlet AZ o AzureRM per modificare le risorse di automazione al di fuori del contesto di un Runbook. 

## <a name="python-modules"></a>Moduli Python

È possibile creare manuali operativi Python 2 in automazione di Azure. Per informazioni sul modulo Python, vedere [gestire i pacchetti Python 2 in automazione di Azure](../python-packages.md).

## <a name="custom-modules"></a>Moduli personalizzati

Automazione di Azure supporta i moduli di PowerShell personalizzati che è possibile creare per usare con le configurazioni manuali operativi e DSC. Un tipo di modulo personalizzato è un modulo di integrazione che contiene facoltativamente un file di metadati per definire la funzionalità personalizzata per i cmdlet del modulo. Un esempio di uso di un modulo di integrazione è disponibile in [aggiungere un tipo di connessione](../automation-connections.md#add-a-connection-type).

Automazione di Azure può importare un modulo personalizzato per rendere disponibili i relativi cmdlet. Dietro le quinte, archivia il modulo e lo usa nelle sandbox di Azure, proprio come gli altri moduli.

## <a name="migrating-to-az-modules"></a>Migrazione a moduli Az

### <a name="migration-considerations"></a>Considerazioni sulla migrazione

Questa sezione include considerazioni da tenere in considerazione quando si esegue la migrazione ai moduli AZ in automazione. Per ulteriori informazioni, vedere [eseguire la migrazione di Azure PowerShell da AzureRM a AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Uso dei moduli AzureRM e AZ Modules nello stesso account di automazione

 Non è consigliabile eseguire i moduli AzureRM e AZ Modules nello stesso account di automazione. Quando si è sicuri di voler eseguire la migrazione da AzureRM a AZ, è consigliabile eseguire il commit completo di una migrazione completa. L'automazione spesso riutilizza sandbox all'interno dell'account di automazione per risparmiare sui tempi di avvio. Se non si esegue la migrazione di un modulo completo, è possibile avviare un processo che usa solo moduli AzureRM e quindi avviare un altro processo che usa solo AZ modules. Il sandbox si interrompe presto e viene visualizzato un errore che informa che i moduli non sono compatibili. Questa situazione comporta arresti anomali casuali per qualsiasi particolare Runbook o configurazione. 

#### <a name="importing-az-modules-into-the-powershell-session"></a>Importazione di AZ Modules nella sessione di PowerShell

L'importazione di un modulo AZ nell'account di automazione non comporta l'importazione automatica del modulo nella sessione di PowerShell utilizzata da manuali operativi. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

* Quando un Runbook richiama un cmdlet da un modulo.
* Quando un Runbook importa il modulo in modo esplicito con il cmdlet [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) .
* Quando un Runbook importa un altro modulo dipendente.

#### <a name="testing-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Test delle configurazioni manuali operativi e DSC prima della migrazione del modulo

Assicurarsi di testare attentamente tutte le configurazioni manuali operativi e DSC, in un account di automazione separato, prima di eseguire la migrazione ai moduli AZ. 

#### <a name="updates-for-tutorial-runbooks"></a>Aggiornamenti per l'esercitazione manuali operativi 

Quando si crea un nuovo account di automazione, anche dopo la migrazione a AZ Modules, l'automazione installa i moduli AzureRM per impostazione predefinita. È comunque possibile aggiornare l'esercitazione manuali operativi con i cmdlet di AzureRM. Tuttavia, non è consigliabile eseguire questi manuali operativi.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Arrestare e depianificare tutti i manuali operativi che usano i moduli AzureRM

Per assicurarsi di non eseguire alcuna configurazione manuali operativi o DSC esistente che usi moduli AzureRM, è necessario arrestare e depianificare tutte le configurazioni e i manuali operativi interessati. Assicurarsi prima di tutto di esaminare ogni configurazione di Runbook o DSC e le relative pianificazioni separatamente, per assicurarsi che sia possibile ripianificare l'elemento in futuro, se necessario. 

Quando si è pronti per rimuovere le pianificazioni, è possibile usare il portale di Azure o il cmdlet [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . Vedere [rimuovere una pianificazione](schedules.md#remove-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Rimuovere i moduli AzureRM

È possibile rimuovere i moduli AzureRM prima di importare i moduli AZ. Tuttavia, se si esegue questa operazione, è possibile interrompere la sincronizzazione del controllo del codice sorgente e causare la mancata esecuzione di script ancora pianificati. Se si decide di rimuovere i moduli, vedere [disinstallare AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Importare moduli Az

È possibile importare i moduli AZ nel portale di Azure. Ricordarsi di importare solo i moduli AZ necessari, non l'intero modulo AZ. Automation. Poiché [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) è una dipendenza per gli altri moduli AZ, assicurarsi di importare questo modulo prima di qualsiasi altro.

1. Nell'account di automazione, in **risorse condivise**, selezionare **moduli**. 
2. Selezionare **Esplora raccolta**.  
3. Nella barra di ricerca immettere il nome del modulo, ad esempio `Az.Accounts`. 
4. Nella pagina **modulo PowerShell** selezionare **Importa** per importare il modulo nell'account di automazione.

    ![Screenshot dell'importazione di moduli nell'account di automazione](../media/modules/import-module.png)

È anche possibile eseguire questa importazione tramite la [PowerShell Gallery](https://www.powershellgallery.com), cercando il modulo da importare. Quando si trova il modulo, selezionarlo e scegliere la scheda **automazione di Azure** . Selezionare **Distribuisci in automazione di Azure**.

![Screenshot dell'importazione di moduli direttamente da PowerShell Gallery](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testare i runbook

Dopo aver importato i moduli AZ nell'account di automazione, è possibile iniziare a modificare le configurazioni di manuali operativi e DSC per usare i nuovi moduli. Un modo per testare la modifica di un Runbook per usare i nuovi cmdlet consiste nell'usare il `Enable-AzureRmAlias -Scope Process` comando all'inizio di Runbook. Aggiungendo questo comando al Runbook, lo script può essere eseguito senza modifiche. 

## <a name="author-modules"></a>Crea moduli

Si consiglia di seguire le considerazioni in questa sezione quando si crea un modulo PowerShell personalizzato da usare in automazione di Azure. Per preparare il modulo per l'importazione, è necessario creare almeno un file con estensione **dll** del modulo psd1, Psm1 o PowerShell con lo stesso nome della cartella del modulo. Quindi si esegue il zip della cartella del modulo in modo che automazione di Azure possa importarlo come singolo file. Il pacchetto **zip** deve avere lo stesso nome della cartella del modulo contenuto. 

Per altre informazioni sulla creazione di un modulo di PowerShell, vedere [come scrivere un modulo di script di PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

### <a name="version-folder"></a>Cartella della versione

Non includere una cartella Version nel pacchetto **zip** per il modulo. Questo problema è meno problematico per manuali operativi, ma causa un problema con il servizio di configurazione dello stato (DSC). Automazione di Azure crea automaticamente la cartella della versione quando il modulo viene distribuito ai nodi gestiti dalla configurazione dello stato. Se esiste una cartella della versione, vengono rilevate due istanze. Di seguito è riportato un esempio di struttura di cartelle per un modulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informazioni della Guida

Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire le informazioni della Guida per i cmdlet usando il `Get-Help` cmdlet. Nell'esempio seguente viene illustrato come definire un riepilogo e un URI della Guida in un file di modulo con **estensione psm1** .

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

  ![Screenshot della guida del modulo di integrazione](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo di connessione

Se il modulo si connette a un servizio esterno, definire un tipo di connessione usando un [modulo di integrazione personalizzato](#custom-modules). Ogni cmdlet del modulo deve accettare un'istanza del tipo di connessione (oggetto connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset di connessione ai parametri corrispondenti del cmdlet ogni volta che chiamano un cmdlet. 

![Usare una connessione personalizzata nel portale di Azure](../media/modules/connection-create-new.png)

Nell'esempio di Runbook seguente viene usato un asset di `ContosoConnection` connessione contoso denominato per accedere alle risorse di Contoso e restituire i dati dal servizio esterno. In questo esempio, i campi vengono mappati alle `UserName` proprietà `Password` e di un `PSCredential` oggetto e quindi passati al cmdlet.

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

È possibile abilitare un comportamento simile per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché solo i campi di connessione per i parametri. In genere si desidera un set di parametri per ognuno, in modo che un utente che non utilizza l'automazione possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set `UserAccount` di parametri viene utilizzato per passare le proprietà dei campi della connessione. `ConnectionObject`consente di passare direttamente la connessione.

### <a name="output-type"></a>Tipo di output

Definire il tipo di output per tutti i cmdlet nel modulo. La definizione di un tipo di output per un cmdlet consente a IntelliSense in fase di progettazione di determinare le proprietà di output del cmdlet durante la creazione. Questa pratica è particolarmente utile durante la creazione di Runbook grafici, per cui le informazioni in fase di progettazione sono fondamentali per un'esperienza utente semplice con il modulo.

Aggiungere `[OutputType([<MyOutputType>])]`, dove `MyOutputType` è un tipo valido. Per altre informazioni su `OutputType`, vedere [About functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è un esempio di aggiunta `OutputType` a un cmdlet:

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

  ![Screenshot del tipo di output Runbook grafico](../media/modules/runbook-graphical-module-output-type.png)

  Questo comportamento è simile alla funzionalità di "tipo Ahead" dell'output di un cmdlet nell'ambiente del servizio di integrazione di PowerShell, senza doverla eseguire.

  ![Screenshot di POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

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

Verificare che il modulo sia completamente contenuto in un pacchetto che può essere copiato tramite Xcopy. I moduli di automazione vengono distribuiti nelle sandbox di automazione durante l'esecuzione di manuali operativi. I moduli devono funzionare indipendentemente dall'host che li esegue. 

Si dovrebbe essere in grado di comprimere e spostare un pacchetto del modulo e di fare in modo che funzioni come di consueto quando viene importato nell'ambiente PowerShell di un altro host. Per eseguire questa operazione, verificare che il modulo non dipenda da alcun file esterno alla cartella del modulo compresso quando il modulo viene importato in automazione. 

Il modulo non deve dipendere da impostazioni univoche del registro di sistema in un host. Esempi sono le impostazioni che vengono eseguite quando un prodotto è installato. 

### <a name="module-file-paths"></a>Percorsi dei file di modulo

Verificare che tutti i file del modulo includano percorsi con meno di 140 caratteri. Qualsiasi percorso con lunghezza superiore a 140 caratteri provoca problemi di importazione manuali operativi. L'automazione non può importare un file con una dimensione del percorso superiore a 140 caratteri `Import-Module`nella sessione di PowerShell con.

## <a name="import-modules"></a>Importare i moduli

Questa sezione definisce diversi modi in cui è possibile importare un modulo nell'account di automazione. 

### <a name="import-modules-in-the-azure-portal"></a>Importare i moduli nell'portale di Azure

Per importare un modulo nel portale di Azure:

1. Passare all'account di Automazione.
2. In **risorse condivise**selezionare **moduli**.
3. Selezionare **Aggiungi un modulo**. 
4. Selezionare il file **zip** contenente il modulo.
5. Selezionare **OK** per avviare l'importazione del processo.

### <a name="import-modules-by-using-powershell"></a>Importare i moduli usando PowerShell

È possibile usare il cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) per importare un modulo nell'account di automazione. Il cmdlet accetta un URL per un pacchetto con estensione zip del modulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

È anche possibile usare lo stesso cmdlet per importare un modulo direttamente dal PowerShell Gallery. Assicurarsi di acquisire `ModuleName` e `ModuleVersion` dalla [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importa i moduli dalla PowerShell Gallery

È possibile importare moduli di [PowerShell Gallery](https://www.powershellgallery.com) direttamente dalla raccolta o dal proprio account di automazione.

Per importare un modulo direttamente dalla PowerShell Gallery:

1. Passare a https://www.powershellgallery.com e cercare il modulo da importare.
2. In **Opzioni di installazione**, nella scheda **automazione di Azure** , selezionare **Distribuisci in automazione di Azure**. Questa azione consente di aprire il portale di Azure. 
3. Nella pagina **Importa** selezionare l'account di automazione e fare clic su **OK**.

![Screenshot del modulo di importazione PowerShell Gallery](../media/modules/powershell-gallery.png)

Per importare un modulo PowerShell Gallery direttamente dall'account di automazione:

1. In **risorse condivise**selezionare **moduli**. 
2. Selezionare **Esplora raccolta**, quindi cercare un modulo nella raccolta. 
3. Selezionare il modulo da importare e selezionare **Importa**. 
4. Selezionare **OK** per avviare il processo di importazione.

![Screenshot dell'importazione di un modulo PowerShell Gallery dal portale di Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Elimina moduli

Se si verificano problemi con un modulo oppure è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminarlo dall'account di automazione. Non è possibile eliminare le versioni originali dei [moduli predefiniti](#default-modules) che vengono importati quando si crea un account di automazione. Se il modulo da eliminare è una versione più recente di uno dei [moduli predefiniti](#default-modules), viene eseguito il rollback alla versione installata con l'account di automazione. In caso contrario, viene rimosso qualsiasi modulo eliminato dall'account di automazione.

### <a name="delete-modules-in-the-azure-portal"></a>Elimina i moduli nell'portale di Azure

Per rimuovere un modulo nel portale di Azure:

1. Passare all'account di Automazione. In **risorse condivise**selezionare **moduli**. 
2. Selezionare il modulo che si desidera rimuovere. 
3. Nella pagina **modulo** selezionare **Elimina**. Se il modulo è uno dei [moduli predefiniti](#default-modules), viene eseguito il rollback alla versione esistente al momento della creazione dell'account di automazione.

### <a name="delete-modules-by-using-powershell"></a>Eliminare i moduli usando PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire il comando seguente:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sull'utilizzo dei moduli di Azure PowerShell, vedere [Introduzione a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [scrittura di un modulo di Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
