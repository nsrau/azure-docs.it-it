---
title: Creare un host Bastion usando l'interfaccia della riga di comando di Azure | Bastion di Azure
description: Questo articolo illustra come creare ed eliminare un host Bastion
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: 8ee90d80230f9115946525ede325e874e98e358e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90024333"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Creare un host di Azure Bastion usando l'interfaccia della riga di comando

Questo articolo illustra come creare un host Bastion di Azure usando l'interfaccia della riga di comando di Azure. Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH trasparente è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Facoltativamente, è possibile creare un host Bastion di Azure usando il [portale di Azure](bastion-create-host-portal.md)o [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host bastion

Questa sezione consente di creare una nuova risorsa di Azure Bastion usando l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Come illustrato negli esempi, usare il `--location` parametro con `--resource-group` per ogni comando per assicurarsi che le risorse vengano distribuite insieme.

1. Creare una rete virtuale e una subnet di Azure Bastion. È necessario creare la subnet di Azure Bastion usando il valore del nome **AzureBastionSubnet**. Questo valore indica ad Azure la subnet in cui devono essere distribuite le risorse Bastion. Questa subnet è diversa rispetto a una subnet del gateway. È necessario usare una subnet di almeno/27 o una subnet più grande (/27,/26 e così via). Creare **AzureBastionSubnet** senza alcuna tabella o delega di route. Se si usano gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere l'articolo [lavorare con gruppi](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Creare un indirizzo IP pubblico per Azure Bastion. L'IP pubblico è l'indirizzo IP pubblico della risorsa Bastion su cui verrà eseguito l'accesso a RDP/SSH (sulla porta 443). L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Creare una nuova risorsa di Azure Bastion nella AzureBastionSubnet della rete virtuale. Per la creazione e la distribuzione della risorsa Bastion sono necessari circa 5 minuti.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md) .

* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi di sicurezza di rete](bastion-nsg.md).
