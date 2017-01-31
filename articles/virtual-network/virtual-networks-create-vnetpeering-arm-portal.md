---
title: Creare un peering reti virtuali usando il portale di Azure | Microsoft Docs
description: Informazioni su come creare una rete virtuale con il portale di Azure in Resource Manager.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: ed7c37ee5482b3ec91489b7aba9b5a9d5df5c97e
ms.openlocfilehash: 06130f18bad86dbbe009548ac95ac0e6c4953db2


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Creare un peering reti virtuali usando il portale di Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un peering reti virtuali basato sullo scenario precedente tramite il portale di Azure, seguire questa procedura.

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Per stabilire un peering reti virtuali, è necessario creare due collegamenti, uno per ogni direzione, tra due reti virtuali. È possibile creare il collegamento per il peering reti virtuali prima da VNET1 a VNET2. Nel portale fare clic su **Esplora** > **e scegliere Reti virtuali**
   
    ![Creare un peering reti virtuali nel portale di Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. Nel pannello Reti virtuali scegliere VNET1, fare clic su Peer e quindi su Aggiungi.
   
    ![Scegliere il peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. Nel pannello Aggiungi peering assegnare un nome al collegamento per il peering LinkToVnet2, scegliere la sottoscrizione e la rete virtuale peer VNET2. Fare clic su OK.
   
    ![Collegamento alla rete virtuale](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. Dopo la creazione del collegamento per il peering reti virtuali, sarà possibile visualizzare lo stato del collegamento come segue:
   
    ![Stato del collegamento](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Creare quindi il collegamento per il peering reti virtuali da VNET2 a VNET1. Nel pannello Reti virtuali scegliere VNET2, fare clic su Peer e quindi su Aggiungi.
   
    ![Peer da un'altra rete virtuale](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. Nel pannello Aggiungi peering assegnare un nome al collegamento per il peering LinkToVnet1, scegliere la sottoscrizione e la rete virtuale peer. Fare clic su OK.
   
    ![Pannello Creazione della rete virtuale](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. Dopo la creazione del collegamento per il peering reti virtuali, sarà possibile visualizzare lo stato del collegamento come segue:
   
    ![Stato del collegamento finale](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Controllare lo stato di LinkToVnet2 che ora risulta anch'esso Connesso.  
   
    ![Stato del collegamento finale 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)
   
   > [!NOTE]
   > Il peering reti virtuali viene stabilito solo se entrambi i collegamenti risultano connessi.
   > 
   > 

Esistono alcune proprietà configurabili per ogni collegamento:

| Opzione | Descrizione | Default |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |Indica se lo spazio indirizzi della rete virtuale peer deve essere incluso come parte del tag Virtual_network. |Sì |
| AllowForwardedTraffic |Consente di accettare o eliminare il traffico che non ha origine da una rete virtuale con peering. |No |
| AllowGatewayTransit |Consente alla rete virtuale peer di usare il gateway di rete virtuale. |No |
| UseRemoteGateways |Consente di usare il gateway di rete virtuale del peer. Per la rete virtuale peer deve essere configurato un gateway ed essere selezionata l'opzione AllowGatewayTransit. Non è possibile usare questa opzione se si ha un gateway configurato. |No |

Ogni collegamento nel peering reti virtuali include un set delle proprietà precedenti. Dal portale è possibile fare clic sul collegamento per il peering reti virtuali e modificare le opzioni disponibili. Fare clic su Salva per rendere effettiva la modifica.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. In questo esempio si useranno due sottoscrizioni A e B e due utenti, utente A e utente B, con privilegi nelle rispettive sottoscrizioni.
3. Nel portale fare clic su Esplora e scegliere Reti virtuali. Fare clic sulla rete virtuale e quindi su Aggiungi.
   
    ![Scenario 2 Esplora](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. Nel pannello Aggiungi accesso fare clic per selezionare un ruolo e scegliere Collaboratore Rete. Fare clic su Aggiungi utenti, digitare il nome di accesso dell'utente B e fare clic su OK.
   
    ![Controllo degli accessi in base al ruolo](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

5. Accedere quindi al portale di Azure come utente B, ovvero l'utente con privilegi per la sottoscrizione B. Seguire i passaggi precedenti per aggiungere l'utente A come Collaboratore Rete.
   
    ![Controllo degli accessi in base al ruolo 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)
   
   > [!NOTE]
   > È possibile disconnettersi e accedere in entrambe le sessioni utente nel browser per assicurarsi che l'autorizzazione sia abilitata correttamente.
   > 
   > 
6. Accedere al portale come utente A, passare al pannello VNET3, fare clic su Peer, selezionare la casella di controllo "Conosco l'ID della risorsa" e digitare l'ID della risorsa per VNET5 nel formato seguente.
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}
   
    ![ID risorsa](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Accedere al portale come utente B e seguire il passaggio precedente per creare il collegamento per il peering da VNET5 a VNet3.
   
    ![ID risorsa 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. Verrà stabilito il peering e tutte le macchine virtuali in VNet3 potranno comunicare con tutte le macchine virtuali in VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Come primo passaggio viene creato il collegamento per il peering reti virtuali da HubVnet a VNET1. Si noti che l'opzione Consenti traffico inoltrato non è selezionata per il collegamento.
   
    ![Peering di base](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Come passaggio successivo è possibile creare collegamenti per il peering da VNET1 a HubVnet. Si noti che l'opzione Consenti traffico inoltrato è selezionata.
   
    ![Peering di base](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. Dopo aver stabilito il peering, è possibile vedere questo [articolo](virtual-network-create-udr-arm-ps.md) e creare route definite dall'utente per reindirizzare il traffico di VNet1 attraverso un'appliance virtuale per usarne le funzionalità. Quando si specifica l'indirizzo hop successivo nella route, è possibile impostarlo sull'indirizzo IP dell'appliance virtuale nella rete virtuale peer HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Per stabilire il peering reti virtuali in questo scenario, è necessario creare solo un collegamento, dalla rete virtuale in Azure Resource Manager a quella nella distribuzione classica, ovvero da **VNET1** a **VNET2**. Nel portale fare clic su **Esplora** > e scegliere **Reti virtuali**
3. Nel pannello Reti virtuali scegliere **VNET1**. Fare clic su **Peer**, quindi su **Aggiungi**.
4. Nel pannello Aggiungi peering assegnare un nome al collegamento. Qui è chiamato **LinkToVNet2**. In Dettagli peer selezionare **Classica**.
5. Scegliere quindi la sottoscrizione e la rete virtuale del peer **VNET2**. Fare quindi clic su OK.
   
    ![Collegamento di Vnet1 a Vnet2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
6. Dopo avere creato questo collegamento del peering reti virtuali, viene eseguito il peering delle due reti virtuali e sarà possibile visualizzare quanto segue:
   
    ![Controllo della connessione peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>Rimuovere il peering reti virtuali
1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Passare al pannello Reti virtuali, fare clic su Peer, fare clic sul collegamento da rimuovere e scegliere il pulsante Elimina.
   
   ![Elimina1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. Dopo la rimozione di un collegamento nel peering reti virtuali, lo stato del collegamento peer sarà Disconnesso.
   
    ![Elimina2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. Con questo stato non si potrà ricreare il collegamento fino a quando lo stato del collegamento peer non diventerà Avviato. È consigliabile rimuovere entrambi i collegamenti prima di ricreare il peering reti virtuali.




<!--HONumber=Dec16_HO1-->


