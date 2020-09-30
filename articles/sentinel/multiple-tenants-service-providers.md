---
title: Gestire più tenant in Azure Sentinel come provider di servizi di sicurezza gestito | Microsoft Docs
description: Come eseguire l'onboarding e gestire più tenant in Azure Sentinel come provider di servizi di sicurezza gestito (MSSP) con Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578148"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Gestire più tenant in Sentinel di Azure come MSSP

Se si è un provider di servizi di sicurezza gestito (MSSP) e si usa [Azure Lighthouse](../lighthouse/overview.md) per offrire ai clienti servizi Operations Center (SOC) per la sicurezza, è possibile gestire le risorse di Azure Sentinel dei clienti direttamente dal tenant di Azure, senza dover connettersi al tenant del cliente. 

## <a name="prerequisites"></a>Prerequisiti

- [Onboarding di Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Per il corretto funzionamento, il tenant (il tenant MSSP) deve avere i provider di risorse di Azure Sentinel registrati in almeno una sottoscrizione. Inoltre, per ogni tenant dei clienti devono essere registrati i provider di risorse. Se Azure Sentinel è stato registrato nel tenant e i clienti si trovano a disposizione, è possibile iniziare. Per verificare la registrazione, seguire questa procedura:

    1. Selezionare **sottoscrizioni** dalla portale di Azure, quindi selezionare una sottoscrizione pertinente dal menu.

    1. Nel menu di navigazione della schermata sottoscrizione, in **Impostazioni**, selezionare **provider di risorse**.

    1. Dal ** *nome della sottoscrizione* | Schermata provider di risorse** , cercare e selezionare *Microsoft. OperationalInsights* e *Microsoft. SecurityInsights*e controllare la colonna **stato** . Se lo stato del provider è *NotRegistered*, selezionare **registra**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Controllare i provider di risorse":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Come accedere a Sentinel di Azure in tenant gestiti

1. In **directory + sottoscrizione**selezionare le directory delegate (directory = tenant) e le sottoscrizioni in cui si trovano le aree di lavoro di Azure Sentinel del cliente.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Controllare i provider di risorse":::

1. Aprire Sentinel di Azure. Verranno visualizzate tutte le aree di lavoro nelle sottoscrizioni selezionate e sarà possibile usarle senza problemi, come qualsiasi area di lavoro nel tenant.

> [!NOTE]
> Non sarà possibile distribuire i connettori in Sentinel di Azure dall'interno di un'area di lavoro gestita. Per distribuire un connettore, è necessario accedere direttamente al tenant in cui si vuole distribuire un connettore ed eseguire l'autenticazione con le autorizzazioni necessarie.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come gestire facilmente più tenant di Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

