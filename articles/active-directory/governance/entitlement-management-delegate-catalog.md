---
title: Delega della governance di accesso ai creatori di cataloghi in Azure AD gestione dei diritti-Azure Active Directory
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120187"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delega della governance di accesso ai creatori di cataloghi in Azure AD gestione dei diritti

Un catalogo è un contenitore di risorse e pacchetti di accesso. Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Per impostazione predefinita, un amministratore globale o un amministratore utente può [creare un catalogo](entitlement-management-catalog-create.md)e può aggiungere altri utenti come proprietari del catalogo.

Per delegare agli utenti che non sono amministratori, in modo che possano creare i propri cataloghi, è possibile aggiungere tali utenti al ruolo di autore del catalogo di Azure AD diritti di gestione. È possibile aggiungere singoli utenti o aggiungere un gruppo, i cui membri sono quindi in grado di creare cataloghi.  Dopo aver creato un catalogo, gli utenti possono aggiungere le risorse di cui sono proprietari nel catalogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>In qualità di amministratore IT, delegare a un creatore del catalogo

Attenersi alla procedura seguente per assegnare un utente al ruolo di autore del catalogo.

**Ruolo prerequisiti:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nella sezione **gestione dei diritti** , fare clic su **Impostazioni**.

1. Fare clic su **Edit**.

    ![Impostazioni per aggiungere autori del catalogo](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Nella sezione **delega diritti di gestione** , fare clic su **Aggiungi autori del catalogo** per selezionare gli utenti o i gruppi a cui si desidera delegare questo ruolo di gestione dei diritti.

1. Fare clic su **Seleziona**.

1. Fare clic su **Save**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Consenti ai ruoli delegati di accedere al portale di Azure

Per consentire ai ruoli delegati, ad esempio gli autori del catalogo e i gestori di pacchetti di accesso, di accedere ai portale di Azure per gestire i pacchetti di accesso, è consigliabile controllare l'impostazione del portale di amministrazione.

**Ruolo prerequisiti:** Amministratore globale o Amministratore utenti

1. Nella portale di Azure fare clic su **Azure Active Directory** , quindi fare clic su **utenti**.

1. Nel menu a sinistra fare clic su **impostazioni utente**.

1. Assicurarsi che **l'opzione limita l'accesso al portale di amministrazione Azure ad** sia impostata su **No**.

    ![Impostazioni utente Azure AD-portale di amministrazione](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
- [Delega della governance di accesso per accedere ai gestori di pacchetti](entitlement-management-delegate-managers.md)

