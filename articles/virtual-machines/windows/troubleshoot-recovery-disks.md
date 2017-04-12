---

title: Usare una macchina virtuale per la risoluzione dei problemi Windows con Azure PowerShell | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle VM Windows in Azure connettendo il disco del sistema operativo a una VM di ripristino usando Azure PowerShell
services: virtual-machines-windows
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/13/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 50e9982fbf33a39b69effed193a4bd137c07a14d
ms.lasthandoff: 03/31/2017


---

# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino usando Azure PowerShell
Se nella macchina virtuale Windows in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire alcuni passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare Azure PowerShell per connettere il disco rigido virtuale a un'altra VM Windows per risolvere eventuali errori e quindi ricreare la VM originale.


## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Eliminare la macchina virtuale su cui si riscontrano i problemi, mantenendo i dischi rigidi virtuali.
2. Collegare e montare il disco rigido virtuale in un'altra VM Windows per risolvere i problemi riscontrati.
3. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco rigido virtuale originale.
4. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
5. Creare una VM usando il disco rigido virtuale originale.

Verificare di aver prima installato e registrato [la versione più recente di Azure PowerShell](/powershell/azureps-cmdlets-docs) nella sottoscrizione:

```powershell
Login-AzureRMAccount
```

Negli esempi seguenti sostituire i nomi dei parametri con i valori desiderati. Alcuni esempi di nomi dei parametri sono `myResourceGroup`, `mystorageaccount` e `myVM`.


## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
È possibile visualizzare uno screenshot della VM in Azure per risolvere i problemi di avvio. Questo screenshot consente di identificare il motivo per cui non è possibile avviare una VM. L'esempio seguente ottiene lo screenshot dalla VM Windows denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Esaminare lo screenshot per determinare perché la macchina virtuale non riesce ad avviarsi correttamente. Esaminare i messaggi di errore o i codici di errore specifici visualizzati.


## <a name="view-existing-virtual-hard-disk-details"></a>Visualizzare i dettagli del disco rigido virtuale esistente
Prima di collegare il disco rigido virtuale a un'altra macchina virtuale, è necessario identificare il nome del disco rigido virtuale.

L'esempio seguente ottiene le informazioni relative alla macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Cercare `Vhd URI` nella sezione `StorageProfile` dall'output del comando precedente. Nell'output di esempio troncato seguente `Vhd URI` è visibile verso la fine del blocco di codice:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Eliminare la VM esistente
In Azure, i dischi rigidi virtuali e le macchine virtuali sono due risorse distinte. In un disco rigido virtuale sono archiviati il sistema operativo, le applicazioni e le configurazioni. La macchina virtuale è invece costituita da metadati che definiscono le dimensioni o il percorso, e da risorse di riferimento, ad esempio un disco rigido virtuale o una scheda di interfaccia di rete virtuale. A ogni disco rigido virtuale associato a una macchina virtuale viene assegnato un lease. È possibile collegare e scollegare i dischi dati anche quando la macchina virtuale è in esecuzione, mentre non è possibile scollegare il disco del sistema operativo, a meno che la risorsa di macchina non sia stata eliminata. Il lease continua ad associare il disco del sistema operativo e la macchina virtuale anche quando questa viene arrestata e deallocata.

Il primo passaggio per ripristinare la macchina virtuale consiste nell'eliminare la risorsa della macchina virtuale stessa. Anche se si elimina la macchina virtuale, i dischi rigidi virtuali restano nell'account di archiviazione. Dopo aver eliminato la macchina virtuale, il disco rigido virtuale viene collegato a un'altra macchina virtuale per diagnosticare e risolvere gli errori.

L'esempio seguente elimina la macchina virtuale denominata `myVM` dal gruppo di risorse `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Attendere il completamento dell'eliminazione della macchina virtuale prima di collegare il disco rigido virtuale a un'altra macchina virtuale. Il lease del disco rigido virtuale che lo associa alla macchina virtuale deve essere rilasciato prima di poter collegare il disco a un'altra macchina.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Collegare il disco rigido virtuale esistente a un'altra macchina virtuale
Nei passaggi successivi viene utilizzata un'altra macchina virtuale per la risoluzione dei problemi. Il disco rigido virtuale esistente viene collegato alla macchina virtuale usata per la risoluzione dei problemi, grazie alla quale è possibile individuare e modificare il contenuto del disco. Questo processo consente, ad esempio, di correggere eventuali errori di configurazione, di esaminare applicazioni aggiuntive o file del registro di sistema. Scegliere o creare un'altra macchina virtuale da usare per la risoluzione dei problemi.

Quando si collega il disco rigido virtuale esistente, specificare l'URL del disco ottenuto con il comando `Get-AzureRmVM` precedente. Nell'esempio seguente il disco rigido virtuale esistente viene collegato alla macchina virtuale usata per la risoluzione dei problemi denominata `myVMRecovery` nel gruppo di risorse `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Per aggiungere un disco, è necessario specificare le dimensioni del disco. Quando viene collegato un disco esistente, `-DiskSizeInGB` viene specificato come `$null`. Questo valore assicura che il disco dati venga collegato correttamente e senza bisogno di determinare le dimensioni reali del disco dati.


## <a name="mount-the-attached-data-disk"></a>Montare il disco dati collegato

1. Stabilire una connessione RDP alla VM di risoluzione dei problemi usando le credenziali appropriate. L'esempio seguente scarica il file di connessione RDP per la VM denominata `myVMRecovery` nel gruppo di risorse denominato `myResourceGroup` e lo scarica in `C:\Users\ops\Documents`.

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Il disco dati viene automaticamente rilevato e collegato. Visualizzare l'elenco di volumi collegati per determinare la lettera di unità, come segue:

    ```powershell
    Get-Disk
    ```

    L'output di esempio seguente illustra il disco rigido virtuale connesso a un disco **2**. Per visualizzare la lettera di unità, è anche possibile usare `Get-Volume`:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Risolvere i problemi del disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Dopo aver risolto gli errori, smontare e scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale con altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.

1. Dalla sessione RDP smontare il disco dati nella VM di ripristino. È necessario il numero del disco ottenuto dal cmdlet `Get-Disk` precedente. Usare quindi `Set-Disk` per impostare il disco come offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Verificare che il disco ora sia impostato come offline usando di nuovo `Get-Disk`. L'output di esempio seguente mostra che il disco ora è impostato come offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Uscire dalla sessione RDP. Dalla sessione di Azure PowerShell rimuovere il disco rigido virtuale dalla VM usata per la risoluzione dei problemi.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Creare una macchina virtuale dal disco rigido originale
Per creare una macchina virtuale dal disco rigido virtuale originale, usare [questo modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Il modello JSON effettivo è disponibile all'indirizzo seguente:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

Il modello distribuisce una macchina virtuale in una rete virtuale esistente, usando l'URL del disco rigido virtuale del comando precedente. L'esempio seguente distribuisce il modello nel gruppo di risorse `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Rispondere ai prompt del modello, ad esempio inserendo il nome della VM, il tipo di sistema operativo e le dimensioni della VM. Il valore `osDiskVhdUri` è lo stesso usato in precedenza per collegare il disco rigido virtuale esistente alla macchina virtuale usata per la risoluzione dei problemi.


## <a name="re-enable-boot-diagnostics"></a>Riabilitare la diagnostica di avvio

Quando si crea la macchina virtuale dal disco rigido virtuale esistente, la diagnostica di avvio potrebbe non essere abilitata automaticamente. L'esempio seguente abilita l'estensione di diagnostica nella macchina virtuale denominata `myVMDeployed` nel gruppo di risorse `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
