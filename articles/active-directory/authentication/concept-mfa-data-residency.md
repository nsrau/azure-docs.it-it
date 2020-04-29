---
title: Residenza dei dati di Azure Multi-Factor Authentication
description: Informazioni sui dati personali e aziendali che Azure Multi-Factor Authentication archivia sugli utenti e sugli utenti e sui dati che rimangono all'interno del paese di origine.
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
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309807"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Residenza dei dati e dati dei clienti per Azure Multi-Factor Authentication

I dati dei clienti vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione quando si sottoscrive un servizio online Microsoft, ad esempio Office 365 e Azure. Per informazioni sul percorso di archiviazione dei dati dei clienti, è possibile usare la sezione [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) di Microsoft Trust Center.

Azure Multi-Factor Authentication e Azure server Multi-Factor Authentication basati sul cloud e archiviare una quantità di dati personali e di dati aziendali. Questo articolo descrive cosa e dove vengono archiviati i dati.

Le seguenti attività di Multi-Factor Authentication attualmente provengono da Data Center statunitensi, tranne nei casi indicati:

* L'autenticazione a due fattori che usa chiamate telefoniche o SMS ha in genere origine da Data Center statunitensi e viene instradata dai provider globali.
    * Le richieste di autenticazione utente per utilizzo generico da altre aree, ad esempio Europa o Australia, sono attualmente elaborate dai data center in tale area. Altri eventi, ad esempio la reimpostazione della password self-service, gli eventi di Azure B2C o gli scenari ibridi che usano l'estensione NPS o AD FS adapter, sono attualmente elaborati dai data center statunitensi.
* Le notifiche push che usano l'app Microsoft Authenticator provengono dai data center statunitensi. Inoltre, i servizi specifici del fornitore del dispositivo possono entrare in gioco da aree diverse.
* I codici di GIURAmento vengono in genere convalidati negli Stati Uniti
    * Anche in questo caso, gli eventi di autenticazione utente generici che hanno origine in altre aree, ad esempio Europa o Australia, vengono elaborati dai Data Center di tale area. Gli eventi aggiuntivi vengono attualmente elaborati dai data center statunitensi.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dati personali archiviati da Azure Multi-Factor Authentication

I dati personali sono informazioni a livello di utente associate a un utente specifico. Gli archivi dati seguenti contengono informazioni personali:

* Utenti bloccati
* Utenti ignorati
* Richieste di modifica del token del dispositivo Microsoft Authenticator
* Report attività Multi-Factor Authentication
* Attivazioni Microsoft Authenticator

Queste informazioni vengono conservate per 90 giorni.

Azure Multi-Factor Authentication non registra i dati personali, ad esempio nome utente, numero di telefono o indirizzo IP, ma è presente un *UserObjectId* che identifica multi-factor authentication tentativi agli utenti. I dati del log vengono archiviati per 30 giorni.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Per i cloud pubblici di Azure, ad eccezione dell'autenticazione di Azure B2C, dell'estensione NPS e di Windows Server 2016 o 2019 AD FS adapter vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei log Multi-Factor Authentication     |
| SMS unidirezionale                          | Nei log Multi-Factor Authentication     |
| Chiamata vocale                           | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication<br />Utenti bloccati in caso di frode segnalata |
| Notifica Microsoft Authenticator | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication<br />Utenti bloccati in caso di frode segnalata<br />Richieste di modifica quando viene modificato Microsoft Authenticator token del dispositivo |

> [!NOTE]
> L'archivio dati del report attività Multi-Factor Authentication viene archiviato nel Stati Uniti per tutti i cloud, indipendentemente dall'area che elabora la richiesta di autenticazione. Microsoft Azure Germania, Microsoft Azure gestite da 21Vianet e Microsoft Government cloud hanno archivi dati indipendenti separati dagli archivi dati dell'area del cloud pubblico, tuttavia questi dati vengono sempre archiviati nella Stati Uniti.

Per Microsoft Azure per enti pubblici, Microsoft Azure Germania, Microsoft Azure gestite da 21Vianet, autenticazione di Azure B2C, estensione NPS e Windows Server 2016 o 2019 AD FS adapter, vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication |
| SMS unidirezionale                          | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication |
| Chiamata vocale                           | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication<br />Utenti bloccati in caso di frode segnalata |
| Notifica Microsoft Authenticator | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication<br />Utenti bloccati in caso di frode segnalata<br />Richieste di modifica quando viene modificato Microsoft Authenticator token del dispositivo |

### <a name="multi-factor-authentication-server"></a>Server Multi-Factor Authentication

Se si distribuisce ed esegue server Multi-Factor Authentication di Azure, vengono archiviati i dati personali seguenti:

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non fornirà più server Multi-Factor Authentication per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dagli utenti devono usare Azure Multi-Factor Authentication basato sul cloud. I clienti esistenti che hanno attivato server Multi-Factor Authentication prima del 1 ° luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication |
| SMS unidirezionale                          | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication |
| Chiamata vocale                           | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication<br />Utenti bloccati in caso di frode segnalata |
| Notifica Microsoft Authenticator | Nei log Multi-Factor Authentication<br />Archivio dati del report attività Multi-Factor Authentication<br />Utenti bloccati in caso di frode segnalata<br />Richieste di modifica quando viene modificato Microsoft Authenticator token del dispositivo |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dati aziendali archiviati da Azure Multi-Factor Authentication

I dati dell'organizzazione sono informazioni a livello di tenant che potrebbero esporre la configurazione o l'installazione dell'ambiente. Le impostazioni del tenant dalle seguenti portale di Azure Multi-Factor Authentication pagine possono archiviare dati aziendali, ad esempio soglie di blocco o informazioni sull'ID chiamante per le richieste di autenticazione del telefono in arrivo:

* Blocco dell'account
* Avviso di illecito
* Notifiche
* Impostazioni telefonata

Per Azure server Multi-Factor Authentication, le pagine portale di Azure seguenti possono contenere dati aziendali:

* Impostazioni del server
* Bypass monouso
* Regole di memorizzazione nella cache
* Stato server Multi-Factor Authentication

## <a name="log-data-location"></a>Percorso dei dati di log

La posizione in cui vengono archiviate le informazioni del log dipende dall'area in cui vengono elaborate. La maggior parte delle aree geografiche dispone di funzionalità native di Multi-Factor Authentication di Azure, quindi i dati di log vengono archiviati nella stessa area che elabora la richiesta di Multi-Factor Authentication. Le aree geografiche senza supporto nativo di Azure Multi-Factor Authentication sono gestite dalle aree geografiche Stati Uniti o Europe e i dati di log vengono archiviati nella stessa area che elabora la richiesta di Multi-Factor Authentication.

Alcuni dati del log di autenticazione di base vengono archiviati solo nel Stati Uniti. Microsoft Azure Germania e Microsoft Azure gestiti da 21Vianet vengono sempre archiviati nel rispettivo cloud. I dati di log del cloud di Microsoft Government vengono sempre archiviati nella Stati Uniti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle informazioni sugli utenti raccolte da Azure Multi-Factor Authentication e server Multi-Factor Authentication di Azure basati sul cloud, vedere [raccolta di dati utente multi-factor authentication Azure](howto-mfa-reporting-datacollection.md).
