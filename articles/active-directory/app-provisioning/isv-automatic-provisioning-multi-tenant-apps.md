---
title: Abilitare il provisioning automatico degli utenti per le applicazioni multi-tenant - Azure ADEnable automatic user provisioning for multi-tenant applications - Azure AD
description: Guida per i fornitori di software indipendenti per l'abilitazione del provisioning automatico
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522394"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Abilitare il provisioning automatico degli utenti per l'applicazione multi-tenantEnable automatic user provisioning for your multi-tenant application

Il provisioning automatico degli utenti è il processo di automazione della creazione, della manutenzione e della rimozione delle identità utente nei sistemi di destinazione, ad esempio le applicazioni software come servizio.

## <a name="why-enable-automatic-user-provisioning"></a>Perché abilitare il provisioning automatico degli utenti?

Le applicazioni che richiedono la presenza di un record utente nell'applicazione prima che il primo accesso dell'utente richieda il provisioning dell'utente. Come fornitore di servizi, puoi vantaggi e vantaggi per i tuoi clienti.

### <a name="benefits-to-you-as-the-service-provider"></a>Vantaggi per te come fornitore di servizi

* Aumentare la sicurezza dell'applicazione utilizzando la piattaforma di identità Microsoft.A increase the security of your application by using the Microsoft identity platform.

* Riduci lo sforzo effettivo e percepito del cliente per adottare la tua applicazione.

* Riduci i costi di integrazione con più provider di identità (IdP) per il provisioning automatico degli utenti utilizzando il provisioning basato su System for Cross-Domain Identity Management (SCIM).

* Riduci i costi di supporto fornendo log avanzati che aiutano i clienti a risolvere i problemi di provisioning degli utenti.

* Aumentare la visibilità dell'applicazione nella raccolta di app di [Azure AD.](https://azuremarketplace.microsoft.com/marketplace/apps)

* Ottenere un elenco con priorità nella pagina Esercitazioni app.

### <a name="benefits-to-your-customers"></a>Vantaggi per i tuoi clienti

* Aumentare la sicurezza rimuovendo automaticamente l'accesso all'applicazione per gli utenti che modificano i ruoli o lasciano l'organizzazione all'applicazione.

* Semplifica la gestione degli utenti per la tua applicazione evitando errori umani e lavoro ripetitivo associato al provisioning manuale.

* Riduci i costi di hosting e manutenzione di soluzioni di provisioning personalizzate.

## <a name="choose-a-provisioning-method"></a>Scegliere un metodo di provisioning

Azure AD offre diversi percorsi di integrazione per abilitare il provisioning automatico degli utenti per l'applicazione.

* Il servizio di provisioning di [Azure AD](../app-provisioning/user-provisioning.md) gestisce il provisioning e il deprovisioning degli utenti da Azure AD all'applicazione (provisioning in uscita) e dall'applicazione ad Azure AD (provisioning in ingresso). Il servizio si connette agli endpoint API di gestione utente SCIM (System for Cross-Domain Identity Management) forniti dall'applicazione.

* Quando si usa [Microsoft Graph](https://docs.microsoft.com/graph/), l'applicazione gestisce il provisioning in ingresso e in uscita di utenti e gruppi da Azure AD all'applicazione eseguendo una query sull'API Microsoft Graph.

* Il provisioning degli utenti SAML JIT (Security Assertion Markup Language Just in Time) può essere abilitato se l'applicazione utilizza SAML per la federazione. Utilizza le informazioni sulle attestazioni inviate nel token SAML per eseguire il provisioning degli utenti.

Per determinare l'opzione di integrazione da utilizzare per l'applicazione, fare riferimento alla tabella di confronto di alto livello e quindi visualizzare le informazioni più dettagliate su ogni opzione.

| Funzionalità abilitate o migliorate dal provisioning automatico| Servizio di provisioning di Azure AD (SCIM 2.0)Azure AD Provisioning Service (SCIM 2.0)| API Microsoft Graph (OData v4.0)| SAML JIT |
|---|---|---|---|
| User and group management in Azure AD| √| √| Solo utente |
| Gestire utenti e gruppi sincronizzati da Active Directory locale| √*| √*| Solo utente |
| Accedere ai dati oltre gli utenti e i gruppi durante il provisioning Accesso ai dati di Office 365 (Team, SharePoint, E-mail, Calendario, Documenti e così via)| X+| √| X |
| Creare, leggere e aggiornare gli utenti in base alle regole di businessCreate, read, and update users based on business rules| √| √| √ |
| Eliminare gli utenti in base alle regole di businessDelete users based on business rules| √| √| X |
| Gestire il provisioning automatico degli utenti per tutte le applicazioni dal portale di AzureManage automatic user provisioning for all applications from the Azure portal| √| X| √ |
| Supportare più provider di identitàSupport multiple identity providers| √| X| √ |
| Supportare gli account guest (B2B)| √| √| √ |
| Supportare account non aziendali (B2C)| X| √| √ |

<sup>*</sup>– La configurazione di Azure AD Connect è necessaria per sincronizzare gli utenti da AD ad Azure AD.  
<sup>+</sup >– L'utilizzo di SCIM per il provisioning non impedisce l'integrazione dell'applicazione con MIcrosoft Graph per altri scopi.

## <a name="azure-ad-provisioning-service-scim"></a>Servizio di provisioning di Azure AD (SCIM)Azure AD Provisioning Service (SCIM)

I servizi di provisioning di Azure AD usano [SCIM,](https://aka.ms/SCIMOverview)uno standard di settore per il provisioning supportato da molti provider di identità (IdP) e applicazioni (ad esempio Slack, G Suite, Dropbox). È consigliabile usare il servizio di provisioning di Azure AD se si vuole supportare gli IdP oltre ad Azure AD, poiché qualsiasi IdP compatibile con SCIM può connettersi all'endpoint SCIM. Creando un endpoint /User semplice, è possibile abilitare il provisioning senza dover gestire il proprio motore di sincronizzazione. 

Per altre informazioni su come gli utenti del servizio di provisioning di Azure AD SCIM, vedere:For more information on how the Azure AD Provisioning Service users SCIM, see: 

* [Ulteriori informazioni sullo standard SCIM](https://aka.ms/SCIMOverview)

* [Uso di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Informazioni sull'implementazione SCIM di Azure ADUnderstand the Azure AD SCIM implementation](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph per il provisioning

Quando si usa Microsoft Graph per il provisioning, si ha accesso a tutti i dati utente avanzati disponibili in Graph.When you use Microsoft Graph for provisioning, you have access to all the rich user data available in Graph. Oltre ai dettagli di utenti e gruppi, è anche possibile recuperare informazioni aggiuntive come i ruoli dell'utente, i report di manager e diretti, i dispositivi di proprietà e registrati e centinaia di altri pezzi di dati disponibili in [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Più di 15 milioni di organizzazioni e il 90% delle aziende fortune 500 usano Azure AD durante l'iscrizione a servizi cloud Microsoft come Office 365, Microsoft Azure, Enterprise Mobility Suite o Microsoft 365. Puoi usare Microsoft Graph per integrare la tua app con flussi di lavoro amministrativi, ad esempio l'onboarding (e la terminazione dei dipendenti), la manutenzione dei profili e altro ancora. 

Altre informazioni sull'uso di Microsoft Graph per il provisioning:Learn more about using Microsoft Graph for provisioning:

* [Home page di Microsoft Graph](https://developer.microsoft.com/graph)

* [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://docs.microsoft.com/graph/overview)

* [Panoramica di Microsoft Graph Auth](https://docs.microsoft.com/graph/auth/)

* [Introduzione a Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Utilizzo di SAML JIT per il provisioning

Se si desidera eseguire il provisioning degli utenti solo al primo accesso all'applicazione e non è necessario eseguire automaticamente il deprovisioning degli utenti, SAML JIT è un'opzione. L'applicazione deve supportare SAML 2.0 come protocollo di federazione per l'utilizzo di SAML JIT.

SAML JIT utilizza le informazioni sulle attestazioni nel token SAML per creare e aggiornare le informazioni utente nell'applicazione. I clienti possono configurare queste attestazioni necessarie nell'applicazione Azure AD in base alle esigenze. A volte il provisioning JIT deve essere abilitato dal lato dell'applicazione in modo che il cliente possa usare questa funzionalità. SAML JIT è utile per la creazione e l'aggiornamento degli utenti, ma non può eliminare o disattivare gli utenti nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare Single Sign-On per l'applicazioneEnable Single Sign-On for your application](../manage-apps/isv-sso-content.md)

* [Inviare l'elenco delle applicazioni](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e collaborare con Microsoft per creare la documentazione sul sito Microsoft.

* [Iscriviti al Microsoft Partner Network (gratuito) e crea il tuo go to market plan](https://partner.microsoft.com/en-us/explore/commercial).
