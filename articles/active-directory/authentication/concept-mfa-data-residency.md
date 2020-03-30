---
title: Residenza dei dati di Azure Multi-Factor AuthenticationAzure Multi-Factor Authentication data residency
description: Informazioni sui dati personali e organizzativi archiviati da Azure Multi-Factor Authentication sull'utente e sull'utente e sui dati che rimangono nel paese di origine.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4556ece7faaacb8657a1012344f2263ee84214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480138"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Data residency and customer data for Azure Multi-Factor Authentication

I dati dei clienti vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione durante la sottoscrizione a un servizio Microsoft Online, ad esempio Office 365 e Azure.Customer data is stored by Azure AD in a geographical location based on the address provided by your organization when sottocribing for a Microsoft Online service such as Office 365 and Azure. Per informazioni sulla posizione in cui sono archiviati i dati dei clienti, è possibile utilizzare la sezione [Dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) del Centro protezione Microsoft.

Azure Multi-Factor Authentication basato su cloud e Azure Multi-Factor Authentication Server elaborano e archiviano una certa quantità di dati personali e dati dell'organizzazione. In questo articolo vengono descritti cosa e dove vengono archiviati i dati.

Le seguenti attività di Multi-Factor Authentication attualmente provengono dai data center degli Stati Uniti, ad eccezione dei casi indicati:

* L'autenticazione a due fattori tramite chiamate telefoniche o SMS in genere proviene dai data center degli Stati Uniti e viene instradata da provider globali.
    * Le richieste di autenticazione utente di uso generale provenienti da altre aree, ad esempio Europa o Australia, vengono attualmente elaborate dai data center di tale area. Altri eventi, ad esempio la reimpostazione della password self-service, gli eventi B2C di Azure o gli scenari ibridi che usano l'estensione Server dei criteri di rete o l'adapter ADFS, vengono tutti elaborati dai data center degli Stati Uniti.Other events such as self-service password resets, Azure B2C events, or hybrid scenarios using NPS Extension or AD FS adapter, are all currently processed by US datacenters.
* Le notifiche push tramite l'app Microsoft Authenticator provengono dai data center degli Stati Uniti. Inoltre, i servizi specifici del fornitore di dispositivi possono entrare in gioco anche da diverse regioni.
* I codici OATH sono in genere attualmente convalidati negli Stati Uniti.
    * Anche in questo caso, gli eventi di autenticazione utente di uso generale che hanno origine in altre aree, ad esempio Europa o Australia, vengono elaborati dai data center in tale area. Gli eventi aggiuntivi vengono attualmente elaborati dai data center statunitensi.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dati personali archiviati da Azure Multi-Factor Authentication

I dati personali sono informazioni a livello di utente associate a una persona specifica. I seguenti archivi dati contengono informazioni personali:

* Utenti bloccati
* Utenti ignorati
* Richieste di modifica del token del dispositivo Microsoft AuthenticatorMicrosoft Authenticator device change requests
* Report attività di Multi-Factor Authentication
* Attivazioni di Microsoft Authenticator

Queste informazioni vengono conservate per 90 giorni.

Azure Multi-Factor Authentication non registra i dati personali, ad esempio nome utente, numero di telefono o indirizzo IP, ma è disponibile un *UserObjectId* che identifica i tentativi di Multi-Factor Authentication per gli utenti. I dati del registro vengono archiviati per 30 giorni.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Per i cloud pubblici di Azure, esclusi l'autenticazione B2C di Azure, l'estensione Server dei criteri di rete e l'adapter ADFS di Windows Server 2016 o 2019, vengono archiviati i dati personali seguenti:For Azure public clouds, excluding Azure B2C authentication, NPS Extension, and Windows Server 2016 or 2019 AD FS Adapter, the following personal data is stored:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei registri di Multi-Factor Authentication     |
| SMS unidirezionali                          | Nei registri di Multi-Factor Authentication     |
| Chiamata vocale                           | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di frode |
| Notifica Microsoft Authenticator | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di frode<br />Richieste di modifica quando cambia il token del dispositivo Microsoft AuthenticatorChange requests when Microsoft Authenticator device token changes |

> [!NOTE]
> L'archivio dati del report attività di Multi-Factor Authentication viene archiviato negli Stati Uniti per tutti i cloud, indipendentemente dall'area che elabora la richiesta di autenticazione. Microsoft Azure Germania, Microsoft Azure gestito da 21Vianet e Microsoft Government Cloud dispongono di archivi dati indipendenti separati dagli archivi dati delle aree cloud pubbliche, tuttavia questi dati vengono sempre archiviati negli Stati Uniti.

Per Microsoft Azure per enti pubblici, Microsoft Azure Germania, Microsoft Azure gestito da 21Vianet, autenticazione B2C di Azure, estensione NPS e adattatore ADFS di Windows Server 2016 o 2019, vengono archiviati i dati personali seguenti:

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication |
| SMS unidirezionali                          | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication |
| Chiamata vocale                           | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di frode |
| Notifica Microsoft Authenticator | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di frode<br />Richieste di modifica quando cambia il token del dispositivo Microsoft AuthenticatorChange requests when Microsoft Authenticator device token changes |

### <a name="multi-factor-authentication-server"></a>Server Multi-Factor Authentication

Se si distribuisce ed esegue Azure Multi-Factor Authentication Server, vengono archiviati i dati personali seguenti:If you deploy and run Azure Multi-Factor Authentication Server, the following personal data is stored:

> [!IMPORTANT]
> A partire dal 1 luglio 2019, Microsoft non offrirà più Multi-Factor Authentication Server per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dai propri utenti devono usare L'autenticazione a più fattori di Azure basata su cloud. I clienti esistenti che hanno attivato Multi-Factor Authentication Server prima del 1 luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

| Tipo di evento                           | Tipo di archivio dati |
|--------------------------------------|-----------------|
| Token OATH                           | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication |
| SMS unidirezionali                          | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication |
| Chiamata vocale                           | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di frode |
| Notifica Microsoft Authenticator | Nei registri di Multi-Factor Authentication<br />Archivio dati del report attività di Multi-Factor Authentication<br />Utenti bloccati in caso di frode<br />Richieste di modifica quando cambia il token del dispositivo Microsoft AuthenticatorChange requests when Microsoft Authenticator device token changes |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dati dell'organizzazione archiviati da Azure Multi-Factor AuthenticationOrganizational data stored by Azure Multi-Factor Authentication

I dati dell'organizzazione sono informazioni a livello di tenant che potrebbero esporre la configurazione o la configurazione dell'ambiente. Le impostazioni del tenant delle pagine di Autenticazione a più fattori del portale di Azure seguenti possono archiviare i dati dell'organizzazione, ad esempio le soglie di blocco o le informazioni sull'ID chiamante per le richieste di autenticazione telefonica in arrivo:Tenant settings from the following Azure portal Multi-Factor Authentication pages may store organizational data such as lockout thresholds or caller ID information for incoming phone authentication requests:

* Blocco dell'account
* Avviso di illecito
* Notifiche
* Impostazioni telefonata

E per Azure Multi-Factor Authentication Server, le pagine del portale di Azure seguenti possono contenere dati organizzativi:And for Azure Multi-Factor Authentication Server, the following Azure portal pages may contain organizational data:

* Impostazioni del server
* Bypass monouso
* Regole di memorizzazione nella cache
* Stato del server Multi-Factor Authentication

## <a name="log-data-location"></a>Posizione dei dati di registro

La posizione di archiviazione delle informazioni di registro dipende dall'area in cui vengono elaborate. La maggior parte delle aree geografiche dispone di funzionalità native di Azure Multi-Factor Authentication, pertanto i dati di log vengono archiviati nella stessa area che elabora la richiesta di Autenticazione a più fattori. Nelle aree geografiche senza il supporto nativo di Azure Multi-Factor Authentication, vengono servite dalle aree geografiche degli Stati Uniti o in Europa e i dati di log vengono archiviati nella stessa area che elabora la richiesta di Autenticazione a più fattori.

Alcuni dati del log di autenticazione di base vengono archiviati solo negli Stati Uniti.Some core authentication log data is only stored in the United States. Microsoft Azure Germania e Microsoft Azure gestito da 21Vianet sono sempre archiviati nel rispettivo cloud. I dati di log di Microsoft Government Cloud vengono sempre archiviati negli Stati Uniti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle informazioni utente raccolte da Azure Multi-Factor Authentication basato su cloud e Azure Multi-Factor Authentication Server, vedere Raccolta dati utente di Azure Multi-Factor Authentication.For more information about what user information is collected by cloud-based Azure Multi-Factor Authentication and Azure Multi-Factor Authentication Server, see [Azure Multi-Factor Authentication user data collection](howto-mfa-reporting-datacollection.md).
