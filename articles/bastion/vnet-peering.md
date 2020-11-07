---
title: Peering di VNet e architettura Bastion di Azure
description: Questo articolo illustra come è possibile usare insieme il peering di VNet e Azure Bastion per connettersi alle macchine virtuali.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: cherylmc
ms.openlocfilehash: ad3cf33dacffc8bcda9376857206784afedf7139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361991"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>Peering di VNet e Azure Bastion (anteprima)

Il peering di Azure Bastion e VNet possono essere usati insieme. Quando si configura il peering VNet, non è necessario distribuire Azure Bastion in ogni VNet con peering. Ciò significa che se si dispone di un host Bastion di Azure configurato in una rete virtuale (VNet), può essere usato per connettersi alle macchine virtuali distribuite in un VNet con peering senza distribuire un host Bastion aggiuntivo. Per ulteriori informazioni sul peering VNet, vedere [informazioni sul peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).

Azure Bastion funziona con i tipi di peering seguenti:

* **Peering di rete virtuale:** Connettere reti virtuali all'interno della stessa area di Azure.
* **Peering di rete virtuale globale:** Connessione di reti virtuali tra aree di Azure.

## <a name="architecture"></a>Architettura

Quando si configura il peering VNet, Azure Bastion può essere distribuito nelle topologie Hub e spoke o con mesh complete. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa VNet, oltre che in reti virtuali con peering. Ciò significa che è possibile consolidare la distribuzione bastione in un singolo VNet e raggiungere comunque le VM distribuite in un VNet con peering, centralizzando la distribuzione complessiva.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagramma di progettazione e architettura":::

Questa figura illustra l'architettura di una distribuzione Bastion di Azure in un modello hub e spoke. In questo diagramma è possibile vedere la configurazione seguente:

* L'host Bastion viene distribuito nella rete virtuale Hub centralizzata.
* Viene distribuito il gruppo di sicurezza di rete centralizzato (NSG).
* Un indirizzo IP pubblico non è obbligatorio nella macchina virtuale di Azure.

**Passaggi**

1. Connettersi al portale di Azure usando un browser HTML5.
1. Selezionare la macchina virtuale a cui connettersi.
1. Azure Bastion viene facilmente rilevato attraverso la VNet con peering.
1. Con un solo clic, la sessione RDP/SSH viene aperta nel browser. Per i limiti della sessione simultanea RDP e SSH, vedere [sessioni RDP e SSH](bastion-faq.md#limits).

   :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Connettere":::

   Per altre informazioni sulla connessione a una macchina virtuale tramite Azure Bastion, vedere:

   * [Connettersi a una macchina virtuale-RDP](bastion-connect-vm-rdp.md).
   * [Connettersi a una macchina virtuale-SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Domande frequenti

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).