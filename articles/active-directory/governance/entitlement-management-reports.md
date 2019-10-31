---
title: Visualizzare report e log in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come visualizzare i log di controllo e il report assegnazioni utenti in Azure Active Directory gestione dei diritti.
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
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3302fc3b2513794cd66d1ebf6db2cbcdb0f713dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173891"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Visualizzare report e log in Azure AD gestione dei diritti

I report di gestione dei diritti di Azure AD e Azure AD log di controllo forniscono dettagli aggiuntivi sulle risorse a cui gli utenti hanno accesso. In qualità di amministratore, è possibile visualizzare i pacchetti di accesso e le assegnazioni di risorse per un utente e visualizzare i log delle richieste a scopo di controllo o per determinare lo stato della richiesta di un utente. Questo articolo descrive come usare i report di gestione dei diritti e i log di controllo Azure AD.

Guardare il video seguente per informazioni su come visualizzare le risorse a cui gli utenti hanno accesso per la gestione dei diritti:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Visualizzare i pacchetti di accesso per un utente

Questo report consente di elencare tutti i pacchetti di accesso che un utente può richiedere e i pacchetti di accesso attualmente assegnati all'utente.

**Ruolo prerequisiti:** Amministratore globale o Amministratore utenti

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **report**.

1. Fare clic su **pacchetti di accesso per un utente**.

1. Fare clic su **Seleziona utenti** per aprire il riquadro Seleziona utenti.

1. Trovare l'utente nell'elenco e quindi fare clic su **Seleziona**.

    Nella scheda **richiesta può** essere visualizzato un elenco dei pacchetti di accesso che l'utente può richiedere. Questo elenco è determinato dai [criteri di richiesta](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definiti per i pacchetti di accesso. 

    ![Accedere ai pacchetti per un utente](./media/entitlement-management-reports/access-packages-report.png)

1. Se sono presenti più criteri o ruoli delle risorse per un pacchetto di accesso, fare clic sulla voce ruoli risorsa o criteri per visualizzare i dettagli della selezione.

1. Fare clic sulla scheda **assegnato** per visualizzare un elenco dei pacchetti di accesso attualmente assegnati all'utente. Quando un pacchetto di accesso viene assegnato a un utente, significa che l'utente ha accesso a tutti i ruoli delle risorse nel pacchetto di accesso.

## <a name="view-resource-assignments-for-a-user"></a>Visualizzare le assegnazioni di risorse per un utente

Questo report consente di elencare le risorse attualmente assegnate a un utente nella gestione dei diritti. Si noti che questo report è per le risorse gestite con la gestione dei diritti. L'utente può accedere ad altre risorse nella directory al di fuori della gestione dei diritti.

**Ruolo prerequisiti:** Amministratore globale o Amministratore utenti

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **report**.

1. Fare clic su **assegnazioni di risorse per un utente**.

1. Fare clic su **Seleziona utenti** per aprire il riquadro Seleziona utenti.

1. Trovare l'utente nell'elenco e quindi fare clic su **Seleziona**.

    Viene visualizzato un elenco delle risorse attualmente assegnate all'utente. L'elenco Mostra anche i criteri e il pacchetto di accesso da cui hanno ottenuto il ruolo della risorsa, insieme alla data di inizio e di fine per l'accesso.
    
    Se un utente ha accesso alla stessa risorsa in due o più pacchetti, è possibile fare clic su una freccia per visualizzare ogni pacchetto e criterio.

    ![Assegnazioni di risorse per un utente](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinare lo stato della richiesta di un utente

Per ottenere ulteriori dettagli su come un utente ha richiesto e ricevuto l'accesso a un pacchetto di accesso, è possibile usare il registro di controllo Azure AD. In particolare, è possibile usare i record di log nelle categorie `EntitlementManagement` e `UserManagement` per ottenere ulteriori dettagli sui passaggi di elaborazione per ogni richiesta.  

1. Fare clic su **Azure Active Directory** e quindi su **log di controllo**.

1. Nella parte superiore, modificare la **categoria** impostando `EntitlementManagement` o `UserManagement`, a seconda del record di controllo che si sta cercando.  

1. Fare clic su **Apply**.

1. Per scaricare i log, fare clic su **download**.

Quando Azure AD riceve una nuova richiesta, scrive un record di controllo, in cui la **categoria** viene `EntitlementManagement` e l' **attività** viene in genere `User requests access package assignment`ta.  Nel caso di un'assegnazione diretta creata nel portale di Azure, il campo **attività** del record di controllo viene `Administrator directly assigns user to access package`e l'utente che esegue l'assegnazione viene identificato da **ActorUserPrincipalName**.

Azure AD scriverà record di controllo aggiuntivi mentre è in corso la richiesta, tra cui:

| Categoria | Attività | Stato della richiesta |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | La richiesta non richiede l'approvazione |
| `UserManagement` | `Create request approval` | La richiesta richiede l'approvazione |
| `UserManagement` | `Add approver to request approval` | La richiesta richiede l'approvazione |
| `EntitlementManagement` | `Approve access package assignment request` | Richiesta approvata |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |La richiesta è stata approvata o non richiede l'approvazione |

Quando a un utente viene assegnato l'accesso, Azure AD scrive un record di controllo per la categoria `EntitlementManagement` con `Fulfill access package assignment`**attività** .  L'utente che ha ricevuto l'accesso viene identificato dal campo **ActorUserPrincipalName** .

Se l'accesso non è stato assegnato, Azure AD scrive un record di controllo per la categoria `EntitlementManagement` con **attività** `Deny access package assignment request`, se la richiesta è stata negata da un responsabile approvazione, o `Access package assignment request timed out (no approver action taken)`, se la richiesta è scaduta prima che un responsabile approvazione possa approvare.

Quando l'assegnazione del pacchetto di accesso dell'utente scade, viene annullata dall'utente o rimossa da un amministratore, quindi Azure AD scrive un record di controllo per la categoria `EntitlementManagement` con **attività** di `Remove access package assignment`.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di Azure AD gestione dei diritti](entitlement-management-troubleshoot.md)
- [Scenari comuni](entitlement-management-scenarios.md)
