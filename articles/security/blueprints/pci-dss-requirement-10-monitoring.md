---
title: Piano per l'elaborazione dei pagamenti di Azure - Requisiti per il monitoraggio
description: Requisito 10 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Requisiti per il monitoraggio per ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-10"></a>Requisito 10 di PCI DSS

**Tenere traccia e monitorare tutti gli accessi alle risorse di rete e ai dati di titolari di carte**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

La disponibilità di meccanismi di registrazione e la possibilità di tenere traccia delle attività degli utenti sono cruciali per prevenire e rilevare i tentativi di compromissione dei dati e ridurne al minimo l'impatto. La presenza di log in tutti gli ambienti consente di ottenere dati completi per monitoraggio, avvisi e analisi in caso di anomalie. Determinare la causa di una compromissione è molto difficile, se non impossibile, senza log delle attività del sistema.

## <a name="pci-dss-requirement-101"></a>Requisito 10.1 di PCI DSS

**10.1** Implementare log di controllo per collegare tutti gli accessi ai componenti di sistema a ogni singolo utente.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure limita l'accesso agli strumenti di amministrazione e diagnostica al personale autorizzato con responsabilità professionali pertinenti. Microsoft Azure limita l'accesso con privilegi agli strumenti usati nell'ambiente di produzione in base ai principi dei privilegi minimi. Microsoft Azure registra e gestisce un log di tutti gli accessi utente ai componenti di sistema di Microsoft Azure nell'ambiente della piattaforma.<br /><br />I componenti della piattaforma Microsoft Azure (inclusi sistema operativo, CloudNet, Fabric e così via) sono configurati per registrare e raccogliere gli eventi di sicurezza. L'attività dell'amministratore nella piattaforma Microsoft Azure viene registrata. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include funzionalità di registrazione complete per tutte le attività del sistema e degli utenti, inclusa la registrazione dei dati di titolari di carte. Per altre informazioni, vedere [PCI Guidance - Logging](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Registrazione).|



## <a name="pci-dss-requirement-102"></a>Requisito 10.2 di PCI DSS

**10.2** Implementare log di controllo automatizzati per tutti i componenti del sistema per ricostruire gli eventi seguenti:
- **10.2.1** Tutti gli accessi utente ai dati di titolari di carte
- **10.2.2** Tutte le azioni eseguite da qualsiasi utente con privilegi di utente root o amministratore
- **10.2.3** Accesso a tutti i log di controllo
- **10.2.4** Tentativi di accesso logico non validi
- **10.2.5** Uso e modifiche dei meccanismi di identificazione e autenticazione (compresi, a titolo esemplificativo, creazione di nuovi account ed elevazione dei privilegi) e tutte le modifiche, le aggiunte o le eliminazioni per gli account con privilegi root o di amministratore
- **10.2.6** Inizializzazione, arresto o sospensione dei log di controllo
- **10.2.7** Creazione ed eliminazione di oggetti a livello di sistema

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure limita l'accesso agli strumenti di amministrazione e diagnostica al personale autorizzato con responsabilità professionali pertinenti. Microsoft Azure limita l'accesso con privilegi agli strumenti usati nell'ambiente di produzione in base ai principi dei privilegi minimi. Microsoft Azure registra e gestisce un log di tutti gli accessi utente ai componenti di sistema di Microsoft Azure nell'ambiente della piattaforma.<br /><br />I componenti della piattaforma Microsoft Azure (inclusi sistema operativo, CloudNet, Fabric e così via) sono configurati per registrare e raccogliere gli eventi di sicurezza. L'attività dell'amministratore nella piattaforma Microsoft Azure viene registrata. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include funzionalità di registrazione complete per tutte le attività del sistema e degli utenti, inclusa la registrazione dei dati di titolari di carte. Per altre informazioni, vedere [PCI Guidance - Logging](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Registrazione).|



## <a name="pci-dss-requirement-103"></a>Requisito 10.3 di PCI DSS

**10.3** Registrare almeno le voci seguenti dei log di controllo per tutti i componenti di sistema per ogni evento:
- **10.3.1** Identificazione utente
- **10.3.2** Tipo di evento
- **10.3.3** Data e ora
- **10.3.4** Indicazione di esito positivo o negativo
- **10.3.5** Origine dell'evento
- **10.3.6** Identità o nome dei dati interessati, del componente di sistema o della risorsa

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Per Microsoft Azure esistono procedure consolidate per la sincronizzazione dei server e dei dispositivi di rete nell'ambiente di Microsoft Azure con i server di riferimento orario NTP di strato 1 sincronizzati con i satelliti GPS (Global Positioning System). La sincronizzazione viene eseguita automaticamente ogni cinque minuti. Microsoft Azure è responsabile di assicurarsi che gli host del servizio sincronizzino correttamente l'orario. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore registra l'ID utente, il tipo di evento, il timestamp, gli eventi riusciti e non riusciti, l'origine dell'evento e il nome della risorsa come richiesto dal requisito 10.3.|



## <a name="pci-dss-requirement-104"></a>Requisito 10.4 di PCI DSS

**10.4** Usando la tecnologia per la sincronizzazione dell'ora, sincronizzare tutti gli orologi e gli orari critici del sistema ed assicurarsi che sia implementato quanto segue per l'acquisizione, la distribuzione e l'archiviazione dell'ora. 
> [!NOTE]
> NTP (Network Time Protocol) è un esempio di tecnologia per la sincronizzazione dell'ora.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Per Microsoft Azure esistono procedure consolidate per la sincronizzazione dei server e dei dispositivi di rete nell'ambiente di Microsoft Azure con i server di riferimento orario NTP di strato 1 sincronizzati con i satelliti GPS (Global Positioning System). La sincronizzazione viene eseguita automaticamente ogni cinque minuti. Microsoft Azure è responsabile di assicurarsi che gli host del servizio sincronizzino correttamente l'orario. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La sincronizzazione dell'ora per il servizio PaaS viene eseguita da Azure.|



### <a name="pci-dss-requirement-1041"></a>Requisito 10.4.1 di PCI DSS

**10.4.1** L'ora dei sistemi critici è esatta e coerente.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La sincronizzazione dell'ora per il servizio PaaS viene eseguita da Azure.|



### <a name="pci-dss-requirement-1042"></a>Requisito 10.4.2 di PCI DSS

**10.4.2** I dati di ora sono protetti.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La sincronizzazione dell'ora per il servizio PaaS viene eseguita da Azure.|



### <a name="pci-dss-requirement-1043"></a>Requisito 10.4.3 di PCI DSS

**10.4.3** Le impostazioni di ora sono ricevute da fonti accettate dal settore.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La sincronizzazione dell'ora per il servizio PaaS viene eseguita da Azure.|



## <a name="pci-dss-requirement-105"></a>Requisito 10.5 di PCI DSS

**10.5** Proteggere i log di controllo in modo che non possano essere modificati.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Gli strumenti FIM e dei sistemi di rilevamento intrusioni vengono implementati all'interno dell'ambiente Microsoft Azure. Microsoft Azure usa EWS per supportare l'analisi in tempo reale degli eventi all'interno dell'ambiente operativo. I sistemi MA e AIMS generano avvisi quasi in tempo reale per gli eventi che potrebbero compromettere il sistema. <br /><br />La registrazione degli eventi di servizi, utenti e sicurezza (log del server Web, log del server FTP e così via) è abilitata e i log vengono conservati a livello centralizzato. Azure limita l'accesso ai log di controllo al personale autorizzato in base alle responsabilità professionali. I registri eventi vengono archiviati nell'infrastruttura di archiviazione sicura di Azure e vengono conservati per 180 giorni. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore prevede il controllo di tutti gli elementi in OMS. Il backup su un'origine esterna può essere eseguito con [Backup di Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>Requisito 10.5.1 di PCI DSS

**10.5.1** Limitare la visualizzazione dei log di controllo a coloro che realmente necessitano di tali informazioni per motivi professionali.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore prevede il controllo di tutti gli elementi in OMS. Il backup su un'origine esterna può essere eseguito con [Backup di Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>Requisito 10.5.2 di PCI DSS

**10.5.2** Proteggere i file dei log di controllo da modifiche non autorizzate.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore prevede il controllo di tutti gli elementi in OMS. Il backup su un'origine esterna può essere eseguito con [Backup di Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>Requisito 10.5.3 di PCI DSS

**10.5.3** Eseguire immediatamente il backup dei file dei log di controllo in un server di log centralizzato o su supporti difficili da alterare.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore prevede il controllo di tutti gli elementi in OMS. Il backup su un'origine esterna può essere eseguito con [Backup di Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>Requisito 10.5.4 di PCI DSS

**10.5.4** Scrivere i log per tecnologie rivolte al pubblico su un server di log o un dispositivo per supporti sicuro, centralizzato e interno.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore prevede il controllo di tutti gli elementi in OMS. Il backup su un'origine esterna può essere eseguito con [Backup di Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>Requisito 10.5.5 di PCI DSS

**10.5.5** Usare un meccanismo di monitoraggio dell'integrità dei file o un software di rilevamento delle modifiche dei log per assicurarsi che i dati di log esistenti non possano essere modificati senza generare avvisi (sebbene l'aggiunta di nuovi dati non dovrebbe generare avvisi).

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore prevede il controllo di tutti gli elementi in OMS. Il backup su un'origine esterna può essere eseguito con [Backup di Azure](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>Requisito 10.6 di PCI DSS

**10.6** Esaminare i log e gli eventi di sicurezza per tutti i componenti di sistema al fine di identificare anomalie o attività sospette.
 
> [!NOTE]
> Possono essere usati strumenti di raccolta, analisi e generazione di avvisi per i log ai fini della conformità a questo requisito.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Gli strumenti FIM e dei sistemi di rilevamento intrusioni vengono implementati all'interno dell'ambiente Microsoft Azure. Microsoft Azure usa EWS per supportare l'analisi in tempo reale degli eventi all'interno dell'ambiente operativo. I sistemi MA e AIMS generano avvisi quasi in tempo reale per gli eventi che potrebbero compromettere il sistema. <br /><br />La registrazione degli eventi di servizi, utenti e sicurezza (log del server Web, log del server FTP e così via) è abilitata e i log vengono conservati a livello centralizzato. Azure limita l'accesso ai log di controllo al personale autorizzato in base alle responsabilità professionali. I registri eventi vengono archiviati nell'infrastruttura di archiviazione sicura di Azure e vengono conservati per 180 giorni. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) per monitorare, segnalare ed evitare anomalie. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre una vista coerente e consolidata delle raccomandazioni per tutte le risorse di Azure.|



### <a name="pci-dss-requirement-1061"></a>Requisito 10.6.1 di PCI DSS

**10.6.1** Esaminare gli elementi seguenti almeno quotidianamente:
- Tutti gli eventi di sicurezza
- Log di tutti i componenti di sistema che archiviano, elaborano o trasmettono dati di titolari di carte e/o dati di autenticazione sensibili
- Log di tutti i componenti di sistema critici
- Log di tutti i server e i componenti di sistema che eseguono funzioni di sicurezza (ad esempio, firewall, sistemi di rilevamento intrusioni/sistemi di prevenzione intrusioni, server di autenticazione, server di reindirizzamento per e-commerce e così via).

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) per monitorare, segnalare ed evitare anomalie. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre una vista coerente e consolidata delle raccomandazioni per tutte le risorse di Azure.|



### <a name="pci-dss-requirement-1062"></a>Requisito 10.6.2 di PCI DSS

**10.6.2** Esaminare periodicamente i log di tutti gli altri componenti di sistema in base alle politiche e alla strategia di gestione dei rischi dell'organizzazione, secondo quanto stabilito dalla valutazione annuale dei rischi dell'organizzazione.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) per monitorare, segnalare ed evitare anomalie. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre una vista coerente e consolidata delle raccomandazioni per tutte le risorse di Azure.|



### <a name="pci-dss-requirement-1063"></a>Requisito 10.6.3 di PCI DSS

**10.6.3** Mantenere monitorate le eccezioni e le anomalie individuate durante il processo di revisione e intervenire all'occorrenza.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Vedere la sezione "Microsoft Azure" relativa al [requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) per monitorare, segnalare ed evitare anomalie. [Azure Advisor](/azure/advisor/advisor-security-recommendations) offre una vista coerente e consolidata delle raccomandazioni per tutte le risorse di Azure.|



## <a name="pci-dss-requirement-107"></a>Requisito 10.7 di PCI DSS

**10.7** Conservare la cronologia dei log di controllo per almeno un anno, con minimo tre mesi disponibili immediatamente per l'analisi (ad esempio disponibili online, archiviati o recuperabili da backup).

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure conserva i log di controllo per un anno e i 3 mesi più recenti sono accessibili immediatamente tramite il portale interno. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include funzionalità di registrazione complete per tutte le attività del sistema e degli utenti, inclusa la registrazione dei dati di titolari di carte. Per altre informazioni, vedere [PCI Guidance - Logging](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Registrazione).|



## <a name="pci-dss-requirement-108"></a>Requisito 10.8 di PCI DSS

**10.8** **Requisito aggiuntivo solo per provider di servizi:** implementare un processo per il rilevamento tempestivo e la segnalazione di errori dei sistemi di controllo di sicurezza critici, inclusi, a titolo esemplificativo, errori di:
- Firewall
- Sistemi di rilevamento intrusioni/sistemi di prevenzione intrusioni
- FIM
- Anti-virus
- Controlli dell'accesso fisico
- Controlli dell'accesso logico
- Meccanismi per i log di controllo
- Controlli di segmentazione (se usati) 

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.



**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa EWS per supportare l'analisi in tempo reale degli eventi all'interno dell'ambiente operativo. I sistemi MA e AIMS generano avvisi quasi in tempo reale per gli eventi che potrebbero compromettere il sistema. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include funzionalità di registrazione complete per tutte le attività del sistema e degli utenti, inclusa la registrazione dei dati di titolari di carte. Per altre informazioni, vedere [PCI Guidance - Logging](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Registrazione).|



### <a name="pci-dss-requirement-1081"></a>Requisito 10.8.1 di PCI DSS

**10.8.1** **Requisito aggiuntivo solo per provider di servizi:** risolvere gli errori di eventuali controlli di sicurezza critici in maniera tempestiva. I processi di risoluzione degli errori presenti nei controlli di sicurezza devono includere:
- Ripristino delle funzioni di sicurezza
- Identificazione e documentazione della durata (data e ora di inizio e fine) dell'errore di sicurezza
- Identificazione e documentazione delle cause dell'errore, inclusa la causa principale, e documentazione delle attività di correzione richieste per identificare ed eliminare la causa principale
- Identificazione e risoluzione di eventuali problemi di sicurezza emersi durante l'errore
- Esecuzione di una valutazione dei rischi per determinare se sono richieste ulteriori azioni a causa dell'errore di sicurezza
- Implementazione di controlli per impedire il ripetersi della causa dell'errore - Ripresa del monitoraggio dei controlli di sicurezza 

> [!NOTE]
> Questo requisito è una procedura consigliata fino al 31 gennaio 2018 e in seguito diventerà un requisito.


**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure usa EWS per supportare l'analisi in tempo reale degli eventi all'interno dell'ambiente operativo. I sistemi MA e AIMS generano avvisi quasi in tempo reale per gli eventi che potrebbero compromettere il sistema. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include funzionalità di registrazione complete per tutte le attività del sistema e degli utenti, inclusa la registrazione dei dati di titolari di carte. Per altre informazioni, vedere [PCI Guidance - Logging](payment-processing-blueprint.md#logging-and-auditing) (Linee guida per PCI - Registrazione).|



## <a name="pci-dss-requirement-109"></a>Requisito 10.9 di PCI DSS

**10.9** Assicurarsi che i criteri di sicurezza e le procedure operative per il monitoraggio dell'accesso alle risorse di rete e ai dati di titolari di carte siano documentati, applicati e noti a tutte le parti interessate.


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore include un caso d'uso e una descrizione delle modalità di gestione e protezione dei dati di titolari di carte.|




