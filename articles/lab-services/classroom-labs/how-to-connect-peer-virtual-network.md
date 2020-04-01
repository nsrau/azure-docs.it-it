---
title: Connettersi a una rete peer in Azure Lab Services Documenti Microsoft
description: Informazioni su come connettere la rete lab a un'altra rete come peer. Ad esempio, connettere la rete scuola/università locale alla rete virtuale di Lab in Azure.For example, connect your on-premises school/university network with Lab's virtual network in Azure.
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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 56c31e03eeec0c81207dc402e864eadec2d768bd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474070"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Connettere la rete del lab a una rete virtuale peer in Azure Lab ServicesConnect your lab's network with a peer virtual network in Azure Lab Services 
In questo articolo vengono fornite informazioni sul peering della rete labs con un'altra rete. 

## <a name="overview"></a>Panoramica
Il peering di reti virtuali consente di connettere senza problemi le reti virtuali di Azure.Virtual network peering enables you to seamlessly connect Azure virtual networks. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola per scopi di connettività. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato attraverso l'infrastruttura backbone Microsoft, in modo analogo al traffico tra macchine virtuali nella stessa rete virtuale, solo tramite indirizzi IP privati. Per altre informazioni, vedere [Peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Potrebbe essere necessario connettere la rete del lab a una rete virtuale peer in alcuni scenari, inclusi i seguenti:You may need to connect your lab's network with a peer virtual network in some scenarios including the following:

- Le macchine virtuali del lab dispongono di software che si connette ai server licenze locali per acquisire la licenza
- Le macchine virtuali nel lab devono accedere ai set di dati (o a qualsiasi altro file) nelle condivisioni di rete dell'università. 

Alcune reti locali sono connesse alla rete virtuale di Azure tramite [ExpressRoute](../../expressroute/expressroute-introduction.md) o [Gateway di rete virtuale.](../../vpn-gateway/vpn-gateway-about-vpngateways.md) Questi servizi devono essere impostati all'esterno di Azure Lab Services.These services must be set up outside of Azure Lab Services. Per altre informazioni sulla connessione di una rete locale ad Azure tramite ExpressRoute, vedere Panoramica di ExpressRoute.To learn more about connecting an on-premises network to Azure using ExpressRoute, see [ExpressRoute overview.](../../expressroute/expressroute-introduction.md) Per la connettività locale che utilizza un gateway di rete virtuale, il gateway, la rete virtuale specificata e l'account lab devono trovarsi tutti nella stessa area.

> [!NOTE]
> Quando si crea una rete virtuale di Azure con un peered con un account lab, è importante comprendere l'impatto dell'area della rete virtuale in cui vengono creati i lab della classe.  Per ulteriori informazioni, fare riferimento alla sezione della guida dell'amministratore su [regioni-località](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account lab
Durante la creazione del nuovo account lab, è possibile selezionare una rete virtuale esistente visualizzata nell'elenco a discesa Rete virtuale peer della scheda Avanzate.During the new lab account creation, you can pick an existing virtual network that shows in the **Peer virtual network** dropdown list on the **Advanced** tab. La rete virtuale selezionata è connessa (con peered) ai lab creati con l'account lab. Tutte le macchine virtuali nei lab creati dopo la modifica di questa modifica avrà accesso alle risorse nella rete virtuale con peering. 

È inoltre disponibile un provisioning per fornire **l'intervallo** di indirizzi di macchine virtuali per i lab. Se viene fornito l'intervallo di indirizzi, tutte le macchine virtuali nei lab con l'account lab verranno create in tale intervallo di indirizzi. L'intervallo di indirizzi deve essere in notazione CIDR (ad esempio 10.20.0.0/20) e non sovrapporsi ad alcun intervallo di indirizzi esistente. Quando si fornisce un intervallo di indirizzi, è importante considerare il numero di macchine virtuali che verranno create nei lab e fornire un intervallo di indirizzi per adattarlo. Per un determinato intervallo, verrà mostrato il numero di laboratori che può ospitare.

![Selezionare da rete a peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Per istruzioni dettagliate sulla creazione di un account lab, vedere [Configurare un account lab](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione del lab
La stessa proprietà può essere abilitata dalla scheda **Configurazione Lab** della pagina **Account lab** se non è stata configurata una rete peer al momento della creazione dell'account lab. La modifica apportata a questa impostazione si applica solo ai lab creati dopo la modifica. Come si può vedere nell'immagine, è possibile abilitare o disabilitare **la rete virtuale Peer** per i lab nell'account lab. 

![Abilitare o disabilitare il peering della rete virtuale dopo la creazione del labEnable or disable VNet peering after the lab is created](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Quando si seleziona una rete virtuale per il campo **Rete virtuale peer,** l'opzione **Consenti al creatore lab** di selezionare il percorso lab è disabilitata. È perché i lab nell'account lab devono trovarsi nella stessa area dell'account lab per potersi connettere con le risorse nella rete virtuale peer. 

> [!IMPORTANT]
> Questa modifica delle impostazioni si applica solo ai lab creati dopo la modifica, non ai lab esistenti. 


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Consentire all'autore del lab di selezionare la località](allow-lab-creator-pick-lab-location.md)
- [Allegare una raccolta di immagini condivise a un lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del labAdd a user as a lab owner](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un lab](how-to-configure-lab-accounts.md)
