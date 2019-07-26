---
title: Visualizzare report e log in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come visualizzare i log di controllo e il report assegnazioni utenti in Azure Active Directory gestione dei diritti (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b4751b421f5af1536af9a88d15e060ab59bdb
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489069"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Visualizzare report e log in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Visualizzare le risorse a cui un utente può accedere

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **report assegnazioni utenti**.

1. Fare clic su **Seleziona utenti** per aprire il riquadro Seleziona utenti.

1. Trovare l'utente nell'elenco a cui si desidera visualizzare le risorse a cui ha accesso.

1. Fare clic sull'utente e quindi su **Seleziona**.

    Viene visualizzato un elenco di risorse a cui l'utente ha accesso. Include il pacchetto di accesso, i criteri e le date.

    ![Report sulle assegnazioni degli utenti](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinare lo stato della richiesta di un utente

Per ottenere ulteriori dettagli su come un utente ha richiesto e ricevuto l'accesso a un pacchetto di accesso, è possibile usare il registro di controllo Azure AD. In particolare, è possibile utilizzare i record del log nelle `EntitlementManagement` categorie `UserManagement` e per ottenere ulteriori dettagli sui passaggi di elaborazione per ogni richiesta.  

1. Fare clic su **Azure Active Directory** e quindi su **log di controllo**.

1. Nella parte superiore, modificare la **categoria** in `EntitlementManagement` o `UserManagement`, a seconda del record di controllo che si sta cercando.  

1. Fare clic su **Apply**.

1. Per scaricare i log, fare clic su **download**.

Quando Azure ad riceve una nuova richiesta, scrive un record di controllo, in cui la **categoria** è `EntitlementManagement` e l' **attività** è in `User requests access package assignment`genere.  Nel caso di un'assegnazione diretta creata nel portale di Azure, il campo **attività** del record di controllo è `Administrator directly assigns user to access package`e l'utente che esegue l'assegnazione viene identificato da **ActorUserPrincipalName**.

Azure AD scriverà record di controllo aggiuntivi mentre è in corso la richiesta, tra cui:

| Category | Attività | Stato richiesta |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | La richiesta non richiede l'approvazione |
| `UserManagement` | `Create request approval` | La richiesta richiede l'approvazione |
| `UserManagement` | `Add approver to request approval` | La richiesta richiede l'approvazione |
| `EntitlementManagement` | `Approve access package assignment request` | Richiesta approvata |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |La richiesta è stata approvata o non richiede l'approvazione |

Quando a un utente viene assegnato l'accesso, Azure ad scrive un record di `EntitlementManagement` controllo per la categoria con **Activity** `Fulfill access package assignment`.  L'utente che ha ricevuto l'accesso viene identificato dal campo **ActorUserPrincipalName** .

Se l'accesso non è stato assegnato, Azure ad scrive un record di controllo `EntitlementManagement` per la categoria con `Deny access package assignment request` **attività** , se la richiesta è stata negata da un `Access package assignment request timed out (no approver action taken)`responsabile approvazione oppure, se la richiesta è scaduta prima che un responsabile approvazione possa approvare.

Quando l'assegnazione del pacchetto di accesso dell'utente scade, viene annullata dall'utente o rimossa da un amministratore, quindi Azure ad scrive un record di `EntitlementManagement` controllo per la categoria `Remove access package assignment`con **attività** di.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di Azure AD gestione dei diritti](entitlement-management-troubleshoot.md)
- [Scenari comuni](entitlement-management-scenarios.md)
