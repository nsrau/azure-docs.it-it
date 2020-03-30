---
title: Configurare il ripristino di emergenza SAP NetWeaver con Azure Site RecoverySet up SAP NetWeaver disaster recovery with Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza per SAP NetWeaver con Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190790"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurare il ripristino di emergenza per una distribuzione di applicazioni SAP NetWeaver multilivello

La maggior parte delle distribuzioni SAP di medie e grandi dimensioni prevede una soluzione di ripristino di emergenza. L'importanza di soluzioni di ripristino di emergenza efficaci e testabili è aumentata con lo spostamento di un numero sempre maggiore di processi aziendali importanti in applicazioni come SAP. Azure Site Recovery è stata testato e integrato con le applicazioni SAP. Site Recovery supera le funzionalità della maggior parte delle soluzioni di ripristino di emergenza locali e a un costo totale di proprietà inferiore rispetto alle soluzioni concorrenti.

Con Site Recovery è possibile:
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale, eseguendo la replica dei componenti in Azure.
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure, eseguendo la replica dei componenti in un altro data center di Azure.
* Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
* Semplifica gli aggiornamenti, i test e la creazione di prototipi dei progetti SAP creando un clone di produzione su richiesta per il test delle applicazioni SAP.

È possibile proteggere le distribuzioni di applicazioni SAP NetWeaver usando [Azure Site Recovery.](site-recovery-overview.md) Questo articolo illustra le procedure consigliate per la protezione di una distribuzione SAP NetWeaver a tre livelli in Azure quando si esegue la replica in un altro data center di Azure tramite Site Recovery.This article covers best practices for protecting a three-tier SAP NetWeaver deployment on Azure when you replicate to another Azure datacenter by using Site Recovery. L'articolo descrive gli scenari e le configurazioni supportati e come eseguire failover di test (esercitazioni di ripristino di emergenza) e failover effettivi.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di conoscere le procedure per eseguire le attività seguenti:

* [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Progettare una rete di ripristino](site-recovery-azure-to-azure-networking-guidance.md)
* [Eseguire un failover di test in Azure](azure-to-azure-walkthrough-test-failover.md)
* [Eseguire un failover in Azure](site-recovery-failover.md)
* [Replicare un controller di dominio](site-recovery-active-directory.md)
* [Replicare un'istanza di SQL ServerReplicate a SQL Server instance](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenari supportati

Con Site Recovery è possibile implementare una soluzione di ripristino di emergenza per gli scenari seguenti:
* Si dispone di sistemi SAP in esecuzione in un data center di Azure e si esegue la replica in un altro data center di Azure (ripristino di emergenza da Azure ad Azure). 
   Per altre informazioni, vedere [Azure-to-Azure replication architecture](https://aka.ms/asr-a2a-architecture) (Architettura di replica da Azure ad Azure).
* Si dispone di sistemi SAP in esecuzione su server VMware (o fisici) in locale. Si stanno anche replicando i sistemi SAP in un sito di ripristino di emergenza in un data center di Azure (ripristino di emergenza da VMware ad Azure). 
   Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [VMware-to-Azure replication architecture](https://aka.ms/asr-v2a-architecture) (Architettura di replica da VMware ad Azure).
* Si dispone di sistemi SAP in esecuzione in Hyper-V locale. Si stanno anche replicando i sistemi SAP in un sito di ripristino di emergenza in un data center di Azure (ripristino di emergenza da Hyper-V ad Azure).
   Questo scenario prevede alcuni componenti aggiuntivi. Per altre informazioni, vedere [Hyper-V-to-Azure replication architecture](https://aka.ms/asr-h2a-architecture) (Architettura di replica da Hyper-V ad Azure).

In questo articolo viene usato uno scenario di ripristino di emergenza da Azure ad Azure.In this article, we use an **Azure-to-Azure** disaster recovery scenario. Lo scenario mostra le funzionalità di ripristino di emergenza SAP di Site Recovery. Poiché la replica di Site Recovery non varia in base all'applicazione, il processo descritto dovrebbe applicarsi anche ad altri scenari.

### <a name="required-foundation-services"></a>Servizi di base necessari
Nello scenario affrontato in questo articolo vengono distribuiti i servizi di base seguenti:
* Azure ExpressRoute o gateway VPN di Azure
* Almeno un controller di dominio di Azure Active Directory e un server DNS in esecuzione in Azure

È consigliabile definire questa infrastruttura prima di distribuire Site Recovery.

## <a name="reference-sap-application-deployment"></a>Riferimento alla distribuzione di un'applicazione SAP

Questa architettura di riferimento esegue SAP NetWeaver in un ambiente Windows in Azure con disponibilità elevata. Questa architettura viene distribuita con dimensioni di macchine virtuali (VM) specifiche che è possibile modificare per soddisfare le esigenze dell'organizzazione.

![Diagramma di un tipico criterio di distribuzione SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza

Per il ripristino di emergenza, è necessario essere in grado di eseguire il failover in un'area secondaria. Ogni livello usa una strategia diversa per fornire protezione per il ripristino di emergenza.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Macchine virtuali che eseguono pool SAP Web Dispatcher

Il componente Web Dispatcher funziona come un servizio di bilanciamento del carico per il traffico SAP tra i server applicazioni SAP. Per ottenere un'elevata disponibilità per il componente Web Dispatcher, Azure Load Balancer implementa la configurazione parallela di Web Dispatcher. Web Dispatcher utilizza una configurazione round robin per la distribuzione del traffico HTTP(S) tra i dispatcher Web disponibili nel pool di servizi di bilanciamento.

#### <a name="vms-running-application-servers-pools"></a>Macchine virtuali che eseguono pool di server applicazioni
La transazione SMLG gestisce i gruppi di accesso per i server applicazioni ABAP. Utilizza la funzione di bilanciamento del carico all'interno del server dei messaggi dei servizi centrali per distribuire il carico di lavoro tra i pool di server applicazioni SAP per il traffico SAPGUIs e RFC. È possibile replicare questa gestione utilizzando Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Macchine virtuali che eseguono cluster SAP Central Services
Questa architettura di riferimento esegue Central Services in macchine virtuali nel livello applicazione. Servizi centrali è un potenziale singolo punto di errore quando si è in una singola macchina virtuale. La distribuzione tipica e la disponibilità elevata non sono requisiti.

Per implementare una soluzione a disponibilità elevata, è possibile usare un cluster di dischi condivisi o un cluster di condivisione file. Per configurare le macchine virtuali per un cluster di dischi condivisi, usare Windows Server Failover Cluster. È consigliabile usare il cloud di controllo come quorum di controllo.

 > [!NOTE]
 > Poiché Site Recovery non replica il cloud di controllo, è consigliabile distribuire il cloud di controllo nell'area di ripristino di emergenza.

Per supportare l'ambiente cluster di failover, [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) esegue il funzionamento del volume condiviso del cluster. Nella funzione, SIOS DataKeeper Cluster replica i dischi indipendenti di proprietà dei nodi del cluster. Poiché Azure non supporta in modo nativo i dischi condivisi, richiede soluzioni fornite da SIOS.

È inoltre possibile gestire il clustering implementando un cluster di condivisione file. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) ha di recente modificato il modello di distribuzione di Central Services in modo da permettere l'accesso alle directory globali /sapmnt tramite un percorso UNC. È comunque consigliabile assicurarsi che la condivisione UNC /sapmnt sia a disponibilità elevata. È possibile controllare l'istanza di Central Services. Usare Cluster di Failover di Windows Server con il file server di scalabilità orizzontale (SOFS) e la funzionalità Storage Spaces Direct (S2D) in Windows Server 2016.Use Windows Server Failover Cluster with Scale Out File Server (SOFS) and the Storage Spaces Direct (S2D) feature in Windows Server 2016.

 > [!NOTE]
 > Site Recovery supporta attualmente solo la replica dei punti con coerenza degli arresti anomali delle macchine virtuali che utilizzano spazi di archiviazione diretti e il nodo passivo di SIOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Altre considerazioni sul ripristino di emergenza

È possibile usare Site Recovery per orchestrare il failover della distribuzione SAP completa tra le aree di Azure.You can use Site Recovery to orchestrate the failover of full SAP deployment across Azure regions.
Di seguito sono riportati i passaggi per la configurazione del ripristino di emergenza:

1. Replicare le macchine virtuali
1. Progettare una rete di ripristino
1. Replicare un controller di dominio
1. Replicare un livello dati di base
1. Eseguire un failover di test
1. Eseguire un failover

Di seguito è riportato il consiglio per il ripristino di emergenza di ogni livello usato in questo esempio.

 **Livelli di SAP** | **Raccomandazione**
 --- | ---
**Pool di componenti SAP Web Dispatcher** |  Eseguire la replica tramite Site Recovery 
**Pool di server applicazioni SAP** |  Eseguire la replica tramite Site Recovery 
**Cluster SAP Central Services** |  Eseguire la replica tramite Site Recovery 
**Macchine virtuali di Active directory** |  Utilizzare la replica di Active Directory 
**Server di database SQL** |  Usa replica AlwaysOn di SQL Server

## <a name="replicate-virtual-machines"></a>Replicare le macchine virtuali

Per avviare la replica di tutte le macchine virtuali dell'applicazione SAP al data center di ripristino di emergenza di Azure, seguire le indicazioni in [Replicare una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md).

* Per istruzioni sulla protezione di Active Directory e DNS, vedere [come proteggere Active Directory e DNS.](site-recovery-active-directory.md)

* Per istruzioni sulla protezione del livello di database in esecuzione in SQL ServerSQL Server, vedere [come proteggere SQL Server.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

Se si usa un indirizzo IP statico, è possibile specificare l'indirizzo IP che dovrà essere usato dalla macchina virtuale. Per impostare l'indirizzo IP, passare a **Configurazione e configurazione** > **scheda di interfaccia di rete**.

![Schermata che illustra come impostare un indirizzo IP privato nel riquadro della scheda di interfaccia di rete di Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Aggiungere macchine virtuali a gruppi di failover

1. Creare un piano di ripristino aggiungendo il server applicazioni, il dispatcher Web e le macchine virtuali dei servizi SAP Central.Create a recovery plan by adding the application server, web dispatcher, and SAP Central services VMs.
1. Selezionare **Personalizza** per raggruppare le macchine virtuali. Per impostazione predefinita, tutte le macchine virtuali fanno parte del gruppo 1.By default, all VMs are part of Group 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Aggiungere script al piano di ripristino
Affinché le applicazioni funzionino correttamente, potrebbe essere necessario eseguire alcune operazioni sulle macchine virtuali di Azure.For your applications to function correctly, you might need to do some operations on the Azure virtual machines. Eseguire queste operazioni dopo il failover o durante un failover di test. È inoltre possibile automatizzare alcune operazioni successive al failover. Ad esempio, aggiornare la voce DNS e modificare i binding e le connessioni aggiungendo gli script corrispondenti al piano di ripristino.

È possibile distribuire gli script di Site Recovery più usati nell'account di Automazione di Azure selezionando **Distribuisci in Azure.** Quando si utilizza uno script pubblicato, seguire le indicazioni contenute nello script.

[![Distribuire in AzureDeploy to Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Aggiungere uno script pre-azione al gruppo 1 per eseguire il failover del gruppo di disponibilità di SQL Server.Add a pre-action script to Group 1 to fail over the SQL Server availability group. Utilizzare lo script ASR-SQL-FailoverAG pubblicato negli script di esempio. Seguire le istruzioni contenute nello script e apportare le modifiche necessarie nello script in modo appropriato.
1. Aggiungere uno script post-azione per collegare un servizio di bilanciamento del carico alle macchine virtuali di cui è stato eseguito il failover del livello Web (Gruppo 1). Usare lo script ASR-AddSingleLoadBalancer pubblicato negli script di esempio. Seguire le istruzioni nello script e apportare le modifiche necessarie nello script in base alle esigenze.

![Piano di ripristino SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Eseguire un failover di test

1. Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
1. Selezionare il piano di ripristino creato per le applicazioni SAP.
1. Selezionare **Failover di test**.
1. Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
1. Quando l'ambiente secondario diventa disponibile, eseguire le convalide.
1. Una volta completate le convalide, pulire l'ambiente di failover selezionando **Pulizia failover test**.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1. Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
1. Selezionare il piano di ripristino creato per le applicazioni SAP.
1. Selezionare **Failover**.
1. Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sulla creazione di una soluzione di ripristino di emergenza per le distribuzioni SAP NetWeaver tramite Site Recovery. Vedere il white paper scaricabile [SAP NetWeaver: Building a Disaster Recovery Solution with Site Recovery](https://aka.ms/asr_sap). Nel white paper vengono illustrati suggerimenti per varie architetture SAP. È possibile visualizzare le applicazioni supportate e i tipi di macchina virtuale per SAP in Azure.You can see supported applications and VM types for SAP on Azure. Sono inoltre disponibili opzioni di piano per testare la soluzione di ripristino di emergenza.
* Sono disponibili altre informazioni sulla [replica di altri carichi di lavoro](site-recovery-workload.md) con Site Recovery.
