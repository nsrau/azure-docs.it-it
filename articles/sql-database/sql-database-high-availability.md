---
title: Disponibilità elevata - Servizio del database SQL di Azure | Documentazione Microsoft
description: Informazioni sulle funzionalità di disponibilità elevata del servizio di database SQL di Azure
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: a9874681d59d193fc3c3d0fd4271e2a6a0fb0dc6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060384"
---
# <a name="high-availability-and-azure-sql-database"></a>Disponibilità elevata e database SQL di Azure

Database SQL di Azure è la piattaforma distribuita come servizio del database a disponibilità elevata che garantisce che il database sia attivo e in esecuzione il 99,99% del tempo, senza doversi preoccupare di manutenzione e tempi di inattività. Si tratta di un processo del motore di database di SQL Server completamente gestito ospitato nel cloud di Azure che assicura che il database di SQL Server sia sempre aggiornato o con patch senza influire sul carico di lavoro. Database SQL di Azure può effettuare rapidamente il recupero anche nei casi più critici garantendo che i dati siano sempre disponibili.

La piattaforma Azure gestisce completamente ogni database SQL di Azure e garantisce che i dati non vengano persi oltre a una percentuale elevata di disponibilità dei dati. Azure gestisce automaticamente l'applicazione di patch, i backup, la replica, il rilevamento degli errori, i possibili errori di hardware, software o rete sottostanti, la distribuzione di correzioni di bug, i failover, gli aggiornamenti del database e altre attività di manutenzione. Gli ingegneri di SQL Server hanno implementato le procedure note, assicurando che tutte le operazioni di manutenzione vengano completate in meno dello 0,01% del tempo del ciclo di vita del database. Questa architettura è progettata per garantire che i dati di cui è stato eseguito il commit non vengano mai persi e che le operazioni di manutenzione vengano eseguite senza influire sul carico di lavoro. Non ci sono finestre di manutenzione o tempi di inattività che richiedono l'arresto del carico di lavoro mentre il database viene aggiornato o se ne esegue la manutenzione. La disponibilità elevata incorporata nel database SQL di Azure garantisce che il database non sia mai un singolo punto di guasto nell'architettura del software.

In Azure SQL esistono due modelli a disponibilità elevata applicati:

- Modello Standard/utilizzo generico che garantisce il 99,99% di disponibilità, ma con possibile riduzione del livello delle prestazioni durante l'attività di manutenzione.
- Modello Premium/business critical, anch'esso garantisce il 99,99% di disponibilità con impatto minimo sulle prestazioni del carico di lavoro anche durante le attività di manutenzione.

Azure aggiorna e applica le patch al sistema operativo, ai driver e al motore di database di SQL Server in modo trasparente con tempi di inattività minimi per gli utenti finali. Il database SQL di Azure viene eseguito sulla versione stabile più recente del motore di database di SQL Server e del sistema operativo Windows. La maggior parte degli utenti non si accorgerà del fatto che gli aggiornamenti vengono eseguiti continuamente.

## <a name="standard-availability"></a>Disponibilità Standard

La disponibilità Standard si riferisce al 99,99% dei contratti di servizio che viene applicata nei livelli Standard/Basic/per utilizzo generico. La disponibilità si ottiene separando i livelli di calcolo e archiviazione. Nel modello a disponibilità Standard sono presenti due livelli:

- Un livello di calcolo senza stato che esegue il processo sqlserver.exe e contiene solo i dati temporanei e memorizzati nella cache, ad esempio cache dei piani, pool di buffer, pool di archiviazione della colonna. Il nodo di SQL Server senza stato è gestito da Azure Service Fabric che inizializza il processo, controlla l'integrità del nodo e, se necessario, esegue il failover in un'altra posizione.
- Un livello di dati con stato con i file di database (.mdf/.ldf) archiviati nei dischi di archiviazione Premium di Azure. Archiviazione di Azure garantisce che i dati dei record che si trovano in un file di database non vengano persi. Archiviazione di Azure è dotato di disponibilità/ridondanza dei dati incorporata che assicura che ogni record nel file di log o pagina nel file di dati verrà conservato anche se si blocca il processo di SQL Server.

Ogni volta che viene aggiornato il motore di database o il sistema operativo oppure se viene rilevato un problema critico nel processo di SQL Server, Azure Service Fabric sposta il processo di SQL Server senza stato in un altro nodo di calcolo senza stato. I dati nel livello di Archiviazione di Azure non sono interessati e i dati e i file di log vengono associati al processo di SQL Server appena inizializzato. Il tempo di failover previsto può essere misurato in secondi. Questo processo garantisce il 99,99% della disponibilità, ma potrebbe influire sulle prestazioni per carichi di lavoro importanti in esecuzione a causa del tempo di transizione e del fatto che il nuovo nodo di SQL Server inizia con la cache a freddo.

## <a name="premium-availability"></a>Disponibilità Premium

Nel livello Premium del Database SQL di Azure è abilitata la disponibilità Premium, progettata per carichi di lavoro elevati che non tollerano un impatto sulle prestazioni dovuto alle operazioni di manutenzione continua.

Nel modello Premium il database SQL di Azure integra calcolo e archiviazione nel singolo nodo. Sia il processo del motore di database di SQL Server che i file mdf/ldf sottostanti vengono posizionati nello stesso nodo con una risorsa di archiviazione SSD collegata in locale che offre bassa latenza al carico di lavoro.

La disponibilità elevata viene implementata usando i [gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) standard. Ogni database è un cluster di nodi del database con un database primario accessibile per il carico di lavoro del cliente e alcuni processi secondari contenenti le copie dei dati. Il nodo primario esegue il push costante delle modifiche ai nodi secondari per garantire che i dati siano disponibili nelle repliche secondarie se il nodo primario si arresta per un qualsiasi motivo. Il failover viene gestito dal motore di database di SQL Server: una replica secondaria diventa il nodo primario e viene creata una nuova replica secondaria per garantire un numero sufficiente di nodi nel cluster. Il carico di lavoro viene reindirizzato automaticamente al nuovo nodo primario. Il tempo di failover viene misurato in millisecondi e la nuova istanza primaria è subito pronta per continuare a gestire le richieste.

## <a name="zone-redundant-configuration-preview"></a>Configurazione con ridondanza della zona (anteprima)

Per impostazione predefinita, le repliche di set di quorum per le configurazioni di archiviazione locale vengono create nello stesso data center. Con l'introduzione di [Zone di disponibilità di Azure](../availability-zones/az-overview.md), si ha la possibilità di posizionare le varie repliche nei set di quorum per zone di disponibilità diverse nella stessa area. Per eliminare un singolo punto di guasto, viene duplicato anche l'anello di controllo in più zone come tre anelli gateway. Il routing a un anello gateway specifico è controllato da [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché la configurazione con ridondanza della zona non crea ridondanza aggiuntiva del database, l'uso delle zone di disponibilità nei livelli di servizio Premium o Business Critical (anteprima) è disponibile senza costi aggiuntivi. Se si seleziona un database con ridondanza della zona, è possibile rendere i database Premium o Business Critical (anteprima) resilienti a un set molto più ampio di errori, incluse interruzioni irreversibili del data center, senza modifiche della logica dell'applicazione. È anche possibile convertire qualsiasi pool o database Premium o Business Critical (anteprima) alla configurazione con ridondanza della zona.

Poiché il set di quorum con ridondanza della zona dispone di repliche in diversi data center distanti tra loro, la maggiore latenza di rete può aumentare il tempo di commit e pertanto compromettere le prestazioni di alcuni carichi di lavoro OLTP. È sempre possibile tornare alla configurazione a singola zona disabilitando l'impostazione di ridondanza della zona. Questo processo è un'operazione di dimensionamento dei dati ed è simile al normale aggiornamento dell'obiettivo del livello di servizio. Al termine del processo, viene eseguita la migrazione del database o del pool da un anello con ridondanza della zona a un anello a singola zona o viceversa.

> [!IMPORTANT]
> I pool elastici e i database con ridondanza della zona sono attualmente supportati solo per il livello di servizio Premium. Durante l'anteprima pubblica, i backup e i record di controllo vengono archiviati nell'archiviazione con ridondanza geografica e accesso in lettura. È quindi possibile che non siano disponibili automaticamente in caso di un'interruzione a livello di zona. 

La versione con ridondanza della zona dell'architettura a disponibilità elevata è illustrata nel diagramma seguente:
 
![architettura a disponibilità elevata con ridondanza della zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Scalabilità in lettura
Come descritto, i livelli di servizio Premium e business critical (anteprima) sfruttano i set di quorum e la tecnologia AlwaysOn per la disponibilità elevata sia nelle configurazioni a singola zona e che in quelle con ridondanza della zona. Uno dei vantaggi della tecnologia AlwaysOn è che le repliche sono sempre in uno stato coerente a livello di transazione. Le repliche hanno lo stesso livello di prestazioni della replica primaria, pertanto l'applicazione può sfruttare tale capacità aggiuntiva per la manutenzione dei carichi di lavoro di sola lettura senza costi aggiuntivi (scalabilità in lettura). In questo modo le query di sola lettura verranno isolate dal carico di lavoro principale di lettura/scrittura e non ne comprometteranno le prestazioni. La funzionalità di scalabilità in lettura è destinata alle applicazioni che includono carichi di lavoro di sola lettura separati in modo logico, ad esempio dati di analisi, e quindi potrebbero sfruttare tale capacità senza connettersi alla replica primaria. 

Per usare la funzionalità di scalabilità in lettura con un determinato database, è necessario attivarla in modo esplicito quando si crea il database oppure in seguito modificandone la configurazione con PowerShell richiamando i cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) o [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) oppure tramite l'API REST di Azure Resource Manager con il metodo [Create o Update per i database](/rest/api/sql/databases/createorupdate).

Dopo che la scalabilità in lettura è stata abilitata per un database, le applicazioni che si connettono a tale database verranno indirizzate alla replica di lettura/scrittura o a una replica di sola lettura di tale database a seconda della proprietà `ApplicationIntent` configurata nella stringa di connessione dell'applicazione. Per informazioni sulla proprietà `ApplicationIntent`, vedere [Specifying Application Intent](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) (Specifica della finalità dell'applicazione). 

Se la scalabilità in lettura è disabilitata o è stata impostata la proprietà ReadScale in un livello di servizio non supportato, tutte le connessioni vengono indirizzate alla replica in lettura-scrittura, indipendentemente dalla proprietà `ApplicationIntent`.  

> [!NOTE]
> È possibile attivare la scalabilità in lettura su un database Standard o di Utilizzo generale, anche se non comporterà il routing della sessione prevista di sola lettura in una replica separata. Questa operazione viene eseguita per supportare le applicazioni esistenti con scalabilità verticale tra i livelli Standard/Utilizzo generale e Premium/Business critical.  

La funzionalità di scalabilità in lettura supporta la coerenza a livello di sessione. Se la sessione di sola lettura si riconnette dopo un errore di connessione causato dalla mancata disponibilità della replica, può essere reindirizzata a una replica diversa. Sebbene sia poco probabile, ciò può comportare l'elaborazione del set di dati non aggiornato. Analogamente, se un'applicazione scrive i dati usando una sessione di lettura/scrittura e li legge immediatamente usando la sessione di sola lettura, è possibile che i nuovi dati non siano immediatamente visibili.

## <a name="conclusion"></a>Conclusioni
Il database SQL di Azure è completamente integrato con la piattaforma Azure e altamente dipendente da Service Fabric per il rilevamento degli errori e il ripristino, dai BLOB del servizio di archiviazione di Azure per la protezione dati e da Zone di disponibilità per la tolleranza di errore. Allo stesso tempo, il database SQL di Azure sfrutta completamente la tecnologia del gruppo di disponibilità AlwaysOn del nuovo prodotto SQL Server per la replica e il failover. La combinazione di queste tecnologie consente alle applicazioni di ottenere tutti i vantaggi di un modello di archiviazione mista e supportare i contratti di servizio più esigenti. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Zone di disponibilità di Azure](../availability-zones/az-overview.md)
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md)
- Altre informazioni su [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) 
