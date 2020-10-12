---
title: Concessione dell'accesso per la gestione di PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come concedere l'accesso ad altri amministratori per gestire Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f8991d5ba9efb8e3223dd44a8d037acf2de849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009563"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Delega accesso a Privileged Identity Management

Per delegare l'accesso a Privileged Identity Management (PIM), un amministratore globale può assegnare altri utenti al ruolo di amministratore dei ruoli con privilegi. Per impostazione predefinita, gli amministratori della sicurezza e i lettori di sicurezza hanno accesso in sola lettura a Privileged Identity Management. Per concedere l'accesso a Privileged Identity Management, il primo utente può assegnare altri utenti al ruolo di **amministratore dei ruoli con privilegi** . Il ruolo di amministratore del ruolo con privilegi è necessario solo per la gestione dei ruoli Azure AD. Le autorizzazioni di amministratore del ruolo con privilegi non sono necessarie per gestire le impostazioni per le risorse di Azure.

> [!NOTE]
> La gestione di Privileged Identity Management richiede Multi-Factor Authentication di Azure. Poiché gli account Microsoft non possono eseguire la registrazione per Azure Multi-Factor Authentication, un utente che accede con una account Microsoft non può accedere Privileged Identity Management.

Assicurarsi che almeno due utenti abbiano sempre il ruolo di amministratore dei ruoli con privilegi, per l'eventualità in cui uno di questi venga bloccato o il suo account venga eliminato.

## <a name="delegate-access-to-manage-pim"></a>Delega dell'accesso per la gestione di PIM

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. In Azure AD aprire **Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Ruoli**.

    ![Ruoli di Azure AD Privileged Identity Management-ruoli](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selezionare il ruolo **amministratore ruolo con privilegi** per aprire la pagina membri.

    ![Amministratore dei ruoli con privilegi - Membri](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selezionare **Aggiungi membro**  per aprire il riquadro **Aggiungi membri gestiti** .

1. Selezionare **Seleziona membri** per aprire il riquadro **Seleziona membri** .

    ![Amministratore dei ruoli con privilegi - Seleziona membri](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selezionare un membro e quindi fare clic su **Seleziona**.

1. Selezionare **OK** per rendere il membro idoneo per il ruolo di **amministratore del ruolo con privilegi** .

    Quando si assegna un nuovo ruolo a un utente in Privileged Identity Management, questi vengono automaticamente configurati come **idonei** per attivare il ruolo.

1. Per rendere permanente il membro, selezionare l'utente nell'elenco dei membri amministratore del ruolo con privilegi.

1. Selezionare **altro** e quindi **rendere permanente** per rendere permanente l'assegnazione.

    ![Amministratore dei ruoli con privilegi - Rendi permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Inviare all'utente un collegamento per [iniziare a usare Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Rimuovere l'accesso per la gestione di PIM

Prima di rimuovere un utente dal ruolo di amministratore dei ruoli con privilegi, assicurarsi che siano sempre presenti almeno due utenti assegnati al ruolo.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Ruoli**.

1. Selezionare il ruolo **amministratore ruolo con privilegi** per aprire la pagina membri.

1. Selezionare la casella di controllo accanto all'utente che si desidera rimuovere, quindi selezionare **Rimuovi membro**.

    ![Amministratore dei ruoli con privilegi - Rimuovi membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando viene richiesto di confermare che si desidera rimuovere il membro dal ruolo, selezionare **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare Privileged Identity Management](pim-getting-started.md)
