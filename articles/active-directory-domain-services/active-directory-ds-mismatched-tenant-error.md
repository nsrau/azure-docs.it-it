---
title: Risolvere gli errori di directory senza corrispondenza per i domini gestiti esistenti di Azure AD Domain Services | Microsoft Docs
description: Comprendere e risolvere gli errori di directory senza corrispondenza per i domini gestiti esistenti di Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: maheshu
ms.openlocfilehash: 118773be1f03701246051b8832695c591d76b1e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Risolvere gli errori di directory senza corrispondenza per i domini gestiti esistenti di Azure AD Domain Services
Si dispone di un dominio gestito esistente che è stato abilitato tramite il portale di Azure classico. Quando si passa al nuovo portale di Azure e si visualizza il dominio gestito, compare il messaggio di errore seguente:

![Errore della directory senza corrispondenza](.\media\getting-started\mismatched-tenant-error.png)

Non è possibile gestire il dominio gestito fino a quando l'errore non viene risolto.


## <a name="whats-causing-this-error"></a>Causa dell'errore
Questo errore si verifica quando il dominio gestito e la rete virtuale in cui è abilitato appartengono a due diversi tenant di Azure AD. Ad esempio, si dispone di un dominio gestito denominato "contoso.com" abilitato per il tenant di Azure AD di Contoso. Tuttavia, la rete virtuale di Azure in cui è stato abilitato il dominio gestito appartiene a Fabrikam, un altro tenant di Azure AD.

Il nuovo portale di Azure, e in particolare l'estensione Azure AD Domain Services, si basa su Azure Resource Manager. Nell'ambiente di Azure Resource Manager moderno, vengono applicate alcune restrizioni per offrire maggiore sicurezza e per il controllo dell'accesso basato sui ruoli sulle risorse. L'abilitazione di Azure AD Domain Services per un tenant di Azure AD è un'operazione delicata perché causa la sincronizzazione degli hash delle credenziali con il dominio gestito. Per eseguire questa operazione è necessario essere amministratore del tenant per la directory. È anche necessario disporre dei privilegi di amministratore nella rete virtuale in cui si abilita il dominio gestito. Affinché il controllo degli accesi i base al ruolo funzioni in modo coerente, il dominio gestito e la rete virtuale devono appartenere allo stesso tenant di Azure AD.

In breve, non è possibile abilitare un dominio gestito per un tenant di Azure AD "contoso.com" in una rete virtuale che appartiene a una sottoscrizione di Azure di proprietà di un altro tenant di Azure AD "fabrikam.com". Il portale di Azure classico non è basato su piattaforma di Resource Manager e non impone tali restrizioni.

**Configurazione valida**: in questo scenario di distribuzione, il dominio gestito Contoso è abilitato per il tenant di Azure AD di Contoso. Il dominio gestito viene esposto in una rete virtuale che appartiene a una sottoscrizione di Azure di proprietà del tenant di Azure AD di Contoso. Pertanto, sia il dominio gestito che la rete virtuale appartengono allo stesso tenant di Azure AD. Questa configurazione è valida e completamente supportata.

![Configurazione tenant valida](./media/getting-started/valid-tenant-config.png)

**Configurazione tenant senza corrispondenza**: in questo scenario di distribuzione, il dominio gestito Contoso è abilitato per il tenant di Azure AD di Contoso. Tuttavia, il dominio gestito è esposto in una rete virtuale che appartiene a una sottoscrizione di Azure di proprietà del tenant Fabrikam di Azure AD. Pertanto, sia il dominio gestito che la rete virtuale appartengono a due diversi tenant di Azure AD. Questa configurazione è la configurazione del tenant senza corrispondenza e non è supportata. La rete virtuale deve essere spostata nello stesso tenant di Azure AD, ovvero in Contoso, come dominio gestito. Per informazioni dettagliate, vedere la sezione [Risoluzione](#resolution).

![Configurazione tenant senza corrispondenza](./media/getting-started/mismatched-tenant-config.png)

Pertanto, questo errore si verifica quando il dominio gestito e la rete virtuale in cui è abilitato appartengono a due diversi tenant di Azure AD.

Le regole seguenti si applicano nell'ambiente di Resource Manager:
- Una directory di Azure AD può avere più sottoscrizioni di Azure.
- Una sottoscrizione di Azure potrebbe avere più risorse, ad esempio le reti virtuali.
- Un singolo dominio gestito di Azure AD Domain Services è abilitato per una directory di Azure AD.
- Un dominio gestito di Azure AD Domain Services può essere abilitato in una rete virtuale che appartiene a una delle sottoscrizioni di Azure all'interno dello stesso tenant di Azure AD.


## <a name="resolution"></a>Risoluzione
Per risolvere l'errore di directory senza corrispondenza ci sono due possibilità. L'utente può:

- Fare clic sul pulsante **Elimina** per eliminare il dominio gestito esistente. Effettuare una nuova creazione tramite il [portale di Azure](https://portal.azure.com), in modo che il dominio gestito e la rete virtuale in cui si trova appartengano alla directory di Azure AD. È necessario aggiungere di nuovo al dominio gestito appena creato tutti i computer precedentemente aggiunti al dominio eliminato.

- Contattare il supporto tecnico di Azure per spostare la sottoscrizione di Azure contenente la rete virtuale nella directory di Azure AD a cui appartiene il dominio gestito. Fare clic su **Nuova richiesta di supporto** e specificare **la directory senza corrispondenza** nella sezione **Dettagli** della richiesta di supporto. Includere le informazioni contenute nel messaggio di errore come parte della richiesta di supporto.


## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Guida alla risoluzione dei problemi: Azure AD Domain Services](active-directory-ds-troubleshooting.md)
