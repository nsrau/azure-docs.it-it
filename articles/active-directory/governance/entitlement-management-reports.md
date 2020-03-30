---
title: Visualizzare i report & i log nella gestione dei diritti - Azure ADView reports and logs in entitlement management - Azure AD
description: Informazioni su come visualizzare il report delle assegnazioni utente e i log di controllo nella gestione dei diritti di Azure Active Directory.Learn how to view the user assignments report and audit logs in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128428"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Visualizzare report e log nella gestione dei diritti di Azure ADView reports and logs in Azure AD entitlement management

I report di gestione dei diritti di Azure AD e il log di controllo di Azure AD forniscono dettagli aggiuntivi sulle risorse a cui gli utenti hanno accesso. In qualità di amministratore, è possibile visualizzare i pacchetti di accesso e le assegnazioni delle risorse per un utente e visualizzare i registri delle richieste a scopo di controllo o per determinare lo stato della richiesta di un utente. Questo articolo descrive come usare i report di gestione dei diritti e i log di controllo di Azure AD.

Guardare il video seguente per informazioni su come visualizzare le risorse a cui gli utenti hanno accesso nella gestione dei diritti:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Visualizzare i pacchetti di accesso per un utenteView access packages for a user

Questo report consente di elencare tutti i pacchetti di accesso che un utente può richiedere e i pacchetti di accesso attualmente assegnati all'utente.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Rapporti.**

1. Fare clic su **Pacchetti di access per un utente**.

1. Fare clic su **Seleziona utenti** per aprire il riquadro Seleziona utenti.

1. Individuare l'utente nell'elenco e quindi fare clic su **Seleziona**.

    La scheda **Può richiedere** visualizza un elenco dei pacchetti di accesso che l'utente può richiedere. Questo elenco è determinato dai criteri di [richiesta](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definiti per i pacchetti di accesso. 

    ![Accedere ai pacchetti per un utenteAccess packages for a user](./media/entitlement-management-reports/access-packages-report.png)

1. Se sono presenti più ruoli o criteri di risorsa per un pacchetto di accesso, fare clic sulla voce relativa ai ruoli o ai criteri della risorsa per visualizzare i dettagli di selezione.

1. Fare clic sulla scheda **Assegnato** per visualizzare un elenco dei pacchetti di accesso attualmente assegnati all'utente. Quando un pacchetto di accesso viene assegnato a un utente, significa che l'utente ha accesso a tutti i ruoli delle risorse nel pacchetto di accesso.

## <a name="view-resource-assignments-for-a-user"></a>Visualizzare le assegnazioni delle risorse per un utente

Questo report consente di elencare le risorse attualmente assegnate a un utente nella gestione dei diritti. Si noti che questo report è destinato alle risorse gestite con la gestione dei diritti. L'utente potrebbe avere accesso ad altre risorse nella directory al di fuori della gestione dei diritti.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, fai clic su **Rapporti.**

1. Fare clic su **Assegnazioni risorse per un utente.**

1. Fare clic su **Seleziona utenti** per aprire il riquadro Seleziona utenti.

1. Individuare l'utente nell'elenco e quindi fare clic su **Seleziona**.

    Viene visualizzato un elenco delle risorse attualmente assegnate all'utente. L'elenco mostra anche il pacchetto di accesso e i criteri da cui hanno ottenuto il ruolo della risorsa, insieme alla data di inizio e di fine per l'accesso.
    
    Se un utente ha accesso alla stessa risorsa in due o più pacchetti, è possibile fare clic su una freccia per visualizzare ogni pacchetto e criterio.

    ![Assegnazioni di risorse per un utente](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinare lo stato della richiesta di un utente

Per ottenere dettagli aggiuntivi su come un utente ha richiesto e ricevuto l'accesso a un pacchetto di accesso, è possibile usare il log di controllo di Azure AD. In particolare, è possibile utilizzare `EntitlementManagement` i `UserManagement` record di log nelle categorie e per ottenere ulteriori dettagli sui passaggi di elaborazione per ogni richiesta.  

1. Fare clic su **Azure Active Directory** e quindi su Log di **controllo**.

1. In alto, cambia **Category** la `EntitlementManagement` Categoria `UserManagement`in o , a seconda del record di controllo che stai cercando.  

1. Fare clic su **Applica**.

1. Per scaricare i registri, fare clic su **Scarica**.

Quando Azure AD riceve una nuova richiesta, scrive un record `EntitlementManagement` di controllo, `User requests access package assignment`in cui la **categoria** è e **l'attività** è in genere .  Nel caso di un'assegnazione diretta creata nel portale di `Administrator directly assigns user to access package`Azure, il campo **Impegno** del record di controllo è e l'utente che esegue l'assegnazione viene identificato da **ActorUserPrincipalName**.

Azure AD scriverà record di controllo aggiuntivi mentre la richiesta è in corso, tra cui:Azure AD will write additional audit records while the request is in progress, including:

| Category | Attività | Stato della richiesta |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | La richiesta non richiede l'approvazione |
| `UserManagement` | `Create request approval` | La richiesta richiede l'approvazione |
| `UserManagement` | `Add approver to request approval` | La richiesta richiede l'approvazione |
| `EntitlementManagement` | `Approve access package assignment request` | Richiesta approvata |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Richiesta approvata o non richiede l'approvazione |

Quando a un utente viene assegnato l'accesso, Azure AD scrive un record di controllo per la `EntitlementManagement` categoria con **Attività** `Fulfill access package assignment`.  L'utente che ha ricevuto l'accesso è identificato dal campo **ActorUserPrincipalName.**

Se l'accesso non è stato assegnato, `EntitlementManagement` Azure AD `Deny access package assignment request`scrive un record di controllo per `Access package assignment request timed out (no approver action taken)`la categoria con **Attività** , se la richiesta è stata negata da un approvatore oppure , se la richiesta è scadina prima che un approvatore possa approvare.

Quando l'assegnazione del pacchetto di accesso dell'utente scade, viene annullata dall'utente o `EntitlementManagement` rimossa da `Remove access package assignment`un amministratore, Azure AD scrive un record di controllo per la categoria con **Attività** di .

## <a name="next-steps"></a>Passaggi successivi

- [Archiviare report e registri](entitlement-management-logs-and-reporting.md)
- [Risolvere i problemi relativi alla gestione dei diritti di Azure ADTroubleshoot Azure AD entitlement management](entitlement-management-troubleshoot.md)
- [Scenari comuni](entitlement-management-scenarios.md)
