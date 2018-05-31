---
title: Disponibilità elevata - Servizio del database SQL di Azure | Microsoft Docs
description: Informazioni sulle funzionalità di disponibilità elevata del servizio di database SQL di Azure
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 04/24/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: e541513890d357587e5c1e792165123c2beb5d96
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777018"
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure
Dall'inizio dell'offerta PaaS del database SQL di Azure, Microsoft ha promesso ai clienti l'integrazione della disponibilità elevata nel servizio evitando ai clienti di dover gestire, aggiungere una logica specifica o prendere decisioni in merito alla disponibilità elevata. Microsoft mantiene il controllo completo sulla configurazione e il funzionamento del sistema di disponibilità elevata offrendo ai clienti un contratto di servizio. Il contratto di servizio di disponibilità elevata si applica a un database SQL in un'area e non assicura protezione in caso di errore nell'intera area dovuto a fattori al di fuori del ragionevole controllo di Microsoft (ad esempio calamità naturali, guerra, atti di terrorismo, sommosse, azioni governative o un errore della rete o del dispositivo esterno al data center Microsoft, inclusi problemi presso la sede del cliente o tra questa e il data center Microsoft).

Per semplificare l'ambito dei problemi della disponibilità elevata, Microsoft utilizza i presupposti seguenti:
1.  Gli errori hardware e software sono inevitabili
2.  Lo staff operativo commette errori che causano problemi
3.  Gli interventi di manutenzione pianificati causano interruzioni 

Benché tali eventi singoli siano poco frequenti, a livello di cloud accadono ogni settimana, se non tutti i giorni. 

## <a name="fault-tolerant-sql-databases"></a>Database SQL a tolleranza di errore
I clienti sono più interessati alla resilienza dei propri database e meno a quella del servizio di database SQL nel suo complesso. Il tempo di attività del 99,99% per un servizio perde significato se il database del cliente fa parte di quello 0,01% di database inattivi. Ogni singolo database deve essere a tolleranza di errore e la mitigazione dei rischi non deve mai comportare la perdita di una transazione approvata. 

Per i dati, il servizio di database SQL utilizza sia l'archiviazione locale (LS, local storage) basata su dischi rigidi virtuali/dischi collegati diretti sia l'archiviazione remota (RS, remote storage) basata sui BLOB di pagine di Archiviazione Premium di Azure. 
- L'archiviazione locale viene usata nei database e nei pool elastici Premium o Business Critical (anteprima), progettati per le applicazioni OLTP strategiche con requisiti elevati di operazioni di I/O al secondo. 
- L'archiviazione remota viene usata per i livelli di servizio Basic, Standard e Utilizzo generico progettati per carichi di lavoro aziendali orientati al budget che richiedono scalabilità indipendente di potenza di calcolo e archiviazione. Utilizzano un unico BLOB di pagine per file di log e database e meccanismi predefiniti di replica e failover di archiviazione.

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

La soluzione a disponibilità elevata del database di SQL si basa sulla tecnologia dei [gruppi di disponibilità Always ON](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server e funziona per database LS e RS con differenze minime. Nella configurazione LS la tecnologia del gruppo di disponibilità Always ON viene usata per la persistenza, mentre in quella RS viene usata per la disponibilità (RTO basso tramite replica geografica attiva). 

## <a name="local-storage-configuration"></a>Configurazione dell'archiviazione locale

In questo tipo di configurazione, ogni database viene portato online dal servizio di gestione all'interno dell'anello di controllo. Una replica primaria e almeno due repliche secondarie (set di quorum) si trovano all'interno di un anello di tenant che si estende su tre sottosistemi fisici indipendenti all'interno dello stesso data center. Tutte le letture e le scritture sono inviate dal gateway alla replica primaria e le scritture vengono replicate in modo asincrono nelle repliche secondarie. Il database SQL usa uno schema di commit basato su quorum in cui i dati vengono scritti nella replica primaria e in almeno una replica secondaria prima del commit della transazione.

Il sistema di failover di [Service Fabric](../service-fabric/service-fabric-overview.md) ricompila automaticamente le repliche in caso di errori dei nodi e mantiene l'appartenenza al set di quorum quando i nodi escono ed entrano nel sistema. La manutenzione pianificata è attentamente coordinata per evitare che il set di quorum scenda al di sotto di un numero di repliche minime (in genere due). Questo è un modello ottimale per i database Premium e Business Critical (anteprima), ma richiede ridondanza dei componenti di calcolo e archiviazione e comporta costi maggiori.

## <a name="remote-storage-configuration"></a>Configurazione dell'archiviazione remota

Per le configurazioni di archiviazione remota (livelli Basic, Standard o Utilizzo generale), viene mantenuta esattamente una copia nell'archiviazione BLOB remota, usando le funzionalità dei sistemi di archiviazione per durabilità, ridondanza e rilevamento di bit rot. 

L'architettura a disponibilità elevata è illustrata nel diagramma seguente:
 
![architettura a disponibilità elevata](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Rilevamento degli errori e recupero 
Un sistema distribuito su larga scala richiede un sistema di rilevamento degli errori altamente affidabile in grado di rilevare gli errori in modo efficiente, rapido e più vicino possibile al cliente. Per database SQL, questo sistema si basa su Azure Service Fabric. 

Nel caso della replica primaria, è immediatamente evidente se e quando ha avuto esito negativo e non è possibile continuare il lavoro perché tutte le letture e le scritture hanno luogo innanzitutto nella replica primaria. Il processo di promozione di una replica secondaria a primaria ha un obiettivo del tempo di ripristino (RTO) di 30 secondi e un obiettivo del punto di ripristino (RPO) pari a 0. Per mitigare l'impatto dell'RTO di 30 secondi, la procedura consigliata consiste nel tentativo di riconnettersi più volte con un tempo di attesa ridotto per i tentativi non riusciti.

Quando una replica secondaria ha esito negativo, il database rimane inattivo per un set di quorum minimo, senza riserve. Service Fabric avvia il processo di riconfigurazione, analogo a quello che segue l'errore della replica primaria, quindi dopo una breve attesa per determinare se l'errore è permanente, viene creata un'altra replica secondaria. Nei casi di fuori servizio temporaneo, ad esempio in caso di errore del sistema operativo o aggiornamento, non viene creata immediatamente una nuova replica per consentire invece il riavvio del nodo in errore. 

Per le configurazioni di archiviazione remota, database SQL usa la funzionalità Always ON per il failover dei database durante gli aggiornamenti. A tale scopo, viene attivata in anticipo come parte dell'evento di aggiornamento pianificato una nuova istanza di SQL che collega e ripristina il file di database dall'archiviazione remota. In caso di arresti anomali del processo o altri eventi imprevisti, Windows Fabric gestisce la disponibilità dell'istanza e, come ultimo passaggio del ripristino, collega il file di database remoto.

## <a name="zone-redundant-configuration-preview"></a>Configurazione con ridondanza della zona (anteprima)

Per impostazione predefinita, le repliche di set di quorum per le configurazioni di archiviazione locale vengono create nello stesso data center. Con l'introduzione di [Zone di disponibilità di Azure](../availability-zones/az-overview.md), si ha la possibilità di posizionare le varie repliche nei set di quorum per zone di disponibilità diverse nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché la configurazione con ridondanza della zona non crea ridondanza aggiuntiva del database, l'uso delle zone di disponibilità nei livelli di servizio Premium o Business Critical (anteprima) è disponibile senza costi aggiuntivi. Se si seleziona un database con ridondanza della zona, è possibile rendere i database Premium o Business Critical (anteprima) resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del data center, senza modifiche della logica dell'applicazione. È anche possibile convertire qualsiasi pool o database Premium o Business Critical (anteprima) alla configurazione con ridondanza della zona.

Poiché il set di quorum con ridondanza della zona dispone di repliche in diversi data center distanti tra loro, la maggiore latenza di rete può aumentare il tempo di commit e pertanto compromettere le prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione di dimensionamento dei dati ed è simile al normale aggiornamento dell'obiettivo del livello di servizio. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> I pool elastici e i database con ridondanza della zona sono supportati solo nei livelli di servizio Premium e Business Critical (anteprima). Durante l'anteprima pubblica, i backup e i record di controllo vengono archiviati nell'archiviazione con ridondanza geografica e accesso in lettura. È quindi possibile che non siano disponibili automaticamente in caso di un'interruzione a livello di zona. 

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:
 
![architettura a disponibilità elevata con ridondanza della zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Scalabilità in lettura
Come descritto, i livelli di servizio Premium e Business Critical (anteprima) sfruttano i set di quorum e la tecnologia AlwaysON per la disponibilità elevata nelle configurazioni a singola zona e con ridondanza della zona. Uno dei vantaggi della tecnologia AlwaysOn è che le repliche sono sempre in uno stato coerente a livello di transazione. Le repliche hanno lo stesso livello di prestazioni della replica primaria, pertanto l'applicazione può sfruttare tale capacità aggiuntiva per la manutenzione dei carichi di lavoro di sola lettura senza costi aggiuntivi (scalabilità in lettura). In questo modo le query di sola lettura verranno isolate dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. La funzionalità di scalabilità in lettura è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati in modo logico, ad esempio dati di analisi, e quindi potrebbero sfruttare tale capacità senza connettersi alla replica primaria. 

Per usare la funzionalità di scalabilità in lettura con un determinato database, è necessario attivarla in modo esplicito quando si crea il database oppure in seguito modificandone la configurazione con PowerShell richiamando i cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) o [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) oppure tramite l'API REST di Azure Resource Manager con il metodo [Create o Update per i database](/rest/api/sql/databases/createorupdate).

Dopo che la scalabilità in lettura è stata abilitata per un database, le applicazioni che si connettono a tale database verranno indirizzate alla replica di lettura/scrittura o a una replica di sola lettura di tale database a seconda della proprietà `ApplicationIntent` configurata nella stringa di connessione dell'applicazione. Per informazioni sulla proprietà `ApplicationIntent`, vedere [Specifying Application Intent](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) (Specifica della finalità dell'applicazione). 

Se la scalabilità in lettura è disabilitata o è stata impostata la proprietà ReadScale in un livello di servizio non supportato, tutte le connessioni vengono indirizzate alla replica in lettura-scrittura, indipendentemente dalla proprietà `ApplicationIntent`.  

> [!NOTE]
> È possibile attivare la scalabilità in lettura su un database Standard o di Utilizzo generale, anche se non comporterà il routing della sessione prevista di sola lettura in una replica separata. Questa operazione viene eseguita per supportare le applicazioni esistenti con scalabilità verticale tra i livelli Standard/Utilizzo generale e Premium/Business critical.  

La funzionalità di scalabilità in lettura supporta la coerenza a livello di sessione. Se la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, può essere reindirizzata a una replica diversa. Sebbene sia poco probabile, ciò può comportare l'elaborazione del set di dati non aggiornato. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura/scrittura e li legge immediatamente usando la sessione di sola lettura, è possibile che i nuovi dati non siano immediatamente visibili.

## <a name="conclusion"></a>Conclusioni
Il database SQL di Azure è completamente integrato con la piattaforma Azure e altamente dipendente da Service Fabric per il rilevamento degli errori e il ripristino, dai BLOB del servizio di archiviazione di Azure per la protezione dati e da Zone di disponibilità per la tolleranza di errore. Allo stesso tempo, il database SQL di Azure sfrutta completamente i vantaggi offerti dalla tecnologia Always On di SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di ottenere tutti i vantaggi di un modello di archiviazione mista e supportare i contratti di servizio più esigenti. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../availability-zones/az-overview.md)
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) 
