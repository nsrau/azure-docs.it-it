---
title: "Disponibilità elevata - servizio di Database SQL di Azure | Documenti Microsoft"
description: "Informazioni sulle funzionalità e funzionalità di disponibilità elevata del servizio di Database SQL di Azure"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Database SQL a disponibilità elevata e Azure
Dall'inizio dell'offerta PaaS di Database SQL di Azure, Microsoft ha apportato la promessa ai clienti che il servizio integrata la disponibilità elevata in e non sono è necessario operare, aggiungere una logica speciale per i clienti o prendere decisioni intorno a disponibilità elevata. Microsoft offre ai clienti un contratto di servizio e mantenere il controllo completo sulla configurazione di sistema a disponibilità elevata e l'operazione. Il contratto a disponibilità elevata si applica a un database SQL in un'area e non fornisce protezione in caso di errore totale area a causa di fattori di fuori del ragionevole controllo (ad esempio, calamità naturali, guerra, atti di terrorismo, sommosse, azione per enti pubblici o una rete o Errore dispositivo esterno per il data center, tra cui presso il cliente o tra sito del cliente e il data center).

Per semplificare lo spazio dei problemi di disponibilità elevata, Microsoft utilizza i presupposti seguenti:
1.  Errori hardware e software sono inevitabili
2.  Lo staff operativo commettono che portano a errori
3.  Operazioni di manutenzione pianificate causano interruzioni 

In tali eventi singoli sono poco frequenti, a livello di cloud, che ogni settimana, se non tutti i giorni. 

## <a name="fault-tolerant-sql-databases"></a>Database SQL a tolleranza di errore
I clienti sono più interessati alla resilienza dei propri database e sono meno interessati la resilienza del servizio Database SQL nel suo complesso. 99,99% tempo di attività per un servizio non è significativo se "database" fa parte di % 0,01 dei database che sono inattivi. Ogni database deve essere a tolleranza di errore e attenuazione errore mai deve comportare la perdita di un commit della transazione. 

Per i dati, Database SQL utilizza l'archiviazione locale (LS) in base diretti collegati dischi/dischi rigidi virtuali e l'archiviazione remota (RS) basato sui blob di pagine di archiviazione Premium di Azure. 
- Archiviazione locale viene utilizzato il database Premium e il pool, sono progettati per le applicazioni OLTP con requisiti elevati di IOPS. 
- Archiviazione remota viene usata per i livelli di servizio Basic e Standard, progettati per i database di piccoli, freddi o grandi dimensioni che richiedono l'archiviazione e calcolano power aumentare in modo indipendente. Usano un blob di pagine solo per i file di database e di log e i meccanismi di replica e failover di archiviazione predefinito.

In entrambi i casi, la replica, rilevamento degli errori e i meccanismi di failover del Database SQL sono completamente automatizzati e operano senza intervento umano. Questa architettura è progettata per garantire che il commit dei dati non vengano mai persi e durabilità dei dati che ha la precedenza su tutti else.

Vantaggi principali:
- I clienti di ottenere i massimi vantaggi dei database replicati senza dover configurare o gestire complicata hardware, software, il sistema operativo o ambienti di virtualizzazione.
- Proprietà ACID complete dei database relazionali vengono gestite dal sistema.
- I failover sono completamente automatizzati senza perdita di dati eseguito il commit.
- Routing di connessioni alla replica primaria in modo dinamico è gestito dal servizio con alcuna logica di applicazione richiesto.
- L'elevato livello di ridondanza automatica viene fornito senza alcun costo aggiuntivo.

> [!NOTE]
> L'architettura a disponibilità elevata descritto è soggetto a modifiche senza preavviso. 

## <a name="data-redundancy"></a>Ridondanza dei dati

La soluzione a disponibilità elevata nel Database SQL si basa sul [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) tecnologia di SQL Server e la utilizza per i database LS sia RS con differenze minime. Nella configurazione di LS, AlwaysON viene utilizzato per la persistenza in Reporting Services viene utilizzato per la disponibilità (RTO basso). 

## <a name="local-storage"></a>Archiviazione locale

In caso di LS, ogni database viene portato online dal servizio di gestione (MS) all'interno dell'anello di controllo. Una replica primaria e almeno due repliche secondarie (set di quorum) si trovano all'interno di un anello di tenant che si estende su tre sottosistemi fisici indipendenti all'interno dello stesso Data Center. Tutte le letture e scritture vengono inviate dal gateway (gateway) per la replica primaria e le operazioni di scrittura vengono replicate in modo asincrono alle repliche secondarie. Database SQL viene utilizzato uno schema di commit basato su quorum in cui i dati vengono scritti per il database primario e almeno una replica secondaria prima del commit della transazione.

Il [Service Fabric](/azure/service-fabric/service-fabric-overview.md) sistema failover Ricompila le repliche come nodi di esito negativo e mantiene l'appartenenza al set di quorum come nodi indicano e aggiungere il sistema automaticamente. Manutenzione pianificata è coordinata con attenzione per evitare che il set di quorum risulti non disponibile, di sotto di un numero di repliche minime (in genere 2). Questo modello funziona bene per i database Premium, ma richiede ridondanza dei componenti di elaborazione e di archiviazione e comporta un costo superiore.

## <a name="remote-storage"></a>Archiviazione remota

Per le configurazioni di archiviazione remoti (livelli Basic e Standard), esattamente una copia del database viene mantenuta nell'archiviazione blob remoti, sfruttando le funzionalità di sistemi di archiviazione per la durabilità, ridondanza e il rilevamento di bit rot. 

L'architettura a disponibilità elevata è illustrato il diagramma seguente:
 
![architettura a disponibilità elevata](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Recupero e il rilevamento degli errori 
Un sistema di rilevamento di errore estremamente affidabili in grado di rilevare errori in modo affidabile, è necessario un sistema distribuito su larga scala, rapidamente e più vicino possibile al cliente. Per il Database SQL, questo sistema è basato su Azure Service Fabric. 

Con la replica primaria, è immediatamente evidente se e quando la replica primaria non è riuscita e lavoro non può continuare perché tutte le letture e scritture avvengono nella replica primaria prima di tutto. Questo processo di innalzamento di livello una replica secondaria per lo stato del database primario è l'obiettivo del tempo di ripristino (RTO) = 30 secondi e l'obiettivo del punto di ripristino (RPO) = 0. Per ridurre l'impatto del 30 sec RTO, la procedura consigliata consiste nel provare a riconnettersi più volte con un tempo di attesa inferiore per la connessione non riusciti.

Quando una replica secondaria non riesce, il database è fino a un quorum-numero minimo, con nessuna riserve. Infrastruttura del servizio avvia il processo di riconfigurazione simile a quello che segue l'errore della replica primaria, pertanto, dopo una breve attesa per determinare se l'errore è permanente, un'altra replica secondaria viene creata. In caso di stato out-of-service temporaneo, ad esempio un errore del sistema operativo o un aggiornamento, una nuova replica non viene compilata immediatamente per consentire il nodo in errore invece riavviare. 

Per le configurazioni di archiviazione remota, Database SQL utilizza la funzionalità AlwaysON per database failover durante gli aggiornamenti. A tale scopo, una nuova istanza SQL rimosso in anticipo come parte dell'evento di aggiornamento pianificato, e allega e consente di recuperare il file di database da Archiviazione remota. In caso di arresti anomali del processo o altri eventi imprevisti, Windows Fabric gestisce la disponibilità dell'istanza e, come ultimo passaggio di ripristino, allega il file di database remoto.

## <a name="conclusion"></a>Conclusioni
Database SQL di Azure è perfettamente integrato con la piattaforma Azure e dipende nell'infrastruttura del servizio per il ripristino e il rilevamento degli errori e sui blob di archiviazione di Azure per la protezione dati. Allo stesso tempo, database SQL di Azure utilizza la tecnologia AlwaysOn dal prodotto SQL Server per la replica e failover. La combinazione di queste tecnologie consente alle applicazioni di comprendere i vantaggi di un modello di archiviazione mista completamente e supportare SLA più esigenti. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- Informazioni su [Traffic Manager di Azure](/traffic-manager/traffic-manager-overview.md) 
