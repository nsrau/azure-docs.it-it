---
title: Case study Azure del database SQL di Microsoft Azure - Snelstart | Microsoft Docs
description: Informazioni su come SnelStart usa il database SQL per espandere rapidamente i servizi aziendali con una frequenza di 1.000 nuovi database SQL di Azure al mese
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 3861814974b95f0c65879158cb02ec727a25c99f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Grazie ad Azure, SnelStart ha rapidamente ampliato i servizi aziendali con una frequenza di 1.000 nuovi database SQL di Azure al mese
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart produce rinomati software di gestione aziendale e finanziaria per le piccole e medie imprese nei Paesi Bassi. I 55.000 clienti sono serviti da un organico composto da 110 dipendenti, tra cui 35 esperti del reparto IT. Grazie al passaggio dal software desktop a un'offerta Software come servizio (SaaS) compilata in Azure, SnelStart ha sfruttato al massimo i servizi incorporati, l'automazione della gestione tramite l'ambiente familiare in C# e l'ottimizzazione delle prestazioni e della scalabilità con provisioning eccessivo o insufficiente delle aziende, usando pool elastici. L'uso di Azure garantisce a SnelStart fluidità nello spostamento dei clienti tra ambiente locale e cloud.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Perché SnelStart ha esteso i servizi dal desktop al cloud
> "Usare Azure comporta la possibilità di offrire software più rapidamente, reagire velocemente alle richieste dei clienti e scalare le soluzioni quando le richieste aumentano".
> 
> - Henry Been, progettista software
> 
> 

SnelStart ha gestito per anni un'azienda di successo nella produzione di software tramite un modello di sviluppo tradizionale: codice, pacchetto, spedizione e ripetizione. Nel corso del tempo, la velocità del cambiamento è aumentata sempre di più. Le normative cambiano di frequente e i clienti necessitano di metodi semplici per elaborare i record finanziari e collaborare con i contabili e con il governo per stare al passo con tali modifiche.

"La spedizione dei software ai clienti era costosa e limitante", spiega Henry Been, progettista software. "I costi di produzione, imballo e spedizione limitavano la frequenza di rilascio di nuovi software. Dovevamo imballare gli aggiornamenti per spedizioni periodiche, ma ciò ostacolava la soddisfazione delle esigenze dei clienti. Non riuscivamo mai ad avere la certezza che i clienti eseguissero l'aggiornamento alla versione più recente del prodotto. Pertanto, era necessario supportare più versioni, con un conseguente processo di supporto che si rendeva altrettanto difficoltoso".

Been aggiunge: "Cercavamo un modo per programmare e rilasciare aggiornamenti a velocità sostenuta per aumentare il ritmo dell'innovazione e creare nuovi servizi per i clienti. Cercavamo anche un modo per automatizzare un numero maggiore di processi al fine di semplificare le esigenze di amministrazione aziendale dei clienti".

Per SnelStart, la soluzione era l'estensione dei servizi e l'acquisizione del titolo di provider SaaS basato su cloud. La piattaforma del database SQL di Azure ha aiutato SnelStart a raggiungere questi obiettivi senza incorrere negli importanti sovraccarichi del reparto IT che una soluzione Infrastruttura come servizio (IaaS) avrebbe comportato.

Il modello cloud consente anche a SnelStart di correggere i bug e fornire rapidamente nuove funzionalità, senza necessità di scaricare e a aggiornare il software da parte dei clienti. Secondo Been, "grazie ai servizi cloud di Azure, possiamo agire rapidamente sulle richieste di modifica da terze parti. Invece di dover spedire una nuova versione a migliaia di clienti, possiamo adattare le informazioni inviate dall'applicazione desktop a nuovi formati richiesti da terze parti".

## <a name="a-modern-company-with-traditional-roots"></a>Un'azienda moderna con radici tradizionali
SnelStart è un'azienda high-tech moderna e agile, con origini umili risalenti al 1964, anno in cui i fondatori hanno dato vita a un'azienda di produzione di ricambi per strumenti musicali. L'azienda di software SnelStart prese avvio negli anni '80, durante la proliferazione dei personal computer. L'azienda aveva bisogno di un'alternativa migliore per il software di contabilità disponibile al momento, quindi si occupò personalmente della questione. Nel 1982, l'azienda creò le fondamenta di quello che sarebbe effettivamente diventato il software di contabilità di SnelStart. Fin dall'inizio, il software fu ammirato per la sua semplicità e velocità, a tal punto che l'azienda cambiò il proprio business core, reinventandosi come azienda di software.

Nel 1995, SnelStart rilascia la prima applicazione di contabilità per Windows. L'applicazione, basata su database Microsoft Visual Basic 1.0 e Microsoft Access, ha consentito di registrare un aumento della base di clienti, con 5.000 utenti in più.

Oggi, SnelStart è un importante fornitore di applicazioni line-of-business (LOB) e di amministrazione aziendale per le piccole e medie imprese e per gli imprenditori autonomi dei Paesi Bassi. Come Carlo Kuip, progettista IT, afferma, "il nostro obiettivo è consentire l'automazione completa dei servizi di amministrazione aziendale per i clienti".

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Ottimizzazione delle prestazioni e dei costi con i pool elastici
SnelStart è stata un pioniere globale nell'adozione di pool elastici. I pool elastici consentono all'azienda di limitare i costi e gestire i requisiti sulle prestazioni in modo più efficiente. Secondo Been, "Usando i pool elastici, è possibile ottimizzare le prestazioni in base alle esigenze dei clienti, senza un provisioning eccessivo. Eseguire il provisioning in base al carico di picco risulterebbe notevolmente costoso. Al contrario, l'opzione per condividere risorse tra più database a scarso uso consente di creare una soluzione economica e con buone prestazioni".

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>I database SQL di Azure aiutano a containerizzare i dati a scopo di isolamento e sicurezza
Il database SQL di Azure consente a SnelStart di spostare su Azure, in modo semplice e trasparente, i dati amministrativi aziendali dei clienti che si trovano in locale. Il database SQL di Azure è un comodo contenitore che offre isolamento, limiti per l'autenticazione, autorizzazione e funzionalità semplici per il backup e il ripristino. I database offrono un modello concettuale ideale per l'amministrazione aziendale. Secondo Carlo Kuip, progettista IT, "gli elementi all'interno dei confini del contenitore conservano dati riservati, fondamentali per l'azienda e l'archiviazione di tali elementi in un database isolato ne garantisce la protezione. Siam in grado di gestire le autorizzazioni a livello di database e di automatizzare perfino la gestione e la scalabilità orizzontale di questi database, senza l'intervento degli amministratori di database (DBA) nello staff".

SQL Data Warehouse di Azure svolge anche un ruolo nella gestione e nella sicurezza di SnelStart, grazie alla capacità di raccogliere i dati di telemetria dell'azienda, ad esempio dati relativi al rilevamento delle intrusioni, alla registrazione delle attività degli utenti e alla connettività.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure elimina i sovraccarichi in modo che gli sviluppatori possano dedicare più tempo alla creazione di iniziative di valore
Il modello della piattaforma Azure ha eliminato il sovraccarico dell'infrastruttura e consentito a SnelStart di automatizzare le distribuzioni tramite le librerie di gestione di C#. Come dichiarato da Kuip, "migliorando le operazioni correnti con uno staff molto ridotto, abbiamo potuto contemporaneamente concentrarci sull'offerta di nuove opzioni di scalabilità, velocità e ripristino di emergenza per i nostri clienti. Il cambiamento verso lo sviluppo di servizi ha liberato risorse che si sono concentrate su nuovi servizi e funzionalità, invece di dedicare tempo al semplice aggiornamento del codice esistente per tenere il passo con le nuove normative o codici tributari". Aggiunge: "Grazie all'automazione della gestione e all'uso dell'offerta SaaS, siamo in grado di offrire più valore ai nostri clienti senza grandi investimenti in termini di personale operativo". Ad esempio, usando i pool elastici di Azure, SnelStart ha potuto aggiungere una vasta gamma di nuove funzionalità, tra cui una più solida integrazione dei dati dei clienti con le banche, nuovi servizi di fatturazione, controlli in background per le piccole aziende e servizi di posta elettronica.

> "Solo nei primi mesi del 2016, abbiamo aumentato le distribuzioni del database SQL di Azure da circa 5.500 a oltre 12.000 e attualmente stiamo aggiungendo circa 1.000 database al mese".
> 
> - Henry Been, progettista software
> 
> 

La gestione dei database viene ulteriormente automatizzata tramite la funzionalità dei processi elastici. Secondo quanto dichiarato da Kuio, "troviamo enormemente utile l'individuazione automatica dei database in un'istanza [server] del database SQL". In questo modo, SnelStart è in grado di eseguire operazioni di gestione nella base clienti in continua crescita dinamica, senza sovraccarichi aggiuntivi.

SnelStart sta anche sviluppando un'API che funge da broker tra i dati dei clienti e le applicazioni compilate da produttori di software di terze parti. Kuip spiega: "Questa API consentirà di aggiungere funzionalità al software, ad esempio eliminando la necessità di immettere dati per le fatture e altri documenti". I clienti non dovranno più digitare manualmente le fatture nel software di contabilità per piccole aziende in uso. Il software di SnelStart inserirà direttamente le informazioni necessarie. Questo consente ai clienti di aggiungere attività di amministrazione aziendale con l'ecosistema di informazioni che emergono dalla trasformazione digitale in atto nel settore.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Come i servizi Azure abilitano SaaS per SnelStart
Grazie ad Azure, SnelStart può essere usato da clienti e contabili sul cloud, in modo più efficiente. Ad esempio, sia i clienti che i contabili possono condividere informazioni accedendo direttamente all'API client di SnelStart, ospitata in Azure. Kuip afferma: "questi servizi riutilizzabili sono disponibili per applicazioni Web a uso dei clienti e forniscono infrastruttura e funzioni comuni al fine di consentire l'accesso all'amministrazione aziendale ai clienti e al software di terze parti usato dai clienti. Tra queste, funzionalità di configurazione del prodotto, gestione delle regole del firewall e gestione dei processi a esecuzione prolungata come i backup".

> Il nostro obiettivo è consentire l'automazione completa dei servizi di amministrazione aziendale ai nostri clienti". 
> 
> - Carlo Kuip, progettista IT
> 
> 

I servizi Web SnelStart consentono anche ai clienti e ai contabili di accedere facilmente ai dati nel pool di database SQL elastici di Azure. Questo modello SaaS, insieme all'elasticità del database e a Azure Resource Manager, fornisce a SnelStart funzionalità di scalabilità che completano ogni distribuzione di Azure. L'implementazione è completamente automatizzata tramite le librerie di gestione in C#.

![Architettura di SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. A partire da giugno 2016, SnelStart dispone di oltre 11.000 database e di oltre 50 pool elastici

## <a name="simplicity-from-the-cloud"></a>Semplicità dal cloud
Dal passaggio a una soluzione Azure basata su cloud, SnelStart è stato in grado di supportare la crescita rapida dei clienti, offrendo servizi e funzionalità innovativi. Secondo Been, "con Azure, siamo in grado di offrire ai clienti aggiornamenti quasi continui, senza ampliare il personale tecnico. E possiamo avere tutte le altre eccezionali funzionalità di Azure, come la scalabilità e il ripristino di emergenza, con un'unica soluzione".

> "Quando abbiamo dismesso definitivamente Redmond, ho ricevuto una chiamata da uno sviluppatore dei Paesi Bassi, il quale mi contattava in merito a un problema specifico. Siamo riusciti a ottenere da Microsoft una modifica nell'ambiente di produzione entro 48 ore per risolvere il problema".
> 
> - Henry Been, progettista software
> 
> 

SnelStart apprezza notevolmente la solida partnership sviluppata con il team responsabile del database SQL di Microsoft Azure. Come afferma Kuip, "ci confrontiamo sulle funzionalità e su come usare la tecnologia, abitudine apprezzata da entrambe le parti".
L'obiettivo immediato per SnelStart è supportare l'aumento della soddisfazione dei clienti. Come dichiarato da Been, "eliminando le limitazioni tecniche e di risorse che avevamo in qualità di fornitori di software indipendente, non esiste alcun limite al potenziale di crescita dell'azienda".

## <a name="more-information"></a>Altre informazioni
* Per altre informazioni sui pool elastici, vedere [pool elastici](sql-database-elastic-pool.md).
* Per altre informazioni sui ruoli Web e i ruoli di lavoro, vedere l'argomento relativo ai [ruoli di lavoro](../fundamentals-introduction-to-azure.md#compute).    
* Per altre informazioni su Azure SQL Data Warehouse, vedere [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Per altre informazioni su SnelStart, vedere [SnelStart](http://www.snelstart.nl).

