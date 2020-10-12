---
title: Failover e ripristino di emergenza di Video Indexer
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
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065406"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza di Video Indexer

Video Indexer di Servizi multimediali di Azure non consente di avere il failover immediato del servizio in caso di interruzione o errore di un data center a livello di area. Questo articolo illustra come configurare l'ambiente per un failover per garantire la disponibilità ottimale per le app e il tempo di recupero ridotto a icona in caso di emergenza.

È consigliabile configurare la continuità aziendale e il ripristino di emergenza (BCDR) tra le coppie di aree per sfruttare i vantaggi dei criteri di isolamento e disponibilità di Azure. Per altre informazioni, vedere [Aree abbinate di Azure](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione di Azure, iscriversi alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Eseguire il failover in un account secondario

Per implementare BCDR, è necessario disporre di due account Video Indexer per gestire la ridondanza.

1. Creare due account Video Indexer connessi ad Azure (vedere [creare un account di video Indexer](connect-to-azure.md)). Creare un account per l'area primaria e l'altro per l'area di Azure abbinata.
1. Se si verifica un errore nell'area primaria, passare all'indicizzazione usando l'account secondario.

> [!TIP]
> È possibile automatizzare BCDR configurando gli avvisi del log attività per le notifiche di integrità del servizio in base alla [creazione di avvisi del log attività nelle notifiche del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md).

Per informazioni sull'uso di più tenant, vedere [gestire più tenant](manage-multiple-tenants.md). Per implementare BCDR, scegliere una di queste due opzioni: [video Indexer account per tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) o [sottoscrizione di Azure per ogni tenant](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Passaggi successivi

[Gestire un account di video Indexer connesso ad Azure](manage-account-connected-to-azure.md).
