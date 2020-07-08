---
title: Creare un host Bastion usando l'interfaccia della riga di comando di Azure | Bastion di Azure
description: Questo articolo illustra come creare ed eliminare un host Bastion
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255515"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Creare un host di Azure Bastion usando l'interfaccia della riga di comando

Questo articolo illustra come creare un host Bastion di Azure usando l'interfaccia della riga di comando di Azure. Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH trasparente è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Facoltativamente, è possibile creare un host Bastion di Azure usando il [portale di Azure](bastion-create-host-portal.md)o [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host bastion

Questa sezione consente di creare una nuova risorsa di Azure Bastion usando l'interfaccia della riga di comando di Azure.

1. Creare una rete virtuale e una subnet di Azure Bastion. È necessario creare la subnet di Azure Bastion usando il valore del nome **AzureBastionSubnet**. Questo valore indica ad Azure la subnet in cui devono essere distribuite le risorse Bastion. Questa subnet è diversa rispetto a una subnet del gateway. È necessario usare una subnet di almeno/27 o una subnet più grande (/27,/26 e così via). Creare **AzureBastionSubnet** senza alcuna tabella o delega di route. Se si usano gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere l'articolo [lavorare con gruppi](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Creare un indirizzo IP pubblico per Azure Bastion. L'IP pubblico è l'indirizzo IP pubblico della risorsa Bastion su cui verrà eseguito l'accesso a RDP/SSH (sulla porta 443). L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. Creare una nuova risorsa di Azure Bastion nella AzureBastionSubnet della rete virtuale. Per la creazione e la distribuzione della risorsa Bastion sono necessari circa 5 minuti.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md) .

* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi di sicurezza di rete](bastion-nsg.md).
