---
title: Eseguire script della shell in una macchina virtuale Linux in Azure
description: Questo argomento descrive come eseguire script all'interno di una macchina virtuale Linux di Azure usando la funzionalità Esegui comando
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 21787854590d3ca0be2cbd6e9d167de33482c787
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597889"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Eseguire script della shell nella VM Linux usando il comando Run

La funzionalità Esegui comando usa l'agente della macchina virtuale (VM) per eseguire gli script della shell in una VM Linux di Azure. È possibile usare questi script per la gestione generale del computer o delle applicazioni. Consentono di diagnosticare e risolvere rapidamente i problemi di accesso e di rete delle VM e di riportare la macchina virtuale a uno stato valido.

## <a name="benefits"></a>Vantaggi

È possibile accedere alle macchine virtuali in diversi modi. Esegui comando può eseguire script sulle macchine virtuali in modalità remota tramite l'agente di macchine virtuali. Usare il comando Run tramite il portale di Azure, l' [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)o l'interfaccia della riga di comando di [Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per le macchine virtuali Linux.

Questa funzionalità è utile in tutti gli scenari in cui si vuole eseguire uno script all'interno di una macchina virtuale. È uno degli unici modi per risolvere i problemi e correggere una macchina virtuale che non dispone della porta RDP o SSH aperta a causa di una configurazione non corretta della rete o dell'utente amministratore.

## <a name="restrictions"></a>Restrizioni

Quando si usa il comando Run, si applicano le restrizioni seguenti:

* L'output è limitato agli ultimi 4.096 byte.
* Il tempo minimo per eseguire uno script è circa 20 secondi.
* Gli script vengono eseguiti per impostazione predefinita come utente con privilegi elevati in Linux.
* È possibile eseguire uno script alla volta.
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare uno script in esecuzione.
* Il tempo massimo di esecuzione di uno script è di 90 minuti. Successivamente, lo script si timeout.
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, l'esecuzione del comando richiede la connettività (porta 443) agli indirizzi IP pubblici di Azure. Se l'estensione non ha accesso a questi endpoint, è possibile che gli script vengano eseguiti correttamente, ma non restituiscono i risultati. Se si sta bloccando il traffico nella macchina virtuale, è possibile usare i [tag di servizio](../../virtual-network/security-overview.md#service-tags) per consentire il traffico agli indirizzi IP pubblici di Azure usando il tag `AzureCloud`.

## <a name="available-commands"></a>Comandi disponibili

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Linux. Per eseguire qualsiasi script personalizzato desiderato, è possibile usare il comando **RunShellScript** . Quando si usa l'interfaccia della riga di comando di Azure o PowerShell per eseguire un comando, il valore specificato per il parametro `--command-id` o `-CommandId` deve essere uno dei valori elencati di seguito. Quando si specifica un valore che non è un comando disponibile, viene visualizzato questo errore:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrizione**|
|---|---|
|**RunShellScript**|Esegue uno script della shell di Linux.|
|**ifconfig**| Ottiene la configurazione di tutte le interfacce di rete.|

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'esempio seguente usa il comando [AZ VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per eseguire uno script della shell in una VM Linux di Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Per eseguire i comandi come utente diverso, immettere `sudo -u` per specificare un account utente.

## <a name="azure-portal"></a>Portale di Azure

Passare a una macchina virtuale nel [portale di Azure](https://portal.azure.com) e selezionare **Esegui comando** in **operazioni**. Viene visualizzato un elenco dei comandi disponibili da eseguire nella macchina virtuale.

![Elenco di comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi possono avere parametri di input facoltativi o obbligatori. Per questi comandi, i parametri vengono presentati come campi di testo per fornire i valori di input. Per ogni comando è possibile visualizzare lo script che viene eseguito espandendo **lo script di visualizzazione**. **RunShellScript** è diverso dagli altri comandi, in quanto consente di fornire uno script personalizzato.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Dopo aver scelto il comando, selezionare **Esegui** per eseguire lo script. Al termine dell'esecuzione dello script, viene restituito l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **ifconfig**.

![Eseguire l'output dello script di comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

L'esempio seguente usa il cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) per eseguire uno script di PowerShell in una macchina virtuale di Azure. Il cmdlet si aspetta che lo script a cui fa riferimento il parametro `-ScriptPath` sia presente nella posizione in cui il cmdlet viene eseguito.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

Per elencare i comandi di esecuzione o visualizzare i dettagli di un comando, è necessario disporre dell'autorizzazione `Microsoft.Compute/locations/runCommands/read` a livello di sottoscrizione. Il ruolo predefinito [Reader](../../role-based-access-control/built-in-roles.md#reader) e i livelli superiori hanno questa autorizzazione.

L'esecuzione di un comando richiede l'autorizzazione `Microsoft.Compute/virtualMachines/runCommand/action` a livello di sottoscrizione. Il ruolo [collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e i livelli superiori hanno questa autorizzazione.

È possibile utilizzare uno dei [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) o creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) per utilizzare il comando Esegui.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri modi per eseguire script e comandi in modalità remota nella macchina virtuale, vedere [eseguire script nella VM Linux](run-scripts-in-vm.md).
