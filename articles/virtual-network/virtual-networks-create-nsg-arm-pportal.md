---
title: Creare un gruppo di sicurezza di rete - Portale di Azure | Microsoft Docs
description: Informazioni su come creare e distribuire gruppi di sicurezza di rete mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793644"
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Creare un gruppo di sicurezza di rete tramite il portale di Azure

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Questo articolo illustra il modello di distribuzione Gestione risorse. È anche possibile creare gruppi di sicurezza di rete con il [modello di distribuzione classica](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Creare il gruppo di sicurezza di rete NSG-FrontEnd
Per creare il gruppo di sicurezza di rete **NSG-FrontEnd** come illustrato nello scenario, seguire questa procedura:

1. In un browser passare a https://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Selezionare **+ Crea una risorsa >** > **Gruppi di sicurezza di rete**.
   
    ![Portale di Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. In **Gruppi di sicurezza di rete** selezionare **Aggiungi**.
   
    ![Portale di Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. In **Crea gruppo di sicurezza di rete** creare un gruppo denominato *NSG-FrontEnd* nel gruppo di risorse *RG-NSG* e quindi selezionare **Crea**.
   
    ![Portale di Azure - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Creare regole in un gruppo di sicurezza di rete esistente
Per creare regole in un gruppo di sicurezza di rete esistente dal portale di Azure, seguire questa procedura:

1. Selezionare **Tutti i servizi** e quindi cercare **gruppi di sicurezza di rete**. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata.
2. Nell'elenco dei gruppi di sicurezza di rete selezionare **NSG-FrontEnd** > **Regole di sicurezza in ingresso**
   
    ![Portale di Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Nell'elenco **Regole di sicurezza in ingresso** selezionare **Aggiungi**.
   
    ![Portale di Azure - Aggiungi regola](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. In **Aggiungi regola di sicurezza in ingresso** creare una regola denominata *web-rule* con priorità *200*, che consente l'accesso tramite *TCP* alla porta *80* a qualsiasi VM da qualsiasi origine, quindi selezionare **OK**. Notare che la maggior parte di queste impostazioni ha già i valori predefiniti.
   
    ![Portale di Azure - Impostazioni delle regole](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. La nuova regola viene visualizzata dopo pochi secondi nel gruppo di sicurezza di rete.
   
    ![Portale di Azure - Nuova regola](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Ripetere i passaggi fino al 6 per creare una regola in entrata denominata *rdp-rule* con priorità *250*, che consente l'accesso tramite *TCP* alla porta *3389* per qualsiasi VM da qualsiasi origine.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associare il gruppo di sicurezza di rete alla subnet FrontEnd

1. Selezionare **Tutti i servizi >**, immettere **Gruppi di risorse**, selezionare **Gruppi di risorse** quando viene visualizzato e quindi selezionare **RG-NSG**.
2. In **RG-NSG** selezionare **...** > **TestVNet**.
   
    ![Portale di Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. In **Impostazioni** selezionare **Subnet** > **FrontEnd** > **Gruppo di sicurezza di rete** > **NSG-FrontEnd**.
   
    ![Portale di Azure - Impostazioni della subnet](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Nel pannello **FrontEnd** selezionare **Salva**.
   
    ![Portale di Azure - Impostazioni della subnet](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Creare il gruppo di sicurezza di rete NSG-BackEnd
Per creare il gruppo di sicurezza di rete **NSG-BackEnd** e associarlo alla subnet **BackEnd**, seguire questa procedura:

1. Per creare un gruppo di sicurezza di rete denominato *NSG-BackEnd*, ripetere la procedura in [Creare il gruppo di sicurezza di rete NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG).
2. Per creare le regole **in entrata** nella tabella seguente, ripetere la procedura illustrata in [Creare regole in un gruppo di sicurezza di rete esistente](#Create-rules-in-an-existing-NSG).
   
   | Regola in entrata | Regola in uscita |
   | --- | --- |
   | ![Portale di Azure - Regola in entrata](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portale di Azure - Regola in uscita](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Per associare il gruppo di sicurezza di rete **NSG-BackEnd** alla subnet **BackEnd**, ripetere la procedura illustrata in [Associare il gruppo di sicurezza di rete alla subnet FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [gestire i gruppi di sicurezza esistenti](manage-network-security-group.md)
* [Abilitare la registrazione](virtual-network-nsg-manage-log.md) per i gruppi di sicurezza di rete.