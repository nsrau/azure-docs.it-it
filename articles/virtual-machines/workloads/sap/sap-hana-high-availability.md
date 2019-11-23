---
title: Disponibilità elevata di SAP HANA in macchine virtuali di Azure su SUSE Linux Enterprise Server | Microsoft Docs
description: Disponibilità elevata di SAP HANA in macchine virtuali di Azure su SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: sedusch
ms.openlocfilehash: ffa2f937a14aa14750480d1c45498fb4c49fcc30
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721494"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Disponibilità elevata di SAP HANA in macchine virtuali di Azure su SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]: https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

Per lo sviluppo locale, è possibile usare la replica di sistema HANA oppure l'archiviazione condivisa per fornire la disponibilità elevata per SAP HANA.
In Macchine virtuali di Azure la replica di sistema HANA in Azure è attualmente l'unica funzione per la disponibilità elevata supportata. La replica SAP HANA è costituita da un nodo primario e da almeno un nodo secondario. Le modifiche ai dati nel nodo primario vengono replicate nel nodo secondario in modo sincrono o asincrono.

Questo articolo descrive come distribuire e configurare le macchine virtuali, installare il framework del cluster, nonché installare e configurare la replica di sistema SAP HANA.
Nelle configurazioni di esempio e nei comandi di installazione vengono usati il numero di istanza **03** e l'ID di sistema HANA **HN1**.

Leggere prima di tutto i documenti e le note SAP seguenti:

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione di software SAP.
  * Informazioni importanti sulla capacità per le dimensioni delle macchine virtuali di Azure.
  * Software SAP e combinazioni di sistemi operativi e database supportati.
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure.
* Nota SAP [2015553], che elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* Nota SAP [2205917], che contiene le impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications.
* Nota SAP [1944799], che contiene le linee guida SAP HANA per SUSE Linux Enterprise Server for SAP Applications.
* Nota SAP [2178632], che contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* La nota SAP [401162] contiene informazioni su come evitare l'errore "indirizzo già in uso" quando si configura la replica di sistema HANA.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), che contiene tutte le note SAP necessarie per Linux.
* [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Piattaforme IaaS certificate per SAP HANA)
* Guida alla [pianificazione e implementazione di macchine virtuali di Azure per SAP in Linux][planning-guide] .
* [Distribuzione di macchine virtuali di Azure per SAP in Linux][deployment-guide] (questo articolo).
* Guida alla [distribuzione DBMS di macchine virtuali di Azure per SAP in Linux][dbms-guide] .
* [SUSE Linux Enterprise Server per le guide alle procedure consigliate di SAP Applications 12 SP3][sles-for-sap-bp]
  * Configurazione di un'infrastruttura ottimizzata per le prestazioni per la replica di sistema SAP HANA (SLES for SAP Applications 12 SP1). La guida contiene tutte le informazioni necessarie per configurare la replica di sistema SAP HANA per lo sviluppo locale. Usare la guida per le indicazioni di base.
  * Configurazione di un'infrastruttura ottimizzata per i costi per la replica di sistema SAP HANA (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Overview

Per ottenere disponibilità elevata, il sistema SAP HANA viene installato in due macchine virtuali. I dati vengono replicati tramite la replica di sistema HANA.

![Panoramica della disponibilità elevata SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

La procedura di configurazione della replica di sistema SAP HANA usa un nome host virtuale dedicato e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico:

* Configurazione front-end: indirizzo IP 10.0.0.13 per hn1-db
* Configurazione back-end: connessione a interfacce di rete primarie di tutte le macchine virtuali che devono far parte della replica di sistema HANA
* Porta probe: porta 62503
* Regole di bilanciamento del carico: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Eseguire la distribuzione per Linux

L'agente delle risorse per SAP HANA è incluso in SUSE Linux Enterprise Server for SAP Applications.
Azure Marketplace contiene un'immagine per SUSE Linux Enterprise Server for SAP Applications 12 che è possibile usare per distribuire nuove macchine virtuali.

### <a name="deploy-with-a-template"></a>Eseguire la distribuzione con un modello

È possibile usare uno dei modelli di avvio rapido di GitHub per distribuire tutte le risorse necessarie. Il modello consente di distribuire le macchine virtuali, il servizio di bilanciamento del carico, il set di disponibilità e così via.
Per distribuire il modello, seguire questi passaggi:

1. Aprire il [modello di database][template-multisid-db] o il [modello convergente][template-converged] nel portale di Azure. 
    Il modello di database crea le regole di bilanciamento del carico solo per un database. Il modello con convergenza crea anche le regole di bilanciamento del carico per un'istanza di ASCS/SCS ed ERS (solo Linux). Se si prevede di installare un sistema basato su SAP NetWeaver e si vuole installare l'istanza di ASC/SCS sulle stesse macchine, usare il [modello convergente][template-converged].

1. Immettere i parametri seguenti:
    - **Sap System Id** (ID sistema SAP): immettere l'ID del sistema SAP da installare. L'ID viene usato come prefisso per le risorse distribuite.
    - **Tipo di stack**: questo parametro è applicabile solo se si usa il modello convergente. Selezionare il tipo di stack SAP NetWeaver.
    - **Tipo di sistema operativo**: selezionare una delle distribuzioni Linux. Per questo esempio, selezionare **SLES 12**.
    - **Tipo di database**: selezionare **HANA**.
    - **Sap System Size** (Dimensioni sistema SAP): immettere il numero di istanze SAP fornite dal nuovo sistema. Se non si è certi del numero di istanze SAP necessarie per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
    - **System Availability**: selezionare **HA**.
    - **Nome utente amministratore e password amministratore**: viene creato un nuovo utente che può essere usato per accedere al computer.
    - **New Or Existing Subnet** (Subnet nuova o esistente): determina se devono essere create una nuova rete virtuale e una nuova subnet o se deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare **Esistente**.
    - **ID subnet**: se si vuole implementare la macchina virtuale in una rete virtuale esistente per cui è stata definita la subnet a cui assegnare la macchina virtuale, denominare l'ID di tale subnet. L'ID in genere ha il formato **/subscriptions/\<ID sottoscrizione>/resourceGroups/\<nome gruppo di risorse>/providers/Microsoft.Network/virtualNetworks/\<nome rete virtuale>/subnets/\<nome subnet>** .

### <a name="manual-deployment"></a>Distribuzione manuale

> [!IMPORTANT]
> Assicurarsi che il sistema operativo selezionato sia dotato di certificazione SAP per SAP HANA sugli specifici tipi di macchina virtuale in uso. Per un elenco dei tipi di macchina virtuale certificati per SAP HANA e delle versioni di sistema operativo correlate, vedere l'elenco delle [piattaforme IaaS certificate per SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Fare clic su ogni voce presente nell'elenco dei tipi di macchina virtuale per ottenere l'elenco completo delle versioni di sistema operativo supportate da SAP HANA per lo specifico tipo di VM.
>  

1. Creare un gruppo di risorse.
1. Creare una rete virtuale.
1. Creare un set di disponibilità.
   - Impostare il numero massimo di domini di aggiornamento.
1. Creare un servizio di bilanciamento del carico (interno). Si consiglia [Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   - Selezionare la rete virtuale creata nel passaggio 2.
1. Creare la macchina virtuale 1.
   - Usare un'immagine SLES4SAP nella raccolta di Azure che sia supportata per SAP HANA nel tipo di macchina virtuale selezionato.
   - Selezionare il set di disponibilità creato nel passaggio 3.
1. Creare la macchina virtuale 2.
   - Usare un'immagine SLES4SAP nella raccolta di Azure che sia supportata per SAP HANA nel tipo di macchina virtuale selezionato.
   - Selezionare il set di disponibilità creato nel passaggio 3. 
1. Aggiungere dischi dati.
1. Se si usa Load Balancer standard, seguire questa procedura di configurazione:
   1. Prima, creare il pool di indirizzi IP front-end:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
      1. Impostare **Assegnazione** su **Statico** e immettere l'indirizzo IP (ad esempio, **10.0.0.13**).
      1. Selezionare **OK**.
      1. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.
   
   1. Creare quindi un pool back-end:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
      1. Selezionare **Rete virtuale**.
      1. Selezionare **Aggiungi una macchina virtuale**.
      1. Selezionare * * macchina virtuale * *.
      1. Selezionare le macchine virtuali del cluster SAP HANA e i relativi indirizzi IP.
      1. Selezionare **Aggiungi**.
   
   1. Creare quindi un probe di integrità:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
      1. Selezionare **TCP** come protocollo e la porta 625**03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
      1. Selezionare **OK**.
   
   1. Successivamente, creare le regole di bilanciamento del carico:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
      1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **Hana-lb**.
      1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creato in precedenza (ad esempio, **Hana-frontend**, **Hana-backend** e **Hana-HP**).
      1. Selezionare **porte a disponibilità elevata**.
      1. Aumentare il valore di **Timeout di inattività** a 30 minuti.
      1. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
      1. Selezionare **OK**.

   > [!Note]
   > Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico di Azure standard (nessun indirizzo IP pubblico), non vi sarà connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [connettività degli endpoint pubblici per le macchine virtuali con Azure Load Balancer standard negli scenari di disponibilità elevata di SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

1. In alternativa, se lo scenario prevede l'uso del servizio di bilanciamento del carico di base, seguire questa procedura di configurazione:
   1. Prima, creare il pool di indirizzi IP front-end:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
      1. Impostare **Assegnazione** su **Statico** e immettere l'indirizzo IP (ad esempio, **10.0.0.13**).
      1. Selezionare **OK**.
      1. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.
   
   1. Creare quindi un pool back-end:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
      1. Selezionare **Aggiungi una macchina virtuale**.
      1. Selezionare il set di disponibilità creato nel passaggio 3.
      1. Selezionare le macchine virtuali del cluster SAP HANA.
      1. Selezionare **OK**.
   
   1. Creare quindi un probe di integrità:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
      1. Selezionare **TCP** come protocollo e la porta 625**03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
      1. Selezionare **OK**.
   
   1. Per SAP HANA 1.0, creare le regole di bilanciamento del carico:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
      1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**15).
      1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
      1. Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3**03**15.
      1. Aumentare il valore di **Timeout di inattività** a 30 minuti.
      1. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
      1. Selezionare **OK**.
      1. Ripetere questi passaggi per la porta 3**03**17.
   
   1. Per SAP HANA 2.0, creare le regole di bilanciamento del carico per il database di sistema:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
      1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**13).
      1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
      1. Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3**03**13.
      1. Aumentare il valore di **Timeout di inattività** a 30 minuti.
      1. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
      1. Selezionare **OK**.
      1. Ripetere questi passaggi per la porta 3**03**14.
   
   1. Per SAP HANA 2.0, creare prima le regole di bilanciamento del carico per il database tenant:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
      1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**40).
      1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
      1. Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3**03**40.
      1. Aumentare il valore di **Timeout di inattività** a 30 minuti.
      1. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
      1. Selezionare **OK**.
      1. Ripetere questi passaggi per le porte 3**03**41 e 3**03**42.

   Per ulteriori informazioni sulle porte necessarie per SAP HANA, vedere il capitolo [connessioni ai database tenant](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) nella Guida ai [database tenant di SAP Hana](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) o la [Nota SAP 2388694][2388694].

> [!IMPORTANT]
> Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net. IPv4. tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Load Balancer Probe di integrità](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Vedere anche la nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure) per creare un cluster Pacemaker di base per questo server HANA. È possibile usare lo stesso cluster Pacemaker per SAP HANA e SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Installare SAP HANA

Per i passaggi in questa sezione vengono usati i prefissi seguenti:
- **[T]** : il passaggio si applica a tutti i nodi.
- **[1]** : il passaggio si applica solo al nodo 1.
- **[2]** : Il passaggio si applica solo al nodo 2 del cluster Pacemaker.

1. **[T]** Configurare il layout dei dischi: **gestione volumi logici (LVM, Logical Volume Manager)** .

   È consigliabile usare LVM per i volumi che archiviano file di log e dati. L'esempio seguente presuppone che le macchine virtuali abbiano quattro dischi dati collegati, usati per creare due volumi.

   Elencare tutti i dischi disponibili:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Output di esempio:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Creare i volumi fisici per tutti i dischi da usare:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Creare un gruppo di volumi per i file di dati. Usare un gruppo di volumi per i file di log e uno per la directory condivisa di SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Creare i volumi logici. Quando si usa `lvcreate` senza l'opzione `-i`, viene creato un volume lineare. È consigliabile creare un volume con striping per migliorare le prestazioni di I/O. L'argomento `-i` deve corrispondere al numero del volume fisico sottostante. In questo documento vengono usati due volumi fisici per il volume di dati, quindi l'argomento dell'opzione `-i` è impostato su **2**. Un volume fisico viene usato per il volume di log, quindi non viene usata alcuna opzione `-i` in modo esplicito. Usare l'opzione `-i` e impostarla sul numero del volume fisico sottostante quando si usano più volumi fisici per ogni volume di dati, di log o condiviso.

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Creare le directory di montaggio e copiare l'UUID di tutti i volumi logici:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Creare voci `fstab` per i tre volumi logici:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Inserire la riga seguente nel file `/etc/fstab`:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Montare i nuovi volumi:

   <pre><code>sudo mount -a
   </code></pre>

1. **[T]** Configurare il layout dei dischi: **dischi normali**.

   Per sistemi demo, è possibile inserire i dati e i file di log HANA in un solo disco. Creare una partizione in /dev/disk/azure/scsi1/lun0 e formattarla con XFS:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Inserire questa riga nel file /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Creare la directory di destinazione e montare il disco:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[T]** Configurare la risoluzione dei nomi host per tutti gli host.

   È possibile usare un server DNS o modificare il file /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserire le righe seguenti nel file /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[T]** Installare i pacchetti di disponibilità elevata SAP HANA:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Per installare la replica di sistema SAP HANA, seguire il capitolo 4 della guida [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/) (Scenario di ottimizzazione delle prestazioni della replica di sistema SAP HANA).

1. **[T]** Eseguire il programma **hdblcm** dal DVD di HANA. Immettere i valori seguenti al prompt:
   * Choose installation: immettere **1**.
   * Select additional components for installation: immettere **1**.
   * Enter Installation Path [/hana/shared]: premere INVIO.
   * Enter Local Host Name [..]: premere INVIO.
   * Aggiungere altri host al sistema? (y/n) [n]: premere INVIO.
   * Enter SAP HANA System ID: immettere il SID di HANA, ad esempio: **HN1**.
   * Enter Instance Number [00]: immettere il numero di istanza HANA. Immettere **03** se è stato usato il modello di Azure o se è stata seguita la sezione di questo articolo relativa alla distribuzione manuale.
   * Select Database Mode / Enter Index [1]: premere INVIO.
   * Select System Usage / Enter Index [4]: selezionare il valore di utilizzo di sistema.
   * Enter Location of Data Volumes [/hana/data/HN1]: premere INVIO.
   * Enter Location of Log Volumes [/hana/log/HN1]: premere INVIO.
   * Limitare l'allocazione massima della memoria? [n]: premere INVIO.
   * Enter Certificate Host Name For Host '...' [...]: premere INVIO.
   * Enter SAP Host Agent User (sapadm) Password: immettere la password dell'utente agente host.
   * Confirm SAP Host Agent User (sapadm) Password: immettere di nuovo la password dell'utente agente host per confermare.
   * Enter System Administrator (hdbadm) Password: immettere la password dell'amministratore di sistema.
   * Confirm System Administrator (hdbadm) Password: immettere di nuovo la password dell'amministratore di sistema per confermare.
   * Enter System Administrator Home Directory [/usr/sap/HN1/home]: premere INVIO.
   * Enter System Administrator Login Shell [/bin/sh]: premere INVIO.
   * Enter System Administrator User ID [1001]: premere INVIO.
   * Enter ID of User Group (sapsys) [79]: premere INVIO.
   * Enter Database User (SYSTEM) Password: immettere la password dell'utente del database.
   * Confirm Database User (SYSTEM) Password: immettere di nuovo la password dell'utente del database per confermare.
   * Riavviare il sistema dopo il riavvio della macchina? [n]: premere INVIO.
   * Continuare? (y/n): verificare il riepilogo. Immettere **y** per continuare.

1. **[T]** Aggiornare l'agente host SAP.

   Scaricare l'archivio più recente dell'agente host SAP da [SAP Software Center][sap-swcenter] ed eseguire il comando seguente per aggiornare l'agente. Sostituire il percorso dell'archivio in modo da puntare al file scaricato:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurare la replica di sistema di SAP HANA 2.0

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

* **[T]** : il passaggio si applica a tutti i nodi.
* **[1]** : il passaggio si applica solo al nodo 1.
* **[2]** : Il passaggio si applica solo al nodo 2 del cluster Pacemaker.

1. **[1]** Creare il database tenant.

   Se si usa SAP HANA 2.0 o MDC, creare un database tenant per il sistema SAP NetWeaver. Sostituire **NW1** con il SID del sistema SAP.

   Eseguire il comando seguente come < hanasid\>ADM:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Configurare la replica di sistema nel primo nodo:

   Eseguire il backup dei database come < hanasid\>ADM:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copiare i file PKI di sistema nel sito secondario:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Creare il sito primario:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configurare la replica di sistema nel secondo nodo:
    
   Registrare il secondo nodo per avviare la replica di sistema. Eseguire il comando seguente come < hanasid\>ADM:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurare la replica di sistema di SAP HANA 1.0

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

* **[T]** : il passaggio si applica a tutti i nodi.
* **[1]** : il passaggio si applica solo al nodo 1.
* **[2]** : Il passaggio si applica solo al nodo 2 del cluster Pacemaker.

1. **[1]** Creare gli utenti richiesti.

   Eseguire il comando seguente come radice. Assicurarsi di sostituire le stringhe in grassetto (ID di sistema HANA **HN1** e numero di istanza **03**) con i valori dell'installazione di SAP HANA in uso:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[T]** Creare una voce di archivio chiavi.

   Eseguire il comando seguente come root per creare una nuova voce dell'archivio chiavi:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Eseguire il backup del database.

   Eseguire il backup dei database come radice:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se si usa un'installazione multi-tenant, eseguire anche il backup del database tenant:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configurare la replica di sistema nel primo nodo.

   Creare il sito primario come < hanasid\>ADM:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configurare la replica di sistema nel nodo secondario.

   Registrare il sito secondario come < hanasid\>ADM:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Creare le risorse cluster SAP HANA

Prima di tutto, creare la topologia HANA. Eseguire i comandi seguenti in uno dei nodi del cluster Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Creare quindi le risorse HANA:

> [!IMPORTANT]
> Sono state rilevate situazioni di test recenti, in cui netcat smette di rispondere alle richieste dovute al backlog e alla limitazione della gestione di una sola connessione. La risorsa netcat smette di restare in ascolto delle richieste del servizio di bilanciamento del carico di Azure e l'IP mobile diventa non disponibile.  
> Per i cluster Pacemaker esistenti, è consigliabile sostituire Netcat con socat, seguendo le istruzioni riportate in [protezione avanzata del rilevamento](https://www.suse.com/support/kb/doc/?id=7024128)del servizio di bilanciamento del carico di Azure. Si noti che la modifica richiederà un breve tempo di inattività.  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

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
  params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:625<b>03</b>,backlog=10,fork,reuseaddr /dev/null" \
  op monitor timeout=20s interval=10 depth=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

<pre><code>sudo crm_mon -r

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

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come testare la configurazione. Ogni test presuppone che si usi l'account utente ROOT e che il master SAP HANA sia in esecuzione nella macchina virtuale **hn1-db-0**.

### <a name="test-the-migration"></a>Testare la migrazione

Prima di iniziare il test, assicurarsi che in Pacemaker non vi siano azioni non riuscite (tramite crm_mon - r), non siano presenti vincoli di posizione imprevisti (ad esempio rimasti da un test di migrazione precedente) e che HANA si trovi nello stato di sincronizzazione, ad esempio con SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

È possibile eseguire la migrazione del nodo master SAP HANA eseguendo il comando seguente:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se si imposta `AUTOMATED_REGISTER="false"`, questa sequenza di comandi esegue la migrazione del nodo master SAP HANA e del gruppo contenente l'indirizzo IP virtuale a hn1-db-1.

Al termine della migrazione, l'output di crm_mon -r è simile al seguente

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

La risorsa SAP HANA in hn1-db-0 non viene avviata come secondaria. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

La migrazione crea vincoli di posizione che devono essere eliminati di nuovo:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

È anche necessario eseguire la pulizia dello stato della risorsa nodo secondario:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitorare lo stato della risorsa HANA usando crm_mon -r. Dopo l'avvio di HANA in hn1-db-0, l'output dovrebbe essere simile al seguente

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testare l'agente di isolamento di Azure (non SBD)

È possibile testare la configurazione dell'agente di isolamento di Azure disabilitando l'interfaccia di rete nel nodo hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

La macchina virtuale dovrebbe ora venire riavviata o arrestata, a seconda della configurazione del cluster.
Se si imposta `stonith-action` su off, la macchina virtuale viene arrestata e viene eseguita la migrazione delle risorse alla macchina virtuale in esecuzione.

Una volta riavviata la macchina virtuale, la risorsa SAP HANA non viene avviata come secondaria se si imposta `AUTOMATED_REGISTER="false"`. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testare l'isolamento SBD

È possibile testare la configurazione di SBD terminando il processo inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Il nodo del cluster hn1-db-0 deve essere riavviato. In seguito, il servizio Pacemaker potrebbe non venire avviato. Assicurarsi di riavviarlo.

### <a name="test-a-manual-failover"></a>Testare un failover manuale

È possibile testare un failover manuale arrestando il servizio `pacemaker` nel nodo hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Dopo il failover, è possibile avviare nuovamente il servizio. Se si imposta `AUTOMATED_REGISTER="false"`, la risorsa SAP HANA nel nodo hn1-db-0 non viene avviata come secondaria. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Test SUSE

> [!IMPORTANT]
> Assicurarsi che il sistema operativo selezionato sia dotato di certificazione SAP per SAP HANA sugli specifici tipi di macchina virtuale in uso. Per un elenco dei tipi di macchina virtuale certificati per SAP HANA e delle versioni di sistema operativo correlate, vedere l'elenco delle [piattaforme IaaS certificate per SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Fare clic su ogni voce presente nell'elenco dei tipi di macchina virtuale per ottenere l'elenco completo delle versioni di sistema operativo supportate da SAP HANA per lo specifico tipo di VM.

Eseguire tutti i test case elencati nella guida allo scenario di ottimizzazione delle prestazioni della replica di sistema SAP HANA o dello scenario di ottimizzazione dei costi della replica di sistema SAP HANA, a seconda del caso d'uso. È possibile trovare le guide nella pagina relativa alle [procedure consigliate per SLES per SAP][sles-for-sap-bp].

I test seguenti sono una copia delle descrizioni dei test della guida di SUSE Linux Enterprise Server for SAP Applications 12 SP1 per lo scenario di ottimizzazione delle prestazioni della replica di sistema SAP HANA. Per una versione aggiornata, fare sempre riferimento alla guida. Assicurarsi sempre che HANA sia sincronizzato prima di avviare il test e che la configurazione di Pacemaker sia corretta.

Nelle descrizioni dei test seguenti si presuppone che PREFER_SITE_TAKEOVER="true" e AUTOMATED_REGISTER="false".
NOTA: i test seguenti sono progettati per essere eseguiti in sequenza e dipendono dal risultato dei test precedenti.

1. TEST 1: ARRESTARE IL DATABASE PRIMARIO NEL NODO 1

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Eseguire i comandi seguenti come < hanasid\>ADM sul nodo HN1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker rileva l'istanza HANA arrestata ed esegue il failover nell'altro nodo. Una volta completato il failover, l'istanza HANA nel nodo hn1-db-0 viene arrestata perché Pacemaker non registra automaticamente il nodo come nodo secondario HANA.

   Eseguire i comandi seguenti per registrare il nodo hn1-db-0 come secondario e pulire la risorsa per la quale si è verificato un errore.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 2: ARRESTARE IL DATABASE PRIMARIO NEL NODO 2

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Eseguire i comandi seguenti come < hanasid\>ADM sul nodo HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker rileva l'istanza HANA arrestata ed esegue il failover nell'altro nodo. Una volta completato il failover, l'istanza HANA nel nodo hn1-db-1 viene arrestata perché Pacemaker non registra automaticamente il nodo come nodo secondario HANA.

   Eseguire i comandi seguenti per registrare il nodo hn1-db-1 come secondario e pulire la risorsa per la quale si è verificato un errore.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 3: PROVOCARE UN ARRESTO ANOMALO DEL DATABASE PRIMARIO NEL NODO

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Eseguire i comandi seguenti come < hanasid\>ADM sul nodo HN1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker rileva l'istanza HANA terminata ed esegue il failover nell'altro nodo. Una volta completato il failover, l'istanza HANA nel nodo hn1-db-0 viene arrestata perché Pacemaker non registra automaticamente il nodo come nodo secondario HANA.

   Eseguire i comandi seguenti per registrare il nodo hn1-db-0 come secondario e pulire la risorsa per la quale si è verificato un errore.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 4: PROVOCARE UN ARRESTO ANOMALO DEL DATABASE PRIMARIO NEL NODO 2

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Eseguire i comandi seguenti come < hanasid\>ADM sul nodo HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker rileva l'istanza HANA terminata ed esegue il failover nell'altro nodo. Una volta completato il failover, l'istanza HANA nel nodo hn1-db-1 viene arrestata perché Pacemaker non registra automaticamente il nodo come nodo secondario HANA.

   Eseguire i comandi seguenti per registrare il nodo hn1-db-1 come secondario e pulire la risorsa per la quale si è verificato un errore.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 5: PROVOCARE UN ARRESTO ANOMALO DEL NODO DEL SITO PRIMARIO (NODO 1)

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Eseguire i comandi seguenti come utente ROOT nel nodo hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker rileva il nodo del cluster terminato e isola il nodo. Una volta isolato il nodo, Pacemaker attiva un'operazione di acquisizione dell'istanza HANA. Quando il nodo isolato viene riavviato, Pacemaker non si avvia automaticamente.

   Eseguire i comandi seguenti per avviare Pacemaker, pulire i messaggi SBD per il nodo hn1-db-0, registrare il nodo hn1-db-0 come secondario e pulire la risorsa per la quale si è verificato un errore.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

1. TEST 6: PROVOCARE UN ARRESTO ANOMALO DEL NODO DEL SITO SECONDARIO (NODO 2)

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-1
   </code></pre>

   Eseguire i comandi seguenti come utente ROOT nel nodo hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker rileva il nodo del cluster terminato e isola il nodo. Una volta isolato il nodo, Pacemaker attiva un'operazione di acquisizione dell'istanza HANA. Quando il nodo isolato viene riavviato, Pacemaker non si avvia automaticamente.

   Eseguire i comandi seguenti per avviare Pacemaker, pulire i messaggi SBD per il nodo hn1-db-1, registrare il nodo hn1-db-1 come secondario e pulire la risorsa per la quale si è verificato un errore.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 7: ARRESTARE IL DATABASE SECONDARIO NEL NODO 2

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Eseguire i comandi seguenti come < hanasid\>ADM sul nodo HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker rileva l'istanza HANA arrestata e contrassegna la risorsa come in errore nel nodo hn1-db-1. Pacemaker dovrebbe riavviare automaticamente l'istanza HANA. Eseguire il comando seguente per pulire lo stato di errore.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 8: PROVOCARE UN ARRESTO ANOMALO DEL DATABASE SECONDARIO NEL NODO 2

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Eseguire i comandi seguenti come < hanasid\>ADM sul nodo HN1-DB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker rileva l'istanza HANA terminata e contrassegna la risorsa come in errore nel nodo hn1-db-1. Eseguire il comando seguente per pulire lo stato di errore. Pacemaker dovrebbe quindi riavviare automaticamente l'istanza HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

1. TEST 9: PROVOCARE UN ARRESTO ANOMALO DEL NODO DEL SITO SECONDARIO (NODO 2) CHE ESEGUE IL DATABASE HANA SECONDARIO

   Stato delle risorse prima dell'avvio del test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

   Eseguire i comandi seguenti come utente ROOT nel nodo hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker rileva il nodo del cluster terminato e isola il nodo. Quando il nodo isolato viene riavviato, Pacemaker non si avvia automaticamente.

   Eseguire i comandi seguenti per avviare Pacemaker, pulire i messaggi SBD per il nodo hn1-db-1 e pulire la risorsa per la quale si è verificato un errore.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come configurare la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze Large), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md)
