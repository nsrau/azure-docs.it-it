---
title: "Disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM) | Microsoft Docs"
description: "Configurare la disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM)."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 258ccee349e07448ebebaebe64cd6fb6888d7ed4
ms.contentlocale: it-it
ms.lasthandoff: 05/20/2017


---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

In locale è possibile usare la replica di sistema HANA oppure l'archiviazione condivisa per stabilire la disponibilità elevata per SAP HANA.
Attualmente è supportata solo l'impostazione della replica di sistema HANA in Azure. La replica SAP HANA è costituita da un nodo master e almeno un nodo slave. Le modifiche ai dati nel nodo master vengono replicate nei nodi slave in modo sincrono o asincrono.

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare e configurare la replica di sistema SAP HANA.
Nelle configurazioni di esempio, nei comandi di installazione e così via vengono usati il numero di istanza 03 e l'ID di sistema HANA HDB.

Leggere prima di tutto le note e i documenti seguenti relativi a SAP

* Nota su SAP [2205917] Impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications
* Nota su SAP [1944799] Linee guida su SAP HANA per SUSE Linux Enterprise Server for SAP Applications
* [SAP HANA SR Performance Optimized Scenario][suse-hana-ha-guide] (Scenario di ottimizzazione delle prestazioni di SAP HANA SR)  
  La guida contiene tutte le informazioni necessarie per configurare la replica di sistema SAP HANA in locale. Usare la guida per le indicazioni di base.
## <a name="deploying-linux"></a>Distribuzione di Linux

L'agente delle risorse per SAP HANA è incluso in SUSE Linux Enterprise Server for SAP Applications.
Azure Marketplace contiene un'immagine per SUSE Linux Enterprise Server for SAP Applications 12 con BYOS (Bring Your Own Subscription) che è possibile usare per distribuire nuove macchine virtuali.

### <a name="manual-deployment"></a>Distribuzione manuale

1. Creare un gruppo di risorse
1. Creare una rete virtuale
1. Creare due account di archiviazione
1. Creare un set di disponibilità  
   Impostare il numero massimo di domini di aggiornamento
1. Creare un servizio di bilanciamento del carico (interno)  
   Selezionare la rete virtuale del passaggio precedente
1. Creare la macchina virtuale 1  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   Selezionare l'account di archiviazione 1  
   Selezionare il set di disponibilità  
1. Creare la macchina virtuale 2  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   Selezionare l'account di archiviazione 2   
   Selezionare il set di disponibilità  
1. Aggiungere dischi dati
1. Configurare il servizio di bilanciamento del carico
    1. Creare un pool di indirizzi IP front-end
        1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
        1. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, hana-frontend)
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
    1. Creare le regole di bilanciamento del carico
        1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
        1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**15)
        1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe integrità creati in precedenza (ad esempio, hana-frontend)
        1. Mantenere il protocollo TCP, immettere la porta 3**03**15
        1. Aumentare il timeout di inattività a 30 minuti
       1. **Assicurarsi di abilitare l'indirizzo IP mobile**
        1. Fare clic su OK.
        1. Ripetere i passaggi precedenti per la porta 3**03**17

### <a name="deploy-with-template"></a>Eseguire la distribuzione con un modello
È possibile usare uno dei modelli di avvio rapido di GitHub per distribuire tutte le risorse necessarie. Il modello consente di distribuire le macchine virtuali, il servizio di bilanciamento del carico, il set di disponibilità e così via. Per distribuire il modello, seguire questi passaggi:

1. Aprire il [modello di database][template-multisid-db] o il [modello con convergenza][template-converged] nel portale di Azure. Il modello di database consente di creare solo le regole di bilanciamento del carico per un database, mentre il modello con convergenza crea anche le regole di bilanciamento del carico per un'istanza di ASCS/SCS ed ERS (solo Linux). Se si prevede di installare un sistema basato su SAP NetWeaver e si vuole installare anche l'istanza di ASCS/SCS nelle stesse macchine, usare il [modello con convergenza][template-converged].
1. Immettere i parametri seguenti
    1. ID sistema SAP  
       Immettere l'ID del sistema SAP che si vuole installare. L'ID verrà usato come prefisso per le risorse distribuite.
    1. Tipo di stack (applicabile solo se si usa il modello con convergenza)  
       Selezionare il tipo di stack SAP NetWeaver
    1. Tipo di sistema operativo  
       Selezionare una delle distribuzioni Linux. Per questo esempio, selezionare SLES 12 BYOS
    1. Tipo di database  
       Selezionare HANA
    1. Dimensioni del sistema SAP  
       Quantità di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP
    1. Disponibilità del sistema  
       Selezionare la disponibilità elevata.
    1. Nome utente e password amministratore  
       Verrà creato un nuovo utente con cui è possibile accedere alla macchina
    1. Subnet nuova o esistente  
       Determina se devono essere create una nuova rete virtuale e una nuova subnet o deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare "existing".
    1. ID subnet  
    ID della subnet a cui devono essere connesse le macchine virtuali. Selezionare la subnet della rete virtuale Express Route o VPN per connettere la macchina virtuale alla rete locale. L'ID si presenta in genere come segue: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>Configurazione della disponibilità elevata di Linux

Gli elementi seguenti sono preceduti dall'indicazione [A] - applicabile a tutti i nodi, [1] - applicabile solo al nodo 1 o [2] - applicabile solo al nodo 2.

1. [A] Solo SLES per SAP BYOS - Registrare SLES per poter usare i repository
1. [A] Solo SLES per SAP BYOS - Aggiungere il modulo cloud pubblico
1. [A] Aggiornare SLES
    ```bash
    sudo zypper update

    ```

1. [1] Abilitare l'accesso SSH
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] Abilitare l'accesso SSH
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] Abilitare l'accesso SSH
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] Installare l'estensione per la disponibilità elevata
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] Configurare il layout dei dischi
    1. LVM  
    In genere è consigliabile usare LVM per i volumi che archiviano i dati e file di log. L'esempio seguente presuppone che le macchine virtuali abbiano quattro dischi dati collegati che devono essere usati per creare due volumi.
        * Creare i volumi fisici per tutti i dischi da usare.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Creare un gruppo di volumi per i file di dati, un gruppo di volumi per i file di log e uno per la directory condivisa di SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Creare i volumi logici
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Creare le directory di montaggio e copiare l'UUID di tutti i volumi logici
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Creare voci fstab per i tre volumi logici
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Inserire questa riga in /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Montare i nuovi volumi
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Dischi normali  
       Per sistemi demo o di piccole dimensioni, è possibile inserire i dati e i file di log HANA su un disco. I comandi seguenti creano una partizione in /dev/sdc e la formattano con XFS.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>Annotare l'ID di /dev/sdc1
    sudo /sbin/blkid sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] Configurare la risoluzione dei nomi host per tutti gli host  
    È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti
    ```bash
    sudo vi /etc/hosts
    ```
    Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] Installare il cluster
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Aggiungere un nodo al cluster
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] Modificare la password hacluster in modo in modo da usare la stessa password
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] Configurare corosync per usare un altro trasporto e aggiungere nodelist. In caso contrario, il cluster non funzionerà.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Aggiungere il seguente contenuto in grassetto nel file.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of note 1 >
      }
      node {
        ring0_addr:     < ip address of note 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Riavviare quindi il servizio corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] Installare pacchetti HANA a disponibilità elevata  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Installazione di SAP HANA

Seguire il capitolo 4 della guida [SAP HANA SR Performance Optimized Scenario][suse-hana-ha-guide] (Scenario di ottimizzazione delle prestazioni di SAP HANA SR) per installare la replica di sistema di SAP HANA.

1. [A] Eseguire hdblcm dal DVD di HANA
    * Scegliere l'installazione -> 1
    * Selezionare i componenti aggiuntivi per l'installazione -> 1
    * Immettere il percorso di installazione [/hana/shared]: -> INVIO
    * Immettere il nome host locale [..]: -> INVIO
    * Aggiungere altri host al sistema? (y/n) [n]: -> INVIO
    * Immettere l'ID di sistema SAP HANA: <SID of HANA e.g. HDB>
    * Immettere il numero di istanza [00]:   
  Numero di istanza di HANA. Usare 03 se è stato usato il modello di Azure o è stato seguito l'esempio precedente
    * Selezionare la modalità di database/immettere l'indice [1]: -> INVIO
    * Selezionare l'utilizzo del sistema/immettere l'indice [4]:  
  Selezionare l'utilizzo del sistema
    * Immettere il percorso dei volumi di dati [/hana/data/HDB]: -> INVIO
    * Immettere il percorso dei volumi di log [/hana/log/HDB]: -> INVIO
    * Limitare l'allocazione massima della memoria? [n]: -> INVIO
    * Immettere il nome host del certificato per l'host '...' [...]: -> INVIO
    * Immettere la password dell'utente agente host SAP (sapadm):
    * Confermare la password dell'utente agente host SAP (sapadm):
    * Immettere la password dell'amministratore di sistema (hdbadm):
    * Confermare la password dell'amministratore di sistema (hdbadm):
    * Immettere la home directory dell'amministratore di sistema [/usr/sap/HDB/home]: -> INVIO
    * Immettere la shell di accesso dell'amministratore di sistema [/bin/sh]: -> INVIO
    * Immettere l'ID utente dell'amministratore di sistema [1001]: -> INVIO
    * Immettere l'ID del gruppo di utenti (sapsys) [79]: -> INVIO
    * Immettere la password dell'utente del database (SYSTEM):
    * Confermare la password dell'utente del database (SYSTEM):
    * Riavviare il sistema dopo il riavvio della macchina? [n]: -> INVIO
    * Continuare? (y/n):  
  Convalidare il riepilogo e immettere y per continuare
1. [A] Aggiornare l'agente host SAP  
  Scaricare l'archivio dell'agente host SAP più recente dal sito [SAP Softwarecenter] [sap-swcenter] ed eseguire il comando seguente per aggiornare l'agente. Sostituire il percorso dell'archivio in modo da puntare al file scaricato.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] Creare la replica HANA (come radice)  
    Eseguire il comando indicato di seguito. Assicurarsi di sostituire le stringhe in grassetto (ID di sistema HANA HDB e numero di istanza 03) con i valori dell'installazione di SAP HANA.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] Creare una voce di archivio chiavi (come radice)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] Eseguire il backup del database (come radice)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] Passare all'utente sapsid (ad esempio hdbadm) e creare il sito primario.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] Passare all'utente sapsid (ad esempio hdbadm) e creare il sito secondario.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Configurare il framework del cluster

Modificare le impostazioni predefinite

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-saphana.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Caricare ora il file nel cluster
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Creare il dispositivo STONITH

Il dispositivo STONITH usa un'entità servizio per l'autorizzazione in Microsoft Azure. Per creare un'entità servizio, seguire questa procedura.

1. Passare a <https://portal.azure.com>
1. Aprire il pannello Azure Active Directory  
   Passare a Proprietà e annotare l'ID directory. Si tratta dell'**ID tenant**.
1. Fare clic su Registrazioni per l'app
1. Fare clic su Aggiungi.
1. Immettere un nome, selezionare il tipo di applicazione "App Web/API", immettere un URL di accesso (ad esempio http://localhost) e fare clic su Crea
1. L'URL di accesso non viene usato e può essere qualsiasi URL valido
1. Selezionare la nuova app e fare clic su Chiavi nella scheda Impostazioni
1. Immettere una descrizione per una nuova chiave, selezionare "Non scade mai" e fare clic su Salva
1. Annotare il valore. Viene usato come **password** per l'entità servizio
1. Annotare l'ID applicazione. Viene usato come nome utente (**ID di accesso** nella procedura seguente) dell'entità servizio

L'entità servizio non ha le autorizzazioni per accedere alle risorse di Azure per impostazione predefinita. È necessario concedere all'entità servizio le autorizzazioni per avviare e arrestare (deallocare) tutte le macchine virtuali del cluster.

1. Passare a https://portal.azure.com
1. Aprire il pannello Tutte le risorse
1. Selezionare la macchina virtuale
1. Fare clic su Controllo di accesso (IAM)
1. Fare clic su Aggiungi.
1. Selezionare il ruolo Proprietario
1. Immettere il nome dell'applicazione creata in precedenza
1. Fare clic su OK.

Dopo aver modificato le autorizzazioni per le macchine virtuali, è possibile configurare i dispositivi STONITH nel cluster.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Caricare ora il file nel cluster
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Creare risorse SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Caricare ora il file nel cluster
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Caricare ora il file nel cluster
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Testare la configurazione del cluster
Il capitolo seguente descrive come testare la configurazione. Ogni test presuppone che ci si trovi nella radice e che il master SAP HANA sia in esecuzione nella macchina virtuale saphanavm1.

#### <a name="fencing-test"></a>Test di isolamento

È possibile testare la configurazione dell'agente di isolamento disabilitando l'interfaccia di rete nel nodo saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

La macchina virtuale dovrebbe ora venire riavviata o arrestata, a seconda della configurazione del cluster.
Se si imposta stonith-action su off, la macchina virtuale verrà arrestata e verrà eseguita la migrazione delle risorse alla macchina virtuale in esecuzione.

Una volta riavviata la macchina virtuale, la risorsa SAP HANA non verrà avviata come secondaria se si imposta AUTOMATED_REGISTER="false". In questo caso, è necessario configurare l'istanza di HANA come secondaria eseguendo il comando seguente:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Test di un failover manuale

È possibile testare un failover manuale arrestando il servizio pacemaker nel nodo saphanavm1.
<pre><code>
service pacemaker stop
</code></pre>

Dopo il failover, è possibile avviare nuovamente il servizio. La risorsa SAP HANA in saphanavm1 non verrà avviata come secondaria se si imposta AUTOMATED_REGISTER="false". In questo caso, è necessario configurare l'istanza di HANA come secondaria eseguendo il comando seguente:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Test di una migrazione

È possibile eseguire la migrazione del nodo master SAP HANA eseguendo il comando seguente
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

In questo modo, dovrebbe venire eseguita la migrazione del nodo master SAP HANA e del gruppo che contiene l'indirizzo IP virtuale di saphanavm2.
La risorsa SAP HANA in saphanavm1 non verrà avviata come secondaria se si imposta AUTOMATED_REGISTER="false". In questo caso, è necessario configurare l'istanza di HANA come secondaria eseguendo il comando seguente:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

La migrazione crea vincoli di posizione che devono essere eliminati di nuovo.

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

È anche necessario eseguire la pulizia di stato della risorsa nodo secondario

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md). 

