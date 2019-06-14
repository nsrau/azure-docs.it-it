---
title: Creare un servizio di Load Balancer con un front-end con ridondanza della zona - Portale di Azure
titlesuffix: Azure Load Balancer
description: Informazioni su come creare un Load Balancer Standard con un front-end di indirizzo IP pubblico con ridondanza della zona con il portale di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 448ae5f8a615a526460ac92eaaf6c7d16761aec2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60684918"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Creare un Load Balancer Standard con un front-end con ridondanza della zona usando il portale di Azure

Questo articolo illustra la creazione di un [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end con ridondanza della zona tramite un indirizzo standard IP pubblico. Un indirizzo IP front-end singolo in un servizio Load Balancer Standard offre per impostazione predefinita la ridondanza della zona.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
>  Il supporto per le zone di disponibilità è disponibile per determinate risorse, aree e famiglie di dimensioni di macchine virtuali di Azure. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Creare un servizio di bilanciamento del carico con ridondanza della zona

1. Nel browser passare al portale di Azure [https://portal.azure.com](https://portal.azure.com) e accedere con l'account Azure.
2. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer.**
3. Nella pagina **Crea servizio di bilanciamento del carico** in **Nome** digitare **myLoadBalancer**.
4. In **Tipo** selezionare **Pubblica**.
5. In SKU selezionare **Standard**.
6. Fare clic su **Indirizzo IP pubblico**, quindi su **Crea nuovo** e nella pagina **Crea indirizzo IP pubblico** digitare **myPublicIPStandard** per il nome.
    >[!NOTE] 
    > L'indirizzo IP pubblico creato in questo passaggio ha SKU Standard e ridondanza della zona per impostazione predefinita. 
8. In **Località** selezionare **Stati Uniti orientali 2** e quindi fare clic su **OK**. Il servizio di bilanciamento del carico avvia la distribuzione e richiede alcuni minuti per completare correttamente la distribuzione.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).



