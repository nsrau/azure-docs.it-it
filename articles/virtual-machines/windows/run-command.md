---
title: Eseguire gli script di PowerShell in una macchina virtuale Windows in Azure
description: In questo argomento viene descritto come eseguire gli script di PowerShell all'interno di una macchina virtuale Windows di Azure usando la funzionalità Esegui comando
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: dd1e20504d96b55d6a450512ea287b9352fb043a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496934"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Eseguire gli script di PowerShell nella macchina virtuale Windows usando Esegui comando

La funzionalità Esegui comando usa l'agente macchina virtuale per eseguire gli script PowerShell in una macchina virtuale Windows di Azure. Questi script possono essere usati per la gestione generale del computer o delle applicazioni. Consentono di diagnosticare e risolvere rapidamente i problemi di accesso e di rete della macchina virtuale e di riportarla a uno stato valido.



## <a name="benefits"></a>Vantaggi

È possibile accedere alle macchine virtuali in diversi modi. La funzionalità Esegui comando può eseguire script nelle macchine virtuali in modalità remota, tramite l'agente per la macchina virtuale. La funzionalità Esegui comando può essere usata tramite il portale di Azure, l'[API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) o [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) per le macchine virtuali Windows.

Questa funzionalità è utile in tutti gli scenari in cui si vuole eseguire uno script all'interno di una macchina virtuale. È uno dei pochi modi per risolvere e correggere i problemi in una macchina virtuale che non ha la porta RDP o SSH aperta a causa di una configurazione non corretta della rete o dell'utente amministratore.

## <a name="restrictions"></a>Restrizioni

Quando si usa Esegui comando, sono valide le limitazioni seguenti:

* L'output è limitato agli ultimi 4096 byte.
* Il tempo minimo per eseguire uno script è di circa 20 secondi.
* Gli script vengono eseguiti come script di sistema in Windows.
* È possibile eseguire uno script alla volta.
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare l'esecuzione di uno script.
* Il tempo massimo di esecuzione di uno script è di 90 minuti. Dopo questo tempo viene raggiunto il timeout.
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, Esegui comando richiede la connettività agli indirizzi IP pubblici di Azure (porta 443). Se l'estensione non ha accesso a questi endpoint, gli script potrebbero funzionare correttamente, ma non restituiranno risultati. Se si sta bloccando il traffico nella macchina virtuale, è possibile usare i [tag di servizio](../../virtual-network/security-overview.md#service-tags) per consentire il traffico verso gli indirizzi IP pubblici di Azure tramite il tag `AzureCloud`.

## <a name="available-commands"></a>Comandi disponibili

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Windows. Si può usare il comando **RunPowerShellScript** per eseguire qualsiasi script personalizzato desiderato. Quando si usa l'interfaccia della riga di comando di Azure o PowerShell per eseguire un comando, il valore specificato per il parametro `--command-id` o `-CommandId` deve essere uno dei valori elencati di seguito. Quando si specifica un valore che non è un comando disponibile, viene visualizzato questo errore:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrizione**|
|---|---|
|**RunPowerShellScript**|Esegue uno script di PowerShell.|
|**EnableRemotePS**|Configura il computer per abilitare PowerShell remoto.|
|**EnableAdminAccount**|Controlla se l'account Administrator locale è disabilitato, e in tal caso lo abilita.|
|**IPConfig**| Visualizza informazioni dettagliate relative a indirizzo IP, subnet mask e gateway predefinito per ogni scheda associata a TCP/IP.|
|**RDPSettings**|Controlla le impostazioni del Registro e le impostazioni dei criteri di dominio. Suggerisce le azioni dei criteri se il computer fa parte di un dominio o converte le impostazioni nei valori predefiniti.|
|**ResetRDPCert**|Rimuove il certificato TLS/SSL associato al listener del protocollo RDP e ripristina la sicurezza del listener RDP sull'impostazione predefinita. Usare questo script se vengono visualizzati eventuali problemi con il certificato.|
|**SetRDPPort**|Imposta il numero di porta predefinito o specificato dall'utente per le connessioni Desktop remoto. Abilita regole del firewall per l'accesso in entrata alla porta.|

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'esempio seguente usa il comando [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per eseguire uno script della shell su una macchina virtuale Windows di Azure.

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

Passare a una macchina virtuale nel [portale di Azure](https://portal.azure.com) e selezionare **Esegui comando** in **OPERAZIONI**. Viene visualizzato un elenco dei comandi disponibili per l'esecuzione nella macchina virtuale.

![Elenco di comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi potrebbero avere parametri di input obbligatori o facoltativi. Per questi comandi, i parametri vengono presentati come campi di testo in cui immettere i valori di input. Per ogni comando è possibile visualizzare lo script che viene eseguito espandendo **Visualizza script**. **RunPowerShellScript** è diverso dagli altri comandi, in quanto consente di specificare script personalizzati.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Dopo aver scelto il comando, selezionare **Esegui** per eseguire lo script. Lo script viene eseguito e, al termine, restituisce l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **RDPSettings**.

![Eseguire l'output dello script di comando](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

L'esempio seguente usa il cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) per eseguire uno script di PowerShell in una macchina virtuale di Azure. Il cmdlet si aspetta che lo script a cui fa riferimento il parametro `-ScriptPath` sia presente nella posizione in cui il cmdlet viene eseguito.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

Per elencare i comandi di esecuzione o visualizzare i dettagli di un comando è necessaria l'autorizzazione `Microsoft.Compute/locations/runCommands/read`. Il ruolo predefinito [Lettore](../../role-based-access-control/built-in-roles.md#reader) e i livelli superiori hanno questa autorizzazione.

L'esecuzione di un comando richiede l'autorizzazione `Microsoft.Compute/virtualMachines/runCommand/action`. Il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e i livelli superiori hanno questa autorizzazione.

È possibile usare uno dei [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) o creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) per usare Esegui comando.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri modi per eseguire comandi e script in remoto nella macchina virtuale, vedere [Eseguire gli script nella macchina virtuale Windows](run-scripts-in-vm.md).
