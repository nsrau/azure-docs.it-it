---
title: Eseguire script della shell in una macchina virtuale Linux in AzureRun shell scripts in a Linux VM on Azure
description: Questo argomento descrive come eseguire script all'interno di una macchina virtuale Linux di Azure usando la funzionalità Esegui comando
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758614"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Run shell scripts in your Linux VM by using Run Command

The Run Command feature uses the virtual machine (VM) agent to run shell scripts within an Azure Linux VM. È possibile utilizzare questi script per la gestione generale del computer o delle applicazioni. Possono aiutare a diagnosticare e correggere rapidamente l'accesso alle macchine virtuali e i problemi di rete e riportare la macchina virtuale a uno stato valido.

## <a name="benefits"></a>Vantaggi

È possibile accedere alle macchine virtuali in diversi modi. Esegui comando può eseguire script nelle macchine virtuali in remoto usando l'agente di macchine virtuali. È possibile usare Esegui comando tramite il portale di Azure, l'API REST o l'interfaccia della riga di comando di Azure per le macchine virtuali Linux.You use Run Command through the Azure portal, [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)or [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) for Linux VMs.

Questa funzionalità è utile in tutti gli scenari in cui si vuole eseguire uno script all'interno di una macchina virtuale. È uno degli unici modi per risolvere i problemi e correggere una macchina virtuale che non ha la porta RDP o SSH aperta a causa di una configurazione di rete o utente amministrativa non corretta.

## <a name="restrictions"></a>Restrizioni

Quando si utilizza Esegui comando, si applicano le restrizioni seguenti:

* L'output è limitato agli ultimi 4.096 byte.
* Il tempo minimo per eseguire uno script è di circa 20 secondi.
* Gli script vengono eseguiti per impostazione predefinita come utente con privilegi elevati in Linux.Scripts run by default as an elevated user on Linux.
* È possibile eseguire uno script alla volta.
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare uno script in esecuzione.
* Il tempo massimo di esecuzione di uno script è di 90 minuti. Dopo di che, lo script svanirà.
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, Esegui comando richiede connettività (porta 443) agli indirizzi IP pubblici di Azure.To function correctly, Run Command requires connectivity (port 443) to Azure public IP addresses. Se l'estensione non ha accesso a questi endpoint, gli script potrebbero essere eseguiti correttamente ma non restituire i risultati. Se si blocca il traffico nella macchina virtuale, è possibile usare [i tag](../../virtual-network/security-overview.md#service-tags) `AzureCloud` di servizio per consentire il traffico verso gli indirizzi IP pubblici di Azure usando il tag.

## <a name="available-commands"></a>Comandi disponibili

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Linux. È possibile utilizzare il comando **RunShellScript** per eseguire qualsiasi script personalizzato desiderato. Quando si usa l'interfaccia della riga di comando di Azure o `--command-id` `-CommandId` PowerShell per eseguire un comando, il valore specificato per il parametro o deve essere uno dei valori elencati seguenti. Quando si specifica un valore che non è un comando disponibile, viene visualizzato questo errore:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrizione**|
|---|---|
|**RunShellScript**|Esegue uno script della shell Linux.|
|**ifconfig**| Ottiene la configurazione di tutte le interfacce di rete.|

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'esempio seguente usa il comando az vm run-command per eseguire uno script della shell in una macchina virtuale Linux di Azure.The following example uses the [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) command command to run a shell script on an Azure Linux VM.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Per eseguire comandi come utente `sudo -u` diverso, immettere per specificare un account utente.

## <a name="azure-portal"></a>Portale di Azure

Passare a una macchina virtuale nel [portale di Azure](https://portal.azure.com) e selezionare Esegui **comando** in **OPERATIONS**. Viene visualizzato un elenco dei comandi disponibili da eseguire nella macchina virtuale.

![Elenco dei comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi potrebbero avere parametri di input facoltativi o obbligatori. Per questi comandi, i parametri vengono presentati come campi di testo per fornire i valori di input. Per ogni comando, è possibile visualizzare lo script in esecuzione espandendo **Visualizza script**. **RunShellScript** è diverso dagli altri comandi, perché consente di fornire uno script personalizzato.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Dopo aver scelto il comando, selezionare **Esegui** per eseguire lo script. Al termine dello script, restituisce l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **ifconfig**.

![Eseguire l'output dello script di comando ](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

L'esempio seguente usa il cmdlet Invoke-AzVMRunCommand per eseguire uno script di PowerShell in una macchina virtuale di Azure.The following example uses the [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet to run a PowerShell script on an Azure VM. Il cmdlet si aspetta che lo script a cui fa riferimento il parametro `-ScriptPath` sia presente nella posizione in cui il cmdlet viene eseguito.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

L'elenco dei comandi di esecuzione o `Microsoft.Compute/locations/runCommands/read` la visualizzazione dei dettagli di un comando richiede l'autorizzazione a livello di sottoscrizione. Il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) incorporato e i livelli superiori dispongono di questa autorizzazione.

L'esecuzione di `Microsoft.Compute/virtualMachines/runCommand/action` un comando richiede l'autorizzazione a livello di sottoscrizione. Il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e i livelli superiori dispongono di questa autorizzazione.

È possibile utilizzare uno dei [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) o creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) per utilizzare Esegui comando.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su altri modi per eseguire script e comandi in modalità remota nella macchina virtuale, vedere [Eseguire script nella macchina virtuale Linux.](run-scripts-in-vm.md)
