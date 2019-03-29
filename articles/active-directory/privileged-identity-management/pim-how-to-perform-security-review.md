---
title: Eseguire una verifica di accesso dei ruoli personali AD Azure in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come eseguire una verifica di accesso dei ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66f16e02716ceb94d2c8b10bb246a13dc566229c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578314"
---
# <a name="perform-an-access-review-of-my-azure-ad-roles-in-pim"></a>Eseguire una verifica di accesso dei ruoli personali AD Azure in PIM
Azure Active Directory (AD) Privileged Identity Management (PIM) semplifica come le aziende gestiscono l'accesso con privilegi alle risorse di Azure AD e altri Microsoft online services come Office 365 o Microsoft Intune.  

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore dei ruoli con privilegi dell'organizzazione richieda di confermare a intervalli regolari che il ruolo sia ancora necessario. È possibile che si riceva un messaggio di posta elettronica contenente un collegamento oppure accedere direttamente al [portale di Azure](https://portal.azure.com). Per eseguire una verifica automatica dei ruoli assegnati, seguire la procedura descritta in questo articolo.

Per gli amministratori dei ruoli con privilegi o gli amministratori globali interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Aggiungere l'applicazione Privileged Identity Management
Per eseguire la verifica, è possibile usare l'applicazione Azure AD Privileged Identity Management (PIM) nel [portale di Azure](https://portal.azure.com/) .  Se l'applicazione Azure AD Privileged Identity Management non è disponibile nel portale, seguire questa procedura per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare.
3. Selezionare **Tutti i servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso
L'approvazione o la negazione dell'accesso indica semplicemente al revisore se si sta usando ancora il ruolo. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambierà solo dopo il revisore avrà applicato i risultati.
Seguire questa procedura per trovare e completare la verifica dell'accesso:

1. Nell'applicazione PIM selezionare **Verifica dell'accesso con privilegi**. Eventuali verifiche di accesso in sospeso verranno visualizzate nel pannello Verifiche di accesso di Azure AD.
2. Selezionare la verifica da completare.
3. A meno che non abbia creato la verifica, l'utente verrà visualizzato come unico utente nella verifica. Selezionare il segno di spunta accanto al nome.
4. Scegliere **Approva** o **Nega**. Potrebbe essere necessario includere un motivo per la decisione nella casella di testo **Specifica il motivo** .  
5. Chiudere il pannello **Rivedi ruoli Azure Active Directory** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una verifica di accesso dei ruoli delle risorse di Azure in PIM](pim-resource-roles-perform-access-review.md)
