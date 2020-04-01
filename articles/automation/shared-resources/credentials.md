---
title: Asset credenziali in Automazione di Azure
description: Gli asset credenziali in Automazione di Azure includono le credenziali di sicurezza che possono essere usate per l'autenticazione nelle risorse a cui accede il Runbook o la configurazione DSC. Questo articolo illustra come creare asset credenziali e usarli in un Runbook o in una configurazione DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 680e68d17637d71c1a1e5e8cfa539ee90028ac4e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478773"
---
# <a name="credential-assets-in-azure-automation"></a>Asset credenziali in Automazione di Azure

Un asset delle credenziali di automazione contiene un oggetto che contiene credenziali di sicurezza, ad esempio un nome utente e una password. I runbook e le configurazioni DSC utilizzano cmdlet che accettano un oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) per l'autenticazione. In alternativa, possono estrarre il nome `PSCredential` utente e la password dell'oggetto da fornire a un'applicazione o un servizio che richiede l'autenticazione. Automazione di Azure archivia in modo sicuro le proprietà di una credenziale e l'accesso alle proprietà in una configurazione runbook o DSC con l'attività [Get-AutomationPSCredential.Azure](#activities-used-to-access-credentials) Automation securely stores the properties of a credential and access to the properties in a runbook or DSC configuration with the Get-AutomationPSCredential activity.

> [!NOTE]
> Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di Automazione.These assets are encrypted and stored in Azure Automation using a unique key that is generated for each Automation account. Questa chiave viene archiviata in Key Vault. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Cmdlet di Azure PowerShell Az usati per le risorse delle credenzialiAzure PowerShell Az cmdlets used for credential assets

Per il modulo Azure PowerShell Az, i cmdlet nella tabella seguente vengono usati per creare e gestire gli asset delle credenziali di automazione con Windows PowerShell.For the Azure PowerShell Az module, the cmdlets in the following table are used to create and manage Automation credential assets with Windows PowerShell. Vengono spediti come parte del [modulo Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), disponibile per l'utilizzo nei runbook di automazione e nelle configurazioni DSC. Vedere [Supporto dei moduli Az in Automazione di Azure](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzAutomationCredentialGet-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera informazioni riguardo un asset credenziali. Questo cmdlet non restituisce un oggetto PSCredential.  |
| [New-AzAutomationCredentialNew-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Crea nuove credenziali di Automazione. |
| [Rimuovere-AzAutomationCredentialRemove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Rimuove le credenziali di Automazione. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Imposta le proprietà per le credenziali di Automazione esistenti. |

## <a name="activities-used-to-access-credentials"></a>Attività utilizzate per accedere alle credenziali

Le attività nella tabella seguente vengono utilizzate per accedere alle credenziali nei runbook e nelle configurazioni DSC.

| Attività | Descrizione |
|:--- |:--- |
| `Get-AutomationPSCredential` |Ottiene una credenziale da usare in un runbook o in una configurazione DSC. La credenziale è `PSCredential` sotto forma di oggetto. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Ottiene una credenziale con una richiesta di nome utente e password. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Crea un asset delle credenziali. |

Per lo sviluppo locale utilizzando Azure `Get-AutomationPSCredential` Automation Authoring Toolkit, il cmdlet fa parte dell'assembly [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Per Azure che utilizza il contesto `Orchestrator.AssetManagement.Cmdlets`di automazione, il cmdlet è in . Vedere [Gestire i moduli in Automazione di Azure.See Manage modules in Azure Automation.](modules.md)

Per poter recuperare `PSCredential` oggetti nel codice, è possibile installare il [componente aggiuntivo Microsoft Azure Automation ISE per PowerShell ISE](https://github.com/azureautomation/azure-automation-ise-addon).

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Lo script può anche importare il modulo richiesto dove necessario, come nell'esempio seguente:Your script can also import the required module where needed, as in the following example: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> È consigliabile evitare `Name` di `Get-AutomationPSCredential`utilizzare variabili nel parametro di . Il loro utilizzo può complicare l'individuazione delle dipendenze tra runbook o configurazioni DSC e risorse di credenziali in fase di progettazione.

## <a name="python2-functions-that-access-credentials"></a>Funzioni Python2 che accedono alle credenziali

La funzione nella tabella seguente viene usata per accedere alle credenziali in un runbook Python2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informazioni riguardo un asset credenziali. |

> [!NOTE]
> Importa `automationassets` il modulo nella parte superiore del runbook Python per accedere alle funzioni delle risorse.

## <a name="creating-a-new-credential-asset"></a>Creazione di un nuovo asset credenziali

È possibile creare un nuovo asset delle credenziali usando il portale di Azure o Windows PowerShell.You can create a new credential asset using the Azure portal or using Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Creare un nuovo asset delle credenziali con il portale di AzureCreate a new credential asset with the Azure portal

1. Dall'account di automazione selezionare **Credenziali** in **Risorse condivise**.
1. Selezionare **Aggiungi credenziali**.
2. Nel riquadro Nuova credenziale immettere un nome di credenziale appropriato in base agli standard di denominazione. 
3. Digitare l'ID di accesso nel campo **Nome utente.** 
4. Per entrambi i campi della password, immettere la chiave di accesso segreta.
5. Se la casella di autenticazione a più fattori è selezionata, deselezionarla. 
6. Fare clic su **Crea** per salvare la nuova risorsa credenziali.

> [!NOTE]
> Automazione di Azure non supporta gli account utente che usano l'autenticazione a più fattori.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Creare un nuovo asset delle credenziali con Windows PowerShellCreate a new credential asset with Windows PowerShell

L'esempio seguente mostra come creare un nuovo asset di credenziali di automazione. Un `PSCredential` oggetto viene prima creato con il nome e la password, quindi utilizzato per creare l'asset delle credenziali. In alternativa, è `Get-Credential` possibile utilizzare il cmdlet per richiedere all'utente di digitare un nome e una password.


```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Uso di credenziali PowerShell 

Un runbook o una configurazione DSC `Get-AutomationPSCredential` recupera un asset delle credenziali con l'attività. Questa attività recupera `PSCredential` un oggetto che è possibile utilizzare con un'attività o un cmdlet che richiede una credenziale. È anche possibile recuperare le proprietà dell'oggetto credenziali da usare individualmente. L'oggetto dispone di proprietà per il nome utente e la password sicura. In alternativa, è possibile utilizzare il [metodo GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) per recuperare un oggetto [NetworkCredential](/dotnet/api/system.net.networkcredential) che rappresenta una versione non protetta della password.

> [!NOTE]
> `Get-AzAutomationCredential`non recupera `PSCredential` un oggetto che può essere utilizzato per l'autenticazione. Fornisce solo informazioni sulle credenziali. Se è necessario utilizzare una credenziale in un `PSCredential` runbook, è necessario recuperarla come oggetto utilizzando `Get-AutomationPSCredential`.

### <a name="textual-runbook-example"></a>Esempio di runbook testuale

Nell'esempio seguente viene illustrato come usare una credenziale di PowerShell in un runbook. Recupera la credenziale e assegna il nome utente e la password alle variabili.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

È anche possibile usare una credenziale per eseguire l'autenticazione in Azure con [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Nella maggior parte dei casi, è necessario utilizzare un [account RunAs](../manage-runas-account.md) e recuperare la connessione con [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Esempio di runbook grafico

È possibile `Get-AutomationPSCredential` aggiungere un'attività a un runbook grafico facendo clic con il pulsante destro del mouse sulle credenziali nel riquadro Libreria dell'editor grafico e selezionando **Aggiungi all'area di disegno**.

![Aggiungere credenziali all'area di disegno](../media/credentials/credential-add-canvas.png)

La figura seguente mostra un esempio dell'uso di credenziali in un Runbook grafico. In questo caso, la credenziale viene usata per fornire l'autenticazione per un runbook alle risorse di Azure, come descritto in Usare Azure AD in Automazione di [Azure per eseguire l'autenticazione in Azure.In](../automation-use-azure-ad.md)this case, the credential is used to provide authentication for a runbook to Azure resources, as described in Azure AD in Azure Automation to authenticate to Azure. La prima attività recupera le credenziali che possono accedere alla sottoscrizione di Azure. L'attività di connessione dell'account utilizza quindi questa credenziale per fornire l'autenticazione per tutte le attività successive. Un [collegamento alla](../automation-graphical-authoring-intro.md#links-and-workflow) pipeline `Get-AutomationPSCredential` viene utilizzato qui poiché prevede un singolo oggetto.  

![Aggiungere credenziali all'area di disegno](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Utilizzo delle credenziali in una configurazione DSCUsing credentials in a DSC configuration

Mentre le configurazioni DSC in Automazione di Azure possono usare gli asset delle credenziali usando `Get-AutomationPSCredential`, possono anche passare gli asset delle credenziali tramite parametri. Per ulteriori informazioni vedere [Compilazione di configurazioni in Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Uso delle credenziali in Python2

L'esempio seguente mostra un esempio di accesso alle credenziali nei runbook Python2.The following example shows an example of accessing credentials in Python2 runbooks.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sui collegamenti nella creazione grafica, consultate [Collegamenti nella creazione grafica.](../automation-graphical-authoring-intro.md#links-and-workflow)
* Per comprendere i diversi metodi di autenticazione per l'automazione, vedere [Sicurezza di automazione](../automation-security-overview.md)di Azure.To understand the different authentication methods for Automation, see Azure Automation Security .
* Per iniziare a utilizzare i runbook grafici, vedere [Il mio primo runbook grafico](../automation-first-runbook-graphical.md).
* Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il mio primo runbook del flusso di lavoro](../automation-first-runbook-textual.md)di PowerShell .
* Per iniziare con i runbook Python2, vedere [Il mio primo runbook Python2](../automation-first-runbook-textual-python2.md). 
