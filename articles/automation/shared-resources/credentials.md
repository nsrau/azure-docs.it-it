---
title: Gestire le credenziali in Automazione di Azure
description: Questo articolo illustra come creare asset di credenziali e usarli in un runbook o in una configurazione DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b95eeaf7dd72c85c3940e3cdc2a71c193c35ff5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008609"
---
# <a name="manage-credentials-in-azure-automation"></a>Gestire le credenziali in Automazione di Azure

Un asset di credenziali di Automazione contiene un oggetto con credenziali di sicurezza, ad esempio un nome utente e una password. I runbook e le configurazioni DSC usano cmdlet che accettano un oggetto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) per l'autenticazione. In alternativa, è possibile estrarre il nome utente e la password dell'oggetto `PSCredential` per fornirli ad applicazioni o servizi che richiedono l'autenticazione. 

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Tali asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di Automazione. Automazione di Azure memorizza la chiave nel Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, Automazione carica la chiave dal Key Vault e quindi la usa per crittografare l'asset. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Cmdlet di PowerShell usati per accedere alle credenziali

I cmdlet nella tabella seguente creano e gestiscono le credenziali di Automazione con PowerShell. Sono inclusi nei [moduli Az](modules.md#az-modules).

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera un oggetto [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) contenente i metadati relativi alla credenziale. Il cmdlet non recupera l'oggetto `PSCredential` stesso.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Crea nuove credenziali di Automazione. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Rimuove le credenziali di Automazione. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Imposta le proprietà per le credenziali di Automazione esistenti. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Altri cmdlet usati per accedere alle credenziali

I cmdlet nella tabella seguente vengono usati per accedere alle credenziali nei runbook e nelle configurazioni DSC. 

| Cmdlet | Descrizione |
|:--- |:--- |
| `Get-AutomationPSCredential` |Ottiene un oggetto `PSCredential` da usare in un runbook o in una configurazione DSC. Nella maggior parte dei casi è consigliabile usare questo [ cmdlet interno](modules.md#internal-cmdlets) anziché il cmdlet `Get-AzAutomationCredential`, perché quest'ultimo recupera solo le informazioni sulle credenziali. Queste informazioni non sono in genere utili da passare a un altro cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Ottiene una credenziale con una richiesta di nome utente e password. Questo cmdlet fa parte del modulo Microsoft.PowerShell.Security predefinito. Vedere [Moduli predefiniti](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential?view=azuresmps-4.0.0) | Crea un asset di credenziali. Questo cmdlet fa parte del modulo di Azure predefinito. Vedere [Moduli predefiniti](modules.md#default-modules).|

Per recuperare oggetti `PSCredential` nel codice è necessario importare il modulo `Orchestrator.AssetManagement.Cmdlets`. Per altre informazioni, vedere [Gestire i moduli in Automazione di Azure](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> È consigliabile evitare di usare le variabili nel parametro `Name` di `Get-AutomationPSCredential`. Il loro uso può complicare l'individuazione delle dipendenze tra runbook o configurazioni DSC e gli asset di credenziali in fase di progettazione.

## <a name="python-2-functions-that-access-credentials"></a>Funzioni Python 2 che accedono alle credenziali

La funzione nella tabella seguente viene usata per accedere alle credenziali in un runbook Python 2.

| Funzione | Descrizione |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informazioni riguardo un asset credenziali. |

> [!NOTE]
> Importare il modulo `automationassets` nella parte superiore del runbook Python per accedere alle funzioni di asset.

## <a name="create-a-new-credential-asset"></a>Creare un nuovo asset di credenziali

È possibile creare un nuovo asset di credenziali usando il portale di Azure o Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Creare un nuovo asset di credenziali con il portale di Azure

1. Dall'account di Automazione, selezionare **Credenziali** in **Risorse condivise**.
1. Selezionare **Aggiungi credenziali**.
2. Nel riquadro Nuove credenziali immettere un nome di credenziale appropriato secondo gli standard di denominazione. 
3. Digitare l'ID di accesso nel campo **nome utente**. 
4. Per entrambi i campi password immettere la chiave di accesso segreta.

    ![Creare nuove credenziali](../media/credentials/credential-create.png)

5. Se è selezionata la casella di autenticazione a più fattori, deselezionarla. 
6. Fare clic su **Crea** per salvare il nuovo asset di credenziali.

> [!NOTE]
> Automazione di Azure non supporta gli account utente che usano l'autenticazione a più fattori.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Creare un nuovo asset di credenziali con Windows PowerShell

L'esempio seguente illustra come creare un nuovo asset di credenziali di Automazione. Viene prima creato un oggetto `PSCredential` con il nome e la password, che verrà quindi usato per creare l'asset di credenziali. In alternativa è possibile usare il cmdlet `Get-Credential` per richiedere all'utente di digitare un nome e una password.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Ottenere un asset di credenziali

Un runbook o una configurazione DSC recupera un asset di credenziali con il cmdlet `Get-AutomationPSCredential` interno. Questo cmdlet ottiene un oggetto `PSCredential` che è possibile usare con un cmdlet che richiede una credenziale. È anche possibile recuperare le proprietà dell'oggetto credenziali da usare individualmente. L'oggetto ha proprietà per il nome utente e la password di sicurezza. 

> [!NOTE]
> Il cmdlet `Get-AzAutomationCredential` non recupera un oggetto `PSCredential` che può essere usato per l'autenticazione. Fornisce solo informazioni sulle credenziali. Se è necessario usare una credenziale in un runbook, è necessario recuperarla come oggetto `PSCredential` usando `Get-AutomationPSCredential`.

In alternativa è possibile usare il metodo [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) per recuperare un oggetto [NetworkCredential](/dotnet/api/system.net.networkcredential) che rappresenta una versione non protetta della password.

### <a name="textual-runbook-example"></a>Esempio di runbook testuale

L'esempio seguente mostra come usare una credenziale PowerShell in un runbook. Recupera la credenziale e assegna il nome utente e la password alle variabili.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

È anche possibile usare una credenziale per eseguire l'autenticazione in Azure con [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Nella maggior parte dei casi, è consigliabile usare un [account RunAs](../manage-runas-account.md) e recuperare la connessione con [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Esempio di runbook grafico

È possibile aggiungere un'attività a un runbook grafico per il cmdlet interno `Get-AutomationPSCredential` facendo clic con il pulsante destro del mouse sulla credenziale nel riquadro Libreria dell'editor grafico e scegliendo **Aggiungi ad area di disegno**.

![Aggiungere credenziali all'area di disegno](../media/credentials/credential-add-canvas.png)

La figura seguente mostra un esempio dell'uso di credenziali in un Runbook grafico. In questo caso, la credenziale fornisce l'autenticazione per un runbook di risorse di Azure, come descritto in [Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure](../automation-use-azure-ad.md). La prima attività recupera le credenziali che possono accedere alla sottoscrizione di Azure. L'attività di connessione account usa quindi questa credenziale per fornire l'autenticazione per eventuali attività successive. Viene usato un [collegamento pipeline](../automation-graphical-authoring-intro.md#use-links-for-workflow) perché `Get-AutomationPSCredential` prevede un singolo oggetto.  

![Aggiungere credenziali all'area di disegno](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Usare le credenziali in una configurazione DSC

Anche se le configurazioni DSC in Automazione di Azure possono funzionare con asset di credenziali usando `Get-AutomationPSCredential`, possono anche passare le risorse di credenziali tramite parametri. Per ulteriori informazioni vedere [Compilazione di configurazioni in Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="use-credentials-in-a-python-2-runbook"></a>Usare le credenziali in un runbook Python 2

L'esempio seguente illustra l'accesso alle credenziali nei runbook Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere ai certificati, vedere [Gestire i moduli in Automazione di Azure](modules.md).
* Per informazioni generali sui runbook, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Panoramica di State Configuration di Automazione di Azure](../automation-dsc-overview.md). 
