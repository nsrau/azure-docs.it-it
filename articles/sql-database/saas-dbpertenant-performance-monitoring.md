---
title: "Monitorare le prestazioni di più database SQL di Azure in un'app SaaS multi-tenant | Microsoft Docs"
description: Monitorare e gestire le prestazioni di database SQL di Azure e di pool in un'app SaaS multi-tenant
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: sstein
ms.openlocfilehash: 289f1f99b1661e499fa7132887e2f65e086ad689
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorare e gestire le prestazioni di database SQL di Azure e di pool in un'app SaaS multi-tenant

In questa esercitazione vengono illustrati diversi scenari di gestione delle prestazioni chiave usati nelle applicazioni SaaS. Usando un generatore di carico per simulare l'attività in tutti i database tenant, vengono illustrate le funzionalità di monitoraggio e avviso predefinite del database SQL e dei pool elastici.

L'app del database per tenant SaaS Wingtip Tickets usa un modello di dati a tenant singolo, in cui ogni sede di eventi, ovvero il tenant, ha un database proprio. Come molte applicazioni SaaS, il modello di carico di lavoro tenant previsto è imprevedibile e sporadico. In altre parole, le vendite di biglietti possono verificarsi in qualsiasi momento. Per sfruttare i vantaggi di questo modello di utilizzo tipico dei database, i database tenant sono distribuiti in pool di database elastici. I pool elastici consentono di ottimizzare il costo di una soluzione condividendo le risorse tra molti database. Con questo tipo di modello, è importante monitorare l'utilizzo dei database e delle risorse del pool per assicurarsi che i carichi siano bilanciati in modo ragionevole tra i pool. È anche necessario assicurarsi che i singoli database dispongano delle risorse appropriate e che i pool non si avvicinino ai limiti di [eDTU](sql-database-what-is-a-dtu.md). Questa esercitazione illustra alcuni modi per monitorare e gestire i database e i pool, nonché come adottare misure correttive in risposta a variazioni del carico di lavoro.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Simulare l'utilizzo nei database tenant tramite l'esecuzione di un generatore di carico specificato
> * Monitorare la risposta dei database tenant a un aumento del carico di lavoro
> * Potenziare il pool elastico in risposta al carico maggiore per i database
> * Eseguire il provisioning di un secondo pool elastico per bilanciare il carico delle attività dei database


Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* È stata distribuita l'app del database per tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Deploy and explore the Wingtip Tickets SaaS Database Per Tenant application](saas-dbpertenant-get-started-deploy.md) (Distribuire ed esplorare l'applicazione del database per tenant SaaS Wingtip Tickets)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduzione ai modelli di gestione delle prestazioni SaaS

La gestione delle prestazioni dei database comprende la compilazione e l'analisi dei dati sulle prestazioni, quindi l'adozione delle misure appropriate adattando i parametri in modo da mantenere tempi di risposta accettabili per l'applicazione. Quando si ospitano più tenant, i pool di database elastici rappresentano una soluzione conveniente per fornire e gestire le risorse per un gruppo di database con carichi di lavoro imprevedibili. Con determinati modelli di carico di lavoro, anche solo due database S3 possono trarre vantaggio dalla gestione in un pool.

![diagramma applicazioni](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

I pool e i database nei pool devono essere monitorati per assicurarsi che rimangano entro intervalli di prestazioni accettabili. Ottimizzare la configurazione del pool per soddisfare le esigenze del carico di lavoro aggregato di tutti i database, assicurandosi che le unità eDTU del pool siano appropriate per il carico di lavoro complessivo. Impostare il minimo e il massimo per le unità eDTU per ogni database su valori appropriati per i requisiti specifici di ogni applicazione.

### <a name="performance-management-strategies"></a>Strategie per la gestione delle prestazioni

* Per evitare di dover monitorare manualmente le prestazioni, è consigliabile **impostare l'attivazione di avvisi quando i database o i pool escono dai normali intervalli**.
* Per rispondere alle fluttuazioni a breve termine del livello di prestazioni aggregate di un pool, è possibile **scegliere un livello superiore o inferiore di eDTU per il pool**. Se la fluttuazione si verifica a intervalli regolari o prevedibili, **è possibile pianificare il ridimensionamento automatico del pool**. Ad esempio, ridurre il numero di eDTU quando il carico di lavoro è notoriamente leggero, ad esempio durante la notte o nei fine settimana.
* Per rispondere a fluttuazioni a lungo termine o a modifiche del numero di database, **è possibile spostare singoli database in altri pool**.
* Per rispondere ad aumenti a breve termine del carico per *singoli* database, **è possibile rimuovere singoli database da un pool e assegnare loro un livello di prestazioni singolo**. Quando il carico si riduce di nuovo, il database può essere reinserito nel pool. Quando questo tipo di comportamento è noto in anticipo, i database possono essere spostati preventivamente per assicurarsi che abbiano sempre le risorse necessarie ed evitare effetti sugli altri database nel pool. Se si tratta di un requisito prevedibile, come nel caso di un picco di vendite di biglietti per un evento di grande richiamo, questo comportamento di gestione può essere integrato nell'applicazione.

Il [portale di Azure](https://portal.azure.com) include funzionalità di monitoraggio e avviso predefinite per la maggior parte delle risorse. Per il database SQL, il monitoraggio e gli avvisi sono disponibili per database e pool. Le funzionalità di monitoraggio e avviso predefinite sono specifiche delle risorse, quindi è comodo usarle per un numero limitato di risorse, mentre non sono altrettanto utili quando si usano molte risorse.

Per gli scenari con volumi elevati in cui si lavora con molte risorse, si può usare [Log Analytics (OMS)](saas-dbpertenant-log-analytics.md). Si tratta di un servizio di Azure separato che fornisce funzionalità di analisi per log di diagnostica e dati di telemetria raccolti in un'area di lavoro di Log Analytics. Log Analytics consente di raccogliere dati di telemetria da molti servizi e supporta l'esecuzione di query e l'impostazione di avvisi.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="provision-additional-tenants"></a>Eseguire il provisioning di altri tenant

Anche se i pool possono essere una soluzione conveniente anche con solo due database S3, con l'aumentare del numero di database inseriti nel pool migliora anche il risparmio consentito dalla ripartizione dei costi. Per comprendere meglio il funzionamento della gestione e del monitoraggio delle prestazioni su larga scala, per questa esercitazione è necessario distribuire almeno 20 database.

Se è già stato effettuato il provisioning di un batch di tenant in un'esercitazione precedente, saltare la sezione [Simulare l'utilizzo in tutti i database tenant](#simulate-usage-on-all-tenant-databases).

1. In **PowerShell ISE** aprire …\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenere lo script aperto durante l'esecuzione dei vari scenari presentati in questa esercitazione.
1. Impostare **$DemoScenario** = **1**, **Effettuare il provisioning di un batch di tenant**
1. Premere **F5** per eseguire lo script.

Lo script distribuirà 17 tenant in meno di cinque minuti.

Lo script *New-TenantBatch* usa un set annidato o collegato di modelli di [Resource Manager](../azure-resource-manager/index.md) che creano un batch di tenant, che copia per impostazione predefinita il database **basetenantdb** nel server di catalogo per creare i nuovi database tenant, quindi li registra nel catalogo e infine li inizializza con il nome del tenant e il tipo di sede. Questo comportamento è coerente con il modo in cui l'app effettua il provisioning di un nuovo tenant. Tutte le modifiche apportate a *basetenantdb* vengono applicate agli eventuali nuovi tenant di cui viene effettuato il provisioning in seguito. Vedere l'[esercitazione sulla gestione dello schema](saas-tenancy-schema-management.md) per informazioni su come apportare modifiche allo schema di database tenant *esistenti*, incluso il database *basetenantdb*.

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulare l'utilizzo in tutti i database tenant

È disponibile lo script *Demo-PerformanceMonitoringAndManagement.ps1* che simula un carico di lavoro eseguito su tutti i database tenant. Il carico viene generato usando uno degli scenari di caricamento disponibili:

| Demo | Scenario |
|:--|:--|
| 2 | Generare un carico di intensità normale (circa 40 DTU) |
| 3 | Generare un carico con picchi più lunghi e più frequenti per ogni database|
| 4 | Generare un carico con picchi DTU maggiori per ogni database (circa 80 DTU)|
| 5 | Generare un carico normale e un carico elevato in un singolo tenant (circa 95 DTU)|
| 6 | Generare un carico non bilanciato su più pool|

Il generatore di carico applica un carico di solo CPU *sintetico* a ogni database tenant. Il generatore avvia un processo per ogni database tenant, che chiama periodicamente una stored procedure che genera il carico. I livelli di carico (espressi in eDTU), la durata e gli intervalli sono diversi per i vari database, in modo da simulare un'attività imprevedibile dei tenant.

1. In **PowerShell ISE** aprire …\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenere lo script aperto durante l'esecuzione dei vari scenari presentati in questa esercitazione.
1. Impostare **$DemoScenario** = **2**, *Generare un carico di intensità normale*.
1. Premere **F5** per applicare un carico a tutti i database tenant.

L'app di database per tenant SaaS Wingtip Tickets è un'app SaaS e il carico di lavoro reale sulle app di questo tipo è in genere sporadico e imprevedibile. Per simulare questa situazione, il generatore produce un carico casuale distribuito tra tutti i tenant. Servono alcuni minuti perché emerga il modello di carico, quindi eseguire il generatore di carico per 3-5 minuti prima di provare a monitorare il carico come descritto nelle sezioni seguenti.

> [!IMPORTANT]
> Il generatore di carico esegue una serie di processi nella sessione di PowerShell locale. Mantenere aperta la scheda *Demo-PerformanceMonitoringAndManagement.ps1*. Se si chiude la scheda o si sospende il computer, il generatore di carico si arresta. Il generatore di carico rimane in uno stato di *chiamata del processo* in cui genera il carico in tutti i nuovi tenant di cui viene effettuato il provisioning dopo l'avvio del generatore. Usare *CTRL+C* per arrestare la chiamata di nuovi processi e chiudere lo script. L'esecuzione del generatore di carico continuerà, ma solo nei tenant esistenti.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorare l'utilizzo delle risorse usando il portale di Azure

Per monitorare l'utilizzo delle risorse risultante dal carico applicato, aprire il portale per il pool che contiene i database tenant:

1. Aprire il [portale di Azure](https://portal.azure.com) e passare al server *tenants1-dpt-&lt;UTENTE&gt;*.
1. Scorrere verso il basso e individuare i pool elastici, quindi fare clic su **Pool1**. Questo pool contiene tutti i database tenant creati finora.

Osservare i grafici **Monitoraggio pool elastico** e **Monitoraggio database elastico**.

L'utilizzo delle risorse del pool è l'utilizzo aggregato di tutti i database nel pool. Il grafico relativo ai database mostra i cinque database principali:

![grafico dei database](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Dato che il pool include altri database oltre ai primi cinque, l'utilizzo del pool mostra un livello di attività non rappresentato nel grafico per i primi cinque database. Per altri dettagli, fare clic su **Utilizzo risorse database**:

![Utilizzo risorse database](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Impostare avvisi sulle prestazioni per il pool

Impostare un avviso per il pool da attivare quando l'utilizzo è \>75%, come indicato di seguito:

1. Aprire *Pool1*, nel server *tenants1-dpt-\<utente\>*, nel [portale di Azure](https://portal.azure.com).
1. Fare clic su **Regole di avviso** e quindi fare clic su **+ Aggiungi avviso**:

   ![aggiungere un avviso](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Specificare un nome, ad esempio **DTU elevate**.
1. Impostare i valori seguenti:
   * **Metrica = Percentuale eDTU**
   * **Condizione = maggiore di**
   * **Soglia = 75**
   * **Periodo = Negli ultimi 30 minuti**
1. Aggiungere un indirizzo e-mail alla casella *Indirizzi di posta elettronica aggiuntivi dell'amministratore* e fare clic su **OK**.

   ![impostare l'avviso](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Potenziare un pool con carico eccessivo

Se il livello di carico aggregato per un pool aumenta fino a superarne la capacità massima e raggiunge il 100% di utilizzo delle unità eDTU, ciò ha effetti sulle prestazioni dei database con potenziale rallentamento dei tempi di risposta alle query per tutti i database nel pool.

**Come soluzione a breve termine**, valutare la possibilità di aumentare le prestazioni del pool per offrire risorse aggiuntive oppure di rimuovere i database dal pool, spostandoli in altri pool o fuori dal pool in un livello di servizio autonomo.

**Nel lungo termine**, prendere in considerazione l'ottimizzazione delle query o dell'utilizzo degli indici per migliorare le prestazioni dei database. A seconda di quanto un'applicazione è sensibile agli effetti dei problemi di prestazioni, è buona norma potenziare un pool prima di raggiungere il 100% di utilizzo delle eDTU. Usare un avviso per ricevere segnalazioni con il dovuto anticipo.

È possibile simulare le condizioni di carico eccessivo per un pool aumentando il carico prodotto dal generatore di carico. Creando picchi più frequenti e di lunga durata per i database si aumenta il carico aggregato del pool senza modificare i requisiti dei singoli database. È facile aumentare le prestazioni del pool nel portale o da PowerShell. In questo esercizio viene usato il portale.

1. Impostare *$DemoScenario* = **3**, _Generare un carico con picchi più lunghi e più frequenti per ogni database_ per aumentare l'intensità del carico aggregato del pool senza modificare il carico di picco necessario per ogni database.
1. Premere **F5** per applicare un carico a tutti i database tenant.

1. Passare a **Pool1** nel portale di Azure.

Monitorare il maggiore utilizzo di eDTU del pool nel grafico superiore. Sono necessari alcuni minuti prima di vedere gli effetti del nuovo carico di lavoro maggiore, ma presto il pool dovrebbe raggiungere il massimo utilizzo e, dato che il nuovo modello di carico è costante, si verificherà presto un sovraccarico del pool.

1. Per aumentare le prestazioni del pool, fare clic su **Configura pool** nella parte superiore della pagina **Pool1**.
1. Impostare **eDTU pool** su **100**. La modifica del numero di eDTU del pool non cambia le impostazioni per i singoli database, che è ancora pari a un massimo di 50 eDTU per database. Le impostazioni per i singoli database sono visibili sul lato destro della pagina **Configura pool**.
1. Fare clic su **Salva** per inviare la richiesta di ridimensionamento del pool.

Tornare a **Pool1** > **Panoramica** per visualizzare i grafici di monitoraggio. Monitorare l'effetto dell'assegnazione di altre risorse al pool, anche se, con pochi database e un carico casuale, non sempre è facile notare conseguenze lampanti finché non si prolunga l'esecuzione per un periodo minimo. Mentre si analizzano i grafici tenere che presente che il valore 100% nel grafico superiore rappresenta ora 100 eDTU, mentre 100% nel grafico inferiore corrisponde ancora a 50 eDTU perché il numero massimo di eDTU per database è ancora 50.

I database rimango online e pienamente disponibili durante l'intero processo. All'ultimo momento, quando ogni database è pronto per essere abilitato con il nuovo numero di eDTU del pool, le eventuali connessioni attive vengono interrotte. Il codice delle applicazioni dovrebbe sempre essere scritto in modo da prevedere tentativi di riattivazione delle connessioni interrotte, quindi l'applicazione si riconnetterà al database nel pool potenziato.

## <a name="load-balance-between-pools"></a>Bilanciare il carico tra i pool

In alternativa al potenziamento del pool, è possibile creare un secondo pool e spostare database in questo pool per bilanciare il carico tra i due. A questo scopo, il nuovo pool deve essere creato nello stesso server del primo.

1. Nel [portale di Azure](https://portal.azure.com) aprire il server **tenants1-dpt-&lt;UTENTE&gt;**.
1. Fare clic su **+ Nuovo pool** per creare un pool nel server corrente.
1. Nel modello **Pool di database elastici**:

    1. Impostare **Nome** su *Pool2*.
    1. Lasciare il piano tariffario **Pool Standard**.
    1. Fare clic su **Configura pool**.
    1. Impostare **eDTU pool** su *50 eDTU*.
    1. Fare clic su **Aggiungi database** per visualizzare un elenco di database nel server che possono essere aggiunti a *Pool2*.
    1. Selezionare 10 database per spostarli nel nuovo pool e quindi fare clic su **Seleziona**. Se si è eseguito il generatore di carico, al servizio è già noto che il profilo delle prestazioni richiede un pool di dimensioni superiori alle 50 eDTU predefinite e viene suggerito di iniziare con un'impostazione di 100 eDTU.

    ![Suggerimento](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Per questa esercitazione, lasciare l'impostazione predefinita di 50 eDTU e fare di nuovo clic su **Seleziona**.
    1. Fare clic su **OK** per creare il nuovo pool e spostarvi i database selezionati.

La creazione del pool e lo spostamento dei database richiedono alcuni minuti. Quando i database vengono spostati, rimangono online e completamente accessibili fino all'ultimo momento, quando viene chiusa qualsiasi connessione aperta. Purché esista una logica per i tentativi, i client si connetteranno quindi al database nel nuovo pool.

Passare a **Pool2**, nel server *tenants1-dpt-\<utente\>*, per aprire il pool e monitorarne le prestazioni. Se non viene visualizzato, attendere il completamento del provisioning del nuovo pool.

Si nota ora una riduzione dell'utilizzo delle risorse di *Pool1* e un carico simile per *Pool2*.

## <a name="manage-performance-of-a-single-database"></a>Gestire le prestazioni di un database singolo

Se un singolo database in un pool è sottoposto a un carico elevato prolungato, a seconda della configurazione del pool, potrebbe tendenzialmente usare in modo predominante molte delle risorse nel pool con effetti sugli altri database. Se è probabile che questo tipo di attività continui per un certo tempo, è possibile spostare temporaneamente il database fuori dal pool. In questo modo il database può avere a disposizione le risorse aggiuntive necessarie ed essere isolato dagli altri database.

Questo esercizio simula l'effetto di un carico elevato per Contoso Concert Hall in concomitanza con l'inizio della vendita dei biglietti per un concerto di grande richiamo.

1. Aprire lo script...\\*Demo-PerformanceMonitoringAndManagement.ps1* in **PowerShell ISE**.
1. Impostare **$DemoScenario = 5, Generare un carico normale e un carico elevato in un singolo tenant (circa 95 DTU).**
1. Impostare **$SingleTenantDatabaseName = contosoconcerthall**
1. Eseguire lo script con **F5**.


1. Nel [portale di Azure](https://portal.azure.com) passare all'elenco di database nel server *tenants1-dpt-\<user\>*. 
1. Fare clic sul database **contosoconcerthall**.
1. Fare clic sul pool in cui si trova **contosoconcerthall**. Individuare il pool nella sezione **Pool di database elastici**.

1. Esaminare il grafico **Monitoraggio pool elastico** e cercare il maggiore utilizzo di eDTU del pool. Dopo un paio di minuti, il carico elevato dovrebbe iniziare a comparire e il pool dovrebbe raggiungere velocemente l'utilizzo al 100%.
2. Esaminare i dati in **Monitoraggio database elastico** che mostrano i database più attivi nell'ultima ora. Il database *contosoconcerthall* dovrebbe essere presto visualizzato come uno dei cinque database più attivi.
3. **Fare clic sul** **grafico** Monitoraggio database elastico. Viene aperta la pagina **Utilizzo risorse database** in cui è possibile monitorare i singoli database. Ciò consente di isolare i dati per il database *contosoconcerthall*.
4. Nell'elenco di database fare clic su **contosoconcerthall**.
5. Fare clic su **Piano tariffario (piano DTU)** per aprire la pagina **Configura prestazioni** in cui è possibile impostare un livello di prestazioni autonomo per il database.
6. Fare clic sulla scheda **Standard** per aprire le opzioni di scalabilità nel livello Standard.
7. Scorrere il **dispositivo di scorrimento DTU** verso destra per selezionare **100 DTU**. Si noti che questa impostazione corrisponde all'obiettivo di servizio, **S3**.
8. Fare clic su **Applica** per spostare il database fuori dal pool e impostarlo come database *S3 Standard*.
9. Dopo aver completato il ridimensionamento, monitorare l'effetto sul database contosoconcerthall e su Pool1 nei pannelli del pool elastico e del database.

Quando il carico elevato per il database contosoconcerthall diminuisce, è necessario reinserirlo tempestivamente nel pool per ridurre i costi. Se non è chiaro quando ciò accade, è possibile impostare un avviso nel database che verrà attivato quando l'utilizzo di DTU scende sotto il massimo consentito per ogni database nel pool. Lo spostamento di un database in un pool è descritto nell'esercizio 5.

## <a name="other-performance-management-patterns"></a>Altri modelli di gestione delle prestazioni

**Scalabilità preventiva** Nell'esercizio precedente è stato illustrato come ridimensionare un database isolato quando si conosce il database su cui intervenire. Se il responsabile della gestione di Contoso Concert Hall avesse informato Wingtips dell'imminente vendita di biglietti, sarebbe stato possibile rimuovere preventivamente il database dal pool. In caso contrario, sarebbe stato probabilmente necessario un avviso per il pool o il database per ricevere una segnalazione della situazione. È meglio evitare di venire a conoscenza di un problema di questo tipo quando gli altri tenant nel pool si lamentano del calo di prestazioni. Se il tenant può prevedere il tempo per cui saranno necessarie risorse aggiuntive, inoltre, è possibile configurare un runbook di Automazione di Azure per spostare il database fuori dal pool e reinserirlo in base a una pianificazione definita.

**Scalabilità self-service dei tenant** Dato che la scalabilità richiede attività facilmente richiamabili tramite l'API di gestione, è possibile integrare in modo semplice funzioni di scalabilità dei database tenant nell'applicazione che interagisce con i tenant e offrirle come funzionalità del servizio SaaS. Ad esempio, fare in modo che i tenant possano amministrare in autonomia l'aumento o la riduzione delle prestazioni, eventualmente collegando anche queste operazioni in modo diretto alla fatturazione.

**Aumento e riduzione delle prestazioni per un pool con una pianificazione in base ai modelli di utilizzo**

Se l'utilizzo aggregato dei tenant segue modelli prevedibili, è possibile usare Automazione di Azure per aumentare e ridurre le prestazioni di un pool con una pianificazione. Ad esempio, ridurre le prestazioni dopo le 18 e aumentarle di nuovo prima delle 6 nei giorni della settimana in cui è noto che si verifica un calo delle risorse richieste.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Simulare l'utilizzo nei database tenant tramite l'esecuzione di un generatore di carico specificato
> * Monitorare la risposta dei database tenant a un aumento del carico di lavoro
> * Potenziare il pool elastico in risposta al carico maggiore per i database
> * Eseguire il provisioning di un secondo pool elastico per bilanciare il carico delle attività dei database

[Esercitazione sul ripristino di un tenant singolo](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sulla distribuzione dell'applicazione del database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Pool elastici SQL](sql-database-elastic-pool.md)
* [Automazione di Azure](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) - Esercitazione sulla configurazione e l'uso di Log Analytics
