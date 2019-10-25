---
title: Verificare l'accesso ai ruoli di Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come verificare l'accesso dei ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b9f16d6c7f738e64f154506228c832e31e0f5b
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809058"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Controllare l'accesso ai ruoli di Azure AD in Privileged Identity Management

Privileged Identity Management (PIM) semplifica il modo in cui le aziende gestiscono l'accesso con privilegi alle risorse in Azure Active Directory (AD) e in altri Servizi online Microsoft, ad esempio Office 365 o Microsoft Intune. Eseguire la procedura descritta in questo articolo per verificare autonomamente i ruoli assegnati.

Se si è stati assegnati a un ruolo amministrativo, è possibile che l'amministratore dei ruoli con privilegi dell'organizzazione richieda di confermare a intervalli regolari che il ruolo sia ancora necessario. Si potrebbe ricevere un messaggio di posta elettronica che include un collegamento oppure è possibile passare direttamente alla [portale di Azure](https://portal.azure.com) e iniziare.

Per gli amministratori dei ruoli con privilegi o gli amministratori globali interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Aggiungere un riquadro del dashboard PIM

Se il servizio Azure AD Privileged Identity Management non è stato aggiunto al dashboard nel portale di Azure, seguire questa procedura per iniziare.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il nome utente nell'angolo superiore destro del portale di Azure e quindi selezionare la directory da usare.
3. Selezionare **Tutti i servizi** e usare la casella di testo Filtro per cercare **Azure AD Privileged Identity Management**.
4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso

L'approvazione o la negazione dell'accesso indica semplicemente al revisore se si sta usando ancora il ruolo. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambierà solo dopo il revisore avrà applicato i risultati.
Seguire questa procedura per trovare e completare la verifica dell'accesso:

1. Nel servizio Privileged Identity Management selezionare **Verifica accesso con privilegi**. Se sono presenti verifiche di accesso in sospeso, queste vengono visualizzate nella pagina delle verifiche di **accesso** Azure ad.
2. Selezionare la verifica da completare.
3. A meno che non abbia creato la verifica, l'utente verrà visualizzato come unico utente nella verifica. Selezionare il segno di spunta accanto al nome.
4. Scegliere **Approva** o **Nega**. Potrebbe essere necessario includere un motivo per la decisione nella casella di testo **Specifica il motivo** .  
5. Chiudere il pannello **Rivedi ruoli Azure Active Directory** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una verifica di accesso dei ruoli delle risorse di Azure in PIM](pim-resource-roles-perform-access-review.md)
