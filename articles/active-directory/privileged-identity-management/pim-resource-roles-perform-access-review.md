---
title: Verificare l'accesso ai ruoli delle risorse di Azure in PIM-Azure AD | Microsoft Docs
description: Informazioni su come verificare l'accesso dei ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847013"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Verificare l'accesso ai ruoli delle risorse di Azure in Privileged Identity Management

Le verifiche di accesso di Privileged Identity Management (PIM) consentono di proteggere l'accesso ai ruoli con privilegi in Azure Active Directory (Azure AD). Questo articolo illustra la procedura per completare una verifica delle assegnazioni di ruolo con privilegi in una verifica di accesso Azure AD.

Se si è assegnati a un ruolo amministrativo, potrebbe essere necessario completare una verifica di accesso da parte dell'amministratore per confermare la necessità di un ruolo. La richiesta di conferma può essere un messaggio di posta elettronica che include un collegamento oppure è possibile confermare il [portale di Azure](https://portal.azure.com).

Per gli amministratori dei ruoli con privilegi interessati alle verifiche dell'accesso sono disponibili altre informazioni in [Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Approvare o negare l'accesso

È possibile approvare o negare l'accesso a seconda che si usi ancora questo ruolo o meno. Scegliere **Approva** se si vuole restare nel ruolo o **Nega** se l'accesso non è più necessario. Lo stato cambia solo dopo che il revisore ha applicato i risultati.

Seguire questa procedura per trovare e completare la verifica dell'accesso:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Azure Active Directory** e aprire **Privileged Identity Management**.
1. Selezionare **Verifica accesso**.

   ![Screenshot dell'applicazione Privileged Identity Management, con il pannello Verifica accesso selezionato](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selezionare la verifica da completare.
1. Scegliere **approva** o **Nega**. Nella **casella fornire un motivo**immettere una motivazione aziendale per la decisione, se necessario.

   ![Screenshot della pagina dei dettagli della verifica](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una verifica di accesso dei ruoli di Azure AD in Privileged Identity Management](pim-how-to-perform-security-review.md)
