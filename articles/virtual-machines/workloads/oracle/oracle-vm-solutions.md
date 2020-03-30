---
title: Soluzioni Oracle in macchine virtuali di Azure Documenti Microsoft
description: Informazioni sulle configurazioni supportate e sulle limitazioni delle immagini delle macchine virtuali Oracle in Microsoft Azure.Learn about supported configurations and limitations of Oracle virtual machine images on Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 3abc09f8c82442e3b24a9edf6ef4fb42f19dfde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806950"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Immagini di Oracle VM e relativa distribuzione in Microsoft Azure

Questo articolo illustra informazioni sulle soluzioni Oracle basate sulle immagini di macchine virtuali pubblicate da Oracle in Azure Marketplace.This article covers information about Oracle solutions based on virtual machine images published by Oracle in the Azure Marketplace. Se si è interessati alle soluzioni applicative cross-cloud con Oracle Cloud Infrastructure, vedere [Soluzioni applicative Oracle che integrano Microsoft Azure e Oracle Cloud Infrastructure](oracle-oci-overview.md).

Per ottenere un elenco delle immagini attualmente disponibili, eseguire il comando seguente:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partire da maggio 2019 sono disponibili le seguenti immagini:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Queste immagini sono considerate di tipo "Bring Your Own License" e verranno quindi addebitati solo i costi di calcolo, archiviazione e rete sostenuti durante l'esecuzione di una VM.  Si presuppone che sia disponibile una licenza idonea per l'uso di software Oracle e che sia disponibile un contratto di assistenza corrente con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Per informazioni dettagliate sulla mobilità delle licenze, vedere la nota pubblicata [Oracle and Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) (Oracle e Microsoft). 

I singoli utenti possono anche scegliere di basare le proprie soluzioni su un'immagine personalizzata creata da zero in Azure oppure caricare un'immagine personalizzata dai propri ambienti locali.

## <a name="oracle-database-vm-images"></a>Immagini della macchina virtuale del database OracleOracle database VM images
Oracle supporta l'esecuzione di Oracle Database 12.1 e versioni successive delle edizioni Standard ed Enterprise in Azure su immagini di macchine virtuali basate su Oracle Linux.  Per ottenere prestazioni ottimali per i carichi di lavoro di produzione di Oracle Database in Azure, assicurarsi di ridimensionare correttamente l'immagine della macchina virtuale e usare SSD Premium o dischi gestiti Ultra SSD. Per istruzioni su come ottenere rapidamente un database Oracle in Azure usando l'immagine della macchina virtuale pubblicata da Oracle, [provare la procedura dettagliata guida introduttiva](oracle-database-quick-create.md)al database Oracle .

### <a name="attached-disk-configuration-options"></a>Opzioni di configurazione dei dischi collegati

I dischi collegati si basano sul servizio di archiviazione BLOB di Azure. Ogni disco standard è in grado di supportare un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). I dischi premium Microsoft sono la soluzione preferibile per i carichi di lavoro di database ad alte prestazioni e possono raggiungere fino a 5000 IOPS per disco. È possibile utilizzare un singolo disco se soddisfa le esigenze di prestazioni. Tuttavia, è possibile migliorare le prestazioni di IOPS efficaci se si utilizzano più dischi collegati, distribuire i dati del database tra di essi e quindi utilizzare Oracle Automatic Storage Management (ASM). Per altre informazioni specifiche per Oracle ASM, vedere [Oracle Automatic Storage overview](https://www.oracle.com/technetwork/database/index-100339.html) (Panoramica di Oracle Automatic Storage). Per un esempio di come installare e configurare Oracle ASM in una macchina virtuale Linux Azure, vedere l'esercitazione Installazione e configurazione di Oracle Automated Storage Management.For an example of how to install and configure Oracle ASM on a Linux Azure VM, see the [Installing and Configuring Oracle Automated Storage Management](configure-oracle-asm.md) tutorial.

### <a name="shared-storage-configuration-options"></a>Opzioni di configurazione dell'archiviazione condivisa

File NetApp di Azure è stato progettato per soddisfare i requisiti principali dell'esecuzione di carichi di lavoro ad alte prestazioni come i database nel cloud e fornisce;
- Servizio di archiviazione NFS condiviso nativo di Azure per l'esecuzione di carichi di lavoro Oracle tramite il client NFS nativo della macchina virtuale o dNFS OracleAzure native shared NFS storage service for running Oracle workloads either through VM native NFS client, or Oracle dNFS
- Livelli di prestazioni scalabili che riflettono la gamma reale di richieste di IOPS
- Bassa latenza
- High availability, high durability and manageability at scale, typically demanded by mission critical enterprise workloads (like SAP and Oracle)
- Backup e ripristino rapidi ed efficienti, per ottenere il più aggressivo RTO e RPO SLA

Queste funzionalità sono possibili perché File NetApp di Azure è basato su NetApp® ONTAP® sistemi all-flash in esecuzione all'interno dell'ambiente del data center di Azure, come servizio azure Native.These capabilities are possible because Azure NetApp Files is based on NetApp® ONTAP® all-flash systems running within Azure data center environment – as an Azure Native service. Il risultato è una tecnologia di archiviazione di database ideale di cui è possibile eseguire il provisioning e l'usmazione come altre opzioni di archiviazione di Azure.The result is an ideal database storage technology that can be provisioned and consumed as just like other Azure storage options. Per altre informazioni su come distribuire e accedere ai volumi NFS dei file NetApp di Azure.See [Azure NetApp Files documentation](https://docs.microsoft.com/azure/azure-netapp-files/) for more information on how to deploy and access Azure NetApp Files NFS volumes. Per consigli sulle procedure consigliate per il funzionamento di un database Oracle in File NetApp di Azure, vedere Guida alle procedure consigliate per la [distribuzione di Oracle in Azure.See Oracle on Azure Deployment Best Practice Guide Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) for best practice recommendations for operating an Oracle database on Azure NetApp Files.


## <a name="licensing-oracle-database--software-on-azure"></a>Licenze Oracle Database & software in Azure
Microsoft Azure is an authorized cloud environment for running Oracle Database. La tabella Oracle Core Factor non è applicabile quando si concede in licenza i database Oracle nel cloud. Al contrario, quando si utilizzano macchine virtuali con tecnologia Hyper-Threading abilitata per i database Enterprise Edition, contare due VCPU come equivalente a una licenza Oracle Processor se l'hyperthreading è abilitato (come indicato nel documento dei criteri). I dettagli della politica sono disponibili [qui](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
I database Oracle richiedono in genere memoria e I/O più elevati. Per questo motivo, le [macchine virtuali con ottimizzazione per](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) la memoria sono consigliate per questi carichi di lavoro. Per ottimizzare ulteriormente i carichi di lavoro, le [vCPU Constrained Core](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) sono consigliate per i carichi di lavoro del database Oracle che richiedono una quantità elevata di memoria, archiviazione e larghezza di banda di I/O, ma non un numero elevato di core.

Quando si esegue la migrazione del software e dei carichi di lavoro Oracle da locale a Microsoft Azure, Oracle offre la mobilità delle licenze come indicato nelle domande frequenti su [Oracle in Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real Application Cluster (Oracle RAC) è progettato per ridurre l'errore di un singolo nodo in una configurazione cluster multinodo locale. Si basa su due tecnologie locali, non native agli ambienti cloud pubblici su vasta scala, ovvero il multicast di rete e i dischi condivisi. Se la soluzione di database richiede Oracle RAC in Azure, è necessario un software di terze parti per abilitare queste tecnologie. Per ulteriori informazioni su Oracle RAC, vedere la [pagina FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza
Quando si usano i database Oracle in Azure, si è responsabili dell'implementazione di una soluzione di disponibilità elevata e ripristino di emergenza per evitare tempi di inattività. 

È possibile ottenere disponibilità elevata e ripristino di emergenza per Oracle Database Enterprise Edition (senza basarsi su Oracle RAC) in Azure utilizzando [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)o Oracle [GoldenGate,](https://www.oracle.com/technetwork/middleware/goldengate)con due database in due macchine virtuali separate. Entrambe le macchine virtuali dovrebbero trovarsi nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente.  È inoltre consigliabile posizionare le macchine virtuali nello stesso set di disponibilità per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati. Se si desidera disporre della ridondanza geografica, impostare i due database per la replica tra due aree diverse e connettere le due istanze a un gateway VPN.

L'esercitazione Implement Oracle Data Guard in Azure illustra la procedura di configurazione di base in Azure.The tutorial [Implement Oracle Data Guard on Azure](configure-oracle-dataguard.md) walks you through the basic setup procedure on Azure.  

Con Oracle Data Guard, la disponibilità elevata può essere ottenuta con un database primario in una macchina virtuale, un database secondario (standby) in un'altra macchina virtuale e la replica unidirezionale configurata tra di essi. Il risultato è l'accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra due database. Per informazioni su come configurare una soluzione a disponibilità elevata per i database usando questi strumenti, vedere la documentazione di [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) e [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web di Oracle. Se è necessario accedere in lettura-scrittura alla copia del database, è possibile utilizzare [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

L'esercitazione [Implementa Oracle GoldenGate in Azure](configure-oracle-golden-gate.md) illustra la procedura di configurazione di base in Azure.The tutorial Implement Oracle GoldenGate on Azure walks you through the basic setup procedure on Azure.

Oltre a disporre di una soluzione di disponibilità del servizio registrazione problemi di emergenza e di ripristino in Azure, è necessario disporre di una strategia di backup per ripristinare il database. L'esercitazione [Backup e ripristino di un database Oracle](oracle-backup-recovery.md) illustra la procedura di base per stabilire un backup coerente.


## <a name="support-for-jd-edwards"></a>Supporto per JD Edwards
Secondo la nota del supporto Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne versioni 9.2 e successive sono supportate su **qualsiasi offerta** di cloud pubblico che soddisfi il loro specifico `Minimum Technical Requirements` (MTR).  È necessario creare immagini personalizzate che soddisfano le specifiche MTR per la compatibilità delle applicazioni con il sistema operativo e il software. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Immagini di macchine virtuali Oracle WebLogic Server

* **Il clustering è supportato solo nella Enterprise Edition.** Si dispone della licenza per l'utilizzo del clustering WebLogic solo quando si utilizza l'edizione Enterprise di Oracle WebLogic Server. Non utilizzare il clustering con Oracle WebLogic Server Standard Edition.
* **Il multicast UDP non è supportato.**  Azure supporta l'unicast UDP, ma non il multicast e il broadcast. Oracle WebLogic Server è in grado di fare affidamento sulle funzionalità unicast di Azure UDP. Per ottenere risultati ottimali basandosi su unicast UDP, è consigliabile mantenere statica o con non più di 10 server gestiti.
* **Oracle WebLogic Server prevede che le porte pubbliche e private siano le stesse per l'accesso T3 (ad esempio, quando si utilizza Enterprise JavaBeans).** Si consideri uno scenario a più livelli in cui un'applicazione del livello di servizio (EJB) è in esecuzione in un cluster Oracle WebLogic Server costituito da due o più macchine virtuali, in una rete virtuale denominata *SLWLS*. Il livello client si trova in una subnet diversa nella stessa rete virtuale, eseguendo un semplice programma Java che tenta di chiamare EJB nel livello di servizio. Poiché è necessario bilanciare il carico del livello di servizio, è necessario creare un endpoint pubblico con carico bilanciato per le macchine virtuali nel cluster Oracle WebLogic Server. Se la porta privata specificata è diversa dalla porta pubblica (ad esempio, 7006:7008), si verificherà un errore simile al seguente:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Questo avviene perché per qualsiasi accesso T3 remoto, Oracle WebLogic Server prevede che la porta di bilanciamento del carico e la porta del server gestito WebLogic siano uguali. In questo caso, il client accede alla porta 7006 (la porta del servizio di bilanciamento del carico) e il server gestito è in ascolto sulla porta 7008 (la porta privata). Questa restrizione è applicabile solo per l'accesso T3 e non per quello HTTP.

   Per evitare questo problema, utilizzare una delle soluzioni alternative seguenti:

  * Utilizzare gli stessi numeri di porta pubblica e privata per gli endpoint con carico bilanciato dedicati all'accesso T3.
  * Includere il seguente parametro JVM all'avvio di Oracle WebLogic Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Per informazioni correlate, vedere l'articolo della KB **860340.1** all'indirizzo <https://support.oracle.com>.

* **Limitazioni del clustering dinamico e del bilanciamento del carico.** Si supponga di voler usare un cluster dinamico in Oracle WebLogic Server ed esporlo tramite un singolo endpoint pubblico con carico bilanciato in Azure.Suppose you want to use a dynamic cluster in Oracle WebLogic Server and expose it through a single, public load-balanced endpoint in Azure. Questa operazione può essere eseguita purché si utilizzi un numero di porta fisso per ogni server gestito (non assegnato dinamicamente da un intervallo) e non si avviino più server gestiti rispetto ai computer di cui l'amministratore tiene traccia. Ovvero, non è disponibile più di un server gestito per macchina virtuale. Se la configurazione comporta l'avvio di più server WebLogic Oracle rispetto alle macchine virtuali, ovvero in cui più istanze di Oracle WebLogic Server condividono la stessa macchina virtuale, non è possibile per più di una di queste istanze di Server WebLogic Oracle da associare a un numero di porta specificato. Gli altri in quella macchina virtuale hanno esito negativo.

   Se si configura il server di amministrazione per assegnare automaticamente numeri di porta univoci ai server gestiti, il bilanciamento del carico non sarà possibile poiché Azure non supporta il mapping da una singola porta pubblica a più porte private, necessario per questa configurazione.
* **Più istanze di Oracle WebLogic Server in una macchina virtuale.** A seconda dei requisiti della distribuzione, è possibile eseguire più istanze di Oracle WebLogic Server nella stessa macchina virtuale, se la macchina virtuale è sufficientemente grande. Ad esempio, in una macchina virtuale di medie dimensioni, che contiene due core, è possibile scegliere di eseguire due istanze di Oracle WebLogic Server.For example, on a medium size virtual machine, which contains two cores, you could choose to run two instances of Oracle WebLogic Server. Tuttavia, è comunque consigliabile evitare di introdurre singoli punti di errore nell'architettura, come nel caso in cui sia stata utilizzata una sola macchina virtuale che esegue più istanze di Oracle WebLogic Server. L'utilizzo di almeno due macchine virtuali potrebbe essere un approccio migliore e ogni macchina virtuale potrebbe quindi eseguire più istanze di Oracle WebLogic Server. Ogni istanza di Oracle WebLogic Server potrebbe ancora far parte dello stesso cluster. Tuttavia, attualmente non è possibile usare Azure per bilanciare il carico degli endpoint esposti da tali distribuzioni di Oracle WebLogic Server all'interno della stessa macchina virtuale, poiché il servizio di bilanciamento del carico di Azure richiede la distribuzione dei server con carico bilanciato tra univoci macchine virtuali.

## <a name="oracle-jdk-virtual-machine-images"></a>Immagini di macchine virtuali Oracle JDK
* **Aggiornamenti più recenti di JDK 6 e 7.**  Sebbene sia consigliabile usare la versione pubblica supportata più recente di Java (attualmente Java 8), Azure rende disponibili anche le immagini JDK 6 e 7. Queste sono destinate alle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre gli aggiornamenti alle immagini JDK precedenti potrebbero non essere più disponibili al pubblico, considerata la partnership di Microsoft con Oracle, le immagini JDK 6 e 7 fornite da Azure sono progettate per contenere un aggiornamento non pubblico più recente che normalmente viene offerto da Oracle solo a un gruppo selezionato di clienti supportati di Oracle. Le nuove versioni delle immagini JDK verranno rese disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

   The JDK available in the JDK 6 and 7 images, and the virtual machines and images derived from them, can only be used within Azure.
* **JDK a 64 bit.**  Le immagini delle macchine virtuali Oracle WebLogic Server e le immagini delle macchine virtuali Oracle JDK fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile avere una panoramica delle soluzioni Oracle correnti basate sulle immagini delle macchine virtuali in Microsoft Azure.You have now an overview of current Oracle solutions based on virtual machine images in Microsoft Azure. Your next step is to deploy your first Oracle database on Azure.

> [!div class="nextstepaction"]
> [Creare un database Oracle in AzureCreate an Oracle database on Azure](oracle-database-quick-create.md)
