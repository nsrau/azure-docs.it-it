---
title: Creare un indirizzo IP pubblico di zona con il portale di Azure | Microsoft Docs
description: "Creare un indirizzo IP pubblico in una zona di disponibilità con il portale di Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 0e796c8be0a5db8cb67e4a091fa51ed89920852c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Creare un indirizzo IP pubblico in una zona di disponibilità con il portale di Azure

È possibile distribuire un indirizzo IP pubblico in una zona di disponibilità di Azure (anteprima). Una zona di disponibilità è una zona fisicamente separata in un'area di Azure. È possibile passare agli argomenti seguenti:

> * Creare un indirizzo IP pubblico in una zona di disponibilità
> * Identificare le risorse correlate create nella zona di disponibilità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com. 

## <a name="create-a-zonal-public-ip-address"></a>Creare un indirizzo IP pubblico di zona

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Rete** e quindi **Indirizzo IP pubblico**.
3. Immettere o selezionare i valori per le impostazioni seguenti, selezionare la sottoscrizione, accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **Crea**:

    |Impostazione|Valore|
    |---|---|
    |SKU| **Basic**: assegnato con il metodo di allocazione statico o dinamico. Può essere applicato a qualsiasi risorsa di Azure cui è possibile assegnare un indirizzo IP pubblico, tra cui interfacce di rete, gateway VPN, gateway applicazione e servizi di bilanciamento del carico con connessione Internet. È possibile assegnare l'indirizzo IP pubblico a una zona specifica nell'impostazione **Zona di disponibilità**. Questo SKU non fornisce ridondanza della zona. Per altre informazioni sulle zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). **Standard**: assegnato solo con il metodo di allocazione statico. Può essere assegnato a interfacce di rete o a servizi di bilanciamento del carico con connessione Internet standard. Se si assegna l'indirizzo IP pubblico a un servizio di bilanciamento del carico con connessione Internet standard, è necessario selezionare Standard. Per altre informazioni sugli SKU dei servizi di bilanciamento del carico di Azure, vedere [Azure load balancer standard SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) (SKU Standard dei servizi di bilanciamento del carico di Azure). Per impostazione predefinita, questo SKU fornisce ridondanza della zona. Può essere creato a livello di zona ed è garantito in una zona di disponibilità specifica. Lo SKU Standard è in versione di anteprima. Prima di creare un indirizzo IP pubblico con SKU Standard, è necessario registrarsi per l'anteprima. A questo scopo, vedere [Eseguire la registrazione per l'anteprima dello SKU Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up). Lo SKU Standard può essere creato solo in una posizione supportata.  Per un elenco delle posizioni (aree) supportate, vedere [Region availability](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) (Disponibilità a livello di area) e monitorare la pagina [Aggiornamenti di Rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network) per altro supporto sulle aree.|   
    |NOME|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
    |Gruppo di risorse|Fare clic su Crea nuovo e immettere myResourceGroup|
    |Località|Europa occidentale|
    |Zona di disponibilità|Se si seleziona lo SKU **Standard**, è possibile selezionare *Con ridondanza della zona* se si vuole che l'indirizzo IP sia resiliente tra le zone. Se si seleziona lo SKU **Basic**, l'indirizzo IP non è resiliente tra le zone. Indipendentemente dallo SKU selezionato, è possibile scegliere di assegnare l'indirizzo a una zona specifica. |

    Le impostazioni vengono visualizzati nel portale, come mostrato nell'immagine seguente:

    ![Creare un indirizzo IP pubblico di zona](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups). La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.

## <a name="next-steps"></a>Passaggi successivi

- Leggere altre informazioni sulle [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Leggere altre informazioni sugli [indirizzi IP pubblici](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)