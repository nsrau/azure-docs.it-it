---
title: Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando il portale di Azure | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona con il portale di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: d09115d7b01b72476ebc9eb883a43aa4c59d2e3b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando il portale di Azure

Questo articolo illustra la creazione di un [servizio di bilanciamento del carico standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end con ridondanza della zona tramite un indirizzo standard IP pubblico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Eseguire la registrazione per l'anteprima di Zone di disponibilità, Load Balancer Standard e l'IP pubblico Standard
 
>[!NOTE]
Lo [SKU di Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) è attualmente in anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usare lo [SKU di Load Balancer Basic](load-balancer-overview.md) disponibile a livello generale per i servizi di produzione. 

> [!NOTE]
> Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Prima di selezionare una zona o l'opzione con ridondanza della zona per l'indirizzo IP pubblico front-end per il servizio di bilanciamento del carico, è necessario completare i passaggi in [Eseguire la registrazione per l'anteprima di Zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Creare un servizio di bilanciamento del carico con ridondanza della zona

1. Utilizzare un browser per accedere al portale di Azure ( [http://portal.azure.com](http://portal.azure.com) ) con il proprio account di Azure.
2. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer.**
3. In **Crea servizio di bilanciamento del carico digitare **myPublicLB** in **Nome**.
4. In **Tipo** selezionare **Pubblica**.
5. In SKU selezionare **Standard (anteprima)**.
6. Fare clic su **Indirizzo IP pubblico**, fare clic su **Crea nuovo**, nella pagina **Crea indirizzo IP pubblico** digitare **myPublicIPStandard** in Nome e per **Zona di disponibilità (anteprima)** selezionare **Con ridondanza della zona**.
7. In **Località** selezionare **Stati Uniti orientali 2** e quindi fare clic su **OK**. Il servizio di bilanciamento del carico avvia la distribuzione e richiede alcuni minuti per completare correttamente la distribuzione.

    ![Creare un servizio di bilanciamento del carico standard con ridondanza della zona con il portale di Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un IP pubblico in una zona di disponibilità](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



