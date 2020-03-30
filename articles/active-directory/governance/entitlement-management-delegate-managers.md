---
title: Delegare la governance dell'accesso per accedere ai gestori dei pacchetti nella gestione dei diritti di Azure AD - Azure Active DirectoryDelegate access governance to access package managers in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come delegare la governance dell'accesso da parte degli amministratori IT per accedere ai gestori di pacchetti e ai project manager in modo che possano gestire l'accesso da soli.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174366"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegare la governance dell'accesso per accedere ai gestori dei pacchetti nella gestione dei diritti di Azure ADDelegate access governance to access package managers in Azure AD entitlement management

Per delegare la creazione e la gestione dei pacchetti di accesso in un catalogo, aggiungere utenti al ruolo di gestione pacchetti di accesso. I gestori dei pacchetti di Access devono avere familiarità con la necessità per gli utenti di richiedere l'accesso alle risorse in un catalogo. Ad esempio, se un catalogo viene utilizzato per un progetto, un responsabile di progetto potrebbe essere un gestore di pacchetti di accesso per tale catalogo.  I gestori di pacchetti di Access non possono aggiungere risorse a un catalogo, ma possono gestire i pacchetti di accesso e i criteri in un catalogo.  Quando si delega a un gestore di pacchetti di accesso, tale persona può quindi essere responsabile di:When delegating to an access package manager, that person can then be responsible for:

- Ruoli di un utente alle risorse in un catalogo
- Chi avrà bisogno di accedere
- Chi deve approvare le richieste di accesso
- Quanto durerà il progetto

Questo video offre una panoramica su come delegare la governance dell'accesso dal proprietario del catalogo al gestore di pacchetti.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>In qualità di proprietario del catalogo, delegare a un gestore di pacchetti di accesso

Seguire questi passaggi per assegnare un utente al ruolo di gestione pacchetti di accesso:Follow these steps to assign a user to the Access package manager role:

**Ruolo prerequisito:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Cataloghi** e quindi aprire il catalogo a cui si desidera aggiungere amministratori.

1. Nel menu a sinistra, fai clic su **Ruoli e amministratori.**

    ![Cataloga i ruoli e gli amministratori](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Fare clic su **Aggiungi gestori di pacchetti** di accesso per selezionare i membri per questi ruoli.

1. Fare clic su **Seleziona** per aggiungere questi membri.

## <a name="remove-an-access-package-manager"></a>Rimuovere un gestore di pacchetti di accessoRemove an access package manager

Seguire questi passaggi per rimuovere un utente dal ruolo di gestione pacchetti di accesso:Follow these steps to remove a user from the Access package manager role:

**Ruolo prerequisito:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Cataloghi** e quindi aprire il catalogo a cui si desidera aggiungere amministratori.

1. Nel menu a sinistra, fai clic su **Ruoli e amministratori.**

1. Aggiungere un segno di spunta accanto a un gestore di pacchetti di accesso che si desidera rimuovere.

1. Scegliere **Rimuovi**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md)
