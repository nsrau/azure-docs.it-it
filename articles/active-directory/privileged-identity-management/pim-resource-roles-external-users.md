---
title: Assegnare i ruoli delle risorse di Azure ai guest in PIM-Azure AD | Microsoft Docs
description: Informazioni su come invitare gli utenti Guest esterni e assegnare i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021947"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Invitare gli utenti guest e assegnare i ruoli delle risorse di Azure in Privileged Identity Management

Gli utenti guest di Azure Active Directory (Azure AD) fanno parte delle funzionalità di collaborazione business-to-business (B2B) di Azure AD in modo che sia possibile gestire utenti e fornitori Guest esterni come guest in Azure AD. Quando si combina la collaborazione B2B con Azure AD Privileged Identity Management (PIM), è possibile estendere i requisiti di conformità e governance ai guest. Ad esempio, è possibile usare queste funzionalità Privileged Identity Management per le attività di identità di Azure con i guest:

- Assegnare l'accesso a specifiche risorse di Azure
- Abilitare l'accesso JIT alla VM
- Specificare una data di fine e durata dell'assegnazione
- Richiedi autenticazione a più fattori per l'assegnazione o l'attivazione attiva
- Eseguire verifiche di accesso
- Usare gli avvisi e log di controllo

Questo articolo descrive come invitare un guest nell'organizzazione e come gestire l'accesso alle risorse di Azure usando Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quando invitare i Guest?

Di seguito sono riportati alcuni esempi di quando è possibile invitare i guest nell'organizzazione:

- Consentire a un fornitore esterno indipendente che ha solo un account di posta elettronica di accedere alle risorse di Azure per un progetto.
- Consentire a un partner esterno di una grande organizzazione che utilizza a livello locale Active Directory Federation Services di accedere all'applicazione di spesa.
- Consentire ai tecnici di assistenza che non fanno parte della propria organizzazione (come il supporto Microsoft) di accedere temporaneamente alla propria risorsa di Azure per la risoluzione dei problemi.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Come funziona la collaborazione con i guest B2B?

Quando si usa la collaborazione B2B, è possibile invitare un utente esterno alla propria organizzazione come Guest. Il Guest può essere gestito come un utente dell'organizzazione, ma un guest deve essere autenticato nella propria organizzazione principale e non nell'organizzazione Azure AD. Ciò significa che se il Guest non ha più accesso alla propria organizzazione principale, perderà anche l'accesso all'organizzazione. Se, ad esempio, il Guest abbandona l'organizzazione, perderà automaticamente l'accesso a tutte le risorse condivise in Azure AD senza dover eseguire alcuna operazione. Per ulteriori informazioni sulla collaborazione B2B, vedere [che cos'è l'accesso utente guest in Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagramma che illustra come viene autenticato un utente guest nella home directory](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Controllare le impostazioni di collaborazione Guest

Per assicurarsi di poter invitare i guest nell'organizzazione, è consigliabile controllare le impostazioni di collaborazione Guest.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Azure Active Directory** **impostazioni utente** > .

1. Selezionare **Gestisci impostazioni di collaborazione esterna**.

    ![Pagina impostazioni di collaborazione esterna che mostra le impostazioni relative alle restrizioni di autorizzazione, invito e collaborazione](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Verificare che gli **amministratori e gli utenti nel ruolo mittente dell'invito Guest possano invitare** switch sia impostato su **Sì**.

## <a name="invite-a-guest-and-assign-a-role"></a>Invitare un Guest e assegnare un ruolo

Con Privileged Identity Management è possibile invitare un Guest e renderli idonei per un ruolo di risorse di Azure.

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [privilegiato amministratore ruolo](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) o [amministratore utente](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) .

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Risorse di Azure**.

1. Usare **Filtro della risorsa** per filtrare l'elenco delle risorse gestite.

1. Selezionare la risorsa che si vuole gestire, ad esempio una risorsa, un gruppo di risorse, una sottoscrizione o un gruppo di gestione.

    È necessario impostare l'ambito solo su ciò che è necessario per il Guest.

1. In Gestisci selezionare **ruoli** per visualizzare l'elenco dei ruoli per le risorse di Azure.

    ![Elenco dei ruoli delle risorse di Azure che mostra il numero di utenti attivi e idonei](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selezionare il ruolo minimo che l'utente dovrà avere.

    ![Pagina ruolo selezionato che elenca i membri correnti del ruolo](./media/pim-resource-roles-external-users/selected-role.png)

1. Nella pagina ruolo selezionare **Aggiungi membro** per aprire il riquadro nuova assegnazione.

1. Fare clic su **Selezionare un membro o un gruppo**.

    ![Nuova assegnazione: selezionare un membro o un riquadro del gruppo che elenca utenti e gruppi insieme a un'opzione di invito](./media/pim-resource-roles-external-users/select-member-group.png)

1. Per invitare un Guest, fare clic su **invita**.

    ![Invita una pagina Guest con caselle per immettere un indirizzo di posta elettronica e specificare un messaggio personale](./media/pim-resource-roles-external-users/invite-guest.png)

1. Dopo aver selezionato un Guest, fare clic su **invita**.

    Il guest deve essere aggiunto come membro selezionato.

1. Nel riquadro **selezionare un membro o un gruppo** fare clic su **Seleziona**.

1. Nel riquadro **impostazioni di appartenenza** selezionare il tipo di assegnazione e la durata.

    ![Pagina nuova assegnazione-impostazioni appartenenza con opzioni per specificare il tipo di assegnazione, la data di inizio e la data di fine](./media/pim-resource-roles-external-users/membership-settings.png)

1. Per completare l'assegnazione, selezionare **fine** e quindi **Aggiungi**.

    L'assegnazione del ruolo Guest verrà visualizzata nell'elenco dei ruoli.

    ![Pagina ruolo che elenca il Guest come idoneo](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Attivare il ruolo come Guest

Se si è un utente esterno, è necessario accettare l'invito come guest nell'organizzazione Azure AD ed eventualmente attivare l'assegnazione di ruolo.

1. Aprire il messaggio di posta elettronica con l'invito. Il messaggio di posta elettronica sarà simile al seguente:

    ![Invito tramite posta elettronica con nome della directory, messaggio personale e collegamento introduttivo](./media/pim-resource-roles-external-users/email-invite.png)

1. Selezionare il collegamento **Get Started (introduzione** ) nel messaggio di posta elettronica.

1. Dopo aver verificato le autorizzazioni, fare clic su **Accetta**.

    ![Pagina Verifica autorizzazioni in un browser con un elenco di autorizzazioni che l'organizzazione desidera rivedere](./media/pim-resource-roles-external-users/invite-accept.png)

1. Potrebbe essere richiesto di accettare le condizioni per l'utilizzo e specificare se si desidera rimanere connessi. Nel portale di Azure, se si è *idonei* per un ruolo, non sarà ancora possibile accedere alle risorse.

1. Per attivare l'assegnazione di ruolo, aprire il messaggio di posta elettronica con il collegamento attiva ruolo. Il messaggio di posta elettronica sarà simile al seguente:

    ![Messaggio di posta elettronica che indica che l'utente è idoneo per un ruolo con un collegamento attiva ruolo](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selezionare **attiva ruolo** per aprire i ruoli idonei in Privileged Identity Management.

    ![Pagina ruoli personali in Privileged Identity Management elencare i ruoli idonei](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. In azione selezionare il collegamento **attiva** .

    A seconda delle impostazioni di ruolo, è necessario specificare alcune informazioni per attivare il ruolo.

1. Dopo aver specificato le impostazioni per il ruolo, fare clic su **Attiva** per attivare il ruolo.

    ![Attivare l'ambito e le opzioni dell'elenco di pagine per specificare l'ora di inizio, la durata e il motivo](./media/pim-resource-roles-external-users/activate-role.png)

    A meno che l'amministratore non debba approvare la richiesta, si ottiene accesso alle risorse specificate.

## <a name="view-activity-for-a-guest"></a>Visualizzare l'attività per un Guest

È possibile visualizzare i log di controllo per tenere traccia delle attività dei guest.

1. In qualità di amministratore, aprire Privileged Identity Management e selezionare la risorsa condivisa.

1. Selezionare **controllo risorse** per visualizzare l'attività per la risorsa. Di seguito viene riportato un esempio di attività per un gruppo di risorse.

    ![Risorse di Azure-pagina di controllo delle risorse che elenca l'ora, il richiedente e l'azione](./media/pim-resource-roles-external-users/audit-resource.png)

1. Per visualizzare l'attività per il Guest, selezionare **Azure Active Directory** > **utenti** > *nome Guest*.

1. Selezionare **log di controllo** per visualizzare i log di controllo per l'organizzazione. Se necessario, è possibile specificare i filtri.

    ![Log di controllo della directory che elencano date, target, initiationd by e Activity](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli di amministratore di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Informazioni sull'accesso degli utenti guest in Azure AD collaborazione B2B](../b2b/what-is-b2b.md)
