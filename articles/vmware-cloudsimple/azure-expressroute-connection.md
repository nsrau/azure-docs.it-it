---
title: Azure VMware Solution by CloudSimple - Connect Private Cloud to Azure network using ExpressRoute
description: Descrive come connettere l'ambiente CloudSimple Private Cloud alla rete virtuale di Azure usando ExpressRouteDescribes how to connect your CloudSimple Private Cloud environment to the Azure virtual network using ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015236"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Connettere l'ambiente Cloud Cloud semplice alla rete virtuale di Azure usando ExpressRouteConnect your CloudSimple Private Cloud environment to the Azure virtual network using ExpressRoute

Your CloudSimple Private Cloud can be connected to your Azure virtual network using Azure ExpressRoute.  Questa connessione ad alta larghezza di banda e bassa latenza consente di accedere ai servizi in esecuzione nella sottoscrizione di Azure dall'ambiente cloud privato.

La connessione di rete virtuale consente di:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati vSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico mentre i livelli applicazione e database vengono eseguiti nel cloud privato.

![Connessione ExpressRoute di Azure alla rete virtualeAzure ExpressRoute Connection to virtual network](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Configurare una connessione di rete virtualeSet up a virtual network connection

Per configurare la connessione di rete virtuale al cloud privato, sono necessari la chiave di autorizzazione, l'URI del circuito peer e l'accesso alla sottoscrizione di Azure.To set up the virtual network connection to your Private Cloud, you need your authorization key, peer circuit URI, and access to your Azure subscription. Queste informazioni sono disponibili nella pagina Connessione di rete virtuale nel portale CloudSimple.This information is available on the Virtual Network Connection page in the CloudSimple portal. Per istruzioni, vedere Ottenere informazioni sul peering per la [rete virtuale di Azure alla connessione CloudSimple](virtual-network-connection.md). In caso di problemi nell'ottenere le informazioni, inviare una richiesta di <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a>.

> [!TIP]
> Se si dispone già di una rete virtuale di Azure, di una subnet gateway e di un gateway di rete virtuale, è possibile andare al passaggio 4.If you already have a Azure virtual network, gateway subnet, and virtual network gateway, you can skip to step 4.

1. Creare una rete virtuale nella sottoscrizione di Azure e verificare che lo spazio di indirizzi selezionato sia diverso dallo spazio di indirizzi del cloud privato.  Se si dispone già di una rete virtuale di Azure, è possibile usare quella esistente.  Per informazioni dettagliate, vedere Creare una rete virtuale usando il portale di Azure.For details, see Create a [virtual network using the Azure portal.](../virtual-network/quick-create-portal.md)
2. Creare la subnet del gateway nella rete virtuale di Azure.Create the gateway subnet on your Azure virtual network.  Se nella rete virtuale di Azure è già presente una subnet gateway, è possibile usare quella esistente. Per informazioni dettagliate, vedere [Creare la subnet del gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Creare il gateway di rete virtuale nella rete virtuale.  Se si dispone di un gateway di rete virtuale esistente, è possibile usare quello esistente. Per informazioni dettagliate, vedere Creare il gateway di [rete virtuale.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)
4. Creare la connessione tra la rete virtuale e il cloud privato riscattando la chiave di autorizzazione come descritto in [Connettere una rete virtuale a un circuito - sottoscrizione diversa](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se si usa un gateway di rete virtuale esistente e dispone di una connessione ExpressRoute allo stesso percorso del circuito CloudSimple ExpressRoute, la connessione non verrà stabilita.  Creare una nuova rete virtuale e seguire i passaggi precedenti.

## <a name="test-the-virtual-network-connection"></a>Testare la connessione di rete virtualeTest the virtual network connection

Dopo aver creato la connessione, è possibile controllare lo stato della connessione selezionando **Proprietà** in **Impostazioni**.  Stato e Stato provisioning dovrebbero essere **visualizzati Come completato**.

![Stato connessione](media/azure-expressroute-connection.png)

Per testare la connessione di rete virtuale:To test the virtual network connection:

1. Creare una macchina virtuale nella sottoscrizione di Azure.Create a virtual machine in your Azure subscription.
2. Trova l'indirizzo IP del tuo vCenter Private Cloud (fai riferimento alla tua email di benvenuto).
3. Eseguire il ping del vCenter cloud dalla macchina virtuale creata nella rete virtuale di Azure.Ping your Cloud vCenter from the virtual machine created in your Azure virtual network.
4. Eseguire il ping della macchina virtuale di Azure da una macchina virtuale in esecuzione nel cloud privato vCenter.Ping your Azure virtual machine from a virtual machine running in your Private Cloud vCenter.

In caso di problemi durante la creazione della connessione, inviare una richiesta di <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a>.
