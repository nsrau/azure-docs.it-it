---
title: Autenticazione basata su password con Azure Active Directory
description: Indicazioni sull'architettura per ottenere l'autenticazione basata su password con Azure Active Directory.
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
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172823"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Autenticazione basata su password con Azure Active Directory

Single Sign-On (SSO) basato su password usa il processo di autenticazione esistente per l'applicazione. Quando si Abilita l'accesso Single Sign-on basato su password, Azure Active Directory (Azure AD) raccoglie, crittografa e archivia in modo sicuro le credenziali utente nella directory. Azure AD fornisce il nome utente e la password all'applicazione quando l'utente tenta di eseguire l'accesso.

Scegliere SSO basato su password quando un'applicazione esegue l'autenticazione con un nome utente e una password anziché con intestazioni e token di accesso. SSO basato su password supporta qualsiasi applicazione basata su cloud con una pagina di accesso basata su HTML. 

## <a name="use-when"></a>Casi di utilizzo

È necessario proteggere con la preautenticazione e fornire SSO tramite l'insieme di credenziali delle password per le app Web.

![diagramma dell'architettura](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: accede a un'applicazione basata su formato da app personali o visitando direttamente il sito. 

* **Web browser**: il componente con cui l'utente interagisce per accedere all'URL esterno dell'applicazione. L'utente accede all'applicazione basata su form tramite l'estensione app. 

* **Estensione app**: identifica l'applicazione SSO basata su password configurata e fornisce le credenziali al modulo di accesso. L'estensione app è installata nel Web browser. 

* **Azure ad**: autentica l'utente.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementare SSO basato su password con Azure AD

* [Informazioni su SSO basato su password](../manage-apps/what-is-single-sign-on.md) 

* [Configurare l'accesso SSO basato su password per le applicazioni cloud ](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [Configurare l'accesso Single Sign-on basato su password per le applicazioni locali con il proxy di applicazione](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

