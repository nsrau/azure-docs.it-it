---
title: Soluzioni Oracle in Microsoft Azure | Microsoft Docs
description: Informazioni sulle configurazioni supportate e sulle limitazioni delle soluzioni Oracle in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9174f7c8d16ff311312980fbe4d35996ec7ac832
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Soluzioni Oracle e rispettiva distribuzione in Microsoft Azure
Questo articolo fornisce le informazioni necessarie per una distribuzione corretta delle diverse soluzioni Oracle in Microsoft Azure. Queste soluzioni sono basate su immagini di macchine virtuali pubblicate da Oracle in Azure Marketplace. Per ottenere un elenco delle immagini attualmente disponibili, eseguire il comando seguente:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
A partire dal 16/06/2017, l'elenco di immagini è il seguente:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Queste immagini sono considerate di tipo "Bring Your Own License" e verranno quindi addebitati solo i costi di calcolo, archiviazione e rete sostenuti durante l'esecuzione di una VM.  Si presuppone che sia disponibile una licenza idonea per l'uso di software Oracle e che sia disponibile un contratto di assistenza corrente con Oracle. Oracle garantisce la mobilità delle licenze da locale ad Azure. Per informazioni dettagliate sulla mobilità delle licenze, vedere la nota pubblicata [Oracle and Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) (Oracle e Microsoft). 

I singoli utenti possono anche scegliere di basare le proprie soluzioni sulle immagini personalizzate create da zero in Azure oppure caricare immagini personalizzate dai propri ambienti locali.

## <a name="support-for-jd-edwards"></a>Supporto per JD Edwards
In base alla nota [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) del supporto tecnico Oracle, JD Edwards EnterpriseOne 9.2 e versioni successive è supportato in **qualsiasi offerta cloud pubblica** che rispetta le specifiche indicate in `Minimum Technical Requirements` (MTR).  Sarà necessario creare immagini personalizzate che soddisfano le specifiche MTR per la compatibilità delle applicazioni con il sistema operativo e il software. 

## <a name="oracle-database-virtual-machine-images"></a>Immagini di macchine virtuali Oracle Database
Oracle supporta l'esecuzione di Oracle DB 12.1 Standard ed Enterprise Edition in Azure nelle immagini di macchine virtuali basate su Oracle Linux.  Per ottenere prestazioni ottimali per carichi di lavoro di produzione del database Oracle in Azure, assicurarsi di ridimensionare in modo appropriato l'immagine della VM e di usare Managed Disks con supporto di risorse di archiviazione Premium. Per istruzioni su come iniziare rapidamente a usare un database Oracle in Azure tramite l'immagine della VM pubblicata da Oracle, [vedere la procedura dettagliata per l'avvio rapido del database Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opzioni di configurazione dei dischi collegati

I dischi collegati si basano sul servizio di archiviazione BLOB di Azure. Ogni disco standard è in grado di supportare un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). I dischi premium Microsoft sono la soluzione preferibile per i carichi di lavoro di database ad alte prestazioni e possono raggiungere fino a 5000 IOPS per disco. Sebbene sia possibile usare un singolo disco se questo soddisfa le esigenze di prestazioni, è possibile migliorare le prestazioni delle operazioni IOPS effettive usando più dischi collegati, distribuendo tra loro i dati dei database e quindi usando Oracle Automatic Storage Management (ASM). Per altre informazioni specifiche per Oracle ASM, vedere [Oracle Automatic Storage overview](http://www.oracle.com/technetwork/database/index-100339.html) (Panoramica di Oracle Automatic Storage). Per un esempio della procedura di installazione e configurazione di Oracle ASM in una VM Linux di Azure, vedere l'esercitazione [Installing and Configuring Oracle Automated Storage Management](configure-oracle-asm.md) (Installazione e configurazione di Oracle Automated Storage Management).

### <a name="oracle-realtime-application-cluster-rac"></a>Oracle Realtime Application Cluster (RAC)
Oracle RAC è progettato per ridurre l'errore di un singolo nodo in una configurazione cluster a più nodi locale.  Si basa su due tecnologie locali, non native agli ambienti cloud pubblici su vasta scala, ovvero il multicast di rete e i dischi condivisi. Sono disponibili soluzioni di terze parti create da società quali [FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/), che emulano queste tecnologie nel caso in cui sia necessario distribuire Oracle RAC in Azure. 

### <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza
Quando si usano i database Oracle in Azure, si è responsabili dell'implementazione di una soluzione a disponibilità elevata e con ripristino di emergenza per evitare i tempi di inattività. 

La disponibilità elevata e il ripristino di emergenza per Oracle Database Enterprise Edition (senza RAC) su Azure possono essere ottenuti usando [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) oppure [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), con due database in due macchine virtuali separate. Entrambe le macchine virtuali dovrebbero trovarsi nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente.  È inoltre consigliabile posizionare le macchine virtuali nello stesso set di disponibilità per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati.  Se si vuole la ridondanza geografica, è possibile configurare la replica di questi due database tra due aree diverse e connettere le due istanze con un gateway VPN.

Per informazioni sulla procedura di configurazione di base per eseguire una valutazione di questo scenario in Azure, vedere "[Implement Oracle DataGuard on Azure](configure-oracle-dataguard.md)" (Implementare Oracle DataGuard in Azure).  

Con Oracle Data Guard, la disponibilità elevata può essere ottenuta con un database primario in una macchina virtuale, un database secondario (standby) in un'altra macchina virtuale e la replica unidirezionale configurata tra di essi. Il risultato è l'accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra due database. Per informazioni su come configurare una soluzione a disponibilità elevata per i database usando questi strumenti, vedere la documentazione di [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web di Oracle. Se è necessario accedere in lettura-scrittura alla copia del database, è possibile utilizzare [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Per informazioni sulla procedura di configurazione di base per eseguire una valutazione di questo scenario in Azure, vedere "[Implement Oracle DataGuard on Azure](configure-oracle-golden-gate.md)" (Implementare Oracle GoldenGate in Azure).

Anche se in Azure è disponibile una soluzione a disponibilità elevata e con ripristino di emergenza, è consigliabile assicurarsi di avere una strategia di backup attiva per il ripristino del database.  Per informazioni sulla procedura di base per la creazione di un backup coerente, vedere l'esercitazione [Backup and recover an Oracle Database](oracle-backup-recovery.md) (Backup e ripristino di un database Oracle).

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Immagini di macchine virtuali Oracle WebLogic Server
* **Il clustering è supportato solo nella Enterprise Edition.** Si dispone della licenza per usare il clustering di WebLogic solo con la Enterprise Edition di WebLogic Server. Non utilizzare il clustering con WebLogic Server Standard Edition.
* **Il multicast UDP non è supportato.** Azure supporta l'unicast UDP, ma non il multicast e il broadcast. WebLogic Server è in grado di basarsi sulle funzionalità unicast UDP di Azure. Per ottenere risultati ottimali basandosi sull’unicast UDP, è consigliabile che le dimensioni del cluster WebLogic siano statiche o che non siano presenti più di 10 server gestiti inclusi nel cluster.
* **WebLogic Server prevede che le porte pubbliche e private siano le stesse per l'accesso T3 (ad esempio, quando si usa Enterprise JavaBeans).** Prendere in considerazione uno scenario a più livelli in cui un'applicazione di livello di servizio (EJB) è in esecuzione su un cluster WebLogic Server costituito da due o più VM in una rete virtuale denominata **SLWLS**. Il livello client è in una subnet diversa nella stessa rete virtuale, che esegue un semplice programma Java provando a chiamare EJB nel livello di servizio. Poiché è necessario bilanciare il carico del livello di servizio, è necessario creare un endpoint con carico bilanciato pubblico per le macchine virtuali nel cluster WebLogic Server. Se la porta privata specificata è diversa dalla porta pubblica (ad esempio, 7006:7008), si verificherà un errore simile al seguente:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Questo perché per ciascun accesso remoto T3, WebLogic Server prevede che la porta del servizio di bilanciamento del carico e la porta del server WebLogic gestito sia la stessa. In questo caso, il client accede alla porta 7006 (la porta del servizio di bilanciamento del carico) e il server gestito è in ascolto sulla porta 7008 (la porta privata). Questa restrizione è applicabile solo per l'accesso T3 e non per quello HTTP.

   Per evitare questo problema, utilizzare una delle soluzioni alternative seguenti:

  * Utilizzare gli stessi numeri di porta pubblica e privata per gli endpoint con carico bilanciato dedicati all'accesso T3.
  * Includere il parametro JVM seguente durante l’avvio di WebLogic Server:

         -Dweblogic.rjvm.enableprotocolswitch=true

Per informazioni correlate, vedere l'articolo KB **860340.1** all’indirizzo <http://support.oracle.com>.

* **Limitazioni del clustering dinamico e del bilanciamento del carico.** Si supponga che si desidera utilizzare un cluster dinamico in WebLogic Server ed esporlo tramite un unico endpoint pubblico con carico bilanciato in Azure. Questa operazione può essere eseguita se si utilizza un numero di porta fisso per ciascuno dei server gestiti (non assegnato in modo dinamico da un intervallo) e non si avviano più server gestiti rispetto alle macchine rilevate dall'amministratore (vale a dire non più di un server gestito per ciascuna macchina virtuale). Se la configurazione comporta l'avvio di più server WebLogic rispetto alle macchine virtuali (vale a dire più istanze WebLogic Server condivideranno la stessa macchina virtuale), solo uno di questi server di istanze WebLogic Server potrà eseguire l'associazione a un determinato numero di porta. Gli altri server su tale macchina virtuale avranno esito negativo.

   D'altra parte, se si configura il server di amministrazione per assegnare automaticamente numeri di porta univoci ai server gestiti, il bilanciamento del carico non sarà possibile poiché Azure non supporta il mapping da una singola porta pubblica a più porte private, necessario per questa configurazione.
* **Più istanze di WebLogic in una macchina virtuale.** A seconda dei requisiti di distribuzione, si potrebbe prendere in considerazione la possibilità di eseguire più istanze di WebLogic Server sulla stessa macchina virtuale, se la macchina virtuale è sufficientemente grande. Ad esempio, su una macchina virtuale di dimensioni medie, che contiene 2 core, si potrebbe scegliere di eseguire due istanze di WebLogic Server. Tuttavia è comunque consigliabile evitare l'introduzione di singoli punti di errore nell'architettura, come avverrebbe se venisse utilizzata una sola macchina virtuale che esegue più istanze di WebLogic Server. L’utilizzo di almeno due macchine virtuali potrebbe rappresentare un approccio migliore e ciascuna delle macchine virtuali potrebbe eseguire più istanze di WebLogic Server. Ognuna di queste istanze di WebLogic Server potrebbe far ancora parte dello stesso cluster. Tuttavia, attualmente non è possibile utilizzare Azure per bilanciare il carico degli endpoint esposti da tali distribuzioni WebLogic Server all'interno della stessa macchina virtuale, poiché il servizio di bilanciamento del carico di Azure richiede che i server con carico bilanciato siano distribuiti tra le macchine virtuali univoche.

## <a name="oracle-jdk-virtual-machine-images"></a>Immagini di macchine virtuali Oracle JDK
* **Aggiornamenti più recenti di JDK 6 e 7.** Sebbene sia consigliabile usare la versione pubblica supportata più recente di Java (attualmente Java 8), Azure rende disponibili anche le immagini JDK 6 e 7. Queste sono destinate alle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre gli aggiornamenti alle immagini JDK precedenti potrebbero non essere più disponibili al pubblico, considerata la partnership di Microsoft con Oracle, le immagini JDK 6 e 7 fornite da Azure sono progettate per contenere un aggiornamento non pubblico più recente che normalmente viene offerto da Oracle solo a un gruppo selezionato di clienti supportati di Oracle. Le nuove versioni delle immagini JDK verranno rese disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

   Il JDK disponibile in queste immagini JDK 6 e 7 e le macchine virtuali e le immagini che ne derivano possono essere usati solo all'interno di Azure.
* **JDK a 64 bit.** Le immagini delle macchine virtuali Oracle WebLogic Server e le immagini delle macchine virtuali Oracle JDK fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile una panoramica delle soluzioni Oracle correnti in Microsoft Azure. Il passaggio successivo consiste nel distribuire il primo database Oracle in Azure.
- Per iniziare, provare a eseguire l'esercitazione [Creare un database Oracle in Azure](oracle-database-quick-create.md).
