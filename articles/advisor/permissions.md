---
title: Autorizzazioni in Azure Advisor
description: Le autorizzazioni di Advisor e come si può bloccare la possibilità di configurare le sottoscrizioni o posporre o ignorare le raccomandazioni.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467613"
---
# <a name="permissions-in-azure-advisor"></a>Autorizzazioni in Azure Advisor

Azure Advisor fornisce consigli basati sull'utilizzo e configurazione delle risorse di Azure e le sottoscrizioni. Advisor Usa la [ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) fornito da [Role-Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) per gestire l'accesso alle funzionalità di preparazione e le raccomandazioni. 

## <a name="roles-and-their-access"></a>I ruoli e il relativo accesso

Nella tabella seguente definisce i ruoli e accedere con privilegi all'interno di Advisor:

| **Ruolo** | **Visualizza gli elementi consigliati** | **Modificare le regole** | **Modificare la configurazione della sottoscrizione** | **Modifica di configurazione del gruppo di risorse**| **Chiudere e posticipare le raccomandazioni**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietario della sottoscrizione|**X**|**X**|**X**|**X**|**X**|
|Collaboratore alla sottoscrizione|**X**|**X**|**X**|**X**|**X**|
|Lettura per la sottoscrizione|**X**|--|--|--|--|
|Gruppo di risorse proprietario|**X**|--|--|**X**|**X**|
|Gruppo di risorse per i collaboratori|**X**|--|--|**X**|**X**|
|Gruppo di risorse lettore|**X**|--|--|--|--|
|Proprietario della risorsa|**X**|--|--|--|**X**|
|Collaboratore di risorse|**X**|--|--|--|**X**|
|Lettore di risorse|**X**|--|--|--|--|

> [!NOTE]
> L'accesso alla risorsa interessata dell'indicazione dipende accesso per visualizzare le raccomandazioni.

## <a name="permissions-and-unavailable-actions"></a>Le autorizzazioni e le azioni non disponibili

Mancanza di autorizzazioni appropriate può bloccare la possibilità di eseguire azioni in Advisor. Di seguito sono alcuni problemi comuni.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Non è possibile configurare le sottoscrizioni o gruppi di risorse

Quando si tenta di configurare le sottoscrizioni o gruppi di risorse in Advisor, è probabile che l'opzione per includere o escludere è disabilitata. Questo stato indica che non è un livello sufficiente di autorizzazioni per tale gruppo di risorse o sottoscrizione. Per risolvere questo problema, informazioni su come [concedere un accesso utente](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Non è possibile posporre o ignorare un Consiglio

Se si riceve un errore durante il tentativo posporre o ignorare un Consiglio, potrebbe non disporre delle autorizzazioni necessarie. Assicurarsi di avere almeno l'accesso collaboratore alla risorsa interessata della raccomandazione si posticipare o eliminazione. Per risolvere questo problema, informazioni su come [concedere un accesso utente](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha offerto una panoramica dell'utilizzo Advisor RBAC per controllare le autorizzazioni utente e su come risolvere i problemi comuni. Per altre informazioni su Advisor, vedere:

- [Informazioni su Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Introduzione ad Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
