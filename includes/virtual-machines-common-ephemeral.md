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
ms.openlocfilehash: 0e59a28ce1fb3c562bf76420a5e62e347230e964
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669701"
---
I dischi del sistema operativo temporaneo vengono creati nell'archivio della macchina virtuale (VM) locale e non vengono salvati nell'archiviazione di Azure remota. I dischi del sistema operativo temporaneo funzionano bene per i carichi di lavoro senza stato, in cui le applicazioni sono a tolleranza di singoli errori delle macchine virtuali, ma sono più interessate dal tempo di distribuzione delle VM o dalla ricreazione dell'immagine delle singole istanze Con il disco del sistema operativo temporaneo si ottiene una latenza di lettura/scrittura più bassa per il disco del sistema operativo e una ricreazione più veloce della macchina virtuale 
 
Le funzionalità principali dei dischi temporanei sono: 
- Ideale per le applicazioni senza stato.
- Possono essere usati con le immagini del Marketplace e personalizzate.
- Possibilità di reimpostare rapidamente o ricreare l'immagine delle macchine virtuali e delle istanze del set di scalabilità sullo stato di avvio originale.  
- Latenza inferiore, simile a un disco temporaneo. 
- I dischi del sistema operativo temporaneo sono gratuiti e non vengono addebitati costi di archiviazione per il disco del sistema operativo.
- Sono disponibili in tutte le aree di Azure. 
- Il disco del sistema operativo temporaneo è supportato dalla [raccolta immagini condivise](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Differenze principali tra dischi del sistema operativo permanenti e temporanei:

|                             | Disco del sistema operativo permanente                          | Disco del sistema operativo temporaneo                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite di dimensioni per il disco del sistema operativo      | 2 TiB                                                                                        | Dimensioni della cache per le dimensioni della macchina virtuale o 2TiB, a seconda del numero minore. Per le **dimensioni della cache in Gib**, vedere [DS](../articles/virtual-machines/linux/sizes-general.md), [es](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Dimensioni delle macchine virtuali supportate          | Tutti                                                                                          | DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| Supporto del tipo di disco           | Disco del sistema operativo gestito e non gestito                                                                | Solo disco del sistema operativo gestito                                                               |
| Supporto di area              | Tutte le aree                                                                                  | Tutte le aree                              |
| Persistenza dei dati            | I dati del disco del sistema operativo scritti nel disco del sistema operativo vengono archiviati in archiviazione di Azure                                  | I dati scritti nel disco del sistema operativo vengono archiviati nella risorsa di archiviazione locale della macchina virtuale e non vengono salvati in modo permanente in archiviazione di Azure. |
| Arresta-stato deallocato      | Le macchine virtuali e le istanze del set di scalabilità possono essere arrestate e riavviate dallo stato stop-deallocato | Non è possibile arrestare la deallocazione delle VM e delle istanze del set di scalabilità                                  |
| Supporto del disco del sistema operativo specializzato | Yes                                                                                          | No                                                                                 |
| Ridimensionamento del disco del sistema operativo              | Supportato durante la creazione della macchina virtuale e dopo la deallocazione della macchina virtuale                                | Supportato solo durante la creazione della macchina virtuale                                                  |
| Ridimensionamento in una nuova dimensione della macchina virtuale   | I dati del disco del sistema operativo vengono conservati                                                                    | I dati nel disco del sistema operativo vengono eliminati, viene effettuato di nuovo il provisioning del sistema operativo                                      |

## <a name="size-requirements"></a>Requisiti di dimensione

È possibile distribuire le immagini di macchine virtuali e istanze fino alla dimensione della cache VM. Ad esempio, le immagini standard di Windows Server del Marketplace sono circa 127 GiB, il che significa che è necessario disporre di una dimensione della macchina virtuale con una cache maggiore di 127 GiB. In questo caso, il [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) ha una dimensione della cache di 86 GIB, che non è sufficientemente grande. Il Standard_DS3_v2 ha una dimensione della cache di 172 GiB, che è sufficientemente grande. In questo caso, Standard_DS3_v2 è la dimensione più piccola nella serie DSv2 che è possibile usare con questa immagine. Le immagini Linux di base nel Marketplace e le immagini di Windows Server che sono indicate `[smallsize]` da tendono a essere circa 30 GiB e possono usare la maggior parte delle dimensioni disponibili per le macchine virtuali.

I dischi temporanei richiedono anche che le dimensioni della VM supportino l'archiviazione Premium. Le dimensioni in genere (ma non sempre) hanno `s` un nel nome, ad esempio DSv2 e EsV3. Per altre informazioni, vedere [dimensioni delle VM di Azure](../articles/virtual-machines/linux/sizes.md) per informazioni dettagliate su quali dimensioni supportano archiviazione Premium.

## <a name="powershell"></a>PowerShell

Per usare un disco temporaneo per una distribuzione di VM PowerShell, usare [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) nella configurazione della macchina virtuale. `-DiffDiskSetting` Impostare su `Local` e su`-Caching` . `ReadOnly`     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Per le distribuzioni di set di scalabilità, usare il cmdlet [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) nella configurazione. `-DiffDiskSetting` Impostare su `Local` e su`-Caching` . `ReadOnly`


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Interfaccia della riga di comando

Per usare un disco temporaneo per una distribuzione di VM CLI, impostare il `--ephemeral-os-disk` parametro in [AZ VM create](/cli/azure/vm#az-vm-create) su `true` e il `--os-disk-caching` parametro su `ReadOnly`.

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

Per i set di scalabilità si usa `--ephemeral-os-disk true` lo stesso parametro per [AZ-vmss-create](/cli/azure/vmss#az-vmss-create) e `--os-disk-caching` si imposta `ReadOnly`il parametro su.

## <a name="portal"></a>Portale   

Nella portale di Azure è possibile scegliere di usare dischi temporanei quando si distribuisce una macchina virtuale aprendo la sezione **Avanzate** della scheda **dischi** . Per **Usa disco del sistema operativo temporaneo** selezionare **Sì**.

![Screenshot che mostra il pulsante di opzione per scegliere di usare un disco del sistema operativo temporaneo](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se l'opzione per l'uso di un disco temporaneo è disattivata, è possibile che sia stata selezionata una dimensione della macchina virtuale che non dispone di una dimensione della cache superiore a quella dell'immagine del sistema operativo o che non supporta l'archiviazione Premium. Tornare alla pagina Nozioni di **base** e provare a scegliere le altre dimensioni della macchina virtuale.

È anche possibile creare set di scalabilità con dischi del sistema operativo temporanei usando il portale. Assicurarsi di selezionare una dimensione di macchina virtuale con dimensioni della cache sufficienti e quindi in **Usa disco del sistema operativo temporaneo** selezionare **Sì**.

![Screenshot che mostra il pulsante di opzione per scegliere di usare un disco del sistema operativo temporaneo per il set di scalabilità](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Distribuzione del modello del set di scalabilità  
Il processo per creare un set di scalabilità che usa un disco del sistema operativo temporaneo consiste `diffDiskSettings` nell'aggiungere la `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` proprietà al tipo di risorsa nel modello. Inoltre, i criteri di memorizzazione nella cache devono essere `ReadOnly` impostati su per il disco del sistema operativo temporaneo. 


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

## <a name="vm-template-deployment"></a>Distribuzione modello di macchina virtuale 
È possibile distribuire una macchina virtuale con un disco del sistema operativo temporaneo usando un modello. Il processo di creazione di una VM che usa dischi del sistema operativo temporanei consiste `diffDiskSettings` nell'aggiungere la proprietà al tipo di risorsa Microsoft. Compute/virtualMachines nel modello. Inoltre, i criteri di memorizzazione nella cache devono essere `ReadOnly` impostati su per il disco del sistema operativo temporaneo. 

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


## <a name="reimage-a-vm-using-rest"></a>Ricreare l'immagine di una VM con REST
Attualmente, l'unico metodo per ricreare l'immagine di un'istanza di macchina virtuale con un disco del sistema operativo temporaneo consiste nell'usare l'API REST. Per i set di scalabilità la ricreazione dell'immagine è già disponibile tramite PowerShell, l'interfaccia della riga di comando e il portale.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Qual è la dimensione dei dischi del sistema operativo locale?**

R: Sono supportate le piattaforme e le immagini personalizzate, fino alla dimensione della cache della VM, in cui tutte le operazioni di lettura/scrittura nel disco del sistema operativo saranno locali nello stesso nodo della macchina virtuale. 

**D: È possibile ridimensionare il disco del sistema operativo temporaneo?**

R: No, una volta eseguito il provisioning del disco del sistema operativo temporaneo, il disco del sistema operativo non può essere ridimensionato. 

**D: È possibile aggiungere una Managed Disks a una VM temporanea?**

R: Sì, è possibile aggiungere un disco dati gestito a una macchina virtuale che usa un disco del sistema operativo temporaneo. 

**D: Tutte le dimensioni delle macchine virtuali saranno supportate per i dischi del sistema operativo temporaneo?**

R: No, sono supportate tutte le dimensioni delle macchine virtuali di archiviazione Premium (DS, ES, FS, GS e M) ad eccezione delle dimensioni serie B, serie N e H.  
 
**D: È possibile applicare il disco del sistema operativo temporaneo alle macchine virtuali esistenti e ai set di scalabilità?**

R: No, il disco del sistema operativo temporaneo può essere usato solo durante la creazione della macchina virtuale e del set di scalabilità. 

**D: È possibile combinare dischi del sistema operativo temporanei e normali in un set di scalabilità?**

R: No, non è possibile avere una combinazione di istanze del disco del sistema operativo effimere e persistenti nello stesso set di scalabilità. 

**D: È possibile creare il disco del sistema operativo temporaneo usando PowerShell o l'interfaccia della riga di comando?**

R: Sì, è possibile creare macchine virtuali con disco del sistema operativo temporaneo usando REST, modelli, PowerShell e l'interfaccia della riga di comando.

**D: Quali funzionalità non sono supportate con il disco del sistema operativo temporaneo?**

R: I dischi temporanei non supportano:
- Acquisizione di immagini di VM
- Snapshot dei dischi 
- Crittografia dischi di Azure 
- Backup di Azure
- Azure Site Recovery  
- Scambio del disco del sistema operativo 
