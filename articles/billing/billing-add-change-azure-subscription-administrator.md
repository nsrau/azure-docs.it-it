---
title: Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure | Microsoft Docs
description: Descrive come aggiungere o modificare un amministratore di una sottoscrizione di Azure mediante il controllo degli accessi in base al ruolo.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 6cc965f8e775e02e9dec9f610516739a9a2c1936
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127718"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure

Per gestire l'accesso alle risorse di Azure, è necessario il ruolo di amministratore appropriato. In Azure è disponibile un sistema di autorizzazione, denominato controllo degli accessi in base al ruolo, con diversi ruoli predefiniti tra cui è possibile scegliere. È possibile assegnare questi ruoli in ambiti diversi, ad esempio gruppo di gestione, sottoscrizione o gruppo di risorse.

Microsoft consiglia di gestire l'accesso alle risorse usando il controllo degli accessi in base al ruolo. Se però si sta ancora usando il modello di distribuzione classico e si stanno gestendo le risorse classiche tramite il [modulo PowerShell di gestione del servizio Azure](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure), sarà necessario usare un amministratore classico. 

> [!TIP]
> Se si usa il portale di Azure solo per gestire le risorse classiche, non è necessario usare l'amministratore classico.

Per altre informazioni, vedere [Distribuzione Azure Resource Manager o distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md) e [Amministratori della sottoscrizione classica di Azure](../role-based-access-control/classic-administrators.md).

Questo articolo descrive come aggiungere o modificare il ruolo di amministratore per un utente mediante il controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](../role-based-access-control/built-in-roles.md#owner) (un ruolo Controllo degli accessi in base al ruolo) nell'ambito della sottoscrizione. Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

1. Nel portale di Azure aprire [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Fare clic sulla sottoscrizione in cui si intende concedere l'accesso.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

    ![Screenshot che mostra le assegnazioni di ruolo](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Fare clic su **Aggiungi** > **Aggiungi un'assegnazione di ruolo** per aprire il riquadro **Aggiungi un'assegnazione di ruolo**.

    Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione sarà disabilitata.

1. Nell’elenco a discesa **Ruolo**, selezionare il ruolo **Proprietario.**

1. Nell'elenco **Selezione**, selezionare un utente. Se nell'elenco l'utente non è visualizzato, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati e gli indirizzi e-mail.

    ![Schermata che mostra il pulsante Proprietario selezionato](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Fare clic su **Salva** per assegnare un ruolo.

    Dopo qualche istante, all’utente viene assegnato il ruolo di Proprietario per l'ambito della sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md)
* [Comprendere i diversi ruoli in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Procedura: Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
