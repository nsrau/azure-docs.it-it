---
title: Configurare le impostazioni del ruolo delle risorse di Azure in PIM-Azure AD | Microsoft Docs
description: Informazioni su come configurare le impostazioni dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 8c9acc1487fcbf8398b7a556c63f97963b264451
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74182730"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management

Quando si configurano le impostazioni del ruolo delle risorse di Azure, si definiscono le impostazioni predefinite applicate alle assegnazioni di ruolo delle risorse di Azure in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Per configurare il flusso di lavoro di approvazione e specificare gli utenti che possono approvare o rifiutare le richieste, usare le procedure seguenti.

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo delle risorse di Azure seguire questa procedura.

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente nel ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa che si desidera gestire, ad esempio una sottoscrizione o un gruppo di gestione.

    ![Pagina delle risorse di Azure che elenca le risorse che possono essere gestite](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selezionare **impostazioni ruolo**.

    ![Pagina impostazioni ruolo che elenca i ruoli delle risorse di Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selezionare il ruolo di cui si desidera configurare le impostazioni.

    ![Pagina dei dettagli dell'impostazione del ruolo che elenca diverse impostazioni di assegnazione e attivazione](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selezionare **modifica** per aprire il riquadro **impostazioni ruolo** . La prima scheda consente di aggiornare la configurazione per l'attivazione del ruolo in Privileged Identity Management.

    ![Pagina Modifica impostazioni ruolo con scheda attivazione aperta](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selezionare la scheda **assegnazione** o il pulsante **Avanti: assegnazione** nella parte inferiore della pagina per aprire la scheda Impostazioni assegnazione. Queste impostazioni controllano le assegnazioni di ruolo eseguite all'interno dell'interfaccia Privileged Identity Management.

    ![Scheda assegnazione ruolo nella pagina impostazioni ruolo](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Usare la scheda **notifica** o il pulsante **Avanti: attivazione** nella parte inferiore della pagina per ottenere la scheda delle impostazioni di notifica per questo ruolo. Queste impostazioni controllano tutte le notifiche di posta elettronica correlate a questo ruolo.

    > [!NOTE]
    > È in corso il rollback di questa interfaccia utente delle notifiche tra aree e sottoscrizioni di Azure. Se non è possibile configurare queste notifiche granulari, verificare di nuovo in un giorno o due.

    ![Scheda notifiche ruolo nella pagina impostazioni ruolo](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

1. Selezionare il pulsante **Aggiorna** in qualsiasi momento per aggiornare le impostazioni del ruolo.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Quando si configurano le impostazioni per un ruolo è possibile scegliere tra due opzioni di durata dell'assegnazione per ogni tipo di assegnazione (idoneo e attivo). Queste opzioni diventano la durata massima predefinita quando un utente viene assegnato al ruolo in Privileged Identity Management.

È possibile scegliere una delle opzioni seguenti per la durata dell'assegnazione **idonea**:

| | |
| --- | --- |
| **Consenti le assegnazioni idonee permanenti** | Gli amministratori delle risorse possono assegnare un'assegnazione idonea permanente. |
| **Scadenza delle assegnazioni attive dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni idonee venga specificata una data di inizio e fine. |

L'utente può scegliere una delle opzioni di durata dell'assegnazione **attiva**:

| | |
| --- | --- |
| **Consenti l'assegnazione permanente attiva** | Gli amministratori delle risorse possono assegnare un'assegnazione attiva permanente. |
| **Scadenza delle assegnazioni attive dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni attive venga specificata una data di inizio e fine. |

> [!NOTE]
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori delle risorse. Inoltre, gli utenti possono avviare richieste self-service per [estendere o rinnovare le assegnazioni di ruolo](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Privileged Identity Management offre inoltre l'imposizione facoltativa di Azure Multi-Factor Authentication (MFA) per due scenari distinti.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Richiedi Multi-Factor Authentication in caso di assegnazione attiva

In alcuni casi, potrebbe essere necessario assegnare un utente o un gruppo a un ruolo per una breve durata (ad esempio, un giorno). In questo caso, gli utenti assegnati non devono richiedere l'attivazione. In questo scenario, Privileged Identity Management non può applicare l'autenticazione a più fattori quando l'utente usa l'assegnazione di ruolo perché è già attiva nel ruolo dal momento in cui viene assegnata.

Per assicurarsi che l'amministratore delle risorse che soddisfi l'assegnazione sia chi dichiara di essere, è possibile applicare l'autenticazione a più fattori nell'assegnazione attiva selezionando la casella di controllo **richiedi multi-factor authentication su assegnazione attiva** .

### <a name="require-multi-factor-authentication-on-activation"></a>Richiedi il servizio Multi-Factor Authentication all'attivazione

È possibile richiedere agli utenti idonei per un ruolo di dimostrare chi sta usando Azure Multi-Factor Authentication prima di poter attivare. L'autenticazione a più fattori garantisce che l'utente sia quello che afferma di essere con una ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente.

Per richiedere l'autenticazione a più fattori prima dell'attivazione, selezionare la casella **richiedi multi-factor authentication on Activation** .

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durata massima dell'attivazione

Usare il dispositivo di scorrimento **Durata massima dell'attivazione** per impostare il tempo massimo, espresso in ore, in cui un ruolo rimane attivo prima della scadenza. Il valore può essere compreso tra 1 e 24 ore.

## <a name="require-justification"></a>Immettere la giustificazione

È possibile richiedere agli utenti di immettere una giustificazione aziendale quando vengono attivati. Per richiedere l'immissione di una giustificazione, selezionare l'opzione **Richiedi la giustificazione in caso di assegnazione attiva** o l'opzione **Richiedi la giustificazione all'attivazione**.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

Se si vuole richiedere l'approvazione per attivare un ruolo, seguire questa procedura.

1. Selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

1. Selezionare **Seleziona responsabili approvazione** per aprire la pagina **selezionare un membro o un gruppo** .

    ![Selezionare un riquadro utente o gruppo per selezionare i responsabili approvazione](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selezionare almeno un utente o un gruppo e quindi fare clic su **Seleziona**. È possibile aggiungere qualsiasi combinazione di utenti e gruppi. È necessario selezionare almeno un responsabile approvazione. Non esistono responsabili approvazione predefiniti.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato le impostazioni di tutti i ruoli, selezionare **Aggiorna** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-alerts.md)
