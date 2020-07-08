---
title: Failover Video Indexer e ripristino di emergenza
titleSuffix: Azure Media Services
description: Informazioni su come eseguire il failover a un account di Video Indexer secondario se si verifica un errore o un'emergenza del Data Center a livello di area.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79499616"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover Video Indexer e ripristino di emergenza

Servizi multimediali di Azure Video Indexer non fornisce il failover immediato del servizio in caso di interruzione o errore di un Data Center a livello di area. Questo articolo illustra come configurare l'ambiente per un failover per garantire la disponibilità ottimale per le app e il tempo di recupero ridotto a icona in caso di emergenza.

È consigliabile configurare il ripristino di emergenza per la continuità aziendale (BCDR) tra coppie di aree per trarre vantaggio dai criteri di isolamento e disponibilità di Azure. Per altre informazioni, vedere [Aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione di Azure, iscriversi alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Eseguire il failover in un account secondario

Per implementare BCDR, è necessario disporre di due account Video Indexer per gestire la ridondanza.

1. Creare due account Video Indexer connessi ad Azure (vedere [creare un account di video Indexer](connect-to-azure.md)). Creare un account per l'area primaria e l'altro per l'area di Azure abbinata.
1. Se si verifica un errore nell'area primaria, passare all'indicizzazione usando l'account secondario.

> [!TIP]
> È possibile automatizzare BCDR configurando gli avvisi del log attività per le notifiche di integrità del servizio in base alla [creazione di avvisi del log attività nelle notifiche del servizio](../../service-health/alerts-activity-log-service-notifications.md).

Per informazioni sull'uso di più tenant, vedere [gestire più tenant](manage-multiple-tenants.md). Per implementare BCDR, scegliere una di queste due opzioni: [video Indexer account per tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) o [sottoscrizione di Azure per ogni tenant](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Passaggi successivi

[Gestire un account di video Indexer connesso ad Azure](manage-account-connected-to-azure.md).
