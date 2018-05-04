---
title: Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP - Identificazione e autenticazione
description: Automazione di applicazioni Web per FedRAMP - Identificazione e autenticazione
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="identification-and-authentication-ia"></a>Identificazione e autenticazione (IA, Identification and Authentication)

## <a name="nist-800-53-control-ia-1"></a>Controllo IA-1 NIST 800-53

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Procedure e criteri di identificazione e autenticazione

**IA-1** L'organizzazione sviluppa, documenta e distribuisce a [assegnazione: ruoli o personale definiti dall'organizzazione] criteri di identificazione e autenticazione relativi allo scopo, all'ambito, ai ruoli, alle responsabilità, all'impegno nella gestione, al coordinamento tra le entità organizzative e alla conformità. Distribuisce inoltre le procedure per facilitare l'implementazione dei criteri di identificazione e di autenticazione e dei relativi controlli. Esamina e aggiorna i criteri di identificazione e autenticazione correnti [assegnazione: frequenza definita dall'organizzazione] e le procedure di identificazione e autenticazione [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure di identificazione e autenticazione a livello aziendale del cliente possono essere sufficienti per effettuare questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-2"></a>Controllo IA-2 NIST 800-53

#### <a name="identification-and-authentication-organizational-users"></a>Identificazione e autenticazione (utenti dell'organizzazione)

**IA-2** Il sistema informativo identifica in modo univoco e autentica gli utenti dell'organizzazione (o elabora azioni per conto di utenti dell'organizzazione).

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli account creati da questo progetto hanno identificatori univoci. Gli account predefiniti con identificatori non univoci vengono disabilitati o rimossi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-1"></a>Controllo IA-2 (1) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account con privilegi

**IA-2 (1)** Il sistema informativo implementa l'autenticazione a più fattori per l'accesso alla rete per gli account con privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'implementazione dell'autenticazione a più fattori per l'accesso alla rete per gli account con privilegi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-2"></a>Controllo IA-2 (2) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account senza privilegi

**IA-2 (2)** Il sistema informativo implementa l'autenticazione a più fattori per l'accesso alla rete per gli account senza privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'implementazione dell'autenticazione a più fattori per l'accesso alla rete per gli account senza privilegi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-3"></a>Controllo IA-2 (3) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso locale per gli account con privilegi

**IA-2 (3)** Il sistema informativo implementa l'autenticazione a più fattori per l'accesso locale per gli account con privilegi.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non hanno l'accesso locale alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure non consente l'accesso locale a meno che non sia necessario l'accesso fisico. L'accesso locale come amministratore deve essere usato solo per la risoluzione di problemi in istanze in cui il server riscontra problemi di rete o se l'autenticazione del dominio non funziona. <br /> Azure implementa l'autenticazione a più fattori per l'accesso locale con meccanismi di controllo di accesso necessario per l'accesso fisico all'ambiente. Le chat nei Data Center di Azure che contengono tutti i sistemi di infrastruttura di Azure all'interno del sistema sono protette tramite diversi meccanismi di protezione fisica, incluso il requisito dell'accesso attraverso smart card aziendali e dispositivi biometrici. Entrambe le forme di autenticazione sono necessarie per l'accesso fisico nel punto di ingresso alle condivisioni di percorso dei data center di Azure. |


 ### <a name="nist-800-53-control-ia-2-4"></a>Controllo IA-2 (4) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso locale per gli account senza privilegi

**IA-2 (4)** Il sistema informativo implementa l'autenticazione a più fattori per l'accesso locale per gli account senza privilegi.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non hanno l'accesso locale alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure considera tutti gli account di Microsoft Azure per enti pubblici usati dal personale di Microsoft Azure per enti pubblici come con privilegi. L'autenticazione a più fattori è implementata per tutti gli account del personale di Microsoft Azure per enti pubblici tramite smart card e i codici PIN, ed è incluso l'accesso locale. |


 ### <a name="nist-800-53-control-ia-2-5"></a>Controllo IA-2 (5) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Autenticazione di gruppi

**IA-2 (5)** L'organizzazione richiede agli utenti di essere autenticati con un autenticatore individuale quando viene usato un autenticatore di gruppo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nessun account condiviso/di gruppo è consentito nelle risorse distribuite da questo progetto. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-8"></a>Controllo IA-2 (8) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account con privilegi - Resistente al replay

**IA-2 (8)** Il sistema informativo implementa meccanismi di autenticazione resistenti agli attacchi di tipo replay per l'accesso alla rete per gli account con privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso alle risorse distribuite da questo progetto è protetto da attacchi di tipo replay dalla funzionalità incorporata Kerberos di Azure Active Directory, Active Directory e il sistema operativo Windows. Nell'autenticazione Kerberos l'autenticatore inviato dal client contiene dati aggiuntivi, ad esempio un elenco di indirizzi IP crittografato, timestamp di client e durata del ticket. Se un pacchetto viene riprodotto, viene controllato il timestamp. Se il timestamp è precedente o uguale a quello di un autenticatore precedente, il pacchetto viene rifiutato. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-9"></a>Controllo IA-2 (9) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso alla rete per gli account senza privilegi - Resistente al replay

**IA-2 (9)** Il sistema informativo implementa il meccanismo di autenticazione resistente al replay per l'accesso alla rete per gli account senza privilegi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso alle risorse distribuite da questo progetto è protetto da attacchi di tipo replay dalla funzionalità incorporata Kerberos di Azure Active Directory, Active Directory e il sistema operativo Windows. Nell'autenticazione Kerberos l'autenticatore inviato dal client contiene dati aggiuntivi, ad esempio un elenco di indirizzi IP crittografato, timestamp di client e durata del ticket. Se un pacchetto viene riprodotto, viene controllato il timestamp. Se il timestamp è precedente o uguale a quello di un autenticatore precedente, il pacchetto viene rifiutato. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-11"></a>Controllo IA-2 (11) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accesso remoto - Dispositivo separato

**IA-2 (11)** Il sistema informativo implementa l'autenticazione a più fattori per l'accesso remoto per gli account con privilegi e senza privilegi in modo che uno dei fattori venga fornito da un dispositivo separato dal sistema a cui si sta accedendo e che il dispositivo soddisfi i [ assegnazione: requisiti di complessità del meccanismo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'implementazione dell'autenticazione a più fattori per l'accesso remoto alle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-2-12"></a>Controllo IA-2 (12) NIST 800-53

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identificazione e autenticazione (utenti dell'organizzazione) | Accettazione delle credenziali PIV

**IA-2 (12)** Il sistema informativo accetta e verifica elettronicamente le credenziali di verifica dell'identità personale (PIV, Personal Identity Verification).

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'accettazione e verifica delle credenziali di verifica dell'identità personale (PIV, Personal Identity Verification). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-3"></a>Controllo IA-3 NIST 800-53

#### <a name="device-identification-and-authentication"></a>Identificazione e autenticazione dei dispositivi

**IA-3** Il sistema informativo identifica e autentica in modo univoco i [assegnazione: dispositivi e/o tipi di dispositivo specifici definiti dall'organizzazione] prima di stabilire una [selezione (una o più): connessione di rete, locale, remota].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'implementazione dell'autenticazione e identificazione dei dispositivi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-4a"></a>Controllo IA-4.a NIST 800-53

#### <a name="identifier-management"></a>Gestione identificatori

**IA-4** L'organizzazione gestisce gli identificatori di sistema informativo ricevendo l'autorizzazione da [assegnazione: ruoli o personale definiti dall'organizzazione] per assegnare un identificatore singolo, di gruppo, di ruolo o di dispositivo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli identificatori (ad esempio, singoli utenti, gruppi, ruoli e dispositivi) per le risorse del cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-4b"></a>Controllo IA-4.b NIST 800-53

#### <a name="identifier-management"></a>Gestione identificatori

**IA-4** L'organizzazione gestisce gli identificatori del sistema informativo selezionando un identificatore che identifica un individuo, un gruppo, un ruolo o un dispositivo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Durante la distribuzione questo progetto richiede gli identificatori specificati dal cliente per i singoli account.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-4c"></a>Controllo IA-4.c NIST 800-53

#### <a name="identifier-management"></a>Gestione identificatori

**IA-4.c** L'organizzazione gestisce gli identificatori del sistema informativo assegnando l'identificatore all'individuo, gruppo, ruolo o dispositivo a cui è destinato.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli identificatori (ad esempio, singoli utenti, gruppi, ruoli e dispositivi) per le risorse del cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-4d"></a>Controllo IA-4.d NIST 800-53

#### <a name="identifier-management"></a>Gestione identificatori

**IA-4.d** L'organizzazione gestisce gli identificatori del sistema informativo in modo da impedirne il riutilizzo per [assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Ad Active Directory e agli account locali del sistema operativo di Windows viene assegnato un ID di sicurezza univoco (SID). Agli account di Azure Active Directory viene assegnato un ID di oggetto univoco globale. Questi ID univoci non sono soggetti a riutilizzo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-4e"></a>Controllo IA-4.e NIST 800-53

#### <a name="identifier-management"></a>Gestione identificatori

**IA-4.e** L'organizzazione gestisce gli identificatori del sistema informativo in modo da disabilitare l'identificatore dopo [assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto implementa un'attività pianificata per Active Directory per disabilitare automaticamente gli account dopo 35 giorni di inattività. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-4-4"></a>Controllo IA-4 (4) NIST 800-53

#### <a name="identifier-management--identify-user-status"></a>Gestione identificatori | Identificazione dello stato utente

**IA-4 (4)** L'organizzazione gestisce gli identificatori individuali identificando in modo univoco ogni individuo come [assegnazione: caratteristiche definite dall'organizzazione per l'identificazione dello stato degli individui].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Supporto di Azure Active Directory e di Active Directory che indica terzisti, fornitori e altri tipi di utente usando le convenzioni di denominazione applicate ai relativi identificatori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5a"></a>Controllo IA-5.a NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.a** L'organizzazione gestisce gli autenticatori del sistema informativo verificando, come parte della distribuzione iniziale degli autenticatori, l'identità del singolo, gruppo, ruolo o dispositivo che riceve l'autenticatore.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli autenticatori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5b"></a>Controllo IA-5.b NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.b** L'organizzazione gestisce gli autenticatori del sistema informativo stabilendo il contenuto iniziale degli autenticatori definiti dall'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Tutto il contenuto iniziale degli autenticatori degli account creati da questo progetto soddisfa i requisiti indicati in IA-5 (1) verificati quando specificato dall'utente durante la distribuzione.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5c"></a>Controllo IA-5.c NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.c** L'organizzazione gestisce gli autenticatori del sistema informativo assicurando che abbiano un meccanismo sufficientemente complesso per l'uso previsto.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli autenticatori usati da questo progetto soddisfano i requisiti di complessità richiesti da FedRAMP. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5d"></a>Controllo IA-5.d NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.d** L'organizzazione gestisce gli autenticatori del sistema informativo tramite la definizione e l'implementazione di procedure amministrative per la distribuzione iniziale degli autenticatori, per quelli persi/compromessi o danneggiati e per la revoca degli autenticatori.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli autenticatori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5e"></a>Controllo IA-5.e NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.e** L'organizzazione gestisce gli autenticatori del sistema informativo modificandone il contenuto predefinito prima dell'installazione nel sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Tutti i valori predefiniti degli autenticatori per i componenti di questo progetto sono stati modificati. Gli autenticatori sono specificati dal cliente durante la distribuzione di questa soluzione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5f"></a>Controllo IA-5.f NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.f** L'organizzazione gestisce gli autenticatori del sistema informativo mediante la definizione di limiti di durata minima e massima e delle condizioni di riutilizzo degli autenticatori.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della gestione degli autenticatori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5g"></a>Controllo IA-5.g NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.g** L'organizzazione gestisce gli autenticatori del sistema informativo modificando/aggiornando gli autenticatori [assegnazione: periodo di tempo definito dall'organizzazione per tipo di autenticatore].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Vengono stabiliti e configurati criteri di gruppo per implementare le restrizioni di durata delle password (60 giorni). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5h"></a>Controllo IA-5.h NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.h** L'organizzazione gestisce gli autenticatori del sistema informativo proteggendone i contenuti per impedire la divulgazione e la modifica non autorizzate.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto implementa Key Vault per proteggere i contenuti degli autenticatori da divulgazione e modifica non autorizzate. Gli autenticatori seguenti vengono archiviati in Key Vault: la password di Azure per la distribuzione degli account, la password dell'amministratore delle macchine virtuali, la password dell'account del servizio SQL Server. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5i"></a>Controllo IA-5.i NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.i** L'organizzazione gestisce gli autenticatori del sistema informativo richiedendo agli utenti di prendere e implementare nei dispositivi misure di protezione specifiche per proteggere gli autenticatori.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto implementa Key Vault per proteggere i contenuti degli autenticatori da divulgazione e modifica non autorizzate. Gli autenticatori seguenti vengono archiviati in Key Vault: la password di Azure per la distribuzione degli account, la password dell'amministratore delle macchine virtuali, la password dell'account del servizio SQL Server. Key Vault esegue la crittografia delle chiavi e dei segreti, come le chiavi di autenticazione, le chiavi dell'account di archiviazione, le chiavi di crittografia dei dati e le password, usando chiavi protette da moduli di protezione hardware (HSM). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-5j"></a>Controllo IA-5.j NIST 800-53

#### <a name="authenticator-management"></a>Gestione autenticatori

**IA-5.j** L'organizzazione gestisce gli autenticatori del sistema informativo modificando gli autenticatori per gli account di gruppo/ruolo quando cambia l'appartenenza a tali account.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nessun account condiviso/di gruppo è consentito nelle risorse distribuite da questo progetto. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-1a"></a>Controllo IA-5 (1).a NIST 800-53

#### <a name="authenticator-management--password-based-authentication"></a>Gestione autenticatori | Autenticazione basata su password

**IA-5 (1)** Il sistema informativo per l'autenticazione basata su password applica la complessità minima delle password di [assegnazione: requisiti definiti dall'organizzazione per la distinzione tra maiuscole/minuscole, numero di caratteri, combinazione di lettere maiuscole, lettere minuscole lettere, numeri e caratteri speciali, tra cui i requisiti minimi per ogni tipo].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Vengono stabiliti e configurati criteri di gruppo per applicare i requisiti di complessità delle password per gli account locali delle macchine virtuali e per gli account AD.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-1b"></a>Controllo IA-5 (1) b NIST 800-53

#### <a name="authenticator-management--password-based-authentication"></a>Gestione autenticatori | Autenticazione basata su password

**B IA-5 (1)** Il sistema informativo per l'autenticazione basata su password applica il numero minimo di caratteri modificati seguente quando vengono create nuove password: [assegnazione: numero definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare l'autenticazione basata su password nelle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-1c"></a>Controllo IA-5 (1).c NIST 800-53

#### <a name="authenticator-management--password-based-authentication"></a>Gestione autenticatori | Autenticazione basata su password

**IA-5 (1).c** Il sistema informativo per l'autenticazione basata su password archivia e trasmette solo password protette tramite crittografia.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Directory di Azure viene usata per assicurare che tutte le password siano protette tramite crittografia durante l'archiviazione e la trasmissione. Come parte della funzionalità di sicurezza incorporata viene automaticamente eseguito l'hashing delle password memorizzate da Active Directory e localmente sulle macchine virtuali di Windows distribuite. Le sessioni di autenticazione del desktop remoto sono protette tramite TLS per assicurare la protezione degli autenticatori durante la trasmissione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-1d"></a>Controllo IA-5 (1).d NIST 800-53

#### <a name="authenticator-management--password-based-authentication"></a>Gestione autenticatori | Autenticazione basata su password

**IA-5 (1).d** Il sistema informativo per l'autenticazione basata su password applica le restrizioni di durata minima e massima delle password di [assegnazione: numeri definiti dall'organizzazione per la durata minima, durata massima].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Vengono stabiliti e configurati criteri di gruppo per applicare restrizioni a livello di password che applicano una restrizione di durata minima (1 giorno) e massima (60 giorni) per gli account locali e gli account AD. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-1e"></a>Controllo IA-5 (1).e NIST 800-53

#### <a name="authenticator-management--password-based-authentication"></a>Gestione autenticatori | Autenticazione basata su password

**IA-5 (1).e** Il sistema informativo per l'autenticazione basata su password impedisce il riutilizzo delle password per [assegnazione: numero definito dall'organizzazione] generazioni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un controller di dominio cui vengono aggiunte tutte le macchine virtuali distribuite. Vengono stabiliti e configurati criteri di gruppo per applicare restrizioni sulle condizioni di riutilizzo (24 password) per gli account locali e gli account AD. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-1f"></a>Controllo IA-5 (1).f NIST 800-53

#### <a name="authenticator-management--password-based-authentication"></a>Gestione autenticatori | Autenticazione basata su password

**IA-5 (1).f** Il sistema informativo per l'autenticazione basata su password consente di usare una password temporanea per gli accessi di sistema con una modifica immediata in una password permanente.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory viene usato per gestire il controllo dell'accesso al sistema informativo. Nelle fasi iniziali della creazione di un account o durante la generazione di una password temporanea, Azure Active Directory viene usato per richiedere che l'utente cambi la password all'accesso successivo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-2a"></a>Controllo IA-5 (2).a NIST 800-53

#### <a name="authenticator-management--pki-based-authentication"></a>Gestione autenticatori | Autenticazione basata su infrastruttura a chiave pubblica (PKI)

**IA-5 (2).a** Il sistema informativo per l'autenticazione basata su infrastruttura a chiave pubblica (PKI) convalida le certificazioni mediante la costruzione e la verifica di un percorso di certificazione a un ancoraggio attendibile che include il controllo delle informazioni sullo stato del certificato.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare l'autenticazione basata su infrastruttura a chiave pubblica (PKI) nelle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-2b"></a>Controllo IA-5 (2).b NIST 800-53

#### <a name="authenticator-management--pki-based-authentication"></a>Gestione autenticatori | Autenticazione basata su infrastruttura a chiave pubblica (PKI)

**IA-5 (2) b**Il sistema informativo per l'autenticazione basata su infrastruttura a chiave pubblica (PKI) applica gli accessi autorizzati alla chiave privata corrispondente.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare l'autenticazione basata su infrastruttura a chiave pubblica (PKI) nelle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-2c"></a>Controllo IA-5 (2).c NIST 800-53

#### <a name="authenticator-management--pki-based-authentication"></a>Gestione autenticatori | Autenticazione basata su infrastruttura a chiave pubblica (PKI)

**IA-5 (2).c** Il sistema informativo per l'autenticazione basata su infrastruttura a chiave pubblica (PKI) esegue il mapping dell'identità autenticata all'account individuale o del gruppo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare l'autenticazione basata su infrastruttura a chiave pubblica (PKI) nelle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-2d"></a>Controllo IA-5 (2).d NIST 800-53

#### <a name="authenticator-management--pki-based-authentication"></a>Gestione autenticatori | Autenticazione basata su infrastruttura a chiave pubblica (PKI)

**IA-5 (2).d** Il sistema informativo per l'autenticazione basata su infrastruttura a chiave pubblica (PKI) implementa una cache locale di revoca di dati per supportare l'individuazione e la convalida del percorso in caso di impossibilità di accedere alle informazioni di revoca attraverso la rete.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare l'autenticazione basata su infrastruttura a chiave pubblica (PKI) nelle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-3"></a>Controllo IA-5 (3) NIST 800-53

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Gestione autenticatori | Registrazione di terze parti attendibili o di persona

**IA-5 (3)** L'organizzazione richiede che il processo di registrazione per la ricezione [assegnazione: tipi di autenticatori e/o autenticatori specifici definiti dall'organizzazione] sia effettuato [Selezione: di persona; da terze parti attendibili] da [assegnazione: autorità di registrazione definita dall'organizzazione] con l'autorizzazione di [assegnazione: ruoli o personale definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della registrazione degli autenticatori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-4"></a>Controllo IA-5 (4) NIST 800-53

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Gestione autenticatori | Supporto automatizzato per la determinazione della complessità della password

**IA-5 (4)** L'organizzazione usa strumenti automatizzati per determinare se gli autenticatori delle password sono sufficientemente complessi da soddisfare i [assegnazione: requisiti definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Gli account utente distribuiti con questo progetto includono AD e gli account utente locali. Entrambi forniscono meccanismi che forzano la conformità con i requisiti stabiliti per la creazione delle password iniziali e per la modifica delle password. Azure Active Directory è lo strumento automatico usato per determinare se gli autenticatori di password sono sufficientemente complessi da soddisfare le restrizioni relative a lunghezza, complessità, rotazione e durata delle password definite in IA-5 (1). Azure Active Directory assicura che la complessità dell'autenticatore di password nella fase di creazione rispetti questi standard. Le password specificate dai clienti usate per distribuire questa soluzione vengono controllate per verificare che soddisfino i requisiti di complessità. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-6"></a>Controllo IA-5 (6) NIST 800-53

#### <a name="authenticator-management--protection-of-authenticators"></a>Gestione autenticatori | Protezione degli autenticatori

**IA-5 (6)** L'organizzazione protegge gli autenticatori in modo proporzionato alla categoria di sicurezza delle informazioni a cui l'autenticatore consente l'accesso.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della protezione degli autenticatori. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-7"></a>Controllo IA-5 (7) NIST 800-53

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Gestione autenticatori | Nessun autenticatore statico non crittografato incorporato

**IA-5 (7)** L'organizzazione garantisce che nessun autenticatore statico non crittografato venga incorporato in applicazioni o in script di accesso o archiviato in tasti funzione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto non fa alcun uso di autenticatori statici non crittografati incorporati in applicazioni, script di accesso o tasti funzione. Qualsiasi script o applicazione che usa un autenticatore effettua una chiamata a un contenitore di Azure Key Vault prima di ogni uso. L'accesso ai contenitori di Azure Key Vault viene controllato e ciò consente il rilevamento di violazioni del divieto, nel caso in cui un account del servizio venga usato per accedere a un sistema senza una chiamata corrispondente al contenitore di Azure Key Vault. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-8"></a>Controllo IA-5 (8) NIST 800-53

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Gestione autenticatori | Più account del sistema informativo

**IA-5 (8)** L'organizzazione implementa [assegnazione: misure di sicurezza per la protezione definite dall'organizzazione] per la gestione dei rischi di compromissione a causa di singoli utenti con account su più sistemi informativi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente può affidarsi alle misure di sicurezza per la protezione a livello aziendale per gestire i rischi associati a singoli utenti con account su più sistemi. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-11"></a>Controllo IA-5 (11) NIST 800-53

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Gestione autenticatori | Autenticazione basata su token hardware

**IA-5 (11)** Il sistema informativo per l'autenticazione basata su token hardware, usa i meccanismi che soddisfano [assegnazione: requisiti di qualità del token definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare meccanismi che soddisfano i requisiti di qualità dell'autenticazione basata su token hardware. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-5-13"></a>Controllo IA-5 (13) NIST 800-53

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Gestione autenticatori | Scadenza degli autenticatori memorizzati nella cache

**IA-5 (13)** Il sistema informativo impedisce l'uso degli autenticatori memorizzati nella cache dopo [assegnazione: periodo di tempo definito dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nessuna risorsa distribuita da questo progetto è configurata per consentire l'uso degli autenticatori memorizzati nella cache. L'autenticazione in macchine virtuali distribuite richiede l'immissione di un autenticatore al momento dell'autenticazione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-6"></a>Controllo IA-6 NIST 800-53

#### <a name="authenticator-feedback"></a>Commenti e suggerimenti sugli autenticatori

**IA-6** Il sistema informativo non consente di visualizzare i commenti e i suggerimenti delle informazioni di autenticazione durante il processo di autenticazione per proteggere le informazioni da possibile sfruttamento/uso da parte di utenti non autorizzati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso alle risorse distribuite da questo progetto avviene da Desktop remoto e si basa sull'autenticazione di Windows. Il comportamento predefinito delle sessioni di autenticazione di Windows nasconde le password mentre si inseriscono durante una sessione di autenticazione.  |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-7"></a>Controllo IA-7 NIST 800-53

#### <a name="cryptographic-module-authentication"></a>Autenticazione del modulo di crittografia

**IA-7** Il sistema informativo implementa meccanismi per l'autenticazione in un modulo di crittografia che soddisfa i requisiti delle leggi federali in vigore, degli ordini esecutivi, delle direttive, dei criteri, dei regolamenti, degli standard e delle indicazioni per il tipo di autenticazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto usa l'autenticazione di Windows, Desktop remoto e BitLocker. Questi componenti possono essere configurati per basarsi su moduli di crittografia convalidati FIPS 140. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-ia-8"></a>Controllo IA-8 NIST 800-53

#### <a name="identification-and-authentication-non-organizational-users"></a>Identificazione e autenticazione (Utenti non appartenenti all'organizzazione)

**IA-8** Il sistema informativo identifica in modo univoco e autentica gli utenti non appartenenti all'organizzazione o elabora azioni per conto di utenti non appartenenti all'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'identificazione e autenticazione degli utenti non appartenenti all'organizzazione che accedono alle risorse distribuite dal cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-8-1"></a>Controllo IA-8 (1) NIST 800-53

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identificazione e autenticazione (Utenti non appartenenti all'organizzazione) | Accettazione delle credenziali PIV da altre agenzie

**IA-8 (1)** Il sistema informativo accetta e verifica elettronicamente le credenziali di verifica dell'identità personale (PIV, Personal Identity Verification) di altre agenzie federali.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'accettazione e verifica delle credenziali di verifica dell'identità personale (PIV, Personal Identity Verification) da altre agenzie federali. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-8-2"></a>Controllo IA-8 (2) NIST 800-53

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identificazione e autenticazione (Utenti non appartenenti all'organizzazione) | Accettazione delle credenziali di terze parti

**IA-8 (2)** Il sistema informativo accetta solo le credenziali di terze parti approvate FICAM.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di accettare solo le credenziali di terze parti approvate dall'iniziativa Trust Framework Solutions di Federal Identity, Credential, and Access Management (FICAM). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-8-3"></a>Controllo IA-8 (3) NIST 800-53

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identificazione e autenticazione (Utenti non appartenenti all'organizzazione) | Uso di prodotti approvati da FICAM

**IA-8 (3)** L'organizzazione usa solo componenti di sistemi informativi approvati da FICAM in [assegnazione: sistemi informativi definiti dall'organizzazione] per accettare le credenziali di terze parti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di usare solo le risorse approvate dall'iniziativa Trust Framework Solutions di Federal Identity, Credential, and Access Management (FICAM) per l'accettazione delle credenziali di terze parti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-ia-8-4"></a>Controllo IA-8 (4) NIST 800-53

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identificazione e autenticazione (Utenti non appartenenti all'organizzazione) | Uso di profili emessi da FICAM

**IA-8 (4)** Il sistema informativo è conforme ai profili emessi da FICAM.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di conformarsi ai profili emessi dall'iniziativa Trust Framework Solutions di Federal Identity, Credential, and Access Management (FICAM). |
| **Provider (Microsoft Azure)** | Non applicabile |
