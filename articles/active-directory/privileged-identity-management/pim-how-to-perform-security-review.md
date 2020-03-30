---
title: Esaminare l'accesso ai ruoli di Azure AD in PIM - Azure AD Documenti Microsoft
description: Informazioni su come esaminare l'accesso dei ruoli di Azure Active Directory in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847099"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Esaminare l'accesso ai ruoli di Azure AD in Gestione delle identità con privilegiReview access to Azure AD roles in Privileged Identity Management

Privileged Identity Management (PIM) semplifica il modo in cui le aziende gestiscono l'accesso con privilegi alle risorse in Azure Active Directory (AD) e altri servizi online Microsoft come Office 365 o Microsoft Intune. Seguire i passaggi descritti in questo articolo per rivedere autonomamente i ruoli assegnati.

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore dei ruoli con privilegi dell'organizzazione richieda di confermare a intervalli regolari che il ruolo sia ancora necessario. È possibile ricevere un messaggio di posta elettronica che include un collegamento oppure passare direttamente al portale di [Azure](https://portal.azure.com) e iniziare.

Per gli amministratori dei ruoli con privilegi o gli amministratori globali interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Aggiungere un riquadro del dashboard PIM

Se il servizio Gestione identità con privilegi di Azure AD non è stato aggiunto al dashboard nel portale di Azure, seguire questi passaggi per iniziare.

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare.
3. Selezionare **Tutti i servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso

L'approvazione o la negazione dell'accesso indica semplicemente al revisore se si sta usando ancora il ruolo. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambierà solo dopo il revisore avrà applicato i risultati.
Seguire questa procedura per trovare e completare la verifica dell'accesso:

1. Nel servizio Gestione identità con privilegi selezionare **Controlla accesso con privilegi**. Se sono presenti verifiche di accesso in sospeso, vengono visualizzate nella pagina **Revisioni di Access** di Azure AD.
2. Selezionare la verifica da completare.
3. A meno che non abbia creato la verifica, l'utente verrà visualizzato come unico utente nella verifica. Selezionare il segno di spunta accanto al nome.
4. Scegliere **Approva** o **Nega**. Potrebbe essere necessario includere un motivo per la decisione nella casella di testo **Specifica il motivo** .  
5. Chiudere il pannello **Rivedi ruoli Azure Active Directory** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una verifica di accesso dei ruoli delle risorse di Azure in PIM](pim-resource-roles-perform-access-review.md)
