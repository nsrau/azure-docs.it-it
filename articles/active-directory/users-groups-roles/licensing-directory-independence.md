---
title: Caratteristiche dell'interazione con più tenant-Azure AD | Microsoft Docs
description: Informazioni sui tenant di Azure Active Directory come organizzazioni completamente indipendenti
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878120"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Informazioni sul modo in cui interagiscono più organizzazioni di Azure Active Directory

In Azure Active Directory (Azure AD) ogni tenant è un'organizzazione completamente indipendente, ovvero un peer logicamente indipendente dalle altre organizzazioni di Azure AD gestite dall'utente. Questa indipendenza tra le organizzazioni include l'indipendenza delle risorse, l'indipendenza amministrativa e l'indipendenza della sincronizzazione. Non esiste alcuna relazione padre-figlio tra le organizzazioni.

## <a name="resource-independence"></a>Indipendenza delle risorse.

* Se si crea o si elimina una risorsa Azure AD in un'organizzazione, non ha alcun effetto sulle risorse di un'altra organizzazione, con l'eccezione parziale degli utenti esterni.
* Se si registra un nome di dominio con un'organizzazione, non può essere usato da altre organizzazioni.

## <a name="administrative-independence"></a>Indipendenza amministrativa

Se un utente non amministratore dell'organizzazione "contoso" crea un'organizzazione di test "test", allora:

* Per impostazione predefinita, l'utente che crea un'organizzazione viene aggiunto come utente esterno nella nuova organizzazione e assegna il ruolo di amministratore globale in tale organizzazione.
* Gli amministratori dell'organizzazione ' Contoso ' non dispongono di privilegi amministrativi diretti per l'organizzazione ' test ', a meno che un amministratore di ' test ' non conceda specificamente questi privilegi. Tuttavia, gli amministratori di "contoso" possono controllare l'accesso all'organizzazione "test" se controllano l'account utente che ha creato "test".
* Se si aggiunge o rimuove un ruolo Azure AD per un utente in un'organizzazione, la modifica non influisce sui ruoli assegnati dall'utente in un'altra organizzazione Azure AD.

## <a name="synchronization-independence"></a>Indipendenza della sincronizzazione

Ogni organizzazione Azure AD può essere configurata in modo indipendente per ottenere la sincronizzazione dei dati da una singola istanza di uno dei seguenti:

* Strumento Azure AD Connect, per sincronizzare i dati con una singola foresta AD.
* Connettore di Azure Active Directory per Forefront Identity Manager, per sincronizzare i dati con una o più foreste locali e/o origini dati non Azure AD.

## <a name="add-an-azure-ad-organization"></a>Aggiungere un'organizzazione Azure AD

Per aggiungere un'organizzazione Azure AD nel portale di Azure, accedere al [portale di Azure](https://portal.azure.com) con un account che sia un amministratore Azure ad globale e selezionare **nuovo**.

> [!NOTE]
> A differenza di altre risorse di Azure, le organizzazioni Azure AD non sono risorse figlio di una sottoscrizione di Azure. Se la sottoscrizione di Azure è stata annullata o scaduta, è comunque possibile accedere ai dati dell'organizzazione Azure AD usando Azure PowerShell, l'API Microsoft Graph o l'interfaccia di amministrazione di Microsoft 365. È anche possibile [associare un'altra sottoscrizione all'organizzazione](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Passaggi successivi

Per Azure AD considerazioni e procedure consigliate per la gestione delle licenze, vedere [che cos'è Azure Active Directory Licensing?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
