---
title: Eseguire script di PowerShell in una macchina virtuale Windows in AzureRun PowerShell scripts in a Windows VM in Azure
description: In questo argomento viene descritto come eseguire script di PowerShell all'interno di una macchina virtuale Windows di Azure usando la funzionalità Esegui comando
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749235"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Run PowerShell scripts in your Windows VM by using Run Command

The Run Command feature uses the virtual machine (VM) agent to run PowerShell scripts within an Azure Windows VM. È possibile utilizzare questi script per la gestione generale del computer o delle applicazioni. Possono aiutare a diagnosticare e correggere rapidamente l'accesso alle macchine virtuali e i problemi di rete e riportare la macchina virtuale a uno stato valido.

 

## <a name="benefits"></a>Vantaggi

È possibile accedere alle macchine virtuali in diversi modi. Esegui comando può eseguire script nelle macchine virtuali in remoto usando l'agente di macchine virtuali. È possibile usare Esegui comando tramite il portale di Azure, l'API REST o PowerShell per le macchine virtuali Windows.You use Run Command through the Azure portal, [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)or [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) for Windows VMs.

Questa funzionalità è utile in tutti gli scenari in cui si vuole eseguire uno script all'interno di una macchina virtuale. È uno degli unici modi per risolvere i problemi e correggere una macchina virtuale che non ha la porta RDP o SSH aperta a causa di una configurazione di rete o utente amministrativa non corretta.

## <a name="restrictions"></a>Restrizioni

Quando si utilizza Esegui comando, si applicano le restrizioni seguenti:

* L'output è limitato agli ultimi 4.096 byte.
* Il tempo minimo per eseguire uno script è di circa 20 secondi.
* Gli script vengono eseguiti come sistema in Windows.
* È possibile eseguire uno script alla volta.
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare uno script in esecuzione.
* Il tempo massimo di esecuzione di uno script è di 90 minuti. Dopo di che, si scionirà.
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, Esegui comando richiede connettività (porta 443) agli indirizzi IP pubblici di Azure.To function correctly, Run Command requires connectivity (port 443) to Azure public IP addresses. Se l'estensione non ha accesso a questi endpoint, gli script potrebbero essere eseguiti correttamente ma non restituire i risultati. Se si blocca il traffico nella macchina virtuale, è possibile usare [i tag](../../virtual-network/security-overview.md#service-tags) `AzureCloud` di servizio per consentire il traffico verso gli indirizzi IP pubblici di Azure usando il tag.

## <a name="available-commands"></a>Comandi disponibili

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Windows. È possibile utilizzare il comando **RunPowerShellScript** per eseguire qualsiasi script personalizzato desiderato. Quando si usa l'interfaccia della riga di comando di Azure o `--command-id` `-CommandId` PowerShell per eseguire un comando, il valore specificato per il parametro o deve essere uno dei valori elencati seguenti. Quando si specifica un valore che non è un comando disponibile, viene visualizzato questo errore:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrizione**|
|---|---|
|**RunPowerShellScript**|Esegue uno script di PowerShell.Runs a PowerShell script.|
|**EnableRemotePS**|Configura il computer per abilitare PowerShell remoto.|
|**EnableAdminAccount**|Controlla se l'account amministratore locale è disabilitato e, in tal caso, lo abilita.|
|**IPConfig**| Mostra informazioni dettagliate per l'indirizzo IP, la subnet mask e il gateway predefinito per ogni scheda associata a TCP/IP.|
|**RDPSettings**|Controlla le impostazioni del Registro e le impostazioni dei criteri di dominio. Suggerisce le azioni dei criteri se il computer fa parte di un dominio o modifica le impostazioni sui valori predefiniti.|
|**ResetRDPCert**|Rimuove il certificato SSL collegato al listener RDP e ripristina la sicurezza predefinita del listener RDP. Usare questo script se vengono visualizzati eventuali problemi con il certificato.|
|**SetRDPPort**|Imposta il numero di porta predefinito o specificato dall'utente per le connessioni Desktop remoto. Abilita le regole del firewall per l'accesso in ingresso alla porta.|

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'esempio seguente usa il comando az vm run-command per eseguire uno script della shell in una macchina virtuale Windows di Azure.The following example uses the [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) command command to run a shell script on an Azure Windows VM.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Portale di Azure

Passare a una macchina virtuale nel [portale di Azure](https://portal.azure.com) e selezionare Esegui **comando** in **OPERATIONS**. Viene visualizzato un elenco dei comandi disponibili da eseguire nella macchina virtuale.

![Elenco dei comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi potrebbero avere parametri di input facoltativi o obbligatori. Per questi comandi, i parametri vengono presentati come campi di testo per fornire i valori di input. Per ogni comando, è possibile visualizzare lo script in esecuzione espandendo **Visualizza script**. **RunPowerShellScript** è diverso dagli altri comandi, perché consente di fornire uno script personalizzato.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Dopo aver scelto il comando, selezionare **Esegui** per eseguire lo script. Al termine dello script, restituisce l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **RDPSettings**.

![Eseguire l'output dello script di comando ](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

L'esempio seguente usa il cmdlet Invoke-AzVMRunCommand per eseguire uno script di PowerShell in una macchina virtuale di Azure.The following example uses the [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet to run a PowerShell script on an Azure VM. Il cmdlet si aspetta che lo script a cui fa riferimento il parametro `-ScriptPath` sia presente nella posizione in cui il cmdlet viene eseguito.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

L'elenco dei comandi di esecuzione o `Microsoft.Compute/locations/runCommands/read` la visualizzazione dei dettagli di un comando richiede l'autorizzazione a livello di sottoscrizione. Il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) incorporato e i livelli superiori dispongono di questa autorizzazione.

L'esecuzione di `Microsoft.Compute/virtualMachines/runCommand/action` un comando richiede l'autorizzazione a livello di sottoscrizione. Il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e i livelli superiori dispongono di questa autorizzazione.

È possibile utilizzare uno dei [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) o creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) per utilizzare Esegui comando.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri modi per eseguire script e comandi in modalità remota nella macchina virtuale, vedere [Eseguire script nella macchina virtuale Windows.](run-scripts-in-vm.md)
