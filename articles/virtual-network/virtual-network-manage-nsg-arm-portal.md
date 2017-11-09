---
title: Gestire NSG usando il portale di Azure | Documentazione Microsoft
description: Informazioni su come gestire i gruppi di sicurezza di rete esistenti usando il portale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Gestire gruppi di sicurezza di rete usando il portale

> [!div class="op_single_selector"]
> * [Portale](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del modello di distribuzione classica per le distribuzioni più recenti.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Recuperare le informazioni
È possibile visualizzare i gruppi di sicurezza di rete (NSG, Network Security Group) esistenti, recuperare le regole relative a un NSG esistente e trovare le risorse a cui un NSG è associato.

### <a name="view-existing-nsgs"></a>Visualizzare NSG esistenti

Per visualizzare tutti gli NSG esistenti in una sottoscrizione, seguire questa procedura:

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.

2. Fare clic su **Sfoglia>** > **Gruppi di sicurezza di rete**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Controllare l'elenco di NSG nel pannello **Gruppi di sicurezza di rete** .

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Visualizzare gli NSG in un gruppo di risorse

Per visualizzare l'elenco di NSG nel gruppo di risorse **RG-NSG** , seguire questa procedura:

1. Fare clic su **Gruppi di risorse >** > **RG-NSG** > **...**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Nell'elenco di risorse, cercare gli elementi che visualizzano l'icona NSG, come illustrato nel pannello **Risorse** riportato di seguito.

    ![Portale di Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Elencare tutte le regole per un NSG

Per visualizzare le regole di un NSG denominato **NSG-FrontEnd**, seguire questa procedura:

1. Nel pannello **Gruppi di sicurezza di rete** o nel pannello **Risorse** illustrato in precedenza fare clic su **NSG-FrontEnd**.

2. Nella scheda **Impostazioni** fare clic su **Regole di sicurezza in ingresso**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. Il pannello **Regole di sicurezza in ingresso** è visualizzato come illustrato di seguito.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Nella scheda **Impostazioni** fare clic su **Regole di sicurezza in uscita** per visualizzare le regole in uscita.

    > [!NOTE]
    > Per visualizzare le regole predefinite, fare clic sull'icona **Regole predefinite** nella parte superiore del pannello in cui sono visualizzate le regole.
    >

### <a name="view-nsgs-associations"></a>Visualizzare le associazioni di NSG

Per visualizzare le risorse a cui l'NSG **NSG-FrontEnd** è associato, seguire questa procedura:

1. Nel pannello **Gruppi di sicurezza di rete** o nel pannello **Risorse** illustrato in precedenza fare clic su **NSG-FrontEnd**.

2. Nella scheda **Impostazioni** fare clic su **Subnet** per visualizzare le subnet associate all'NSG.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Nella scheda **Impostazioni** fare clic su **Interfacce di rete** per visualizzare le NIC associate all'NSG.

## <a name="manage-rules"></a>Gestire le regole
È possibile aggiungere regole a un NSG esistente, modificare le regole esistenti e rimuovere regole.

### <a name="add-a-rule"></a>Aggiungere una regola
Per aggiungere una regola che consenta il traffico **in ingresso** alla porta **443** da qualsiasi computer all'NSG **NSG-FrontEnd**, seguire questa procedura:

1. Nel pannello **Gruppi di sicurezza di rete** o nel pannello **Risorse** illustrato in precedenza fare clic su **NSG-FrontEnd**.
2. Nella scheda **Impostazioni** fare clic su **Regole di sicurezza in ingresso**.
3. Nel pannello **Regole di sicurezza in ingresso** fare clic su **Aggiungi**. Quindi, nel pannello **Aggiungi regola di sicurezza in ingresso**, inserire i valori come illustrato di seguito e quindi fare clic su **OK**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Dopo alcuni secondi, si noti la nuova regola nel pannello **Regole di sicurezza in ingresso** .

    ![Portale di Azure - NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Modificare una regola
Per modificare la regola creata in precedenza in modo da consentire traffico in ingresso solo da **Internet**, seguire questa procedura:

1. Nel pannello **Gruppi di sicurezza di rete** o nel pannello **Risorse** illustrato in precedenza fare clic su **NSG-FrontEnd**.
2. Nella scheda **Impostazioni** fare clic sulla regola creata in precedenza.
3. Nel pannello **allow-https** modificare la proprietà di **origine** come illustrato di seguito e quindi fare clic su **Salva**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Eliminare una regola

Per eliminare la regola creata in precedenza, seguire questa procedura:

1. Nel pannello **Gruppi di sicurezza di rete** o nel pannello **Risorse** illustrato in precedenza fare clic su **NSG-FrontEnd**.
2. Nella scheda **Impostazioni** fare clic sulla regola creata in precedenza.
3. Nel pannello **allow-https** fare clic su **Elimina** e quindi fare clic su **Sì**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gestire le associazioni
È possibile associare un NSG a subnet e schede di interfaccia di rete. È anche possibile annullare l'associazione tra un NSG e qualsiasi risorsa a cui è associato.

### <a name="associate-an-nsg-to-a-nic"></a>Associare un NSG a una NIC
Per associare l'NSG **NSG-FrontEnd** alla NIC **TestNICWeb1**, seguire questa procedura:

1. Nel pannello **Gruppi di sicurezza di rete** o nel pannello **Risorse** illustrato in precedenza fare clic su **NSG-FrontEnd**.
2. Nella scheda **Impostazioni** fare clic su **Interfacce di rete** > **Associa** > **interfacesAssociateTestNICWeb1**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Annullare l'associazione tra un NSG e una NIC

Per annullare l'associazione tra l'NSG **NSG-FrontEnd** e la NIC **TestNICWeb1**, seguire questa procedura:

1. Nel portale di Azure fare clic su **Gruppi di risorse >** > **RG-NSG** > **...** > **TestNICWeb1**.

2. Nel pannello **TestNICWeb1** fare clic su **Modifica sicurezza...** > **Nessuna**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> È inoltre possibile usare questo pannello per associare la NIC a qualsiasi NSG esistente.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Annullare l'associazione tra un NSG e una subnet

Per annullare l'associazione tra l'NSG **NSG-FrontEnd** e la subnet **FrontEnd**, seguire questa procedura:

1. Nel portale di Azure fare clic su **Gruppi di risorse >** > **RG-NSG** > **...** > **TestVNet**.

2. Nel pannello **Impostazioni** fare clic su **Subnet** > **FrontEnd** > **Gruppo di sicurezza di rete** > **Nessuno**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Nel pannello **FrontEnd** fare clic su **Salva**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associare un gruppo di sicurezza di rete a una subnet

Per associare di nuovo l'NSG **NSG-FrontEnd** alla subnet **FronEnd**, seguire questa procedura:

1. Nel portale di Azure fare clic su **Gruppi di risorse >** > **RG-NSG** > **...** > **TestVNet**.
2. Nel pannello **Impostazioni** fare clic su **Subnet** > **FrontEnd** > **Gruppo di sicurezza di rete** > **NSG-FrontEnd**.
3. Nel pannello **FrontEnd** fare clic su **Salva**.

> [!NOTE]
> Per associare un NSG a una subnet è anche possibile usare il pannello **Impostazioni** dell'NSG.
>

## <a name="delete-an-nsg"></a>Eliminare un gruppo di sicurezza di rete
È possibile eliminare un NSG solo se non è associato ad alcuna risorsa. Per eliminare un NSG, seguire questa procedura:

1. Nel portale di Azure fare clic su **Gruppi di risorse >** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. Nel pannello **Impostazioni** fare clic su **Interfacce di rete**.
3. Se sono elencate NIC, scegliere la NIC ed eseguire il passaggio 2 in [Annullare l'associazione tra un NSG e una NIC](#Dissociate-an-NSG-from-a-NIC).
4. Ripetere il passaggio 3 per ogni NIC.
5. Nel pannello **Impostazioni** fare clic su **Subnet**.
6. Se sono elencate subnet, scegliere la subnet ed eseguire i passaggi 2 e 3 in [Annullare l'associazione tra un NSG e una subnet](#Dissociate-an-NSG-from-a-subnet).
7. Scorrere a sinistra fino al pannello**NSG-FrontEnd**, quindi fare clic su **Elimina** > **Sì**.

    ![Portale di Azure - Gruppi di sicurezza di rete](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare la registrazione](virtual-network-nsg-manage-log.md) per gli NSG.
