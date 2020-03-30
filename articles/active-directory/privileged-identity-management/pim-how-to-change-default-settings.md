---
title: Configurare le impostazioni dei ruoli di Azure AD in PIM - Azure AD Documenti Microsoft
description: Informazioni su come configurare le impostazioni dei ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205025"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurare le impostazioni dei ruoli di Azure AD in Gestione delle identità con privilegiConfigure Azure AD role settings in Privileged Identity Management

Un amministratore del ruolo Con privilegi può personalizzare la gestione delle identità con privilegi (PIM, Privileged Identity Management) nell'organizzazione di Azure Active Directory (Azure AD), inclusa la modifica dell'esperienza per un utente che sta attivando un'assegnazione di ruolo idonea.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte dei ruoli di Azure AD di Gestione delle identità con privilegi viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure.Start in November 2019, the Azure AD roles portion of Privileged Identity Management is being updated to a new version that matches the experiences for Azure resource roles. In questo modo vengono create funzionalità aggiuntive e [modifiche all'API esistente.](azure-ad-roles-features.md#api-changes) Durante l'implementazione della nuova versione, le procedure seguite in questo articolo dipendono dalla versione di Privileged Identity Management attualmente in uso. Seguire i passaggi descritti in questa sezione per determinare la versione di Gestione identità con privilegi in uso. Dopo aver conosciuto la versione di Gestione identità privilegiate, è possibile selezionare le procedure descritte in questo articolo che corrispondono a tale versione.

1. Accedere al [portale](https://portal.azure.com/) di Azure con un utente che risieda il ruolo di amministratore del ruolo Privileged.Sign in to the Azure portal with a user who is in the [Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role role role.
1. Aprire **Gestione identità con privilegi**di Azure AD . Se hai un banner nella parte superiore della pagina di panoramica, segui le istruzioni nella scheda **Nuova versione** di questo articolo. In caso contrario, seguire le istruzioni nella scheda **Versione precedente.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Seguire i passaggi descritti in questo articolo per approvare o rifiutare le richieste per i ruoli di Azure AD.

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo di Azure AD seguire questa procedura.

1. Accedere al portale di [Azure](https://portal.azure.com/) con un utente con ruolo amministratore del ruolo [con privilegi.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
gt
1. Aprire Le impostazioni ruolo dei **ruoli** &gt; **di**Azure **AD Privileged Identity Management** &gt; .

    ![Pagina Delle impostazioni del ruolo che elenca i ruoli di Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selezionare il ruolo di cui si desidera configurare le impostazioni.

    ![Pagina dei dettagli dell'impostazione dei ruoli in cui sono elencate diverse impostazioni di assegnazione e attivazione](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selezionare **Modifica** per aprire la pagina Impostazioni dei ruoli.

    ![Pagina Modifica impostazioni ruolo con le opzioni per aggiornare le impostazioni di assegnazione e attivazione](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Nella pagina delle impostazioni di ogni ruolo sono presenti numerose impostazioni che è possibile configurare.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Quando si configurano le impostazioni per un ruolo è possibile scegliere tra due opzioni di durata dell'assegnazione per ogni tipo di assegnazione (idoneo e attivo). Queste opzioni diventano la durata massima predefinita quando un utente viene assegnato al ruolo in Gestione identità privilegiate.

Puoi scegliere una di queste opzioni di durata dell'assegnazione **idonee:**

| | |
| --- | --- |
| **Allow permanent eligible assignment (Consenti assegnazione idonea permanente)** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono assegnare un'assegnazione idonea permanente. |
| **Scadenza dell'assegnazione idonea dopo** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono richiedere che tutte le assegnazioni idonee abbiano una data di inizio e una data di fine specificate. |

L'utente può scegliere una delle opzioni di durata dell'assegnazione **attiva**:

| | |
| --- | --- |
| **Allow permanent active assignment (Consenti assegnazione attiva permanente)** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono assegnare un'assegnazione attiva permanente. |
| **Scadenza dell'assegnazione attiva dopo** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono richiedere che tutte le assegnazioni attive abbiano una data di inizio e di fine specificate. |

> [!NOTE]
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate da amministratori globali e amministratori di ruoli con privilegi. Inoltre, gli utenti possono avviare richieste self-service per [estendere o rinnovare le assegnazioni](pim-resource-roles-renew-extend.md)di ruolo.

## <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Privileged Identity Management offre inoltre l'imposizione facoltativa di Azure Multi-Factor Authentication (MFA) per due scenari distinti.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Richiedi Multi-Factor Authentication in caso di assegnazione attiva

In alcuni casi, è possibile assegnare un utente a un ruolo per una breve durata (ad esempio, un giorno). In questo caso, gli utenti assegnati non devono richiedere l'attivazione. In questo scenario, la gestione delle identità con privilegi non può applicare l'autenticazione a più fattori quando l'utente utilizza l'assegnazione di ruolo perché sono già attivi nel ruolo dal momento in cui viene assegnato.

Per assicurarsi che l'amministratore che soddisfa l'assegnazione sia chi dichiara di essere, è possibile applicare l'autenticazione a più fattori all'assegnazione attiva selezionando la casella Richiedi autenticazione a più **fattori nell'assegnazione attiva.**

### <a name="require-multi-factor-authentication-on-activation"></a>Richiedi il servizio Multi-Factor Authentication all'attivazione

È possibile richiedere agli utenti idonei per un ruolo di dimostrare chi usano Azure Multi-Factor Authentication prima di poter essere attivati. L'autenticazione a più fattori assicura che l'utente sia chi dice di essere con ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente.

Per richiedere l'autenticazione a più fattori prima dell'attivazione, selezionare la casella **Richiedi Autenticazione a più fattori all'attivazione** nella scheda Assegnazione dell'impostazione **Modifica ruolo**.

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durata massima dell'attivazione

Usare il dispositivo di scorrimento **Durata massima dell'attivazione** per impostare il tempo massimo, espresso in ore, in cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra una e 24 ore.

## <a name="require-justification"></a>Immettere la giustificazione

È possibile richiedere agli utenti di immettere una motivazione aziendale quando vengono attivati. Per richiedere l'immissione di una giustificazione, selezionare l'opzione **Richiedi la giustificazione in caso di assegnazione attiva** o l'opzione **Richiedi la giustificazione all'attivazione**.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

Se si impostano più approvatori, l'approvazione viene completata non appena uno di essi approva o nega. Non è possibile richiedere l'approvazione di almeno due utenti. Per richiedere l'approvazione per attivare un ruolo, attenersi alla seguente procedura.

1. Selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

1. Selezionare **Seleziona approvatori**.

    ![Selezionare un riquadro utente o gruppo per selezionare gli approvatori](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selezionare almeno un utente e quindi fare clic su **Seleziona**. È necessario selezionare almeno un responsabile approvazione. Non esistono responsabili approvazione predefiniti.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato tutte le impostazioni del ruolo, selezionare **Aggiorna** per salvare le modifiche.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo di Azure AD seguire questa procedura.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Impostazioni**.

    ![Azure AD roles - Settings](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selezionare **Ruoli**.

1. Selezionare il ruolo di cui si desidera configurare le impostazioni.

    ![Azure AD roles - Settings Roles](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Nella pagina delle impostazioni per ogni ruolo sono presenti numerose impostazioni che è possibile configurare. Tali impostazioni interessano solo gli utenti **idonei** per le assegnazioni e non le assegnazioni **permanenti**.

## <a name="activations"></a>Attivazioni

Usare il dispositivo di scorrimento **Attivazioni** per impostare il tempo massimo, espresso in ore, per cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra 1 e 72 ore.

## <a name="notifications"></a>Notifiche

Usare il parametro **Notifiche** per determinare se gli amministratori riceveranno notifiche via email all'attivazione dei ruoli. Questa notifica può essere utile per rilevare attivazioni non autorizzate o illegittime.

Se impostato su **Abilita**, le notifiche sono inviate a:

- Amministratore dei ruoli con privilegi
- Amministratore della sicurezza
- Amministratore globale

Per ulteriori informazioni, vedere [Notifiche tramite posta elettronica in Gestione identità privilegiate](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Ticket di evento imprevisto/richiesta

Utilizzare l'interruttore **Ticket evento imprevisto/richiesta** per richiedere agli amministratori idonei di includere un numero di ticket quando attivano il proprio ruolo. Questa pratica può rendere più efficaci i controlli dell'accesso ai ruoli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Usare l'opzione **Multi-Factor Authentication** per specificare se richiedere agli utenti di verificare la propria identità con MFA prima di attivare i ruoli. Devono solo verificare la propria identità una volta per sessione, non ogni volta che attivano un ruolo. Tenere presente due suggerimenti quando si abilita l'autenticazione MFA:

- Gli utenti che dispongono di @outlook.comaccount Microsoft per i propri indirizzi di posta elettronica (in genere, ma non sempre) non possono registrarsi per Azure Multi-Factor Authentication. Se si desidera assegnare ruoli agli utenti con account Microsoft, è necessario renderli amministratori permanenti o disabilitare l'autenticazione a più fattori per tale ruolo.
- Non è possibile disabilitare Azure Multi-Factor Authentication per i ruoli con privilegi elevati per Azure AD e Office 365.You cannot disable Azure Multi-Factor Authentication for highly privileged roles for Azure AD and Office 365. Questa funzionalità di sicurezza consente di proteggere i ruoli seguenti:This safety feature helps protect the following roles:  
  
  - Amministratore di Azure Information ProtectionAzure Information Protection administrator
  - Amministratore fatturazione
  - Amministratore di applicazioni cloud
  - Amministratore di conformità
  - Amministratore di accesso condizionale
  - Amministratore di Dynamics 365
  - Responsabile approvazione per l'accesso a Customer Lockbox
  - Writer di directory
  - Amministratore di Exchange
  - Amministratore globale
  - Amministratore di Intune
  - Amministratore di Power BI
  - Amministratore dei ruoli con privilegi
  - Amministratore della sicurezza
  - Amministratore di SharePoint
  - Amministratore di Skype for Business
  - Amministratore utenti

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Richiedi approvazione

Se si desidera delegare l'approvazione necessaria per attivare un ruolo, attenersi alla seguente procedura.

1. Impostare l'opzione **Richiedi approvazione** su **Abilitato**. Il riquadro viene espanso con le opzioni per selezionare i responsabili dell'approvazione.

    ![Ruoli di Azure AD - Impostazioni - Richiedi approvazioneAzure AD roles - Settings - Require approval](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se non si specifica alcun approvatore, l'amministratore del ruolo Privileged diventa l'approvatore predefinito e deve quindi approvare tutte le richieste di attivazione per questo ruolo.

1. Per aggiungere responsabili approvazione, fare clic su **Seleziona responsabili approvazione**.

    ![Ruoli di Azure AD - Impostazioni - Richiedi approvazioneAzure AD roles - Settings - Require approval](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selezionare uno o più approvatori oltre all'amministratore del ruolo Privileged e quindi fare clic su **Seleziona**. Si consiglia di aggiungere almeno due approvatori. Anche se ti aggiungi come approvatore, non puoi approvare autonomamente l'attivazione di un ruolo. I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato tutte le impostazioni del ruolo, selezionare **Salva** per salvare le modifiche.

---

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurare gli avvisi di sicurezza per i ruoli di Azure AD in Gestione delle identità con privilegiConfigure security alerts for Azure AD roles in Privileged Identity Management](pim-how-to-configure-security-alerts.md)
