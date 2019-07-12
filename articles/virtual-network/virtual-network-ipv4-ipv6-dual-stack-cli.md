---
title: Distribuire un'applicazione dual-stack IPv6 nella rete virtuale di Azure - CLI
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuisce un'applicazione di dual-stack IPv6 nella rete virtuale di Azure tramite la CLI di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 9e591bdf2ff0b6493f092d666d02c2614c907700
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798971"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Distribuire un'applicazione dual-stack IPv6 nella rete virtuale di Azure - interfaccia della riga di comando (anteprima)

Questo articolo illustra come distribuire un'applicazione dual stack (IPv4 + IPv6) in Azure che include una rete virtuale con una subnet dual-stack, un servizio di bilanciamento del carico con configurazioni front-end dual (IPv4 + IPv6), le macchine virtuali con schede di rete con una configurazione IP doppia, dual-stack in regole di gruppo di sicurezza di rete Dual e due indirizzi IP pubblici.

> [!Important]
> Dual-stack di IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare in locale invece di comando di Azure, questa Guida introduttiva richiede di usare il comando di Azure versione 2.0.49 o versione successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti
Per l'utilizzo di IPv6 per la funzionalità di rete virtuale di Azure, è necessario configurare la sottoscrizione usando Azure PowerShell nel modo seguente:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. È possibile controllare lo stato della registrazione eseguendo il comando di Azure:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Al termine della registrazione eseguire questo comando:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di creare la rete virtuale dual stack, è necessario creare un gruppo di risorse [creare il gruppo di az](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myRGDualStack* nel *eastus* percorso:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Creare IPv4 e IPv6 indirizzi IP pubblici per il bilanciamento del carico
Per accedere agli endpoint IPv4 e IPv6 in Internet, assegnare indirizzi IP pubblici IPv4 e IPv6 per il bilanciamento del carico. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip). L'esempio seguente crea IPv4 e IPv6 indirizzo IP pubblico denominato *dsPublicIP_v4* e *dsPublicIP_v6* nel *myRGDualStack* gruppo di risorse:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Creare gli indirizzi IP pubblici per macchine virtuali

Per accedere in remoto le macchine virtuali su internet, gli indirizzi IP pubblici IPv4 è necessario per le macchine virtuali. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

In questa sezione viene configurato duale front-end IP (IPv4 e IPv6) e il pool di indirizzi back-end di bilanciamento del carico e quindi creare un Load Balancer Basic.

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare l'istanza di Load Balancer Basic con [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) denominate **dsLB** che include un pool front-end denominato **dsLbFrontEnd_v4**, un pool di back-end denominato  **dsLbBackEndPool_v4** che è associato l'indirizzo IP pubblico IPv4 **dsPublicIP_v4** creato nel passaggio precedente. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Crea front-end IPv6

Creare un indirizzo IP front-end IPV6 con [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). L'esempio seguente crea una configurazione IP front-end denominata *dsLbFrontEnd_v6* e collega le *dsPublicIP_v6* indirizzo:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurare il pool di indirizzi back-end IPv6

Creare pool con un indirizzo di back-end IPv6 [creare pool con az network lb address](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). L'esempio seguente crea pool di indirizzi back-end denominato *dsLbBackEndPool_v6* per includere le macchine virtuali con configurazioni di interfaccia di rete IPv6:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. 

Creare una regola di bilanciamento del carico con [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). L'esempio seguente crea regole di bilanciamento del carico denominate *dsLBrule_v4* e *dsLBrule_v6* e bilancia il traffico sul *TCP* porta *80* per le configurazioni di IP front-end IPv4 e IPv6:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Creare risorse di rete
Prima di distribuire alcune macchine virtuali, è necessario creare risorse di rete supporto - set di disponibilità, il gruppo di sicurezza di rete, reti virtuali e schede di rete virtuale. 
### <a name="create-an-availability-set"></a>Creare un set di disponibilità
Per migliorare la disponibilità dell'app, posizionare le macchine virtuali nel set di disponibilità.

Creare un set di disponibilità con [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). L'esempio seguente crea un set di disponibilità denominato *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che prevarranno le comunicazioni in ingresso e in uscita nella rete virtuale.

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Creare una regola di gruppo di sicurezza di rete per le connessioni in ingresso e in uscita

Creare una regola di gruppo di sicurezza di rete per consentire le connessioni RDP con connessione internet alla porta 3389, tramite la porta 80 e per le connessioni in uscita con [Azure network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). L'esempio seguente crea una rete virtuale denominata *dsVNET* con le subnet *dsSubNET_v4* e *dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Creare NIC

Creare schede di rete virtuale per ogni VM con [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). L'esempio seguente crea una scheda di rete virtuale per ogni macchina virtuale. Ogni interfaccia di rete ha due configurazioni IP (configurazione IPv4 1, 1 configurazione IPv6). Creare la configurazione di IPV6 con [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare le VM con [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già. 

Crea macchina virtuale *dsVM0* come indicato di seguito:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Crea macchina virtuale *dsVM1* come indicato di seguito:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Rete virtuale dual-stack IPv6 di visualizzazione nel portale di Azure
È possibile visualizzare la rete virtuale dual-stack IPv6 nel portale di Azure come indicato di seguito:
1. Nella barra di ricerca del portale, immettere *dsVnet*.
2. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca. Verrà avviata il **Overview** pagina della rete virtuale dual-stack denominata *dsVnet*. La rete virtuale dual-stack Mostra due schede di rete con IPv4 e IPv6 configurazioni che si trova nella subnet dual-stack denominata *dsSubnet*.

  ![Rete virtuale di dual-stack IPv6 in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> IPv6 per la rete virtuale di Azure è disponibile nel portale di Azure in sola lettura per questa versione di anteprima.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è creato un servizio Load Balancer Basic con una configurazione IP front-end dual (IPv4 e IPv6). È stato creato anche un due macchine virtuali incluse interfacce di rete con due configurazioni IP (IPV4 + IPv6) che sono stati aggiunti al pool back-end del servizio di bilanciamento del carico. Per altre informazioni sul supporto IPv6 in reti virtuali di Azure, vedere [What ' s IPv6 per la rete virtuale di Azure?](ipv6-overview.md)
