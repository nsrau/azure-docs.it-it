---
title: Concedere l'accesso ad altri amministratori per gestire PIM - Azure | Microsoft Docs
description: Informazioni su come concedere l'accesso ad altri amministratori per gestire Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9d5fce5a80ac1f281fdbe6afe7f9a97816807ccc
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287088"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Concedere l'accesso ad altri amministratori per gestire PIM

L'amministratore globale che abilita Azure AD Privileged Identity Management (PIM) per un'organizzazione ottiene automaticamente le assegnazioni di ruolo e l'accesso a PIM. Per impostazione predefinita, nessun altro utente ottiene l'accesso in scrittura, inclusi gli altri amministratori globali. Gli altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza hanno l'accesso in sola lettura a PIM. Per concedere l'accesso a PIM, il primo utente può assegnare ad altri il ruolo di **amministratore dei ruoli con privilegi**.

> [!NOTE]
> Per la gestione di PIM è necessario Azure Multi-Factor Authentication. Poiché gli account Microsoft non possono effettuare la registrazione per Azure MFA, gli utenti che eseguono l'accesso con un account Microsoft non possono accedere a PIM.

Assicurarsi che almeno due utenti abbiano sempre il ruolo di amministratore dei ruoli con privilegi, per l'eventualità in cui uno di questi venga bloccato o il suo account venga eliminato.

## <a name="grant-access-to-manage-pim"></a>Concedere l'accesso per la gestione di PIM

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli della directory di Azure AD**.

1. Fare clic su **Ruoli**.

    ![Ruoli della directory di Azure AD PIM - Ruoli](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Fare clic sul ruolo **Amministratore dei ruoli con privilegi** per aprire la pagina dei membri.

    ![Amministratore dei ruoli con privilegi - Membri](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Fare clic su **Aggiungi membro** per aprire il riquadro Aggiungi membri gestiti.

1. Fare clic su **Seleziona membri** per aprire il riquadro Seleziona membri.

    ![Amministratore dei ruoli con privilegi - Seleziona membri](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selezionare un membro e quindi fare clic su **Seleziona**.

1. Fare clic su **OK** per rendere il membro idoneo per il ruolo **Amministratore dei ruoli con privilegi**.

    Quando si assegna un nuovo ruolo a un utente in PIM, questo viene configurato automaticamente come **Idoneo** per attivare il ruolo.

1. Per rendere permanente il membro, fare clic sull'utente nell'elenco Amministratore dei ruoli con privilegi.

1. Fare clic su **Altro** e quindi su **Rendi permanente** per rendere permanente l'assegnazione.

    ![Amministratore dei ruoli con privilegi - Rendi permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Inviare all'utente un collegamento a [Iniziare a usare PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Rimuovere l'accesso per la gestione di PIM

Prima di rimuovere un utente dal ruolo di amministratore dei ruoli con privilegi, assicurarsi che siano sempre presenti almeno due utenti assegnati al ruolo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli della directory di Azure AD**.

1. Fare clic su **Ruoli**.

1. Fare clic sul ruolo **Amministratore dei ruoli con privilegi** per aprire la pagina dei membri.

1. Aggiungere un segno di spunta accanto all'utente che si vuole rimuovere e quindi fare clic su **Rimuovi membro**.

    ![Amministratore dei ruoli con privilegi - Rimuovi membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Nel messaggio visualizzato in cui viene chiesto se si vuole rimuovere il membro dal ruolo fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare PIM](pim-getting-started.md)