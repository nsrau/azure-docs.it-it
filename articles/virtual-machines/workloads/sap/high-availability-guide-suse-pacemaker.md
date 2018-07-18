---
title: Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure | Microsoft Docs
description: Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: sedusch
ms.openlocfilehash: ba44a8988c4af68abf4d155a2b9cb490b6122d39
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656415"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md

Per configurare un cluster Pacemaker in Azure sono disponibili due opzioni. È possibile usare un agente di isolamento, che si occupa di riavviare un nodo bloccato tramite le API di Azure, oppure un dispositivo SBD.

Per il dispositivo SBD è necessaria una macchina virtuale supplementare che possa fungere da server di destinazione iSCSI e fornire un dispositivo SBD. Il server di destinazione iSCSI può tuttavia essere condiviso con altri cluster Pacemaker. L'uso di un dispositivo SBD consente di ridurre il tempo di failover e, se si usano dispositivi SBD a livello locale, non richiede di apportare alcuna modifica alla modalità di gestione del cluster Pacemaker. Per l'isolamento tramite SBD è comunque possibile usare l'agente di isolamento di Azure come meccanismo di backup nel caso in cui il server di destinazione iSCSI non sia disponibile.

Se non si vogliono sostenere i costi di una macchina virtuale supplementare, è anche possibile usare l'agente di isolamento di Azure. Lo svantaggio è che un failover può richiedere dai 10 ai 15 minuti se si verifica un errore durante l'arresto di una risorsa o se i nodi del cluster non riescono più a comunicare tra loro.

![Panoramica di Pacemaker su SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

## <a name="sbd-fencing"></a>Isolamento tramite SBD

Seguire questa procedura se si vuole usare un dispositivo SBD per l'isolamento.

### <a name="set-up-an-iscsi-target-server"></a>Configurare un server di destinazione iSCSI

Se non è già presente, è prima necessario creare una macchina virtuale di destinazione iSCSI. I server di destinazione iSCSI possono essere condivisi con più cluster Pacemaker.

1. Distribuire una nuova macchina virtuale SLES 12 SP1 o versione successiva e connettersi alla macchina tramite ssh. La macchina non dovrà essere di grandi dimensioni. È sufficiente una macchina virtuale di dimensioni Standard_E2s_v3 o Standard_D2s_v3.

1. Aggiornare SLES.

   <pre><code>
   sudo zypper update
   </code></pre>

1. Rimuovere i pacchetti.

   Per evitare un problema noto con targetcli e SLES 12 SP3, disinstallare i pacchetti seguenti. È possibile ignorare gli errori relativi ai pacchetti non trovati.
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. Installare i pacchetti di destinazione iSCSI.

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Abilitare il servizio di destinazione iSCSI.

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Creare il dispositivo iSCSI nel server di destinazione iSCSI

Collegare un nuovo disco dati alla macchina virtuale di destinazione iSCSI che è possibile usare per questo cluster. Il disco dati può anche essere di 1 GB soltanto e deve essere inserito in un account di archiviazione Premium o un disco gestito Premium per poter sfruttare i vantaggi offerti dal [contratto di servizio per una singola macchina virtuale](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Eseguire il comando seguente nella **macchina virtuale di destinazione iSCSI** per creare un disco iSCSI per il nuovo cluster. Nell'esempio seguente, **cl1** viene usato per identificare il nuovo cluster, mentre **prod-cl1-0** e **prod-cl1-1** sono i nomi host dei nodi del cluster da sostituire con i nomi host dei nodi del cluster in uso.

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>Configurare il dispositivo SBD

Connettersi al dispositivo iSCSI creato nell'ultimo passaggio del cluster.
Eseguire i comandi seguenti nei nodi del nuovo cluster che si intende creare.
Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]**  Connettersi ai dispositivi iSCSI

   Abilitare prima i servizi iSCSI e SBD.

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Modificare il nome dell'iniziatore nel primo nodo

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modificare il contenuto del file in modo che corrisponda agli ACL usati durante la creazione del dispositivo iSCSI nel server di destinazione iSCSI.

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]**  Modificare il nome dell'iniziatore nel secondo nodo

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modificare il contenuto del file in modo che corrisponda agli ACL usati durante la creazione del dispositivo iSCSI nel server di destinazione iSCSI.

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** Riavviare il servizio iSCSI

   Riavviare ora il servizio iSCSI per applicare la modifica.
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Connettersi ai dispositivi iSCSI. Nell'esempio seguente, 10.0.0.17 è l'indirizzo IP del server di destinazione iSCSI e 3260 è la porta predefinita. <b>iqn.2006-04.cl1.local:cl1</b> è il nome di destinazione elencato quando si esegue il primo comando.

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Assicurarsi che il dispositivo iSCSI sia disponibile e annotare il nome del dispositivo (nell'esempio seguente /dev/sde).

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   A questo punto, recuperare l'ID del dispositivo iSCSI.

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   Vengono elencati tre ID di dispositivo. È consigliabile usare l'ID che inizia con scsi-3. Nell'esempio precedente si tratta dell'ID seguente:
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** Creare il dispositivo SBD

   Usare l'ID del dispositivo iSCSI per creare un nuovo dispositivo SBD nel primo nodo del cluster.

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** Adattare la configurazione di SBD

   Aprire il file di configurazione SBD.

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   Modificare la proprietà del dispositivo SBD, abilitare l'integrazione di Pacemaker e modificare la modalità di avvio di SBD.

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   Creare il file di configurazione softdog.

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Caricare ora il modulo.

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Installazione del cluster

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]** Aggiornare SLES

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** Abilitare l'accesso SSH

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** Abilitare l'accesso SSH

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Abilitare l'accesso SSH

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Installare l'estensione per la disponibilità elevata
   
   <pre><code>
   sudo zypper install sle-ha-release fence-agents
   </code></pre>

1. **[A]** Configurare la risoluzione dei nomi host   

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti. Il vantaggio di usare /etc/hosts è che il cluster diventa indipendente dal DNS che potrebbe essere un singolo punto di guasto.

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Installare il cluster
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** Aggiungere un nodo al cluster
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** Modificare la password hacluster in modo da usare la stessa password

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** Configurare corosync per usare un altro trasporto e aggiungere nodelist In caso contrario, il cluster non funzionerà.
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   Aggiungere il contenuto in grassetto seguente al file se i valori non sono presenti o sono diversi.
   
   <pre><code> 
   [...]
     <b>token:          5000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Riavviare quindi il servizio corosync

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Modificare le impostazioni predefinite di Pacemaker

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>Creare il dispositivo STONITH

Il dispositivo STONITH usa un'entità servizio per l'autorizzazione in Microsoft Azure. Per creare un'entità servizio, seguire questa procedura.

1. Passare a <https://portal.azure.com>.
1. Aprire il pannello Azure Active Directory  
   Passare a Proprietà e annotare l'ID directory. Si tratta dell'**ID tenant**.
1. Fare clic su Registrazioni per l'app
1. Fare clic su Aggiungi.
1. Immettere un nome, selezionare il tipo di applicazione "App Web/API", immettere un URL di accesso (ad esempio, http://localhost) e fare clic su Crea.
1. L'URL di accesso non viene usato e può essere qualsiasi URL valido
1. Selezionare la nuova app e fare clic su Chiavi nella scheda Impostazioni
1. Immettere una descrizione per una nuova chiave, selezionare "Non scade mai" e fare clic su Salva
1. Annotare il valore. Viene usato come **password** per l'entità servizio
1. Annotare l'ID applicazione. Viene usato come nome utente (**ID di accesso** nella procedura seguente) dell'entità servizio

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Creare un ruolo personalizzato per l'agente di isolamento

L'entità servizio non ha le autorizzazioni per accedere alle risorse di Azure per impostazione predefinita. È necessario concedere all'entità servizio le autorizzazioni per avviare e arrestare (deallocare) tutte le macchine virtuali del cluster. Se il ruolo personalizzato non è già stato creato, è possibile farlo usando [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role).

Per il file di input usare il contenuto seguente. Tale contenuto deve essere adattato alle sottoscrizioni, ovvero è necessario sostituire c276fc76-9cd4-44c9-99a7-4fd71546436e ed e91d47c4-76f3-4271-a796-21b4ecfe3624 con gli ID delle sottoscrizioni. Se si ha una sola sottoscrizione, rimuovere la seconda voce in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** Assegnare il ruolo personalizzato all'entità servizio

Assegnare all'entità servizio il ruolo personalizzato "Linux Fence Agent Role" creato nel capitolo precedente. Non usare più il ruolo Owner.

1. Passare a https://portal.azure.com.
1. Aprire il pannello Tutte le risorse
1. Selezionare la macchina virtuale del primo nodo del cluster.
1. Fare clic su Controllo di accesso (IAM)
1. Fare clic su Aggiungi.
1. Selezionare il ruolo "Linux Fence Agent Role".
1. Immettere il nome dell'applicazione creata in precedenza
1. Fare clic su OK.

Ripetere la procedura precedente per il secondo nodo del cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Creare il dispositivo STONITH

Dopo aver modificato le autorizzazioni per le macchine virtuali, è possibile configurare i dispositivi STONITH nel cluster.

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]**  Creare la topologia per l'isolamento tramite SBD

Se si intende usare un dispositivo SBD, è comunque consigliabile usare un agente di isolamento di Azure come backup nel caso in cui il server di destinazione iSCSI non sia disponibile.

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** Abilitare l'uso di un dispositivo STONITH

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha].
