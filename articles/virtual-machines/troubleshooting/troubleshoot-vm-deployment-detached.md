---
title: Risolvere i problemi di distribuzione della macchina virtuale a causa di dischi scollegati | Microsoft Docs
description: Risolvere i problemi di distribuzione della macchina virtuale a causa di dischi scollegati
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75486820"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>Risolvere i problemi di distribuzione della macchina virtuale a causa di dischi scollegati

## <a name="symptom"></a>Sintomo

Quando si tenta di aggiornare una macchina virtuale in cui si è verificato un errore di scollegamento del disco dati precedente, è possibile che venga riportato il codice di errore.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di riconnettere un disco dati la cui ultima operazione di scollegamento non è riuscita. Il modo migliore per uscire da questo stato è scollegare il disco che ha avuto esito negativo.

## <a name="solution-1-powershell"></a>Soluzione 1: PowerShell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>Passaggio 1: ottenere i dettagli della macchina virtuale e del disco

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

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Passaggio 2: impostare il flag per i dischi in errore su "true".

Ottenere l'indice della matrice del disco guasto e impostare il flag **toBeDetached** per il disco che ha avuto esito negativo (per il quale si è verificato l'errore **AttachDiskWhileBeingDetached** ) su "true". Questa impostazione implica lo scollegamento del disco dalla macchina virtuale. Il nome del disco che ha avuto esito negativo è reperibile in **ErrorMessage**.

> ! Nota: la versione dell'API specificata per le chiamate Get e put deve essere 2019-03-01 o successiva.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

In alternativa, è anche possibile scollegare il disco usando il comando seguente, che sarà utile per gli utenti che usano le versioni API prima del 01 marzo 2019.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>Passaggio 3: aggiornare la macchina virtuale

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>Soluzione 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>Passaggio 1: ottenere il payload della macchina virtuale.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Passaggio 2: impostare il flag per i dischi in errore su "true".

Impostare il flag **toBeDetached** per la mancata riuscita del disco su true nel payload restituito nel passaggio 1. Nota: la versione dell'API specificata per le chiamate Get e put deve essere `2019-03-01` maggiore o uguale a.

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

In alternativa, è anche possibile rimuovere il disco dati in errore dal payload precedente, utile per gli utenti che usano le versioni API prima del 01 marzo 2019.

### <a name="step-3-update-the-virtual-machine"></a>Passaggio 3: aggiornare la macchina virtuale

Usare il payload del corpo della richiesta impostato nel passaggio 2 e aggiornare la macchina virtuale come indicato di seguito:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Risposta di esempio:**

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
