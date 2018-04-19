---
title: Privileged Identity Management per risorse di Azure - MFA | Microsoft Docs
description: Questo documento descrive come abilitare l'autenticazione a più fattori per le risorse PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - Ruoli delle risorse - MFA

PIM per i ruoli delle risorse di Azure consente agli amministratori delle risorse e agli amministratori delle identità di proteggere l'infrastruttura cruciale di Azure con l'appartenenza con limiti di tempo e l'accesso Just-In-Time. PIM offre inoltre l'imposizione facoltativa di Azure Multi-Factor Authentication (MFA) per due scenari distinti.

## <a name="require-mfa-to-activate"></a>Richiedere l'autenticazione MFA per l'attivazione

Gli amministratori delle risorse possono richiedere ai membri idonei di un ruolo di completare l'autenticazione MFA di Azure prima di poter essere attivati. Questo processo assicura che l'utente che richiede l'attivazione sia effettivamente chi dichiara di essere con ragionevole certezza. L'imposizione di questa opzione consente di proteggere risorse critiche in situazioni di potenziale compromissione dell'account utente. 

Per imporre questo requisito, selezionare una risorsa nell'elenco delle risorse gestite. Dal [dashboard di panoramica](pim-resource-roles-overview-dashboards.md) selezionare un ruolo nell'elenco dei ruoli nella parte inferiore destra della schermata.

Inoltre, è possibile ottenere le impostazioni dei ruoli dalle schede "Ruoli" o "Impostazioni dei ruoli" nel menu di spostamento a sinistra.

>[!Note]
>Se le opzioni nel menu di spostamento a sinistra sono inattive e viene visualizzato un banner nella parte superiore della pagina che indica "You have eligible roles that can be activated" (Sono disponibili ruoli idonei che possono essere attivati), ciò significa che non si è un amministratore attivo ed è necessario eseguire l'[attivazione](pim-resource-roles-activate-your-roles.md) prima di continuare.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Se si visualizza l'appartenenza di un ruolo, selezionare "Impostazioni dei ruoli" dalla barra nella parte superiore della schermata per aprire il dettaglio delle impostazioni dei ruoli.

Fare clic sul pulsante **Modifica** in alto per modificare le impostazioni dei ruoli.

Nella sezione sotto ad **Attiva** fare clic sulla casella di controllo **Require Multi-Factor Authentication to activate** (Richiedi autenticazione a più fattori per l'attivazione) e quindi fare clic su Salva.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Richiedere l'autenticazione MFA al momento dell'assegnazione

In alcuni casi, è possibile che un amministratore delle risorse voglia assegnare un membro a un ruolo per un breve periodo (un giorno, ad esempio) senza che i membri assegnati debbano richiedere l'attivazione. In questo scenario, PIM non può imporre l'autenticazione MFA quando il membro usa l'assegnazione di ruolo perché il membro è già attivo nel ruolo dal momento dell'assegnazione.

Per assicurarsi che l'amministratore delle risorse che gestisce l'assegnazione sia effettivamente chi dichiara di essere, è possibile imporre l'autenticazione MFA al momento dell'assegnazione.

Dalla stessa schermata dei dettagli delle impostazioni dei ruoli, selezionare la casella di controllo "Require Multi-Factor Authentication on assignment" (Richiedi autenticazione a più fattori al momento dell'assegnazione).

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Passaggi successivi

[Richiedere l'approvazione per l'attivazione](pim-resource-roles-approval-workflow.md)

[Usare il log di controllo](pim-resource-roles-use-the-audit-log.md)



