---
title: 'Soluzione VMware di Azure di CloudSimple: connettere il cloud privato alla rete di Azure tramite ExpressRoute'
description: Viene descritto come connettere l'ambiente cloud privato CloudSimple alla rete virtuale di Azure tramite ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536352"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Connettere l'ambiente cloud privato CloudSimple alla rete virtuale di Azure tramite ExpressRoute

Il cloud privato CloudSimple può essere connesso alla rete virtuale di Azure usando Azure ExpressRoute.  Questa connessione con larghezza di banda elevata e bassa latenza consente di accedere ai servizi in esecuzione nella sottoscrizione di Azure dall'ambiente cloud privato.

La connessione alla rete virtuale consente di:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati rete VSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico mentre i livelli dell'applicazione e del database vengono eseguiti nel cloud privato.

![Connessione Azure ExpressRoute alla rete virtuale](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Configurare una connessione di rete virtuale

Per configurare la connessione di rete virtuale al cloud privato, sono necessari la chiave di autorizzazione, l'URI del circuito peer e l'accesso alla sottoscrizione di Azure. Queste informazioni sono disponibili nella pagina connessione della rete virtuale nel portale di CloudSimple. Per istruzioni, vedere [ottenere informazioni sul peering per la rete virtuale di Azure per la connessione CloudSimple](virtual-network-connection.md). In caso di problemi durante il recupero delle informazioni, inviare una <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">richiesta di supporto</a>.

> [!TIP]
> Se si ha già una rete virtuale di Azure, una subnet del gateway e un gateway di rete virtuale, è possibile procedere al passaggio 4.

1. Creare una rete virtuale nella sottoscrizione di Azure e verificare che lo spazio degli indirizzi selezionato sia diverso dallo spazio degli indirizzi del cloud privato.  Se è già presente una rete virtuale di Azure, è possibile usare quella esistente.  Per informazioni dettagliate, vedere [creare una rete virtuale usando il portale di Azure](../virtual-network/quick-create-portal.md).
2. Creare la subnet del gateway nella rete virtuale di Azure.  Se si dispone già di una subnet del gateway nella rete virtuale di Azure, è possibile usare quella esistente. Per informazioni dettagliate, vedere [creare la subnet del gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Creare il gateway di rete virtuale nella rete virtuale.  Se si dispone di un gateway di rete virtuale esistente, è possibile utilizzarne uno esistente. Per informazioni dettagliate, vedere [creare il gateway di rete virtuale](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Creare la connessione tra la rete virtuale e il cloud privato riscattando la chiave di autorizzazione come descritto in [connettere una rete virtuale a una sottoscrizione circuit-different](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se si usa un gateway di rete virtuale esistente e è presente una connessione ExpressRoute alla stessa località del circuito CloudSimple ExpressRoute, la connessione non verrà stabilita.  Creare una nuova rete virtuale e seguire i passaggi precedenti.

## <a name="test-the-virtual-network-connection"></a>Testare la connessione alla rete virtuale

Dopo aver creato la connessione, è possibile controllare lo stato della connessione selezionando **Proprietà** in **Impostazioni**.  Lo stato e lo stato di provisioning devono indicare operazione **riuscita**.

![Stato della connessione](media/azure-expressroute-connection.png)

Per testare la connessione alla rete virtuale:

1. Creare una macchina virtuale nella sottoscrizione di Azure.
2. Trovare l'indirizzo IP del cloud privato vCenter (fare riferimento al messaggio di posta elettronica di benvenuto).
3. Effettuare il ping del cloud vCenter dalla macchina virtuale creata nella rete virtuale di Azure.
4. Effettuare il ping della macchina virtuale di Azure da una macchina virtuale in esecuzione nel cloud privato vCenter.

Se si verificano problemi durante il tentativo di stabilire la connessione, inviare una <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">richiesta di supporto</a>.
