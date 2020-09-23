---
title: Panoramica della continuità aziendale con database di Azure per PostgreSQL-server flessibile
description: Informazioni sui concetti relativi alla continuità aziendale con database di Azure per PostgreSQL-server flessibile
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 264bb8c66510c90fecf12d2e4e68bd969b4fb474
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938692"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Panoramica della continuità aziendale con database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

**Continuità aziendale** nel database di Azure per PostgreSQL: il server flessibile si riferisce a meccanismi, criteri e procedure che consentono all'azienda di continuare a lavorare in caso di interruzione, in particolare per l'infrastruttura di elaborazione. Nella maggior parte dei casi, il server flessibile gestirà gli eventi di interruzione che possono verificarsi nell'ambiente cloud e mantiene in esecuzione le applicazioni e i processi aziendali. Tuttavia, esistono alcuni eventi che non possono essere gestiti automaticamente, ad esempio:

- L'utente Elimina o aggiorna accidentalmente una riga in una tabella.
- Il terremoto causa un'interruzione dell'alimentazione e una temporanea disabilita un Data Center o una zona di disponibilità.
- Patch del database necessarie per risolvere un problema di sicurezza o un bug.

Il server flessibile offre funzionalità che proteggono i dati e mitigano i tempi di inattività per i database mission-critical in caso di eventi di inattività pianificati e non pianificati. Basato sull'infrastruttura di Azure che offre già una resilienza e una disponibilità affidabili, il server flessibile dispone di funzionalità di continuità aziendale che offrono una protezione degli errori aggiuntiva, requisiti di tempo per il recupero degli indirizzi e riduzione dell'esposizione alla perdita di dati. Quando si progettano le applicazioni, è necessario prendere in considerazione la tolleranza al tempo di inattività, ovvero l'obiettivo del tempo di ripristino (RTO) e l'esposizione alla perdita di dati, ovvero l'obiettivo del punto di ripristino (RPO). Ad esempio, il database cruciale per l'azienda richiede tempi di attività molto più severi rispetto a un database di prova.  

> [!IMPORTANT]
> Il tempo di esecuzione% del contratto di servizio (SLA) non è disponibile durante l'anteprima. 

Nella tabella seguente vengono illustrate le funzionalità offerte da server flessibili.


| **Funzionalità** | **Descrizione** | **Considerazioni** |
| ---------- | ----------- | ------------ |
| **Backup automatici** | Il server flessibile esegue automaticamente backup giornalieri dei file di database e consente di eseguire il backup continuo dei log delle transazioni. I backup possono essere conservati da 7 giorni fino a 35 giorni. Sarà possibile ripristinare il server di database in qualsiasi punto nel tempo entro il periodo di conservazione dei backup. RTO dipende dalle dimensioni dei dati da ripristinare e dal tempo necessario per eseguire il ripristino del log. Il numero può essere compreso tra pochi minuti e 12 ore. Per altri dettagli, vedere [concetti-backup e ripristino](./concepts-backup-restore.md). |I dati di backup rimangono all'interno dell'area. |
| **Disponibilità elevata di ridondanza della zona** | Il server flessibile può essere distribuito con la configurazione a disponibilità elevata con ridondanza della zona in cui i server primari e di standby vengono distribuiti in due diverse zone di disponibilità all'interno di un'area. Questa configurazione a disponibilità elevata protegge i database da errori a livello di zona e consente inoltre di ridurre i tempi di inattività dell'applicazione durante gli eventi di tempo di inattività pianificati e I dati del server primario vengono replicati nella replica di standby in modalità sincrona. In caso di eventuali interferenze sul server primario, viene eseguito automaticamente il failover del server nella replica di standby. RTO nella maggior parte dei casi dovrebbe essere entro 60s-120S. È previsto che RPO sia zero (nessuna perdita di dati). Per altre informazioni, vedere [concetti-disponibilità elevata](./concepts-high-availability.md). | Supportato nei livelli di calcolo per utilizzo generico e con ottimizzazione per la memoria. Disponibile solo nelle aree in cui sono disponibili più zone. |
| **Dischi gestiti Premium** | I file di database vengono archiviati in un'archiviazione altamente durevole e affidabile gestita da Premium. In questo modo viene garantita la ridondanza dei dati con tre copie della replica archiviate in una zona di disponibilità con funzionalità automatiche di ripristino dei dati. Per ulteriori informazioni, vedere la [documentazione relativa a Managed disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview). | Dati archiviati all'interno di una zona di disponibilità. |
| **Backup con ridondanza della zona** | I backup flessibili dei server vengono archiviati in modo automatico e sicuro in un'archiviazione con ridondanza della zona all'interno di un'area. Durante un errore a livello di zona in cui viene effettuato il provisioning del server e se il server non è configurato con la ridondanza della zona, è comunque possibile ripristinare il database usando il punto di ripristino più recente in un'area diversa. Per altre informazioni, vedere [concetti-backup e ripristino](./concepts-backup-restore.md).| Applicabile solo nelle aree in cui sono disponibili più zone.|


## <a name="planned-downtime-events"></a>Eventi di tempo di inattività pianificati
Di seguito sono riportati alcuni scenari di manutenzione pianificata. Questi eventi in genere comportano pochi minuti di inattività e senza perdita di dati.

| **Scenario** | **Processo**|
| ------------------- | ----------- | 
| <b>Scalabilità di calcolo (avviata dall'utente)| Durante l'operazione di ridimensionamento del calcolo, i checkpoint attivi possono essere completati, le connessioni client vengono svuotate, vengono annullate le transazioni di cui non è stato eseguito il commit, lo spazio di archiviazione viene scollegato e quindi arrestato. Viene eseguito il provisioning di un nuovo server flessibile con lo stesso nome del server di database con la configurazione di calcolo ridimensionata. Lo spazio di archiviazione viene quindi collegato al nuovo server e il database viene avviato, che esegue il ripristino, se necessario, prima di accettare le connessioni client. |
| <b>Scalabilità verticale dell'archiviazione (avviata dall'utente) | Quando viene avviata un'operazione di scalabilità verticale, i checkpoint attivi possono essere completati, le connessioni client vengono svuotate, tutte le transazioni di cui non è stato eseguito il commit vengono annullate e quindi arrestate. Lo spazio di archiviazione viene ridotto alle dimensioni desiderate e quindi collegato al nuovo server. Se necessario, viene eseguito un ripristino prima di accettare le connessioni client. Si noti che la riduzione delle dimensioni di archiviazione non è supportata. |
| <b>Nuova distribuzione software (avviata da Azure) | Le nuove funzionalità di implementazione o correzioni di bug vengono eseguite automaticamente nell'ambito della manutenzione pianificata del servizio ed è possibile pianificare il momento in cui si verificano tali attività. Per altre informazioni, vedere il [portale](https://aka.ms/servicehealthpm). | 
| <b>Aggiornamenti della versione secondaria (avviati da Azure) | Database di Azure per PostgreSQL applica automaticamente patch ai server di database alla versione secondaria determinata da Azure. Questa operazione viene eseguita come parte della manutenzione pianificata del servizio. Il server di database viene riavviato automaticamente con la nuova versione secondaria. Per altre informazioni, vedere la [documentazione](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification). È anche possibile controllare il [portale](https://aka.ms/servicehealthpm).| 

 Quando il server flessibile è configurato con la **disponibilità elevata con ridondanza della zona**, il server flessibile esegue prima di tutto il ridimensionamento e le operazioni di manutenzione sul server di standby. Per altre informazioni, vedere [concetti-disponibilità elevata](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Mitigazione del tempo di inattività non pianificato

I tempi di inattività non pianificati possono verificarsi a causa di interruzioni impreviste, ad esempio errori hardware sottostanti, problemi di rete e bug software. Se il server di database configurato con disponibilità elevata si arresta in modo imprevisto, la replica standby viene attivata e i client possono riprendere le operazioni. Se non è configurato con la disponibilità elevata (HA), se il tentativo di riavvio non riesce, viene eseguito automaticamente il provisioning di un nuovo server di database. Sebbene non sia possibile evitare tempi di inattività non pianificati, il server flessibile contribuisce a ridurre i tempi di inattività eseguendo automaticamente le operazioni di ripristino senza richiedere l'intervento dell'uomo. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo di inattività non pianificato: scenari di errore e ripristino del servizio
Di seguito sono riportati alcuni scenari di errore non pianificati e il processo di ripristino. 

| **Scenario** | **Processo di ripristino [non a disponibilità elevata]** | **Processo di ripristino [HA]** |
| ---------- | ---------- | ------- |
| <B>Errore del server di database | Se il server di database è inattivo, Azure tenterà di riavviare il server di database. Se l'operazione ha esito negativo, il server di database verrà riavviato in un altro nodo fisico.  <br /> <br /> Il tempo di recupero (RTO) dipende da diversi fattori, tra cui l'attività al momento dell'errore, ad esempio la transazione di grandi dimensioni e il volume di ripristino da eseguire durante il processo di avvio del server di database. <br /> <br /> Le applicazioni che usano i database PostgreSQL devono essere compilate in modo da rilevare e ritentare le connessioni eliminate e le transazioni non riuscite. | Se viene rilevato un errore del server di database, viene eseguito il failover del server sul server di standby, riducendo così i tempi di inattività. Per ulteriori informazioni, vedere la [pagina concetti di disponibilità elevata](../concepts-high-availability.md). Il valore di RTO dovrebbe essere 60-120S, senza perdita di dati. |
| <B>Errore di archiviazione | Le applicazioni non hanno alcun effetto sui problemi correlati all'archiviazione, ad esempio un errore del disco o un danneggiamento del blocco fisico. Poiché i dati vengono archiviati in tre copie, la copia dei dati viene gestita dalla risorsa di archiviazione superstite. Il blocco di dati danneggiato viene automaticamente ripristinato e viene creata automaticamente una nuova copia dei dati. | Per eventuali errori rari e non reversibili, ad esempio l'intera archiviazione è inaccessibile, viene eseguito il failover del server flessibile sulla replica di standby per ridurre il tempo di inattività. Per ulteriori informazioni, vedere la [pagina concetti di disponibilità elevata](../concepts-high-availability.md). |
| <b> Errori logici/utente | Per correggere gli errori dell'utente, ad esempio le tabelle eliminate accidentalmente o i dati aggiornati in modo errato, è necessario eseguire un [ripristino temporizzato](https://docs.microsoft.com/azure/postgresql/concepts-backup) (ripristino temporizzato). Durante l'esecuzione dell'operazione di ripristino, è necessario specificare il punto di ripristino personalizzato, ovvero l'ora immediatamente prima dell'errore.<br> <br>  Se si desidera ripristinare solo un subset di database o tabelle specifiche anziché tutti i database nel server di database, è possibile ripristinare il server di database in una nuova istanza, esportare le tabelle tramite [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html), quindi utilizzare [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) per ripristinare le tabelle nel database. | Questi errori utente non sono protetti con la disponibilità elevata, in quanto tutte le modifiche vengono replicate in modo sincrono nella replica standby. È necessario eseguire un ripristino temporizzato per correggere tali errori. |
| <b> Errore zona di disponibilità | Per eseguire il ripristino da un errore a livello di zona, è possibile eseguire il ripristino temporizzato usando il backup e scegliendo un punto di ripristino personalizzato con l'ultima volta per ripristinare i dati più recenti. Un nuovo server flessibile verrà distribuito in un'altra zona senza effetti. Il tempo impiegato per il ripristino dipende dal backup precedente e dal volume dei log delle transazioni da ripristinare. | Al server di standby viene effettuato automaticamente il failover di un server flessibile entro 60-120S senza perdita di dati. Per ulteriori informazioni, vedere la [pagina concetti di disponibilità elevata](../concepts-high-availability.md). | 
| <b> Errore area | La replica di lettura tra aree e il ripristino geografico delle funzionalità di backup non sono ancora supportati nell'anteprima. | |


> [!IMPORTANT]
>  **Non è possibile**ripristinare i server eliminati   . Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Usare [blocco risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)   per evitare l'eliminazione accidentale del server.


## <a name="next-steps"></a>Passaggi successivi

-   Informazioni sulla [disponibilità elevata con ridondanza della zona](./concepts-high-availability.md)
-   Informazioni su [backup e ripristino](./concepts-backup-restore.md)
