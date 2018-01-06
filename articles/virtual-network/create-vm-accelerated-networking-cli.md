---
title: Creare una macchina virtuale di Azure con rete accelerata | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Linux con accelerazione di rete.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 908d81c363a556917d211e0bcc92188f849fb690
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Creare una macchina virtuale Linux con accelerazione di rete

> [!IMPORTANT] 
> Con accelerazione di rete abilitata, è necessario creare macchine virtuali. Questa funzionalità non può essere abilitata nelle macchine virtuali esistenti. È possibile seguire la procedura seguente per abilitare l'accelerazione di rete:
>   1. Eliminare la macchina virtuale.
>   2. Ricreare la macchina virtuale con accelerazione di rete abilitato.
>

In questa esercitazione imparare a creare una macchina virtuale Linux (VM) con accelerazione di rete. La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso ad alte prestazioni consente di ignorare l'host dal relativo al percorso dati, riducendo l'utilizzo della CPU, per l'utilizzo con i carichi di lavoro rete più complesse in tipi di macchine Virtuali supportati, instabilità e latenza. La figura seguente illustra la comunicazione tra due macchine virtuali con e senza la configurazione di rete:

![Confronto](./media/create-vm-accelerated-networking/accelerated-networking.png)

Senza rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla VM deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per altre informazioni sui commutatori virtuali, vedere l'articolo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualizzazione rete Hyper-V e commutatore virtuale).

Con rete accelerata, il traffico di rete raggiunge la scheda di interfaccia di rete della VM e quindi viene inoltrato alla VM. Tutti i criteri di rete che si applica il commutatore virtuale sono ora scaricati e applicati in hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità rete accelerata si applicano solo alla VM in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due VM connesse alla stessa Rete virtuale di Azure. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

## <a name="benefits"></a>Vantaggi
* **Latenza più bassa e più pacchetti al secondo (pps):** rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
* **Instabilità ridotta:** l'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
* **Utilizzo ridotto della CPU:** ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
* **Ubuntu 16.04**: 4.11.0-1013 o versione successiva di kernel
* **SLES SP3**: 4.4.92-6.18 o versione successiva di kernel
* **RHEL**: 7.4.2017120423 o versione successiva di kernel
* **CentOS**: 7.4.20171206 o versione successiva di kernel

## <a name="supported-vm-instances"></a>Istanze di macchina virtuale supportate
Rete accelerata è supportata in più generale e dimensioni di istanze di calcolo ottimizzata con Vcpu 4 o più. Sulle istanze, ad esempio D/DSv3 o/ESv3 E che supportano l'Hyper-Threading, accelerazione di rete è supportata nelle istanze di macchina virtuale con più di 8 Vcpu.  Sono supportate serie: D/DSv2, D/DSv3, E/ESv3, ADFS/F/Fsv2 e Ms/Mms. 

Per ulteriori informazioni sulle istanze di macchina virtuale, vedere [le dimensioni di VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regioni
Disponibile in tutte le aree di Azure pubbliche fatta eccezione per Asia orientale.   Cloud di Azure per enti pubblici non è ancora supportata.

## <a name="limitations"></a>Limitazioni
L'uso della funzionalità Rete accelerata presenta le limitazioni seguenti:

* **Creazione di un'interfaccia di rete**: la funzionalità rete accelerata può essere abilitata solo per una nuova scheda di interfaccia di rete. Non può essere abilitata per una scheda di interfaccia di rete esistente.
* **Creazione di una VM**: una scheda di interfaccia di rete con rete accelerata abilitata può essere collegata a una VM solo durante la creazione della VM. Non è possibile collegare la scheda di interfaccia di rete a una VM esistente. Se l'aggiunta della macchina virtuale a un gruppo di disponibilità è impostata, tutte le macchine virtuali nel set di disponibilità devono inoltre accelerato networking abilitato.
* **Distribuzione tramite Gestione risorse di Azure:** macchine virtuali (classico) non possono essere distribuiti con accelerazione di rete.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure tramite il comando [az login](/cli/azure/#login). Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio inclusi *myResourceGroup*, *myNic*, e *myVm*.

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nel *centralus* percorso:

```azurecli
az group create --name myResourceGroup --location centralus
```

È necessario selezionare un'area supportata di Linux nella [Linux accelerated rete](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Creare una rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente crea una rete virtuale denominata *myVnet* con una subnet:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete
Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg#create). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Il gruppo di sicurezza di rete contiene regole predefinite di diversi, uno dei quali Disabilita tutti gli accessi in ingresso da Internet. Aprire una porta per consentire l'accesso SSH per la macchina virtuale con [creare una regola gruppo rete az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

## <a name="create-a-network-interface-with-accelerated-networking"></a>Creare un'interfaccia di rete con rete accelerata

Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Se non si prevede di accedere alla macchina virtuale da Internet, ma per completare i passaggi in questo articolo non è necessario un indirizzo IP pubblico, è necessario.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Creare un'interfaccia di rete con [az rete nic creare](/cli/azure/network/nic#create) con networking accelerato abilitato. L'esempio seguente crea un'interfaccia di rete denominata *myNic* nel *mySubnet* subnet del *myVnet* rete virtuale e lo associa il  *myNetworkSecurityGroup* gruppo di sicurezza di rete all'interfaccia di rete:

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

## <a name="create-a-vm-and-attach-the-nic"></a>Creare una macchina virtuale e collegare la scheda di rete
Quando si crea la macchina virtuale, specificare la scheda di rete creata con `--nics`. È necessario selezionare una dimensione e di distribuzione elencati in [Linux accelerated rete](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Creare una VM con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una macchina virtuale denominata *myVM* con l'immagine di UbuntuLTS e una dimensione che supporta l'accelerazione di rete (*Standard_DS4_v2*):

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

Per un elenco di tutte le dimensioni delle macchine Virtuali e di caratteristiche, vedere [le dimensioni di VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Dopo aver creata la macchina virtuale, viene restituito l'output simile al seguente esempio di output. Annotare il **publicIpAddress**. Questo indirizzo viene utilizzato per accedere alla macchina virtuale nei passaggi successivi.

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

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Verificare che la rete sia abilitata

Usare il comando seguente per creare una sessione SSH con la VM. Sostituire `<your-public-ip-address>` con l'indirizzo IP pubblico assegnato a virtual machine creato e sostituire *azureuser* se è stato utilizzato un valore diverso per `--admin-username` al momento della creazione della macchina virtuale.

```bash
ssh azureuser@<your-public-ip-address>
```

Dalla shell Bash, immettere `uname -r` e verificare che la versione del kernel è una delle seguenti versioni, o versione successiva:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Verificare che il dispositivo VF Mellanox viene esposto per la macchina virtuale con il `lspci` comando. L'output restituito è simile al seguente output:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Controllo dell'attività su VF (virtual function) con il `ethtool -S eth0 | grep vf_` comando. Se si riceve l'output simile all'esempio seguente l'output, con accelerazione di rete è abilitata e l'utilizzo.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
La funzionalità di rete accelerata è ora abilitata per la VM.
