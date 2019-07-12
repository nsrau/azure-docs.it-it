---
title: Configurare IBM Db2 HADR in macchine virtuali di Azure (VM) in RHEL | Microsoft Docs
description: Stabilire la disponibilità elevata di IBM Db2 LUW nelle macchine virtuali di Azure (VM) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812129"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Disponibilità elevata di IBM Db2 LUW in macchine virtuali di Azure su Red Hat Enterprise Linux Server

IBM Db2 per Linux, UNIX e Windows (LUW) nella [configurazione di ripristino (HADR) di emergenza e disponibilità elevata](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) è costituito da un nodo che esegue un'istanza del database primario e almeno un nodo che esegue un'istanza del database secondario. Modifiche all'istanza del database primario vengono replicate in un'istanza del database secondario in modo sincrono o asincrono, a seconda della configurazione. 

Questo articolo descrive come distribuire e configurare le macchine virtuali di Azure (VM), installare il framework del cluster e installare il LUW Db2 IBM con configurazione HADR. 

L'articolo non illustra come installare e configurare LUW Db2 IBM con l'installazione di software SAP o HADR. Per aiutarti a eseguire queste attività, offriamo i riferimenti a manuali di installazione di SAP e IBM. Questo articolo è incentrato sulle parti specifiche per l'ambiente di Azure. 

Le versioni di IBM Db2 supportate sono 10.5 e successive, come documentato nella nota SAP [1928533].

Prima di iniziare un'installazione, vedere la documentazione e note SAP seguenti:

| Nota SAP | DESCRIZIONE |
| --- | --- |
| [1928533] | Applicazioni SAP in Azure: Prodotti e tipi di macchine virtuali di Azure supportati |
| [2015553] | SAP in Azure: Prerequisiti di supporto |
| [2178632] | Metriche chiave del monitoraggio per SAP in Azure |
| [2191498] | SAP in Linux con Azure: Monitoraggio avanzato |
| [2243692] | Linux in Azure (IaaS) della macchina virtuale: problemi delle licenze SAP |
| [2002167] | Red Hat Enterprise Linux 7.x: installazione e aggiornamento |
| [2694118] | Componente aggiuntivo a disponibilità elevata di Red Hat Enterprise Linux in Azure |
| [1999351] | Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2233094] | DB6: Applicazioni SAP in Azure che usano IBM Db2 per Linux, UNIX e Windows - informazioni aggiuntive |
| [1612105] | DB6: Domande frequenti su Db2 con HADR |


| Documentazione | 
| --- |
| [Community Wiki SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Include tutte le note su SAP necessarie per Linux |
| [Pianificazione di macchine virtuali di Azure e implementazione per SAP in Linux][planning-guide] Guida |
| [Distribuzione di macchine virtuali di Azure per SAP in Linux][deployment-guide] (questo articolo) |
| [Distribuzione di gestione system(DBMS) per SAP in Linux di macchine virtuali di Azure database][dbms-guide] Guida |
| [Carico di lavoro SAP in Azure elenco di controllo di pianificazione e distribuzione][azr-sap-plancheck] |
| [Panoramica del componente aggiuntivo disponibilità elevata per Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin] (Amministrazione dei componenti aggiuntivi a disponibilità elevata) |
| [High Availability Add-On Reference][rhel-ha-ref] (Riferimento dei componenti aggiuntivi a disponibilità elevata) |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp] (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst] (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
| [Distribuzione di DBMS di macchine virtuali di Azure di IBM Db2 per carico di lavoro SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Criteri di supporto per RHEL cluster a disponibilità elevata - gestione di IBM Db2 per Linux, Unix e Windows in un Cluster][rhel-db2-supp]



## <a name="overview"></a>Panoramica
Per ottenere disponibilità elevata, IBM Db2 LUW con HADR è installato in almeno due macchine virtuali di Azure, che vengono distribuite in un' [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) o attraverso [zone di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Il grafico seguente visualizza un programma di installazione del server di database di due macchine virtuali di Azure. Sia il server di database macchine virtuali di Azure sono proprio spazio di archiviazione collegato e siano in esecuzione. In HADR, un'istanza di database in una delle macchine virtuali di Azure ha il ruolo dell'istanza primaria. Tutti i client sono connessi all'istanza primaria. Tutte le modifiche nelle transazioni di database vengono rese persistenti in locale nel log delle transazioni Db2. I record del log delle transazioni vengono salvati in modo permanente in locale, i record vengono trasferiti tramite TCP/IP all'istanza del database nel server di database secondo, il server di standby o istanza di standby. L'istanza di standby aggiorna il database locale eseguendo il rollforward della transazione trasferita i record del log. In questo modo, il server di standby viene mantenuto sincronizzato con il server primario.

HADR è solo una funzionalità di replica. Dispone di alcun rilevamento degli errori e non funzionalità di acquisizione della proprietà o il failover automatico. Un'acquisizione della proprietà o il trasferimento al server di standby deve essere avviato manualmente da un amministratore del database. Per ottenere un'acquisizione automatica e il rilevamento degli errori, è possibile usare la funzionalità clustering di Linux Pacemaker. Pacemaker consente di monitorare le istanze del server due database. Quando si blocca l'istanza del server di database primario, avvia Pacemaker un' *automatica* acquisizione della proprietà HADR dal server di standby. Pacemaker assicura anche che l'indirizzo IP virtuale viene assegnato al nuovo server primario.

![Panoramica della disponibilità elevata IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Per avere SAP application server connettono al database primario, è necessario un nome host virtuale e un indirizzo IP virtuale. In caso di failover, i server applicazioni SAP si connetteranno alla nuova istanza di database primario. In un ambiente Azure, un' [servizio di bilanciamento del carico di Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) è necessaria per usare un indirizzo IP virtuale nel modo necessario per HADR di IBM Db2. 

Per comprendere completamente come IBM Db2 LUW con HADR e Pacemaker si inserisce in una configurazione di sistema SAP a disponibilità elevata, l'immagine seguente viene presentata una panoramica di un'installazione a disponibilità elevata di un sistema SAP basato su database IBM Db2. Questo articolo illustra solo IBM Db2, ma offre riferimenti ad altri articoli su come configurare altri componenti di un sistema SAP.

![Panoramica sull'ambiente completo di disponibilità elevata di IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Panoramica generale dei passaggi necessari
Per distribuire una configurazione di IBM Db2, è necessario seguire questa procedura:

  + Pianificare l'ambiente.
  + Distribuire le macchine virtuali.
  + Aggiornare Linux RHEL e configurare i sistemi di file.
  + Installare e configurare Pacemaker.
  + Il programma di installazione [glusterfs cluster][glusterfs] or [Azure NetApp Files][anf-rhel]
  + Installare [ASCS/ERS in un cluster separato][ascs-ha-rhel].
  + Installare i database IBM Db2 con l'opzione Distributed e la disponibilità elevata (SWPM).
  + Installare e creare un nodo del database secondario e un'istanza e configurare HADR.
  + Verificare che funzioni HADR.
  + Applicare la configurazione di Pacemaker al controllo IBM Db2.
  + Configurare il bilanciamento del carico di Azure.
  + Installare primario e i server applicazioni di finestra di dialogo.
  + Controllare e adattare la configurazione dei server applicazioni SAP.
  + Eseguire failover e il test di acquisizione della proprietà.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Pianificare l'infrastruttura di Azure per l'hosting di IBM Db2 LUW con HADR

Completare il processo di pianificazione prima di eseguire la distribuzione. Pianificazione crea le basi per la distribuzione di una configurazione di Db2 con HADR in Azure. Nella tabella seguente sono elencati gli elementi chiave che devono essere parte della pianificazione per IMB Db2 LUW (parte del database dell'ambiente SAP):

| Argomento | Breve descrizione |
| --- | --- |
| Definire gruppi di risorse di Azure | Gruppi di risorse in cui si distribuisce macchine Virtuali, rete virtuale, Azure Load Balancer e altre risorse. Può essere nuovo o esistente. |
| Rete virtuale / definizione della Subnet | In cui vengano implementate le macchine virtuali per Azure Load Balancer e IBM Db2. Può essere nuovo o esistente. |
| Macchine virtuali che ospitano LUW di IBM Db2 | Dimensioni di macchina virtuale, archiviazione, rete, indirizzo IP. |
| Nome host virtuale e indirizzo IP virtuale per il database IBM Db2| Il virtual IP o nome host utilizzato per la connessione dei server applicazioni SAP. **db-virt-hostname**, **db-virt-ip**. |
| Fencing in Azure | Non è possibile metodo per evitare situazioni di split brain. |
| Azure Load Balancer | Utilizzo di Basic o Standard (scelta consigliata), porta per il database di Db2 (un Consiglio 62500) probe **porta-probe**. |
| Risoluzione dei nomi| Risoluzione dei nomi come funziona nell'ambiente. Servizio DNS è vivamente consigliato. Il file hosts locale può essere utilizzato. |
    
Per altre informazioni su Linux Pacemaker in Azure, vedere [configurazione di Pacemaker su Red Hat Enterprise Linux in Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Distribuzione su Red Hat Enterprise Linux

L'agente delle risorse per IBM Db2 LUW è incluso in Red Hat Enterprise Linux Server a disponibilità elevata Addon. Per la configurazione descritta in questo documento, è consigliabile usare Red Hat Enterprise Linux per SAP. Azure Marketplace contiene un'immagine per Red Hat Enterprise Linux 7.4 o versione successiva per SAP che è possibile usare per distribuire nuove macchine virtuali di Azure. Tenere presente i vari modelli di supporto o servizio che sono offerti da Red Hat in Azure Marketplace quando si sceglie un'immagine di macchina virtuale nella macchina virtuale di Azure Marketplace.

### <a name="hosts-dns-updates"></a>Host: Aggiornamenti del DNS
Creare un elenco di tutti i nomi host, inclusi i nomi host virtuali e aggiornare i server DNS per abilitare l'indirizzo IP appropriato per la risoluzione del nome host. Se un server DNS non esiste o non è possibile aggiornare e creare voci DNS, è necessario usare i file host locale delle singole macchine virtuali che fanno parte di questo scenario. Se si usano le voci di file host, assicurarsi che le voci vengono applicate a tutte le macchine virtuali nell'ambiente di sistema SAP. Tuttavia, è consigliabile usare il DNS che, in teoria, si estende in Azure


### <a name="manual-deployment"></a>Distribuzione manuale

Assicurarsi che il sistema operativo selezionato è supportato da IBM/SAP per IBM Db2 LUW. L'elenco delle versioni del sistema operativo supportate per macchine virtuali di Azure e le versioni di Db2 è disponibile nella nota SAP [1928533]. L'elenco delle versioni del sistema operativo dalla versione di Db2 singoli è disponibile in SAP Product Availability Matrix. Si consiglia un minimo di Red Hat Enterprise Linux 7.4 per SAP grazie ai miglioramenti delle prestazioni correlate ad Azure in questo o in un secondo momento le versioni di Red Hat Enterprise Linux.

1. Creare o selezionare un gruppo di risorse.
1. Creare o selezionare una rete virtuale e subnet.
1. Creare un set di disponibilità di Azure o distribuire una zona di disponibilità.
    + Per il set di disponibilità, impostare i domini di aggiornamento massima a 2.
1. Creare la macchina virtuale 1.
    + Usa Red Hat Enterprise Linux per immagini SAP in Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare una zona di disponibilità.
1.  Creare la macchina virtuale 2.
    + Usa Red Hat Enterprise Linux per immagini SAP in Azure Marketplace.
    + Selezionare il set di disponibilità di Azure in creata nel passaggio 3 oppure selezionare la zona di disponibilità (non la stessa area come indicato nel passaggio 3).
1. Aggiungere dischi dati per le macchine virtuali e quindi controllare l'indicazione di una configurazione di sistema di file in questo articolo [distribuzione di DBMS di macchine virtuali di Azure di IBM Db2 per carico di lavoro SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker
    
Per creare un cluster Pacemaker di base per questo server IBM Db2, vedere [configurazione di Pacemaker su Red Hat Enterprise Linux in Azure][rhel-pcs-azr]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installare l'ambiente LUW Db2 IBM e SAP

Prima di iniziare l'installazione di un ambiente SAP in IBM Db2 LUW basato, consultare la documentazione seguente:

+ Documentazione di Azure
+ Documentazione di SAP
+ Documentazione di IBM

Nella sezione introduttiva di questo articolo vengono forniti collegamenti a questa documentazione.

Consultare i manuali di installazione SAP sull'installazione di applicazioni basate su NetWeaver in IBM Db2 LUW.
È possibile trovare le guide nel portale del supporto di SAP con il [Finder di Guida all'installazione SAP][sap-instfind].

È possibile ridurre il numero delle guide visualizzata nel portale, impostare i filtri seguenti:
- Vorrei: "Installare un nuovo sistema"
- Il Database: "IBM Db2 per Linux, Unix e Windows"
- Filtri aggiuntivi per le versioni SAP NetWeaver, configurazione dello stack o sistema operativo

#### <a name="red-hat-firewall-rules"></a>Regole del firewall di Red Hat
Red Hat Enterprise Linux è abilitato per impostazione predefinita firewall. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Hint di installazione per la configurazione di IBM Db2 LUW con HADR

Per configurare l'istanza di database IBM Db2 LUW primaria:

- Usare l'opzione distribuita o la disponibilità elevata.
- Installare l'istanza SAP ASCS/ERS e il Database.
- Eseguire un backup del database appena installato.

> [!IMPORTANT] 
> Annotare la "porta di comunicazione del Database" che viene impostata durante l'installazione. Deve essere lo stesso numero di porta per entrambe le istanze del database.
>![Definizione di porte di SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Impostazioni di IBM Db2 HADR per Azure

   Quando si usa un agente di isolamento Pacemaker di Azure, impostare i parametri seguenti:

   - Durata intervallo di peer HADR (secondi) (HADR_PEER_WINDOW) = 240  
   - Valore di timeout HADR (HADR_TIMEOUT) = 45

È consigliabile i parametri precedenti basati su test iniziale/acquisire la proprietà di failover. È obbligatorio il test per il corretto funzionamento del failover e acquisire la proprietà con queste impostazioni dei parametri. Poiché le configurazioni dei singoli possono variare, i parametri potrebbero richiedere regolazione. 

> [!NOTE]
> Specifiche per IBM Db2 con configurazione HADR con il normale avvio: L'istanza del database secondario o standby deve essere in esecuzione prima di poter avviare l'istanza del database primario.

   
> [!NOTE]
> Per l'installazione e configurazione che è specifico di Azure e Pacemaker: Durante la procedura di installazione tramite SAP Software Provisioning Manager, c'è una domanda sulla disponibilità elevata per IBM Db2 LUW esplicita:
>+ Non si seleziona **IBM Db2 pureScale**.
>+ Non si seleziona **installare IBM Tivoli: sistema di automazione per Multiplatforms**.
>+ Non si seleziona **generare i file di configurazione di cluster**.
>![SAP SWPM - opzioni DB2 a disponibilità elevata](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Per configurare il server di database di Standby con la procedura copia di sistema omogeneo SAP, eseguire questi passaggi:

1. Selezionare il **copia di sistema** opzione > **sistemi di destinazione** > **Distributed** > **istanza Database**.
1. Come un metodo di copia, selezionare **sistema omogeneo** in modo che è possibile usare backup per ripristinare un backup nell'istanza del server di standby.
1. Quando si raggiunge il passaggio di uscita per ripristinare il database per la copia di sistema omogeneo, chiudere il programma di installazione. Ripristinare il database da un backup dell'host primario. Tutte le fasi di installazione successiva già eseguite nel server di database primario.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Regole del firewall Red Hat per HADR di DB2
Aggiungere le regole del firewall per consentire il traffico a DB2 e tra DB2 per HADR funzionamento:
+ Porta di comunicazione del database. Se si usa le partizioni, aggiungere anche tali porte.
+ Porta HADR (valore del parametro DB2 HADR_LOCAL_SVC)
+ Porta probe di Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Controllo di IBM Db2 HADR
A scopo dimostrativo e le procedure descritte in questo articolo, il database SID è **ID2**.

Dopo aver configurato HADR e lo stato è CONNECTED e PEER sui nodi primari e di standby, eseguire la verifica seguente:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configurazione di Pacemaker di DB2

Quando si usa Pacemaker per il failover automatico in caso di errore in un nodo, è necessario configurare di conseguenza le istanze di Db2 e Pacemaker. In questa sezione viene descritto questo tipo di configurazione.

Gli elementi seguenti sono preceduti:

- **[A]** : Applicabile a tutti i nodi
- **[1]** : Applicabile solo al nodo 1 
- **[2]** : Applicabile solo al nodo 2

**[A]**  Prerequisiti per la configurazione di Pacemaker:
1. Arrestare entrambi i server database db2 utente\<sid > con db2stop.
1. Modificare l'ambiente shell per db2\<sid > all'utente */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Configurazione di pacemaker

**[1]**  Configurazione di Pacemaker HADR specifiche di IBM Db2:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]**  Risorse creare IBM Db2:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]**  Risorse avviare IBM Db2:
* Inserire Pacemaker dalla modalità manutenzione.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]**  Assicurarsi che lo stato del cluster è OK e che tutte le risorse siano avviate. Non è importante nodo in cui le risorse sono in esecuzione in.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 az-idb02]

Elenco completo delle risorse:

 rsc_st_azure   (stonith:fence_azure_arm):      Set Master/Slave az-idb01 avviato: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Resource Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01 nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Az-idb01 avviata

Lo stato del daemon: corosync: pacemaker attivo o disabilitata: pcsd attivo o disabilitata: attivo/abilitato
</pre>

> [!IMPORTANT]
> È necessario gestire il Pacemaker istanza in cluster Db2 tramite strumenti di Pacemaker. Se si usano comandi di db2, ad esempio db2stop, Pacemaker rileva l'azione di errore della risorsa. Se stai eseguendo la manutenzione, è possibile inserire i nodi o le risorse in modalità di manutenzione. Pacemaker sospende Monitoraggio risorse e quindi è possibile usare i comandi di amministrazione di normali db2.


### <a name="configure-azure-load-balancer"></a>Configurare Azure Load Balancer
Per configurare Azure Load Balancer, è consigliabile usare la [Azure SKU di Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e quindi eseguire quanto segue:

1. Creare un pool IP front-end:

   a. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **pool di indirizzi IP front-end**, quindi selezionare **Add**.

   b. Immettere il nome del nuovo pool di IP front-end (ad esempio, **connessione a Db2**).

   c. Impostare il **assegnazione** al **statici**e immettere l'indirizzo IP **Virtual IP** definite all'inizio.

   d. Selezionare **OK**.

   e. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.

1. Creare un pool back-end:

   a. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **pool back-end**, quindi selezionare **Add**.

   b. Immettere il nome del nuovo pool di back-end (ad esempio, **Db2-backend**).

   c. Selezionare **Aggiungi una macchina virtuale**.

   d. Selezionare il set di disponibilità o le macchine virtuali che ospita il database IBM Db2 creato nel passaggio precedente.

   e. Selezionare le macchine virtuali del cluster di IBM Db2.

   f. Selezionare **OK**.

1. Creare un probe di integrità:

   a. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **probe di integrità**e selezionare **Add**.

   b. Immettere il nome del nuovo probe di integrità (ad esempio, **Db2-hp**).

   c. Selezionare **TCP** come il protocollo e porte **62500**. Mantenere il **Interval** valore impostato su **5**e mantenere la **soglia non integra** valore impostato su **2**.

   d. Selezionare **OK**.

1. Creare le regole di bilanciamento del carico:

   a. Nel portale di Azure, aprire il servizio di bilanciamento del carico di Azure, selezionare **regole di bilanciamento del carico**, quindi selezionare **Add**.

   b. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, **Db2-SID**).

   c. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creata in precedenza (ad esempio, **Db2-frontend**).

   d. Mantenere il **Protocol** impostata su **TCP**e immettere la porta *porta di comunicazione Database*.

   e. Aumentare il valore di **Timeout di inattività** a 30 minuti.

   f. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.

   g. Selezionare **OK**.

**[A]**  Aggiungi regola di firewall per la porta probe:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Apportare modifiche ai profili SAP per l'utilizzo di IP virtuale per la connessione
Per connettersi all'istanza primaria di configurazione HADR, SAP livello dell'applicazione deve usare l'indirizzo IP virtuale che è definito e configurato per il servizio di bilanciamento del carico di Azure. Le modifiche seguenti sono necessari:

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installare primario e i server applicazioni di finestra di dialogo

Quando si installa primario e i server applicazioni di finestra di dialogo con una configurazione HADR di Db2, utilizzare l'host virtuale nome scelto per la configurazione. 

Se è stata eseguita l'installazione prima di creazione della configurazione HADR di Db2, apportare le modifiche come descritto nella sezione precedente, come indicato di seguito per gli stack SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Controllare i sistemi stack ABAP + Java o Java URL JDBC

Utilizzare lo strumento di configurazione di J2EE per verificare o aggiornare l'URL di JDBC. Poiché lo strumento di configurazione di J2EE è uno strumento con interfaccia grafico, è necessario avere X server installata:
 
1. Accedere al server dell'applicazione principale dell'istanza di J2EE ed eseguire:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. Nel riquadro di sinistra, scegliere **store sicurezza**.
1. Nel riquadro di destra, scegliere il chiave jdbc/pool / \<SAPSID>/url.
1. Modificare il nome host nell'URL di JDBC con il nome host virtuale.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Selezionare **aggiungere**.
1. Per salvare le modifiche, selezionare l'icona del disco nell'angolo superiore sinistro.
1. Chiudere lo strumento di configurazione.
1. Riavviare l'istanza di Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurare l'archiviazione dei log per l'installazione HADR
Per configurare il log di Db2 dell'archiviazione per il programma di installazione HADR, è consigliabile configurare sia il database primario e il database di standby per avere la funzionalità di recupero automatico dei log da tutti i percorsi di archivio di log. Il database primario e standby deve essere in grado di recuperare file di archivio dei log da tutte le posizioni di archiviazione log in cui uno dei database istanze potrebbero archiviare i file di log. 

L'archiviazione dei log viene eseguita solo dal database primario. Se si modificano i ruoli HADR dei server di database o se si verifica un errore, il nuovo database primario è responsabile per l'archiviazione dei log. Se è stato impostato più percorsi di archiviazione di log, i log potrebbero essere archiviati due volte. In caso di completamento dell'aggiornamento remoto o locale, potrebbe essere inoltre necessario copiare manualmente i log archiviati dal server primario precedente al percorso del log attivo del nuovo server primario.

È consigliabile configurare una condivisione NFS o GlusterFS, in cui vengono scritti i log da entrambi i nodi comune. Condivisione NFS o GlusterFS deve essere a disponibilità elevata. 

È possibile usare le condivisioni NFS a disponibilità elevata esistenti o GlusterFS per trasporti o una directory del profilo. Per altre informazioni, vedere:

- [GlusterFS in VM di Azure in Red Hat Enterprise Linux per SAP NetWeaver][glusterfs] 
- [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Red Hat Enterprise Linux con file di NetApp Azure for SAP Applications][anf-rhel]
- [File di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (per creare le condivisioni NFS)

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come è possibile testare la configurazione Db2 HADR. Ogni test presuppone IBM Db2 primario è in esecuzione la *az-idb01* macchina virtuale. È necessario usare gli utenti con privilegi sudo o root (scelta non consigliata).

Lo stato iniziale per tutti i test case è illustrato di seguito: (stato crm_mon PC o - r)

- **lo stato di PC** è uno snapshot dello stato di Pacemaker in fase di esecuzione 
- **-r crm_mon** è output continuo dello stato di Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Lo stato originale in un sistema SAP è documentato nella transazione DBACOCKPIT > Configurazione > Panoramica, come illustrato nell'immagine seguente:

![DBACockpit - precedente alla migrazione](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Acquisire la proprietà di test di IBM Db2


> [!IMPORTANT] 
> Prima di iniziare il test, assicurarsi che:
> * Pacemaker non siano presenti azioni non riuscite (PC status).
> * Non sono presenti vincoli di posizione (leftovers del test di migrazione)
> * Funzionamento della sincronizzazione di IBM Db2 HADR. Contattare l'utente db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Eseguire la migrazione il nodo che esegue il database di Db2 primario eseguendo il seguente comando:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Al termine della migrazione, l'output di stato crm sarà simile a:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Lo stato originale in un sistema SAP è documentato nella transazione DBACOCKPIT > Configurazione > Panoramica, come illustrato nell'immagine seguente:

![DBACockpit - dopo la migrazione](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Migrazione delle risorse con "spostamento di risorse di PC" Crea vincoli di posizione. I vincoli di località impediscono in questo caso l'istanza di IBM Db2 in esecuzione su az-idb01. Se non vengono eliminati i vincoli di posizione, la risorsa non è possibile eseguire il failback.

Verrà avviati in az-idb01 Remove vincolare il percorso e nodo di standby.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
E lo stato del cluster viene modificato da:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![Vincolare percorso rimosso DBACockpit:](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Eseguire la migrazione della risorsa al *az-idb01* e deselezionare i vincoli di posizione
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **lo spostamento di risorse i PC \<res_name > <host>:** Crea vincoli di posizione e può causare problemi con l'acquisizione della proprietà
- **risorse di PC non crittografata \<res_name >** : Cancella i vincoli di posizione
- **pulizia delle risorse PCS \<res_name >** : Cancella tutti gli errori della risorsa

### <a name="test-a-manual-takeover"></a>Testare un'acquisizione manuale

È possibile testare un'acquisizione manuale arrestando il servizio Pacemaker nel *az-idb01* nodo:
<pre><code>systemctl stop pacemaker</code></pre>

status on *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Dopo il failover, è possibile avviare nuovamente il servizio sul *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Terminare il processo di Db2 nel nodo che esegue il database primario HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

L'istanza di Db2 sta esito negativo e Pacemaker si sposta nodo master e il report seguente stato:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker verrà riavviata l'istanza di database primario Db2 nello stesso nodo, o verrà eseguito il failover al nodo che esegue l'istanza del database secondario e viene restituito un errore.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Terminare il processo di Db2 nel nodo che esegue l'istanza del database secondario

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Il nodo ottiene in errore indicato ed errore segnalato
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Nel ruolo secondario che fosse assegnato prima viene riavviata l'istanza di Db2.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Forzare l'arresto DB tramite db2stop sul nodo che esegue l'istanza di database primario HADR

Come utente db2\<sid > esecuzione forzata db2stop comando:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Errore rilevato:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

L'istanza del database secondario HADR Db2 alzata di livello al ruolo primario.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Arresto anomalo del sistema della macchina virtuale che esegue l'istanza del database primario HADR con "halt"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

In tal caso, Pacemaker rileverà che il nodo che esegue l'istanza del database primario non risponde.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

il passaggio successivo consiste nel verificare la presenza di un *dividere cervello* situazione. Dopo il nodo integro ha determinato che il nodo dell'ultima esecuzione l'istanza del database primario è inattivo, viene eseguito un failover delle risorse.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


In caso di un kernel panic, il nodo in errore verrà essere restared dall'agente di isolamento. Dopo il nodo in errore viene riportato online, è necessario avviare cluster pacemaker
<pre><code>sudo pcs cluster start</code></pre> Avvia l'istanza di Db2 nel ruolo secondario.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Passaggi successivi
- [Scenari e architettura a disponibilità elevata per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurazione di Pacemaker su Red Hat Enterprise Linux in Azure][rhel-pcs-azr]
