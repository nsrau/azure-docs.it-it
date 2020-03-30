---
title: Configurare le impostazioni del ruolo delle risorse di Azure in PIM - Azure AD Documenti Microsoft
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
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638681"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Configurare le impostazioni del ruolo delle risorse di Azure in Gestione delle identità con privilegiConfigure Azure resource role settings in Privileged Identity Management

Quando si configurano le impostazioni dei ruoli delle risorse di Azure, si definiscono le impostazioni predefinite applicate alle assegnazioni dei ruoli delle risorse di Azure in Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Per configurare il flusso di lavoro di approvazione e specificare gli utenti che possono approvare o rifiutare le richieste, usare le procedure seguenti.

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo delle risorse di Azure seguire questa procedura.

1. Accedere al portale di [Azure](https://portal.azure.com/) con un utente con ruolo amministratore del ruolo [con privilegi.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa che si desidera gestire, ad esempio una sottoscrizione o un gruppo di gestione.

    ![Pagina Risorse di Azure che elencano le risorse che possono essere gestiteAzure resources page listing resources that can be managed](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selezionare **Impostazioni ruolo**.

    ![Pagina Delle impostazioni del ruolo che elenca i ruoli delle risorse di AzureRole settings page listing Azure resource roles](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selezionare il ruolo di cui si desidera configurare le impostazioni.

    ![Pagina dei dettagli dell'impostazione dei ruoli in cui sono elencate diverse impostazioni di assegnazione e attivazione](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selezionare **Modifica** per aprire il riquadro **Impostazioni ruolo.** La prima scheda consente di aggiornare la configurazione per l'attivazione dei ruoli in Gestione identità con privilegi.

    ![Pagina Modifica impostazioni ruolo con la scheda Attivazione aperta](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selezionare la scheda **Assegnazione** o il pulsante **Avanti: Assegnazione** nella parte inferiore della pagina per aprire la scheda delle impostazioni di assegnazione. Queste impostazioni controllano le assegnazioni di ruolo effettuate all'interno dell'interfaccia di Gestione identità con privilegi.

    ![Scheda Assegnazione ruolo nella pagina delle impostazioni del ruolo](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Utilizzare la scheda **Notifica** o il pulsante **Avanti: Attivazione** nella parte inferiore della pagina per accedere alla scheda delle impostazioni di notifica per questo ruolo. Queste impostazioni controllano tutte le notifiche di posta elettronica relative a questo ruolo.

    ![Scheda Notifiche ruolo nella pagina delle impostazioni del ruolo](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Nella scheda **Notifiche** della pagina delle impostazioni del ruolo, Gestione identità con privilegi consente un controllo granulare su chi riceve le notifiche e sulle notifiche che riceve.

    - **Disattivazione di un messaggio di posta elettronica**<br>È possibile disattivare e-mail specifiche deselezionando la casella di controllo destinatario predefinito ed eliminando eventuali destinatari aggiuntivi.  

    - **Limita le email agli indirizzi email specificati**<br>È possibile disattivare i messaggi di posta elettronica inviati ai destinatari predefiniti deselezionando la casella di controllo destinatario predefinito. È quindi possibile aggiungere altri indirizzi di posta elettronica come destinatari aggiuntivi. Se si desidera aggiungere più di un indirizzo di posta elettronica, separarli utilizzando un punto e virgola (;).

    - **Inviare messaggi di posta elettronica sia ai destinatari predefiniti che a quelli aggiuntivi**<br>È possibile inviare messaggi di posta elettronica sia al destinatario predefinito che a quello aggiuntivo selezionando la casella di controllo del destinatario predefinito e aggiungendo indirizzi di posta elettronica per altri destinatari.

    - **Solo e-mail critiche**<br>Per ogni tipo di e-mail, è possibile selezionare la casella di controllo per ricevere solo le e-mail critiche. Ciò significa che Gestione identità privilegiate continuerà a inviare messaggi di posta elettronica ai destinatari configurati solo quando il messaggio di posta elettronica richiede un'azione immediata. Ad esempio, i messaggi di posta elettronica che chiedono agli utenti di estendere l'assegnazione di ruolo non verranno attivati mentre verranno attivati messaggi di posta elettronica che richiedono agli amministratori di approvare una richiesta di estensione.

1. Selezionare il pulsante **Aggiorna** in qualsiasi momento per aggiornare le impostazioni del ruolo.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Quando si configurano le impostazioni per un ruolo è possibile scegliere tra due opzioni di durata dell'assegnazione per ogni tipo di assegnazione (idoneo e attivo). Queste opzioni diventano la durata massima predefinita quando un utente viene assegnato al ruolo in Gestione identità privilegiate.

Puoi scegliere una di queste opzioni di durata dell'assegnazione **idonee:**

| | |
| --- | --- |
| **Allow permanent eligible assignment (Consenti assegnazione idonea permanente)** | Gli amministratori delle risorse possono assegnare un'assegnazione idonea permanente. |
| **Scadenza dell'assegnazione idonea dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni idonee venga specificata una data di inizio e fine. |

L'utente può scegliere una delle opzioni di durata dell'assegnazione **attiva**:

| | |
| --- | --- |
| **Allow permanent active assignment (Consenti assegnazione attiva permanente)** | Gli amministratori delle risorse possono assegnare un'assegnazione attiva permanente. |
| **Scadenza dell'assegnazione attiva dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni attive venga specificata una data di inizio e fine. |

> [!NOTE]
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori delle risorse. Inoltre, gli utenti possono avviare richieste self-service per [estendere o rinnovare le assegnazioni](pim-resource-roles-renew-extend.md)di ruolo.

## <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Privileged Identity Management offre inoltre l'imposizione facoltativa di Azure Multi-Factor Authentication (MFA) per due scenari distinti.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Richiedi Multi-Factor Authentication in caso di assegnazione attiva

In alcuni casi, è possibile assegnare un utente o un gruppo a un ruolo per un breve periodo (ad esempio un giorno). In questo caso, gli utenti assegnati non devono richiedere l'attivazione. In questo scenario, la gestione delle identità con privilegi non può applicare l'autenticazione a più fattori quando l'utente utilizza l'assegnazione di ruolo perché sono già attivi nel ruolo dal momento in cui viene assegnato.

Per assicurarsi che l'amministratore delle risorse che soddisfa l'assegnazione sia chi dichiara di essere, è possibile applicare l'autenticazione a più fattori all'assegnazione attiva selezionando la casella Richiedi autenticazione a **più fattori nell'assegnazione attiva.**

### <a name="require-multi-factor-authentication-on-activation"></a>Richiedi il servizio Multi-Factor Authentication all'attivazione

È possibile richiedere agli utenti idonei per un ruolo di dimostrare chi usano Azure Multi-Factor Authentication prima di poter essere attivati. L'autenticazione a più fattori assicura che l'utente sia chi dice di essere con ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente.

Per richiedere l'autenticazione a più fattori prima dell'attivazione, selezionare la casella **Richiedi autenticazione a più fattori all'attivazione.**

Per altre informazioni, vedere [Autenticazione a più fattori e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durata massima dell'attivazione

Usare il dispositivo di scorrimento **Durata massima dell'attivazione** per impostare il tempo massimo, espresso in ore, in cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra una e 24 ore.

## <a name="require-justification"></a>Immettere la giustificazione

È possibile richiedere agli utenti di immettere una motivazione aziendale quando vengono attivati. Per richiedere l'immissione di una giustificazione, selezionare l'opzione **Richiedi la giustificazione in caso di assegnazione attiva** o l'opzione **Richiedi la giustificazione all'attivazione**.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

Se si vuole richiedere l'approvazione per attivare un ruolo, seguire questa procedura.

1. Selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

1. Selezionare **Seleziona responsabili approvazione** per aprire la pagina **Seleziona un membro o un gruppo.**

    ![Selezionare un riquadro utente o gruppo per selezionare gli approvatori](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selezionare almeno un utente o un gruppo e quindi fare clic su **Seleziona**. È possibile aggiungere qualsiasi combinazione di utenti e gruppi. È necessario selezionare almeno un responsabile approvazione. Non esistono responsabili approvazione predefiniti.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato tutte le impostazioni del ruolo, selezionare **Aggiorna** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Gestione identità con privilegiConfigure security alerts for Azure resource roles in Privileged Identity Management](pim-resource-roles-configure-alerts.md)
