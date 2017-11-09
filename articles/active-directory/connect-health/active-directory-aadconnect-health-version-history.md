---
title: Cronologia delle versioni di Azure AD Connect Health
description: "Questo documento descrive le versioni di Azure AD Connect Health e ciò che includono."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c990a184d44771c78330f54f518bb4c35a36a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: cronologia delle versioni
Il team di Azure Active Directory aggiorna regolarmente Azure AD Connect Health con nuove funzionalità. Questo articolo elenca le versioni e le funzionalità che sono state rilasciate.

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

