---
title: Autorizzazioni in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra come Azure Sentinel usa il controllo degli accessi in base al ruolo di Azure per assegnare autorizzazioni agli utenti e identifica le azioni consentite per ogni ruolo.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: 9c100ca82b751a050e74cce85e1794701a789288
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95498470"
---
# <a name="permissions-in-azure-sentinel"></a>Autorizzazioni in Azure Sentinel

Azure Sentinel usa il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md) per fornire [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Usare il controllo degli accessi in base al ruolo di Azure per creare e assegnare ruoli all'interno del team operativo di sicurezza per concedere l'accesso appropriato a Sentinel I diversi ruoli consentono di controllare con precisione i vantaggi che gli utenti di Azure Sentinel possono vedere e fare. I ruoli di Azure possono essere assegnati direttamente all'area di lavoro di Azure Sentinel (vedere la nota di seguito) o in una sottoscrizione o in un gruppo di risorse a cui appartiene l'area di lavoro, che verrà ereditata da Azure Sentinel.

## <a name="roles-for-working-in-azure-sentinel"></a>Ruoli per l'uso in Sentinel di Azure

### <a name="azure-sentinel-specific-roles"></a>Ruoli specifici di Azure Sentinel

Sono disponibili tre ruoli predefiniti Sentinel di Azure.

**Tutti i ruoli predefiniti di Sentinel di Azure concedono l'accesso in lettura ai dati nell'area di lavoro di Azure Sentinel.**

- Il [lettore di Sentinel di Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) può visualizzare dati, eventi imprevisti, cartelle di lavoro e altre risorse di Azure Sentinel.

- Il [risponditore sentinella di Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) può, oltre a quanto sopra, gestire gli eventi imprevisti (assegna, ignora e così via)

- Il [collaboratore sentinella di Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) può, oltre a quanto sopra, creare e modificare cartelle di lavoro, regole di analisi e altre risorse di Azure Sentinel.

> [!NOTE]
>
> - Per ottenere risultati ottimali, è necessario assegnare questi ruoli al **gruppo di risorse** che contiene l'area di lavoro di Azure Sentinel. In questo modo, i ruoli verranno applicati a tutte le risorse distribuite per supportare Azure Sentinel, perché tali risorse devono essere inserite anche nello stesso gruppo di risorse.
>
> - Un'altra opzione consiste nell'assegnare i ruoli direttamente nell'area di **lavoro** di Azure Sentinel. In tal caso, è necessario assegnare anche gli stessi ruoli nella **risorsa della soluzione** SecurityInsights in tale area di lavoro. Potrebbe essere necessario assegnarli anche ad altre risorse e sarà necessario gestire costantemente le assegnazioni di ruolo sulle risorse.

### <a name="additional-roles-and-permissions"></a>Ruoli e autorizzazioni aggiuntivi

È possibile che agli utenti con requisiti specifici del processo siano assegnati ruoli aggiuntivi o autorizzazioni specifiche per completare le attività.

- Uso dei PlayBook per automatizzare le risposte alle minacce

    Azure Sentinel USA **PlayBook** per la risposta automatica alle minacce. I PlayBook sono basati sulle **app** per la logica di Azure e sono una risorsa di Azure separata. Potrebbe essere necessario assegnare a membri specifici del team delle operazioni di sicurezza la possibilità di usare app per la logica per le operazioni di orchestrazione, automazione e risposta (SOAR) di sicurezza. È possibile usare il ruolo [collaboratore app](../role-based-access-control/built-in-roles.md#logic-app-contributor) per la logica o il ruolo [operatore app](../role-based-access-control/built-in-roles.md#logic-app-operator) per la logica per assegnare autorizzazioni esplicite per l'uso di PlayBook.

- Connessione di origini dati ad Azure Sentinel

    Per aggiungere **connettori dati**, è necessario assegnare le autorizzazioni di scrittura utente nell'area di lavoro di Azure Sentinel. Si notino anche le autorizzazioni aggiuntive necessarie per ogni connettore, come elencato nella pagina del connettore pertinente.

- Utenti guest che assegnano eventi imprevisti

    Se un utente guest deve essere in grado di assegnare gli eventi imprevisti, oltre al ruolo Risponditore sentinella di Azure, è necessario che all'utente sia assegnato anche il ruolo di [lettore di directory](../active-directory/roles/permissions-reference.md#directory-readers). Si noti che questo ruolo *non* è un ruolo di Azure, ma un ruolo **Azure Active Directory** e che questo ruolo è assegnato per impostazione predefinita a utenti regolari (non Guest). 

Per un confronto affiancato, vedere la [tabella seguente](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>Altri ruoli che potrebbero essere stati assegnati

Quando si assegnano ruoli di Azure specifici di Azure Sentinel, è possibile che si trovino altri ruoli di Azure e Log Analytics Azure che potrebbero essere stati assegnati agli utenti per altri scopi. È necessario tenere presente che questi ruoli concedono un set di autorizzazioni più ampio che include l'accesso all'area di lavoro di Azure Sentinel e ad altre risorse:

- **Ruoli di Azure:** [proprietario](../role-based-access-control/built-in-roles.md#owner), [collaboratore](../role-based-access-control/built-in-roles.md#contributor)e [lettore](../role-based-access-control/built-in-roles.md#reader). I ruoli di Azure concedono l'accesso a tutte le risorse di Azure, tra cui Log Analytics aree di lavoro e risorse di Azure Sentinel.

- **Ruoli di log Analytics:** [log Analytics collaboratore](../role-based-access-control/built-in-roles.md#log-analytics-contributor) e [lettore di log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics ruoli concedono l'accesso alle aree di lavoro Log Analytics. 

Ad esempio, un utente a cui viene assegnato il ruolo di **lettore Sentinel di Azure** , ma non il ruolo **collaboratore sentinella di Azure** , potrà comunque modificare gli elementi in Sentinel di Azure se è stato assegnato il ruolo **collaboratore** a livello di Azure. Se pertanto si desidera concedere le autorizzazioni a un utente solo in Sentinel di Azure, è necessario rimuovere attentamente le autorizzazioni precedenti di questo utente, assicurandosi di non interrompere l'accesso necessario a un'altra risorsa.

## <a name="roles-and-allowed-actions"></a>Ruoli e azioni consentite

La tabella seguente riepiloga i ruoli e le azioni consentite in Azure Sentinel. 

| Ruolo | Creare ed eseguire playbook| Creare e modificare cartelle di lavoro, regole analitiche e altre risorse di Azure Sentinel | Gestire gli eventi imprevisti (Ignora, assegna e così via) | Visualizzare dati, eventi imprevisti, cartelle di lavoro e altre risorse di Azure Sentinel |
|---|---|---|---|---|
| Ruolo con autorizzazioni di lettura di Azure Sentinel | -- | -- | -- | &#10003; |
| Risponditore di Azure Sentinel | -- | -- | &#10003; | &#10003; |
| Collaboratore di Azure Sentinel | -- | &#10003; | &#10003; | &#10003; |
| Collaboratore Sentinel di Azure + collaboratore app per la logica | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-azure-rbac"></a>Ruoli personalizzati e controllo degli accessi in base al ruolo avanzato di Azure

- Oltre a, o invece di usare i ruoli predefiniti di Azure, è possibile creare ruoli personalizzati di Azure per Sentinel di Azure. I ruoli personalizzati di Azure per Sentinel di Azure vengono creati nello stesso modo in cui si creano altri [ruoli personalizzati di Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), in base a [autorizzazioni specifiche per Sentinel di Azure](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e ad [Azure log Analytics risorse](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- È possibile usare il controllo degli accessi in base al ruolo di Azure Log Analytics nei dati nell'area di lavoro di Azure Sentinel. Sono inclusi sia il controllo degli accessi in base al tipo di dati che il controllo delle risorse di Azure. Per altre informazioni sui ruoli di Log Analytics, vedere [gestire i dati di log e le aree di lavoro in monitoraggio di Azure](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare i ruoli per gli utenti di Sentinel di Azure e il ruolo che consente agli utenti di eseguire.

* [Blog di Sentinel di Azure](https://aka.ms/azuresentinelblog). Post di blog sulla sicurezza e sulla conformità di Azure.
