---
title: Connessione al servizio peering di Azure
description: Informazioni sulla connessione al servizio di peering Microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398921"
---
# <a name="peering-service-connection"></a>Connessione al servizio di peering

Una connessione si riferisce in genere a un set di informazioni logiche, che identifica un servizio di peering. Viene definito specificando gli attributi seguenti:

- Nome logico
- Partner di connettività
- Posizione fisica del cliente
- Prefissi IP

Il cliente può stabilire una singola connessione o più connessioni in base ai requisiti. Una connessione viene usata anche come unità di raccolta di dati di telemetria. Ad esempio, per optare per gli avvisi di telemetria, il cliente deve definire la connessione che verrà monitorata.

> [!Note]
> Quando ci si iscrive al servizio di peering, vengono analizzati i dati di telemetria di Windows e Microsoft 365 per fornire misure di latenza per i prefissi selezionati.
>Per altre informazioni sulla telemetria della connessione, vedere la [telemetria della connessione al servizio di peering](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Come registrare una connessione

**Scenario** : Supponiamo che una succursale sia distribuita in diverse aree geografiche, come illustrato nella figura seguente. In questo caso, il cliente deve fornire un nome logico, il nome del provider di servizi (SP), la posizione fisica del cliente e i prefissi IP (di proprietà del cliente o allocati dal provider di servizi) associati a una singola connessione. Questo processo deve essere ripetuto per registrare il servizio di peering per connessioni con ridondanza geografica separate.

![Connessioni con ridondanza geografica](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Il livello di stato: la filtrazione viene considerata per la posizione fisica del cliente quando la connessione si trova nella Stati Uniti.
>

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come registrare la connessione al servizio di peering, vedere [registrare il servizio di peering usando il portale di Azure](azure-portal.md).

Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).

Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).
