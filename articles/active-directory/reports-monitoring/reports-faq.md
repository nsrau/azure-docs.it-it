---
title: Domande frequenti sui report di Azure Active Directory | Microsoft Docs
description: Domande frequenti sui report Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1984e2f0bb55115c7d700b91f564d9f01c145151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015331"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Domande frequenti sui report di Azure Active Directory

Questo articolo include risposte alle domande frequenti sulla creazione di report in Azure Active Directory (Azure AD). Per altre informazioni, vedere [Creazione di report in Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Guida introduttiva 

**D: attualmente si utilizzano le `https://graph.windows.net/<tenant-name>/reports/` API endpoint per eseguire il pull Azure ad i report di controllo e di utilizzo delle applicazioni integrati nei sistemi di report a livello di codice. Che cosa devo passare a?**

**R:** Consultare la [documentazione di riferimento sulle API](https://developer.microsoft.com/graph/) per informazioni su come [usare le API per accedere ai report attività](concept-reporting-api.md). Questo endpoint dispone di due report (**controllo** e **accessi**) che forniscono tutti i dati ottenuti nell'endpoint API precedente. Anche questo nuovo endpoint include un report sugli accessi con la licenza Azure AD Premium, che è possibile usare per ottenere informazioni sull'utilizzo dell'app, l'utilizzo dei dispositivi e gli accessi degli utenti.

---

**D: attualmente si usano le `https://graph.windows.net/<tenant-name>/reports/` API dell'endpoint per estrarre i report di sicurezza Azure ad (tipi specifici di rilevamento, ad esempio le credenziali perse o gli accessi da indirizzi IP anonimi) nei sistemi di report a livello di programmazione. Che cosa devo passare a?**

**R:** È possibile usare l' [API di rilevamento dei rischi di Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)   per accedere ai rilevamenti di sicurezza tramite Microsoft Graph. Questo nuovo formato offre maggiore flessibilità nel modo in cui è possibile eseguire query sui dati, con filtri avanzati, selezione dei campi e altro ancora, e standardizza i rilevamenti dei rischi in un unico tipo per semplificare l'integrazione in SIEM e altri strumenti di raccolta dati. Poiché i dati sono un formato diverso, non è possibile sostituire le query precedenti con una nuova query. Tuttavia, [la nuova API usa Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta), ovvero lo standard Microsoft per tali api come Microsoft 365 o Azure ad. Il lavoro richiesto può quindi estendere gli attuali investimenti in Microsoft Graph o consentire di iniziare la transizione alla nuova piattaforma standard.

---

**D: Come ottenere una licenza Premium?**

**R:** Vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso.

---

**D: In quanto tempo è possibile visualizzare le attività dopo aver acquistato una licenza Premium?**

**R:** Se si dispone già di dati sulle attività come licenza gratuita, è possibile visualizzarli immediatamente. Se non si dispone di dati, i dati verranno visualizzati nei report dopo uno o due giorni.

---

**D: È possibile visualizzare i dati del mese precedente dopo avere acquistato una licenza Azure AD Premium?**

**R:** Se è stato recentemente passato a una versione Premium (inclusa una versione di valutazione), è possibile visualizzare inizialmente i dati fino a 7 giorni. Man mano che i dati si accumulano, è possibile visualizzare quelli degli ultimi 30 giorni.

---

**D: È necessario essere amministratore globale per visualizzare gli accessi al portale di Azure o per ottenere i dati tramite l'API?**

**R:** No, è possibile accedere ai dati dei report tramite il portale o l'API anche se si ha il **ruolo con autorizzazioni di lettura per la sicurezza** o **Amministratore della sicurezza** per il tenant. Naturalmente anche gli **amministratori globali** hanno accesso a questi dati.

---


## <a name="activity-logs"></a>Log attività


**D: Qual è la conservazione dei dati per i log attività (controllo e accessi) nel portale di Azure?** 

**R:** Per ulteriori informazioni, vedere [criteri di conservazione dei dati per i report Azure ad](reference-reports-data-retention.md).

---

**D: quanto tempo è necessario fino a quando non è possibile visualizzare i dati dell'attività dopo aver completato l'attività?**

**R:** I log di controllo hanno una latenza compresa tra 15 minuti e un'ora. Per la visualizzazione dei log delle attività di accesso possono essere necessari da 15 minuti fino a 2 ore per alcuni record.

---

**D: è possibile ottenere Microsoft 365 informazioni sui log attività tramite la portale di Azure?**

**R:** Anche se Microsoft 365 attività e i log attività Azure AD condividono numerose risorse della directory, se si desidera una visualizzazione completa dei log attività Microsoft 365, è necessario passare all'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com) per ottenere informazioni sul log attività di Office 365.

---

**D: quali API si utilizzano per ottenere informazioni sui log attività Microsoft 365?**

**R:** Usare le [API di gestione di Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview) per accedere ai log attività Microsoft 365 tramite un'API.

---

**D: Quanti record è possibile scaricare dal portale di Azure?**

**R:** Dal portale di Azure è possibile scaricare fino a 5.000 record. Per impostazione predefinita, vengono scaricati gli ultimi 5.000 record, ordinati partendo dai record *più recenti*.

---

## <a name="risky-sign-ins"></a>Accessi a rischio

**D: esiste un rilevamento dei rischi in Identity Protection, ma non viene visualizzato l'accesso corrispondente nel report degli accessi. È previsto?**

**R:** Sì, la protezione dell'identità valuta il rischio per tutti i flussi di autenticazione, sia interattivi che non interattivi. Tuttavia, tutti i report degli accessi mostrano solo gli accessi interattivi.

---

**D: Come sapere perché un accesso o un utente è stato contrassegnato come rischioso nel portale di Azure?**

**R:** Se si dispone di una sottoscrizione di **Azure ad Premium** , è possibile ottenere altre informazioni sui rilevamenti dei rischi sottostanti selezionando l'utente in **utenti contrassegnati per il rischio** o selezionando un record nel report degli **accessi a rischio** . Se si dispone di una sottoscrizione **gratuita** o **Basic** , è possibile visualizzare i report utenti a rischio e accessi a rischio, ma non è possibile visualizzare le informazioni di rilevamento dei rischi sottostanti.

---

**D: come vengono calcolati gli indirizzi IP nel report degli accessi e degli accessi a rischio?**

**R:** Gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. Il mapping degli indirizzi IP è ulteriormente complicato da fattori come la possibilità che provider di telefonia mobile e VPN rilascino indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Attualmente nei report di Azure AD la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni. 

---

**D: qual è il significato del rilevamento dei rischi "accesso con rischi aggiuntivi"?**

**R:** Per offrire informazioni dettagliate su tutti gli accessi rischiosi effettuati nell'ambiente, "È stato rilevato un accesso con rischi aggiuntivi" funziona come segnaposto per gli accessi per rilevamenti esclusivi per i sottoscrittori di Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Accesso condizionale

**D: Quali sono le novità di questa funzionalità?**

**R:** I clienti possono ora risolvere i problemi relativi ai criteri di accesso condizionale tramite tutti i report degli accessi. I clienti possono esaminare lo stato di accesso condizionale e approfondire i dettagli dei criteri applicati all'accesso e il risultato per ogni criterio.

**D: Come iniziare?**

**R**: Per iniziare:

* Andare al report degli accessi nel [portale di Azure](https://portal.azure.com).
* Fare clic sull'accesso di cui si desiderano risolvere i problemi.
* Passare alla scheda **accesso condizionale** . Qui è possibile visualizzare tutti i criteri che hanno influito sull'accesso e il risultato per ogni criterio. 
    
**D: quali sono tutti i valori possibili per lo stato di accesso condizionale?**

**R:** Lo stato di accesso condizionale può includere i valori seguenti:

* **Non applicato**: ciò significa che non è presente alcun criterio di accesso condizionale con l'utente e con l'app previsti. 
* **Operazione riuscita**: ciò significa che è presente un criterio di accesso condizionale con l'utente e con l'app previsti e i criteri di accesso condizionale sono stati soddisfatti correttamente. 
* **Errore**: l'accesso ha soddisfatto la condizione dell'utente e dell'applicazione di almeno un criterio di accesso condizionale e i controlli di concessione non sono soddisfatti o impostati per bloccare l'accesso.
    
**D: quali sono tutti i valori possibili per il risultato dei criteri di accesso condizionale?**

**R:** I criteri di accesso condizionale possono avere i seguenti risultati:

* **Operazione riuscita**: il criterio è stato soddisfatto correttamente.
* **Operazione non riuscita**: il criterio non è stato soddisfatto.
* **Non applicato**: le condizioni dei criteri potrebbero non essere soddisfatte.
* **Non abilitato**: a causa di criteri nello stato disabilitato. 
    
**D: il nome del criterio nel report tutti gli accesso non corrisponde al nome del criterio in CA. perché?**

**R:** Il nome del criterio nel report di tutti gli accessi è basato sul nome del criterio di accesso condizionale al momento dell'accesso. Ciò può essere incoerente con il nome del criterio nell'accesso condizionale se è stato aggiornato il nome del criterio in un secondo momento, vale a dire, dopo l'accesso.

**D: l'accesso è stato bloccato a causa di un criterio di accesso condizionale, ma il report sull'attività di accesso indica che l'accesso è riuscito. Perché?**

**R:** Attualmente, il report di accesso potrebbe non visualizzare risultati accurati per gli scenari di Exchange ActiveSync quando viene applicato l'accesso condizionale. Possono verificarsi casi in cui il risultato dell'accesso nel report Mostra un accesso riuscito, ma l'accesso non è riuscito a causa di un criterio di accesso condizionale.