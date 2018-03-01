---
title: Azure Security and Compliance Blueprint - Automazione di applicazioni Web per FedRAMP - Protezione dei sistemi e delle comunicazioni
description: Automazione delle applicazioni Web per FedRAMP - Protezione dei sistemi e delle comunicazioni
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: ce0917cec67612736103932903eab18d7f0f21bb
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="system-and-communications-protection-sc"></a>Protezione dei sistemi e delle comunicazioni (SC)

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti nell'ambito del documento NIST Special Publication 800-53, revisione 4. Fare riferimento al documento NIST 800-53 Rev. 4 per informazioni sulle procedure e indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-sc-1"></a>Controllo SC-1 NIST 800-53

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Criteri e procedure per la protezione dei sistemi e delle comunicazioni

**SC-1** L'organizzazione sviluppa, documenta e distribuisce a [Assegnazione: ruoli o personale definiti dall'organizzazione] criteri di protezione dei sistemi e delle comunicazioni relativi a scopo, ambito ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre a procedure per semplificare l'implementazione dei criteri di protezione dei sistemi e delle comunicazioni e dei controlli di protezione associati. Esamina e aggiorna anche i criteri di protezione dei sistemi e delle comunicazioni correnti [Assegnazione: frequenza definita dall'organizzazione] e le procedure di protezione dei sistemi e delle comunicazioni [Assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure del cliente per la protezione dei sistemi e delle comunicazioni a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-2"></a>Controllo SC-2 NIST 800-53

#### <a name="application-partitioning"></a>Partizionamento delle applicazioni

**SC-2** Il sistema informativo separa le funzionalità utente (inclusi i servizi dell'interfaccia utente) dalle funzionalità di gestione del sistema informativo stesso.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto separa le funzionalità utente dalle funzionalità di gestione del sistema tramite l'imposizione di controlli di accesso logici e tramite l'architettura del sistema. Le funzionalità utente sono limitate alle interfacce delle applicazioni Web distribuite ai clienti. Le interfacce delle funzionalità di gestione del sistema sono separate dalle interfacce utente. La connettività di gestione viene realizzata interamente tramite un bastion host (jumpbox) protetto situato in una subnet di gestione con regole di gruppo di sicurezza di rete appropriate per limitare l'accesso alle risorse di produzione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-3"></a>Controllo SC-3 NIST 800-53

#### <a name="security-function-isolation"></a>Isolamento delle funzioni di sicurezza

**SC-3** Il sistema informativo mantiene isolate le funzioni di sicurezza dalle funzioni non correlate alla sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto eseguono sistemi operativi Windows. Windows gestisce domini di esecuzione separati per ogni processo in esecuzione tramite l'assegnazione di uno spazio indirizzi virtuali privato a ogni processo. La soluzione implementa anche un'architettura e controlli di accesso progettati per isolare le funzionalità di sicurezza dove necessario. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-4"></a>Controllo SC-4 NIST 800-53

#### <a name="information-in-shared-resources"></a>Informazioni nelle risorse condivise

**SC-4** Il sistema informativo impedisce il trasferimento non autorizzato e non intenzionale di informazioni tramite risorse di sistema condivise.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto eseguono sistemi operativi Windows. Il sistema operativo gestisce le risorse (ad esempio la memoria e le risorse di archiviazione) in modo che tali informazioni siano accessibili solo a utenti e ruoli con le autorizzazioni appropriate. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-5"></a>Controllo SC-5 NIST 800-53

#### <a name="denial-of-service-protection"></a>Protezione da attacchi Denial of Service

**SC-5** il sistema informativo protegge completamente dagli attacchi di tipo Denial of Service seguenti o ne limita gli effetti: [Assegnazione: tipi definiti dall'organizzazione di attacchi Denial of Service o riferimenti a fonti per tali informazioni] impiegando [Assegnazione: misure di sicurezza definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un gateway applicazione che include funzionalità di web application firewall e bilanciamento del carico. Le macchine virtuali distribuite che supportano i livelli Web, database e Active Directory vengono distribuite in un set di disponibilità scalabile. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-6"></a>Controllo SC-6 NIST 800-53

#### <a name="resource-availability"></a>Disponibilità delle risorse

**SC-6** Il sistema informativo protegge la disponibilità delle risorse allocando [Assegnazione: risorse definite dall'organizzazione] per [Selezione (una o più opzioni); priorità; quota; [Assegnazione: misure di sicurezza definite dall'organizzazione]].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto eseguono sistemi operativi Windows. Ogni processo di Windows rende disponibili le risorse necessarie per l'esecuzione di un programma. La priorità delle risorse è gestita dal sistema operativo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-7a"></a>Controllo SC-7.a NIST 800-53

#### <a name="boundary-protection"></a>Protezione dei limiti

**SC-7.a** Il sistema informativo effettua il monitoraggio e il controllo delle comunicazioni in corrispondenza del limite esterno del sistema e dei limiti chiave interni del sistema stesso.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un gateway applicazione e funzioni di bilanciamento del carico e configura regole di gruppo di sicurezza di rete per controllare le comunicazioni in corrispondenza dei limiti esterni e tra subnet interne. I log di diagnostica e degli eventi del gateway applicazione, del servizio di bilanciamento del carico e del gruppo di sicurezza di rete vengono raccolti da Log Analytics di OMS per consentire il monitoraggio dei clienti. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-7b"></a>Controllo SC-7.b NIST 800-53

#### <a name="boundary-protection"></a>Protezione dei limiti

**SC-7.b** Il sistema informativo implementa subnet per i componenti di sistema accessibili pubblicamente e [Selezione: fisicamente; logicamente] separati dalle reti interne dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce le risorse in un'architettura con una subnet Web, una subnet di database, una subnet Active Directory e una subnet di gestione separate. Le subnet sono separate logicamente da regole del gruppo di sicurezza di rete applicate alle singole subnet per limitare il traffico tra subnet esclusivamente a quello necessario per le funzionalità di sistema e di gestione (ad esempio, il traffico esterno non può accedere alle subnet del database, di gestione o di Active Directory). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-7c"></a>Controllo SC-7.c NIST 800-53

#### <a name="boundary-protection"></a>Protezione dei limiti

**SC-7.c** Il sistema informativo si connette a reti o a sistemi informativi esterni solo tramite interfacce gestite costituite da dispositivi di protezione dei limiti disposti in base all'architettura di sicurezza dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce un gateway applicazione per gestire connessioni esterne ad applicazioni Web distribuite ai clienti. Le connessioni esterne per l'accesso per la gestione sono limitate a un bastion host/jumpbox distribuito in una subnet di gestione con regole di sicurezza di rete applicate per limitare le connessioni esterne a indirizzi IP autorizzati. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-3"></a>Controllo SC-7 (3) NIST 800-53

#### <a name="boundary-protection--access-points"></a>Protezione dei limiti | Punti di accesso

**SC-7 (3)** L'organizzazione limita il numero di connessioni di rete esterne al sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce due indirizzi IP pubblici: uno associato al gateway applicazione e uno associato al bastion host/jumpbox di gestione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-4a"></a>Controllo SC-7 (4).a NIST 800-53

#### <a name="boundary-protection--external-telecommunications-services"></a>Protezione dei limiti | Servizi di telecomunicazione esterni

**SC-7 (4).a** L'organizzazione implementa un'interfaccia gestita per ogni servizio di telecomunicazione esterno.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce due indirizzi IP pubblici: uno associato al gateway applicazione e uno associato al bastion host/jumpbox di gestione. La gestione di queste interfacce viene abilitata tramite rete software-defined. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-4b"></a>Controllo SC-7 (4).b NIST 800-53

#### <a name="boundary-protection--external-telecommunications-services"></a>Protezione dei limiti | Servizi di telecomunicazione esterni

**SC-7 (4).b** L'organizzazione stabilisce criteri di flusso di traffico per ogni interfaccia gestita.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce due indirizzi IP pubblici: uno associato al gateway applicazione e uno associato al bastion host/jumpbox di gestione. La gestione di queste interfacce viene abilitata tramite rete software-defined. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-4c"></a>Controllo SC-7 (4).c NIST 800-53

#### <a name="boundary-protection--external-telecommunications-services"></a>Protezione dei limiti | Servizi di telecomunicazione esterni

**SC-7 (4).c** L'organizzazione protegge la riservatezza e l'integrità delle informazioni trasmesse attraverso ogni interfaccia.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il gateway per applicazioni Web distribuito da questo progetto viene configurato con un listener HTTPS, garantendo la riservatezza e l'integrità delle sessioni di comunicazione. Anche le connessioni Desktop remoto al jumpbox vengono crittografate e garantiscono quindi integrità e riservatezza. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-4d"></a>Controllo SC-7 (4).d NIST 800-53

#### <a name="boundary-protection--external-telecommunications-services"></a>Protezione dei limiti | Servizi di telecomunicazione esterni

**SC-7 (4).d** L'organizzazione documenta ogni eccezione ai criteri di flusso del traffico supportandola con una missione o un'esigenza aziendale e con la durata di tale esigenza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I clienti non sono responsabili delle operazioni del data center (inclusi i servizi di telecomunicazione). Tutti i servizi di telecomunicazione vengono forniti e gestiti da Microsoft Azure. Questo controllo viene ereditato e proviene da Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-4e"></a>Controllo SC-7 (4).e NIST 800-53

#### <a name="boundary-protection--external-telecommunications-services"></a>Protezione dei limiti | Servizi di telecomunicazione esterni

**SC-7 (4).e** L'organizzazione esamina le eccezioni ai criteri di flusso del traffico [Assegnazione: frequenza definita dall'organizzazione] e rimuove le eccezioni non più supportate da una missione o da un'esigenza di business esplicita.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I clienti non sono responsabili delle operazioni del data center (inclusi i servizi di telecomunicazione). Tutti i servizi di telecomunicazione vengono forniti e gestiti da Microsoft Azure. Questo controllo viene ereditato e proviene da Azure. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-5"></a>Controllo SC-7 (5) NIST 800-53

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Protezione dei limiti | Nega per impostazione predefinita/Consenti come eccezione

**SC-7 (5)** Il sistema informativo in corrispondenza di interfacce gestite nega il traffico di comunicazione di rete per impostazione predefinita e lo consente come eccezione (nega tutto, consenti come eccezione).

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I set di regole applicati ai gruppi di sicurezza di rete distribuiti da questo progetto sono configurati in base a uno schema Nega per impostazione predefinita. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-7"></a>Controllo SC-7 (7) NIST 800-53

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Protezione dei limiti | Impedisci lo split tunneling per i dispositivi remoti

**SC-7 (7)** Il sistema informativo, in combinazione con un dispositivo remoto, impedisce al dispositivo di stabilire contemporaneamente connessioni non remote al sistema e comunicazioni tramite altri tipi di connessione a risorse di reti esterne.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I clienti possono creare criteri di configurazione dei dispositivi remoti a livello di organizzazione specifici per lo split tunneling. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-8"></a>Controllo SC-7 (8) NIST 800-53

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Protezione dei limiti | Instrada il traffico a server proxy autenticati

**SC-7 (8)** Il sistema informativo instrada [Assegnazione: traffico di comunicazione interno definito dall'organizzazione] a [Assegnazione: reti esterne definite dall'organizzazione] tramite server proxy autenticati in interfacce gestite.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile dell'instradamento delle informazioni definite dall'utente tramite un proxy autenticato a una rete esterna. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-10"></a>Controllo SC-7 (10) NIST 800-53

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Protezione dei limiti | Evita esfiltrazioni non autorizzate

**SC-7 (10)** L'organizzazione impedisce l'esfiltrazione non autorizzata di informazioni tra interfacce gestite.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di impedire l'esfiltrazione non autorizzata di informazioni tra interfacce gestite. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-12"></a>Controllo SC-7 (12) NIST 800-53

#### <a name="boundary-protection--host-based-protection"></a>Protezione dei limiti | Protezione basata su host

**SC-7 (12)** L'organizzazione implementa [Assegnazione: meccanismi di protezione dei limiti basati su host e definiti dall'organizzazione] in corrispondenza di [Assegnazione: componenti del sistema informativo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto sono configurate con un firewall basato su host abilitato. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-13"></a>Controllo SC-7 (13) NIST 800-53

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Protezione dei limiti | Isolamento di strumenti/meccanismi/componenti di supporto di sicurezza

**SC-7 (13)** L'organizzazione isola [Assegnazione: strumenti, meccanismi e componenti di supporto di sicurezza delle informazioni definite dall'organizzazione] da altri componenti del sistema informativo interni tramite l'implementazione di subnet separate fisicamente con interfacce gestite ad altri componenti del sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce risorse in un'architettura con una subnet di gestione separata per la distribuzione ai clienti di strumenti e componenti di supporto per la sicurezza delle informazioni. Le subnet sono separate logicamente da regole di gruppo di sicurezza di rete. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-18"></a>Controllo SC-7 (18) NIST 800-53

#### <a name="boundary-protection--fail-secure"></a>Protezione dei limiti | Protezione da errori

**SC-7 (18)** Il sistema informativo è protetto nel caso di un errore di funzionamento di un dispositivo di protezione dei limiti.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | Non sono presenti dispositivi fisici di protezione dei limiti all'interno dell'ambito dei sistemi distribuiti in Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure distribuisce server gateway e VPN SSL geograficamente separati e ridondanti. Se si verifica un errore di un sistema gateway, ciò avviene in modo protetto e l'accesso viene limitato all'ambiente. Per stabilire una connessione all'ambiente Microsoft Azure, un utente deve stabilire una connessione separata a un server gateway attivo gestito da Microsoft Azure. <br /> Se, poi, alcuni dispositivi di rete di Microsoft Azure (inclusi i router perimetrali, i router di accesso, i servizi di bilanciamento del carico, i commutatori di aggregazione e i Tor) generano errori, il circuito interessato viene disconnesso e quindi la sicurezza è comunque garantita. Un errore di un dispositivo di rete di Microsoft Azure non può causare l'ingresso di informazioni esterne al sistema nel dispositivo né consentire il rilascio non autorizzato di informazioni. La ridondanza incorporata consente alle risorse di Microsoft Azure di generare errori senza influire sulla disponibilità. |


 ### <a name="nist-800-53-control-sc-7-20"></a>Controllo SC-7 (20) NIST 800-53

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Protezione dei limiti | Isolamento/separazione dinamica

**SC-7 (20)** Il sistema informativo offre la possibilità di isolare/separare in modo dinamico [Assegnazione: componenti del sistema informativo definiti dall'organizzazione] da altri componenti del sistema.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di garantire che il sistema abbia la possibilità di isolare in modo dinamico le risorse distribuite al cliente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-7-21"></a>Controllo SC-7 (21) NIST 800-53

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Protezione dei limiti | Isolamento dei componenti del sistema informativo

**SC-7 (21)** L'organizzazione impiega meccanismi di protezione dei limiti per separare [Assegnazione: componenti del sistema informativo definiti dall'organizzazione] supportando [Assegnazione: missioni e/o funzioni aziendali definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce le risorse in un'architettura con una subnet Web, una subnet di database, una subnet Active Directory e una subnet di gestione separate. Le subnet sono separate logicamente da regole del gruppo di sicurezza di rete applicate alle singole subnet per limitare il traffico tra subnet esclusivamente a quello necessario per le funzionalità di sistema e di gestione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-8"></a>Controllo SC-8 NIST 800-53

#### <a name="transmission-confidentiality-and-integrity"></a>Riservatezza e integrità delle trasmissioni

**SC-8** Il sistema informativo protegge la [Selezione (una o più opzioni): riservatezza; integrità] delle informazioni trasmesse.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'implementazione di SI-8 (1) soddisfa questo requisito di controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-8-1"></a>Controllo SC-8 (1) NIST 800-53

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Riservatezza e integrità delle trasmissioni | Protezione crittografica o fisica in alternativa

**SC-8 (1)** Il sistema informativo implementa meccanismi di crittografia per [Selezione (una o più opzioni): impedire la divulgazione non autorizzata di informazioni; rilevare le modifiche alle informazioni] durante la trasmissione, a meno che queste non siano protette in altro modo da [Assegnazione: misure di sicurezza fisiche alternative definite dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto configura le risorse in modo che comunichino solo tramite protocolli protetti. Il componente WAF del gateway applicazione è configurato in modo da accettare comunicazioni da usi esterni attraverso HTTPS/TLS e comunicare con il pool di back-end solo tramite HTTPS/TLS. SQL Server è configurato per la comunicazione solo tramite HTTPS/TLS. I servizi Desktop remoto sono configurati per l'uso di connessioni protette. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-10"></a>Controllo SC-10 NIST 800-53

#### <a name="network-disconnect"></a>Disconnessione di rete

**SC-10** il sistema informativo termina la connessione di rete associata a una sessione di comunicazione alla fine della sessione o dopo [Assegnazione: periodo di tempo definito dall'organizzazione] di inattività.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'autenticazione per le sessioni di Desktop remoto è gestita da Active Directory. Quando per un utente viene disabilitato l'accesso ad Active Directory, le sessioni remote vengono chiuse immediatamente. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-12"></a>Controllo SC-12 NIST 800-53

#### <a name="cryptographic-key-establishment-and-management"></a>Definizione e gestione di chiavi crittografiche

**SC-12** L'organizzazione definisce e gestisce chiavi crittografiche per la crittografia obbligatoria impiegata all'interno del sistema informativo in base a [Assegnazione: requisiti definiti dall'organizzazione per la generazione, la distribuzione, l'archiviazione, l'accesso e la distruzione di chiavi].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto distribuisce Azure Key Vault. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Azure Key Vault può generare chiavi usando la funzionalità di generazione di chiavi di un modulo di protezione hardware FIPS 140-2 livello 2. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-12-1"></a>Controllo SC-12 (1) NIST 800-53

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Definizione e gestione di chiavi crittografiche | Disponibilità

**SC-12 (1)** L'organizzazione gestisce la disponibilità delle informazioni in caso di perdita di chiavi crittografiche da parte degli utenti.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Key Vault consente di archiviare le chiavi crittografiche e i segreti usati in questo progetto. Key Vault semplifica il processo di gestione delle chiavi che accedono ai dati e ne eseguono la crittografia. In Key Vault vengono archiviati gli autenticatori seguenti: la password di Azure per la distribuzione degli account, la password dell'amministratore delle macchine virtuali, la password dell'account del servizio SQL Server. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-12-2"></a>Controllo SC-12 (2) NIST 800-53

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Definizione e gestione di chiavi crittografiche | Chiavi simmetriche

**SC-12 (2)** L'organizzazione genera, controlla e distribuisce chiavi crittografiche simmetriche usando tecnologie e processi [Selezione: conformi a FIPS NIST; approvati NSA].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Key Vault viene usato per generare, controllare e distribuire chiavi crittografiche. Azure Key Vault può generare chiavi usando la funzionalità di generazione di chiavi di un modulo di protezione hardware FIPS 140-2 livello 2. Le chiavi vengono archiviate e gestite in contenitori crittografati in modo sicuro in Azure Key Vault. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-12-3"></a>Controllo SC-12 (3) NIST 800-53

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Definizione e gestione di chiavi crittografiche | Chiavi asimmetriche

**SC-12 (3)** L'organizzazione genera, controlla e distribuisce chiavi di crittografia asimmetriche usando [Selezione: tecnologie e processi di gestione delle chiavi approvati NSA; certificati o materiale per le chiavi preposizionato PKI di classe 3 approvati; certificati e token di protezione hardware PKI di classe 3 o di classe 4 approvati che proteggono la chiave privata dell'utente].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della generazione, del controllo e della distribuzione delle chiavi crittografiche asimmetriche (se vengono usate all'interno delle risorse distribuite al cliente). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-13"></a>Controllo SC-13 NIST 800-53

#### <a name="cryptographic-protection"></a>Protezione crittografica

**SC-13** Il sistema informativo implementa [Assegnazione: usi crittografici definiti dall'organizzazione e tipo di crittografia necessario per ogni uso] in conformità alle leggi federali, agli ordini esecutivi, alle direttive, ai criteri, alle normative e agli standard applicabili.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Questo progetto usa l'autenticazione di Windows, Desktop remoto e BitLocker. Questi componenti possono essere configurati per basarsi su moduli di crittografia convalidati FIPS 140. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-15a"></a>Controllo SC-15.a NIST 800-53

#### <a name="collaborative-computing-devices"></a>Dispositivi di elaborazione di collaborazione

**SC-15.a** Il sistema informativo impedisce l'attivazione remota di dispositivi di elaborazione di collaborazione con le eccezioni seguenti: [Assegnazione: eccezioni definite dall'organizzazione in cui deve essere consentita l'attivazione remota].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nell'ambito di questo progetto non vengono distribuiti dispositivi di elaborazione di collaborazione. Nota: nell'ambito di sistemi distribuiti in Azure sono presenti dispositivi di elaborazione di collaborazione fisici. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-15b"></a>Controllo SC-15.b NIST 800-53

#### <a name="collaborative-computing-devices"></a>Dispositivi di elaborazione di collaborazione

**SC-15.b** Il sistema informativo indica esplicitamente l'uso agli utenti fisicamente presenti presso i dispositivi.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nell'ambito di questo progetto non vengono distribuiti dispositivi di elaborazione di collaborazione. Nota: nell'ambito di sistemi distribuiti in Azure sono presenti dispositivi di elaborazione di collaborazione fisici. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-17"></a>Controllo SC-17 NIST 800-53

#### <a name="public-key-infrastructure-certificates"></a>Certificati di infrastruttura a chiave pubblica

**SC-17** L'organizzazione rilascia certificati di chiave pubblica nell'ambito di [Assegnazione: criteri dei certificati definiti dall'organizzazione] o ottiene certificati di chiave pubblica da un provider di servizi approvato.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Per il rilascio di certificati il cliente può affidarsi a un'infrastruttura a chiave pubblica a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-18a"></a>Controllo SC-18.a NIST 800-53

#### <a name="mobile-code"></a>Codice mobile

**SC-18.a** L'organizzazione definisce il codice mobile e le tecnologie di codice mobile accettabili e inaccettabili.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di protezione del sistema e delle comunicazioni a livello aziendale del cliente possono definire codice mobile accettabile e inaccettabile. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-18b"></a>Controllo SC-18.b NIST 800-53

#### <a name="mobile-code"></a>Codice mobile

**SC-18.b** L'organizzazione stabilisce le restrizioni di utilizzo e le indicazioni di implementazione per codice mobile e tecnologie di codice mobile accettabili e inaccettabili.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le procedure di protezione del sistema e delle comunicazioni a livello aziendale del cliente possono stabilire restrizioni d'uso del codice mobile. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-18c"></a>Controllo SC-18.c NIST 800-53

#### <a name="mobile-code"></a>Codice mobile

**SC-18.c** L'organizzazione effettua l'autorizzazione, il monitoraggio e il controllo dell'uso di codice mobile all'interno del sistema informativo dell'organizzazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Per l'autorizzazione, il monitoraggio e il controllo dell'uso di codice mobile il cliente può basarsi su un processo a livello aziendale. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-19a"></a>Controllo SC-19.a NIST 800-53

#### <a name="voice-over-internet-protocol"></a>Voice over Internet Protocol

**SC-19.a** L'organizzazione stabilisce restrizioni di utilizzo e indicazioni per l'implementazione di tecnologie Voice over Internet Protocol (VoIP) in base alla possibilità di danni al sistema informativo in caso di uso da parte di utenti malintenzionati.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nell'ambito di questo progetto non vengono distribuite tecnologie VoIP (Voice over Internet Protocol). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-19b"></a>Controllo SC-19.b NIST 800-53

#### <a name="voice-over-internet-protocol"></a>Voice over Internet Protocol

**SC-19.b** L'organizzazione effettua l'autorizzazione, il monitoraggio e il controllo dell'uso di VoIP all'interno del sistema informativo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Nell'ambito di questo progetto non vengono distribuite tecnologie VoIP (Voice over Internet Protocol). |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-20a"></a>Controllo SC-20.a NIST 800-53

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Servizio protetto di risoluzione del nome e dell'indirizzo (origine autorevole)

**SC-20.a** Il sistema informativo offre elementi aggiuntivi per l'autenticazione dell'origine dei dati e per la verifica dell'integrità, oltre ai dati di risoluzione autorevole del nome che il sistema restituisce in risposta alle query esterne di risoluzione del nome e dell'indirizzo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di garantire un servizio sicuro di risoluzione dell'indirizzo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-20b"></a>Controllo SC-20.b NIST 800-53

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Servizio protetto di risoluzione del nome e dell'indirizzo (origine autorevole)

**SC-20.b** Il sistema informativo offre i mezzi per indicare lo stato di sicurezza delle aree figlio e (se l'elemento figlio supporta servizi di risoluzione sicuri) per abilitare la verifica di una catena di certificati tra dominio padre e dominio figlio, nell'ambito di uno spazio dei nomi gerarchico distribuito.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di garantire un servizio sicuro di risoluzione dell'indirizzo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-21"></a>Controllo SC-21 NIST 800-53

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Servizio protetto di risoluzione del nome e dell'indirizzo (resolver ricorsivo o di memorizzazione nella cache)

**SC-21** il sistema informativo richiede ed esegue l'autenticazione dell'origine dati e la verifica dell'integrità dei dati stessi nelle risposte relative alla risoluzione del nome e dell'indirizzo che il sistema riceve da origini autorevoli.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile della configurazione delle risorse distribuite al cliente per richiedere ed eseguire l'autenticazione dell'origine dati e la verifica dell'integrità dei dati stessi nelle risposte relative alla risoluzione del nome e dell'indirizzo che il sistema riceve da origini autorevoli. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-22"></a>Controllo SC-22 NIST 800-53

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architettura e provisioning per il servizio di risoluzione del nome e dell'indirizzo

**SC-22** I sistemi informativi che forniscono collettivamente il servizio di risoluzione del nome e dell'indirizzo per un'organizzazione sono a tolleranza di errore e implementano la separazione dei ruoli interni ed esterni.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente è responsabile di garantire che i sistemi che offrono i servizi di risoluzione dell'indirizzo per le risorse distribuite ai clienti sono a tolleranza di errore e implementano la separazione dei ruoli interni ed esterni. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-23"></a>Controllo SC-23 NIST 800-53

#### <a name="session-authenticity"></a>Autenticità della sessione

**SC-23** Il sistema informativo protegge l'autenticità delle sessioni di comunicazione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso remoto alle risorse distribuite da questo progetto, inclusi il portale di Azure, la connessione Desktop remoto e il gateway di applicazioni Web, è protetto tramite TLS. Il protocollo TLS garantisce l'autenticità per le comunicazioni a livello di sessione. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-23-1"></a>Controllo SC-23 (1) NIST 800-53

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Autenticità della sessione | Invalida gli identificatori di sessione alla disconnessione

**SC-23 (1)** Il sistema informativo invalida gli identificatori di sessione alla disconnessione dell'utente o se la sessione viene chiusa in altro modo.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'accesso remoto alle risorse distribuite da questo progetto, inclusi il portale di Azure, la connessione Desktop remoto e il gateway di applicazioni Web, è protetto tramite TLS. Il portale di Azure e le sessioni di Desktop remoto invalidano gli identificatori di sessione alla disconnessione. L'invalidamento della sessione Web viene applicata tramite gateway applicazione di Azure - regole WAF (Web Application Firewall). WAF applica l'affinità basata su cookie per sessione ed effettua il timeout della sessione dopo 30 minuti (configurabili dopo la distribuzione in base alle regole specifiche dell'organizzazione) di inattività del client. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-24"></a>Controllo SC-24 NIST 800-53

#### <a name="fail-in-known-state"></a>Errore in uno stato conosciuto

**SC-24** Il sistema informativo genera un errore in uno [Assegnazione: stato conosciuto definito dall'organizzazione] per [Assegnazione: tipi di errore definiti dall'organizzazione] mantenendo le [informazioni sullo stato del sistema definite dall'organizzazione] nell'errore.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Il cliente ha la responsabilità di garantire che le risorse distribuite al cliente generino errori in uno stato conosciuto. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-28"></a>Controllo SC-28 NIST 800-53

#### <a name="protection-of-information-at-rest"></a>Protezione delle informazioni inattive

**SC-28** Il sistema informativo protegge la [Selezione (una o più opzioni): riservatezza; integrità] delle [Assegnazione: informazioni definite dall'organizzazione inattive].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | L'implementazione di SC-28 (1) soddisfa questo requisito di controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ### <a name="nist-800-53-control-sc-28-1"></a>Controllo SC-28 (1) NIST 800-53

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Protezione delle informazioni inattive | Protezione crittografica

**SC-28 (1)** Il sistema informativo implementa meccanismi di crittografia per impedire la divulgazione e la modifica non autorizzate di [Assegnazione: informazioni definite dall'organizzazione] in [Assegnazione: componenti del sistema informativo definiti dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto implementano la crittografia dischi per proteggere la riservatezza e l'integrità delle informazioni inattive. Crittografia dischi di Azure per Windows viene implementata tramite la funzionalità BitLocker di Windows. SQL Server è configurato per l'uso di Transparent Data Encryption (TDE), che esegue la crittografia e la decrittografia in tempo reale dei dati e dei file di log per proteggere le informazioni inattive. TDE garantisce che i dati archiviati non siano soggetti ad accesso non autorizzato. Il cliente può scegliere di implementare controlli aggiuntivi a livello di applicazione per proteggere l'integrità delle informazioni archiviate. La riservatezza e l'integrità di tutti i BLOB di archiviazione distribuiti da questo progetto sono protetti tramite l'uso della crittografia del servizio Archiviazione di Azure (SSE, Storage Service Encryption). SSE protegge i dati inattivi all'interno degli account di archiviazione di Azure usando la crittografia AES a 256 bit. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-sc-39"></a>Controllo SC-39 NIST 800-53

#### <a name="process-isolation"></a>Isolamento del processo

**SC-39** Il sistema informativo gestisce un dominio di esecuzione separato per ogni processo in esecuzione.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le macchine virtuali distribuite da questo progetto eseguono sistemi operativi Windows. Windows gestisce domini di esecuzione separati per ogni processo in esecuzione tramite l'assegnazione di uno spazio indirizzi virtuali privato a ogni processo. |
| **Provider (Microsoft Azure)** | Non applicabile |
