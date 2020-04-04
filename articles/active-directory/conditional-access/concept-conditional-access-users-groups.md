---
title: Utenti e gruppi nei criteri di accesso condizionale - Azure Active DirectoryUsers and groups in Conditional Access policy - Azure Active Directory
description: Utenti e gruppi in un criterio di accesso condizionale di Azure ADWho are users and groups in an Azure AD Conditional Access policy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ae866959dd2112bacbb6b56e5683e7b3b851a0
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631912"
---
# <a name="conditional-access-users-and-groups"></a>Accesso condizionale: utenti e gruppi

Un criterio di accesso condizionale deve includere un'assegnazione utente come uno dei segnali nel processo decisionale. Gli utenti possono essere inclusi o esclusi dai criteri di accesso condizionale. 

![L'utente come segnale nelle decisioni prese dall'accesso condizionale](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Includi utenti

Questo elenco di utenti include in genere tutti gli utenti di destinazione dell'organizzazione in un criterio di accesso condizionale. 

Quando si crea criteri di accesso condizionale, sono disponibili le opzioni seguenti.

- nessuno
   - Nessun utente selezionato
- tutti gli utenti
   - Tutti gli utenti presenti nella directory, inclusi i guest B2B.
- Selezionare Utenti e gruppi
   - Tutti gli utenti guest ed esterni (anteprima)
      - Questa selezione include tutti gli ospiti B2B `user type` e gli `guest`utenti esterni, incluso qualsiasi utente con l'attributo impostato su . Questa selezione si applica anche a qualsiasi utente esterno connesso da un'organizzazione diversa, ad esempio Cloud Solution Provider (CSP). 
   - Ruoli directory (anteprima)Directory roles (preview)
      - Consente agli amministratori di selezionare ruoli di directory di Azure AD specifici usati per determinare l'assegnazione. Ad esempio, le organizzazioni possono creare criteri più restrittivi per gli utenti assegnati al ruolo di amministratore globale.
   - Utenti e gruppi
      - Consente il targeting di set specifici di utenti. Ad esempio, le organizzazioni possono selezionare un gruppo che contiene tutti i membri del reparto risorse umane quando un'app HR viene selezionata come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

## <a name="exclude-users"></a>Escludi utenti

Quando le organizzazioni includono ed escludono un utente o un gruppo, l'utente o il gruppo viene escluso dal criterio, poiché un'azione di esclusione esegue l'override di un'inclusione nei criteri. Le esclusioni sono comunemente utilizzate per l'accesso di emergenza o i conti break-glass. Ulteriori informazioni sugli account di accesso di emergenza e perché sono importanti sono disponibili nei seguenti articoli: 

* [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Creare una strategia di gestione di controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)

Quando si crea criteri di accesso condizionale, sono disponibili le opzioni seguenti.

- Tutti gli utenti guest ed esterni (anteprima)
   - Questa selezione include tutti gli ospiti B2B `user type` e gli `guest`utenti esterni, incluso qualsiasi utente con l'attributo impostato su . Questa selezione si applica anche a qualsiasi utente esterno connesso da un'organizzazione diversa, ad esempio Cloud Solution Provider (CSP). 
- Ruoli directory (anteprima)Directory roles (preview)
   - Consente agli amministratori di selezionare ruoli di directory di Azure AD specifici usati per determinare l'assegnazione. Ad esempio, le organizzazioni possono creare criteri più restrittivi per gli utenti assegnati al ruolo di amministratore globale.
- Utenti e gruppi
   - Consente il targeting di set specifici di utenti. Ad esempio, le organizzazioni possono selezionare un gruppo che contiene tutti i membri del reparto risorse umane quando un'app HR viene selezionata come app cloud. Un gruppo può essere un gruppo di qualsiasi tipo in Azure AD, inclusi gruppi di sicurezza e distribuzione dinamici o assegnati.

### <a name="preventing-administrator-lockout"></a>Impedire il blocco dell'amministratore

Per impedire a un amministratore di bloccarsi dalla propria directory durante la creazione di un criterio applicato a **Tutti gli utenti** e Tutte le **app,** verrà visualizzato il seguente avviso.

> Non chiuderti fuori! È consigliabile applicare prima un criterio a un piccolo set di utenti per verificare che si comporti come previsto. Si consiglia inoltre di escludere almeno un amministratore da questo criterio. In questo modo si garantisce che l'accesso sia ancora disponibile e di poter aggiornare un criterio se è necessaria una modifica. Si prega di esaminare gli utenti interessati e le applicazioni.

Per impostazione predefinita, il criterio fornisce un'opzione per escludere l'utente corrente dal criterio, ma questa impostazione predefinita può essere sostituita dall'amministratore, come illustrato nell'immagine seguente. 

![Attenzione, non chiudetevi fuori!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: app o azioni cloudConditional Access: Cloud apps or actions](concept-conditional-access-cloud-apps.md)

- [Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)
