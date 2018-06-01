---
title: Configurare la replica di sistema SAP HANA in Macchine virtuali di Azure | Microsoft Docs
description: Configurare la disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: 1965438e64af84d0c808b0684f9e81c797193bff
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266862"
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

In locale è possibile usare la replica di sistema HANA oppure l'archiviazione condivisa per stabilire la disponibilità elevata per SAP HANA.
La replica di sistema HANA è l'unica funzione per la disponibilità elevata supportata fino a questo momento in Macchine virtuali di Azure. La replica SAP HANA è costituita da un nodo primario e da almeno un nodo secondario. Le modifiche ai dati nel nodo primario vengono replicate nel nodo secondario in modo sincrono o asincrono.

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare e configurare la replica di sistema SAP HANA.
Nelle configurazioni di esempio, nei comandi di installazione e così via vengono usati il numero di istanza 03 e l'ID di sistema HANA HN1.

Leggere prima di tutto le note e i documenti seguenti relativi a SAP

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2205917] contiene le impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [1944799] contiene linee guida per SAP HANA per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux (questo articolo)][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP HANA SR Performance Optimized Scenario][suse-hana-ha-guide] (Scenario ottimizzato per le prestazioni di SAP HANA SR) La guida contiene tutte le informazioni necessarie per configurare la replica di sistema SAP HANA in locale. Usare la guida per le indicazioni di base.

## <a name="overview"></a>Panoramica

Per ottenere disponibilità elevata, il sistema SAP HANA viene installato in due macchine virtuali. I dati vengono replicati tramite la replica del sistema HANA.

![Panoramica della disponibilità elevata SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Il programma di installazione di SAP HANA SR usa un nome host virtuale e indirizzi IP virtuali dedicati. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. L'elenco seguente mostra la configurazione del bilanciamento del carico.

* Configurazione front-end
  * Indirizzo IP 10.0.0.13 per hn1-db
* Configurazione back-end
  * Connessione a interfacce di rete primarie di tutte le macchine virtuali che devono far parte della replica del sistema HANA
* Porta probe
  * Porta 62503
* Regole di bilanciamento del carico
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Distribuzione di Linux

L'agente delle risorse per SAP HANA è incluso in SUSE Linux Enterprise Server for SAP Applications.
Azure Marketplace contiene un'immagine per SUSE Linux Enterprise Server for SAP Applications 12 che è possibile usare per distribuire nuove macchine virtuali.

### <a name="deploy-with-template"></a>Eseguire la distribuzione con un modello
È possibile usare uno dei modelli di avvio rapido di GitHub per distribuire tutte le risorse necessarie. Il modello consente di distribuire le macchine virtuali, il servizio di bilanciamento del carico, il set di disponibilità e così via. Per distribuire il modello, seguire questi passaggi:

1. Aprire il [modello di database][template-multisid-db] o il [modello con convergenza][template-converged] nel portale di Azure. 
   Il modello di database crea solo le regole di bilanciamento del carico per un database, mentre il modello con convergenza crea anche le regole di bilanciamento del carico per un'istanza di ASCS/SCS ed ERS (solo Linux). Se si prevede di installare un sistema basato su SAP NetWeaver e si vuole installare anche l'istanza di ASCS/SCS nelle stesse macchine, usare il [modello con convergenza][template-converged].
1. Immettere i parametri seguenti
    1. Sap System ID  
       Immettere l'ID del sistema SAP che si vuole installare. L'ID verrà usato come prefisso per le risorse distribuite.
    1. Tipo di stack (applicabile solo se si usa il modello con convergenza)   
       Selezionare il tipo di stack SAP NetWeaver
    1. Tipo di sistema operativo  
       Selezionare una delle distribuzioni Linux. Per questo esempio, selezionare SLES 12
    1. Tipo di database  
       Selezionare HANA
    1. Dimensioni del sistema SAP  
       Quantità di SAPS che verranno forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP
    1. Disponibilità del sistema  
       Selezionare la disponibilità elevata.
    1. Nome utente e password amministratore  
       Verrà creato un nuovo utente con cui è possibile accedere alla macchina
    1. Subnet nuova o esistente  
       Determina se devono essere create una nuova rete virtuale e una nuova subnet o deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare "existing".
    1. Subnet ID  
    ID della subnet a cui devono essere connesse le macchine virtuali. Per connettere la macchina virtuale alla rete locale, selezionare la subnet della rete virtuale Express Route o della VPN. L'ID si presenta in genere come segue: /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

### <a name="manual-deployment"></a>Distribuzione manuale

1. Creare un gruppo di risorse
1. Creare una rete virtuale
1. Creare un set di disponibilità  
   Impostare il numero massimo di domini di aggiornamento
1. Creare un servizio di bilanciamento del carico (interno)  
   Selezionare la rete virtuale creata nel secondo
1. Creare la macchina virtuale 1  
   Usare almeno SLES4SAP 12 SP1, in questo esempio verrà usata l'immagine SLES4SAP 12 SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES per SAP 12 SP2 (Premium)  
   Selezionare il set di disponibilità creato in precedenza  
1. Creare la macchina virtuale 2  
   Usare almeno SLES4SAP 12 SP1, in questo esempio verrà usata l'immagine SLES4SAP 12 SP1 BYOS https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES per SAP 12 SP2 (Premium)  
   Selezionare il set di disponibilità creato in precedenza  
1. Aggiungere dischi dati
1. Configurare il servizio di bilanciamento del carico
    1. Creare un pool di indirizzi IP front-end
        1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
        1. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, hana-frontend)
        1. Impostare Assegnazione su Statico e immettere l'indirizzo IP, ad esempio **10.0.0.13**
        1. Fare clic su OK.
        1. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare il relativo indirizzo IP
    1. Creare un pool back-end
        1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
        1. Immettere il nome del nuovo pool back-end (ad esempio, hana-backend)
        1. Fare clic su Aggiungi una macchina virtuale
        1. Selezionare il set di disponibilità creato in precedenza
        1. Selezionare le macchine virtuali del cluster SAP HANA
        1. Fare clic su OK.
    1. Creare un probe di integrità
        1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
        1. Immettere il nome del nuovo probe integrità (ad esempio, hana-hp)
        1. Selezionare TCP come protocollo, la porta 625**03**, mantenere 5 per Intervallo e impostare il valore di Soglia di non integrità su 2
        1. Fare clic su OK.
    1. SAP HANA 1.0: Creare regole di bilanciamento del carico
        1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
        1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**15)
        1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe integrità creati in precedenza (ad esempio, hana-frontend)
        1. Mantenere il protocollo TCP, immettere la porta 3**03**15
        1. Aumentare il timeout di inattività a 30 minuti
        1. **Assicurarsi di abilitare l'indirizzo IP mobile**
        1. Fare clic su OK.
        1. Ripetere i passaggi precedenti per la porta 3**03**17
    1. SAP HANA 2.0: Creare regole di bilanciamento del carico per il database di sistema
        1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
        1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio hana-lb-3**03**13
        1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe integrità creati in precedenza (ad esempio, hana-frontend)
        1. Mantenere il protocollo TCP, immettere la porta 3**03**13
        1. Aumentare il timeout di inattività a 30 minuti
        1. **Assicurarsi di abilitare l'indirizzo IP mobile**
        1. Fare clic su OK.
        1. Ripetere i passaggi precedenti per la porta 3**03**14
    1. SAP HANA 2.0: Creare regole di bilanciamento del carico per il primo database tenant
        1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
        1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio, hana-lb-3**03**40
        1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe integrità creati in precedenza (ad esempio, hana-frontend)
        1. Mantenere il protocollo TCP, immettere la porta 3**03**40
        1. Aumentare il timeout di inattività a 30 minuti
        1. **Assicurarsi di abilitare l'indirizzo IP mobile**
        1. Fare clic su OK.
        1. Ripetere i passaggi precedenti per le porte 3**03**41 e 3**03**42

Per altre informazioni sulle porte necessarie per SAP HANA, leggere il capitolo [Connections to Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) (Connessioni a database tenant) della guida [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) (Database tenant SAP HANA) o la [nota SAP 2388694][2388694].


## <a name="create-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure) per creare un cluster Pacemaker di base per questo server HANA. È possibile usare lo stesso cluster Pacemaker anche per SAP HANA e SAP NetWeaver (A)SCS.

## <a name="installing-sap-hana"></a>Installazione di SAP HANA

Gli elementi seguenti sono preceduti dal prefisso **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2 del cluster Pacemaker.

1. **[A]** Configurare il layout dei dischi
    1. LVM  
       
       È in genere consigliabile usare LVM per i volumi che archiviano i dati e file di log. L'esempio seguente presuppone che le macchine virtuali abbiano quattro dischi dati collegati che devono essere usati per creare due volumi.

       Elencare tutti i dischi disponibili
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Output di esempio
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Creare i volumi fisici per tutti i dischi da usare.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Creare un gruppo di volumi per i file di dati, un gruppo di volumi per i file di log e uno per la directory condivisa di SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
        Creare i volumi logici. Quando si usa lvcreate senza il parametro -i verrà creato un volume lineare. È consigliabile creare un volume con striping per migliorare le prestazioni di I/O; l'argomento -i deve corrispondere al numero del volume fisico sottostante. In questo documento vengono usati 2 volumi fisici per il volume di dati e quindi l'argomento del parametro -i è 2. Viene usato 1 volume fisico per il volume di log e quindi non viene usato nessun parametro -i in modo esplicito. Usare il parametro -i e sostituire il numero con lo stesso numero di volume fisico sottostante quando si usa più di 1 volume fisico per ogni volume dei dati, dei log e della condivisione.

       <pre><code>
       sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Creare le directory di montaggio e copiare l'UUID di tutti i volumi logici
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Creare voci fstab per i tre volumi logici
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Inserire questa riga in /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Montare i nuovi volumi
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Dischi normali  
       Per sistemi demo, è possibile inserire i dati e i file di log HANA in un solo disco. I comandi seguenti creano una partizione in /dev/disk/azure/scsi1/lun0 e la formattano con XFS.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Inserire questa riga in /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Creare la directory di destinazione e montare il disco.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** Configurare la risoluzione dei nomi host per tutti gli host  
    È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti
    ```bash
    sudo vi /etc/hosts
    ```
    Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Installare pacchetti HANA a disponibilità elevata  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Per installare la replica di sistema SAP HANA, seguire il capitolo 4 della guida SAP HANA SR Performance Optimized Scenario (Scenario di ottimizzazione delle prestazioni di SAP HANA SR) all'indirizzo https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/

1. **[A]** Eseguire hdblcm dal DVD di HANA
    * Scegliere l'installazione -> 1
    * Selezionare i componenti aggiuntivi per l'installazione -> 1
    * Immettere il percorso di installazione [/hana/shared]: -> INVIO
    * Immettere il nome host locale [..]: -> INVIO
    * Aggiungere altri host al sistema? (y/n) [n]: -> INVIO
    * Immettere l'ID di sistema SAP HANA: <SID of HANA e.g. HN1>
    * Immettere il numero di istanza [00]:   
  Numero di istanza di HANA. Usare 03 se è stato usato il modello di Azure o se è stata seguita la procedura di distribuzione manuale
    * Selezionare la modalità di database/immettere l'indice [1]: -> INVIO
    * Selezionare l'utilizzo del sistema/immettere l'indice [4]:  
  Selezionare l'utilizzo del sistema
    * Immettere il percorso dei volumi di dati [/hana/data/HN1]: -> INVIO
    * Immettere il percorso dei volumi di log [/hana/log/HN1]: -> INVIO
    * Limitare l'allocazione massima della memoria? [n]: -> INVIO
    * Immettere il nome host del certificato per l'host "..." [...]: -> INVIO
    * Immettere la password dell'utente agente host SAP (sapadm):
    * Confermare la password dell'utente agente host SAP (sapadm):
    * Immettere la password dell'amministratore di sistema (hdbadm):
    * Confermare la password dell'amministratore di sistema (hdbadm):
    * Immettere la home directory dell'amministratore di sistema [/usr/sap/HN1/home]: -> INVIO
    * Immettere la shell di accesso dell'amministratore di sistema [/bin/sh]: -> INVIO
    * Immettere l'ID utente dell'amministratore di sistema [1001]: -> INVIO
    * Immettere l'ID del gruppo di utenti (sapsys) [79]: -> INVIO
    * Immettere la password dell'utente del database (SYSTEM):
    * Confermare la password dell'utente del database (SYSTEM):
    * Riavviare il sistema dopo il riavvio della macchina? [n]: -> INVIO
    * Continuare? (y/n):   
  Convalidare il riepilogo e immettere y per continuare

1. **[A]** Aggiornare l'agente host SAP  
  Scaricare l'archivio dell'agente host SAP più recente dal sito [SAP Softwarecenter] [sap-swcenter] ed eseguire il comando seguente per aggiornare l'agente. Sostituire il percorso dell'archivio in modo da puntare al file scaricato.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Configurare la replica di sistema di SAP HANA 2.0

Gli elementi seguenti sono preceduti dal prefisso **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2 del cluster Pacemaker.

1. **[1]** Creare un database tenant

   Se si usa SAP HANA 2.0 o MDC, creare un database tenant per il sistema SAP NetWeaver. Sostituire NW1 con il SID del sistema SAP in uso.

   Accedere come `<hanasid`>adm ed eseguire il comando seguente

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Configurare la replica di sistema nel primo nodo
   
   Accedere come `<hanasid`> adm ed eseguire il backup dei database

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copiare i file dell'infrastruttura a chiave pubblica (PKI) di sistema nel sito secondario

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Creare il sito primario.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Configurare la replica di sistema nel secondo nodo
    
    Registrare il secondo nodo per avviare la replica di sistema. Accedere come `<hanasid`>adm ed eseguire il comando seguente

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurare la replica di sistema di SAP HANA 1.0

1. **[1]**  Creare gli utenti necessari

    Accedere come root ed eseguire il comando seguente. Assicurarsi di sostituire le stringhe in grassetto (ID di sistema HANA HN1 e numero di istanza 03) con i valori dell'installazione di SAP HANA in uso.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** Creare una voce di archivio chiavi
   
    Accedere come root ed eseguire il comando seguente per creare una nuova voce di archivio chiavi.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]** Eseguire il backup dei database

   Accedere come root ed eseguire il backup dei database

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se si usa un'installazione multi-tenant, eseguire anche il backup del database tenant

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Configurare la replica di sistema nel primo nodo
    
    Accedere come `<hanasid`>adm e creare il sito primario.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Configurare la replica di sistema nel nodo secondario.

    Accedere come `<hanasid`> adm e registrare il sito secondario.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Creare le risorse cluster SAP HANA

   Prima di tutto, creare la topologia HANA. Eseguire i comandi seguenti in uno dei nodi del cluster Pacemaker.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Creare poi le risorse HANA.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Testare la configurazione del cluster
La sezione seguente descrive come testare la configurazione. Ogni test presuppone che si usi l'account root e che il master SAP HANA sia in esecuzione nella macchina virtuale hn1-db-0.

#### <a name="fencing-test"></a>Test di isolamento

È possibile testare la configurazione dell'agente di isolamento disabilitando l'interfaccia di rete nel nodo hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

La macchina virtuale dovrebbe ora venire riavviata o arrestata, a seconda della configurazione del cluster.
Se si imposta stonith-action su off, la macchina virtuale verrà arrestata e verrà eseguita la migrazione delle risorse alla macchina virtuale in esecuzione.

Una volta riavviata la macchina virtuale, la risorsa SAP HANA non viene avviata come secondaria se si imposta AUTOMATED_REGISTER="false". In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Test di un failover manuale

È possibile testare un failover manuale arrestando il servizio pacemaker nel nodo hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Dopo il failover, è possibile avviare nuovamente il servizio. Se si imposta AUTOMATED_REGISTER="false", la risorsa SAP HANA in hn1-db-0 non viene avviata come secondaria. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Test di una migrazione

È possibile eseguire la migrazione del nodo master SAP HANA eseguendo il comando seguente
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se si imposta AUTOMATED_REGISTER="false", questa sequenza di comandi deve eseguire la migrazione del nodo master SAP HANA e del gruppo contenente l'indirizzo IP virtuale a hn1-db-1.
La risorsa SAP HANA in hn1-db-0 non viene avviata come secondaria. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

La migrazione crea vincoli di posizione che devono essere eliminati di nuovo.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

È anche necessario eseguire la pulizia di stato della risorsa nodo secondario

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md). 
