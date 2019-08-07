---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780102"
---
Per creare una rete virtuale nel modello di distribuzione Resource Manager usando il portale di Azure, seguire questa procedura. Usare i **valori di esempio** se si usano questi passaggi come un'esercitazione. In caso contrario, assicurarsi di sostituire i valori di esempio con valori reali. Per altre informazioni sull'uso delle reti virtuali, vedere la [panoramica sulla rete virtuale](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Per consentire a questa rete virtuale di connettersi a una posizione locale, è necessario coordinarsi con l'amministratore di rete locale per definire un intervallo di indirizzi IP da usare in modo specifico per questa rete virtuale. Se su entrambi i lati della connessione VPN è presente un intervallo di indirizzi duplicato, il traffico non viene indirizzato nel modo previsto. Se inoltre si vuole connettere questa rete virtuale a un'altra rete virtuale, lo spazio degli indirizzi non può sovrapporsi a un'altra rete virtuale. Pianificare quindi con attenzione la configurazione della rete.
>

1. In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.
2. Fare clic su **Crea una risorsa**. Nel campo **Cerca nel Marketplace** digitare "rete virtuale". Individuare **Rete virtuale** nell'elenco restituito e fare clic per aprire la pagina **Rete virtuale**.
3. Fare clic su **Create**(Crea). Verrà visualizzata la pagina **Crea rete virtuale**.
4. Nella pagina **Crea rete virtuale** configurare le impostazioni della rete virtuale. Durante la compilazione dei campi, il punto esclamativo rosso diventa un segno di spunta verde se i caratteri immessi nel campo sono validi. Usare i valori seguenti:

   - **Nome**: Rete virtuale1
   - **Spazio degli indirizzi**: 10.1.0.0/16
   - **Sottoscrizione** Verificare che la sottoscrizione elencata sia quella che si vuole usare. È possibile cambiare sottoscrizione tramite l'elenco a discesa.
   - **Gruppo di risorse**: TestRG1 (fare clic su **Crea nuovo** per creare un nuovo gruppo)
   - **Località**: East US
   - **Subnet**: Front-end
   - **Intervallo di indirizzi**: 10.1.0.0/24

   ![Pagina Crea rete virtuale](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Lasciare DDoS come Basic, gli endpoint di servizio come disabilitati e il firewall come disabilitato.
6. Fare clic su **Crea** per creare la rete virtuale.