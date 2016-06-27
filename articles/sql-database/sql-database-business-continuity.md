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
   ms.workload="sqldb-business-continuity"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# Panoramica: Continuità aziendale cloud e ripristino di emergenza del database con il database SQL di Azure

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

Queste funzionalità consentono di gestire gli scenari elencati in precedenza. Per indicazioni sulla scelta di una funzionalità specifica, vedere [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md).

> [AZURE.NOTE] I valori ERT e RPO sono gli obiettivi di progettazione e offrono solo indicazioni. Non fanno parte del [contratto di servizio per il database SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Ripristino temporizzato

Il [ripristino temporizzato](sql-database-point-in-time-restore.md) è concepito per riportare il database a un punto precedente nel tempo. Questa funzionalità si basa sui backup del database, sui backup incrementali e sui backup dei log delle transazioni che il servizio gestisce automaticamente per ogni database utente. È disponibile per tutti i livelli di servizio. È possibile tornare indietro di 7 giorni con il livello Basic, di 14 giorni con il livello Standard e di 35 giorni con il livello Premium. Per informazioni dettagliate su come usare la funzionalità di ripristino temporizzato, vedere [Ripristino da errore umano](sql-database-user-error-recovery.md).

### Ripristino geografico

Il [ripristino geografico](sql-database-geo-restore.md) è disponibile anche con i database di livello Basic, Standard e Premium. È l'opzione di ripristino predefinita quando anche il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Analogamente alla funzionalità di ripristino temporizzato, quella di ripristino geografico si basa sui backup di database in Archiviazione di Azure con ridondanza geografica. Esegue il ripristino della copia di backup con replica geografica ed è quindi resiliente alle interruzioni dell'archiviazione nell'area primaria. Per informazioni dettagliate su come usare la funzionalità di ripristino geografico, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

### Replica geografica attiva

La [replica geografica attiva](sql-database-geo-replication-overview.md) è disponibile per i database a tutti i livelli. È stata progettata per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico. Usando la funzionalità di replica geografica attiva, è possibile creare fino a quattro database secondari leggibili su server in diverse aree geografiche. È possibile avviare il failover su qualsiasi database secondario. Inoltre, la replica geografica attiva può essere usata per supportare gli scenari di aggiornamento o riposizionamento dell'applicazione e anche di bilanciamento dei carichi di lavoro di sola lettura. Vedere [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md) per informazioni su come [configurare la replica geografica](sql-database-geo-replication-portal.md) ed eseguire il [failover nel database secondario](sql-database-geo-replication-failover-portal.md). Per informazioni dettagliate su come implementare l'aggiornamento dell'applicazione senza tempo di inattività, vedere [Aggiornamento dell'applicazione senza tempo di inattività](sql-database-business-continuity-application-upgrade.md).

## Passaggi successivi

- [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)


## Risorse aggiuntive

- [Backup automatici del database SQL](sql-database-automated-backups.md)
- [Aggiornamento dell'applicazione senza tempo di inattività](sql-database-business-continuity-application-upgrade.md)

<!---HONumber=AcomDC_0615_2016-->