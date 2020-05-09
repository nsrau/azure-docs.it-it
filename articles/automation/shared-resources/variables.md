---
title: Gestire le variabili in automazione di Azure
description: Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC in Automazione di Azure.  Questo articolo illustra nel dettaglio le variabili e spiega come usarle nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bf7840daad02f679cad4c3b798d2add02c863a15
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651958"
---
# <a name="manage-variables-in-azure-automation"></a>Gestire le variabili in automazione di Azure

Gli Asset variabili sono valori disponibili per tutti manuali operativi e le configurazioni DSC nell'account di automazione. È possibile gestirli dalla portale di Azure, da PowerShell, all'interno di una Runbook o in una configurazione DSC.

Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più configurazioni manuali operativi o DSC.

- Condivisione di un valore tra più processi dalla stessa configurazione di Runbook o DSC.

- Gestione di un valore usato da manuali operativi o dalle configurazioni DSC dal portale o dalla riga di comando di PowerShell. Un esempio è un set di elementi di configurazione comuni, ad esempio un elenco specifico di nomi di VM, un gruppo di risorse specifico, un nome di dominio AD e altro ancora.  

Automazione di Azure Salva in modo permanente le variabili e le rende disponibili anche se la configurazione di Runbook o DSC ha esito negativo. Questo comportamento consente a una configurazione Runbook o DSC di impostare un valore che viene quindi usato da un altro Runbook o dalla stessa configurazione Runbook o DSC alla successiva esecuzione.

Automazione di Azure archivia in modo sicuro ogni variabile crittografata. Quando si crea una variabile, è possibile specificarne la crittografia e l'archiviazione da automazione di Azure come asset protetto. 

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in automazione di Azure usando una chiave univoca generata per ogni account di automazione. Automazione di Azure archivia la chiave nel Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, l'automazione carica la chiave da Key Vault e la usa per crittografare l'asset. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario specificare un tipo di dati nell'elenco a discesa in modo che il portale possa visualizzare il controllo appropriato per immettere il valore della variabile. Di seguito sono riportati i tipi di variabili disponibili in automazione di Azure:

* string
* Integer
* Datetime
* Boolean
* Null

La variabile non è limitata al tipo di dati specificato. È necessario impostare la variabile utilizzando Windows PowerShell se si desidera specificare un valore di tipo diverso. Se si indica `Not defined`, il valore della variabile viene impostato su null. È necessario impostare il valore con il cmdlet [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) o il cmdlet `Set-AutomationVariable` Internal.

Non è possibile usare la portale di Azure per creare o modificare il valore per un tipo di variabile complesso. Tuttavia, è possibile fornire un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi vengono recuperati come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

>[!NOTE]
>Le variabili nome macchina virtuale possono avere una lunghezza massima di 80 caratteri. Le variabili del gruppo di risorse possono contenere un massimo di 90 caratteri. Vedere [regole di denominazione e restrizioni per le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-to-access-variables"></a>Cmdlet di PowerShell per accedere alle variabili

I cmdlet nella tabella seguente creano e gestiscono le variabili di automazione con PowerShell. Vengono forniti come parte dei [moduli AZ](modules.md#az-modules).

| Cmdlet | Descrizione |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Recupera il valore di una variabile esistente. Se il valore è un tipo semplice, viene recuperato lo stesso tipo. Se si tratta di un tipo complesso, `PSCustomObject` viene recuperato un tipo. <br>**Nota:**  Non è possibile utilizzare questo cmdlet per recuperare il valore di una variabile crittografata. L'unico modo per eseguire questa operazione consiste nell'usare il `Get-AutomationVariable` cmdlet interno in una configurazione RUNBOOK o DSC. Vedere [cmdlet interni per accedere alle variabili](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Rimuove una variabile esistente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Imposta il valore di una variabile esistente. |

## <a name="internal-cmdlets-to-access-variables"></a>Cmdlet interni per accedere alle variabili

I cmdlet interni della tabella seguente vengono usati per accedere alle variabili nei manuali operativi e nelle configurazioni DSC. Questi cmdlet sono dotati del modulo `Orchestrator.AssetManagement.Cmdlets`globale. Per ulteriori informazioni, vedere [cmdlet interni](modules.md#internal-cmdlets).

| Cmdlet interno | Descrizione |
|:---|:---|
|`Get-AutomationVariable`|Recupera il valore di una variabile esistente.|
|`Set-AutomationVariable`|Imposta il valore di una variabile esistente.|

> [!NOTE]
> Evitare di usare le `Name` variabili nel parametro `Get-AutomationVariable` di in una configurazione Runbook o DSC. L'uso delle variabili può complicare l'individuazione delle dipendenze tra manuali operativi e le variabili di automazione in fase di progettazione.

`Get-AutomationVariable`non funziona in PowerShell, ma solo in una configurazione Runbook o DSC. Ad esempio, per visualizzare il valore di una variabile crittografata, è possibile creare un Runbook per ottenere la variabile e quindi scriverla nel flusso di output:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Funzioni di Python 2 per accedere alle variabili

Le funzioni nella tabella seguente vengono usate per accedere alle variabili in un Runbook Python 2.

|Funzioni di Python 2|Descrizione|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera il valore di una variabile esistente. |
|`automationassets.set_automation_variable`|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario importare il `automationassets` modulo nella parte superiore del Runbook Python per accedere alle funzioni di asset.

## <a name="create-and-get-a-variable"></a>Creare e ottenere una variabile

>[!NOTE]
>Se si desidera rimuovere la crittografia per una variabile, è necessario eliminare la variabile e ricrearla come non crittografata.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Creare e ottenere una variabile usando il portale di Azure

1. Dall'account di automazione fare clic sul riquadro **Asset** , quindi sul pannello **Asset** e selezionare **variables (variabili**).
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **nuova variabile** e quindi fare clic su **Crea** per salvare la nuova variabile.

> [!NOTE]
> Una volta salvata, una variabile crittografata non può essere visualizzata nel portale. E può essere aggiornato.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Creare e ottenere una variabile in Windows PowerShell

La configurazione di Runbook o DSC USA `New-AzAutomationVariable` il cmdlet per creare una nuova variabile e impostarne il valore iniziale. Se la variabile è crittografata, la chiamata deve utilizzare `Encrypted` il parametro. Lo script può recuperare il valore della variabile usando `Get-AzAutomationVariable`. 

>[!NOTE]
>Uno script di PowerShell non è in grado di recuperare un valore crittografato. L'unico modo per eseguire questa operazione consiste nell'usare il `Get-AutomationVariable` cmdlet Internal.

Nell'esempio seguente viene illustrato come creare una variabile stringa e quindi restituirne il valore.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Nell'esempio seguente viene illustrato come creare una variabile con un tipo complesso e quindi recuperare le relative proprietà. In questo caso, viene usato un oggetto macchina virtuale di [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>Esempi di Runbook testuali

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Recuperare e impostare un valore semplice da una variabile

Nell'esempio seguente viene illustrato come impostare e recuperare una variabile in un Runbook testuale. In questo esempio si presuppone la creazione di variabili `NumberOfIterations` integer `NumberOfRunnings` denominate e e `SampleMessage`una variabile di stringa denominata.

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Recuperare e impostare una variabile in un Runbook Python 2

Nell'esempio seguente viene illustrato come ottenere una variabile, impostare una variabile e gestire un'eccezione per una variabile inesistente in un Runbook Python 2.

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

## <a name="graphical-runbook-examples"></a>Esempi di Runbook grafici

In un Runbook grafico è possibile aggiungere attività per i cmdlet interni `Get-AutomationVariable` o. `Set-AutomationVariable` È sufficiente fare clic con il pulsante destro del mouse su ogni variabile nel riquadro libreria dell'editor grafico e selezionare l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

Nell'immagine seguente vengono illustrate le attività di esempio per aggiornare una variabile con un valore semplice in un Runbook grafico. In questo esempio l'attività per `Get-AzVM` recupera una singola macchina virtuale di Azure e salva il nome del computer in una variabile di stringa di automazione esistente. Non è importante se il [collegamento è una pipeline o una sequenza](../automation-graphical-authoring-intro.md#links-and-workflow) perché il codice prevede solo un singolo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere alle variabili, vedere [gestire i moduli in automazione di Azure](modules.md).
* Per informazioni generali su manuali operativi, vedere [esecuzione di Runbook in automazione di Azure](../automation-runbook-execution.md).
* Per informazioni dettagliate sulle configurazioni DSC, vedere [Cenni preliminari sulla configurazione dello stato](../automation-dsc-overview.md).
