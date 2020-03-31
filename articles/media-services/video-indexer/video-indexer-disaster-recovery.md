---
title: Failover dell'indicizzatore video e ripristino di emergenza
titleSuffix: Azure Media Services
description: Informazioni su come eseguire il failover a un account dell'indicizzatore video secondario se si verifica un errore o un'emergenza del data center regionale.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499616"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover dell'indicizzatore video e ripristino di emergenza

L'indicizzatore video di Servizi multimediali di Azure non fornisce il failover immediato del servizio in caso di interruzione o errore del data center regionale. In questo articolo viene illustrato come configurare l'ambiente per un failover per garantire la disponibilità ottimale per le app e ridurre al minimo i tempi di ripristino in caso di emergenza.

È consigliabile configurare il ripristino di emergenza della continuità aziendale (BCDR) tra coppie di aree per trarre vantaggio dai criteri di isolamento e disponibilità di Azure.We recommend that you configure business continuity disaster recovery (BCDR) across regional pairs to benefit from Azure's isolation and availability policies. Per altre informazioni, vedere [Aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, iscriversi alla versione di valutazione gratuita di Azure.If you don't have an Azure subscription at have an Azure subscription to, sign up for [Azure free trial](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Failover su un account secondario

Per implementare BCDR, è necessario disporre di due account dell'indicizzatore video per gestire la ridondanza.

1. Creare due account dell'indicizzatore video connessi ad Azure (vedere [Creare un account dell'indicizzatore video).](connect-to-azure.md) Creare un account per l'area primaria e l'altro per l'area di azure abbinata.
1. Se si verifica un errore nell'area primaria, passare all'indicizzazione usando l'account secondario.

> [!TIP]
> È possibile automatizzare BCDR impostando avvisi del log attività per le notifiche di integrità del servizio in base a Creare avvisi del log attività nelle notifiche del [servizio.](../../service-health/alerts-activity-log-service-notifications.md)

Per informazioni sull'utilizzo di più tenant, vedere [Gestire più tenant.](manage-multiple-tenants.md) Per implementare BCDR, scegliere una di queste due opzioni: [Account indicizzatore video per tenant](manage-multiple-tenants.md#video-indexer-account-per-tenant) o Sottoscrizione di Azure per [tenant.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Passaggi successivi

[Gestire un account dell'indicizzatore video connesso ad Azure.](manage-account-connected-to-azure.md)
