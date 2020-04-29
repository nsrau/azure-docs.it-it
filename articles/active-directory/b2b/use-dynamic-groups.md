---
title: Gruppi dinamici e collaborazione B2B-Azure Active Directory | Microsoft Docs
description: Mostra come usare i gruppi dinamici di Azure AD con la funzionalità di collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78226857"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Gruppi dinamici e Collaborazione B2B di Azure Active Directory

## <a name="what-are-dynamic-groups"></a>Che cosa sono i gruppi dinamici
La configurazione dinamica dell'appartenenza a gruppi di sicurezza per Azure Active Directory (Azure AD) è disponibile nel [portale di Azure](https://portal.azure.com). Gli amministratori possono impostare regole per popolare i gruppi creati in Azure AD in base agli attributi utente, ad esempio userType, Department o Country/Region. I membri possono essere automaticamente aggiunti o rimossi in un gruppo di sicurezza in base agli attributi. Questi gruppi possono fornire accesso ad applicazioni o a risorse cloud, (documenti, siti di SharePoint) e per assegnare licenze ai membri. Per altre informazioni sui gruppi dinamici, vedere [Gruppi dedicati in Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

La [licenza di Azure AD Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/) appropriata è necessaria per creare e usare gruppi dinamici. Maggiori informazioni sono riportate nell’articolo [Creare regole basate su attributi per l'appartenenza dinamica ai gruppi in Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Creazione di un gruppo dinamico "tutti gli utenti"
È possibile creare un gruppo contenente tutti gli utenti di un tenant usando una regola di appartenenza. Quando in futuro gli utenti vengono aggiunti o rimossi dal tenant, l'appartenenza al gruppo viene modificata automaticamente.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account a cui è assegnato il ruolo di amministratore globale o amministratore utente nel tenant.
1. Selezionare **Azure Active Directory**.
2. In **Gestisci**selezionare **gruppi**, quindi selezionare **nuovo gruppo**.
1. Nella pagina **nuovo gruppo** , in **tipo di gruppo**, selezionare **sicurezza**. Immettere un **Nome gruppo** e una **Descrizione gruppo** per il nuovo gruppo. 
2. In **tipo di appartenenza**selezionare **utente dinamico**, quindi selezionare **Aggiungi query dinamica**. 
4. Sopra la casella di testo **sintassi regola** selezionare **modifica**. Nella pagina **modifica sintassi regola** Digitare l'espressione seguente nella casella di testo:

   ```
   user.objectId -ne null
   ```
1. Selezionare **OK**. La regola viene visualizzata nella casella sintassi regola:

   ![Sintassi della regola per tutti gli utenti gruppo dinamico](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Selezionare **Salva**. Il nuovo gruppo dinamico include ora gli utenti Guest B2B e gli utenti membri.


1. Selezionare **Crea** nella pagina **nuovo gruppo** per creare il gruppo.

## <a name="creating-a-group-of-members-only"></a>Creazione di un gruppo di membri

Se si vuole che il gruppo escluda gli utenti guest e includa solo i membri del tenant, creare un gruppo dinamico come descritto in precedenza, ma nella casella **sintassi regola** immettere l'espressione seguente:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Nell'immagine seguente viene illustrata la sintassi della regola per un gruppo dinamico modificato in modo da includere solo i membri ed escludere i guest.

![Mostra la regola in cui il tipo di utente è uguale al membro](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Creazione di un gruppo di utenti Guest

Potrebbe inoltre essere utile creare un nuovo gruppo dinamico che contiene solo gli utenti guest, in modo che sia possibile applicare a essi criteri quali, ad esempio, criteri di accesso condizionale di Azure AD. Creare un gruppo dinamico come descritto in precedenza, ma nella casella **sintassi regola** immettere l'espressione seguente:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Nell'immagine seguente viene illustrata la sintassi della regola per un gruppo dinamico modificato in modo da includere solo i guest ed escludere gli utenti membri.

![Mostra la regola in cui il tipo di utente è uguale a guest](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Passaggi successivi

- [Proprietà dell'utente di Collaborazione B2B](user-properties.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)
- [Accesso condizionale per gli utenti di collaborazione B2B](conditional-access.md)

