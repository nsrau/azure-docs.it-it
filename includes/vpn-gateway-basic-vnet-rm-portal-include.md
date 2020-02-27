---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619809"
---
Seguendo questa procedura è possibile creare una rete virtuale con il modello di distribuzione Resource Manager e il portale di Azure. Per altre informazioni sulle reti virtuali, vedere la [panoramica di Rete virtuale](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Per la connessione della rete virtuale a una posizione locale, coordinarsi con l'amministratore di rete locale per definire un intervallo di indirizzi IP che possa essere usato specificamente per questa rete virtuale. Se su entrambi i lati della connessione VPN è presente un intervallo di indirizzi duplicato, il traffico verrà indirizzato in modo imprevisto. Se inoltre si vuole connettere questa rete virtuale a un'altra rete virtuale, lo spazio degli indirizzi non può sovrapporsi a un'altra rete virtuale. Pianificare la configurazione di rete di conseguenza.
>
>

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In **Cerca risorse, servizio e documenti (G +/)** Digitare *rete virtuale*.

   ![Pagina Individua risorse rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Pagina Individua risorse rete virtuale")
1. Selezionare **rete virtuale** dai risultati del **Marketplace** .

   ![Seleziona rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Pagina Individua risorse rete virtuale")
1. Nella pagina **rete virtuale** fare clic su **Crea**.

   ![pagina rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Fare clic su Crea")
1. Una volta fatto clic su Crea, viene visualizzata la pagina **Crea rete virtuale** .

   ![Pagina Crea rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Pagina Crea rete virtuale")
1. Nella pagina **Crea rete virtuale** configurare le impostazioni della rete virtuale. Durante la compilazione dei campi, il punto esclamativo rosso diventa un segno di spunta verde con la convalida dei caratteri immessi nel campo. Alcuni valori vengono inseriti automaticamente e possono essere sostituiti con valori personalizzati.

   - **Nome**: immettere un nome per la rete virtuale.
   - **Spazio indirizzi**: immettere lo spazio degli indirizzi. Se si hanno più spazi indirizzi da aggiungere, immettere il primo in questo campo. È possibile aggiungere altri spazi indirizzi in un secondo momento, dopo aver creato la rete virtuale. Se la configurazione richiede lo spazio degli indirizzi IPv6, selezionare la casella di controllo per immettere tali informazioni.
   - **Sottoscrizione**: verificare che la sottoscrizione elencata sia corretta. È possibile cambiare sottoscrizione tramite l'elenco a discesa.
   - **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo immettendo un nome per il nuovo gruppo di risorse. Se si crea un nuovo gruppo, assegnare il nome al gruppo di risorse in base ai valori di configurazione pianificati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Località**: selezionare la località della rete virtuale. La località determina la posizione in cui risiederanno le risorse distribuite nella rete virtuale.
   - **Subnet**: aggiungere il **nome** della subnet e l' **intervallo di indirizzi**della subnet. È possibile aggiungere altre subnet in un secondo momento, dopo aver creato la rete virtuale.
   - **Protezione DDoS**: selezionare **Basic**, a meno che non si desideri usare il servizio standard.
   - **Endpoint servizio**: è possibile lasciare questa impostazione **disabilitata**, a meno che la configurazione non specifichi questa impostazione.
   - **Firewall**: è possibile lasciare questa impostazione **disabilitata**, a meno che la configurazione non specifichi questa impostazione.
1. Fare clic su **Crea** per avviare la distribuzione della rete virtuale.
