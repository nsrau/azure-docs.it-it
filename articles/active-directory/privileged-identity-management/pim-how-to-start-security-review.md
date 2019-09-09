---
title: Creare una verifica di accesso dei ruoli Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come creare una verifica di accesso dei ruoli Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2069f0161ea2600a62ab123206d0ebe3c00642c3
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804376"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Creare una verifica di accesso dei ruoli Azure AD in PIM

Accesso ai ruoli di Azure AD con privilegi per i dipendenti cambiano nel tempo. Per ridurre i rischi associati alle assegnazioni di ruolo obsolete, è necessario esaminare periodicamente l'accesso. È possibile usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per creare verifiche di accesso per i ruoli di Azure AD con privilegi. È anche possibile configurare le verifiche di accesso periodiche che vengono eseguite automaticamente.

Questo articolo descrive come creare una o più verifiche di accesso per i ruoli di Azure AD con privilegi.

## <a name="prerequisites"></a>Prerequisiti

- [Amministratore del ruolo con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Apri verifiche di accesso

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo di amministratore dei ruoli con privilegi.

1. Aprire **Azure AD Privileged Identity Management**.

1. Nel menu a sinistra fare clic su **Azure ad ruoli** e quindi su verifiche di **accesso**.

1. In Gestisci fare clic su verifiche di **accesso**.

    ![Azure AD Roles-elenco di verifiche di accesso che mostra lo stato di tutte le revisioni](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore dello stato.

![Elenco di verifiche di accesso che mostra lo stato delle revisioni avviate](./media/pim-how-to-start-security-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile visualizzare le istruzioni su come [verificare l'accesso ai ruoli Azure ad](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento dei revisori che completano le proprie revisioni nella pagina **Panoramica** della verifica di accesso. Nessun diritto di accesso viene modificato nella directory fino al [completamento della verifica](pim-how-to-complete-review.md).

![Pagina di panoramica delle verifiche di accesso che mostra i dettagli della verifica](./media/pim-how-to-start-security-review/access-review-overview.png)

Se si verifica una sola volta, al termine del periodo di verifica di accesso o se l'amministratore interrompe la verifica di accesso, seguire i passaggi descritti in [completare una verifica di accesso dei ruoli Azure ad](pim-how-to-complete-review.md) per visualizzare e applicare i risultati.  

Per gestire una serie di verifiche di accesso, passare alla verifica di accesso e trovare le occorrenze imminenti nelle revisioni pianificate e modificare di conseguenza la data di fine o aggiungere/rimuovere revisori.

In base alle impostazioni selezionate in **al termine**, l'applicazione automatica verrà eseguita dopo la data di fine della verifica o quando si arresta manualmente la revisione. Lo stato della revisione passerà da **completato** a stati intermedi, ad esempio l' **applicazione** e infine lo stato **applicato**. È necessario che gli utenti negati, se presenti, vengano rimossi dai ruoli in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Controllare l'accesso ai ruoli di Azure AD](pim-how-to-perform-security-review.md)
- [Completare una verifica di accesso dei ruoli Azure AD](pim-how-to-complete-review.md)
- [Creare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-start-access-review.md)
