---
title: Caratteristiche dell'interazione con più tenant - Azure AD Documenti Microsoft
description: Informazioni sui tenant di Azure Active Directory come organizzazioni completamente indipendentiUnderstanding your Azure Active Directory tenants as fully independent organizations
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878120"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Comprendere l'interazione tra più organizzazioni di Azure Active Directory

In Azure Active Directory (Azure AD) ogni tenant è un'organizzazione completamente indipendente: un peer logicamente indipendente dalle altre organizzazioni di Azure AD gestite. Questa indipendenza tra le organizzazioni include l'indipendenza delle risorse, l'indipendenza amministrativa e l'indipendenza della sincronizzazione. Non esiste alcuna relazione padre-figlio tra organizzazioni.

## <a name="resource-independence"></a>Indipendenza delle risorse.

* Se si crea o si elimina una risorsa di Azure AD in un'organizzazione, non ha alcun impatto su alcuna risorsa in un'altra organizzazione, con l'eccezione parziale degli utenti esterni.
* Se registri uno dei tuoi nomi di dominio con un'organizzazione, non può essere utilizzato da nessun'altra organizzazione.

## <a name="administrative-independence"></a>Indipendenza amministrativa

Se un utente non amministrativo dell'organizzazione 'Contoso' crea un'organizzazione di test 'Test', allora:

* Per impostazione predefinita, l'utente che crea un'organizzazione viene aggiunto come utente esterno nella nuova organizzazione e gli viene assegnato il ruolo di amministratore globale in tale organizzazione.
* Gli amministratori dell'organizzazione 'Contoso' non dispongono di privilegi amministrativi diretti per l'organizzazione 'Test', a meno che un amministratore di 'Test' non conceda specificamente tali privilegi. Tuttavia, gli amministratori di 'Contoso' possono controllare l'accesso all'organizzazione 'Test' se controllano l'account utente che ha creato 'Test'.
* Se si aggiunge o si rimuove un ruolo di Azure AD per un utente in un'organizzazione, la modifica non influisce sui ruoli assegnati all'utente in qualsiasi altra organizzazione di Azure AD.

## <a name="synchronization-independence"></a>Indipendenza della sincronizzazione

È possibile configurare ogni organizzazione di Azure AD in modo indipendente per ottenere la sincronizzazione dei dati da una singola istanza di:You can configure each Azure AD organization independently to get data synchronized from a single instance of either:

* Strumento Azure AD Connect, per sincronizzare i dati con una singola foresta AD.
* Connettore di Azure Active Directory per Forefront Identity Manager, per sincronizzare i dati con una o più foreste locali e/o origini dati non Azure AD.

## <a name="add-an-azure-ad-organization"></a>Aggiungere un'organizzazione di Azure ADAdd an Azure AD organization

Per aggiungere un'organizzazione di Azure AD nel portale di Azure, accedere al portale di [Azure](https://portal.azure.com) con un account che sia un amministratore globale di Azure AD e selezionare **Nuovo.**

> [!NOTE]
> A differenza di altre risorse di Azure, le organizzazioni di Azure AD non sono risorse figlio di una sottoscrizione di Azure.Unlike other Azure resources, your Azure AD organizations are not child resources of an Azure subscription. Se la sottoscrizione di Azure viene annullata o è scaduta, è comunque possibile accedere ai dati dell'organizzazione di Azure AD usando Azure PowerShell, l'API Microsoft Graph o l'interfaccia di amministrazione di Microsoft 365.If your Azure subscription is canceled or expired, you can still access your Azure AD organization's organization using Azure PowerShell, the Microsoft Graph API, or the Microsoft 365 admin center. È inoltre possibile [associare un'altra sottoscrizione all'organizzazione.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Passaggi successivi

Per considerazioni sulle licenze di Azure AD e procedure consigliate, vedere [Che cos'è la licenza](../fundamentals/active-directory-licensing-whatis-azure-portal.md)di Azure Active Directory? .
