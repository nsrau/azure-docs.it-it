---
title: Case study Azure del database SQL di Microsoft Azure - Daxko/CSI | Microsoft Docs
description: Informazioni su come Daxko/CSI usa il database SQL per accelerare il ciclo di sviluppo e migliorare i servizi al cliente e le prestazioni
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: app development case study
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 98a83c735a79cdba080dd74af8e2677788c075e3


---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI ha usato Azure per accelerare il ciclo di sviluppo e migliorare i servizi al cliente e le prestazioni
![Logo Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI Software ha affrontato una sfida: la base di clienti dei centri fitness e di ricreazione ha registrato un aumento rapido grazie alla soluzione software aziendale onnicomprensiva. Tuttavia, il supporto delle esigenze dell'infrastruttura IT per la crescita della base di clienti metteva a dura prova lo staff IT aziendale. La società era sempre più vincolata alle crescenti spese operative, in particolare per la gestione dei database in continua crescita. Ancor peggio, le spese operative venivano tagliate dalle risorse di sviluppo impegnate su nuove iniziative, tra cui le funzionalità di mobilità per il software aziendale.

Secondo David Molina, responsabile del reparto Sviluppo del prodotto presso Daxko/CSI, Azure ha fornito a CSI Software il modello di piattaforma distribuita come servizio (PaaS) necessario per semplificare la gestione dei database, aumentare la scalabilità e liberare risorse che potessero concentrarsi sul software e non sulle operazioni. "Il database SQL di Azure ha rappresentato un'ottima opzione per la nostra azienda. Senza la preoccupazione per la manutenzione di SQL Server, un cluster di failover, e per tutte le altre esigenze dell'infrastruttura, la soluzione si è rivelata ideale per noi".

Dopo la migrazione ad Azure, CSI Software necessita di uno staff operativo composto da solo due risorse per gestire oltre 600 database dei clienti. L'azienda usa i pool elastici del database SQL di Azure per spostare i database dei clienti a seconda delle dimensioni e delle esigenze.

Molina continua: "I clienti hanno avvertito immediatamente il cambiamento. Prima di adoperare i pool elastici, i clienti registravano occasionalmente timeout e altri problemi durante i picchi di attività. Con i pool elastici di Azure, possono eseguire picchi di attività in base alle esigenze e usare il software senza problemi".

Oltre a migliorare le prestazioni per i clienti, i pool elastici di Azure liberano risorse CSI Software, che possono concentrarsi sullo sviluppo di nuovi servizi e funzionalità, invece di occuparsi della gestione e delle operazioni. Tali risorse IT hanno aiutato CSI Software a migliorare l'offerta del software aziendale, SpectrumNG, per coinvolgere i membri della palestra, migliorare l'efficienza del personale e fornire al personale e ai membri accesso mobile per operazioni interattive e notifiche in tempo reale.

Azure ha anche consentito a CSI Software di accelerare e migliorare il ciclo di sviluppo e di controllo di qualità (QA) tramite opzioni di automazione. Grazie all'implementazione di Azure nell'azienda, i responsabili della compilazione possono creare pacchetti di componenti con un semplice clic. Come descritto da Molina, "come parte del ciclo di rilascio, il controllo di qualità è ora in grado di eseguire la distribuzione in un ambiente di test in Azure, che riproduce fedelmente il nostro stack di produzione. Siamo in grado di distribuire immediatamente le compilazioni nell'ambiente di sviluppo per vagliare le modifiche. Questo fattore rappresenta per noi una vera conquista, poiché in precedenza non disponevamo di un sistema di parità per i test".

## <a name="offloading-to-the-cloud"></a>Offload nel cloud
Prima di passare al cloud, CSI Software ha compilato correttamente la propria infrastruttura multi-tenant in un datacenter locale a Houston. Con l'espansione, l'azienda ha registrato un incremento di problematiche legate all'acquisto, al provisioning e alla gestione di tutti gli hardware e software necessari per supportare i clienti. La gestione delle operazioni da parte del personale IT ha causato un rallentamento nel provisioning di nuove risorse e nell'implementazione di nuovi servizi ai clienti.

Software CSI ha analizzato le opzioni cloud per eliminare le spese generali e concentrarsi sul codice e non più sulle operazioni. L'azienda ha scoperto che numerosi dei principali provider di cloud offrivano esclusivamente soluzioni di infrastruttura distribuita come servizio (IaaS), che richiedono numerosi operatori IT per la gestione del relativo stack. Alla fine, CSI Software ha stabilito che la soluzione PaaS di Azure fosse più adatta alle proprie esigenze. Molina spiega: "Azure sposta hardware e software del sistema fuori dall'area di lavoro, consentendoci di concentrarci sulle offerte del software, riducendo contestualmente le spese IT".

## <a name="making-the-transition-to-azure"></a>Eseguire la transizione ad Azure
Dopo aver scelto Azure per la soluzione PaaS, CSI Software ha avviato la migrazione dell'infrastruttura di back-end e dei database nel cloud. Prima di Azure, i clienti SpectrumNG dovevano installare un'applicazione client per la comunicazione con un servizio Windows Communication Foundation (WCF) sul back-end. Secondo Molina, "anche se alcuni clienti ospitavano tutti gli elementi nei propri datacenter, abbiamo compilato un prodotto multi-tenant. Ospitavamo tutti gli elementi in un datacenter a Houston, usando SQL Server come archivio dati.

"L'offerta del prodotto includeva anche un portale Web per i membri (scritto in ASP.net), progettato come white-label e per riflettere la presenza del cliente sul Web, nonché dotato di un'API SOAP per supportare le pagine online ed eventuali integrazioni di terze parti".

La migrazione dell'architettura sul cloud non richiedeva molto tempo. Secondo Molina "la maggior parte dello sforzo consisteva nella modifica della modalità di lettura delle informazioni sui file di configurazione, nella modifica della stringa di connessione centralizzata, nell'automazione della creazione dei pacchetti, nel caricamento e nella distribuzione delle versioni".

Per sviluppare l'automazione della compilazione, i progettisti di CSI Software hanno usato Azure PowerShell e le API REST per creare pacchetti e caricarli in un ambiente di gestione temporanea per il rilascio ogni notte.
La transizione completa a una distribuzione Azure basata su cloud è avvenuta rapidamente e senza causare problemi al team IT di CSI Software. "Complessivamente, abbiamo creato un ambiente beta nel cloud in tre-quattro settimane dall'inizio del progetto. Un successo sorprendente la nostra azienda", spiega Molina.

Dopo la configurazione e il testing dell'ambiente, CSI Software ha avviato la migrazione dei clienti. Per i clienti che già usavano l'hosting CSI Software, la transizione è stata quasi diretta. Per i clienti che usavano una distribuzione locale, la migrazione al cloud ha richiesto più tempo, ma non ha comunque comportato problemi per i clienti e per CSI Software.

Lo staff IT di CSI Software ha usato la seguente procedura per avviare i nuovi clienti in Azure:

1. Gli script di Azure PowerShell sono stati usati per creare un nuovo database per i clienti. Tutti i clienti iniziano su un livello Premium per garantire velocità effettiva sufficiente nella fase iniziale della transizione.
2. Quando possibile, CSI Software usa la Migrazione guidata SQL Azure per spostare i dati esistenti in un'istanza di database SQL di Azure.
3. Infine, vengono usati i Microsoft SQL Server Integration Services (SSIS) per risolvere eventuali discrepanze nei dati o per eseguire operazioni di pulizia dati, a seconda delle esigenze.

Oggi, circa il 99% dei clienti di CSI Software è ospitato in Azure, in quattro datacenter regionali (centro-settentrionale, centro-meridionale, est e ovest). Grazie alla presenza di datacenter nell'area geografica di ogni cliente, la latenza viene ridotta al minimo.

## <a name="azure-elastic-pools-free-up-it-resources"></a>I pool elastici di Aure liberano risorse IT
Numerose funzionalità di Azure sono risultate utili a CSI Software per spostare il focus dall'infrastruttura e dalle operazioni allo sviluppo. Probabilmente il vantaggio più importante è dato dai pool elastici.

Al momento, CSI Software fornisce ai clienti circa 550 database. Prima dei pool elastici, era difficile gestire i numerosi database all'interno della struttura di un livello. I responsabili delle operazioni dovevano assegnare livelli di prestazioni in base alle esigenze di picco dei clienti, che richiedeva notevole dispendio di risorse IT. Grazie ai pool elastici, i responsabili possono assegnare ai tenant pool Premium o Standard, a seconda delle esigenze, e quindi spostare i clienti in base alle dimensioni e alle esigenze. I clienti hanno avvertito gli effetti dei pool elastici quasi immediatamente. Prima di adoperare i pool elastici, i clienti registravano timeout e altri problemi durante i picchi. Con i pool elastici, gli utenti possono eseguire picchi di attività in base alle esigenze e continuare a usare SpectrumNG senza problemi.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>La replica geografica attiva di Azure accelera la creazione di report
Numerosi clienti CSI Software stanno traendo vantaggio dalla replica geografica attiva di Azure. La replica geografica attiva consente di configurare fino a quattro database secondari accessibili in lettura nella stessa area del datacenter o in aree diverse. CSI Software usa la replica geografica attiva in due modi: innanzitutto, i database secondari sono disponibili nel caso di un'interruzione del datacenter o di impossibilità di connettersi al database primario. In secondo luogo, i database secondari sono leggibili e possono essere usati per l'offload dei carichi di lavoro di sola lettura, come ad esempio i processi di creazione di report. Alcuni clienti CSI Software usano la replica per accelerare i flussi di lavoro per la creazione di report.

## <a name="csi-software-application-logic-and-architecture"></a>Architettura e logica dell'applicazione CSI Software
SpectrumNG usa i ruoli Web. Poiché l'applicazione è multi-tenant, viene usato un servizio WCF per gestire la richiesta di connessione iniziale dai clienti. Come dichiarato da Molina, "la richiesta identifica ogni cliente, che ci consente quindi di compilare una stringa di connessione nei relativi database per eseguire qualsiasi azione necessaria".

Per il livello Web del servizio, CSI Software usa il ridimensionamento automatico di Azure, in base a data e ora. Le risorse disponibili vengono automaticamente aumentate per soddisfare l'incremento dell'uso durante le ore lavorative, secondo il fuso orario di ciascun datacenter regionale. Viene anche impostata la riduzione automatica delle risorse durante il weekend, quando le esigenze dei clienti sono inferiori.

![Architettura di Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Un ruolo di lavoro dei servizi cloud consente di disegnare dati strutturati dal database SQL di Azure e dati semistrutturati dall'archiviazione di tabelle. Gli utenti SpectrumNG interagiscono con i dati tramite un ruolo Web dei servizi cloud.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Uso di app Web e di un livello di piano Web per le app per dispositivi mobili
L'uso del database SQL di Azure ha consentito di liberare risorse CSI Software per sviluppare nuove iniziative, tra cui una piattaforma completa per dispositivi mobili basata su un'API personalizzata ospitata nelle app Web di Azure. La piattaforma consente ai membri e allo staff della palestra di usare dispositivi mobili per controllare i programmi, prenotare lezioni e ricevere messaggi.

La piattaforma usa Service Oriented Architecture (SOA) per creare un singolo componente, ad esempio un sistema POS o un sistema di vendite, spostarlo in tempo reale in un altro piano Web e scegliere un servizio per supportare tale componente, lasciando tutti gli altri elementi sul piano Web originale. Questa funzione offre a CSI Software massima flessibilità e consente di mantenere bassi i costi.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure consente agli sviluppatori Software CSI di concentrarsi su applicazioni e servizi
Il database SQL di Azure non rappresenta solo un enorme vantaggio per i clienti SpectrumNG, che usufruiscono di un servizio veloce e affidabile, ma è anche un grande successo per lo staff IT e gli sviluppatori di CSI Software. Trasferendo le operazioni nel cloud di Azure, CSI Software ha ridotto i costi legati a risorse e infrastruttura, ha accelerato notevolmente i cicli di sviluppo e non necessita più di gestire i database nel dettaglio per ottimizzare le prestazioni dei tenant.

## <a name="more-information"></a>Altre informazioni
* Per altre informazioni sui pool elastici, vedere [pool elastici](sql-database-elastic-pool.md).
* Per altre informazioni sugli strumenti del database e sulla scalabilità elastica, vedere [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md).
* Per altre informazioni sulla migrazione di un database SQL Server, vedere [Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).
* Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
* Per altre informazioni sui ruoli Web e i ruoli di lavoro, vedere l'argomento relativo ai [ruoli di lavoro](../fundamentals-introduction-to-azure.md#compute).    
* Per altre informazioni sul bus di servizio di Azure, vedere [Bus di servizio](https://azure.microsoft.com/services/service-bus/).
* Per altre informazioni sulla scalabilità automatica, vedere [Come configurare la scalabilità automatica di un servizio cloud](../cloud-services/cloud-services-how-to-scale.md).




<!--HONumber=Dec16_HO2-->


