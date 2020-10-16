---
title: Autenticazione OpenID Connect con Azure Active Directory
description: Indicazioni sull'architettura per ottenere questo modello di autenticazione
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f2ad3b5e86eebfc2d6f1f42f8a2ab0520144b5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114205"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Autenticazione OpenID Connect con Azure Active Directory

OpenID Connect (OIDC) è un protocollo di autenticazione basato sul protocollo OAuth2, usato per l'autorizzazione. OIDC usa i flussi di messaggi standardizzati da OAuth2 per fornire i servizi di identità. 

L'obiettivo di progettazione di OIDC è "rendere semplici e complesse le cose possibili". OIDC consente agli sviluppatori di autenticare gli utenti tra siti Web e app senza dover possedere e gestire i file delle password. Questo consente al generatore di app di verificare in modo sicuro l'identità della persona che attualmente usa il browser o l'app nativa connessa all'applicazione.

L'autenticazione dell'utente deve essere eseguita presso un provider di identità in cui verranno controllate la sessione o le credenziali dell'utente. A tale scopo, è necessario un agente attendibile. Le app native avviano in genere il browser di sistema a tale scopo. Le visualizzazioni incorporate sono considerate non attendibili perché non c'è niente da impedire all'app di snooping sulla password utente. 

Oltre all'autenticazione, è possibile richiedere il consenso dell'utente. Il consenso è l'autorizzazione esplicita dell'utente per consentire a un'applicazione di accedere alle risorse protette. Il consenso è diverso dall'autenticazione perché il consenso deve essere fornito una sola volta per una risorsa. Il consenso rimane valido fino a quando l'utente o l'amministratore revoca manualmente la concessione. 

## <a name="use-when"></a>Casi di utilizzo

È necessario il consenso dell'utente e per l'accesso Web.

![diagramma dell'architettura](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: richiede un servizio dall'applicazione.

* **Agent attendibile**: il componente con cui l'utente interagisce. Questo agente attendibile è in genere un Web browser.

* **Applicazione**: l'applicazione o il server di risorse è il percorso in cui risiedono la risorsa o i dati. Considera attendibile il provider di identità per autenticare e autorizzare l'agente attendibile in modo sicuro. 

* **Azure ad**: il provider OIDC, noto anche come provider di identità, gestisce in modo sicuro qualsiasi operazione eseguita con le informazioni dell'utente, l'accesso e le relazioni di trust tra le entità in un flusso. Autentica l'identità dell'utente, concede e revoca l'accesso alle risorse e rilascia i token. 

## <a name="implement-oidc-with-azure-ad"></a>Implementare OIDC con Azure AD

* [Integrazione di applicazioni con Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Protocolli OAuth 2,0 e OpenID Connect nella piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Microsoft Identity Platform e protocollo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [Accesso Web con OpenID Connect in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [Proteggere l'applicazione usando OpenID Connect e Azure AD](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
