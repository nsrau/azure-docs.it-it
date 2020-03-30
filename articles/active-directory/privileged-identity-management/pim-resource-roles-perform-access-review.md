---
title: Esaminare l'accesso ai ruoli delle risorse di Azure in PIM - Azure AD Documenti Microsoft
description: Informazioni su come esaminare l'accesso ai ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847013"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Esaminare l'accesso ai ruoli delle risorse di Azure in Gestione identità con privilegiReview access to Azure resource roles in Privileged Identity Management

Le verifiche di accesso di Privileged Identity Management (PIM) consentono di proteggere l'accesso ai ruoli con privilegi in Azure Active Directory (Azure AD). Questo articolo illustra come completare un'analisi delle assegnazioni di ruolo con privilegi in una verifica dell'accesso ad Azure AD.

Se si è assegnati a un ruolo amministrativo, potrebbe essere necessario completare una verifica di accesso dall'amministratore per verificare la necessità di un ruolo. La richiesta di conferma può venire un messaggio di posta elettronica che include un collegamento oppure è possibile confermare nel portale di [Azure.](https://portal.azure.com)

Per gli amministratori dei ruoli con privilegi interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso

È possibile approvare o negare l'accesso in base al fatto che si utilizzi ancora questo ruolo o meno. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Il stato cambia solo dopo che il revisore applica i risultati.

Seguire questa procedura per trovare e completare la verifica dell'accesso:

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Selezionare **Azure Active Directory** e aprire Gestione **identità con privilegi**.
1. Selezionare **Controlla accesso**.

   ![Screenshot dell'applicazione Gestione identità con privilegi con il pannello Controlla accesso selezionato](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selezionare la verifica da completare.
1. Scegliere **Approva** o **Nega**. Nella **casella Fornire un motivo**immettere una motivazione aziendale per la decisione, se necessario.

   ![Screenshot della pagina dei dettagli della verifica](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una verifica di accesso dei ruoli di Azure AD in Gestione delle identità con privilegiPerform an access review of my Azure AD roles in Privileged Identity Management](pim-how-to-perform-security-review.md)
