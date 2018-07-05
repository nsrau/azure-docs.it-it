---
title: Applicazione di Azure Multi-Factor Authentication per le risorse di Azure usando Privileged Identity Management | Microsoft Docs
description: Questo documento descrive come abilitare l'autenticazione a più fattori per le risorse PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 21f6fef214f27630ff0eadc39e1e26c9c344f353
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444733"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Applicazione di Azure Multi-Factor Authentication per le risorse di Azure usando Privileged Identity Management

Privileged Identity Management (PIM) per i ruoli delle risorse di Azure consente agli amministratori delle risorse e agli amministratori delle identità di proteggere l'infrastruttura cruciale di Azure con l'appartenenza con limiti di tempo e l'accesso Just-In-Time. PIM offre inoltre l'applicazione facoltativa di Azure Multi-Factor Authentication (MFA) per due scenari distinti.

## <a name="require-multi-factor-authentication-to-activate"></a>Richiedi il servizio Multi-Factor Authentication per l'attivazione

Gli amministratori delle risorse possono richiedere ai membri idonei di un ruolo di eseguire MFA di Azure prima di poter essere attivati. Questo processo assicura che l'utente che richiede l'attivazione sia effettivamente chi dichiara di essere con ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente. 

Per imporre questo requisito, selezionare una risorsa nell'elenco delle risorse gestite. Dal [dashboard di panoramica](pim-resource-roles-overview-dashboards.md) selezionare un ruolo nell'elenco dei ruoli nella parte inferiore destra della schermata.

Inoltre, è possibile ottenere le impostazioni dei ruoli dalle schede **Ruoli** o **Impostazioni dei ruoli** nel riquadro a sinistra.

>[!Note]
>Se le opzioni nel riquadro a sinistra sono inattive e viene visualizzato un banner nella parte superiore della pagina che indica "You have eligible roles that can be activated" (Sono disponibili ruoli idonei che possono essere attivati), ciò significa che non si è un amministratore attivo. In tal caso è necessario eseguire l'[attivazione](pim-resource-roles-activate-your-roles.md) prima di continuare.

![Schede "Ruoli " e "Impostazioni dei ruoli" ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Per visualizzare l'appartenenza di un ruolo, selezionare **Impostazioni dei ruoli** dalla barra nella parte superiore della schermata per aprire i **Dettagli delle impostazioni dei ruoli**.

Fare clic sul pulsante **Modifica** in alto per modificare le impostazioni dei ruoli.

Nella sezione che si trova in **Attiva** fare clic sulla casella di controllo **Richiedi il servizio Multi-Factor Authentication per l'attivazione** e quindi fare clic su Salva. Selezionare quindi **Salva**.

![Richiedi il servizio Multi-Factor Authentication all'attivazione](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Richiedi il servizio Multi-Factor Authentication all'assegnazione

In alcuni casi, è possibile che un amministratore delle risorse voglia assegnare un membro a un ruolo per un breve periodo (un giorno, ad esempio). In questo caso, non è necessario che i membri assegnati ne richiedano l'attivazione. In questo scenario, PIM non può imporre l'autenticazione MFA quando il membro usa l'assegnazione di ruolo perché il membro è già attivo nel ruolo dal momento dell'assegnazione.

Per assicurarsi che l'amministratore delle risorse che gestisce l'assegnazione sia effettivamente chi dichiara di essere, è possibile imporre l'autenticazione MFA al momento dell'assegnazione.

Dalla stessa schermata dei dettagli delle impostazioni dei ruoli, selezionare la casella di controllo **Richiedi il servizio Multi-Factor Authentication all'assegnazione**.

![Richiedi il servizio Multi-Factor Authentication all'assegnazione](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Passaggi successivi

[Richiedere l'approvazione per l'attivazione](pim-resource-roles-approval-workflow.md)

[Usare il log di controllo](pim-resource-roles-use-the-audit-log.md)



