---
title: Abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack | Microsoft Docs
description: Abilitare il provider di servizi accedere a una sottoscrizione di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 5d7398853e20702aef450e2532784f0dca7aac57
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246603"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

Se si usa Azure Stack con un Provider di servizi Cloud (CSP), si potrebbe scegliere di gestire la propria sottoscrizione per accedere alle risorse in Azure e in Azure Stack. È anche possibile consentire il provider di gestire la sottoscrizione per l'utente. Questo articolo illustra come:

* Assegnare l'accesso al provider di servizio di sottoscrizione.
* Assicurarsi che il provider del servizio può gestire il servizio.

> [!NOTE]
> Se il provider CSP non gestisce l'account e ignorare i passaggi seguenti, CSP non è possibile gestire la sottoscrizione di Azure Stack per l'utente.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gestire la sottoscrizione con un Provider di servizi Cloud

Aggiungere il provider come CSP **utente** alla sottoscrizione.

1. Aggiungere il CSP come utente guest con il ruolo utente nella directory del tenant. Per i passaggi aggiungere un utente, vedere [aggiungere nuovi utenti ad Azure Active Directory](../../active-directory/add-users-azure-active-directory.md)
2. Il CSP crea la sottoscrizione di Azure Stack locale per l'utente. Si è pronti per iniziare a usare Azure Stack.
3. I CSP devono creare una risorsa nella sottoscrizione per verificare che possono anche gestire le risorse. Ad esempio, è possibile [creare una macchina virtuale Windows con il portale di Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Abilitare il Provider di servizi Cloud gestire la sottoscrizione utilizzando diritti RBAC

Aggiungere il provider come CSP **proprietario** alla sottoscrizione.

1. Aggiungere il CSP come utente guest alla directory tenant. Per i passaggi aggiungere un utente, vedere [aggiungere nuovi utenti ad Azure Active Directory](../../active-directory/add-users-azure-active-directory.md)
2. Aggiungere il **proprietario** ruolo all'utente guest CSP. Per i passaggi aggiungere l'utente CSP nella sottoscrizione, vedere [controllo di accesso degli accessi in base a gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md). Il CSP crea la sottoscrizione di Azure Stack locale per l'utente. Si è pronti per iniziare a usare Azure Stack.
3. I CSP devono creare una risorsa nella sottoscrizione per verificare che essi possono gestire le risorse.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come recuperare le informazioni sull'utilizzo di risorse di Azure Stack, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](../azure-stack-billing-and-chargeback.md).
