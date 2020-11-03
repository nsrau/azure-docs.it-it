---
title: Panoramica della continuità aziendale-Server flessibile per database di Azure per MySQL
description: Informazioni sui concetti relativi alla continuità aziendale con il server flessibile database di Azure per MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: c29e952e22aaccf31c10de8f6e16d240b4660a23
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240716"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Panoramica della continuità aziendale con database di Azure per MySQL-server flessibile (anteprima)

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Il server flessibile database di Azure per MySQL consente funzionalità di continuità aziendale che proteggono i database in caso di interruzione pianificata e non pianificata. Funzionalità come backup automatici e disponibilità elevata si riferiscono a diversi livelli di protezione degli errori con tempi di ripristino e esposizioni di perdita dei dati diversi. Quando si progetta l'applicazione per la protezione da errori, è consigliabile prendere in considerazione l'obiettivo del tempo di ripristino (RTO) e l'obiettivo del punto di ripristino (RPO) per ogni applicazione. RTO è la tolleranza ai tempi di inattività e RPO è la tolleranza alla perdita dei dati dopo un'interruzione del servizio di database.

Nella tabella seguente vengono illustrate le funzionalità offerte da server flessibili.

| **Funzionalità** | **Descrizione** | **Restrizioni** |
| ---------- | ----------- | ------------ |
| **Backup & ripristino** | Il server flessibile esegue automaticamente backup giornalieri dei file di database e consente di eseguire il backup continuo dei log delle transazioni. I backup possono essere conservati per qualsiasi periodo compreso tra 1 e 35 giorni. Sarà possibile ripristinare il server di database in qualsiasi punto nel tempo entro il periodo di conservazione dei backup. Il tempo di ripristino dipenderà dalle dimensioni dei dati da ripristinare e dal tempo necessario per eseguire il ripristino del log. Per altri dettagli, vedere [concetti: backup e ripristino](./concepts-backup-restore.md) . |I dati di backup rimangono all'interno dell'area |
| **Backup con ridondanza locale** | I backup flessibili dei server vengono archiviati in modo automatico e sicuro in una risorsa di archiviazione con ridondanza locale all'interno di un'area e nella stessa zona di disponibilità. I backup con ridondanza locale replicano i file di dati di backup del server tre volte all'interno di una singola posizione fisica nell'area primaria. L'archiviazione di backup con ridondanza locale offre almeno 99,999999999% (11 nove) di durabilità degli oggetti nel corso di un determinato anno. Per altri dettagli, vedere [concetti: backup e ripristino](./concepts-backup-restore.md) .| Applicabile in tutte le aree |
| **Disponibilità elevata di ridondanza della zona** | Il server flessibile può essere distribuito in modalità a disponibilità elevata, che distribuisce i server primari e di standby in due diverse zone di disponibilità all'interno di un'area. Questa operazione protegge da errori a livello di zona e consente inoltre di ridurre i tempi di inattività dell'applicazione durante gli eventi di tempo di inattività pianificati I dati del server primario vengono replicati in modo sincrono nella replica standby. Durante un evento di tempo di inattività, viene eseguito automaticamente il failover del server di database nella replica standby. Per altri dettagli, vedere [concetti-disponibilità elevata](./concepts-high-availability.md) . | Supportato nei livelli di calcolo per utilizzo generico e con ottimizzazione per la memoria. Disponibile solo nelle aree in cui sono disponibili più zone.|
| **Condivisioni file Premium** | I file di database vengono archiviati in una condivisione file Premium di Azure estremamente durevole e affidabile che fornisce la ridondanza dei dati con tre copie della replica archiviate in una zona di disponibilità con funzionalità automatiche di recupero dati. Per altri dettagli, vedere [condivisioni file Premium](../../storage/files/storage-how-to-create-premium-fileshare.md) . | Dati archiviati all'interno di una zona di disponibilità |

> [!IMPORTANT]
> Durante il periodo di anteprima, non è previsto alcun tempo di indisponibilità, RTO e contratto di RPO. Dettagli forniti in questa pagina solo a scopo informativo e di pianificazione.

## <a name="planned-downtime-mitigation"></a>Mitigazione dei tempi di inattività pianificati

Ecco alcuni scenari di manutenzione pianificata che comportano tempi di inattività:

| **Scenario** | **Processo**|
| :------------ | :----------- |
| **Scalabilità di calcolo (utente)**| Quando si esegue un'operazione di ridimensionamento del calcolo, viene eseguito il provisioning di un nuovo server flessibile usando la configurazione di calcolo con scalabilità. Nel server di database esistente, i checkpoint attivi possono essere completati, le connessioni client vengono svuotate, tutte le transazioni di cui non è stato eseguito il commit vengono annullate e quindi arrestate. Lo spazio di archiviazione viene quindi collegato al nuovo server e il database viene avviato, che esegue il ripristino, se necessario, prima di accettare le connessioni client. |
| **Nuova distribuzione software (Azure)** | Le nuove funzionalità di implementazione o correzioni di bug vengono eseguite automaticamente nell'ambito della manutenzione pianificata del servizio ed è possibile pianificare il momento in cui si verificano tali attività. Per ulteriori informazioni, vedere la [documentazione](https://aka.ms/servicehealthpm)di e verificare anche il [portale](https://aka.ms/servicehealthpm) |
| **Aggiornamenti della versione secondaria (Azure)** | Database di Azure per MySQL applica automaticamente patch ai server di database alla versione secondaria determinata da Azure. Questa operazione viene eseguita come parte della manutenzione pianificata del servizio. Questo potrebbe comportare un breve periodo di inattività in termini di secondi e il server di database viene riavviato automaticamente con la nuova versione secondaria. Per ulteriori informazioni, vedere la [documentazione](../concepts-monitoring.md#planned-maintenance-notification)di e verificare anche il [portale](https://aka.ms/servicehealthpm).|

Quando il server flessibile è configurato con la **disponibilità elevata con ridondanza della zona** , il server flessibile esegue prima le operazioni sul server di standby e quindi sul server primario senza failover. Per altri dettagli, vedere [concetti-disponibilità elevata](./concepts-high-availability.md) .

## <a name="unplanned-downtime-mitigation"></a>Mitigazione del tempo di inattività non pianificato

I tempi di inattività non pianificati possono verificarsi a causa di errori imprevisti, inclusi errori hardware sottostanti, problemi di rete e bug software. Se il server di database si interrompe in modo imprevisto, se configurato con disponibilità elevata [HA], viene attivata la replica standby. In caso contrario, viene eseguito automaticamente il provisioning di un nuovo server di database. Sebbene non sia possibile evitare tempi di inattività non pianificati, il server flessibile riduce il tempo di inattività eseguendo automaticamente le operazioni di ripristino a livello di server di database e di archiviazione senza richiedere l'intervento dell'uomo.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo di inattività non pianificato: scenari di errore e ripristino del servizio

Di seguito sono riportati alcuni scenari di errore non pianificati e il processo di ripristino:

| **Scenario** | **Processo di ripristino [non a disponibilità elevata]** | **Processo di ripristino [HA]** |
| :---------- | ---------- | ------- |
| **Errore del server di database** | Se il server di database è inattivo a causa di un errore hardware sottostante, le connessioni attive vengono eliminate e tutte le transazioni in corso vengono interrotte. Azure tenterà di riavviare il server di database. Se ha esito positivo, viene eseguito il recupero del database. Se il riavvio ha esito negativo, si tenterà di riavviare il server di database in un altro nodo fisico.  <br /> <br /> Il tempo di recupero (RTO) dipende da diversi fattori, tra cui l'attività al momento dell'errore, ad esempio la transazione di grandi dimensioni e la quantità di ripristino da eseguire durante il processo di avvio del server di database. <br /> <br /> Le applicazioni che usano i database MySQL devono essere compilate in modo da rilevare e ritentare le connessioni eliminate e le transazioni non riuscite.  Quando l'applicazione viene ritentata, le connessioni vengono indirizzate al server di database appena creato. | Se viene rilevato un errore del server di database, il server di database standby viene attivato, riducendo così i tempi di inattività. Per ulteriori informazioni, vedere la [pagina concetti di disponibilità elevata](concepts-high-availability.md) . Si prevede che RTO sia 60-120 s, con RPO = 0 |
| **Errore di archiviazione** | Le applicazioni non hanno alcun effetto sui problemi correlati all'archiviazione, ad esempio un errore del disco o un danneggiamento del blocco fisico. Poiché i dati vengono archiviati in 3 copie, la copia dei dati viene gestita dalla risorsa di archiviazione superstite. I danneggiamenti del blocco vengono corretti automaticamente. Se una copia dei dati viene persa, viene creata automaticamente una nuova copia dei dati. | Per gli errori non reversibili, viene eseguito il failover del server flessibile sulla replica standby per ridurre i tempi di inattività. Per ulteriori informazioni, vedere la [pagina concetti di disponibilità elevata](./concepts-high-availability.md) . |
| **Errori logici/utente** | Il ripristino da errori dell'utente, ad esempio le tabelle eliminate accidentalmente o i dati aggiornati in modo errato, comporta l'esecuzione di un [ripristino temporizzato](concepts-backup-restore.md) (ripristino temporizzato), mediante il ripristino e il recupero dei dati fino al momento precedente all'errore.<br> <br>  Se si desidera ripristinare solo un subset di database o tabelle specifiche anziché tutti i database nel server di database, è possibile ripristinare il server di database in una nuova istanza, esportare le tabelle tramite [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html), quindi utilizzare [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) per ripristinare le tabelle nel database. | Questi errori utente non sono protetti con la disponibilità elevata a causa del fatto che tutte le operazioni utente vengono replicate anche nella modalità standby. |
| **Errore zona di disponibilità** | Sebbene si verifichi un raro evento, se si vuole eseguire il ripristino da un errore a livello di zona, è possibile eseguire il ripristino temporizzato usando il backup e scegliendo punto di ripristino personalizzato per ottenere i dati più recenti. Un nuovo server flessibile verrà distribuito in un'altra zona. Il tempo impiegato per il ripristino dipende dal backup precedente e dal numero di log delle transazioni da ripristinare. | Il server flessibile esegue il failover automatico sul sito di standby. Per ulteriori informazioni, vedere la [pagina concetti di disponibilità elevata](./concepts-high-availability.md) . |
| **Errore area** | Le funzionalità di replica tra aree e ripristino geografico non sono ancora supportate nella versione di anteprima. | |

> [!IMPORTANT]
> **Non è possibile** ripristinare i server eliminati. Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Usare [blocco risorse di Azure](../../azure-resource-manager/management/lock-resources.md) per evitare l'eliminazione accidentale del server.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
- Informazioni su [backup e ripristino](./concepts-backup-restore.md)
