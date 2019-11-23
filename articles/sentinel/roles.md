---
title: Autorizzazioni in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra come Azure Sentinel usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identifica le azioni consentite per ogni ruolo.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0bf95b499a7366dad1e7b78fa4298aa6a42bb5fe
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316791"
---
# <a name="permissions-in-azure-sentinel"></a>Autorizzazioni in Sentinel di Azure

Azure Sentinel usa il [controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/role-assignments-portal.md)per fornire [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Con il controllo degli accessi in base al ruolo, è possibile usare e creare ruoli all'interno del team operativo di sicurezza per concedere l'accesso appropriato ad Azure Sentinel In base ai ruoli, si ha un controllo accurato sugli elementi che possono essere visualizzati dagli utenti con accesso a Sentinel di Azure. È possibile assegnare i ruoli RBAC direttamente nell'area di lavoro di Azure Sentinel oppure a una sottoscrizione o a un gruppo di risorse a cui appartiene l'area di lavoro.

Sono disponibili tre ruoli di Azure Sentinel predefiniti specifici.  
**Tutti i ruoli predefiniti di Sentinel di Azure concedono l'accesso in lettura ai dati nell'area di lavoro di Azure Sentinel.**
- **Azure Sentinel Reader**: un utente assegnato a questo ruolo dispone dei diritti di visualizzazione per Azure Sentinel. L'utente può visualizzare gli eventi imprevisti e i dati, ma non apportare modifiche.
- **Risponditore sentinella di Azure**: un utente assegnato a questo ruolo può leggere ed eseguire azioni su eventi imprevisti, ad esempio modifiche di assegnazione e gravità.
- **Collaboratore sentinella di Azure**: un utente assegnato a questo ruolo può leggere ed eseguire azioni su eventi imprevisti e creare ed eliminare le regole analitiche.

Oltre ai ruoli di controllo degli accessi in base al ruolo dedicati di Azure Sentinel, sono disponibili ruoli RBAC di Azure e Log Analytics che possono concedere un set più ampio di autorizzazioni che includono l'accesso all'area di lavoro di Azure Sentinel e ad altre risorse:

- **Ruoli di Azure:** [proprietario](../role-based-access-control/built-in-roles.md#owner), [collaboratore](../role-based-access-control/built-in-roles.md#contributor)e [lettore](../role-based-access-control/built-in-roles.md#reader). I ruoli di Azure concedono l'accesso a tutte le risorse di Azure, tra cui Log Analytics aree di lavoro e risorse di Azure Sentinel.

-   **Ruoli di log Analytics:** [log Analytics collaboratore](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics ruoli concedono l'accesso a tutte le aree di lavoro di Log Analytics. 

> [!NOTE]
> I ruoli Log Analytics concedere anche l'accesso in lettura a tutte le risorse di Azure, ma solo le autorizzazioni di scrittura per Log Analytics risorse.


Ad esempio, un utente assegnato con i ruoli **Reader di Azure sentinella** e **collaboratore di Azure** (non **collaboratore sentinelle di Azure**) potrà modificare i dati in Sentinel di Azure, anche se dispone solo delle autorizzazioni di **lettura Sentinel** . Se pertanto si desidera concedere le autorizzazioni a un solo in Sentinel di Azure, è necessario rimuovere attentamente le autorizzazioni precedenti di questo utente assicurandosi di non interrompere il ruolo di autorizzazione necessario per un'altra risorsa.

> [!NOTE]
>- Azure Sentinel USA PlayBook per la risposta automatica alle minacce. I PlayBook sfruttano le app per la logica di Azure e sono una risorsa di Azure separata. Potrebbe essere necessario assegnare membri specifici del team delle operazioni di sicurezza con l'opzione di usare app per la logica per le operazioni di orchestrazione, automazione e risposta (SOAR) di sicurezza. È possibile usare il ruolo [collaboratore app](../role-based-access-control/built-in-roles.md#logic-app-contributor) per la logica o il ruolo [operatore app](../role-based-access-control/built-in-roles.md#logic-app-operator) per la logica per assegnare autorizzazioni esplicite per l'uso di PlayBook.
>- Per aggiungere connettori dati, i ruoli necessari per ogni connettore sono per tipo di connettore e sono elencati nella pagina connettore pertinente. Inoltre, per connettere qualsiasi origine dati, è necessario disporre dell'autorizzazione di scrittura per l'area di lavoro di Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente mostra i ruoli e le azioni consentite in Sentinel di Azure. Un simbolo X indica che l'azione è consentita per il ruolo.

| Ruolo | Creare ed eseguire PlayBook| Creare e modificare dashboard, regole analitiche e altre risorse di Azure Sentinel | Gestire gli eventi imprevisti (Ignora, assegna e così via) | Visualizza dati, eventi imprevisti, dashboard e altre risorse di Azure Sentinel |
|--- |---|---|---|---|
| Lettore sentinella di Azure | -- | -- | -- | X |
| Risponditore sentinella di Azure|--|--| X | X |
| Collaboratore sentinella di Azure | -- | X | X | X |
| Collaboratore Sentinel di Azure + collaboratore app per la logica | X | X | X | X |


> [!NOTE]
> - È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Ad esempio, assegnare il ruolo Collaboratore sentinella di Azure solo agli utenti che devono creare regole o dashboard.
> - Si consiglia di impostare le autorizzazioni per Azure Sentinel nell'ambito del gruppo di risorse, in modo che l'utente possa accedere a tutte le aree di lavoro di Sentinel di Azure nello stesso gruppo di risorse.
>
## <a name="building-custom-rbac-roles"></a>Creazione di ruoli RBAC personalizzati

Oltre a, o invece di usare i ruoli RBAC predefiniti, è possibile creare ruoli RBAC personalizzati per Azure Sentinel. I ruoli di controllo degli accessi in base al ruolo personalizzati per Azure Sentinel vengono creati nello stesso modo in cui si creano altri ruoli [personalizzati](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) di controllo degli accessi in base al ruolo di Azure.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Controllo degli accessi in base al ruolo avanzato nei dati archiviati in Sentinel di Azure
  
È possibile usare l'Log Analytics controllo degli accessi in base al ruolo avanzato tra i dati nell'area di lavoro di Azure Sentinel. Sono inclusi il controllo degli accessi in base al ruolo per tipo di dati e il controllo degli accessi in base al ruolo incentrato sulle risorse. Per altre informazioni sui ruoli di Log Analytics, vedere [gestire i dati di log e le aree di lavoro in monitoraggio di Azure](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare i ruoli per gli utenti di Sentinel di Azure e il ruolo che consente agli utenti di eseguire.

* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
