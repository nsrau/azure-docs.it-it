---
title: Configurare IBM Db2 HADR in macchine virtuali (VM) di Azure Documenti Microsoft
description: Stabilire la disponibilità elevata di IBM Db2 LUW nelle macchine virtuali (VM) di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926739"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Disponibilità elevata di IBM Db2 LUW in macchine virtuali di Azure su SUSE Linux Enterprise Server con Pacemaker

IBM Db2 per Linux, UNIX e Windows (LUW) nella configurazione a disponibilità elevata e ripristino di [emergenza (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) è costituito da un nodo che esegue un'istanza di database primario e da almeno un nodo che esegue un'istanza di database secondaria. Le modifiche all'istanza del database primario vengono replicate in un'istanza di database secondaria in modo sincrono o asincrono, a seconda della configurazione. 

Questo articolo descrive come distribuire e configurare le macchine virtuali di Azure, installare il framework del cluster e installare IBM Db2 LUW con configurazione HADR. 

L'articolo non illustra come installare e configurare IBM Db2 LUW con l'installazione del software HADR o SAP. Per facilitare l'esecuzione di queste attività, vengono forniti riferimenti ai manuali di installazione di SAP e IBM. Questo articolo è incentrato sulle parti specifiche dell'ambiente Azure.This article focuses on parts that are specific to the Azure environment. 

Le versioni di IBM Db2 supportate sono 10.5 e successive, come documentato nella nota SAP [1928533].

Prima di iniziare un'installazione, vedere le note e la documentazione SAP seguenti:

| Nota SAP | Descrizione |
| --- | --- |
| [1928533] | Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di AzureSAP applications on Azure: Supported products and Azure VM types |
| [2015553] | PREREQUISITi di supporto per SAP in Azure |
| [2178632] | Metriche di monitoraggio chiave per SAP in AzureKey monitoring metrics for SAP on Azure |
| [2191498] | SAP su Linux con Azure: monitoraggio avanzato |
| [2243692] | Linux in Azure (IaaS): problemi di licenza SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: note di installazione |
| [1999351] | Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2233094] | DB6: applicazioni SAP in Azure che utilizzano IBM Db2 per Linux, UNIX e Windows - informazioni aggiuntive |
| [1612105] | DB6: domande frequenti su Db2 con HADR |


| Documentazione | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Ha tutte le note SAP necessarie per Linux |
| Guida alla [pianificazione e all'implementazione di Macchine virtuali di Azure per SAP su LinuxAzure Virtual Machines planning and implementation for SAP on Linux guide][planning-guide] |
| [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide] (questo articolo)Azure Virtual Machines deployment for SAP on Linux (this article) |
| Guida alla distribuzione del sistema di [gestione di database di Macchine virtuali di Azure (DBMS) per SAP su LinuxAzure Virtual Machines database management system(DBMS) deployment for SAP on Linux guide][dbms-guide] |
| [Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP in Azure][azr-sap-plancheck] |
| [Guide alle procedure consigliate di SUSE Linux Enterprise Server for SAP Applications 12 SP4][sles-for-sap-bp] |
| [Estensione a disponibilità elevata di SUSE Linux Enterprise 12 SP4][sles-ha-guide] |
| [Distribuzione DBMS DI IBM Db2 Azure Virtual Machines per il carico di lavoro SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Panoramica
Per ottenere la disponibilità elevata, IBM Db2 LUW con HADR viene installato in almeno due macchine virtuali di Azure, distribuite in un set di disponibilità di Azure o tra zone di disponibilità di Azure.To achieve high availability, IBM Db2 LUW with HADR is installed on at least two Azure virtual machines, which are deployed in an [Azure availability set](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) or across Azure Availability [zones.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

La grafica seguente visualizza una configurazione di due macchine virtuali di Azure del server di database. Entrambe le macchine virtuali di Azure del server di database dispongono di un proprio spazio di archiviazione collegato e sono in esecuzione. In HADR, un'istanza di database in una delle macchine virtuali di Azure ha il ruolo dell'istanza primaria. Tutti i client sono connessi a questa istanza primaria. Tutte le modifiche apportate alle transazioni di database vengono mantenute localmente nel log delle transazioni Db2. Poiché i record del log delle transazioni vengono salvati in modo permanente localmente, i record vengono trasferiti tramite TCP/IP all'istanza del database nel secondo server di database, nel server di standby o nell'istanza standby. L'istanza standby aggiorna il database locale eseguendo il rollforward dei record del log delle transazioni trasferiti. In questo modo, il server di standby viene mantenuto sincronizzato con il server primario.

HADR è solo una funzionalità di replica. Non ha alcun rilevamento degli errori e nessuna funzionalità di acquisizione o failover automatico. Un'acquisizione o un trasferimento al server di standby deve essere avviato manualmente da un amministratore del database. Per ottenere un rilevamento automatico di acquisizione e errore, è possibile utilizzare la funzionalità di clustering Linux Pacemaker.To achieve a automatic takeover and failure detection, you can use the Linux Pacemaker clustering feature. Pacemaker monitora le due istanze del server di database. Quando l'istanza del server di database primario si arresta in modo anomalo, Pacemaker avvia un'acquisizione *automatica* HADR da parte del server di standby. Pacemaker garantisce inoltre che l'indirizzo IP virtuale venga assegnato al nuovo server primario.

![Panoramica sulla disponibilità elevata di IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Per fare in modo che i server applicazioni SAP si connettano al database primario, è necessario un nome host virtuale e un indirizzo IP virtuale. In caso di failover, i server applicazioni SAP si connetteranno a una nuova istanza del database primario. In un ambiente Azure, è necessario un servizio di [bilanciamento del carico](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) di Azure per usare un indirizzo IP virtuale nel modo necessario per HADR di IBM Db2.In an Azure environment, an Azure load balancer is required to use a virtual IP address in the way that's required for HADR of IBM Db2. 

Per aiutarti a comprendere appieno in che modo IBM Db2 LUW con HADR e Pacemaker si inserisce in una configurazione del sistema SAP a disponibilità elevata, l'immagine seguente presenta una panoramica di una configurazione a disponibilità elevata di un sistema SAP basato sul database IBM Db2. In questo articolo viene illustrato solo IBM Db2, ma vengono forniti riferimenti ad altri articoli su come impostare altri componenti di un sistema SAP.

![Panoramica dell'ambiente completo ad alta disponibilità IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Panoramica generale dei passaggi necessari
Per distribuire una configurazione IBM Db2, è necessario attenersi alla seguente procedura:

  + Pianificare l'ambiente.
  + Distribuire le macchine virtuali.
  + Aggiornare SUSE Linux e configurare i file system.
  + Installare e configurare Pacemaker.
  + Installare [NFS a disponibilità elevata][nfs-ha].
  + Installare [ASCS/ERS in un cluster separato.][ascs-ha]
  + Installare il database IBM Db2 con l'opzione Distributed/High Availability (SWPM).
  + Installare e creare un nodo e un'istanza del database secondario e configurare HADR.
  + Verificare che HADR funzioni.
  + Applicare la configurazione Pacemaker per controllare IBM Db2.
  + Configurare Azure Load Balancer.Configure Azure Load Balancer.
  + Installare i server applicazioni primari e di dialogo.
  + Controllare e adattare la configurazione dei server applicazioni SAP.
  + Eseguire test di failover e di acquisizione.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Pianificare l'infrastruttura di Azure per l'hosting di IBM Db2 LUW con HADR

Completare il processo di pianificazione prima di eseguire la distribuzione. La pianificazione crea le basi per la distribuzione di una configurazione di Db2 con HADR in Azure.Planning builds the foundation for deploying a configuration of Db2 with HADR in Azure. Gli elementi chiave che devono essere parte della pianificazione per IMB Db2 LUW (parte del database dell'ambiente SAP) sono elencati nella tabella seguente:

| Argomento | Breve descrizione |
| --- | --- |
| Definire i gruppi di risorse di AzureDefine Azure resource groups | Gruppi di risorse in cui si distribuiscono macchine virtuali, rete virtuale, Azure Load Balancer e altre risorse. Può essere esistente o nuovo. |
| Rete virtuale /Definizione subnet | Dove vengono distribuite le macchine virtuali per IBM Db2 e Azure Load Balancer. Può essere esistente o appena creato. |
| Macchine virtuali che ospitano IBM Db2 LUW | Dimensione della macchina virtuale, archiviazione, rete, indirizzo IP. |
| Nome host virtuale e IP virtuale per il database IBM Db2| L'indirizzo IP virtuale o il nome host utilizzato per la connessione dei server applicazioni SAP. **db-virt-hostname**, **db-virt-ip**. |
| Scherma di AzureAzure fencing | Scherma di Azure o scherma SBD (scelta consigliata). Metodo per evitare situazioni di cervello diviso. |
| SBD VM | Dimensioni della macchina virtuale SBD, archiviazione, rete. |
| Azure Load Balancer | Utilizzo della porta di base o standard (consigliato), della porta probe per il database Db2 (la nostra raccomandazione 62500) **probe-port**. |
| Risoluzione dei nomi| Funzionamento della risoluzione dei nomi nell'ambiente. Il servizio DNS è altamente consigliato. È possibile utilizzare il file host locale. |
    
Per altre informazioni su Linux Pacemaker in Azure, vedere Configurare Pacemaker in SUSE Linux Enterprise Server in Azure.For more information about Linux Pacemaker in Azure, see [Set up Pacemaker on SUSE Linux Enterprise Server in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

## <a name="deployment-on-suse-linux"></a>Distribuzione su SUSE Linux

L'agente di risorse per IBM Db2 LUW è incluso in SUSE Linux Enterprise Server for SAP Applications. Per la configurazione descritta in questo documento, è necessario utilizzare SUSE Linux Server for SAP Applications. Azure Marketplace contiene un'immagine per SUSE Enterprise Server for SAP Applications 12 che è possibile usare per distribuire nuove macchine virtuali di Azure.The Azure Marketplace contains an image for SUSE Enterprise Server for SAP Applications 12 that you can use to deploy new Azure virtual machines. Tenere presente i vari modelli di supporto o servizio offerti da SUSE tramite Azure Marketplace quando si sceglie un'immagine di macchina virtuale in Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Host: aggiornamenti DNS
Creare un elenco di tutti i nomi host, inclusi i nomi host virtuali, e aggiornare i server DNS in modo da abilitare l'indirizzo IP appropriato per la risoluzione dei nomi host. Se un server DNS non esiste o non è possibile aggiornare e creare voci DNS, è necessario utilizzare i file host locali delle singole macchine virtuali che partecipano a questo scenario. Se si usano voci di file host, assicurarsi che le voci siano applicate a tutte le macchine virtuali nell'ambiente di sistema SAP. Tuttavia, si consiglia di utilizzare il DNS che, idealmente, si estende in Azure


### <a name="manual-deployment"></a>Distribuzione manuale

Assicurarsi che il sistema operativo selezionato sia supportato da IBM/SAP per IBM Db2 LUW. L'elenco delle versioni del sistema operativo supportate per le versioni di Azure VMs e Db2 è disponibile nella nota SAP [1928533.] L'elenco delle versioni del sistema operativo per singola versione Db2 è disponibile nella matrice di disponibilità del prodotto SAP. È consigliabile un minimo di SLES 12 SP4 a causa di miglioramenti delle prestazioni correlati ad Azure in questa o successiva versione di SUSE Linux.

1. Creare o selezionare un gruppo di risorse.
1. Creare o selezionare una rete virtuale e una subnet.
1. Creare un set di disponibilità di Azure o distribuire una zona di disponibilità.
    + Per il set di disponibilità, impostare il numero massimo di domini di aggiornamento su 2.For the availability set, set the maximum update domains to 2.
1. Creare la macchina virtuale 1.Create Virtual Machine 1.
    + Usare SLES per l'immagine SAP in Azure Marketplace.Use SLES for SAP image in the Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare Zona di disponibilità.
1.  Creare una macchina virtuale 2.Create Virtual Machine 2.
    + Usare SLES per l'immagine SAP in Azure Marketplace.Use SLES for SAP image in the Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare zona di disponibilità (non la stessa zona del passaggio 3).
1. Aggiungere dischi dati alle macchine virtuali e quindi verificare il suggerimento di una configurazione del file system nell'articolo Distribuzione DBMS di [IBM Db2 Azure Virtual Machines per][dbms-db2]il carico di lavoro SAP .

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker
    
Per creare un cluster Pacemaker di base per questo server IBM Db2, vedere [Configurare Pacemaker in SUSE Linux Enterprise Server in Azure.][sles-pacemaker] 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installare l'ambiente IBM Db2 LUW e SAP

Prima di avviare l'installazione di un ambiente SAP basato su IBM Db2 LUW, consultare la documentazione seguente:

+ Documentazione di Azure
+ Documentazione SAP
+ Documentazione IBM

I collegamenti a questa documentazione sono disponibili nella sezione introduttiva di questo articolo.

Consultare i manuali di installazione SAP sull'installazione di applicazioni basate su NetWeaver su IBM Db2 LUW.

Le guide sono disponibili nel portale della Guida SAP utilizzando [SAP Installation Guide Finder][sap-instfind].

È possibile ridurre il numero di guide visualizzate nel portale impostando i seguenti filtri:

- Voglio: "Installare un nuovo sistema"
- Il mio database: "IBM Db2 per Linux, Unix e Windows"
- Filtri aggiuntivi per le versioni SAP NetWeaver, la configurazione dello stack o il sistema operativo

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Suggerimenti per l'installazione per l'impostazione di IBM Db2 LUW con HADR

Per impostare l'istanza del database IBM Db2 LUW primaria:

- Utilizzare l'opzione di disponibilità elevata o distribuita.
- Installare l'istanza SAP ASCS/ERS e Database.
- Eseguire un backup del database appena installato.


> [!IMPORTANT] 
> Annotare la "porta di comunicazione del database" impostata durante l'installazione. Deve essere lo stesso numero di porta per entrambe le istanze di database

Per configurare il server di database Standby utilizzando la procedura di copia di sistema omogenea SAP, eseguire i passaggi seguenti:

1. Selezionare l'opzione **Copia di** sistema >**Istanza database****distribuito** > dei **sistemi** > di destinazione .
1. Come metodo di copia, selezionare **Sistema omogeneo** in modo da poter utilizzare il backup per ripristinare un backup nell'istanza del server di standby.
1. Quando si raggiunge la fase di uscita per ripristinare il database per una copia di sistema omogenea, uscire dal programma di installazione. Ripristinare il database da un backup dell'host primario. Tutte le fasi di installazione successive sono già state eseguite nel server di database primario.
1. Configurare HADR per IBM Db2.

   > [!NOTE]
   > Per l'installazione e la configurazione specifiche di Azure e Pacemaker: durante la procedura di installazione tramite SAP Software Provisioning Manager, esiste una domanda esplicita sulla disponibilità elevata per IBM Db2 LUW:
   >+ Non selezionare **IBM Db2 pureScale**.
   >+ Non selezionare **Installa IBM Tivoli System Automation for Multiplatforms**.
   >+ Non selezionare Genera file di **configurazione del cluster**.

   Quando si utilizza un dispositivo SBD per Linux Pacemaker, impostare i seguenti parametri Db2 HADR:
   + Durata della finestra peer HADR (secondi) (HADR_PEER_WINDOW)  
   + Valore di timeout HADR (HADR_TIMEOUT) - 60

   Quando si usa un agente di scherma di Azure Pacemaker, impostare i parametri seguenti:When you use an Azure Pacemaker fencing agent, set the following parameters:
   + Durata della finestra peer HADR (secondi) (HADR_PEER_WINDOW)  
   + Valore di timeout HADR (HADR_TIMEOUT) - 60

È consigliabile utilizzare i parametri precedenti in base ai test di failover/acquisizione iniziali. È obbligatorio verificare la corretta funzionalità di failover e acquisizione con queste impostazioni dei parametri. Poiché le singole configurazioni possono variare, i parametri potrebbero richiedere una regolazione. 

> [!IMPORTANT]
> Specifico per IBM Db2 con configurazione HADR con avvio normale: l'istanza del database secondario o standby deve essere in esecuzione prima di poter avviare l'istanza del database primario.

A scopo dimostrativo e le procedure descritte in questo articolo, il SID del database è **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Controllo IBM Db2 HADR
Dopo aver configurato HADR e lo stato è PEER e CONNECTED nei nodi primario e standby, eseguire il controllo seguente:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configurazione di Db2 Pacemaker

Quando si usa Pacemaker per il failover automatico in caso di errore di un nodo, è necessario configurare le istanze Db2 e Pacemaker di conseguenza. In questa sezione viene descritto questo tipo di configurazione.

I seguenti elementi sono preceduti da:

- **[A]**: Applicabile a tutti i nodi
- **[1]**: Applicabile solo al nodo 1 
- **[2]**: Applicabile solo al nodo 2

**[A]** Prerequisiti per la configurazione di Pacemaker:
1. Arrestare entrambi i server\<di database con l'utente db2 sid> con db2stop.
1. Modificare l'ambiente della\<shell per db2 sid>utente in */bin/ksh*. Si consiglia di utilizzare lo strumento Yast. 


### <a name="pacemaker-configuration"></a>Configurazione Pacemaker

> [!IMPORTANT]
> I test recenti hanno rivelato situazioni in cui netcat smette di rispondere alle richieste a causa del backlog e della sua limitazione della gestione di una sola connessione. La risorsa netcat interrompe l'ascolto delle richieste di bilanciamento di Azure Load e l'IP mobile non è più disponibile.  
> Per i cluster Pacemaker esistenti, è stato consigliato in passato la sostituzione di netcat con socat. È attualmente consigliabile usare l'agente di risorse azure-lb, che fa parte degli agenti delle risorse del pacchetto, con i requisiti di versione del pacchetto seguenti:Currently we recommend using azure-lb resource agent, which is part of package resource-agents, with the following package version requirements:
> - Per SLES 12 SP4/SP5, la versione deve essere almeno resource-agents-4.3.018.a7fb5035-3.30.1.  
> - Per SLES 15/15 SP1, la versione deve essere almeno resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Si noti che la modifica richiederà tempi di inattività brevi.  
> Per i cluster Pacemaker esistenti, se la configurazione è già stata modificata per usare socat come descritto in Protezione avanzata del rilevamento del bilanciamento del [carico di Azure,](https://www.suse.com/support/kb/doc/?id=7024128)non è necessario passare immediatamente all'agente di risorse azure-lb.

**[1]** Configurazione Pacemaker specifica di IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Creare risorse IBM Db2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Avviare le risorse IBM Db2:
* Mettere Pacemaker fuori dalla modalità di manutenzione.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Assicurarsi che lo stato del cluster sia OK e che tutte le risorse siano state avviate. Non è importante su quale nodo sono in esecuzione le risorse.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodi configurati
# <a name="5-resources-configured"></a>5 risorse configurate

# <a name="online--azibmdb01-azibmdb02-"></a>In linea: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Elenco completo delle risorse:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Iniziato azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Gruppo di risorse: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): azibmdb02 avviato
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): avviato azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Master: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Schiavi: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> È necessario gestire l'istanza Db2 in cluster Pacemaker utilizzando gli strumenti Pacemaker.You must manage the Pacemaker clustered Db2 instance by using Pacemaker tools. Se si utilizzano comandi db2 come db2stop, Pacemaker rileva l'azione come errore della risorsa. Se si esegue la manutenzione, è possibile mettere i nodi o le risorse in modalità di manutenzione. Pacemaker sospende le risorse di monitoraggio ed è quindi possibile usare i normali comandi di amministrazione db2.


### <a name="configure-azure-load-balancer"></a>Configurare Azure Load Balancer
Per configurare Azure Load Balancer, è consigliabile usare lo [SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) di Azure Standard Load Balancer e quindi eseguire le operazioni seguenti.

> [!NOTE]
> Lo SKU di Load Balancer Standard ha restrizioni per l'accesso agli indirizzi IP pubblici dai nodi sotto il servizio di bilanciamento del carico. L'articolo [Connettività degli endpoint pubblici per le macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) che usano Il bilanciamento del carico standard di Azure negli scenari di disponibilità elevata SAP descrive come abilitare tali nodi per accedere agli indirizzi IP pubblici

1. Creare un pool IP front-end:

   a. Nel portale di Azure aprire Azure Load Balancer, selezionare **pool IP front-end**e quindi **Aggiungi**.

   b. Immettere il nome del nuovo pool IP front-end, ad esempio **Db2-connection**.

   c. Impostare **Assegnazione** su **Statico**e immettere l'indirizzo IP **IP virtuale-IP** definito all'inizio.

   d. Selezionare **OK**.

   e. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.

1. Creare un pool back-end:

   a. Nel portale di Azure aprire Azure Load Balancer, selezionare **Pool back-end**e quindi **selezionare Aggiungi.**

   b. Immettere il nome del nuovo pool back-end, ad esempio **Db2-back-end**.

   c. Selezionare **Aggiungi una macchina virtuale**.

   d. Selezionare il set di disponibilità o le macchine virtuali che ospitano il database IBM Db2 creato nel passaggio precedente.

   e. Selezionare le macchine virtuali del cluster IBM Db2.

   f. Selezionare **OK**.

1. Creare un probe di integrità:Create a health probe:

   a. Nel portale di Azure aprire Azure Load Balancer, selezionare probe di **integrità**e selezionare **Aggiungi.**

   b. Immettere il nome del nuovo probe di integrità (ad esempio, **Db2-hp**).

   c. Selezionare **TCP** come protocollo e porta **62500**. Mantenere il valore **Intervallo** impostato su **5**e mantenere il valore di **soglia non integro** impostato su **2**.

   d. Selezionare **OK**.

1. Creare le regole di bilanciamento del carico:Create the load-balancing rules:

   a. Nel portale di Azure aprire Azure Load Balancer, selezionare Regole di **bilanciamento del carico**e quindi **Aggiungi.In**the Azure portal, open the Azure Load Balancer, select Load balancing rules , and then select Add .

   b. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **Db2-SID**.

   c. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creato in precedenza, ad esempio **Db2-frontend**.

   d. Mantenere il **protocollo** impostato su **TCP**e immettere port *Database Communication port*.

   e. Aumentare il valore di **Timeout di inattività** a 30 minuti.

   f. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.

   g. Selezionare **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Apportare modifiche ai profili SAP per utilizzare l'IP virtuale per la connessione
Per connettersi all'istanza primaria della configurazione HADR, il livello dell'applicazione SAP deve usare l'indirizzo IP virtuale definito e configurato per Azure Load Balancer. Sono necessarie le seguenti modifiche:

/sapmnt/\<SID>/profilo/DEFAULT. Pfl
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/globale/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installare server applicazioni primari e con finestre di dialogo

Quando si installano server applicazioni primari e dialogo su una configurazione Db2 HADR, utilizzare il nome host virtuale selezionato per la configurazione. 

Se l'installazione è stata eseguita prima della creazione della configurazione Db2 HADR, apportare le modifiche come descritto nella sezione precedente e come indicato di seguito per gli stack Java SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Controllo URL JDBC da parte di ABAP-Java o Java

Utilizzare lo strumento J2EE Config per controllare o aggiornare l'URL JDBC. Poiché lo strumento di configurazione J2EE è uno strumento grafico, è necessario che sia installato il server X:
 
1. Accedere al server applicazioni principale dell'istanza J2EE ed eseguire:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Nel riquadro sinistro scegliere **Archivio di sicurezza**.
1. Nel riquadro destro scegliere la chiave jdbc/pool/SAPSID\<>/url.
1. Modificare il nome host nell'URL JDBC con il nome host virtuale.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selezionare **Aggiungi**.
1. Per salvare le modifiche, selezionare l'icona del disco in alto a sinistra.
1. Chiudere lo strumento di configurazione.
1. Riavviare l'istanza Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurare l'archiviazione dei log per l'installazione di HADR
Per configurare l'archiviazione del registro Db2 per l'installazione di HADR, è consigliabile configurare sia il database primario che il database standby in modo che dispongano della funzionalità di recupero automatico dei log da tutti i percorsi di archiviazione del log. Sia il database primario che il database standby devono essere in grado di recuperare i file di archivio di log da tutti i percorsi di archivio log in cui una delle istanze del database potrebbe archiviare i file di registro. 

L'archiviazione dei log viene eseguita solo dal database primario. Se si modificano i ruoli HADR dei server di database o se si verifica un errore, il nuovo database primario è responsabile dell'archiviazione dei log. Se sono stati impostati più percorsi di archiviazione dei log, i log potrebbero essere archiviati due volte. In caso di recupero locale o remoto, potrebbe anche essere necessario copiare manualmente i registri archiviati dal server primario precedente al percorso di registro attivo del nuovo server primario.

È consigliabile configurare una condivisione NFS comune in cui i log vengono scritti da entrambi i nodi. La condivisione NFS deve essere altamente disponibile. 

È possibile utilizzare condivisioni NFS a disponibilità elevata esistenti per i trasporti o una directory del profilo. Per altre informazioni, vedere:

- [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server][nfs-ha] 
- [Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in SUSE Linux Enterprise Server con file NetApp di Azure per applicazioni SAPHigh availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server with Azure NetApp Files for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [File NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) di Azure (per creare condivisioni NFS)Azure NetApp Files (to create NFS shares)


## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

In questa sezione viene descritto come testare l'installazione di Db2 HADR. Ogni test presuppone che l'utente abbia *effettuato l'accesso come directory principale dell'utente* e che il database primario IBM Db2 sia in esecuzione sulla macchina virtuale *azibmdb01.*

Lo stato iniziale di tutti i test case è spiegato qui: (crm_mon stato -r o crm)

- **stato crm** è un'istantanea dello stato di Pacemaker in fase di esecuzione 
- **crm_mon -r** è l'uscita continua dello stato pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nell'immagine seguente:

![DBACockpit - Pre-migrazione](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Test aree di acquisizione di IBM Db2


> [!IMPORTANT] 
> Prima di avviare il test, assicurarsi che:
> * Pacemaker non ha azioni non riuscite (stato crm).
> * Non esistono vincoli di posizione (avanzi del test di migrazione)There are no location constraints (leftovers of migration test)
> * La sincronizzazione di IBM Db2 HADR sta funzionando. Verificare con\<l'utente db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Eseguire la migrazione del nodo che esegue il database Db2 primario eseguendo il comando seguente:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Al termine della migrazione, l'output dello stato crm è simile al:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nell'immagine seguente:

![DBACockpit - Post-Migrazione](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

La migrazione delle risorse con "crm resource migrate" crea vincoli di posizione. I vincoli di posizione devono essere eliminati. Se i vincoli di posizione non vengono eliminati, la risorsa non può eseguire il failback o è possibile che si verifichino acquisizioni indesiderate. 

Eseguire la migrazione della risorsa a azibmdb01 e cancellare i vincoli di posizioneMigrate the resource back to *azibmdb01* and clear the location constraints
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **res_name> \<host \<della migrazione delle risorse di risorse di crm:crm resource migrate res_name> host>:** Crea vincoli di posizione e può causare problemi con l'acquisizione
- **risorsa crm \<cancella res_name>**: Cancella i vincoli di posizione
- **crm resource \<cleanup res_name>**: Cancella tutti gli errori della risorsa

### <a name="test-the-fencing-agent"></a>Testare l'agente di scherma

In questo caso, testiamo la scherma SBD, che ti consigliamo di fare quando si utilizza SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Il nodo del cluster *azibmdb01* deve essere riavviato. Il ruolo HADR primario IBM Db2 verrà spostato in *azibmdb02*. Quando *azibmdb01* torna in linea, l'istanza Db2 verrà spostata nel ruolo di un'istanza di database secondaria. 

Se il servizio Pacemaker non si avvia automaticamente nel primo database primario riavviato, assicurarsi di avviarlo manualmente con:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testare una acquisizione manuale

È possibile testare un'acquisizione manuale arrestando il servizio Pacemaker sul nodo *azibmdb01:You* can test a manual take over by stopping the Pacemaker service on azibmdb01 node:
<pre><code>service pacemaker stop</code></pre>

stato su *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Dopo il failover, è possibile riavviare il servizio in *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Interrompere il processo Db2 nel nodo che esegue il database primario HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

L'istanza Db2 avrà esito negativo e Pacemaker segnalerà il seguente stato:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker riavvierà l'istanza del database primario Db2 sullo stesso nodo oppure eseguirà il failover al nodo che esegue l'istanza del database secondario e viene segnalato un errore.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Terminare il processo Db2 nel nodo che esegue l'istanza del database secondario

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Il nodo entra in errore dichiarato e l'errore segnalato
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

L'istanza Db2 viene riavviata nel ruolo secondario assegnato in precedenza.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arrestare DB tramite db2stop forza sul nodo che esegue l'istanza del database primario HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Come utente\<db2 sid> eseguire il comando db2stop force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Rilevato errore
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

L'istanza del database secondario Db2 HADR è stata promossa nel ruolo primario
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arresti anomali della macchina virtuale con riavvio nel nodo che esegue l'istanza del database primario HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promuoverà l'istanza secondaria al ruolo dell'istanza primaria. L'istanza primaria precedente si sposterà nel ruolo secondario dopo la macchina virtuale e tutti i servizi sono completamente ripristinati dopo il riavvio della macchina virtuale:The old primary instance will move into the secondary role after the VM and all services are fully restored after the VM reboot:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Arresto anomalo della macchina virtuale che esegue l'istanza del database primario HADR con "arresto"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

In tal caso, Pacemaker rileverà che il nodo che esegue l'istanza del database primario non risponde.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Il passo successivo è quello di verificare la presenza di una situazione *di Split cervello.* Dopo che il nodo superstite ha determinato che il nodo che ha eseguito l'ultima istanza del database primario è inattivo, viene eseguito un failover delle risorse.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


In caso di "arresto" del nodo, il nodo in cui si è verificato l'errore deve essere riavviato tramite gli strumenti di gestione di Azure (nel portale di Azure, in PowerShell o nell'interfaccia della riga di comando di Azure). Dopo che il nodo in errore è di nuovo online, avvia l'istanza Db2 nel ruolo secondario.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Passaggi successivi
- [Architettura e scenari di disponibilità elevata per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurare Pacemaker in SUSE Linux Enterprise Server in AzureSet up Pacemaker on SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

