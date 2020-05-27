---
title: Configurare la preferenza di routing per un indirizzo IP pubblico - Portale di Azure
description: Informazioni su come creare un indirizzo IP pubblico con una preferenza di routing per il traffico Internet
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 5d56b276e44a1dbdffb66270e04763ea729628ef
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594920"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configurare la preferenza di routing per un indirizzo IP pubblico con il portale di Azure

Questo articolo illustra come configurare una preferenza di routing tramite rete ISP (opzione **Internet**) per un indirizzo IP pubblico. Dopo aver creato l'indirizzo IP pubblico, è possibile associarlo alle risorse di Azure seguenti per il traffico Internet in ingresso e in uscita:

* Macchina virtuale
* Set di scalabilità di macchine virtuali
* Servizio Azure Kubernetes
* Servizio di bilanciamento del carico Internet
* Gateway applicazione
* Firewall di Azure

Per impostazione predefinita, la preferenza di routing per l'indirizzo IP pubblico è impostata sulla rete globale Microsoft per tutti i servizi di Azure e può essere associata a qualsiasi servizio di Azure.

> [!IMPORTANT]
> La preferenza di routing è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="register-the-feature-for-your-subscription"></a>Registrare la funzionalità per la propria sottoscrizione
La funzionalità Preferenza di routing è attualmente disponibile in anteprima. Registrare la funzionalità per la propria sottoscrizione usando Azure PowerShell come indicato di seguito:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Creare un IP pubblico con una preferenza di routing
1. Accedere al [portale di Azure](https://preview.portal.azure.com/).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca digitare *indirizzo IP pubblico*.
3. Nei risultati della ricerca selezionare **Indirizzo IP pubblico**. Quindi, nella pagina **Indirizzo IP pubblico** selezionare **Crea**.
3. Nelle opzioni **Preferenza di routing** selezionare **Internet**.

      ![Creare un indirizzo IP pubblico](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Gli indirizzi IP pubblici vengono creati con un indirizzo IPv4 o IPv6. Tuttavia, attualmente la preferenza di routing supporta solo IPV4.

È possibile associare l'indirizzo IP pubblico creato in precedenza a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Seguire la sezione per l'interfaccia della riga di comando nella pagina dell'esercitazione: [Associare un indirizzo IP pubblico a una macchina virtuale](associate-public-ip-address-vm.md#azure-cli) per associare l'indirizzo IP pubblico alla VM. È anche possibile associare l'indirizzo IP pubblico creato in precedenza a un'istanza di [Azure Load Balancer](../load-balancer/load-balancer-overview.md), assegnandolo alla configurazione **front-end** del servizio. L'indirizzo IP pubblico viene usato come indirizzo IP virtuale (indirizzo VIP) di bilanciamento del carico.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sugli [indirizzi IP pubblici con preferenza di routing](routing-preference-overview.md).
- [Configurare la preferenza di routing per una VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configurare la preferenza di routing per un indirizzo IP pubblico con PowerShell](routing-preference-powershell.md).
- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
