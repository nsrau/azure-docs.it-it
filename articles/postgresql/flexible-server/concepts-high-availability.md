---
title: Panoramica della disponibilità elevata con ridondanza della zona con database di Azure per PostgreSQL-server flessibile (anteprima)
description: Informazioni sui concetti relativi alla disponibilità elevata con ridondanza della zona con database di Azure per PostgreSQL-server flessibile
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b23c95ef0005c8246feb8dc32e4a07a0ae19b72f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359545"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Concetti relativi alla disponibilità elevata in database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL: il server flessibile offre una configurazione a disponibilità elevata con funzionalità di failover automatico tramite la distribuzione del server con **ridondanza della zona** . Quando viene distribuito in una configurazione con ridondanza della zona, il server flessibile esegue automaticamente il provisioning e la gestione di una replica standby in una zona di disponibilità diversa. Usando la replica di streaming PostgreSQL, i dati vengono replicati nel server di replica di standby in modalità **sincrona** . 

La configurazione con ridondanza della zona consente la funzionalità di failover automatico senza perdita di dati durante gli eventi pianificati, ad esempio l'operazione di calcolo con scalabilità avviata dall'utente e anche durante gli eventi non pianificati, ad esempio errori hardware e software sottostanti, errori di rete e guasti delle zone di disponibilità 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="disponibilità elevata con ridondanza della zona"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Architettura a disponibilità elevata con ridondanza della zona

È possibile scegliere l'area e la zona di disponibilità per la distribuzione del server di database primario. Viene eseguito il provisioning di un server di replica standby in una zona di disponibilità diversa con la stessa configurazione del server primario, inclusi il livello di calcolo, le dimensioni di calcolo, le dimensioni della risorsa archiviazione e la configurazione di rete. I log delle transazioni vengono replicati in modalità sincrona nella replica di standby usando la replica di flusso PostgreSQL. I backup automatici vengono eseguiti periodicamente dal server di database primario, mentre i log delle transazioni vengono archiviati continuamente nell'archivio di backup dalla replica di standby. 

L'integrità della configurazione della disponibilità elevata viene monitorata e segnalata continuamente nel portale. Gli Stati di disponibilità elevata con ridondanza della zona sono elencati di seguito:

| **Status** | **Descrizione** |
| ------- | ------ |
| <b> Inizializzazione | Procedura di creazione di un nuovo server di standby |
| <b> Replica dei dati | Dopo la creazione della modalità standby, viene rilevata la replica primaria. |
| <b> Integro | La replica si trova in uno stato stabile e integro. |
| <b> Failover in corso | È in corso il failover del server di database in modalità standby. |
| <b> Rimozione standby | Nel processo di eliminazione del server di standby. | 
| <b> Non abilitato | La disponibilità elevata con ridondanza della zona non è abilitata.  |

## <a name="steady-state-operations"></a>Operazioni di stato stazionario

Le applicazioni client PostgreSQL sono connesse al server primario utilizzando il nome del server di database. Le letture dell'applicazione vengono gestite direttamente dal server primario, mentre i commit e le scritture vengono confermati nell'applicazione solo dopo che i dati sono stati salvati nel server primario e nella replica di standby. A causa di questo requisito di round trip aggiuntivo, le applicazioni possono prevedere una latenza elevata per scritture e commit. È possibile monitorare l'integrità della disponibilità elevata nel portale.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="disponibilità elevata con ridondanza della zona-stato stabile"::: 

1. I client si connettono al server flessibile ed eseguono operazioni di scrittura.
2. Le modifiche vengono replicate nel sito di standby.
3. Primario che riceve il riconoscimento.
4. Scritture/commit sono riconosciute.

## <a name="failover-process---planned-downtimes"></a>Processo di failover-tempi di inattività pianificati

Gli eventi di tempo di inattività pianificato includono aggiornamenti software periodici e aggiornamenti della versione secondaria di Azure. Una volta configurata la disponibilità elevata, queste operazioni vengono prima applicate alla replica di standby mentre le applicazioni continuano ad accedere al server primario. Una volta aggiornata la replica di standby, le connessioni al server primario vengono svuotate e viene attivato un failover che attiva la replica di standby come primaria con lo stesso nome del server di database. Le applicazioni client dovranno riconnettersi con lo stesso nome del server di database al nuovo server primario e possono riprendere le operazioni. Un nuovo server di standby verrà stabilito nella stessa zona del database primario precedente. 

Per le altre operazioni avviate dall'utente, ad esempio l'archiviazione con scalabilità o di calcolo, le modifiche vengono applicate prima in standby, seguite dal database primario. Attualmente, le connessioni non vengono sottoposte a failover nello stato di standby e pertanto comporta un tempo di inattività durante l'esecuzione dell'operazione sul server primario.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Riduzione dei tempi di inattività pianificati con la finestra manutenzione

 È possibile pianificare le attività di manutenzione avviate da Azure scegliendo un intervallo di 30 minuti in un giorno di preferenza, in cui le attività nei database dovrebbero essere basse. Le attività di manutenzione di Azure, ad esempio l'applicazione di patch o gli aggiornamenti della versione secondaria, verranno eseguite durante tale intervallo.  Per i server flessibili configurati con la disponibilità elevata, queste attività di manutenzione vengono eseguite prima nella replica di standby e quindi attivate. Le applicazioni riconnettersi quindi al nuovo server primario e riprendere le operazioni mentre viene effettuato il provisioning di un nuovo standby.

## <a name="failover-process---unplanned-downtimes"></a>Processo di failover-tempi di inattività non pianificati

Interruzioni non pianificate includono bug software o errori dei componenti dell'infrastruttura che incidono sulla disponibilità del database. Se il sistema di monitoraggio rileva la mancata disponibilità del server, la replica per la replica standby viene interrotta e la replica standby viene attivata come server di database primario. I client possono riconnettersi al server di database usando la stessa stringa di connessione e riprendere le operazioni. Si prevede che il tempo di failover complessivo imprenda 60-120S. Tuttavia, a seconda dell'attività nel server di database primario al momento del failover, ad esempio transazioni di grandi dimensioni e tempi di ripristino, il failover potrebbe richiedere più tempo.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="disponibilità elevata con ridondanza della zona-failover"::: 

1. Il server di database primario non è attivo e i client perdono la connettività del database. 
2. Il server di standby viene attivato per diventare il nuovo server primario. Il client si connette al nuovo server primario utilizzando la stessa stringa di connessione. L'applicazione client nella stessa zona del server di database primario riduce la latenza e migliora le prestazioni.
3. Il server di standby viene stabilito nella stessa zona del server primario precedente e viene avviata la replica di streaming. 
4. Una volta stabilita la replica dello stato stabile, l'applicazione client esegue il commit e le scritture vengono riconosciute dopo che i dati sono stati salvati in entrambi i siti.

## <a name="point-in-time-restore"></a>Ripristino temporizzato 

Server flessibili configurati con disponibilità elevata, replicare i dati in tempo reale nel server di standby per mantenendoli aggiornati. Gli eventuali errori utente sul server primario, ad esempio l'eliminazione accidentale di una tabella o aggiornamenti di dati non corretti, vengono replicati fedelmente nella replica di standby. Pertanto, non è possibile utilizzare la modalità standby per eseguire il ripristino da errori logici. Per risolvere questi errori, è necessario eseguire il ripristino temporizzato dai backup.  Grazie alla funzionalità di ripristino temporizzato del server flessibile, è possibile eseguire il ripristino fino al momento in cui si è verificato l'errore. Per i database configurati con la disponibilità elevata, un nuovo server di database verrà ripristinato come server con una singola zona flessibile con un nome fornito dall'utente. È quindi possibile esportare l'oggetto dal server di database e importarlo nel server di database di produzione. Analogamente, se si desidera clonare il server di database a scopo di test e sviluppo oppure si desidera eseguire il ripristino per altri scopi, è possibile eseguire ripristini temporizzati.

## <a name="zone-redundant-high-availability---features"></a>Disponibilità elevata con ridondanza della zona: funzionalità

-   La replica standby verrà distribuita in una configurazione esatta della macchina virtuale identica a quella del server primario, tra cui Vcore, archiviazione, impostazioni di rete (VNET, firewall) e così via.

-   Possibilità di aggiungere la disponibilità elevata per un server di database esistente.

-   Possibilità di rimuovere la replica standby disabilitando la disponibilità elevata.

-   Possibilità di scegliere la zona di disponibilità per il server di database primario.

-   Possibilità di arrestare, avviare e riavviare i server di database primario e di standby.

-   I backup automatici vengono eseguiti dal server di database primario e archiviati in una risorsa di archiviazione con ridondanza della zona.

-   I client si connettono sempre al server di database primario.

-   Possibilità di riavviare il server per rilevare eventuali modifiche ai parametri del server statico.
  
-   Le attività di manutenzione periodica, ad esempio gli aggiornamenti della versione secondaria, vengono eseguite prima in standby e viene eseguito il failover del servizio per ridurre i tempi di inattività.  

## <a name="zone-redundant-high-availability---limitations"></a>Disponibilità elevata con ridondanza della zona-limitazioni

-   La disponibilità elevata non è supportata con il livello di calcolo in sequenza.
-   La disponibilità elevata è supportata solo nelle aree in cui sono disponibili più zone.
-   A causa della replica sincrona in un'altra zona di disponibilità, le applicazioni possono riscontrare una latenza di scrittura e commit elevata.

-   Non è possibile usare la replica standby per le query di sola lettura.

-   A seconda dell'attività del server primario al momento del failover, il completamento del failover potrebbe richiedere fino a due minuti o più.

-   Il riavvio del server di database primario per la selezione delle modifiche dei parametri statici consente inoltre di riavviare la replica standby.

-   La configurazione di repliche di lettura aggiuntive non è supportata.

-   La configurazione delle attività di gestione avviate dal cliente non può essere pianificata durante la finestra di manutenzione gestita.

-   Gli eventi pianificati, ad esempio il calcolo e l'archiviazione della scalabilità, si verificano prima in standby e quindi nel server primario. Il servizio non viene sottoposto a failover. 

-  Se la decodifica logica o la replica logica è configurata con un server flessibile configurato per la disponibilità elevata, in caso di failover al server di standby, gli slot di replica logica non vengono copiati nel server di standby.  

## <a name="next-steps"></a>Passaggi successivi

-   Scopri di più sulla [continuità aziendale](./concepts-business-continuity.md)
-   Informazioni su come [gestire la disponibilità elevata](./how-to-manage-high-availability-portal.md)
-   Informazioni su [backup e ripristino](./concepts-backup-restore.md)