---
title: Configurare il ripristino di emergenza di SAP NetWeaver con Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza per SAP NetWeaver con Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77190790"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurare il ripristino di emergenza per una distribuzione di applicazioni SAP NetWeaver multilivello

La maggior parte delle distribuzioni SAP di medie e grandi dimensioni prevede una soluzione di ripristino di emergenza. L'importanza di soluzioni di ripristino di emergenza efficaci e testabili è aumentata con lo spostamento di un numero sempre maggiore di processi aziendali importanti in applicazioni come SAP. Azure Site Recovery è stata testato e integrato con le applicazioni SAP. Site Recovery supera le funzionalità della maggior parte delle soluzioni di ripristino di emergenza locali e a un costo totale di proprietà inferiore rispetto alle soluzioni in competizione.

Con Site Recovery è possibile:
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale, eseguendo la replica dei componenti in Azure.
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure, eseguendo la replica dei componenti in un altro data center di Azure.
* Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
* Semplifica gli aggiornamenti, i test e la creazione di prototipi del progetto SAP creando un clone di produzione su richiesta per il test delle applicazioni SAP.

È possibile proteggere le distribuzioni di applicazioni SAP NetWeaver usando [Azure Site Recovery](site-recovery-overview.md). Questo articolo illustra le procedure consigliate per la protezione di una distribuzione SAP NetWeaver a tre livelli in Azure quando si esegue la replica in un altro Data Center di Azure usando Site Recovery. Questo articolo descrive gli scenari e le configurazioni supportati e illustra come eseguire i failover di test (drill-ripristino di emergenza) e i failover effettivi.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di conoscere le procedure per eseguire le attività seguenti:

* [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Progettare una rete di ripristino](site-recovery-azure-to-azure-networking-guidance.md)
* [Eseguire un failover di test in Azure](azure-to-azure-walkthrough-test-failover.md)
* [Eseguire un failover in Azure](site-recovery-failover.md)
* [Replicare un controller di dominio](site-recovery-active-directory.md)
* [Replicare un'istanza di SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenari supportati

Con Site Recovery è possibile implementare una soluzione di ripristino di emergenza per gli scenari seguenti:
* I sistemi SAP sono in esecuzione in un Data Center di Azure e vengono replicati in un altro Data Center di Azure (ripristino di emergenza da Azure ad Azure). 
   Per altre informazioni, vedere [Azure-to-Azure replication architecture](https://aka.ms/asr-a2a-architecture) (Architettura di replica da Azure ad Azure).
* Sono presenti sistemi SAP in esecuzione su server VMware (o fisici) in locale. Si sta anche replicando i sistemi SAP in un sito di ripristino di emergenza in un Data Center di Azure (ripristino di emergenza da VMware ad Azure). 
   Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [VMware-to-Azure replication architecture](https://aka.ms/asr-v2a-architecture) (Architettura di replica da VMware ad Azure).
* Sono presenti sistemi SAP in esecuzione in Hyper-V in locale. Si sta anche replicando i sistemi SAP in un sito di ripristino di emergenza in un Data Center di Azure (ripristino di emergenza da Hyper-V ad Azure).
   Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [Hyper-V-to-Azure replication architecture](https://aka.ms/asr-h2a-architecture) (Architettura di replica da Hyper-V ad Azure).

In questo articolo viene usato uno scenario **di ripristino di emergenza da Azure ad Azure** . In questo scenario vengono illustrate le funzionalità di ripristino di emergenza di SAP di Site Recovery. Poiché la replica di Site Recovery non varia in base all'applicazione, il processo descritto dovrebbe applicarsi anche ad altri scenari.

### <a name="required-foundation-services"></a>Servizi di base necessari
Nello scenario affrontato in questo articolo vengono distribuiti i servizi di base seguenti:
* Azure ExpressRoute o gateway VPN di Azure
* Almeno un controller di dominio Azure Active Directory e un server DNS in esecuzione in Azure

È consigliabile definire questa infrastruttura prima di distribuire Site Recovery.

## <a name="reference-sap-application-deployment"></a>Riferimento alla distribuzione di un'applicazione SAP

Questa architettura di riferimento esegue SAP NetWeaver in un ambiente Windows in Azure con disponibilità elevata. Questa architettura viene distribuita con dimensioni specifiche della macchina virtuale (VM) che è possibile modificare per soddisfare le esigenze dell'organizzazione.

![Diagramma di un tipico criterio di distribuzione SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza

Per il ripristino di emergenza, è necessario essere in grado di eseguire il failover in un'area secondaria. Ogni livello usa una strategia diversa per fornire protezione per il ripristino di emergenza.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>VM che eseguono pool di Dispatcher Web di SAP

Il componente Dispatcher Web funziona come bilanciamento del carico per il traffico SAP tra i server applicazioni SAP. Per ottenere la disponibilità elevata per il componente Dispatcher Web, Azure Load Balancer implementa la configurazione di Dispatcher Web Parallel. Il dispatcher Web usa una configurazione Round Robin per la distribuzione del traffico HTTP (S) tra i dispatcher Web disponibili nel pool di bilanciamento del servizio.

#### <a name="vms-running-application-servers-pools"></a>Macchine virtuali che eseguono pool di server applicazioni
La transazione SMLG gestisce i gruppi di accesso per i server applicazioni ABAP. Usa la funzione di bilanciamento del carico nel server dei messaggi dei servizi centrali per distribuire il carico di lavoro tra i pool di server applicazioni SAP per il traffico SAPGUIs e RFC. È possibile replicare questa gestione usando Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Macchine virtuali che eseguono cluster SAP Central Services
Questa architettura di riferimento esegue Central Services in macchine virtuali nel livello applicazione. Servizi centrali è un potenziale singolo punto di errore quando si trova in una singola macchina virtuale. La distribuzione tipica e la disponibilità elevata non sono requisiti.

Per implementare una soluzione a disponibilità elevata, è possibile usare un cluster di dischi condivisi o un cluster di condivisione file. Per configurare le macchine virtuali per un cluster di dischi condivisi, usare Windows Server Failover Cluster. Si consiglia di usare il cloud Witness come quorum di controllo.

 > [!NOTE]
 > Poiché Site Recovery non replica il cloud di controllo, è consigliabile distribuire il server di controllo del cloud nell'area di ripristino di emergenza.

Per supportare l'ambiente del cluster di [failover, la](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) funzione del volume condiviso del cluster è la funzione del volume condiviso del cluster. Nella funzione, il cluster di indicizzazione datakeeper replica i dischi indipendenti di proprietà dei nodi del cluster. Poiché Azure non supporta in modo nativo i dischi condivisi, richiede le soluzioni fornite da questo.

È anche possibile gestire il clustering implementando un cluster di condivisione file. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ha di recente modificato il modello di distribuzione di Central Services in modo da permettere l'accesso alle directory globali /sapmnt tramite un percorso UNC. È comunque consigliabile assicurarsi che la condivisione UNC/sapmnt sia a disponibilità elevata. È possibile controllare l'istanza di servizi centrali. Usare Windows Server failover cluster con Scale Out file server (SOFS) e la funzionalità Spazi di archiviazione diretta (S2D) in Windows Server 2016.

 > [!NOTE]
 > Site Recovery attualmente supporta solo la replica di punti coerenti con l'arresto anomalo del sistema delle macchine virtuali che usano spazi di archiviazione diretta e il nodo passivo di datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Ulteriori considerazioni sul ripristino di emergenza

È possibile usare Site Recovery per orchestrare il failover della distribuzione SAP completa tra le aree di Azure.
Di seguito sono riportati i passaggi per la configurazione del ripristino di emergenza:

1. Replicare le macchine virtuali
1. Progettare una rete di ripristino
1. Replicare un controller di dominio
1. Replicare un livello dati di base
1. Eseguire un failover di test
1. Eseguire un failover

Di seguito è riportata la raccomandazione per il ripristino di emergenza di ogni livello utilizzato in questo esempio.

 **Livelli di SAP** | **Consiglio**
 --- | ---
**Pool di componenti SAP Web Dispatcher** |  Eseguire la replica tramite Site Recovery 
**Pool di server applicazioni SAP** |  Eseguire la replica tramite Site Recovery 
**Cluster SAP Central Services** |  Eseguire la replica tramite Site Recovery 
**Macchine virtuali di Active directory** |  Usare la replica di Active Directory 
**Server di database SQL** |  Usare la replica SQL Server Always On

## <a name="replicate-virtual-machines"></a>Replicare le macchine virtuali

Per avviare la replica di tutte le macchine virtuali dell'applicazione SAP al data center di ripristino di emergenza di Azure, seguire le indicazioni in [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md).

* Per informazioni sulla protezione di Active Directory e DNS, vedere la pagina relativa [alla protezione di Active Directory e DNS](site-recovery-active-directory.md).

* Per informazioni sulla protezione del livello di database in esecuzione in SQL Server, vedere la pagina relativa [alla protezione di SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

Se si usa un indirizzo IP statico, è possibile specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale. Per impostare l'indirizzo IP, passare a **Impostazioni calcolo e rete**  >  **scheda di interfaccia di rete**.

![Schermata che illustra come impostare un indirizzo IP privato nel riquadro della scheda di interfaccia di rete di Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Aggiungere macchine virtuali a gruppi di failover

1. Creare un piano di ripristino aggiungendo il server applicazioni, il dispatcher Web e le VM dei servizi centrali SAP.
1. Selezionare **Personalizza** per raggruppare le macchine virtuali. Per impostazione predefinita, tutte le VM fanno parte del gruppo 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Aggiungere script al piano di ripristino
Per il corretto funzionamento delle applicazioni, potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure. Eseguire queste operazioni dopo il failover o durante un failover di test. È anche possibile automatizzare alcune operazioni successive al failover. Ad esempio, aggiornare la voce DNS e modificare le associazioni e le connessioni aggiungendo gli script corrispondenti al piano di ripristino.

È possibile distribuire gli script di Site Recovery più usati nell'account di automazione di Azure selezionando **Distribuisci in Azure**. Quando si usa uno script pubblicato, seguire le istruzioni nello script.

[![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Aggiungere uno script di pre-azione al gruppo 1 per eseguire il failover del gruppo di disponibilità SQL Server. Usare lo script ASR-SQL-FailoverAG pubblicato negli script di esempio. Seguire le istruzioni nello script e apportare le modifiche necessarie nello script in modo appropriato.
1. Aggiungere uno script post-azione per il collegamento di un servizio di bilanciamento del carico alle macchine virtuali di cui è stato eseguito il failover del livello Web (gruppo 1). Usare lo script ASR-AddSingleLoadBalancer pubblicato negli script di esempio. Seguire le istruzioni nello script e apportare le modifiche necessarie nello script in base alle esigenze.

![Piano di ripristino SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
1. Selezionare il piano di ripristino creato per le applicazioni SAP.
1. Selezionare **Failover di test**.
1. Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
1. Quando l'ambiente secondario diventa disponibile, eseguire le convalide.
1. Al termine delle convalide, pulire l'ambiente di failover selezionando **Pulisci failover di test**.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1. Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
1. Selezionare il piano di ripristino creato per le applicazioni SAP.
1. Selezionare **Failover**.
1. Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla creazione di una soluzione di ripristino di emergenza per le distribuzioni di SAP NetWeaver con Site Recovery. Vedere la pagina relativa alla [creazione di una soluzione di ripristino di emergenza con Site Recovery di white paper SAP NetWeaver](https://aka.ms/asr_sap). Il white paper illustra le raccomandazioni per diverse architetture SAP. È possibile visualizzare le applicazioni e i tipi di VM supportati per SAP in Azure. Sono inoltre disponibili opzioni di piano per il test della soluzione di ripristino di emergenza.
* Sono disponibili altre informazioni sulla [replica di altri carichi di lavoro](site-recovery-workload.md) con Site Recovery.
