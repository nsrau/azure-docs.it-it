---
title: Autorizzazioni in Azure AD | Microsoft Docs
description: Informazioni sugli ambiti e sulle autorizzazioni in Azure Active Directory e su come usarli
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156892"
---
# <a name="permissions-in-azure-ad"></a>Autorizzazioni in Azure AD
Azure Active Directory (Azure AD) fa ampio uso delle autorizzazioni per i flussi OAuth e OpenID Connect (OIDC). Quando l'app riceve un token di accesso da Azure AD, include le attestazioni che descrivono le autorizzazioni (note anche come ambiti) dell'app in relazione a una particolare risorsa. Ciò semplifica l'autorizzazione per la risorsa perché è necessario solo controllare che il token contenga l'autorizzazione appropriata per l'API chiamata. 

## <a name="types-of-permissions"></a>Tipi di autorizzazioni
Azure AD definisce due tipi di autorizzazioni: 
* **Autorizzazioni delegate**: usati dalle app con un utente connesso. Per queste app l'utente o un amministratore fornisce il consenso per le autorizzazioni richieste dall'app e all'app viene delegata l'autorizzazione per agire per conto dell'utente connesso quando vengono effettuate chiamate a un'API. A seconda dell'API, l'utente potrebbe non essere in grado di fornire il consenso direttamente all'API e dovrà invece [chiedere a un amministratore di fornire il "consenso dell'amministratore"](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).
* **Autorizzazioni dell'applicazione**: usate dalle app che vengono eseguite senza un utente connesso, ad esempio le app eseguite come servizi in background o daemon. Le autorizzazioni dell'applicazione possono essere concesse solo tramite il [consenso di un amministratore](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant) perché sono in genere molto potenti e consentono l'accesso ai dati di diversi utenti o a dati a cui altrimenti potrebbero accedere solo gli amministratori. 

Le autorizzazioni valide sono le autorizzazioni che l'app avrà quando effettuerà le richieste a un'API. 

* Per le autorizzazioni delegate, le autorizzazioni valide dell'app sono costituite dall'intersezione con meno privilegi tra le autorizzazioni delegate concesse all'app (tramite il consenso) e i privilegi dell'utente attualmente connesso. L'app non può mai avere più privilegi rispetto all'utente connesso. All'interno delle organizzazioni, i privilegi dell'utente connesso possono essere determinati da criteri o dall'appartenenza a uno o più ruoli di amministratore. Per altre informazioni sui ruoli di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md).
    Si supponga, ad esempio, che all'app sia stata concessa l'autorizzazione delegata `User.ReadWrite.All` in Microsoft Graph. Questa autorizzazione concede nominalmente all'app l'autorizzazione per leggere e aggiornare il profilo di ogni utente in un'organizzazione. Se l'utente connesso è un amministratore globale, l'app sarà in grado di aggiornare il profilo di tutti gli utenti dell'organizzazione. Se tuttavia l'utente connesso non ha un ruolo di amministratore, l'app sarà in grado di aggiornare solo il profilo dell'utente connesso. Non sarà in grado di aggiornare i profili di altri utenti nell'organizzazione, perché l'utente per conto del quale è autorizzata ad agire non dispone di tali privilegi.
* Per le autorizzazioni dell'applicazione, le autorizzazioni valide dell'app corrispondono al livello completo di privilegi impliciti nell'autorizzazione. Un'app che ha l'autorizzazione dell'applicazione `User.ReadWrite.All` può ad esempio aggiornare il profilo di tutti gli utenti dell'organizzazione. 

## <a name="permission-attributes"></a>Attributi delle autorizzazioni
Le autorizzazioni in Azure AD hanno diverse proprietà che aiutano utenti, amministratori o sviluppatori di app a prendere decisioni informate sugli elementi a cui l'autorizzazione concede l'accesso. 

> [!NOTE]
> È possibile visualizzare le autorizzazioni esposte da un'entità servizio o un'applicazione Azure AD usando il portale di Azure o PowerShell. Provare a usare questo script per visualizzare le autorizzazioni esposte da Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nome proprietà | DESCRIZIONE | Esempio | 
| --- | --- | --- |
| ID | Valore GUID che identifica in modo univoco l'autorizzazione. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | Indica se l'ambito è disponibile per l'uso. | true | 
| type | Indica se l'autorizzazione richiede il consenso dell'utente o dell'amministratore. | Utente | 
| AdminConsentDescription | Descrizione che viene visualizzata per gli amministratori durante l'esperienza di consenso dell'amministratore. | Consente all'app di leggere i messaggi di posta elettronica nelle cassette postali degli utenti. | 
| AdminConsentDisplayName | Nome descrittivo visualizzato per gli amministratori durante l'esperienza di consenso dell'amministratore. | Leggi i messaggi di un altro utente | 
| UserConsentDescription | Descrizione che viene visualizzata per gli utenti durante l'esperienza di consenso dell'utente. |  Consente all'app di leggere i messaggi di posta elettronica nella cassetta postale personale. | 
| UserConsentDisplayName | Nome descrittivo visualizzato per gli utenti durante l'esperienza di consenso dell'utente. | Lettura dei messaggi | 
| Valore | Stringa usata per identificare l'autorizzazione durante i flussi di autorizzazione OAuth 2.0. Questa stringa può anche essere combinata con la stringa dell'URI ID app per formare un nome di autorizzazione completo. | `Mail.Read` | 

## <a name="types-of-consent"></a>Tipi di consenso
Le applicazioni in Azure AD si basano sul consenso per ottenere l'accesso alle API o alle risorse necessarie. Ci sono diversi tipi di consenso che l'app deve riconoscere per funzionare correttamente. Se si definiscono le autorizzazioni, è anche necessario comprendere in che modo gli utenti potranno accedere all'app o all'API.

* **Consenso dell'utente statico**: si verifica automaticamente durante il [flusso di autorizzazione OAuth 2.0](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) quando si specifica la risorsa con cui l'app vuole interagire. Nello scenario di consenso dell'utente statico, l'app deve avere già specificato tutte le autorizzazioni necessarie nella configurazione dell'app nel portale di Azure. Se l'utente (o l'amministratore, a seconda dei casi) non ha fornito il consenso per l'app, Azure AD chiederà all'utente di fornire il consenso in questo momento. 

    Leggere altre informazioni sulla registrazione di un'app Azure AD che richiede l'accesso a un set statico di API.
* **Consenso dell'utente dinamico**: si tratta di una funzionalità del modello di app Azure AD v2. In questo scenario l'app richiede un set di ambiti necessari nel [flusso di autorizzazione OAuth 2.0 per le app v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Se l'utente non ha già fornito il consenso, verrà chiesto di fornirlo in questo momento. [Altre informazioni sul consenso dinamico](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > Il consenso dinamico può risultare comodo, ma presenta un'importante sfida per le autorizzazioni che richiedono il consenso dell'amministratore, perché nell'esperienza di consenso dell'amministratore tali autorizzazioni non sono note al momento del consenso. Se sono necessari ambiti con privilegi di amministratore, l'app deve registrarli nel portale di Azure.
  
* **Consenso dell'amministratore**: è necessario quando l'app richiede l'accesso a determinate autorizzazioni con privilegi elevati. Ciò garantisce che gli amministratori dispongano di ulteriori controlli prima di autorizzare le app o gli utenti ad accedere a dati dell'organizzazione che richiedono privilegi elevati. [Altre informazioni su come fornire il consenso dell'amministratore](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Procedure consigliate

### <a name="resource-best-practices"></a>Procedure consigliate per le risorse
Le risorse che espongono le API devono definire autorizzazioni specifiche dei dati o delle azioni che stanno proteggendo. Ciò aiuta a garantire che i client non ottengano autorizzazioni di accesso ai dati non necessarie e che gli utenti siano ben informati in merito ai dati per i quali forniscono il consenso.

Le risorse devono definire esplicitamente le autorizzazioni `Read` e `ReadWrite` separatamente. 

Le risorse devono contrassegnare le autorizzazioni che consentono l'accesso ai dati di più utenti come autorizzazioni `Admin`. 

Le risorse devono seguire il modello di denominazione seguente `Subject.Permission[.Modifier]`, dove `Subject` corrisponde al tipo di dati disponibili, `Permission` corrisponde all'azione che un utente può eseguire su tali dati e `Modifier` è un modificatore facoltativo che consente di descrivere le specializzazioni di un'altra autorizzazione. Ad esempio:  
* Mail.Read: consente agli utenti di leggere i messaggi di posta. 
* Mail.ReadWrite: consente agli utenti di leggere o scrivere messaggi di posta.
* Mail.ReadWrite.All: consente a un amministratore o a un utente di accedere a tutti i messaggi di posta nell'organizzazione.

### <a name="client-best-practices"></a>Procedure consigliate per il client
Richiedere solo le autorizzazioni per gli ambiti necessari per l'app. Le app con un numero eccessivo di autorizzazioni potrebbero comportare l'esposizione dei dati utente, se vengono compromesse.

I client non devono richiedere autorizzazioni dell'applicazione e autorizzazioni delegate alla stessa app. Ciò può comportare l'elevazione dei privilegi e consentire a un utente di accedere a dati a cui non potrebbe accedere con le sue autorizzazioni. 




