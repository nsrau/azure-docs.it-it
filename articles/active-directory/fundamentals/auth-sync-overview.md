---
title: Panoramica del protocollo di sincronizzazione e autenticazione Azure Active Directory
description: Indicazioni sull'architettura per l'integrazione di Azure AD con i protocolli di autenticazione legacy e i modelli di sincronizzazione
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441199"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Integrazione di Azure Active Directory con protocolli di autenticazione e di sincronizzazione

Microsoft Azure Active Directory (Azure AD) consente l'integrazione con molti protocolli di autenticazione e sincronizzazione. Le integrazioni di autenticazione consentono di usare Azure AD e le relative funzionalità di sicurezza e gestione con modifiche minime o nulle per le applicazioni che usano metodi di autenticazione legacy. Le integrazioni di sincronizzazione consentono di sincronizzare i dati di utenti e gruppi per Azure AD e quindi le funzionalità di gestione Azure AD degli utenti. Alcuni modelli di sincronizzazione abilitano anche il provisioning automatizzato.

## <a name="legacy-authentication-protocols"></a>Protocolli di autenticazione legacy

Nella tabella seguente viene illustrata l'integrazione di Azure AD di autenticazione con i protocolli di autenticazione legacy e le relative funzionalità. Selezionare il nome di un protocollo di autenticazione per vedere

* Descrizione dettagliata

* Scenari di utilizzo

* Diagramma dell'architettura

* Spiegazione dei componenti di sistema

* Collegamenti per la modalità di implementazione dell'integrazione

 

| Protocollo di autenticazione| Autenticazione| Autorizzazione| Multi-Factor Authentication| Accesso condizionale |
| - |- | - | - | - |
| [Autenticazione basata su intestazione](auth-header-based.md)|![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Autenticazione LDAP](auth-ldap.md)| ![segno di spunta](./media/authentication-patterns/check.png)| | |  |
| [Autenticazione OAuth 2.0](auth-oauth2.md)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Autenticazione OIDC](auth-oidc.md)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Autenticazione SSO basata su password](auth-password-based-sso.md )| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Autenticazione RADIUS]( auth-radius.md)| ![segno di spunta](./media/authentication-patterns/check.png)| | ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Servizi del Gateway Desktop remoto](auth-remote-desktop-gateway.md)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Secure Shell (SSH)](auth-ssh.md) |  ![segno di spunta](./media/authentication-patterns/check.png)| | ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Autenticazione SAML](auth-saml.md)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |
| [Autenticazione di Windows-delega vincolata Kerberos](auth-kcd.md)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Modelli di sincronizzazione

La tabella seguente presenta Azure AD integrazione con i modelli di sincronizzazione e le relative funzionalità. Selezionare il nome di un modello da visualizzare

* Descrizione dettagliata

* Scenari di utilizzo

* Diagramma dell'architettura

* Spiegazione dei componenti di sistema

* Collegamenti per la modalità di implementazione dell'integrazione



| Modello di sincronizzazione| Sincronizzazione delle directory| Provisioning utenti |
| - | - | - |
| [Sincronizzazione delle directory](sync-directory.md)| ![segno di spunta](./media/authentication-patterns/check.png)|  |
| [Sincronizzazione LDAP](sync-ldap.md)| ![segno di spunta](./media/authentication-patterns/check.png)|  |
| [Sincronizzazione SCIM](sync-scim.md)| ![segno di spunta](./media/authentication-patterns/check.png)| ![segno di spunta](./media/authentication-patterns/check.png) |

