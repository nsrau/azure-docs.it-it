---
title: Autenticazione OAUTH 2,0 con Azure Active Directory
description: Indicazioni sull'architettura per ottenere l'autenticazione OAUTH 2,0 con Azure Active Directory.
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
ms.openlocfilehash: daf40a2ced3f753619e9c4723dbe78cd7e51ff21
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577907"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Autenticazione OAuth 2,0 con Azure Active Directory

OAuth 2,0 è il protocollo di settore per l'autorizzazione. Consente a un utente di concedere l'accesso limitato alle risorse protette. Progettato per funzionare in modo specifico con Hypertext Transfer Protocol (HTTP), OAuth separa il ruolo del client dal proprietario della risorsa. Il client richiede l'accesso alle risorse controllate dal proprietario della risorsa e ospitate dal server di risorse. Il server delle risorse rilascia token di accesso con l'approvazione del proprietario della risorsa. Il client usa i token di accesso per accedere alle risorse protette ospitate dal server di risorse. 

OAuth 2,0 è direttamente correlato a OpenID Connect (OIDC). Poiché OIDC è un livello di autenticazione e autorizzazione basato su OAuth 2,0, non è compatibile con le versioni precedenti di OAuth 1,0. Azure Active Directory (Azure AD) supporta tutti i flussi OAuth 2,0. 

## <a name="use-when"></a>Utilizzare quando:

Per rich client & scenari di app moderne e accesso all'API Web RESTful.

![Diagramma dell'architettura](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente** : richiede un servizio dall'applicazione Web (app). L'utente è in genere il proprietario della risorsa che possiede i dati e ha la possibilità di consentire ai client di accedere ai dati o alla risorsa. 

* **Web browser** : il Web browser con cui l'utente interagisce è il client OAuth. 

* **App Web** : l'app Web o il server di risorse è il percorso in cui risiedono la risorsa o i dati. Considera attendibile il server di autorizzazione per autenticare e autorizzare il client OAuth in modo sicuro. 

* **Azure ad** : Azure ad è il server di autorizzazione, noto anche come provider di identità (IDP). Gestisce in modo sicuro qualsiasi operazione eseguita con le informazioni dell'utente, l'accesso e la relazione di trust. È responsabile dell'emissione dei token che concedono e revocano l'accesso alle risorse.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementare OAuth 2,0 con Azure AD

* [Integrazione di applicazioni con Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Protocolli OAuth 2,0 e OpenID Connect nella piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Tipi di applicazioni e OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
