---
title: Abilitare l'accesso SSO per l'applicazione multi-tenant
description: Linee guida per i fornitori di software indipendenti per l'integrazione con Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763279"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Abilitare l'accesso Single Sign-on per l'applicazione multi-tenant  

Quando si offre un'applicazione per l'uso da parte di altre aziende tramite un acquisto o una sottoscrizione, l'applicazione viene reso disponibile ai clienti all'interno dei propri tenant di Azure. Questa operazione è nota come creazione di un'applicazione multi-tenant. Per una panoramica di questo concetto, vedere [applicazioni multi-tenant in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [tenant in Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Che cos'è Single Sign-on

Single Sign-on (SSO) consente di aggiungere sicurezza e praticità quando gli utenti effettuano l'accesso alle applicazioni usando Azure Active Directory e altre identità. Quando un'applicazione è abilitata per l'accesso SSO, gli utenti non devono immettere credenziali separate per accedere a tale applicazione. Per una spiegazione completa di Single Sign-on. [Vedere Single Sign-on to Applications in Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Perché abilitare l'accesso Single Sign-on nell'applicazione?

L'abilitazione dell'accesso SSO nell'applicazione multi-tenant presenta molti vantaggi. Quando si Abilita l'accesso SSO per l'applicazione:

* L'applicazione può essere elencata in Azure Marketplace, in cui l'app è individuabile da milioni di organizzazioni che usano Azure Active Directory.
  * Consente ai clienti di configurare rapidamente l'applicazione con Azure AD.

* L'applicazione può essere individuata nella raccolta di app di Office 365, nell'utilità di avvio delle app di Office 365 e in Microsoft Search in Office.com

* L'applicazione può usare l'API REST di Microsoft Graph per accedere ai dati che determinano la produttività degli utenti disponibile dalla Microsoft Graph.

* È possibile ridurre i costi di supporto rendendoli più semplici per i clienti.
  * La documentazione specifica dell'applicazione coprodotta con il team di Azure AD per i clienti reciproci semplifica l'adozione.
  * Se SSO con un clic è abilitato, gli amministratori IT dei clienti non devono apprendere come configurare l'applicazione per l'uso nell'organizzazione.

* Si fornisce ai clienti la possibilità di gestire completamente l'autenticazione e l'autorizzazione delle identità dei dipendenti e dei guest.

  * Inserimento di tutte le responsabilità di gestione e conformità degli account con il proprietario del cliente di tali identità.

  * Possibilità di abilitare o disabilitare l'accesso SSO per provider di identità, gruppi o utenti specifici per soddisfare le esigenze aziendali.

* Si aumenta la commercializzazione e l'adozione. Molte organizzazioni di grandi dimensioni richiedono che (o aspirano) i dipendenti abbiano esperienze SSO senza problemi in tutte le applicazioni. Semplificare l'accesso SSO è importante.

* Si riduce l'attrito degli utenti finali, che può aumentare l'utilizzo degli utenti finali e aumentare i ricavi.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Come abilitare l'accesso Single Sign-on nell'applicazione pubblicata

1. [Scegliere il protocollo federativo appropriato per l'applicazione multi-tenant](isv-choose-multi-tenant-federation.md).
1. Implementare SSO nell'applicazione
   - Vedere [linee guida sui modelli di autenticazione](../develop/v2-app-types.md)
   - Vedere gli [esempi di codice di Azure Active Directory](../develop/sample-v2-code.md) per i protocolli OIDC e OAuth
1. [Creare il tenant di Azure](isv-tenant-multi-tenant-app.md) e testare l'applicazione
1. [Creare e pubblicare la documentazione SSO sul sito](isv-create-sso-documentation.md).
1. [Inviare l'elenco di applicazioni e il](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) partner con Microsoft per creare la documentazione sul sito di Microsoft.
1. [Unisciti alla Microsoft Partner Network (gratuita) e crea il tuo piano di marketing Vai a](https://partner.microsoft.com/en-us/explore/commercial#gtm).
