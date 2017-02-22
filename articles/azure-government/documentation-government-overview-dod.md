---
title: Panoramica su DoD in Azure per enti pubblici | Documentazione Microsoft
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Dipartimento della Difesa (DoD) in Azure per enti pubblici
## <a name="overview"></a>Panoramica
Il DOD (Department of Defense) usa Azure per enti pubblici per distribuire un'ampia gamma di soluzioni e carichi di lavoro, inclusi quelli descritti nel documento <a href="http://iasecontent.disa.mil/cloud/SRG/index.html">DoD Cloud Computing Security Requirements Guide, Version 1, Release 2g</a> (Guida ai requisiti di sicurezza del cloud computing del DoD, versione 1, seconda edizione) a livello di impatto 4 (L4) e livello di impatto 5 (L5).

Azure per enti pubblici è il primo e unico servizio cloud commerciale con iperscalabilità, destinatario di una Information Impact Level 5 Provisional Authorization da parte della Defense Information Systems Agency del DoD. Inoltre, sono ora generalmente disponibili aree di Azure per enti pubblici dedicate ai carichi di lavoro dei clienti del DoD degli Stati Uniti.

Uno dei principali stimoli che hanno favorito la migrazione al cloud del Dipartimento della Difesa è la possibilità di consentire alle organizzazioni di concentrarsi sui propri compiti e ridurre al minimo le distrazioni di creazione e gestione di soluzioni IT interne.

Le architetture cloud basate su Azure per enti pubblici consento al personale DoD di dedicarsi prevalentemente al raggiungimento dei propri obiettivi e alla gestione dei servizi IT per le voci doganali e di altri carichi di lavoro delle applicazioni.  Ciò permette il riallineamento delle risorse IT più importati al fine di concentrarsi sulle attività di sviluppo, analisi e sicurezza informatica.

L'elasticità e la flessibilità offerte da Azure garantiscono enormi vantaggi ai clienti di DoD. Eseguire la scalabilità di un carico di lavoro nel cloud è molto più semplice, rapido e conveniente che seguire i tradizionali processi per procurarsi servizi e hardware quando si lavora in locale o e nei data center del Dipartimento della Difesa. Ad esempio, per ottenere un nuovo hardware multi-server potrebbero volerci parecchi mesi (persino per un ambiente di prova) e le elevate spese in conto capitale devono essere approvate. Per contro, se si usa Azure, è possibile configurare una migrazione di prova di un carico di lavoro in poche settimane o addirittura giorni, in modo conveniente (alla conclusione del test, l'ambiente può essere eliminato senza costi correnti).

Questa flessibilità è significativa. La migrazione ad Azure permette ai clienti DoD di beneficiare non soltanto di un concreto risparmio di denaro, ma anche delle nuove opportunità offerte dal cloud. Ad esempio, è facile creare un ambiente di test per ottenere informazioni dettagliate sulle nuove tecnologie, è possibile eseguire la migrazione di un'applicazione e testarla in Azure prima di impegnarsi nella distribuzione di produzione nel cloud. Chi è responsabile di garantire il raggiungimento degli obiettivi può esplorare altre opzioni più convenienti in tutta semplicità e senza correre alcun rischio.

La sicurezza è un altro aspetto chiave e, sebbene qualsiasi distribuzione cloud richieda una pianificazione appropriata per garantire un'offerta dei servizi sicura e affidabile, in realtà la maggior parte dei carichi di lavoro basati sul cloud (inclusi i carichi di lavoro L4 ) e configurati correttamente in Azure per enti pubblici saranno più sicuri rispetto a molte distribuzioni tradizionali nei centri dati e nelle sedi del Dipartimento della Difesa. Questo perché le agenzia di difesa hanno l'esperienza e le competenze necessarie per proteggere fisicamente tutti gli asset; tuttavia, le aree di superficie dell'IT presentano sfide differenti. La sicurezza informatica è un'area in rapida evoluzione, che richiede particolari competenze e la capacità di sviluppare e distribuire rapidamente le contromisure necessarie. La piattaforma di Azure, sia commerciale che per gli enti pubblici, ora supporta centinaia di migliaia di clienti grazie a una scalabilità che permette a Microsoft di rilevare rapidamente i vettori di attacco in continua evoluzione, così da convogliare le proprie risorse nelle attività di sviluppo e implementazione in tempi rapidi delle difese appropriate.

## <a name="dod-region-qa"></a>Domande e risposte sulle aree DoD

### <a name="what-are-the-azure-government-dod-regions"></a>Quali sono le aree DoD di Azure per enti pubblici? 
Le aree US DoD East e US DoD Central sono aree fisicamente separate di Microsoft Azure, progettate per soddisfare i requisiti di sicurezza del DOD degli Stati Uniti per il cloud computing, in particolar modo per i dati designati a livello di impatto 5 in base al documento DoD Cloud Computing Security Requirements Guide (SRG) (Guida ai requisiti di sicurezza del cloud computing del DoD (SRG)).   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Qual è la differenza tra Azure per enti pubblici e le aree DoD di Azure per enti pubblici? 
Azure per enti pubblici è un cloud della community del governo degli Stati Uniti che offre servizi per clienti federali, statali, locali, tribali, entità soggette a ITAR e provider di soluzioni operanti per loro conto. Tutte le aree di Azure per enti pubblici sono progettate e destinate a soddisfare i requisiti di sicurezza di dati a livello di impatto 5 e standard FedRAMP High.

Le aree DoD di Azure per enti pubblici sono progettate per supportare i requisiti di separazione fisica per i dati a livello di impatto 5, fornendo un'infrastruttura di archiviazione e calcolo dedicata a uso dei soli clienti DoD.  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>Qual è la differenza tra dati a livello di impatto 4 e dati a livello di impatto 5?  
I dati a livello di impatto 4 sono informazioni non classificate controllate (CUI) che possono includere dati soggetti al controllo dell'esportazione, informazioni sulla privacy, informazioni sanitarie protette e altri dati che richiedono la designazione CUI esplicita, ad esempio Riservati all'istituzione competente, Sensibili ai fini dell'applicazione della legge, Informazioni di sicurezza riservate.

I dati a livello di impatto 5 includono informazioni non classificate controllate (CUI) che richiedono un livello di protezione più elevato, se ritenuto necessario dal proprietario delle informazioni, dal diritto pubblico o dalla normativa governativa.  I dati a livello di impatto 5 includono sistemi di sicurezza nazionali non classificati.  Maggiori informazioni sui livelli di impatto SRG, sulle relative caratteristiche e sui requisiti distintivi sono disponibili nella sezione 3 del documento DoD Cloud Computing Security Requirements Guide (Guida ai requisiti di sicurezza del cloud computing del DoD).  

### <a name="what-data-is-categorized-as-impact-level-5"></a>Quali dati sono classificati a livello di impatto 5? 
Il livello 5 comprende informazioni non classificate controllate (CUI) che richiedono un livello di protezione più elevato rispetto al livello 4, se ritenuto necessario dal proprietario delle informazioni, dal diritto pubblico o da altre normative governative. Il livello 5 supporta anche sistemi di sicurezza nazionale (NSS) non classificati.  Questo livello include classificazioni di informazioni NSS e CUI basate sull'istruzione CNSSI-1253 fino a riservatezza moderata e integrità moderata (M-M-x).

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>In che modo Microsoft si contraddistingue nel supporto dei dati a livello di impatto 5? 
I dati a livello di impatto 5, per definizione, possono essere elaborati solo in un'infrastruttura dedicata che garantisca la separazione fisica dei clienti DoD da realtà non federali.  Attraverso le aree US DoD East e US DoD Central, Microsoft offre un servizio esclusivo per i clienti DoD in grado di superare le aspettative dichiarate del DoD e il livello di protezione e funzionalità offerte da qualsiasi altra soluzione cloud commerciale con iperscalabilità.

### <a name="do-these-regions-support-classified-data-requirements"></a>Queste aree supportano requisiti di dati classificati? 
Le aree DoD di Azure per enti pubblici supportano solo dati non classificati fino al livello di impatto 5 compreso.  I dati a livello di impatto 6 vengono definiti come informazioni classificate fino al livello SECRET.

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>Quali organizzazioni nel DoD possono usare le aree DoD di Azure per enti pubblici? 
Le aree US DoD East e US DoD Central sono pensate per supportare la base dei clienti del DoD degli Stati Uniti.  Sono inclusi:
* Ufficio del Segretario della Difesa
* Capi di stato maggiore congiunto
* Stato maggiore congiunto
* Agenzie di difesa
* DoD - Attività sul campo
* Dipartimento dell'Esercito
* Dipartimento della Marina (incluso il Corpo dei Marines degli Stati Uniti)
* Dipartimento dell'Aeronautica
* Guardia costiera degli Stati Uniti
* Comando combattente unificato
* Altri uffici, agenzie, attività e comandi che rientrano sotto il controllo o la supervisione di uno dei suddetti enti approvati

### <a name="are-the-dod-regions-more-secure"></a>Le aree DoD sono più sicure? 
Microsoft gestisce tutti i data center di Azure e l'infrastruttura di supporto in osservanza degli standard locali e internazionali per la sicurezza e conformità alla base degli investimenti e dei risultati di adeguamento di tutte le piattaforme cloud commerciali.  Queste nuove aree DoD offrono garanzie specifiche e l'impegno a soddisfare i requisiti definiti nella SRG DoD per il cloud computing.

### <a name="why-are-there-multiple-dod-regions"></a>Perché sono presenti più aree DoD? 
Con più aree DoD, Microsoft offre ai clienti la possibilità di progettare delle proprie soluzioni per scenari di ripristino di emergenza in diverse aree geografiche, al fine di garantire la continuità aziendale e soddisfare i requisiti per l'accreditamento del sistema.  Inoltre, i clienti possono ottimizzare le prestazioni tramite la distribuzione di soluzioni nell'area più vicina alla loro posizione fisica.

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>Queste aree DoD sono connesse alla NIPRNet? 
Il DoD impone che i servizi cloud commerciali usati per CUI siano collegati ai clienti tramite un punto di accesso cloud (CAP).  Di conseguenza, le aree DoD di Azure sono collegate alla NIPRNet tramite connessioni ridondanti a più CAP geograficamente distribuiti.  Un CAP DoD è un sistema di dispositivi di monitoraggio e protezione dei limiti di rete che garantisce la protezione della rete e dei servizi del sistema di informazioni DoD.

### <a name="what-does-general-availability-mean"></a>Che cosa significa disponibilità generale? 
Con disponibilità generale si indica che le aree DoD in Azure per enti pubblici possono essere usate per sostenere i carichi di lavoro di produzione e che saranno supportati contratti di servizio sovvenzionati per tutti i servizi distribuiti nelle aree e anche generalmente disponibili.

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>In che modo un cliente DoD può acquistare i servizi DoD di Azure per enti pubblici? 
I servizi DoD di Azure per enti pubblici possono essere acquistati da enti qualificati attraverso gli stessi canali di rivendita di Azure per enti pubblici.  In linea con l'impegno di Microsoft nel semplificare piano di acquisto e stima dei costi dei servizi cloud, i prezzi per le aree DoD in Azure per enti pubblici saranno inclusi nel Calcolatore dei prezzi di Azure al momento della disponibilità generale.  I servizi DoD di Azure per enti pubblici consentono scalabilità verticale rapida per soddisfare la domanda, permettendoti di pagare solo per ciò che usi.
Non sarà richiesta alcuna modifica contrattuale per i clienti con contratto Enterprise che già usano Azure per enti pubblici.  

### <a name="how-are-the-dod-regions-priced"></a>Come vengono determinati i prezzi delle aree DoD? 
Le aree DoD usano prezzi basati sulle aree.  Ciò significa che i costi dei servizi per clienti DoD convalidati si basano sull'area di Azure per enti pubblici in cui si eseguono i carichi di lavoro.  Per informazioni più specifiche sui prezzi, consultare l'Account Executive Microsoft.  I prezzi per le aree DoD saranno forniti tramite il calcolatore su Azure.com in un secondo momento.

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>In che modo un'organizzazione DoD viene convalidata per le aree DoD di Azure per enti pubblici? 
Per ottenere l'accesso alle aree DoD di Azure, i clienti devono completare un processo di valutazione preventiva per verificare l'organizzazione e l'uso previsto dell'ambiente DoD di Azure.  Dopo il completamento corretto del processo di valutazione preventiva, Microsoft fornisce al richiedente aziendale altre istruzioni per creare una sottoscrizione, accedere all'ambiente e fornire il controllo degli accessi in base al ruolo ad altri membri dell'organizzazione.

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>I fornitori di software indipendenti e i provider di soluzioni create in Azure possono distribuire soluzioni nelle aree DoD di Azure per enti pubblici? 
I provider di soluzioni con offerte di servizi cloud create in Azure possono distribuire soluzioni single-tenant e multi-tenant solo DoD nelle aree DoD di Azure per enti pubblici.  Tali provider devono prima dimostrare l'idoneità fornendo prove documentate di un contratto in essere con un ente DoD approvato o devono disporre di una lettera di sponsorizzazione di un ente DoD approvato.  I provider di servizi nelle aree DoD di Azure per enti pubblici devono includere difesa della rete di computer, segnalazione di problemi e personale selezionato per l'uso delle soluzioni di gestione di informazioni a livello di impatto 5 comprese nella loro offerta.  Sono disponibili informazioni aggiuntive per i provider di soluzioni nel documento DoD Cloud Computing Security Requirements Guide (Guida ai requisiti di sicurezza del cloud computing del DoD).

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>Office 365 o Microsoft Dynamics 365 faranno parte di questa offerta? 
Microsoft fornisce servizi di Office 365 per il DoD a livello di impatto 5 contestualmente a questa offerta.  Per Dynamics 365 è prevista un'offerta di servizi di livello di impatto 5 dalle aree DoD di Azure, che sarà disponibile in un secondo momento.

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>In che modo posso collegarmi alle aree DoD dopo aver ottenuto una sottoscrizione? 
Le aree DoD sono disponibili tramite il portale di gestione di Azure per enti pubblici.  I clienti DoD autorizzati all'uso visualizzeranno le aree elencate come opzioni disponibili durante la distribuzione dei servizi disponibili.  Per indicazioni generali sulla gestione delle sottoscrizioni di Azure per enti pubblici, consultare la nostra documentazione.

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>Quali servizi fanno parte dell'ambito di accreditamento del livello di impatto 5? 
Azure è un servizio classico che viene arricchito ogni settimana di nuovi servizi e funzionalità, con la portata dell'offerta dei servizi in costante espansione.  Per informazioni più aggiornate, visitare il nostro <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Centro protezione Microsoft.


## <a name="next-steps"></a>Passaggi successivi:
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Pagina Web del Dipartimento della Difesa (DoD) nel Centro protezione Microsoft </a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html"> Guida ai requisiti di sicurezza del cloud computing del DoD, versione 1, seconda edizione </a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/"> Canali dei rivenditori di Azure per enti pubblici

<a href="https://blogs.msdn.microsoft.com/azuregov/">log di Microsoft Azure per enti pubblici. </a>




<!--HONumber=Jan17_HO3-->


