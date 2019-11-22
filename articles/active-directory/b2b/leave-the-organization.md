---
title: Lasciare un'organizzazione come utente Guest-Azure Active Directory
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
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272489"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Uscire da un'organizzazione come utente guest

Un utente guest B2B di Azure Active Directory (Azure AD) può decidere di uscire da un'organizzazione in qualsiasi momento se non deve più usare le app di tale organizzazione o mantenere eventuali associazione ad essa. Un utente può uscire da un'organizzazione in modo autonomo, senza dover contattare un amministratore.

> [!NOTE]
> Un utente Guest non può lasciare un'organizzazione se il proprio account è disabilitato nel tenant Home o nel tenant di risorse. Se il proprio account è disabilitato, l'utente Guest dovrà contattare l'amministratore del tenant, che può eliminare l'account Guest o abilitare l'account Guest in modo che l'utente possa uscire dall'organizzazione.

## <a name="leave-an-organization"></a>Uscire da un'organizzazione

Per uscire da un'organizzazione, attenersi alla seguente procedura.

1. Passare alla pagina del profilo del pannello di accesso eseguendo una delle operazioni seguenti:
   
   - Nella [portale di Azure](https://portal.azure.com)fare clic sul proprio nome nell'angolo in alto a destra e selezionare **Visualizza account**.
   - Aprire il [Pannello di accesso](https://myapps.microsoft.com), fare clic sul nome in alto a destra e accanto a **organizzazioni**Selezionare l'icona delle impostazioni (a forma di ingranaggio).
 
   ![Screenshot che illustra le impostazioni utente nel riquadro di accesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se non è già stato effettuato l'accesso all'organizzazione che si vuole lasciare, in **organizzazioni**fare clic sul collegamento **Accedi per uscire dall'organizzazione** accanto al nome dell'organizzazione. Dopo aver eseguito l'accesso, fare di nuovo clic sul nome in alto a destra e accanto a **organizzazioni**selezionare l'icona delle impostazioni (a forma di ingranaggio).

3. In **Organizzazioni**, individuare l'organizzazione da cui uscire e selezionare **Esci da organizzazione**.

   ![Screenshot che mostra l'opzione Esci da organizzazione nell'interfaccia utente](media/leave-the-organization/LeaveOrg.png)

4. Nella richiesta di conferma selezionare **Esci**. 

## <a name="account-removal"></a>Rimozione dell'account

Quando un utente esce da un'organizzazione, l'account viene eliminato in modo temporaneo dalla directory. Per impostazione predefinita, l'oggetto utente si sposta sull'area **utenti eliminati** in Azure ad ma non viene eliminato definitivamente per 30 giorni. Questa eliminazione temporanea consente all'amministratore di ripristinare l'account utente (inclusi i gruppi e autorizzazioni), nel caso in cui l'utente richieda il ripristino dell'account entro i 30 giorni.

Se necessario, un amministratore tenant può eliminare definitivamente l'account in qualsiasi momento durante il periodo di 30 giorni. A tale scopo, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.
2. In **Gestisci** selezionare **Utenti**.
3. Selezionare **Utenti eliminati**.
4. Selezionare la casella di controllo accanto a un utente eliminato e quindi **Elimina definitivamente**.

Se si elimina in modo definitivo un utente, l'azione è irreversibile.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di Azure AD B2B, vedere [Informazioni su Collaborazione B2B di Azure AD](what-is-b2b.md)



