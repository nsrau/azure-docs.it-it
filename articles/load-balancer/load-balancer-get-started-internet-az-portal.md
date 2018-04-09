---
title: Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando il portale di Azure | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona con il portale di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando il portale di Azure

Questo articolo illustra la creazione di un [servizio di bilanciamento del carico standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end con ridondanza della zona tramite un indirizzo standard IP pubblico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="register-for-availability-zones-preview"></a>Registrarsi all'anteprima di Zone di disponibilità
 
Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Creare un servizio di bilanciamento del carico con ridondanza della zona

1. Nel browser passare al portale di Azure [http://portal.azure.com](http://portal.azure.com) e accedere con l'account Azure.
2. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer.**
3. In **Crea servizio di bilanciamento del carico digitare **myPublicLB** in **Nome**.
4. In **Tipo** selezionare **Pubblica**.
5. In SKU selezionare **Standard (anteprima)**.
6. Fare clic su **Indirizzo IP pubblico**, fare clic su **Crea nuovo**, nella pagina **Crea indirizzo IP pubblico** digitare **myPublicIPStandard** in Nome e per **Zona di disponibilità (anteprima)** selezionare **Con ridondanza della zona**.
7. In **Località** selezionare **Stati Uniti orientali 2** e quindi fare clic su **OK**. Il servizio di bilanciamento del carico avvia la distribuzione e richiede alcuni minuti per completare correttamente la distribuzione.

    ![Creare un servizio di bilanciamento del carico standard con ridondanza della zona con il portale di Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un IP pubblico in una zona di disponibilità](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



