---
title: Azure AD guidata sicurezza ruoli in PIM-Azure Active Directory | Microsoft Docs
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
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847040"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD guidata sicurezza ruoli in Privileged Identity Management

Se si è la prima persona a usare Privileged Identity Management (PIM) nell'organizzazione di Azure Active Directory (Azure AD), viene visualizzata una procedura guidata per iniziare. La procedura guidata consente di comprendere i rischi di sicurezza delle identità con privilegi e di come utilizzare Privileged Identity Management per ridurre tali rischi. Se si vuole farlo in seguito, non è necessario apportare modifiche alle assegnazioni dei ruoli esistenti nella procedura guidata.

## <a name="wizard-overview"></a>Panoramica della procedura guidata

Prima che l'organizzazione inizi a usare Privileged Identity Management, tutte le assegnazioni di ruolo sono permanenti: gli utenti sono sempre in questi ruoli anche se attualmente non necessitano dei privilegi. Il primo passaggio della procedura guidata visualizza un elenco dei ruoli con privilegi elevati e il numero di utenti attualmente presenti in tali ruoli. È possibile visualizzare i dettagli di un ruolo particolare per visualizzare altre informazioni sugli utenti nel caso in cui uno o più utenti non siano noti.

Il secondo passaggio della procedura guidata offre la possibilità di modificare le assegnazioni dei ruoli di amministratore.  

> [!WARNING]
> È importante avere almeno un amministratore globale e più di un amministratore del ruolo con privilegi con un account aziendale (non un account Microsoft). Se è presente un solo amministratore del ruolo con privilegi, l'organizzazione non può gestire Privileged Identity Management se tale account viene eliminato.
> Tenere inoltre permanenti le assegnazioni di ruolo se un utente ha un account Microsoft (in altre parole, un account usato per accedere ai servizi Microsoft come Skype e Outlook.com). Se si prevede di richiedere l'autenticazione a più fattori per l'attivazione per quel ruolo, l'utente verrà bloccato.

## <a name="run-the-wizard"></a>Eseguire la procedura guidata

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli** , quindi selezionare **procedura guidata**.

    ![Azure AD ruoli-pagina della procedura guidata che mostra i 3 passaggi per eseguire la procedura guidata](./media/pim-security-wizard/wizard-start.png)

1. Selezionare **1 individua ruoli con privilegi**.

1. Esaminare l'elenco di ruoli con privilegi per verificare quali utenti sono permanenti o idonei.

    ![Individuazione dei ruoli con privilegi-riquadro del ruolo che Mostra membri permanenti e idonei](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selezionare **Avanti** per selezionare gli utenti o i gruppi che si desidera rendere idonei.

    ![Convertire i membri in una pagina idonea con le opzioni per selezionare i membri che si desidera rendere idonei per i ruoli](./media/pim-security-wizard/convert-members-eligible.png)

1. Dopo aver selezionato gli utenti o i gruppi, fare clic su **Avanti**.

    ![Pagina verifica modifiche che mostra i membri con assegnazioni di ruolo permanenti che verranno convertite](./media/pim-security-wizard/review-changes.png)

1. Selezionare **OK** per convertire le assegnazioni permanenti in idonee.

    Al termine della conversione, verrà visualizzata una notifica.

    ![Notifica che mostra lo stato di una conversione](./media/pim-security-wizard/notification-completion.png)

Se è necessario convertire altre assegnazioni di ruolo con privilegi in idonee, è possibile eseguire nuovamente la procedura guidata. Se si vuole usare l'interfaccia Privileged Identity Management invece della procedura guidata, vedere [assegnare Azure ad ruoli in Privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md)
