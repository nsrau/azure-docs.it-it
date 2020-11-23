---
title: Installare l'agente di monitoraggio di Azure
description: Opzioni per l'installazione dell'agente di monitoraggio di Azure (AMA) in macchine virtuali di Azure e server abilitati per Azure Arc.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 4c6252b31b4be05ea3c0bcf160a28bf335239b23
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324854"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Installare l'agente di monitoraggio di Azure (anteprima)
Questo articolo fornisce le diverse opzioni attualmente disponibili per l'installazione dell' [agente di monitoraggio di Azure](azure-monitor-agent-overview.md) sia nelle macchine virtuali di Azure che nei server abilitati per Azure Arc, oltre alle opzioni per creare [associazioni con regole di raccolta dati](data-collection-rule-azure-monitor-agent.md) che definiscono i dati che l'agente deve raccogliere.

## <a name="prerequisites"></a>Prerequisiti
I prerequisiti seguenti sono necessari prima di installare l'agente di monitoraggio di Azure.

- L' [identità del sistema gestito](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) deve essere abilitata nelle macchine virtuali di Azure. Questa operazione non è necessaria per i server abilitati per Azure Arc. L'identità del sistema verrà abilitata automaticamente se l'agente viene installato come parte del processo di [creazione e assegnazione di una regola di raccolta dati utilizzando il portale di Azure](#install-with-azure-portal).
- Il [tag del servizio AzureResourceManager](../../virtual-network/service-tags-overview.md) deve essere abilitato nella rete virtuale per la macchina virtuale.

## <a name="virtual-machine-extension-details"></a>Dettagli estensione macchina virtuale
L'agente di monitoraggio di Azure viene implementato come [estensione della macchina virtuale di Azure](../../virtual-machines/extensions/overview.md) con i dettagli nella tabella seguente. Può essere installato usando uno dei metodi per installare le estensioni delle macchine virtuali, incluse quelle descritte in questo articolo.

| Proprietà | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Eseguire l'installazione con il portale di Azure
Per installare l'agente di monitoraggio di Azure usando il portale di Azure, seguire la procedura per [creare una regola di raccolta dati](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) nel portale di Azure. In questo modo è possibile associare la regola di raccolta dati a una o più macchine virtuali di Azure o a server abilitati per Azure Arc. L'agente verrà installato in tutte le macchine virtuali in cui non è già presente.


## <a name="install-with-resource-manager-template"></a>Installare con Gestione risorse modello
È possibile usare i modelli di Gestione risorse per installare l'agente di monitoraggio di Azure in macchine virtuali di Azure e nei server abilitati per Azure Arc e per creare un'associazione con le regole di raccolta dei dati. Prima di creare l'associazione, è necessario creare una regola di raccolta dati.

Ottenere i modelli di esempio per l'installazione dell'agente e la creazione dell'associazione dagli elementi seguenti: 

- [Modello per l'installazione dell'agente di monitoraggio di Azure (Azure e Azure Arc)](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Modello per creare un'associazione con la regola di raccolta dati](../samples/resource-manager-data-collection-rules.md)

Installare i modelli usando [un metodo di distribuzione per gestione risorse modelli](../../azure-resource-manager/templates/deploy-powershell.md) , ad esempio i comandi seguenti.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Eseguire l'installazione con PowerShell
È possibile installare l'agente di monitoraggio di Azure in macchine virtuali di Azure e nei server abilitati per Azure ARC usando il comando di PowerShell per l'aggiunta di un'estensione della macchina virtuale. 

### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure
Usare i comandi di PowerShell riportati di seguito per installare l'agente di monitoraggio di Azure in macchine virtuali di Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Server con abilitazione di Azure Arc
Usare i comandi di PowerShell seguenti per installare i server abilitati per l'agente di monitoraggio di Azure in Azure Arc.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile installare l'agente di monitoraggio di Azure in macchine virtuali di Azure e nei server abilitati per Azure ARC usando il comando dell'interfaccia della riga di comando di Azure per aggiungere un'estensione della macchina virtuale. 

### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure
Usare i seguenti comandi dell'interfaccia della riga di comando per installare l'agente di monitoraggio di Azure in macchine virtuali di Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Server con abilitazione di Azure Arc
Usare i comandi dell'interfaccia della riga di comando seguenti per installare i server abilitati per l'agente di monitoraggio di Azure in Azure.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Passaggi successivi

- [Creare una regola di raccolta dati](data-collection-rule-azure-monitor-agent.md) per raccogliere dati dall'agente e inviarli a monitoraggio di Azure.
