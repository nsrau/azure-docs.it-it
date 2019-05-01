---
title: Visualizzare i log e i report in Gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su come visualizzare il report di assegnazioni di utenti e dei log di controllo nella gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541541"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Visualizzazione di report e i log in Gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione dei diritti di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Visualizzare le risorse di a che un utente può accedere

1. Fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **report assegnazioni utente**.

1. Fare clic su **selezionare gli utenti** per aprire il riquadro di selezione utenti.

1. Trovare l'utente nell'elenco che si desidera visualizzare le risorse di che cui hanno accesso.

1. Fare clic sull'utente e quindi fare clic su **seleziona**.

    Viene visualizzato un elenco di risorse che l'utente può accedere. Include il pacchetto di accesso, criteri e le date.

    ![Report sulle assegnazioni degli utenti](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinare lo stato della richiesta di un utente

Per ottenere ulteriori dettagli sul modo in cui un utente ha richiesto e ricevuto l'accesso a un pacchetto di accesso, è possibile usare il log di controllo di Azure AD. In particolare, è possibile utilizzare i record del log nel `EntitlementManagement` e `UserManagement` categorie per ottenere dettagli aggiuntivi sui passaggi di elaborazione per ogni richiesta.  

1. Fare clic su **Azure Active Directory** e quindi fare clic su **log di controllo**.

1. Nella parte superiore, modificare il **categoria** a una delle due `EntitlementManagement` o `UserManagement`, a seconda del record di controllo si sta cercando.  

1. Fare clic su **Apply**.

1. Per scaricare i log, fare clic su **scaricare**.

Quando Azure AD riceve una nuova richiesta, viene scritto un record di controllo, in cui il **categoria** viene `EntitlementManagement` e il **attività** viene in genere `User requests access package assignment`.  Nel caso di un'assegnazione diretta creata nel portale di Azure, il **impegno** campo del record di controllo è `Administrator directly assigns user to access package`, e l'utente che esegue l'assegnazione è identificato dal **ActorUserPrincipalName**.

Azure AD verrà scritto il record di controllo aggiuntivi mentre la richiesta è in corso, tra cui:

| Categoria | Attività | Stato della richiesta |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Richiesta non richiede l'approvazione |
| `UserManagement` | `Create request approval` | Richiesta richiede l'approvazione |
| `UserManagement` | `Add approver to request approval` | Richiesta richiede l'approvazione |
| `EntitlementManagement` | `Approve access package assignment request` | Richiesta approvata |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Richiesta approvata o non richiede l'approvazione |

Quando un utente è assegnato l'accesso, Azure AD scrive un record di controllo per il `EntitlementManagement` categoria con **impegno** `Fulfill access package assignment`.  L'utente che ha ricevuto l'accesso è identificato da **ActorUserPrincipalName** campo.

Se non è stato assegnato l'accesso, quindi, Azure AD scrive un record di controllo per il `EntitlementManagement` categoria con **impegno** entrambi `Deny access package assignment request`, se la richiesta è stata rifiutata da un responsabile approvazione, o `Access package assignment request timed out (no approver action taken)`, se il timeout della richiesta prima di un responsabile approvazione può approvare.

Quando l'assegnazione di pacchetti di accesso dell'utente scade, viene annullata dall'utente o rimosso da un amministratore, quindi, Azure AD scrive un record di controllo per il `EntitlementManagement` categoria con **impegno** di `Remove access package assignment`.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di gestione dei diritti di Azure AD](entitlement-management-troubleshoot.md)
- [Scenari comuni](entitlement-management-scenarios.md)
