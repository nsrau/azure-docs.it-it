---
title: Configurare le impostazioni del ruolo Azure AD in PIM-Azure AD | Microsoft Docs
description: Informazioni su come configurare le impostazioni del ruolo Azure AD in Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205025"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management

Un amministratore del ruolo con privilegi può personalizzare Privileged Identity Management (PIM) nell'organizzazione Azure Active Directory (Azure AD), inclusa la modifica dell'esperienza di un utente che sta attivando un'assegnazione di ruolo idonea.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte Azure AD ruoli di Privileged Identity Management viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure. In questo modo vengono create funzionalità aggiuntive e le [modifiche apportate all'API esistente](azure-ad-roles-features.md#api-changes). Mentre è in corso il rollback della nuova versione, le procedure descritte in questo articolo dipendono dalla versione di Privileged Identity Management attualmente disponibile. Attenersi alla procedura descritta in questa sezione per determinare la versione di Privileged Identity Management. Quando si conosce la versione di Privileged Identity Management, è possibile selezionare le procedure descritte in questo articolo corrispondenti a tale versione.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente appartenente al ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Aprire **Azure AD Privileged Identity Management**. Se si dispone di un banner nella parte superiore della pagina Panoramica, seguire le istruzioni riportate nella scheda **nuova versione** di questo articolo. In caso contrario, seguire le istruzioni riportate nella scheda **versione precedente** .

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Attenersi alla procedura descritta in questo articolo per approvare o negare le richieste di Azure AD ruoli.

# <a name="new-version"></a>[Nuova versione](#tab/new)

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo di Azure AD seguire questa procedura.

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente nel ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
gt
1. Aprire **Azure AD Privileged Identity Management** &gt; **Azure ad ruoli** &gt; **impostazioni del ruolo**.

    ![Pagina impostazioni ruolo che elenca Azure AD ruoli](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selezionare il ruolo di cui si desidera configurare le impostazioni.

    ![Pagina dei dettagli dell'impostazione del ruolo che elenca diverse impostazioni di assegnazione e attivazione](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selezionare **modifica** per aprire la pagina impostazioni ruolo.

    ![Pagina Modifica impostazioni ruolo con le opzioni per aggiornare le impostazioni di assegnazione e attivazione](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Nella pagina delle impostazioni di ogni ruolo sono presenti numerose impostazioni che è possibile configurare.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Quando si configurano le impostazioni per un ruolo è possibile scegliere tra due opzioni di durata dell'assegnazione per ogni tipo di assegnazione (idoneo e attivo). Queste opzioni diventano la durata massima predefinita quando un utente viene assegnato al ruolo in Privileged Identity Management.

È possibile scegliere una delle opzioni seguenti per la durata dell'assegnazione **idonea**:

| | |
| --- | --- |
| **Consenti le assegnazioni idonee permanenti** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono assegnare un'assegnazione idonea permanente. |
| **Scadenza delle assegnazioni attive dopo** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono richiedere che tutte le assegnazioni idonee dispongano di una data di inizio e di fine specificata. |

L'utente può scegliere una delle opzioni di durata dell'assegnazione **attiva**:

| | |
| --- | --- |
| **Consenti l'assegnazione permanente attiva** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono assegnare un'assegnazione attiva permanente. |
| **Scadenza delle assegnazioni attive dopo** | Gli amministratori globali e gli amministratori dei ruoli con privilegi possono richiedere che tutte le assegnazioni attive abbiano una data di inizio e di fine specificata. |

> [!NOTE]
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori globali e dai ruoli con privilegi. Inoltre, gli utenti possono avviare richieste self-service per [estendere o rinnovare le assegnazioni di ruolo](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Privileged Identity Management offre inoltre l'imposizione facoltativa di Azure Multi-Factor Authentication (MFA) per due scenari distinti.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Richiedi Multi-Factor Authentication in caso di assegnazione attiva

In alcuni casi, potrebbe essere necessario assegnare un utente a un ruolo per un breve periodo di tempo (ad esempio, un giorno). In questo caso, gli utenti assegnati non devono richiedere l'attivazione. In questo scenario, Privileged Identity Management non può applicare l'autenticazione a più fattori quando l'utente usa l'assegnazione di ruolo perché è già attiva nel ruolo dal momento in cui viene assegnata.

Per assicurarsi che l'amministratore che esegue l'assegnazione sia colui che dichiara di essere, è possibile applicare l'autenticazione a più fattori per l'assegnazione attiva controllando la casella **richiedi multi-factor authentication in assegnazione attiva** .

### <a name="require-multi-factor-authentication-on-activation"></a>Richiedi il servizio Multi-Factor Authentication all'attivazione

È possibile richiedere agli utenti idonei per un ruolo di dimostrare chi sta usando Azure Multi-Factor Authentication prima di poter attivare. L'autenticazione a più fattori garantisce che l'utente sia quello che afferma di essere con una ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente.

Per richiedere l'autenticazione a più fattori prima dell'attivazione, selezionare la casella **richiedi multi-factor authentication all'attivazione** nella scheda assegnazione dell' **impostazione modifica ruolo**.

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durata massima dell'attivazione

Usare il dispositivo di scorrimento **Durata massima dell'attivazione** per impostare il tempo massimo, espresso in ore, in cui un ruolo rimane attivo prima della scadenza. Il valore può essere compreso tra 1 e 24 ore.

## <a name="require-justification"></a>Immettere la giustificazione

È possibile richiedere agli utenti di immettere una giustificazione aziendale quando vengono attivati. Per richiedere l'immissione di una giustificazione, selezionare l'opzione **Richiedi la giustificazione in caso di assegnazione attiva** o l'opzione **Richiedi la giustificazione all'attivazione**.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

Se si imposta più responsabili approvazione, l'approvazione viene completata non appena uno di essi approva o nega. Non è possibile richiedere l'approvazione da almeno due utenti. Per richiedere l'approvazione per l'attivazione di un ruolo, attenersi alla seguente procedura.

1. Selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

1. Selezionare **Seleziona responsabili approvazione**.

    ![Selezionare un riquadro utente o gruppo per selezionare i responsabili approvazione](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selezionare almeno un utente e quindi fare clic su **Seleziona**. È necessario selezionare almeno un responsabile approvazione. Non esistono responsabili approvazione predefiniti.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato le impostazioni di tutti i ruoli, selezionare **Aggiorna** per salvare le modifiche.

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo di Azure AD seguire questa procedura.

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Impostazioni**.

    ![Ruoli di Azure AD-impostazioni](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selezionare **Ruoli**.

1. Selezionare il ruolo di cui si desidera configurare le impostazioni.

    ![Ruoli di Azure AD-ruoli delle impostazioni](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Nella pagina delle impostazioni per ogni ruolo sono presenti numerose impostazioni che è possibile configurare. Tali impostazioni interessano solo gli utenti **idonei** per le assegnazioni e non le assegnazioni **permanenti**.

## <a name="activations"></a>Attivazioni

Usare il dispositivo di scorrimento **Attivazioni** per impostare il tempo massimo, espresso in ore, per cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra 1 e 72 ore.

## <a name="notifications"></a>Notifiche

Usare il parametro **Notifiche** per determinare se gli amministratori riceveranno notifiche via email all'attivazione dei ruoli. Questa notifica può essere utile per rilevare le attivazioni non autorizzate o illegittime.

Se impostato su **Abilita**, le notifiche sono inviate a:

- Amministratore dei ruoli con privilegi
- Amministratore della sicurezza
- Amministratore globale

Per ulteriori informazioni, vedere [notifiche tramite posta elettronica in Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Ticket di evento imprevisto/richiesta

Usare l' **opzione** per richiedere agli amministratori idonei di includere un numero di ticket quando attivano il proprio ruolo. Questa procedura può rendere più efficienti i controlli di accesso ai ruoli.

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori

Usare l'opzione **Multi-Factor Authentication** per specificare se richiedere agli utenti di verificare la propria identità con MFA prima di attivare i ruoli. È necessario verificarne l'identità una sola volta per ogni sessione, non ogni volta che viene attivato un ruolo. Tenere presente due suggerimenti quando si abilita l'autenticazione MFA:

- Gli utenti che dispongono di account Microsoft per gli indirizzi di posta elettronica (in genere @outlook.com, ma non sempre) non possono registrarsi per Multi-Factor Authentication di Azure. Se si desidera assegnare ruoli agli utenti con account Microsoft, è necessario renderli amministratori permanenti o disabilitare l'autenticazione a più fattori per quel ruolo.
- Non è possibile disabilitare Multi-Factor Authentication di Azure per i ruoli con privilegi elevati per Azure AD e Office 365. Questa funzionalità di sicurezza consente di proteggere i ruoli seguenti:  
  
  - Amministratore Azure Information Protection
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
  - Amministratore Power BI
  - Amministratore dei ruoli con privilegi
  - Amministratore della sicurezza
  - Amministratore di SharePoint
  - Amministratore di Skype for Business
  - Amministratore utenti

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Richiedi approvazione

Se si desidera delegare l'approvazione richiesta per attivare un ruolo, attenersi alla seguente procedura.

1. Impostare l'opzione **Richiedi approvazione** su **Abilitato**. Il riquadro viene espanso con le opzioni per selezionare i responsabili dell'approvazione.

    ![Ruoli Azure AD-impostazioni-Richiedi approvazione](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se non vengono specificati responsabili approvazione, l'amministratore del ruolo con privilegi diventa il responsabile approvazione predefinito ed è quindi necessario per approvare tutte le richieste di attivazione per questo ruolo.

1. Per aggiungere responsabili approvazione, fare clic su **Seleziona responsabili approvazione**.

    ![Ruoli Azure AD-impostazioni-Richiedi approvazione](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selezionare uno o più responsabili approvazione oltre all'amministratore dei ruoli con privilegi, quindi fare clic su **Seleziona**. È consigliabile aggiungere almeno due responsabili approvazione. Anche se si aggiunge se stessi come responsabile approvazione, non è possibile approvare autonomamente l'attivazione di un ruolo. I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato le impostazioni di tutti i ruoli, selezionare **Salva** per salvare le modifiche.

---

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurare gli avvisi di sicurezza per i ruoli di Azure AD in Privileged Identity Management](pim-how-to-configure-security-alerts.md)
