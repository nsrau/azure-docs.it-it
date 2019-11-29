---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566290"
---
L'acceleratore di scrittura è una funzionalità del disco per macchine virtuali di serie M disponibile in Archiviazione Premium esclusivamente con Azure Managed Disks. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per Archiviazione Premium di Azure. L'acceleratore di scrittura è la soluzione ideale quando gli aggiornamenti dei file di log devono persistere nel disco in modo altamente efficiente per i database moderni.

L'acceleratore di scrittura è disponibile a livello generale per le macchine virtuali di serie M nel cloud pubblico.

## <a name="planning-for-using-write-accelerator"></a>Pianificazione per l'uso dell'acceleratore di scrittura

L'acceleratore di scrittura deve essere usato per i volumi che contengono il log delle transazioni o i log di ripristino di un DBMS. Non è consigliabile usare l'acceleratore di scrittura per i volumi di dati di un DBMS, dato che la funzionalità è stata ottimizzata per essere usata sui dischi di registro.

L'acceleratore di scrittura funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> L'abilitazione dell'acceleratore di scrittura per il disco del sistema operativo della macchina virtuale comporterà il riavvio della macchina virtuale.
>
> Per abilitare l'acceleratore di scrittura in un disco di Azure esistente che NON fa parte di un volume costituito da più dischi con gestori di dischi o volumi Windows, spazi di archiviazione Windows, file server di scalabilità orizzontale di Windows, LVM di Linux o MDADM, è necessario arrestare il carico di lavoro che accede al disco di Azure. Le applicazioni di database che usano il disco di Azure DEVONO essere arrestate.
>
> Per abilitare o disabilitare l'acceleratore di scrittura per un volume esistente costituito da più dischi di Archiviazione Premium di Azure in striping con gestori di dischi o volumi Windows, spazi di archiviazione Windows, file server di scalabilità orizzontale di Windows, LVM di Linux o MDADM, tutti i dischi che compongono il volume devono essere abilitati o disabilitati per l'acceleratore di scrittura in passaggi distinti. **Prima di abilitare o disabilitare l'acceleratore di scrittura in una configurazione di questo tipo, arrestare la macchina virtuale di Azure**.

L'abilitazione dell'acceleratore di scrittura per i dischi del sistema operativo non dovrebbe essere necessaria per le configurazioni di macchine virtuali correlate a SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Restrizioni per l'uso dell'acceleratore di scrittura

Quando si usa l'acceleratore di scrittura per dischi o dischi rigidi virtuali di Azure, si applicano queste restrizioni:

- La memorizzazione nella cache del disco Premium deve essere impostata su 'Nessuna' o 'Sola lettura'. Tutte le altre modalità di memorizzazione nella cache non sono supportate.
- Gli snapshot non sono attualmente supportati per i dischi abilitati per l'acceleratore di scrittura. Durante il backup, il servizio Backup di Azure esclude automaticamente i dischi abilitati per l'acceleratore di scrittura collegati alla macchina virtuale.
- Solo dimensioni di I/O più piccole (<=512 KiB) usano il percorso dell'acceleratore. In scenari di carichi di lavoro dove i dati vengono caricati in blocco oppure dove i buffer dei log delle transazioni di diversi sistemi di gestione di database vengono popolati con quantità rilevanti di dati prima di venire salvati in modo permanente nella risorsa di archiviazione, è possibile che l'I/O scritto su disco non usi il percorso dell'acceleratore.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura. I limiti correnti sono:

| SKU di VM | Numero di dischi con l'acceleratore di scrittura | Numero di operazioni di I/O al secondo dell'acceleratore di scrittura per ogni macchina virtuale |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

I limiti di operazioni di I/O al secondo sono per ogni macchina virtuale e *non* per ogni disco. Tutti i dischi dell'acceleratore di scrittura condividono lo stesso limite di operazioni di I/O al secondo per ogni macchina virtuale.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Abilitazione dell'acceleratore di scrittura per un disco specifico

Le prossime sezioni descrivono come abilitare l'acceleratore di scrittura nei dischi rigidi virtuali di Archiviazione Premium di Azure.

### <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti sono validi per l'utilizzo dell'acceleratore di scrittura in questo momento:

- I dischi ai quali si vuole applicare l'acceleratore di scrittura di Azure devono essere [dischi gestiti di Azure](https://azure.microsoft.com/services/managed-disks/) in Archiviazione Premium.
- È necessario usare una macchina virtuale di serie M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Abilitazione dell'acceleratore di scrittura di Azure con Azure PowerShell

A partire dalla versione 5.5.0, il modulo Azure PowerShell include modifiche ai cmdlet per abilitare o disabilitare l'acceleratore di scrittura per specifici dischi di Archiviazione Premium di Azure.
Per abilitare o distribuire i dischi supportati dall'acceleratore di scrittura, i comandi di PowerShell seguenti sono stati modificati ed estesi per accettare un parametro per l'acceleratore di scrittura.

È stato aggiunto il nuovo parametro opzionale **-WriteAccelerator** ai cmdlet seguenti:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Se non si assegna il parametro, la proprietà viene impostata su false e verranno distribuiti dischi senza supporto dell'acceleratore di scrittura.

È stato aggiunto il nuovo parametro opzionale **-OsDiskWriteAccelerator** ai cmdlet seguenti:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Se non si specifica il parametro, la proprietà viene impostata su false di default e verranno distribuiti dischi che non sfruttano l'acceleratore di scrittura.

È stato aggiunto il nuovo parametro booleano facoltativo (non nullable) **-OsDiskWriteAccelerator** ai cmdlet seguenti:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Specificare $true o $false per controllare il supporto dell'acceleratore di scrittura di Azure per i dischi.

Ecco alcuni esempi di comandi:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

È possibile creare script per due scenari principali, come illustrato nelle sezioni seguenti.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Aggiunta di un nuovo disco supportato dall'acceleratore di scrittura tramite PowerShell

È possibile usare questo script per aggiungere un nuovo disco alla macchina virtuale. Il disco creato con questo script usa l'acceleratore di scrittura.

Sostituire `myVM`, `myWAVMs`, `log001`, dimensioni del disco e LunID del disco con i valori appropriati per la specifica distribuzione.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Abilitazione dell'acceleratore di scrittura per un disco di Azure esistente con PowerShell

Questo script può essere utilizzato per abilitare l'acceleratore di scrittura per un disco esistente. Sostituire `myVM`, `myWAVMs` e `test-log001` con i valori appropriati per la specifica distribuzione. Lo script aggiunge l'acceleratore di scrittura a un disco esistente in cui il valore **$newstatus** è impostato su '$true'. L'uso del valore '$false' disabiliterà l'acceleratore di scrittura per il disco specificato.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> L'esecuzione dello script precedente consente di scollegare il disco specificato, abilitare l'acceleratore di scrittura per il disco e quindi di ricollegare il disco.

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Abilitazione dell'acceleratore di scrittura con il portale di Azure

È possibile abilitare l'acceleratore di scrittura tramite il portale, dove si specificano le impostazioni di memorizzazione nella cache del disco:

![Acceleratore di scrittura nel portale di Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Abilitazione dell'acceleratore di scrittura con l’interfaccia della riga di comando di Azure

È possibile usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) per abilitare l'acceleratore di scrittura.

Per abilitare l'acceleratore di scrittura in un disco esistente, usare [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update). È possibile usare gli esempi seguenti sostituendo diskName, VMName e ResourceGroup con i propri valori: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Per collegare un disco con l'acceleratore di scrittura abilitato, usare [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach). È possibile usare l'esempio seguente sostituendo i valori appropriati: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Per disabilitare l'acceleratore di scrittura, usare [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update) impostando le proprietà su false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Abilitazione dell'acceleratore di scrittura con l’API Rest

Per eseguire la distribuzione tramite l'API Rest di Azure, è necessario installare ARMClient di Azure.

### <a name="install-armclient"></a>Installare ARMClient

Per eseguire ARMClient, è necessario installarlo tramite Chocolatey. È possibile installarlo tramite cmd.exe o PowerShell. Usare diritti elevati per questi comandi ("Esegui come amministratore").

Se si usa cmd.exe eseguire il comando seguente: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Se si usa Power Shell, eseguire il comando seguente: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

A questo punto è possibile installare l'armclient usando il comando seguente in cmd.exe o PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Ottenere la configurazione corrente della macchina virtuale

Per modificare gli attributi della configurazione del disco, è prima di tutto necessario ottenere la configurazione corrente in un file JSON. È possibile ottenere la configurazione corrente eseguendo il comando seguente: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Sostituire i termini all'interno di '<<   >>' con i dati personali, incluso il nome da assegnare al file JSON.

L'output potrebbe essere simile al seguente:

```JSON
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

Il passaggio successivo include l'aggiornamento del file JSON e l'abilitazione dell'acceleratore di scrittura nel disco denominato 'log1'. Questo passaggio può essere eseguito aggiungendo l'attributo nel file JSON dopo la voce della cache del disco.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Aggiornare quindi la distribuzione esistente con questo comando: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

L'output dovrebbe essere simile al seguente. È possibile notare che l'acceleratore di scrittura è abilitato per un disco.

```JSON
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
          "writeAcceleratorEnabled": true,
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

Una volta terminata la modifica, l'unità sarà supportata dall'acceleratore di scrittura.
