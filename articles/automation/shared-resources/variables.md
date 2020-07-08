---
title: Gestire variabili in Automazione di Azure
description: Questo articolo descrive come usare le variabili nei runbook e nelle configurazioni DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 28f69d3ef8301e00b470ce09353be6ae3259bbe3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744971"
---
# <a name="manage-variables-in-azure-automation"></a>Gestire variabili in Automazione di Azure

Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC nell'account di Automazione. È possibile gestirli dal portale di Azure, da PowerShell, all'interno di un runbook o in una configurazione DSC.

Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più runbook o configurazioni DSC.

- Condivisione di un valore tra più processi dello stesso runbook o configurazione DSC.

- Gestione di un valore usato dai runbook o dalle configurazioni DSC dal portale o dalla riga di comando di PowerShell. Un esempio è un set di elementi di configurazione comuni, ad esempio un elenco specifico di nomi di macchine virtuali, un gruppo di risorse specifico, un nome di dominio AD e altro ancora.  

Automazione di Azure salva in modo permanente le variabili e le rende disponibili anche in caso di errore per un runbook o una configurazione DSC. Questo comportamento consente a un runbook o a una configurazione DSC di impostare un valore che viene quindi usato da un altro runbook o dallo stesso runbook o dalla stessa configurazione DSC alla successiva esecuzione.

Automazione di Azure archivia in modo sicuro ogni variabile crittografata. Quando si crea una variabile, è possibile specificare che venga crittografata e archiviata da Automazione di Azure come asset sicuro. 

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di Automazione. Automazione di Azure archivia la chiave nel Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, Automazione carica la chiave da Key Vault e quindi la usa per crittografare l'asset. 

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario selezionare un tipo di dati nell'elenco a discesa, in modo che nel portale sia visualizzato il controllo appropriato per immettere il valore della variabile. Di seguito sono riportati i tipi di variabile disponibili in Automazione di Azure:

* string
* Integer
* Datetime
* Boolean
* Null

La variabile non è limitata al tipo di dati specificato. È necessario impostarla usando Windows PowerShell se si vuole specificare un valore di tipo diverso. Se si indica `Not defined`, il valore della variabile viene impostato su Null. È necessario impostare il valore con il cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) o il cmdlet interno `Set-AutomationVariable`.

Non è possibile usare il portale di Azure per creare o modificare il valore per un tipo di variabile complesso. Tuttavia, è possibile fornire un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi verranno recuperati come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

>[!NOTE]
>Le variabili del nome della macchina virtuale possono avere una lunghezza massima di 80 caratteri. Le variabili dei gruppi di risorse possono contenere un massimo di 90 caratteri. Vedere [Regole di denominazione e restrizioni per le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-to-access-variables"></a>Cmdlet di PowerShell per accedere alle variabili

I cmdlet nella tabella seguente vengono usati per creare e gestire le variabili di Automazione con PowerShell. Sono inclusi nei [moduli Az](modules.md#az-modules).

| Cmdlet | Descrizione |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Recupera il valore di una variabile esistente. Se il valore è di tipo semplice, verrà recuperato lo stesso tipo. Se è un tipo complesso, viene recuperato un tipo `PSCustomObject`. <br>**Nota:**  non è possibile usare questo cmdlet per recuperare il valore di una variabile crittografata. L'unico modo per eseguire questa operazione consiste nell'usare il cmdlet `Get-AutomationVariable` interno in un runbook o una configurazione DSC. Vedere [Cmdlet interni per accedere alle variabili](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Rimuove una variabile esistente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Imposta il valore di una variabile esistente. |

## <a name="internal-cmdlets-to-access-variables"></a>Cmdlet interni per accedere alle variabili

I cmdlet interni nella tabella seguente vengono usati per accedere alle variabili nei runbook e nelle configurazioni DSC. Questi cmdlet sono inclusi nel modulo globale `Orchestrator.AssetManagement.Cmdlets`. Per altre informazioni, vedere [Cmdlet interni](modules.md#internal-cmdlets).

| Cmdlet interno | Descrizione |
|:---|:---|
|`Get-AutomationVariable`|Recupera il valore di una variabile esistente.|
|`Set-AutomationVariable`|Imposta il valore di una variabile esistente.|

> [!NOTE]
> Evitare di usare le variabili nel parametro `Name` di `Get-AutomationVariable` in un runbook o una configurazione DSC. L'uso delle variabili può complicare l'individuazione delle dipendenze tra runbook e variabili di Automazione in fase di progettazione.

`Get-AutomationVariable` non funziona in PowerShell, ma solo in un runbook o una configurazione DSC. Ad esempio, per visualizzare il valore di una variabile crittografata, è possibile creare un runbook per ottenere la variabile e quindi scriverla nel flusso di output:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Funzioni di Python 2 per accedere alle variabili

Le funzioni nella tabella seguente vengono usate per accedere alle variabili in un runbook di Python 2.

|Funzioni di Python 2|Descrizione|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera il valore di una variabile esistente. |
|`automationassets.set_automation_variable`|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario importare il modulo `automationassets` nella parte superiore del runbook Python per accedere alle funzioni dell'asset.

## <a name="create-and-get-a-variable"></a>Creare e ottenere una variabile

>[!NOTE]
>Se si vuole rimuovere la crittografia per una variabile, è necessario eliminare la variabile e ricrearla come non crittografata.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Creare e ottenere una variabile usando il portale di Azure

1. Dall'account di Automazione fare clic sul riquadro **Asset**, quindi sul pannello **Asset** e infine selezionare **Variabili**.
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **Nuova variabile** e quindi fare clic su **Crea** per salvare la nuova variabile.

> [!NOTE]
> Dopo averla salvata, una variabile crittografata non può essere visualizzata nel portale. Può essere solo aggiornata.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Creare e ottenere una variabile in Windows PowerShell

Il runbook o la configurazione DSC usa il cmdlet `New-AzAutomationVariable` per creare una nuova variabile e impostarne il valore iniziale. Se la variabile è crittografata, la chiamata deve usare il parametro `Encrypted`. Lo script può recuperare il valore della variabile usando `Get-AzAutomationVariable`. 

>[!NOTE]
>Uno script di PowerShell non può recuperare un valore crittografato. L'unico modo per eseguire questa operazione consiste nell'usare il cmdlet `Get-AutomationVariable` interno.

L'esempio seguente illustra come creare una variabile stringa e quindi restituirne il valore.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

L'esempio seguente illustra come creare una variabile di tipo complesso e quindi recuperarne le proprietà. In questo caso, viene usato un oggetto macchina virtuale recuperato da [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0).

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>Esempi di runbook testuali

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Recuperare e impostare un valore semplice da una variabile

L'esempio seguente illustra come impostare e recuperare una variabile in un runbook testuale. Questo esempio presuppone la creazione di variabili integer denominate `NumberOfIterations` e `NumberOfRunnings` e di una variabile stringa denominata `SampleMessage`.

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Recuperare e impostare una variabile in un runbook Python 2

L'esempio seguente illustra come recuperare una variabile, impostare una variabile e gestire un'eccezione per una variabile inesistente in un runbook di Python2.

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

## <a name="graphical-runbook-examples"></a>Esempi di runbook grafici

In un runbook grafico è possibile aggiungere attività per i cmdlet interni `Get-AutomationVariable` o `Set-AutomationVariable`. È sufficiente fare clic con il pulsante destro del mouse su ogni variabile nel riquadro della raccolta dell'editor grafico e selezionare l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio, l'attività per `Get-AzVM` recupera una singola macchina virtuale di Azure e salva il nome del computer in una variabile stringa di Automazione esistente. Non è importante se il [collegamento è una pipeline o una sequenza](../automation-graphical-authoring-intro.md#use-links-for-workflow) poiché il codice si aspetta un singolo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere alle variabili, vedere [Gestire i moduli in Automazione di Azure](modules.md).
* Per informazioni generali sui runbook, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Panoramica di State Configuration di Automazione di Azure](../automation-dsc-overview.md).
