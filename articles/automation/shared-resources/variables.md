---
title: Gestire le variabili nell'automazione di AzureManage variables in Azure Automation
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732754"
---
# <a name="manage-variables-in-azure-automation"></a>Gestire le variabili nell'automazione di AzureManage variables in Azure Automation

Le risorse variabili sono valori disponibili per tutti i runbook e le configurazioni DSC nell'account di Automazione. È possibile gestirli dal portale di Azure, da PowerShell, in un runbook o in una configurazione DSC.

Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più runbook o configurazioni DSC.

- Condivisione di un valore tra più processi dalla stessa configurazione runbook o DSC.

- Gestione di un valore utilizzato dai runbook o dalle configurazioni DSC dal portale o dalla riga di comando di PowerShell.Managing a value used by runbooks or DSC configurations from the portal or from the PowerShell command line. Un esempio è un set di elementi di configurazione comuni, ad esempio un elenco specifico di nomi di macchine virtuali, un gruppo di risorse specifico, un nome di dominio active Directory e altro ancora.  

Automazione di Azure rende persistenti le variabili e le rende disponibili anche in caso di errore di configurazione di un runbook o DSC. Questo comportamento consente a una configurazione runbook o DSC di impostare un valore che viene quindi utilizzato da un altro runbook o dallo stesso runbook o configurazione DSC alla successiva esecuzione.

Automazione di Azure archivia ogni variabile crittografata in modo sicuro. Quando si crea una variabile, è possibile specificarne la crittografia e l'archiviazione di Automazione di Azure come asset sicuro. Altre risorse sicure includono credenziali, certificati e connessioni. Automazione di Azure crittografa questi asset e li archivia usando una chiave univoca generata per ogni account di Automazione.Azure Automation encrypts these assets and stores them using a unique key that is generated for each Automation account. La chiave viene archiviata in un insieme di credenziali delle chiavi gestito dal sistema. Prima di archiviare un asset sicuro, Automazione di Azure carica la chiave dall'insieme di credenziali delle chiavi e quindi la usa per crittografare l'asset. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario specificare un tipo di dati dall'elenco a discesa in modo che il portale possa visualizzare il controllo appropriato per l'immissione del valore della variabile. Di seguito sono disponibili tipi di variabili in Automazione di Azure:The following are variable types available in Azure Automation:

* string
* Integer
* Datetime
* Boolean
* Null

La variabile non è limitata al tipo di dati designato. È necessario impostare la variabile utilizzando Windows PowerShell se si desidera specificare un valore di un tipo diverso. Se si `Not defined`indica , il valore della variabile è impostato su Null. È necessario impostare il valore con il `Set-AutomationVariable` cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) o l'attività.

Non è possibile usare il portale di Azure per creare o modificare il valore per un tipo di variabile complessa. Tuttavia, è possibile fornire un valore di qualsiasi tipo utilizzando Windows PowerShell.However, you can provide a value of any type using Windows PowerShell. I tipi complessi vengono recuperati come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

>[!NOTE]
>Le variabili nome macchina virtuale possono essere al massimo di 80 caratteri. Le variabili del gruppo di risorse possono essere al massimo di 90 caratteri. Vedere [Regole di denominazione e restrizioni per](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)le risorse di Azure.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Cmdlet di PowerShell che creano e gestiscono asset variabili

Per il modulo Az, i cmdlet nella tabella seguente vengono utilizzati per creare e gestire asset variabili di automazione con Windows PowerShell. Vengono spediti come parte del [modulo Az.Automation](/powershell/azure/overview), disponibile per l'utilizzo nei runbook di automazione e nelle configurazioni DSC.

| Cmdlet | Descrizione |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Recupera il valore di una variabile esistente. Non è possibile utilizzare questo cmdlet per recuperare il valore di una variabile crittografata. L'unico modo per eseguire `Get-AutomationVariable` questa operazione consiste nell'utilizzare l'attività in un runbook o in una configurazione DSC. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Rimuove una variabile esistente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Imposta il valore di una variabile esistente. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Attività per accedere alle variabili nei runbook e nelle configurazioni DSC

Le attività nella tabella seguente vengono utilizzate per accedere alle variabili nei runbook e nelle configurazioni DSC. I cmdlet per queste attività vengono `Orchestrator.AssetManagement.Cmdlets`forniti con il modulo globale .

| Attività | Descrizione |
|:---|:---|
|`Get-AutomationVariable`|Recupera il valore di una variabile esistente.|
|`Set-AutomationVariable`|Imposta il valore di una variabile esistente.|

> [!NOTE]
> Evitare di `Name` utilizzare `Get-AutomationVariable` variabili nel parametro di in un runbook o nella configurazione DSC. L'utilizzo di questo parametro può complicare l'individuazione delle dipendenze tra runbook o configurazioni DSC e variabili di automazione in fase di progettazione.

Si `Get-AutomationVariable` noti che non funziona in PowerShell, ma solo in una configurazione runbook o DSC. Ad esempio, per visualizzare il valore di una variabile crittografata, è possibile creare un runbook per ottenere la variabile e quindi scriverlo nel flusso di output:For example, to see the value of an encrypted variable, you might create a runbook to get the variable and then write it to the output stream:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funzioni per accedere alle variabili nei runbook di Python 2Functions to access variables in Python 2 runbooks

Le funzioni nella tabella seguente vengono usate per accedere alle variabili in un runbook di Python 2.

|Funzioni Python 2|Descrizione|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera il valore di una variabile esistente. |
|`automationassets.set_automation_variable`|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario `automationassets` importare il modulo nella parte superiore del runbook Python per accedere alle funzioni delle risorse.

## <a name="working-with-automation-variables"></a>Utilizzo delle variabili di automazioneWorking with Automation variables

>[!NOTE]
>Se si desidera rimuovere la crittografia per una variabile, è necessario eliminare la variabile e ricrearla come non crittografata.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Creare una nuova variabile usando il portale di AzureCreate a new variable using the Azure portal

1. Nell'account Di automazione fare clic sul riquadro **Asset,** quindi sul pannello **Asset** e selezionare **Variabili**.
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **Nuova variabile** e quindi fare clic su **Crea** per salvare la nuova variabile.

> [!NOTE]
> Dopo aver salvato una variabile crittografata, questa non può essere visualizzata nel portale. Può solo essere aggiornato.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Creare e usare una variabile in Windows PowerShellCreate and use a variable in Windows PowerShell

Uno script di `New-AzAutomationVariable` PowerShell usa il cmdlet o l'equivalente del modulo di AzureRM per creare una nuova variabile e impostarne il valore iniziale. Se la variabile è crittografata, `Encrypted` la chiamata deve utilizzare il parametro .

Lo script recupera il valore della variabile utilizzando [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Se il valore è un tipo semplice, il cmdlet recupera lo stesso tipo. Se si tratta di un `PSCustomObject` tipo complesso, viene recuperato un tipo.

>[!NOTE]
>Uno script di PowerShell non può recuperare un valore crittografato. L'unico modo per eseguire `Get-AutomationVariable` questa operazione consiste nell'utilizzare un'attività in un runbook o in una configurazione DSC.

Nell'esempio seguente viene illustrato come creare una variabile di tipo String e quindi restituirne il valore.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Nell'esempio seguente viene illustrato come creare una variabile con un tipo complesso e quindi recuperarne le proprietà. In questo caso, viene utilizzato un oggetto macchina virtuale da [Get-AzVM.In](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) this case, a virtual machine object from Get-AzVM is used.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Creare e usare una variabile in una configurazione runbook o DSCCreate and use a variable in a runbook or DSC configuration

L'unico modo per creare una nuova variabile dall'interno di `New-AzAutomationVariable` un runbook o di una configurazione DSC consiste nell'usare il cmdlet o il relativo equivalente del modulo AzureRM. Lo script utilizza questo cmdlet per impostare il valore iniziale della variabile. Lo script può quindi `Get-AzAutomationVariable`recuperare il valore utilizzando . Se il valore è un tipo semplice, viene recuperato lo stesso tipo. Se si tratta di un `PSCustomObject` tipo complesso, viene recuperato un tipo.

>[!NOTE]
>L'unico modo per recuperare un valore `Get-AutomationVariable` crittografato consiste nell'utilizzare l'attività nel runbook o nella configurazione DSC. 

### <a name="textual-runbook-samples"></a>Esempi di runbook testuali

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Impostare e recuperare un valore semplice da una variabileSet and retrieve a simple value from a variable

I comandi di esempio seguenti mostrano come impostare e recuperare una variabile in un runbook testuale. In questo esempio si presuppone `NumberOfIterations` la `NumberOfRunnings` creazione di `SampleMessage`variabili integer denominate e e di una variabile stringa denominata .

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Impostare e recuperare una variabile in un runbook di Python 2Set and retrieve a variable in a Python 2 runbook

L'esempio seguente mostra come usare una variabile, impostare una variabile e gestire un'eccezione per una variabile inesistente in un runbook di Python 2.The following sample shows how to use a variable, set a variable, and handle an exception for a inexistent variable in a Python 2 runbook.

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

In un runbook grafico, `Get-AutomationVariable` è `Set-AutomationVariable` possibile aggiungere l'attività o . È sufficiente fare clic con il pulsante destro del mouse sulla variabile nel riquadro Libreria dell'editor grafico e selezionare l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Impostare i valori in una variabileSet values in a variable

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo `Get-AzVM` esempio recupera una singola macchina virtuale di Azure e salva il nome del computer in una variabile di stringa di automazione esistente. Non importa se il collegamento è una pipeline o una [sequenza](../automation-graphical-authoring-intro.md#links-and-workflow) poiché il codice prevede solo un singolo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla connessione di attività nella creazione grafica, vedere [Collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow).
- Per iniziare a utilizzare i runbook grafici, vedere [Il mio primo runbook grafico](../automation-first-runbook-graphical.md).
