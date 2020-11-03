---
title: Disponibilità elevata-database di Azure per MySQL
description: Questo articolo fornisce informazioni sulla disponibilità elevata in database di Azure per MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: b301946ce818559510b4e401b1f0aaf7c235d5a3
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242297"
---
# <a name="high-availability-in-azure-database-for-mysql"></a>Disponibilità elevata nel database di Azure per MySQL
Il servizio database di Azure per MySQL offre un livello di disponibilità elevato garantito con il contratto di servizio con copertura finanziaria del [99,99%](https://azure.microsoft.com/support/legal/sla/mysql) di tempo di indisponibilità. Database di Azure per MySQL offre disponibilità elevata durante gli eventi pianificati, ad esempio l'operazione di calcolo con scalabilità dell'utente avviate da, e anche quando si verificano eventi non pianificati, ad esempio hardware, software o errori di rete sottostanti. Database di Azure per MySQL è in grado di eseguire rapidamente il ripristino dalla maggior parte delle circostanze critiche, garantendo al tempo stesso un tempo di inattività durante l'uso

Database di Azure per MySQL è adatto per l'esecuzione di database mission-critical che richiedono tempi di ingrandimento. Basato sull'architettura di Azure, il servizio offre funzionalità intrinseche di disponibilità elevata, ridondanza e resilienza per attenuare i tempi di inattività del database da interruzioni pianificate e non pianificate, senza che sia necessario configurare componenti aggiuntivi. 

## <a name="components-in-azure-database-for-mysql"></a>Componenti nel database di Azure per MySQL

| **Componente** | **Descrizione**|
| ------------ | ----------- |
| <b>Server di database MySQL | Database di Azure per MySQL offre sicurezza, isolamento, misure di sicurezza delle risorse e funzionalità di riavvio rapido per i server di database. Queste funzionalità facilitano operazioni quali la scalabilità e l'operazione di ripristino del server di database dopo un'interruzione che si verifica in pochi secondi. <br/> Le modifiche dei dati nel server di database vengono in genere eseguite nel contesto di una transazione di database. Tutte le modifiche al database vengono registrate in modo sincrono sotto forma di registri write-ahead (ib_log) in archiviazione di Azure, che è collegato al server di database. Durante il processo di [Checkpoint](https://dev.mysql.com/doc/refman/5.7/en/innodb-checkpoints.html) del database, anche le pagine di dati della memoria del server di database vengono scaricate nella risorsa di archiviazione. |
| <b>Archiviazione remota | Tutti i file di dati fisici MySQL e i file di log vengono archiviati in archiviazione di Azure, progettato per archiviare tre copie di dati all'interno di un'area per garantire la ridondanza dei dati, la disponibilità e l'affidabilità. Anche il livello di archiviazione è indipendente dal server di database. Può essere scollegato da un server di database in cui si è verificato l'errore e ricollegato a un nuovo server di database entro pochi secondi. Inoltre, archiviazione di Azure monitora continuamente eventuali errori di archiviazione. Se viene rilevato un danneggiamento del blocco, viene automaticamente corretto creando un'istanza di una nuova copia di archiviazione. |
| <b>Gateway | Il gateway funge da proxy del database, instrada tutte le connessioni client al server di database. |

## <a name="planned-downtime-mitigation"></a>Mitigazione dei tempi di inattività pianificati
Database di Azure per MySQL è progettato per garantire un'elevata disponibilità durante le operazioni di inattività pianificate. 

:::image type="content" source="./media/concepts-high-availability/elastic-scaling-mysql-server.png" alt-text="visualizzazione della scalabilità elastica in MySQL di Azure":::

Di seguito sono riportati alcuni scenari di manutenzione pianificata:

| **Scenario** | **Descrizione**|
| ------------ | ----------- |
| <b>Scalabilità verticale/orizzontale di calcolo | Quando l'utente esegue un'operazione di aumento/riduzione del livello di calcolo, viene eseguito il provisioning di un nuovo server di database usando la configurazione di calcolo ridimensionata. Nel server di database precedente, i checkpoint attivi possono essere completati, le connessioni client vengono svuotate, tutte le transazioni di cui non è stato eseguito il commit vengono annullate e quindi arrestate. Lo spazio di archiviazione viene quindi scollegato dal server di database precedente e collegato al nuovo server di database. Quando l'applicazione client ritenta la connessione o tenta di creare una nuova connessione, il gateway indirizza la richiesta di connessione al nuovo server di database.|
| <b>Ridimensionamento dell'archiviazione | La scalabilità verticale dell'archiviazione è un'operazione online che non interrompe il server di database.|
| <b>Nuova distribuzione software (Azure) | Le nuove funzionalità di implementazione o correzioni di bug avvengono automaticamente nell'ambito della manutenzione pianificata del servizio. Per ulteriori informazioni, vedere la [documentazione](concepts-monitoring.md#planned-maintenance-notification)di e verificare anche il [portale](https://aka.ms/servicehealthpm).|
| <b>Aggiornamenti della versione secondaria | Database di Azure per MySQL applica automaticamente patch ai server di database alla versione secondaria determinata da Azure. Questa operazione viene eseguita come parte della manutenzione pianificata del servizio. Questo potrebbe comportare un breve periodo di inattività in termini di secondi e il server di database viene riavviato automaticamente con la nuova versione secondaria. Per ulteriori informazioni, vedere la [documentazione](concepts-monitoring.md#planned-maintenance-notification)di e verificare anche il [portale](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Mitigazione del tempo di inattività non pianificato

Il tempo di inattività non pianificato può verificarsi a causa di errori imprevisti, inclusi errori hardware sottostanti, problemi di rete e bug software. Se il server di database si interrompe in modo imprevisto, viene eseguito automaticamente il provisioning di un nuovo server di database in pochi secondi. L'archiviazione remota viene collegata automaticamente al nuovo server di database. Il motore MySQL esegue l'operazione di ripristino usando WAL e i file di database e apre il server di database per consentire ai client di connettersi. Le transazioni di cui non è stato eseguito il commit vengono perse ed è necessario eseguire un nuovo tentativo da parte dell'applicazione. Sebbene non sia possibile evitare tempi di inattività non pianificati, database di Azure per MySQL riduce il tempo di inattività eseguendo automaticamente le operazioni di ripristino a livello di server di database e di archiviazione senza richiedere l'intervento umano. 


:::image type="content" source="./media/concepts-high-availability/availability-for-mysql-server.png" alt-text="visualizzazione della scalabilità elastica in MySQL di Azure":::

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Tempo di inattività non pianificato: scenari di errore e ripristino del servizio
Ecco alcuni scenari di errore e il ripristino automatico del database di Azure per MySQL:

| **Scenario** | **Ripristino automatico** |
| ---------- | ---------- |
| <B>Errore del server di database | Se il server di database è inattivo a causa di un errore hardware sottostante, le connessioni attive vengono eliminate e tutte le transazioni in corso vengono interrotte. Viene automaticamente distribuito un nuovo server di database e l'archivio dati remoto viene collegato al nuovo server di database. Al termine del ripristino del database, i client possono connettersi al nuovo server di database tramite il gateway. <br /> <br /> Le applicazioni che usano i database MySQL devono essere compilate in modo da rilevare e ritentare le connessioni eliminate e le transazioni non riuscite.  Quando l'applicazione viene ritentata, il gateway reindirizza in modo trasparente la connessione al server di database appena creato. |
| <B>Errore di archiviazione | Le applicazioni non hanno alcun effetto sui problemi correlati all'archiviazione, ad esempio un errore del disco o un danneggiamento del blocco fisico. Poiché i dati vengono archiviati in 3 copie, la copia dei dati viene gestita dalla risorsa di archiviazione superstite. I danneggiamenti del blocco vengono corretti automaticamente. Se una copia dei dati viene persa, viene creata automaticamente una nuova copia dei dati. |

Di seguito sono riportati alcuni scenari di errore che richiedono l'intervento dell'utente per il ripristino:

| **Scenario** | **Piano di ripristino** |
| ---------- | ---------- |
| <b> Errore area | L'errore di un'area è un evento raro. Tuttavia, se è necessaria la protezione da un errore dell'area, è possibile configurare una o più repliche di lettura in altre aree per il ripristino di emergenza. Per informazioni dettagliate, vedere [questo articolo](howto-read-replicas-portal.md) sulla creazione e la gestione di repliche di lettura. In caso di errore a livello di area, è possibile alzare di livello manualmente la replica di lettura configurata nell'altra area come server di database di produzione. |
| <b> Errori logici/utente | Il ripristino da errori dell'utente, ad esempio le tabelle eliminate accidentalmente o i dati aggiornati in modo errato, comporta l'esecuzione di un [ripristino temporizzato](concepts-backup.md) (ripristino temporizzato), mediante il ripristino e il recupero dei dati fino al momento precedente all'errore.<br> <br>  Se si desidera ripristinare solo un subset di database o tabelle specifiche anziché tutti i database nel server di database, è possibile ripristinare il server di database in una nuova istanza, esportare le tabelle tramite [mysqldump](concepts-migrate-dump-restore.md)e quindi utilizzare [Restore](concepts-migrate-dump-restore.md#restore-your-mysql-database-using-command-line-or-mysql-workbench) per ripristinare le tabelle nel database. |



## <a name="summary"></a>Riepilogo

Database di Azure per MySQL offre funzionalità di riavvio rapido dei server di database, archiviazione ridondante e routing efficiente dal gateway. Per la protezione dei dati aggiuntiva, è possibile configurare i backup con replica geografica e distribuire anche una o più repliche di lettura in altre aree. Con le funzionalità intrinseche di disponibilità elevata, database di Azure per MySQL protegge i database dalla maggior parte delle interruzioni più comuni e offre un [contratto di tempo di](https://azure.microsoft.com/support/legal/sla/mysql)attività leader del settore, con supporto finanziario pari al 99,99%. Tutte queste funzionalità di disponibilità e affidabilità consentono ad Azure di essere la piattaforma ideale per l'esecuzione di applicazioni cruciali.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulle [aree di Azure](../availability-zones/az-overview.md)
- Informazioni sulla [gestione degli errori di connettività temporanei](concepts-connectivity.md)
- Informazioni sulle modalità di [replica dei dati con le repliche in lettura](howto-read-replicas-portal.md)