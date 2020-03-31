---
title: Risolvere i problemi relativi alla distribuzione di macchine virtuali a causa di dischi scollegati. Documenti Microsoft
description: Risolvere i problemi di distribuzione delle macchine virtuali a causa di dischi scollegatiTroubleshoot virtual machine deployment due to detached disks
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486820"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>Risolvere i problemi di distribuzione delle macchine virtuali a causa di dischi scollegatiTroubleshoot virtual machine deployment due to detached disks

## <a name="symptom"></a>Sintomo

Quando si sta tentando di aggiornare una macchina virtuale il cui precedente disconnessione del disco dati non è riuscita, è possibile che si verifichi questo codice di errore.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di ricollegare un disco dati la cui ultima operazione di scollegamento non è riuscita. Il modo migliore per uscire da questo stato consiste nello scollegare il disco in errore.

## <a name="solution-1-powershell"></a>Soluzione 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>Passaggio 1: Ottenere i dettagli della macchina virtuale e del discoStep 1: Get the virtual machine and disk details

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Passaggio 2: Impostare il flag per i dischi con errori su "true".

Ottenere l'indice di array del disco in errore e impostare il flag **toBeDetached** per il disco in errore (per il quale si è verificato l'errore **AttachDiskWhileBeingDetached)** su "true". Questa impostazione implica lo scollegamento del disco dalla macchina virtuale. Il nome del disco in errore è disponibile nel **messaggio di errore**.

> ! Nota: la versione dell'API specificata per le chiamate Get e Put deve essere 2019-03-01 o superiore.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

In alternativa, è anche possibile scollegare questo disco utilizzando il comando seguente, che sarà utile per gli utenti che utilizzano le versioni API prima del 01 marzo 2019.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>Passaggio 3: Aggiornare la macchina virtualeStep 3: Update the virtual machine

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>Soluzione 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>Passaggio 1: Ottenere il payload della macchina virtuale.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Passaggio 2: Impostare il flag per i dischi con errori su "true".

Impostare il flag **toBeDetached** per il disco in errore su true nel payload restituito nel passaggio 1. Nota: la versione API specificata per le `2019-03-01` chiamate Get and Put deve essere maggiore o superiore.

**Corpo della richiesta di esempio**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

In alternativa è anche possibile rimuovere il disco dati in errore dal payload precedente, che è utile per gli utenti che utilizzano versioni API prima del 01 marzo 2019.

### <a name="step-3-update-the-virtual-machine"></a>Passaggio 3: Aggiornare la macchina virtualeStep 3: Update the virtual machine

Usare il payload del corpo della richiesta impostato nel passaggio 2 e aggiornare la macchina virtuale come segue:Use the request body payload set in Step 2 and update the virtual machine as follows:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Esempio di risposta:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>Passaggi successivi

Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md).

Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md).
