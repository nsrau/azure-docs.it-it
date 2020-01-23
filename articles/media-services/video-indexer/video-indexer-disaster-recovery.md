---
title: Video Indexer continuità aziendale e ripristino di emergenza-Azure
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
ms.openlocfilehash: 2f54c340226a9ea78643df8e0a984c8ed8475c94
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513576"
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

Per informazioni sull'uso di più tenant, vedere [gestire più tenant](manage-multiple-tenants.md). Per implementare BCDR, scegliere una di queste due opzioni: [video Indexer account per tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) o [sottoscrizione di Azure per ogni tenant](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Passaggi successivi

[Gestire un account di video Indexer connesso ad Azure](manage-account-connected-to-azure.md).
