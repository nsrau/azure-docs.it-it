---
title: Acceleratore di scrittura di Azure per distribuzioni SAP | Microsoft Docs
description: Guida operativa per i sistemi SAP HANA distribuiti in macchine virtuali di Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d1ca15028590824cef95e3e9c2d957f9883a0e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Acceleratore di scrittura di Azure per distribuzioni SAP
L'acceleratore di scrittura di Azure è una funzionalità implementata esclusivamente per le macchine virtuali della serie M. L'acceleratore di scrittura di Azure non è disponibile con altre serie di macchine virtuali in Azure, ad eccezione della serie M. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per l'Archiviazione Premium di Azure. 

>[!NOTE]
> Attualmente, l'acceleratore di scrittura di Azure è disponibile come anteprima pubblica e richiede l'aggiunta dell'ID della sottoscrizione di Azure nell'elenco degli elementi consentiti.

La funzionalità dell'acceleratore di scrittura di Azure è disponibile come anteprima pubblica in:

- Stati Uniti occidentali 2
- Europa occidentale

## <a name="planning-for-using-azure-write-accelerator"></a>Pianificazione per l'uso dell'acceleratore di scrittura di Azure
L'acceleratore di scrittura di Azure deve essere usato per i volumi che contengono il log delle transazioni o i log di ripristino di un DBMS. Non è consigliabile usare l'acceleratore di scrittura di Azure per i volumi di dati di un sistema DBMS. Questa restrizione è consigliata perché l'acceleratore di scrittura di Azure richiede il montaggio dei dischi rigidi virtuali di Archiviazione Premium di Azure senza la memorizzazione nella cache delle letture aggiuntiva disponibile per l'Archiviazione Premium. Con questo tipo di memorizzazione nella cache si possono riscontrare vantaggi maggiori con i database tradizionali. Dato che l'acceleratore di scrittura influisce solo sulle attività di scrittura e non velocizza le letture, la progettazione supportata per SAP prevede l'uso dell'acceleratore di scrittura sulle unità del log delle transazioni o dei log di ripristino dei database SAP supportati. 

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). È pertanto necessario tenerne conto per la pianificazione. 

>[!NOTE]
> Dato che la funzionalità dell'acceleratore di scrittura di Azure è ancora in anteprima pubblica, non sono ancora supportate distribuzioni in scenari di produzione.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura di Azure. I limiti correnti sono:

- 16 dischi rigidi virtuali per una macchina virtuale M128xx
- 8 dischi rigidi virtuali per una macchina virtuale M64xx

> [!IMPORTANT]
> Per abilitare o disabilitare l'acceleratore di scrittura di Azure per un volume esistente costituito da più dischi di Archiviazione Premium di Azure in striping con gestori di dischi o volumi Windows, spazi di archiviazione Windows, file server di scalabilità orizzontale di Windows, LVM di Linux o MDADM, tutti i dischi che compongono il volume devono essere abilitati o disabilitati per l'acceleratore di scrittura in passaggi distinti. **Prima di abilitare o disabilitare l'acceleratore di scrittura in una configurazione di questo tipo, arrestare la macchina virtuale di Azure**. 


> [!IMPORTANT]
> Per abilitare l'acceleratore di scrittura di Azure in un disco di Azure esistente che NON fa parte di un volume costituito da più dischi con gestori di dischi o volumi Windows, spazi di archiviazione Windows, file server di scalabilità orizzontale di Windows, LVM di Linux o MDADM, è necessario arrestare il carico di lavoro che accede al disco di Azure. Le applicazioni di database che usano il disco di Azure DEVONO essere arrestate.

> [!IMPORTANT]
> L'abilitazione dell'acceleratore di scrittura per il disco del sistema operativo di Azure della macchina virtuale comporterà il riavvio della macchina virtuale. 

L'abilitazione dell'acceleratore di scrittura di Azure per i dischi operativi non dovrebbe essere necessaria per le configurazioni di macchine virtuali correlate a SAP.

### <a name="restrictions-when-using-azure-write-accelerator"></a>Restrizioni per l'uso dell'acceleratore di scrittura di Azure
Quando si usa l'acceleratore di scrittura di Azure per dischi o dischi rigidi virtuali di Azure, si applicano queste restrizioni:

- La memorizzazione nella cache del disco Premium deve essere impostata su 'Nessuna'. Tutte le altre modalità di memorizzazione nella cache non sono supportate.
- La creazione di snapshot sul disco abilitato per l'acceleratore di scrittura non è ancora supportata. Questa restrizione impedisce al Servizio Backup di Azure di creare uno snapshot coerente con l'applicazione di tutti i dischi della macchina virtuale.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Abilitazione dell'acceleratore di scrittura per un disco specifico
Le prossime sezioni descrivono come abilitare l'acceleratore di scrittura di Azure nei dischi rigidi virtuali di Archiviazione Premium di Azure.

Attualmente, gli unici metodi disponibili per abilitare l'acceleratore di scrittura sono l'API Rest di Azure e PowerShell. Nel corso delle prossime settimane verrà aggiunto il supporto di altri metodi nel portale di Azure.

### <a name="prerequisites"></a>Prerequisiti
I prerequisiti seguenti sono validi per l'utilizzo dell'acceleratore di scrittura di Azure in questo momento:

- L'ID della sottoscrizione usato per distribuire la macchina virtuale e le risorse di archiviazione per la macchina virtuale deve essere incluso nell'elenco degli elementi consentiti. Contattare il referente di Microsoft CSA, GBB o l'account manager per richiedere l'aggiunta dell'ID della sottoscrizione all'elenco degli elementi consentiti. 
- I dischi su cui si vuole applicare l'acceleratore di scrittura di Azure devono essere [dischi gestiti di Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Abilitazione tramite PowerShell
Il modulo Azure PowerShell dalla versione 5.5.0 include modifiche ai cmdlet pertinenti per abilitare o disabilitare l'acceleratore di scrittura di Azure per specifici dischi di Archiviazione Premium di Azure.
Per abilitare o distribuire i dischi supportati dall'acceleratore di scrittura, i comandi di PowerShell seguenti sono stati modificati ed estesi per accettare un parametro per l'acceleratore di scrittura.

È stato aggiunto il nuovo parametro opzionale "WriteAccelerator" ai cmdlet seguenti: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Se non si assegna il parametro, la proprietà viene impostata su false e verranno distribuiti dischi senza supporto dell'acceleratore di scrittura di Azure.

È stato aggiunto il nuovo parametro opzionale "OsDiskWriteAccelerator" ai cmdlet seguenti: 

- Set-AzureRmVmssStorageProfile

Se non si assegna il parametro, la proprietà viene impostata su false e verranno distribuiti dischi che non sfruttano l'acceleratore di scrittura di Azure.

È stato aggiunto il nuovo parametro booleano facoltativo (non nullable) "OsDiskWriteAccelerator" ai cmdlet seguenti: 

- Update-AzureRmVM
- Update-AzureRmVmss

Specificare $true o $false per controllare il supporto dell'acceleratore di scrittura di Azure per i dischi.

Ecco alcuni esempi di comandi:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

È possibile creare script per due scenari principali, come illustrato nelle sezioni seguenti.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Aggiunta di un nuovo disco supportato dall'acceleratore di scrittura di Azure
È possibile usare questo script per aggiungere un nuovo disco alla macchina virtuale. Il disco creato con questo script userà l'acceleratore di scrittura di Azure.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
È necessario adattare i nomi di macchina virtuale, disco e gruppo di risorse, le dimensioni del disco e il LunID del disco per la distribuzione specifica.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Abilitazione dell'acceleratore di scrittura di Azure per un disco di Azure esistente
Se è necessario abilitare l'acceleratore di scrittura per un disco esistente, è possibile usare questo script per eseguire l'attività:

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

È necessario adattare i nomi di macchina virtuale, disco e gruppo di risorse. Lo script precedente aggiunge l'acceleratore di scrittura a un disco esistente se il valore $newstatus è impostato su '$true'. L'uso del valore '$false' disabiliterà l'acceleratore di scrittura per il disco specificato.

> [!Note]
> L'esecuzione dello script precedente consente di scollegare il disco specificato, abilitare l'acceleratore di scrittura per il disco e quindi di ricollegare il disco.




### <a name="enabling-through-rest-apis"></a>Abilitazione tramite le API Rest
Per eseguire la distribuzione tramite l'API Rest di Azure, è necessario installare ARMClient di Azure

#### <a name="install-armclient"></a>Installare ARMClient

Per eseguire ARMClient, è necessario installarlo tramite Chocolatey. È possibile installarlo tramite cmd.exe o PowerShell. Usare diritti elevati per questi comandi ("Esegui come amministratore").

Se si usa cmd.exe eseguire il comando seguente:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Con PowerShell è necessario usare:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

È ora possibile installare ARMClient con il comando seguente in cmd.exe o PowerShell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Ottenere la configurazione corrente della macchina virtuale
Per modificare gli attributi della configurazione del disco, è prima di tutto necessario ottenere la configurazione corrente in un file JSON. È possibile ottenere la configurazione corrente eseguendo il comando seguente:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Sostituire i termini all'interno di '<<   >>' con i dati personali, incluso il nome da assegnare al file JSON.

L'output potrebbe essere simile al seguente:

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Il passaggio successivo include l'aggiornamento del file JSON e l'abilitazione dell'acceleratore di scrittura nel disco denominato 'log1'. Questa operazione può essere eseguita aggiungendo l'attributo nel file JSON dopo la voce della cache del disco. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Aggiornare quindi la distribuzione esistente con questo comando:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

L'output dovrebbe essere simile al seguente. È possibile notare che l'acceleratore di scrittura è abilitato per un disco.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Dopo la modifica, l'unità sarà supportata dall'acceleratore di scrittura.

 