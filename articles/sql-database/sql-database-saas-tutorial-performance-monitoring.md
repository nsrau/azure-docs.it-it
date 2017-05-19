---
title: Monitoraggio delle prestazioni dell&quot;applicazione WTP (applicazione SaaS di esempio con database SQL di Azure) | Microsoft Docs
description: Monitorare le prestazioni di un&quot;applicazione SaaS di esempio che usa il database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 1698a06dc326b1d215bb635c40724f914ba82442
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="monitor-performance-of-the-wtp-sample-saas-application"></a>Monitorare le prestazioni dell'applicazione SaaS di esempio WTP

Questa esercitazione illustra le funzionalità predefinire per il monitoraggio e gli avvisi di database SQL e dei pool elastici. Vengono poi esplorati alcuni dei principali scenari di gestione delle prestazioni usati nelle applicazioni SaaS.

L'app Wingtip Tickets usa un modello di dati a tenant singolo, in cui ogni sede di eventi (tenant) ha un database proprio. Come molte applicazioni SaaS, il modello di carico di lavoro tenant previsto è imprevedibile e sporadico. In altre parole, le vendite di biglietti possono verificarsi in qualsiasi momento. Per sfruttare i vantaggi di questo modello di utilizzo tipico dei database, i database tenant sono distribuiti in pool di database elastici. I pool elastici consentono di ottimizzare il costo di una soluzione condividendo le risorse tra molti database. Con questo tipo di modello, è importante monitorare l'utilizzo dei database e delle risorse del pool per assicurarsi che i carichi siano bilanciati in modo ragionevole tra i pool. È anche necessario assicurarsi che i singoli database dispongano delle risorse appropriate e che i pool non si avvicinino ai limiti di [eDTU](sql-database-what-is-a-dtu.md). Questa esercitazione illustra alcuni modi per monitorare e gestire i database e i pool, nonché come adottare misure correttive in risposta a variazioni del carico di lavoro.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Simulare l'utilizzo nei database tenant tramite l'esecuzione di un generatore di carico specificato
> * Monitorare la risposta dei database tenant a un aumento del carico di lavoro
> * Potenziare il pool elastico in risposta al carico maggiore per i database
> * Eseguire il provisioning di un secondo pool elastico per bilanciare il carico delle attività dei database


Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app WTP è stata distribuita. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare un'applicazione SaaS multi-tenant di esempio che usa il database di SQL Azure](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduzione ai modelli di gestione delle prestazioni di SaaS

La gestione delle prestazioni dei database comprende la compilazione e l'analisi dei dati sulle prestazioni, quindi l'adozione delle misure appropriate adattando i parametri in modo da mantenere tempi di risposta accettabili per l'applicazione. Quando si ospitano più tenant, i pool di database elastici rappresentano una soluzione conveniente per fornire e gestire le risorse per un gruppo di database con carichi di lavoro imprevedibili. Con determinati modelli di carico di lavoro, anche solo due database S3 possono trarre vantaggio dalla gestione in un pool. Un pool non solo consente di condividere il costo delle risorse, ma anche di eliminare la necessità di monitorare e controllare costantemente singoli database.

![diagramma](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

I pool e i database nei pool devono comunque continuare a essere monitorati per accertarsi che offrano prestazioni entro intervalli accettabili. Ottimizzare la configurazione del pool per soddisfare le esigenze del carico di lavoro aggregato, assicurandosi che le unità eDTU del pool siano appropriate per il carico di lavoro complessivo. Impostare il minimo e il massimo per le unità eDTU per ogni database su valori appropriati per i requisiti specifici di ogni applicazione.

### <a name="performance-management-strategies"></a>Strategie per la gestione delle prestazioni

* Per evitare di dover monitorare manualmente le prestazioni, è consigliabile **impostare la generazione di avvisi quando i database o i pool escono dai normali intervalli**.
* Per rispondere alle fluttuazioni a breve termine del livello di prestazioni aggregate di un pool, è possibile **scegliere un livello superiore o inferiore di eDTU per il pool**. Se la fluttuazione si verifica a intervalli regolari o prevedibili, **è possibile pianificare il ridimensionamento automatico del pool**. Ad esempio, ridurre il numero di eDTU quando il carico di lavoro è notoriamente leggero, ad esempio durante la notte o nei fine settimana.
* Per rispondere a fluttuazioni a lungo termine o a modifiche del numero di database, **è possibile spostare singoli database in altri pool**.
* Per rispondere ad aumenti a breve termine del carico per *singoli* database, **è possibile rimuovere singoli database da un pool e assegnare loro un livello di prestazioni singolo** per un certo periodo. Quando il carico si riduce di nuovo, il database può essere reinserito nel pool. Se questo tipo di comportamento è noto in anticipo, i database possono essere spostati preventivamente in modo da assicurarsi che abbiano sempre le risorse necessarie ed evitare effetti sugli altri database nel pool. Se si tratta di un requisito prevedibile, come nel caso di un picco di vendite di biglietti per un evento di grande richiamo, questo comportamento di gestione può essere integrato nell'applicazione.

Il [portale di Azure](https://portal.azure.com) include funzionalità di monitoraggio e avviso predefinite per la maggior parte delle risorse. Per il database SQL, il monitoraggio e gli avvisi sono disponibili per database e pool. Le funzionalità di monitoraggio e avviso predefinite sono specifiche delle risorse, quindi è comodo usarle per un numero limitato di risorse, mentre non sono altrettanto utili quando si usano molte risorse.

Per scenari con volumi elevati, è possibile usare Log Analytics (noto anche come OMS). Si tratta di un servizio di Azure separato che fornisce funzionalità di analisi per log di diagnostica e dati di telemetria raccolti in un'area di lavoro di Log Analytics, che consente di raccogliere dati di telemetria da molti servizi e supporta l'esecuzione di query e l'impostazione di avvisi.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="provision-additional-tenants"></a>Eseguire il provisioning di altri tenant

Anche se i pool possono essere una soluzione conveniente anche con solo due database S3, con l'aumentare del numero di database inseriti nel pool migliora anche il risparmio consentito dalla ripartizione dei costi. Per comprendere meglio il funzionamento della gestione e del monitoraggio delle prestazioni su larga scala, per questa esercitazione è necessario distribuire almeno 20 database.

Se è già stato effettuato il provisioning di un batch di tenant in un'esercitazione precedente, è possibile saltare la sezione [Simulare l'utilizzo in tutti i database tenant](#simulate-usage-on-all-tenant-databases).

1. Aprire …\\Learning Modules\\Provision and Catalog\\*Demo-PerformanceMonitoringAndManagement.ps1* in **PowerShell ISE**. Mantenere lo script aperto durante l'esecuzione dei vari scenari presentati in questa esercitazione.
1. Impostare **$DemoScenario** = **1**, **Effettuare il provisioning di un batch di tenant**
1. Premere **F5** per eseguire lo script.

Lo script distribuirà 17 tenant in meno di cinque minuti.

Lo script *New-TenantBatch* usa un set annidato o collegato di modelli di [Resource Manager](../azure-resource-manager/index.md) che creano un batch di tenant, che copia per impostazione predefinita il database **baseTenantDb** nel server di catalogo per creare i nuovi database tenant, quindi li registra nel catalogo e infine li inizializza con il nome del tenant e il tipo di sede. Questo comportamento è coerente con il modo in cui l'app WTP esegue il provisioning di un nuovo tenant. Tutte le modifiche apportate a *baseTenantDB* vengono applicate agli eventuali nuovi tenant di cui viene eseguito il provisioning in seguito. Vedere l'[esercitazione sulla gestione dello schema](sql-database-saas-tutorial-schema-management.md) per informazioni su come apportare modifiche allo schema di database tenant *esistenti*, incluso il database *di riferimento*.

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Simulare modelli di utilizzo diversi generando tipi di carico diversi

Lo script *Demo-PerformanceMonitoringAndManagement.ps1* avvia il generatore di carico usando uno dei *tipi di carico* disponibili:

| Demo | Scenario |
|:--|:--|
| 2 | Generare un carico di intensità normale (circa 40 DTU) |
| 3 | Generare un carico con picchi più lunghi e più frequenti per ogni database|
| 4 | Generare un carico con picchi DTU maggiori per ogni database (circa 80 DTU)|
| 5 | Generare un carico normale e un carico elevato in un singolo tenant (circa 95 DTU)|
| 6 | Generare un carico non bilanciato su più pool|

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulare l'utilizzo in tutti i database tenant

Il generatore di carico applica un carico di solo CPU *sintetico* a ogni database tenant. Il generatore avvia un processo per ogni database tenant, che chiama periodicamente una stored procedure che genera il carico. I livelli di carico (espressi in eDTU), la durata e gli intervalli sono diversi per i vari database, in modo da simulare un'attività imprevedibile dei tenant.

1. Impostare **$DemoScenario** = **2**, *Generare una carico di intensità normale*.
1. Premere **F5** per applicare un carico a tutti i database tenant.

Considerata la natura sporadica del carico, lasciare in esecuzione il generatore per 10-20 minuti, in modo che l'attività possa stabilizzarsi e generare un modello adeguato.

> [!IMPORTANT]
> Il generatore di carico esegue una serie di processi nella sessione di PowerShell locale. Mantenere aperta la scheda *Demo-PerformanceMonitoringAndManagement.ps1*. Se si chiude la scheda o si sospende il computer, il generatore di carico si arresta.

## <a name="monitor-resource-usage-using-the-portal"></a>Monitorare l'utilizzo delle risorse tramite il portale

Per monitorare l'utilizzo delle risorse risultante dal carico applicato, aprire il portale per il pool che contiene i database tenant.

1. Aprire il [portale di Azure](https://portal.azure.com) e passare al server tenants1-&lt;UTENTE&gt;.
1. Dovrebbe essere visualizzato l'elenco dei database tenant compreso il nuovo batch di database.
1. Scorrere verso il basso e individuare i pool elastici, quindi fare clic su **Pool1**. Questo pool contiene tutti i database tenant creati finora.
1. Espandere il pannello del pool visualizzato e osservare il grafico di utilizzo del pool e il grafico di utilizzo dei principali database.

L'utilizzo del pool è fondamentalmente l'utilizzo aggregato di tutti i database nel pool. Il grafico di utilizzo dei database mostra i 5 database più usati:

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Dato che il pool include altri database oltre ai primi 5, l'utilizzo del pool mostra un livello di attività non rappresentato nel grafico per i primi 5 database. Per visualizzare altri dettagli, fare clic su **Utilizzo risorse database**:

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Impostare avvisi sulle prestazioni per il pool

Impostare un avviso per il pool da attivare quando l'utilizzo è \>75% per 5 minuti, come indicato di seguito:

1. Aprire *Pool1* (nel server *tenants1-\<utente\>*) nel [portale di Azure](https://portal.azure.com).
1. Fare clic su **Regole di avviso** e quindi fare clic su **+ Aggiungi avviso**:

   ![aggiungere un avviso](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Specificare un nome, ad esempio **DTU elevate**. 
1. Impostare i valori seguenti:
   * **Metrica = Percentuale eDTU**
   * **Condizione = maggiore di** .
   * **Soglia = 75**.
   * **Periodo = Negli ultimi 30 minuti**.

   ![impostare l'avviso](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

È possibile configurare l'invio delle notifiche al proprio indirizzo di posta elettronica per l'account Azure e facoltativamente aggiungere altri indirizzi di posta elettronica. Si consiglia di non configurare questa opzione, se non si è il proprietario della sottoscrizione usata per l'esercitazione.

> [!NOTE]
> Dato che l'avviso viene generato solo se il valore di soglia viene superato per gli ultimi 30 minuti, il generatore di carico deve essere eseguito per più di 30 minuti per testare l'avviso.


## <a name="scale-up-a-busy-pool"></a>Potenziare un pool con carico eccessivo

Se il livello di carico aggregato per un pool aumenta fino a superarne la capacità massima e raggiunge il 100% di utilizzo delle unità eDTU, ciò ha effetti sulle prestazioni dei database con potenziale rallentamento dei tempi di risposta alle query per tutti i database nel pool.

Come soluzione a breve termine, valutare la possibilità di aumentare le prestazioni del pool per fornire risorse aggiuntive oppure di rimuovere database dal pool, spostandoli in altri pool o fuori dal pool in un livello di servizio autonomo.

Nel lungo termine, prendere in considerazione l'ottimizzazione delle query o dell'utilizzo degli indici per migliorare le prestazioni dei database. A seconda di quanto un'applicazione è sensibile agli effetti dei problemi di prestazioni, è buona norma potenziare un pool prima di raggiungere il 100% di utilizzo delle eDTU. Usare un avviso per ricevere segnalazioni con il dovuto anticipo.

È possibile simulare le condizioni di carico eccessivo per un pool aumentando il carico prodotto dal generatore di carico. Creando picchi più frequenti e di lunga durata per i database si aumenta il carico aggregato del pool senza modificare i requisiti dei singoli database. È facile aumentare le prestazioni del pool nel portale o da PowerShell. In questo esercizio viene usato il portale.

1. Impostare *$DemoScenario* = **3**, _Generare un carico con picchi più lunghi e più frequenti per ogni database_ per aumentare l'intensità del carico aggregato del pool senza modificare il carico di picco necessario per ogni database.
1. Premere **F5** per applicare un carico a tutti i database tenant.


1. **Aprire il pannello del pool** **per tenants1/Pool1**.


1. Monitorare il maggiore utilizzo di DTU del pool nel grafico superiore. Sono necessari alcuni minuti prima di vedere gli effetti del nuovo carico di lavoro maggiore, ma presto il pool dovrebbe raggiungere l'utilizzo al 100% e, dato che il nuovo modello di carico è costante, si verificherà preso un sovraccarico del pool.


1. Per potenziare il pool, fare clic su **Configura pool**
1. Impostare il dispositivo di scorrimento **eDTU pool** su 100 (è consigliabile non superare questo valore per limitare i costi). Si noti che lo spazio di archiviazione aggregato disponibile per tutti i database nel pool, indicato da **GB pool** è collegato all'impostazione delle eDTU e aumenta corrispondentemente. La modifica del numero di eDTU del pool non cambia le impostazioni per i singoli database, che è ancora pari a un massimo di 50 eDTU per database. Le impostazioni per i singoli database sono visibili sul lato destro del pannello **Configura pool**.
1. Fare clic su **Salva** per inviare la richiesta. La modifica richiede solitamente da 3 a 5 minuti per un pool Standard.

Tornare a **Pool1** > **Panoramica** per visualizzare i grafici di monitoraggio. Monitorare l'effetto dell'assegnazione di ulteriori risorse al pool, anche se con pochi database e un carico casuale non sempre è facile notare conseguenze lampanti. Mentre si analizzano i grafici tenere che presente che il valore 100% nel grafico superiore rappresenta ora 100 eDTU, mentre 100% nel grafico inferiore corrisponde ancora a 50 eDTU perché il numero massimo di eDTU per database è ancora 50.

I database rimango online e pienamente disponibili durante l'intero processo. All'ultimo momento, quando ogni database è pronto per essere abilitato con il nuovo numero di eDTU del pool, le eventuali connessioni attive vengono interrotte. Il codice delle applicazioni dovrebbe sempre essere scritto in modo da prevedere tentativi di riattivazione delle connessioni interrotte, quindi l'applicazione si riconnetterà al database nel pool potenziato.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Creare un secondo pool e bilanciare il carico dei database per gestire un carico aggregato maggiore

In alternativa al potenziamento del pool, è possibile creare un secondo pool e spostare database in questo pool per bilanciare il carico tra i due. A questo scopo, il nuovo pool deve essere creato nello stesso server del primo.

1. Aprire il **pannello del server per il server customers1-&lt;UTENTE&gt;**. Se è aperto il pannello di un database o un pool, è possibile aprire il controllo Informazioni di base e selezionare il nome del server come collegamento.
1. Fare clic su **+ Nuovo pool** per creare un pool nel server corrente
1. Nel modello di nuovo pool di database elastico:

    1. Impostare **Nome = Pool2**.
    1. Lasciare il piano tariffario **Pool Standard**.
    1. Fare clic su **Configura pool**.
    1. Nel pannello Configura pool visualizzato impostare **eDTU pool = 50 DTU**.
    1. Fare clic sul comando **Aggiungi database** per visualizzare un elenco di database nel server non inclusi nel pool corrente.
    1. Nell'elenco **selezionare** metà dei database (10 su 20) per spostarli nel nuovo pool e quindi fare clic su **Seleziona**.
    1. Fare di nuovo clic su **Seleziona** per accettare le modifiche alla configurazione. Si noti la stima dei costi per un mese di utilizzo con le opzioni selezionate.
    1. Selezionare **OK** per creare il nuovo pool con la nuova configurazione e per spostare i database.

La creazione del pool e lo spostamento dei database al suo interno richiede alcuni minuti. Ogni database spostato rimane online e completamente accessibile fino all'ultimo momento, quando viene chiusa qualsiasi connessione aperta. Quando un client ritenta la connessione, si connetterà al database nel nuovo pool.

Il pool creato verrà visualizzato nel pannello del server customers1. Fare clic sul nome del pool per aprire il pannello del pool e monitorarne le prestazioni.

Si dovrebbe notare una riduzione dell'utilizzo delle risorse di Pool1 è un carico simile per Pool2.

## <a name="manage-an-increased-load-on-a-single-database"></a>Gestire un aumento del carico su un singolo database

Se un singolo database in un pool è sottoposto a un carico elevato prolungato, a seconda della configurazione del pool, potrebbe tendenzialmente usare in modo predominante molte delle risorse nel pool con effetti sugli altri database. Se è probabile che questo tipo di attività continui per un certo tempo, è possibile spostare il database fuori dal pool temporaneamente. Ciò consente sia di assegnare più risorse al database rispetto agli altri database nel pool, sia di isolarlo dagli altri database. Questo esercizio simula l'effetto di un carico elevato per Contoso Concert Hall in concomitanza con l'inizio della vendita dei biglietti per un concerto di grande richiamo.

1. Nello script …\\**Demo-PerformanceManagementAndMonitoring**.ps1
1. Impostare **$DemoScenario = 5, Generare un carico normale e un carico elevato in un singolo tenant (circa 95 DTU).**
1. Impostare **$SingleTenantDatabaseName = contosoconcerthall**
1. Eseguire lo script con **F5**.
1. **Aprire il pannello del pool** **per Customers1/Pool1**.
1. Esaminare i dati visualizzati in **Monitoraggio pool elastico** nella parte superiore del pannello e cercare il maggiore utilizzo di DTU del pool. Dopo un paio di minuti, il carico elevato dovrebbe iniziare a comparire e il pool dovrebbe raggiungere velocemente l'utilizzo al 100%.
1. Monitorare anche i dati in **Monitoraggio database elastico** che mostrano i database più attivi nell'ultima ora. Il database contosoconcerthall dovrebbe essere presto visualizzato come uno dei 5 database più attivi.
1. **Fare clic sul grafico di Monitoraggio database**  **elastico**. Verrà aperto un pannello **Utilizzo risorse database** in cui è possibile monitorare in modo selettivo i singoli database. Ciò consente di isolare i dati per il database contosoconcerthall.
1. Nell'elenco dei database **fare clic su contosoconcerthall**. Verrà aperto il pannello corrispondente.
1. Scegliere **Piano tariffario (piano DTU)** nel menu di scelta rapida per aprire il pannello **Configura prestazioni** in cui è possibile impostare un livello di prestazioni isolato per il database.
1. Fare clic sulla scheda **Standard** per aprire le opzioni di scalabilità nel livello Standard.
1. Scorrere il **dispositivo di scorrimento DTU** verso destra per selezionare 100 DTU. Si noti che questo valore corrisponde all'obiettivo di servizio, **S3**, indicato tra parentesi tra gli indicatori di dimensioni per DTU e spazio di archiviazione.
1. Fare clic su **Applica** per spostare il database fuori dal pool e impostarlo come database S3 Standard.
1. Dopo aver completato la distribuzione, monitorare l'effetto sul database contosoconcerthall e sul pool da cui è stato rimosso nei pannelli del pool elastico e del database.

Quando il carico maggiore del normale per il database contosoconcerthall diminuisce, è necessario reinserirlo tempestivamente nel pool per ridurre i costi. Se non è chiaro quando ciò accade è possibile impostare un avviso nel database che verrà generato quando l'utilizzo di DTU scende sotto il massimo consentito per ogni database nel pool. Lo spostamento di un database in un pool è descritto nell'esercizio 5.

## <a name="other-performance-management-patterns"></a>Altri modelli di gestione delle prestazioni

**Scalabilità preventiva** Nell'esercizio 6 è stato illustrato come ridimensionare un database isolato quando si conosce il database su cui intervenire. Se il responsabile della gestione di Contoso Concert Hall avesse informato WTP dell'imminente vendita di biglietti, il database avrebbe potuto essere rimosso dal pool preventivamente. In caso contrario, sarebbe stato probabilmente necessario un avviso per il pool o il database per ricevere una segnalazione della situazione. È meglio evitare di venire a conoscenza di un problema di questo tipo quando gli altri tenant nel pool si lamentano del calo di prestazioni. Se il tenant può prevedere il tempo per cui saranno necessarie risorse aggiuntive, inoltre, è possibile configurare un runbook di Automazione di Azure per spostare il database fuori dal pool e reinserirlo in base a una pianificazione definita.

**Scalabilità self-service dei tenant** Dato che la scalabilità richiede attività facilmente richiamabili tramite l'API di gestione, è possibile integrare in modo semplice funzioni di scalabilità dei database tenant nell'applicazione che interagisce con i tenant e offrirle come funzionalità del servizio SaaS. Ad esempio, fare in modo che i tenant possano amministrare in autonomia l'aumento o la riduzione delle prestazioni, eventualmente collegando anche queste operazioni in modo diretto alla fatturazione.

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Aumento e riduzione delle prestazioni per un pool con una pianificazione in base ai modelli di utilizzo

Se l'utilizzo aggregato dei tenant segue modelli prevedibili, è possibile usare Automazione di Azure per aumentare e ridurre le prestazioni di un pool con una pianificazione. Ad esempio, ridurre le prestazioni dopo le 18 e aumentarle di nuovo prima delle 6 nei giorni della settimana in cui è noto che si verifica un calo delle risorse richieste.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Simulare l'utilizzo nei database tenant tramite l'esecuzione di un generatore di carico specificato
> * Monitorare la risposta dei database tenant a un aumento del carico di lavoro
> * Potenziare il pool elastico in risposta al carico maggiore per i database
> * Eseguire il provisioning di un secondo pool elastico per bilanciare il carico delle attività dei database

[Esercitazione sul ripristino di un tenant singolo](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Pool elastici SQL](sql-database-elastic-pool.md)
* [Automazione di Azure](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) - Esercitazione sulla configurazione e l'uso di Log Analytics
