---
title: Usare i tag del servizio
titleSuffix: Azure SignalR Service
description: Usare i tag di servizio per consentire il traffico in uscita al servizio Azure SignalR
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84302103"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Usare i tag di servizio per il servizio Azure SignalR

È possibile usare i [tag di servizio](../virtual-network/security-overview.md#service-tags) per il servizio Azure SignalR durante la configurazione del gruppo di sicurezza di [rete](../virtual-network/security-overview.md#network-security-groups). Consente di definire la regola di sicurezza di rete in uscita per gli endpoint del servizio Azure SignalR senza che sia necessario impostare come hardcoded gli indirizzi IP.

Il servizio Azure SignalR gestisce questi tag del servizio. Non è possibile creare un tag di servizio personalizzato o modificarne uno esistente. Microsoft gestisce questi prefissi di indirizzo che corrispondono al tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

## <a name="use-service-tag-on-portal"></a>Usare un tag di servizio nel portale

È possibile consentire il traffico in uscita al servizio Azure SignalR aggiungendo una nuova regola di sicurezza di rete in uscita:

1. Passare al gruppo di sicurezza di rete.

1. Fare clic sul menu impostazioni denominato **regole di sicurezza in uscita**.

1. Fare clic sul pulsante **+ Aggiungi** nella parte superiore.

1. Scegliere il **tag del servizio** in **destinazione**.

1. Scegliere **AzureSignalR** in **tag del servizio di destinazione**.

1. Compilare **443** negli **intervalli di porte di destinazione**.

    ![Creare una regola di sicurezza in uscita](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Modificare gli altri campi in base alle esigenze.

1. Scegliere **Aggiungi**.


## <a name="next-steps"></a>Passaggi successivi

- [Gruppi di sicurezza di rete: Tag del servizio](../virtual-network/security-overview.md#security-rules)
