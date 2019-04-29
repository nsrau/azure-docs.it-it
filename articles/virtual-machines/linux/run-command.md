---
title: Eseguire gli script della shell in una VM Linux in Azure
description: In questo argomento viene descritto come eseguire gli script all'interno di una macchina virtuale Linux di Azure tramite Esegui comando
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e865d4e9cbad2c2064d961bc6e407440ce8556fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772071"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Eseguire gli script della shell nella macchina virtuale Linux con Esegui comando

Esegui comando usa l'agente di macchine Virtuali per eseguire gli script della shell all'interno di una macchina virtuale Linux di Azure. Questi script possono essere usati per il computer generale o per la gestione delle applicazioni e può essere usato per diagnosticare e correggere i problemi di accesso e di rete della macchina virtuale e ottenere che la macchina virtuale torni a uno stato valido rapidamente.

## <a name="benefits"></a>Vantaggi

Sono disponibili più opzioni che possono essere usate per accedere alle macchine virtuali. Esegui comando può eseguire gli script nelle macchine virtuali in remoto tramite l'agente VM. Può essere usato tramite il portale di Azure, l'[API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) o l'[interfaccia della riga di comando di Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per le VM Linux.

Questa funzionalità è utile in tutti gli scenari in cui si desidera eseguire uno script all'interno di una macchina virtuale e fa parte dell'unico modo per risolvere i problemi e correggere una macchina virtuale che non dispone della porta aperta RDP o SSH a causa della rete impropria o della configurazione amministrativa dell'utente.

## <a name="restrictions"></a>Restrizioni

Di seguito c'è un elenco di restrizioni che sono presenti quando si usa l'esecuzione del comando.

* L'output verrà limitato all'ultimo pari a 4.096 byte
* Il tempo minimo per eseguire uno script è di circa 20 secondi
* Script eseguiti per impostazione predefinita come utente con privilegi elevati in Linux
* Può essere eseguito uno script in un momento
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare l'esecuzione di uno script
* Il tempo massimo in cui può essere eseguito uno script è 90 minuti, dopo di che si verifica il timeout
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, Esegui comando richiede la connettività agli indirizzi IP pubblici di Azure (porta 443). Se l'estensione non ha accesso a questi endpoint, gli script possono funzionare correttamente, ma non restituiscono risultati. Se si sta bloccando il traffico nella macchina virtuale, è possibile usare i [tag di servizio](../../virtual-network/security-overview.md#service-tags) per consentire il traffico verso gli indirizzi IP pubblici di Azure tramite il tag `AzureCloud`.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Ecco un esempio usando il comando [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) per eseguire uno script della shell su una macchina virtuale Linux di Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Per eseguire i comandi come utente diverso, è possibile usare `sudo -u`specificare un account utente da usare.

## <a name="azure-portal"></a>Portale di Azure

Passare a una macchina virtuale in [Azure](https://portal.azure.com) e selezionare **Esegui comando** in **OPERAZIONI**. Viene visualizzato un elenco di comandi disponibili per l'esecuzione nella macchina virtuale.

![Eseguire l'elenco comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi potrebbero avere parametri di input obbligatori o facoltativi. Per tali comandi vengono presentati i parametri come campi di testo di fornire i valori di input. Per ogni comando è possibile visualizzare lo script che viene eseguito espandendo **Visualizza script**. **RunShellScript** è diverso da altri comandi, in quanto consente di fornire script personalizzati.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Una volta scelto il comando, fare clic su **Esegui** per eseguire lo script. Lo script viene eseguito e,m al termine, restituisce l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **ifconfig**.

![Eseguire l'output dello script di comando ](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Comandi disponibili

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Linux. Il comando **RunShellScript** può essere usato per eseguire qualsiasi script personalizzato desiderato.

|**Nome**|**Descrizione**|
|---|---|
|**RunShellScript**|Esegue uno script della shell di Linux.|
|**ifconfig**| Visualizza la configurazione IP di tutte le interfacce di rete|

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

Elencare i comandi di esecuzione o mostrare i dettagli di un comando richiede l'`Microsoft.Compute/locations/runCommands/read` autorizzazione che il ruolo dell'elemento predefinito [Lettore](../../role-based-access-control/built-in-roles.md#reader) e versioni successive hanno.

L'esecuzione di un comando richiede l'`Microsoft.Compute/virtualMachines/runCommand/action` autorizzazione, che il ruolo [Collaboratore](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ha e versioni successive.

È possibile usare uno dei ruoli [incorporati](../../role-based-access-control/built-in-roles.md) o creare un ruolo [personalizzato](../../role-based-access-control/custom-roles.md) per usare Esegui del comando.

## <a name="next-steps"></a>Passaggi successivi

Vedere, [Eseguire gli script in VM Linux](run-scripts-in-vm.md) per altre informazioni su altri modi per eseguire comandi e script in remoto nella macchina virtuale.
