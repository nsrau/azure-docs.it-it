---
title: Automazione dei progetti Azure FedRAMP - Protezione fisica e dell'ambiente
description: Applicazioni Web per FedRAMP - Protezione fisica e dell'ambiente
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9e9e473285f4e82e1da40b8c3d496d65733fbd45
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="physical-and-environmental-protection-pe"></a>Protezione fisica e ambientale (PE, Physical and Environmental Protection)

> [!NOTE]
> Questi controlli sono definiti dal NIST e dal Ministero del commercio degli Stati Uniti come parte del documento NIST Special Publication 800-53, Revisione 4. Consultare la pubblicazione NIST 800-53 Rev. 4 per informazioni sulle procedure e le indicazioni di test per ogni controllo.

## <a name="nist-800-53-control-pe-1"></a>Controllo PE-1 NIST 800-53

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Criteri e procedure di protezione fisica e ambientale

**PE-1** L'organizzazione sviluppa, documenta e distribuisce a [assegnazione: personale o ruoli definiti dall'organizzazione] criteri di protezione fisica e ambientale relativi a scopo, ambito, ruoli, responsabilità, impegno di gestione, coordinamento tra entità dell'organizzazione e conformità, oltre che procedure per semplificare l'implementazione dei criteri di protezione fisica e ambientale e dei controlli di protezione associati. Esamina e aggiorna inoltre i criteri di protezione fisica e ambientale correnti [assegnazione: frequenza definita dall'organizzazione] e le procedure associate [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | I criteri e le procedure del cliente per la protezione fisica e ambientale a livello aziendale possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pe-2a"></a>Controllo PE-2.a NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizzazioni di accesso fisico

**PE-2.a** L'organizzazione sviluppa, approva e gestisce un elenco di persone con accesso autorizzato alla struttura in cui si trova il sistema informativo.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. L'accesso fisico a un data center di Microsoft deve essere approvato dal team DCM (Datacenter Management) con il relativo strumento di accesso. Le assegnazioni degli accessi richiedono una data di fine, dopo la quale l'accesso verrà automaticamente rimosso e dovrà essere nuovamente approvato. Se l'accesso non è più necessario, i dirigenti o i responsabili della sicurezza dei data center dovranno richiedere manualmente l'interruzione dell'accesso. |


 ## <a name="nist-800-53-control-pe-2b"></a>Controllo PE-2.b NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizzazioni di accesso fisico

**PE-2.b** L'organizzazione emette le credenziali di autorizzazione per l'accesso alla struttura.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Lo strumento di accesso ai data center è la fonte autorevole che elenca tutto il personale autorizzato ad accedere a uno specifico data center. Lo strumento è collegato ai dispositivi di controllo degli accessi per la sicurezza fisica dei data center e autorizza l'accesso in base ai livelli di accesso approvati dal team DCM. I livelli di accesso vengono assegnati nello strumento al badge di un utente emesso da Microsoft o a un badge di accesso temporaneo assegnato a livello di data center dal CRS (Control Room Supervisor). I livelli di accesso vengono approvati dal team DCM. Oltre alle credenziali assegnate ai badge fisici, alcune aree del data center richiedono la registrazione dei dati biometrici dell'utente (conformazione della mano o impronte digitali). |


 ## <a name="nist-800-53-control-pe-2c"></a>Controllo PE-2.c NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizzazioni di accesso fisico

**PE-2.c** L'organizzazione esamina l'elenco degli accessi indicando in dettaglio l'accesso autorizzato alla struttura in base a singoli utenti [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Oltre alla revoca dell'accesso descritta nella parte a, Microsoft Azure esamina gli elenchi degli accessi autorizzati per i data center di Azure ogni 90 giorni per rimuovere/aggiornare singoli accessi a seconda delle esigenze. |


 ## <a name="nist-800-53-control-pe-2d"></a>Controllo PE-2.d NIST 800-53

#### <a name="physical-access-authorizations"></a>Autorizzazioni di accesso fisico

**PE-2.d** L'organizzazione rimuove gli utenti dall'elenco degli accessi alla struttura quando l'accesso non è più necessario.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure rimuove automaticamente l'accesso quando viene raggiunta la data di fine di assegnazione. Se l'accesso non è più necessario, i dirigenti o i responsabili della sicurezza dei data center dovranno richiedere manualmente l'interruzione dell'accesso nel relativo strumento. Inoltre, Microsoft Azure rimuoverà eventuali autorizzazioni di accesso non necessarie individuate in seguito alla revisione dell'elenco degli accessi descritta nella parte c. |


 ## <a name="nist-800-53-control-pe-3a"></a>Controllo PE-3.a NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.a** L'organizzazione applica autorizzazioni di accesso fisico a [assegnazione: punti di ingresso/uscita definiti dall'organizzazione alla struttura in cui si trova il sistema informativo] verificando le singole autorizzazioni prima di concedere l'accesso alla struttura e controllando i dati in ingresso/uscita dalla struttura con [selezione (uno o più): [assegnazione: sistemi/dispositivi di controllo dell'accesso fisico definiti dall'organizzazione]; controlli].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure applica autorizzazioni di accesso fisico per tutti i punti di accesso fisico ai data center di Azure con addetti alla sicurezza, allarmi, videosorveglianza, autenticazione a più fattori e dispositivi mantrap 24 ore su 24, 7 giorni su 7. |


 ## <a name="nist-800-53-control-pe-3b"></a>Controllo PE-3.b NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.b** L'organizzazione gestisce i log di controllo di accesso fisico per [assegnazione: punti di ingresso/uscita definiti dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Tutti gli accessi alle strutture di data center di Azure vengono registrati e controllati. |


 ## <a name="nist-800-53-control-pe-3c"></a>Controllo PE-3.c NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.c** L'organizzazione fornisce [assegnazione: misure di sicurezza definite dall'organizzazione] per controllare l'accesso alle aree all'interno della struttura ufficialmente designate come pubblicamente accessibili.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I data center di Azure non contengono aree designate come accessibili pubblicamente. |


 ## <a name="nist-800-53-control-pe-3d"></a>Controllo PE-3.d NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.d** L'organizzazione accompagna i visitatori e ne monitora l'attività [assegnazione: circostanze definite dall'organizzazione che richiedono l'accompagnamento e il monitoraggio dei visitatori].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Tutti i visitatori con accesso approvato al data center (vedere PE-2) hanno un badge o un elemento visivo, ad esempio un badge colorato, che li contraddistingue come 'Escort Only' (Solo con accompagnatore) e devono rimanere tutto il tempo con l'accompagnatore. I visitatori con accompagnatore non hanno alcun livello di accesso e possono spostarti solo con l'accesso dei loro accompagnatori. Gli accompagnatori monitorano tutte le attività del loro visitatore all'interno del data center. |


 ## <a name="nist-800-53-control-pe-3e"></a>Controllo PE-3.e NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.e** L'organizzazione protegge chiavi, combinazioni e altri dispositivi di accesso fisico.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Chiavi fisiche e badge di accesso temporaneo sono protetti all'interno dell'SOC (Security Operations Center). I responsabili della sicurezza sono operativi 24 ore su 24, 7 giorni su 7. Le chiavi vengono verificate dal personale eseguendo una corrispondenza tra il badge di accesso dell'utente e la chiave fisica. A ogni turno vengono eseguiti inventari delle chiavi, che non possono essere portate fuori sede. |


 ## <a name="nist-800-53-control-pe-3f"></a>Controllo PE-3.f NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.f** L'organizzazione esegue inventari di [assegnazione: dispositivi di accesso fisico definiti dall'organizzazione] ogni [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I dispositivi di accesso fisico all'interno dei data center di Azure vengono aggiunti agli inventari almeno una volta all'anno. Chiavi e badge di accesso temporaneo vengono aggiunti all'inventario varie volte al giorno, a ogni turno. Lettori di badge e dispositivi di accesso analoghi sono collegati al sistema di sicurezza fisica in cui lo stato è costantemente rappresentato. |


 ## <a name="nist-800-53-control-pe-3g"></a>Controllo PE-3.g NIST 800-53

#### <a name="physical-access-control"></a>Controllo dell'accesso fisico

**PE-3.g** L'organizzazione cambia combinazioni e chiavi [assegnazione: frequenza definita dall'organizzazione] e/o quando le chiavi vengono perse, le combinazioni vengono compromesse o in caso di trasferimento o chiusura di rapporto di un utente.  

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I data center di Microsoft Azure includono procedure da implementare in caso di perdita di un badge di accesso o una chiave oppure di trasferimento o chiusura di rapporto di un utente. In caso di chiusura di rapporto o trasferimento, l'accesso dell'utente viene immediatamente rimosso dal sistema insieme al badge. |


 ### <a name="nist-800-53-control-pe-3-1"></a>Controllo PE-3 (1) NIST 800-53

#### <a name="physical-access-control--information-system-access"></a>Controllo di accesso fisico | Accesso al sistema informativo

**PE-3 (1)** L'organizzazione applica autorizzazioni di accesso fisico al sistema informativo oltre ai controlli di accesso fisico per la struttura in [assegnazione: spazi fisici definiti dall'organizzazione che contengono uno o più componenti del sistema informativo].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure limita anche alcune aree all'interno dei data center di Microsoft che contengono sistemi critici (ad esempio, ambienti di condivisione, ambienti critici, sale dei permutatori e così via) con vari meccanismi di sicurezza, tra cui controllo elettronico di accesso, dispositivi biometrici e controlli antipass-back. Agli ambienti di condivisione dell'accesso ad Azure viene assegnato un livello superiore distinto di accesso DCAT rispetto ad altre aree del data center. Tutti i fornitori e i dipendenti a tempo pieno di Azure che hanno accesso agli ambienti di condivisione di Azure per enti pubblici devono essere formalmente sottoposti a un processo di cloud screening di Microsoft e di verifica della cittadinanza statunitense per poter essere autorizzati ad accedere all'ambiente. Per altre informazioni sul processo di cloud screening per gli ambienti di condivisione di Azure per enti pubblici, vedere la sezione PS-03. |


 ## <a name="nist-800-53-control-pe-4"></a>Controllo PE-4 NIST 800-53

#### <a name="access-control-for-transmission-medium"></a>Controllo di accesso per mezzi di trasmissione

**PE-4** L'organizzazione controlla l'accesso fisico a [assegnazione: linee di trasmissione e distribuzione del sistema informativo definite dall'organizzazione] all'interno delle strutture organizzative con [assegnazione: misure di sicurezza definite dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure ha implementato il controllo di accesso per mezzi di trasmissione progettando e creando sale dei permutatori e ambienti di condivisione per proteggere le linee di trasmissione e distribuzione del sistema informativo da danni accidentali, interruzioni e manomissioni fisiche. L'accesso alle sale dei permutatori e agli ambienti di condivisione richiede l'autenticazione a due fattori (badge di accesso e dati biometrici). In questo modo l'accesso è limitato esclusivamente al personale autorizzato (vedere PE-2, PE-3). All'interno del permutatore, le linee di distribuzione e trasmissione sono protette da danni accidentali, interruzioni e manomissioni fisiche con l'utilizzo di condotti in metallo, rack bloccati, cabine o passacavi. |


 ## <a name="nist-800-53-control-pe-5"></a>Controllo PE-5 NIST 800-53

#### <a name="access-control-for-output-devices"></a>Controllo di accesso per dispositivi di output

**PE-5** L'organizzazione controlla l'accesso fisico ai dispositivi di output del sistema informativo per impedire a utenti non autorizzati di ottenere output.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I data center di Microsoft Azure non hanno dispositivi di output (monitor, stampanti, dispositivi audio e così via) permanentemente connessi alle risorse o alle risorse condivise di Azure. Oltre a non avere dispositivi di output, i responsabili della sicurezza eseguono varie ispezioni fisiche nella struttura a ogni turno, controllando che le porte siano bloccate e i rack protetti. L'accesso al data center è limitato alle persone con autorizzazioni approvate. L'accesso agli ambienti di condivisione richiede l'autenticazione a due fattori (badge di accesso e dati biometrici). |


 ## <a name="nist-800-53-control-pe-6a"></a>Controllo PE-6.a NIST 800-53

#### <a name="monitoring-physical-access"></a>Monitoraggio dell'accesso fisico

**PE-6.a** L'organizzazione monitora l'accesso fisico alla struttura in cui si trova il sistema informativo per rilevare e rispondere a incidenti di sicurezza fisica.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. L'accesso fisico viene monitorato mediante l'implementazione di dispositivi e processi di sicurezza nei data center. Alcuni esempi includono il monitoraggio elettronico del controllo di accesso, allarmi e videosistemi, nonché agenti di sicurezza sul posto all'interno della struttura 24 ore su 24, 7 giorni su 7. Un CRS (Control Room Supervisor) all'interno dell'SOC fornisce monitoraggio costante dell'accesso fisico nel data center. |


 ## <a name="nist-800-53-control-pe-6b"></a>Controllo PE-6.b NIST 800-53

#### <a name="monitoring-physical-access"></a>Monitoraggio dell'accesso fisico

**PE-6.b** L'organizzazione esamina i registri di accesso fisico [assegnazione: frequenza definita dall'organizzazione] e in caso si verifichino [assegnazione: eventi o potenziali indicazioni di eventi definiti dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I registri di accesso fisico vengono esaminati in modo continuo e gestiti per eventuali analisi investigative successive. |


 ## <a name="nist-800-53-control-pe-6c"></a>Controllo PE-6.c NIST 800-53

#### <a name="monitoring-physical-access"></a>Monitoraggio dell'accesso fisico

**PE-6.c** L'organizzazione coordina i risultati delle analisi e delle indagini in base alla capacità di risposta agli incidenti dell'organizzazione. 

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Gli eventi di sicurezza che si verificano nel data center sono documentati dal team addetto alla sicurezza. Il team crea report che acquisiscono i dettagli di un evento di sicurezza dopo che si è verificato. <br /> Per gli eventi che richiedono una segnalazione alle autorità, il team addetto alla sicurezza di Microsoft Azure si coordina con il principale provider delle applicazioni (ad esempio, Office 365) per inviare la segnalazione all'ente pubblico, al team US-CERT e a FedRAMP, in conformità alle linee guida US-CERT (vedere IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>Controllo PE-6 (1) NIST 800-53

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitoraggio dell'accesso fisico | Allarmi di intrusione/apparecchiature di sorveglianza

**PE-6 (1)** L'organizzazione monitora allarmi di intrusione fisica e apparecchiature di sorveglianza.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Oltre alla sicurezza in loco 24 ore su 24, 7 giorni su 7, i data center di Microsoft Azure (con lease e completamente gestiti) usano anche sistemi di monitoraggio degli allarmi e telecamere a circuito chiuso. Gli allarmi vengono monitorati e gestiti dal CRS (Control Room Supervisor) presente nell'SOC 24 ore su 24, 7 giorni su 7. In fase di risposta, il CRS usa le fotocamere installate nell'area dell'incidente analizzato per fornire al risponditore informazioni in tempo reale. |


 ### <a name="nist-800-53-control-pe-6-4"></a>Controllo PE-6 (4) NIST 800-53

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitoraggio dell'accesso fisico | Monitoraggio dell'accesso fisico al sistema informativo

**PE-6 (4)** L'organizzazione monitora l'accesso fisico al sistema informativo oltre a eseguire il monitoraggio dell'accesso fisico della struttura in [assegnazione: spazi fisici definiti dall'organizzazione che contengono uno o più componenti del sistema informativo].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure monitora l'accesso fisico alle strutture e ai sistemi informativi all'interno dei data center. Le apparecchiature di tutti i servizi online di Microsoft si trovano all'interno dei data center in cui viene monitorato l'accesso fisico. Le sale dei permutatori e gli ambienti di condivisione sono protetti con controllo di accesso, allarmi e video. |


 ## <a name="nist-800-53-control-pe-8a"></a>Controllo PE-8.a NIST 800-53

#### <a name="visitor-access-records"></a>Record di accesso dei visitatori

**PE-8.a** L'organizzazione gestisce record di accesso dei visitatori alla struttura in cui si trova il sistema informativo per [assegnazione: periodo definito dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I record di accesso ai data center vengono gestiti nel relativo strumento sotto forma di richieste approvate. Come descritto nel controllo PE-3, i visitatori devono essere costantemente accompagnati. L'accesso degli accompagnatori all'interno del data center viene registrato e, se necessario, può essere correlato al visitatore per indagini successive. |


 ## <a name="nist-800-53-control-pe-8b"></a>Controllo PE-8.b NIST 800-53

#### <a name="visitor-access-records"></a>Record di accesso dei visitatori

**PE-8.b** L'organizzazione esamina i record di accesso dei visitatori [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I visitatori con una richiesta di accesso approvata saranno sottoposti ad analisi dei record di accesso nel momento in cui la loro identità verrà verificata a fronte di un documento valido o un badge emesso da Microsoft. Come descritto nel controllo PE-3, i visitatori dei data center devono essere costantemente accompagnati. |


 ### <a name="nist-800-53-control-pe-8-1"></a>Controllo PE-8 (1) NIST 800-53

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Record di accesso dei visitatori | Gestione/analisi automatizzata dei record

**PE-8 (1)** L'organizzazione adotta meccanismi automatizzati per facilitare la gestione e l'analisi dei record di accesso dei visitatori.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure gestisce i record di accesso ai data center in DCAT sotto forma di richieste DCAT approvate. Le richieste DCAT possono essere approvate solo dal team DCM. I livelli di accesso nel data center vengono assegnati e gestiti all'interno di DCAT. L'accesso ai data center viene esaminato ogni trimestre. Tutti gli accessi al data center di Azure vengono registrati in DCAT e sono disponibili per potenziali indagini future. I visitatori devono essere costantemente accompagnati. L'accesso degli accompagnatori all'interno del data center viene registrato nel sistema di monitoraggio degli allarmi e, se necessario, può essere correlato al visitatore per indagini successive. L'accesso dei visitatori viene costantemente esaminato dall'accompagnatore assegnato e dal CRS con telecamere a circuito chiuso e il sistema di monitoraggio degli allarmi. I visitatori non sono autorizzati all'accesso e devono essere costantemente accompagnati. |


 ## <a name="nist-800-53-control-pe-9"></a>Controllo PE-9 NIST 800-53

#### <a name="power-equipment-and-cabling"></a>Apparecchiature di alimentazione e cavi

**PE-9** L'organizzazione protegge le apparecchiature di alimentazione e i cavi del sistema informativo da danni e distruzione.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure offre spazi protettivi ed etichettatura appropriata dei cavi. Le apparecchiature dell'infrastruttura di Microsoft Azure, ad esempio cavi, linee elettriche e generatori di riserva, devono essere collocate in ambienti progettati per la protezione da rischi ambientali, ad esempio furti, incendi, esplosivi, fumo, acqua, polvere, vibrazioni, terremoti, prodotti chimici dannosi, interferenze elettriche, interruzioni di corrente e disturbi elettrici (picchi). Tutte le risorse dei servizi online mobili (ad esempio, rack, server, dispositivi di rete) devono essere bloccate o fissate per garantire protezione da furti o danni indotti da spostamento. I cavi di alimentazione e del sistema informativo all'interno di un ambiente Microsoft Azure vengono etichettati in modo appropriato e protetti da intercettazioni o danni. I cavi di alimentazione e del sistema informativo sono separati tra loro in tutti i punti all'interno di un ambiente per evitare interferenze. |


 ## <a name="nist-800-53-control-pe-10a"></a>Controllo PE-10.a NIST 800-53

#### <a name="emergency-shutoff"></a>Arresto di emergenza

**PE-10.a** L'organizzazione consente di arrestare l'alimentazione al sistema informativo o ai singoli componenti di sistema in situazioni di emergenza.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure ha installato interruttori EPO (arresto di emergenza) all'interno dei data center, in base alle norme antincendio locali. In alcuni data center gestiti di Microsoft Azure, la progettazione non richiede più questi interruttori. |


 ## <a name="nist-800-53-control-pe-10b"></a>Controllo PE-10.b NIST 800-53

#### <a name="emergency-shutoff"></a>Arresto di emergenza

**PE-10.b** L'organizzazione installa commutatori o dispositivi per l'arresto di emergenza in [assegnazione: posizione definita dall'organizzazione nelle vicinanze del sistema informativo o del componente di sistema] per agevolare l'accesso al personale in modo sicuro.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Questi interruttori vengono posizionati in modo strategico per consentire l'attivazione in situazioni di emergenza. Gli interruttori possono essere posizionati negli ambienti di condivisione, nei centri operativi delle strutture o secondo quanto indicato dalle norme antincendio locali. |


 ## <a name="nist-800-53-control-pe-10c"></a>Controllo PE-10.c NIST 800-53

#### <a name="emergency-shutoff"></a>Arresto di emergenza

**PE-10.c** L'organizzazione protegge la funzionalità di arresto di emergenza da attivazioni non autorizzate.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Per impedire l'attivazione accidentale, gli interruttori possono avere un alloggiamento protettivo, richiedere l'attivazione a due fattori o emettere un allarme acustico di avvertimento prima dell'attivazione. Gli interruttori EPO sono anche sottoposti a videosorveglianza. |


 ## <a name="nist-800-53-control-pe-11"></a>Controllo PE-11 NIST 800-53

#### <a name="emergency-power"></a>Alimentazione di emergenza

**PE-11** L'organizzazione fornisce un gruppo di continuità a breve termine per facilitare [selezione (uno o più): un arresto regolato del sistema informativo; la transizione del sistema informativo a un alimentatore alternativo a lungo termine] in caso di perdita della fonte di alimentazione principale.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure ha implementato un sistema di alimentazione di emergenza proteggendo circuiti e apparecchiature del data center con un gruppo di continuità (UPS) che fornisce alimentazione a breve termine in attesa che i generatori tornino disponibili. |


 ### <a name="nist-800-53-control-pe-11-1"></a>Controllo PE-11 (1) NIST 800-53

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Alimentazione di emergenza | Alimentatore alternativo a lungo termine - Capacità operativa minima

**PE-11 (1)** L'organizzazione fornisce un alimentatore alternativo a lungo termine per il sistema informativo in grado di mantenere la capacità operativa minima in caso di perdita prolungata della fonte di alimentazione principale.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure ha implementato un alimentatore alternativo a lungo termine per il sistema informativo in grado di mantenere la capacità operativa minima in caso di perdita prolungata della fonte di alimentazione principale. In caso di calo o interruzione di corrente a un livello di tensione non accettabile, il sistema UPS si attiva immediatamente e assume il carico di alimentazione. In questo modo viene garantito un livello di alimentazione sufficiente per l'esecuzione dei server fino al ripristino dei generatori. I generatori di emergenza forniscono alimentazione di riserva in caso di interruzioni prolungate o manutenzioni pianificate e possono alimentare i data center con riserve di combustibile sul posto in caso di calamità naturale. Azure gestisce un generatore diesel in molti nostri data center. I generatori di riserva vengono usati in base alle esigenze per mantenere la stabilità della rete o in casi di interventi straordinari di riparazione e manutenzione che richiedono di scollegare i data center dalla rete elettrica. |


 ## <a name="nist-800-53-control-pe-12"></a>Controllo PE-12 NIST 800-53

#### <a name="emergency-lighting"></a>Illuminazione di emergenza

**PE-12** L'organizzazione impiega e gestisce un sistema di illuminazione automatica di emergenza per il sistema informativo che si attiva in caso di interruzione dell'alimentazione e che copre le uscite di emergenza e di sicurezza all'interno della struttura.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I data center di Microsoft Azure (con lease e completamente gestiti) implementano un sistema di illuminazione di emergenza dall'alto su circuiti dedicati, supportato da gruppi di continuità e generatori (vedere il controllo PE-11). Il sistema di illuminazione automatica di emergenza viene implementato lungo le uscite di emergenza e sicurezza e all'interno degli ambienti di condivisione in conformità al Life Safety Code della National Fire Protection Association. In caso di perdita di corrente, l'illuminazione di emergenza passerà automaticamente all'alimentazione fornita dal gruppo di continuità e dai generatori. I sistemi di illuminazione di emergenza nei data center di Microsoft Azure sono sottoposti a manutenzione di routine per garantire che rimangano sempre perfettamente funzionanti. |


 ## <a name="nist-800-53-control-pe-13"></a>Controllo PE-13 NIST 800-53

#### <a name="fire-protection"></a>Protezione antincendio

**PE-13** L'organizzazione impiega e gestisce dispositivi/sistemi di rilevamento ed estinzione di incendi per il sistema informativo, supportati da una fonte di energia indipendente.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure ha implementato un livello di protezione antincendio installando sistemi di rilevamento ed estinzione nei data center di Microsoft Azure. <br /> I data center di Microsoft Azure implementano efficaci meccanismi di rilevamento degli incendi. L'approccio di protezione antincendio di Microsoft Azure prevede l'uso di rilevatori di fumo fotoelettrici installati sotto il pavimento e sul soffitto, integrati con irrigatori antincendio. In ogni ambiente di condivisione sono anche installati sistemi Xtralis VESDA (Very Early Smoke Detection Apparatus) che monitorano l'aria. Le unità VESDA sono sistemi di campionamento dell'aria installati in varie aree altamente sensibili. Le unità VESDA garantiscono una risposta investigativa prima che venga attivato un allarme antincendio. <br /> Nei data center sono installate cassette antincendio per la segnalazione manuale di allarmi. Nei data center sono anche disponibili estintori, che vengono ispezionati, controllati ed etichettati in modo appropriato ogni anno. Il personale addetto alla sicurezza sorveglia tutte le aree dell'edificio più volte durante lo stesso turno. Il personale addetto ai data center esegue un'ispezione giornaliera sul sito, per verificare che vengano sempre rispettati tutti i requisiti antincendio. <br /> Le aree contenenti apparecchiature elettriche sensibili (ambienti di condivisione, sale dei permutatori e così via) sono protette da impianti sprinkler a preazione con doppio interblocco (tubazioni a secco). Questi impianti con tubazioni a secco sono costituiti da un sistema a preazione in due fasi che, per rilasciare acqua, prevede l'attivazione dell'irrigatore (per via del calore) e il rilevamento del fumo. L'attivazione dell'irrigatore rilascia la pressione nei tubi, che si riempiono di acqua. Viene emessa acqua anche quando si attiva un rilevatore di fumo o calore. <br /> I sistemi di rilevamento/estinzione di incendi e di illuminazione di emergenza sono collegati ai gruppi di continuità e ai generatori dei data center, garantendo una fonte di alimentazione ridondante. |


 ### <a name="nist-800-53-control-pe-13-1"></a>Controllo PE-13 (1) NIST 800-53

#### <a name="fire-protection--detection-devices--systems"></a>Protezione antincendio | Dispositivi/sistemi di rilevamento

**PE-13 (1)** L'organizzazione impiega dispositivi/sistemi di rilevamento di incendi per il sistema informativo che si attivano automaticamente e inviano segnalazioni a [assegnazione: personale o ruoli definiti dall'organizzazione] e [assegnazione: risponditori di emergenza definiti dall'organizzazione] in caso di incendio.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure impiega dispositivi/sistemi di rilevamento di incendi che si attivano automaticamente e inviano una segnalazione al personale addetto al data center e ai risponditori di emergenza in caso di incendio. Nel caso in cui si attivi un meccanismo di rilevamento di incendi in un ambiente di condivisione, la caserma dei vigili del fuoco di zona riceve automaticamente una segnalazione dal sistema antincendio. I sistemi di protezione e rilevamento di incendi sono anche collegati al sistema di sicurezza che allertano la struttura e il personale addetto alla sicurezza. |


 ### <a name="nist-800-53-control-pe-13-2"></a>Controllo PE-13 (2) NIST 800-53

#### <a name="fire-protection--suppression-devices--systems"></a>Protezione antincendio | Dispositivi/sistemi di estinzione

**PE-13 (2)** L'organizzazione impiega dispositivi/sistemi di estinzione di incendi per il sistema informativo che inviano una segnalazione automatica in caso di attivazione a [assegnazione: personale o ruoli definiti dall'organizzazione] e [assegnazione: risponditori di emergenza definiti dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Nel caso in cui si attivi nel data center un sistema di estinzione, la caserma dei vigili del fuoco di zona riceve automaticamente una segnalazione dal sistema antincendio. I sistemi di protezione e rilevamento di incendi sono anche collegati al sistema di sicurezza che allertano la struttura e il personale addetto alla sicurezza. |


 ### <a name="nist-800-53-control-pe-13-3"></a>Controllo PE-13 (3) NIST 800-53

#### <a name="fire-protection--automatic-fire-suppression"></a>Protezione antincendio | Estinzione automatica di incendi

**PE-13 (3)** L'organizzazione impiega una funzionalità automatica di estinzione di incendi per il sistema informativo quando la struttura non è gestita in modo continuo.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. I data center di Microsoft Azure sono gestiti dal personale 24 ore su 24, 7 giorni su 7. I sistemi antincendio si attivano automaticamente senza alcun intervento manuale quando viene rilevato un allarme antincendio. |


 ## <a name="nist-800-53-control-pe-14a"></a>Controllo PE-14.a NIST 800-53

#### <a name="temperature-and-humidity-controls"></a>Controlli di temperatura e umidità

**PE-14.a** L'organizzazione gestisce temperatura e umidità all'interno della struttura in cui si trova il sistema informativo a [assegnazione: livelli accettabili definiti dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure gestisce i livelli di temperatura e umidità in base alle linee guida dell'American Society of Heating, Refrigerating and Air-conditioning Engineers (ASHRAE). I livelli di temperatura e umidità vengono costantemente monitorati dal BMS (Building Management System) del data center. |


 ## <a name="nist-800-53-control-pe-14b"></a>Controllo PE-14.b NIST 800-53

#### <a name="temperature-and-humidity-controls"></a>Controlli di temperatura e umidità

**PE-14.b** L'organizzazione monitora i livelli di temperatura e umidità [assegnazione: frequenza definita dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Nei data center di Microsoft Azure i livelli di temperatura e umidità vengono costantemente monitorati dal BMS (Building Management System). Il personale addetto al data center monitora il BMS dal centro operativo della struttura, in modo da poter gestire la temperatura e l'umidità nel data center prima che vengano superati i punti di allarme. |


 ### <a name="nist-800-53-control-pe-14-2"></a>Controllo PE-14 (2) NIST 800-53

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Controlli di temperatura e umidità | Monitoraggio con allarmi/notifiche

**PE-14 (2)** L'organizzazione impiega il monitoraggio di temperatura e umidità che emette un allarme o una notifica in caso di cambiamenti potenzialmente dannosi per il personale o le apparecchiature.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Nei data center di Microsoft Azure i livelli di temperatura e umidità vengono costantemente monitorati dal BMS (Building Management System). Il personale addetto al data center monitora il BMS dal centro operativo della struttura, in modo da poter gestire la temperatura e l'umidità nel data center prima che vengano superati i punti di allarme. |


 ## <a name="nist-800-53-control-pe-15"></a>Controllo PE-15 NIST 800-53

#### <a name="water-damage-protection"></a>Protezione dai danni idrici

**PE-15** L'organizzazione protegge il sistema informativo da danni derivanti da perdite idriche fornendo valvole di chiusura o isolamento accessibili, perfettamente funzionanti e note al personale addetto.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure fornisce sistemi di rilevamento di perdite idriche in aree a rischio (ad esempio, le unità di trattamento aria). Anche i sistemi di estinzione includono allarmi di rilevamento di perdite idriche monitorati. Il sistema di rilevamento di perdite idriche è integrato nel sistema di allarme e segnalazione della struttura. Gli irrigatori nei data center sono suddivisi in zone. Il personale addetto al data center ha familiarità con le procedure di emergenza che richiedono l'uso di valvole di chiusura dell'acqua e con le posizioni in cui sono installate. Gli irrigatori possono essere chiusi singolarmente o in gruppo tramite valvole a saracinesca. Tutti gli irrigatori presenti nelle aree critiche sono costituiti da impianti sprinkler a preazione con doppio interblocco che richiedono due forme di attivazione prima dell'avvio del flusso. La pressione del sistema di irrigazione è monitorata e attiva un allarme in caso di perdita idrica. |


 ### <a name="nist-800-53-control-pe-15-1"></a>Controllo PE-15 (1) NIST 800-53

#### <a name="water-damage-protection--automation-support"></a>Protezione da danni idrici | Supporto di automazione

**PE-15 (1)** L'organizzazione impiega meccanismi automatizzati per rilevare la presenza di acqua in prossimità del sistema informativo e invia una segnalazione a [assegnazione: personale o ruoli definiti dall'organizzazione].

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure impiega meccanismi automatizzati per rilevare la presenza di acqua nei data center e invia un'allerta al personale addetto al data center. Azure fornisce sistemi di rilevamento di perdite idriche in aree a rischio (ad esempio, le unità di trattamento aria). Anche i sistemi di estinzione includono allarmi di rilevamento di perdite idriche monitorati. Il sistema di rilevamento di perdite idriche è integrato nel sistema di allarme e segnalazione della struttura. La pressione del sistema di irrigazione è monitorata e attiva un allarme in caso di perdita idrica. |


 ## <a name="nist-800-53-control-pe-16"></a>Controllo PE-16 NIST 800-53

#### <a name="delivery-and-removal"></a>Recapito e rimozione

**PE-16** L'organizzazione autorizza, monitora e controlla [assegnazione: tipi di componenti del sistema informativo definiti dall'organizzazione] in ingresso e in uscita dalla struttura e ne gestisce i record.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Microsoft Azure implementa questo requisito per conto dei clienti. Microsoft Azure implementa severi controlli e normative sugli elementi che possono entrare e uscire dal data center. Tutte le risorse e i componenti di sistema vengono monitorati nel database dello strumento di gestione delle risorse. |


 ## <a name="nist-800-53-control-pe-17a"></a>Controllo PE-17.a NIST 800-53

#### <a name="alternate-work-site"></a>Sede di lavoro alternativa

**PE-17.a** L'organizzazione impiega [assegnazione: controlli di sicurezza definiti dall'organizzazione] nelle sedi di lavoro alternative.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le disposizioni delle sedi di lavoro alternative a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pe-17b"></a>Controllo PE-17.b NIST 800-53

#### <a name="alternate-work-site"></a>Sede di lavoro alternativa

**PE-17.b** L'organizzazione valuta come fattibile l'efficacia dei controlli di sicurezza nelle sedi di lavoro alternative.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le disposizioni delle sedi di lavoro alternative a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pe-17c"></a>Controllo PE-17.c NIST 800-53

#### <a name="alternate-work-site"></a>Sede di lavoro alternativa

**PE-17.c** L'organizzazione consente ai dipendenti di comunicare con il personale addetto alla protezione delle informazioni in caso di problemi di sicurezza.

**Responsabilità:** `Customer Only`

|||
|---|---|
| **Cliente** | Le disposizioni delle sedi di lavoro alternative a livello aziendale del cliente possono essere sufficienti per soddisfare i requisiti di questo controllo. |
| **Provider (Microsoft Azure)** | Non applicabile |


 ## <a name="nist-800-53-control-pe-18"></a>Controllo PE-18 NIST 800-53

#### <a name="location-of-information-system-components"></a>Collocazione dei componenti del sistema informativo

**PE-18** L'organizzazione colloca i componenti del sistema informativo all'interno della struttura per ridurre al minimo possibili danni da [assegnazione: rischi fisici e ambientali definiti dall'organizzazione] e potenziali accessi non autorizzati.

**Responsabilità:** `Azure Only`

|||
|---|---|
| **Cliente** | I clienti non possono accedere fisicamente alle risorse di sistema nei data center di Azure. |
| **Provider (Microsoft Azure)** | Azure implementa un approccio strategico di progettazione dei data center per soddisfare i requisiti di collocazione dei componenti del sistema informativo. Le apparecchiature di tutti i servizi online di Microsoft sono collocate in ambienti progettati per la protezione da rischi ambientali, ad esempio furti, incendi, esplosivi, fumo, acqua, polvere, vibrazioni, terremoti, prodotti chimici dannosi, interferenze elettriche, interruzioni di corrente, disturbi elettrici (picchi) e radiazioni. La struttura e l'infrastruttura hanno implementato supporti antisismici per la protezione dai rischi ambientali. Le sale dei permutatori e gli ambienti di condivisione sono protetti con controllo di accesso, allarmi e video. La struttura è anche sorvegliata da responsabili della sicurezza 24 ore su 24, 7 giorni su 7. Tutte le risorse Azure mobili sono bloccate o fissate per garantire protezione da furti o danni indotti da spostamento. |


