---
title: "Novità Note sulla versione per Azure Active Directory | Microsoft Docs"
description: "Informazioni sulle novità di Azure Active Directory (Azure AD), incluse le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 766ab10c853eb56a1203686d21d0e131c44a41b5
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory




> Per restare sempre aggiornati sulle novità di Azure Active Directory, sottoscrivere questo [feed](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) nel lettore di feed RSS preferito.



Il servizio Azure Active Directory viene continuamente migliorato. Questo articolo fornisce informazioni sugli sviluppi più recenti in relazione a:

-   Versioni più recenti 
-   Problemi noti 
-   Correzioni di bug 
-   Funzionalità deprecate 
-   Modifiche pianificate 

Visitare regolarmente questa pagina che verrà aggiornata ogni mese.

## <a name="november-2017"></a>Novembre 2017

**Tipo**: funzionalità deprecate  
**Categoria di servizio:** ACS  
**Funzionalità del prodotto:** Servizio di controllo di accesso 

<a name="acs-retirement"></a>

Il Sevizio di controllo di accesso di Microsoft Azure Active Directory, noto anche come Servizio di controllo di accesso o ACS, verrà deprecato alla fine del 2018.  Altre informazioni, ad esempio una pianificazione dettagliata e istruzioni sulla migrazione di livello elevato, saranno disponibili nelle prossime settimane. Nel frattempo, lasciare commenti in questa pagina con eventuali domande sull'ACS. Un membro del team di Microsoft si occuperà di rispondere.

---


## <a name="october-2017"></a>Ottobre 2017

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità (ILM)  


**Deprecazione delle API di report di Azure AD (versione beta) nel nodo `https://graph.windows.net/<tenant-name>/reports/`**

Il portale di Azure offre:

- Una nuova console di amministrazione di Azure Active Directory 
- Nuove API per i report sulle attività e sulla sicurezza
 
Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint **/reports** verranno ritirate il 10 dicembre 2017. 

---

**Tipo:** Correzione   
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO  


Azure Active Directory supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password.  Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](http://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettendo un URL Web e quindi salvando la pagina.
 
A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata per un determinato periodo. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** MFA  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  


Nel mondo odierno l'autenticazione MFA (Multi-Factor Authentication) è fondamentale per proteggere l'organizzazione. Il team Microsoft che si occupa di identità sta perfezionando questo tipo di autenticazione per rendere le credenziali più adattive e semplificare l'esperienza. Al momento sono stati fatti due importanti passi avanti in questo percorso: 

- Integrazione dei risultati della richiesta a più fattori direttamente nel report di accesso di Azure AD, con accesso a livello di codice ai risultati dell'autenticazione MFA

- Integrazione più stretta della configurazione di MFA con l'esperienza di configurazione di base di Azure AD nel portale di Azure

Con questa versione di anteprima pubblica, la gestione e i report di MFA diventano una parte integrante dell'esperienza di configurazione di base di Azure AD, consentendo di usare la funzionalità del portale di gestione di MFA direttamente da Azure AD.

Vedere le informazioni di [riferimento sui report dell'autenticazione a più fattori nel portale di Azure](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance  


Le **condizioni per l'utilizzo di Azure AD** forniscono un metodo semplice per presentare le informazioni agli utenti finali. Gli utenti vedranno così le dichiarazioni di non responsabilità rilevanti in relazione ai requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione. 

- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici). 

- Condizioni per l'utilizzo specifiche per l'accesso ad app a elevato impatto aziendale, come Salesforce.

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure Active Directory](active-directory-tou.md).


---
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** PIM  
**Funzionalità del prodotto:** Privileged Identity Management  


Con Azure Active Directory Privileged Identity Management (PIM) è ora possibile gestire, controllare e monitorare l'accesso a Risorse di Azure (anteprima) nell'organizzazione, tra cui sottoscrizioni, gruppi di risorse e anche macchine virtuali. Tutte le risorse nel portale di Azure che sfruttano la funzionalità Controllo degli accessi in base al ruolo di Azure possono usare tutte le straordinarie funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD PIM e alcune nuove eccezionali funzionalità che saranno presto disponibili nei ruoli di Azure AD.

Per altre informazioni, vedere [PIM per Risorse di Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance  


Le verifiche di accesso (anteprima) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali: 

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso dei rispettivi accessi alle applicazioni e le appartenenze ai gruppi. Le informazioni dettagliate fornite dalle verifiche di accesso consentono ai revisori di decidere in modo efficiente se i guest possono continuare ad accedere.

- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e le appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO  


**Possibilità di nascondere le applicazioni di terze parti da App personali e dall'icona di avvio di Office 365**

È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. Ciò è utile nei casi in cui vengono visualizzati riquadri delle app per servizi back-end o riquadri duplicati che creano disordine nelle icone di avvio delle app dell'utente. È possibile abilitare o disabilitare questa proprietà nella sezione delle proprietà dell'app di terze parti tramite l'opzione **Visibile agli utenti?**. È anche possibile nascondere un'app a livello di codice tramite PowerShell. 

Per altre informazioni, vedere [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md) (Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory). 


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API fornisce funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa agli eventi di rischio di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.


## <a name="september-2017"></a>Settembre 2017

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità (ILM)  


Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di MIM 2016 SP1 fino alla build 4.4.1459.0 per Microsoft Identity Manager 2016. 
- Richiede **Microsoft Identity Manager 2016 build 4.4.1302.0**. 

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
