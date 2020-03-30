---
title: Delegare la governance dell'accesso ai creatori del catalogo nella gestione dei diritti di Azure AD - Azure Active DirectoryDelegate access governance to catalog creators in Azure AD entitlement management - Azure Active Directory
description: Informazioni su come delegare la governance dell'accesso dagli amministratori IT ai creatori di cataloghi e ai project manager in modo che possano gestire l'accesso da soli.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120187"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegare la governance dell'accesso ai creatori di cataloghi nella gestione dei diritti di Azure ADDelegate access governance to catalog creators in Azure AD entitlement management

Un catalogo è un contenitore di risorse e pacchetti di accesso. Creare un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Per impostazione predefinita, un amministratore globale o un amministratore utente può [creare un catalogo](entitlement-management-catalog-create.md)e può aggiungere altri utenti come proprietari del catalogo.

Per delegare agli utenti che non sono amministratori, in modo che possano creare i propri cataloghi, è possibile aggiungere tali utenti al ruolo di creatore del catalogo definito dalla gestione dei diritti di Azure AD. È possibile aggiungere singoli utenti oppure un gruppo, i cui membri sono quindi in grado di creare cataloghi.  Dopo aver creato un catalogo, è possibile aggiungere successivamente le risorse di cui sono proprietari al catalogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>In qualità di amministratore IT, delega a un creatore del catalogo

Seguire questi passaggi per assegnare un utente al ruolo di creatore del catalogo.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra, nella sezione **Gestione dei diritti,** fare clic su **Impostazioni.**

1. Fare clic su **Edit**.

    ![Impostazioni per aggiungere creatori di cataloghi](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Nella sezione **Gestione diritti delegati** fare clic su Aggiungi creatori di **cataloghi** per selezionare gli utenti o i gruppi a cui si desidera delegare questo ruolo di gestione dei diritti.

1. Fare clic su **Seleziona**.

1. Fare clic su **Salva**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Consentire ai ruoli delegati di accedere al portale di AzureAllow delegated roles to access the Azure portal

Per consentire ai ruoli delegati, ad esempio i creatori di cataloghi e i gestori di pacchetti di accesso, di accedere al portale di Azure per gestire i pacchetti di accesso, è necessario controllare l'impostazione del portale di amministrazione.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Utenti**.

1. Nel menu a sinistra, fai clic su **Impostazioni utente.**

1. Verificare che **L'impostazione dell'accesso al portale** di amministrazione di Azure AD sia impostata su **No**.

    ![Impostazioni utente di Azure AD - Portale di amministrazioneAzure AD user settings - Administration portal](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
- [Delegare la governance dell'accesso per accedere ai gestori di pacchettiDelegate access governance to access package manager](entitlement-management-delegate-managers.md)

