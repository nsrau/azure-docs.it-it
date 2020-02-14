---
title: Utenti e gruppi nei criteri di accesso condizionale-Azure Active Directory
description: Utenti e gruppi in un Azure AD criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192129"
---
# <a name="conditional-access-users-and-groups"></a>Accesso condizionale: utenti e gruppi

Un criterio di accesso condizionale deve includere un'assegnazione utente come uno dei segnali nel processo decisionale. Gli utenti possono essere inclusi o esclusi dai criteri di accesso condizionale. 

![Utente come segnale nelle decisioni prese dall'accesso condizionale](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Includi utenti

Questo elenco di utenti include in genere tutti gli utenti a cui è destinata un'organizzazione in un criterio di accesso condizionale. 

Quando si creano criteri di accesso condizionale, è possibile includere le opzioni seguenti.

- nessuno
   - Nessun utente selezionato
- tutti gli utenti
   - Tutti gli utenti presenti nella directory, inclusi i guest B2B.
- Selezionare Utenti e gruppi
   - Tutti gli utenti guest ed External (anteprima)
      - Questa selezione include tutti i guest B2B e gli utenti esterni che includono qualsiasi utente con l'attributo `user type` impostato su `guest`. Questa selezione si applica anche a qualsiasi utente esterno connesso da un'organizzazione diversa, ad esempio un provider di soluzioni cloud (CSP). 
   - Ruoli della directory (anteprima)
      - Consente agli amministratori di selezionare ruoli di directory Azure AD specifici utilizzati per determinare l'assegnazione. Ad esempio, le organizzazioni possono creare criteri più restrittivi per gli utenti assegnati al ruolo di amministratore globale.
   - Utenti e gruppi
      - Consente la destinazione di set di utenti specifici. Ad esempio, le organizzazioni possono selezionare un gruppo che contiene tutti i membri del reparto risorse umane quando si seleziona un'app per le risorse umane come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

## <a name="exclude-users"></a>Escludi utenti

Le esclusioni vengono comunemente usate per gli account di accesso di emergenza o break-Glass. Altre informazioni sugli account di accesso di emergenza e sul motivo per cui sono importanti sono disponibili negli articoli seguenti: 

* [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Creare una strategia di gestione del controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

Quando si creano criteri di accesso condizionale, è possibile escludere le opzioni seguenti.

- Tutti gli utenti guest ed External (anteprima)
   - Questa selezione include tutti i guest B2B e gli utenti esterni che includono qualsiasi utente con l'attributo `user type` impostato su `guest`. Questa selezione si applica anche a qualsiasi utente esterno connesso da un'organizzazione diversa, ad esempio un provider di soluzioni cloud (CSP). 
- Ruoli della directory (anteprima)
   - Consente agli amministratori di selezionare ruoli di directory Azure AD specifici utilizzati per determinare l'assegnazione. Ad esempio, le organizzazioni possono creare criteri più restrittivi per gli utenti assegnati al ruolo di amministratore globale.
- Utenti e gruppi
   - Consente la destinazione di set di utenti specifici. Ad esempio, le organizzazioni possono selezionare un gruppo che contiene tutti i membri del reparto risorse umane quando si seleziona un'app per le risorse umane come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: app Cloud o azioni](concept-conditional-access-cloud-apps.md)

- [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)
