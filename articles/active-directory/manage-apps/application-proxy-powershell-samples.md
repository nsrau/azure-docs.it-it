---
title: Esempi di PowerShell per Azure AD proxy di applicazione
description: Usare questi esempi di PowerShell per Azure AD proxy di applicazione per ottenere informazioni sulle app e sui connettori del proxy di applicazione nella directory, assegnare utenti e gruppi alle app e ottenere informazioni sul certificato.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25097f8b72c7c8dde742701ce576c1b0e2fbabd2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480944"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Esempi di Azure AD PowerShell per Azure AD proxy di applicazione

La tabella seguente include collegamenti a esempi di script di PowerShell per Azure AD proxy di applicazione. Questi esempi richiedono il [modulo PowerShell AzureAD V2 per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) o la [versione di anteprima AzureAD V2 per il modulo Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), salvo diversa indicazione.


Per ulteriori informazioni sui cmdlet utilizzati in questi esempi, vedere Gestione [delle applicazioni proxy](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) di applicazione e [gestione del connettore del proxy di applicazione](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**App del proxy di applicazione**||
| [Elenca le informazioni di base per tutte le app del proxy di applicazione](scripts/powershell-get-all-app-proxy-apps-basic.md) | Elenca le informazioni di base (AppId, DisplayName, ObjId) su tutte le app proxy di applicazione nella directory. |
| [Elenca le informazioni estese per tutte le app del proxy di applicazione](scripts/powershell-get-all-app-proxy-apps-extended.md) | Elenca le informazioni estese (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) su tutte le app proxy di applicazione nella directory.  |
| [Elencare tutte le app proxy di applicazione per gruppo di connettori](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Elenca le informazioni su tutte le app proxy di applicazione nella directory e i gruppi di connettori a cui sono assegnate le app. |
| [Ottenere tutte le app proxy di applicazione con criteri per la durata dei token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Elenca tutte le app proxy di applicazione nella directory con i criteri per la durata dei token e i relativi dettagli. Questo esempio richiede la [versione di anteprima di AzureAD V2 PowerShell per il modulo Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Gruppi di connettori**||
| [Ottenere tutti i connettori e i gruppi del connettore nella directory](scripts/powershell-get-all-connectors.md) | Elenca tutti i gruppi e i connettori del connettore presenti nella directory. |
| [Spostare tutte le app assegnate a un gruppo di connettori a un altro gruppo di connettori](scripts/powershell-move-all-apps-to-connector-group.md) | Sposta tutte le applicazioni attualmente assegnate a un gruppo di connettori a un gruppo di connettori diverso. |
|**Utenti e gruppi assegnati**||
| [Visualizzare gli utenti e i gruppi assegnati a un'applicazione proxy di applicazione](scripts/powershell-display-users-group-of-app.md) | Elenca gli utenti e i gruppi assegnati a un'applicazione proxy di applicazione specifica. |
| [Assegnare un utente a un'applicazione](scripts/powershell-assign-user-to-app.md) | Assegna un utente specifico a un'applicazione. |
| [Assegnare un gruppo a un'applicazione](scripts/powershell-assign-group-to-app.md) | Assegna un gruppo specifico a un'applicazione. |
|**Configurazione URL esterno**||
| [Ottenere tutte le app proxy di applicazione usando i domini predefiniti (msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Elenca tutte le applicazioni proxy di applicazione che utilizzano domini predefiniti (msappproxy.net). |
| [Ottenere tutte le app proxy di applicazione usando la pubblicazione con caratteri jolly](scripts/powershell-get-all-wildcard-apps.md) | Elenca tutte le app proxy di applicazione usando la pubblicazione con caratteri jolly. |
|**Configurazione del dominio personalizzato**||
| [Ottenere tutte le app proxy di applicazione usando domini personalizzati e informazioni sui certificati](scripts/powershell-get-all-custom-domains-and-certs.md) | Elenca tutte le app proxy di applicazione che usano domini personalizzati e le informazioni del certificato associate ai domini personalizzati. |
| [Ottenere tutte le app dell'applicazione proxy Azure AD pubblicate senza certificato caricato](scripts/powershell-get-all-custom-domain-no-cert.md) | Elenca tutte le app proxy di applicazione che usano domini personalizzati ma non è stato caricato un certificato SSL valido. |
| [Ottenere tutte le app dell'applicazione proxy Azure AD pubblicate con il certificato identico](scripts/powershell-get-custom-domain-identical-cert.md) | Elenca tutte le app dell'applicazione proxy Azure AD pubblicate con lo stesso certificato. |
| [Ottenere tutte le app dell'applicazione proxy Azure AD pubblicate con il certificato identico e sostituirlo](scripts/powershell-get-custom-domain-replace-cert.md) | Per Azure AD applicazioni proxy pubblicate con un certificato identico, consente di sostituire il certificato in blocco. |
