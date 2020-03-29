---
title: Autorizzazioni in Azure AdvisorPermissions in Azure Advisor
description: Autorizzazioni advisor e come possono bloccare la possibilità di configurare sottoscrizioni o posticipare o ignorare i consigli.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422326"
---
# <a name="permissions-in-azure-advisor"></a>Autorizzazioni in Azure AdvisorPermissions in Azure Advisor

Azure Advisor offre suggerimenti in base all'utilizzo e alla configurazione delle risorse e delle sottoscrizioni di Azure.Azure Advisor provides recommendations based on the usage and configuration of your Azure resources and subscriptions. Advisor usa i [ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) forniti dal controllo degli accessi in [base](https://docs.microsoft.com/azure/role-based-access-control/overview) al ruolo per gestire l'accesso ai suggerimenti e alle funzionalità di Advisor. 

## <a name="roles-and-their-access"></a>Ruoli e loro accesso

Nella tabella seguente vengono definiti i ruoli e l'accesso di cui dispongono in Advisor:

| **Ruolo** | **Visualizza i consigli** | **Modificare le regole** | **Modificare la configurazione della sottoscrizione** | **Modificare la configurazione del gruppo di risorse**| **Ignorare e rinviare le raccomandazioni**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietario della sottoscrizione|**X**|**X**|**X**|**X**|**X**|
|Collaboratore alla sottoscrizione|**X**|**X**|**X**|**X**|**X**|
|Lettore di abbonamenti|**X**|--|--|--|--|
|Proprietario del gruppo di risorse|**X**|--|--|**X**|**X**|
|Collaboratore del gruppo di risorseResource group Contributor|**X**|--|--|**X**|**X**|
|Lettore del gruppo di risorse|**X**|--|--|--|--|
|Proprietario della risorsa|**X**|--|--|--|**X**|
|Collaboratore risorsa|**X**|--|--|--|**X**|
|Lettore di risorse|**X**|--|--|--|--|

> [!NOTE]
> L'accesso per visualizzare i suggerimenti dipende dall'accesso alla risorsa interessata della raccomandazione.

## <a name="permissions-and-unavailable-actions"></a>Autorizzazioni e azioni non disponibili

La mancanza di autorizzazioni appropriate può bloccare la capacità di eseguire azioni in Advisor. Di seguito sono riportati alcuni problemi comuni.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Impossibile configurare sottoscrizioni o gruppi di risorse

Quando si tenta di configurare sottoscrizioni o gruppi di risorse in Advisor, è possibile che l'opzione per includere o escludere sia disabilitata. Questo stato indica che non si dispone di un livello di autorizzazione sufficiente per il gruppo di risorse o la sottoscrizione. Per risolvere questo problema, informazioni su come concedere a [un utente l'accesso](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Impossibile rinviare o ignorare una raccomandazione

Se si riceve un errore quando si tenta di posticipare o ignorare un suggerimento, è possibile che non si disponga di autorizzazioni sufficienti. Assicurarsi di disporre almeno dell'accesso del collaboratore alla risorsa interessata della raccomandazione che si sta rinviando o ignorando. Per risolvere questo problema, informazioni su come concedere a [un utente l'accesso](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata fornita una panoramica di come Advisor usa il controllo degli accessi in base al ruolo per controllare le autorizzazioni utente e come risolvere i problemi comuni. Per altre informazioni su Advisor, vedere:

- [Informazioni su Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Introduzione ad Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
