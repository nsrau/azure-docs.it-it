---
title: Configurare il controllo di accesso alla rete
titleSuffix: Azure SignalR Service
description: Configurare il controllo di accesso alla rete per il servizio Azure SignalR.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 78b9859adbcf61ae3a9fc0d72a63d5973f90a487
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302163"
---
# <a name="configure-network-access-control"></a>Configurare il controllo di accesso alla rete

Il servizio Azure SignalR consente di proteggere e controllare il livello di accesso all'endpoint del servizio, in base al tipo di richiesta e al subset di reti usate. Quando vengono configurate le regole di rete, solo le applicazioni che richiedono dati tramite il set di reti specificato possono accedere al servizio Azure SignalR.

Il servizio Azure SignalR dispone di un endpoint pubblico accessibile tramite Internet. È anche possibile creare [endpoint privati per il servizio Azure SignalR](howto-private-endpoints.md). L'endpoint privato assegna un indirizzo IP privato dalla VNet al servizio Azure SignalR e protegge tutto il traffico tra il VNet e il servizio Azure SignalR tramite un collegamento privato. Il controllo di accesso alla rete del servizio Azure SignalR fornisce il controllo di accesso per gli endpoint pubblici e privati.

Facoltativamente, è possibile scegliere di consentire o negare determinati tipi di richieste per endpoint pubblico e ogni endpoint privato. Ad esempio, è possibile bloccare tutte le [connessioni server](signalr-concept-internals.md#server-connections) dall'endpoint pubblico e assicurarsi che provengano solo da un VNet specifico.

Un'applicazione che accede a un servizio di Azure SignalR quando le regole di controllo di accesso alla rete sono attive richiede ancora la corretta autorizzazione per la richiesta.

## <a name="scenario-a---no-public-traffic"></a>Scenario A-nessun traffico pubblico

Per negare completamente tutto il traffico pubblico, è necessario innanzitutto configurare la regola di rete pubblica in modo da non consentire alcun tipo di richiesta. Quindi, è necessario configurare regole che concedano l'accesso al traffico da reti virtuali specifiche. Questa configurazione consente di creare un limite di rete protetto per le applicazioni.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Scenario B: solo connessioni client dalla rete pubblica

In questo scenario è possibile configurare la regola di rete pubblica in modo da consentire solo le [connessioni client](signalr-concept-internals.md#client-connections) dalla rete pubblica. È quindi possibile configurare le regole di rete privata per consentire altri tipi di richieste provenienti da un VNet specifico. Questa configurazione nasconde i server app dalla rete pubblica e stabilisce connessioni sicure tra i server app e il servizio Azure SignalR.

## <a name="managing-network-access-control"></a>Gestione del controllo di accesso alla rete

È possibile gestire il controllo di accesso alla rete per il servizio Azure SignalR tramite il portale di Azure.

### <a name="azure-portal"></a>Portale di Azure

1. Passare al servizio Azure SignalR che si vuole proteggere.

1. Fare clic sul menu impostazioni denominato **controllo di accesso alla rete**.

    ![ACL di rete nel portale](media/howto-network-access-control/portal.png)

1. Per modificare l'azione predefinita, impostare il pulsante **Consenti/Nega** .

    > [!TIP]
    > L'azione predefinita è l'azione che viene eseguita quando non esiste alcuna regola ACL corrispondente. Se, ad esempio, l'azione predefinita è **Deny**, i tipi di richiesta non inclusi in modo esplicito di seguito verranno negati.

1. Per modificare la regola di rete pubblica, selezionare tipi di richieste consentiti in **rete pubblica**.

    ![Modificare l'ACL della rete pubblica nel portale ](media/howto-network-access-control/portal-public-network.png)

1. Per modificare le regole di rete dell'endpoint privato, selezionare i tipi di richieste consentiti in ogni riga in **connessioni a endpoint privati**.

    ![Modificare l'ACL dell'endpoint privato nel portale ](media/howto-network-access-control/portal-private-endpoint.png)

1. Fare clic su **Salva** per applicare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Collegamento privato di Azure](/azure/private-link/private-link-overview).
