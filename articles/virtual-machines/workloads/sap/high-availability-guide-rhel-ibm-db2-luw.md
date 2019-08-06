---
title: Configurare IBM DB2 HADR in macchine virtuali (VM) di Azure in RHEL | Microsoft Docs
description: Stabilire la disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure (VM) RHEL.
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
ms.openlocfilehash: 232468d9b3466759d7af2b7be68a1f553ced6e6d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68348651"
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
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Disponibilità elevata di IBN Db2 LUW in macchine virtuali di Azure su Red Hat Enterprise Linux Server

IBM DB2 per Linux, UNIX e Windows (LUW) nella [configurazione di disponibilità elevata e ripristino di emergenza (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) è costituito da un nodo che esegue un'istanza di database primario e almeno un nodo che esegue un'istanza del database secondario. Le modifiche apportate all'istanza del database primario vengono replicate in modo sincrono o asincrono in un'istanza del database secondario, a seconda della configurazione. 

Questo articolo descrive come distribuire e configurare le macchine virtuali (VM) di Azure, installare il Framework del cluster e installare IBM DB2 LUW con la configurazione di HADR. 

L'articolo non illustra come installare e configurare IBM DB2 LUW con l'installazione del software HADR o SAP. Per consentire l'esecuzione di queste attività, vengono forniti riferimenti ai manuali di installazione di SAP e IBM. Questo articolo è incentrato sulle parti specifiche dell'ambiente Azure. 

Le versioni supportate di IBM DB2 sono 10,5 e versioni successive, come descritto nella nota SAP [1928533].

Prima di iniziare un'installazione, vedere le note e la documentazione SAP seguenti:

| Nota SAP | DESCRIZIONE |
| --- | --- |
| [1928533] | Applicazioni SAP in Azure: Prodotti e tipi di macchine virtuali di Azure supportati |
| [2015553] | SAP in Azure: Prerequisiti di supporto |
| [2178632] | Metriche di monitoraggio principali per SAP in Azure |
| [2191498] | SAP in Linux con Azure: Monitoraggio avanzato |
| [2243692] | VM Linux in Azure (IaaS): problemi delle licenze SAP |
| [2002167] | Red Hat Enterprise Linux 7.x: installazione e aggiornamento |
| [2694118] | Componente aggiuntivo Red Hat Enterprise Linux disponibilità elevata in Azure |
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
| [Panoramica del componente aggiuntivo per la disponibilità elevata per Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin] (Amministrazione dei componenti aggiuntivi a disponibilità elevata) |
| [High Availability Add-On Reference][rhel-ha-ref] (Riferimento dei componenti aggiuntivi a disponibilità elevata) |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp] (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst] (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
| [Distribuzione DBMS di macchine virtuali di Azure IBM DB2 per il carico di lavoro SAP][dbms-db2] |
| [IBM DB2 HADR 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR 10,5][db2-hadr-10.5] |
| [Criteri di supporto per i cluster RHEL a disponibilità elevata-gestione di IBM DB2 per Linux, UNIX e Windows in un cluster][rhel-db2-supp]



## <a name="overview"></a>Panoramica
Per ottenere la disponibilità elevata, IBM DB2 LUW con HADR è installato in almeno due macchine virtuali di Azure, che vengono distribuite in un [set di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) o tra [zone di disponibilità di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

La grafica seguente mostra una configurazione di due macchine virtuali di Azure del server di database. Entrambe le macchine virtuali di Azure del server di database hanno una propria archiviazione collegata e sono in esecuzione. In HADR, un'istanza di database in una delle macchine virtuali di Azure ha il ruolo di istanza primaria. Tutti i client sono connessi all'istanza primaria. Tutte le modifiche apportate alle transazioni del database vengono rese disponibili localmente nel log delle transazioni DB2. Poiché i record del log delle transazioni vengono salvati in modo permanente in locale, i record vengono trasferiti tramite TCP/IP all'istanza del database nel secondo server di database, nel server di standby o nell'istanza di standby. L'istanza standby aggiorna il database locale eseguendo il rollforward dei record del log delle transazioni trasferiti. In questo modo, il server di standby viene mantenuto sincronizzato con il server primario.

HADR è solo una funzionalità di replica. Non è disponibile alcun rilevamento degli errori e nessuna funzionalità di acquisizione automatica o di failover. Un'acquisizione o un trasferimento al server di standby deve essere avviato manualmente da un amministratore di database. Per ottenere un'acquisizione automatica e un rilevamento degli errori, è possibile usare la funzionalità di clustering pacemaker di Linux. Pacemaker monitora le due istanze del server di database. Quando l'istanza del server di database primario si arresta in modo anomalo, pacemaker avvia un'acquisizione *automatica* del HADR dal server di standby. Pacemaker garantisce inoltre che l'indirizzo IP virtuale venga assegnato al nuovo server primario.

![Panoramica della disponibilità elevata di IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Per fare in modo che i server applicazioni SAP si connettano al database primario, sono necessari un nome host virtuale e un indirizzo IP virtuale. In caso di failover, i server applicazioni SAP si connetteranno a una nuova istanza di database primario. In un ambiente Azure è necessario un servizio di [bilanciamento del carico di Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) per usare un indirizzo IP virtuale nel modo necessario per HADR di IBM DB2. 

Per comprendere completamente il modo in cui IBM DB2 LUW con HADR e pacemaker si integrano in una configurazione di sistema SAP a disponibilità elevata, nell'immagine seguente viene presentata una panoramica di una configurazione a disponibilità elevata di un sistema SAP basato su un database IBM DB2. Questo articolo illustra solo IBM DB2, ma fornisce riferimenti ad altri articoli su come configurare altri componenti di un sistema SAP.

![Panoramica dell'ambiente completo della disponibilità elevata di IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Panoramica di alto livello dei passaggi necessari
Per distribuire una configurazione di IBM DB2, è necessario attenersi alla procedura seguente:

  + Pianificare l'ambiente.
  + Distribuire le macchine virtuali.
  + Aggiornare RHEL Linux e configurare i file System.
  + Installare e configurare pacemaker.
  + Configurare il [cluster GlusterFS][glusterfs] o [Azure NetApp files][anf-rhel]
  + Installare [ASC/ERS in un cluster separato][ascs-ha-rhel].
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
| Schermatura di Azure | Viene impedito il metodo per evitare la suddivisione di situazioni cerebrali. |
| Azure Load Balancer | Utilizzo di Basic o standard (scelta consigliata), porta Probe per database DB2 (raccomandazione 62500) **Probe-Port**. |
| Risoluzione dei nomi| Funzionamento della risoluzione dei nomi nell'ambiente. Il servizio DNS è altamente consigliato. È possibile usare il file hosts locale. |
    
Per altre informazioni su pacemaker per Linux in Azure, vedere la pagina relativa alla [configurazione di pacemaker in Red Hat Enterprise Linux in Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Distribuzione in Red Hat Enterprise Linux

L'agente di risorse per IBM DB2 LUW è incluso in Red Hat Enterprise Linux addon a disponibilità elevata del server. Per la configurazione descritta in questo documento, è necessario usare Red Hat Enterprise Linux per SAP. Azure Marketplace contiene un'immagine per Red Hat Enterprise Linux 7,4 per SAP o versione successiva che è possibile usare per distribuire nuove macchine virtuali di Azure. Quando si sceglie un'immagine di macchina virtuale nel Marketplace di macchine virtuali di Azure, tenere presente i vari modelli di supporto o di servizio offerti da Red Hat tramite Azure Marketplace.

### <a name="hosts-dns-updates"></a>Host: Aggiornamenti del DNS
Creare un elenco di tutti i nomi host, inclusi i nomi host virtuali, e aggiornare i server DNS per abilitare l'indirizzo IP appropriato per la risoluzione dei nomi host. Se un server DNS non esiste o non è possibile aggiornare e creare voci DNS, è necessario usare i file host locali delle singole VM che partecipano a questo scenario. Se si usano voci di file host, assicurarsi che le voci vengano applicate a tutte le macchine virtuali nell'ambiente di sistema SAP. Tuttavia, si consiglia di usare il DNS che, idealmente, si estende in Azure


### <a name="manual-deployment"></a>Distribuzione manuale

Verificare che il sistema operativo selezionato sia supportato da IBM/SAP per IBM DB2 LUW. L'elenco delle versioni del sistema operativo supportate per le macchine virtuali di Azure e le versioni DB2 è disponibile nella nota SAP [1928533]. L'elenco delle versioni del sistema operativo per ogni singola versione di DB2 è disponibile nella matrice di disponibilità del prodotto SAP. È consigliabile un minimo di Red Hat Enterprise Linux 7,4 per SAP grazie ai miglioramenti delle prestazioni correlati ad Azure in questa o in versioni successive Red Hat Enterprise Linux.

1. Creare o selezionare un gruppo di risorse.
1. Creare o selezionare una rete virtuale e una subnet.
1. Creare un set di disponibilità di Azure o distribuire una zona di disponibilità.
    + Per il set di disponibilità, impostare il numero massimo di domini di aggiornamento su 2.
1. Creazione della macchina virtuale 1.
    + Usare Red Hat Enterprise Linux per l'immagine SAP in Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 o selezionare zona di disponibilità.
1.  Creare la macchina virtuale 2.
    + Usare Red Hat Enterprise Linux per l'immagine SAP in Azure Marketplace.
    + Selezionare il set di disponibilità di Azure creato nel passaggio 3 oppure selezionare zona di disponibilità (non la stessa zona del passaggio 3).
1. Aggiungere dischi dati alle macchine virtuali, quindi controllare l'indicazione di un file system installazione nell'articolo [distribuzione DBMS di macchine virtuali di Azure IBM DB2 per il carico di lavoro SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker
    
Per creare un cluster Pacemaker di base per questo server IBM DB2, vedere la pagina relativa alla [configurazione di pacemaker in Red Hat Enterprise Linux in Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Regole del firewall di Red Hat
Red Hat Enterprise Linux dispone di un firewall abilitato per impostazione predefinita. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Suggerimenti sull'installazione per la configurazione di IBM DB2 LUW con HADR

Per configurare l'istanza primaria del database IBM DB2 LUW:

- Usare l'opzione di disponibilità elevata o distribuita.
- Installare SAP ASC/ERS e l'istanza di database.
- Eseguire un backup del nuovo database installato.

> [!IMPORTANT] 
> Prendere nota della "porta di comunicazione del database" impostata durante l'installazione. Deve corrispondere allo stesso numero di porta per entrambe le istanze di database.
>![Definizione porta SWPM SAP](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Impostazioni di IBM DB2 HADR per Azure

   Quando si usa un agente di schermatura pacemaker di Azure, impostare i parametri seguenti:

   - Durata della finestra peer HADR (secondi) (HADR_PEER_WINDOW) = 240  
   - Valore di timeout HADR (HADR_TIMEOUT) = 45

Si consiglia di usare i parametri precedenti in base al failover iniziale/test di acquisizione. È obbligatorio verificare la correttezza delle funzionalità di failover e acquisizione con queste impostazioni dei parametri. Poiché le singole configurazioni possono variare, i parametri potrebbero richiedere la regolazione. 

> [!NOTE]
> Specifico per IBM DB2 con configurazione HADR con avvio normale: Prima di poter avviare l'istanza del database primario, è necessario che l'istanza del database secondario o standby sia attiva e in esecuzione.

   
> [!NOTE]
> Per l'installazione e la configurazione specifiche di Azure e pacemaker: Durante la procedura di installazione tramite SAP software Provisioning Manager, esiste una domanda esplicita sulla disponibilità elevata per IBM DB2 LUW:
>+ Non selezionare **IBM DB2 pureScale**.
>+ Non selezionare **Installa IBM Tivoli System Automation per piattaforme multipiattaforma**.
>+ Non selezionare **genera file di configurazione del cluster**.
>![Opzioni disponibilità elevata di SAP SWPM-DB2](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Per configurare il server di database di standby usando la procedura di copia di sistema omogenea di SAP, eseguire questi passaggi:

1. Selezionare l'opzione **copia di sistema** >**istanza database** **distribuito** > di **sistemi** > di destinazione.
1. Come metodo di copia, selezionare **sistema omogeneo** in modo da poter utilizzare il backup per ripristinare un backup nell'istanza del server di standby.
1. Quando si raggiunge il passaggio di uscita per ripristinare il database per la copia di sistema omogenea, uscire dal programma di installazione. Ripristinare il database da un backup dell'host primario. Tutte le fasi di installazione successive sono già state eseguite sul server di database primario.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Regole di Red Hat firewall per DB2 HADR
Aggiungere regole del firewall per consentire il funzionamento del traffico per DB2 e tra DB2 per HADR:
+ Porta di comunicazione del database. Se si usano le partizioni, aggiungere anche queste porte.
+ Porta HADR (valore del parametro DB2 HADR_LOCAL_SVC)
+ Porta Probe di Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Verifica HADR IBM DB2
A scopo dimostrativo e alle procedure descritte in questo articolo, il SID del database è **ID2**.

Dopo aver configurato HADR e lo stato è PEER e CONNECTED nei nodi Primary e standby, effettuare le seguenti verifiche:

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



## <a name="db2-pacemaker-configuration"></a>Configurazione di DB2 pacemaker

Quando si usa pacemaker per il failover automatico in caso di errore di un nodo, è necessario configurare le istanze DB2 e pacemaker di conseguenza. Questa sezione descrive questo tipo di configurazione.

Gli elementi seguenti sono preceduti da uno dei seguenti elementi:

- **[A]** : Applicabile a tutti i nodi
- **[1]** : Applicabile solo al nodo 1 
- **[2]** : Applicabile solo al nodo 2

**[A]** prerequisiti per la configurazione di pacemaker:
1. Arrestare entrambi i server di database con l'\<utente DB2 SID > con db2stop.
1. Modificare l'ambiente della Shell per\<DB2 SID > utente in */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Configurazione pacemaker

**[1]** configurazione pacemaker specifica di IBM DB2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** creare risorse IBM DB2:
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

**[1]** avviare le risorse IBM DB2:
* Attivare la modalità manutenzione per pacemaker.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** verificare che lo stato del cluster sia OK e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [AZ-idb01 AZ-idb02]

Elenco completo delle risorse:

 rsc_st_azure   (stonith:fence_azure_arm):      Avvio AZ-idb01 master/slave set: Db2_HADR_ID2-Master [Db2_HADR_ID2] Masters: [AZ-idb01] Slaves: [AZ-idb02] Resource Group: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (OCF:: heartbeat: IPaddr2):       Avvio AZ-idb01 nc_db2id2_ID2 (OCF:: heartbeat: Azure-lb):      Avvio AZ-idb01

Stato del daemon: Corosync: attivo/disabilitato pacemaker: attivo/disabilitato PCSD: attivo/abilitato
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

**[A] aggiungere una** regola del firewall per la porta Probe:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Accedere al server applicazioni primario dell'istanza di J2EE ed eseguire:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1.Nel riquadro a sinistra scegliere **Archivio sicurezza**.
1.Nel riquadro destro scegliere la chiave JDBC/pool/\<SAPSID>/URL.
1.Modificare il nome host nell'URL JDBC per il nome host virtuale.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1.Selezionare **Aggiungi**.
1.Per salvare le modifiche, selezionare l'icona del disco in alto a sinistra.
1.Chiudere lo strumento di configurazione di.
1.Riavviare l'istanza di Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurare l'archiviazione dei log per l'installazione di HADR
Per configurare l'archiviazione dei log DB2 per il programma di installazione di HADR, è consigliabile configurare il database primario e quello di standby in modo da ottenere la funzionalità di recupero log automatica da tutti i percorsi di archiviazione dei log. Sia il database primario che quello di standby devono essere in grado di recuperare i file di archivio di log da tutti i percorsi di archiviazione dei log in cui una delle istanze del database può archiviare i file di log. 

L'archiviazione dei log viene eseguita solo dal database primario. Se si modificano i ruoli HADR dei server di database o si verifica un errore, il nuovo database primario è responsabile dell'archiviazione dei log. Se sono stati configurati più percorsi di archiviazione dei log, è possibile che i log vengano archiviati due volte. In caso di un recupero locale o remoto, potrebbe essere necessario copiare manualmente i log archiviati dal server primario precedente al percorso di log attivo del nuovo server primario.

Si consiglia di configurare una condivisione NFS comune o GlusterFS, in cui i log vengono scritti da entrambi i nodi. La condivisione NFS o GlusterFS deve essere a disponibilità elevata. 

È possibile utilizzare condivisioni NFS a disponibilità elevata o GlusterFS per trasporti o una directory di profilo. Per altre informazioni, vedere:

- [GlusterFS in VM di Azure in Red Hat Enterprise Linux per SAP NetWeaver][glusterfs] 
- [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP][anf-rhel]
- [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (per creare condivisioni NFS)

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come è possibile testare l'installazione di DB2 HADR. Ogni test presuppone che IBM DB2 Primary sia in esecuzione nella macchina virtuale *AZ-idb01* . È necessario usare l'utente con privilegi sudo o radice (scelta non consigliata).

Lo stato iniziale di tutti i test case è illustrato qui: (stato crm_mon-r o PC)

- **lo stato dei PC** è uno snapshot dello stato di pacemaker in fase di esecuzione 
- **crm_mon-r** è l'output continuo dello stato di pacemaker

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

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nella figura seguente:

![DBACockpit-pre-migrazione](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Acquisizione dei test di IBM DB2


> [!IMPORTANT] 
> Prima di iniziare il test, assicurarsi che:
> * Pacemaker non ha azioni non riuscite (stato dei PC).
> * Nessun vincolo di posizione (avanzi del test di migrazione)
> * La sincronizzazione IBM DB2 HADR funziona. Verificare con l'utente\<DB2 SID > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Eseguire la migrazione del nodo che esegue il database DB2 primario eseguendo il comando seguente:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Al termine della migrazione, l'output dello stato CRM sarà simile al seguente:
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

Lo stato originale in un sistema SAP è documentato in Transaction DBACOCKPIT > Configuration > Overview, come illustrato nella figura seguente:

![DBACockpit-post-migrazione](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

La migrazione delle risorse con lo spostamento di risorse dei PC crea vincoli di percorso. In questo caso i vincoli di posizione impediscono l'esecuzione dell'istanza di IBM DB2 in AZ-idb01. Se i vincoli di percorso non vengono eliminati, non è possibile eseguire il failback della risorsa.

Rimuovere il percorso vincolo e il nodo standby verranno avviati in AZ-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
E lo stato del cluster passa a:
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

![DBACockpit-rimosso percorso vincolato](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Eseguire di nuovo la migrazione della risorsa a *AZ-idb01* e cancellare i vincoli location
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **risorse PC spostare \<RES_NAME > <host>:** Crea vincoli di posizione e può causare problemi con l'acquisizione
- **> RES_NAME cancellazione \<risorse PC**: Cancella i vincoli di posizione
- **pulizia\<risorse PC RES_NAME >** : Cancella tutti gli errori della risorsa

### <a name="test-a-manual-takeover"></a>Testare un'acquisizione manuale

È possibile testare un'acquisizione manuale arrestando il servizio pacemaker sul nodo *AZ-idb01* :
<pre><code>systemctl stop pacemaker</code></pre>

stato in *AZ-ibdb02*
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

Dopo il failover, è possibile riavviare il servizio in *AZ-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Terminare il processo DB2 sul nodo che esegue il database primario HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

L'istanza DB2 avrà esito negativo e pacemaker sposterà il nodo master e segnalerà lo stato seguente:

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

Pacemaker riavvierà l'istanza del database primario DB2 nello stesso nodo oppure verrà eseguito il failover sul nodo che esegue l'istanza del database secondario e viene segnalato un errore.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Terminare il processo DB2 sul nodo che esegue l'istanza del database secondario

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Il nodo non è stato dichiarato ed è stato segnalato un errore
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

L'istanza di DB2 viene riavviata nel ruolo secondario assegnato in precedenza.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arrestare DB tramite db2stop Force sul nodo che esegue l'istanza del database primario HADR

Come utente DB2\<SID > Execute Command db2stop Force:
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

L'istanza del database secondario DB2 HADR è stata innalzata nel ruolo primario.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Arrestare l'arresto anomalo della macchina virtuale che esegue l'istanza del database primario HADR con "Halt"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

In tal caso, pacemaker rileverà che il nodo che esegue l'istanza del database primario non risponde.

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

Il passaggio successivo consiste nel verificare la presenza di una situazione di *Split Brain* . Dopo che il nodo superstite ha determinato che il nodo che ha eseguito l'ultima esecuzione dell'istanza di database primario è inattivo, viene eseguito un failover delle risorse.

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


Nel caso di un kernel panic, il nodo in errore verrà riavviato dall'agente di schermatura. Quando il nodo con errore è nuovamente online, è necessario avviare il cluster Pacemaker
<pre><code>sudo pcs cluster start</code></pre> viene avviata l'istanza DB2 nel ruolo secondario.

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
- [Architettura e scenari a disponibilità elevata per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurazione di pacemaker in Red Hat Enterprise Linux in Azure][rhel-pcs-azr]
