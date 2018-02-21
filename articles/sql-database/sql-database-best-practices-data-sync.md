---
title: Procedure consigliate per la sincronizzazione dati SQL di Azure (anteprima)| Microsoft Docs
description: Informazioni sulle procedure consigliate per la configurazione e l'esecuzione della sincronizzazione dati SQL di Azure (anteprima).
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 1c8ad4b318d52b5cb6af284b3304cfa7ad35522b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Procedure consigliate per la sincronizzazione dati SQL: anteprima 

Questo articolo descrive le procedure consigliate per la sincronizzazione dati SQL di Azure (anteprima).

Per una panoramica della sincronizzazione dati SQL (anteprima), vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Sicurezza e affidabilità

### <a name="client-agent"></a>Agente client

-   Installare l'agente client usando un account utente con privilegi minimi e con accesso ai servizi di rete.  
-   Installare l'agente client in un computer diverso dal computer locale con SQL Server.  
-   Non registrare un database locale con più di un agente.    
    -   Evitare questa operazione anche se si intende sincronizzare tabelle diverse relative a diversi gruppi di sincronizzazione.  
    -   La registrazione di un database locale con più agenti client può causare problemi quando si elimina uno dei gruppi di sincronizzazione.

### <a name="database-accounts-with-least-required-privileges"></a>Account di database con privilegi minimi

-   **Per la configurazione della sincronizzazione**. Create/Alter Table; Alter Database; Create Procedure; Select/ Alter Schema; Create User-Defined Type.

-   **Per la sincronizzazione continua**. Select/Insert/Update/Delete su tabelle selezionate per la sincronizzazione e su tabelle di rilevamento e metadati di sincronizzazione; Execute su stored procedure create dal servizio; Execute su tipi di tabella definiti dagli utenti.

-   **Per il deprovisioning**. Alter su tabelle che fanno parte della sincronizzazione; Select/Delete su tabelle di metadati di sincronizzazione; Control su tabelle di rilevamento della sincronizzazione, stored procedure e tipi di tabelle definiti dall'utente.

Il database SQL di Azure supporta un solo set di credenziali. Per eseguire queste operazioni nei limiti di questo vincolo, considerare le opzioni seguenti:

-   Modificare le credenziali per le diverse fasi, ad esempio *credentials1* per la configurazione e *credentials2* per la sincronizzazione continua.  
-   Modificare l'autorizzazione delle credenziali, ovvero, modificare l'autorizzazione dopo aver configurato la sincronizzazione.

## <a name="setup"></a>Configurazione

### <a name="database-considerations-and-constraints"></a>Vincoli e considerazioni sui database

#### <a name="sql-database-instance-size"></a>Dimensione dell'istanza del database SQL

Quando si crea una nuova istanza di database SQL, impostare la dimensione massima in modo che sia sempre maggiore rispetto al database da distribuire. Se la dimensione massima impostata non è maggiore rispetto al database distribuito, la sincronizzazione avrà esito negativo. Anche se la sincronizzazione dati SQL (anteprima) non offre funzioni di aumento automatico, è possibile eseguire il comando `ALTER DATABASE` per aumentare le dimensioni del database dopo che è stato creato. Verificare di rientrare nei limiti delle dimensioni delle istanze di database SQL.

> [!IMPORTANT]
> La sincronizzazione dati SQL (anteprima) consente di archiviare metadati aggiuntivi con ogni database. Tenere conto di questi metadati quando si calcola lo spazio necessario. La maggiore quantità di overhead è correlata alla larghezza delle tabelle (ad esempio, le tabelle strette richiedono più overhead) e alla quantità di traffico.

### <a name="table-considerations-and-constraints"></a>Vincoli e considerazioni sulle tabelle

#### <a name="selecting-tables"></a>Selezione di tabelle

Non è necessario includere in un gruppo di sincronizzazione tutte le tabelle presenti in un database. Le tabelle che si includono in un gruppo di sincronizzazione incidono sui costi e sull'efficienza. Includere pertanto in un gruppo di sincronizzazione solo le tabelle necessarie all'azienda e quelle da cui le prime dipendono.

#### <a name="primary-keys"></a>Chiavi primarie

Ogni tabella inclusa in un gruppo di sincronizzazione deve avere una chiave primaria. Il servizio di sincronizzazione dati SQL (anteprima) non può sincronizzare una tabella che non dispone di una chiave primaria.

Prima di usare la sincronizzazione dati SQL (anteprima) in fase di produzione, testare le prestazioni della sincronizzazione iniziale e di quella continua.

### <a name="provisioning-destination-databases"></a>Provisioning dei database di destinazione

La sincronizzazione dati SQL (anteprima) consente il provisioning automatico di base dei database.

Questa sezione descrive le limitazioni del provisioning nel servizio di sincronizzazione dati SQL (anteprima).

#### <a name="autoprovisioning-limitations"></a>Limitazioni del provisioning automatico

Per quanto riguarda il provisioning automatico, la sincronizzazione dati SQL (anteprima) presenta le limitazioni seguenti:

-   Nella tabella di destinazione vengono create solo le colonne selezionate.  
    Nelle tabelle di destinazione non viene eseguito il provisioning delle colonne che non fanno parte del gruppo di sincronizzazione.
-   Gli indici vengono creati solo per le colonne selezionate.  
    Se gli indici della tabella di origine includono colonne che non fanno parte del gruppo di sincronizzazione, non verrà eseguito il provisioning di questi indici nelle tabelle di destinazione.  
-   Non viene eseguito il provisioning degli indici nelle colonne di tipo XML.  
-   Non viene eseguito il provisioning dei vincoli CHECK.  
-   Non viene eseguito il provisioning dei trigger esistenti nelle tabelle di origine.  
-   Non vengono create viste e stored procedure nel database di destinazione.

#### <a name="recommendations"></a>Raccomandazioni

-   Usare la funzionalità di provisioning automatico della sincronizzazione dati SQL (anteprima) solo per testare il servizio.  
-   Per la fase di produzione eseguire il provisioning dello schema del database.

### <a name="locate-hub"></a> Ubicazione del database hub

#### <a name="enterprise-to-cloud-scenario"></a>Scenario da azienda a cloud

Per ridurre al minimo la latenza, mantenere il database hub in prossimità della maggiore concentrazione del traffico del database del gruppo di sincronizzazione.

#### <a name="cloud-to-cloud-scenario"></a>Scenario da cloud a cloud

-   Quando tutti i database di un gruppo di sincronizzazione sono ubicati in un unico data center, l'hub deve trovarsi nello stesso data center. Questa configurazione riduce la latenza e il costo del trasferimento dei dati tra data center.
-   Quando i database di un gruppo di sincronizzazione sono ubicati in più data center, l'hub deve trovarsi nello stesso data center in cui si trova la maggior parte dei database e in cui è presente il maggior volume di traffico.

#### <a name="mixed-scenarios"></a>Scenari misti

Applicare le linee guida precedenti a configurazioni più complesse di gruppi di sincronizzazione, ad esempio a scenari misti da azienda a cloud e da cloud a cloud.

## <a name="sync"></a>Sincronizzazione

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitare una sincronizzazione iniziale lenta e dispendiosa

Questa sezione prende in esame la sincronizzazione iniziale di un gruppo di sincronizzazione e spiega come evitare che per tale sincronizzazione iniziale siano richiesti tempi più lunghi e costi più elevati del necessario.

#### <a name="how-initial-sync-works"></a>Funzionamento della sincronizzazione iniziale

Quando si crea un gruppo di sincronizzazione, iniziare con dati in un solo database. Se sono presenti dati in più database, la sincronizzazione dati SQL (anteprima) considera ogni riga come un conflitto da risolvere. La risoluzione dei conflitti causa un rallentamento della sincronizzazione iniziale, che può richiedere giorni o addirittura mesi a seconda delle dimensioni del database.

Se i database si trovano in data center diversi, ogni riga deve spostarsi da un data center a un altro, con il conseguente aumento dei costi della sincronizzazione iniziale.

#### <a name="recommendation"></a>Raccomandazione

Se possibile, iniziare con i dati in un unico database del gruppo di sincronizzazione.

### <a name="design-to-avoid-synchronization-loops"></a> Progettazione che evita i cicli di sincronizzazione

Si verifica un ciclo di sincronizzazione quando all'interno di un gruppo di sincronizzazione sono presenti riferimenti circolari. In uno scenario di questo tipo ogni modifica in un database viene replicata in modo circolare e all'infinito nei database del gruppo di sincronizzazione.   

È consigliabile evitare i cicli di sincronizzazione in quanto possono provocare una riduzione delle prestazioni e un aumento significativo dei costi.

### <a name="handling-changes-that-fail-to-propagate"></a> Modifiche che non vengono propagate

#### <a name="reasons-that-changes-fail-to-propagate"></a>Cause della mancata propagazione delle modifiche

È possibile che le modifiche apportate non vengano propagate per uno dei motivi seguenti:

-   Incompatibilità dello schema o del tipo di dati.
-   Inserimento di valori null in colonne che non ammettono valori null.
-   Violazione di vincoli di chiavi esterne.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Cosa accade se le modifiche non vengono propagate?

-   Il gruppo di sincronizzazione indica che si trova in uno stato di **Warning** (Avviso).
-   I dettagli sono elencati nel visualizzatore log dell'interfaccia utente del portale.
-   Se il problema non viene risolto per 45 giorni, il database risulterà "Non aggiornato".

> [!NOTE]
> Queste modifiche non verranno mai propagate. L'unico modo per risolvere il problema è ricreare il gruppo di sincronizzazione.

#### <a name="recommendation"></a>Raccomandazione

Monitorare regolarmente l'integrità del database e del gruppo di sincronizzazione attraverso l'interfaccia del portale e del log.


## <a name="maintenance"></a>Manutenzione 

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Evitare database e gruppi di sincronizzazione non aggiornati

È possibile che un gruppo di sincronizzazione o un database all'interno di un gruppo di sincronizzazione non sia più aggiornato. Quando lo stato di un gruppo di sincronizzazione è **Out-of-date** (Non aggiornato), il gruppo smette di funzionare. Quando lo stato di un database è **Out-of-date** (Non aggiornato), può verificarsi una perdita di dati. È consigliabile evitare questo scenario anziché provare a risolvere il problema.

#### <a name="avoid-out-of-date-databases"></a>Evitare database e gruppi di sincronizzazione non aggiornati

Lo stato di un database viene impostato su **Out-of-date** (Non aggiornato) quando il database è offline da almeno 45 giorni. Per evitare che lo stato di un database venga impostato su **Out-of-date** (Non aggiornato), verificare che nessun database rimanga offline per 45 giorni o più.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitare i gruppi di sincronizzazione non aggiornati

Lo stato di un gruppo di sincronizzazione viene impostato su **Out-of-date** (Non aggiornato) quando le modifiche nel gruppo non vengono propagate al resto del gruppo di sincronizzazione per 45 giorni o più. Per evitare che lo stato di un gruppo di sincronizzazione venga impostato su **Out-of-date** (Non aggiornato), controllare regolarmente il log della cronologia del gruppo di sincronizzazione. Verificare che tutti i conflitti siano risolti e che le modifiche vengano propagate a tutti i database del gruppo di sincronizzazione.

È possibile che un gruppo di sincronizzazione non riesca ad applicare una modifica per uno dei motivi seguenti:

-   Incompatibilità dello schema tra tabelle.
-   Incompatibilità dei dati tra tabelle.
-   Inserimento di una riga con valore null in una colonna che non ammette valori null.
-   Aggiornamento di una riga con un valore che viola un vincolo di chiave esterna.

Per evitare che i gruppi di sincronizzazione non vengano aggiornati:

-   Aggiornare lo schema per includere i valori contenuti nelle righe con esito negativo.
-   Aggiornare i valori delle chiavi esterne per includere i valori contenuti nelle righe con esito negativo.
-   Aggiornare i valori dei dati nella riga con esito negativo in modo che siano compatibili con lo schema o con le chiavi esterne nel database di destinazione.

### <a name="avoid-deprovisioning-issues"></a>Evitare problemi di deprovisioning

In alcuni casi, l'annullamento della registrazione di un database con un agente client può causare un errore di sincronizzazione.

#### <a name="scenario"></a>Scenario

1. Il gruppo di sincronizzazione A è stato creato tramite un'istanza di database SQL e un database di SQL Server locale, associato all'agente locale 1.
2. Lo stesso database locale è registrato con l'agente locale 2, che non è associato ad alcun gruppo di sincronizzazione.
3. Se si annulla la registrazione del database locale dall'agente locale 2, vengono rimosse le tabelle di rilevamento e metadati del gruppo di sincronizzazione A per il database locale.
4. Le operazioni del gruppo di sincronizzazione A non riescono e viene visualizzato il messaggio di errore seguente: "The current operation could not be completed because the database is not provisioned for sync or you do not have permissions to the sync configuration tables" (Non è stato possibile completare l'operazione corrente perché non è stato eseguito il provisioning del database per la sincronizzazione o non si dispone delle autorizzazioni per accedere alle tabelle di configurazione della sincronizzazione).

#### <a name="solution"></a>Soluzione

Per evitare questo scenario, non registrare un database con più agenti.

Per risolvere il problema:

1. Rimuovere il database da ogni gruppo di sincronizzazione a cui appartiene.  
2. Riaggiungere il database a ogni gruppo di sincronizzazione da cui è stato rimosso.  
3. Distribuire ogni gruppo di sincronizzazione interessato (questa operazione esegue il provisioning del database).  

### <a name="modifying-your-sync-group"></a>Modifica di un gruppo di sincronizzazione

Non provare a rimuovere un database da un gruppo di sincronizzazione e quindi a modificare il gruppo senza aver prima distribuito una delle modifiche.

Rimuovere prima un database da un gruppo di sincronizzazione. Distribuire quindi la modifica e attendere che venga completato il deprovisioning. Al termine di questa operazione, è possibile modificare il gruppo di sincronizzazione e distribuire le modifiche.

Se si prova a rimuovere un database e quindi a modificare un gruppo di sincronizzazione senza aver prima distribuito una delle modifiche, una delle due operazioni avrà esito negativo. L'interfaccia del portale può essere visualizzata in modo incoerente. In questo caso, aggiornare la pagina per ripristinare lo stato corretto.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL (anteprima), vedere:

-   [Sincronizzare i dati tra più database cloud e locali con l'anteprima di sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   [Impostare la sincronizzazione dati SQL di Azure (anteprima)](sql-database-get-started-sql-data-sync.md)
-   [Monitorare sincronizzazione dati SQL di Azure (anteprima) con OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Risolvere i problemi della sincronizzazione dati SQL di Azure (anteprima)](sql-database-troubleshoot-data-sync.md)  
-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL (anteprima):  
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL (anteprima)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
