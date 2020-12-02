---
title: Confronto di identità esterne - Azure Active Directory | Microsoft Docs
description: Le identità esterne di Azure AD consentono a persone esterne all'organizzazione di accedere ad app e risorse usando la loro identità. Confrontare le soluzioni disponibili per identità esterne, tra cui la collaborazione B2B e B2C di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/23/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6be0720b7eafb251b262d5cd182bdf06e201402d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173074"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Che cosa sono le identità esterne in Azure Active Directory?

Con le identità esterne in Azure AD, è possibile consentire a persone esterne all'organizzazione di accedere ad app e risorse aziendali, permettendo loro di eseguire l'accesso con l'identità che preferiscono. Partner, distributori, fornitori e altri utenti guest possono usare le proprie identità. Sia che abbiano un'identità digitale emessa dall'azienda o da un ente pubblico oppure un'identità di social network non gestita, come Google o Facebook, possono usare le proprie credenziali per l'accesso. Il provider di identità gestisce l'identità dell'utente esterno, mentre l'amministratore interno gestisce l'accesso alle proprie app con Azure AD per mantenere protette le risorse.

## <a name="external-identities-scenarios"></a>Scenari di identità esterne

Più che sulla relazione dell'utente con l'organizzazione, la funzionalità Identità esterne di Azure AD è incentrata sul modo in cui l'utente vuole accedere alle app e alle risorse. All'interno di questo framework, Azure AD supporta un'ampia varietà di scenari, dalla collaborazione business-to-business (B2B) alla gestione dell'accesso per applicazioni destinate a consumatori/clienti o cittadini (business-to-consumer o B2C).

- **Condividere app e risorse con utenti esterni (collaborazione B2B)** . Invitare gli utenti esterni nel proprio tenant come utenti "guest" a cui è possibile assegnare autorizzazioni e al tempo stesso consentire loro di usare le credenziali esistenti per l'autenticazione. Per accedere alle risorse condivise, gli utenti usano un semplice processo di invito e riscatto con il proprio account aziendale o dell'istituto di istruzione oppure con un altro account di posta elettronica. È anche possibile usare la [gestione entitlement di Azure AD](../governance/entitlement-management-overview.md) per configurare i criteri che [gestiscono l'accesso per gli utenti esterni](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). Ora, con la disponibilità dei [flussi utente di iscrizione self-service (anteprima)](self-service-sign-up-overview.md), è possibile consentire agli utenti esterni di iscriversi autonomamente alle applicazioni. L'esperienza può essere personalizzata per consentire l'iscrizione con un'identità aziendale, dell'istituto di istruzione o di social network, come Google o Facebook. È anche possibile raccogliere informazioni sull'utente durante il processo di iscrizione. Per altre informazioni, vedere la [documentazione di Azure AD B2B](index.yml).

- **Creare percorsi utente con una soluzione di gestione delle identità white-label per le app destinate a consumatori e clienti (Azure AD B2C)** . Le organizzazioni o gli sviluppatori che creano app rivolte ai clienti possono estenderle a milioni di consumatori, clienti o cittadini tramite Azure AD B2C. Gli sviluppatori possono usare Azure AD come sistema completo per la gestione delle identità e degli accessi dei clienti per le proprie applicazioni. I clienti possono accedere con un'identità già stabilita, ad esempio Facebook o Gmail. Con Azure AD B2C, è possibile personalizzare e controllare completamente il modo in cui i clienti si iscrivono, accedono e gestiscono i loro profili quando usano le applicazioni rese disponibili. Per altre informazioni, vedere la [documentazione di Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Confrontare le soluzioni per Identità esterne

La tabella seguente offre un confronto dettagliato degli scenari che è possibile abilitare con la funzionalità Identità esterne di Azure AD.

|   | Collaborazione con utenti esterni (B2B) | Accesso alle app destinate a consumatori/clienti (B2C)  |
| ---- | --- | --- |
| **Scenario principale** | Collaborazione con le applicazioni Microsoft (Microsoft 365, Teams e così via) o con le proprie applicazioni (app SaaS, app personalizzate e così via).  | Gestione delle identità e dell'accesso per applicazioni SaaS moderne o personalizzate (non per le app Microsoft).   |
| **Destinatari**    | Collaborazione con partner commerciali di organizzazioni esterne, come fornitori e partner. Gli utenti vengono visualizzati come utenti guest nella directory. Questi utenti possono o meno avere un'identità gestita.  | Clienti del prodotto. Questi utenti vengono gestiti in una directory di Azure AD separata.  |
| **Provider di identità supportati** | Gli utenti esterni possono collaborare usando account aziendali o dell'istituto di istruzione, qualsiasi indirizzo di posta elettronica, provider di identità basati su SAML e WS-Fed, Gmail e Facebook.  | Consumatori con account di applicazioni locali (qualsiasi indirizzo di posta elettronica o nome utente) varie identità di social network supportate e utenti con identità emesse dall'azienda o da un ente pubblico.       |
| **Gestione degli utenti esterni**   | Gli utenti esterni vengono gestiti nella stessa directory dei dipendenti, ma vengono in genere contrassegnati come utenti guest. Gli utenti guest possono essere gestiti allo stesso modo dei dipendenti, possono essere aggiunti agli stessi gruppi e così via.    | Gli utenti esterni vengono gestiti nella directory di Azure AD B2C. Vengono gestiti separatamente rispetto alla directory di dipendenti e partner dell'organizzazione (se disponibile).  |
| **Single sign-on (SSO)**      | è supportato l'accesso SSO a tutte le app connesse di Azure AD. È possibile ad esempio concedere l'accesso a Microsoft 365 oppure ad app locali e ad altre app SaaS, ad esempio Salesforce o Workday.    | È supportato il SSO ad app di proprietà del cliente all'interno di tenant B2C di Azure AD. L'accesso SSO a Microsoft 365 o ad altre app SaaS Microsoft non è supportato.    |
| **Criteri di sicurezza e conformità**        | gestiti dall'organizzazione host (o che manda l'invito), ad esempio con [criteri di accesso condizionale](conditional-access.md). | Gestiti dall'organizzazione tramite accesso condizionale e protezione delle identità.        |
| **Personalizzazione**  | viene usato il marchio dell'organizzazione host o che invia l'invito.    | Personalizzazione completa per applicazione o organizzazione.   |
| **Modello di fatturazione** | [Prezzi delle identità esterne](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) basati su utenti attivi mensili. <br>Vedere anche [Dettagli della configurazione B2B](external-identities-pricing.md) | [Prezzi delle identità esterne](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) basati su utenti attivi mensili. <br>Vedere anche [Dettagli della configurazione B2C](../../active-directory-b2c/billing.md) |
| **Altre informazioni** | [Post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentazione](what-is-b2b.md)                   | [pagina del prodotto](https://azure.microsoft.com/services/active-directory-b2c/), [documentazione](../../active-directory-b2c/index.yml)       |

Proteggere e gestire clienti e partner oltre i confini dell'organizzazione con Azure AD per identità esterne.

## <a name="about-multitenant-applications"></a>Informazioni sulle applicazioni multi-tenant

Se si fornisce un'app come servizio e non si vogliono gestire gli account utente dei clienti, un'app multi-tenant rappresenta probabilmente la scelta migliore. Quando si sviluppano applicazioni per altri tenant di Azure AD, è possibile destinarle a utenti di una singola organizzazione (tenant singolo) o di qualsiasi organizzazione già dotata di un tenant di Azure AD (applicazioni multi-tenant). Le registrazioni app in Azure AD sono a tenant singolo per impostazione predefinita, ma è possibile renderle multi-tenant. Le applicazioni multi-tenant vengono registrate una sola volta nell'istanza di Azure AD dell'organizzazione. Tuttavia, qualsiasi utente di Azure AD di qualsiasi organizzazione può usare l'applicazione senza ulteriori interventi da parte dello sviluppatore. Per altre informazioni, vedere [Gestire le identità in applicazioni multi-tenant](/azure/architecture/multitenant-identity/), [Guida pratica](../develop/howto-convert-app-to-be-multi-tenant.md).

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Informazioni su Azure AD B2C](../../active-directory-b2c/overview.md)