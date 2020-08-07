---
title: Autorizzazioni in Azure Advisor
description: Le autorizzazioni di Advisor e il modo in cui possono bloccare la possibilità di configurare le sottoscrizioni o posticipare o ignorare le raccomandazioni.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 3c7b42372ae6de5c2f519b69aec8a154a2f35a82
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927386"
---
# <a name="permissions-in-azure-advisor"></a>Autorizzazioni in Azure Advisor

Azure Advisor fornisce consigli basati sull'utilizzo e la configurazione delle risorse e delle sottoscrizioni di Azure. Advisor usa i [ruoli predefiniti](../role-based-access-control/built-in-roles.md) forniti dal controllo degli [accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) per gestire l'accesso ai consigli e alle funzionalità di Advisor. 

## <a name="roles-and-their-access"></a>Ruoli e relativo accesso

Nella tabella seguente vengono definiti i ruoli e l'accesso in Advisor:

| **Ruolo** | **Visualizza le raccomandazioni** | **Modifica regole** | **Modificare la configurazione della sottoscrizione** | **Modificare la configurazione del gruppo di risorse**| **Ignorare e posticipare le raccomandazioni**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietario della sottoscrizione|**X**|**X**|**X**|**X**|**X**|
|Collaboratore alla sottoscrizione|**X**|**X**|**X**|**X**|**X**|
|Lettore di sottoscrizioni|**X**|--|--|--|--|
|Proprietario del gruppo di risorse|**X**|--|--|**X**|**X**|
|Collaboratore gruppo di risorse|**X**|--|--|**X**|**X**|
|Lettore del gruppo di risorse|**X**|--|--|--|--|
|Proprietario risorsa|**X**|--|--|--|**X**|
|Collaboratore risorse|**X**|--|--|--|**X**|
|Lettore di risorse|**X**|--|--|--|--|

> [!NOTE]
> L'accesso per visualizzare le raccomandazioni dipende dall'accesso alle risorse interessate dalla raccomandazione.

## <a name="permissions-and-unavailable-actions"></a>Autorizzazioni e azioni non disponibili

La mancanza di autorizzazioni appropriate può impedire la possibilità di eseguire azioni in Advisor. Di seguito sono riportati alcuni problemi comuni.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Non è possibile configurare le sottoscrizioni o i gruppi di risorse

Quando si tenta di configurare le sottoscrizioni o i gruppi di risorse in Advisor, è possibile notare che l'opzione da includere o escludere è disabilitata. Questo stato indica che non si dispone di un livello di autorizzazione sufficiente per il gruppo di risorse o la sottoscrizione. Per risolvere questo problema, informazioni su come [concedere un accesso utente](../role-based-access-control/quickstart-assign-role-user-portal.md).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Non è possibile posticipare o ignorare una raccomandazione

Se viene visualizzato un errore durante il tentativo di posticipare o ignorare una raccomandazione, è possibile che non si disponga di autorizzazioni sufficienti. Assicurarsi di avere almeno l'accesso collaboratore alla risorsa interessata della raccomandazione che si sta posticipando o scegliendo. Per risolvere questo problema, informazioni su come [concedere un accesso utente](../role-based-access-control/quickstart-assign-role-user-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha fornito una panoramica del modo in cui Advisor usa il controllo degli accessi in base al ruolo per controllare le autorizzazioni utente e risolvere i problemi comuni Per altre informazioni su Advisor, vedere:

- [Che cos'è Azure Advisor?](./advisor-overview.md)
- [Introduzione ad Azure Advisor](./advisor-get-started.md)
