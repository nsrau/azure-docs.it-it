---
title: Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come proteggere le distribuzioni di applicazioni SAP NetWeaver con Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: asgang
ms.openlocfilehash: 27dfdec4e833a2f30963157ba2f4d95232e21270
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267333"
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Site Recovery

La maggior parte delle distribuzioni SAP di medie e grandi dimensioni prevede una soluzione di ripristino di emergenza. L'importanza di soluzioni di ripristino di emergenza efficaci e testabili è aumentata con lo spostamento di un numero sempre maggiore di processi aziendali importanti in applicazioni come SAP. Azure Site Recovery è stata testato e integrato con le applicazioni SAP. Site Recovery supera le capacità della maggior parte delle soluzioni di ripristino di emergenza locali, a un costo totale di proprietà inferiore rispetto alle soluzioni della concorrenza.

Con Site Recovery è possibile:
* **Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale**, eseguendo la replica dei componenti in Azure.
* **Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure**, eseguendo la replica dei componenti in un altro data center di Azure.
* **Semplificare la migrazione nel cloud** usando Site Recovery per eseguire la migrazione della distribuzione SAP ad Azure.
* **Semplificare gli aggiornamenti, i test e la creazione di prototipi dei progetti SAP**, creando un clone di produzione on demand per i test delle applicazioni SAP.

Questo articolo descrive come proteggere le distribuzioni di applicazioni SAP NetWeaver con [Azure Site Recovery](site-recovery-overview.md). L'articolo illustra le procedure consigliate per la protezione di una distribuzione SAP NetWeaver a tre livelli in Azure eseguendo la replica in un altro data center di Azure con Site Recovery. Descrive gli scenari e le configurazioni supportati e la procedura per eseguire failover di test (analisi ripristino di emergenza) e failover effettivi.

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare, assicurarsi di conoscere le procedure per eseguire le attività seguenti:

* [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Progettare una rete di ripristino](site-recovery-azure-to-azure-networking-guidance.md)
* [Eseguire un failover di test in Azure](azure-to-azure-walkthrough-test-failover.md)
* [Eseguire un failover in Azure](site-recovery-failover.md)
* [Replicare un controller di dominio](site-recovery-active-directory.md)
* [Replicare SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenari supportati
Con Site Recovery è possibile implementare una soluzione di ripristino di emergenza per gli scenari seguenti:
* Sistemi SAP in esecuzione in un data center di Azure con replica in un altro data center di Azure (ripristino di emergenza da Azure ad Azure). Per altre informazioni, vedere [Azure-to-Azure replication architecture](https://aka.ms/asr-a2a-architecture) (Architettura di replica da Azure ad Azure).
* Sistemi SAP in esecuzione su server VMware (o fisici) locali che eseguono la replica a un sito di ripristino di emergenza in un data Center di Azure (ripristino di emergenza da VMware ad Azure). Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [VMware-to-Azure replication architecture](https://aka.ms/asr-v2a-architecture) (Architettura di replica da VMware ad Azure).
* Sistemi SAP in esecuzione su server Hyper-V locali che eseguono la replica a un sito di ripristino di emergenza in un data Center di Azure (ripristino di emergenza da Hyper-V ad Azure). Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [Hyper-V-to-Azure replication architecture](https://aka.ms/asr-h2a-architecture) (Architettura di replica da Hyper-V ad Azure).

In questo articolo viene usato un scenario di ripristino di emergenza da Azure ad Azure per illustrare le funzionalità di ripristino di emergenza SAP di Site Recovery. Poiché la replica di Site Recovery non varia in base all'applicazione, il processo descritto dovrebbe applicarsi anche ad altri scenari.

### <a name="required-foundation-services"></a>Servizi di base necessari
Nello scenario affrontato in questo articolo vengono distribuiti i servizi di base seguenti:
* Azure ExpressRoute o gateway VPN di Azure
* Almeno un controller di dominio Active Directory e un server DNS in esecuzione in Azure

È consigliabile definire questa infrastruttura prima di distribuire Site Recovery.

## <a name="reference-sap-application-deployment"></a>Riferimento alla distribuzione di un'applicazione SAP

Questa architettura di riferimento mostra l'esecuzione di SAP NetWeaver in un ambiente Windows su Azure con disponibilità elevata.  Questa architettura viene distribuita con dimensioni di macchina virtuale (VM) specifiche, che possono essere modificate in base alle esigenze dell'organizzazione.

![Diagramma di un tipico criterio di distribuzione SAP](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza

Per il ripristino di emergenza, è necessario essere in grado di eseguire il failover in un'area secondaria. Ogni livello usa una strategia diversa per fornire una protezione con ripristino di emergenza.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Macchine virtuali che eseguono pool di componenti SAP Web Dispatcher 
Il componente Web Dispatcher viene usato come servizio di bilanciamento del carico per il traffico SAP tra i server applicazioni SAP. Per ottenere la disponibilità elevata per il componente Web Dispatcher, viene usato Azure Load Balancer per implementare la configurazione di Web Dispatcher parallela in una configurazione round robin per la distribuzione del traffico HTTP(S) tra i componenti Web Dispatcher disponibili nel pool di bilanciamento del carico. Questo verrà replicato tramite Azure Site Recovery(ASR) e gli script di automazione consentiranno di configurare il bilanciamento del carico nell'area del ripristino di emergenza. 

####<a name="vms-running-application-servers-pool"></a>Macchina virtuale che esegue il pool di server applicazioni
Per gestire i gruppi di accesso per i server applicazioni ABAP, viene usata la transazione SMLG. Questa usa la funzione di bilanciamento del carico nel server messaggi di Central Services per distribuire il carico di lavoro nel pool di server applicazioni SAP per le interfacce utente grafiche di SAP e il traffico RFC. Questo verrà replicato tramite Azure Site Recovery 

####<a name="vms-running-sap-central-services-cluster"></a>Macchina virtuale che esegue Cluster SAP Central Services
Questa architettura di riferimento esegue Central Services in macchine virtuali nel livello applicazione. Central Services è un singolo punto di errore potenziale se distribuito in un'unica macchina virtuale, che è la distribuzione tipica quando la disponibilità elevata non è un requisito.<br>

Per implementare una soluzione a disponibilità elevata, può essere usato un cluster di dischi condiviso o un cluster di condivisione file. Per configurare le macchine virtuali per un cluster di dischi condivisi, usare il Cluster di Failover di Windows Server. Cloud Witness è consigliato come quorum di controllo. 
 > [!NOTE]
 > Azure Site Recovery non replica il server di controllo cloud pertanto si consiglia di distribuire il server di controllo cloud nell'area di ripristino di emergenza.

Per supportare l'ambiente cluster di failover, [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) svolge la funzione di volume condiviso del cluster replicando i dischi indipendenti di proprietà dei nodi del cluster. Azure non supporta in modo nativo i dischi condivisi e di conseguenza richiede soluzioni fornite da SIOS. 

Un altro modo di gestire il clustering consiste nell'implementare un cluster di condivisioni file. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ha di recente modificato il modello di distribuzione di Central Services in modo da permettere l'accesso alle directory globali /sapmnt tramite un percorso UNC. Questa modifica elimina il requisito relativo a SIOS o ad altre soluzioni con dischi condivisi nelle macchine virtuali di Central Services. È comunque consigliabile assicurarsi che la condivisione UNC /sapmnt abbia disponibilità elevata. A questo scopo, nell'istanza di Central Services usare Windows Server Failover Cluster con File server di scalabilità orizzontale e la funzionalità Storage Spaces Direct in Windows Server 2016. 
 > [!NOTE]
 > Attualmente il supporto di Azure Site Recovery arresta solo la replica del punto consistente delle macchine virtuali tramite gli spazi di archiviazione diretti 


## <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza

È possibile usare Azure Site Recovery per orchestrare il failover della distribuzione completa di SAP nelle aree di Azure.
Di seguito è riportata la procedura per la configurazione del ripristino di emergenza 

1. Replicare le macchine virtuali 
2. Progettare una rete di ripristino
3.  Replicare un controller di dominio
4.  Replicare un livello dati di base 
5.  Eseguire un failover di test 
6.  Eseguire un failover 

Di seguito è presente l'indicazione per il ripristino di emergenza di ogni livello usato in questo esempio. 

 **Livelli di SAP** | **Consiglio**
 --- | ---
**Pool di componenti SAP Web Dispatcher** |  Replica con Site Recovery 
Pool di server applicazioni SAP |  Replica con Site Recovery 
**Cluster SAP Central Services** |  Replica con Site Recovery 
**Macchine virtuali di Active directory** |  Replica di Active Directory 
**Server di database SQL** |  Replica di SQL AlwaysOn

##<a name="replicate-virtual-machines"></a>Replicare le macchine virtuali

Per avviare la replica di tutte le macchine virtuali dell'applicazione SAP al data center di ripristino di emergenza di Azure, seguire le indicazioni in [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md).


* Per le linee guida sulla protezione di Active Directory e DNS, fare riferimento al documento [Proteggere Active Directory e DNS con Azure Site Recovery](site-recovery-active-directory.md).

* Per le linee guida sulla protezione del livello database in esecuzione in SQL Server, fare riferimento al documento [Proteggere SQL Server](site-recovery-active-directory.md).

## <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

Se si usa un indirizzo IP statico, è possibile specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale. Per impostare l'indirizzo IP, passare a **Compute and Network settings** > **Network interface card** (Impostazioni Calcolo e rete, Scheda interfaccia di rete).

![Schermata che illustra come impostare un indirizzo IP privato nel riquadro della scheda di interfaccia di rete di Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino
Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Aggiunta di macchine virtuali a gruppi di failover

1.  Creare un piano di ripristino aggiungendo il server applicazioni, il dispatcher web e le macchine virtuali dei servizi centrali SAP.
2.  Fare clic su "Personalizza" per raggruppare le VM. Per impostazione predefinita, tutte le VM fanno parte di "Gruppo 1".



### <a name="add-scripts-to-the-recovery-plan"></a>Aggiungere script al piano di ripristino
Per far sì che le applicazioni funzionino correttamente, potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o durante un failover di test. È possibile automatizzare alcune operazioni successive al failover. È ad esempio possibile aggiornare la voce DNS e modificare associazioni e connessioni aggiungendo gli script corrispondenti al piano di ripristino.


È possibile distribuire gli script di Azure Site Recovery usate più comunemente nell'account di Automazione facendo clic sul pulsante "Distribuisci in Azure" di seguito. Quando si usa uno script pubblicato, assicurarsi di seguire le istruzioni nello script.

[![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Aggiungere uno script precedente all'azione a "Gruppo 1" per il gruppo di disponibilità SQL. Usare lo script "ASR-SQL-FailoverAG" pubblicato negli script di esempio. Assicurarsi di seguire le istruzioni nello script e apportare le modifiche necessarie nello script nel modo appropriato.
2. Aggiungere uno script successivo all'azione per collegare un servizio di bilanciamento del carico nelle macchine virtuali sottoposte a failover di livello Web (Gruppo 1). Usare lo script "ASR-AddSingleLoadBalancer" pubblicato negli script di esempio. Assicurarsi di seguire le istruzioni nello script e apportare le modifiche necessarie nello script nel modo appropriato.

![Piano di ripristino SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Eseguire un failover di test

1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato per le applicazioni SAP.
3.  Selezionare **Failover di test**.
4.  Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
5.  Quando l'ambiente secondario diventa disponibile, eseguire le convalide.
6.  Al termine delle convalide, per pulire l'ambiente di failover di test, selezionare **Pulisci failover di test**.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato per le applicazioni SAP.
3.  Selezionare **Failover**.
4.  Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione di una soluzione di ripristino di emergenza per le distribuzioni SAP NetWeaver con Site Recovery, vedere il white paper scaricabile [SAP NetWeaver - Creazione di una soluzione di ripristino di emergenza con Azure Site Recovery](http://aka.ms/asr-sap). Il white paper include consigli per diverse architetture SAP, elenca le applicazioni e i tipi di VM supportati per SAP in Azure e descrive le opzioni dei piani di test per la soluzione di ripristino di emergenza.
* Sono disponibili altre informazioni sulla [replica di altri carichi di lavoro](site-recovery-workload.md) con Site Recovery.
