---
title: Usare più tenant in Sentinel di Azure per i provider di servizi MSSP | Microsoft Docs
description: Come usare più tenant in Sentinel di Azure per i provider di servizi MSSP.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240498"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Usare più tenant in Sentinel di Azure 

Se si è un provider di servizi di sicurezza gestito (MSSP) e si usa [Azure Lighthouse](../lighthouse/overview.md) per gestire il SoC (Security Operations Center) dei clienti, sarà possibile gestire le risorse di Azure Sentinel dei clienti senza connettersi direttamente al tenant del cliente, dal tenant di Azure. 

## <a name="prerequisites"></a>Prerequisiti
- [Onboarding di Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Per il corretto funzionamento, il tenant deve essere registrato nel provider di risorse di Azure Sentinel in almeno una sottoscrizione. Se si dispone di una sentinella di Azure registrata nel tenant, si è pronti per iniziare. In caso contrario, nella portale di Azure selezionare **sottoscrizioni** seguite da **provider di risorse** , `Microsoft.Security.Insights` quindi cercare e selezionare **registra**.
   ![Controllare i provider di risorse](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Come accedere a Sentinel di Azure da altri tenant
1. In **directory + sottoscrizione**selezionare le directory delegate e le sottoscrizioni in cui si trovano le aree di lavoro di Azure Sentinel del cliente.

   ![Genera eventi imprevisti della sicurezza](media/multiple-tenants-service-providers/directory-subscription.png)

1. Aprire Sentinel di Azure. Verranno visualizzate tutte le aree di lavoro nelle sottoscrizioni selezionate e sarà possibile usarle senza problemi, come qualsiasi area di lavoro nel tenant.

> [!NOTE]
> Non sarà possibile distribuire i connettori in Sentinel di Azure dall'interno di un'area di lavoro gestita. Per distribuire un connettore, è necessario accedere direttamente al tenant in cui si vuole distribuire un connettore ed eseguire l'autenticazione con le autorizzazioni necessarie.





## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come gestire facilmente più tenant di Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

