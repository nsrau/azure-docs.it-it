---
title: Cronologia delle versioni di Azure AD Connect Health
description: Questo documento descrive le versioni di Azure AD Connect Health e ciò che includono.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27c0d3055512145396f204fdb17e9375f65a0db3
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562326"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Cronologia di rilascio delle versioni
Il team di Azure Active Directory aggiorna regolarmente Azure AD Connect Health con nuove funzionalità. Questo articolo elenca le versioni e le funzionalità che sono state rilasciate.  

> [!NOTE]
> Gli agenti di Connect Health vengono aggiornati automaticamente quando viene rilasciata una nuova versione. Verificare che le impostazioni di aggiornamento automatico siano abilitate da portale di Azure. 
>

Azure AD Connect Health per la sincronizzazione è integrato con l'installazione di Azure AD Connect. Per altre informazioni, vedere [Azure AD Connect: Cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history). Per fornire un feedback sulle funzionalità, esprimere un voto nel sito [User Voice di Connect Health](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>2019 luglio
**Aggiornamento dell'agente**
* Agente di Azure AD Connect Health per AD FS (versione 3.1.59.0) 
   1. Modifica del testo in TestWindowsTransport
   2. Modifiche per il caricamento di AD FS RP
   
* Agente di Azure AD Connect Health per AD FS (versione 3.1.56.0) 
   1. Aggiungere il test di TestWindowsTransport e rimuovere i controlli degli endpoint di WsTrust nel test CheckOffice365Endpoints
   2. Log del sistema operativo e informazioni .NET
   3. Aumentare le dimensioni di caricamento del messaggio di configurazione RP a 1 MB.
   4. Correzioni di bug
   
* Agente di Azure AD Connect Health per AD DS (versione 3.1.56.0) 
   1. Log del sistema operativo e informazioni .NET 
   2. Correzioni di bug

## <a name="may-2019"></a>Maggio 2019
**Aggiornamento dell'agente:** 
* Agente di Azure AD Connect Health per AD FS (versione 3.1.51.0) 
   1. Correzione di bug per distinguere tra più accessi che condividono lo stesso client-Request-ID.
   2. Correzione di bug per analizzare errori di nome utente/password non validi nei server localizzati della lingua.   

## <a name="april-2019"></a>Aprile 2019
**Aggiornamento dell'agente:** 
* Agente di Azure AD Connect Health per AD FS (versione 3.1.46.0) 
   1. Correzione controlla il processo di avviso duplicato SPN per ADFS

## <a name="march-2019"></a>Marzo 2019
**Aggiornamento dell'agente:** 
* Agente di Azure AD Connect Health per AD DS (versione 3.1.41.0)  
   1. Raccolta di versioni .NET
   2. Miglioramento della raccolta dei contatori delle prestazioni in assenza di determinate categorie
   3. Correzione di bug per impedire la generazione di più istanze dell'agente di monitoraggio

* Agente di Azure AD Connect Health per AD FS (versione 3.1.41.0) 
   1. Integrare e aggiornare gli script di test di AD FS usando ADFSToolBox
   2. Implementare la raccolta di versioni .NET
   3. Miglioramento della raccolta dei contatori delle prestazioni in assenza di determinate categorie
   4. Correzione di bug per impedire la generazione di più istanze dell'agente di monitoraggio


## <a name="november-2018"></a>Novembre 2018
**Nuove funzionalità con disponibilità generale:** 
* Azure AD Connect Health: diagnosi e correzione degli errori di sincronizzazione che causano la duplicazione di attributi dal portale

**Aggiornamento dell'agente:** 
* Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.1.24.0) 
   1. Conformità e applicazione del protocollo Transport Layer Security (TLS) versione 1.2
   2. Riduzione della frequenza degli avvisi per il catalogo globale
   3. Correzione di bug relativi alla registrazione dell'agente di integrità

* Agente di Azure AD Connect Health per Active Directory Federation Services (versione 3.1.24.0)  
   1. Conformità e applicazione del protocollo Transport Layer Security (TLS) versione 1.2
   2. Supporto di Test-ADFSRequestToken per il sistema operativo localizzato
   3. Problema di blocco EventHandler agente diagnostica risolto
   4. Correzione di bug relativi alla registrazione dell'agente di integrità

## <a name="august-2018"></a>Agosto 2018 
*  Agente di Azure AD Connect Health per la sincronizzazione (versione 3.1.7.0) rilasciato con Azure AD Connect versione 1.1.880.0    
   1. Hotfix per il [problema dell'utilizzo elevato della CPU da parte dell'agente di monitoraggio con le versioni KB di .NET Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Giugno 2018 
**Nuove funzionalità di anteprima:** 
* Azure AD Connect Health: diagnosi e correzione degli errori di sincronizzazione che causano la duplicazione di attributi dal portale 

**Aggiornamento dell'agente:** 
* Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.1.7.0)    
  1. Hotfix per il [problema dell'utilizzo elevato della CPU da parte dell'agente di monitoraggio con le versioni KB di .NET Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agente di Azure AD Connect Health per Active Directory Federation Services (versione 3.1.7.0)  
  1. Hotfix per il [problema dell'utilizzo elevato della CPU da parte dell'agente di monitoraggio con le versioni KB di .NET Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Correzioni di risultati di test nel server secondario di ADFS Server 2016
   
* Agente di Azure AD Connect Health per Active Directory Federation Services (versione 3.1.2.0)  
  1. Hotfix per la gestione della memoria dell'agente e avvisi correlati specifici per la versione 3.0.244.0


## <a name="may-2018"></a>Maggio 2018
**Aggiornamento dell'agente:**
* Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.244.0)
  1. Miglioramenti privacy agente  
  2. Correzioni di bug e miglioramenti generali

* Agente di Azure AD Connect Health per Active Directory Federation Services (versione 3.0.244.0)
  1. Servizio agente di diagnostica e miglioramenti del modulo di PowerShell correlati
  2. Miglioramenti privacy agente  
  3. Correzioni di bug e miglioramenti generali

* Agente di Azure AD Connect Health per la sincronizzazione (versione 3.0.164.0) rilasciato con Azure AD Connect versione 1.1.819.0 
  1. Miglioramenti privacy agente  
  2. Correzioni di bug e miglioramenti generali


## <a name="march-2018"></a>Marzo 2018
**Nuove funzionalità di anteprima:**
* Azure AD Connect Health per AD FS - Report sugli indirizzi IP rischiosi e avvisi.

**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per AD DS (versione 3.0.176.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Correzioni di bug e miglioramenti generali
* Agente di Azure AD Connect Health per AD FS (versione 3.0.176.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Correzioni di bug e miglioramenti generali
* Agente di Azure AD Connect Health per la sincronizzazione (versione 3.0.129.0) rilasciato con Azure AD Connect versione 1.1.750.0  
  1. Miglioramenti della disponibilità dell'agente 
  2. Correzioni di bug e miglioramenti generali

## <a name="december-2017"></a>Dicembre 2017
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.145.0)
  1. Miglioramenti della disponibilità dell'agente 
  2. Sono stati aggiunti nuovi comandi sulla risoluzione dei problemi dell'agente
  3. Correzioni di bug e miglioramenti generali
* Agente di Azure AD Connect Health per AD FS (versione 3.0.145.0)
  1. Sono stati aggiunti nuovi comandi sulla risoluzione dei problemi dell'agente
  2. Miglioramenti della disponibilità dell'agente 
  3. Correzioni di bug e miglioramenti generali
  
## <a name="october-2017"></a>Ottobre 2017
**Aggiornamento dell'agente:**

 * Agente di Azure AD Connect Health per Sync (versione 3.0.129.0) rilasciato con Azure AD Connect versione 1.1.649.0
<br></br> È stato risolto il problema di compatibilità di versione tra Azure AD Connect e l'agente di Azure AD Connect Health per la sincronizzazione. Questo problema interessa i clienti che eseguono l'aggiornamento sul posto di Azure AD Connect versione 1.1.647.0, ma hanno attualmente l'agente integrità versione 3.0.127.0. Dopo l'aggiornamento, l'agente integrità non può più inviare dati di integrità relativi al servizio di sincronizzazione di Azure AD Connect al servizio integrità di Azure Active Directory. Con questa correzione, l'agente di integrità versione 3.0.129.0 viene installato durante l'aggiornamento sul posto di Azure AD Connect. L'agente integrità versione 3.0.129.0 non presenta il problema di compatibilità con Azure AD Connect versione 1.1.649.0.

## <a name="july-2017"></a>Luglio 2017
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.68.0)
  1. Correzioni di bug e miglioramenti generali
  2. Supporto di cloud sovrani
* Agente di Azure AD Connect Health per AD FS (versione 3.0.68.0)
  1. Correzioni di bug e miglioramenti generali
  2. Supporto di cloud sovrani
* Agente di Azure AD Connect Health per Sync (versione 3.0.68.0) rilasciato con Azure AD Connect versione 1.1.614.0
  1. Supporto per il cloud di Microsoft Azure per enti pubblici e Microsoft Cloud Germany

## <a name="april-2017"></a>Aprile 2017      
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per AD FS (versione 3.0.12.0)
  1. Correzioni di bug e miglioramenti generali
* Agente di Azure AD Connect Health per Active Directory Domain Services (versione 3.0.12.0)
  1. Miglioramenti nel caricamento dei contatori delle prestazioni
  2. Correzioni di bug e miglioramenti generali

## <a name="october-2016"></a>Ottobre 2016
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per Active Directory Federation Services (versione 2.6.408.0)
* Sono stati apportati miglioramenti alla rilevazione degli indirizzi IP client nelle richieste di autenticazione
* Correzioni di bug relativi agli avvisi
* Agente di Azure AD Connect Health per AD DS (versione 2.6.408.0)
* Correzioni di bug relativi agli avvisi.
* Agente di Azure AD Connect Health per Sync (versione 2.6.353.0) rilasciato con Azure AD Connect versione 1.1.281.0
* Vengono forniti i dati necessari per i report sugli errori di sincronizzazione
* Correzioni di bug relativi agli avvisi

**Nuove funzionalità di anteprima:**

* Report sugli errori di sincronizzazione per Azure AD Connect

**Nuove funzionalità:**

* Azure AD Connect Health per AD FS: il campo dell'indirizzo IP è disponibile nel report per i primi 50 utenti con nome utente/password non validi.

## <a name="july-2016"></a>Luglio 2016
**Nuove funzionalità di anteprima:**

* [Azure AD Connect Health per Servizi di dominio Active Directory](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Gennaio 2016
**Aggiornamento dell'agente:**

* Agente di Azure AD Connect Health per AD FS (versione 2.6.91.1512)

**Nuove funzionalità:**

* [Strumento di test della connettività per agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembre 2015
**Nuove funzionalità:**

* Supporto per il [Controllo degli accessi in base al ruolo](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nuove funzionalità di anteprima:**

* [Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md).

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
Informazioni su come [monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](whatis-hybrid-identity-health.md).

