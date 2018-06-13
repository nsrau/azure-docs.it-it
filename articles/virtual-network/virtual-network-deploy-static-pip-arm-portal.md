---
title: Creare una VM con un indirizzo IP pubblico statico - Portale di Azure | Documentazione Microsoft
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50ae4d6e8c275db16f811a2a1a063eda441f150b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525540"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Creare una VM con un indirizzo IP pubblico statico mediante il portale di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md) (PowerShell (classico))

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Creare una VM con un IP pubblico statico

Per creare una VM con un IP pubblico statico nel portale di Azure, completare i passaggi seguenti:

1. In un browser passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Nell'angolo superiore sinistro del portale fare clic su **Crea una risorsa**>>**Calcolo**>**Windows Server 2012 R2 Datacenter**.
3. Nell'elenco **Selezionare un modello di distribuzione** selezionare **Resource Manager** e fare clic su **Crea**.
4. Nel riquadro **Informazioni di base** immettere le informazioni della macchina virtuale come indicato di seguito, quindi fare clic su **OK**.
   
    ![Portale di Azure: Nozioni di base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. Nel riquadro **Scegli una dimensione** fare clic su **Standard A1** come indicato di seguito, quindi fare clic su **Seleziona**.
   
    ![Portale di Azure: Scegliere una dimensione](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. Nel riquadro **Impostazioni** fare clic su **Indirizzo IP pubblico**, quindi nel riquadro **Crea indirizzo IP pubblico**, in **Assegnazione**, fare clic su **Statico** come indicato di seguito. Fare quindi clic su **OK**.
   
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. Nel riquadro **Impostazioni** fare clic su **OK**.
8. Esaminare il riquadro **Riepilogo**, come indicato di seguito, quindi fare clic su **OK**.
   
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Notare il nuovo riquadro nel dashboard.
   
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Dopo aver creato la macchina virtuale, viene visualizzato il riquadro **Impostazioni** come indicato di seguito:
    
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Impostare gli indirizzi IP all'interno del sistema operativo

Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale. È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario, ad esempio durante l'[assegnazione di più indirizzi IP a una macchina virtuale Windows](virtual-network-multiple-ip-addresses-portal.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato all'[interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings) di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Passaggi successivi

Qualsiasi traffico di rete può scorrere da e verso la VM creata in questo articolo. All'interno di un gruppo di sicurezza di rete è possibile definire regole di sicurezza in entrata e in uscita che limitano il traffico in ingresso e in uscita dall'interfaccia di rete, la subnet o entrambe le risorse. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md).