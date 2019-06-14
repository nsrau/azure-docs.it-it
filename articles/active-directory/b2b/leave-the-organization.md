---
title: Uscire da un'organizzazione come utente guest - Azure Active Directory | Microsoft Docs
description: Illustra come un utente guest di Azure AD B2B possa uscire da un'organizzazione usando il pannello di accesso.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d9eb883cc014c1bea092a12e22b6d144a37994
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112978"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Uscire da un'organizzazione come utente guest

Un utente guest B2B di Azure Active Directory (Azure AD) può decidere di uscire da un'organizzazione in qualsiasi momento se non deve più usare le app di tale organizzazione o mantenere eventuali associazione ad essa. Un utente può uscire da un'organizzazione in modo autonomo, senza dover contattare un amministratore.

> [!NOTE]
> Se l'account è disabilitata nel tenant home o tenant delle risorse, un utente guest non può lasciare un'organizzazione. Se l'account è disabilitato, sarà necessario contattare l'amministratore del tenant, che è possibile eliminare l'account guest o abilitare l'account guest in modo che l'utente può lasciare l'organizzazione dell'utente guest.

## <a name="leave-an-organization"></a>Uscire da un'organizzazione

Per lasciare un'organizzazione, seguire questa procedura.

1. Passare alla pagina del profilo del Pannello di accesso eseguendo una delle operazioni seguenti:
   
   - Nel [portale di Azure](https://portal.azure.com), fare clic sul proprio nome nell'angolo superiore destro e selezionare **Visualizza account**.
   - Aprire il [Pannello di accesso](https://myapps.microsoft.com), fare clic sul proprio nome nell'angolo superiore destro e accanto alla **organizzazioni**, selezionare l'icona Impostazioni (ingranaggio).
 
   ![Screenshot che illustra le impostazioni utente nel riquadro di accesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se non è già stato effettuato l'accesso per l'organizzazione si desidera lasciare, sotto **organizzazioni**, fare clic sui **Accedi per uscire dall'organizzazione** collegamento accanto al nome dell'organizzazione. Dopo che è stato effettuato, selezionare il proprio nome nuovamente in alto a destra e accanto a **organizzazioni**, selezionare l'icona Impostazioni (ingranaggio).

3. In **Organizzazioni**, individuare l'organizzazione da cui uscire e selezionare **Esci da organizzazione**.

   ![Screenshot che mostra l'opzione Esci da organizzazione nell'interfaccia utente](media/leave-the-organization/LeaveOrg.png)

4. Nella richiesta di conferma selezionare **Esci**. 

## <a name="account-removal"></a>Rimozione dell'account

Quando un utente esce da un'organizzazione, l'account viene eliminato in modo temporaneo dalla directory. Per impostazione predefinita, l'oggetto utente sposta per la **utenti eliminati** area di Azure AD, ma non viene eliminato definitivamente per 30 giorni. Questa eliminazione temporanea consente all'amministratore di ripristinare l'account utente (inclusi i gruppi e autorizzazioni), nel caso in cui l'utente richieda il ripristino dell'account entro i 30 giorni.

Se necessario, un amministratore tenant può eliminare definitivamente l'account in qualsiasi momento durante il periodo di 30 giorni. A tale scopo, effettuare l'operazione seguente:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.
2. In **Gestisci** selezionare **Utenti**.
3. Selezionare **Utenti eliminati**.
4. Selezionare la casella di controllo accanto a un utente eliminato e quindi **Elimina definitivamente**.

Se si elimina in modo definitivo un utente, l'azione è irreversibile.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di Azure AD B2B, vedere [Informazioni su Collaborazione B2B di Azure AD](what-is-b2b.md)



