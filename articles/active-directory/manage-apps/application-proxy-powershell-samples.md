---
title: Esempi di PowerShell per Azure AD Application Proxy
description: Usare questi esempi di PowerShell per Azure AD Application Proxy per ottenere informazioni sulle app e sui connettori di Application Proxy nella directory, assegnare utenti e gruppi alle app e ottenere informazioni sul certificato.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57efd88b9d194442920496107d4c37ba2baf94d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85554984"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Esempi di Azure AD PowerShell per Azure AD Application Proxy

La tabella seguente include collegamenti a esempi di script di Azure PowerShell per Azure AD Application Proxy. Per questi esempi è necessario il [modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), se non diversamente specificato.


Per altre informazioni sui cmdlet usati in questi esempi, vedere [Gestione delle applicazioni di Application Proxy](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) e [Gestione dei connettori di Application Proxy](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| Collegamento | Descrizione |
|---|---|
|**App di Application Proxy**||
| [Elenco delle informazioni di base per tutte le app di Application Proxy](scripts/powershell-get-all-app-proxy-apps-basic.md) | Contiene le informazioni di base (AppId, DisplayName, ObjId) su tutte le app di Application Proxy nella directory. |
| [Elenco di informazioni complete per tutte le app di Application Proxy](scripts/powershell-get-all-app-proxy-apps-extended.md) | Contiene informazioni complete (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) su tutte le app di Application Proxy nella directory.  |
| [Elenco di tutte le app di Application Proxy per gruppo di connettori](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Contiene informazioni su tutte le app di Application Proxy nella directory e sui gruppi di connettori a cui sono assegnate. |
| [Ottenere tutte le app di Application Proxy con criteri di durata dei token](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Elenca tutte le app di Application Proxy nella directory con criteri di durata dei token e relativi dettagli. Per questo esempio è necessaria la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Gruppi di connettori**||
| [Ottenere tutti i gruppi di connettori e i connettori nella directory](scripts/powershell-get-all-connectors.md) | Elenca tutti i gruppi di connettori e i connettori nella directory. |
| [Spostare tutte le app assegnate a un gruppo di connettori in un altro gruppo di connettori](scripts/powershell-move-all-apps-to-connector-group.md) | Sposta tutte le applicazioni attualmente assegnate a un gruppo di connettori in un gruppo di connettori diverso. |
|**Utenti e gruppi assegnati**||
| [Visualizzare utenti e gruppi assegnati a un'applicazione di Application Proxy](scripts/powershell-display-users-group-of-app.md) | Elenca gli utenti e i gruppi assegnati a una specifica applicazione di Application Proxy. |
| [Assegnare un utente a un'applicazione](scripts/powershell-assign-user-to-app.md) | Assegna un utente specifico a un'applicazione. |
| [Assegnare un gruppo a un'applicazione](scripts/powershell-assign-group-to-app.md) | Assegna un gruppo specifico a un'applicazione. |
|**Configurazione di URL esterni**||
| [Ottenere tutte le app di Application Proxy che usano domini predefiniti (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Elenca tutte le applicazioni di Application Proxy che usano domini predefiniti (.msappproxy.net). |
| [Ottenere tutte le app di Application Proxy che usano la pubblicazione con caratteri jolly](scripts/powershell-get-all-wildcard-apps.md) | Elenca tutte le app di Application Proxy che usano la pubblicazione con caratteri jolly. |
|**Configurazione di domini personalizzati**||
| [Ottenere tutte le app di Application Proxy che usano domini personalizzati e le informazioni sul certificato](scripts/powershell-get-all-custom-domains-and-certs.md) | Elenca tutte le app di Application Proxy che usano domini personalizzati e le informazioni sul certificato associate ai domini personalizzati. |
| [Ottenere tutte le app di Azure AD Application Proxy pubblicate senza un certificato caricato](scripts/powershell-get-all-custom-domain-no-cert.md) | Elenca tutte le app di Application Proxy che usano domini personalizzati ma non hanno un certificato TLS/SSL valido caricato. |
| [Ottenere tutte le app di Azure AD Application Proxy pubblicate con il certificato identico](scripts/powershell-get-custom-domain-identical-cert.md) | Elenca tutte le app di Azure AD Application Proxy pubblicate senza un certificato identico. |
| [Ottenere tutte le app di Azure AD Application Proxy pubblicate con il certificato identico e sostituirlo](scripts/powershell-get-custom-domain-replace-cert.md) | Per le app di Azure AD Application Proxy pubblicate con un certificato identico, consente di sostituire il certificato in blocco. |
