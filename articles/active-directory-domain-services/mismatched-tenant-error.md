---
title: Correzione di errori di directory non corrispondenti in Azure AD Domain Services | Microsoft Docs
description: Informazioni sul significato di un errore di directory non corrispondente e su come risolverlo in Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 0d96fd4c435251fb4dd604351e6efeea8bde8353
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704537"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Risolvere gli errori di directory senza corrispondenza per i domini gestiti esistenti di Azure AD Domain Services

Se un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) Mostra un errore del tenant non corrispondente, non è possibile amministrare il dominio gestito fino a quando non viene risolto. Questo errore si verifica se la rete virtuale di Azure sottostante viene spostata in un'altra directory Azure AD.

Questo articolo spiega perché si è verificato l'errore e come risolverlo.

## <a name="what-causes-this-error"></a>Causa dell'errore

Si verifica un errore di directory non corrispondente quando un dominio gestito Azure AD DS e una rete virtuale appartengono a due tenant Azure AD diversi. Ad esempio, è possibile che si disponga di un dominio gestito Azure AD DS denominato *aadds.contoso.com* che viene eseguito nel tenant Azure ad di contoso. Tuttavia, la rete virtuale di Azure per il dominio gestito fa parte del tenant Azure AD fabrikam.

Azure usa il controllo degli accessi in base al ruolo (RBAC) per limitare l'accesso alle risorse. Quando si Abilita Azure AD DS in un tenant di Azure AD, gli hash delle credenziali vengono sincronizzati con il dominio gestito. Per eseguire questa operazione, è necessario essere un amministratore tenant per la directory Azure AD e controllare l'accesso alle credenziali. Per distribuire le risorse in una rete virtuale di Azure e controllare il traffico, è necessario disporre dei privilegi amministrativi per la rete virtuale in cui si distribuisce Azure AD DS.

Affinché il controllo degli accessi in base al ruolo funzioni in modo coerente e sicuro per tutte le risorse Azure AD USA DS, il dominio gestito e la rete virtuale devono appartenere allo stesso tenant di Azure AD.

Le regole seguenti si applicano nell'ambiente di Resource Manager:

- Una directory di Azure AD può avere più sottoscrizioni di Azure.
- Una sottoscrizione di Azure potrebbe avere più risorse, ad esempio le reti virtuali.
- Un singolo dominio gestito di Azure AD Domain Services è abilitato per una directory di Azure AD.
- Un dominio gestito di Azure AD Domain Services può essere abilitato in una rete virtuale che appartiene a una delle sottoscrizioni di Azure all'interno dello stesso tenant di Azure AD.

### <a name="valid-configuration"></a>Configurazione valida

Nello scenario di distribuzione di esempio seguente, il dominio gestito contoso Azure AD DS è abilitato nel tenant di Contoso Azure AD. Il dominio gestito viene distribuito in una rete virtuale appartenente a una sottoscrizione di Azure di proprietà del tenant di Contoso Azure AD. Sia il dominio gestito che la rete virtuale appartengono allo stesso tenant di Azure AD. Questa configurazione di esempio è valida e completamente supportata.

![Configurazione del tenant di Azure AD DS valida con il dominio gestito e la parte della rete virtuale dello stesso tenant di Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Configurazione tenant senza corrispondenza

In questo scenario di distribuzione di esempio, il dominio gestito contoso Azure AD DS è abilitato nel tenant di Contoso Azure AD. Tuttavia, il dominio gestito viene distribuito in una rete virtuale appartenente a una sottoscrizione di Azure di proprietà del tenant di Fabrikam Azure AD. Il dominio gestito e la rete virtuale appartengono a due diversi tenant di Azure AD. Questa configurazione di esempio è un tenant non corrispondente e non è supportata. La rete virtuale deve essere spostata nello stesso tenant Azure AD del dominio gestito.

![Configurazione tenant senza corrispondenza](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Errore di risoluzione del tenant non corrispondente

Le due opzioni seguenti risolvono l'errore di directory non corrispondente:

* [Eliminare il dominio gestito Azure AD DS](delete-aadds.md) dalla directory Azure ad esistente. [Creare una sostituzione Azure ad dominio gestito DS](tutorial-create-instance.md) nella stessa directory Azure ad della rete virtuale che si vuole usare. Quando si è pronti, aggiungere tutti i computer aggiunti in precedenza al dominio eliminato al dominio gestito ricreato.
* [Spostare la sottoscrizione di Azure](../billing/billing-subscription-transfer.md) contenente la rete virtuale nella stessa directory Azure ad del dominio gestito di Azure AD DS.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi relativi a Azure AD DS, vedere la [Guida alla risoluzione dei](troubleshoot.md)problemi.
