---
title: Aggiornare gli indirizzi IP pubblici
titleSuffix: Azure Virtual Network
description: Aggiornare gli indirizzi IP pubblici da Basic a standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: blehr
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 0c248149694c2bf66b8c94e9c0a29a8f7da9f4e4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843741"
---
# <a name="upgrade-public-ip-addresses"></a>Aggiornare gli indirizzi IP pubblici

Gli indirizzi IP pubblici di Azure vengono creati con uno SKU, ovvero Basic o standard, che determinano gli aspetti della loro funzionalità, inclusi il metodo di allocazione, l'utilizzo nelle zone di disponibilità e le risorse a cui possono essere associati. 

In questo articolo vengono esaminati gli scenari seguenti:
* Come aggiornare un IP pubblico dello SKU di base a un IP pubblico con SKU standard (usando il portale, PowerShell o l'interfaccia della riga di comando)
* Come eseguire la migrazione di un IP riservato di Azure classico a un indirizzo IP pubblico dello SKU di Azure Resource Manager Basic

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Aggiornare l'indirizzo IP pubblico da Basic a SKU standard

>[!NOTE]
>La possibilità di aggiornare gli indirizzi IP pubblici da Basic a standard non è disponibile in tutte le aree.  Per ulteriori informazioni, vedere [**limitazioni**](#limitations) .

Per eseguire l'aggiornamento di un indirizzo IP pubblico, non deve essere associato a nessuna risorsa (vedere [Questa pagina](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) per altre informazioni su come dissociare gli indirizzi IP pubblici).

>[!IMPORTANT]
>Gli indirizzi IP pubblici aggiornati dallo SKU Basic a quello standard continuano a non avere [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  Ciò significa che non possono essere associati a una risorsa di Azure con ridondanza della zona o collegata a una zona specificata nelle aree in cui è disponibile.

---
# <a name="basic-to-standard---powershell"></a>[**Da Basic a standard-PowerShell**](#tab/option-upgrade-powershell)

Nell'esempio seguente si presuppone la creazione precedente di un IP pubblico dello SKU di base, usando l'esempio fornito in [Questa pagina](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic) con un indirizzo IP pubblico di base **myBasicPublicIP** in **myResourceGroup**.

Per aggiornare l'indirizzo IP, è sufficiente eseguire i comandi seguenti usando PowerShell.  Nota Se l'indirizzo IP è già allocato in modo statico, la sezione può essere ignorata.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Da Basic a standard-CLI**](#tab/option-upgrade-cli)

Nell'esempio seguente si presuppone la creazione precedente di un IP pubblico dello SKU di base, usando l'esempio fornito in [Questa pagina](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic) con un indirizzo IP pubblico di base **myBasicPublicIP** in **myResourceGroup**.

Per aggiornare l'indirizzo IP, è sufficiente eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.  Nota Se l'indirizzo IP è già allocato in modo statico, la sezione può essere ignorata.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Aggiornare (migrare) un IP riservato classico a un IP pubblico statico

Per trarre vantaggio dalle nuove funzionalità di Azure Resource Manager, è possibile eseguire la migrazione dell'indirizzo IP statico pubblico esistente, denominato IP riservati, dal modello classico al modello di Azure Resource Manager moderno.  L'IP pubblico migrato sarà un tipo di SKU di base.


---

# <a name="reserved-to-basic---powershell"></a>[**Riservato a Basic-PowerShell**](#tab/option-migrate-powershell)

Nell'esempio seguente si presuppone la creazione precedente di un IP riservato di Azure classico **myReservedIP** in **myResourceGroup**. Un altro prerequisito per la migrazione consiste nel garantire che la sottoscrizione Azure Resource Manager sia stata registrata per la migrazione. Questa operazione è descritta in dettaglio nei passaggi 3 e 4 di [Questa pagina](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps).

Per eseguire la migrazione del IP riservato, eseguire i comandi seguenti usando PowerShell.  Nota Se l'indirizzo IP non è associato ad alcun servizio (al di sotto di un servizio **denominato MyServices**), questo passaggio può essere ignorato.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Il comando precedente Visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con i passaggi seguenti per preparare ed eseguire il commit della migrazione:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Viene creato un nuovo gruppo di risorse in Azure Resource Manager usando il nome della IP riservato migrata (nell'esempio precedente, il gruppo di risorse **myReservedIP-migrato**).

# <a name="reserved-to-basic---cli"></a>[**Riservato all'interfaccia della riga di comando di base**](#tab/option-migrate-cli)

Nell'esempio seguente si presuppone la creazione precedente di un IP riservato di Azure classico **myReservedIP** in **myResourceGroup**. Un altro prerequisito per la migrazione consiste nel garantire che la sottoscrizione Azure Resource Manager sia stata registrata per la migrazione. Questa operazione è descritta in dettaglio nei passaggi 3 e 4 di [Questa pagina](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli).

Per eseguire la migrazione della IP riservato, eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.  Nota Se l'indirizzo IP non è associato ad alcun servizio (al di **sotto di un servizio denominato MyServices** e distribuzione **),** questo passaggio può essere ignorato.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Il comando precedente Visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con i passaggi seguenti per preparare ed eseguire il commit della migrazione:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Viene creato un nuovo gruppo di risorse in Azure Resource Manager usando il nome della IP riservato migrata (nell'esempio precedente, il gruppo di risorse **myReservedIP-migrato**).

---

## <a name="limitations"></a>Limitazioni

* Questa funzionalità non è attualmente disponibile nelle aree geografiche seguenti:<br>
US Gov Virginia<br>
US DoD (area orientale)<br>
US DoD (area centrale)<br>
Cina orientale<br>
Cina orientale 2<br>
Cina settentrionale<br>
Cina settentrionale 2

* Per aggiornare un indirizzo IP pubblico di base, non può essere associato ad alcuna risorsa di Azure.  Per ulteriori informazioni su come dissociare gli indirizzi IP pubblici, consultare [Questa pagina](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) .  Allo stesso modo, per eseguire la migrazione di un IP riservato, non può essere associato ad alcun servizio cloud.  Consultare [Questa pagina](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) per altre informazioni su come dissociare gli indirizzi IP riservati.  
* Gli indirizzi IP pubblici aggiornati dallo SKU Basic a quello standard continueranno a non avere [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) e pertanto non potranno essere associati a una risorsa di Azure con ridondanza della zona o di zona.  Si noti che questo si applica solo alle aree che offrono le zone di disponibilità.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure, tra cui la differenza tra i tipi di SKU e le [impostazioni degli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Informazioni su come [aggiornare i bilanciamento del carico pubblico di Azure da Basic a standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard).
- Informazioni sugli [IP riservati di Azure classico](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) e [sulla migrazione delle risorse classiche a Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).
