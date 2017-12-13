---
title: Domande frequenti sulla creazione di report in Azure Active Directory | Documentazione Microsoft
description: Domande frequenti sulla creazione di report in Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e1c1b8bdf94104c0047e367f67a29d557fcc8df9
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Domande frequenti sulla creazione di report in Azure Active Directory

Questo articolo include risposte alle domande frequenti sulla creazione di report in Azure Active Directory (Azure AD). Per altre informazioni, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md). 

**D: se si usano le API dell'endpoint https://graph.windows.net/&lt;nome-tentant&gt;/reports/ per eseguire il pull a livello di codice dei report di controllo e di utilizzo delle applicazioni integrate di Azure AD, a quale API occorre passare?**

**R:** consultare la [documentazione Microsoft di riferimento sulle API](https://developer.microsoft.com/graph/) per vedere come è possibile usare le nuove API per accedere ai [report sulle attività](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Questo endpoint dispone di due report (Controllo e Accessi) che forniscono tutti i dati che si ottengono nell'endpoint delle API precedente. Anche questo nuovo endpoint include un report sugli accessi con la licenza Azure AD Premium, che è possibile usare per ottenere informazioni sull'utilizzo dell'app, l'utilizzo dei dispositivi e gli accessi degli utenti.


--- 

**D: se si usano le API dell'endpoint https://graph.windows.net/&lt;nome-tenant&gt;/reports/ per eseguire il pull a livello di codice dei report sulla sicurezza di Azure AD (tipi specifici di rilevamenti, come perdita di credenziali o accessi da indirizzi IP anonimi) nei sistemi di gestione dei report, a quale API occorre passare?**

**R:** è possibile usare l'[API per gli eventi di rischio di protezione dell'identità](active-directory-identityprotection-graph-getting-started.md) per accedere ai rilevamenti relativi alla sicurezza tramite Microsoft Graph. Questo nuovo formato offre maggiore flessibilità per l'esecuzione di query sui dati, grazie a filtri avanzati, modalità di selezione dei campi e altro, oltre a standardizzare gli eventi di rischio su un solo tipo per una più facile integrazione con gli strumenti per la raccolta di informazioni di sicurezza e gestione degli eventi e altri dati. Poiché i dati sono un formato diverso, non è possibile sostituire le query precedenti con una nuova query. Tuttavia, [la nuova API usa Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), ovvero lo standard Microsoft per API come Office 365 o Azure AD. Saranno quindi necessari interventi per estendere gli investimenti esistenti per Microsoft Graph o per avviare la transizione a questa nuova piattaforma standard.

--- 

**D: Qual è la conservazione dei dati per i log attività (controllo e accessi) nel portale di Azure?** 

**R:** vengono offerti 7 giorni di dati per i clienti con sottoscrizione gratuita. In alternativa, è possibile accedere ai dati fino a 30 giorni acquistando una licenza Azure AD Premium 1 o 2. Per altre informazioni sulla conservazione dei report, vedere [Criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md).

--- 

**D: Quanto tempo occorre per la visualizzazione dei dati sull'attività dopo il completamento della stessa?**

**R:** i log dell'attività di controllo hanno una latenza compresa tra 15 minuti e un'ora. Per la visualizzazione dei log delle attività di accesso possono essere necessari da 15 minuti fino a 2 ore per alcuni record.

---

**D: è necessario essere amministratore globale per visualizzare gli accessi al portale di Azure o per ottenere i dati tramite l'API?**

**R:** No. È necessario avere il **ruolo con autorizzazioni di lettura per la sicurezza** oppure essere un **amministratore della protezione** o un **amministratore globale** per ottenere i dati dei report nel portale di Azure o tramite l'API.

---

**D: È possibile ottenere informazioni sui log attività di Office 365 tramite il portale di Azure?**

**R:** Sebbene l'attività di Office 365 e i log attività di Azure AD condividano numerose risorse della directory, se si desidera una visualizzazione completa dei log attività di Office 365, è necessario usare l'interfaccia di amministrazione di Office 365 per ottenere informazioni sui log attività di Office 365.

---


**D: Quali API è necessario usare per ottenere informazioni sui log attività di Office 365?**

**R:** Usare le API Gestione di Office 365 per accedere ai [log attività di Office 365 tramite un'API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**D: Quanti record è possibile scaricare dal portale di Azure?**

**R:** Dal portale di Azure è possibile scaricare fino a 120 KB di record. I record vengono ordinati in base ai *più recenti* e, per impostazione predefinita, vengono visualizzati 120 KB dei record più recenti. 

---

**D: Su quanti record è possibile eseguire query tramite le API delle attività?**

**R:** Le query possono essere eseguite su un numero massimo di 1 milione di record (se non si usa l'operatore TOP, che ordina i record in base al più recente). Se si usa l'operatore TOP, è possibile eseguire query su un massimo di 500 KB di record. [Qui](active-directory-reporting-api-getting-started.md) è possibile trovare le query di esempio su come usare l'API.

---

**D: Come ottenere una licenza Premium?**

**R:** Per la risposta a questa domanda, vedere [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md).

---

**D: In quanto tempo è possibile visualizzare le attività dopo aver acquistato una licenza Premium?**

**R:** Se si dispone già di dati sulle attività come licenza gratuita, è possibile visualizzare questi dati. Se non si dispone di dati, saranno necessari uno o due giorni.

---

**D: È possibile visualizzare i dati del mese precedente dopo avere acquistato una licenza Azure AD Premium?**

**R:**: Se di recente si è passati alla versione Premium (anche di valutazione), inizialmente è possibile visualizzare i dati fino a 7 giorni. Quando i dati si accumulano, verranno visualizzati fino a 30 giorni.

---

**D: Esiste un evento di rischio nella protezione dell'identità, ma non vengono visualizzati accessi corrispondenti in tutti gli accessi. È normale?**

**R:** Sì, la protezione dell'identità valuta il rischio per tutti i flussi di autenticazione, sia interattivi che non interattivi. Tuttavia, tutti i report degli accessi mostrano solo gli accessi interattivi.

---

**D: Come è possibile scaricare il report "Utenti contrassegnati per il rischio" nel portale di Azure?**

**R:**: L'opzione per scaricare il report *Utenti contrassegnati per il rischio* verrà aggiunta a breve.

---

**D: Come sapere perché un accesso o un utente è stato contrassegnato come rischioso nel portale di Azure?**

**R:** I clienti dell'edizione Premium possono scoprire altre informazioni sugli eventi di rischio sottostanti facendo clic sull'utente in "Utenti contrassegnati per il rischio" o facendo clic su "Accessi a rischio". I clienti della versione Basic e Free riescono a visualizzare gli utenti e gli accessi a rischio senza le informazioni sull'evento di rischio sottostante.

---

**D: come vengono calcolati gli indirizzi IP nel report degli accessi e degli accessi a rischio?**

**R:** Gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. A ciò si aggiungono fattori come la possibilità che provider di telefonia mobile e VPN rilascino indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Per questi motivi, la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni. 

---

**D: Cosa significa l'evento di rischio "È stato rilevato un accesso con rischi aggiuntivi"?**

**R:** Per offrire informazioni approfondite su tutti gli accessi rischiosi effettuati nell'ambiente, viene visualizzato l'evento di rischio "È stato rilevato un accesso con rischi aggiuntivi" per gli accessi considerati rischiosi a seguito di rilevamenti effettuati esclusivamente per i sottoscrittori di Azure AD Identity Protection.

---
