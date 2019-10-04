---
title: Abilitare l'accesso SSO per l'applicazione multi-tenant
description: Linee guida per fornitori di software indipendenti per l'integrazione con Azure active Directory
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
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795171"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Abilitare Single Sign-on per l'applicazione multi-tenant  

Quando offrire la tua applicazione per l'uso da altre società tramite un acquisto o una sottoscrizione, rendere l'applicazione disponibile per i clienti nel proprio tenant di Azure. Ciò è noto come la creazione di un'applicazione multi-tenant. Per una panoramica di questo concetto, vedere [le applicazioni multi-tenant in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [Tenancy in Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Che cos'è Single Sign-On

Accesso Single sign-on (SSO) aggiunge sicurezza e praticità quando gli utenti l'accesso alle applicazioni con Azure Active Directory e altre identità. Quando un'applicazione è abilitato l'accesso SSO, gli utenti non dovranno immettere le credenziali separate per accedere a tale applicazione. Per una spiegazione completa dell'accesso Single sign-on. [Vedere Single sign-on alle applicazioni in Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Il motivo per cui abilitare Single Sign-on nell'applicazione?

Esistono numerosi vantaggi all'attivazione dell'accesso SSO nell'applicazione multi-tenant. Quando si abilita l'accesso SSO per l'applicazione:

* È possibile elencare l'applicazione in Azure Marketplace, in cui l'app è individuabile da milioni di organizzazioni che usano Azure Active Directory.
  * Consente ai clienti di configurare rapidamente l'applicazione con Azure AD.

* L'applicazione può essere individuabile nella raccolta di App Office 365, l'avvio di App di Office 365 e all'interno di Microsoft Search su Office.com

* L'applicazione può usare l'API REST di Microsoft Graph per accedere ai dati che contribuiscono alla produttività utente reso disponibile da Microsoft Graph.

* Ridurre i costi di supporto, rendendo più semplice per i clienti.
  * La documentazione specifica dell'applicazione coproduced con il team di Azure AD per l'adozione facilita i clienti reciproci.
  * Se è abilitato l'accesso SSO con un clic, gli amministratori IT dei clienti autorizzati per informazioni su come configurare l'applicazione per l'uso all'interno dell'organizzazione.

* Offerto ai clienti la possibilità di gestire completamente dei dipendenti e guest le identità autenticazione e autorizzazione.

  * Inserimento di tutte le responsabilità di conformità e gestione account con il proprietario del cliente di tali identità.

  * Che fornisce capacità di abilitare o disabilitare l'accesso SSO per i provider di identità specifico, gruppi o utenti soddisfare le proprie esigenze aziendali.

* Aumentare la commerciabilità e adoptability. Molte grandi organizzazioni che richiedono (o mirare a) ai propri dipendenti abbiano un'esperienza SSO trasparente per tutte le applicazioni. Effettua l'accesso SSO facile è importante.

* È ridurre gli attriti degli utenti finali, che possono aumentare l'utilizzo dell'utente finale e incrementare i ricavi.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Come abilitare Single Sign-on nell'applicazione pubblicata

1. [Scegliere il protocollo di federazione corretto per l'applicazione multi-tenant](isv-choose-multi-tenant-federation.md).
1. Implementare l'accesso SSO nell'applicazione
   - Vedere [indicazioni su modelli di autenticazione](../develop/v2-app-types.md)
   - Visualizzare [esempi Azure active Directory codice](../develop/sample-v2-code.md) per protocolli OIDC e OAuth
1. [Creare il Tenant di Azure](isv-tenant-multi-tenant-app.md) e testare l'applicazione
1. [Creare e pubblicare nel sito di documentazione relativa a SSO](isv-create-sso-documentation.md).
1. [Invia l'inserzione dell'applicazione](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e dei partner con Microsoft per creare la documentazione nel sito Web Microsoft.
1. [Partecipare a Microsoft Partner Network (gratuito) e creare di Vai per commercializzare piano](https://partner.microsoft.com/en-us/explore/commercial#gtm).
