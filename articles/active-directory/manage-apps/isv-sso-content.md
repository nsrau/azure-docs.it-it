---
title: Abilitare l'accesso SSO per l'applicazione multi-tenant
description: Linee guida per i fornitori di software indipendenti sull'integrazione con Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795171"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Abilitare Single Sign-On per l'applicazione multi-tenantEnable Single Sign-On for your multi-tenant application  

Quando si offre l'applicazione per l'utilizzo da parte di altre società tramite un acquisto o una sottoscrizione, l'applicazione viene resportata disponibile ai clienti all'interno dei propri tenant di Azure.When you offer your application for use by other companies through a purchase or subscription, you make your application available to customers within their own Azure tenants. Questa operazione è nota come creazione di un'applicazione multi-tenant. Per una panoramica di questo concetto, vedere [Applicazioni multi-tenant in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [Tenancy in Azure Active Directory.](../develop/single-and-multi-tenant-apps.md)

## <a name="what-is-single-sign-on"></a>Che cos'è Single Sign-On

Single Sign-On (SSO) aggiunge sicurezza e praticità quando gli utenti accedono alle applicazioni usando Azure Active Directory e altre identità. Quando un'applicazione è abilitata per SSO, gli utenti non devono immettere credenziali separate per accedere a tale applicazione. Per una spiegazione completa dell'accesso Single Sign-On. [Vedere Single Sign-On per le applicazioni in Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Perché abilitare Single Sign-On nell'applicazione?

L'abilitazione di SSO nell'applicazione multi-tenant offre numerosi vantaggi. Quando si abilita SSO per l'applicazione:

* Your application can be listed in the Azure Marketplace, where your app is discoverable by millions of organizations using Azure Active Directory.
  * Consente ai clienti di configurare rapidamente l'applicazione con Azure AD.

* L'applicazione può essere individuabile nella raccolta app di Office 365, nell'icona di avvio delle app di Office 365 e in Microsoft Search in Office.com

* L'applicazione può usare l'API REST di Microsoft Graph per accedere ai dati che determinano la produttività degli utenti disponibile in Microsoft Graph.

* Riduci i costi di supporto semplificando le tue esigenze per i tuoi clienti.
  * La documentazione specifica dell'applicazione è stata coprodotta con il team di Azure AD per i nostri clienti comuni semplifica l'adozione.
  * Se SSO con un clic è abilitato, gli amministratori IT dei clienti non devono imparare a configurare l'applicazione per l'utilizzo nell'organizzazione.

* Offri ai tuoi clienti la possibilità di gestire completamente l'autenticazione e l'autorizzazione delle identità dei dipendenti e degli ospiti.

  * Affidare a tali identità tutte le responsabilità di gestione e conformità degli account con il proprietario del cliente.

  * Possibilità di abilitare o disabilitare SSO per provider di identità, gruppi o utenti specifici per soddisfare le proprie esigenze aziendali.

* Aumentate la vostra commerciabilità e adottabilità. Molte organizzazioni di grandi dimensioni richiedono che (o aspirano a) i propri dipendenti hanno esperienze SSO senza soluzione di continuità in tutte le applicazioni. Rendere SSO facile è importante.

* Riduci l'attrito dell'utente finale, che può aumentare l'utilizzo dell'utente finale e aumentare le entrate.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Come abilitare Single Sign-On nell'applicazione pubblicata

1. [Scegliere il protocollo di federazione più adatto per l'applicazione multi-tenant.](isv-choose-multi-tenant-federation.md)
1. Implementare SSO nell'applicazioneImplement SSO in your application
   - Vedere le indicazioni sui modelli di [autenticazioneSee guidance on authentication patterns](../develop/v2-app-types.md)
   - Vedere Esempi di codice di Azure active Directory per i protocolli OIDC e OAuthSee [Azure active Directory code samples](../develop/sample-v2-code.md) for OIDC and OAuth protocols
1. [Creare il tenant di Azure](isv-tenant-multi-tenant-app.md) e testare l'applicazioneCreate your Azure Tenant and test your application
1. [Creare e pubblicare la documentazione SSO sul sito.](isv-create-sso-documentation.md)
1. [Inviare l'elenco delle applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e collaborare con Microsoft per creare la documentazione sul sito Microsoft.
1. [Iscriviti al Microsoft Partner Network (gratuito) e crea il tuo go to market plan](https://partner.microsoft.com/en-us/explore/commercial#gtm).
