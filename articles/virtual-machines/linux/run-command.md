---
title: Eseguire script della shell in una macchina virtuale Linux in Azure
description: In questo argomento viene descritto come eseguire script all'interno di una macchina virtuale Linux di Azure usando la funzionalità Esegui comando
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: a14fafde8ecea0370c74cdbfd39a85d8dfb15612
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651077"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Eseguire script della shell nella macchina virtuale Linux usando Esegui comando

La funzionalità Esegui comando usa l'agente di macchine virtuali per eseguire gli script della shell all'interno di una macchina virtuale Linux di Azure. Questi script possono essere usati per la gestione generale del computer o delle applicazioni. Consentono di diagnosticare e risolvere rapidamente i problemi di accesso e di rete della macchina virtuale e di riportarla a uno stato valido.

## <a name="benefits"></a>Vantaggi

È possibile accedere alle macchine virtuali in diversi modi. Esegui comando può eseguire gli script nelle macchine virtuali in modalità remota tramite l'agente di macchine virtuali. Può essere usato tramite il portale di Azure, l'[API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) o l'[interfaccia della riga di comando di Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per le macchine virtuali Linux.

Questa funzionalità è utile in tutti gli scenari in cui si vuole eseguire uno script all'interno di una macchina virtuale. È uno dei pochissimi modi per risolvere e correggere i problemi in una macchina virtuale che non ha la porta RDP o SSH aperta a causa di una configurazione non corretta della rete o dell'utente amministratore.

## <a name="restrictions"></a>Restrizioni

Quando si usa Esegui comando, si applicano le limitazioni seguenti:

* L'output è limitato agli ultimi 4.096 byte.
* Il tempo minimo per eseguire uno script è di circa 20 secondi.
* Gli script vengono eseguiti per impostazione predefinita come utente con privilegi elevati in Linux.
* È possibile eseguire uno script alla volta.
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare l'esecuzione di uno script.
* Il tempo massimo di esecuzione di uno script è di 90 minuti. Successivamente, si verifica un timeout.
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, Esegui comando richiede la connettività agli indirizzi IP pubblici di Azure (porta 443). Se l'estensione non ha accesso a questi endpoint, gli script potrebbero funzionare correttamente, ma non restituirebbero risultati. Se si sta bloccando il traffico nella macchina virtuale, è possibile usare i [tag di servizio](../../virtual-network/security-overview.md#service-tags) per consentire il traffico verso gli indirizzi IP pubblici di Azure tramite il tag `AzureCloud`.

## <a name="available-commands"></a>Comandi disponibili

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Linux. Si può usare il comando **RunShellScript** per eseguire qualsiasi script personalizzato desiderato. Quando si usa l'interfaccia della riga di comando di Azure o PowerShell per eseguire un comando, il valore specificato per il parametro `--command-id` o `-CommandId` deve essere uno dei valori elencati di seguito. Quando si specifica un valore che non è un comando disponibile, viene visualizzato questo errore:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrizione**|
|---|---|
|**RunShellScript**|Esegue uno script della shell di Linux.|
|**ifconfig**| Visualizza la configurazione di tutte le interfacce di rete.|

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'esempio seguente usa il comando [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per eseguire uno script della shell su una macchina virtuale Linux di Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Per eseguire i comandi come un utente diverso, immettere `sudo -u`per specificare un account utente.

## <a name="azure-portal"></a>Portale di Azure

Passare a una macchina virtuale nel [portale di Azure](https://portal.azure.com) e selezionare **Esegui comando** in **OPERAZIONI**. Viene visualizzato un elenco di comandi disponibili per l'esecuzione nella macchina virtuale.

![Elenco di comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi potrebbero avere parametri di input obbligatori o facoltativi. Per questi comandi, i parametri vengono presentati come campi di testo in cui fornire i valori di input. Per ogni comando è possibile vedere lo script che viene eseguito espandendo **Visualizza script**. **RunShellScript** è diverso da altri comandi, in quanto consente di fornire script personalizzati.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Dopo aver scelto il comando, selezionare **Esegui**. Lo script viene eseguito e, al termine, restituisce l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **ifconfig**.

![Eseguire l'output dello script di comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

L'esempio seguente usa il cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) per eseguire uno script di PowerShell in una macchina virtuale di Azure. Il cmdlet si aspetta che lo script a cui fa riferimento il parametro `-ScriptPath` sia presente nella posizione in cui il cmdlet viene eseguito.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

Per elencare i comandi di esecuzione o visualizzare i dettagli di un comando è necessaria l'autorizzazione `Microsoft.Compute/locations/runCommands/read`. Il ruolo predefinito [Lettore](../../role-based-access-control/built-in-roles.md#reader) e i livelli superiori hanno questa autorizzazione.

L'esecuzione di un comando richiede l'autorizzazione `Microsoft.Compute/virtualMachines/runCommand/action`. Il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e i livelli superiori hanno questa autorizzazione.

È possibile usare uno dei [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) o creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) per usare Esegui comando.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Eseguire gli script nella macchina virtuale Linux](run-scripts-in-vm.md) per informazioni su altri modi per eseguire comandi e script in remoto nella macchina virtuale.
