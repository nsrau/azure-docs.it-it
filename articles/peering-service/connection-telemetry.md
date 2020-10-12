---
title: Telemetria della connessione del servizio peering di Azure
description: Informazioni sulla telemetria della connessione del servizio di peering Microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871301"
---
# <a name="peering-service-connection-telemetry"></a>Telemetria di connessione al servizio di peering

I dati di telemetria delle connessioni forniscono informazioni dettagliate raccolte per la connettività tra la località del cliente e la rete Microsoft. I clienti possono ottenere la telemetria per la connessione al servizio di peering di Azure registrando la connessione nella portale di Azure. Questa funzionalità offre la sicurezza dei prefissi e informazioni dettagliate sulle prestazioni della rete.


La telemetria della connessione è costituita dagli ambiti seguenti:

### <a name="latency-measurement"></a>Misurazione della latenza

 La latenza viene misurata dal client al PoP Microsoft Edge per i prefissi registrati all'interno del servizio di peering.

### <a name="route-prefix-monitoring-and-protection"></a>Monitoraggio e protezione del prefisso di route

I percorsi di routing vengono monitorati per eventuali attività sospette acquisite nei log eventi. Per alcuni di questi fattori, ad esempio, vengono creati i registri eventi:

- Hijack di prefisso
- Prelievo prefisso
- Perdite di route

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connessione al servizio di peering, vedere [Connessione al servizio di peering](connection.md).
- Per eseguire il caricamento di una connessione del servizio di peering, vedere [onboarding an peering Service Model](onboarding-model.md).
- Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).
