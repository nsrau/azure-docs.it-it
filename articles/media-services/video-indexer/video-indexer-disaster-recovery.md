---
title: Video Indexer continuità aziendale e ripristino di emergenza (BCDR)-Azure | Microsoft Docs
description: Informazioni su come eseguire il failover a un account di Video Indexer secondario se si verifica un'interruzione del Data Center a livello di area o un errore.
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
ms.openlocfilehash: ce788b4640f0a6c6f25b3280ce4f52fd018d1699
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668309"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Gestire Video Indexer continuità aziendale e il ripristino di emergenza

Servizi multimediali di Azure Video Indexer non fornisce il failover immediato del servizio in caso di interruzione o errore di un Data Center a livello di area. Questo articolo illustra come configurare l'ambiente per un failover per garantire la disponibilità ottimale per le applicazioni e il tempo di recupero ridotto a icona in caso di emergenza.

È consigliabile configurare la continuità aziendale e il ripristino di emergenza (BCDR) tra le coppie di aree per sfruttare i vantaggi dei criteri di isolamento e disponibilità di Azure. Per altre informazioni, vedere [Aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Prerequisiti 

Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione di Azure, iscriversi alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Eseguire il failover in un account secondario

Per implementare BCDR, è necessario disporre di due account Video Indexer per gestire la ridondanza.

1. Creare due account Video Indexer connessi ad Azure (vedere [creare account](connect-to-azure.md)). Uno per l'area primaria e l'altro per l'area di Azure abbinata. 
1. Se si verifica un errore nell'area primaria, passare all'indicizzazione utilizzando l'account secondario.

> [!TIP]
> È possibile automatizzare BCDR configurando gli avvisi del log attività per le notifiche di integrità del servizio in base alla [creazione di avvisi del log attività nelle notifiche del servizio](../../service-health/alerts-activity-log-service-notifications.md).

Per informazioni sull'uso di più tenant, vedere [gestire più tenant](manage-multiple-tenants.md). Per implementare BCDR, scegliere una delle due opzioni seguenti: [Video Indexer account per tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) o [sottoscrizione di Azure per ogni tenant](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Passaggi successivi

[Gestire un account di video Indexer connesso ad Azure](manage-account-connected-to-azure.md).
