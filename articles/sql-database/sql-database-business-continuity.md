<properties
   pageTitle="Continuità aziendale cloud - Ripristino del database - Database SQL | Microsoft Azure"
   description="Informazioni su come il database SQL di Azure supporta la continuità aziendale cloud e il ripristino del database e consente di mantenere le applicazioni cloud cruciali in esecuzione."
   keywords="continuità aziendale, continuità aziendale cloud, ripristino di emergenza del database, ripristino del database"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# Continuità aziendale del database SQL di Azure

Il database SQL di Azure include una serie di soluzioni di continuità aziendale. Il concetto di continuità aziendale fa riferimento alla progettazione, alla distribuzione e all'esecuzione di applicazioni in modo da renderle resilienti a eventi di arresto improvviso, pianificati o non, che comportano la perdita permanente o temporanea della capacità dell'applicazione di assolvere alla propria funzione aziendale. Gli eventi non pianificati spaziano dagli errori umani alle interruzioni permanenti o temporanee del servizio fino ai casi di emergenza locale che possono provocare la perdita di implementazioni su larga scala in una determinata area di Azure. Gli eventi pianificati includono la ridistribuzione delle applicazioni in un'area diversa e gli aggiornamenti. L'obiettivo della continuità aziendale è quello di permettere all'applicazione di continuare a funzionare durante questi eventi con un impatto minimo sulla funzione aziendale.

Per esaminare le soluzioni di continuità aziendale cloud del database SQL, è necessario acquisire familiarità con alcuni concetti. Si tratta di:

* **Ripristino di emergenza (DR, Disaster Recovery)**: processo di ripristino della normale funzione aziendale dell'applicazione.

* **Tempo di ripristino stimato (ERT, Estimated Recovery Time)**: durata stimata per il recupero della completa disponibilità del database dopo una richiesta di ripristino o failover.

* **Obiettivo del tempo di ripristino (RTO, Recovery Time Objective)**: tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Il valore RTO misura la perdita massima di disponibilità durante i guasti.

* **Obiettivo del punto di ripristino (RPO, Recovery Point Objective)**: intervallo massimo di tempo degli ultimi aggiornamenti che l'applicazione può perdere dal momento in cui viene completamente ripristinata dopo l'evento di arresto improvviso. Il valore RPO misura la perdita massima di dati durante i guasti.


## Scenari di continuità aziendale cloud del database SQL

Di seguito sono descritti gli scenari principali da prendere in considerazione durante la pianificazione della continuità aziendale e del ripristino dei database.

### Progettare applicazioni per la continuità aziendale

L'applicazione che si sta sviluppando ha un'importanza cruciale per la propria azienda. Si desidera progettarla e configurarla in modo che riesca a sopravvivere a errori irreversibili del servizio nell'ambito di un'area. Si conoscono i requisiti di RPO e RTO per l'applicazione e si sceglie la configurazione che soddisfa questi requisiti.

### Ripristino da errore umano

Si dispone dei diritti amministrativi per l'accesso alla versione di produzione dell'applicazione. Come parte del processo di manutenzione regolare si è commesso un errore e si sono eliminati alcuni dati critici nell'ambiente di produzione. Si desidera ripristinare rapidamente i dati per ridurre l'impatto dell'errore.

### Ripristino dopo un'interruzione del servizio

Si esegue l'applicazione nell'ambiente di produzione e si riceve un avviso che segnala un'interruzione grave del servizio nell'area in cui l'applicazione è distribuita. Si desidera avviare il processo che consente di ripristinare l'applicazione in un'area diversa in modo da ridurre l'impatto sulle attività aziendali.

### Esercitazione per il ripristino di emergenza

Poiché il ripristino da un'interruzione del servizio ha l'effetto di riposizionare il livello dati dell'applicazione in un'altra area, per precauzione si desidera testare periodicamente il processo di ripristino e valutarne l'impatto sull'applicazione.

### Aggiornamento dell'applicazione senza tempo di inattività

Si sta rilasciando un aggiornamento importante dell'applicazione che comporta modifiche allo schema del database, distribuzione di altre stored procedure e così via. Questo processo richiederà l'arresto dell'accesso degli utenti al database. Allo stesso tempo, si desidera assicurarsi che l'aggiornamento non determini un'interruzione significativa delle operazioni aziendali.

## Funzionalità di continuità aziendale del database SQL

La tabella seguente include un elenco delle funzionalità di continuità aziendale del database SQL e descrive le differenze dei vari [livelli di servizio](sql-database-service-tiers.md):

| Funzionalità | Livello Basic | Livello Standard |Livello Premium
| --- |--- | --- | ---
| Ripristino temporizzato | Qualsiasi punto di ripristino entro 7 giorni | Qualsiasi punto di ripristino entro 14 giorni | Qualsiasi punto di ripristino entro 35 giorni
| Ripristino geografico | ERT < 12 ore, RPO < 1 ora | ERT < 12 ore, RPO < 1 ora | ERT < 12 ore, RPO < 1 ora
| Replica geografica attiva | ERT < 30 sec, RPO < 5 sec | ERT < 30 sec, RPO < 5 sec | ERT < 30 sec, RPO < 5 sec

Queste funzionalità consentono di gestire gli scenari elencati in precedenza.

> [AZURE.NOTE] I valori ERT e RPO sono gli obiettivi di progettazione e offrono solo indicazioni. Non fanno parte del [contratto di servizio per il database SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Ripristino temporizzato

Il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) è concepito per riportare il database a un punto precedente nel tempo. Questa funzionalità si basa sui backup del database, sui backup incrementali e sui backup dei log delle transazioni che il servizio gestisce automaticamente per ogni database utente. È disponibile per tutti i livelli di servizio. È possibile tornare indietro di 7 giorni con il livello Basic, di 14 giorni con il livello Standard e di 35 giorni con il livello Premium.

### Ripristino geografico

Il [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore) è disponibile anche con i database di livello Basic, Standard e Premium. È l'opzione di ripristino predefinita quando anche il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Analogamente alla funzionalità di ripristino temporizzato, quella di ripristino geografico si basa sui backup di database in Archiviazione di Azure con ridondanza geografica. Esegue il ripristino della copia di backup con replica geografica ed è quindi resiliente alle interruzioni dell'archiviazione nell'area primaria.

### Replica geografica attiva

La [replica geografica attiva](sql-database-geo-replication-overview.md) è disponibile per i database a tutti i livelli. È stata progettata per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico. Usando la funzionalità di replica geografica attiva, è possibile creare fino a quattro database secondari leggibili su server in diverse aree geografiche. È possibile avviare il failover su qualsiasi database secondario. Inoltre, la replica geografica attiva può essere usata per supportare gli [scenari di aggiornamento o riposizionamento dell'applicazione](sql-database-manage-application-rolling-upgrade.md) e anche di bilanciamento dei carichi di lavoro di sola lettura.

## Scelta delle funzionalità di continuità aziendale

Quando si progetta un'applicazione per la continuità aziendale è necessario fornire una risposta alle domande seguenti:

1. Quale funzionalità per la continuità aziendale è adatta a proteggere l'applicazione da eventuali interruzioni del servizio?
2. Quale livello di ridondanza e topologia di replica è opportuno usare?

### Quando usare il ripristino geografico

La funzionalità di [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore) fornisce un'opzione predefinita di ripristino quando un database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Per impostazione predefinita, il database SQL offre una protezione di base incorporata per ciascun database. Avviene mediante l'esecuzione e l'archiviazione dei [backup del database](sql-database-automated-backups.md) nella risorsa di archiviazione con ridondanza geografica di Azure (GRS). Se si sceglie questo metodo, non è necessaria alcuna configurazione speciale o allocazione di risorse aggiuntive. È possibile ripristinare il database in qualsiasi area tramite i backup con ridondanza geografica automatizzati.

È consigliabile usare la protezione incorporata se l'applicazione soddisfa i criteri seguenti:

1. Non è considerata cruciale. Non ha un contratto di servizio vincolante e, di conseguenza, un tempo di inattività di 24 o più ore non comporta alcuna responsabilità finanziaria.
2. La frequenza di modifica dei dati è bassa (ad esempio, è espressa in termini di transazioni all'ora). Un valore RPO di 1 ora non genererà una perdita di dati significativa.
3. L'applicazione è suscettibile alla variazione del costo e non può giustificare il costo aggiuntivo della replica geografica.

> [AZURE.NOTE] La funzionalità di ripristino geografico non esegue un'allocazione preliminare della capacità di calcolo in un'area specifica per ripristinare i database attivi dal backup durante l'interruzione del servizio. Il servizio gestirà il carico di lavoro associato alle richieste di ripristino geografico in modo da ridurre al minimo l'impatto sui database esistenti in tale area e le loro richieste di capacità saranno prioritarie. Pertanto, il tempo di ripristino del database dipenderà da quanti altri database verranno ripristinati contemporaneamente nella stessa area, dalle dimensioni del database, dal numero di log delle transazioni, dalla larghezza di banda della rete e così via.

### Quando usare la replica geografica attiva

La [replica geografica attiva](sql-database-geo-replication-overview.md) consente di creare e gestire database leggibili (secondari) in un'area diversa da quella del database primario, applicando un meccanismo di replica asincrona per mantenerli aggiornati. Questa funzionalità garantisce che il database abbia le risorse di calcolo e i dati necessari per supportare il carico di lavoro dell'applicazione dopo il ripristino.

È consigliabile utilizzare la replica geografica attiva se l’applicazione soddisfa i criteri seguenti:

1. È considerata cruciale. Ha un contratto di servizio vincolante con valori RPO e RTO elevati. La perdita di dati e disponibilità comporta una responsabilità finanziaria.
2. La frequenza di modifica dei dati è alta (ad esempio, è espressa in termini di transazioni al minuto o al secondo). Un valore RPO di 1 ora associato alla protezione predefinita avrà come risultato una perdita di dati inaccettabile.
3. Il costo legato all'uso della replica geografica è notevolmente inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

## Progettare soluzioni cloud per il ripristino di emergenza. 

Quando si progetta l'applicazione per la continuità aziendale, è necessario considerare alcune opzioni di configurazione. La scelta dipenderà dalla topologia di distribuzione dell'applicazione e dalle parti dell'applicazione più vulnerabili a un'interruzione del servizio. Per informazioni aggiuntive, vedere [Progettazione di soluzioni cloud per il ripristino di emergenza mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

Per informazioni sulle strategie di ripristino dettagliate da usare con un pool elastico, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Passaggi successivi

- Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere l'articolo relativo agli [scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->