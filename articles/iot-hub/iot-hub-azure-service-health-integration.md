---
title: Controllare integrità risorse e servizio Hub Azure Microsoft Docs
description: Usare l'integrità dei servizi di Azure e Integrità risorse di Azure per monitorare l'hub Internet delle cose
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548518"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Controllare integrità risorse e servizio dell'hub Internet

L'hub Azure è integrato con il [servizio integrità dei servizi di Azure](../service-health/overview.md) per offrire la possibilità di monitorare l'integrità a livello di servizio del servizio hub Internet e degli hub Internet. È anche possibile impostare gli avvisi per ricevere una notifica quando lo stato del servizio hub Internet o dell'hub Internet viene modificato. Il servizio integrità dei servizi di Azure è una combinazione di tre servizi più piccoli: Integrità risorse di Azure, integrità dei servizi di Azure e la pagina relativa allo stato di Azure. Le sezioni di questo articolo descrivono in modo più dettagliato ogni servizio e la relativa relazione con l'hub.

Il servizio integrità dei servizi di Azure consente di monitorare gli eventi a livello di servizio, ad esempio interruzioni e aggiornamenti che potrebbero influire sulla disponibilità del servizio hub Internet e dei singoli hub Internet. L'hub Internet si integra anche con monitoraggio di Azure per fornire le metriche della piattaforma dell'hub Internet e i log delle risorse dell'hub cose che è possibile usare per monitorare le condizioni e gli errori operativi che si verificano in uno specifico Hub. Per altre informazioni, vedere [monitorare l'hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Verificare l'integrità di un hub Internet con Integrità risorse di Azure

Integrità risorse di Azure fa parte del servizio integrità dei servizi di Azure che tiene traccia dell'integrità delle singole risorse. È possibile controllare lo stato di integrità dell'hub Internet delle cose direttamente dal portale.

Per visualizzare la cronologia dello stato e dello stato dell'hub Internet delle cose usando il portale, seguire questa procedura:

1. In [portale di Azure](https://portal.azure.com)passare all'hub Internet delle cose in portale di Azure.

1. Nel riquadro sinistro, in **supporto e risoluzione dei problemi** , selezionare **integrità risorse** .

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Pagina integrità risorse per un hub Internet":::

Per altre informazioni su Integrità risorse di Azure e su come interpretare i dati di integrità, vedere [Panoramica di integrità risorse](../service-health/resource-health-overview.md) nella documentazione sull'integrità dei servizi di Azure.

È anche possibile selezionare **Aggiungi avviso di integrità risorse** per configurare gli avvisi da attivare quando lo stato di integrità dell'hub Internet viene modificato. Per altre informazioni, vedere [configurare gli avvisi per gli eventi di integrità del servizio](../service-health/alerts-activity-log-service-notifications-portal.md) e gli argomenti correlati nella documentazione sull'integrità dei servizi di Azure.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Controllare l'integrità degli hub Internet nella sottoscrizione con integrità dei servizi di Azure

Con l'integrità dei servizi di Azure è possibile verificare lo stato di integrità di tutti gli hub Internet delle cose nella sottoscrizione.

Per controllare l'integrità degli hub IoT, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare a **Service Health** integrità  >  **risorse** servizio integrità.

3. Nelle caselle di riepilogo a discesa selezionare la sottoscrizione e quindi selezionare l' **Hub** Internet come tipo di risorsa.

Per altre informazioni sull'integrità dei servizi di Azure e su come interpretare i dati di integrità, vedere [Panoramica dell'integrità](../service-health/service-health-overview.md) dei servizi nella documentazione sull'integrità dei servizi di Azure.

Per informazioni su come configurare gli avvisi con integrità dei servizi di Azure, vedere [configurare gli avvisi per gli eventi di integrità del servizio](../service-health/alerts-activity-log-service-notifications-portal.md) e gli argomenti correlati nella documentazione sull'integrità dei servizi di Azure.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Controllare l'integrità del servizio hub Internet per area nella pagina stato di Azure

Per visualizzare lo stato dell'hub Internet e altri servizi in base all'intera area, è possibile usare la [pagina stato di Azure](https://status.azure.com/status). Per ulteriori informazioni sulla pagina stato di Azure, vedere [Panoramica sullo stato](../service-health/azure-status-overview.md) di Azure nella documentazione sull'integrità dei servizi di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [servizio integrità dei servizi di Azure](../service-health/overview.md) per informazioni dettagliate su integrità dei servizi di azure, integrità risorse di Azure e pagina stato di Azure.
* Per una descrizione del monitoraggio dell' [Hub Azure.](monitor-iot-hub.md)
