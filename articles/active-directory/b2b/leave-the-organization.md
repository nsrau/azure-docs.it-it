---
title: Uscire da un'organizzazione come utente guest - Azure Active Directory | Microsoft Docs
description: Illustra come un utente guest di Azure AD B2B possa uscire da un'organizzazione usando il pannello di accesso.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 423de49faa20289a7c5663ae621f890b63a5752c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428512"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Uscire da un'organizzazione come utente guest

Un utente guest B2B di Azure Active Directory (Azure AD) può decidere di uscire da un'organizzazione in qualsiasi momento se non deve più usare le app di tale organizzazione o mantenere eventuali associazione ad essa. Un utente può uscire da un'organizzazione in modo autonomo, senza dover contattare un amministratore.

## <a name="leave-an-organization"></a>Uscire da un'organizzazione

Per uscire da un'organizzazione, l'utente che ha fatto accesso tramite il [Pannello di accesso](https://myapps.microsoft.com), esegue le operazioni seguenti:

1. Se non è già stato eseguito l'accesso all'organizzazione da cui si intende uscire, selezionare il nome utente nell'angolo superiore destro e fare clic sull'organizzazione da cui uscire.
2. Nell'angolo superiore destro selezionare il nome utente.
3. Accanto a **Organizzazioni**, selezionare l'icona Impostazioni a forma di ingranaggio.
 
   ![Screenshot che illustra le impostazioni utente nel riquadro di accesso](media/leave-the-organization/UserSettings.png) 

3. In **Organizzazioni**, individuare l'organizzazione da cui uscire e selezionare **Esci da organizzazione**.

   ![Screenshot che mostra l'opzione Esci da organizzazione nell'interfaccia utente](media/leave-the-organization/LeaveOrg.png)

4. Nella richiesta di conferma selezionare **Esci**. 

## <a name="account-removal"></a>Rimozione dell'account

Quando un utente esce da un'organizzazione, l'account viene eliminato in modo temporaneo dalla directory. Per impostazione predefinita, l'oggetto utente viene spostato nella sezione **Utenti eliminati** di Azure AD, ma non viene eliminato definitivamente per 30 giorni. Questa eliminazione temporanea consente all'amministratore di ripristinare l'account utente (inclusi i gruppi e autorizzazioni), nel caso in cui l'utente richieda il ripristino dell'account entro i 30 giorni.

Se necessario, un amministratore tenant può eliminare definitivamente l'account in qualsiasi momento durante il periodo di 30 giorni. A tale scopo, effettuare l'operazione seguente:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.
2. In **Gestisci** selezionare **Utenti**.
3. Selezionare **Utenti eliminati**.
4. Selezionare la casella di controllo accanto a un utente eliminato e quindi **Elimina definitivamente**.

Se si elimina in modo definitivo un utente, l'azione è irreversibile.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di Azure AD B2B, vedere [Informazioni su Collaborazione B2B di Azure AD](what-is-b2b.md)



