---
title: Notifica di manutenzione pianificata-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la funzionalità di notifica di manutenzione pianificata nel database di Azure per PostgreSQL-server singolo
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 0691411f57944f8203120ec2a6ed19013135458c
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920254"
---
# <a name="planned-maintenance-notification-in-azure-database-for-postgresql---single-server"></a>Notifica di manutenzione pianificata nel Database di Azure per PostgreSQL - Server singolo

Informazioni su come prepararsi per gli eventi di manutenzione pianificata nel database di Azure per PostgreSQL.

## <a name="what-is-a-planned-maintenance"></a>Che cos'è una manutenzione pianificata?

Il servizio database di Azure per PostgreSQL esegue l'applicazione automatica delle patch dell'hardware, del sistema operativo e del motore di database sottostanti. La patch include le nuove funzionalità del servizio, la sicurezza e gli aggiornamenti software. Per il motore PostgreSQL gli aggiornamenti della versione secondaria sono automatici e sono inclusi come parte del ciclo di applicazione delle patch. Non è richiesto alcun intervento da parte dell'utente o alcuna impostazione di configurazione per l'applicazione di patch. La patch viene testata estensivamente e implementata con procedure di distribuzione sicure.

Una manutenzione pianificata è una finestra di manutenzione quando questi aggiornamenti del servizio vengono distribuiti nei server in una determinata area di Azure. Durante la manutenzione pianificata, viene creato un evento di notifica per informare i clienti riguardo a quando l'aggiornamento del servizio viene distribuito nell'area di Azure che ospita i server. La durata minima tra due operazioni di manutenzione pianificata è di 30 giorni. Si riceve una notifica della finestra di manutenzione successiva 72 ore prima.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Manutenzione pianificata-durata e conseguenze per i clienti

Una manutenzione pianificata per una determinata area di Azure è in genere prevista per l'esecuzione di 15 ore. La finestra include anche il tempo del buffer per eseguire un piano di rollback, se necessario. Durante la manutenzione pianificata, possono essere presenti riavvii o failover del server di database, che potrebbero causare una breve indisponibilità dei server di database per gli utenti finali. I server del database di Azure per PostgreSQL sono in esecuzione nei contenitori, quindi i riavvii del server di database sono in genere rapidi e si prevede di essere completati 60-120 in genere in L'intero evento di manutenzione pianificata che include tutti i riavvii del server viene monitorato attentamente dal team di progettazione. Il tempo di failover del server dipende dal tempo di recupero del database. in questo modo, il database può essere connesso più a lungo se si dispone di un'attività transazionale intensa sul server al momento del failover. Per evitare tempi di riavvio più lunghi, è consigliabile evitare transazioni con esecuzione prolungata (caricamenti bulk) durante gli eventi di manutenzione pianificata.

In breve, mentre l'evento di manutenzione pianificata viene eseguito per 15 ore, l'effetto del singolo server dura generalmente 60 secondi, a seconda dell'attività transazionale sul server. Una notifica viene inviata 72 ore di calendario prima dell'avvio della manutenzione pianificata e ne è in corso un'altra durante la manutenzione per una determinata area.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Come è possibile ricevere una notifica per la manutenzione pianificata?

È possibile utilizzare la funzionalità notifiche di manutenzione pianificata per ricevere avvisi per un evento di manutenzione pianificata imminente. Si riceverà la notifica sulle ore di calendario di manutenzione imminente di 72 prima dell'evento e di un'altra, mentre la manutenzione è in corso per una determinata area.

### <a name="planned-maintenance-notification"></a>Notifica di manutenzione pianificata

> [!IMPORTANT]
> Le notifiche di manutenzione pianificata sono attualmente disponibili in anteprima in tutte le aree **ad eccezione** degli Stati Uniti centro-occidentali

Le **notifiche di manutenzione pianificata** consentono di ricevere avvisi per il prossimo evento di manutenzione pianificata nel database di Azure per PostgreSQL. Queste notifiche sono integrate con la manutenzione pianificata [dell'integrità dei servizi](../service-health/overview.md) e consentono di visualizzare tutte le operazioni di manutenzione pianificate per le sottoscrizioni in un'unica posizione. Consente inoltre di ridimensionare la notifica ai destinatari giusti per gruppi di risorse diversi, in quanto è possibile che si disponga di contatti diversi responsabili di risorse diverse. Si riceverà la notifica sulle ore di calendario di manutenzione imminenti 72 prima dell'evento.

Ogni tentativo di fornire **notifiche di manutenzione pianificate** 72 ore di preavviso per tutti gli eventi verrà effettuato. Tuttavia, in caso di patch di sicurezza o critiche, le notifiche potrebbero essere inviate più vicino all'evento o essere omesse.

È possibile controllare la notifica di manutenzione pianificata in portale di Azure o configurare gli avvisi per la ricezione della notifica. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Controllare la notifica di manutenzione pianificata da portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)selezionare integrità del **servizio**.
2. Selezionare la scheda **manutenzione pianificata**
3. Selezionare **sottoscrizione**, * * area e **servizio** per cui si desidera controllare la notifica di manutenzione pianificata. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Per ricevere la notifica di manutenzione pianificata

1. Nel [portale](https://portal.azure.com) selezionare **Integrità del servizio**.
2. Nella sezione **Avvisi** selezionare **Avvisi integrità**.
3. Selezionare **+ Aggiungi avviso di integrità del servizio** e compilare i campi.
4. Compilare i campi obbligatori. 
5. Scegliere il **tipo di evento**, selezionare **manutenzione pianificata** o **selezionare tutto**
6. Nei **gruppi di azioni** definire come si vuole ricevere l'avviso (ricevere un messaggio di posta elettronica, attivare un'app per la logica e così via).  
7. Assicurarsi che Abilita regola al momento della creazione sia impostata su Sì.
8. Selezionare **Crea regola di avviso** per completare l'avviso

Per i passaggi dettagliati su come creare gli **avvisi di integrità del servizio**, vedere [creare avvisi del log attività nelle notifiche del servizio](../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>È possibile annullare o posticipare la manutenzione pianificata?

La manutenzione è necessaria per mantenere il server protetto, stabile e aggiornato. L'evento di manutenzione pianificata non può essere annullato o posticipato. Quando la notifica viene inviata a una determinata area di Azure, non è possibile apportare modifiche alla pianificazione dell'applicazione di patch per i singoli server di tale area. La patch viene implementata per l'intera area in una sola volta. Database di Azure per PostgreSQL: il servizio server singolo è progettato per un'applicazione cloud nativa che non richiede un controllo granulare o una personalizzazione del servizio. Se si vuole avere la possibilità di pianificare la manutenzione per i server, è consigliabile prendere in considerazione [server flessibili](./flexible-server/overview.md).

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Tutte le aree di Azure sono state applicate allo stesso tempo?

No, viene applicata una patch per tutte le aree di Azure durante le tempistiche della finestra per la distribuzione. La finestra di distribuzione saggia in genere si estende dalle 17:00 alle 8.00 all'ora locale del giorno successivo, in una determinata area di Azure. Le aree di Azure con associazione geografica vengono applicate in diversi giorni. Per la disponibilità elevata e la continuità aziendale dei server di database, è consigliabile sfruttare le [repliche di lettura tra più aree](./concepts-read-replicas.md#cross-region-replication) .

## <a name="retry-logic"></a>Logica di retry

Un errore temporaneo è un errore che si risolve in modo autonomo. Durante la manutenzione possono verificarsi [errori temporanei](./concepts-connectivity.md#transient-errors) . La maggior parte di questi eventi viene attenuata automaticamente dal sistema in meno di 60 secondi. Gli errori temporanei devono essere gestiti tramite la [logica di ripetizione dei tentativi](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Passaggi successivi

- Per eventuali domande o suggerimenti sull'uso di database di Azure per PostgreSQL, inviare un messaggio di posta elettronica al team di database di Azure per PostgreSQL all'indirizzo AskAzureDBforPostgreSQL@service.microsoft.com
- Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-on-metric.md).
- [Risolvere i problemi di connessione al database di Azure per PostgreSQL-server singolo](howto-troubleshoot-common-connection-issues.md)
- [Gestire gli errori temporanei e connettersi in modo efficiente al database di Azure per PostgreSQL-server singolo](concepts-connectivity.md)
