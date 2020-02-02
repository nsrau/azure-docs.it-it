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
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930408"
---
# <a name="credential-assets-in-azure-automation"></a>Asset credenziali in Automazione di Azure

Un asset delle credenziali di automazione contiene un oggetto che contiene le credenziali di sicurezza, ad esempio un nome utente e una password. I Runbook e le configurazioni DSC possono usare i cmdlet che accettano un oggetto PSCredential per l'autenticazione oppure possono estrarre il nome utente e la password dell'oggetto PSCredential per fornirli ad alcune applicazioni o servizi che richiedono l'autenticazione. Le proprietà delle credenziali sono archiviate in modo sicuro in Automazione di Azure ed è possibile accedervi nel Runbook o nella configurazione DSC con l'attività [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave viene archiviata in Key Vault. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset.

## <a name="azure-powershell-az-cmdlets"></a>Cmdlet di Azure PowerShell AZ

Per Azure PowerShell AZ Module, i cmdlet della tabella seguente vengono usati per creare e gestire asset delle credenziali di automazione con Windows PowerShell. Vengono forniti come parte del [modulo AzureAz. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC.

| Cmdlet | Description |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Recupera informazioni riguardo un asset credenziali. Questa operazione non restituisce un oggetto PSCredential.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Crea nuove credenziali di Automazione. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Rimuove le credenziali di Automazione. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Imposta le proprietà per le credenziali di Automazione esistenti. |

## <a name="activities"></a>Attività

Le attività incluse nella tabella seguente vengono usate per accedere alle credenziali in un Runbook o nelle configurazioni DSC.

| Attività | Description |
|:--- |:--- |
| Get-AutomationPSCredential |Ottiene una credenziale da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> È consigliabile evitare di usare le variabili nel parametro –Name di Get-AutomationPSCredential, poiché ciò può complicare l'individuazione delle dipendenze tra i Runbook o configurazioni DSC e gli asset credenziali durante la fase di progettazione.

## <a name="python2-functions"></a>Funzioni Python2

La funzione nella tabella seguente viene usata per accedere alle credenziali in un runbook Python2.

| Funzione | Description |
|:---|:---|
| automationassets.get_automation_credential | Recupera informazioni riguardo un asset credenziali. |

> [!NOTE]
> È necessario importare il modulo "automationassets" nella parte superiore del runbook Python per poter accedere alle funzioni dell'asset.

## <a name="creating-a-new-credential-asset"></a>Creazione di un nuovo asset credenziali

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Per creare un nuovo asset credenziali con il portale di Azure

1. Con il proprio account di Automazione, selezionare **Credenziali** in **Risorse condivise**.
1. Selezionare **Aggiungi credenziali**.
1. Completare il modulo e selezionare **Crea** per salvare le nuove credenziali.

> [!NOTE]
> Gli account utente che usano l'autenticazione a più fattori non sono supportati per l'uso in Automazione di Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Per creare un nuovo asset credenziali con Windows PowerShell

I comandi di esempio seguenti mostrano come creare nuove credenziali di Automazione. Viene prima creato un oggetto PSCredential con il nome e la password, che verrà quindi usato per creare l'asset credenziali. In alternativa, usare il cmdlet **Get-Credential** per ricevere la richiesta di digitare nome e password.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Uso di credenziali PowerShell

L'attività **Get-AutomationPSCredential** permette di recuperare un asset credenziali in un Runbook o configurazione DSC. Verrà restituito un [PSCredential object](/dotnet/api/system.management.automation.pscredential) da usare con un'attività o un cmdlet che richiede un parametro PSCredential. È anche possibile recuperare le proprietà dell'oggetto credenziali da usare individualmente. L'oggetto ha una proprietà per il nome utente e la password protetta oppure è possibile usare il metodo **GetNetworkCredential** per restituire un oggetto [NetworkCredential](/dotnet/api/system.net.networkcredential) che fornirà una versione non protetta della password.

> [!NOTE]
> **Get-AzAutomationCredential** non restituisce un **PSCredential** che può essere usato per l'autenticazione. Fornisce solo informazioni sulle credenziali. Se è necessario usare una credenziale in un Runbook, è necessario usare **Get-AutomationPSCredential** per recuperare l'oggetto **PSCredential** .

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale

I comandi di esempio seguenti mostrano come usare le credenziali PowerShell in un Runbook. In questo esempio, vengono recuperate le credenziali e il nome utente e la password vengono assegnati alle variabili.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

È anche possibile usare le credenziali per eseguire l'autenticazione in Azure con [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Nella maggior parte dei casi, è consigliabile usare un [account RunAs](../manage-runas-account.md) e recuperarlo con [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Esempio di Runbook grafico

Per aggiungere un'attività **Get-AutomationPSCredential** a un runbook grafico, fare clic con il pulsante destro del mouse sulle credenziali nel riquadro della libreria dell'editor grafico e scegliere **Aggiungi ad area di disegno**.

![Aggiungere credenziali all'area di disegno](../media/credentials/credential-add-canvas.png)

La figura seguente mostra un esempio dell'uso di credenziali in un Runbook grafico. In questo caso, viene usato per fornire l'autenticazione per un Runbook alle risorse di Azure, come descritto in [autenticazione di manuali operativi con Azure ad account utente](../automation-create-aduser-account.md). La prima attività recupera le credenziali che possono accedere alla sottoscrizione di Azure. L'attività **Connect-AzureRmAccount** usa quindi questa credenziale per fornire l'autenticazione per tutte le attività che lo derivano. Viene usato un [collegamento pipeline](../automation-graphical-authoring-intro.md#links-and-workflow) poiché **Get-AutomationPSCredential** prevede un singolo oggetto.  

![Aggiungere credenziali all'area di disegno](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Uso di credenziali PowerShell in DSC

Mentre le configurazioni DSC in automazione di Azure possono fare riferimento ad asset di credenziali usando **Get-AutomationPSCredential**, gli asset delle credenziali possono essere passati anche tramite parametri, se lo si desidera. Per ulteriori informazioni vedere [Compilazione di configurazioni in Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Uso delle credenziali in Python2

L'esempio seguente illustra un esempio di accesso alle credenziali dei runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui collegamenti nella creazione grafica, vedere [Creazione grafica in Automazione di Azure](../automation-graphical-authoring-intro.md#links-and-workflow)
* Per comprendere i diversi metodi di autenticazione con Automazione, vedere [Sicurezza in Automazione di Azure](../automation-security-overview.md)
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](../automation-first-runbook-graphical.md)
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](../automation-first-runbook-textual.md)
* Per iniziare a usare i runbook di Python2, vedere [Il primo runbook Python2](../automation-first-runbook-textual-python2.md) 
