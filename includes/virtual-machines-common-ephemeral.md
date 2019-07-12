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
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805882"
---
I dischi del sistema operativo temporanei vengono creati nello spazio di archiviazione macchina virtuale locale (VM) e non salvati all'archiviazione di Azure remoto. I dischi del sistema operativo temporanei funzionano anche per carichi di lavoro senza stati, in cui le applicazioni sono a tolleranza di singoli errori di macchina virtuale, ma sono più interessati dalla fase di distribuzione della macchina virtuale o la ricreazione dell'immagine di singole istanze di macchina virtuale. Con disco del sistema operativo temporaneo, si otterrà una latenza più bassa di lettura/scrittura per il disco del sistema operativo e più veloci ricreazione dell'immagine della macchina virtuale. 
 
Le funzionalità principali di dischi temporanei sono: 
- Ideale per le applicazioni senza stato.
- Possono essere utilizzati con Marketplace e le immagini personalizzate.
- Istanze del set di capacità di ripristino rapido o macchine virtuali di ricreazione dell'immagine e scalabilità per lo stato di avvio originale.  
- Latenza più bassa, simile a un disco temporaneo. 
- I dischi del sistema operativo temporanei sono gratuiti, si dovranno sostenere alcun costi di archiviazione per il disco del sistema operativo.
- Sono disponibili in tutte le aree di Azure. 
- Il disco del sistema operativo temporaneo è supportato dal [raccolta di immagini condivise](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Differenze principali tra i dischi del sistema operativo permanenti e temporanee:

|                             | Disco del sistema operativo persistente                          | Disco del sistema operativo temporaneo                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Dimensione massima per disco del sistema operativo      | 2 TiB                                                                                        | Memorizzare nella cache di dimensione per la dimensione di VM o 2TiB, qualunque sia il minore. Per il **memorizza nella cache di dimensioni in GiB**, vedere [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Dimensioni delle macchine virtuali supportate          | Tutti                                                                                          | DSv1, DSv2, DSv3, Esv3, ADFS, FsV2, GS, M                                               |
| Supporto dei tipi di disco           | Disco del sistema operativo gestito e non gestito                                                                | Solo disco del sistema operativo gestito                                                               |
| Supporto di area              | Tutte le aree                                                                                  | Tutte le aree                              |
| Persistenza dei dati            | Dati scritti su disco del sistema operativo del disco del sistema operativo vengono archiviati in archiviazione di Azure                                  | I dati scritti su disco del sistema operativo viene archiviati nella risorsa di archiviazione della macchina virtuale locale e non sono persistenti in archiviazione di Azure. |
| Stato di arresto-deallocazione      | Le macchine virtuali e istanze del set di scalabilità possono essere arrestata-deallocata e riavviate dallo stato di arresto-deallocazione | Le macchine virtuali e istanze del set di scalabilità non possono essere arrestata-deallocata                                  |
| Supporto di dischi del sistema operativo specializzato | Sì                                                                                          | No                                                                                 |
| Ridimensionamento del disco del sistema operativo              | Durante la creazione della macchina virtuale e al termine della macchina virtuale di arresto-deallocazione supportati                                | È supportato durante la creazione di una VM solo                                                  |
| Ridimensionamento di una nuova dimensione di macchina virtuale   | I dati su disco del sistema operativo viene mantenuti                                                                    | I dati sul disco del sistema operativo sono stati eliminati, sistema operativo è di nuovo effettuato il provisioning                                      |

## <a name="size-requirements"></a>Requisiti di dimensione

È possibile distribuire le immagini di macchina virtuale e istanza fino alla dimensione della cache della macchina virtuale. Ad esempio, le immagini di Windows Server Standard da marketplace sono GiB circa 127, che significa che è necessario una dimensione di macchina virtuale che dispone di una cache maggiore di 127 GiB. In questo caso, il [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) ha una dimensione della cache di 86 GiB, di cui non è sufficientemente grande. Il Standard_DS2_v2 ha una dimensione della cache di 172 GiB, che è abbastanza grande. In questo caso, il Standard_DS3_v2 corrisponde alle dimensioni minime della serie DSv2 che è possibile usare con questa immagine. Le immagini di Linux nelle immagini del Marketplace e Windows Server che sono contrassegnate da `[smallsize]` tendono a essere di circa 30 GiB e possono usare la maggior parte delle dimensioni di VM disponibili.

I dischi temporanei richiedono inoltre che le dimensioni VM supportino archiviazione Premium. Le dimensioni in genere (ma non sempre) hanno un `s` nel nome, ad esempio DSv2 ed EsV3. Per altre informazioni, vedere [dimensioni delle macchine Virtuali di Azure](../articles/virtual-machines/linux/sizes.md) per informazioni dettagliate intorno al quale le dimensioni supportano archiviazione Premium.

## <a name="powershell"></a>PowerShell

Per usare un disco temporaneo per una distribuzione di VM di PowerShell, usare [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) nella configurazione della macchina virtuale. Impostare il `-DiffDiskSetting` al `Local` e `-Caching` a `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Per le distribuzioni di set di scalabilità, usare il [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet nella configurazione. Impostare il `-DiffDiskSetting` al `Local` e `-Caching` a `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Per usare un disco temporaneo per una distribuzione di VM CLI, impostare il `--ephemeral-os-disk` parametro nel [az vm create](/cli/azure/vm#az-vm-create) al `true` e il `--os-disk-caching` parametro `ReadOnly`.

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

Per i set di scalabilità, usare la stessa `--ephemeral-os-disk true` parametro per [az vmss create](/cli/azure/vmss#az-vmss-create) e impostare il `--os-disk-caching` parametro `ReadOnly`.

## <a name="portal"></a>Portale   

Nel portale di Azure, è possibile scegliere di usare i dischi temporanei quando si distribuisce una macchina virtuale, aprire il **avanzate** sezione il **dischi** scheda. Per la **il disco del sistema operativo temporaneo di utilizzo** selezionate **Yes**.

![Screenshot che mostra il pulsante di opzione per la scelta di usare un disco del sistema operativo temporaneo](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se l'opzione per l'uso di dischi temporanei viene disattivata, si potrebbe essere stata selezionata una dimensione di VM che non dispongono di una dimensione della cache più grande dell'immagine del sistema operativo o che non supporta archiviazione Premium. Tornare al **nozioni di base** pagina e provare a scegliere un'altra dimensione di macchina virtuale.

Inoltre, è possibile creare set di scalabilità con dischi del sistema operativo temporanei usando il portale. Assicurarsi semplicemente di selezionare una dimensione di VM con una dimensione sufficientemente grande della cache e quindi in **il disco del sistema operativo temporaneo di utilizzo** selezionate **Yes**.

![Screenshot che mostra il pulsante di opzione per la scelta di usare un disco del sistema operativo temporaneo per il set di scalabilità](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Distribuzione del modello del set di scalabilità  
La procedura per creare un set di scalabilità che usa un disco del sistema operativo temporaneo consiste nell'aggiungere il `diffDiskSettings` proprietà per il `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` tipo di risorsa nel modello. Inoltre, i criteri di memorizzazione nella cache devono essere impostati `ReadOnly` per il disco del sistema operativo temporaneo. 


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

## <a name="vm-template-deployment"></a>Distribuzione dei modelli di macchina virtuale 
È possibile distribuire una macchina virtuale con dischi del sistema operativo temporanei usando un modello. La procedura per creare una VM che usa i dischi del sistema operativo temporanei consiste nell'aggiungere il `diffDiskSettings` proprietà al tipo di risorsa COMPUTE/virtualmachines nel modello. Inoltre, i criteri di memorizzazione nella cache devono essere impostati `ReadOnly` per il disco del sistema operativo temporaneo. 

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


## <a name="reimage-a-vm-using-rest"></a>Ricrea l'immagine di una macchina virtuale usando REST
Attualmente, l'unico metodo per ricreare l'immagine di un'istanza di macchina virtuale con disco del sistema operativo temporaneo è tramite l'API REST. Per i set di scalabilità, la ricreazione dell'immagine è già disponibile tramite Powershell, CLI e il portale.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Qual è la dimensione dei dischi del sistema operativo locale?**

R: Sono supportati le immagini personalizzate, fino alla dimensione della cache della macchina virtuale, in cui verranno eseguiti localmente nello stesso nodo della macchina virtuale tutte lettura/scrittura al disco del sistema operativo e piattaforma. 

**D: È possibile ridimensionare il disco del sistema operativo temporaneo?**

R: No, al termine del provisioning, il disco del sistema operativo temporaneo non può essere ridimensionato il disco del sistema operativo. 

**D: È possibile collegare un disco gestito a una macchina virtuale temporanea?**

R: Sì, è possibile collegare un disco dati gestito a una macchina virtuale che usa dischi del sistema operativo temporanei. 

**D: Tutte le dimensioni di macchina virtuale saranno supportate per i dischi del sistema operativo temporanei?**

R: No, tutte le dimensioni di VM di archiviazione Premium sono supportati (DS, ES, ADFS, GS e M), ad eccezione della serie B, N-series e le dimensioni della serie H.  
 
**D: Il disco del sistema operativo temporaneo è applicabile alle macchine virtuali esistenti e set di scalabilità?**

R: No, ephemeral disco del sistema operativo può essere utilizzato solo durante la macchina virtuale e la creazione del set di scalabilità. 

**D: Non è possibile combinare temporanei e normali dischi del sistema operativo in un set di scalabilità?**

R: No, è possibile avere una combinazione di istanze del disco del sistema operativo temporanee e permanenti all'interno del set di scalabilità stesso. 

**D: È il disco del sistema operativo temporaneo possibile creare tramite Powershell o CLI?**

R: Sì, è possibile creare macchine virtuali con disco del sistema operativo temporaneo usando REST, modelli, PowerShell e CLI.

**D: Quali funzionalità non supportate con temporanei del disco del sistema operativo?**

R: Non supportano i dischi temporanei:
- Acquisizione di immagini di macchina virtuale
- Snapshot dei dischi 
- Azure Disk Encryption 
- Backup di Azure
- Azure Site Recovery  
- Scambio di dischi del sistema operativo 
