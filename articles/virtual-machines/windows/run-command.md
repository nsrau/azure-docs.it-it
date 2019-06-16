---
title: Eseguire gli script di PowerShell in una macchina virtuale di Windows in Azure
description: In questo argomento viene descritto come eseguire gli script di PowerShell all'interno di una macchina virtuale di Linux di Azure tramite Esegui comando
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 23973445992ceaeb0cd3bc0589665f2fac5b64e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575319"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Eseguire gli script di PowerShell nella macchina virtuale Linux con Esegui comando

Esegui comando usa l'agente di macchine virtuali per eseguire gli script di PowerShell all'interno di una macchina virtuale Windows di Azure. Questi script possono essere usati per il computer generale o per la gestione delle applicazioni e può essere usato per diagnosticare e correggere i problemi di accesso e di rete della macchina virtuale e ottenere che la macchina virtuale torni a uno stato valido rapidamente.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Vantaggi

Sono disponibili più opzioni che possono essere usate per accedere alle macchine virtuali. Esegui comando può eseguire gli script nelle macchine virtuali in remoto tramite l'agente VM. Può essere usato tramite il portale di Azure, l'[API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) o [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) per le VM Windows.

Questa funzionalità è utile in tutti gli scenari in cui si desidera eseguire uno script all'interno di una macchina virtuale e fa parte dell'unico modo per risolvere i problemi e correggere una macchina virtuale che non dispone della porta aperta RDP o SSH a causa della rete impropria o della configurazione amministrativa dell'utente.

## <a name="restrictions"></a>Restrizioni

Le seguenti limitazioni si applicano quando si usa Esegui comando

* L'output verrà limitato all'ultimo pari a 4096 byte
* Il tempo minimo per eseguire uno script è di circa 20 secondi
* Gli script eseguiti come sistema in Windows
* Può essere eseguito uno script in un momento
* Gli script che richiedono informazioni (modalità interattiva) non sono supportati.
* Non è possibile annullare l'esecuzione di uno script
* Il tempo massimo in cui può essere eseguito uno script è 90 minuti, dopo di che si verifica il timeout
* La connettività in uscita dalla macchina virtuale è necessaria per restituire i risultati dello script.

> [!NOTE]
> Per funzionare correttamente, Esegui comando richiede la connettività agli indirizzi IP pubblici di Azure (porta 443). Se l'estensione non ha accesso a questi endpoint, gli script possono funzionare correttamente, ma non restituiscono risultati. Se si sta bloccando il traffico nella macchina virtuale, è possibile usare i [tag di servizio](../../virtual-network/security-overview.md#service-tags) per consentire il traffico verso gli indirizzi IP pubblici di Azure tramite il tag `AzureCloud`.

## <a name="run-a-command"></a>Esegui un comando

Passare a una macchina virtuale in [Azure](https://portal.azure.com) e selezionare **Esegui comando** in **OPERAZIONI**. Viene visualizzato un elenco di comandi disponibili per l'esecuzione nella macchina virtuale.

![Eseguire l'elenco comandi](./media/run-command/run-command-list.png)

Seleziona un comando da eseguire Alcuni comandi potrebbero avere parametri di input obbligatori o facoltativi. Per tali comandi vengono presentati i parametri come campi di testo di fornire i valori di input. Per ogni comando è possibile visualizzare lo script che viene eseguito espandendo **Visualizza script**. **RunPowerShellScript** è diverso da altri comandi, in quanto consente di fornire script personalizzati.

> [!NOTE]
> I comandi incorporati non sono modificabili.

Una volta scelto il comando, fare clic su **Esegui** per eseguire lo script. Lo script viene eseguito e,m al termine, restituisce l'output e gli eventuali errori nella finestra di output. Lo screenshot seguente illustra un esempio di output dall'esecuzione del comando **RDPSettings**.

![Eseguire l'output dello script di comando](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Comandi:

Questa tabella mostra l'elenco di comandi disponibili per le macchine virtuali Windows. Il comando **RunPowerShellScript** può essere usato per eseguire qualsiasi script personalizzato desiderato.

|**Nome**|**Descrizione**|
|---|---|
|**RunPowerShellScript**|Esegue uno script di PowerShell|
|**EnableRemotePS**|Configura il computer per abilitare PowerShell remoto.|
|**EnableAdminAccount**|Controlla se l'account Administrator locale è disabilitato e, in tal caso, lo consente.|
|**IPConfig**| Indirizzo Visualizza le informazioni dettagliate per l'indirizzo IP, subnet mask e il gateway predefinito per ogni scheda associata a TCP/IP.|
|**RDPSettings**|Controlla le impostazioni del Registro e le impostazioni dei criteri di dominio. Suggerisce le azioni dei criteri se il computer fa parte di un dominio o modifica le impostazioni per i valori predefiniti.|
|**ResetRDPCert**|Rimuove il certificato SSL associato al listener del protocollo RDP e ripristina la sicurezza del listener RDP per impostazione predefinita. Usare questo script se vengono visualizzati eventuali problemi con il certificato.|
|**SetRDPPort**|Imposta il valore predefinito o il numero di porta dell'utente specificato per le connessioni Desktop remoto. Abilita la regola del firewall per l'accesso in ingresso alla porta.|

## <a name="powershell"></a>PowerShell

Ecco un esempio usando il cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) per eseguire uno script di PowerShell in una macchina virtuale di Azure. Il cmdlet si aspetta che lo script a cui fa riferimento il parametro `-ScriptPath` sia presente nella posizione in cui il cmdlet viene eseguito.


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitare l'accesso a Esegui comando

Per elencare i comandi di esecuzione o che visualizza i dettagli di un comando richiedono il `Microsoft.Compute/locations/runCommands/read` l'autorizzazione a livello di sottoscrizione, quale predefiniti [lettore](../../role-based-access-control/built-in-roles.md#reader) ruolo e versioni successive.

Richiede l'esecuzione di un comando il `Microsoft.Compute/virtualMachines/runCommand/action` l'autorizzazione a livello di sottoscrizione, che il [collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ruolo e versioni successive.

È possibile usare uno dei ruoli [incorporati](../../role-based-access-control/built-in-roles.md) o creare un ruolo [personalizzato](../../role-based-access-control/custom-roles.md) per usare Esegui del comando.

## <a name="next-steps"></a>Passaggi successivi

Vedere, [Eseguire gli script in VM Windows](run-scripts-in-vm.md) per altre informazioni su altri modi per eseguire comandi e script in remoto nella macchina virtuale.
