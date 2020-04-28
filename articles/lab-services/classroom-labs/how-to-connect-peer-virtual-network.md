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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 9e53b6bdb041bfac5a82ed607b75b25ab0513f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188005"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Connettere la rete del Lab a una rete virtuale peer in Azure Lab Services

Questo articolo fornisce informazioni sul peering della rete Labs con un'altra rete.

## <a name="overview"></a>Panoramica

Il peering di rete virtuale consente di connettere facilmente le reti virtuali di Azure. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola per scopi di connettività. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato tramite l'infrastruttura backbone di Microsoft, in modo analogo al traffico instradato tra le macchine virtuali nella stessa rete virtuale, solo tramite indirizzi IP privati. Per altre informazioni, vedere [peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md).

Potrebbe essere necessario connettere la rete del Lab a una rete virtuale peer in alcuni scenari, tra cui quelli riportati di seguito:

- Le macchine virtuali nel Lab hanno un software che si connette ai server licenze locali per acquisire la licenza.
- Le macchine virtuali nel lab devono accedere ai set di dati (o a qualsiasi altro file) nelle condivisioni di rete dell'Università.

Determinate reti locali sono connesse alla rete virtuale di Azure tramite [ExpressRoute](../../expressroute/expressroute-introduction.md) o il [gateway di rete virtuale](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Questi servizi devono essere impostati al di fuori della Azure Lab Services. Per altre informazioni sulla connessione di una rete locale ad Azure tramite ExpressRoute, vedere [Panoramica di ExpressRoute](../../expressroute/expressroute-introduction.md). Per la connettività locale tramite un gateway di rete virtuale, il gateway, la rete virtuale specificata e l'account lab devono trovarsi nella stessa area.

> [!NOTE]
> Quando si crea una rete virtuale di Azure di cui verrà creato il peering con un account Lab, è importante comprendere in che modo l'area della rete virtuale influisca sui laboratori della classe.  Per ulteriori informazioni, vedere la sezione della Guida dell'amministratore in [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account Lab

Durante la creazione di un nuovo [account Lab](tutorial-setup-lab-account.md), è possibile selezionare una rete virtuale esistente visualizzata nell'elenco a discesa **rete virtuale peer** nella scheda **Avanzate** .  L'elenco mostrerà solo le reti virtuali nella stessa area dell'account Lab. La rete virtuale selezionata è connessa (con peering) ai laboratori creati con l'account Lab.  Tutte le macchine virtuali nei Lab che vengono create dopo l'esecuzione di questa modifica avranno accesso alle risorse nella rete virtuale con peering.

![Selezionare VNet per peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Intervallo di indirizzi

È disponibile anche un'opzione per specificare l' **intervallo di indirizzi** per le macchine virtuali per i Lab.  La proprietà **intervallo di indirizzi** si applica solo se la **rete virtuale peer** è abilitata per il Lab.  Se viene specificato l'intervallo di indirizzi, tutte le macchine virtuali nei Lab sotto l'account Lab verranno create in tale intervallo di indirizzi. L'intervallo di indirizzi deve essere in notazione CIDR (ad esempio 10.20.0.0/20) e non sovrapporsi ad alcun intervallo di indirizzi esistente.  Quando si specifica un intervallo di indirizzi, è importante considerare il numero di *Lab* che verrà creato e fornire un intervallo di indirizzi per adattarlo. Lab Services presuppone un massimo di 512 macchine virtuali per Lab.  Ad esempio, un intervallo IP con "/23" può creare solo un Lab.  Un intervallo con '/21' consentirà la creazione di quattro Lab.

Se l' **intervallo di indirizzi** non è specificato, Lab Services utilizzerà l'intervallo di indirizzi predefinito fornito da Azure quando si crea la rete virtuale di cui eseguire il peering con la rete virtuale.  L'intervallo è spesso un elemento come 10. x. 0,0/16.  Questo può comportare una sovrapposizione degli intervalli IP, quindi assicurarsi di specificare e l'intervallo di indirizzi nelle impostazioni del Lab o controllare l'intervallo di indirizzi della rete virtuale di cui si sta eseguendo il peering.

## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione del Lab

La stessa proprietà può essere abilitata dalla scheda **configurazione Labs** della pagina **account Lab** se non è stata configurata una rete peer al momento della creazione dell'account Lab. La modifica apportata a questa impostazione si applica solo ai Lab creati dopo la modifica. Come è possibile vedere nell'immagine, è possibile abilitare o disabilitare la **rete virtuale peer** per Lab nell'account Lab.

![Abilita o Disabilita il peering VNet dopo la creazione del Lab](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Quando si seleziona una rete virtuale per il campo **rete virtuale peer** , l'opzione **Consenti al creatore Lab di selezionare il percorso Lab** è disabilitata. Questo perché i Lab nell'account lab devono trovarsi nella stessa area dell'account Lab per connettersi alle risorse nella rete virtuale peer.

> [!IMPORTANT]
> L'impostazione della rete virtuale con peering si applica solo ai Lab creati dopo la modifica, non ai Lab esistenti.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Consentire all'autore del lab di selezionare la località](allow-lab-creator-pick-lab-location.md)
- [Alleghi una raccolta di immagini condivise a un Lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del Lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall per un Lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni per un Lab](how-to-configure-lab-accounts.md)
