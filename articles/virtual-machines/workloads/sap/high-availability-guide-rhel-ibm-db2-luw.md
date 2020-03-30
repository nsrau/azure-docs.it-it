---
title: Set up IBM Db2 HADR in macchine virtuali di Azure (VM) su RHEL Documenti Microsoft
description: Stabilire la disponibilità elevata di IBM Db2 LUW in Macchine virtuali di Azure (VM) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598698"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Disponibilità elevata di IBN Db2 LUW in macchine virtuali di Azure su Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7. x: installazione e aggiornamento |
| [2694118] | Red Hat Enterprise Linux LINUX HA Add-On in Azure |
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
| [Panoramica del componente aggiuntivo ad alta disponibilità per Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin] (Amministrazione dei componenti aggiuntivi a disponibilità elevata) |
| [High Availability Add-On Reference][rhel-ha-ref] (Riferimento dei componenti aggiuntivi a disponibilità elevata) |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp] (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst] (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
| [Distribuzione DBMS DI IBM Db2 Azure Virtual Machines per il carico di lavoro SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Criteri di supporto per i cluster RHEL a disponibilità elevata - Gestione di IBM Db2 per Linux, Unix e Windows in un cluster][rhel-db2-supp]



## <a name="overview"></a>Panoramica
Per ottenere la disponibilità elevata, IBM Db2 LUW con HADR viene installato in almeno due macchine virtuali di Azure, distribuite in un set di disponibilità di Azure o tra zone di disponibilità di Azure.To achieve high availability, IBM Db2 LUW with HADR is installed on at least two Azure virtual machines, which are deployed in an [Azure availability set](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) or across Azure Availability [zones.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

La grafica seguente visualizza una configurazione di due macchine virtuali di Azure del server di database. Entrambe le macchine virtuali di Azure del server di database dispongono di un proprio spazio di archiviazione collegato e sono in esecuzione. In HADR, un'istanza di database in una delle macchine virtuali di Azure ha il ruolo dell'istanza primaria. Tutti i client sono connessi all'istanza primaria. Tutte le modifiche apportate alle transazioni di database vengono mantenute localmente nel log delle transazioni Db2. Poiché i record del log delle transazioni vengono salvati in modo permanente localmente, i record vengono trasferiti tramite TCP/IP all'istanza del database nel secondo server di database, nel server di standby o nell'istanza standby. L'istanza standby aggiorna il database locale eseguendo il rollforward dei record del log delle transazioni trasferiti. In questo modo, il server di standby viene mantenuto sincronizzato con il server primario.

HADR è solo una funzionalità di replica. Non ha alcun rilevamento degli errori e nessuna funzionalità di acquisizione o failover automatico. Un'acquisizione o un trasferimento al server di standby deve essere avviato manualmente da un amministratore del database. Per ottenere un rilevamento automatico di acquisizione e errore, è possibile utilizzare la funzionalità di clustering Linux Pacemaker.To achieve a automatic takeover and failure detection, you can use the Linux Pacemaker clustering feature. Pacemaker monitora le due istanze del server di database. Quando l'istanza del server di database primario si arresta in modo anomalo, Pacemaker avvia un'acquisizione *automatica* HADR da parte del server di standby. Pacemaker garantisce inoltre che l'indirizzo IP virtuale venga assegnato al nuovo server primario.

![Panoramica sulla disponibilità elevata di IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Per fare in modo che i server applicazioni SAP si connettano al database primario, è necessario un nome host virtuale e un indirizzo IP virtuale. In caso di failover, i server applicazioni SAP si connetteranno a una nuova istanza del database primario. In un ambiente Azure, è necessario un servizio di [bilanciamento del carico](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) di Azure per usare un indirizzo IP virtuale nel modo necessario per HADR di IBM Db2.In an Azure environment, an Azure load balancer is required to use a virtual IP address in the way that's required for HADR of IBM Db2. 

Per aiutarti a comprendere appieno in che modo IBM Db2 LUW con HADR e Pacemaker si inserisce in una configurazione del sistema SAP a disponibilità elevata, l'immagine seguente presenta una panoramica di una configurazione a disponibilità elevata di un sistema SAP basato sul database IBM Db2. In questo articolo viene illustrato solo IBM Db2, ma vengono forniti riferimenti ad altri articoli su come impostare altri componenti di un sistema SAP.

![Panoramica dell'ambiente completo ad alta disponibilità IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Panoramica generale dei passaggi necessari
Per distribuire una configurazione IBM Db2, è necessario attenersi alla seguente procedura:

  + Pianificare l'ambiente.
  + Distribuire le macchine virtuali.
  + Aggiornare RHEL Linux e configurare i file system.
  + Installare e configurare Pacemaker.
  + Installazione [del cluster glusterfs][glusterfs] o dei file NetApp di AzureSetup glusterfs cluster or Azure [NetApp Files][anf-rhel]
  + Installare [ASCS/ERS in un cluster separato.][ascs-ha-rhel]
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
| Scherma di AzureAzure fencing | Metodo per evitare situazioni di cervello diviso è impedito. |
| Azure Load Balancer | Utilizzo della porta di base o standard (consigliato), della porta probe per il database Db2 (la nostra raccomandazione 62500) **probe-port**. |
| Risoluzione dei nomi| Funzionamento della risoluzione dei nomi nell'ambiente. Il servizio DNS è altamente consigliato. È possibile utilizzare il file host locale. |
    
Per altre informazioni su Linux Pacemaker in Azure, vedere [Configurazione di Pacemaker su Red Hat Enterprise Linux in Azure.][rhel-pcs-azr]

## <a name="deployment-on-red-hat-enterprise-linux"></a>Distribuzione su Red Hat Enterprise Linux

L'agente di risorse per IBM Db2 LUW è incluso in Red Hat Enterprise Linux Server HA Addon. Per la configurazione descritta in questo documento, è consigliabile usare Red Hat Enterprise Linux per SAP. Azure Marketplace contiene un'immagine per Red Hat Enterprise Linux 7.4 per SAP o versione successiva che è possibile usare per distribuire nuove macchine virtuali di Azure.The Azure Marketplace contains an image for Red Hat Enterprise Linux 7.4 for SAP or higher che è possibile usare per distribuire nuove macchine virtuali di Azure.The Azure Marketplace contains an image for Red Hat Enterprise Linux 7.4 for SAP or higher che è possibile usare per distribuire nuove macchine virtuali di Tenere presente i vari modelli di supporto o servizio offerti da Red Hat tramite Azure Marketplace quando si sceglie un'immagine di macchina virtuale in Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Host: aggiornamenti DNS
Creare un elenco di tutti i nomi host, inclusi i nomi host virtuali, e aggiornare i server DNS in modo da abilitare l'indirizzo IP appropriato per la risoluzione dei nomi host. Se un server DNS non esiste o non è possibile aggiornare e creare voci DNS, è necessario utilizzare i file host locali delle singole macchine virtuali che partecipano a questo scenario. Se si usano voci di file host, assicurarsi che le voci siano applicate a tutte le macchine virtuali nell'ambiente di sistema SAP. Tuttavia, si consiglia di utilizzare il DNS che, idealmente, si estende in Azure


### <a name="manual-deployment"></a>Distribuzione manuale

Assicurarsi che il sistema operativo selezionato sia supportato da IBM/SAP per IBM Db2 LUW. L'elenco delle versioni del sistema operativo supportate per le versioni di Azure VMs e Db2 è disponibile nella nota SAP [1928533.] L'elenco delle versioni del sistema operativo per singola versione Db2 è disponibile nella matrice di disponibilità del prodotto SAP. È consigliabile un minimo di Red Hat Enterprise Linux 7.4 per SAP a causa di miglioramenti delle prestazioni correlati ad Azure in questa o successiva versione di Red Hat Enterprise Linux.

1. Creare o selezionare un gruppo di risorse.
1. Creare o selezionare una rete virtuale e una subnet.
1. Creare un set di disponibilità di Azure o distribuire una zona di disponibilità.
    + Per il set di disponibilità, impostare il numero massimo di domini di aggiornamento su 2.For the availability set, set the maximum update domains to 2.
1. Creare la macchina virtuale 1.Create Virtual Machine 1.
    + Usare l'immagine di Red Hat Enterprise Linux per SAP in Azure Marketplace.Use Red Hat Enterprise Linux for SAP image in the Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare Zona di disponibilità.
1.  Creare una macchina virtuale 2.Create Virtual Machine 2.
    + Usare l'immagine di Red Hat Enterprise Linux per SAP in Azure Marketplace.Use Red Hat Enterprise Linux for SAP image in the Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare zona di disponibilità (non la stessa zona del passaggio 3).
1. Aggiungere dischi dati alle macchine virtuali e quindi verificare il suggerimento di una configurazione del file system nell'articolo Distribuzione DBMS di [IBM Db2 Azure Virtual Machines per][dbms-db2]il carico di lavoro SAP .

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker
    
Per creare un cluster Pacemaker di base per questo server IBM Db2, vedere [Configurazione di Pacemaker su Red Hat Enterprise Linux in Azure.][rhel-pcs-azr] 

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

#### <a name="red-hat-firewall-rules"></a>Regole del firewall Red Hat
Red Hat Enterprise Linux ha il firewall abilitato per impostazione predefinita. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Suggerimenti per l'installazione per l'impostazione di IBM Db2 LUW con HADR

Per impostare l'istanza del database IBM Db2 LUW primaria:

- Utilizzare l'opzione di disponibilità elevata o distribuita.
- Installare l'istanza SAP ASCS/ERS e Database.
- Eseguire un backup del database appena installato.

> [!IMPORTANT] 
> Annotare la "porta di comunicazione del database" impostata durante l'installazione. Deve essere lo stesso numero di porta per entrambe le istanze di database.
>![Definizione porta SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Impostazioni DI IBM Db2 HADR per Azure

   Quando si usa un agente di scherma di Azure Pacemaker, impostare i parametri seguenti:When you use an Azure Pacemaker fencing agent, set the following parameters:

   - Durata della finestra peer HADR (secondi) (HADR_PEER_WINDOW)  
   - Valore di timeout HADR (HADR_TIMEOUT) - 45

È consigliabile utilizzare i parametri precedenti in base ai test di failover/acquisizione iniziali. È obbligatorio verificare la corretta funzionalità di failover e acquisizione con queste impostazioni dei parametri. Poiché le singole configurazioni possono variare, i parametri potrebbero richiedere una regolazione. 

> [!NOTE]
> Specifico per IBM Db2 con configurazione HADR con avvio normale: l'istanza del database secondario o standby deve essere in esecuzione prima di poter avviare l'istanza del database primario.

   
> [!NOTE]
> Per l'installazione e la configurazione specifiche di Azure e Pacemaker: durante la procedura di installazione tramite SAP Software Provisioning Manager, esiste una domanda esplicita sulla disponibilità elevata per IBM Db2 LUW:
>+ Non selezionare **IBM Db2 pureScale**.
>+ Non selezionare **Installa IBM Tivoli System Automation for Multiplatforms**.
>+ Non selezionare Genera file di **configurazione del cluster**.
>![Opzioni SAP SWPM - DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Per configurare il server di database Standby utilizzando la procedura di copia di sistema omogenea SAP, eseguire i passaggi seguenti:

1. Selezionare l'opzione **Copia di** sistema >**Istanza database****distribuito** > dei **sistemi** > di destinazione .
1. Come metodo di copia, selezionare **Sistema omogeneo** in modo da poter utilizzare il backup per ripristinare un backup nell'istanza del server di standby.
1. Quando si raggiunge la fase di uscita per ripristinare il database per una copia di sistema omogenea, uscire dal programma di installazione. Ripristinare il database da un backup dell'host primario. Tutte le fasi di installazione successive sono già state eseguite nel server di database primario.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Regole del firewall Red Hat per DB2 HADR
Aggiungere regole del firewall per consentire il traffico verso DB2 e tra DB2 per il funzionamento di HADR:
+ Porta di comunicazione del database. Se si utilizzano partizioni, aggiungere anche tali porte.
+ Porta HADR (valore del parametro DB2 HADR_LOCAL_SVC)
+ Porta probe di AzureAzure probe port
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Controllo IBM Db2 HADR
A scopo dimostrativo e le procedure descritte in questo articolo, il SID del database è **ID2**.

Dopo aver configurato HADR e lo stato è PEER e CONNECTED nei nodi primario e standby, eseguire il controllo seguente:

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



## <a name="db2-pacemaker-configuration"></a>Configurazione di Db2 Pacemaker

Quando si usa Pacemaker per il failover automatico in caso di errore di un nodo, è necessario configurare le istanze Db2 e Pacemaker di conseguenza. In questa sezione viene descritto questo tipo di configurazione.

I seguenti elementi sono preceduti da:

- **[A]**: Applicabile a tutti i nodi
- **[1]**: Applicabile solo al nodo 1 
- **[2]**: Applicabile solo al nodo 2

**[A]** Prerequisito per la configurazione pacemaker:
1. Arrestare entrambi i server\<di database con l'utente db2 sid> con db2stop.
1. Modificare l'ambiente della\<shell per db2 sid>utente in */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Configurazione Pacemaker

**[1]** Configurazione Pacemaker specifica di IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Creare risorse IBM Db2:
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

**[1]** Avviare le risorse IBM Db2:
* Mettere Pacemaker fuori dalla modalità di manutenzione.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Assicurarsi che lo stato del cluster sia OK e che tutte le risorse siano state avviate. Non è importante su quale nodo sono in esecuzione le risorse.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

In linea: [ az-idb01 az-idb02 ]

Elenco completo delle risorse:

 rsc_st_azure (stonith:fence_azure_arm): avviato az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Slaves: [ az-idb02 ] Resource Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Avviato az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb):

Stato daemon: corosync: pacemaker attivo/disabilitato: pcsd attivo/disabilitato: attivo/abilitato
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

**[A]** Aggiungi regola firewall per la porta probe:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Accedere al server applicazioni principale dell'istanza J2EE ed eseguire:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. Nel riquadro sinistro scegliere **Archivio di sicurezza**.
1. Nel riquadro destro scegliere `jdbc/pool/\<SAPSID>/url`il tasto .
1. Modificare il nome host nell'URL JDBC con il nome host virtuale.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Selezionare **Aggiungi**.
1. Per salvare le modifiche, selezionare l'icona del disco in alto a sinistra.
1. Chiudere lo strumento di configurazione.
1. Riavviare l'istanza Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurare l'archiviazione dei log per l'installazione di HADR
Per configurare l'archiviazione del registro Db2 per l'installazione di HADR, è consigliabile configurare sia il database primario che il database standby in modo che dispongano della funzionalità di recupero automatico dei log da tutti i percorsi di archiviazione del log. Sia il database primario che il database standby devono essere in grado di recuperare i file di archivio di log da tutti i percorsi di archivio log in cui una delle istanze del database potrebbe archiviare i file di registro. 

L'archiviazione dei log viene eseguita solo dal database primario. Se si modificano i ruoli HADR dei server di database o se si verifica un errore, il nuovo database primario è responsabile dell'archiviazione dei log. Se sono stati impostati più percorsi di archiviazione dei log, i log potrebbero essere archiviati due volte. In caso di recupero locale o remoto, potrebbe anche essere necessario copiare manualmente i registri archiviati dal server primario precedente al percorso di registro attivo del nuovo server primario.

È consigliabile configurare una condivisione NFS comune o GlusterFS, in cui i log vengono scritti da entrambi i nodi. La condivisione NFS o GlusterFS deve essere altamente disponibile. 

È possibile utilizzare condivisioni NFS a disponibilità elevata esistenti o GlusterFS per i trasporti o una directory di profilo. Per altre informazioni, vedere:

- [GlusterFS in VM di Azure in Red Hat Enterprise Linux per SAP NetWeaver][glusterfs] 
- [Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in Red Hat Enterprise Linux con i file NetApp di Azure per le applicazioni SAPHigh availability for SAP NetWeaver on Azure VMs on Azure VMs on Red Hat Enterprise Linux with Azure NetApp Files for SAP Applications][anf-rhel]
- [File NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) di Azure (per creare condivisioni NFS)Azure NetApp Files (to create NFS shares)

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

In questa sezione viene descritto come testare l'installazione di Db2 HADR. Ogni test presuppone che IBM Db2 primario sia in esecuzione sulla macchina virtuale *az-idb01.* È necessario utilizzare l'utente con privilegi sudo o root (scelta non consigliata).

Lo stato iniziale per tutti i test case è spiegato qui: (crm_mon stato -r o pcs)

- **stato pcs** è un'istantanea dello stato di Pacemaker in fase di esecuzione 
- **crm_mon -r** è l'uscita continua dello stato pacemaker

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

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nell'immagine seguente:

![DBACockpit - Pre-migrazione](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Test aree di acquisizione di IBM Db2


> [!IMPORTANT] 
> Prima di avviare il test, assicurarsi che:
> * Pacemaker non ha azioni non riuscite (stato pcs).
> * Non esistono vincoli di posizione (avanzi del test di migrazione)There are no location constraints (leftovers of migration test)
> * La sincronizzazione di IBM Db2 HADR sta funzionando. Verificare con\<l'utente db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Eseguire la migrazione del nodo che esegue il database Db2 primario eseguendo il comando seguente:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Al termine della migrazione, l'output dello stato crm è simile al:
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

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nell'immagine seguente:

![DBACockpit - Post-Migrazione](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

La migrazione delle risorse con "spostazione risorse pcs" crea vincoli di posizione. I vincoli di posizione in questo caso impediscono l'esecuzione dell'istanza IBM Db2 in az-idb01. Se i vincoli di posizione non vengono eliminati, la risorsa non può eseguire il failback.

Rimuovere il vincolo di posizione e il nodo standby verrà avviato in az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
E lo stato del cluster cambia per:
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

![DBACockpit - Contenitore di posizione rimosso](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Eseguire la migrazione della risorsa ad az-idb01 e cancellare i vincoli di posizioneMigrate the resource back to *az-idb01* and clear the location constraints
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **spostamento \<delle risorse <host>pcs res_name> :** Crea vincoli di posizione e può causare problemi con l'acquisizione
- **Risorsa pcs \<cancella res_name>**: Cancella i vincoli di posizione
- **pulizia risorse \<pcs res_name>**: Cancella tutti gli errori della risorsa

### <a name="test-a-manual-takeover"></a>Testare una acquisizione manuale

È possibile testare un'acquisizione manuale arrestando il servizio Pacemaker sul nodo *az-idb01:You* can test a manual takeover by stopping the Pacemaker service on az-idb01 node:
<pre><code>systemctl stop pacemaker</code></pre>

stato in *az-ibdb02*
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

Dopo il failover, è possibile riavviare il servizio in *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Interrompere il processo Db2 nel nodo che esegue il database primario HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

L'istanza Db2 avrà esito negativo e Pacemaker sposterà il nodo master e segnalerà lo stato seguente:

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

Pacemaker riavvierà l'istanza del database primario Db2 sullo stesso nodo oppure eseguirà il failover al nodo che esegue l'istanza del database secondario e viene segnalato un errore.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Terminare il processo Db2 nel nodo che esegue l'istanza del database secondario

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Il nodo entra in errore dichiarato e l'errore segnalato
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

L'istanza Db2 viene riavviata nel ruolo secondario assegnato in precedenza.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arrestare DB tramite db2stop forza sul nodo che esegue l'istanza del database primario HADR

Come utente\<db2 sid> eseguire il comando db2stop force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Rilevato errore:

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

L'istanza del database secondario Db2 HADR è stata promossa nel ruolo primario.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Arresto anomalo della macchina virtuale che esegue l'istanza del database primario HADR con "arresto"

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

Il passo successivo è quello di verificare la presenza di una situazione *di Split cervello.* Dopo che il nodo superstite ha determinato che il nodo che ha eseguito l'ultima istanza del database primario è inattivo, viene eseguito un failover delle risorse.

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


In caso di panico del kernel, il nodo guasto verrà rimesso dall'agente di scherma. Dopo che il nodo in cui si è verificato l'errore è tornato online, è necessario avviare il cluster pacemaker
<pre><code>sudo pcs cluster start</code></pre> avvia l'istanza Db2 nel ruolo secondario.

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
- [Architettura e scenari di disponibilità elevata per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurazione di Pacemaker in Red Hat Enterprise Linux in Azure][rhel-pcs-azr]
