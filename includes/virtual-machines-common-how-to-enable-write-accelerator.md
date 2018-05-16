---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 4fe1f2ad4bad9d670094bbb4eed188baf28108ea
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="write-accelerator"></a>Acceleratore di scrittura
L'acceleratore di scrittura è una funzionalità del disco per macchine virtuali di serie M disponibile in Archiviazione Premium esclusivamente con Azure Managed Disks. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per Archiviazione Premium di Azure. L'acceleratore di scrittura è la soluzione ideale quando gli aggiornamenti dei file di log devono persistere nel disco in modo altamente efficiente per i database moderni.

L'acceleratore di scrittura è disponibile a livello generale per le macchine virtuali di serie M nel cloud pubblico.

## <a name="planning-for-using-write-accelerator"></a>Pianificazione per l'uso dell'acceleratore di scrittura
L'acceleratore di scrittura deve essere usato per i volumi che contengono il log delle transazioni o i log di ripristino di un DBMS. Non è consigliabile usare l'acceleratore di scrittura per i volumi di dati di un DBMS, dato che la funzionalità è stata ottimizzata per essere usata sui dischi di registro.

L'acceleratore di scrittura funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 


> [!IMPORTANT]
> Per abilitare o disabilitare l'acceleratore di scrittura per un volume esistente costituito da più dischi di Archiviazione Premium di Azure in striping con gestori di dischi o volumi Windows, spazi di archiviazione Windows, file server di scalabilità orizzontale di Windows, LVM di Linux o MDADM, tutti i dischi che compongono il volume devono essere abilitati o disabilitati per l'acceleratore di scrittura in passaggi distinti. **Prima di abilitare o disabilitare l'acceleratore di scrittura in una configurazione di questo tipo, arrestare la macchina virtuale di Azure**. 


> [!IMPORTANT]
> Per abilitare l'acceleratore di scrittura in un disco di Azure esistente che NON fa parte di un volume costituito da più dischi con gestori di dischi o volumi Windows, spazi di archiviazione Windows, file server di scalabilità orizzontale di Windows, LVM di Linux o MDADM, è necessario arrestare il carico di lavoro che accede al disco di Azure. Le applicazioni di database che usano il disco di Azure DEVONO essere arrestate.

> [!IMPORTANT]
> L'abilitazione dell'acceleratore di scrittura per il disco del sistema operativo della macchina virtuale comporterà il riavvio della macchina virtuale. 

L'abilitazione dell'acceleratore di scrittura per i dischi del sistema operativo non dovrebbe essere necessaria per le configurazioni di macchine virtuali correlate a SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Restrizioni per l'uso dell'acceleratore di scrittura
Quando si usa l'acceleratore di scrittura per dischi o dischi rigidi virtuali di Azure, si applicano queste restrizioni:

- La memorizzazione nella cache del disco Premium deve essere impostata su 'Nessuna' o 'Sola lettura'. Tutte le altre modalità di memorizzazione nella cache non sono supportate.
- La creazione di snapshot sul disco abilitato per l'acceleratore di scrittura non è ancora supportata. Questa restrizione impedisce al Servizio Backup di Azure di creare uno snapshot coerente con l'applicazione di tutti i dischi della macchina virtuale.
- Solo dimensioni di I/O più piccole (<=32KiB) usano il percorso dell'acceleratore. In scenari di carichi di lavoro dove i dati vengono caricati in blocco oppure dove i buffer dei log delle transazioni di diversi sistemi di gestione di database vengono popolati con quantità rilevanti di dati prima di venire salvati in modo permanente nella risorsa di archiviazione, è possibile che l'I/O scritto su disco non usi il percorso dell'acceleratore.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura. I limiti correnti sono:

| SKU di VM | Numero di dischi con l'acceleratore di scrittura | Numero di operazioni di I/O al secondo dell'acceleratore di scrittura per ogni macchina virtuale |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Abilitazione dell'acceleratore di scrittura per un disco specifico
Le prossime sezioni descrivono come abilitare l'acceleratore di scrittura nei dischi rigidi virtuali di Archiviazione Premium di Azure.


### <a name="prerequisites"></a>prerequisiti
I prerequisiti seguenti sono validi per l'utilizzo dell'acceleratore di scrittura in questo momento:

- I dischi ai quali si vuole applicare l'acceleratore di scrittura di Azure devono essere [dischi gestiti di Azure](https://azure.microsoft.com/services/managed-disks/) in Archiviazione Premium.
- È necessario usare una macchina virtuale di serie M

### <a name="enabling-through-power-shell"></a>Abilitazione tramite PowerShell
A partire dalla versione 5.5.0, il modulo Azure PowerShell include modifiche ai cmdlet per abilitare o disabilitare l'acceleratore di scrittura per specifici dischi di Archiviazione Premium di Azure.
Per abilitare o distribuire i dischi supportati dall'acceleratore di scrittura, i comandi di PowerShell seguenti sono stati modificati ed estesi per accettare un parametro per l'acceleratore di scrittura.

È stato aggiunto il nuovo parametro opzionale "WriteAccelerator" ai cmdlet seguenti: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Se non si assegna il parametro, la proprietà viene impostata su false e verranno distribuiti dischi senza supporto dell'acceleratore di scrittura.

È stato aggiunto il nuovo parametro opzionale "OsDiskWriteAccelerator" ai cmdlet seguenti: 

- Set-AzureRmVmssStorageProfile

Se non si assegna il parametro, la proprietà viene impostata su false e verranno distribuiti dischi che non sfruttano l'acceleratore di scrittura.

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

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>Aggiunta di un nuovo disco supportato dall'acceleratore di scrittura
È possibile usare questo script per aggiungere un nuovo disco alla macchina virtuale. Il disco creato con questo script userà l'acceleratore di scrittura.

```

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
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

È necessario adattare i nomi di macchina virtuale, disco e gruppo di risorse. Lo script precedente aggiunge l'acceleratore di scrittura a un disco esistente in cui il valore $newstatus è impostato su '$true'. L'uso del valore '$false' disabiliterà l'acceleratore di scrittura per il disco specificato.

> [!Note]
> L'esecuzione dello script precedente consente di scollegare il disco specificato, abilitare l'acceleratore di scrittura per il disco e quindi di ricollegare il disco.

### <a name="enabling-through-azure-portal"></a>Abilitazione tramite il portale di Azure

È possibile abilitare l'acceleratore di scrittura tramite il portale, dove si specificano le impostazioni di memorizzazione nella cache del disco: 

![Acceleratore di scrittura nel portale di Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)


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

Il passaggio successivo include l'aggiornamento del file JSON e l'abilitazione dell'acceleratore di scrittura nel disco denominato 'log1'. Questo passaggio può essere eseguito aggiungendo l'attributo nel file JSON dopo la voce della cache del disco. 

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

 
