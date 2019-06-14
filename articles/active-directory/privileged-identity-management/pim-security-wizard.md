---
title: I ruoli sicurezza guidata di Azure AD in PIM - Azure Active Directory | Microsoft Docs
description: Descrive la procedura guidata relativa alla sicurezza che è possibile usare per convertire le assegnazioni di ruolo di Azure AD con privilegi permanenti in assegnazioni idonee usando Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6f978612cbbf0c326c3e66f25a0fbf4b749cc73
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60286931"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Procedura guidata relativa alla sicurezza per i ruoli di Azure AD in PIM

Se si è la prima persona per l'esecuzione di Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per l'organizzazione, verrà visualizzato con una procedura guidata. La procedura guidata offre informazioni sui rischi di sicurezza delle identità con privilegi e su come usare PIM per ridurre tali rischi. Se si vuole farlo in seguito, non è necessario apportare modifiche alle assegnazioni dei ruoli esistenti nella procedura guidata.

## <a name="wizard-overview"></a>Panoramica della procedura guidata

Prima che l'organizzazione inizi a usare PIM, tutte le assegnazioni dei ruoli sono permanenti, ovvero gli utenti sono sempre presenti nei ruoli anche se in quel momento non necessitano dei privilegi del ruolo. Il primo passaggio della procedura guidata visualizza un elenco dei ruoli con privilegi elevati e il numero di utenti attualmente presenti in tali ruoli. È possibile visualizzare i dettagli di un ruolo particolare per visualizzare altre informazioni sugli utenti nel caso in cui uno o più utenti non siano noti.

Il secondo passaggio della procedura guidata offre la possibilità di modificare le assegnazioni dei ruoli di amministratore.  

> [!WARNING]
> È importante che siano presenti almeno un amministratore globale e più amministratori dei ruoli con privilegi con un account aziendale (non un account Microsoft). Se è presente un solo amministratore dei ruoli con privilegi, l'organizzazione non potrà gestire PIM se tale account viene eliminato.
> Mantenere inoltre assegnazioni permanenti di ruoli se l'utente dispone di un account Microsoft (usato per accedere a servizi Microsoft come Skype e Outlook.com). Se si prevede di richiedere l'autenticazione MFA per l'attivazione di questo ruolo, l'utente verrà bloccato.

## <a name="run-the-wizard"></a>Eseguire la procedura guidata

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD** e quindi su **Procedura guidata**.

    ![Ruoli di Azure AD - Procedura guidata](./media/pim-security-wizard/wizard-start.png)

1. Fare clic su **1 Individua i ruoli con privilegi**.

1. Esaminare l'elenco di ruoli con privilegi per verificare quali utenti sono permanenti o idonei.

    ![Individuare gli utenti con ruoli con privilegi](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Fare clic su **Avanti** per selezionare i membri da rendere idonei.

    ![Convertire i membri in idonei](./media/pim-security-wizard/convert-members-eligible.png)

1. Dopo avere selezionato i membri, fare clic su **Avanti**.

    ![Verificare le modifiche](./media/pim-security-wizard/review-changes.png)

1. Fare clic su **OK** per convertire le assegnazioni permanenti in idonee.

    Al termine della conversione, verrà visualizzata una notifica.

    ![Notifiche](./media/pim-security-wizard/notification-completion.png)

Se è necessario convertire altre assegnazioni di ruolo con privilegi in idonee, è possibile eseguire nuovamente la procedura guidata. Se si vuole usare l'interfaccia PIM invece la procedura guidata, vedere [assegnare i ruoli di Azure AD in PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli di Azure AD in PIM](pim-how-to-add-role-to-user.md)
- [Concedere l'accesso ad altri amministratori per gestire PIM](pim-how-to-give-access-to-pim.md)
