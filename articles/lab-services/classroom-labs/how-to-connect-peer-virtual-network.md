---
title: Connettersi a una rete di peer in Azure Lab Services | Microsoft Docs
description: Informazioni su come connettere la rete lab con un'altra rete come peer. Ad esempio, è possibile connettere la rete di scuola/università in locale con la rete virtuale del Lab in Azure.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695405"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>La connessione di rete del lab con una rete virtuale peer in Azure Lab Services 
Questo articolo fornisce informazioni sul peering della rete lab con un'altra rete. 

## <a name="overview"></a>Panoramica
Peering reti virtuali consente di connettere reti virtuali di Azure. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola per scopi di connettività. Il traffico tra macchine virtuali nelle reti virtuali con peering viene instradato attraverso l'infrastruttura backbone Microsoft, analogo a quello del traffico instradato tra le macchine virtuali nella stessa rete virtuale, tramite solo indirizzi IP privati. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Potrebbe essere necessario per la connessione di rete del lab con una rete virtuale peer in alcuni scenari, tra cui quelle seguenti:

- Le macchine virtuali nel lab sono software che si connette ai server licenze locale per acquisire la licenza
- Le macchine virtuali nel lab devono accedere a set di dati (o qualsiasi altro file) nelle condivisioni di rete dell'università. 

Alcune reti locali sono connesse alla rete virtuale di Azure sia attraverso [ExpressRoute](../../expressroute/expressroute-introduction.md) oppure [Gateway di rete virtuale](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Questi servizi devono essere impostati all'esterno di Azure Lab Services. Per altre informazioni sulla connessione di una rete locale ad Azure tramite ExpressRoute, vedere la [panoramica relativa a ExpressRoute]) (.. /expressroute/expressroute-Introduction.MD). Per la connettività da sito locale tramite un Gateway di rete virtuale, il gateway, rete virtuale specificata e l'account del lab deve essere tutti nella stessa area.


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account lab
Durante la creazione di nuovi account lab, è possibile selezionare una rete virtuale esistente che mostra le **rete virtuale Peer** nell'elenco a discesa. Rete virtuale selezionata è connected(peered) per Lab creato con l'account del lab. Tutte le macchine virtuali nel lab che vengono creati dopo la creazione di questa modifica potrebbe avere accesso alle risorse nella rete virtuale con peering. 

![Selezionare la rete virtuale per il peering](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Per istruzioni dettagliate per la creazione di un account del lab, vedere [configurare un account lab](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione di lab
La stessa proprietà può essere abilitata dal **configurazione Labs** scheda della finestra di **Account del Lab** pagina se è stata impostata una rete peer al momento della creazione dell'account lab. Modifiche apportate a questa impostazione si applica solo ai laboratori di creati dopo la modifica.

![Abilitare o disabilitare la VNet peering dopo aver creato il lab](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Come si vede nella figura, è possibile abilitare o disabilitare **rete virtuale Peer** per Lab con l'account del lab. 

> [!IMPORTANT]
> Modifica di questa impostazione si applica solo ai laboratori creati dopo la modifica venga apportata, non per i lab esistenti. 



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

