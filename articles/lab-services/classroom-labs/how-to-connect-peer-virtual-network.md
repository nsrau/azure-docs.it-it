---
title: Connettersi a una rete peer in Azure Lab Services | Microsoft Docs
description: Informazioni su come connettere la rete lab con un'altra rete come peer. Ad esempio, connettere la rete School/University locale con la rete virtuale del Lab in Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701676"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Connettere la rete del Lab a una rete virtuale peer in Azure Lab Services 
Questo articolo fornisce informazioni sul peering della rete Labs con un'altra rete. 

## <a name="overview"></a>Panoramica
Il peering di rete virtuale consente di connettere facilmente le reti virtuali di Azure. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola per scopi di connettività. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato tramite l'infrastruttura backbone di Microsoft, in modo analogo al traffico instradato tra le macchine virtuali nella stessa rete virtuale, solo tramite indirizzi IP privati. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Potrebbe essere necessario connettere la rete del Lab a una rete virtuale peer in alcuni scenari, tra cui quelli riportati di seguito:

- Le macchine virtuali nel Lab hanno un software che si connette ai server licenze locali per acquisire la licenza
- Le macchine virtuali nel lab devono accedere ai set di dati (o a qualsiasi altro file) nelle condivisioni di rete dell'Università. 

Determinate reti locali sono connesse alla rete virtuale di Azure tramite [ExpressRoute](../../expressroute/expressroute-introduction.md) o il [gateway di rete virtuale](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Questi servizi devono essere impostati al di fuori della Azure Lab Services. Per altre informazioni sulla connessione di una rete locale ad Azure tramite ExpressRoute, vedere [Panoramica di ExpressRoute](../../expressroute/expressroute-introduction.md). Per la connettività locale tramite un gateway di rete virtuale, il gateway, la rete virtuale specificata e l'account lab devono trovarsi nella stessa area.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account Lab
Durante la creazione di un nuovo account Lab, è possibile selezionare una rete virtuale esistente visualizzata nell'elenco a discesa **rete virtuale peer** . La rete virtuale selezionata è connessa (con peering) ai laboratori creati con l'account Lab. Tutte le macchine virtuali nei Lab creati dopo l'esecuzione di questa modifica possono accedere alle risorse nella rete virtuale con peering. 

![Selezionare VNet per peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Per istruzioni dettagliate per la creazione di un account Lab, vedere [configurare un account Lab](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione del Lab
La stessa proprietà può essere abilitata dalla scheda **configurazione Labs** della pagina **account Lab** se non è stata configurata una rete peer al momento della creazione dell'account Lab. La modifica apportata a questa impostazione si applica solo ai Lab creati dopo la modifica. Come è possibile vedere nell'immagine, è possibile abilitare o disabilitare la **rete virtuale peer** per Lab nell'account Lab. 

![Abilita o Disabilita il peering VNet dopo la creazione del Lab](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Quando si seleziona una rete virtuale per il campo **rete virtuale peer** , l'opzione **Consenti al creatore Lab di selezionare il percorso Lab** è disabilitata. Il motivo è che i Lab nell'account lab devono trovarsi nella stessa area dell'account Lab per connettersi alle risorse nella rete virtuale peer. 

> [!IMPORTANT]
> Questa modifica dell'impostazione si applica solo ai Lab creati dopo la modifica, non ai Lab esistenti. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

