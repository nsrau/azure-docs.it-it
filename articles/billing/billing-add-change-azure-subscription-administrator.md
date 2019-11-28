---
title: Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure
description: Descrive come aggiungere o modificare un amministratore di una sottoscrizione di Azure mediante il controllo degli accessi in base al ruolo.
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: banders
ms.openlocfilehash: b6a269b1b267cb88c3c0cae42cffb3ba9684e291
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224138"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure


Per gestire l'accesso alle risorse di Azure, è necessario il ruolo di amministratore appropriato. In Azure è disponibile un sistema di autorizzazione, il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md), con diversi ruoli predefiniti tra cui è possibile scegliere. È possibile assegnare questi ruoli in ambiti diversi, ad esempio gruppo di gestione, sottoscrizione o gruppo di risorse. Per impostazione predefinita, la persona che crea una nuova sottoscrizione di Azure può assegnare ad altri utenti l'accesso amministrativo a una sottoscrizione.

Questo articolo descrive come aggiungere o modificare il ruolo di amministratore per un utente mediante il controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.

Microsoft consiglia di gestire l'accesso alle risorse usando il controllo degli accessi in base al ruolo. Se però si sta ancora usando il modello di distribuzione classico e si stanno gestendo le risorse classiche tramite il [modulo PowerShell di gestione del servizio Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure), sarà necessario usare un amministratore classico.

> [!TIP]
> Se si usa solo il portale di Azure per gestire le risorse classiche, non è necessario usare l'amministratore classico.

Per altre informazioni, vedere [Distribuzione Azure Resource Manager o distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md) e [Amministratori della sottoscrizione classica di Azure](../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Assegnare un amministratore della sottoscrizione

Per designare un utente come amministratore di una sottoscrizione di Azure, un amministratore esistente dovrà assegnargli il ruolo di [Proprietario](../role-based-access-control/built-in-roles.md#owner) (un ruolo Controllo degli accessi in base al ruolo) nell'ambito della sottoscrizione. Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

Se non si conosce l'amministratore account per una sottoscrizione, seguire questa procedura per individuarlo.

1. Aprire la [pagina Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

### <a name="to-assign-a-user-as-an-administrator"></a>Per assegnare un utente come amministratore

1. Accedere al portale di Azure come proprietario della sottoscrizione e aprire [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Fare clic sulla sottoscrizione in cui si intende concedere l'accesso.

1. Fare clic su **Controllo di accesso (IAM)** .

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
