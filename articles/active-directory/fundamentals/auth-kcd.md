---
title: Delega vincolata Kerberos con Azure Active Directory
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
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114466"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Autenticazione di Windows: delega vincolata Kerberos con Azure Active Directory

La delega vincolata Kerberos (delega vincolata Kerberos) fornisce la delega vincolata tra le risorse ed è basata sui nomi dei principi di servizio. È necessario che gli amministratori di dominio creino le delega ed è limitato a un singolo dominio. DELEGA vincolata Kerberos basato su risorse viene spesso usato per fornire l'autenticazione Kerberos per un'applicazione Web che dispone di utenti in più domini all'interno di una foresta Active Directory.

Azure Active Directory Application Proxy possibile fornire Single Sign-On (SSO) e l'accesso remoto alle applicazioni basate su delega vincolata Kerberos che richiedono un ticket Kerberos per l'accesso e la delega vincolata Kerberos (delega vincolata Kerberos).

Per abilitare l'accesso Single Sign-on alle applicazioni delega vincolata Kerberos locali che usano l'autenticazione integrata di Windows (IWA), è possibile assegnare ai connettori proxy di applicazione l'autorizzazione per rappresentare gli utenti in Active Directory. Il connettore del proxy di applicazione usa questa autorizzazione per inviare e ricevere token per conto dell'utente.

## <a name="use-when"></a>Casi di utilizzo

È necessario fornire l'accesso remoto, proteggere con la pre-autenticazione e fornire SSO alle applicazioni IWA locali.

![Diagramma dell'architettura](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: accede all'applicazione legacy gestita dal proxy di applicazione.

* **Web browser**: il componente con cui l'utente interagisce per accedere all'URL esterno dell'applicazione.

* **Azure ad**: autentica l'utente. 

* **Servizio proxy di applicazione**: funge da proxy inverso per inviare la richiesta dall'utente all'applicazione locale. Si trova in Azure AD. Il proxy di applicazione può inoltre applicare eventuali criteri di accesso condizionale.

* **Connettore del proxy di applicazione**: installato in locale nei server Windows per fornire la connettività all'applicazione. Restituisce la risposta a Azure AD. Esegue la negoziazione delega vincolata Kerberos con Active Directory, rappresentando l'utente per ottenere un token Kerberos per l'applicazione.

* **Active Directory**: Invia il token Kerberos per l'applicazione al connettore del proxy di applicazione.

* **Applicazioni legacy**: applicazioni che ricevono richieste utente dal proxy di applicazione. Le applicazioni legacy restituiscono la risposta al connettore del proxy di applicazione.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementare l'autenticazione di Windows (delega vincolata Kerberos) con Azure AD

* [Delega vincolata Kerberos per l'accesso Single Sign-On alle app con il proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
