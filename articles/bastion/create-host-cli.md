---
title: Creare un host Bastion usando l'interfaccia della riga di comando di AzureCreate a Bastion host using Azure CLI Bastione di Azure
description: In questo articolo imparerai a creare ed eliminare un host Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337571"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Creare un host di Azure Bastion usando l'interfaccia della riga di comando di AzureCreate an Azure Bastion host using Azure CLI

Questo articolo illustra come creare un host di Azure Bastion usando l'interfaccia della riga di comando di Azure.This article shows you how to create an Azure Bastion host using Azure CLI. Dopo aver eseguito il provisioning del servizio Bastion di Azure nella rete virtuale, l'esperienza RDP/SSH senza interruzioni è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Facoltativamente, è possibile creare un host Di Azure Bastion usando il portale di [Azure](bastion-create-host-portal.md)o [Azure PowerShell.](bastion-create-host-powershell.md)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host Bastion

Questa sezione consente di creare una nuova risorsa Bastion di Azure usando l'interfaccia della riga di comando di Azure.This section helps you create a new Azure Bastion resource using Azure CLI.

1. Creare una rete virtuale e una subnet di Azure Bastion.Create a virtual network and an Azure Bastion subnet. È necessario creare la subnet di Azure Bastion usando il valore del nome **AzureBastionSubnet**. Questo valore consente ad Azure di sapere in quale subnet distribuire le risorse Bastion.This value lets Azure know which subnet to deploy the Bastion resources to. Questo è diverso da una subnet del gateway. È necessario utilizzare una subnet di almeno /27 o subnet più grande (/27, /26 e così via). Creare **AzureBastionSubnet** senza tabelle di route o deleghe. Se si usano gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere l'articolo Usare i gruppi di [sicurezza di rete.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Creare un indirizzo IP pubblico per Azure Bastion.Create a public IP address for Azure Bastion. L'indirizzo IP pubblico è l'indirizzo IP pubblico della risorsa Bastion su cui si accede a RDP/SSH (tramite la porta 443). L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Creare una nuova risorsa Bastion di Azure in AzureBastionSubnet della rete virtuale. La creazione e la distribuzione della risorsa Bastion richiede circa 5 minuti.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Passaggi successivi

* Leggi le domande frequenti sul [Bastion](bastion-faq.md) per ulteriori informazioni.

* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi](bastion-nsg.md)di sicurezza di rete.
