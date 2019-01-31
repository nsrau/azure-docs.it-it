---
title: Configurare le impostazioni dei ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come configurare le impostazioni dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 37938896c21bbe0ba5d6fafedc3c30ef52d3cfef
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153352"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Configurare le impostazioni dei ruoli delle risorse di Azure in PIM

Per configurare le impostazioni dei ruoli delle risorse di Azure, è necessario definire le impostazioni predefinite applicate alle assegnazioni dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM). Per configurare il flusso di lavoro di approvazione e specificare gli utenti che possono approvare o rifiutare le richieste, usare le procedure seguenti.

## <a name="open-role-settings"></a>Aprire le impostazioni del ruolo

Per aprire le impostazioni per un ruolo delle risorse di Azure seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Fare clic sulla risorsa da gestire, ad esempio una sottoscrizione o un gruppo di gestione.

    ![Elenco delle risorse di Azure da gestire](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Fare clic su **Impostazioni dei ruoli**.

    ![Impostazioni dei ruoli](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Fare clic sul ruolo di cui si intende configurare le impostazioni.

    ![Dettagli dell'impostazione del ruolo](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Fare clic su **Modifica** per aprire il riquadro Impostazioni dei ruoli.

    ![Modifica delle impostazioni dei ruoli](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Nella pagina delle impostazioni di ogni ruolo sono presenti numerose impostazioni che è possibile configurare.

## <a name="assignment-duration"></a>Durata dell'assegnazione

Quando si configurano le impostazioni per un ruolo è possibile scegliere tra due opzioni di durata dell'assegnazione per ogni tipo di assegnazione (idoneo e attivo). Queste opzioni diventano la durata massima predefinita quando un membro viene assegnato al ruolo in PIM.

È possibile scegliere una delle opzioni seguenti per la durata dell'assegnazione **idonea**:

| | |
| --- | --- |
| **Consenti le assegnazioni idonee permanenti** | Gli amministratori delle risorse possono assegnare l'appartenenza idonea permanente. |
| **Scadenza delle assegnazioni attive dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni idonee venga specificata una data di inizio e fine. |

L'utente può scegliere una delle opzioni di durata dell'assegnazione **attiva**:

| | |
| --- | --- |
| **Consenti l'assegnazione permanente attiva** | Gli amministratori delle risorse possono assegnare l'appartenenza attiva permanente. |
| **Scadenza delle assegnazioni attive dopo** | Gli amministratori delle risorse possono richiedere che per tutte le assegnazioni attive venga specificata una data di inizio e fine. |

> [!NOTE] 
> Tutte le assegnazioni con una data di fine specificata possono essere rinnovate dagli amministratori delle risorse. I membri possono anche avviare richieste self-service per [estendere o rinnovare le assegnazioni dei ruoli](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

PIM offre l'imposizione facoltativa dell'autenticazione MFA per due scenari distinti.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Richiedi Multi-Factor Authentication in caso di assegnazione attiva

Può essere necessario assegnare un membro a un ruolo per un breve periodo (un giorno, ad esempio). In questo caso, non è necessario che i membri assegnati richiedano l'attivazione. In questo scenario, PIM non può imporre l'autenticazione MFA quando il membro usa l'assegnazione di ruolo perché il membro è già attivo nel ruolo dal momento dell'assegnazione.

Per assicurarsi che l'amministratore delle risorse che gestisce l'assegnazione sia effettivamente chi dichiara di essere, è possibile imporre l'autenticazione MFA all'assegnazione attiva selezionando l'opzione **Richiedi Multi-Factor Authentication in caso di assegnazione attiva**.

### <a name="require-multi-factor-authentication-on-activation"></a>Richiedi il servizio Multi-Factor Authentication all'attivazione

È possibile richiedere ai membri idonei di un ruolo di completare l'autenticazione MFA prima di poter essere attivati. Questo processo assicura che l'utente che richiede l'attivazione sia effettivamente chi dichiara di essere con ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente.

Per richiedere a un membro idoneo di eseguire l'autenticazione MFA prima dell'attivazione, selezionare l'opzione **Richiedi il servizio Multi-Factor Authentication all'attivazione**.

Per altre informazioni, vedere [Multi-Factor Authentication (MFA) e PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Durata massima dell'attivazione

Usare il dispositivo di scorrimento **Durata massima dell'attivazione** per impostare il tempo massimo, espresso in ore, in cui un ruolo rimane attivo prima della scadenza. Questo valore può essere compreso tra 1 e 24 ore.

## <a name="require-justification"></a>Immettere la giustificazione

È possibile richiedere che i membri immettano una giustificazione in caso di assegnazione attiva o all'attivazione. Per richiedere l'immissione di una giustificazione, selezionare l'opzione **Richiedi la giustificazione in caso di assegnazione attiva** o l'opzione **Richiedi la giustificazione all'attivazione**.

## <a name="require-approval-to-activate"></a>Richiedere l'approvazione per l'attivazione

Se si vuole richiedere l'approvazione per attivare un ruolo, seguire questa procedura.

1. Selezionare la casella di controllo **Richiedi l'approvazione per l'attivazione**.

1. Fare clic su **Seleziona responsabili approvazione** per aprire il riquadro Selezionare un membro o un gruppo.

    ![Selezionare un membro o un gruppo](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selezionare almeno un membro o un gruppo e quindi fare clic su **Seleziona**. È possibile aggiungere qualsiasi combinazione di membri e i gruppi. È necessario selezionare almeno un responsabile approvazione. Non esistono responsabili approvazione predefiniti.

    I responsabili dell'approvazione selezionati vengono visualizzati in un apposito elenco.

1. Dopo aver specificato tutte le impostazioni dei ruoli, fare clic su **Aggiorna** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
- [Configurare gli avvisi di sicurezza per i ruoli delle risorse in PIM](pim-resource-roles-configure-alerts.md)
