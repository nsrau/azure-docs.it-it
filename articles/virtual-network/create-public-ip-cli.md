---
title: Creare un IP pubblico-interfaccia della riga di comando di Azure
description: Informazioni su come creare un indirizzo IP pubblico usando l'interfaccia della riga di comando di Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d0934dd30f9738e2a67d4aff23c96a48aaaa61b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302851"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Guida introduttiva: creare un indirizzo IP pubblico usando l'interfaccia della riga di comando

Questo articolo illustra come creare una risorsa indirizzo IP pubblico usando l'interfaccia della riga di comando di Azure. Per altre informazioni sulle risorse a cui questo può essere associato, sulla differenza tra lo SKU Basic e standard e altre informazioni correlate, vedere [indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Per questo esempio, si concentrerà solo sugli indirizzi IPv4; per altre informazioni sugli indirizzi IPv6, vedere [IPv6 per Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Prerequisiti

- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [AZ Group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) denominato **myResourceGroup** nel percorso **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**SKU standard-uso di zone**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Il comando seguente funziona per l'API versione 2020-08-01 o successiva.  Per altre informazioni sulla versione API attualmente in uso, vedere [provider e tipi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Usare il comando [AZ Network Public-IP create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per creare un indirizzo IP pubblico con ridondanza della zona standard denominato **myStandardZRPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Per le versioni dell'API precedenti alla 2020-08-01, eseguire il comando precedente senza specificare un parametro di zona per creare un indirizzo IP con ridondanza della zona. 
>

Per creare un indirizzo IP pubblico di zona standard in Zona 2 denominato **myStandardZonalPublicIP** in **myResourceGroup**, usare il comando seguente:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Si noti che le opzioni precedenti per le zone sono selezioni valide solo nelle aree con [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU standard-nessuna zona**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Il comando seguente funziona per l'API versione 2020-08-01 o successiva.  Per altre informazioni sulla versione API attualmente in uso, vedere [provider e tipi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Usare il comando [AZ Network Public-IP create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per creare un indirizzo IP pubblico standard come risorsa non di zona denominata **myStandardPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Questa selezione è valida in tutte le aree ed è la selezione predefinita per gli indirizzi IP pubblici standard in aree senza [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-create-public-ip-basic)

Usare il comando [AZ Network Public-IP create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) per creare un indirizzo IP pubblico statico di base denominato **myBasicPublicIP** in **myResourceGroup**.  Gli indirizzi IP pubblici di base non hanno il concetto di zone di disponibilità.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard
    --allocation-method Static
```
Se è accettabile che l'indirizzo IP cambi nel tempo, è possibile selezionare l'assegnazione IP **dinamica** cambiando il metodo di allocazione in ' Dynamic '.

---

## <a name="additional-information"></a>Informazioni aggiuntive 

Per altri dettagli sulle singole variabili elencate in precedenza, vedere [gestire gli indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Passaggi successivi
- Associare un [indirizzo IP pubblico a una macchina virtuale](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).