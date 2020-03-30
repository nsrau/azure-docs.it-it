---
title: Correggere gli errori di directory non corrispondenti in Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni sul significato di un errore di directory non corrispondente e su come risolverlo in Servizi di dominio Azure AD
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
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613377"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Risolvere gli errori di directory senza corrispondenza per i domini gestiti esistenti di Azure AD Domain Services

Se un dominio gestito di Servizi di dominio Azure Active Directory (Azure AD DS) mostra un errore del tenant non corrispondente, non è possibile amministrare il dominio gestito fino alla risoluzione. Questo errore si verifica se la rete virtuale di Azure sottostante viene spostata in una directory di Azure AD diversa.

In questo articolo viene spiegato perché si verifica l'errore e come risolverlo.

## <a name="what-causes-this-error"></a>Quali sono le cause di questo errore?

Un errore di directory non corrispondente si verifica quando un dominio gestito di Servizi di dominio Active Directory di Azure e una rete virtuale appartengono a due tenant di Azure AD diversi. Ad esempio, si potrebbe avere un dominio gestito di Azure AD DS denominato aaddscontoso.com che viene eseguito nel tenant di Azure AD di Contoso.For example, you may have an Azure AD DS managed domain called *aaddscontoso.com* that runs in Contoso's Azure AD tenant. Tuttavia, la rete virtuale di Azure per il dominio gestito fa parte del tenant Fabrikam Azure AD.

Azure usa il controllo degli accessi in base al ruolo per limitare l'accesso alle risorse. Quando si abilita Servizi di dominio Active Directory di Azure in un tenant di Azure AD, gli hashe delle credenziali vengono sincronizzati con il dominio gestito. Questa operazione richiede di essere un amministratore tenant per la directory di Azure AD e l'accesso alle credenziali deve essere controllato. To deploy resources to an Azure virtual network and control traffic, you must have administrative privileges on the virtual network in which you deploy Azure AD DS.

Affinché il controllo degli accessi in base al ruolo funzioni in modo coerente e sicuro l'accesso a tutte le risorse utilizzate da Servizi di dominio Active Directory di Azure, il dominio gestito e la rete virtuale devono appartenere allo stesso tenant di Azure AD.

Le regole seguenti si applicano nell'ambiente di Resource Manager:

- Una directory di Azure AD può avere più sottoscrizioni di Azure.
- Una sottoscrizione di Azure potrebbe avere più risorse, ad esempio le reti virtuali.
- Un singolo dominio gestito di Azure AD Domain Services è abilitato per una directory di Azure AD.
- Un dominio gestito di Azure AD Domain Services può essere abilitato in una rete virtuale che appartiene a una delle sottoscrizioni di Azure all'interno dello stesso tenant di Azure AD.

### <a name="valid-configuration"></a>Configurazione valida

Nello scenario di distribuzione di esempio seguente, il dominio gestito Contoso Azure AD DS è abilitato nel tenant di Azure AD di Contoso.In the following example deployment scenario, the Contoso Azure AD DS managed domain is enabled in the Contoso Azure AD tenant. Il dominio gestito viene distribuito in una rete virtuale appartenente a una sottoscrizione di Azure di proprietà del tenant di Azure AD di Contoso.The managed domain is deployed in a virtual network that belongs to an Azure subscription owned by the Contoso Azure AD tenant. Sia il dominio gestito che la rete virtuale appartengono allo stesso tenant di Azure AD. Questa configurazione di esempio è valida e completamente supportata.

![Configurazione valida del tenant di Servizi di dominio Active Directory con la parte del dominio gestito e della rete virtuale dello stesso tenant di Azure ADValid Azure AD DS tenant configuration with the managed domain and virtual network part of the same Azure AD tenant](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Configurazione tenant senza corrispondenza

In questo scenario di distribuzione di esempio, il dominio gestito Contoso Azure AD DS è abilitato nel tenant di Azure AD di Contoso.In this example deployment scenario, the Contoso Azure AD DS managed domain is enabled in the Contoso Azure AD tenant. Tuttavia, il dominio gestito viene distribuito in una rete virtuale che appartiene a una sottoscrizione di Azure di proprietà del tenant di Fabrikam Azure AD. Il dominio gestito e la rete virtuale appartengono a due tenant di Azure AD diversi. Questa configurazione di esempio è un tenant non corrispondente e non è supportata. La rete virtuale deve essere spostata nello stesso tenant di Azure AD del dominio gestito.

![Configurazione tenant senza corrispondenza](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Risolvere l'errore del tenant non corrispondente

Le due opzioni seguenti risolvono l'errore di directory non corrispondente:

* [Eliminare il dominio gestito](delete-aadds.md) di Servizi di dominio Active Directory di Azure dalla directory di Azure AD esistente. Creare un dominio gestito di Servizi di dominio Active Directory [di Azure sostitutivo](tutorial-create-instance.md) nella stessa directory di Azure AD della rete virtuale che si vuole usare. Quando si è pronti, aggiungere tutti i computer aggiunti in precedenza al dominio eliminato al dominio gestito ricreato.
* [Spostare la sottoscrizione](../cost-management-billing/manage/billing-subscription-transfer.md) di Azure contenente la rete virtuale nella stessa directory di Azure AD del dominio gestito di Servizi di dominio Active Directory di Azure.Move the Azure subscription containing the virtual network to the same Azure AD directory as the Azure AD DS managed domain.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi relativi a Azure AD DS, vedere la [guida alla risoluzione dei problemi.](troubleshoot.md)
