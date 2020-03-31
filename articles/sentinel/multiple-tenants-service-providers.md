---
title: Usare più tenant di Azure Sentinel per i provider di servizi MSSP Documenti Microsoft
description: Come usare più tenant di Azure Sentinel per i provider di servizi MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476016"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Usare più tenant in Azure SentinelWork with multiple tenants in Azure Sentinel 

Se si è un provider di servizi di sicurezza gestita (MSSP) e si usa [Azure Lighthouse](../lighthouse/overview.md) per gestire i centri operativi di sicurezza (SOC) dei clienti, sarà possibile gestire le risorse di Azure Sentinel dei clienti senza connettersi direttamente al tenant del cliente dal proprio tenant di Azure. 

## <a name="prerequisites"></a>Prerequisiti
- [Faro di bordo di Azure](../lighthouse/how-to/onboard-customer.md)
- Affinché questa operazione funzioni correttamente, il tenant deve essere registrato nel provider di risorse di Azure Sentinel in almeno una sottoscrizione. Se nel tenant è presente un messaggio di Azure Sentinel registrato, è possibile iniziare. In caso contrario, selezionare **Sottoscrizioni** dal portale di Azure, seguito da **Provider di risorse.**  Quindi, dalla schermata **SOC - Provider di risorse,** cercare e selezionare `Microsoft.OperationalInsights` e `Microsoft.SecurityInsights`, quindi selezionare **Registra**.
   ![Controllare i provider di risorseCheck resource providers](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Come accedere ad Azure Sentinel da altri tenantHow to access Azure Sentinel from other tenants
1. In **Directory e sottoscrizione**selezionare le directory delegate e le sottoscrizioni in cui si trovano le aree di lavoro di Azure Sentinel del cliente.

   ![Generare gli eventi imprevisti della sicurezza](media/multiple-tenants-service-providers/directory-subscription.png)

1. Aprire Azure Sentinel.Open Azure Sentinel. Tutte le aree di lavoro verranno visualizzate nelle sottoscrizioni selezionate e sarà possibile utilizzarle senza problemi, ad esempio tutte le aree di lavoro nel tenant.

> [!NOTE]
> Non sarà possibile distribuire connettori in Azure Sentinel dall'interno di un'area di lavoro gestita. Per distribuire un connettore, è necessario accedere direttamente al tenant in cui si desidera distribuire un connettore ed autenticarvi con le autorizzazioni necessarie.





## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato illustrato come gestire senza problemi più tenant di Azure Sentinel.In this document, you learned how to manage multiple Azure Sentinel tenants seamlessly. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

