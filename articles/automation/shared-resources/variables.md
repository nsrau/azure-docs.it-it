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
ms.openlocfilehash: e56a1c9a158974266b810d31a0e9bb898262761a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849429"
---
# <a name="variable-assets-in-azure-automation"></a>Asset di tipo variabile in Automazione di Azure

Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC nell'account di automazione. Possono essere gestiti dalla portale di Azure, da PowerShell, all'interno di una configurazione Runbook o DSC. Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più runbook o configurazioni DSC.

- Condivisione di un valore tra più processi dello stesso runbook o configurazione DSC.

- Gestire un valore dal portale o dalla riga di comando di PowerShell usata da manuali operativi o dalle configurazioni DSC, ad esempio un set di elementi di configurazione comuni come un elenco specifico di nomi di VM, un gruppo di risorse specifico, un nome di dominio AD e altro ancora.  

Poiché le variabili di automazione sono persistenti, sono disponibili anche se la configurazione di Runbook o DSC ha esito negativo. Questo comportamento consente l'impostazione di un valore da parte di un Runbook che viene quindi usato da un altro o viene usato dalla stessa configurazione di Runbook o DSC la volta successiva che viene eseguita.

Quando si crea una variabile, è possibile specificare che venga archiviata in modalità crittografata. Le variabili crittografate vengono archiviate in modo sicuro in automazione di Azure e il relativo valore non può essere recuperato dal cmdlet [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) incluso nel modulo Azure PowerShell. L'unico modo in cui è possibile recuperare un valore crittografato è dall'attività **Get-AutomationVariable** in un runbook o configurazione DSC. Se si desidera modificare una variabile crittografata in non crittografato, è possibile eliminare e ricreare la variabile come non crittografata.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave è archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito da Automazione di Azure.

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario selezionare un tipo di dati nell'elenco a discesa, in modo che nel portale possa essere visualizzato il controllo appropriato per immettere il valore della variabile. La variabile non è limitata a questo tipo di dati. È necessario impostare la variabile utilizzando Windows PowerShell se si desidera specificare un valore di tipo diverso. Se si specifica **non definito**, il valore della variabile imposta su **$null**ed è necessario impostare il valore con il cmdlet [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) o l'attività **set-AutomationVariable** . Non è possibile creare o modificare il valore per un tipo di variabile complesso nel portale, ma è possibile specificare un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi verranno restituiti come [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

Di seguito è riportato un elenco dei tipi di variabile disponibili in Automazione:

* Stringa
* Integer
* Data e ora
* boolean
* Null

## <a name="azurerm-powershell-cmdlets"></a>Cmdlet di PowerShell AzureRM

Per AzureRM, per creare e gestire asset di credenziali di automazione con Windows PowerShell, vengono usati i cmdlet della tabella seguente. Vengono forniti come parte del [modulo AzureRM. Automation](/powershell/azure/overview), disponibile per l'uso nei manuali operativi di automazione e nelle configurazioni DSC.

| Cmdlets | Description |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Recupera il valore di una variabile esistente.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Rimuove una variabile esistente.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Imposta il valore di una variabile esistente.|

## <a name="activities"></a>Attività

Le attività nella tabella seguente vengono usate per accedere alle variabili in un Runbook e le configurazioni DSC. La differenza tra i cmdlet Get-AzureRmAutomationVariable e Get-AutomationVariable è stata chiarita sopra all'inizio di questo documento.

| Attività | Description |
|:---|:---|
|Get-AutomationVariable|Recupera il valore di una variabile esistente.|
|Set-AutomationVariable|Imposta il valore di una variabile esistente.|

> [!NOTE]
> È consigliabile evitare di usare le variabili nel parametro –Name di **Get-AutomationVariable** in un runbook o una configurazione DSC perché ciò può complicare l'individuazione delle dipendenze tra i runbook o la configurazione DSC e le variabili di automazione in fase di progettazione.

Le funzioni nella tabella seguente vengono usate per accedere e recuperare le variabili in un runbook di Python2.

|Funzioni Python2|Description|
|:---|:---|
|automationassets.get_automation_variable|Recupera il valore di una variabile esistente. |
|automationassets.set_automation_variable|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario importare il modulo "automationassets" nella parte superiore del runbook Python per poter accedere alle funzioni dell'asset.

## <a name="creating-a-new-automation-variable"></a>Creazione di una nuova variabile di automazione

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Per creare una nuova variabile con il portale di Azure

1. Dall'account di automazione fare clic sul riquadro **Asset** e poi sul pannello **Asset** selezionare **Variabili**.
2. Nel riquadro **Variabili** selezionare **Aggiungi variabile**.
3. Completare le opzioni nel pannello **Nuova variabile**, fare clic su **Crea** e salvare la nuova variabile.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Per creare una nuova variabile con Windows PowerShell

Il cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) crea una nuova variabile e ne imposta il valore iniziale. È possibile recuperare il valore usando [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Se il valore è di tipo semplice, verrà restituito lo stesso tipo. Se è di tipo complesso, verrà restituito un **PSCustomObject** .

I comandi di esempio seguenti mostrano come creare una variabile di tipo stringa e restituirne il valore.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

I comandi di esempio seguenti mostrano come creare una variabile di tipo complesso e restituirne le relative proprietà. In questo caso, viene usato un oggetto macchina virtuale recuperato da **Get-AzureRmVm**.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Uso di una variabile in un Runbook o in una configurazione DSC

Usare l'attività **Set-AutomationVariable** per impostare il valore di una variabile di automazione in un runbook di PowerShell o una configurazione DSC e **Get-AutomationVariable** per recuperarlo. Non è consigliabile usare il cmdlet **Set-AzureRMAutomationVariable** o **Get-AzureRMAutomationVariable** in un runbook o una configurazione DSC perché sono meno efficienti rispetto alle attività del flusso di lavoro. Non è inoltre possibile recuperare il valore delle variabili sicure con **Get-AzureRMAutomationVariable**. L'unico modo per creare una nuova variabile dall'interno di un runbook o una configurazione DSC consiste nell'usare il cmdlet [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable).

### <a name="textual-runbook-samples"></a>Esempi di runbook testuali

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Impostazione e recupero di un valore semplice da una variabile

I comandi di esempio seguenti mostrano come impostare e recuperare una variabile in un runbook testuale. In questo esempio si presuppone che siano state create variabili di tipo integer denominate *NumberOfIterations* e *NumberOfRunnings* e una variabile di tipo String denominata *SampleMessage* .

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Impostazione e recupero di una variabile in Python2

L'esempio di codice seguente illustra come usare una variabile, impostare una variabile e gestire un'eccezione per una variabile non esistente in un runbook di Python2.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Esempi di runbook grafici

In un runbook grafico è possibile aggiungere **Get-AutomationVariable** o **Set-AutomationVariable** facendo clic con il pulsante destro del mouse sulla variabile nel riquadro della libreria dell'editor grafico e scegliendo l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Impostazione dei valori in una variabile

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio **Get-AzureRmVM** recupera una singola macchina virtuale di Azure e il nome del computer viene salvato in una variabile di automazione esistente con un tipo di stringa. Non è importante se il [collegamento è una pipeline o una sequenza](../automation-graphical-authoring-intro.md#links-and-workflow) poiché è prevista la presenza di un solo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Fasi successive

- Per altre informazioni su come collegare attività nella creazione grafica, vedere [Creazione grafica in Automazione di Azure](../automation-graphical-authoring-intro.md#links-and-workflow)
- Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](../automation-first-runbook-graphical.md)
