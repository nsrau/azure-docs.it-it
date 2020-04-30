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
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732754"
---
# <a name="manage-variables-in-azure-automation"></a>Gestire le variabili in automazione di Azure

Gli Asset variabili sono valori disponibili per tutti manuali operativi e le configurazioni DSC nell'account di automazione. È possibile gestirli dalla portale di Azure, da PowerShell, all'interno di una Runbook o in una configurazione DSC.

Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più configurazioni manuali operativi o DSC.

- Condivisione di un valore tra più processi dalla stessa configurazione di Runbook o DSC.

- Gestione di un valore usato da manuali operativi o dalle configurazioni DSC dal portale o dalla riga di comando di PowerShell. Un esempio è un set di elementi di configurazione comuni, ad esempio un elenco specifico di nomi di VM, un gruppo di risorse specifico, un nome di dominio AD e altro ancora.  

Automazione di Azure Salva in modo permanente le variabili e le rende disponibili anche se la configurazione di Runbook o DSC ha esito negativo. Questo comportamento consente a una configurazione Runbook o DSC di impostare un valore che viene quindi usato da un altro Runbook o dalla stessa configurazione Runbook o DSC alla successiva esecuzione.

Automazione di Azure archivia in modo sicuro ogni variabile crittografata. Quando si crea una variabile, è possibile specificarne la crittografia e l'archiviazione da automazione di Azure come asset protetto. Altre risorse protette includono credenziali, certificati e connessioni. Automazione di Azure crittografa questi asset e li archivia usando una chiave univoca generata per ogni account di automazione. La chiave viene archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, automazione di Azure carica la chiave dalla Key Vault e la usa per crittografare l'asset. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario specificare un tipo di dati nell'elenco a discesa in modo che il portale possa visualizzare il controllo appropriato per immettere il valore della variabile. Di seguito sono riportati i tipi di variabili disponibili in automazione di Azure:

* Stringa
* Integer
* Datetime
* Boolean
* Null

La variabile non è limitata al tipo di dati designato. È necessario impostare la variabile utilizzando Windows PowerShell se si desidera specificare un valore di tipo diverso. Se si indica `Not defined`, il valore della variabile viene impostato su null. È necessario impostare il valore con il cmdlet [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) o l' `Set-AutomationVariable` attività.

Non è possibile usare la portale di Azure per creare o modificare il valore per un tipo di variabile complesso. Tuttavia, è possibile fornire un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi vengono recuperati come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

>[!NOTE]
>Le variabili nome macchina virtuale possono avere una lunghezza massima di 80 caratteri. Le variabili del gruppo di risorse possono contenere un massimo di 90 caratteri. Vedere [regole di denominazione e restrizioni per le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Cmdlet di PowerShell per la creazione e la gestione di Asset variabili

Per il modulo AZ, i cmdlet della tabella seguente vengono usati per creare e gestire asset di variabili di automazione con Windows PowerShell. Vengono forniti come parte del [modulo AZ. Automation](/powershell/azure/overview), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC.

| Cmdlet | Descrizione |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Recupera il valore di una variabile esistente. Non è possibile utilizzare questo cmdlet per recuperare il valore di una variabile crittografata. L'unico modo per eseguire questa operazione è usare l' `Get-AutomationVariable` attività in una configurazione RUNBOOK o DSC. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Rimuove una variabile esistente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Imposta il valore di una variabile esistente. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Attività per accedere alle variabili in manuali operativi e nelle configurazioni DSC

Le attività nella tabella seguente vengono usate per accedere alle variabili in manuali operativi e nelle configurazioni DSC. I cmdlet per queste attività sono dotati del modulo `Orchestrator.AssetManagement.Cmdlets`globale.

| Attività | Descrizione |
|:---|:---|
|`Get-AutomationVariable`|Recupera il valore di una variabile esistente.|
|`Set-AutomationVariable`|Imposta il valore di una variabile esistente.|

> [!NOTE]
> Evitare di usare le `Name` variabili nel parametro `Get-AutomationVariable` di in una configurazione Runbook o DSC. L'uso di questo parametro può complicare l'individuazione delle dipendenze tra manuali operativi o le configurazioni DSC e le variabili di automazione in fase di progettazione.

Si noti `Get-AutomationVariable` che non funziona in PowerShell, ma solo in una configurazione RUNBOOK o DSC. Ad esempio, per visualizzare il valore di una variabile crittografata, è possibile creare un Runbook per ottenere la variabile e quindi scriverla nel flusso di output:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funzioni per accedere alle variabili in Python 2 manuali operativi

Le funzioni nella tabella seguente vengono usate per accedere alle variabili in un Runbook Python 2.

|Funzioni di Python 2|Descrizione|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera il valore di una variabile esistente. |
|`automationassets.set_automation_variable`|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario importare il `automationassets` modulo nella parte superiore del Runbook Python per accedere alle funzioni di asset.

## <a name="working-with-automation-variables"></a>Utilizzo delle variabili di automazione

>[!NOTE]
>Se si desidera rimuovere la crittografia per una variabile, è necessario eliminare la variabile e ricrearla come non crittografata.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Crea una nuova variabile usando il portale di Azure

1. Dall'account di automazione fare clic sul riquadro **Asset** , quindi sul pannello **Asset** e selezionare **variables (variabili**).
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **nuova variabile** e quindi fare clic su **Crea** per salvare la nuova variabile.

> [!NOTE]
> Una volta salvata, una variabile crittografata non può essere visualizzata nel portale. E può essere aggiornato.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Creare e usare una variabile in Windows PowerShell

Uno script di PowerShell usa `New-AzAutomationVariable` il cmdlet o il relativo modulo AzureRM equivalente per creare una nuova variabile e impostarne il valore iniziale. Se la variabile è crittografata, la chiamata deve utilizzare `Encrypted` il parametro.

Lo script recupera il valore della variabile utilizzando [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Se il valore è un tipo semplice, il cmdlet recupera lo stesso tipo. Se si tratta di un tipo complesso, `PSCustomObject` viene recuperato un tipo.

>[!NOTE]
>Uno script di PowerShell non è in grado di recuperare un valore crittografato. L'unico modo per eseguire questa operazione consiste nell'usare `Get-AutomationVariable` un'attività in una configurazione RUNBOOK o DSC.

Nell'esempio seguente viene illustrato come creare una variabile di tipo stringa e quindi restituirne il valore.

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

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Creare e usare una variabile in un Runbook o una configurazione DSC

L'unico modo per creare una nuova variabile dall'interno di un Runbook o una configurazione DSC consiste nell' `New-AzAutomationVariable` usare il cmdlet o il relativo modulo AzureRM equivalente. Lo script utilizza questo cmdlet per impostare il valore iniziale della variabile. Lo script può quindi recuperare il valore usando `Get-AzAutomationVariable`. Se il valore è un tipo semplice, viene recuperato lo stesso tipo. Se è un tipo complesso, viene recuperato un `PSCustomObject` tipo.

>[!NOTE]
>L'unico modo per recuperare un valore crittografato consiste nell'usare `Get-AutomationVariable` l'attività nella configurazione RUNBOOK o DSC. 

### <a name="textual-runbook-samples"></a>Esempi di runbook testuali

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Impostare e recuperare un valore semplice da una variabile

I comandi di esempio seguenti mostrano come impostare e recuperare una variabile in un runbook testuale. In questo esempio si presuppone la creazione di variabili `NumberOfIterations` integer `NumberOfRunnings` denominate e e `SampleMessage`una variabile di stringa denominata.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Impostare e recuperare una variabile in un Runbook Python 2

L'esempio seguente illustra come usare una variabile, impostare una variabile e gestire un'eccezione per una variabile inesistente in un Runbook Python 2.

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

In un Runbook grafico è possibile aggiungere l' `Get-AutomationVariable` attività o `Set-AutomationVariable` . È sufficiente fare clic con il pulsante destro del mouse sulla variabile nel riquadro libreria dell'editor grafico e selezionare l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Impostare i valori in una variabile

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio, `Get-AzVM` recupera una singola macchina virtuale di Azure e salva il nome del computer in una variabile di stringa di automazione esistente. Non è importante se il [collegamento è una pipeline o una sequenza](../automation-graphical-authoring-intro.md#links-and-workflow) perché il codice prevede solo un singolo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla connessione delle attività nella creazione grafica, vedere [collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow).
- Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](../automation-first-runbook-graphical.md).
