---
title: Configurare le preferenze di routing per un indirizzo IP pubblico - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Informazioni su come configurare le preferenze di routing per un indirizzo IP pubblico con Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: e856e3fc253800b71176dad3db40e7a13a7e6140
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707872"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Configurare le preferenze di routing per un indirizzo IP pubblico con Azure PowerShell

Questo articolo illustra come configurare le preferenze di routing usando la rete ISP (opzione **Internet**) per un indirizzo IP pubblico con Azure PowerShell. Dopo aver creato l'indirizzo IP pubblico, è possibile associarlo alle risorse di Azure seguenti per il traffico Internet in ingresso e in uscita:

* Macchina virtuale
* Set di scalabilità di macchine virtuali
* Servizio Azure Kubernetes
* Servizio di bilanciamento del carico Internet
* Gateway applicazione
* Firewall di Azure

Per impostazione predefinita, la preferenza di routing per l'indirizzo IP pubblico è impostata sulla rete globale Microsoft per tutti i servizi di Azure e può essere associata a qualsiasi servizio di Azure.

> [!IMPORTANT]
> La preferenza di routing è attualmente Anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 6.9.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="register-the-feature-for-your-subscription"></a>Registrare la funzionalità per la propria sottoscrizione
La funzionalità Preferenza di routing è attualmente in anteprima. Registrare la funzionalità per la propria sottoscrizione come indicato di seguito:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Creare un IP pubblico con preferenza di routing Internet

Il comando seguente crea un nuovo indirizzo IP pubblico con preferenza di routing *Internet* nell'area di Azure *Stati Uniti orientali*:

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

È possibile associare l'indirizzo IP pubblico creato in precedenza a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Attenersi alla sezione dell'interfaccia della riga di comando nella pagina dell'esercitazione: [Associare un indirizzo IP pubblico a una macchina virtuale](associate-public-ip-address-vm.md#azure-cli) per associare l'indirizzo IP pubblico alla macchina virtuale. È anche possibile associare l'indirizzo IP pubblico creato con un'istanza di [Azure Load Balancer](../load-balancer/load-balancer-overview.md), assegnandolo alla configurazione **front-end** del servizio di bilanciamento del carico. L'indirizzo IP pubblico viene usato come indirizzo IP virtuale (indirizzo VIP) di bilanciamento del carico.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [preferenze di routing per gli indirizzi IP pubblici](routing-preference-overview.md).
- [Configurare le preferenze di routing per una macchina virtuale con Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md).
