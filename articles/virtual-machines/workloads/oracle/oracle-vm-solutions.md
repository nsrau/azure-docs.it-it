---
title: Soluzioni Oracle nelle macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle configurazioni supportate e sulle limitazioni delle immagini delle macchine virtuali Oracle in Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 32e79e12eae9997df8163401d2abc5f06bc02fc0
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993494"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Immagini delle macchine virtuali Oracle e rispettiva distribuzione in Microsoft Azure

Questo articolo offre informazioni sulle soluzioni Oracle basate su immagini di macchine virtuali pubblicate da Oracle in Azure Marketplace. Se si è interessati a soluzioni di applicazioni tra cloud con Oracle Cloud Infrastructure, vedere [Soluzioni di applicazioni Oracle che integrano Microsoft Azure e Oracle Cloud Infrastructure](oracle-oci-overview.md).

Per ottenere un elenco delle immagini attualmente disponibili, eseguire il comando seguente:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partire da giugno 2020 sono disponibili le immagini seguenti:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Queste immagini sono considerate di tipo "Bring Your Own License" e verranno quindi addebitati solo i costi di calcolo, archiviazione e rete sostenuti durante l'esecuzione di una VM.  Si presuppone che sia disponibile una licenza idonea per l'uso di software Oracle e che sia disponibile un contratto di assistenza corrente con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Per informazioni dettagliate sulla mobilità delle licenze, vedere la nota pubblicata [Oracle and Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) (Oracle e Microsoft).

I singoli utenti possono anche scegliere di basare le proprie soluzioni su un'immagine personalizzata creata da zero in Azure oppure caricare un'immagine personalizzata dai propri ambienti locali.

## <a name="oracle-database-vm-images"></a>Immagini di macchine virtuali del database Oracle

Oracle supporta l'esecuzione di Oracle Database 12.1 e versioni successive Standard ed Enterprise Edition in Azure nelle immagini di macchine virtuali basate su Oracle Linux.  Per ottenere prestazioni ottimali per carichi di lavoro di produzione di Oracle Database in Azure, assicurarsi di ridimensionare in modo appropriato l'immagine della VM e di usare dischi gestiti SSD Premium o Ultra SSD. Per istruzioni su come iniziare rapidamente a usare un database Oracle in Azure tramite l'immagine della VM pubblicata da Oracle, [vedere la procedura dettagliata per l'avvio rapido del database Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opzioni di configurazione dei dischi collegati

I dischi collegati si basano sul servizio di archiviazione BLOB di Azure. Ogni disco standard è in grado di supportare un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). I dischi premium Microsoft sono la soluzione preferibile per i carichi di lavoro di database ad alte prestazioni e possono raggiungere fino a 5000 IOPS per disco. È possibile usare un singolo disco se soddisfa le esigenze di prestazioni. Tuttavia, è possibile migliorare le prestazioni delle operazioni IOPS effettive usando più dischi collegati, distribuendo tra questi dischi i dati dei database e quindi usando Oracle Automatic Storage Management (ASM). Per altre informazioni specifiche per Oracle ASM, vedere [Oracle Automatic Storage overview](https://www.oracle.com/technetwork/database/index-100339.html) (Panoramica di Oracle Automatic Storage). Per un esempio della procedura di installazione e configurazione di Oracle ASM in una VM Linux di Azure, vedere l'esercitazione [Installazione e configurazione di Oracle Automated Storage Management](configure-oracle-asm.md).

### <a name="shared-storage-configuration-options"></a>Opzioni di configurazione dell'archiviazione condivisa

Azure NetApp Files è stato progettato per soddisfare i requisiti di base per l'esecuzione di carichi di lavoro ad alte prestazioni come i database nel cloud e fornisce:

- Servizio di archiviazione NFS condiviso nativo di Azure per l'esecuzione di carichi di lavoro Oracle tramite client NFS nativo della macchina virtuale oppure Oracle dNFS
- Livelli di prestazioni scalabili che riflettono la gamma reale di richieste di IOPS
- Bassa latenza
- Disponibilità elevata, durabilità elevata e gestibilità su larga scala, in genere richiesta da carichi di lavoro aziendali cruciali, come SAP e Oracle
- Backup e ripristino rapidi ed efficienti per ottenere i contratti di servizio RTO e RPO più aggressivi possibili

Queste funzionalità sono possibili perché Azure NetApp Files si basa sui sistemi All-Flash NetApp® ONTAP® eseguiti nell'ambiente data center di Azure, come un servizio nativo di Azure. Il risultato è una tecnologia di archiviazione di database ideale di cui è possibile eseguire il provisioning e che è usabile come altre opzioni di archiviazione di Azure. Per altre informazioni su come distribuire e accedere ai volumi NFS Azure NetApp Files, vedere [Documentazione di Azure NetApp Files](../../../azure-netapp-files/index.yml). Per informazioni sulle procedure consigliate per l'uso di un database Oracle in Azure NetApp Files, vedere [Oracle on Azure Deployment Best Practice Guide Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) (Guida alle procedure consigliate per la distribuzione di Oracle in Azure con Azure NetApp Files).

## <a name="licensing-oracle-database--software-on-azure"></a>Licenze Oracle Database e software in Azure

Microsoft Azure è un ambiente cloud autorizzato per l'esecuzione di Oracle Database. La tabella Core Factor di Oracle non è applicabile alle licenze di database Oracle nel cloud. Al contrario, quando si usano macchine virtuali con tecnologia Hyper-Threading abilitata per i database Enterprise Edition, se la tecnologia è abilitata due vCPU equivalgono a una licenza del processore Oracle (come indicato nel documento dei criteri). Le informazioni dettagliate relative al criterio sono disponibili [qui](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
I database Oracle richiedono in genere una maggiore quantità di memoria e I/O. Per questo motivo, per questi carichi di lavoro sono consigliabili [macchine virtuali ottimizzate per la memoria](../../sizes-memory.md). Per ottimizzare ulteriormente i carichi di lavoro, sono consigliabili [vCPU core vincolate](../../constrained-vcpu.md) per carichi di lavoro Oracle Database che richiedono memoria, archiviazione e larghezza di banda I/O elevate, ma non un numero elevato di core.

Quando si esegue la migrazione di carichi di lavoro e software Oracle dall'ambiente locale a Microsoft Azure, Oracle fornisce la mobilità delle licenze come indicato nelle [Domande frequenti su Oracle in Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)

Oracle Real Application cluster (Oracle RAC) è progettato per ridurre l'errore di un singolo nodo in una configurazione cluster a più nodi locale. Si basa su due tecnologie locali, non native agli ambienti cloud pubblici su vasta scala, ovvero il multicast di rete e i dischi condivisi. Se per la soluzione di database è necessario Oracle RAC in Azure, è necessario un software di terze parti per abilitare tali tecnologie. Per altre informazioni su Oracle Real Application Cluster, vedere la [pagina relativa alla soluzione SkyCluster ](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza

Quando si usano i database Oracle in Azure, si è responsabili dell'implementazione di una soluzione a disponibilità elevata e con ripristino di emergenza per evitare i tempi di inattività.

La disponibilità elevata e il ripristino di emergenza per Oracle Database Enterprise Edition (senza basarsi su Oracle RAC) possono essere ottenuti su Azure usando [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) oppure [Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate), con due database in due macchine virtuali separate. Entrambe le macchine virtuali dovrebbero trovarsi nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente.  È inoltre consigliabile posizionare le macchine virtuali nello stesso set di disponibilità per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati. Se si vuole la ridondanza geografica, configurare questi due database affinché siano in grado di eseguire la replica tra due aree diverse e connettere le due istanze con un gateway VPN.

L'esercitazione [Implementare Oracle Data Guard in Azure](configure-oracle-dataguard.md) illustra la procedura di configurazione di base in Azure.  

Con Oracle Data Guard, la disponibilità elevata può essere ottenuta con un database primario in una macchina virtuale, un database secondario (standby) in un'altra macchina virtuale e la replica unidirezionale configurata tra di essi. Il risultato è l'accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra due database. Per informazioni su come configurare una soluzione a disponibilità elevata per i database usando questi strumenti, vedere la documentazione di [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) e [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web di Oracle. Se è necessario accedere in lettura-scrittura alla copia del database, è possibile utilizzare [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

L'esercitazione [Implementare Oracle Golden Gate in Azure](configure-oracle-golden-gate.md) illustra la procedura di configurazione di base in Azure.

Oltre ad avere una soluzione a disponibilità elevata e con ripristino di emergenza in Azure, è consigliabile assicurarsi di avere una strategia di backup attiva per il ripristino del database. L'esercitazione [Eseguire backup e ripristino di un database Oracle](./oracle-overview.md) illustra la procedura di base per la creazione di un backup coerente.

## <a name="support-for-jd-edwards"></a>Supporto per JD Edwards

In base alla nota [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) del supporto tecnico Oracle, JD Edwards EnterpriseOne 9.2 e versioni successive è supportato in **qualsiasi offerta cloud pubblica** che rispetta le specifiche indicate in `Minimum Technical Requirements` (MTR).  È necessario creare immagini personalizzate che soddisfano le specifiche MTR per la compatibilità delle applicazioni con il sistema operativo e il software.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Offerte di macchine virtuali Oracle WebLogic Server

Oracle e Microsoft collaborano per portare WebLogic Server in Azure Marketplace sotto forma di raccolta di offerte di applicazioni Azure.  Queste offerte sono descritte nell'articolo [Applicazioni Oracle WebLogic Server in Azure](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Immagini di macchine virtuali Oracle WebLogic Server

- **Il clustering è supportato solo nella Enterprise Edition.** Si dispone della licenza per usare il clustering di WebLogic solo con la Enterprise Edition di Oracle WebLogic Server. Non usare il clustering con Oracle WebLogic Server Standard Edition.
- **Il multicast UDP non è supportato.** Azure supporta l'unicast UDP, ma non il multicast e il broadcast. Oracle WebLogic Server è in grado di basarsi sulle funzionalità unicast UDP di Azure. Per ottenere risultati ottimali basandosi sull'unicast UDP, è consigliabile che le dimensioni del cluster WebLogic siano statiche o che non siano presenti più di 10 server gestiti.
- **Oracle WebLogic Server prevede che le porte pubbliche e private siano le stesse per l'accesso T3 (ad esempio, quando si usa Enterprise JavaBeans).** Prendere in considerazione uno scenario a più livelli in cui un'applicazione di livello di servizio (EJB) è in esecuzione su un cluster Oracle WebLogic Server costituito da due o più VM in una rete virtuale denominata *SLWLS*. Il livello client è in una subnet diversa nella stessa rete virtuale, che esegue un semplice programma Java provando a chiamare EJB nel livello di servizio. Poiché è necessario bilanciare il carico del livello di servizio, è necessario creare un endpoint con carico bilanciato pubblico per le macchine virtuali nel cluster Oracle WebLogic Server. Se la porta privata specificata è diversa dalla porta pubblica (ad esempio, 7006:7008), si verificherà un errore simile al seguente:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Questo perché per ciascun accesso remoto T3, Oracle WebLogic Server prevede che la porta del servizio di bilanciamento del carico e la porta del server WebLogic gestito sia la stessa. In questo caso, il client accede alla porta 7006 (la porta del servizio di bilanciamento del carico) e il server gestito è in ascolto sulla porta 7008 (la porta privata). Questa restrizione è applicabile solo per l'accesso T3 e non per quello HTTP.

   Per evitare questo problema, utilizzare una delle soluzioni alternative seguenti:

- Utilizzare gli stessi numeri di porta pubblica e privata per gli endpoint con carico bilanciato dedicati all'accesso T3.
- Includere il parametro JVM seguente durante l'avvio di Oracle WebLogic Server:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Per informazioni correlate, vedere l'articolo della KB **860340.1** all'indirizzo <https://support.oracle.com>.

- **Limitazioni del clustering dinamico e del bilanciamento del carico.** Si supponga di voler usare un cluster dinamico in Oracle WebLogic Server ed esporlo tramite un unico endpoint pubblico con carico bilanciato in Azure. Questa operazione può essere eseguita se si usa un numero di porta fisso per ciascuno dei server gestiti (non assegnato in modo dinamico da un intervallo) e non si avviano più server gestiti rispetto alle macchine rilevate dall'amministratore. (vale a dire non più di un server gestito per ciascuna macchina virtuale). Se la configurazione comporta l'avvio di più server Oracle WebLogic rispetto alle macchine virtuali (vale a dire più istanze Oracle WebLogic Server condivideranno la stessa macchina virtuale), solo una di queste istanze di server WebLogic potrà eseguire l'associazione a un determinato numero di porta. Gli altri server su tale macchina virtuale avranno esito negativo.

   Se si configura il server di amministrazione per assegnare automaticamente numeri di porta univoci ai server gestiti, il bilanciamento del carico non sarà possibile poiché Azure non supporta il mapping da una singola porta pubblica a più porte private, necessario per questa configurazione.
- **Più istanze di Oracle WebLogic in una macchina virtuale.** A seconda dei requisiti di distribuzione, si potrebbe prendere in considerazione di eseguire più istanze di Oracle WebLogic Server sulla stessa macchina virtuale, se la macchina virtuale è sufficientemente grande. Ad esempio, su una macchina virtuale di dimensioni medie, che contiene due core, si potrebbe scegliere di eseguire due istanze di Oracle WebLogic Server. Tuttavia è comunque consigliabile evitare l'introduzione di singoli punti di errore nell'architettura, come avverrebbe se venisse usata una sola macchina virtuale che esegue più istanze di Oracle WebLogic Server. L'uso di almeno due macchine virtuali potrebbe rappresentare un approccio migliore e ciascuna delle macchine virtuali potrebbe eseguire più istanze di Oracle WebLogic Server. Ogni istanza di Oracle WebLogic Server potrebbe far ancora parte dello stesso cluster. Tuttavia, attualmente non è possibile usare Azure per bilanciare il carico degli endpoint esposti da tali distribuzioni Oracle WebLogic Server all'interno della stessa macchina virtuale, poiché il servizio di bilanciamento del carico di Azure richiede che i server con carico bilanciato siano distribuiti tra macchine virtuali univoche.

## <a name="oracle-jdk-virtual-machine-images"></a>Immagini di macchine virtuali Oracle JDK

- **Aggiornamenti più recenti di JDK 6 e 7.** Sebbene sia consigliabile usare la versione pubblica supportata più recente di Java (attualmente Java 8), Azure rende disponibili anche le immagini JDK 6 e 7. Queste sono destinate alle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre gli aggiornamenti alle immagini JDK precedenti potrebbero non essere più disponibili al pubblico, considerata la partnership di Microsoft con Oracle, le immagini JDK 6 e 7 fornite da Azure sono progettate per contenere un aggiornamento non pubblico più recente che normalmente viene offerto da Oracle solo a un gruppo selezionato di clienti supportati di Oracle. Le nuove versioni delle immagini JDK verranno rese disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

   Il JDK disponibile nelle immagini JDK 6 e 7 e le macchine virtuali e le immagini che ne derivano possono essere usati solo all'interno di Azure.
- **JDK a 64 bit.** Le immagini delle macchine virtuali Oracle WebLogic Server e le immagini delle macchine virtuali Oracle JDK fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

## <a name="next-steps"></a>Passaggi successivi

Ora è disponibile una panoramica delle soluzioni Oracle correnti basate sulle immagini della macchina virtuale in Microsoft Azure. Il passaggio successivo consiste nel distribuire il primo database Oracle in Azure.

> [!div class="nextstepaction"]
> [Creare un database Oracle in Azure](oracle-database-quick-create.md)