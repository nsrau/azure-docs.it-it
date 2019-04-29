---
title: Configurare IBM Db2 HADR in macchine virtuali di Azure (VM) | Microsoft Docs
description: Stabilire la disponibilità elevata di IBM Db2 LUW nelle macchine virtuali di Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835950"
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Disponibilità elevata di IBM Db2 LUW in macchine virtuali di Azure su SUSE Linux Enterprise Server con Pacemaker

IBM Db2 LUW (Linux, Unix e Windows) nella [configurazione HADR](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) costituiti da un nodo che esegue un'istanza del database primario e almeno un nodo che esegue un'istanza del database secondario. Le modifiche all'istanza del database primario vengono replicate all'istanza di database secondario in modo sincrono o asincrono, dipende dalla configurazione. 

Questo articolo descrive come distribuire e configurare le macchine virtuali, installare il framework del cluster e installare e configurare IBM Db2 LUW nella configurazione HADR. L'articolo non illustra come installare e configurare IBM Db2 LUW nell'installazione del software SAP o HADR. Per eseguire queste attività sono disponibili riferimenti a manuali di installazione di SAP e IBM. Lo stato attivo è su parti specifiche nell'ambiente Azure. 

Le versioni di IBM Db2 supportate sono 10.5 e versioni successive come documentato nella nota SAP #[1928533].

Leggere le note SAP e la documentazione prima di usare per raggiungere un'installazione seguenti:

| Nota SAP | DESCRIZIONE |
| --- | --- |
| [1928533] | Applicazioni SAP in Azure: prodotti e tipi di macchine virtuali di Azure supportati. |
| [2015553] | SAP in Microsoft Azure: Prerequisiti di supporto |
| [2178632] | Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [2191498] | SAP in Linux con Azure: monitoraggio avanzato |
| [2243692] | Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Note sull'installazione |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2233094] |DB6: Applicazioni SAP in Azure con IBM Db2 per Linux, UNIX e Windows - informazioni aggiuntive |
| [1612105] |DB6: Domande frequenti sul ripristino di emergenza (HADR) di Db2 la disponibilità elevata |


| Documentazione | 
| --- |
| [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) dispone di tutte le note su SAP necessarie per Linux |
| [Pianificazione di macchine virtuali di Azure e implementazione per SAP in Linux] [ planning-guide] Guida |
| [Distribuzione di macchine virtuali di Azure per SAP in Linux] [ deployment-guide] (questo articolo) |
| [Distribuzione DBMS di macchine virtuali di Azure per SAP in Linux] [ dbms-guide] Guida |
| [Carico di lavoro SAP in Azure elenco di controllo di pianificazione e distribuzione][azr-sap-plancheck] |
| [Guide alle procedure consigliate per SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp] |
| [SUSE Linux Enterprise ad alta disponibilità estensione 12 SP3][sles-ha-guide] |
| [Distribuzione di DBMS di macchine virtuali di Azure di IBM Db2 per carico di lavoro SAP][dbms-db2] |
| [Ripristino di emergenza disponibilità elevata di IBM Db2 11.1][db2-hadr-11.1] |
| [Ripristino di emergenza disponibilità elevata di IBM Db2 10.5 R][db2-hadr-10.5] |

## <a name="overview"></a>Panoramica
Per ottenere disponibilità elevata, IBM Db2 LUW con HADR è installato in almeno due macchine virtuali di Azure, che vengono distribuite in un' [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) o attraverso [zone di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). Il grafico seguente illustra una configurazione del server di database due macchine virtuali di Azure. Sia il server di database macchine virtuali di Azure sono proprio spazio di archiviazione collegato e siano in esecuzione. In HADR, un'istanza di database in una delle macchine virtuali di Azure ha il ruolo dell'istanza primaria. Tutti i client connessi a questa istanza primaria. Tutte le modifiche nelle transazioni di database vengono rese persistenti in locale nel log delle transazioni Db2. I record del log delle transazioni vengono salvati in modo permanente in locale, i record vengono trasferiti tramite TCP/IP all'istanza del database nel server di database secondo, il server di standby o istanza di standby. L'istanza di standby aggiorna il database locale eseguendo il rollforward della transazione trasferita i record del log. Pertanto, il server di standby viene mantenuto sincronizzato con il server primario.

HADR è solo una funzionalità di replica. Dispone di alcun rilevamento degli errori e non funzionalità di acquisizione della proprietà o il failover automatico. Un'acquisizione della proprietà o il trasferimento per il server di standby deve essere avviato manualmente da un amministratore del database. Per ottenere un'acquisizione automatica e il rilevamento degli errori, è possibile usare funzionalità di clustering Linux Pacemaker. Pacemaker monitora il server/istanze di due database. Quando si blocca l'istanza o un server database primario, avvia Pacemaker un' **automatica** acquisizione della proprietà HADR dal server di standby e assicura anche che l'indirizzo IP virtuale viene assegnato al nuovo server primario.

![Panoramica della disponibilità elevata di IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Per SAP server applicazioni per connettersi al database primario, è necessario un nome host virtuale e un indirizzo IP virtuale. Nel caso di un failover, i server applicazioni SAP si connetteranno alla nuova istanza di database primario. In un ambiente Azure, un' [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) è necessaria per usare un indirizzo IP virtuale nel modo necessario per HADR di IBM Db2. 

Per una migliore comprensione, come IBM Db2 LUW con HADR e Pacemaker si inserisce in una configurazione di sistema SAP a disponibilità elevata, la figura seguente viene presentata una panoramica di un'installazione a disponibilità elevata di un sistema SAP basato su database IBM Db2. Questo articolo illustra solo IBM Db2 e fa riferimento ad altri articoli sull'impostazione altri componenti di sistema SAP.

![Panoramica sull'ambiente IBM DB2 a disponibilità elevata completa](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Panoramica generale dei passaggi necessari
Per distribuire una configurazione di IBM Db2, questi passaggi devono essere trattati:

  + Piano di ambiente
  + Distribuire le macchine virtuali
  + Aggiornare SUSE Linux e configurare i file System
  + Installare e configurare Pacemaker
  + Installare [NFS a disponibilità elevata][nfs-ha]
  + Installare [ASCS/ERS in cluster separato][ascs-ha] 
  + Installare i database IBM Db2 con l'opzione Distributed e la disponibilità elevata (SWPM)
  + Installazione/creare nodo secondario del database e l'istanza e configurare HADR
  + Verificare che funzioni HADR
  + Applicare la configurazione di Pacemaker al controllo IBM Db2
  + Configurare Azure Load Balancer 
  + Installare Primary + finestra di dialogo Server applicazioni
  + Controllo/adattare configurazione del server applicazioni SAP
  + Eseguire il failover o test di acquisizione della proprietà



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Pianificazione dell'infrastruttura di Azure per l'hosting di IBM Db2 LUW con HADR

Scorrere la pianificazione prima di eseguire la distribuzione. In fase di compilazione le basi per la distribuzione di una configurazione di Db2 con HADR in Azure. Elementi chiave che devono far parte della pianificazione per IMB Db2 LUW (parte del database dell'ambiente SAP).

| Argomento | Breve descrizione |
| --- | --- |
| Definire gruppi di risorse di Azure | Gruppi di risorse in cui si distribuisce macchine Virtuali, rete virtuale, Azure Load Balancer e altre risorse. Può essere nuovo o esistente |
| Rete virtuale / definizione della Subnet | Macchine virtuali per Azure Load Balancer e IBM Db2 in cui sono recupero da distribuire. Può essere nuovo o esistente |
| Macchine virtuali che ospitano LUW di IBM Db2 | Dimensioni VM, archiviazione, rete, indirizzo IP |
| Nome host virtuale e indirizzo IP virtuale per il database IBM Db2| IP/nome host virtuale utilizzato per la connessione dei server applicazioni SAP. **db-virt-hostname**, **db-virt-ip** |
| Fencing in Azure | Azure isolamento o isolamento tramite SBD (altamente consigliato). Non è consentita per evitare situazioni di split brain (metodo) |
| SBD VM | Dimensioni della macchina virtuale di SBD, archiviazione, rete |
| Azure Load Balancer | Utilizzo di Basic o Standard (scelta consigliata), probe porta per il database di Db2 (un Consiglio 62500) **porta probe** |
| Risoluzione dei nomi| Risoluzione dei nomi come funziona nell'ambiente. Servizio DNS è vivamente consigliato. È possibile usare il file hosts locale |
    
Altre informazioni sull'uso di Pacemaker di Linux in Azure sono disponibili negli articoli seguenti:

- [Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Distribuzione su SUSE Linux

L'agente delle risorse per IBM Db2 LUW è incluso in SUSE Linux Enterprise Server for SAP Applications. Per la configurazione descritta in questo documento, è obbligatorio usare SUSE Linux Server for SAP Applications. Azure Marketplace contiene un'immagine per SUSE Enterprise Server for SAP Applications 12 che è possibile usare per distribuire nuove macchine virtuali di Azure. Essere a conoscenza dei modelli diversi/servizio di supporto offerti da SUSE tramite le raccolte di Azure quando si sceglie l'immagine di macchina virtuale nella raccolta di macchine Virtuali di Azure. 

### <a name="hosts---dns-updates"></a>Hosts - gli aggiornamenti DNS
Creare un elenco di tutti i nomi host tra cui i nomi host virtuali e aggiornare i server DNS per abilitare l'indirizzo IP appropriato per la risoluzione nome host. Nel caso, un server DNS non esiste o non si è in grado di aggiornare e creare voci DNS, è necessario usare i file hosts locale delle singole macchine virtuali che fanno parte di questo scenario. In caso di utilizzo delle voci dei file host, è necessario assicurarsi che le voci vengono applicate a tutte le macchine virtuali nell'ambiente di sistema SAP. Tuttavia, si consiglia per utilizzare il DNS che idealmente è estesi in Azure


### <a name="manual-deployment"></a>Distribuzione manuale

Assicurarsi che il sistema operativo selezionato è supportato da IBM/SAP per IBM Db2 LUW. L'elenco delle versioni del sistema operativo supportate per macchine virtuali di Azure e le versioni di Db2 è disponibile nella nota SAP [1928533]. L'elenco delle versioni del sistema operativo per le singole versioni di Db2 è disponibile in SAP Product Availability Matrix. Si consiglia un minimo di SLES 12 SP3 in seguito ai miglioramenti delle prestazioni correlate di Azure in questo o in un secondo momento le versioni di SUSE Linux.

1. Creare o selezionare un gruppo di risorse
2. Creare o selezionare una rete virtuale e subnet
3. Crea set di disponibilità di Azure o si distribuiscono nella zona di disponibilità
    + Disponibilità, set - impostare due domini di aggiornamento max
4. Creare la macchina virtuale 1.
    + Usa SLES per immagini SAP nella raccolta di Azure
    + Selezionare Azure set di disponibilità creata nel passaggio 3 oppure selezionare zona di disponibilità
5.  Creare la macchina virtuale 2.
    + Usa SLES per immagini SAP nella raccolta di Azure
    + Selezionare Azure set di disponibilità in creato nel passaggio 3. oppure selezionare la zona di disponibilità - non della stessa area come indicato nel passaggio 3.
6. Aggiungere dischi dati per le macchine virtuali: verificare l'indicazione del programma di installazione del file System nell'articolo [distribuzione DBMS di macchine virtuali di Azure di IBM Db2 per carico di lavoro SAP][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker
    
Seguire i passaggi descritti in [configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure][sles-pacemaker] per creare un cluster Pacemaker di base per questo server IBM Db2. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Installare l'ambiente SAP e IBM Db2 LUW

Prima di iniziare l'installazione di un ambiente SAP in IBM Db2 LUW basato esaminare (collegamenti forniti all'inizio dell'articolo):

+ Documentazione di Azure
+ Documentazione di SAP
+ Documentazione di IBM

Controllare manuali dell'installazione di SAP su come installare applicazioni basate su NetWeaver in IBM Db2 LUW.

È possibile trovare le guide sull'uso del portale SAP aiutare il [Finder di Guida all'installazione di SAP][sap-instfind]

È possibile filtrare la ricerca per ridurre il numero di guide disponibili con i filtri di impostazioni:

+ Vorrei: "Installare un nuovo sistema"
+ Il Database: "IBM Db2 per Linux, Unix e Windows"
+ Filtri aggiuntivi per le versioni SAP Netweaver, configurazione dello stack o sistema operativo.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Hint di installazione per la configurazione di IBM Db2 LUW con HADR

Configurare l'istanza di database IBM Db2 LUW primaria:

- Usare la disponibilità elevata o l'opzione distributed
- Installare l'istanza SAP ASCS/ERS e il Database
- Eseguire un backup del database appena installato


> [!IMPORTANT] 
> Annotare la "porta di comunicazione del Database" impostata durante l'installazione. Deve essere lo stesso numero di porta per entrambe le istanze del database

Per configurare il server di database di Standby procedura copia di sistema omogeneo SAP usando, eseguire questi passaggi:

  - Usare l'opzione di copia di sistema: istanza di Database - distribuiti - sistemi di destinazione.
  - Come metodo di copia, scegliere Copia di sistema omogeneo in modo che è possibile usare backup per ripristinare un backup nel server/istanza di standby
  - Quando si raggiunge il passaggio di uscita per ripristinare il database per la copia di sistema omogeneo, chiudere il programma di installazione. Ripristinare il database da un backup dell'host primario. Tutte le fasi di installazione successivi sono già state eseguite nel server di database primario
- Configurare HADR per IBM Db2

> [!NOTE]
> Installazione/configurazione specifica di Azure e Pacemaker. Durante la procedura di installazione tramite SAP Software Provisioning Manager, c'è una domanda sulla disponibilità elevata per IBM Db2 LUW esplicita:
>+ Non selezionare pureScale IBM Db2
>+ Non si seleziona "installare IBM Tivoli: sistema di automazione per Multiplatforms
>+ Non si seleziona "File di configurazione del cluster di generare"

> [!NOTE]
>Quando si usa un dispositivo SBD per Linux Pacemaker, impostare i parametri di Db2 HADR
>+ Durata intervallo di peer HADR (secondi) (HADR_PEER_WINDOW) = 300  
>+ Valore di timeout HADR (HADR_TIMEOUT) = 60

> [!NOTE]
>Utilizzo di agenti di fencing Pacemaker di Azure:
>+ Durata intervallo di peer HADR (secondi) (HADR_PEER_WINDOW) = 900  
>+ Valore di timeout HADR (HADR_TIMEOUT) = 60

I parametri sono consigliati basato su test iniziale/acquisire la proprietà di failover. È obbligatorio per testare il corretto funzionamento dell'acquisizione della proprietà e il failover con queste impostazioni dei parametri. Poiché le configurazioni dei singoli possono variare, questi parametri potrebbero richiedere regolazione. 

> [!IMPORTANT]
> Specifiche per IBM Db2 in configurazione HADR con il normale avvio - l'istanza del database secondario/standby deve essere in esecuzione prima di poter avviare l'istanza di database primario.

A scopo dimostrativo e le procedure descritte in questo documento, il database SID è **PTR**.

##### <a name="ibm-db2-hadr-check"></a>Controllo HADR di IBM Db2
Una volta configurato HADR e lo stato è CONNECTED e PEER sui nodi primari e di standby.

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



## <a name="db2-pacemaker-configuration"></a>Configurazione di Pacemaker di DB2

Quando si usa Pacemaker per il failover automatico in caso di errore in un nodo, è necessario configurare di conseguenza le istanze di Db2 e Pacemaker. Questa sezione viene descritta in questo tipo di configurazione.

Gli elementi seguenti sono preceduti:

- **[A]**  - applicabile a tutti i nodi
- **[1]**  - applicabile solo al nodo 1 
- **[2]**  - applicabile solo al nodo 2.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Prerequisiti per la configurazione di Pacemaker:
1. Arrestare entrambi server di database con db2 utente\<sid > con db2stop
2. Ambiente della shell cambia per db2\<sid > è consigliabile per utilizzare lo strumento di Yast utente "/ bin/ksh" - 


### <a name="pacemaker-configuration"></a>Configurazione di pacemaker:

**[1]**  Specifica configurazione di Pacemaker IBM Db2 HADR
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Risorse creare IBM Db2
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
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

**[1]**  Risorse avviare IBM Db2 - put Pacemaker dalla modalità manutenzione
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodi configurati
# <a name="5-resources-configured"></a>5 configurate delle risorse

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Elenco completo delle risorse:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Azibmdb02 avviata
#  <a name="resource-group-gipdb2ptrptr"></a>Gruppo di risorse: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Azibmdb02 avviata
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Azibmdb02 avviata
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Set Master/Slave: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Master: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Gli slave: [azibmdb01]
</pre>

> [!IMPORTANT]
> La gestione di Pacemaker istanza in cluster Db2 deve essere eseguita tramite gli strumenti di Pacemaker. Uso dei comandi di db2 (ad esempio db2stop) verrà rilevato da Pacemaker come errore di risorsa. In caso di manutenzione, è possibile inserire i nodi o le risorse per la modalità di manutenzione e Pacemaker verrà sospeso risorse di monitoraggio e i comandi di amministrazione di normali db2 sono utilizzabile.


### <a name="configure-azure-load-balancer"></a>Configurare Azure Load Balancer
È consigliabile usare la [Azure SKU di Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Configurare il bilanciamento del carico di Azure (tramite il portale di Azure). Prima, creare il pool di indirizzi IP front-end:

   1. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **pool di indirizzi IP front-end**e selezionare **Add**.
   2. Immettere il nome del nuovo pool di IP front-end (ad esempio, **connessione a Db2**).
   3. Impostare il **assegnazione** al **statici** e immettere l'indirizzo IP **Virtual IP** definite all'inizio.
   4. Selezionare **OK**.
   5. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.

2. Passaggio successivo consiste nel creare un pool back-end:

   1. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **pool back-end**e selezionare **Add**.
   2. Immettere il nome del nuovo pool di back-end (ad esempio, **Db2-backend**).
   3. Selezionare **Aggiungi una macchina virtuale**.
   4. Selezionare le macchine virtuali/set di disponibilità che ospita il database IBM Db2 creato nel passaggio 3.
   5. Selezionare le macchine virtuali del cluster di IBM Db2.
   6. Selezionare **OK**.

3. Terzo passaggio consiste nel creare un probe di integrità:

   1. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **probe di integrità**e selezionare **Add**.
   2. Immettere il nome del nuovo probe di integrità (ad esempio, **Db2-hp**).
   3. Selezionare **TCP** come il protocollo e porte **62500**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
   4. Selezionare **OK**.

4. Creare le regole di bilanciamento del carico:

   1. Nel portale di Azure, aprire il bilanciamento del carico di Azure, selezionare **regole di bilanciamento del carico**e selezionare **Add**.
   2. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, **Db2-SID**).
   3. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creata in precedenza (ad esempio, **Db2-frontend**).
   4. Mantenere il **Protocol** impostata su **TCP**e immettere la porta *porta di comunicazione Database*.
   5. Aumentare il valore di **Timeout di inattività** a 30 minuti.
   6. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
   7. Selezionare **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Apportare modifiche ai profili SAP per l'utilizzo di IP virtuale per la connessione
Il livello applicazione SAP deve usare l'indirizzo IP virtuale è definito e configurato per il bilanciamento del carico di Azure per connettersi all'istanza primaria della configurazione HADR. Le modifiche seguenti sono necessari.

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installazione principale e finestra di dialogo applicazione server

Quando installazione primario e i server applicazioni di finestra di dialogo con una configurazione HADR di Db2, si dovranno usare nome host virtuale scelto per la configurazione. 

Nel caso in cui è stata eseguita l'installazione prima di creare la configurazione HADR di Db2, è necessario apportare modifiche come descritto nel paragrafo precedente e, come indicato di seguito, per gli stack SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Controllare i sistemi stack ABAP + Java o Java URL JDBC

Utilizzare lo strumento di configurazione di J2EE per verificare o aggiornare l'URL di JDBC. Il lo strumento di configurazione di J2EE è lo strumento con interfaccia grafico, di conseguenza devi **X server** installato:
 
1. Accedere al server applicazioni principale dell'istanza di J2EE ed eseguire:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. Nel riquadro di sinistra, scegliere l'archivio di protezione.
2. Nel riquadro di destra, scegliere il chiavejdbc/pool/<SAPSID>/url.
2. Modificare il nome host nell'URL di JDBC con il nome host virtuale <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Scegliere Aggiungi.
5. Per salvare le modifiche, scegliere l'icona del disco nell'angolo superiore sinistro.
5. Chiudere lo strumento di configurazione.
5. Riavviare l'istanza di Java.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Configurazione di archiviare i log per l'installazione di HADR
Per configurare il log di Db2 dell'archiviazione per il programma di installazione HADR, è consigliabile configurare sia il database primario e il database di standby per avere la funzionalità di recupero automatico dei log da tutti i percorsi di archivio di log. Sia il database primario e il database di standby deve essere in grado di recuperare file di archivio dei log da tutte le posizioni di archiviazione log in cui uno dei database istanze potrebbero archiviare i file di log. 

L'archiviazione dei log viene eseguita solo dal database primario. Se si modificano i ruoli HADR dei server di database o se si verifica un errore, il nuovo database primario è responsabile per l'archiviazione dei log. Se è stato configurato percorsi di archiviazione di log diverso, i log potrebbero essere archiviati due volte e, nel caso di aggiornamento remoto o locale, potrebbe essere necessario copiare manualmente i log archiviati dal server primario precedente al percorso del log attivo del nuovo server primario.

È consigliabile configurare Condivisione NFS comune in cui vengono scritti i log da entrambi i nodi. Deve essere a disponibilità elevata NFS. 

È possibile usare NFS a disponibilità elevata esistente usato per i trasporti, directory del profilo. Lettura:

- [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server][nfs-ha] 
- [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure su SUSE Linux Enterprise Server con file NetApp di Azure per le applicazioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) su come usare [file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) per creare le condivisioni NFS


## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come è possibile testare la configurazione Db2 HADR. **Ogni test presuppone che si è connessi come utente radice** e IBM Db2 primario è in esecuzione **azibmdb01** macchina virtuale.

Lo stato iniziale per tutti i test case è illustrato di seguito: (crm_mon - r o crm status)

- **lo stato di CRM** è uno stato di Pacemaker snapshot in fase di esecuzione 
- **-r crm_mon** è output continuo dello stato di Pacemaker

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

Lo stato originale in un sistema SAP è documentato nella transazione DBACOCKPIT--> Configurazione--> Panoramica, ad esempio:

![DBACockpit - precedente alla migrazione](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Acquisire la proprietà di test di IBM Db2.


> [!IMPORTANT] 
> Prima di iniziare il test, assicurarsi che Pacemaker non siano presenti azioni non riuscite (crm status), non sono presenti vincoli di posizione (leftovers del test di migrazione) e del funzionamento della sincronizzazione di IBM Db2 HADR. Contattare l'utente db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Eseguire la migrazione il nodo che esegue il database di Db2 primario eseguendo il seguente comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Al termine della migrazione, l'output di stato crm sarà simile a:
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

Lo stato originale in un sistema SAP è documentato nella transazione DBACOCKPIT--> Configurazione--> Panoramica, ad esempio: ![DBACockpit - dopo la migrazione](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migrazione delle risorse con "eseguire la migrazione di risorse crm" Crea vincoli di posizione. I vincoli di posizione devono essere eliminati. Se non vengono eliminati i vincoli di posizione, la risorsa non è possibile eseguire il failback o è possibile riscontrare acquisizioni indesiderati. 

Eseguire la migrazione di risorse al **azibmdb01** e deselezionare i vincoli di posizione
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- eseguire la migrazione di risorse CRM < res_name > <host> : crea vincoli di posizione e può causare problemi con l'acquisizione della proprietà
- risorsa CRM deseleziona < res_name - > Cancella i vincoli di posizione
- pulizia delle risorse CRM < res_name - > Cancella tutti gli errori della risorsa


### <a name="test-the-fencing-agent"></a>Testare l'agente di isolamento

In questo caso, viene testata l'isolamento tramite SBD, è consigliata per l'utilizzo con SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Nodo del cluster **azibmdb01** deve essere riavviato. IBM Db2 ruolo HADR primario verrà spostato **azibmdb02**. Quando **azibmdb01** è nuovamente online, Db2 istanza sta per spostare il ruolo di un'istanza del database secondario. 

Per il caso in cui il servizio Pacemaker non viene avviata automaticamente nel database primario precedente riavviato, assicurarsi di avviarla manualmente con:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testare un'acquisizione manuale

È possibile testare un'acquisizione manuale arrestando il servizio Pacemaker nel **azibmdb01** nodo:
<pre><code>service pacemaker stop</code></pre>

status on **azibmdb02**
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

Dopo il failover, è possibile avviare nuovamente il servizio sul **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Terminare il processo di Db2 nel nodo che esegue il database primario HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

L'istanza di Db2 sta esito negativo e verrà segnalato Pacemaker seguendo lo stato:

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

Pacemaker sta per riavviare l'istanza di database primario Db2 sullo stesso nodo o failover al nodo che esegue il database secondario creata un'istanza e viene restituito un errore.

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


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>Terminare il processo di Db2 nel nodo che esegue l'istanza di database secondario

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Il nodo ottiene in errore indicato ed errore segnalato
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

La lettura di Db2 istanza riavviata al ruolo secondario di che fosse assegnato prima

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



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Forzare l'arresto DB tramite db2stop sul nodo che esegue l'istanza di database primario HADR

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

Come utente db2\<sid > esecuzione forzata db2stop comando:
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

L'istanza del database secondario HADR Db2 alzata di livello al ruolo primario
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


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>Arresto anomalo del sistema della macchina virtuale con il riavvio nel nodo che esegue l'istanza di database primario HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker alzerà di livello nell'istanza secondaria per il ruolo di istanza primaria. L'istanza primaria precedente sposta nel ruolo secondario dopo la macchina virtuale e tutti i servizi sono completamente ripristinati dopo il riavvio della macchina virtuale:

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



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>La macchina virtuale che esegue l'istanza di database primario HADR con "halt" di arresto anomalo

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

In tal caso, Pacemaker rileverà che il nodo che esegue l'istanza di database primario non risponde.

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

il passaggio successivo consiste nel verificare la presenza di un **dividere cervello** situazione. Una volta che il nodo che è stata eseguita l'istanza del database primario ultima, sia verso il basso il nodo integro, un failover di risorse sta per essere eseguita
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


Nel caso di "arresto" del nodo, il nodo in errore deve essere riavviato tramite gli strumenti di gestione di Azure (portale, PowerShell, AzureCLI,...). Il nodo in errore sta per avviare l'istanza di Db2 nel ruolo secondario, quando torna online.

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
Consultare la documentazione:

- [Scenari e architettura a disponibilità elevata per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurazione di Pacemaker su SUSE Linux immettere
- pri
- Se Server in Azure] (https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

