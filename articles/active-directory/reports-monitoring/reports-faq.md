---
title: Domande frequenti sui report di Azure Active Directory | Microsoft Docs
description: Domande frequenti sui report di Azure Active Directory.Frequently asked questions around Azure Active Directory reports.
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266507"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Domande frequenti sui report di Azure Active Directory

Questo articolo include risposte alle domande frequenti sulla creazione di report in Azure Active Directory (Azure AD). Per altre informazioni, vedere [Creazione di report in Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introduzione 

**D: Attualmente uso `https://graph.windows.net/<tenant-name>/reports/` le API degli endpoint per eseguire il pull dei report di controllo e utilizzo integrato delle applicazioni di Azure AD nei sistemi di report a livello di codice. A cosa devo passare?**

**R:** Consultare la [documentazione di riferimento sulle API](https://developer.microsoft.com/graph/) per informazioni su come [usare le API per accedere ai report attività](concept-reporting-api.md). Questo endpoint dispone di due report (**Audit** e **Sign-in**) che forniscono tutti i dati ottenuti nell'endpoint API precedente. Anche questo nuovo endpoint include un report sugli accessi con la licenza Azure AD Premium, che è possibile usare per ottenere informazioni sull'utilizzo dell'app, l'utilizzo dei dispositivi e gli accessi degli utenti.

---

**D: Attualmente uso `https://graph.windows.net/<tenant-name>/reports/` le API degli endpoint per estrarre i report di sicurezza di Azure AD (tipi specifici di rilevamenti, ad esempio credenziali trapelate o accessi da indirizzi IP anonimi) nei sistemi di report a livello di programmazione. A cosa devo passare?**

**A:** È possibile utilizzare l'API per i rilevamenti dei rischi di identity protection per accedere ai rilevamenti di sicurezza tramite Microsoft Graph.You can use the [Identity Protection risk detections API](../identity-protection/graph-get-started.md) to access security detections through Microsoft Graph. Questo nuovo formato offre una maggiore flessibilità nella modalità di query sui dati, con filtri avanzati, selezione dei campi e altro ancora, e standardizza i rilevamenti dei rischi in un unico tipo per semplificare l'integrazione nelle SIEM e in altri strumenti di raccolta dati. Poiché i dati sono un formato diverso, non è possibile sostituire le query precedenti con una nuova query. Tuttavia, [la nuova API usa Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), ovvero lo standard Microsoft per API come Office 365 o Azure AD. Quindi il lavoro richiesto può estendere gli attuali investimenti di Microsoft Graph o aiutarti a iniziare la transizione a questa nuova piattaforma standard.

---

**D: Come ottenere una licenza Premium?**

**R:** Vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso.

---

**D: In quanto tempo è possibile visualizzare le attività dopo aver acquistato una licenza Premium?**

**R:** Se si dispone già di dati sulle attività come licenza gratuita, è possibile visualizzarli immediatamente. Se non si dispone di dati, i dati verranno visualizzati nei report dopo uno o due giorni.

---

**D: È possibile visualizzare i dati del mese precedente dopo avere acquistato una licenza Azure AD Premium?**

**A:** Se di recente sei passato a una versione Premium (inclusa una versione di prova), puoi inizialmente visualizzare i dati fino a 7 giorni. Man mano che i dati si accumulano, è possibile visualizzare quelli degli ultimi 30 giorni.

---

**D: È necessario essere amministratore globale per visualizzare gli accessi al portale di Azure o per ottenere i dati tramite l'API?**

**R:** No, è possibile accedere ai dati dei report tramite il portale o l'API anche se si ha il **ruolo con autorizzazioni di lettura per la sicurezza** o **Amministratore della sicurezza** per il tenant. Naturalmente anche gli **amministratori globali** hanno accesso a questi dati.

---


## <a name="activity-logs"></a>Log attività


**D: Qual è la conservazione dei dati per i log attività (controllo e accessi) nel portale di Azure?** 

**R:** La tabella seguente indica il periodo di conservazione dei dati per i log attività. Per altre informazioni, vedere [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).

| Report                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Log di controllo             | 7 giorni        | 30 giorni             | 30 giorni             |
| Accessi               | N/D           | 30 giorni             | 30 giorni             |
| Uso di Azure MFA        | 30 giorni       | 30 giorni             | 30 giorni             |

---

**D: Quanto tempo ci vuole prima di poter visualizzare i dati dell'attività dopo aver completato l'attività?**

**R:** I log di controllo hanno una latenza compresa tra 15 minuti e un'ora. Per la visualizzazione dei log delle attività di accesso possono essere necessari da 15 minuti fino a 2 ore per alcuni record.

---

**D: È possibile ottenere informazioni sui log attività di Office 365 tramite il portale di Azure?**

**A:** Anche se l'attività di Office 365 e i log attività di Azure AD condividono molte risorse della directory, se si vuole una visualizzazione completa dei log attività di Office 365, è necessario passare all'interfaccia di amministrazione di [Microsoft 365](https://admin.microsoft.com) per ottenere le informazioni del log attività di Office 365.

---

**D: Quali API è necessario usare per ottenere informazioni sui log attività di Office 365?**

**A:** Usare le API di gestione di [Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) per accedere ai log attività di Office 365 tramite un'API.

---

**D: Quanti record è possibile scaricare dal portale di Azure?**

**R:** Dal portale di Azure è possibile scaricare fino a 5.000 record. Per impostazione predefinita, vengono scaricati gli ultimi 5.000 record, ordinati partendo dai record *più recenti*.

---

## <a name="risky-sign-ins"></a>Accessi a rischio

**D: È presente un rilevamento dei rischi in Identity Protection, ma non viene visualizzato l'accesso corrispondente nel report degli invii. Questo è previsto?**

**R:** Sì, la protezione dell'identità valuta il rischio per tutti i flussi di autenticazione, sia interattivi che non interattivi. Tuttavia, tutti i report degli accessi mostrano solo gli accessi interattivi.

---

**D: Come sapere perché un accesso o un utente è stato contrassegnato come rischioso nel portale di Azure?**

**A:** Se si dispone di una sottoscrizione **di Azure AD Premium,** è possibile ottenere ulteriori informazioni sui rilevamenti dei rischi sottostanti selezionando l'utente in **Utenti contrassegnati per il rischio** o selezionando un record nel report **Accessi rischiosi.** Se si dispone di una sottoscrizione **gratuita** o **di base,** è possibile visualizzare gli utenti a rischio e i report di accesso rischiosi, ma non è possibile visualizzare le informazioni di rilevamento dei rischi sottostanti.

---

**D: come vengono calcolati gli indirizzi IP nel report degli accessi e degli accessi a rischio?**

**R:** Gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. Il mapping degli indirizzi IP è ulteriormente complicato da fattori come la possibilità che provider di telefonia mobile e VPN rilascino indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Attualmente nei report di Azure AD la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni. 

---

**D: Cosa significa il rilevamento dei rischi "Accesso con rischio aggiuntivo rilevato"?**

**R:** Per offrire informazioni dettagliate su tutti gli accessi rischiosi effettuati nell'ambiente, "È stato rilevato un accesso con rischi aggiuntivi" funziona come segnaposto per gli accessi per rilevamenti esclusivi per i sottoscrittori di Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Accesso condizionale

**D: Quali sono le novità di questa funzionalità?**

**A:** I clienti possono ora risolvere i problemi relativi ai criteri di accesso condizionale tramite tutti i report di accesso. I clienti possono esaminare lo stato di accesso condizionale e approfondire i dettagli dei criteri applicati all'accesso e il risultato per ogni criterio.

**D: Come iniziare?**

**R**: Per iniziare:

* Andare al report degli accessi nel [portale di Azure](https://portal.azure.com).
* Fare clic sull'accesso di cui si desiderano risolvere i problemi.
* Passare alla scheda **Accesso condizionale.** Qui è possibile visualizzare tutti i criteri che hanno influito sull'accesso e il risultato per ogni criterio. 
    
**D: Quali sono tutti i valori possibili per lo stato Accesso condizionale?**

**A:** Lo stato di Accesso condizionale può avere i valori seguenti:Conditional Access status can have the following values:

* **Non applicato**: ciò significa che non è presente alcun criterio di accesso condizionale con l'utente e con l'app previsti. 
* **Operazione riuscita**: ciò significa che è presente un criterio di accesso condizionale con l'utente e con l'app previsti e i criteri di accesso condizionale sono stati soddisfatti correttamente. 
* **Operazione non riuscita**: ciò significa che è presente un criterio di accesso condizionale con l'utente e con l'app previsti e i criteri di accesso condizionale non sono stati soddisfatti correttamente. 
    
**D: Quali sono tutti i valori possibili per il risultato dei criteri di accesso condizionale?**

**A:** Un criterio di accesso condizionale può avere i risultati seguenti:A Conditional Access policy can have the following results:

* **Operazione riuscita**: il criterio è stato soddisfatto correttamente.
* **Operazione non riuscita**: il criterio non è stato soddisfatto.
* **Non applicato**: le condizioni dei criteri potrebbero non essere soddisfatte.
* **Non abilitato**: a causa di criteri nello stato disabilitato. 
    
**D: Il nome del criterio nel report di accesso completo non corrisponde al nome del criterio in CA. Perché?**

**R:** Il nome del criterio nel report di tutti gli accessi è basato sul nome del criterio di accesso condizionale al momento dell'accesso. Ciò può essere incoerente con il nome del criterio nell'accesso condizionale se è stato aggiornato il nome del criterio in un secondo momento, vale a dire, dopo l'accesso.

**D: L'accesso è stato bloccato a causa di un criterio di accesso condizionale, ma il report dell'attività di accesso mostra che l'accesso è riuscito. Perché?**

**A:** Attualmente il report di accesso potrebbe non mostrare risultati accurati per gli scenari di Exchange ActiveSync quando viene applicato l'accesso condizionale. In alcuni casi il risultato dell'accesso nel report mostra un accesso riuscito, ma l'accesso in realtà non è riuscito a causa di un criterio di accesso condizionale. 
