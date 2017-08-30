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
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---
# <a name="azure-active-directory-reporting-faq"></a>Domande frequenti sulla creazione di report in Azure Active Directory

Questo articolo include risposte alle domande frequenti sulla creazione di report in Azure Active Directory.  
Per altre informazioni, vedere la pagina relativa alla [creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md) 

**D: Qual è la conservazione dei dati per i log attività (controllo e accessi) nel portale di Azure?** 

**R:** Offriamo 7 giorni di dati per i clienti con sottoscrizione gratuita. Passando a una licenza AD Premium 1 o Azure AD Premium 2 è possibile accedere ai dati fino a 30 giorni. Per altri dettagli sulla conservazione, vedere la pagina relativa ai [criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md).

--- 

**D: Quanto tempo occorre per la visualizzazione dei dati sull'attività dopo il completamento della stessa?**

**R:** I log dell'attività di controllo hanno una latenza compresa tra 15 minuti e un'ora. I log dell'attività di accesso hanno una latenza compresa tra 15 minuti per la maggior parte dei record e fino a 2 ore per altri record.

---

**Q: È necessario essere amministratore globale per visualizzare i log attività nel portale di Azure o per ottenere i dati tramite l'API?**

**R:** No. L'utente può avere un **ruolo con autorizzazioni di lettura per la sicurezza**, può essere un **amministratore della protezione** o un **amministratore globale** per visualizzare i dati dei report nel portale di Azure o accedendovi tramite l'API.

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

**D: Come vengono calcolati gli indirizzi IP nel report degli accessi e degli accessi a rischio?**

**R:** Gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. A ciò si aggiungono fattori come la possibilità che provider di telefonia mobile e VPN rilascino indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Per questi motivi, la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni. 

---

