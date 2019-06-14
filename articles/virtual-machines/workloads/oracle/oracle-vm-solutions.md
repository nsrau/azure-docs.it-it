---
title: Soluzioni Oracle in macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle configurazioni supportate e sulle limitazioni delle immagini di macchine virtuali Oracle su Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
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
ms.openlocfilehash: 9dd7f7d07b34ed3c1076b46c0bf5185d6c8cd31a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074222"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Immagini di macchina virtuale di Oracle e rispettiva distribuzione in Microsoft Azure

Questo articolo contiene informazioni sulle soluzioni Oracle basate su immagini di macchine virtuali pubblicate da Oracle in Azure Marketplace. Se sei interessato a soluzioni di applicazioni tra cloud con l'infrastruttura Cloud Oracle, vedere [soluzioni delle applicazioni Oracle l'integrazione di Microsoft Azure e dell'infrastruttura Cloud Oracle](oracle-oci-overview.md).

Per ottenere un elenco delle immagini attualmente disponibili, eseguire il comando seguente:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partire da maggio 2019 sono disponibili le immagini seguenti:

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
In base alla nota di supporto tecnico Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), le versioni di JD Edwards EnterpriseOne 9.2 e versioni successive sono supportate nelle **qualsiasi offerta cloud pubblica** che rispetta le specifiche `Minimum Technical Requirements` (MTR).  È necessario creare immagini personalizzate che soddisfano le specifiche MTR per la compatibilità delle applicazioni con il sistema operativo e il software. 

## <a name="oracle-database-vm-images"></a>Immagini di macchina virtuale Oracle database
Oracle supporta l'esecuzione di Oracle DB 12.1 Standard ed Enterprise Edition in Azure nelle immagini di macchine virtuali basate su Oracle Linux.  Per ottenere prestazioni ottimali per carichi di lavoro di produzione del database Oracle in Azure, assicurarsi di ridimensionare in modo appropriato l'immagine della VM e di usare Managed Disks con supporto di risorse di archiviazione Premium. Per istruzioni su come iniziare rapidamente a usare un database Oracle in Azure tramite l'immagine della VM pubblicata da Oracle, [vedere la procedura dettagliata per l'avvio rapido del database Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opzioni di configurazione dei dischi collegati

I dischi collegati si basano sul servizio di archiviazione BLOB di Azure. Ogni disco standard è in grado di supportare un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). I dischi premium Microsoft sono la soluzione preferibile per i carichi di lavoro di database ad alte prestazioni e possono raggiungere fino a 5000 IOPS per disco. È possibile usare un singolo disco se questo soddisfa le esigenze di prestazioni. Tuttavia, è possibile migliorare le prestazioni delle operazioni IOPS effettive se usare più dischi collegati, distribuendo i dati dei database tra di essi e quindi utilizzare Oracle Automatic Storage Management (ASM). Per altre informazioni specifiche per Oracle ASM, vedere [Oracle Automatic Storage overview](https://www.oracle.com/technetwork/database/index-100339.html) (Panoramica di Oracle Automatic Storage). Per un esempio di come installare e configurare Oracle ASM su una VM Linux di Azure, vedere la [Installing and Configuring Oracle Automated Storage Management](configure-oracle-asm.md) esercitazione.

### <a name="shared-storage-configuration-options"></a>Opzioni di configurazione di archiviazione condivisa

File di Azure NetApp è stato progettato per soddisfare i requisiti di base dell'esecuzione di carichi di lavoro a prestazioni elevate, come i database nel cloud e fornisce;
- Nativa di Azure condiviso del servizio di archiviazione NFS per l'esecuzione di carichi di lavoro Oracle sia tramite macchine Virtuali native client NFS o dNFS Oracle
- Livelli di prestazioni scalabili che riflettono le esigenze reali intervallo di IOPS
- Bassa latenza
- Disponibilità elevata, una durabilità elevata e la facilità di gestione su larga scala, in genere richiesta dai carichi di lavoro aziendali critici (ad esempio, SAP e Oracle)
- Backup veloci ed efficienti e recupero, per raggiungere gli obiettivi RTO e RPO contratti di servizio più aggressivo

Queste funzionalità sono possibili in quanto file NetApp di Azure si basa su sistemi di all-flash® di NetApp ONTAP® in esecuzione in ambiente di centro dati di Azure, come un servizio di Azure Native. Il risultato è una tecnologia di archiviazione di database ideale che può essere eseguito il provisioning e può essere utilizzata solo come le altre opzioni di archiviazione di Azure. Visualizzare [documentazione di Azure i file di NetApp](https://docs.microsoft.com/azure/azure-netapp-files/) per altre informazioni su come distribuire e accedere ai volumi NFS i file di Azure NetApp. Visualizzare [Oracle su Azure distribuzione migliore pratica usando Azure NetApp file di Guida](https://www.netapp.com/us/media/tr-4780.pdf) per le procedure consigliate per il funzionamento di un database Oracle in Azure i file di NetApp.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC è progettato per ridurre l'errore di un singolo nodo in una configurazione cluster a più nodi locale. Si basa su due tecnologie locali, non native agli ambienti cloud pubblici su vasta scala, ovvero il multicast di rete e i dischi condivisi. Se la soluzione di database richiede Oracle RAC in Azure, è necessario terza software di terze parti per abilitare queste tecnologie. Per altre informazioni su Oracle RAC, vedere la [FlashGrid SkyCluster pagina](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerazioni sulla disponibilità elevata e sul ripristino di emergenza
Quando si usano i database Oracle in Azure, si è responsabili dell'implementazione di una soluzione di ripristino di elevata disponibilità e ripristino di emergenza per evitare qualsiasi tempo di inattività. 

Elevata disponibilità e ripristino di emergenza per Oracle Database Enterprise Edition (senza basarsi su Oracle RAC) può essere ottenuto su Azure usando [Data Guard, Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), o [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), con due database in due macchine virtuali separate. Entrambe le macchine virtuali dovrebbero trovarsi nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente.  È inoltre consigliabile posizionare le macchine virtuali nello stesso set di disponibilità per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati. Qualora si volesse hanno ridondanza geografica, impostare i due database per eseguire la replica tra due aree diverse e connettere le due istanze con un Gateway VPN.

L'esercitazione [implementare Oracle Data Guard su Azure](configure-oracle-dataguard.md) illustra la procedura di configurazione di base in Azure.  

Con Oracle Data Guard, la disponibilità elevata può essere ottenuta con un database primario in una macchina virtuale, un database secondario (standby) in un'altra macchina virtuale e la replica unidirezionale configurata tra di essi. Il risultato è l'accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra due database. Per informazioni su come configurare una soluzione a disponibilità elevata per i database usando questi strumenti, vedere la documentazione di [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web di Oracle. Se è necessario accedere in lettura-scrittura alla copia del database, è possibile utilizzare [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

L'esercitazione [implementare Oracle GoldenGate in Azure](configure-oracle-golden-gate.md) illustra la procedura di configurazione di base in Azure.

Oltre a disporre di una soluzione a disponibilità elevata e ripristino di emergenza in Azure, è necessario disporre di una strategia di backup in grado di ripristinare il database. L'esercitazione [Backup e ripristinare un Database Oracle](oracle-backup-recovery.md) illustra la procedura di base per la definizione di un backup coerente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Immagini di macchine virtuali Oracle WebLogic Server

* **Il clustering è supportato solo nella Enterprise Edition.** Si dispone della licenza per usare il clustering di WebLogic solo con la Enterprise Edition di WebLogic Server. Non utilizzare il clustering con WebLogic Server Standard Edition.
* **Il multicast UDP non è supportato.** Azure supporta l'unicast UDP, ma non il multicast e il broadcast. WebLogic Server è in grado di basarsi sulle funzionalità unicast UDP di Azure. Per ottenere risultati ottimali basandosi sull'unicast UDP, è consigliabile che le dimensioni del cluster WebLogic siano mantenute statici o mantenuto con non più di 10 server gestiti.
* **WebLogic Server prevede che le porte pubbliche e private siano le stesse per l'accesso T3 (ad esempio, quando si usa Enterprise JavaBeans).** Si consideri uno scenario multilivello in cui un'applicazione del servizio livello (EJB) è in esecuzione in un cluster WebLogic Server costituito da due o più macchine virtuali, in una rete virtuale denominata *SLWLS*. Il livello client è in una subnet diversa nella stessa rete virtuale, che esegue un semplice programma Java provando a chiamare EJB nel livello di servizio. Perché è necessario il livello di servizio di bilanciamento del carico, un endpoint pubblico con bilanciamento del carico deve essere creato per le macchine virtuali del cluster WebLogic Server. Se la porta privata specificata è diversa dalla porta pubblica (ad esempio, 7006:7008), si verificherà un errore simile al seguente:

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

* **Limitazioni del clustering dinamico e del bilanciamento del carico.** Si supponga che si desidera utilizzare un cluster dinamico in WebLogic Server ed esporlo tramite un unico endpoint pubblico con carico bilanciato in Azure. Questa operazione può essere eseguita fino a quando si utilizza un numero di porta fissa per ogni server gestito (non assegnato dinamicamente da un intervallo) e non vengono avviati più server gestiti rispetto alle macchine rilevate dall'amministratore. Non è non più di un server gestito per ogni macchina virtuale). Se la configurazione comporta più server WebLogic in corso l'avvio rispetto alle macchine virtuali (vale a dire, in cui più istanze di WebLogic Server condivideranno la stessa macchina virtuale), quindi non è possibile che più di uno di tali istanze di WebLogic Server Per associare a un determinato numero di porta. Gli altri server su tale macchina virtuale non riuscire.

   Se si configura il server di amministrazione per assegnare automaticamente numeri di porta univoci ai server gestiti, il bilanciamento del carico non sarà possibile poiché Azure non supporta il mapping da una singola porta pubblica a più porte private, necessario per questa configurazione.
* **Più istanze di WebLogic Server in una macchina virtuale.** A seconda dei requisiti di distribuzione, è possibile eseguire più istanze di WebLogic Server nella macchina virtuale stessa, se la macchina virtuale è sufficientemente grande. Ad esempio, su una macchina virtuale di dimensioni medie, che contiene 2 core, si potrebbe scegliere di eseguire due istanze di WebLogic Server. Tuttavia, è comunque consigliabile evitare di introdurre singoli punti di guasto nell'architettura, che sarebbe il caso se è stata usata una sola macchina virtuale che esegue più istanze di WebLogic Server. Usando almeno due macchine virtuali potrebbe essere un approccio migliore e ogni macchina virtuale è stato possibile eseguire più istanze di WebLogic Server. Ogni istanza di WebLogic Server potrebbe far ancora parte dello stesso cluster. Tuttavia, attualmente non è possibile usare Azure per gli endpoint di bilanciamento del carico esposti da tali distribuzioni WebLogic Server nella macchina virtuale stessa, in quanto servizio di bilanciamento del carico di Azure richiede il server con carico bilanciato siano distribuiti tra univoco macchine virtuali.

## <a name="oracle-jdk-virtual-machine-images"></a>Immagini di macchine virtuali Oracle JDK
* **Aggiornamenti più recenti di JDK 6 e 7.** Sebbene sia consigliabile usare la versione pubblica supportata più recente di Java (attualmente Java 8), Azure rende disponibili anche le immagini JDK 6 e 7. Queste sono destinate alle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre gli aggiornamenti alle immagini JDK precedenti potrebbero non essere più disponibili al pubblico, considerata la partnership di Microsoft con Oracle, le immagini JDK 6 e 7 fornite da Azure sono progettate per contenere un aggiornamento non pubblico più recente che normalmente viene offerto da Oracle solo a un gruppo selezionato di clienti supportati di Oracle. Le nuove versioni delle immagini JDK verranno rese disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

   Il JDK disponibile in JDK 6 e 7 immagini e le macchine virtuali e le immagini derivate da essi, può essere utilizzato solo all'interno di Azure.
* **JDK a 64 bit.** Le immagini delle macchine virtuali Oracle WebLogic Server e le immagini delle macchine virtuali Oracle JDK fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

## <a name="next-steps"></a>Passaggi successivi
È ora disponibile una panoramica delle soluzioni Oracle correnti basate su immagini di macchina virtuale in Microsoft Azure. Il passaggio successivo consiste nel distribuire il primo database Oracle in Azure.

> [!div class="nextstepaction"]
> [Creare un database Oracle in Azure](oracle-database-quick-create.md)
