---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 47407df90a83501b8739a428789e20cddc59e83d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145932"
---
I dischi del sistema operativo temporanei vengono creati nello spazio di archiviazione macchina virtuale (VM) locali e non persistenti nella risorsa di archiviazione di Azure remoto. I dischi del sistema operativo temporanei funzionano anche per carichi di lavoro senza stati, in cui le applicazioni sono a tolleranza di singoli errori di macchina virtuale, ma sono più interessati a tempo per ricreare l'immagine di singole istanze di VM o il tempo che necessario per le distribuzioni su larga scala. È anche adatto per le applicazioni, distribuite usando il modello di distribuzione classica, spostare il modello di distribuzione Resource Manager. Il disco del sistema operativo temporaneo offre latenza minore per operazioni di lettura/scrittura nel disco del sistema operativo e una ricreazione più rapida delle immagini delle VM. Inoltre, il disco del sistema operativo temporaneo è gratuito, si dovranno sostenere alcun costi di archiviazione per il disco del sistema operativo. 
 
Le funzionalità principali di dischi temporanei sono: 
- Possono essere utilizzati con le immagini del Marketplace e le immagini personalizzate.
- È possibile distribuire immagini di macchina virtuale fino alla dimensione della Cache della macchina virtuale.
- Possibilità di reimpostare o ricrearne l'immagine le proprie macchine virtuali per lo stato originale di avvio rapido.  
- Latenza più bassa in fase di esecuzione simile a un disco temporaneo. 
- Nessun costo per il disco del sistema operativo. 
 
 
Differenze principali tra i dischi del sistema operativo permanenti e temporanee:

|                             | Disco del sistema operativo persistente                          | Disco del sistema operativo temporaneo                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Dimensione massima per disco del sistema operativo      | 2 TiB                                                                                        | Memorizzare nella cache di dimensione per la dimensione di VM o 2TiB, qualunque sia il minore - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), e [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Dimensioni delle macchine virtuali supportate          | Tutti                                                                                          | DSv1, DSv2, DSv3, Esv3, ADFS, FsV2, GS, M                                               |
| Supporto dei tipi di disco           | Disco del sistema operativo gestito e non gestito                                                                | Solo disco del sistema operativo gestito                                                               |
| Supporto di area              | Tutte le aree                                                                                  | Tutte le aree                              |
| Persistenza dei dati            | Dati scritti su disco del sistema operativo del disco del sistema operativo vengono archiviati in archiviazione di Azure                                  | I dati scritti su disco del sistema operativo viene archiviati nella risorsa di archiviazione della macchina virtuale locale e non sono persistenti in archiviazione di Azure. |
| Stato di arresto-deallocazione      | Le macchine virtuali e istanze del set di scalabilità possono essere arrestata-deallocata e riavviate dallo stato di arresto-deallocazione | Le macchine virtuali e istanze del set di scalabilità non possono essere arrestata-deallocata                                  |
| Supporto di dischi del sistema operativo specializzato | Sì                                                                                          | N.                                                                                 |
| Ridimensionamento del disco del sistema operativo              | Durante la creazione della macchina virtuale e al termine della macchina virtuale di arresto-deallocazione supportati                                | È supportato durante la creazione di una VM solo                                                  |
| Ridimensionamento di una nuova dimensione di macchina virtuale   | I dati su disco del sistema operativo viene mantenuti                                                                    | I dati sul disco del sistema operativo sono stati eliminati, sistema operativo è di nuovo effettuato il provisioning                                      |

## <a name="register-for-the-preview"></a>Registrarsi per l'anteprima


Eseguire la registrazione automatica per l'anteprima di dischi del sistema operativo temporanei utilizzando la versione più recente di Azure o Azure PowerShell.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature –FeatureName LocalDiffDiskPreview -ProviderNamespace Microsoft.Compute
```

Per controllare se sono registrati per l'anteprima:

```azurepowershell-interactive
Get-AzProviderFeature –FeatureName LocalDiffDiskPreview -ProviderNamespace Microsoft.Compute
```

### <a name="cli"></a>Interfaccia della riga di comando

```azurecli-interactive
az provider register --namespace Microsoft.Compute
az feature register --namespace Microsoft.Compute --name LocalDiffDiskPreview
```

Per controllare se sono registrati per l'anteprima:
 
```azurecli-interactive
az provider show --namespace Microsoft.Compute
```


## <a name="scale-set-deployment"></a>Distribuzione set di scalabilità  
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

## <a name="vm-deployment"></a>Distribuzione di macchine Virtuali 
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

R: Per l'anteprima, il supporto di piattaforma e/o le immagini fino alla dimensione della cache della macchina virtuale, in cui verranno eseguiti localmente nello stesso nodo della macchina virtuale tutte lettura/scrittura al disco del sistema operativo. 

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
- Crittografia dischi di Azure 
- Backup di Azure
- Azure Site Recovery  
- Scambio di dischi del sistema operativo 
