---
title: Soluzioni Oracle in macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle configurazioni e le limitazioni supportate per le immagini di macchine virtuali Oracle in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: f13c7e6db2bf13c0547ee8689e22c9f04fe6d511
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826769"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Immagini di macchine virtuali Oracle e relative distribuzioni in Microsoft Azure

Questo articolo contiene informazioni sulle soluzioni Oracle basate su immagini di macchine virtuali pubblicate da Oracle in Azure Marketplace. Se si è interessati a soluzioni di applicazioni tra cloud con l'infrastruttura cloud Oracle, vedere [soluzioni di applicazioni Oracle che integrano Microsoft Azure e l'infrastruttura cloud Oracle](oracle-oci-overview.md).

Per ottenere un elenco delle immagini attualmente disponibili, eseguire il comando seguente:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Al 2019 maggio sono disponibili le immagini seguenti:

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

## <a name="support-for-jd-edwards"></a>Supporto per JD Edwards
Secondo la nota del supporto Oracle, l' [ID doc 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), le versioni 9,2 e successive di JD Edwards EnterpriseOne sono supportate in **qualsiasi offerta di cloud pubblico** che soddisfi le specifiche `Minimum Technical Requirements` (MTR).  È necessario creare immagini personalizzate che soddisfano le specifiche MTR per la compatibilità delle applicazioni con il sistema operativo e il software. 

## <a name="oracle-database-vm-images"></a>Immagini di VM del database Oracle
Oracle supporta l'esecuzione di Oracle DB 12.1 Standard ed Enterprise Edition in Azure nelle immagini di macchine virtuali basate su Oracle Linux.  Per ottenere prestazioni ottimali per carichi di lavoro di produzione del database Oracle in Azure, assicurarsi di ridimensionare in modo appropriato l'immagine della VM e di usare Managed Disks con supporto di risorse di archiviazione Premium. Per istruzioni su come iniziare rapidamente a usare un database Oracle in Azure tramite l'immagine della VM pubblicata da Oracle, [vedere la procedura dettagliata per l'avvio rapido del database Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opzioni di configurazione dei dischi collegati

I dischi collegati si basano sul servizio di archiviazione BLOB di Azure. Ogni disco standard è in grado di supportare un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). I dischi premium Microsoft sono la soluzione preferibile per i carichi di lavoro di database ad alte prestazioni e possono raggiungere fino a 5000 IOPS per disco. È possibile utilizzare un singolo disco se soddisfa le esigenze di prestazioni. Tuttavia, è possibile migliorare le prestazioni di IOPS effettive se si usano più dischi collegati, si distribuiscono i dati del database tra di essi e quindi si usa Oracle Automatic Storage Management (ASM). Per altre informazioni specifiche per Oracle ASM, vedere [Oracle Automatic Storage overview](https://www.oracle.com/technetwork/database/index-100339.html) (Panoramica di Oracle Automatic Storage). Per un esempio su come installare e configurare Oracle ASM in una macchina virtuale Linux di Azure, vedere l'esercitazione sull' [installazione e la configurazione di Oracle Automated Storage Management](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Opzioni di configurazione dell'archiviazione condivisa

Azure NetApp Files è stato progettato per soddisfare i requisiti di base per l'esecuzione di carichi di lavoro ad alte prestazioni come i database nel cloud e fornisce;
- Servizio di archiviazione NFS condiviso nativo di Azure per l'esecuzione di carichi di lavoro Oracle tramite VM native NFS client o Oracle dNFS
- Livelli di prestazioni scalabili che riflettono la gamma reale di richieste di IOPS
- Bassa latenza
- Disponibilità elevata, durabilità elevata e gestibilità su larga scala, in genere richiesta da carichi di lavoro aziendali mission-critical, come SAP e Oracle
- Backup e ripristino rapidi ed efficienti per ottenere i contratti di RTO e RPO più aggressivi

Queste funzionalità sono possibili perché Azure NetApp Files si basa su NetApp® ONTAP® tutti i sistemi Flash eseguiti in Azure data center Environment, come un servizio nativo di Azure. Il risultato è una tecnologia di archiviazione di database ideale di cui è possibile eseguire il provisioning e l'utilizzo come altre opzioni di archiviazione di Azure. Per altre informazioni su come distribuire e accedere ai volumi NFS Azure NetApp Files, vedere [Azure NetApp files documentazione](https://docs.microsoft.com/azure/azure-netapp-files/) . Per informazioni sulle procedure consigliate per l'uso di un database Oracle in Azure NetApp Files, vedere la [Azure NetApp Files Guida alle procedure consigliate per la distribuzione di Oracle in Azure](https://www.netapp.com/us/media/tr-4780.pdf) .


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC è progettato per ridurre l'errore di un singolo nodo in una configurazione cluster a più nodi locale. Si basa su due tecnologie locali, non native agli ambienti cloud pubblici su vasta scala, ovvero il multicast di rete e i dischi condivisi. Se per la soluzione di database è necessario Oracle RAC in Azure, è necessario disporre di terze parti software per abilitare tali tecnologie. Per ulteriori informazioni su Oracle RAC, vedere la [pagina FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza
Quando si usano database Oracle in Azure, si è responsabili dell'implementazione di una soluzione di disponibilità elevata e ripristino di emergenza per evitare tempi di inattività. 

La disponibilità elevata e il ripristino di emergenza per Oracle Database Enterprise Edition (senza basarsi su Oracle RAC) possono essere realizzati in Azure usando [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)o [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), con due database in due componenti virtuali distinti macchine. Entrambe le macchine virtuali dovrebbero trovarsi nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente.  È inoltre consigliabile posizionare le macchine virtuali nello stesso set di disponibilità per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati. Se si vuole avere la ridondanza geografica, configurare i due database per la replica tra due aree diverse e connettere le due istanze con un gateway VPN.

L'esercitazione [implementare Oracle Data Guard in Azure](configure-oracle-dataguard.md) illustra la procedura di configurazione di base in Azure.  

Con Oracle Data Guard, la disponibilità elevata può essere ottenuta con un database primario in una macchina virtuale, un database secondario (standby) in un'altra macchina virtuale e la replica unidirezionale configurata tra di essi. Il risultato è l'accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra due database. Per informazioni su come configurare una soluzione a disponibilità elevata per i database usando questi strumenti, vedere la documentazione di [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web di Oracle. Se è necessario accedere in lettura-scrittura alla copia del database, è possibile utilizzare [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

L'esercitazione [implementare Oracle GoldenGate in Azure](configure-oracle-golden-gate.md) illustra la procedura di configurazione di base in Azure.

Oltre a disporre di una soluzione di disponibilità elevata e di ripristino di emergenza progettata in Azure, è necessario disporre di una strategia di backup per ripristinare il database. Il [backup e il ripristino di un Oracle database](oracle-backup-recovery.md) di esercitazione illustrano la procedura di base per stabilire un backup coerente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Immagini di macchine virtuali Oracle WebLogic Server

* **Il clustering è supportato solo nella Enterprise Edition.** Si dispone della licenza per usare il clustering di WebLogic solo con la Enterprise Edition di WebLogic Server. Non utilizzare il clustering con WebLogic Server Standard Edition.
* **Il multicast UDP non è supportato.** Azure supporta l'unicast UDP, ma non il multicast e il broadcast. WebLogic Server è in grado di basarsi sulle funzionalità unicast UDP di Azure. Per ottenere risultati ottimali che si basano sull'unicast UDP, è consigliabile che le dimensioni del cluster WebLogic siano mantenute statiche o mantengano non più di 10 server gestiti.
* **WebLogic Server prevede che le porte pubbliche e private siano le stesse per l'accesso T3 (ad esempio, quando si usa Enterprise JavaBeans).** Si consideri uno scenario a più livelli in cui un'applicazione di livello di servizio (EJB) viene eseguita in un cluster WebLogic Server costituito da due o più macchine virtuali, in una rete virtuale denominata *SLWLS*. Il livello client si trova in una subnet diversa nella stessa rete virtuale, eseguendo un semplice programma Java che prova a chiamare EJB nel livello di servizio. Poiché è necessario bilanciare il carico del livello di servizio, è necessario creare un endpoint pubblico con carico bilanciato per le macchine virtuali nel cluster WebLogic Server. Se la porta privata specificata è diversa dalla porta pubblica (ad esempio, 7006:7008), si verificherà un errore simile al seguente:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Questo perché per ciascun accesso remoto T3, WebLogic Server prevede che la porta del servizio di bilanciamento del carico e la porta del server WebLogic gestito sia la stessa. In questo caso, il client accede alla porta 7006 (la porta del servizio di bilanciamento del carico) e il server gestito è in ascolto sulla porta 7008 (la porta privata). Questa restrizione è applicabile solo per l'accesso T3 e non per quello HTTP.

   Per evitare questo problema, utilizzare una delle soluzioni alternative seguenti:

  * Utilizzare gli stessi numeri di porta pubblica e privata per gli endpoint con carico bilanciato dedicati all'accesso T3.
  * Includere il parametro JVM seguente durante l’avvio di WebLogic Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Per informazioni correlate, vedere l'articolo della KB **860340.1** all'indirizzo <https://support.oracle.com>.

* **Limitazioni del clustering dinamico e del bilanciamento del carico.** Si supponga che si desidera utilizzare un cluster dinamico in WebLogic Server ed esporlo tramite un unico endpoint pubblico con carico bilanciato in Azure. Questa operazione può essere eseguita solo se si usa un numero di porta fisso per ogni server gestito (non assegnato dinamicamente da un intervallo) e non si avviano più server gestiti di quanti sono i computer che l'amministratore sta monitorando. Ovvero non esiste più di un server gestito per ogni macchina virtuale. Se la configurazione comporta l'avvio di più server WebLogic rispetto alle macchine virtuali, ovvero dove più istanze di WebLogic Server condividono la stessa macchina virtuale, non è possibile usare più di una di queste istanze di WebLogic Server. per eseguire l'associazione a un determinato numero di porta. Gli altri utenti della macchina virtuale avranno esito negativo.

   Se si configura il server di amministrazione per assegnare automaticamente numeri di porta univoci ai server gestiti, il bilanciamento del carico non sarà possibile poiché Azure non supporta il mapping da una singola porta pubblica a più porte private, necessario per questa configurazione.
* **Più istanze di WebLogic Server in una macchina virtuale.** A seconda dei requisiti della distribuzione, è possibile prendere in considerazione l'esecuzione di più istanze di WebLogic Server nella stessa macchina virtuale, se la macchina virtuale è sufficientemente grande. Ad esempio, su una macchina virtuale di dimensioni medie, che contiene 2 core, si potrebbe scegliere di eseguire due istanze di WebLogic Server. Tuttavia, è comunque consigliabile evitare di introdurre singoli punti di errore nell'architettura, come avviene se fosse stata usata una sola macchina virtuale in cui sono in esecuzione più istanze di WebLogic Server. L'uso di almeno due macchine virtuali potrebbe essere un approccio migliore e ogni macchina virtuale potrebbe quindi eseguire più istanze di WebLogic Server. Ogni istanza di WebLogic Server può comunque far parte dello stesso cluster. Tuttavia, non è attualmente possibile usare Azure per bilanciare il carico degli endpoint esposti da tali distribuzioni di WebLogic Server nella stessa macchina virtuale, perché il servizio di bilanciamento del carico di Azure richiede la distribuzione dei server con carico bilanciato tra i server univoci macchine virtuali.

## <a name="oracle-jdk-virtual-machine-images"></a>Immagini di macchine virtuali Oracle JDK
* **Aggiornamenti più recenti di JDK 6 e 7.** Sebbene sia consigliabile usare la versione pubblica supportata più recente di Java (attualmente Java 8), Azure rende disponibili anche le immagini JDK 6 e 7. Queste sono destinate alle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre gli aggiornamenti alle immagini JDK precedenti potrebbero non essere più disponibili al pubblico, considerata la partnership di Microsoft con Oracle, le immagini JDK 6 e 7 fornite da Azure sono progettate per contenere un aggiornamento non pubblico più recente che normalmente viene offerto da Oracle solo a un gruppo selezionato di clienti supportati di Oracle. Le nuove versioni delle immagini JDK verranno rese disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

   Il JDK disponibile nelle immagini JDK 6 e 7 e le macchine virtuali e le immagini da essi derivate possono essere usate solo in Azure.
* **JDK a 64 bit.** Le immagini delle macchine virtuali Oracle WebLogic Server e le immagini delle macchine virtuali Oracle JDK fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile una panoramica delle soluzioni Oracle correnti basate sulle immagini di macchine virtuali in Microsoft Azure. Il passaggio successivo consiste nel distribuire il primo database Oracle in Azure.

> [!div class="nextstepaction"]
> [Creare un database Oracle in Azure](oracle-database-quick-create.md)
