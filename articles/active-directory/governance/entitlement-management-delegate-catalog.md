---
title: Delega della governance di accesso ai creatori di cataloghi in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come delegare la governance degli accessi dagli amministratori IT ai creatori di cataloghi e ai responsabili di progetto in modo da poter gestire l'accesso autonomamente.
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
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170763"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Delega della governance di accesso ai creatori di cataloghi in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per delegare agli utenti che non sono amministratori, in modo che possano creare i propri cataloghi, è possibile aggiungere tali utenti al ruolo di autore del catalogo di Azure AD diritti di gestione. È possibile aggiungere singoli utenti o aggiungere un gruppo, i cui membri sono quindi in grado di creare cataloghi.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>In qualità di amministratore IT, delegare a un creatore del catalogo

Attenersi alla procedura seguente per assegnare un utente al ruolo di autore del catalogo.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nella sezione **gestione dei diritti** , fare clic su **Impostazioni**.

1. Fare clic su **Modifica**.

    ![Impostazioni per aggiungere autori del catalogo](./media/entitlement-management-delegate/settings-delegate.png)

1. Nella sezione **delega diritti di gestione** , fare clic su **Aggiungi autori del catalogo** per selezionare gli utenti o i gruppi a cui si desidera delegare questo ruolo di gestione dei diritti.

1. Fare clic su **Seleziona**.

1. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
- [Delega della governance di accesso per accedere ai gestori di pacchetti](entitlement-management-delegate-managers.md)

