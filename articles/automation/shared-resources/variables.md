---
title: Asset di tipo variabile in Automazione di Azure
description: Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC in Automazione di Azure.  Questo articolo illustra nel dettaglio le variabili e spiega come usarle nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 598be26024c22ba81c3f33510423605abc854b13
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216820"
---
# <a name="variable-assets-in-azure-automation"></a>Asset di tipo variabile in Automazione di Azure

Gli Asset variabili sono valori disponibili per tutti manuali operativi e le configurazioni DSC nell'account di automazione. È possibile gestirli dalla portale di Azure, da PowerShell, all'interno di una Runbook o in una configurazione DSC.

Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più configurazioni manuali operativi o DSC.

- Condivisione di un valore tra più processi dalla stessa configurazione di Runbook o DSC.

- Gestione di un valore usato da manuali operativi o dalle configurazioni DSC dal portale o dalla riga di comando di PowerShell. Un esempio è un set di elementi di configurazione comuni, ad esempio un elenco specifico di nomi di VM, un gruppo di risorse specifico, un nome di dominio AD e altro ancora.  

Poiché le variabili di automazione sono persistenti, sono disponibili anche se la configurazione di Runbook o DSC ha esito negativo. Questo comportamento consente a una configurazione Runbook o DSC di impostare un valore che viene quindi usato da un altro Runbook o dalla stessa configurazione Runbook o DSC alla successiva esecuzione.

Quando si crea una variabile, è possibile specificarne la crittografia e l'archiviazione da automazione di Azure come asset protetto. Altre risorse protette includono credenziali, certificati e connessioni. Automazione di Azure crittografa questi asset e li archivia usando una chiave univoca generata per ogni account di automazione. La chiave viene archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, automazione di Azure carica la chiave dalla Key Vault e la usa per crittografare l'asset. 

Automazione di Azure archivia in modo sicuro ogni variabile crittografata. Il relativo valore non può essere recuperato con il cmdlet [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) incluso nel modulo Azure PowerShell. L'unico modo per recuperare un valore crittografato consiste nell'usare l'attività `Get-AutomationVariable` in una configurazione Runbook o DSC.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario specificare un tipo di dati nell'elenco a discesa in modo che il portale possa visualizzare il controllo appropriato per immettere il valore della variabile. Di seguito sono riportati i tipi di variabili disponibili in automazione di Azure:

* String
* Integer
* Datetime
* Boolean
* Null

La variabile non è limitata al tipo di dati designato. È necessario impostare la variabile utilizzando Windows PowerShell se si desidera specificare un valore di tipo diverso. Se si indica che **non è definito**, il valore della variabile viene impostato su null ed è necessario impostare il valore con il cmdlet [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) o l'attività `Set-AutomationVariable`.

Non è possibile usare il portale per creare o modificare il valore per un tipo di variabile complesso. Tuttavia, è possibile fornire un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi vengono recuperati come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvando il file in una variabile.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Cmdlet di PowerShell per la creazione e la gestione di Asset variabili

Per il modulo AZ, i cmdlet della tabella seguente vengono usati per creare e gestire asset di variabili di automazione con Windows PowerShell. Vengono forniti come parte del [modulo AZ. Automation](/powershell/azure/overview), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC.

| Cmdlet | Descrizione |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Recupera il valore di una variabile esistente.|
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Rimuove una variabile esistente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Imposta il valore di una variabile esistente.|

## <a name="activities-to-access-variables"></a>Attività per accedere alle variabili

Le attività nella tabella seguente vengono usate per accedere alle variabili in manuali operativi e nelle configurazioni DSC. La differenza tra `Get-AzAutomationVariable` e `Get-AutomationVariable` viene illustrata per le variabili crittografate all'inizio di questo articolo.

| Attività | Descrizione |
|:---|:---|
|`Get-AutomationVariable`|Recupera il valore di una variabile esistente.|
|`Set-AutomationVariable`|Imposta il valore di una variabile esistente.|

> [!NOTE]
> Evitare di usare le variabili nel parametro `Name` di `Get-AutomationVariable` in una configurazione Runbook o DSC. L'uso di questo parametro può complicare l'individuazione delle dipendenze tra manuali operativi o le configurazioni DSC e le variabili di automazione in fase di progettazione.

Le funzioni nella tabella seguente vengono usate per accedere e recuperare le variabili in un runbook di Python2.

|Funzioni Python2|Descrizione|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera il valore di una variabile esistente. |
|`automationassets.set_automation_variable`|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario importare il modulo `automationassets` nella parte superiore del Runbook Python per accedere alle funzioni di asset.

## <a name="creating-a-new-automation-variable"></a>Creazione di una nuova variabile di automazione

### <a name="create-a-new-variable-using-the-azure-portal"></a>Crea una nuova variabile usando il portale di Azure

1. Dall'account di automazione fare clic sul riquadro **Asset** , quindi sul pannello **Asset** e selezionare **variables (variabili**).
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **nuova variabile** e quindi fare clic su **Crea** per salvare la nuova variabile.

>[!NOTE]
>Se si desidera rimuovere la crittografia per una variabile, è necessario eliminare la variabile e ricrearla come non crittografata.

### <a name="create-a-new-variable-with-windows-powershell"></a>Creare una nuova variabile con Windows PowerShell

Lo script usa il cmdlet `New-AzAutomationVariable` per creare una nuova variabile e impostarne il valore iniziale. Può quindi recuperare il valore utilizzando `Get-AzAutomationVariable`. Se il valore è un tipo semplice, viene recuperato lo stesso tipo. Se è un tipo complesso, viene recuperato un tipo di `PSCustomObject`.

Nell'esempio seguente viene illustrato come creare una variabile di tipo stringa e quindi restituirne il valore.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Nell'esempio seguente viene illustrato come creare una variabile con un tipo complesso e quindi come recuperarne le proprietà. In questo caso, viene usato un oggetto macchina virtuale di [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Uso di una variabile in un Runbook o in una configurazione DSC

Usare l'attività `Set-AutomationVariable` per impostare il valore di una variabile di automazione in una configurazione di Runbook o DSC di PowerShell e il `Get-AutomationVariable` per recuperarla. Non usare i cmdlet `Set-AzAutomationVariable` e `Get-AzAutomationVariable` o i relativi equivalenti del modulo AzureRM in una configurazione Runbook o DSC, perché sono meno efficienti delle attività del flusso di lavoro. 

Si noti che non è possibile recuperare il valore di una variabile protetta con `Get-AzAutomationVariable` o il relativo modulo AzureRM equivalente. 

L'unico modo per creare una nuova variabile dall'interno di un Runbook o una configurazione DSC consiste nell'usare il cmdlet `New-AzAutomationVariable`.

### <a name="textual-runbook-samples"></a>Esempi di runbook testuali

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Impostare e recuperare un valore semplice da una variabile

I comandi di esempio seguenti mostrano come impostare e recuperare una variabile in un runbook testuale. Questo esempio presuppone la creazione di variabili integer denominate `NumberOfIterations` e `NumberOfRunnings` e una variabile stringa denominata `SampleMessage`.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Impostare e recuperare una variabile in un Runbook python2

Nell'esempio seguente viene illustrato come utilizzare una variabile, impostare una variabile e gestire un'eccezione per una variabile inesistente in un Runbook python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Esempi di Runbook grafici

In un Runbook grafico è possibile aggiungere l'attività `Get-AutomationVariable` o `Set-AutomationVariable`. È sufficiente fare clic con il pulsante destro del mouse sulla variabile nel riquadro libreria dell'editor grafico e selezionare l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Impostare i valori in una variabile

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio `Get-AzVM` recupera una singola macchina virtuale di Azure e salva il nome del computer in una variabile di stringa di automazione esistente. Non è importante se il [collegamento è una pipeline o una sequenza](../automation-graphical-authoring-intro.md#links-and-workflow) perché il codice prevede solo un singolo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla connessione delle attività nella creazione grafica, vedere [collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow).
- Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](../automation-first-runbook-graphical.md).
