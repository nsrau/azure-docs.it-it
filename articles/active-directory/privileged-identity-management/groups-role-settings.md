---
title: Configurare le impostazioni di gruppi di accesso con privilegi in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare le impostazioni dei gruppi assegnabili ai ruoli in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb7f93437cd45914d3824e9f557241ba0d71162
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835391"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Configurare le impostazioni del gruppo di accesso con privilegi (anteprima) in Privileged Identity Management

Le impostazioni del ruolo sono le impostazioni predefinite applicate alle assegnazioni di accesso con privilegi per il proprietario del gruppo e i membri del gruppo in Privileged Identity Management (PIM). Usare la procedura seguente per configurare il flusso di lavoro di approvazione per specificare gli utenti che possono approvare o negare le richieste di elevazione dei privilegi.

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Seguire questa procedura per aprire le impostazioni per un ruolo del gruppo di accesso con privilegi di Azure.

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente nel ruolo di [amministratore dei ruoli con privilegi](../roles/permissions-reference.md#privileged-role-administrator) .

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **accesso con privilegi (anteprima)**.

1. Selezionare il gruppo che si desidera gestire.

    ![Gruppi di accesso con privilegi filtrati in base al nome di un gruppo](./media/groups-role-settings/group-select.png)

1. Selezionare **Impostazioni**.

    ![Pagina impostazioni che elenca le impostazioni di gruppo per il gruppo selezionato](./media/groups-role-settings/group-settings-select-role.png)

1. Selezionare il proprietario o il ruolo membro di cui si desidera visualizzare o modificare le impostazioni. È possibile visualizzare le impostazioni correnti per il ruolo nella pagina **Dettagli impostazione ruolo** .

    ![Pagina dei dettagli dell'impostazione del ruolo che elenca diverse impostazioni di assegnazione e attivazione](./media/groups-role-settings/group-role-setting-details.png)

1. Selezionare **modifica** per aprire la pagina **Modifica impostazione ruolo** . La scheda **attivazione** consente di modificare le impostazioni di attivazione del ruolo, ad esempio se consentire le assegnazioni permanenti idonee e attive.

    ![Pagina Modifica impostazioni ruolo con scheda attivazione aperta](./media/groups-role-settings/role-settings-activation-tab.png)

1. Selezionare la scheda **assegnazione** per aprire la scheda Impostazioni di assegnazione. Queste impostazioni controllano le impostazioni di assegnazione Privileged Identity Management per questo ruolo.

    ![Scheda assegnazione ruolo nella pagina impostazioni ruolo](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Usare la scheda **notifica** o il pulsante **Avanti: attivazione** nella parte inferiore della pagina per ottenere la scheda delle impostazioni di notifica per questo ruolo. Queste impostazioni controllano tutte le notifiche di posta elettronica correlate a questo ruolo.

    ![Scheda notifiche ruolo nella pagina impostazioni ruolo](./media/groups-role-settings/role-settings-notification-tab.png)

1. Selezionare il pulsante **Aggiorna** in qualsiasi momento per aggiornare le impostazioni del ruolo.

Nella scheda **notifiche** della pagina impostazioni ruolo Privileged Identity Management consente un controllo granulare su chi riceve le notifiche e quali notifiche ricevono.

- **Spegnimento di un messaggio di posta elettronica**<br>È possibile disattivare i messaggi di posta elettronica specifici deselezionando la casella di controllo destinatario predefinito ed eliminando eventuali destinatari aggiuntivi.  
- **Limita i messaggi di posta elettronica agli indirizzi di posta elettronica specificati**<br>È possibile disattivare i messaggi di posta elettronica inviati ai destinatari predefiniti cancellando la casella di controllo destinatario predefinito. È quindi possibile aggiungere altri indirizzi di posta elettronica come destinatari aggiuntivi. Se si vuole aggiungere più di un indirizzo di posta elettronica, separarli con un punto e virgola (;).
- **Invia messaggi di posta elettronica sia ai destinatari predefiniti che ai destinatari aggiuntivi**<br>È possibile inviare messaggi di posta elettronica sia al destinatario predefinito che al destinatario aggiuntivo selezionando la casella di controllo destinatario predefinito e aggiungendo gli indirizzi di posta elettronica per altri destinatari.
- **Solo messaggi di posta elettronica critici**<br>Per ogni tipo di messaggio di posta elettronica, è possibile selezionare la casella di controllo per ricevere solo messaggi di posta elettronica critici. Ciò significa che Privileged Identity Management continuerà a inviare messaggi di posta elettronica ai destinatari configurati solo quando il messaggio di posta elettronica richiede un'azione immediata. Ad esempio, i messaggi di posta elettronica che chiedono agli utenti di estendere l'assegnazione di ruolo non verranno attivati, mentre i messaggi di posta elettronica che richiedono agli amministratori di approvare una richiesta di estensione verranno attivati.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Quando si configurano le impostazioni per un ruolo è possibile scegliere tra due opzioni di durata dell'assegnazione per ogni tipo di assegnazione (idoneo e attivo). Queste opzioni diventano la durata massima predefinita quando un utente viene assegnato al ruolo in Privileged Identity Management.

È possibile scegliere una delle opzioni di durata di assegnazione **idonee** seguenti:

| | |
| --- | --- |
| **Allow permanent eligible assignment (Consenti assegnazione idonea permanente)** | Gli amministratori delle risorse possono assegnare un'assegnazione idonea permanente. |
| **Scadenza assegnazione idonea dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni idonee venga specificata una data di inizio e fine. |

L'utente può scegliere una delle opzioni di durata dell'assegnazione **attiva**:

| | |
| --- | --- |
| **Allow permanent active assignment (Consenti assegnazione attiva permanente)** | Gli amministratori delle risorse possono assegnare un'assegnazione attiva permanente. |
| **Scadenza dell'assegnazione attiva dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni attive venga specificata una data di inizio e fine. |

> [!NOTE]
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori delle risorse. Inoltre, gli utenti possono avviare richieste self-service per [estendere o rinnovare le assegnazioni di ruolo](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Privileged Identity Management fornisce l'applicazione facoltativa del Multi-Factor Authentication Azure AD per due scenari distinti.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Richiedi Multi-Factor Authentication in caso di assegnazione attiva

In alcuni casi, potrebbe essere necessario assegnare un utente o un gruppo a un ruolo per una breve durata (ad esempio, un giorno). In questo caso, gli utenti assegnati non devono richiedere l'attivazione. In questo scenario, Privileged Identity Management non può applicare l'autenticazione a più fattori quando l'utente usa l'assegnazione di ruolo perché è già attiva nel ruolo dal momento in cui viene assegnata.

Per assicurarsi che l'amministratore delle risorse che soddisfi l'assegnazione sia chi dichiara di essere, è possibile applicare l'autenticazione a più fattori nell'assegnazione attiva selezionando la casella di controllo **richiedi multi-factor authentication su assegnazione attiva** .

### <a name="require-multi-factor-authentication-on-activation"></a>Richiedi il servizio Multi-Factor Authentication all'attivazione

È possibile richiedere agli utenti idonei per un ruolo di dimostrare chi utilizza Azure AD Multi-Factor Authentication prima di poter eseguire l'attivazione. L'autenticazione a più fattori garantisce che l'utente sia quello che afferma di essere con una ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente.

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

    ![Selezionare un riquadro utente o gruppo per selezionare i responsabili approvazione](./media/groups-role-settings/group-settings-select-approvers.png)

1. Selezionare almeno un utente o un gruppo e quindi fare clic su **Seleziona**. È possibile aggiungere qualsiasi combinazione di utenti e gruppi. È necessario selezionare almeno un responsabile approvazione. Non esistono responsabili approvazione predefiniti.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato le impostazioni di tutti i ruoli, selezionare **Aggiorna** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare l'appartenenza a un gruppo di accesso con privilegi o la proprietà in PIM](groups-assign-member-owner.md)
