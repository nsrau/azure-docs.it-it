---
title: Procedura guidata di sicurezza dei ruoli di Azure AD in PIM - Azure Active Directory Documenti Microsoft
description: Descrive la procedura guidata relativa alla sicurezza che è possibile usare per convertire le assegnazioni di ruolo di Azure AD con privilegi permanenti in assegnazioni idonee usando Azure AD Privileged Identity Management (PIM).
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
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266572"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD roles security wizard in Privileged Identity Management

Se si è la prima persona a usare Privileged Identity Management (PIM) nell'organizzazione di Azure Active Directory (Azure AD), viene visualizzata una procedura guidata per iniziare. La procedura guidata consente di comprendere i rischi per la sicurezza delle identità con privilegi e di come utilizzare Gestione identità con privilegi per ridurre tali rischi. Se si vuole farlo in seguito, non è necessario apportare modifiche alle assegnazioni dei ruoli esistenti nella procedura guidata.

## <a name="wizard-overview"></a>Panoramica della procedura guidata

Prima che l'organizzazione inizi a utilizzare Gestione identità privilegiate, tutte le assegnazioni di ruolo sono permanenti: gli utenti sono sempre in questi ruoli anche se attualmente non hanno bisogno dei propri privilegi. Il primo passaggio della procedura guidata visualizza un elenco dei ruoli con privilegi elevati e il numero di utenti attualmente presenti in tali ruoli. È possibile visualizzare i dettagli di un ruolo particolare per visualizzare altre informazioni sugli utenti nel caso in cui uno o più utenti non siano noti.

Il secondo passaggio della procedura guidata offre la possibilità di modificare le assegnazioni dei ruoli di amministratore.  

> [!WARNING]
> È importante disporre di almeno un amministratore globale e più di un amministratore del ruolo Privileged con un account aziendale (non di un account Microsoft). Se è presente un solo amministratore del ruolo Privileged, l'organizzazione non può gestire Gestione identità con privilegi se tale account viene eliminato.
> Inoltre, mantenere permanenti le assegnazioni di ruolo se un utente dispone di un account Microsoft (in altre parole, un account utilizzato per accedere ai servizi Microsoft come Skype e Outlook.com). Se si prevede di richiedere l'autenticazione a più fattori per l'attivazione per tale ruolo, tale utente verrà bloccato.

## <a name="run-the-wizard"></a>Eseguire la procedura guidata

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD** e quindi Procedura **guidata**.

    ![Ruoli di Azure AD - Pagina della procedura guidata che mostra i 3 passaggi per eseguire la procedura guidataAzure AD roles - Wizard page showing the 3 steps to run the wizard](./media/pim-security-wizard/wizard-start.png)

1. Selezionare **1 Individua ruoli con privilegi**.

1. Esaminare l'elenco di ruoli con privilegi per verificare quali utenti sono permanenti o idonei.

    ![Riquadro Individuazione ruoli con privilegi - Riquadro Ruolo che mostra i membri permanenti e idonei](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selezionare **Avanti** per selezionare gli utenti o i gruppi che si desidera rendere idonei.

    ![Converti i membri nella pagina idonea con le opzioni per selezionare i membri che desideri rendere idonei ai ruoli](./media/pim-security-wizard/convert-members-eligible.png)

1. Dopo aver selezionato gli utenti o i gruppi, selezionare **Avanti**.

    ![Pagina Revisioni che mostra i membri con assegnazioni di ruolo permanenti che verranno convertite](./media/pim-security-wizard/review-changes.png)

1. Selezionare **OK** per convertire le assegnazioni permanenti in idonee.

    Al termine della conversione, verrà visualizzata una notifica.

    ![Notifica che mostra lo stato di una conversione](./media/pim-security-wizard/notification-completion.png)

Se è necessario convertire altre assegnazioni di ruolo con privilegi in idonee, è possibile eseguire nuovamente la procedura guidata. Se si vuole usare l'interfaccia di Gestione identità con privilegi anziché la procedura guidata, vedere Assegnare ruoli di [Azure AD in Gestione identità con privilegi](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Concedere l'accesso ad altri amministratori per gestire Gestione identità con privilegiGrant access to other administrators to manage Privileged Identity Management](pim-how-to-give-access-to-pim.md)
