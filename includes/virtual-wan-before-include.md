---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359502"
---
* Che si abbia una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Si ha una rete virtuale a cui ci si vuole connettere. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'articolo dell'[Avvio rapido](../articles/virtual-network/quick-create-portal.md).

* La rete virtuale non deve contenere alcun gateway di rete virtuale esistente. Se la rete virtuale contiene già un gateway (VPN o ExpressRoute), è necessario rimuovere tutti i gateway prima di procedere. Questa configurazione richiede che le reti virtuali si connettano solo al gateway dell'hub della rete WAN virtuale.

* Un hub virtuale è una rete virtuale che viene creata e usata dalla rete WAN virtuale. Si tratta dell'elemento centrale della rete WAN virtuale in un'area. Ottenere un intervallo di indirizzi IP per l'area dell'hub virtuale. L'intervallo di indirizzi specificati per l'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi dell'ambiente locale a cui ci si connette. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.
