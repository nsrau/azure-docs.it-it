---
title: Creare una verifica di accesso dei ruoli delle risorse di Azure in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come creare una verifica di accesso dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e4de5bbc56f95c0e903b1dac4e8481373716f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143483"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Creare una verifica di accesso dei ruoli delle risorse di Azure in PIM

Accesso ai ruoli delle risorse di Azure con privilegi per i dipendenti cambia nel corso del tempo. Per ridurre i rischi associati alle assegnazioni di ruolo non aggiornato, si deve esaminare periodicamente l'accesso. È possibile usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per creare le verifiche di accesso per i ruoli delle risorse di Azure con privilegi. È anche possibile configurare le verifiche di accesso ricorrenti che vengono eseguiti automaticamente.

Questo articolo descrive come creare uno o più verifiche di accesso per i ruoli delle risorse di Azure con privilegi.

## <a name="prerequisites"></a>Prerequisiti

- [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Verifiche di accesso completo

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo amministratore dei ruoli con privilegi.

1. Aprire **Azure AD Privileged Identity Management**.

1. Nel menu a sinistra, fare clic su **risorse di Azure**.

1. Fare clic sulla risorsa da gestire, ad esempio una sottoscrizione o un gruppo di gestione.

1. In Gestisci, fare clic su **verifiche di accesso**.

    ![Le risorse di Azure - verifiche di accesso](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzato nell'elenco con un indicatore di stato.

![Elenco delle verifiche di accesso](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile mostrare ai membri le istruzioni per la procedura [verificare l'accesso ai ruoli delle risorse Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento i revisori delle verifiche nel **Panoramica** pagina della verifica di accesso. Nessun diritto di accesso viene modificato nella directory finché il [revisione è stata completata](pim-resource-roles-complete-access-review.md).

![Lo stato di avanzamento delle verifiche di accesso](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se si tratta di una singola revisione, dopo il periodo della verifica l'accesso o l'amministratore ha interrotto la verifica di accesso, seguire i passaggi descritti in [completare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-complete-access-review.md) per esaminare e applicare i risultati.  

Per gestire una serie di accesso recensioni, passare alla verifica di accesso e si verrà trovare le ricorrenze future in revisioni pianificato e modificare la data di fine o aggiungere o rimuovere i revisori di conseguenza.

Sulla base delle selezioni nel **impostazioni al completamento**, applicazione automatica verrà da eseguire dopo la data di fine dell'analisi o quando si arresta manualmente la revisione. Lo stato della verifica passerà da **Completed** attraverso alcuni stati intermedi, ad esempio **applicazione** e infine allo stato **applicato**. Si dovrebbero vedere utenti negati, se presente, da rimuovere dai ruoli in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso ai ruoli delle risorse di Azure](pim-resource-roles-perform-access-review.md)
- [Completare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-complete-access-review.md)
- [Creare una verifica di accesso dei ruoli di Azure AD](pim-how-to-start-security-review.md)
