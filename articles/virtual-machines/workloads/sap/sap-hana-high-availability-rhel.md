---
title: Configurare la replica di sistema SAP HANA in Macchine virtuali di Azure (VM) | Microsoft Docs
description: Configurare la disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 62bb00c05359682503d2e99ef282f2523871147d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721532"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Disponibilità elevata di SAP HANA in macchine virtuali di Azure su Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]: https://launchpad.support.sap.com/#/notes/2292690
[2455582]: https://launchpad.support.sap.com/#/notes/2455582
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Per lo sviluppo locale, è possibile usare la replica di sistema HANA oppure l'archiviazione condivisa per fornire la disponibilità elevata per SAP HANA.
In Macchine virtuali di Azure la replica di sistema HANA in Azure è attualmente l'unica funzione per la disponibilità elevata supportata.
La replica SAP HANA è costituita da un nodo primario e da almeno un nodo secondario. Le modifiche ai dati nel nodo primario vengono replicate nel nodo secondario in modo sincrono o asincrono.

Questo articolo descrive come distribuire e configurare le macchine virtuali, installare il framework del cluster, nonché installare e configurare la replica di sistema SAP HANA.
Nelle configurazioni di esempio e nei comandi di installazione vengono usati il numero di istanza **03** e l'ID di sistema HANA **HN1**.

Leggere prima di tutto i documenti e le note SAP seguenti:

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione di software SAP.
  * Informazioni importanti sulla capacità per le dimensioni delle macchine virtuali di Azure.
  * Software SAP e combinazioni di sistemi operativi e database supportati.
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure.
* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2002167] indica le impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux
* La nota SAP [2009879] contiene le linee guida di SAP HANA per Red Hat Enterprise Linux
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP in Linux (questo articolo)][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101) (Replica di sistema SAP HANA nel cluster Pacemaker)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
* Documentazione di RHEL specifica per Azure:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341) (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782) (Installare SAP HANA su Red Hat Enterprise Linux per l'uso in Microsoft Azure)

## <a name="overview"></a>Panoramica

Per ottenere disponibilità elevata, il sistema SAP HANA viene installato in due macchine virtuali. I dati vengono replicati tramite la replica di sistema HANA.

![Panoramica della disponibilità elevata SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

La procedura di configurazione della replica di sistema SAP HANA usa un nome host virtuale dedicato e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico:

* Configurazione front-end: indirizzo IP 10.0.0.13 per hn1-db
* Configurazione back-end: connessione a interfacce di rete primarie di tutte le macchine virtuali che devono far parte della replica di sistema HANA
* Porta probe: porta 62503
* Regole di bilanciamento del carico: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Eseguire la distribuzione per Linux

Azure Marketplace contiene un'immagine per Red Hat Enterprise Linux 7.4 per SAP HANA che è possibile usare per distribuire nuove macchine virtuali.

### <a name="deploy-with-a-template"></a>Eseguire la distribuzione con un modello

È possibile usare uno dei modelli di avvio rapido di GitHub per distribuire tutte le risorse necessarie. Il modello consente di distribuire le macchine virtuali, il servizio di bilanciamento del carico, il set di disponibilità e così via.
Per distribuire il modello, seguire questi passaggi:

1. Aprire il [modello di database][template-multisid-db] nel portale di Azure.
1. Immettere i parametri seguenti:
    * **Sap System Id** (ID sistema SAP): immettere l'ID del sistema SAP da installare. L'ID viene usato come prefisso per le risorse distribuite.
    * **Tipo di sistema operativo**: selezionare una delle distribuzioni Linux. Per questo esempio, selezionare **RHEL 7**.
    * **Tipo di database**: selezionare **HANA**.
    * **Sap System Size** (Dimensioni sistema SAP): immettere il numero di istanze SAP fornite dal nuovo sistema. Se non si è certi del numero di istanze SAP necessarie per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
    * **System Availability**: selezionare **HA**.
    * **Nome utente amministratore, password amministratore o chiave SSH**: viene creato un nuovo utente che può essere usato per accedere al computer.
    * **ID subnet**: se si desidera implementare la macchina virtuale in una rete virtuale esistente per cui è stata definita la subnet a cui assegnare la macchina virtuale, denominare l'ID di tale subnet. L'ID in genere ha il formato **/subscriptions/\<ID sottoscrizione>/resourceGroups/\<nome gruppo di risorse>/providers/Microsoft.Network/virtualNetworks/\<nome rete virtuale>/subnets/\<nome subnet>** . Lasciare vuoto se si vuole creare una nuova rete virtuale

### <a name="manual-deployment"></a>Distribuzione manuale

1. Creare un gruppo di risorse.
1. Creare una rete virtuale.
1. Creare un set di disponibilità.  
   Impostare il numero massimo di domini di aggiornamento.
1. Creare un servizio di bilanciamento del carico (interno). Si consiglia [Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   * Selezionare la rete virtuale creata nel passaggio 2.
1. Creare la macchina virtuale 1.  
   Usare almeno Red Hat Enterprise Linux 7.4 per SAP HANA. In questo esempio viene usata l'immagine di Red Hat Enterprise Linux 7.4 per SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Selezionare il set di disponibilità creato al passaggio 3.
1. Creare la macchina virtuale 2.  
   Usare almeno Red Hat Enterprise Linux 7.4 per SAP HANA. In questo esempio viene usata l'immagine di Red Hat Enterprise Linux 7.4 per SAP HANA <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Selezionare il set di disponibilità creato al passaggio 3.
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
   1. Configurare il servizio di bilanciamento del carico. Prima, creare il pool di indirizzi IP front-end:

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
> Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net. IPv4. TCP _timestamps** su **0**. Per informazioni dettagliate, vedere [Load Balancer Probe di integrità](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Vedere anche la nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Installare SAP HANA

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

* **[T]** : il passaggio si applica a tutti i nodi.
* **[1]** : il passaggio si applica solo al nodo 1.
* **[2]** : Il passaggio si applica solo al nodo 2 del cluster Pacemaker.

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

1. **[T]** Configurazione di RHEL per HANA

   Configurare RHEL come descritto nelle note SAP [2292690] e [2455582] e in <https://access.redhat.com/solutions/2447641>.

1. **[T]** Installare SAP HANA

   Per installare SAP HANA System Replication, vedere <https://access.redhat.com/articles/3004101>.

   * Eseguire il programma **hdblcm** dal DVD di HANA. Immettere i valori seguenti al prompt:
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

1. **[T]** Configurare il firewall

   Creare la regola del firewall per la porta probe del servizio di bilanciamento del carico di Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Configurare la replica di sistema di SAP HANA 2.0

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

* **[T]** : il passaggio si applica a tutti i nodi.
* **[1]** : il passaggio si applica solo al nodo 1.
* **[2]** : Il passaggio si applica solo al nodo 2 del cluster Pacemaker.

1. **[T]** Configurare il firewall

   Creare regole del firewall per consentire il traffico di HANA System Replication e del client. Le porte necessarie sono elencate in [TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports) (Porte TCP/IP per tutti i prodotti SAP). I comandi seguenti sono solo un esempio per consentire il traffico di HANA 2.0 System Replication e del client al database SYSTEMDB, HN1 e NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Creare il database tenant.

   Se si usa SAP HANA 2.0 o MDC, creare un database tenant per il sistema SAP NetWeaver. Sostituire **NW1** con il SID del sistema SAP.

   Eseguire come < hanasid\>ADM il seguente comando:

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

1. **[1]** Verificare lo stato della replica

   Verificare lo stato della replica e attendere che tutti i database siano sincronizzati. Se lo stato rimane sconosciuto, controllare le impostazioni del firewall.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurare la replica di sistema di SAP HANA 1.0

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

* **[T]** : il passaggio si applica a tutti i nodi.
* **[1]** : il passaggio si applica solo al nodo 1.
* **[2]** : Il passaggio si applica solo al nodo 2 del cluster Pacemaker.

1. **[T]** Configurare il firewall

   Creare regole del firewall per consentire il traffico di HANA System Replication e del client. Le porte necessarie sono elencate in [TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports) (Porte TCP/IP per tutti i prodotti SAP). I comandi seguenti sono solo un esempio per consentire HANA 2.0 System Replication. Adattarli alla propria installazione di SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi della procedura di [configurazione di Pacemaker in Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) per creare un cluster Pacemaker di base per questo server HANA.

## <a name="create-sap-hana-cluster-resources"></a>Creare le risorse cluster SAP HANA

Installare gli agenti di risorse SAP HANA in **tutti i nodi**. Assicurarsi di abilitare un repository contenente il pacchetto.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Creare quindi la topologia HANA. Eseguire i comandi seguenti in uno dei nodi del cluster Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Creare quindi le risorse HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come testare la configurazione. Prima di iniziare un test, assicurarsi che in Pacemaker non vi siano azioni non riuscite (tramite pcs status), non siano presenti vincoli di posizione imprevisti (ad esempio rimasti da un test di migrazione precedente) e che HANA si trovi nello stato di sincronizzazione, ad esempio con systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testare la migrazione

Stato delle risorse prima dell'avvio del test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

È possibile eseguire la migrazione del nodo master SAP HANA eseguendo il comando seguente:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Se si imposta `AUTOMATED_REGISTER="false"`, questo comando esegue la migrazione del nodo master SAP HANA e del gruppo contenente l'indirizzo IP virtuale a hn1-db-1.

Al termine della migrazione, l'output di sudo pcs status è simile al seguente

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

La risorsa SAP HANA in hn1-db-0 viene arrestata. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

La migrazione crea vincoli di posizione che devono essere eliminati di nuovo:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitorare lo stato della risorsa HANA usando pcs status. Dopo l'avvio di HANA in hn1-db-0, l'output dovrebbe essere simile al seguente

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testare l'agente di isolamento di Azure

Stato delle risorse prima dell'avvio del test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

È possibile testare la configurazione dell'agente di isolamento di Azure disabilitando l'interfaccia di rete nel nodo in cui SAP HANA viene eseguito come master.
Per una descrizione su come simulare un errore di rete, vedere l' [articolo della Knowledge base 79523 di Red Hat](https://access.redhat.com/solutions/79523) . In questo esempio viene usato lo script net_breaker per bloccare tutti gli accessi alla rete.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

La macchina virtuale dovrebbe ora venire riavviata o arrestata, a seconda della configurazione del cluster.
Se si imposta `stonith-action` su off, la macchina virtuale viene arrestata e viene eseguita la migrazione delle risorse alla macchina virtuale in esecuzione.

> [!NOTE]
> Prima che le macchine virtuali siano di nuovo online possono trascorrere fino a 15 minuti.

Una volta riavviata la macchina virtuale, la risorsa SAP HANA non viene avviata come secondaria se si imposta `AUTOMATED_REGISTER="false"`. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stato delle risorse dopo il test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testare un failover manuale

Stato delle risorse prima dell'avvio del test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

È possibile testare un failover manuale arrestando il cluster nel nodo hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Dopo il failover, è possibile avviare di nuovo il cluster. Se si imposta `AUTOMATED_REGISTER="false"`, la risorsa SAP HANA nel nodo hn1-db-0 non viene avviata come secondaria. In questo caso, configurare l'istanza di HANA come secondaria eseguendo questo comando:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stato delle risorse dopo il test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come configurare la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze Large), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md)
