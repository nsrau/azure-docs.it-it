<properties
pageTitle="Considerazioni sull'utilizzo di immagini della macchina virtuale Oracle | Microsoft Azure"
description="Informazioni sulle configurazioni supportate e sulle limitazioni per una macchina virtuale di Oracle su Windows Server in Azure prima di distribuire."
services="virtual-machines-windows"
documentationCenter=""
manager=""
authors="bbenz"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

#Considerazioni varie sulle immagini di macchine virtuali Oracle


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


In questo articolo vengono illustrate delle considerazioni sulle macchine virtuali Oracle su Azure, che sono basate su immagini software Oracle fornite da Microsoft con Windows Server come sistema operativo.

-  Immagini di macchine virtuali Oracle Database
-  Immagini di macchine virtuali Oracle WebLogic Server
-  Immagini di macchine virtuali Oracle JDK

##Immagini di macchine virtuali Oracle Database
### Il clustering (RAC) non è supportato.

Azure non supporta attualmente Oracle Real Application Clusters (RAC) del Database Oracle. Sono possibili solo le istanze di Oracle Database autonome. Questo perché Azure attualmente non supporta la condivisione del disco virtuale in modalità lettura/scrittura tra più istanze di macchine virtuali. Anche il multicast UDP non è supportato.

### Nessun IP statico interno

Azure assegna a ciascuna macchina virtuale un indirizzo IP interno. A meno che la macchina virtuale faccia parte di una rete virtuale, l'indirizzo IP della macchina virtuale è dinamico e potrebbe cambiare dopo il riavvio della macchina virtuale. Ciò può causare problemi perché Oracle Database prevede che l'indirizzo IP sia statico. Per evitare il problema, provare ad aggiungere la macchina virtuale a una rete virtuale di Azure. Per ulteriori informazioni, vedere [Rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) e [Creare una rete virtuale in Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### Opzioni di configurazione dei dischi collegati

È possibile posizionare i file di dati sul disco del sistema operativo della macchina virtuale o sui dischi collegati, noti anche come dischi dati. I dischi collegati potrebbero offrire prestazioni migliori e flessibilità nelle dimensioni rispetto al disco del sistema operativo. Il disco del sistema operativo può essere preferibile solo per i database inferiori a 10 giga byte (GB).

I dischi collegati si basano sul servizio di archiviazione BLOB di Azure. Ciascun disco è in grado di eseguire un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). Le prestazioni dei dischi collegati potrebbero non essere ottimali inizialmente e quelle delle operazioni IOPS potrebbero migliorare notevolmente dopo un periodo «burn-in» di circa 60-90 minuti di operazione continua. Se in seguito un disco rimane inattivo, le prestazioni delle operazioni IOPS potrebbero diminuire fino a un nuovo periodo burn-in di operazione continua. In breve, più un disco è attivo, più è probabile che le prestazioni delle operazioni IOPS siano ottimali.

Sebbene l'approccio più semplice consista nel collegare un unico disco alla macchina virtuale e inserire i file dei database su tale disco, questo approccio presente maggiori limitazioni in termini di prestazioni. Al contrario, spesso è possibile migliorare le prestazioni delle operazioni IOPS effettive utilizzando più dischi collegati, distribuendo i dati dei database su di essi, quindi utilizzando Oracle Automatic Storage Management (ASM). Per ulteriori informazioni, vedere [Panoramica dell'archiviazione automatica di Oracle](http://www.oracle.com/technetwork/database/index-100339.html). Sebbene sia possibile utilizzare lo striping a livello di sistema operativo di più dischi, tale approccio è sconsigliato perché non è noto per migliorare le prestazioni delle operazioni IOPS.

Prendere in considerazione due diversi approcci per il collegamento di più dischi a seconda se si desidera assegnare la priorità alle prestazioni delle operazioni di lettura o a quelle delle operazioni di scrittura per il database:

- **Solo Oracle ASM**: è probabile che si ottengano prestazioni IOPS migliori in termini di operazioni di scrittura, ma peggiori in termini di operazioni di lettura rispetto all’approccio che utilizza i pool di archiviazione di Windows Server 2012. L’illustrazione seguente descrive questa disposizione in modo logico. ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

- **Oracle ASM con pool di archiviazione di Windows Server 2012**: è probabile che si ottengano prestazioni IOPS migliori in termini di operazioni di lettura se il database esegue principalmente operazioni di lettura o se si prediligono le prestazioni delle operazioni di lettura rispetto a quelle delle operazioni di scrittura. È necessaria un'immagine basata sul sistema operativo Windows Server 2012. Per ulteriori informazioni sui pool di archiviazione, vedere [Distribuzione degli spazi di archiviazione su un server autonomo](http://technet.microsoft.com/library/jj822938.aspx). In questo caso, viene innanzitutto eseguito lo «striping» di due sottoinsiemi uguali di dischi collegati come dischi fisici in due volumi del pool di archiviazione, quindi i volumi vengono aggiunti a un gruppo di dischi ASM. L’illustrazione seguente descrive questa disposizione in modo logico.

	![](media/virtual-machines-windows-classic-oracle-considerations/image3.png)

>[AZURE.IMPORTANT] Valutare il compromesso tra prestazioni di scrittura e prestazioni di lettura caso per caso. I risultati effettivi possono variare quando si utilizzano questi approcci.

### Considerazioni sulla disponibilità elevata e sul ripristino di emergenza

Quando si utilizzano le macchine virtuali Oracle Database in Azure, si è responsabili dell'implementazione di una soluzione di disponibilità elevata e di ripristino di emergenza per evitare i tempi di inattività. Si è anche responsabili del backup dei propri dati e delle applicazioni.

La disponibilità elevata e il ripristino di emergenza per Oracle Database Enterprise Edition (senza RAC) su Azure possono essere ottenuti utilizzando [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) oppure [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), con due database in due macchine virtuali separate. Entrambe le macchine virtuali dovrebbero trovarsi nello stesso [servizio cloud](virtual-machines-linux-classic-connect-vms.md) e nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente. È inoltre consigliabile posizionare le macchine virtuali nello stesso [set di disponibilità](virtual-machines-windows-manage-availability.md) per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati. Solo le macchine virtuali nello stesso servizio cloud possono partecipare allo stesso set di disponibilità. Ogni macchina virtuale deve disporre di almeno 2 GB di memoria e 5 GB di spazio su disco.

Con Oracle Data Guard, la disponibilità elevata può essere ottenuta con un database primario in una macchina virtuale, un database secondario (standby) in un'altra macchina virtuale e la replica unidirezionale configurata tra di essi. Il risultato è l'accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra due database. Per informazioni su come configurare una soluzione a disponibilità elevata per i database utilizzando questi strumenti, vedere la documentazione di [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web di Oracle. Se è necessario accedere in lettura-scrittura alla copia del database, è possibile utilizzare [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##Immagini di macchine virtuali Oracle WebLogic Server

-  **Il clustering è supportato solo nella Enterprise Edition.** Se si utilizzano le immagini concesse in licenza da Microsoft di WebLogic Server (in particolare, quelle con Windows Server come sistema operativo), si dispone della licenza per utilizzare il clustering di WebLogic solo con la Enterprise Edition di WebLogic Server. Non utilizzare il clustering con WebLogic Server Standard Edition.

-  **Timeout di connessione:** se l'applicazione si basa sulle connessioni agli endpoint pubblici di un altro servizio cloud di Azure (ad esempio, un servizio di livello database), Azure potrebbe chiudere le connessioni aperte dopo 4 minuti di inattività. Ciò potrebbe influire sulle funzionalità e sulle applicazioni che si basano sui pool di connessioni, poiché le connessioni inattive per più di tale limite potrebbero non essere più valide. Se questo influisce sulle applicazioni, provare ad abilitare la logica «keep-alive» sul pool di connessioni.

	Se un endpoint è *interno* alla distribuzione del servizio cloud di Azure (come la macchina virtuale di un database autonomo all’interno dello *stesso* servizio cloud delle macchine virtuali WebLogic), la connessione è diretta e non si basa sul servizio di bilanciamento del carico di Azure, pertanto non è soggetta al timeout di connessione.

-  **Il multicast UDP non è supportato.** Azure supporta l’unicast UDP, ma non il multicast e il broadcast. WebLogic Server è in grado di basarsi sulle funzionalità unicast UDP di Azure. Per ottenere risultati ottimali basandosi sull’unicast UDP, è consigliabile che le dimensioni del cluster WebLogic siano statiche o che non siano presenti più di 10 server gestiti inclusi nel cluster.

-  **WebLogic Server prevede che le porte pubbliche e private siano le stesse per l'accesso T3 (ad esempio, quando si utilizza Enterprise JavaBeans).** Prendere in considerazione uno scenario a più livelli in cui un'applicazione di livello di servizio (EJB) è in esecuzione su un cluster WebLogic Server costituito da due o più server gestiti, in un servizio cloud denominato **SLWLS**. Il livello client è in un altro servizio cloud, che esegue un semplice programma Java provando a chiamare EJB nel livello di servizio. Poiché è necessario bilanciare il carico del livello di servizio, è necessario creare un endpoint con carico bilanciato pubblico per le macchine virtuali nel cluster WebLogic Server. Se la porta privata specificata per tale endpoint è diversa dalla porta pubblica (ad esempio, 7006:7008), si verificherà un errore simile al seguente:

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	Questo perché per ciascun accesso remoto T3, WebLogic Server prevede che la porta del servizio di bilanciamento del carico e la porta del server WebLogic gestito sia la stessa. In questo caso, il client accede alla porta 7006 (la porta del servizio di bilanciamento del carico) e il server gestito è in ascolto sulla porta 7008 (la porta privata). Questa restrizione è applicabile solo per l'accesso T3 e non per quello HTTP.

	Per evitare questo problema, utilizzare una delle soluzioni alternative seguenti:

	-  Utilizzare gli stessi numeri di porta pubblica e privata per gli endpoint con carico bilanciato dedicati all'accesso T3.

	-  Includere il parametro JVM seguente durante l’avvio di WebLogic Server:

			-Dweblogic.rjvm.enableprotocolswitch=true

Per informazioni correlate, vedere l'articolo KB **860340.1** all’indirizzo <http://support.oracle.com>.

-  **Limitazioni del clustering dinamico e del bilanciamento del carico.** Si supponga che si desidera utilizzare un cluster dinamico in WebLogic Server ed esporlo tramite un unico endpoint pubblico con carico bilanciato in Azure. Questa operazione può essere eseguita se si utilizza un numero di porta fisso per ciascuno dei server gestiti (non assegnato in modo dinamico da un intervallo) e non si avviano più server gestiti rispetto alle macchine rilevate dall'amministratore (vale a dire non più di un server gestito per ciascuna macchina virtuale). Se la configurazione comporta l’avvio di più server WebLogic rispetto alle macchine virtuali (vale a dire più istanze WebLogic Server condivideranno la stessa macchina virtuale), solo uno di questi server di istanze WebLogic Server potrà eseguire l’associazione a un determinato numero di porta. Gli altri server su tale macchina virtuale avranno esito negativo.

	D'altra parte, se si configura il server di amministrazione per assegnare automaticamente numeri di porta univoci ai server gestiti, il bilanciamento del carico non sarà possibile poiché Azure non supporta il mapping da una singola porta pubblica a più porte private, necessario per questa configurazione.

-  **Più istanze di WebLogic in una macchina virtuale.** A seconda dei requisiti di distribuzione, si potrebbe prendere in considerazione la possibilità di eseguire più istanze di WebLogic Server sulla stessa macchina virtuale, se la macchina virtuale è sufficientemente grande. Ad esempio, su una macchina virtuale di dimensioni medie, che contiene 2 core, si potrebbe scegliere di eseguire due istanze di WebLogic Server. Tuttavia è comunque consigliabile evitare l'introduzione di singoli punti di errore nell'architettura, come avverrebbe se venisse utilizzata una sola macchina virtuale che esegue più istanze di WebLogic Server. L’utilizzo di almeno due macchine virtuali potrebbe rappresentare un approccio migliore e ciascuna delle macchine virtuali potrebbe eseguire più istanze di WebLogic Server. Ognuna di queste istanze di WebLogic Server potrebbe far ancora parte dello stesso cluster. Tuttavia, attualmente non è possibile utilizzare Azure per bilanciare il carico degli endpoint esposti da tali distribuzioni WebLogic Server all'interno della stessa macchina virtuale, poiché il servizio di bilanciamento del carico di Azure richiede che i server con carico bilanciato siano distribuiti tra le macchine virtuali univoche.

##Immagini di macchine virtuali Oracle JDK

-  **Aggiornamenti più recenti di JDK 6 e 7.** Sebbene sia consigliabile utilizzare la versione pubblica supportata più recente di Java (attualmente Java 8), Azure rende disponibili anche le immagini JDK 6 e 7. Queste sono destinate alle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre gli aggiornamenti alle immagini JDK precedenti potrebbero non essere più disponibili al pubblico, considerata la partnership di Microsoft con Oracle, le immagini JDK 6 e 7 fornite da Azure sono progettate per contenere un aggiornamento non pubblico più recente che normalmente viene offerto da Oracle solo a un gruppo selezionato di clienti supportati di Oracle. Le nuove versioni delle immagini JDK verranno rese disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

	Il JDK disponibile in queste immagini JDK 6 e 7 e le macchine virtuali e le immagini che ne derivano, potrebbero essere utilizzati solo all’interno di Azure.

-  **JDK a 64 bit.** Le immagini delle macchine virtuali Oracle WebLogic Server e le immagini delle macchine virtuali Oracle JDK fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

##Risorse aggiuntive
[Immagini di macchine virtuali Oracle per Azure](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0323_2016-->