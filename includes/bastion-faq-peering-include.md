---
title: includere file
description: includere file
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356653"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>È comunque possibile distribuire più host Bastion tra reti virtuali con peering?

Sì. Per impostazione predefinita, un utente Visualizza l'host Bastion distribuito nella stessa rete virtuale in cui risiede la macchina virtuale. Tuttavia, nel menu **Connetti** , un utente può visualizzare più host Bastion rilevati nelle reti con peering. Possono selezionare l'host Bastion che preferiscono usare per connettersi alla VM distribuita nella rete virtuale.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Se la reti virtuali con peering viene distribuita in sottoscrizioni diverse, la connettività viene eseguita tramite Bastion?

Sì, la connettività tramite Bastion continuerà a funzionare per reti virtuali con peering in una sottoscrizione diversa per un singolo tenant. Le sottoscrizioni tra due tenant diversi non sono supportate. Per visualizzare Bastion nel menu a discesa **Connetti** , l'utente deve selezionare le Subs a cui hanno accesso nella sottoscrizione **> sottoscrizione globale**.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Filtro sottoscrizioni globali" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Ho accesso alla VNet con peering, ma non vedo la macchina virtuale distribuita.

Verificare che l'utente disponga dell'accesso in **lettura** alla macchina virtuale e al VNet con peering. Verificare inoltre in IAM che l'utente disponga dell'accesso in **lettura** alle risorse seguenti:

* Ruolo Lettore nella macchina virtuale.
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale.
* Ruolo Lettore nella risorsa Azure Bastion.
* Ruolo lettore sulla rete virtuale (non necessario se non è presente una rete virtuale con peering).

|Autorizzazioni|Descrizione|Tipo di autorizzazione|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Recupera un Bastion Host|Action|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Ottiene riferimenti Bastion Host in una rete virtuale.|Action|
|Microsoft. Network/virtualNetworks/bastionHosts/default/Action|Ottiene riferimenti Bastion Host in una rete virtuale.|Action|
|Microsoft.Network/networkInterfaces/read|Ottiene una definizione dell’interfaccia di rete.|Action|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Ottiene una definizione di configurazione IP dell'interfaccia di rete.|Action|
|Microsoft.Network/virtualNetworks/read|Ottiene la definizione della rete virtuale|Action|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali nella subnet della rete virtuale|Action|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Ottiene i riferimenti a tutte le macchine virtuali in una rete virtuale|Azione|