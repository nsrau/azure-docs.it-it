---
title: Residenza dei dati per Azure Multi-Factor Authentication
description: Informazioni sui dati personali e aziendali archiviati da Azure Multi-Factor Authentication sull'amministratore e gli utenti e su quali dati rimangono all'interno del paese/area geografica di origine.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a75e9d5c8501274553edef36b475b2bd21b7875
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738802"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Residenza dei dati e dati dei clienti per Azure Multi-Factor Authentication

I dati dei clienti vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione al momento della sottoscrizione di un servizio online Microsoft, ad esempio Office 365 e Azure. Per informazioni sulla posizione di archiviazione dei dati dei clienti, è possibile usare la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Azure Multi-Factor Authentication basato sul cloud e il server Azure Multi-Factor Authentication elaborano e archiviano una certa quantità di dati personali e aziendali. Questo articolo descrive quali dati vengono archiviati e dove.

Le attività di Multi-Factor Authentication seguenti provengono attualmente da data center statunitensi, tranne nei casi indicati:

* L'autenticazione a due fattori tramite chiamate telefoniche o SMS ha in genere origine da data center statunitensi e viene instradata da provider globali.
    * Le richieste di autenticazione utente generiche da altre aree, ad esempio Europa o Australia, sono attualmente elaborate dai data center in tale area. Altri eventi, ad esempio la reimpostazione della password self-service, gli eventi di Azure B2C o gli scenari ibridi che usano l'estensione NPS o la scheda AD FS, sono tutti attualmente elaborati dai data center statunitensi.
* Le notifiche push che usano l'app Microsoft Authenticator provengono dai data center statunitensi. Inoltre, potrebbero entrare in gioco anche servizi specifici di fornitori di dispositivi da aree diverse.
* Attualmente, i codici OATH vengono in genere convalidati negli Stati Uniti.
    * Anche in questo caso, gli eventi di autenticazione utente generici che hanno origine in altre aree, ad esempio Europa o Australia, vengono elaborati dai data center in tale area. Gli eventi aggiuntivi vengono attualmente elaborati dai data center statunitensi.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dati personali archiviati da Azure Multi-Factor Authentication

I dati personali sono informazioni a livello di utente associate a un utente specifico. Gli archivi dati seguenti contengono informazioni personali:

* Utenti bloccati
* Utenti ignorati
* Richieste di modifica del token del dispositivo Microsoft Authenticator
* Report di attività di Multi-Factor Authentication
* Attivazioni di Microsoft Authenticator

Queste informazioni vengono conservate per 90 giorni.

Azure Multi-Factor Authentication non registra dati personali, ad esempio nome utente, numero di telefono o indirizzo IP, ma esiste un *UserObjectId* che identifica i tentativi Multi-Factor Authentication per gli utenti. I dati di log vengono archiviati per 30 giorni.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Per i cloud pubblici di Azure, ad eccezione dell'autenticazione Azure B2C, dell'estensione NPS e della scheda AD FS di Windows Server 2016 o 2019, vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | In log di Azure Multi-Factor Authentication     |
| SMS unidirezionale                          | In log di Azure Multi-Factor Authentication     |
| Chiamata vocale                           | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di segnalazione di frode |
| Notifica di Microsoft Authenticator | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di segnalazione di frode<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

> [!NOTE]
> L'archivio dati dei report attività di Multi-Factor Authentication viene archiviato nel Stati Uniti per tutti i cloud, indipendentemente dall'area che elabora la richiesta di autenticazione. Microsoft Azure Germania, Microsoft Azure gestito da 21Vianet e il cloud Microsoft per enti pubblici hanno archivi dati indipendenti separati dagli archivi dati dell'area del cloud pubblico. Tuttavia questi dati vengono sempre archiviati negli Stati Uniti.

Per Microsoft Azure per enti pubblici, Microsoft Azure Germania, Microsoft Azure gestito da 21Vianet, l'autenticazione Azure B2C, l'estensione NPS e la scheda AD FS di Windows Server 2016 o 2019 vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication |
| SMS unidirezionale                          | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication |
| Chiamata vocale                           | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di segnalazione di frode |
| Notifica di Microsoft Authenticator | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di segnalazione di frode<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

### <a name="multi-factor-authentication-server"></a>Server Multi-Factor Authentication

Se si distribuisce ed esegue il server Azure Multi-Factor Authentication, vengono archiviati i dati personali seguenti:

> [!IMPORTANT]
> A partire dal 1° luglio 2019, Microsoft non fornirà più il server Multi-Factor Authentication per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori ai loro utenti dovranno usare il servizio Azure Multi-Factor Authentication basato sul cloud. Gli attuali clienti che anno attivato il server Multi-Factor Authentication prima del 1° luglio potranno scaricare la versione più recente e gli aggiornamenti futuri, oltre a generare le credenziali di attivazione come di consueto.

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication |
| SMS unidirezionale                          | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication |
| Chiamata vocale                           | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di segnalazione di frode |
| Notifica di Microsoft Authenticator | In log di Azure Multi-Factor Authentication<br />Archivio dati dei report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di segnalazione di frode<br />Richieste di modifica quando viene modificato il token del dispositivo di Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dati aziendali archiviati da Azure Multi-Factor Authentication

I dati aziendali sono informazioni a livello di tenant che potrebbero esporre informazioni sulla configurazione o l'installazione dell'ambiente. Le impostazioni del tenant dalle pagine seguenti del portale di Azure per Multi-Factor Authentication potrebbero archiviare dati aziendali, ad esempio le soglie di blocco o informazioni sull'ID chiamante per le richieste di autenticazione telefonica in arrivo:

* Blocco dell'account
* Avviso di illecito
* Notifiche
* Impostazioni telefonata

Per il server Azure Multi-Factor Authentication, le pagine del portale di Azure seguenti possono contenere dati aziendali:

* Impostazioni del server
* Bypass monouso
* Regole di memorizzazione nella cache
* Stato del server Multi-Factor Authentication

## <a name="log-data-location"></a>Posizione dei dati di log

La posizione in cui vengono archiviate le informazioni di log dipende dall'area in cui vengono elaborate. La maggior parte delle aree geografiche dispone di funzionalità native di Azure Multi-Factor Authentication, quindi i dati di log vengono archiviati nella stessa area che elabora la richiesta di Multi-Factor Authentication. Le aree geografiche senza supporto nativo di Azure Multi-Factor Authentication sono gestite dalle aree geografiche Stati Uniti o Europa e i dati di log vengono archiviati nella stessa area che elabora la richiesta di Multi-Factor Authentication.

Alcuni dati di log di autenticazione di base vengono archiviati solo negli Stati Uniti. I dati di log per Microsoft Azure Germania e Microsoft Azure gestito da 21Vianet vengono sempre archiviati nel rispettivo cloud. I dati di log del cloud Microsoft per enti pubblici vengono sempre archiviati nella Stati Uniti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle informazioni utente raccolte da Azure Multi-Factor Authentication basato sul cloud e dal server Azure Multi-Factor Authentication, vedere [Raccolta di dati utente di Azure Multi-Factor Authentication](howto-mfa-reporting-datacollection.md).
