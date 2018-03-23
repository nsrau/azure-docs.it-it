---
title: Disponibilità di SAP HANA in un'area di Azure | Microsoft Docs
description: Operazioni di SAP HANA in macchine virtuali native di Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilità di SAP HANA in un'area di Azure
In questa sezione vengono presentati diversi scenari che descrivono gli scenari di disponibilità in un'area di Azure. Azure include molte aree distribuite in tutto il mondo. Per un elenco delle aree di Azure, fare riferimento alla pagina [Aree di Azure](https://azure.microsoft.com/regions/). Per la distribuzione di SAP HANA in macchine virtuali in un'area di Azure, Microsoft offre la possibilità di distribuire una singola macchina virtuale con un'istanza di HANA. In alternativa, per una maggiore disponibilità è possibile distribuire due macchine virtuali con due istanze di HANA in un [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) che usano la replica di sistema HANA per garantire la disponibilità. Azure offre un'anteprima pubblica delle [zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Queste zone di disponibilità non verranno tuttavia discusse in dettaglio, fatta eccezione per alcune considerazioni generali sull'uso dei set di disponibilità rispetto alle zone di disponibilità.

Qual è la differenza tra i set di disponibilità di Azure e le zone di disponibilità? Per le aree di Azure in cui sono disponibili le zone di disponibilità, le aree dispongono di più data center, indipendenti nella fornitura di alimentazione, raffreddamento e rete. Il motivo alla base dell'offerta di diverse zone all'interno di una singola area di Azure è quello di consentire la distribuzione delle applicazioni tra le due o tre zone di disponibilità offerte. Supponendo che i problemi relativi alle fonti di alimentazione e/o alla rete interessino solo un'infrastruttura della zona di disponibilità, la distribuzione dell'applicazione in un'area di Azure è ancora pienamente funzionale. La capacità potrebbe risultare ridotta dal momento che alcune macchine virtuali in una zona potrebbero andare perse. Tuttavia, le macchine virtuali nelle altre due zone sono ancora pienamente funzionanti. 
 
Un set di disponibilità di Azure è una funzionalità di raggruppamento logico che è possibile usare in Azure per garantire che le risorse delle macchine virtuali inserite dall'utente siano isolate tra loro in caso di errore quando vengono distribuite all'interno di un data center di Azure. Azure garantisce che le macchine virtuali inserite all'interno di un set di disponibilità vengano eseguite tra più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. O come indicato in altri esempi di documentazione su Azure, si tratta di inserimenti in diversi [domini di aggiornamento e di errore](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Questi inserimenti avvengono in genere all'interno di un data center di Azure. Supponendo che i problemi relativi alle fonti di alimentazione e/o alla rete interessino il data center in cui ha luogo la distribuzione, risulterebbe interessata l'intera capacità in un'area di Azure.

L'inserimento di data center che rappresentano le zone di disponibilità di Azure è un compromesso tra la fornitura di latenza di rete tra i servizi distribuiti in diverse zone che sono accettabili per la maggior parte delle applicazioni e una certa distanza tra i data center, in modo che eventuali catastrofi naturali non abbiano ripercussioni sull'alimentazione e la fornitura di rete e l'infrastruttura per tutte le zone di disponibilità in questa area. Tuttavia, come hanno dimostrato catastrofi naturali di grave entità, non sempre le zone di disponibilità potrebbero essere in grado di fornire la disponibilità in un'area come desiderato. Basti pensare all'uragano Maria che ha colpito l'isola di Porto Rico il 20 agosto 2017 causando un black out quasi totale per tutti i 140 chilometri dell'isola.   
  


## <a name="single-vm-scenario"></a>Scenario basato su una singola macchina virtuale
In questo scenario è stata creata una macchina virtuale di Azure per l'istanza di SAP HANA. È stata usata Archiviazione Premium di Azure per l'hosting del disco del sistema operativo e di tutti i dischi dati. Il contratto di servizio per il tempo di attività del 99,9% di Azure e i contratti di servizio degli altri componenti di Azure sono sufficienti per soddisfare i contratti di servizio relativi alla disponibilità per i clienti. In questo scenario non occorre fare ricorso a un set di disponibilità di Azure per le macchine virtuali che eseguono il livello DBMS. Questo scenario si basa su due diverse funzionalità:

- Riavvio automatico della macchina virtuale di Azure (noto anche come correzione del servizio di Azure)
- Riavvio automatico di SAP HANA

Il riavvio automatico della macchina virtuale di Azure o "correzione del servizio" è una caratteristica di Azure che funziona su due livelli:

- L'host del server di Azure verifica l'integrità di una macchina virtuale ospitata nell'host del server
- Il controller di infrastruttura di Azure monitora l'integrità e la disponibilità dell'host del server

Per ogni macchina virtuale ospitata in un host del server di Azure, una funzionalità di controllo dell'integrità monitora l'integrità della macchina o delle macchine virtuali ospitate. Nel caso in cui una macchina virtuale si trovi in uno stato di non integrità, il riavvio della VM può essere avviato dall'agente host di Azure che controlla l'integrità della macchina virtuale. Il controller di infrastruttura di Azure verifica l'integrità dell'host controllando molti parametri diversi che indicano problemi con l'hardware dell'host, verificando anche l'accessibilità dell'host attraverso la rete. Un'indicazione di problemi a livello di host può portare alle azioni seguenti:

- Reboot dell'host e riavvio delle macchine virtuali in esecuzione nell'host, se l'host segnala uno stato di integrità negativo.
- Reboot dell'host e riavvio della macchina o delle macchine virtuali originariamente ospitate nell'host in un host integro nel caso in cui l'host non sia in uno stato integro dopo il reboot. In questo caso, l'host sarà contrassegnato come non integro e non verrà usato per ulteriori distribuzioni fino a quando non sarà eliminato o sostituito.
- Riavvio immediato delle macchine virtuali in un host integro nei casi in cui l'host non integro abbia problemi nel processo di reboot. 

Con il monitoraggio degli host e delle macchine virtuali offerto da Azure, le macchine virtuali di Azure in cui si verificano problemi a livello di host vengono riavviate automaticamente in un host di Azure integro. 

La seconda funzionalità su cui si può fare affidamento in questo scenario è il riavvio automatico del servizio HANA eseguito in questa macchina virtuale riavviata dopo il reboot della macchina virtuale. Il [riavvio automatico del servizio HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) può essere configurato tramite i servizi watchdog dei diversi servizi HANA.

Questo scenario basato su una singola macchina virtuale potrebbe essere ottimizzato con l'aggiunta di un nodo di failover a freddo a una configurazione di SAP HANA o, come è definito nella documentazione di SAP HANA, [failover automatico dell'host](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Questa configurazione può risultare utile in situazioni di distribuzione in locale in cui l'hardware del server è limitato e si dedica un singolo nodo server come nodo di failover automatico dell'host per un set di host di produzione. Tuttavia, per situazioni in cui l'infrastruttura sottostante di Azure fornisce un server di destinazione integro per il corretto riavvio di una macchina virtuale di Azure, lo scenario di failover automatico dell'host di SAP HANA non è utile ai fini della distribuzione. 

Di conseguenza, non è disponibile un'architettura di riferimento che preveda un nodo di standby per il failover automatico dell'host di HANA. Questo vale anche per le configurazioni con scalabilità orizzontale di SAP HANA.


## <a name="availability-scenarios-involving-two-different-vms"></a>Scenari di disponibilità con due diverse macchine virtuali
L'uso di due macchine virtuali di Azure all'interno dei set di disponibilità di Azure consente di aumentare il tempo di attività tra queste due macchine virtuali se tali VM vengono inserite in un set di disponibilità di Azure in un'area di Azure. La configurazione di base in Azure ha un aspetto analogo a quello riportato nella figura seguente: ![Due macchine virtuali con tutti i livelli](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Per illustrare i diversi scenari di disponibilità, alcuni dei livelli nell'immagine precedente sono tagliati e l'immagine è limitata ai livelli di macchine virtuali, host, set di disponibilità e aree di Azure. Le reti virtuali, i gruppi di risorse e le sottoscrizioni di Azure non svolgono alcun ruolo per gli scenari descritti.

### <a name="replicating-backups-to-second-virtual-machine"></a>Replica di backup nella seconda macchina virtuale
Una delle configurazioni più elementari consiste nel disporre di backup, in particolare di backup del log delle transazioni forniti da una macchina virtuale a un'altra macchina virtuale di Azure. È possibile scegliere qualsiasi tipo di Archiviazione di Azure. Sarà necessario creare uno script della copia dei backup pianificati eseguiti nella prima macchina virtuale per la seconda macchina virtuale. In caso di utilizzi che richiedono istanze della seconda macchina virtuale, sarà necessario ripristinare i backup completi, incrementali/differenziali e del log delle transazioni fino al punto necessario. L'architettura avrà un aspetto simile al seguente: ![Due macchine virtuali con replica di archiviazione](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Questa configurazione non è indicata per ottenere tempi di RPO e RTO ottimali. In particolare i tempi di RTO ne risentirebbero a causa della necessità di ripristinare interamente il database completo con i backup copiati. Tuttavia, questa configurazione è utile per il ripristino in caso di eliminazione accidentale dei dati nelle istanze principali. Con questa configurazione, in qualsiasi momento è possibile eseguire il ripristino fino a un determinato punto nel tempo, estrarre i dati e importare i dati eliminati nell'istanza principale. Di conseguenza, può risultare vantaggioso usare questo metodo di copia di backup in combinazione con altre funzionalità per la disponibilità elevata. Nella fase in cui vengono copiati solo i backup è possibile che venga visualizzata una macchina virtuale di dimensioni inferiori rispetto alle istanze principali di SAP HANA in cui è in esecuzione. Tenere presente che le macchine virtuali più piccole dispongono di un numero minore di dischi rigidi virtuali che possono essere collegati. Fare riferimento a [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) per i limiti dei singoli tipi di macchine virtuali.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>Uso della replica di sistema SAP HANA senza failover automatico
Per gli scenari seguenti si usa la replica di sistema SAP HANA. La documentazione su SAP è disponibile a partire dall'articolo [System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (Replica di sistema). Tra due macchine virtuali di Azure in una singola area di Azure ci sono due diverse configurazioni che presentano alcune differenze relativamente all'obiettivo del tempo di ripristino (RTO). In generale, gli scenari privi di failover automatico potrebbero non essere troppo rilevanti per gli scenari all'interno di un'area di Azure. Il motivo è che nella maggior parte delle situazioni di errore nell'infrastruttura di Azure la correzione del servizio di Azure riavvia la macchina virtuale primaria in un altro host. Ci sono solo alcuni casi limite in cui una configurazione di questo tipo potrebbe essere utile in termini di scenari di errore o altri che l'utente in quanto cliente dovrebbe prendere in considerazione, in particolare per l'efficienza.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Uso della replica di sistema HANA senza failover automatico e senza precaricamento dei dati 
Si tratta di uno scenario in cui si usa la replica di sistema SAP HANA allo scopo di spostare i dati in modo sincrono per ottenere un obiettivo del punto di ripristino (RPO) pari a 0. D'altro canto, l'obiettivo del tempo di ripristino (RTO) è tale da non comportare la necessità di effettuare il failover o precaricare i dati nella cache dell'istanza di HANA. In tal caso, è possibile ottenere una configurazione più conveniente in termini di risparmio con le operazioni seguenti:

- È possibile eseguire un'altra istanza di SAP HANA nella seconda macchina virtuale che accetta la maggior parte della memoria della macchina virtuale. In genere, un'istanza di questo tipo è un'istanza che in caso di failover alla seconda macchina virtuale potrebbe essere arrestata. Di conseguenza, i dati replicati possono essere caricati nella cache dell'istanza HANA di destinazione nella seconda macchina virtuale.
- È possibile usare dimensioni della macchina virtuale inferiori come seconda macchina virtuale. In caso di failover, è necessario un ulteriore passaggio prima del failover manuale in cui la macchina virtuale viene ridimensionata in base alle dimensioni della macchina virtuale di origine. Lo scenario è simile al seguente:

![Due macchine virtuali con replica di archiviazione](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Anche senza precaricamento dei dati nella destinazione della replica di sistema HANA, sono necessari almeno 64 GB di memoria, oltre a una quantità di memoria sufficiente per mantenere i dati rowstore nella memoria dell'istanza di destinazione.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>Uso della replica di sistema HANA senza failover automatico e con precaricamento dei dati
La differenza con lo scenario presentato in precedenza è che i dati che vengono replicati nell'istanza HANA nella seconda macchina virtuale sono precaricati. In questo modo vengono eliminati i due vantaggi che è possibile ottenere con lo scenario che non prevede il precaricamento dei dati. In questo caso, non è possibile eseguire un altro sistema SAP HANA nella seconda macchina virtuale, né usare dimensioni della macchina virtuale inferiori. Di conseguenza, si tratta di uno scenario che difficilmente viene implementato presso i clienti.


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Uso della replica di sistema SAP HANA con failover automatico

La configurazione di disponibilità standard all'interno di un'area di Azure che la maggior parte dei clienti implementa con SAP HANA è una configurazione in cui le due macchine virtuali di Azure che eseguono SLES Linux dispongono di un cluster di failover definito. Il cluster Linux di SLES è basato sul framework [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) in combinazione con un dispositivo [STONITH](http://linux-ha.org/wiki/STONITH). Da una prospettiva SAP HANA, la modalità di replica usata è sincronizzata ed è configurato un failover automatico. Nella seconda macchina virtuale l'istanza di SAP HANA agisce come nodo di standby a caldo, che riceve un flusso sincrono di record di modifica dall'istanza di SAP HANA primaria. Mentre l'applicazione esegue il commit delle transazioni in corrispondenza del nodo HANA primario, il nodo HANA primario attende per confermare il commit all'applicazione fino a quando il nodo SAP HANA secondario non conferma di avere ricevuto il record di commit. SAP HANA presenta due diverse modalità di replica sincrona. Per informazioni dettagliate e per comprendere le differenze tra queste due modalità di replica sincrona, leggere l'articolo [Replication modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html) (Modalità di replica per la replica di sistema SAP HANA).

La configurazione complessiva ha un aspetto simile al seguente:

![Due macchine virtuali con replica di archiviazione e failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Questa soluzione viene scelta perché consente di ottenere un RPO pari a 0 e tempi di RTO estremamente bassi. Configurare la connettività client SAP HANA in modo che i client SAP HANA usino l'indirizzo IP virtuale per la connessione alla configurazione della replica di sistema HANA. In questo modo viene meno la necessità di riconfigurare l'applicazione in caso di failover al nodo secondario. In questa soluzione gli SKU della macchina virtuale di Azure per il database primario o secondario devono essere gli stessi.  


## <a name="next-steps"></a>Passaggi successivi
Se sono necessarie indicazioni dettagliate su come impostare una configurazione di questo tipo in Azure, leggere gli articoli:

- [Configurare la replica di sistema SAP HANA nelle macchine virtuali di Azure](sap-hana-high-availability.md)
- [Your SAP on Azure – Part 4 – High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (SAP in Azure – Parte 4 – Disponibilità elevata per SAP HANA tramite la replica di sistema)

Se sono necessarie altre informazioni sulla disponibilità di SAP HANA tra le aree di Azure, leggere:

- [SAP HANA Availability across Azure regions](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) (Disponibilità di SAP HANA tra aree di Azure) 

