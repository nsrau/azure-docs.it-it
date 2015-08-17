<properties 
   pageTitle="Panoramica sulla continuità aziendale del database SQL"
   description="Informazioni sulle funzionalità incorporate e sulle opzioni disponibili del database SQL di Azure che consentono di mantenere in esecuzione le applicazioni cloud cruciali e di eseguire il ripristino in caso di errori e interruzioni del servizio."
   services="sql-database"
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/14/2015"
   ms.author="elfish"/>

# Panoramica sulla continuità aziendale

Il concetto di continuità aziendale fa riferimento alla progettazione, alla distribuzione e all'esecuzione dell'applicazione in modo da renderla resiliente a eventi di arresto improvviso, pianificati o non, che comportano la perdita permanente o temporanea della capacità dell'applicazione di assolvere alla propria funzione aziendale. Gli eventi non pianificati spaziano dagli errori umani alle interruzioni permanenti o temporanee del servizio fino ai casi di emergenza locale che possono provocare la perdita di implementazioni su larga scala in una determinata area di Azure. Gli eventi pianificati includono la ridistribuzione dell'applicazione in un'area diversa, gli aggiornamenti dell'applicazione e così via. L'obiettivo della continuità aziendale è quello di permettere all'applicazione di continuare a funzionare durante questi eventi con un impatto minimo sulla funzione aziendale.

Per esaminare le soluzioni di continuità aziendale è necessario acquisire familiarità con alcuni concetti.

**Ripristino di emergenza (DR, Disaster Recovery)**: processo di ripristino della normale funzione aziendale dell'applicazione.

**Tempo di ripristino stimato (ERT, Estimated Recovery Time)**: durata stimata per il recupero della completa disponibilità del database dopo una richiesta di ripristino o failover.

**Obiettivo del tempo di ripristino (RTO, Recovery Time Objective)**: tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Il valore RTO misura la perdita massima di disponibilità durante i guasti.

**Obiettivo del punto di ripristino (RPO, Recovery Point Objective)**: intervallo massimo di tempo degli ultimi aggiornamenti che l'applicazione può perdere dal momento in cui viene completamente ripristinata dopo l'evento di arresto improvviso. Il valore RPO misura la perdita massima di dati durante i guasti.


## Scenari di continuità aziendale

La continuità aziendale consente di gestire gli scenari principali descritti di seguito.

###Progettazione per la continuità aziendale

L'applicazione che si sta sviluppando ha un'importanza cruciale per la propria azienda. Si desidera progettarla e configurarla in modo che riesca a sopravvivere a errori irreversibili del servizio nell'ambito di un'area. Si conoscono i requisiti di RPO e RTO per l'applicazione e si sceglie la configurazione che soddisfa questi requisiti.

###Ripristino da errore umano

Si dispone dei diritti amministrativi per l'accesso alla versione di produzione dell'applicazione. Come parte del processo di manutenzione regolare si è commesso un errore e si sono eliminati alcuni dati critici nell'ambiente di produzione. Si desidera ripristinare rapidamente i dati per ridurre l'impatto dell'errore.

###Ripristino dopo un'interruzione del servizio

Si esegue l'applicazione nell'ambiente di produzione e si riceve un avviso che segnala un'interruzione grave del servizio nell'area in cui l'applicazione è distribuita. Si desidera avviare il processo che consente di ripristinare l'applicazione in un'area diversa in modo da ridurre l'impatto sulle attività aziendali.

###Esercitazione per il ripristino di emergenza

Poiché il ripristino da un'interruzione del servizio ha l'effetto di riposizionare il livello dati dell'applicazione in un'altra area, per precauzione si desidera testare periodicamente il processo di ripristino e valutarne l'impatto sull'applicazione.

###Aggiornamento dell'applicazione senza tempo di inattività

Si sta rilasciando un aggiornamento importante dell'applicazione che comporta modifiche allo schema del database, distribuzione di altre stored procedure e così via. Questo processo richiederà l'arresto dell'accesso degli utenti al database. Allo stesso tempo, si desidera assicurarsi che l'aggiornamento non determini un'interruzione significativa delle operazioni aziendali.

##Funzionalità per la continuità aziendale

La tabella seguente illustra le differenze tra le funzionalità per la continuità aziendale nei vari livelli di servizio:

| Funzionalità | Livello Basic | Livello Standard |Livello Premium 
| --- |--- | --- | ---
| Ripristino temporizzato | Qualsiasi punto di ripristino entro 7 giorni | Qualsiasi punto di ripristino entro 14 giorni | Qualsiasi punto di ripristino entro 35 giorni
| Ripristino geografico | ERT < 12 ore, RPO < 1 ora | ERT < 12 ore, RPO < 1 ora | ERT < 12 ore, RPO < 1 ora
| Replica geografica standard | Non inclusa | ERT < 30 sec, RPO < 5 sec | ERT < 30 sec, RPO < 5 sec
| Replica geografica attiva | Non inclusa | Non inclusa | ERT < 30 sec, RPO < 5 sec

Queste funzionalità consentono di gestire gli scenari elencati in precedenza. Per indicazioni sulla scelta di una funzionalità specifica, vedere [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md).

###Ripristino temporizzato

La funzionalità di ripristino temporizzato è stata progettata per riportare il database a un punto precedente nel tempo. Questa funzionalità si basa sui backup del database, sui backup incrementali e sui backup dei log delle transazioni che il servizio gestisce automaticamente per ogni database utente. È disponibile per tutti i livelli di servizio. È possibile tornare indietro di 7 giorni con il livello Basic, di 14 giorni con il livello Standard e di 35 giorni con il livello Premium. Per informazioni dettagliate su come usare la funzionalità di ripristino temporizzato, vedere [Ripristino da errore umano](sql-database-user-error-recovery.md).

###Ripristino geografico

Anche la funzionalità di ripristino geografico è disponibile con i database Basic, Standard e Premium. È l'opzione di ripristino predefinita quando anche il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Analogamente alla funzionalità di ripristino temporizzato, quella di ripristino geografico si basa sui backup di database in Archiviazione di Azure con ridondanza geografica. Esegue il ripristino della copia di backup con replica geografica ed è quindi resiliente alle interruzioni dell'archiviazione nell'area primaria. Per informazioni dettagliate su come usare la funzionalità di ripristino geografico, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

###Replica geografica standard

La funzionalità di replica geografica standard è disponibile per i database Standard e Premium. È stata progettata per le applicazioni che possono usare la capacità del livello di servizio Standard ma che hanno requisiti di ripristino più elevati rispetto a quelli supportati dalla funzionalità di ripristino geografico. In caso di errore del database primario, è possibile avviare il failover a un database secondario non leggibile archiviato nell'area abbinata di ripristino di emergenza. Per informazioni dettagliate su come configurare la funzionalità di replica geografica, vedere [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md). Per informazioni dettagliate su come eseguire il failover al database secondario, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

###Replica geografica attiva

La funzionalità di replica geografica attiva è disponibile per i database Premium. È stata progettata per le applicazioni con un utilizzo notevole della scrittura e con requisiti di ripristino particolarmente elevati. Usando la funzionalità di replica geografica attiva, è possibile creare fino a quattro database secondari leggibili su server in diverse aree geografiche. È possibile avviare il failover a qualsiasi database secondario allo stesso modo della funzionalità di replica geografica standard. Inoltre, la replica geografica attiva può essere usata per supportare gli scenari di aggiornamento o riposizionamento dell'applicazione e anche di bilanciamento dei carichi di lavoro di sola lettura. Per informazioni dettagliate su come configurare la funzionalità di replica geografica, vedere [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md). Per informazioni dettagliate su come eseguire il failover al database secondario, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md). Per informazioni dettagliate su come implementare l'aggiornamento dell'applicazione senza tempo di inattività, vedere [Aggiornamento dell'applicazione senza tempo di inattività](sql-database-business-continuity-application-upgrade.md).



 

<!---HONumber=August15_HO6-->