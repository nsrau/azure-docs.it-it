---
title: Configurare IBM DB2 HADR in macchine virtuali (VM) di Azure | Microsoft Docs
description: Stabilire la disponibilità elevata di IBM DB2 LUW in macchine virtuali (VM) di Azure.
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
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 4571b0e1a2c9207ce913901f11157f5155201393
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100028"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure in SUSE Linux Enterprise Server con pacemaker

IBM DB2 per Linux, UNIX e Windows (LUW) nella [configurazione di disponibilità elevata e ripristino di emergenza (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) è costituito da un nodo che esegue un'istanza di database primario e almeno un nodo che esegue un'istanza del database secondario. Le modifiche apportate all'istanza del database primario vengono replicate in modo sincrono o asincrono in un'istanza del database secondario, a seconda della configurazione. 

Questo articolo descrive come distribuire e configurare le macchine virtuali (VM) di Azure, installare il Framework del cluster e installare IBM DB2 LUW con la configurazione di HADR. 

L'articolo non illustra come installare e configurare IBM DB2 LUW con l'installazione del software HADR o SAP. Per consentire l'esecuzione di queste attività, vengono forniti riferimenti ai manuali di installazione di SAP e IBM. Questo articolo è incentrato sulle parti specifiche dell'ambiente Azure. 

Le versioni supportate di IBM DB2 sono 10,5 e versioni successive, come descritto nella nota SAP [1928533].

Prima di iniziare un'installazione, vedere le note e la documentazione SAP seguenti:

| Nota SAP | Descrizione |
| --- | --- |
| [1928533] | Applicazioni SAP in Azure: Prodotti e tipi di macchine virtuali di Azure supportati |
| [2015553] | SAP in Azure: Prerequisiti di supporto |
| [2178632] | Metriche di monitoraggio principali per SAP in Azure |
| [2191498] | SAP in Linux con Azure: Monitoraggio avanzato |
| [2243692] | VM Linux in Azure (IaaS): problemi delle licenze SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: Note sull'installazione |
| [1999351] | Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2233094] | DB6: Applicazioni SAP in Azure che usano IBM DB2 per Linux, UNIX e Windows-informazioni aggiuntive |
| [1612105] | DB6: Domande frequenti su DB2 con HADR |


| Documentazione | 
| --- |
| [Wiki della community SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Include tutte le note SAP necessarie per Linux |
| Guida alla [pianificazione e implementazione di macchine virtuali di Azure per SAP in Linux][planning-guide] |
| [Distribuzione di macchine virtuali di Azure per SAP in Linux][deployment-guide] (questo articolo) |
| Guida alla [distribuzione del sistema di gestione di database (DBMS) di macchine virtuali di Azure per SAP in Linux][dbms-guide] |
| [Elenco di controllo del carico di lavoro SAP in pianificazione e distribuzione di Azure][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server per le guide alle procedure consigliate di SAP Applications 12 SP3][sles-for-sap-bp] |
| [Estensione per la disponibilità elevata di SUSE Linux Enterprise 12 SP3][sles-ha-guide] |
| [Distribuzione DBMS di macchine virtuali di Azure IBM DB2 per il carico di lavoro SAP][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Panoramica
Per ottenere la disponibilità elevata, IBM DB2 LUW con HADR è installato in almeno due macchine virtuali di Azure, che vengono distribuite in un [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) o tra [zone di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

La grafica seguente mostra una configurazione di due macchine virtuali di Azure del server di database. Entrambe le macchine virtuali di Azure del server di database hanno una propria archiviazione collegata e sono in esecuzione. In HADR, un'istanza di database in una delle macchine virtuali di Azure ha il ruolo di istanza primaria. Tutti i client sono connessi a questa istanza primaria. Tutte le modifiche apportate alle transazioni del database vengono rese disponibili localmente nel log delle transazioni DB2. Poiché i record del log delle transazioni vengono salvati in modo permanente in locale, i record vengono trasferiti tramite TCP/IP all'istanza del database nel secondo server di database, nel server di standby o nell'istanza di standby. L'istanza standby aggiorna il database locale eseguendo il rollforward dei record del log delle transazioni trasferiti. In questo modo, il server di standby viene mantenuto sincronizzato con il server primario.

HADR è solo una funzionalità di replica. Non è disponibile alcun rilevamento degli errori e nessuna funzionalità di acquisizione automatica o di failover. Un'acquisizione o un trasferimento al server di standby deve essere avviato manualmente da un amministratore di database. Per ottenere un'acquisizione automatica e un rilevamento degli errori, è possibile usare la funzionalità di clustering pacemaker di Linux. Pacemaker monitora le due istanze del server di database. Quando l'istanza del server di database primario si arresta in modo anomalo, pacemaker avvia un'acquisizione *automatica* del HADR dal server di standby. Pacemaker garantisce inoltre che l'indirizzo IP virtuale venga assegnato al nuovo server primario.

![Panoramica della disponibilità elevata di IBM DB2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Per fare in modo che i server applicazioni SAP si connettano al database primario, sono necessari un nome host virtuale e un indirizzo IP virtuale. In caso di failover, i server applicazioni SAP si connetteranno a una nuova istanza di database primario. In un ambiente Azure è necessario un servizio di [bilanciamento del carico di Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) per usare un indirizzo IP virtuale nel modo necessario per HADR di IBM DB2. 

Per comprendere completamente il modo in cui IBM DB2 LUW con HADR e pacemaker si integrano in una configurazione di sistema SAP a disponibilità elevata, nell'immagine seguente viene presentata una panoramica di una configurazione a disponibilità elevata di un sistema SAP basato su un database IBM DB2. Questo articolo illustra solo IBM DB2, ma fornisce riferimenti ad altri articoli su come configurare altri componenti di un sistema SAP.

![Panoramica dell'ambiente completo della disponibilità elevata di IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Panoramica di alto livello dei passaggi necessari
Per distribuire una configurazione di IBM DB2, è necessario attenersi alla procedura seguente:

  + Pianificare l'ambiente.
  + Distribuire le macchine virtuali.
  + Aggiornare SUSE Linux e configurare i file System.
  + Installare e configurare pacemaker.
  + Installare [NFS a disponibilità elevata][nfs-ha].
  + Installare [ASC/ERS in un cluster separato][ascs-ha].
  + Installare il database IBM DB2 con l'opzione Distributed/High Availability (SWPM).
  + Installare e creare un nodo e un'istanza del database secondario e configurare HADR.
  + Verificare che HADR sia funzionante.
  + Applicare la configurazione pacemaker per controllare IBM DB2.
  + Configurare Azure Load Balancer.
  + Installare i server applicazioni primarie e di dialogo.
  + Controllare e adattare la configurazione dei server applicazioni SAP.
  + Eseguire il failover e i test di acquisizione.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Pianificare l'infrastruttura di Azure per l'hosting di IBM DB2 LUW con HADR

Completare il processo di pianificazione prima di eseguire la distribuzione. La pianificazione compila le basi per la distribuzione di una configurazione di DB2 con HADR in Azure. Nella tabella seguente sono elencati gli elementi chiave che devono essere parte della pianificazione per IMB DB2 LUW (parte del database dell'ambiente SAP):

| Argomento | Breve descrizione |
| --- | --- |
| Definire i gruppi di risorse di Azure | Gruppi di risorse in cui si distribuiscono VM, VNet, Azure Load Balancer e altre risorse. Può essere esistente o nuovo. |
| Definizione della rete virtuale/subnet | Dove vengono distribuite le macchine virtuali per IBM DB2 e Azure Load Balancer. Può essere esistente o appena creato. |
| Macchine virtuali che ospitano IBM DB2 LUW | Dimensioni della macchina virtuale, archiviazione, rete e indirizzo IP. |
| Nome host virtuale e IP virtuale per il database IBM DB2| Indirizzo IP virtuale o nome host usato per la connessione dei server applicazioni SAP. **DB-virt-hostname**, **DB-virt-IP**. |
| Schermatura di Azure | Schermatura di Azure o schermatura SBD (altamente consigliata). Metodo che consente di evitare situazioni di divisione cerebrale. |
| VM SBD | Dimensioni, archiviazione e rete della macchina virtuale SBD. |
| Azure Load Balancer | Utilizzo di Basic o standard (scelta consigliata), porta Probe per database DB2 (raccomandazione 62500) **Probe-Port**. |
| Risoluzione dei nomi| Funzionamento della risoluzione dei nomi nell'ambiente. Il servizio DNS è altamente consigliato. È possibile usare il file hosts locale. |
    
Per altre informazioni su pacemaker per Linux in Azure, vedere [configurare pacemaker in SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Distribuzione in SUSE Linux

Resource Agent per IBM DB2 LUW è incluso in SUSE Linux Enterprise Server per le applicazioni SAP. Per la configurazione descritta in questo documento, è necessario usare SUSE Linux Server per le applicazioni SAP. Azure Marketplace contiene un'immagine per SUSE Enterprise Server per applicazioni SAP 12 che è possibile usare per distribuire nuove macchine virtuali di Azure. Quando si sceglie un'immagine di macchina virtuale nel Marketplace di macchine virtuali di Azure, tenere presente i vari modelli di supporto o di servizio offerti da SUSE tramite Azure Marketplace. 

### <a name="hosts-dns-updates"></a>Ospita Aggiornamenti del DNS
Creare un elenco di tutti i nomi host, inclusi i nomi host virtuali, e aggiornare i server DNS per abilitare l'indirizzo IP appropriato per la risoluzione dei nomi host. Se un server DNS non esiste o non è possibile aggiornare e creare voci DNS, è necessario usare i file host locali delle singole VM che partecipano a questo scenario. Se si usano voci di file host, assicurarsi che le voci vengano applicate a tutte le macchine virtuali nell'ambiente di sistema SAP. Tuttavia, si consiglia di usare il DNS che, idealmente, si estende in Azure


### <a name="manual-deployment"></a>Distribuzione manuale

Verificare che il sistema operativo selezionato sia supportato da IBM/SAP per IBM DB2 LUW. L'elenco delle versioni del sistema operativo supportate per le macchine virtuali di Azure e le versioni DB2 è disponibile nella nota SAP [1928533]. L'elenco delle versioni del sistema operativo per ogni singola versione di DB2 è disponibile nella matrice di disponibilità del prodotto SAP. È consigliabile un minimo di SLES 12 SP3 a causa dei miglioramenti delle prestazioni correlati ad Azure in questa o nelle versioni successive di SUSE Linux.

1. Creare o selezionare un gruppo di risorse.
1. Creare o selezionare una rete virtuale e una subnet.
1. Creare un set di disponibilità di Azure o distribuire una zona di disponibilità.
    + Per il set di disponibilità, impostare il numero massimo di domini di aggiornamento su 2.
1. Creazione della macchina virtuale 1.
    + Usare SLES per l'immagine SAP in Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 o selezionare zona di disponibilità.
1.  Creare la macchina virtuale 2.
    + Usare SLES per l'immagine SAP in Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare zona di disponibilità (non la stessa zona del passaggio 3).
1. Aggiungere dischi dati alle macchine virtuali, quindi controllare l'indicazione di un file system installazione nell'articolo [distribuzione DBMS di macchine virtuali di Azure IBM DB2 per il carico di lavoro SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker
    
Per creare un cluster Pacemaker di base per questo server IBM DB2, vedere [configurare pacemaker in SUSE Linux Enterprise Server in Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installare l'ambiente IBM DB2 LUW e SAP

Prima di iniziare l'installazione di un ambiente SAP basato su IBM DB2 LUW, vedere la documentazione seguente:

+ Documentazione di Azure
+ Documentazione di SAP
+ Documentazione di IBM

I collegamenti a questa documentazione sono disponibili nella sezione introduttiva di questo articolo.

Consultare i manuali di installazione di SAP sull'installazione di applicazioni basate su NetWeaver in IBM DB2 LUW.

È possibile trovare le guide nel portale della Guida di SAP usando il [Finder della Guida all'installazione di SAP][sap-instfind].

È possibile ridurre il numero di guide visualizzate nel portale impostando i filtri seguenti:

- Vorrei: "Installare un nuovo sistema"
- Database: "IBM DB2 per Linux, UNIX e Windows"
- Filtri aggiuntivi per le versioni di SAP NetWeaver, la configurazione dello stack o il sistema operativo

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Suggerimenti sull'installazione per la configurazione di IBM DB2 LUW con HADR

Per configurare l'istanza primaria del database IBM DB2 LUW:

- Usare l'opzione di disponibilità elevata o distribuita.
- Installare SAP ASC/ERS e l'istanza di database.
- Eseguire un backup del nuovo database installato.


> [!IMPORTANT] 
> Prendere nota della "porta di comunicazione del database" impostata durante l'installazione. Deve corrispondere allo stesso numero di porta per entrambe le istanze di database

Per configurare il server di database di standby usando la procedura di copia di sistema omogenea di SAP, eseguire questi passaggi:

1. Selezionare l'opzione **copia di sistema** >**istanza database** **distribuito** > di **sistemi** > di destinazione.
1. Come metodo di copia, selezionare **sistema omogeneo** in modo da poter utilizzare il backup per ripristinare un backup nell'istanza del server di standby.
1. Quando si raggiunge il passaggio di uscita per ripristinare il database per la copia di sistema omogenea, uscire dal programma di installazione. Ripristinare il database da un backup dell'host primario. Tutte le fasi di installazione successive sono già state eseguite sul server di database primario.
1. Configurare HADR per IBM DB2.

   > [!NOTE]
   > Per l'installazione e la configurazione specifiche di Azure e pacemaker: Durante la procedura di installazione tramite SAP software Provisioning Manager, esiste una domanda esplicita sulla disponibilità elevata per IBM DB2 LUW:
   >+ Non selezionare **IBM DB2 pureScale**.
   >+ Non selezionare **Installa IBM Tivoli System Automation per piattaforme multipiattaforma**.
   >+ Non selezionare **genera file di configurazione del cluster**.

   Quando si usa un dispositivo SBD per pacemaker Linux, impostare i parametri DB2 HADR seguenti:
   + Durata della finestra peer HADR (secondi) (HADR_PEER_WINDOW) = 300  
   + Valore di timeout HADR (HADR_TIMEOUT) = 60

   Quando si usa un agente di schermatura pacemaker di Azure, impostare i parametri seguenti:
   + Durata della finestra peer HADR (secondi) (HADR_PEER_WINDOW) = 900  
   + Valore di timeout HADR (HADR_TIMEOUT) = 60

Si consiglia di usare i parametri precedenti in base al failover iniziale/test di acquisizione. È obbligatorio verificare la correttezza delle funzionalità di failover e acquisizione con queste impostazioni dei parametri. Poiché le singole configurazioni possono variare, i parametri potrebbero richiedere la regolazione. 

> [!IMPORTANT]
> Specifico per IBM DB2 con configurazione HADR con avvio normale: Prima di poter avviare l'istanza del database primario, è necessario che l'istanza del database secondario o standby sia attiva e in esecuzione.

A scopo dimostrativo e alle procedure descritte in questo articolo, il SID del database è **ptr**.

#### <a name="ibm-db2-hadr-check"></a>Verifica HADR IBM DB2
Dopo aver configurato HADR e lo stato è PEER e CONNECTED nei nodi Primary e standby, effettuare le seguenti verifiche:

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



## <a name="db2-pacemaker-configuration"></a>Configurazione di DB2 pacemaker

Quando si usa pacemaker per il failover automatico in caso di errore di un nodo, è necessario configurare le istanze DB2 e pacemaker di conseguenza. Questa sezione descrive questo tipo di configurazione.

Gli elementi seguenti sono preceduti da uno dei seguenti elementi:

- **[A]** : Applicabile a tutti i nodi
- **[1]** : Applicabile solo al nodo 1 
- **[2]** : Applicabile solo al nodo 2

**[A]** prerequisiti per la configurazione di pacemaker:
1. Arrestare entrambi i server di database con l'\<utente DB2 SID > con db2stop.
1. Modificare l'ambiente della Shell per\<DB2 SID > utente in */bin/ksh*. Si consiglia di usare lo strumento YaST. 


### <a name="pacemaker-configuration"></a>Configurazione pacemaker

**[1]** configurazione pacemaker specifica di IBM DB2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** creare risorse IBM DB2:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** avviare le risorse IBM DB2:
* Attivare la modalità manutenzione per pacemaker.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** verificare che lo stato del cluster sia OK e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodi configurati
# <a name="5-resources-configured"></a>5 risorse configurate

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Elenco completo delle risorse:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>STONITH-SBD (STONITH: External/SBD): Azibmdb02 avviato
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Gruppo di risorse: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Azibmdb02 avviato
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: heartbeat: Anything):      Azibmdb02 avviato
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Set master/slave: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Master: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Schiavi: [azibmdb01]
</pre>

> [!IMPORTANT]
> È necessario gestire l'istanza di DB2 in cluster di pacemaker usando gli strumenti pacemaker. Se si usano comandi DB2 come db2stop, pacemaker rileva l'azione come un errore di risorsa. Se si sta eseguendo la manutenzione, è possibile attivare la modalità di manutenzione per i nodi o le risorse. Pacemaker sospende le risorse di monitoraggio ed è quindi possibile usare i normali comandi di amministrazione di DB2.


### <a name="configure-azure-load-balancer"></a>Configurare Azure Load Balancer
Per configurare Azure Load Balancer, è consigliabile usare lo SKU di [Azure Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e quindi eseguire le operazioni seguenti:

1. Creare un pool di indirizzi IP front-end:

   a. Nel portale di Azure aprire il Azure Load Balancer, selezionare pool di **indirizzi IP**front-end e quindi selezionare **Aggiungi**.

   b. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **DB2-Connection**).

   c. Impostare l' **assegnazione** su **statico**e immettere l'indirizzo IP **virtuale-IP** definito all'inizio.

   d. Selezionare **OK**.

   e. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.

1. Creare un pool back-end:

   a. Nel portale di Azure aprire il Azure Load Balancer, selezionare **pool back-end**e quindi selezionare **Aggiungi**.

   b. Immettere il nome del nuovo pool back-end, ad esempio **DB2-backend**.

   c. Selezionare **Aggiungi una macchina virtuale**.

   d. Selezionare il set di disponibilità o le macchine virtuali che ospitano il database IBM DB2 creato nel passaggio precedente.

   e. Selezionare le macchine virtuali del cluster IBM DB2.

   f. Selezionare **OK**.

1. Creare un probe di integrità:

   a. Nella portale di Azure aprire il Azure Load Balancer, selezionare Probe **integrità**e selezionare **Aggiungi**.

   b. Immettere il nome del nuovo probe di integrità, ad esempio **DB2-HP**.

   c. Selezionare **TCP** come protocollo e la porta **62500**. Per impostare il valore di **intervallo** su **5**, impostare valore **soglia** non integro su **2**.

   d. Selezionare **OK**.

1. Creare le regole di bilanciamento del carico:

   a. Nella portale di Azure aprire il Azure Load Balancer, selezionare regole di **bilanciamento del carico**e quindi selezionare **Aggiungi**.

   b. Immettere il nome della nuova regola di Load Balancer (ad esempio, **DB2-SID**).

   c. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creato in precedenza, ad esempio **DB2-frontend**.

   d. Impostare il **protocollo** su **TCP**e immettere *porta di comunicazione del database*di porta.

   e. Aumentare il valore di **Timeout di inattività** a 30 minuti.

   f. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.

   g. Selezionare **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Apportare modifiche ai profili SAP per l'uso dell'indirizzo IP virtuale per la connessione
Per connettersi all'istanza primaria della configurazione HADR, il livello dell'applicazione SAP deve usare l'indirizzo IP virtuale definito e configurato per la Azure Load Balancer. Sono necessarie le seguenti modifiche:

SID\</sapmnt/>/profile/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installare i server applicazioni primarie e di dialogo

Quando si installano server applicazioni primari e di finestre di dialogo in una configurazione DB2 HADR, utilizzare il nome host virtuale selezionato per la configurazione. 

Se è stata eseguita l'installazione prima di creare la configurazione di DB2 HADR, apportare le modifiche descritte nella sezione precedente e come segue per gli stack Java SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Controllo URL JDBC per ABAP + Java o Java stack Systems

Usare lo strumento di configurazione J2EE per controllare o aggiornare l'URL JDBC. Poiché lo strumento di configurazione J2EE è uno strumento grafico, è necessario installare il server X:
 
1. Accedere al server applicazioni primario dell'istanza di J2EE ed eseguire:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Nel riquadro a sinistra scegliere **Archivio sicurezza**.
1. Nel riquadro destro scegliere la chiave JDBC/pool/\<SAPSID >/URL.
1. Modificare il nome host nell'URL JDBC per il nome host virtuale.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selezionare **Aggiungi**.
1. Per salvare le modifiche, selezionare l'icona del disco in alto a sinistra.
1. Chiudere lo strumento di configurazione di.
1. Riavviare l'istanza di Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurare l'archiviazione dei log per l'installazione di HADR
Per configurare l'archiviazione dei log DB2 per il programma di installazione di HADR, è consigliabile configurare il database primario e quello di standby in modo da ottenere la funzionalità di recupero log automatica da tutti i percorsi di archiviazione dei log. Sia il database primario che quello di standby devono essere in grado di recuperare i file di archivio di log da tutti i percorsi di archiviazione dei log in cui una delle istanze del database può archiviare i file di log. 

L'archiviazione dei log viene eseguita solo dal database primario. Se si modificano i ruoli HADR dei server di database o si verifica un errore, il nuovo database primario è responsabile dell'archiviazione dei log. Se sono stati configurati più percorsi di archiviazione dei log, è possibile che i log vengano archiviati due volte. In caso di un recupero locale o remoto, potrebbe essere necessario copiare manualmente i log archiviati dal server primario precedente al percorso di log attivo del nuovo server primario.

Si consiglia di configurare una condivisione NFS comune in cui i log vengono scritti da entrambi i nodi. La condivisione NFS deve essere a disponibilità elevata. 

È possibile utilizzare condivisioni NFS a disponibilità elevata per trasporti o directory di profilo. Per altre informazioni, vedere:

- [Disponibilità elevata per NFS in macchine virtuali di Azure in SUSE Linux Enterprise Server][nfs-ha] 
- [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server con Azure NetApp Files per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (per creare condivisioni NFS)


## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come è possibile testare l'installazione di DB2 HADR. *Ogni test presuppone che sia stato eseguito l'accesso come utente root* e che IBM DB2 Primary sia in esecuzione nella macchina virtuale *azibmdb01* .

Lo stato iniziale di tutti i test case è illustrato qui: (stato crm_mon-r o CRM)

- **lo stato di CRM** è uno snapshot dello stato di pacemaker in fase di esecuzione 
- **crm_mon-r** è l'output continuo dello stato di pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nella figura seguente:

![DBACockpit-pre-migrazione](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Acquisizione dei test di IBM DB2


> [!IMPORTANT] 
> Prima di iniziare il test, assicurarsi che:
> * Pacemaker non ha azioni non riuscite (stato CRM).
> * Nessun vincolo di posizione (avanzi del test di migrazione)
> * La sincronizzazione IBM DB2 HADR funziona. Verificare con l'utente\<DB2 SID > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Eseguire la migrazione del nodo che esegue il database DB2 primario eseguendo il comando seguente:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Al termine della migrazione, l'output dello stato CRM sarà simile al seguente:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nella figura seguente:

![DBACockpit-post-migrazione](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

La migrazione delle risorse con "migrazione di risorse CRM" crea vincoli di percorso. I vincoli di percorso devono essere eliminati. Se i vincoli di percorso non vengono eliminati, non è possibile eseguire il failback della risorsa oppure è possibile riscontrare acquisizioni indesiderate. 

Eseguire di nuovo la migrazione della risorsa a *azibmdb01* e cancellare i vincoli location
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migrazione \<di risorse CRM RES_NAME \<> host >:** Crea vincoli di posizione e può causare problemi con l'acquisizione
- **> RES_NAME cancellazione \<risorse CRM**: Cancella i vincoli di posizione
- **> di pulitura \<risorse CRM RES_NAME**: Cancella tutti gli errori della risorsa

### <a name="test-the-fencing-agent"></a>Testare l'agente di schermatura

In questo caso, viene testato il SBD di schermatura, che è consigliabile eseguire quando si usa SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Il nodo del cluster *azibmdb01* deve essere riavviato. Il ruolo HADR primario IBM DB2 verrà spostato in *azibmdb02*. Quando *azibmdb01* è di nuovo online, l'istanza di DB2 verrà spostata nel ruolo di un'istanza del database secondario. 

Se il servizio pacemaker non viene avviato automaticamente nel database primario precedentemente riavviato, assicurarsi di avviarlo manualmente con:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testare un'acquisizione manuale

È possibile testare un'acquisizione manuale arrestando il servizio pacemaker nel nodo *azibmdb01* :
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Dopo il failover, è possibile riavviare il servizio in *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Terminare il processo DB2 sul nodo che esegue il database primario HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

L'istanza di DB2 avrà esito negativo e pacemaker segnalerà lo stato seguente:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker riavvierà l'istanza del database primario DB2 nello stesso nodo oppure verrà eseguito il failover sul nodo che esegue l'istanza del database secondario e viene segnalato un errore.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Terminare il processo DB2 sul nodo che esegue l'istanza del database secondario

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Il nodo non è stato dichiarato ed è stato segnalato un errore
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

L'istanza di DB2 viene riavviata nel ruolo secondario assegnato in precedenza.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arrestare DB tramite db2stop Force sul nodo che esegue l'istanza del database primario HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Come utente DB2\<SID > Execute Command db2stop Force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Errore rilevato
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

L'istanza del database secondario DB2 HADR è stata innalzata nel ruolo primario
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arresto anomalo della macchina virtuale con riavvio sul nodo che esegue l'istanza del database primario HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promuoverà l'istanza secondaria al ruolo di istanza primaria. L'istanza primaria precedente verrà spostata nel ruolo secondario dopo il ripristino completo della macchina virtuale e di tutti i servizi dopo il riavvio della macchina virtuale:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Arrestare l'arresto anomalo della macchina virtuale che esegue l'istanza del database primario HADR con "Halt"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

In tal caso, pacemaker rileverà che il nodo che esegue l'istanza del database primario non risponde.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Il passaggio successivo consiste nel verificare la presenza di una situazione di *Split Brain* . Dopo che il nodo superstite ha determinato che il nodo che ha eseguito l'ultima esecuzione dell'istanza di database primario è inattivo, viene eseguito un failover delle risorse.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


In caso di "arresto" del nodo, il nodo con errori deve essere riavviato tramite gli strumenti di gestione di Azure (nel portale di Azure, PowerShell o nell'interfaccia della riga di comando di Azure). Quando il nodo con errore è di nuovo online, avvia l'istanza DB2 nel ruolo secondario.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Passaggi successivi
- [Architettura e scenari a disponibilità elevata per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurare pacemaker in SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

