---
title: Autorizzare l'accesso alla configurazione delle app di Azure usando Azure Active DirectoryAuthorize access to Azure App Configuration using Azure Active Directory
description: Abilitare il controllo degli accessi in base al ruolo per autorizzare l'accesso all'istanza di Configurazione app di AzureEnable RBAC to authorize access to your Azure App
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472622"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizzare l'accesso alla configurazione delle app di Azure usando Azure Active DirectoryAuthorize access to Azure App Configuration using Azure Active Directory
Configurazione app di Azure supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle istanze di configurazione delle app.  Azure AD consente di usare il controllo degli accessi in base al ruolo per concedere autorizzazioni a un'entità di sicurezza.  Un'entità di sicurezza può essere un utente o [un'entità servizio dell'applicazione.](../active-directory/develop/app-objects-and-service-principals.md)  Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [Informazioni sui diversi ruoli](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
Le richieste effettuate dall'entità di sicurezza (un utente o un'applicazione) per accedere a una risorsa di configurazione app devono essere autorizzate.  Con Azure AD, l'accesso a una risorsa è un processo in due passaggi.
1. L'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2.0.The security principal's identity is authenticated and an OAuth 2.0 token is returned.  Il nome della risorsa `https://login.microsoftonline.com/{tenantID}` per `{tenantID}` richiedere un token è dove corrisponde all'ID tenant di Azure Active Directory a cui appartiene l'entità servizio.
2. Il token viene passato come parte di una richiesta al servizio di configurazione app per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione.  Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio un'app Funzioni di Azure, un'app Web di Azure o una macchina virtuale di Azure, può usare un'identità gestita per accedere alle risorse.  Per informazioni su come autenticare le richieste effettuate da un'identità gestita alla configurazione delle app di Azure, vedere Autenticare l'accesso alle risorse di Configurazione app di Azure con Azure Active Directory e le identità gestite per le risorse di Azure.To learn how to authenticate requests made by a managed identity to Azure App Configuration, see [Authenticate access to Azure App Configuration resources with Azure Active Directory and managed identities for Azure Resources.](howto-integrate-azure-managed-service-identity.md)

Il passaggio di autorizzazione richiede l'assegnazione di uno o più ruoli RBAC all'entità di sicurezza. Configurazione app di Azure offre ruoli RBAC che includono set di autorizzazioni per le risorse di configurazione delle app. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni fornite all'entità. Per altre informazioni sui ruoli RBAC, vedere Ruoli RBAC incorporati per la configurazione delle app di Azure.For more information about RBAC roles, see [Built-in RBAC roles for Azure App Configuration.](#built-in-rbac-roles-for-azure-app-configuration) 

## <a name="assign-rbac-roles-for-access-rights"></a>Assegnare ruoli RBAC per i diritti di accessoAssign RBAC roles for access rights
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. L'ambito dell'accesso è la risorsa Configurazione app. Un'entità di sicurezza di Azure AD può essere un utente, un'entità servizio dell'applicazione o [un'identità gestita per](../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security principal may be a user, or an application service principal, or a managed identity for Azure resources.

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Ruoli RBAC incorporati per la configurazione delle app di AzureBuilt-in RBAC roles for Azure App Configuration
Azure provides the following built-in RBAC roles for authorizing access to App Configuration data using Azure AD and OAuth:

- Proprietario dei dati di configurazione dell'app Azure: usare questo ruolo per concedere l'accesso in lettura/scrittura alle risorse di configurazione dell'app.
- Lettore dati di configurazione app di Azure: usare questo ruolo per concedere l'accesso in lettura alle risorse di configurazione delle app.
- Collaboratore: usare questo ruolo per concedere l'accesso di amministratore al servizio senza concedere l'accesso ai dati archiviati nell'istanza di Configurazione app.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'uso delle [identità gestite](howto-integrate-azure-managed-service-identity.md) per amministrare il servizio di configurazione app.