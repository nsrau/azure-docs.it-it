---
title: Puntare un dominio Internet aziendale a un nome di dominio di Traffic Manager di Azure
description: In questo articolo vengono fornite istruzioni per scegliere il nome di dominio aziendale per un nome di dominio di Gestione traffico.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: allensu
ms.openlocfilehash: cd99d8829a8a7bb57b6affe98c1257eaa3ea8ce7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070957"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico di Azure

Quando si crea un profilo di Gestione traffico, Azure assegna automaticamente un nome DNS per tale profilo. Per usare un nome dalla zona DNS, creare un record DNS CNAME che esegua il mapping al nome di dominio del profilo di Gestione traffico. È possibile visualizzare il nome di dominio di Gestione traffico nella sezione **Generale** nella pagina di configurazione del profilo di Gestione traffico.

Ad esempio, per scegliere il nome `www.contoso.com` come nome DNS di Gestione traffico `contoso.trafficmanager.net` si crea il record di risorse DNS seguente:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Tutto il traffico di richieste al *www\.contoso.com* vengono indirizzati al portale *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Non è possibile scegliere un dominio di secondo livello, come *contoso.com*, per il dominio di Gestione traffico. Gli standard di protocollo DNS non consentono record CNAME per i nomi di dominio di secondo livello.

## <a name="next-steps"></a>Passaggi successivi

* [Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)
* [Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)
* [Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)
