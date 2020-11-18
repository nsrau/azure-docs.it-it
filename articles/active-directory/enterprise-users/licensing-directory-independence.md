---
title: Caratteristiche dell'interazione tra più tenant - Azure AD | Microsoft Docs
description: Informazioni sull'indipendenza dei dati delle organizzazioni Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.subservice: enterprise-users
ms.date: 11/15/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: b35c8630f47910c61e169d405c2117e53d0d536e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650528"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Informazioni sull'interazione tra più organizzazioni di Azure Active Directory

In Azure Active Directory (Azure AD) ogni organizzazione è una risorsa totalmente indipendente, ovvero un peer logicamente indipendente dalle altre organizzazioni di Azure AD gestite. Questa indipendenza tra le organizzazioni include l'indipendenza delle risorse, l'indipendenza amministrativa e l'indipendenza della sincronizzazione. Tra le organizzazioni non esiste alcuna relazione padre-figlio.

## <a name="resource-independence"></a>Indipendenza delle risorse.

* Se si crea o si elimina una risorsa Azure AD in un'organizzazione, ciò non influisce sulle risorse presenti in un'altra organizzazione, con la parziale eccezione degli utenti esterni.
* Se si registra un nome di dominio con un'organizzazione, non può essere usato da altre organizzazioni.

## <a name="administrative-independence"></a>Indipendenza amministrativa

Se un utente non amministratore dell'organizzazione "Contoso" crea un'organizzazione di test denominata 'Test', si verifica quanto segue:

* Per impostazione predefinita, l'utente che crea un'organizzazione viene aggiunto come utente esterno nella nuova organizzazione e gli viene assegnato il ruolo di amministratore globale in quella organizzazione.
* Gli amministratori dell'organizzazione 'Contoso' non dispongono di privilegi amministrativi diretti per l'organizzazione 'Test', a meno che questi privilegi non vengano specificamente concessi da un amministratore di 'Test'. Gli amministratori di 'Contoso' possono tuttavia controllare l'accesso all'organizzazione 'Test' se controllano l'account utente che ha creato 'Test'.
* Se si aggiunge o rimuove un ruolo di Azure AD per un utente in un'organizzazione, la modifica non influisce sui ruoli a cui l'utente è assegnato in altre organizzazioni di Azure AD.

## <a name="synchronization-independence"></a>Indipendenza della sincronizzazione

È possibile configurare ogni organizzazione di Azure AD in modo indipendente per sincronizzare i dati da una singola istanza di uno degli elementi seguenti:

* Strumento Azure AD Connect, per sincronizzare i dati con una singola foresta AD.
* Connettore di Azure Active Directory per Forefront Identity Manager, per sincronizzare i dati con una o più foreste locali e/o origini dati non Azure AD.

## <a name="add-an-azure-ad-organization"></a>Aggiungere un'organizzazione di Azure AD

Per aggiungere un'organizzazione di Azure AD nel portale di Azure, accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per Azure AD e selezionare **Nuova**.

> [!NOTE]
> A differenza di altre risorse di Azure, le organizzazioni di Azure AD non sono risorse figlio di una sottoscrizione di Azure. Se si annulla o si lascia scadere la sottoscrizione di Azure, sarà comunque possibile accedere ai dati dell'organizzazione di Azure AD tramite Azure PowerShell, l'API Microsoft Graph o l'interfaccia di amministrazione di Microsoft 365. È anche possibile [associare un'altra sottoscrizione all'organizzazione](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Passaggi successivi

Per le considerazioni sulle licenze di Azure AD e le procedure consigliate, vedere [Che cosa sono le licenze di Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
