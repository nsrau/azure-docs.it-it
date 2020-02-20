---
title: Autorizzare l'accesso alla configurazione di app Azure tramite Azure Active Directory
description: Abilitare RBAC per autorizzare l'accesso all'istanza di configurazione di app Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472622"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizzare l'accesso alla configurazione di app Azure tramite Azure Active Directory
App Azure configurazione supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle istanze di configurazione dell'app.  Azure AD consente di usare il controllo degli accessi in base al ruolo (RBAC) per concedere le autorizzazioni a un'entità di sicurezza.  Un'entità di sicurezza può essere un utente o un' [entità servizio dell'applicazione](../active-directory/develop/app-objects-and-service-principals.md).  Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [informazioni sui diversi ruoli](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
È necessario autorizzare le richieste effettuate dall'entità di sicurezza (un utente o un'applicazione) per accedere a una risorsa di configurazione dell'app.  Con Azure AD, l'accesso a una risorsa è un processo in due passaggi.
1. L'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0.  Il nome della risorsa per richiedere un token è `https://login.microsoftonline.com/{tenantID}` in cui `{tenantID}` corrisponde all'ID tenant Azure Active Directory a cui appartiene l'entità servizio.
2. Il token viene passato come parte di una richiesta al servizio di configurazione dell'app per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2,0 in fase di esecuzione.  Se un'applicazione è in esecuzione in un'entità di Azure, ad esempio un'app funzioni di Azure, un'app Web di Azure o una macchina virtuale di Azure, può usare un'identità gestita per accedere alle risorse.  Per informazioni su come autenticare le richieste effettuate da un'identità gestita per la configurazione app Azure, vedere [autenticare l'accesso alle risorse di configurazione di app Azure con Azure Active Directory e identità gestite per le risorse di Azure](howto-integrate-azure-managed-service-identity.md).

Il passaggio di autorizzazione richiede che uno o più ruoli RBAC siano assegnati all'entità di sicurezza. App Azure configurazione fornisce ruoli RBAC che includono i set di autorizzazioni per le risorse di configurazione dell'app. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni fornite al server principale. Per ulteriori informazioni sui ruoli RBAC, vedere [ruoli RBAC predefiniti per la configurazione app Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Assegnare i ruoli RBAC per i diritti di accesso
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

Quando un ruolo RBAC viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'accesso è limitato alla risorsa di configurazione dell'app. Un'entità di sicurezza Azure AD può essere un utente o un'entità servizio dell'applicazione oppure un' [identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Ruoli RBAC predefiniti per la configurazione di app Azure
Azure fornisce i seguenti ruoli predefiniti di controllo degli accessi in base al ruolo per autorizzare l'accesso ai dati di configurazione dell'app tramite Azure AD e OAuth:

- Proprietario dei dati di configurazione app Azure: usare questo ruolo per concedere l'accesso in lettura/scrittura alle risorse di configurazione dell'app.
- Lettore dati di configurazione app Azure: usare questo ruolo per concedere l'accesso in lettura alle risorse di configurazione dell'app.
- Collaboratore: usare questo ruolo per concedere l'accesso amministrativo al servizio senza concedere l'accesso ai dati archiviati nell'istanza di configurazione dell'app.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'uso delle [identità gestite](howto-integrate-azure-managed-service-identity.md) per amministrare il servizio di configurazione dell'app.