---
title: Creare una verifica di accesso dei ruoli delle risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come creare una revisione di accesso dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847080"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Creare una verifica di accesso dei ruoli delle risorse di Azure in Gestione delle identità con privilegiCreate an access review of Azure resource roles in Privileged Identity Management

L'accesso ai ruoli delle risorse di Azure con privilegi per i dipendenti cambia nel tempo. Per ridurre il rischio associato alle assegnazioni di ruolo non aggiornate, è consigliabile esaminare regolarmente l'accesso. È possibile usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per creare verifiche di accesso per i ruoli delle risorse di Azure con privilegi. È inoltre possibile configurare le verifiche di accesso ricorrenti che si verificano automaticamente.

Questo articolo descrive come creare una o più verifiche di accesso per i ruoli delle risorse di Azure con privilegi.

## <a name="prerequisites"></a>Prerequisiti

[Amministratore del ruolo con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Recensioni di accesso aperte

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo Amministratore dei ruoli con privilegi.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Nel menu a sinistra selezionare **Risorse di Azure**.

1. Selezionare la risorsa che si desidera gestire, ad esempio una sottoscrizione o un gruppo di gestione.

1. In Gestisci selezionare **Verifiche di accesso.**

    ![Risorse di Azure - Elenco delle revisioni di accesso che mostra lo stato di tutte le revisioniAzure resources - Access reviews list showing the status of all reviews](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore del relativo stato.

![Elenco delle verifiche di accesso che mostra lo stato della revisione avviata](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile mostrare loro le istruzioni su come [controllare l'accesso ai ruoli delle risorse](pim-resource-roles-perform-access-review.md)di Azure.

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento man mano che i revisori completano le revisioni nella pagina **Panoramica** della verifica di accesso. Nessun diritto di accesso viene modificato nella directory fino al completamento della [revisione.](pim-resource-roles-complete-access-review.md)

![Pagina di panoramica delle recensioni di accesso che mostra i dettagli della recensione](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se si tratta di una revisione una tantera, al termine del periodo di verifica dell'accesso o l'amministratore interrompe la verifica di accesso, seguire i passaggi descritti in [Completare](pim-resource-roles-complete-access-review.md) una verifica di accesso dei ruoli delle risorse di Azure per visualizzare e applicare i risultati.  

Per gestire una serie di verifiche di accesso, passare alla verifica di accesso e trovare le occorrenze imminenti in Revisioni pianificate e modificare la data di fine o aggiungere/rimuovere i revisori di conseguenza.

In base alle selezioni effettuate in **Impostazioni al completamento,** l'applicazione automatica verrà eseguita dopo la data di fine della revisione o quando si interrompe manualmente la revisione. Lo stato della revisione cambierà da **Completato** a Stati intermedi come **Applicazione** e infine allo stato **Applicato**. Si dovrebbe aspettare di vedere gli utenti negati, se presenti, essere rimossi dai ruoli in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare l'accesso ai ruoli delle risorse di AzureReview access to Azure resource roles](pim-resource-roles-perform-access-review.md)
- [Completare una verifica di accesso dei ruoli delle risorse di AzureComplete an access review of Azure resource roles](pim-resource-roles-complete-access-review.md)
- [Creare una verifica di accesso dei ruoli di Azure ADCreate an access review of Azure AD roles](pim-how-to-start-security-review.md)
