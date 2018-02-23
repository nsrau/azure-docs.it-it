---
title: Monitorare le prestazioni di un database SQL di Azure multi-tenant partizionato in un'app SaaS multi-tenant | Microsoft Docs
description: Monitorare e gestire le prestazioni di un database SQL di Azure multi-tenant partizionato in un'app SaaS multi-tenant
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 3e97f0635a856256dd08c29d33d8058be9c8d8b4
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorare e gestire le prestazioni di un database SQL di Azure multi-tenant partizionato in un'app SaaS multi-tenant

In questa esercitazione vengono illustrati diversi scenari di gestione delle prestazioni chiave usati nelle applicazioni SaaS. Usando un generatore di carico per simulare l'attività in tutti i database multi-tenant partizionati, vengono illustrate le funzionalità di monitoraggio e avviso predefinite del database SQL.

L'app SaaS di database multi-tenant Wingtip Tickets usa un modello di dati multi-tenant partizionato, dove i dati delle sedi di eventi (tenant) possono essere distribuiti tra più database in base all'ID del tenant. Come molte applicazioni SaaS, il modello di carico di lavoro tenant previsto è imprevedibile e sporadico. In altre parole, le vendite di biglietti possono verificarsi in qualsiasi momento. Per sfruttare i vantaggi di questo modello di utilizzo tipico dei database, è possibile aumentare e ridurre le prestazioni dei database per ottimizzare il costo di una soluzione. Con questo tipo di modello, è importante monitorare l'utilizzo delle risorse dei database per assicurarsi che i carichi possano essere equamente bilanciati tra più database. È anche necessario assicurarsi che i singoli database dispongano delle risorse appropriate e che non si avvicinino ai limiti di [DTU](sql-database-what-is-a-dtu.md). Questa esercitazione illustra alcuni modi per monitorare e gestire i database e spiega come adottare misure correttive in risposta a variazioni del carico di lavoro.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Simulare l'utilizzo in un database multi-tenant partizionato tramite un generatore di carico specificato
> * Monitorare il database mentre risponde all'aumento del carico
> * Aumentare le prestazioni del database in risposta al carico maggiore
> * Eseguire il provisioning di un tenant in un database a tenant singolo

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* È stata distribuita l'app SaaS di database multi-tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database multi-tenant Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduzione ai modelli di gestione delle prestazioni SaaS

La gestione delle prestazioni dei database comprende la compilazione e l'analisi dei dati sulle prestazioni, quindi l'adozione delle misure appropriate adattando i parametri in modo da mantenere tempi di risposta accettabili per l'applicazione. 

### <a name="performance-management-strategies"></a>Strategie per la gestione delle prestazioni

* Per evitare di dover monitorare manualmente le prestazioni, è consigliabile **impostare l'attivazione di avvisi quando i database superano i normali intervalli**.
* Per rispondere alle fluttuazioni a breve termine del livello di prestazioni di un database, **è possibile scegliere un livello superiore o inferiore di DTU**. Se la fluttuazione si verifica a intervalli regolari o prevedibili, **è possibile pianificare il ridimensionamento automatico del database**. Ad esempio, ridurre il numero di eDTU quando il carico di lavoro è notoriamente leggero, ad esempio durante la notte o nei fine settimana.
* Per rispondere a fluttuazioni a più lungo termine o a variazioni del numero di tenant, **è possibile spostare singoli tenant in un altro database**.
* Per rispondere ad aumenti del carico a breve termine per *singoli* tenant, **è possibile rimuovere singoli tenant da un database e assegnare loro uno specifico livello di prestazioni**. Non appena il carico si riduce di nuovo, il tenant può essere reinserito nel database multi-tenant. Quando questo tipo di comportamento è noto in anticipo, i tenant possono essere spostati preventivamente per assicurarsi che abbiano sempre le risorse necessarie ed evitare effetti sugli altri tenant nel database multi-tenant. Se si tratta di un requisito prevedibile, come nel caso di un picco di vendite di biglietti per un evento di grande richiamo, questo comportamento di gestione può essere integrato nell'applicazione.

Il [portale di Azure](https://portal.azure.com) include funzionalità di monitoraggio e avviso predefinite per la maggior parte delle risorse. Per il database SQL, le funzionalità di monitoraggio e avviso sono disponibili sui database. Le funzionalità di monitoraggio e avviso predefinite sono specifiche delle risorse, quindi è comodo usarle per un numero limitato di risorse, mentre non sono utili quando si usano molte risorse.

Per gli scenari con volumi elevati in cui si lavora con molte risorse, si può usare [Log Analytics (OMS)](https://azure.microsoft.com/services/log-analytics/). Si tratta di un servizio di Azure separato che fornisce funzionalità di analisi per log di diagnostica e dati di telemetria raccolti in un'area di lavoro di Log Analytics. Log Analytics consente di raccogliere dati di telemetria da molti servizi e supporta l'esecuzione di query e l'impostazione di avvisi.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Ottenere gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="provision-additional-tenants"></a>Eseguire il provisioning di altri tenant

Per illustrare meglio il funzionamento della gestione e del monitoraggio delle prestazioni su larga scala, questa esercitazione ha come prerequisito la presenza di più tenant in un database multi-tenant partizionato.

Se si è già effettuato il provisioning di un batch di tenant in un'esercitazione precedente, passare alla sezione [Simulare l'utilizzo in tutti i database tenant](#simulate-usage-on-all-tenant-databases).

1. In **PowerShell ISE** aprire …\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenere lo script aperto durante l'esecuzione dei vari scenari presentati in questa esercitazione.
1. Impostare **$DemoScenario** = **1**, _Effettuare il provisioning di un batch di tenant_
1. Premere **F5** per eseguire lo script.

In pochi minuti lo script distribuisce 17 tenant nel database multi-tenant. 

Lo script *New-TenantBatch* crea nuovi tenant con chiavi univoche all'interno del database multi-tenant partizionato e li inizializza con il nome del tenant e il tipo di sede di eventi. Questo comportamento è coerente con il modo in cui l'app effettua il provisioning di un nuovo tenant. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulare l'utilizzo in tutti i database tenant

Per simulare l'esecuzione di un carico di lavoro sul database multi-tenant è disponibile lo script *Demo-PerformanceMonitoringAndManagement.ps1*. Il carico viene generato usando uno degli scenari di caricamento disponibili:

| Demo | Scenario |
|:--|:--|
| 2 | Generare un carico di normale intensità (circa 30 DTU) |
| 3 | Generare un carico con picchi di maggiore durata per ogni tenant|
| 4 | Generare un carico con picchi di DTU di maggiore intensità per ogni tenant (circa 70 DTU)|
| 5 | Generare un carico ad alta intensità (circa 90 DTU) su un tenant singolo e un carico di normale intensità su tutti gli altri tenant |

Il generatore di carico applica un carico di solo CPU *sintetico* a ogni database tenant. Il generatore avvia un processo per ogni database tenant, che chiama periodicamente una stored procedure che genera il carico. I livelli di carico, espressi in DTU, la durata e gli intervalli sono diversi per i vari database, in modo da simulare un'attività imprevedibile dei tenant.

1. In **PowerShell ISE** aprire …\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenere lo script aperto durante l'esecuzione dei vari scenari presentati in questa esercitazione.
1. Impostare **$DemoScenario** = **2**, _Generare un carico di normale intensità_.
1. Premere **F5** per applicare un carico a tutti i tenant.

L'app di database multi-tenant Wingtip Tickets è un'app SaaS e il carico di lavoro reale sulle app di questo tipo è in genere sporadico e imprevedibile. Per simulare questa situazione, il generatore produce un carico casuale distribuito tra tutti i tenant. Servono alcuni minuti perché emerga il modello di carico, quindi eseguire il generatore di carico per 3-5 minuti prima di provare a monitorare il carico come descritto nelle sezioni seguenti.

> [!IMPORTANT]
> Il generatore di carico esegue una serie di processi in una nuova finestra di PowerShell. Se si chiude la sessione, il generatore di carico si arresta. Il generatore di carico rimane in uno stato di *chiamata del processo* in cui genera il carico in tutti i nuovi tenant di cui viene effettuato il provisioning dopo l'avvio del generatore. Usare *CTRL+C* per arrestare la chiamata di nuovi processi e chiudere lo script. L'esecuzione del generatore di carico continuerà, ma solo nei tenant esistenti.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorare l'utilizzo delle risorse usando il portale di Azure

Per monitorare l'utilizzo delle risorse risultante dal carico applicato, aprire il portale per il database multi-tenant, **tenants1**, che contiene i tenant:

1. Aprire il [portale di Azure](https://portal.azure.com) e passare al server *tenants1-mt-&lt;UTENTE&gt;*.
1. Scorrere verso il basso per individuare i database e fare clic su **tenants1**. Questo database multi-tenant partizionato contiene tutti i tenant creati finora.

![grafico dei database](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Osservare il grafico **DTU**.

## <a name="set-performance-alerts-on-the-database"></a>Impostare avvisi sulle prestazioni per il database

Impostare un avviso per il database da attivare quando l'utilizzo raggiunge il \>75%, come indicato di seguito:

1. Aprire il database *tenants1* (sul server *tenants1-mt-&lt;UTENTE&gt;*) nel [portale di Azure](https://portal.azure.com).
1. Fare clic su **Regole di avviso** e quindi fare clic su **+ Aggiungi avviso**:

   ![aggiungere un avviso](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Specificare un nome, ad esempio **DTU elevate**.
1. Impostare i valori seguenti:
   * **Metrica = Percentuale DTU**
   * **Condizione = maggiore di**
   * **Soglia = 75**.
   * **Periodo = Negli ultimi 30 minuti**
1. Aggiungere un indirizzo e-mail alla casella *Indirizzi di posta elettronica aggiuntivi dell'amministratore* e fare clic su **OK**.

   ![impostare l'avviso](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Aumentare le prestazioni di un database con carico eccessivo

Se il livello di carico per un database aumenta fino a superarne la capacità massima e raggiunge il 100% di utilizzo delle DTU, ciò ha un impatto negativo sulle prestazioni del database e può determinare un rallentamento dei tempi di risposta alle query.

**Come soluzione a breve termine**, valutare l'opportunità di aumentare le prestazioni del database fornendo risorse aggiuntive oppure di rimuovere tenant dal database multi-tenant e spostarli in un database autonomo.

**Nel lungo termine**, prendere in considerazione l'ottimizzazione delle query o dell'utilizzo degli indici per migliorare le prestazioni dei database. A seconda di quanto un'applicazione è sensibile agli effetti dei problemi di prestazioni, è buona norma aumentare le prestazioni di un database prima che venga raggiunto il 100% di utilizzo delle DTU. Usare un avviso per ricevere segnalazioni con il dovuto anticipo.

È possibile simulare le condizioni di carico eccessivo per un database aumentando il carico prodotto dal generatore. Creando picchi più frequenti e di maggiore durata per i tenant si aumenta il carico del database multi-tenant senza modificare i requisiti dei singoli tenant. È possibile aumentare con facilità le prestazioni del database usando il portale o PowerShell. In questo esercizio viene usato il portale.

1. Impostare *$DemoScenario* = **3**, _Generare un carico con picchi più lunghi e più frequenti per ogni database_ per aumentare l'intensità del carico aggregato del database senza modificare il carico di picco necessario per ogni tenant.
1. Premere **F5** per applicare un carico a tutti i database tenant.
1. Passare al database **tenants1** nel portale di Azure.

Monitorare il maggiore utilizzo di DTU del database nel grafico superiore. Prima di poter osservare gli effetti del maggiore carico di lavoro devono trascorrere alcuni minuti, ma presto il database dovrebbe raggiungere il massimo utilizzo. Pertanto, con il progressivo e costante aumento del carico, si verificherà presto un sovraccarico del database.

1. Per aumentare le prestazioni del database, fare clic su **Piano tariffario (piano DTU)** nel pannello delle impostazioni.
1. Impostare **DTU** su **100**. 
1. Fare clic su **Applica** per inviare la richiesta di ridimensionamento del database.

Tornare a **tenants1** > **Panoramica** per visualizzare i grafici di monitoraggio. Monitorare l'effetto dell'assegnazione di altre risorse al database, anche se, con pochi tenant e un carico casuale, non sempre è facile notare conseguenze evidenti finché non si prolunga l'esecuzione per un certo periodo di tempo. Mentre si esaminano i grafici, tenere presente che il valore 100% nel grafico superiore rappresenta ora 100 DTU, mentre lo stesso valore nel grafico inferiore corrisponde ancora a 50 DTU.

I database rimango online e pienamente disponibili durante l'intero processo. Il codice dell'applicazione dovrebbe sempre essere scritto in modo da prevedere tentativi di riattivazione delle connessioni interrotte, consentendo così all'applicazione di riconnettersi al database.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Effettuare il provisioning di un nuovo tenant in un database autonomo 

Il modello multi-tenant partizionato consente di scegliere se effettuare il provisioning di un nuovo tenant in un database multi-tenant, insieme ad altri tenant, oppure in un database autonomo. Nel secondo caso, è possibile sfruttare i vantaggi offerti dall'isolamento del tenant nel database separato e quindi gestire le prestazioni del tenant, eseguirne il ripristino e così via in modo indipendente dagli altri. Può ad esempio essere utile inserire i clienti standard o i sottoscrittori di una prova gratuita in un database multi-tenant e i clienti premium in singoli database.  Anche se vengono creati database isolati a tenant singolo, è possibile gestirli collettivamente in un pool elastico per ottimizzare i costi delle risorse.

Se si è già effettuato il provisioning di un nuovo tenant in un database autonomo, ignorare i passaggi successivi.

1. In **PowerShell ISE**, aprire …\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Modificare **$TenantName = "Salix Salsa"** e **$VenueType = "dance"**.
1. Impostare **$Scenario** = **2**, _Effettuare il provisioning di un tenant in un nuovo database a tenant singolo_.
1. Premere **F5** per eseguire lo script.

Lo script effettuerà il provisioning del tenant in un database separato, registrerà il database e il tenant nel catalogo e quindi aprirà la pagina degli eventi del tenant nel browser. Aggiornare la pagina Events Hub (Hub eventi). Si noterà che "Salix Salsa" è stato aggiunto come sede di eventi.

## <a name="manage-performance-of-a-single-database"></a>Gestire le prestazioni di un database singolo

Se un tenant singolo all'interno di un database multi-tenant è sottoposto a un carico elevato per un tempo prolungato, potrebbe tendenzialmente usare in modo predominante le risorse del database con effetti sugli altri tenant presenti nello stesso database. Se si prevede che questa attività continui per un certo tempo, è possibile spostare temporaneamente il tenant fuori dal database o in un database autonomo a tenant singolo. In questo modo, il tenant può avere a disposizione le risorse aggiuntive necessarie ed essere completamente isolato dagli altri tenant.

Questo esercizio simula l'effetto di un carico elevato per Salix Salsa in concomitanza con l'inizio della vendita dei biglietti per un evento di grande richiamo.

1. Aprire lo script …\\*Demo-PerformanceMonitoringAndManagement.ps1*.
1. Impostare **$DemoScenario = 5**, _Generare un carico normale e un carico elevato in un tenant singolo (circa 90 DTU)_.
1. Impostare **$SingleTenantName = Salix Salsa**.
1. Eseguire lo script con **F5**.

Accedere al portale e passare a **salixsalsa** > **Panoramica** per visualizzare i grafici di monitoraggio. 

## <a name="other-performance-management-patterns"></a>Altri modelli di gestione delle prestazioni

**Scalabilità self-service dei tenant**

Dato che la scalabilità richiede attività facilmente richiamabili tramite l'API di gestione, è possibile integrare in modo semplice funzioni di scalabilità dei database tenant nell'applicazione che interagisce con i tenant e offrire tali funzioni nell'ambito del servizio SaaS. Ad esempio, fare in modo che i tenant possano amministrare in autonomia l'aumento o la riduzione delle prestazioni, eventualmente collegando anche queste operazioni in modo diretto alla fatturazione.

**Aumento e riduzione delle prestazioni di un database con una pianificazione in base ai modelli di utilizzo**

Se l'utilizzo aggregato dei tenant segue modelli prevedibili, è possibile usare Automazione di Azure per aumentare e ridurre le prestazioni di un database con una pianificazione. Ad esempio, ridurre le prestazioni dopo le 18 e aumentarle di nuovo prima delle 6 nei giorni della settimana in cui notoriamente si verifica un calo delle richieste di risorse.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Simulare l'utilizzo in un database multi-tenant partizionato tramite un generatore di carico specificato
> * Monitorare il database mentre risponde all'aumento del carico
> * Aumentare le prestazioni del database in risposta al carico maggiore
> * Eseguire il provisioning di un tenant in un database a tenant singolo

## <a name="additional-resources"></a>Risorse aggiuntive

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automazione di Azure](../automation/automation-intro.md)