---
title: Confronto di identità esterne - Azure Active Directory | Microsoft Docs
description: Le identità esterne di Azure AD consentono a persone esterne all'organizzazione di accedere ad app e risorse usando la loro identità. Confrontare le soluzioni disponibili per identità esterne, tra cui la collaborazione B2B e B2C di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c45aae35c7c1cf2fbde436303a2ac000dfdd5ec
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339614"
---
# <a name="compare-solutions-for-external-identities-in-azure-active-directory"></a>Confronto di soluzioni per identità esterne in Azure Active Directory

Con le identità esterne in Azure AD, è possibile consentire a persone esterne all'organizzazione di accedere ad app e risorse, eseguendo l'accesso con qualsiasi identità preferiscano usare. Partner, distributori, fornitori e altri utenti guest possono usare le proprie identità. Sia che facciano parte di Azure AD o di un altro sistema gestito dall'IT o che abbiano un'identità di social network non gestita, come Google o Facebook, possono usare le proprie credenziali per l'accesso. Il provider di identità gestisce l'identità dell'utente esterno, mentre l'amministratore interno gestisce l'accesso alle proprie app con Azure AD per mantenere protette le risorse. 

## <a name="external-identities-scenarios"></a>Scenari di identità esterne

Più che sulla relazione dell'utente con l'organizzazione, il servizio Azure AD per identità esterne è incentrato sul modo in cui una persona vuole accedere alle app e alle risorse. All'interno di questo framework, Azure AD supporta un'ampia varietà di scenari, dalla collaborazione business-to-business (B2B) allo sviluppo di app per clienti e utenti (business-to-consumer o B2C).

- **Condividere app con gli utenti esterni (collaborazione B2B)** . Invitare gli utenti esterni nel proprio tenant come utenti guest a cui è possibile assegnare autorizzazioni e al tempo stesso consentire loro di usare le credenziali esistenti per l'autenticazione. Gli utenti accedono alle risorse condivise usando un semplice processo di invito e riscatto con il loro account di rete, dell'istituto di istruzione o di posta elettronica. Ora, con la disponibilità dei flussi utente di iscrizione self-service (anteprima), è anche possibile fornire un'esperienza di accesso per gli utenti esterni tramite l'applicazione che si vuole condividere. È possibile configurare le impostazioni del flusso utente per controllare il modo in cui l'utente effettua l'iscrizione all'applicazione e che gli consente di usare il suo account aziendale o dell'istituto di istruzione oppure qualsiasi identità di social network, come Google o Facebook.  Per altre informazioni, vedere la [documentazione di Azure AD B2B](index.yml).

- **Sviluppare app destinate ad altri tenant di Azure AD (singolo o multi-tenant)** . Quando si sviluppano applicazioni per Azure AD, è possibile destinarle a utenti di una singola organizzazione (tenant singolo) o di qualsiasi organizzazione già dotata di un tenant di Azure (applicazioni multi-tenant). Queste applicazioni multi-tenant vengono registrate una sola volta nella propria istanza di Azure AD, ma possono essere usate da qualsiasi utente di Azure AD di qualsiasi organizzazione, senza ulteriori interventi da parte del proprietario.

- **Sviluppare app white-label per utenti e clienti (Azure AD B2C)** . Le organizzazioni o gli sviluppatori che creano app rivolte ai clienti possono estenderle a clienti, utenti o cittadini tramite Azure AD B2C. Gli sviluppatori possono usare Azure AD Azure AD come sistema completo di gestione delle identità per la propria applicazione, consentendo al contempo ai clienti di accedere con un'identità già disponibile (ad esempio Facebook o Gmail). Con Azure AD B2C, è possibile personalizzare e controllare completamente il modo in cui i clienti si iscrivono, accedono e gestiscono i loro profili quando usano le applicazioni rese disponibili. Per altre informazioni, vedere la [documentazione di Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/).

La tabella seguente offre un confronto dettagliato dei vari scenari in cui è possibile abilitare Azure AD per identità esterne.

| Applicazioni multi-tenant  | Collaborazione con utenti esterni (B2B) | App per utenti o clienti (B2C)  |
| ---- | --- | --- |
| Scenario principale: SaaS (Software-as-a-service) aziendale | Scenario principale: collaborazione tramite applicazioni Microsoft (Microsoft 365, Teams e così via) o tramite il proprio software di collaborazione.  | Scenario principale: applicazioni transazionali che usano applicazioni sviluppate personalizzate.   |
| Destinatari: organizzazioni che desiderano fornire software a molti clienti aziendali.    | Destinatari: organizzazioni che vogliono poter autenticare gli utenti di un'organizzazione partner, indipendentemente dal provider di identità.    | Destinatari: clienti delle app Web o per dispositivi mobili, sia che si tratti di singoli utenti, clienti istituzionali o aziendali, invitati in una directory di Azure AD distinta da quella della propria organizzazione. |
| Identità supportate: dipendenti con account Azure AD. | Identità supportate: dipendenti con account aziendale o dell'istituto di istruzione, partner con account aziendale o dell'istituto di istruzione oppure qualsiasi indirizzo email. A breve sarà disponibile il supporto per la federazione diretta.      | Identità supportate: utenti consumer con account di applicazioni locali (qualsiasi indirizzo email o nome utente) o qualsiasi identità social supportata con federazione diretta.       |
| Gli utenti esterni vengono gestiti nella loro directory, isolati dalla directory in cui è stata registrata l'applicazione.    | Gli utenti esterni vengono gestiti nella stessa directory dei dipendenti, ma con annotazioni speciali. Possono essere gestiti in modo analogo ai dipendenti, possono essere aggiunti agli stessi gruppi e così via.    | Gli utenti esterni vengono gestiti nella directory dell'applicazione. Vengono gestiti separatamente rispetto alla directory di dipendenti e partner dell'organizzazione (se disponibile).  |
| Single Sign-on: è supportato l'accesso SSO a tutte le app connesse di Azure AD.          | Single Sign-on: è supportato l'accesso SSO a tutte le app connesse di Azure AD. È possibile ad esempio concedere l'accesso a Microsoft 365 oppure ad app locali e ad altre app SaaS, ad esempio Salesforce o Workday.    | Single Sign-on: È supportato il SSO ad app di proprietà del cliente all'interno di tenant B2C di Azure AD. L'accesso SSO a Microsoft 365 o ad altre app SaaS Microsoft non è supportato.    |
| Ciclo di vita per i clienti: gestito dall'organizzazione principale dell'utente.      | Ciclo di vita per i partner: gestito dall'organizzazione host (o che manda l'invito).    | Ciclo di vita per i clienti: in modalità self-service o gestito dall'applicazione.      |
| Criteri di sicurezza e conformità: gestiti dall'organizzazione host (o che manda l'invito), ad esempio con [criteri di accesso condizionale](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access).           | Criteri di sicurezza e conformità: gestiti dall'organizzazione host (o che manda l'invito), ad esempio con [criteri di accesso condizionale](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access). | Criteri di sicurezza e conformità: gestito dall'applicazione.        |
| Personalizzazione: viene usato il marchio dell'organizzazione host o che invia l'invito.   | Personalizzazione: viene usato il marchio dell'organizzazione host o che invia l'invito.    | Personalizzazione: gestito dall'applicazione. In genere viene eseguita una personalizzazione in base al prodotto, con l'organizzazione sullo sfondo.   |
| Per altre informazioni, vedere: [Gestire le identità in applicazioni multi-tenant](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [Guida pratica](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Per altre informazioni, vedere: [Post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentazione](what-is-b2b.md)                   | Per altre informazioni, vedere: [pagina del prodotto](https://azure.microsoft.com/services/active-directory-b2c/), [documentazione](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Proteggere e gestire clienti e partner oltre i confini dell'organizzazione con Azure AD per identità esterne.

### <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Informazioni su Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
