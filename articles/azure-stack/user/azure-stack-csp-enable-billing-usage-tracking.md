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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fca08ef1d803c3bd47b0ae925c4dd12255175f2c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630716"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack

*Si applica a: i sistemi integrati di Azure Stack*

Se si usa Azure Stack con un Provider di servizi Cloud (CSP), si potrebbe scegliere di gestire la propria sottoscrizione per accedere alle risorse in Azure e in Azure Stack. È anche possibile consentire il provider di gestire la sottoscrizione per l'utente. Questo articolo illustra come:

 * Assegnare l'accesso al provider di servizio di sottoscrizione.
 * Assicurarsi che il provider del servizio può gestire il servizio.

> [!Note]
>  Se il provider CSP non è la gestione dell'account e ignorare i passaggi seguenti, CSP non è possibile gestire la sottoscrizione di Azure Stack per l'utente.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gestire la sottoscrizione con un Provider di servizi Cloud

Aggiungere il provider come CSP **utente** alla sottoscrizione.

1. Aggiungere il CSP come utente guest con il ruolo utente nella directory del tenant.  Per istruzioni sull'aggiunta di un utente, vedere [aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Il CSP crea la sottoscrizione di Azure Stack locale per l'utente.
3. Si è pronti per iniziare a usare Azure Stack.
4. I CSP devono creare una risorsa nella sottoscrizione per verificare che possono anche gestire le risorse. Ad esempio, è possibile [creare una macchina virtuale Windows con il portale di Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Abilitare il Provider di servizi Cloud gestire la sottoscrizione utilizzando diritti RBAC

Aggiungere il provider come CSP **proprietario** alla sottoscrizione.

1. Aggiungere il CSP come utente guest alla directory tenant.  Per istruzioni sull'aggiunta di un utente, vedere [aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Aggiungere il ruolo di proprietario per l'utente guest CSP. Per i passaggi per aggiungere l'utente CSP nella sottoscrizione, vedere [controllo di accesso degli accessi in base a gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Il CSP crea la sottoscrizione di Azure Stack locale per l'utente.
4. Si è pronti per iniziare a usare Azure Stack.
5. I CSP devono creare una risorsa nella sottoscrizione per verificare che essi possono gestire le risorse.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come recuperare le informazioni sull'utilizzo di risorse di Azure Stack, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](../azure-stack-billing-and-chargeback.md).
