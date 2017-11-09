---
title: Individuare l'hop successivo con Network Watcher di Azure - Portale di Azure | Documentazione Microsoft
description: "Questo articolo descrive come individuare il tipo di hop successivo e l'indirizzo IP mediante la funzionalità Hop successivo del portale di Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Individuare il tipo di hop successivo tramite la funzionalità Hop successivo di Network Watcher di Azure mediante il portale di Azure

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST di Azure](network-watcher-check-next-hop-rest.md)

Hop successivo è una funzionalità di Network Watcher che consente di recuperare il tipo di hop successivo e l'indirizzo IP in base a una macchina virtuale specificata. La funzionalità è utile per determinare se il traffico in uscita da una macchina virtuale attraversa gateway, Internet o reti virtuali per arrivare alla propria destinazione.

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo usa la funzionalità Hop successivo per rilevare il tipo di hop successivo e l'indirizzo IP per una risorsa. Per altre informazioni sulla funzionalità di individuazione dell'hop successivo, consultare la [panoramica sulla funzionalità Hop successivo](network-watcher-next-hop-overview.md).

In questo scenario si apprenderà come:

* Recuperare l'hop successivo da una macchina virtuale.

## <a name="get-next-hop"></a>Ottenere l'hop successivo

### <a name="step-1"></a>Passaggio 1

Nel portale di Azure passare alla risorsa Network Watcher.

### <a name="step-2"></a>Passaggio 2

Fare clic su **Hop successivo** nel riquadro di spostamento, selezionare la macchina virtuale e l'interfaccia di rete, compilare l'indirizzo IP di origine e di destinazione e fare clic sul pulsante **Hop successivo**.

> [!NOTE]
> La funzionalità Hop successivo richiede che la risorsa VM sia allocata.

![Panoramica di come ottenere l'hop successivo][1]

### <a name="step-3"></a>Passaggio 3

Al termine dell'attività vengono forniti i risultati. Vengono visualizzati l'indirizzo IP e il tipo di dispositivo dell'hop successivo. La tabella seguente illustra i valori restituiti disponibili nel portale.

**Tipo di hop successivo**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

Se è stata usata una route personalizzata per indirizzare il traffico, insieme ai risultati viene visualizzata la route definita dall'utente (UDR).

![Risultati dell'ottenimento dell'hop successivo][2]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come verificare le impostazioni del gruppo di sicurezza di rete a livello di codice, consultare l'articolo su come [verificare i gruppi di sicurezza di rete con Network Watcher](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














