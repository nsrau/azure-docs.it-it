---
title: Assegnare ruoli di Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come assegnare ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/16/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4d1e0d43758645d43843417eadf0ce21d43cb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533843"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Assegnare ruoli di Azure AD in Privileged Identity Management

Con Azure Active Directory (Azure AD), un amministratore globale può effettuare assegnazioni **permanenti** di ruoli di amministratore Azure ad. Queste assegnazioni di ruoli possono essere create usando il [portale di Azure](../users-groups-roles/directory-assign-admin-roles.md) o i [ comandi di PowerShell](/powershell/module/azuread#directory_roles).

Il servizio Azure AD Privileged Identity Management (PIM) consente inoltre agli amministratori dei ruoli con privilegi di effettuare assegnazioni di ruolo di amministratore permanenti. Inoltre, gli amministratori dei ruoli con privilegi possono rendere gli utenti **idonei** per i ruoli di amministratore Azure ad. Un amministratore idoneo può attivare il ruolo quando serve, con autorizzazioni che scadono al termine delle operazioni.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte Azure AD ruoli di Privileged Identity Management viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure. In questo modo vengono create funzionalità aggiuntive e le [modifiche apportate all'API esistente](azure-ad-roles-features.md#api-changes). Mentre è in corso il rollback della nuova versione, le procedure descritte in questo articolo dipendono dalla versione di Privileged Identity Management attualmente disponibile. Attenersi alla procedura descritta in questa sezione per determinare la versione di Privileged Identity Management. Quando si conosce la versione di Privileged Identity Management, è possibile selezionare le procedure descritte in questo articolo corrispondenti a tale versione.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente appartenente al ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Aprire **Azure ad Privileged Identity Management**. Se si dispone di un banner nella parte superiore della pagina Panoramica, seguire le istruzioni riportate nella scheda **nuova versione** di questo articolo. In caso contrario, seguire le istruzioni riportate nella scheda **versione precedente** .

  [![Selezionare Azure AD Privileged Identity Management >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="assign-a-role"></a>Assegnare un ruolo

Seguire questa procedura per rendere un utente idoneo per un ruolo di amministratore Azure AD.

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Per informazioni su come concedere a un altro amministratore l'accesso per gestire Privileged Identity Management, vedere [concedere l'accesso ad altri amministratori per gestire Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **ruoli** per visualizzare l'elenco dei ruoli per le autorizzazioni Azure ad.

    ![Screenshot della pagina "ruoli" con l'azione "Aggiungi assegnazioni" selezionata.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selezionare **Aggiungi assegnazioni** per aprire la pagina **Aggiungi assegnazioni** .

1. Selezionare **selezionare un ruolo** per aprire la pagina **selezionare un ruolo** .

    ![Riquadro Nuova assegnazione](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selezionare un ruolo da assegnare, selezionare un membro a cui si vuole assegnare il ruolo e quindi fare clic su **Avanti**.

1. Nell'elenco **tipo di assegnazione** nel riquadro **impostazioni di appartenenza** Selezionare **idoneo** o **attivo**.

    - Le assegnazioni **idonee** richiedono che il membro del ruolo esegua un'azione per usare il ruolo. Le azioni possono includere il completamento di un controllo di autenticazione a più fattori (MFA), l'indicazione di una motivazione aziendale e la richiesta di approvazione da parte di responsabili dell'approvazione designati.

    - Le assegnazioni **attive** non richiedono che il membro esegua alcuna azione per usare il ruolo. I membri con questo tipo di assegnazione dispongono di privilegi assegnati al ruolo in qualsiasi momento.

1. Per specificare una durata di assegnazione specifica, aggiungere le caselle di data e ora di inizio e di fine. Al termine, selezionare **assegna** per creare la nuova assegnazione di ruolo.

    ![Impostazioni di appartenenza a - data e ora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Una volta assegnato il ruolo, viene visualizzata una notifica sullo stato dell'assegnazione.

    ![Nuova assegnazione - Notifica](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Assegnare un ruolo con ambito limitato

Per determinati ruoli, l'ambito delle autorizzazioni concesse può essere limitato a una singola unità amministrativa, a un'entità servizio o a un'applicazione. Questa procedura è un esempio di assegnazione di un ruolo che dispone dell'ambito di un'unità amministrativa. Per un elenco dei ruoli che supportano l'ambito tramite unità amministrativa, vedere [assegnare ruoli con ambito a un'unità amministrativa](../users-groups-roles/roles-admin-units-assign-roles.md). Questa funzionalità è attualmente implementata per Azure AD organizzazioni.

1. Accedere al centro di [amministrazione Azure Active Directory](https://aad.portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi.

1. Selezionare **Azure Active Directory**  >  **ruoli e amministratori**.

1. Selezionare l' **amministratore utente**.

    ![Il comando Aggiungi assegnazione è disponibile quando si apre un ruolo nel portale](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Selezionare **Aggiungi assegnazioni**.

    ![Quando un ruolo supporta l'ambito, è possibile selezionare un ambito](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Nella pagina **Aggiungi assegnazioni** è possibile:

   - Selezionare un utente o un gruppo da assegnare al ruolo
   - Selezionare l'ambito del ruolo (in questo caso, unità amministrative)
   - Selezionare un'unità amministrativa per l'ambito

Per ulteriori informazioni sulla creazione di unità amministrative, vedere [aggiungere e rimuovere unità amministrative](../users-groups-roles/roles-admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Aggiornare o rimuovere un'assegnazione di ruolo esistente

Seguire questi passaggi per aggiornare o rimuovere un'assegnazione di ruolo esistente.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Roles (ruoli** ) per visualizzare l'elenco dei ruoli per Azure ad.

1. Selezionare il ruolo da aggiornare o rimuovere.

1. Trovare l'assegnazione del ruolo nelle schede **Ruoli idonei** o **Ruoli attivi**.

    ![Aggiornare o rimuovere un'assegnazione di ruolo](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Fare clic su **Aggiorna** o su **Rimuovi** per aggiornare o rimuovere l'assegnazione di ruolo.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Rendere un utente idoneo per un ruolo

Seguire questa procedura per rendere un utente idoneo per un ruolo di amministratore Azure AD.

1. Selezionare **ruoli** o **membri**.

    ![Apri ruoli Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selezionare **Aggiungi membro** per aprire **Aggiungi membri gestiti**.

1. Selezionare **selezionare un ruolo**, selezionare un ruolo che si vuole gestire e quindi selezionare **Seleziona**.

    ![Selezionare un ruolo](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selezionare **Seleziona membri**, selezionare gli utenti che si desidera assegnare al ruolo, quindi selezionare **Seleziona**.

    ![Selezionare un utente o un gruppo da assegnare](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. In **Aggiungi membri gestiti**selezionare **OK** per aggiungere l'utente al ruolo.

1. Nell'elenco dei ruoli selezionare il ruolo appena assegnato per visualizzare l'elenco dei membri.

     Quando il ruolo è stato assegnato, l'utente selezionato verrà visualizzato nell'elenco dei membri con il valore **Idoneo** per il ruolo.

    ![Utente idoneo per un ruolo](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Ora che l'utente è idoneo per il ruolo, indicare che può attivarlo in base alle istruzioni riportate in [attivare i ruoli di Azure ad in Privileged Identity Management](pim-how-to-activate-role.md).

    Agli amministratori idonei viene chiesto di registrarsi per Multi-Factor Authentication di Azure durante l'attivazione. Se un utente non è in grado di eseguire la registrazione per l'autenticazione a più fattori o utilizza un account Microsoft (ad esempio @outlook.com ), è necessario renderli permanenti in tutti i relativi ruoli.

## <a name="make-a-role-assignment-permanent"></a>Rendere permanente un'assegnazione di ruolo

Per impostazione predefinita, i nuovi utenti sono *idonei* solo per un ruolo di amministratore Azure ad. Per rendere permanente un'assegnazione di ruolo, eseguire la procedura seguente:

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Membri**.

    ![Elenco di membri](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selezionare un ruolo **idoneo** che si desidera rendere permanente.

1. Selezionare **altro** e quindi fare clic su **Rendi Perm**.

    ![Rendere permanente l'assegnazione di ruolo](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Il ruolo viene elencato come **permanente**.

    ![Elenco di membri con modifica permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Rimuovere un utente da un ruolo

È possibile rimuovere gli utenti da assegnazioni di ruolo, ma assicurarsi che sia sempre presente almeno un utente che è un amministratore globale permanente. Se non si è certi se gli utenti necessitano ancora di assegnazioni di ruoli, è possibile [avviare una verifica di accesso per il ruolo](pim-how-to-start-security-review.md).

Seguire questa procedura per rimuovere un utente specifico da un ruolo di amministratore Azure AD.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Membri**.

    ![Elenco di membri](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selezionare un'assegnazione di ruolo che si desidera rimuovere.

1. Selezionare **altro** e quindi fare clic su **Rimuovi**.

    ![Rimuovere un ruolo](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Nel messaggio che chiede di confermare, selezionare **Sì**.

    ![Confermare la rimozione](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    L'assegnazione di ruolo viene rimossa.

## <a name="authorization-error-when-assigning-roles"></a>Errore di autorizzazione durante l'assegnazione di ruoli

Se è stata abilitata di recente Privileged Identity Management per una sottoscrizione e si riceve un errore di autorizzazione quando si tenta di rendere un utente idoneo per un ruolo di amministratore di Azure AD, il problema potrebbe essere dovuto al fatto che l'entità servizio MS-PIM non dispone ancora delle autorizzazioni appropriate. L'entità servizio MS-PIM deve disporre del ruolo [amministratore accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) per assegnare i ruoli ad altri utenti. Anziché attendere l'assegnazione automatica di questo ruolo a MS-PIM, è possibile assegnarlo manualmente.

Per assegnare il ruolo Amministratore Accesso utenti all'entità servizio MS-PIM per una sottoscrizione, seguire questa procedura.

1. Accedere al portale di Azure come amministratore globale.

1. Scegliere **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Scegliere la propria sottoscrizione.

1. Scegliere **Controllo di accesso (IAM)**.

1. Scegliere **Assegnazioni di ruolo** per visualizzare l'elenco corrente delle assegnazioni di ruoli per l'ambito della sottoscrizione.

   ![Pannello Controllo di accesso (IAM) per una sottoscrizione](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Controllare se all'entità servizio **MS-PIM** è assegnato il ruolo **Amministratore Accesso utenti**.

1. In caso negativo, scegliere **Aggiungi un'assegnazione di ruolo** per aprire il riquadro **Aggiungi un'assegnazione di ruolo**.

1. Nell'elenco a discesa **Ruolo** selezionare **Amministratore Accesso utenti**.

1. Nell'elenco **Seleziona** trovare e selezionare l'entità servizio **MS-PIM**.

   ![Riquadro Aggiungi assegnazione ruolo-aggiungere autorizzazioni per l'entità servizio MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Scegliere **Salva** per assegnare un ruolo.

   Dopo qualche istante, all'entità servizio MS-PIM viene assegnato il ruolo Amministratore Accesso utenti nell'ambito della sottoscrizione.

   ![Pagina controllo di accesso che mostra l'assegnazione del ruolo amministratore accesso utenti per l'entità servizio MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo amministratore Azure AD in Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)