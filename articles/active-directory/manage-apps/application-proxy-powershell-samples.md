---
title: PowerShell samples for Azure AD Application Proxy
description: Usare questi esempi di PowerShell per il proxy di applicazione di Azure AD per ottenere informazioni sulle app e sui connettori del proxy di applicazione nella directory, assegnare utenti e gruppi alle app e ottenere informazioni sui certificati.
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481263"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD PowerShell examples for Azure AD Application Proxy

La tabella seguente include collegamenti a esempi di script di PowerShell per il proxy di applicazione di Azure AD. Questi esempi richiedono il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) o la versione di anteprima del modulo [AzureAD V2 per Graph,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)se non diversamente specificato.


Per ulteriori informazioni sui cmdlet utilizzati in questi esempi, vedere [Application Proxy Application Application Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) e Application Proxy Connector [Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**App proxy di applicazione**||
| [Elencare le informazioni di base per tutte le app del proxy di applicazione](scripts/powershell-get-all-app-proxy-apps-basic.md) | Elenca le informazioni di base (AppId, DisplayName, ObjId) su tutte le app proxy di applicazione nella directory. |
| [Elencare le informazioni estese per tutte le app proxy di applicazione](scripts/powershell-get-all-app-proxy-apps-extended.md) | Elenca le informazioni estese (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) su tutte le app proxy di applicazione nella directory.  |
| [Elenca tutte le app proxy di applicazione per gruppo di connettori](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Elenca informazioni su tutte le app proxy di applicazione nella directory e sui gruppi di connettori a cui sono assegnate. |
| [Ottenere tutte le app proxy di applicazione con criteri di durata dei tokenGet all Application Proxy apps with a token lifetime policy](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Elenca tutte le app proxy di applicazione nella directory con un criterio di durata del token e i relativi dettagli. Questo esempio richiede la versione di anteprima del [modulo AzureAD V2 PowerShell per Graph.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) |
|**Gruppi di connettori**||
| [Ottenere tutti i gruppi di connettori e i connettori nella directoryGet all connector groups and connectors in the directory](scripts/powershell-get-all-connectors.md) | Elenca tutti i gruppi di connettori e i connettori nella directory. |
| [Spostare tutte le app assegnate a un gruppo di connettori in un altro gruppo di connettori](scripts/powershell-move-all-apps-to-connector-group.md) | Sposta tutte le applicazioni attualmente assegnate a un gruppo di connettori in un gruppo di connettori diverso. |
|**Utenti e gruppi assegnati**||
| [Visualizzare utenti e gruppi assegnati a un'applicazione proxy di applicazione](scripts/powershell-display-users-group-of-app.md) | Elenca gli utenti e i gruppi assegnati a un'applicazione proxy di applicazione specifica. |
| [Assegnare un utente a un'applicazioneAssign a user to an application](scripts/powershell-assign-user-to-app.md) | Assegna un utente specifico a un'applicazione. |
| [Assegnare un gruppo a un'applicazioneAssign a group to an application](scripts/powershell-assign-group-to-app.md) | Assegna un gruppo specifico a un'applicazione. |
|**Configurazione URL esterno**||
| [Ottenere tutte le app proxy di applicazione usando domini predefiniti (con estensione msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Elenca tutte le applicazioni proxy di applicazione che utilizzano domini predefiniti (msappproxy.net). |
| [Ottenere tutte le app proxy di applicazione usando la pubblicazione con caratteri jollyGet all Application Proxy apps using wildcard publishing](scripts/powershell-get-all-wildcard-apps.md) | Elenca tutte le app proxy di applicazione utilizzando la pubblicazione con caratteri jolly. |
|**Configurazione del dominio personalizzato**||
| [Ottenere tutte le app proxy di applicazione usando domini personalizzati e informazioni sui certificatiGet all Application Proxy apps using custom domains and certificate information](scripts/powershell-get-all-custom-domains-and-certs.md) | Elenca tutte le app proxy di applicazione che utilizzano domini personalizzati e le informazioni sul certificato associate ai domini personalizzati. |
| [Ottenere tutte le app dell'applicazione proxy di Azure AD pubblicate senza alcun certificato caricatoGet all Azure AD Proxy application apps published with no certificate uploaded](scripts/powershell-get-all-custom-domain-no-cert.md) | Elenca tutte le app proxy di applicazione che utilizzano domini personalizzati ma che non dispongono di un certificato TLS/SSL valido caricato. |
| [Ottenere tutte le app dell'applicazione proxy di Azure AD pubblicate con lo stesso certificatoGet all Azure AD Proxy application apps published with the identical certificate](scripts/powershell-get-custom-domain-identical-cert.md) | Elenca tutte le app dell'applicazione proxy di Azure AD pubblicate con lo stesso certificato. |
| [Pubblicare tutte le app dell'applicazione proxy di Azure AD con il certificato identico e sostituirlo](scripts/powershell-get-custom-domain-replace-cert.md) | Per le app dell'applicazione proxy di Azure AD pubblicate con un certificato identico, è possibile sostituire il certificato in blocco. |
