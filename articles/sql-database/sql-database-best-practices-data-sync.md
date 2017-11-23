---
title: Procedure consigliate per la sincronizzazione dati SQL di Azure | Microsoft Docs
description: Informazioni sulle procedure consigliate per la configurazione e l'esecuzione della sincronizzazione dati SQL di Azure
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d9529fc8acd9347b0505b1c578febc1c2219b37
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Procedure consigliate per la sincronizzazione dati SQL: anteprima 

Questo articolo descrive le procedure consigliate per la sincronizzazione dati SQL (anteprima).

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a>Sicurezza e affidabilità

### <a name="client-agent"></a>Agente client

-   Installare l'agente client usando un account con privilegi minimi con accesso ai servizi di rete.

-   È consigliabile che l'agente client sia installato in un computer distinto dal computer SQL Server locale.

-   Non registrare un database locale con più di un agente.

-   Ciò è valido anche se si procede alla sincronizzazione di tabelle diverse relative a diversi gruppi di sincronizzazione.

-   La registrazione di un database locale con più agenti client può causare problemi nell'eliminazione di uno dei gruppi di sincronizzazione.

### <a name="database-accounts-with-least-required-privilege"></a>Account di database con privilegi minimi

-   **Per la configurazione della sincronizzazione**. Create/Alter Table, Alter Database, Create Procedure, Select/Alter Schema, Create User-Defined Type.

-   **Per la sincronizzazione continua**. Select/Insert/Update/Delete su tabelle selezionate per la sincronizzazione e su tabelle di rilevamento e metadati di sincronizzazione, Execute permission su stored procedure create dal servizio, Execute permission su tipi di tabella definiti dagli utenti.

-   **Per il deprovisioning**. Alter su tabelle che fanno parte della sincronizzazione, Select/Delete su tabelle di metadati di sincronizzazione, Control su tabelle di rilevamento della sincronizzazione, stored procedure e tipi di tabelle definiti dall'utente.

**Come usare queste informazioni quando esiste una singola credenziale per un database nel gruppo di sincronizzazione?**

-   Modificare le credenziali per le diverse fasi, ad esempio *credential1* per la configurazione e *credential2* per la sincronizzazione continua.

-   Modificare l'autorizzazione delle credenziali, ovvero, modificare l'autorizzazione dopo aver configurato la sincronizzazione.

## <a name="setup"></a>Configurazione

### <a name="database-considerations-and-constraints"></a>Vincoli e considerazioni sui database

#### <a name="sql-database-instance-size"></a>Dimensione dell'istanza del database SQL

Quando si crea una nuova istanza di database SQL, impostare la dimensione massima in modo che sia sempre maggiore rispetto al database da distribuire. Se la dimensione massima non è maggiore rispetto al database distribuito, la sincronizzazione non avrà esito positivo. Sebbene non sia presente alcun aumento automatico delle dimensioni, è possibile eseguire un'istruzione ALTER DATABASE per aumentare le dimensioni del database dopo che è stato creato. Verificare di rientrare nei limiti delle dimensioni delle istanze di database SQL.

> [!IMPORTANT]
> Sincronizzazione dati SQL archivia altri metadati con ogni database. Tenere conto di questi metadati quando si calcola lo spazio necessario. La quantità di overhead aggiunto è regolata dalla larghezza delle tabelle (ad esempio tabelle strette richiedono più overhead) e dalla quantità di traffico.

### <a name="table-considerations-and-constraints"></a>Vincoli e considerazioni sulle tabelle

#### <a name="selecting-tables"></a>Selezione di tabelle

Non tutte le tabelle in un database devono far parte di un gruppo di sincronizzazione. La selezione delle tabelle da includere o da escludere da tale gruppo (o da includere in un altro gruppo) può influire su efficienza e costi. Includere pertanto in un gruppo di sincronizzazione solo le tabelle necessarie in base alle esigenze aziendali e le tabelle da cui queste sono dipendenti.

#### <a name="primary-keys"></a>Chiavi primarie

Ogni tabella in un gruppo di sincronizzazione deve disporre di una chiave primaria. Il servizio di sincronizzazione dati SQL (anteprima) non è in grado di sincronizzare le tabelle che non dispongono di una chiave primaria.

Prima di passare alla fase di produzione, sottoporre a test le prestazioni di sincronizzazione iniziali e continue.

### <a name="provisioning-destination-databases"></a>Provisioning dei database di destinazione

Sincronizzazione dati SQL (anteprima) consente il provisioning automatico di base del database.

Questa sezione descrive le limitazioni del provisioning di sincronizzazione dati SQL (anteprima).

#### <a name="auto-provisioning-limitations"></a>Limiti del provisioning automatico

Di seguito sono indicate le limitazioni del provisioning di sincronizzazione dati SQL (anteprima).

-   Nella tabella di destinazione vengono create solo le colonne selezionate.
Pertanto, se alcune colonne non appartengono al gruppo di sincronizzazione non verrà eseguito il provisioning di tali colonne nelle tabelle di destinazione.

-   Gli indici vengono creati solo per le colonne selezionate.
Se gli indici della tabella di origine presenta colonne che non appartengono al gruppo di sincronizzazione non verrà eseguito il provisioning di tali indici nelle tabelle di destinazione.

-   Non viene eseguito il provisioning degli indici nelle colonne di tipo XML.

-   Non viene eseguito il provisioning dei vincoli CHECK.

-   Non viene eseguito il provisioning dei trigger esistenti nelle tabelle di origine.

-   Non vengono create Viste e Stored procedure nel database di destinazione.

#### <a name="recommendations"></a>Raccomandazioni

-   Usare la funzionalità di provisioning automatico solo per testare il servizio.

-   Per la fase di produzione è necessario eseguire il provisioning dello schema del database.

### <a name="locate-hub"></a>Come individuare il database hub

#### <a name="enterprise-to-cloud-scenario"></a>Scenario da azienda a cloud

Per ridurre al minimo la latenza, mantenere il database hub in prossimità della maggiore concentrazione del traffico del database del gruppo di sincronizzazione.

#### <a name="cloud-to-cloud-scenario"></a>Scenario da cloud a cloud

-   Quando tutti i database in un gruppo di sincronizzazione sono ubicati in un data center, l'hub deve trovarsi nello stesso data center. Questa configurazione riduce la latenza e il costo del trasferimento dei dati tra data center.

-   Quando i database in un gruppo di sincronizzazione sono ubicati in più data center, l'hub deve trovarsi nello stesso data center in cui si trova la maggior parte dei database e il traffico del database.

#### <a name="mixed-scenarios"></a>Scenari misti

Applicare le linee guida precedenti a configurazioni più complesse di gruppi di sincronizzazione.

## <a name="sync"></a>Sincronizzazione

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a>Evitare una sincronizzazione iniziale lenta e dispendiosa

Questa sezione prende in esame la sincronizzazione iniziale di un gruppo di sincronizzazione e spiega come evitare che la sincronizzazione iniziale richieda più tempo e costi di più rispetto a quanto previsto.

#### <a name="how-initial-synchronization-works"></a>Funziona della sincronizzazione iniziale

Quando si crea un gruppo di sincronizzazione, iniziare con dati in un solo database. Se i dati sono in più database, la sincronizzazione dati SQL (anteprima) considera ogni riga come un conflitto da risolvere. La risoluzione dei conflitti rallenta la sincronizzazione iniziale, che può così richiedere giorni o mesi a seconda delle dimensioni del database.

Inoltre, se i database si trovano in data center diversi, i costi della sincronizzazione iniziale sono superiori rispetto al necessario, poiché ogni riga deve spostarsi tra diversi data center.

#### <a name="recommendation"></a>Raccomandazione

Dove possibile, iniziare con i dati in un unico database del gruppo di sincronizzazione.

### <a name="design-to-avoid-synchronization-loops"></a>Progettazione che evita i cicli di sincronizzazione

Si ha un ciclo di sincronizzazione quando all'interno di un gruppo di sincronizzazione sono presenti riferimenti circolari che causano la replica di ogni modifica in un database nei database del gruppo di sincronizzazione, in modo ripetitivo e infinito. È consigliabile evitare i cicli di sincronizzazione in quanto possono ridurre le prestazioni e aumentare in modo significativo i costi.

### <a name="handling-changes-that-fail-to-propagate"></a>Gestione delle modifiche che non vengono propagate

#### <a name="reasons-that-changes-fail-to-propagate"></a>Cause della mancata propagazione delle modifiche

Può accadere che le modifiche apportate non vengano propagate per varie cause. Tra queste:

-   Incompatibilità dello schema o del tipo di dati.

-   Tentativo di inserire valori null in colonne che non ammettono valori null.

-   Violazione di vincoli di chiavi esterne.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Cosa accade se le modifiche non vengono propagate?

-   Il gruppo di sincronizzazione mostra uno stato di avviso.

-   I dettagli sono reperibili nel visualizzatore del registro dell'interfaccia utente del portale.

-   Se il problema non viene risolto per 45 giorni, il database risulterà "Non aggiornato".

> [!NOTE]
> Queste modifiche non verranno mai propagate. L'unico modo per recuperare il database è ricreare il gruppo di sincronizzazione.

#### <a name="recommendation"></a>Raccomandazione

Monitorare regolarmente l'integrità del database e del gruppo di sincronizzazione tramite l'interfaccia del portale e del registro.


## <a name="maintenance"></a>Manutenzione 

### <a name="avoid-out-of-date-databases-and-sync-groups"></a>Evitare database e gruppi di sincronizzazione non aggiornati

È possibile che un gruppo di sincronizzazione o un database all'interno di un gruppo di sincronizzazione non sia più aggiornato. Quando lo stato di un gruppo di sincronizzazione è "Non aggiornato", il gruppo smette di funzionare. Quando lo stato del database è "Non aggiornato", può verificarsi una perdita di dati. È consigliabile evitare queste situazioni.

#### <a name="avoid-out-of-date-databases"></a>Evitare database e gruppi di sincronizzazione non aggiornati

Lo stato del database viene impostato su "Non aggiornato" quando il database è offline da almeno 45 giorni. Per evitare che lo stato del database venga impostato su "Non aggiornato", verificare che nessuno dei database resti offline per più di 45 giorni.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitare i gruppi di sincronizzazione non aggiornati

Lo stato di un gruppo di sincronizzazione viene impostato su "Non aggiornato" quando le modifiche all'interno del gruppo non vengono propagate al resto del gruppo di sincronizzazione per più di 45 giorni. Per evitare che lo stato di un gruppo di sincronizzazione venga impostato su "Non aggiornato", controllare regolarmente il registro della cronologia del gruppo di sincronizzazione. Verificare che tutti i conflitti siano risolti e che le modifiche vengano propagate a tutti i database del gruppo di sincronizzazione.

Di seguito sono indicati alcuni motivi che causano la mancata applicazione di una modifica in un gruppo di sincronizzazione:

-   Incompatibilità dello schema tra tabelle.

-   Incompatibilità dei dati tra tabelle.

-   Inserimento di una riga con un valore null in una colonna che non consente valori null.

-   Aggiornamento di una riga con un valore che viola un vincolo di chiave esterna.

Per evitare il mancato aggiornamento dei gruppi di sincronizzazione:

-   Aggiornare lo schema per consentire i valori contenuti nelle righe con esito negativo.

-   Aggiornare i valori della chiave esterna per consentire i valori contenuti nelle righe con esito negativo.

-   Aggiornare i valori dei dati nella riga con esito negativo affinché siano compatibili con lo schema o con le chiavi esterne nel database di destinazione.

### <a name="avoid-deprovisioning-issues"></a>Evitare problemi di deprovisioning

In alcuni casi l'annullamento della registrazione di un database con un agente client può impedire la riuscita delle sincronizzazioni.

#### <a name="scenario"></a>Scenario

1. Il gruppo di sincronizzazione A è stato creato con un'istanza di database SQL e con un database di SQL Server locale, associato all'agente locale 1.

2. Lo stesso database locale è registrato con l'agente locale 2, che non è associato ad alcun gruppo di sincronizzazione.

3. L'annullamento della registrazione del database locale dall'agente locale 2 rimuove le tabelle di tracking/metadati del gruppo di sincronizzazione A per il database locale.

4. Le operazioni del gruppo di sincronizzazione non riescono e viene visualizzato un messaggio di errore simile al seguente: "Impossibile completare l'operazione corrente perché non è stato eseguito il provisioning del database per la sincronizzazione o non si dispone delle autorizzazioni per le tabelle di configurazione della sincronizzazione."

#### <a name="solution"></a>Soluzione

Per evitare questa situazione è necessario evitare di registrare un database con più di un agente.

Per risolvere il problema:

1. Rimuovere il database da ogni gruppo di sincronizzazione a cui appartiene.

2. Aggiungere di nuovo il database a ogni gruppo di sincronizzazione da cui è stato appena rimosso.

3. Distribuire ogni gruppo di sincronizzazione interessato (che esegue il provisioning del database).

### <a name="modifying-your-sync-group"></a>Modifica di un gruppo di sincronizzazione

Non tentare di rimuovere un database da un gruppo di sincronizzazione e quindi modificare il gruppo senza aver prima distribuito una delle modifiche.

Innanzitutto, rimuovere il database dal gruppo di sincronizzazione. Quindi distribuire la modifica e attendere il completamento del deprovisioning. Al termine di questa operazione, è possibile modificare il gruppo di sincronizzazione e distribuire le modifiche.

Se si tenta di rimuovere un database e successivamente di modificare un gruppo di sincronizzazione senza prima distribuire una delle modifiche, una delle operazioni avrà esito negativo e l'interfaccia del portale potrebbe essere visualizzata in uno stato incoerente. In questo caso, aggiornare la pagina per ripristinare lo stato corretto.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   [Impostare la sincronizzazione dati SQL di Azure](sql-database-get-started-sql-data-sync.md)
-   [Monitorare la sincronizzazione dati SQL di Azure con OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
