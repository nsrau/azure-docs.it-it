---
title: Creare una VM con un indirizzo IP pubblico statico - Portale di Azure | Documentazione Microsoft
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
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
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 7740861cccfe0ba4e8ae0694f6d5c5fec3df47ef
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Creare una VM con un indirizzo IP pubblico statico mediante il portale di Azure

> [!div class="op_single_selector"]
- [Portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [Interfaccia della riga di comando di Azure](virtual-network-deploy-static-pip-arm-cli.md)
- [Modello](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell (Classic)](virtual-networks-reserved-public-ip.md) (PowerShell (classico))

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Creare una VM con un IP pubblico statico

Per creare una VM con un IP pubblico statico nel portale di Azure, completare i passaggi seguenti:

1. In un browser passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Nell'angolo superiore sinistro del portale fare clic su **Nuovo**>>**Calcolo**>**Windows Server 2012 R2 Datacenter**.
3. Nell'elenco **Selezionare un modello di distribuzione** selezionare **Resource Manager** e fare clic su **Crea**.
4. Nel pannello **Informazioni di base** immettere le informazioni della VM come illustrato di seguito, quindi fare clic su **OK**.
   
    ![Portale di Azure: Nozioni di base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. Nel pannello **Scegli una dimensione** fare clic su **Standard A1** come illustrato di seguito, quindi sul pulsante di **Seleziona**.
   
    ![Portale di Azure: Scegliere una dimensione](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. Nel pannello **Impostazioni** fare clic su **Indirizzo IP pubblico**, quindi nel pannello **Crea indirizzo IP pubblico**, in **Assegnazione**, fare clic su **Statico** come illustrato di seguito. Fare quindi clic su **OK**.
   
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. Nel pannello **Impostazioni** fare clic su **OK**.
8. Esaminare il pannello **Riepilogo**, come illustrato di seguito, quindi fare clic su **OK**.
   
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Notare il nuovo riquadro nel dashboard.
   
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Dopo aver creato la VM, il pannello **Impostazioni** viene visualizzato il pannello come illustrato di seguito
    
    ![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)


