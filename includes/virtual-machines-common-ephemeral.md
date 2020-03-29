---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155403"
---
I dischi del sistema operativo effimeri vengono creati nell'archiviazione della macchina virtuale (VM) locale e non vengono salvati nell'archiviazione di Azure remota. I dischi del sistema operativo effimeri funzionano bene per i carichi di lavoro senza stato, in cui le applicazioni sono tolleranti agli errori delle singole macchine virtuali, ma sono più interessati dal tempo di distribuzione della macchina virtuale o dal reimaging delle singole istanze della macchina virtuale. Con il disco Del sistema operativo effimero, si ottiene una latenza di lettura/scrittura inferiore sul disco del sistema operativo e una nuova immagine della macchina virtuale. 
 
Le caratteristiche principali dei dischi effimeri sono: 
- Ideale per applicazioni senza stato.
- Possono essere utilizzati sia con Marketplace che con immagini personalizzate.
- Possibilità di reimpostare o ricreare rapidamente l'immagine delle macchine virtuali e delle istanze del set di scalabilità sullo stato di avvio originale.  
- Latenza inferiore, simile a un disco temporaneo. 
- I dischi del sistema operativo effimero sono gratuiti, non si ha alcun costo di archiviazione per il disco del sistema operativo.
- Sono disponibili in tutte le aree di Azure.They are available in all Azure regions. 
- Il disco del sistema operativo effimero è supportato da [Shared Image Gallery](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Differenze principali tra dischi del sistema operativo persistenti ed effimeri:

|                             | Disco del sistema operativo persistente                          | Disco del sistema operativo temporaneo                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite di dimensione per il disco del sistema operativo      | 2 TiB                                                                                        | Dimensioni della cache per la dimensione della macchina virtuale o 2TiB, a seconda del valore più piccolo. Per la dimensione della **cache in GiB**, vedere [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Dimensioni delle macchine virtuali supportate          | Tutti                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Supporto del tipo di disco           | Disco del sistema operativo gestito e non gestito                                                                | Solo disco del sistema operativo gestito                                                               |
| Supporto di area              | Tutte le aree                                                                                  | Tutte le aree                              |
| Salvataggio permanente dei dati            | I dati del disco del sistema operativo scritti sul disco del sistema operativo vengono archiviati in Archiviazione di AzureOS disk disk written to OS disk are stored in Azure Storage                                  | I dati scritti sul disco del sistema operativo vengono archiviati nell'archivio macchina virtuale locale e non vengono salvati in modo permanente in Archiviazione di Azure.Data written to OS disk is stored to the local VM storage and is not persisted to Azure Storage. |
| Stato di stop deallocato      | Le macchine virtuali e le istanze del set di scalabilità possono essere deallocate e riavviate dallo stato di arresto deallocato | Le macchine virtuali e le istanze del set di scalabilità non possono essere deallocate                                  |
| Supporto del disco del sistema operativo specializzato | Sì                                                                                          | No                                                                                 |
| Ridimensionamento del disco del sistema operativo              | Supportato durante la creazione della macchina virtuale e dopo l'arresto della macchina virtuale deallocatoSupported during VM creation and after VM is stop-deallocated                                | Supportato solo durante la creazione della macchina virtualeSupported during VM creation only                                                  |
| Ridimensionamento in una nuova dimensione della macchina virtualeResizing to a new VM size   | I dati del disco del sistema operativo vengono mantenuti                                                                    | I dati sul disco del sistema operativo vengono eliminati, il provisioning del sistema operativo viene                                      |

## <a name="size-requirements"></a>Requisiti di dimensione

È possibile distribuire immagini di macchine virtuali e istanze fino alle dimensioni della cache della macchina virtuale. Ad esempio, le immagini standard di Windows Server dal marketplace sono circa 127 GiB, il che significa che è necessaria una dimensione della macchina virtuale con una cache superiore a 127 GiB. In questo caso, il [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) ha una dimensione della cache di 86 GiB, che non è abbastanza grande. Il Standard_DS3_v2 ha una dimensione della cache di 172 GiB, che è abbastanza grande. In questo caso, il Standard_DS3_v2 è la dimensione più piccola della serie DSv2 che è possibile utilizzare con questa immagine. Le immagini Linux di base nelle immagini Marketplace `[smallsize]` e Windows Server, indicate da, tendono a essere di circa 30 GiB e possono utilizzare la maggior parte delle dimensioni delle macchine virtuali disponibili.

I dischi effimeri richiedono inoltre che la dimensione della macchina virtuale supporti l'archiviazione Premium.Ephemeral disks also require that the VM size supports Premium storage. Le dimensioni di solito (ma `s` non sempre) hanno un nel nome, come DSv2 ed EsV3. Per altre informazioni, vedere [Dimensioni delle macchine virtuali](../articles/virtual-machines/linux/sizes.md) di Azure per informazioni dettagliate sulle dimensioni che supportano l'archiviazione Premium.For more information, see Azure VM sizes for details around which sizes support Premium storage.

## <a name="powershell"></a>PowerShell

Per usare un disco effimero per una distribuzione di vm di PowerShell, usare [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) nella configurazione della macchina virtuale. Impostare `-DiffDiskSetting` `Local` l'impostazione di su e `-Caching` su `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Per le distribuzioni di set di scalabilità, usare il cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) nella configurazione. Impostare `-DiffDiskSetting` `Local` l'impostazione di su e `-Caching` su `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Per utilizzare un disco effimero per una `--ephemeral-os-disk` distribuzione di macchine `true` virtuali `--os-disk-caching` CLI, impostare il parametro in az [vm create](/cli/azure/vm#az-vm-create) to e il parametro su `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Per i set di `--ephemeral-os-disk true` scalabilità, si utilizza lo stesso `--os-disk-caching` parametro `ReadOnly`per [az-vmss-create](/cli/azure/vmss#az-vmss-create) e si imposta il parametro su .

## <a name="portal"></a>Portale   

Nel portale di Azure è possibile scegliere di usare dischi effimeri durante la distribuzione di una macchina virtuale aprendo la sezione **Avanzate** della scheda **Dischi.** Per **Usa disco oS effimero** selezionare **Sì**.

![Screenshot che mostra il pulsante di opzione per scegliere l'utilizzo di un disco del sistema operativo effimero](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se l'opzione per l'utilizzo di un disco effimero è disattivata, è possibile che sia stata selezionata una dimensione della macchina virtuale che non ha una dimensione della cache maggiore dell'immagine del sistema operativo o che non supporta l'archiviazione Premium.If the option for using an ephemeral disk is greyed out, you might have selected a VM size that does not have a cache size larger than the OS image or that doesn't support Premium storage. Tornare alla pagina **Nozioni di base** e provare a scegliere un'altra dimensione della macchina virtuale.

È inoltre possibile creare set di scalabilità con dischi del sistema operativo effimeri usando il portale. È sufficiente assicurarsi di selezionare una dimensione di macchina virtuale con una dimensione della cache sufficientemente grande e quindi in Usa disco del sistema **operativo effimero** selezionare **Sì**.

![Screenshot che mostra il pulsante di opzione per scegliere di utilizzare un disco del sistema operativo effimero per il set di scalabilità](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Distribuzione del modello del set di scalabilitàScale set template deployment  
Il processo per creare un set di scalabilità che usa un `diffDiskSettings` disco `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` del sistema operativo effimero consiste nell'aggiungere la proprietà al tipo di risorsa nel modello. Inoltre, il criterio di memorizzazione nella cache deve essere impostato `ReadOnly` su per il disco del sistema operativo effimero. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Distribuzione del modello di macchina virtuale 
È possibile distribuire una macchina virtuale con un disco del sistema operativo effimero usando un modello. Il processo per creare una macchina virtuale che usa dischi `diffDiskSettings` del sistema operativo effimeri consiste nell'aggiungere la proprietà al tipo di risorsa Microsoft.Compute/virtualMachines nel modello. Inoltre, il criterio di memorizzazione nella cache deve essere impostato `ReadOnly` su per il disco del sistema operativo effimero. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Riutilizzare un'immagine di una macchina virtuale usando RESTReimage a VM using REST
È possibile ricreare l'immagine di un'istanza di macchina virtuale con disco del sistema operativo effimero usando l'API REST come descritto di seguito e tramite il portale di Azure accedendo al riquadro Panoramica della macchina virtuale. Per i set di scalabilità, la ricreazione dell'immagine è già disponibile tramite Powershell, l'interfaccia della riga di comando e il portale.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Qual è la dimensione dei dischi del sistema operativo locale?**

R: La piattaforma e le immagini personalizzate sono supportate, fino alle dimensioni della cache della macchina virtuale, in cui tutte le operazioni di lettura/scrittura sul disco del sistema operativo saranno locali nello stesso nodo della macchina virtuale. 

**D: È possibile ridimensionare il disco oS effimero?**

R: No, una volta eseguito il provisioning del disco del sistema operativo effimero, il disco del sistema operativo non può essere ridimensionato. 

**D: È possibile collegare un disco gestito a una macchina virtuale effimera?**

R: Sì, è possibile collegare un disco dati gestito a una macchina virtuale che usa un disco del sistema operativo effimero. 

**D: Tutte le dimensioni delle macchine virtuali saranno supportate per i dischi del sistema operativo effimero?**

R: No, sono supportate tutte le dimensioni di macchine virtuali di Archiviazione Premium (DS, ES, FS, GS e M), ad eccezione delle dimensioni serie B, serie N e serie H.  
 
**D: Il disco del sistema operativo effimero può essere applicato alle macchine virtuali e ai set di scalabilità esistenti?**

R: No, il disco oS effimero può essere utilizzato solo durante la creazione di macchine virtuali e set di scalabilità. 

**D: È possibile combinare dischi del sistema operativo effimeri e normali in un set di scalabilità?**

R: No, non è possibile avere una combinazione di istanze del disco del sistema operativo effimere e persistenti all'interno dello stesso set di scalabilità. 

**D: È possibile creare il disco del sistema operativo effimero utilizzando Powershell o l'interfaccia della riga di comando?**

R: Sì, è possibile creare macchine virtuali con il disco del sistema operativo effimero usando REST, modelli, PowerShell e CLI.

**D: Quali funzionalità non sono supportate con il disco oS effimero?**

R: I dischi effimeri non supportano:
- Acquisizione di immagini VM
- Snapshot dei dischi 
- Azure Disk Encryption 
- Backup di Azure
- Azure Site Recovery  
- Scambio disco del sistema operativo 
