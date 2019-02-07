---
title: Disponibilità elevata - Servizio del database SQL di Azure | Documentazione Microsoft
description: Informazioni sulle funzionalità di disponibilità elevata del servizio di database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 91f49adbc922e96bf3cf250735ebfe96e6b39868
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512262"
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure

Database SQL di Azure è la piattaforma distribuita come servizio del database a disponibilità elevata che garantisce che il database sia attivo e in esecuzione il 99,99% del tempo, senza doversi preoccupare di manutenzione e tempi di inattività. Si tratta di un processo del motore di database di SQL Server completamente gestito ospitato nel cloud di Azure che assicura che il database di SQL Server sia sempre aggiornato o con patch senza influire sul carico di lavoro. Quando a un'istanza viene applicata una patch o quando ne viene effettuato il failover, il tempo di inattività è in genere trascurabile se si usa la [logica di ripetizione dei tentativi](sql-database-develop-overview.md#resiliency) nell'app. Se il tempo necessario per completare un failover è superiore a 60 secondi, è necessario aprire un caso di supporto. Database SQL di Azure può effettuare rapidamente il recupero anche nei casi più critici garantendo che i dati siano sempre disponibili.

La piattaforma Azure gestisce completamente ogni database SQL di Azure e garantisce che i dati non vengano persi oltre a una percentuale elevata di disponibilità dei dati. Azure gestisce automaticamente l'applicazione di patch, i backup, la replica, il rilevamento degli errori, i possibili errori di hardware, software o rete sottostanti, la distribuzione di correzioni di bug, i failover, gli aggiornamenti del database e altre attività di manutenzione. Gli ingegneri di SQL Server hanno implementato le procedure note, assicurando che tutte le operazioni di manutenzione vengano completate in meno dello 0,01% del tempo del ciclo di vita del database. Questa architettura è progettata per garantire che i dati di cui è stato eseguito il commit non vengano mai persi e che le operazioni di manutenzione vengano eseguite senza influire sul carico di lavoro. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione. La disponibilità elevata incorporata nel database SQL di Azure garantisce che il database non sia mai un singolo punto di guasto nell'architettura del software.

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono due modelli di architettura a disponibilità elevata utilizzabili in Database SQL di Azure (entrambi garantiscono la disponibilità del 99,99%):

- Modello di livello di servizio standard/per utilizzo generico che si basa su una separazione tra calcolo e archiviazione. Questo modello di architettura si basa su un'elevata disponibilità e affidabilità del livello di archiviazione, ma potrebbe avere una potenziale riduzione del livello delle prestazioni durante le attività di manutenzione.
- Modello di servizio Premium/business critical basato su un cluster di processi del motore di database. Questo modello di architettura si basa sul fatto che c’è sempre un quorum di nodi di motore di database disponibili e ha un impatto minimo sulle prestazioni sul carico di lavoro anche durante le attività di manutenzione.

Azure aggiorna e applica le patch al sistema operativo, ai driver e al motore di database di SQL Server in modo trasparente con tempi di inattività minimi per gli utenti finali. Il database SQL di Azure viene eseguito sulla versione stabile più recente del motore di database di SQL Server e del sistema operativo Windows. La maggior parte degli utenti non si accorgerà del fatto che gli aggiornamenti vengono eseguiti continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilità dei livelli di servizio Basic, Standard e Utilizzo generico

La disponibilità Standard si riferisce al 99,99% dei contratti di servizio che viene applicata nei livelli Basic, Standard e Utilizzo generico. La disponibilità elevata in questo modello di architettura viene ottenuta dalla separazione dei livelli di calcolo e dall’archiviazione e dalla replica dei dati nel livello di archiviazione.

La figura seguente illustra quattro nodi nel modello dell'architettura standard con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](media/sql-database-managed-instance/general-purpose-service-tier.png)

Nel modello a disponibilità standard sono presenti due livelli:

- Un livello di calcolo senza stato che esegue il processo `sqlserver.exe` e contiene solo i dati temporanei e memorizzati nella cache, ad esempio cache dei piani, pool di buffer, pool dell'archivio colonne. Il nodo di SQL Server senza stato è gestito da Azure Service Fabric che inizializza il processo, controlla l'integrità del nodo e, se necessario, esegue il failover in un'altra posizione.
- Un livello di dati con stato con i file di database (.mdf/.ldf) archiviati nell’archiviazione Premium di Azure. Archiviazione di Azure garantisce che i dati dei record che si trovano in un file di database non vengano persi. Archiviazione di Azure è dotato di disponibilità/ridondanza dei dati incorporata che assicura che ogni record nel file di log o pagina nel file di dati verrà conservato anche se si blocca il processo di SQL Server.

Ogni volta che viene aggiornato il motore di database o il sistema operativo, ogni volta che qualche parte dell’infrastruttura sottostante non funziona oppure se viene rilevato un problema critico nel processo di SQL Server, Azure Service Fabric sposta il processo di SQL Server senza stato in un altro nodo di calcolo senza stato. È presente un set di nodi di riserva in attesa di eseguire un nuovo servizio di calcolo in caso di failover, per ridurre al minimo il tempo di failover. I dati nel livello di Archiviazione di Azure non sono interessati e i dati e i file di log vengono associati al processo di SQL Server appena inizializzato. Questo processo garantisce il 99,99% della disponibilità, ma potrebbe influire sulle prestazioni di un carico di lavoro importante in esecuzione a causa del tempo di transizione e del fatto che il nuovo nodo di SQL Server inizia con la cache a freddo.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilità dei livelli di servizio Premium e Business critical

Nei livelli di servizio Premium e Business critical del database SQL di Azure è abilitata la disponibilità Premium, progettata per carichi di lavoro elevati che non tollerano un impatto sulle prestazioni dovuto alle operazioni di manutenzione continua.

Nel modello Premium il database SQL di Azure integra calcolo e archiviazione nel singolo nodo. La disponibilità elevata in questo modello di architettura è ottenuta dalla replica di archiviazione (unità SSD collegata al computer locale) e calcolo (processo del motore di database SQL Server) distribuita in cluster con 4 nodi mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server.

![Cluster di nodi di motore di database](media/sql-database-managed-instance/business-critical-service-tier.png)

Sia il processo del motore di database SQL che i file mdf/ldf sottostanti vengono posizionati nello stesso nodo con una risorsa di archiviazione SSD collegata in locale che offre bassa latenza al carico di lavoro. La disponibilità elevata è implementata mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server. Ogni database è un cluster di nodi del database con un database primario accessibile per il carico di lavoro del cliente e tre processi secondari contenenti le copie dei dati. Il nodo primario esegue il push costante delle modifiche ai nodi secondari per garantire che i dati siano disponibili nelle repliche secondarie se il nodo primario si arresta per un qualsiasi motivo. Il failover viene gestito da Azure Service Fabric: una replica secondaria diventa il nodo primario e viene creata una nuova replica secondaria per garantire un numero sufficiente di nodi nel cluster. Il carico di lavoro viene reindirizzato automaticamente al nuovo nodo primario.

Inoltre, nel cluster business critical è integrata una funzionalità di [scalabilità in lettura](sql-database-read-scale-out.md) che fornisce un nodo di sola lettura integrato e gratuito che può essere usato per l'esecuzione di query di sola lettura (ad esempio report) che non dovrebbero incidere sulle prestazioni del carico di lavoro primario.

## <a name="zone-redundant-configuration"></a>Configurazione con ridondanza della zona

Per impostazione predefinita, le repliche di set di quorum per le configurazioni di archiviazione locale vengono create nello stesso data center. Con l'introduzione di [Zone di disponibilità di Azure](../availability-zones/az-overview.md), si ha la possibilità di posizionare le varie repliche nei set di quorum per zone di disponibilità diverse nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché la configurazione con ridondanza della zona non crea ridondanza aggiuntiva del database, l'uso delle zone di disponibilità nei livelli di servizio Premium o Business critical è disponibile senza costi aggiuntivi. Se si seleziona un database con ridondanza della zona, è possibile rendere i database premium o business critical resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del data center, senza modifiche della logica dell'applicazione. È anche possibile convertire qualsiasi pool o database premium o business critical alla configurazione con ridondanza della zona.

Poiché il set di quorum con ridondanza della zona dispone di repliche in diversi data center distanti tra loro, la maggiore latenza di rete può aumentare il tempo di commit e pertanto compromettere le prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione di dimensionamento dei dati ed è simile al normale aggiornamento del livello di servizio. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> I pool elastici e i database con ridondanza della zona sono attualmente supportati solo per il livello di servizio Premium. Per impostazione predefinita, i backup e i record di controllo vengono archiviati nell'archiviazione con ridondanza geografica e accesso in lettura. È quindi possibile che non siano disponibili automaticamente in caso di un'interruzione a livello di zona. 

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:

![architettura a disponibilità elevata con ridondanza della zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database

Il [ripristino accelerato del database](sql-database-accelerated-database-recovery.md) è una nuova funzionalità del motore di database SQL che migliora notevolmente la disponibilità dei database, specialmente in presenza di transazioni a esecuzione prolungata, grazie alla riprogettazione del processo di ripristino del motore di database SQL. Questa funzionalità è attualmente disponibile per database singoli, pool elastici e Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusioni

Il database SQL di Azure è completamente integrato con la piattaforma Azure e altamente dipendente da Service Fabric per il rilevamento degli errori e il ripristino, dai BLOB del servizio di archiviazione di Azure per la protezione dati e da Zone di disponibilità per la tolleranza di errore. Allo stesso tempo, il database SQL di Azure sfrutta completamente la tecnologia del gruppo di disponibilità AlwaysOn del nuovo prodotto SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di ottenere tutti i vantaggi di un modello di archiviazione mista e supportare i contratti di servizio più esigenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../availability-zones/az-overview.md)
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
