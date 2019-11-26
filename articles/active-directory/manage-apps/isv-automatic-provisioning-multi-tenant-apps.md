---
title: Abilitare il provisioning utenti automatico per le applicazioni multi-tenant-Azure AD
description: Guida per i fornitori di software indipendenti per l'abilitazione del provisioning automatizzato
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 74b991fce132c991ebd5fbd3789328e2a500da86
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232305"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Abilitare il provisioning utenti automatico per l'applicazione multi-tenant

Il provisioning utenti automatico è il processo di automazione della creazione, della manutenzione e della rimozione delle identità utente nei sistemi di destinazione come le applicazioni software come servizio.

## <a name="why-enable-automatic-user-provisioning"></a>Perché abilitare il provisioning utenti automatico?

Le applicazioni che richiedono un record utente sono presenti nell'applicazione prima che il primo accesso di un utente richieda il provisioning degli utenti. Il provider di servizi offre vantaggi e vantaggi per i clienti.

### <a name="benefits-to-you-as-the-service-provider"></a>Vantaggi offerti dal provider di servizi

* Aumentare la sicurezza dell'applicazione usando la piattaforma di identità Microsoft.

* Ridurre il lavoro effettivo e percepito del cliente per adottare l'applicazione.

* Ridurre i costi di integrazione con più provider di identità (IDP) per il provisioning utenti automatico usando System for Cross-Domain Identity Management (SCIM).

* Ridurre i costi di supporto fornendo log avanzati per aiutare i clienti a risolvere i problemi di provisioning degli utenti.

* Aumentare la visibilità dell'applicazione nella raccolta di [app Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps).

* Ottenere un elenco con priorità nella pagina delle esercitazioni sulle app.

### <a name="benefits-to-your-customers"></a>Vantaggi per i clienti

* Aumentare la sicurezza rimuovendo automaticamente l'accesso all'applicazione per gli utenti che modificano i ruoli o lasciano l'organizzazione all'applicazione.

* Semplificare la gestione degli utenti per l'applicazione evitando errori umani e operazioni ripetitive associate al provisioning manuale.

* Riduci i costi per l'hosting e la gestione di soluzioni di provisioning personalizzate.

## <a name="choose-a-provisioning-method"></a>Scegliere un metodo di provisioning

Azure AD offre diversi percorsi di integrazione per abilitare il provisioning utenti automatico per l'applicazione.

* Il [servizio di provisioning Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) gestisce il provisioning e il deprovisioning degli utenti da Azure ad all'applicazione (provisioning in uscita) e dall'applicazione ai Azure ad (provisioning in ingresso). Il servizio si connette al sistema per gli endpoint API di gestione degli utenti di gestione identità tra domini (SCIM) forniti dall'applicazione.

* Quando si usa il [Microsoft Graph](https://docs.microsoft.com/graph/), l'applicazione gestisce il provisioning in ingresso e in uscita di utenti e gruppi da Azure ad all'applicazione eseguendo una query sull'API Microsoft Graph.

* Il provisioning dell'utente Security Assertion Markup Language JIT (just-in-Time) può essere abilitato se l'applicazione usa SAML per la Federazione. Usa le informazioni sulle attestazioni inviate nel token SAML per eseguire il provisioning degli utenti.

Per determinare l'opzione di integrazione da usare per l'applicazione, fare riferimento alla tabella di confronto di alto livello e quindi vedere le informazioni più dettagliate su ogni opzione.

| Funzionalità abilitate o migliorate tramite provisioning automatico| Servizio di provisioning di Azure AD (SCIM 2,0)| API Microsoft Graph (OData v 4.0)| JIT SAML |
|---|---|---|---|
| Gestione di utenti e gruppi in Azure AD| √| √| Solo utente |
| Gestire utenti e gruppi sincronizzati da Active Directory locali| √*| √*| Solo utente * |
| Accedere ai dati oltre gli utenti e i gruppi durante il provisioning dell'accesso ai dati di O365 (team, SharePoint, posta elettronica, calendario, documenti e così via)| X+| √| X |
| Creare, leggere e aggiornare gli utenti in base alle regole di business| √| √| √ |
| Elimina gli utenti in base alle regole di business| √| √| X |
| Gestire il provisioning utenti automatico per tutte le applicazioni dal portale di Azure| √| X| √ |
| Supportare più provider di identità| √| X| √ |
| Supportare gli account Guest (B2B)| √| √| √ |
| Supportare account non aziendali (B2C)| X| √| √ |

<sup>*</sup> : il programma di installazione di Azure ad Connect è necessario per sincronizzare gli utenti da ad a Azure ad.  
<sup>+</sup >: l'uso di SCIM per il provisioning non impedisce all'utente di integrare l'applicazione con Microsoft Graph per altri scopi.

## <a name="azure-ad-provisioning-service-scim"></a>Servizio di provisioning Azure AD (SCIM)

Il servizio di provisioning Azure AD USA [scim](https://aka.ms/SCIMOverview), uno standard di settore per il provisioning supportato da molti provider di identità (IDP) e applicazioni (ad esempio Slack, g Suite, Dropbox). Si consiglia di usare il servizio di provisioning Azure AD se si vuole supportare gli IDP oltre al Azure AD, in quanto qualsiasi IdP conforme a SCIM può connettersi all'endpoint SCIM. Creazione di un semplice endpoint/User è possibile abilitare il provisioning senza dover gestire il motore di sincronizzazione. 

Per ulteriori informazioni sul modo in cui gli utenti del servizio Azure AD provisioning SCIM, vedere: 

* [Scopri di più sullo standard SCIM](https://aka.ms/SCIMOverview)

* [Uso di System for Cross-Domain Identity Management (SCIM) per effettuare automaticamente il provisioning di utenti e gruppi da Azure Active Directory alle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Informazioni sull'implementazione di Azure AD SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph per il provisioning

Quando si usa Microsoft Graph per il provisioning, è possibile accedere a tutti i dati utente avanzati disponibili in Graph. Oltre ai dettagli di utenti e gruppi, è anche possibile recuperare informazioni aggiuntive, ad esempio i ruoli dell'utente, i Manager e i report diretti, i dispositivi di proprietà e registrati e centinaia di altre parti di dati disponibili nella [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Più di 15 milioni organizzazioni e 90% delle società Fortune 500 usano Azure AD sottoscrivendo i servizi cloud Microsoft come Office 365, Microsoft Azure, Enterprise Mobility Suite o Microsoft 365. È possibile usare Microsoft Graph per integrare l'app con flussi di lavoro amministrativi, ad esempio l'onboarding dei dipendenti (e la terminazione), la manutenzione del profilo e altro ancora. 

Altre informazioni sull'uso di Microsoft Graph per il provisioning:

* [Home page di Microsoft Graph](https://developer.microsoft.com/graph)

* [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://docs.microsoft.com/graph/overview)

* [Panoramica dell'autenticazione Microsoft Graph](https://docs.microsoft.com/graph/auth/)

* [Introduzione a Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Uso di SAML JIT per il provisioning

Se si vuole eseguire il provisioning degli utenti solo al primo accesso all'applicazione e non è necessario eseguire automaticamente il deprovisioning degli utenti, SAML JIT è un'opzione. L'applicazione deve supportare SAML 2,0 come protocollo federativo per l'uso di SAML JIT.

SAML JIT usa le informazioni sulle attestazioni nel token SAML per creare e aggiornare le informazioni utente nell'applicazione. I clienti possono configurare queste attestazioni richieste nell'applicazione Azure AD in base alle esigenze. In alcuni casi è necessario abilitare il provisioning JIT dal lato dell'applicazione in modo che il cliente possa usare questa funzionalità. Il protocollo JIT SAML è utile per la creazione e l'aggiornamento degli utenti, ma non può eliminare o disattivare gli utenti nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare l'accesso Single Sign-on per l'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-sso-content)

* [Inviare l'elenco di applicazioni e il](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) partner con Microsoft per creare la documentazione sul sito di Microsoft.

* [Unisciti alla Microsoft Partner Network (gratuita) e crea il tuo piano di marketing Vai a](https://partner.microsoft.com/en-us/explore/commercial).
