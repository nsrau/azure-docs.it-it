---
title: "Disponibilità elevata - Servizio del database SQL di Azure | Documentazione Microsoft"
description: "Informazioni sulle funzionalità di disponibilità elevata del servizio di database SQL di Azure"
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure
Dall'inizio dell'offerta PaaS del database SQL di Azure, Microsoft ha promesso ai clienti l'integrazione della disponibilità elevata nel servizio evitando ai clienti di dover gestire, aggiungere logica o prendere decisioni per la disponibilità elevata. Microsoft offre ai clienti un contratto di servizio e mantiene il controllo completo sulla configurazione e il funzionamento del sistema di disponibilità elevata. Il contratto di servizio di disponibilità elevata si applica a un database SQL in un'area e non assicura protezione in caso di errore nell'intera area dovuto a fattori al di fuori del ragionevole controllo di Microsoft (ad esempio calamità naturali, guerra, atti di terrorismo, sommosse, azioni governative o un errore della rete o del dispositivo esterno al data center, inclusi problemi presso la sede del cliente o tra questa e il data center).

Per semplificare l'ambito dei problemi della disponibilità elevata, Microsoft utilizza i presupposti seguenti:
1.  Gli errori hardware e software sono inevitabili
2.  Lo staff operativo commette errori che causano problemi
3.  Gli interventi di manutenzione pianificati causano interruzioni 

Benché tali eventi singoli siano poco frequenti, a livello di cloud accadono ogni settimana, se non tutti i giorni. 

## <a name="fault-tolerant-sql-databases"></a>Database SQL a tolleranza di errore
I clienti sono più interessati alla resilienza dei propri database e meno a quella del servizio di database SQL nel suo complesso. Il tempo di attività del 99,99% per un servizio perde significato se il database del cliente fa parte di quello 0,01% di database inattivi. Ogni singolo database deve essere a tolleranza di errore e la mitigazione dei rischi non deve mai comportare la perdita di una transazione approvata. 

Per i dati, il servizio di database SQL utilizza sia l'archiviazione locale (LS, local storage) basata su dischi rigidi virtuali/dischi collegati diretti sia l'archiviazione remota (RS, remote storage) basata sui BLOB di pagine di Archiviazione Premium di Azure. 
- L'archiviazione locale viene utilizzata nei database e pool Premium, progettati per le applicazioni OLTP con requisiti elevati di operazioni di I/O al secondo. 
- L'archiviazione remota viene usata per i livelli di servizio Basic e Standard, progettati per database piccoli, offlline o grandi che richiedono la scalabilità indipendente di potenza di calcolo e archiviazione. Utilizzano un unico BLOB di pagine per file di log e database e meccanismi predefiniti di replica e failover di archiviazione.

In entrambi i casi, i meccanismi di replica, rilevamento degli errori e failover del servizio di database SQL sono completamente automatizzati e funzionano senza intervento umano. Questa architettura è progettata per garantire che i dati di cui è stato eseguito il commit non vengano mai persi e che la durabilità dei dati abbia la precedenza su tutto il resto.

Vantaggi principali:
- I clienti ottengono i massimi vantaggi dei database replicati senza dover configurare o gestire hardware, software, sistema operativo o ambienti di virtualizzazione complessi.
- Le proprietà ACID complete dei database relazionali vengono gestite dal sistema.
- I failover sono completamente automatizzati senza perdite di dati di cui è stato eseguito il commit.
- Il routing delle connessioni alla replica primaria è gestito in modo dinamico dal servizio senza la necessità di logica di applicazione.
- L'elevato livello di ridondanza automatizzata viene fornito senza alcun costo aggiuntivo.

> [!NOTE]
> L'architettura a disponibilità elevata descritta è soggetta a modifica senza preavviso. 

## <a name="data-redundancy"></a>Ridondanza dei dati

La soluzione a disponibilità elevata del database di SQL si basa sulla tecnologia [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) di SQL Server e funziona per database LS e RS con differenze minime. Nella configurazione LS la tecnologia AlwaysON è utilizzata per la persistenza, mentre nella configurazione RS viene utilizzata per la disponibilità (RTO basso). 

## <a name="local-storage"></a>Archiviazione locale

In caso di archiviazione locale, ogni database viene portato online dal servizio di gestione all'interno dell'anello di controllo. Una replica primaria e almeno due repliche secondarie (set di quorum) si trovano all'interno di un anello di tenant che si estende su tre sottosistemi fisici indipendenti all'interno dello stesso data center. Tutte le letture e le scritture sono inviate dal gateway alla replica primaria e le scritture vengono replicate in modo asincrono nelle repliche secondarie. Il database SQL usa uno schema di commit basato su quorum in cui i dati vengono scritti nella replica primaria e in almeno una replica secondaria prima del commit della transazione.

Il sistema di failover di [Service Fabric](/azure/service-fabric/service-fabric-overview.md) ricompila automaticamente le repliche in caso di errori dei nodi e mantiene l'appartenenza al set di quorum quando i nodi escono ed entrano nel sistema. La manutenzione pianificata è attentamente coordinata per evitare che il set di quorum scenda al di sotto di un numero di repliche minime (in genere due). Questo è un modello ottimale per i database Premium, ma richiede ridondanza dei componenti di elaborazione e di archiviazione e comporta costi superiori.

## <a name="remote-storage"></a>Archiviazione remota

Per le configurazioni di archiviazione remota (livelli Basic e Standard), viene mantenuta esattamente una copia del database nell'archiviazione BLOB remota, sfruttando le funzionalità dei sistemi di archiviazione per durabilità, ridondanza e rilevamento di bit rot. 

L'architettura a disponibilità elevata è illustrata nel diagramma seguente:
 
![architettura a disponibilità elevata](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Rilevamento degli errori e recupero 
Un sistema distribuito su larga scala richiede un sistema di rilevamento degli errori altamente affidabile in grado di rilevare gli errori in modo efficiente, rapido e più vicino possibile al cliente. Per database SQL, questo sistema si basa su Azure Service Fabric. 

Nel caso della replica primaria, è immediatamente evidente se e quando ha avuto esito negativo e non è possibile continuare il lavoro perché tutte le letture e le scritture hanno luogo innanzitutto nella replica primaria. Il processo di promozione di una replica secondaria a primaria ha un obiettivo del tempo di ripristino (RTO) di 30 secondi e un obiettivo del punto di ripristino (RPO) pari a 0. Per mitigare l'impatto dell'RTO di 30 secondi, la procedura consigliata consiste nel tentativo di riconnettersi più volte con un tempo di attesa ridotto per i tentativi non riusciti.

Quando una replica secondaria ha esito negativo, il database rimane inattivo per un set di quorum minimo, senza riserve. Service Fabric avvia il processo di riconfigurazione, analogo a quello che segue l'errore della replica primaria, quindi dopo una breve attesa per determinare se l'errore è permanente, viene creata un'altra replica secondaria. Nei casi di fuori servizio temporaneo, ad esempio in caso di errore del sistema operativo o aggiornamento, non viene creata immediatamente una nuova replica per consentire invece il riavvio del nodo in errore. 

Per le configurazioni di archiviazione remota, il servizio di database SQL utilizza la funzionalità AlwaysON per il failover dei database durante gli aggiornamenti. A tale scopo, viene attivata in anticipo come parte dell'evento di aggiornamento pianificato una nuova istanza di SQL che collega e ripristina il file di database dall'archiviazione remota. In caso di arresti anomali del processo o altri eventi imprevisti, Windows Fabric gestisce la disponibilità dell'istanza e, come ultimo passaggio del ripristino, collega il file di database remoto.

## <a name="conclusion"></a>Conclusioni
Il servizio di database SQL di Azure è completamente integrato con la piattaforma Azure e altamente dipendente da Service Fabric per il rilevamento degli errori e il ripristino e dai BLOB del servizio di archiviazione di Azure per la protezione dati. Allo stesso tempo, il database SQL di Azure utilizza la tecnologia AlwaysOn di SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di ottenere tutti i vantaggi di un modello di archiviazione mista e supportare i contratti di servizio più esigenti. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](/traffic-manager/traffic-manager-overview.md) 
