---
title: Delegare la governance di accesso per accedere ai gestori di pacchetti in Azure AD gestione dei diritti Azure Active Directory
description: Informazioni su come delegare la governance di accesso agli amministratori IT per accedere ai gestori di pacchetti e ai Project Manager in modo che possano gestire l'accesso.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e81389ec953829115062a31a019caf040850cbea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798639"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delega della governance di accesso per accedere ai gestori di pacchetti in Azure AD gestione dei diritti

Per delegare la creazione e la gestione dei pacchetti di accesso in un catalogo, è necessario aggiungere gli utenti al ruolo Gestione pacchetti di accesso. Per accedere ai gestori di pacchetti, è necessario avere familiarità con la necessità per gli utenti di richiedere l'accesso alle risorse in un catalogo. Se, ad esempio, un catalogo viene usato per un progetto, un responsabile del progetto potrebbe essere una gestione pacchetti di accesso per tale catalogo.  I gestori di pacchetti di accesso non possono aggiungere risorse a un catalogo, ma possono gestire i pacchetti e i criteri di accesso in un catalogo.  Quando si delega a una gestione pacchetti di Access, tale utente può quindi essere responsabile di:

- Quali ruoli avrà un utente per le risorse in un catalogo
- Utenti che dovranno accedere
- Utenti che devono approvare le richieste di accesso
- Per quanto tempo il progetto durerà

In questo video viene fornita una panoramica su come delegare la governance di accesso dal proprietario del catalogo per accedere a gestione pacchetti.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Come proprietario del catalogo, delegare a una gestione pacchetti di Access

Attenersi alla procedura seguente per assegnare un utente al ruolo di gestione pacchetti di accesso:

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo al quale si desidera aggiungere gli amministratori.

1. Nel menu a sinistra fare clic su **ruoli e amministratori**.

    ![Ruoli e amministratori di cataloghi](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Fare clic su **Aggiungi gestione pacchetti di accesso** per selezionare i membri per questi ruoli.

1. Fare clic su **Seleziona** per aggiungere questi membri.

## <a name="remove-an-access-package-manager"></a>Rimuovere una gestione pacchetti di accesso

Attenersi alla procedura seguente per rimuovere un utente dal ruolo Gestione pacchetti di accesso:

**Ruolo prerequisiti:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **cataloghi** , quindi aprire il catalogo al quale si desidera aggiungere gli amministratori.

1. Nel menu a sinistra fare clic su **ruoli e amministratori**.

1. Aggiungere un segno di spunta accanto a una gestione pacchetti di accesso che si desidera rimuovere.

1. Scegliere **Rimuovi**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md)
