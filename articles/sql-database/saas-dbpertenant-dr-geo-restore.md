---
title: Ripristino di emergenza per app SaaS tramite i backup con ridondanza geografica del database SQL di Azure | Microsoft Docs
description: Informazioni su come usare i backup con ridondanza geografica del database SQL di Azure per ripristinare un'app SaaS multi-tenant in caso di interruzione
keywords: esercitazione database SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 04/16/2018
ms.author: ayolubek
ms.openlocfilehash: a677e6eb583e293f83df824804aa4cd6f8f5d778
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="recover-a-multi-tenant-saas-application-using-geo-restore-from-database-backups"></a>Ripristinare un'applicazione SaaS multi-tenant tramite il ripristino geografico dai backup del database

In questa esercitazione si esamina uno scenario completo di ripristino di emergenza per un'applicazione SaaS multi-tenant la cui implementazione usa il modello di database per tenant. Si usa il [_ripristino geografico_](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-recovery-using-backups) per ripristinare in un'area alternativa i database di catalogo e tenant dai backup con ridondanza geografica gestiti automaticamente. Dopo aver risolto il problema, si usa la [_replica geografica_](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) per ricollocare i database modificati nella rispettiva area di origine.

![geo-restore-architecture](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Il ripristino geografico è la soluzione di ripristino di emergenza più a basso costo per il database SQL.  Tuttavia, il ripristino dai backup con ridondanza geografica può comportare la perdita di dati fino a un massimo di un'ora e può richiedere molto tempo, a seconda della dimensione di ogni database. **Per ripristinare le applicazioni con RPO (Recovery Point Objective) e RTO (Recovery Time Objective) minimi, usare la replica geografica in alternativa al ripristino di geografico**.

Questa esercitazione illustra entrambi i flussi di lavoro di ripristino e ricollocamento. Si apprenderà come:
> [!div class="checklist"]

>* Sincronizzare le informazioni di configurazione di database e pool elastici nel catalogo dei tenant
>* Configurare un ambiente con immagine speculare in un'area di ripristino che include applicazione, server e pool    
>* Ripristinare i database di catalogo e tenant con il _ripristino geografico_
>* Ricollocare il catalogo dei tenant e i database tenant modificati usando la _replica geografica_ dopo che il problema di interruzione è stato risolto
>* Aggiornare il catalogo mentre ogni database viene ripristinato (o ricollocato) per tenere traccia della posizione corrente della copia attiva del database di ogni tenant
>* Verificare che l'applicazione e il database tenant si trovino sempre nella stessa area di Azure per ridurre la latenza  
 

Prima di iniziare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:
* È stata distribuita l'app SaaS di database per tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione di database per tenant SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).  
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introduzione al modello di ripristino geografico

Il ripristino di emergenza è importante per molte applicazioni, per motivi di conformità o per garantire la continuità aziendale. In caso di interruzione prolungata del servizio, un piano di ripristino di emergenza adeguato può ridurre al minimo le interruzioni delle attività aziendali. Un piano di ripristino di emergenza basato sul ripristino geografico deve soddisfare vari obiettivi:
 * Riservare tutta la capacità necessaria nell'area di ripristino selezionata il più rapidamente possibile per assicurarsi che sia disponibile per ripristinare i database tenant.
 * Definire un ambiente di ripristino con immagine speculare in base alla configurazione originale di pool e database. 
 * Consentire l'annullamento del processo di ripristino mentre è in corso il trasferimento se l'area di origine torna online.
 * Abilitare rapidamente il provisioning di tenant in modo da poter riavviare il prima possibile l'onboarding di nuovi tenant.  
 * Essere ottimizzato per il ripristino dei tenant in ordine di priorità.
 * Essere ottimizzato per portare i tenant online il più velocemente possibile eseguendo i passaggi in parallelo, dove possibile.
 * Garantire resilienza agli errori, possibilità di riavvio e idempotenza.
 * Ricollocare i database nella rispettiva area di origine con un impatto minimo sui tenant quando il problema viene risolto.  

> [!Note]
> L'applicazione viene ripristinata nell'_area abbinata_ all'area in cui è distribuita l'applicazione. Per altre informazioni, vedere [Aree abbinate di Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).   



In questa esercitazione gli obiettivi appena elencati vengono soddisfatti usando le funzionalità del database SQL di Azure e della piattaforma Azure:

* [Modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), per riservare tutta la capacità necessaria il più rapidamente possibile. I modelli di Azure Resource Manager vengono usati per effettuare il provisioning di un'immagine speculare dei server e dei pool elastici di origine nell'area di ripristino. Per il provisioning di nuovi tenant vengono creati anche un server e un pool separati. 
* [Libreria EDCL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (Elastic Database Client Library, libreria client dei database elastici), per creare e gestire un catalogo di database tenant.  Il catalogo viene esteso per includere periodicamente informazioni di configurazione aggiornate di pool e database.
* [Funzionalità di ripristino della gestione delle partizioni](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-recovery-manager) della libreria EDCL, per gestire le voci relative alle posizioni dei database nel catalogo durante il ripristino e il ricollocamento.  
* [Ripristino geografico](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery), per ripristinare i database di catalogo e tenant dai backup con ridondanza geografica gestiti automaticamente. 
* [Operazioni di ripristino asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) inviate in ordine di priorità di tenant, che vengono inserite in coda per ogni pool dal sistema ed elaborate in batch per evitare il sovraccarico del pool. Queste operazioni possono essere annullate prima o durante l'esecuzione, se necessario.    
* [Replica geografica](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview), per ricollocare i database nell'area di origine dopo l'interruzione. L'uso della replica geografica consente di evitare la perdita di dati e di ridurre al minimo l'impatto sul tenant.
* [Alias DNS del server SQL](https://docs.microsoft.com/azure/sql-database/dns-alias-overview), per consentire la connessione del processo di sincronizzazione con il catalogo attivo indipendentemente dalla relativa posizione.  

## <a name="get-the-disaster-recovery--scripts"></a>Ottenere gli script per il ripristino di emergenza 

Gli script per il ripristino di emergenza usati in questa esercitazione sono disponibili nel [repository GitHub della soluzione SaaS di database per tenant Wingtip Tickets](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script di gestione di Wingtip Tickets.
> [!IMPORTANT]
> Come tutti gli script di gestione di Wingtip Tickets, gli script di ripristino di emergenza sono solo esempi e non devono essere usati nell'ambiente di produzione.   

## <a name="review-the-healthy-state-of-the-application"></a>Esaminare lo stato di integrità dell'applicazione
Prima di iniziare il processo di ripristino, esaminare il normale stato di integrità dell'applicazione.
1. Nel Web browser aprire l'hub eventi di Wingtip Tickets all'indirizzo http://events.wingtip-dpt.&lt;utente&gt;.trafficmanager.net sostituendo &lt;utente&gt; con il valore utente della distribuzione.
    * Scorrere la pagina verso il basso e osservare il nome e la posizione del server di catalogo nella parte inferiore della pagina.  La posizione corrisponde all'area in cui l'app è stata distribuita.    
        Suggerimento: passare il puntatore del mouse sulla posizione per ingrandire la visualizzazione.

    ![Stato di integrità dell'hub eventi nell'area di origine](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

1. Fare clic sul tenant Contoso Concert Hall e aprire la relativa pagina degli eventi.
    * Nella parte inferiore della pagina osservare il nome del server dei tenant. La posizione corrisponderà a quella del server di catalogo.

    ![Area di origine di Contoso Concert Hall](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)    
1. Nel [portale di Azure](https://portal.azure.com) esaminare e aprire il gruppo di risorse in cui è stata distribuita l'app.
    * Osservare le risorse e l'area in cui vengono distribuiti i componenti del servizio app e i server del database SQL.

## <a name="sync-tenant-configuration-into-catalog"></a>Sincronizzare la configurazione dei tenant nel catalogo

In questa attività si avvia un processo per sincronizzare la configurazione di server, pool elastici e database nel catalogo dei tenant.  Queste informazioni verranno usate in un secondo momento per configurare un ambiente con immagine speculare nell'area di ripristino.

> [!IMPORTANT]
> Per semplicità, il processo di sincronizzazione e gli altri processi di ripristino e ricollocamento a esecuzione prolungata vengono implementati in questi esempi come sessioni o processi di PowerShell locali che vengono eseguiti con l'account di accesso utente client. I token di autenticazione rilasciati al momento dell'accesso scadono dopo alcune ore e i processi avranno quindi esito negativo. In uno scenario di produzione, i processi a esecuzione prolungata devono essere implementati come servizi di Azure affidabili, in esecuzione in un'entità servizio. Vedere [Usare Azure PowerShell per creare un'entità servizio con un certificato](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. In _PowerShell ISE_ aprire il file ...\Learning Modules\UserConfig.psm1. Sostituire `<resourcegroup>` e `<user>` alle righe 10 e 11 con il valore usato al momento della distribuzione dell'app.  Salvare il file.

2. In *PowerShell ISE* aprire lo script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.
    *  Durante questa esercitazione si eseguirà ogni scenario in questo script di PowerShell. Tenere quindi aperto questo file.

3. Impostare quanto segue:
    * **$DemoScenario = 1**, per avviare un processo in background che esegue la sincronizzazione del server tenant e delle informazioni di configurazione dei pool nel catalogo.

3. Premere **F5** per eseguire lo script di sincronizzazione. 
    *  Queste informazioni verranno usate in un secondo momento per verificare che il processo di ripristino crei un'immagine speculare dei server, dei pool e dei database nell'area di ripristino.  
![Processo di sincronizzazione](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Lasciare la finestra di PowerShell in esecuzione in background e continuare con il resto dell'esercitazione.

> [!Note]
> Il processo di sincronizzazione si connette al catalogo tramite un alias DNS. Questo alias viene modificato durante il ripristino e il ricollocamento in modo da puntare al catalogo attivo. Il processo di sincronizzazione mantiene aggiornato il catalogo con le modifiche apportate alla configurazione dei database o dei pool nell'area di ripristino.  Durante il ricollocamento, queste modifiche vengono applicate alle risorse equivalenti nell'area di origine.

## <a name="geo-restore-recovery-process-overview"></a>Panoramica del processo di ripristino geografico

Il processo di ripristino geografico distribuisce l'applicazione e ripristina i database dai backup nell'area di ripristino.

Il processo di ripristino esegue le operazioni seguenti:

1. Disabilita l'endpoint di Gestione traffico per l'app Web nell'area di origine. La disabilitazione dell'endpoint impedisce agli utenti di connettersi all'app con stato non valido nel caso in cui l'area di origine torni online durante il ripristino.

1. Effettua il provisioning di un server di catalogo di ripristino nell'area di ripristino, esegue il ripristino geografico del database di catalogo e aggiorna l'alias _activecatalog_ in modo da puntare al server di catalogo ripristinato.  
    * Modificando l'alias del catalogo si ha la sicurezza che venga sempre eseguita la sincronizzazione con il catalogo attivo.

1. Contrassegna tutti i tenant esistenti nel catalogo di ripristino come offline per impedire l'accesso ai database tenant prima del ripristino.

1. Effettua il provisioning di un'istanza dell'app nell'area di ripristino e la configura in modo da usare il catalogo ripristinato in tale area.
    * Per mantenere al minimo la latenza, l'app di esempio è stata progettata in modo da connettersi sempre a un database tenant nella stessa area.

1. Effettua il provisioning di un server e di un pool elastico in cui verrà effettuato il provisioning di nuovi tenant. Creando queste risorse si ha la sicurezza che il provisioning di nuovi tenant non interferisca con il ripristino dei tenant esistenti.

1. Aggiorna l'alias new-tenant in modo da puntare al server per i nuovi database tenant nell'area di ripristino. Modificando questo alias si ha la sicurezza che il provisioning dei database per i nuovi tenant venga effettuato nell'area di ripristino.
        
1. Effettua il provisioning di server e pool elastici nell'area di ripristino per ripristinare i database tenant. Questi server e pool sono un'immagine speculare della configurazione nell'area di origine.  Effettuando in anticipo il provisioning dei pool si può riservare la capacità necessaria per ripristinare tutti i database.
    * Un'interruzione in un'area può avere un impatto notevole sull'utilizzo delle risorse disponibili nell'area abbinata.  Se si prevede di usare il ripristino geografico per il ripristino di emergenza, è consigliabile riservare rapidamente le risorse. Valutare l'opportunità di usare la replica geografica se è fondamentale che un'applicazione venga ripristinata in un'area specifica. 

1. Abilita l'endpoint di Gestione traffico per l'app Web nell'area di ripristino. L'abilitazione di questo endpoint consente all'applicazione di effettuare il provisioning di nuovi tenant. In questa fase, i tenant esistenti sono ancora offline.

1. Invia batch di richieste per ripristinare i database in ordine di priorità. 
    * I batch sono organizzati in modo che il ripristino dei database venga eseguito in parallelo in tutti i pool.  
    * L'invio delle richieste di ripristino viene eseguito in maniera asincrona, in modo che le richieste vengano inviate rapidamente e inserite in coda per l'esecuzione in ogni pool.
    * Poiché le richieste di ripristino vengono elaborate in parallelo in tutti i pool, è preferibile distribuire i tenant importanti in più pool. 

1. Monitora il servizio di database SQL per determinare quando vengono ripristinati i database. Una volta ripristinato, un database tenant viene contrassegnato come online nel catalogo e per tale database viene registrata la somma dei valori rowversion. 
    * L'applicazione può accedere ai database tenant non appena vengono contrassegnati come online nel catalogo.
    * Nel catalogo viene archiviata la somma dei valori rowversion per il database tenant. Questa somma funge da impronta digitale che consente al processo di ricollocamento di determinare se il database è stato aggiornato nell'area di ripristino.      

## <a name="run-the-recovery-script"></a>Eseguire lo script di ripristino

> [!IMPORTANT]
> Questa esercitazione illustra come ripristinare i database dai backup con ridondanza geografica. Anche se questi backup sono in genere disponibili entro 10 minuti, talvolta può trascorrere anche un'ora prima che siano disponibili. Lo script viene sospeso finché non sono disponibili  e può quindi essere necessario attendere del tempo.

Si supponga ora che si verifichi un'interruzione nell'area in cui l'applicazione è distribuita e che quindi si esegua lo script di ripristino:

1. In *PowerShell ISE* aprire lo script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 e impostare quanto segue:
    * **$DemoScenario = 2**, per ripristinare l'app in un'area di ripristino da backup con ridondanza geografica.

1. Premere **F5** per eseguire lo script.  
    * Lo script viene aperto in una nuova finestra di PowerShell e quindi avvia un set di processi di PowerShell eseguiti in parallelo  che ripristinano i server, i pool e i database nell'area di ripristino. 
    * L'area di ripristino è l'_area abbinata_ all'area di Azure in cui è stata distribuita l'applicazione. Per altre informazioni, vedere [Aree abbinate di Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions). 

1. Monitorare lo stato del processo di ripristino nella finestra di PowerShell.

    ![Processo di ripristino](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

>Per esplorare il codice per i processi di ripristino, esaminare gli script di PowerShell nella cartella ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Esaminare lo stato dell'applicazione durante il ripristino
Quando l'endpoint dell'applicazione è disabilitato in Gestione traffico, l'applicazione non è disponibile. Il catalogo viene ripristinato e tutti i tenant vengono contrassegnati come offline.  L'endpoint dell'applicazione nell'area di ripristino viene quindi abilitato e l'applicazione torna online. Anche se l'applicazione è disponibile, i tenant risultano offline nell'hub eventi finché non vengono ripristinati i rispettivi database. È importante progettare l'applicazione in modo da gestire i database tenant offline.

1. Dopo che il database di catalogo è stato ripristinato, ma prima che i tenant siano tornati online, aggiornare l'hub eventi di Wingtip Tickets nel Web browser.
    * Nella parte inferiore della pagina osservare che il nome del server di catalogo include ora un suffisso _-recovery_ e si trova nell'area di ripristino.
    * Si noti che i tenant non ancora ripristinati sono contrassegnati come offline e non sono selezionabili.   
 
    ![Processo di ripristino](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

    * Se si apre direttamente la pagina degli eventi di un tenant mentre questo è offline, viene visualizzata una notifica per segnalare che il tenant è offline. Se, ad esempio, Contoso Concert Hall è offline, provare ad aprire http://events.wingtip-dpt.&lt;utente&gt;.trafficmanager.net/contosoconcerthall![processo di ripristino](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Effettuare il provisioning di un nuovo tenant nell'area di ripristino
Ancora prima del ripristino dei database tenant, è possibile effettuare il provisioning di nuovi tenant nell'area di ripristino. Questi ultimi verranno ricollocati insieme ai database ripristinati in un secondo momento.   

1. In *PowerShell ISE* aprire lo script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 e impostare la proprietà seguente:
    * **$DemoScenario = 3**, per effettuare il provisioning di un nuovo tenant nell'area di ripristino.

1. Premere **F5** per eseguire lo script. 

1. Al termine del provisioning, la pagina degli eventi di Hawthorn Hall viene aperta nel browser. 
    * Si noti che il database Hawthorn Hall si trova nell'area di ripristino.
    ![Provisioning di Hawthorn Hall nell'area di ripristino](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

1. Nel browser aggiornare la pagina dell'hub eventi di Wingtip Tickets in modo da visualizzare anche Hawthorn Hall. 
    * Se è stato effettuato il provisioning di Hawthorn Hall senza attendere il ripristino degli altri tenant, è possibile che questi siano ancora offline.

## <a name="review-the-recovered-state-of-the-application"></a>Esaminare lo stato ripristinato dell'applicazione

Al termine del processo di ripristino, l'applicazione e tutti i tenant sono perfettamente funzionanti nell'area di ripristino. 

1. Quando nella finestra della console di PowerShell viene indicato che tutti i tenant sono stati ripristinati, aggiornare l'hub eventi.  Tutti i tenant risulteranno online, incluso il nuovo tenant Hawthorn Hall.

    ![Tenant ripristinati e nuovi nell'hub eventi](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

1. Fare clic su Contoso Concert Hall e aprire la relativa pagina degli eventi.
    * Nella parte inferiore della pagina si noti che il database si trova nel server di ripristino all'interno dell'area di ripristino.

    ![Contoso nell'area di ripristino](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

1. Nel [portale di Azure](https://portal.azure.com) aprire l'elenco dei gruppi di risorse.  
    * Osservare il gruppo di risorse distribuito, oltre al gruppo di risorse di ripristino, con il suffisso _-recovery_.  Il gruppo di risorse di ripristino contiene tutte le risorse create durante il processo di ripristino, oltre alle nuove risorse create durante l'interruzione.  

1. Aprire il gruppo di risorse di ripristino e osservare gli elementi seguenti:
    * Le versioni di ripristino dei server di catalogo e tenants1, con il suffisso _-recovery_.  Tutti i database di catalogo e tenant ripristinati in questi server hanno i nomi usati nell'area di origine.

    * Il server SQL _tenants2-dpt-&lt;utente&gt;-recovery_.  Questo server viene usato per il provisioning di nuovi tenant durante l'interruzione.
    *   Il servizio app denominato _events-wingtip-dpt-&lt;arearipristino&gt;-&lt;utente&gt;_, che rappresenta l'istanza di ripristino dell'app Events. 

    ![Contoso nell'area di ripristino](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png)   
    
1. Aprire il server SQL _tenants2-dpt-&lt;utente&gt;-recovery_.  Osservare che contiene il database _hawthornhall_ e il pool elastico _Pool1_.  Il database _hawthornhall_ è configurato come database elastico nel pool elastico _Pool1_.

## <a name="change-tenant-data"></a>Modificare i dati di un tenant 
In questa attività si aggiorna uno dei database tenant ripristinati. Il processo ricollocamento copierà nell'area di origine i database ripristinati che sono stati modificati. 

1. Nel browser trovare l'elenco degli eventi per Contoso Concert Hall, scorrere gli eventi e notare l'ultimo, _Seriously Strauss_.

1. In *PowerShell ISE* aprire lo script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 e impostare il valore seguente:
    * **$DemoScenario = 4**, per eliminare un evento da un tenant nell'area di ripristino.
1. Premere **F5** per eseguire lo script.
1. Aggiornare la pagina degli eventi di Contoso Concert Hall (http://events.wingtip-dpt.&lt;utente&gt;.trafficmanager.net/contosoconcerthall). Si noterà che l'evento Seriously Strauss non è presente.

A questo punto dell'esercitazione l'applicazione è stata ripristinata ed è in esecuzione nell'area di ripristino.  È stato effettuato il provisioning di un nuovo tenant nell'area di ripristino e sono stati modificati i dati di uno dei tenant ripristinati.  

> [!Note]
> Altre esercitazioni nell'esempio sono state progettate per l'esecuzione con l'app in stato di ripristino. Per esaminare altre esercitazioni, assicurarsi di ricollocare prima l'applicazione.

## <a name="repatriation-process-overview"></a>Panoramica del processo di ricollocamento

Il processo di ricollocamento ripristina l'applicazione e i relativi database nell'area di origine dopo che è stato risolto un problema di interruzione.

![Ricollocamento con ripristino geografico](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 


Il processo esegue le operazioni seguenti:

1. Arresta le attività di ripristino in corso e annulla eventuali richieste di ripristino di database in sospeso o in elaborazione.

1. Riattiva nell'area di origine i database tenant che non sono stati modificati dopo l'interruzione.  Sono inclusi i database non ancora ripristinati e quelli ripristinati ma non modificati successivamente. I database riattivati sono esattamente gli stessi a cui l'ultima volta hanno eseguito l'accesso i rispettivi tenant.

1. Effettua il provisioning di un'immagine speculare del server e del pool elastico dei nuovi tenant nell'area di origine. Al termine di questa operazione, l'alias new-tenant viene aggiornato in modo da puntare a questo server. Se si aggiorna l'alias, l'onboarding dei nuovi tenant viene eseguito nell'area di origine anziché nell'area di ripristino.

1. Usando la replica geografica, sposta il catalogo dall'area di ripristino all'area di origine.

1. Aggiorna la configurazione dei pool nell'area di origine in modo che sia coerente con le modifiche apportate nell'area di ripristino durante l'interruzione.

1. Crea uno o più server e pool necessari per ospitare i nuovi database creati durante l'interruzione.

1. Usando la replica geografica, ricolloca i database tenant ripristinati che sono stati aggiornati dopo il ripristino e tutti i nuovi database tenant di cui è stato effettuato il provisioning durante l'interruzione. 

1. Esegue la pulizia delle risorse create nell'area di ripristino durante il processo di ripristino.

Per limitare il numero di database tenant da ricollocare, i passaggi da 1 a 3 vengono eseguiti immediatamente.  

Il passaggio 4 viene eseguito solo se il catalogo nell'area di ripristino è stato modificato durante l'interruzione. Il catalogo viene aggiornato se sono stati creati nuovi tenant o se è stata modificata una configurazione di database o pool nell'area di ripristino.

È importante che nel passaggio 7 sia ridotta al minimo l'interruzione per i tenant e che non si verifichino perdite di dati. A tale scopo, il processo usa la _replica geografica_.

Prima dell'esecuzione della replica geografica di ogni database, il database corrispondente nell'area di origine viene eliminato. Viene quindi eseguita la replica geografica del database nell'area di ripristino e viene creata una replica secondaria nell'area di origine. Al termine della replica, il tenant viene contrassegnato come offline nel catalogo. Per effetto di questa operazione, vengono interrotte tutte le connessioni al database nell'area di ripristino. Viene quindi eseguito il failover del database in modo da consentire l'elaborazione delle transazioni in sospeso nella replica secondaria e impedire così la perdita di dati. In fase di failover, i ruoli dei database vengono invertiti.  Il database secondario nell'area di origine diventa il database primario di lettura/scrittura e il database nell'area di ripristino diventa un database secondario di sola lettura. La voce del tenant nel catalogo viene aggiornata in modo da fare riferimento al database nell'area di origine e il tenant viene contrassegnato come online.  A questo punto, il ricollocamento del database è completato. 

È necessario scrivere le applicazioni con una logica di retry per assicurarsi che si riconnettano automaticamente in caso di interruzione delle connessioni.  Quando si riconnettono usando il catalogo per negoziare la connessione, questa viene stabilita con il database ricollocato nell'area di origine. Anche se questa breve disconnessione spesso non viene notata, può essere opportuno ricollocare i database nell'area di origine al di fuori dell'orario lavorativo. 

Dopo il ricollocamento di un database, il database secondario nell'area di ripristino può essere eliminato. Il database nell'area di origine si baserà nuovamente sul ripristino geografico per la protezione tramite ripristino di emergenza.

Nel passaggio 8 le risorse presenti nell'area di ripristino, inclusi i server e i pool di ripristino, vengono eliminate.

## <a name="run-the-repatriation-script"></a>Eseguire lo script di ricollocamento
Si supponga ora che il problema di interruzione sia stato risolto e che quindi si esegua lo script di ricollocamento.

Se si sono seguiti i passaggi dell'esercitazione, lo script riattiva immediatamente i tenant Fabrikam Jazz Club e Dogwood Dojo nell'area di origine perché non sono stati modificati. Lo script ricolloca quindi il nuovo tenant Hawthorn Hall, il tenant Contoso Concert Hall perché modificato e anche il catalogo, che è stato aggiornato durante il provisioning di Hawthorn Hall.
  
1. In *PowerShell ISE* aprire lo script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

1. Verificare che il processo di sincronizzazione del catalogo sia ancora in esecuzione nell'istanza di PowerShell.  Se necessario, riavviarlo impostando quanto segue:
    * **$DemoScenario = 1**, per avviare la sincronizzazione delle informazioni di configurazione di server tenant, pool e database nel catalogo.
    * Premere **F5** per eseguire lo script.
1.  Avviare quindi il processo di ricollocamento impostando quanto segue:
    * **$DemoScenario = 5**, per ricollocare l'app nell'area di origine.
    * Premere **F5** per eseguire lo script di ripristino in una nuova finestra di PowerShell.  Il processo di ricollocamento richiede alcuni minuti e può essere monitorato nella finestra di PowerShell.
1. Mentre lo script è in esecuzione, aggiornare la pagina dell'hub eventi (http://events.wingtip-dpt.&lt;utente&gt;.trafficmanager.net)
    * Si noti che tutti i tenant sono online e accessibili durante questo processo.
1. Fare clic su Fabrikam Jazz Club per aprirlo. Se questo tenant non è stato modificato, nella parte inferiore della pagina si noterà che il server di origine è già stato ripristinato.
1. Aprire o aggiornare la pagina degli eventi di Contoso Concert Hall. Nella parte inferiore della pagina si noterà che il database è ancora nel server con il suffisso _-recovery_.  
1. Aggiornare la pagina degli eventi di Contoso Concert Hall al termine del processo di collocamento. Si noterà che il database è ora nell'area di origine.
1. Aggiornare di nuovo l'hub eventi e aprire Hawthorn Hall. Si noterà che anche il database di questo tenant si trova nell'area di origine. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Eseguire la pulizia delle risorse dell'area di ripristino dopo il ricollocamento
Al termine del ricollocamento, è possibile eliminare le risorse nell'area di ripristino senza alcun rischio. 

> [!IMPORTANT]
> È consigliabile eliminare queste risorse in modo tempestivo per arrestare la fatturazione dei costi correlati.

Il processo di ripristino crea tutte le risorse di ripristino in un apposito gruppo di risorse. Il processo di pulizia elimina questo gruppo di risorse e rimuove tutti i riferimenti alle risorse dal catalogo. 

1. In *PowerShell ISE* aprire lo script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 e impostare quanto segue:
    * **$DemoScenario = 6**, per eliminare le risorse obsolete dall'area di ripristino.

1. Premere **F5** per eseguire lo script.

Dopo la pulizia degli script, l'applicazione torna allo stato iniziale.  A questo punto è possibile eseguire nuovamente lo script o provare altre esercitazioni.

## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Progettazione dell'applicazione in modo da mantenere l'app e il database nella stessa area 
L'applicazione è progettata in modo da connettersi sempre da un'istanza nella stessa area del database tenant. Ciò consente di ridurre la latenza tra l'applicazione e il database. Questa ottimizzazione presuppone che l'interazione tra l'app e il database sia più frequente rispetto a quella tra l'utente e l'app.  

Per un certo intervallo di tempo durante il ricollocamento, i database tenant possono essere distribuiti tra aree di origine e di ripristino.  Per ogni database, l'app esegue una ricerca DNS nell'area in cui si trova il database in base al nome del server tenant. Nel database SQL il nome del server è un alias in cui è incluso il nome dell'area. Se l'applicazione non si trova nella stessa area del database, viene eseguito il reindirizzamento all'istanza presente nella stessa area del server di database.  In questo modo si riduce al minimo la latenza tra l'app e il database.  

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:
> [!div class="checklist"]

>* Usare il catalogo dei tenant per memorizzare periodicamente le informazioni di configurazione aggiornate e consentire così la creazione di un ambiente di ripristino con immagine speculare in un'altra area
>* Usare il ripristino geografico per ripristinare i database SQL di Azure nell'area di ripristino
>* Aggiornare il catalogo dei tenant in base alle posizioni dei database tenant  
>* Usare un alias DNS per consentire a un'applicazione di connettersi al catalogo dei tenant senza dover ridefinire la configurazione
>* Usare la replica geografica per ricollocare i database ripristinati nella rispettiva area di origine dopo che è stato risolto un problema di interruzione

A questo punto, passare a [Ripristino di emergenza per un'applicazione SaaS multi-tenant con la replica geografica del database](saas-dbpertenant-dr-geo-replication.md) per informazioni su come ridurre notevolmente il tempo necessario per ripristinare un'applicazione multi-tenant su larga scala usando la replica geografica.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sull'applicazione SaaS Wingtip](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-wtp-overview#sql-database-wingtip-saas-tutorials)
