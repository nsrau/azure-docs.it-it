---
title: Cronologia delle versioni di Azure AD Connect Health
description: Questo documento descrive le versioni di Azure AD Connect Health e ciò che includono.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: edc1771153581e73398e8df25e70660f9f85ceba
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: cronologia delle versioni
Il team di Azure Active Directory aggiorna regolarmente Azure AD Connect Health con nuove funzionalità. Questo articolo elenca le versioni e le funzionalità che sono state rilasciate.

## <a name="march-2018"></a>Marzo 2018
**Aggiornamento dell'agente:**

*   Agente di Azure AD Connect Health per AD DS (versione 3.0.176.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Correzioni di bug e miglioramenti generali
*   Agente di Azure AD Connect Health per AD FS (versione 3.0.176.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Correzioni di bug e miglioramenti generali
* Agente di Azure AD Connect Health per la sincronizzazione (versione 3.0.176.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Correzioni di bug e miglioramenti generali

## <a name="december-2017"></a>Dicembre 2017
**Aggiornamento dell'agente:**

*   Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.145.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Sono stati aggiunti nuovi comandi sulla risoluzione dei problemi dell'agente
  3. Correzioni di bug e miglioramenti generali
*   Agente di Azure AD Connect Health per AD FS (versione 3.0.145.0)
  1. Sono stati aggiunti nuovi comandi sulla risoluzione dei problemi dell'agente
  2. Miglioramenti della disponibilità dell'agente 
  3. Correzioni di bug e miglioramenti generali
  
## <a name="october-2017"></a>Ottobre 2017
**Aggiornamento dell'agente:**

 * Agente di Azure AD Connect Health per Sync (versione 3.0.129.0) rilasciato con Azure AD Connect versione 1.1.649.0
<br></br> È stato risolto il problema di compatibilità di versione tra Azure AD Connect e l'agente di Azure AD Connect Health per la sincronizzazione. Questo problema interessa i clienti che eseguono l'aggiornamento sul posto di Azure AD Connect versione 1.1.647.0, ma hanno attualmente l'agente integrità versione 3.0.127.0. Dopo l'aggiornamento, l'agente integrità non può più inviare dati di integrità relativi al servizio di sincronizzazione di Azure AD Connect al servizio integrità di Azure Active Directory. Con questa correzione, l'agente di integrità versione 3.0.129.0 viene installato durante l'aggiornamento sul posto di Azure AD Connect. L'agente integrità versione 3.0.129.0 non presenta il problema di compatibilità con Azure AD Connect versione 1.1.649.0.

## <a name="july-2017"></a>Luglio 2017
**Aggiornamento dell'agente:**

*   Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.68.0)
  1. Correzioni di bug e miglioramenti generali
  2. Supporto di cloud sovrani
*   Agente di Azure AD Connect Health per AD FS (versione 3.0.68.0)
  1. Correzioni di bug e miglioramenti generali
  2. Supporto di cloud sovrani
* Agente di Azure AD Connect Health per Sync (versione 3.0.68.0) rilasciato con Azure AD Connect versione 1.1.614.0
1. Supporto per il cloud di Microsoft Azure per enti pubblici e Microsoft Cloud Germany

## <a name="april-2017"></a>Aprile 2017      
**Aggiornamento dell'agente:**

*   Agente di Azure AD Connect Health per AD FS (versione 3.0.12.0)
  1. Correzioni di bug e miglioramenti generali
*   Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.12.0)
  1. Miglioramenti nel caricamento dei contatori delle prestazioni
  2. Correzioni di bug e miglioramenti generali

## <a name="october-2016"></a>Ottobre 2016
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per AD FS \(versione 2.6.408.0\)
1. Sono stati apportati miglioramenti alla rilevazione degli indirizzi IP client nelle richieste di autenticazione
2. Correzioni di bug relativi agli avvisi
* Agente di Azure AD Connect Health per AD DS (versione 2.6.408.0)
1. Correzioni di bug relativi agli avvisi.
* Agente di Azure AD Connect Health per Sync (versione 2.6.353.0) rilasciato con Azure AD Connect versione 1.1.281.0
1. Vengono forniti i dati necessari per i report sugli errori di sincronizzazione
2. Correzioni di bug relativi agli avvisi

**Nuove funzionalità di anteprima:**

* Report sugli errori di sincronizzazione per Azure AD Connect

**Nuove funzionalità:**

* Azure AD Connect Health per AD FS: il campo dell'indirizzo IP è disponibile nel report per i primi 50 utenti con nome utente/password non validi.

## <a name="july-2016"></a>Luglio 2016
**Nuove funzionalità di anteprima:**

* [Azure AD Connect Health per Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Gennaio 2016
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per AD FS (versione 2.6.91.1512)

**Nuove funzionalità:**

* [Strumento di test della connettività per agenti di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembre 2015
**Nuove funzionalità:**

* Supporto per il [Controllo degli accessi in base al ruolo](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Nuove funzionalità di anteprima:**

* [Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md).

**Problemi risolti:**

* Correzioni di bug per errori visualizzati durante le registrazioni di agenti.

## <a name="september-2015"></a>Settembre 2015
**Nuove funzionalità:**

* Report delle password del nome utente per AD FS errato
* Supporto per la configurazione del proxy HTTP non autenticato
* Supporto per la configurazione dell'agente nel Server Core
* Miglioramenti agli avvisi per AD FS
* Miglioramenti all'agente di Azure AD Connect Health per AD FS per la connettività e il caricamento di dati.

**Problemi risolti:**

* Correzioni di bug per i tipi di errore nei dati sull'utilizzo di AD FS.

## <a name="june-2015"></a>Giugno 2015
**Versione iniziale di Azure AD Connect Health per AD FS e proxy AD FS.**

**Nuove funzionalità:**

* Avvisi per il monitoraggio di AD FS e dei server proxy AD FS con notifiche di posta elettronica.
* Facilità di accesso alla topologia di AD FS e ai modelli nei contatori delle prestazioni di AD FS.
* Tendenze delle richieste di token con esito positivo nei server AD FS raggruppate per applicazioni, metodi di autenticazione, percorso di rete delle richieste e così via.
* Tendenze delle richieste con esito negativo nei server AD FS raggruppate per applicazioni, tipi di errore e così via.
* Distribuzione più semplice dell'agente con le credenziali di amministratore globale di Azure AD.  

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](active-directory-aadconnect-health.md).

