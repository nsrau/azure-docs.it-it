---
title: Eseguire una verifica di accesso dei ruoli delle risorse di Azure in PIM | Microsoft Docs
description: Informazioni su come eseguire una verifica di accesso dei ruoli delle risorse di Azure AD in Azure AD Privileged Identity Management (PIM).
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
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3b9c998351432a591ece0a61d60256aea89a6f8f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180892"
---
# <a name="perform-an-access-review-of-my-azure-resource-roles-in-pim"></a>Eseguire una verifica di accesso dei ruoli delle risorse di Azure in PIM
Privileged Identity Management (PIM) per le risorse di Azure semplifica per le aziende la gestione dell'accesso con privilegi alle risorse in Azure. 

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore dei ruoli con privilegi dell'organizzazione richieda di confermare a intervalli regolari che il ruolo sia ancora necessario. È possibile che si riceva un messaggio di posta elettronica contenente un collegamento oppure accedere direttamente al [portale di Azure](https://portal.azure.com). Per eseguire una verifica automatica dei ruoli assegnati, seguire la procedura descritta in questo articolo.

Per gli amministratori dei ruoli con privilegi interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Aggiungere l'applicazione Privileged Identity Management
Per eseguire la verifica, è possibile usare l'applicazione PIM di Azure Active Directory (Azure AD) nel [portale di Azure](https://portal.azure.com/). Se non si ha l'applicazione nel portale, attenersi alla procedura seguente per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare durante le operazioni.
3. Selezionare **All services** (Tutti i servizi) e usare la casella **Filtro** per cercare *Azure AD Privileged Identity Management*.
4. Selezionare **Pin to dashboard** (Aggiungi al dashboard) quindi selezionare **Crea**. Si apre l'applicazione PIM.

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso
L'approvazione o la negazione dell'accesso indica semplicemente al revisore se si sta usando ancora il ruolo. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambia solo quando il revisore applica i risultati.

Seguire questa procedura per trovare e completare la verifica dell'accesso:
1. Passare all'applicazione PIM di Azure AD.
2. Selezionare il pannello **Verifica di accesso**.

   ![Screenshot dell'applicazione PIM, con selezionato il pannello Verifica di accesso](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Selezionare la verifica da completare. 
4. Scegliere **Approva** o **Nega**. Potrebbe essere necessario includere un motivo per la decisione nella **casella Provide a reason** (Specifica il motivo).

   ![Screenshot della pagina dei dettagli della verifica](media/azure-pim-resource-rbac/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una verifica di accesso dei ruoli della directory di Azure AD in PIM](pim-how-to-perform-security-review.md)
