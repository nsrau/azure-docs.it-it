---
title: Creare un servizio Load Balancer Standard pubblico con un front-end di indirizzo IP pubblico di zona usando il portale di Azure | Microsoft Docs
description: Informazioni su come creare un servizio Load Balancer Standard pubblico con un front-end di indirizzo IP pubblico di zona con il portale di Azure
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
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 533c48b3a004f85dfbd2970d73dcf7de21811dca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-public-ip-address-frontend-using-azure-portal"></a>Creare un servizio Load Balancer Standard pubblico con un front-end di indirizzo IP pubblico di zona usando il portale di Azure

Questo articolo illustra la creazione di un servizio [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end di zona. Per comprendere il funzionamento delle zone di disponibilità con il servizio Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Il supporto per le zone di disponibilità viene fornito per determinate risorse, aree e famiglie di dimensioni di macchine virtuali di Azure. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Creare un servizio di bilanciamento del carico con un front-end di indirizzo IP di zona

1. Nel browser passare al portale di Azure [http://portal.azure.com](http://portal.azure.com) e accedere con l'account Azure.
2. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer.**
3. Nella pagina **Crea servizio di bilanciamento del carico** in **Nome** digitare **myLoadBalancer**.
4. In **Tipo** selezionare **Pubblica**.
5. In SKU selezionare **Standard**.
6. Fare clic su **Scegliere un indirizzo IP pubblico**, quindi su **Crea nuovo**. Nella pagina **Crea indirizzo IP pubblico** digitare **myPublicIPStandard** in Nome, per SKU selezionare **Standard** e per Zona di disponibilità selezionare **1**.
    
>[!NOTE] 
> L'indirizzo IP pubblico creato in questo passaggio ha SKU Standard per impostazione predefinita.

7. In **Gruppo di risorse** fare clic su **Crea nuovo** e quindi digitare **myResourceGroupZLB** come nome del gruppo di risorse.
8. Per **Posizione** selezionare **Europa occidentale**, quindi fare clic su **OK**. Il servizio di bilanciamento del carico avvia la distribuzione e richiede alcuni minuti per completare correttamente la distribuzione.

    ![Creare un servizio di bilanciamento del carico standard con ridondanza della zona con il portale di Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).



