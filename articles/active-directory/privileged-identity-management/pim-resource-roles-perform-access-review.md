---
title: Come eseguire una verifica di accesso in Privileged Identity Management per le risorse di Azure | Microsoft Docs
description: Questo documento descrive come eseguire una verifica di accesso in PIM per risorse di Azure.
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
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Privileged Identity Management - Ruolo per le risorse - Eseguire una verifica di accesso
Privileged Identity Management per le risorse di Azure semplifica per le aziende la gestione dell'accesso con privilegi alle risorse in Azure. 

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore dei ruoli con privilegi dell'organizzazione richieda di confermare a intervalli regolari che il ruolo sia ancora necessario. È possibile che si riceva un messaggio di posta elettronica contenente un collegamento oppure accedere direttamente al [portale di Azure](https://portal.azure.com). Per eseguire una verifica automatica dei ruoli assegnati, seguire la procedura descritta in questo articolo.

Per gli amministratori dei ruoli con privilegi interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Aggiungere l'applicazione Privileged Identity Management
Per eseguire la verifica, è possibile usare l'applicazione Azure AD Privileged Identity Management (PIM) nel [portale di Azure](https://portal.azure.com/) .  Se l'applicazione Azure AD Privileged Identity Management non è disponibile nel portale, seguire questa procedura per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare.
3. Selezionare **Tutti i servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso
L'approvazione o la negazione dell'accesso indica semplicemente al revisore se si sta usando ancora il ruolo. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambierà solo dopo il revisore avrà applicato i risultati.
Seguire questa procedura per trovare e completare la verifica dell'accesso:
1. Passare all'applicazione Azure AD Privileged Identity Management.
2. Selezionare il pannello Verificare l'accesso

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Selezionare la verifica da completare. 
4. Scegliere **Approva** o **Nega**. Potrebbe essere necessario includere un motivo per la decisione nella casella di testo **Specifica il motivo** .

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
