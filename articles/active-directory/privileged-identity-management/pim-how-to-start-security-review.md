---
title: Creare una verifica di accesso dei ruoli di Azure AD in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come creare una verifica di accesso dei ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0680ddf2c9e654455933bf09699ab81e8ab65d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141840"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Creare una verifica di accesso dei ruoli di Azure AD in PIM

L'accesso a ruoli di Azure AD per le modifiche dipendenti con privilegi nel corso del tempo. Per ridurre i rischi associati alle assegnazioni di ruolo non aggiornato, si deve esaminare periodicamente l'accesso. È possibile usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per creare le verifiche di accesso per i ruoli di Azure AD con privilegi. È anche possibile configurare le verifiche di accesso ricorrenti che vengono eseguiti automaticamente.

Questo articolo descrive come creare uno o più verifiche di accesso per i ruoli di Azure AD con privilegi.

## <a name="prerequisites"></a>Prerequisiti

- [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Verifiche di accesso completo

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo amministratore dei ruoli con privilegi.

1. Aprire **Azure AD Privileged Identity Management**.

1. Nel menu a sinistra, fare clic su **ruoli di Azure AD** e quindi fare clic su **verifiche di accesso**.

1. In Gestisci, fare clic su **verifiche di accesso**.

    ![Ruoli di Azure AD - verifiche di accesso](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzato nell'elenco con un indicatore di stato.

![Elenco delle verifiche di accesso](./media/pim-how-to-start-security-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile mostrare ai membri le istruzioni per la procedura [verificare l'accesso a ruoli di Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento i revisori delle verifiche nel **Panoramica** pagina della verifica di accesso. Nessun diritto di accesso viene modificato nella directory finché il [revisione è stata completata](pim-how-to-complete-review.md).

![Lo stato di avanzamento delle verifiche di accesso](./media/pim-how-to-start-security-review/access-review-overview.png)

Se si tratta di una singola revisione, dopo il periodo della verifica l'accesso o l'amministratore ha interrotto la verifica di accesso, seguire i passaggi descritti in [completare una verifica di accesso dei ruoli di Azure AD](pim-how-to-complete-review.md) per esaminare e applicare i risultati.  

Per gestire una serie di accesso recensioni, passare alla verifica di accesso e si verrà trovare le ricorrenze future in revisioni pianificato e modificare la data di fine o aggiungere o rimuovere i revisori di conseguenza.

Sulla base delle selezioni nel **impostazioni al completamento**, applicazione automatica verrà da eseguire dopo la data di fine dell'analisi o quando si arresta manualmente la revisione. Lo stato della verifica passerà da **Completed** attraverso alcuni stati intermedi, ad esempio **applicazione** e infine allo stato **applicato**. Si dovrebbero vedere utenti negati, se presente, da rimuovere dai ruoli in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso a ruoli di Azure AD](pim-how-to-perform-security-review.md)
- [Completare una verifica di accesso dei ruoli di Azure AD](pim-how-to-complete-review.md)
- [Creare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-start-access-review.md)
