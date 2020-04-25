---
title: Gestire le credenziali in automazione di Azure
description: Gli asset credenziali in Automazione di Azure includono le credenziali di sicurezza che possono essere usate per l'autenticazione nelle risorse a cui accede il Runbook o la configurazione DSC. Questo articolo illustra come creare asset credenziali e usarli in un Runbook o in una configurazione DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4226a625918be378b14e14c55fe4dd4ca5c398d5
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136686"
---
# <a name="manage-credentials-in-azure-automation"></a>Gestire le credenziali in automazione di Azure

Un asset delle credenziali di automazione contiene un oggetto che contiene le credenziali di sicurezza, ad esempio un nome utente e una password. Manuali operativi e le configurazioni DSC usano i cmdlet che accettano un oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) per l'autenticazione. In alternativa, è possibile estrarre il nome utente e la password dell' `PSCredential` oggetto da fornire ad alcune applicazioni o servizi che richiedono l'autenticazione. 

> [!NOTE]
> Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave viene archiviata in Key Vault. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell i cmdlet AZ usati per gli asset delle credenziali

Come parte del Azure PowerShell AZ Module, i cmdlet nella tabella seguente vengono usati per creare e gestire asset delle credenziali di automazione con Windows PowerShell. Vengono forniti nel [modulo AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC. Vedere [AZ Module Support in automazione di Azure](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Description |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera un oggetto [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) contenente i metadati relativi alla credenziale. Il cmdlet non recupera l' `PSCredential` oggetto stesso.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Crea nuove credenziali di Automazione. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Rimuove le credenziali di Automazione. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Imposta le proprietà per le credenziali di Automazione esistenti. |

## <a name="activities-used-to-access-credentials"></a>Attività utilizzate per accedere alle credenziali

Le attività descritte nella tabella seguente vengono usate per accedere alle credenziali nei manuali operativi grafici e nelle configurazioni DSC. Per esempi relativi all'uso delle attività, vedere [creazione grafica in automazione di Azure](../automation-graphical-authoring-intro.md#activities).

| Attività | Descrizione |
|:--- |:--- |
| `Get-AutomationPSCredential` |Ottiene un `PSCredential` oggetto da usare in una configurazione RUNBOOK o DSC. Spesso è consigliabile usare questa attività anziché il `Get-AzAutomationCredential` cmdlet, perché quest'ultimo recupera solo le informazioni sulle credenziali. Queste informazioni non sono in genere utili per passare a un altro cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Ottiene una credenziale con una richiesta di nome utente e password. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Crea un asset delle credenziali. |

Per recuperare `PSCredential` gli oggetti nel codice, è necessario importare il `Orchestrator.AssetManagement.Cmdlets` modulo. Per altre informazioni, vedere [gestire i moduli in automazione di Azure](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> È consigliabile evitare di usare le `Name` variabili nel parametro `Get-AutomationPSCredential`di. Il loro uso può complicare l'individuazione delle dipendenze tra manuali operativi o le configurazioni DSC e gli asset delle credenziali in fase di progettazione.

## <a name="python-2-functions-that-access-credentials"></a>Funzioni Python 2 che accedono alle credenziali

La funzione nella tabella seguente viene usata per accedere alle credenziali in un Runbook Python 2.

| Funzione | Description |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informazioni riguardo un asset credenziali. |

> [!NOTE]
> Importare il `automationassets` modulo nella parte superiore del Runbook Python per accedere alle funzioni di asset.

## <a name="creating-a-new-credential-asset"></a>Creazione di un nuovo asset credenziali

È possibile creare un nuovo asset credenziali usando il portale di Azure o con Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Creare un nuovo asset credenziali con il portale di Azure

1. Dall'account di automazione selezionare **credenziali** in **risorse condivise**.
1. Selezionare **Aggiungi credenziali**.
2. Nel riquadro nuova credenziale immettere un nome di credenziale appropriato secondo gli standard di denominazione. 
3. Digitare l'ID di accesso nel campo **nome utente** . 
4. Per entrambi i campi password immettere la chiave di accesso segreta.

    ![Crea nuove credenziali](../media/credentials/credential-create.png)

5. Se è selezionata la casella autenticazione a più fattori, deselezionarla. 
6. Fare clic su **Crea** per salvare il nuovo asset delle credenziali.

> [!NOTE]
> Automazione di Azure non supporta gli account utente che usano l'autenticazione a più fattori.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Creare un nuovo asset credenziali con Windows PowerShell

Nell'esempio seguente viene illustrato come creare un nuovo asset delle credenziali di automazione. Viene `PSCredential` innanzitutto creato un oggetto con il nome e la password e quindi viene usato per creare l'asset delle credenziali. In alternativa, è possibile utilizzare `Get-Credential` il cmdlet per richiedere all'utente di digitare un nome e una password.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Uso di credenziali PowerShell 

Una configurazione Runbook o DSC recupera un asset delle credenziali con `Get-AutomationPSCredential` l'attività. Questa attività recupera un `PSCredential` oggetto che è possibile usare con un'attività o un cmdlet che richiede una credenziale. È anche possibile recuperare le proprietà dell'oggetto credenziali da usare individualmente. L'oggetto dispone di proprietà per il nome utente e la password di sicurezza. In alternativa, è possibile usare il metodo [getnetworkcredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) per recuperare un oggetto [NetworkCredential](/dotnet/api/system.net.networkcredential) che rappresenta una versione non protetta della password.

> [!NOTE]
> `Get-AzAutomationCredential`non recupera un `PSCredential` oggetto che può essere utilizzato per l'autenticazione. Fornisce solo informazioni sulle credenziali. Se è necessario utilizzare le credenziali in un Runbook, è necessario recuperarlo come `PSCredential` oggetto utilizzando. `Get-AutomationPSCredential`

### <a name="textual-runbook-example"></a>Esempio di Runbook testuale

L'esempio seguente illustra come usare una credenziale di PowerShell in un Runbook. Recupera le credenziali e assegna il nome utente e la password alle variabili.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

È anche possibile usare le credenziali per eseguire l'autenticazione in Azure con [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Nella maggior parte dei casi, è consigliabile usare un [account RunAs](../manage-runas-account.md) e recuperare la connessione con [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Esempio di Runbook grafico

È possibile aggiungere un' `Get-AutomationPSCredential` attività a un Runbook grafico facendo clic con il pulsante destro del mouse sulla credenziale nel riquadro libreria dell'editor grafico e scegliendo **Aggiungi ad area di disegno**.

![Aggiungere credenziali all'area di disegno](../media/credentials/credential-add-canvas.png)

La figura seguente mostra un esempio dell'uso di credenziali in un Runbook grafico. In questo caso, la credenziale fornisce l'autenticazione per un Runbook di risorse di Azure, come descritto in [usare Azure ad in automazione di Azure per l'autenticazione in Azure](../automation-use-azure-ad.md). La prima attività recupera le credenziali che possono accedere alla sottoscrizione di Azure. L'attività di connessione account usa quindi questa credenziale per fornire l'autenticazione per tutte le attività successive. Qui viene usato un [collegamento](../automation-graphical-authoring-intro.md#links-and-workflow) alla pipeline `Get-AutomationPSCredential` perché prevede un singolo oggetto.  

![Aggiungere credenziali all'area di disegno](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Uso delle credenziali in una configurazione DSC

Anche se le configurazioni DSC in automazione di Azure possono funzionare con `Get-AutomationPSCredential`gli asset delle credenziali usando, possono anche passare le risorse delle credenziali tramite parametri. Per ulteriori informazioni vedere [Compilazione di configurazioni in Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Uso delle credenziali in Python 2

Nell'esempio seguente viene illustrato un esempio di accesso alle credenziali in Python 2 manuali operativi.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sui collegamenti nella creazione grafica, vedere [collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow).
* Per comprendere i diversi metodi di autenticazione per l'automazione, vedere [sicurezza di automazione di Azure](../automation-security-overview.md).
* Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](../automation-first-runbook-graphical.md).
* Per iniziare a usare manuali operativi del flusso di lavoro PowerShell, vedere [il primo Runbook del flusso di lavoro PowerShell](../automation-first-runbook-textual.md).
* Per iniziare a usare Python 2 manuali operativi, vedere [il primo Runbook Python 2](../automation-first-runbook-textual-python2.md). 
