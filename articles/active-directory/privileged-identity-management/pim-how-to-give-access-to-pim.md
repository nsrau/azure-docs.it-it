---
title: Concedere l'accesso per gestire PIM - Azure Active Directory Documenti Microsoft
description: Informazioni su come concedere l'accesso ad altri amministratori per gestire Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022120"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Concedere l'accesso ad altri amministratori per gestire Gestione identità con privilegiGrant access to other administrators to manage Privileged Identity Management

L'amministratore globale che abilita la gestione delle identità con privilegi (PIM, Privileged Identity Management) per un'organizzazione ottiene automaticamente le assegnazioni di ruolo e l'accesso a Gestione identità privilegiate. Nessun altro utente nell'organizzazione di Azure Active Directory (Azure AD) ottiene l'accesso in scrittura per impostazione predefinita, tuttavia, inclusi altri amministratori globali. Altri amministratori globali, amministratori della sicurezza e lettori di sicurezza hanno accesso in sola lettura a Gestione identità con privilegi. Per concedere l'accesso a Gestione identità con privilegi, il primo utente può assegnare altri utenti al ruolo **Amministratore ruolo con privilegi.**

> [!NOTE]
> La gestione della gestione delle identità con privilegi richiede Azure Multi-Factor Authentication.Managing Privileged Identity Management requires Azure Multi-Factor Authentication. Poiché gli account Microsoft non possono registrarsi per Azure Multi-Factor Authentication, un utente che accede con un account Microsoft non può accedere a Gestione identità con privilegi.

Assicurarsi che almeno due utenti abbiano sempre il ruolo di amministratore dei ruoli con privilegi, per l'eventualità in cui uno di questi venga bloccato o il suo account venga eliminato.

## <a name="grant-access-to-manage-pim"></a>Concedere l'accesso per la gestione di PIM

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. In Azure AD aprire **Gestione identità con privilegi**.

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Ruoli**.

    ![Ruoli di Azure AD per la gestione delle identità con privilegi - RuoliPrivileged Identity Management Azure AD roles - Roles](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selezionare il ruolo **Di amministratore ruolo con privilegi** per aprire la pagina membri.

    ![Amministratore dei ruoli con privilegi - Membri](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selezionare **Aggiungi membro** per aprire il riquadro Aggiungi membri gestiti.

1. Selezionare **Seleziona membri** per aprire il riquadro Seleziona membri.

    ![Amministratore dei ruoli con privilegi - Seleziona membri](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selezionare un membro e quindi fare clic su **Seleziona**.

1. Selezionare **OK** per rendere il membro idoneo per il ruolo **Dicl.**

    Quando si assegna un nuovo ruolo a un utente in Gestione identità privilegiate, questo viene configurato automaticamente come **idoneo** per attivare il ruolo.

1. Per rendere permanente il membro, selezionare l'utente nell'elenco membro Amministratore ruolo con privilegi.

1. Selezionare **Altro** e quindi **Rendi permanente** per rendere permanente l'assegnazione.

    ![Amministratore dei ruoli con privilegi - Rendi permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Inviare all'utente un collegamento a Iniziare a [utilizzare Gestione identità con privilegi](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Rimuovere l'accesso per la gestione di PIM

Prima di rimuovere un utente dal ruolo di amministratore dei ruoli con privilegi, assicurarsi che siano sempre presenti almeno due utenti assegnati al ruolo.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Ruoli**.

1. Selezionare il ruolo **Di amministratore ruolo con privilegi** per aprire la pagina membri.

1. Selezionare la casella di controllo accanto all'utente che si desidera rimuovere, quindi selezionare **Rimuovi membro**.

    ![Amministratore dei ruoli con privilegi - Rimuovi membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando viene chiesto di confermare la rimozione del membro dal ruolo, selezionare **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare Privileged Identity Management](pim-getting-started.md)
