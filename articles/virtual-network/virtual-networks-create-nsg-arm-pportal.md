<properties 
   pageTitle="Come creare gruppi di sicurezza di rete in modalità ARM mediante il portale di anteprima | Microsoft Azure"
   description="Informazioni su come creare e distribuire gruppi di sicurezza di rete in ARM mediante il portale di anteprima"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Come gestire i gruppi di sicurezza di rete tramite il portale di anteprima

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. È inoltre possibile [creare NSG nel modello di distribuzione classica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Distribuire il modello ARM tramite clic per la distribuzione

A questo punto, è possibile creare un gruppo di sicurezza di rete dall'anteprima. Tuttavia si gestiscono i gruppi di sicurezza di rete esistenti. Prima di poter gestire i gruppi di sicurezza di rete, utilizzare il modello di esempio disponibile nell'archivio pubblico per creare le risorse descritte nello scenario precedente. Distribuire [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

## Creare regole in un gruppo di sicurezza di rete esistente

Per creare regole in un gruppo di sicurezza di rete esistente dal portale di anteprima, attenersi alla procedura seguente.

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Sfoglia>** > **Gruppi di sicurezza di rete**.

![Portale di anteprima - Gruppi di sicurezza di rete](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. Nell'elenco dei gruppi di sicurezza di rete, fare clic su **NSG-FrontEnd** > **Regole di sicurezza in ingresso**

![Portale di anteprima - Gruppi di sicurezza di rete-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Nell'elenco delle **regole di sicurezza in ingresso**, fare clic su **Aggiungi**.

![Portale di anteprima - Aggiungi regola](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Nel pannello **Aggiungi regola di sicurezza in ingresso**, creare una regola denominata *web-rule* con priorità *200* che consente l'accesso tramite *TCP* alla porta *80* a qualsiasi VM da qualsiasi origine, quindi fare clic su **OK**. Notare che la maggior parte di queste impostazioni ha già i valori predefiniti.

![Portale di anteprima - Impostazioni delle regole](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. La nuova regola del gruppo di sicurezza di rete verrà visualizzata dopo pochi secondi.

![Portale di anteprima - Nuova regola](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=AcomDC_1217_2015-->