---
title: Abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack | Documenti Microsoft
description: Abilitare il provider del servizio accedere a una sottoscrizione nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Abilitare un Provider di servizi Cloud gestire la sottoscrizione di Azure Stack

*Si applica a: Azure Stack integrate di sistemi*

Se si usa Azure Stack con un Provider del servizio Cloud (CSP), l'accesso alle risorse nella sottoscrizione di Azure e nello Stack di Azure può essere gestito dal provider. Oppure è possibile gestire la propria sottoscrizione. In questo articolo viene descritto come abilitare il provider di servizi di accesso alla sottoscrizione per conto dell'utente o per verificare che il provider del servizio può gestire il servizio.

> [!Note]  
>  Se i passaggi seguenti vengono ignorati e il CSP non è già gestione dell'account, il provider CSP non sarà in grado di gestire la sottoscrizione di Azure Stack per conto dell'utente.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Gestire la sottoscrizione con un Provider di servizi Cloud

1. Aggiungere il CSP come utente guest con il ruolo utente nella directory di tenant.  Per istruzioni sull'aggiunta di un utente, vedere [aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Il provider CSP verrà quindi creato la sottoscrizione di Azure Stack locale per l'utente.
3. Si è pronti per iniziare a usare Azure Stack.
3. Il CSP deve quindi creare una risorsa nella sottoscrizione per verificare che possono anche gestire le risorse. Ad esempio, è possibile [creare una macchina virtuale Windows con il portale di Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Abilitare il Provider di servizi Cloud gestire la sottoscrizione utilizzando i diritti sui ruoli

Aggiungere il provider CSP come proprietario per la sottoscrizione. 

1. Aggiungere il CSP come utente guest. con il ruolo di proprietario per la directory del tenant.  Per istruzioni sull'aggiunta di un utente, vedere [aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Aggiungere il ruolo di proprietario per l'utente guest CSP. Per i passaggi per aggiungere l'utente CSP alla sottoscrizione, vedere [Use Role-Based di controllo di accesso per gestire l'accesso alle risorse di sottoscrizione di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Il provider CSP verrà quindi creato la sottoscrizione di Azure Stack locale per l'utente.
4. Si è pronti per iniziare a usare Azure Stack.
5. Il CSP deve quindi creare una risorsa nella sottoscrizione per verificare che gestiscono le risorse. 

## <a name="next-steps"></a>Passaggi successivi

  - Per ulteriori informazioni su come recuperare informazioni sull'utilizzo di risorse dallo Stack di Azure, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](../azure-stack-billing-and-chargeback.md).
