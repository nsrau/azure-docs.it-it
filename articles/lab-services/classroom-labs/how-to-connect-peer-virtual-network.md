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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443193"
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
Durante la creazione di un nuovo account Lab, è possibile selezionare una rete virtuale esistente visualizzata nell'elenco a discesa **rete virtuale peer** nella scheda **Avanzate** . La rete virtuale selezionata è connessa (con peering) ai laboratori creati con l'account Lab. Tutte le macchine virtuali nei Lab creati dopo l'esecuzione di questa modifica possono accedere alle risorse nella rete virtuale con peering. 

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

- [Consenti a Lab Creator di selezionare la posizione del Lab](allow-lab-creator-pick-lab-location.md)
- [Alleghi una raccolta di immagini condivise a un Lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del Lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un Lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un Lab](how-to-configure-lab-accounts.md)