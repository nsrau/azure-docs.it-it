---
title: file di inclusione
description: file di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5bf93980a8be86c77240ab981eb812a738a96204
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829105"
---
Seguendo questa procedura è possibile creare una rete virtuale con il modello di distribuzione Resource Manager e il portale di Azure. Per altre informazioni sulle reti virtuali, vedere la [panoramica di Rete virtuale](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Per la connessione della rete virtuale a una posizione locale, coordinarsi con l'amministratore di rete locale per definire un intervallo di indirizzi IP che possa essere usato specificamente per questa rete virtuale. Se su entrambi i lati della connessione VPN è presente un intervallo di indirizzi duplicato, il traffico verrà indirizzato in modo imprevisto. Se inoltre si vuole connettere questa rete virtuale a un'altra rete virtuale, lo spazio degli indirizzi non può sovrapporsi a un'altra rete virtuale. Pianificare la configurazione di rete di conseguenza.
>
>

1. Accedere al [portale di Azure](https://portal.azure.com).  Dal menu portale di Azure o dalla **Home** page, quindi selezionare **Crea una risorsa**. Verrà visualizzata la pagina **Nuova**.

2. In **Cerca nel Marketplace**immettere *rete virtuale* e selezionare **rete virtuale** dai risultati.

   ![Pagina Individua risorse rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Pagina Individua risorse rete virtuale")

   Verrà visualizzata la pagina **Rete virtuale**.

3. Selezionare **Resource Manager** nell'elenco **Selezionare un modello di distribuzione** nella parte inferiore della pagina e quindi **Crea**. Verrà visualizzata la pagina **Crea rete virtuale**.

   ![Pagina Crea rete virtuale](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Pagina Crea rete virtuale")

4. Nella pagina **Crea rete virtuale** configurare le impostazioni della rete virtuale. Durante la compilazione dei campi, il punto esclamativo rosso diventa un segno di spunta verde con la convalida dei caratteri immessi nel campo. Alcuni valori vengono inseriti automaticamente e possono essere sostituiti con valori personalizzati.

   - **Nome**: immettere un nome per la rete virtuale.

   - **Spazio indirizzi**: immettere lo spazio degli indirizzi. Se si hanno più spazi indirizzi da aggiungere, immettere il primo in questo campo. È possibile aggiungere altri spazi indirizzi in un secondo momento, dopo aver creato la rete virtuale.

   - **Sottoscrizione**: verificare che la sottoscrizione elencata sia corretta. È possibile cambiare sottoscrizione tramite l'elenco a discesa.

   - **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo immettendo un nome per il nuovo gruppo di risorse. Se si crea un nuovo gruppo, assegnare il nome al gruppo di risorse in base ai valori di configurazione pianificati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Località**: selezionare la località della rete virtuale. La località determina la posizione in cui risiederanno le risorse distribuite nella rete virtuale.

   - **Subnet**: aggiungere il **nome** della subnet e l' **intervallo di indirizzi**della subnet. È possibile aggiungere altre subnet in un secondo momento, dopo aver creato la rete virtuale. 
     
5. Selezionare **Create** (Crea).
