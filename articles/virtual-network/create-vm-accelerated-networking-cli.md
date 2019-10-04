---
title: Creare una macchina virtuale di Azure con rete accelerata | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Linux con rete accelerata abilitata.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 1e5513b28c1ae64fc8c87bb7a949596feab4623e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873430"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Creare una macchina virtuale Linux con rete accelerata

Questa esercitazione spiega come creare una macchina virtuale (VM) Linux con Rete Accelerata. Per creare una macchina virtuale Windows con la funzionalità rete accelerata, vedere [Creare una macchina virtuale Windows con Rete accelerata](create-vm-accelerated-networking-powershell.md). La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate esclude l'host dal percorso dati, riducendo così la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più impegnativi nei tipi di VM supportati. L'immagine seguente illustra le comunicazioni tra due VM, con e senza rete accelerata:

![Confronto](./media/create-vm-accelerated-networking/accelerated-networking.png)

Senza rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla VM deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per altre informazioni sui commutatori virtuali, vedere l'articolo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualizzazione rete Hyper-V e commutatore virtuale).

Con rete accelerata, il traffico di rete raggiunge la scheda di interfaccia di rete della macchina virtuale e quindi viene inoltrato alla VM. Il carico di tutti i criteri di rete del commutatore virtuale viene ora ripartito e applicato all'hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità rete accelerata si applicano solo alla VM in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due VM connesse alla stessa rete virtuale di Azure. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

## <a name="benefits"></a>Vantaggi
* **Latenza più bassa/più pacchetti al secondo (pps):** Rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
* **Instabilità ridotta:** L'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
* **Utilizzo ridotto della CPU:** Ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le distribuzioni seguenti sono supportate in modo nativo dalla raccolta di Azure: 
* **Ubuntu 14.04 con il kernel linux di azure**
* **Ubuntu 16.04 o versione successiva** 
* **SLES12 SP3 o versione successiva** 
* **RHEL 7.4 o successive**
* **CentOS 7.4 o successive**
* **CoreOS Linux**
* **Debian "Stretch" con kernel backport**
* **Oracle Linux 7.4 e versioni successive con Red Hat compatibili del Kernel (RHCK)**
* **Oracle Linux 7.5 e versioni successive con UEK versione 5**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Limiti e vincoli

### <a name="supported-vm-instances"></a>Istanze di VM supportate
La funzionalità Rete accelerata è supportata nella maggior parte delle istanze di utilizzo generico e ottimizzate per il calcolo con 2 o più vCPU.  Queste serie supportate sono D/DSv2 e F/Fs

Nelle istanze che supportano l'hyperthreading, la Rete accelerata è supportata nelle istanze di macchine virtuali con 4 o più vCPU. Le serie supportate sono D/DSV3, e/esv3, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Per altre informazioni sulle istanze di VM, vedere [Dimensioni per le macchine virtuali Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regioni
Questa funzionalità è disponibile in tutte le aree di Azure pubbliche e nel cloud di Azure per enti pubblici.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Abilitazione della Rete accelerata in una macchina virtuale in esecuzione
In una dimensione della macchina virtuale supportata in cui la Rete accelerata non è abilitata, è possibile abilitare la funzionalità solo quando la macchina virtuale è arrestata e deallocata.  
### <a name="deployment-through-azure-resource-manager"></a>Distribuzione tramite Azure Resource Manager
Le macchine virtuali (classiche) non possono essere distribuite con la funzionalità Rete accelerata.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Creare una macchina virtuale Linux con Rete accelerata di Azure
## <a name="portal-creation"></a>Creazione nel portale
Questo articolo illustra la procedura per creare una macchina virtuale con rete accelerata tramite l'interfaccia della riga di comando di Azure, ma è anche possibile [creare una macchina virtuale con rete accelerata usando il portale di Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando si crea una macchina virtuale nel portale, nel **creare una macchina virtuale** blade, scegliere il **Networking** scheda.  In questa scheda è disponibile un'opzione per **rete accelerata**.  Se si è scelto un [sistema operativo supportato](#supported-operating-systems) e [dimensione di VM](#supported-vm-instances), questa opzione verrà popolato automaticamente su "Sì".  In caso contrario, verrà popolare l'opzione "Disattivato" per la rete accelerata e concedere all'utente un motivo per cui non è possibile abilitato.   

* *Nota:* Solo i sistemi operativi supportati può essere abilitati tramite il portale.  Se si usa un'immagine personalizzata e l'immagine supporta la funzionalità rete accelerata, creare la macchina virtuale usando Powershell o CLI. 

Dopo aver creata la macchina virtuale, è possibile verificare la rete accelerata è abilitata, seguire le istruzioni riportate nel [verificare che sia abilitata la funzionalità rete accelerata](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Creazione dell'interfaccia della riga
### <a name="create-a-virtual-network"></a>Crea rete virtuale

Installare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e accedere all'account di Azure con il comando [az login](/cli/azure/reference-index). Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myNic* e *myVM*.

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *centralus*.

```azurecli
az group create --name myResourceGroup --location centralus
```

Selezionare un'area di Linux supportata nella [rete accelerata Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet). Nell'esempio seguente viene creata una rete virtuale denominata *myVnet* con una subnet:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete
Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Il gruppo di sicurezza di rete contiene diverse regole predefinite, una delle quali disabilita tutti gli accessi in ingresso da Internet. Aprire una porta per consentire l'accesso SSH alla macchina virtuale con [az network nsg rule create](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Creare un'interfaccia di rete con rete accelerata

Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip). Un indirizzo IP pubblico non è necessario se non si prevede di accedere alla macchina virtuale da Internet, ma è indispensabile per completare i passaggi in questo articolo.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Creare un'interfaccia di rete con [az network nic create](/cli/azure/network/nic) con rete accelerata abilitata. L'esempio seguente crea un'interfaccia di rete denominata *myNic* nel subnet *mySubnet* della rete virtuale *myVnet* e associa il gruppo di sicurezza di rete *myNetworkSecurityGroup* all'interfaccia di rete:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Creare una macchina virtuale e collegare le schede di interfaccia di rete
Quando si crea la macchina virtuale, specificare la scheda di interfaccia di rete creata con `--nics`. Selezionare una dimensione e una distribuzione elencate nella [rete accelerata Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Creare una VM con il comando [az vm create](/cli/azure/vm). L'esempio seguente crea una VM denominata *myVM* con l'immagine di UbuntuLTS e una dimensione che supporta la Rete accelerata (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Per un elenco di tutte le dimensioni e le caratteristiche delle VM, vedere [Dimensioni per le macchine virtuali Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Dopo aver creato la macchina virtuale, viene restituito l'output simile al seguente output di esempio. Prendere nota di **publicIpAddress**. Questo indirizzo viene usato nei passaggi successivi per l'accesso alla macchina virtuale.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Verificare che la rete accelerata sia abilitata

Usare il comando seguente per creare una sessione SSH con la VM. Sostituire `<your-public-ip-address>` con l'indirizzo IP pubblico assegnato alla macchina virtuale creata e sostituire *azureuser* se è stato usato un valore diverso per `--admin-username` al momento della creazione della macchina virtuale.

```bash
ssh azureuser@<your-public-ip-address>
```

Dalla shell Bash, immettere `uname -r` e verificare che la versione del kernel sia una delle seguenti versioni, o una versione successiva:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Verificare che il dispositivo VF Mellanox sia esposto per la macchina virtuale con il comando `lspci`. L'output restituito è simile al seguente output:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Controllare l'attività sulla VF (funzione virtuale) con il comando`ethtool -S eth0 | grep vf_`. Se si riceve un output simile al seguente output di esempio, la rete accelerata è abilitata e in funzione.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
La funzionalità di rete accelerata è ora abilitata per la VM.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Gestire l'associazione dinamica e revoca di funzione virtuale 
Le applicazioni devono eseguire tramite l'interfaccia di rete sintetica esposto nella macchina virtuale. Se l'applicazione viene eseguito direttamente sull'interfaccia di rete VF, non può ricevere **tutti** che i pacchetti destinati alla macchina virtuale, perché alcuni pacchetti compaiono attraverso l'interfaccia sintetico.
Se si esegue un'applicazione tramite l'interfaccia di rete sintetica, garantisce che l'applicazione riceve **tutti** che i pacchetti destinati a esso. Rende anche assicurarsi che l'applicazione rimane in esecuzione, anche se VF viene revocato quando l'host viene servita. Le applicazioni all'interfaccia di rete sintetica di associazione è un **obbligatorio** requisito per tutte le applicazioni che sfruttano **funzionalità rete accelerata**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Abilitare la funzione Rete accelerata nelle macchine virtuali esistenti
Se si è creata una macchina virtuale senza Rete accelerata, è possibile abilitare questa funzionalità in una macchina virtuale esistente.  Per il supporto di Rete accelerata, la macchina virtuale deve soddisfare i prerequisiti seguenti, descritti anche in precedenza:

* La macchina virtuale deve essere di dimensioni supportate da Rete accelerata
* La macchina virtuale deve essere un'immagine della raccolta di Azure supportata (e con versione del kernel per Linux)
* Tutte le macchine virtuali in un set di disponibilità o set di scalabilità di macchine virtuali (VMSS) devono essere arrestate/deallocate prima di abilitare Rete accelerata su qualsiasi scheda di rete

### <a name="individual-vms--vms-in-an-availability-set"></a>Macchine virtuali singole e in un set di disponibilità
Prima di tutto, arrestare/deallocare la macchina virtuale oppure, nel caso di un set di disponibilità, tutte le macchine virtuali nel set:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Importante: se la macchina virtuale è stata creata singolarmente, senza un set di disponibilità, per abilitare Rete accelerata è sufficiente arrestare/deallocare la singola macchina virtuale.  Se la macchina virtuale è stata creata con un set di disponibilità, sarà necessario arrestare/deallocare tutte le macchine virtuali incluse nel set di disponibilità prima di abilitare la funzione Rete accelerata in una qualsiasi delle schede di interfaccia di rete. 

Dopo l'arresto, abilitare Rete accelerata nella scheda di rete della macchina virtuale:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Riavviare la macchina virtuale oppure, nel caso di un set di disponibilità, tutte le macchine virtuali nel set, quindi verificare che la funzionalità Rete accelerata sia abilitata: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
Il caso dei set di scalabilità di macchine virtuali è leggermente diverso, ma segue lo stesso flusso di lavoro.  In primo luogo, arrestare le macchine virtuali:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Dopo l'arresto delle macchine virtuali, aggiornare la funzione Rete accelerata nell'interfaccia di rete:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Si noti che, all'interno di un set di scalabilità di macchine virtuali, gli aggiornamenti delle macchine virtuali vengono applicati in base a tre diverse impostazioni, ossia automaticamente, in sequenza e manualmente.  In queste istruzioni i criteri sono impostati sull'aggiornamento automatico, in modo che il set di scalabilità di macchine virtuali rilevi le modifiche immediatamente dopo il riavvio.  Per impostare l'aggiornamento automatico in modo che le modifiche vengano rilevate immediatamente: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Infine, riavviare il set di scalabilità di macchine virtuali:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Dopo il riavvio, attendere che gli aggiornamenti vengano completati. Al termine, la VF verrà visualizzata all'interno della macchina virtuale.  Assicurarsi di usare una dimensione di VM e un sistema operativo supportati.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ridimensionamento di macchine virtuali esistenti con rete accelerata

Le macchine virtuali con la funzione Rete accelerata abilitata possono essere ridimensionate solo nelle macchine virtuali che supportano questa funzionalità.  

Non è possibile ridimensionare una macchina virtuale con Rete accelerata abilitata in un'istanza di macchina virtuale che non supporta questa funzionalità tramite l'operazione di ridimensionamento.  Per ridimensionare una macchina virtuale di questo tipo: 

* Arrestare/deallocare la macchina virtuale oppure, nel caso di un set di disponibilità/set di scalabilità di macchine virtuali, tutte le macchine virtuali nel set/set di scalabilità di macchine virtuali.
* La funzionalità Rete accelerata deve essere disabilitata nella scheda di rete della macchina virtuale o, se all'interno di un set di disponibilità/set di scalabilità di macchine virtuali, in tutte le macchine virtuali nel set/set di scalabilità di macchine virtuali.
* Dopo aver disabilitato Rete accelerata, è possibile convertire la macchina virtuale/il set di disponibilità/il set di scalabilità di macchine virtuali in una nuova dimensione che non supporta Rete accelerata e riavviare.  

