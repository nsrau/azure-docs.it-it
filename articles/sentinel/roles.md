---
title: Autorizzazioni in Azure Sentinel Documenti Microsoft
description: Questo articolo illustra come Azure Sentinel usa il controllo degli accessi in base al ruolo per assegnare autorizzazioni agli utenti e identifica le azioni consentite per ogni ruolo.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587176"
---
# <a name="permissions-in-azure-sentinel"></a>Autorizzazioni in Azure SentinelPermissions in Azure Sentinel

Azure Sentinel usa il controllo degli accessi in base al ruolo per fornire ruoli predefiniti che possono essere assegnati a utenti, gruppi e servizi in Azure.Azure Sentinel uses [Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md), to provide [built-in roles](../role-based-access-control/built-in-roles.md) that can be assigned to users, groups, and services in Azure.

Usando il controllo degli accessi in base al ruolo, è possibile usare e creare ruoli all'interno del team delle operazioni di sicurezza per concedere l'accesso appropriato ad Azure Sentinel.Using RBAC, you can use and create roles within your security operations team to grant appropriate access to Azure Sentinel. In base ai ruoli, è possibile controllare correttamente ciò che gli utenti con accesso ad Azure Sentinel possono vedere. È possibile assegnare ruoli RBAC nell'area di lavoro di Azure Sentinel direttamente o a una sottoscrizione o a un gruppo di risorse a cui appartiene l'area di lavoro.

Esistono tre ruoli predefiniti di Azure Sentinel.There are three specific built-in Azure Sentinel roles.  
**Tutti i ruoli predefiniti di Azure Sentinel concedono l'accesso in lettura ai dati nell'area di lavoro di Azure Sentinel.All Azure Sentinel built-in roles grant read access to the data in your Azure Sentinel workspace.**
- [Lettore Sentinel di AzureAzure Sentinel reader](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Risponditore di Azure SentinelAzure Responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Collaboratore di Azure SentinelAzure Sentinel contributor](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Oltre ai ruoli RBAC dedicati di Azure Sentinel, esistono ruoli RBAC di Azure e Log Analytics che possono concedere un set più ampio di autorizzazioni che includono l'accesso all'area di lavoro di Azure Sentinel e ad altre risorse:In addition to Azure Sentinel dedicated RBAC roles, there are azure and Log Analytics RBAC roles that can grant a wider set of permissions that include access to your Azure Sentinel workspace and other resources:

- **Ruoli di Azure:** [Proprietario](../role-based-access-control/built-in-roles.md#owner), [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)e [Lettore](../role-based-access-control/built-in-roles.md#reader). I ruoli di Azure concedono l'accesso a tutte le risorse di Azure, incluse le aree di lavoro di Log Analytics e le risorse di Azure Sentinel.Azure roles grant access across all your Azure resources, including Log Analytics workspaces and Azure Sentinel resources.

-   **Ruoli di Log Analytics:** [collaboratore](../role-based-access-control/built-in-roles.md#log-analytics-contributor)di Log Analytics , [lettore di Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). I ruoli di Log Analytics concedono l'accesso in tutte le aree di lavoro di Log Analytics.Log Analytics roles grant access across all your Log Analytics workspaces. 

> [!NOTE]
> I ruoli di Log Analytics concedono anche l'accesso in lettura a tutte le risorse di Azure, ma assegneranno solo le autorizzazioni di scrittura alle risorse di Log Analytics.Log Analytics roles also grant read access across all Azure resources but Will only assign write permissions to Log Analytics resources.


Ad esempio, un utente assegnato con i ruoli Lettore di Azure Sentinel e **collaboratore** di Azure (non collaboratore di Azure Sentinel), sarà in grado di modificare i dati in Azure Sentinel, anche se dispongono solo delle autorizzazioni di lettore Sentinel.For example, a user who is assigned with **Azure Sentinel reader** and Azure contributor (not **Azure Sentinel contributor)** roles, will be able to edit data in Azure Sentinel, although they they only have **Sentinel reader** permissions. Pertanto, se si desidera concedere autorizzazioni solo in Azure Sentinel, è necessario rimuovere con attenzione le autorizzazioni precedenti dell'utente assicurandosi di non interrompere qualsiasi ruolo di autorizzazione necessario per un'altra risorsa.

> [!NOTE]
>- Azure Sentinel usa i playbook per la risposta automatica alle minacce. I Playbook sfruttano le app per la logica di Azure e sono una risorsa di Azure separata. È possibile assegnare membri specifici del team delle operazioni di sicurezza con l'opzione per usare le app per la logica per le operazioni di orchestrazione, automazione e risposta (SOAR). È possibile usare il ruolo di [collaboratore App per](../role-based-access-control/built-in-roles.md#logic-app-contributor) la logica o il ruolo [di operatore App per](../role-based-access-control/built-in-roles.md#logic-app-operator) la logica per assegnare l'autorizzazione esplicita per l'utilizzo di playbook.
>- Per aggiungere connettori dati, i ruoli necessari per ogni connettore sono per tipo di connettore e sono elencati nella pagina del connettore pertinente. Inoltre, per connettere qualsiasi origine dati, è necessario disporre dell'autorizzazione di scrittura nell'area di lavoro di Azure Sentinel.In addition, in order to connect any data source, you must have write permission on the Azure Sentinel workspace.



## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

Nella tabella seguente vengono visualizzati i ruoli e le azioni consentite in Azure Sentinel.The following table displays roles and allowed actions in Azure Sentinel. Un simbolo X indica che l'azione è consentita per il ruolo.

| Ruolo | Creare ed eseguire playbook| Creare e modificare dashboard, regole analitiche e altre risorse di Azure SentinelCreate and edit dashboards, analytic rules, and other Azure Sentinel resources | Gestire gli incidenti (ignorare, assegnare, ecc.) | Visualizzare dati, eventi imprevisti, dashboard e altre risorse di Azure SentinelView data, incidents, dashboards and other Azure Sentinel resources |
|--- |---|---|---|---|
| Lettore Sentinel di AzureAzure Sentinel reader | -- | -- | -- | X |
| Risponditore di Azure SentinelAzure Responder|--|--| X | X |
| Collaboratore di Azure SentinelAzure Sentinel contributor | -- | X | X | X |
| Collaboratore di Azure Sentinel - Collaboratore dell'app per la logicaAzure Sentinel contributor - Logic App contributor | X | X | X | X |


> [!NOTE]
> - È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Ad esempio, assegnare il ruolo di collaboratore di Azure Sentinel solo agli utenti che devono creare regole o dashboard.
> - È consigliabile impostare le autorizzazioni per Azure Sentinel nell'ambito del gruppo di risorse, in modo che l'utente possa avere accesso a tutte le aree di lavoro di Azure Sentinel nello stesso gruppo di risorse.
>
## <a name="building-custom-rbac-roles"></a>Creazione di ruoli Controllo degli accessi in base al ruolo personalizzati

Oltre a o al posto di usare i ruoli RBAC incorporati, è possibile creare ruoli RBAC personalizzati per Azure Sentinel.In addition to, or instead of, using built-in RBAC roles, you can create custom RBAC roles for Azure Sentinel. I ruoli RBAC personalizzati per Azure Sentinel vengono creati nello stesso modo in cui si creano altri ruoli RBAC di [Azure personalizzati,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) in base [a autorizzazioni specifiche per Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e per le risorse di [Azure Log Analytics.](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Controllo degli accessi in base al ruolo avanzato nei dati archiviati in Azure Sentinel
  
È possibile usare il controllo avanzato degli accessi ai ruoli di Log Analytics tra i dati nell'area di lavoro di Azure Sentinel.You can use the Log Analytics advanced role-based access control across the data in your Azure Sentinel workspace. Sono inclusi sia il controllo degli accessi in base al ruolo per ogni tipo di dati che il controllo degli accessi in base al ruolo incentrato sulle risorse. Per altre informazioni sui ruoli di Log Analytics, vedere Gestire i dati di log e le aree di lavoro in Monitoraggio di Azure.For more information on Log Analytics roles, see [Manage log data and workspaces in Azure Monitor.](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato illustrato come usare i ruoli per gli utenti di Azure Sentinel e le operazioni che ogni ruolo consente agli utenti di eseguire.

* [Blog di Azure Sentinel](https://aka.ms/azuresentinelblog). Post di blog sulla sicurezza e sulla conformità di Azure.
