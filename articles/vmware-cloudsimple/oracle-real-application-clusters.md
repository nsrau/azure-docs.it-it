---
title: Soluzione Azure VMware di CloudSimple - Ottimizzare il cloud semplice per Oracle RAC
description: Descrive come distribuire un nuovo cluster e ottimizzare l'installazione e la configurazione di una macchina virtuale per Oracle Real Application Cluster (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016018"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Ottimizzare il cloud privato CloudSimple per l'installazione di Oracle RAC

È possibile distribuire Oracle Real Application Clusters (RAC) nell'ambiente CloudSimple Private Cloud. Questa guida descrive come distribuire un nuovo cluster e ottimizzare una macchina virtuale per la soluzione Oracle RAC. Dopo aver completato i passaggi descritti in questo argomento, è possibile installare e configurare Oracle RAC.

## <a name="storage-policy"></a>Criteri di archiviazione

La corretta implementazione di Oracle RAC richiede un numero adeguato di nodi nel cluster.  Nei criteri di archiviazione vSAN, gli errori da tollerare (FTT) vengono applicati ai dischi dati utilizzati per archiviare i dischi di database, log e di ripetizione.  Il numero richiesto di nodi per tollerare in modo efficace gli errori è 2N-1, dove N è il valore di FTT.

Esempio: se l'ampliato f:" desiderato è 2, il numero totale di nodi nel cluster deve essere di 2,2,1 x 5.

## <a name="overview-of-deployment"></a>Panoramica della distribuzione

Nelle sezioni seguenti viene descritto come configurare l'ambiente CloudSimple Private Cloud per Oracle RAC.

1. Procedure consigliate per la configurazione del disco
2. Distribuire il cluster vSphere cloud Simple private Cloud
3. Configurare la rete per Oracle RAC
4. Configurare i criteri di archiviazione vSAN
5. Creare macchine virtuali Oracle e creare dischi VM condivisiCreate Oracle VMs and create shared VM disks
6. Configurare le regole di affinità da macchina da macchina virtuale a hostSet up VM-to host affinity rules

## <a name="best-practices-for-disk-configuration"></a>Procedure consigliate per la configurazione del disco

Le macchine virtuali Oracle RAC dispongono di più dischi, utilizzati per una funzione specifica.  I dischi condivisi vengono montati su tutte le macchine virtuali utilizzate da Oracle RAC Cluster.  I dischi di installazione del sistema operativo e del software vengono montati solo nelle singole macchine virtuali.  

![Panoramica dei dischi delle macchine virtuali Oracle RAC](media/oracle-vm-disks-overview.png)

Nell'esempio seguente vengono utilizzati i dischi definiti nella tabella seguente.

| Disco                                      | Scopo                                       | Disco condiviso |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | Disco del sistema operativo                         | No          |
| Griglia                                      | Percorso di installazione per il software Oracle Grid     | No          |
| DATABASE                                  | Percorso di installazione per il software di database Oracle | No          |
| ORAHOME                                   | Percorso di base per i file binari del database Oracle    | No          |
| DATI1, DATA2, DATI3, DATI4                | Disco in cui sono archiviati i file di database Oracle   | Sì         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Ripeti dischi di log                                | Sì         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Dischi di voto                                  | Sì         |
| FRA1                                | Dischi dell'area di ripristino rapida                      | Sì         |

![Configurazione del disco della macchina virtuale Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configurazione della macchina virtuale

* Ogni macchina virtuale è configurata con quattro controller SCSI.
* Il tipo di controller SCSI è impostato su VMware paravirtual.
* Vengono creati più dischi virtuali (con estensione vmdk).
* I dischi sono montati su controller SCSI diversi.
* Il tipo di condivisione multiwriter è impostato per i dischi del cluster condivisi.
* I criteri di archiviazione vSAN sono definiti per garantire un'elevata disponibilità dei dischi.

### <a name="operating-system-and-software-disk-configuration"></a>Configurazione del sistema operativo e del disco del software

Ogni macchina virtuale Oracle è configurata con più dischi per il sistema operativo host, lo scambio, l'installazione del software e altre funzioni del sistema operativo.  Questi dischi non vengono condivisi tra le macchine virtuali.  

* Tre dischi per ogni macchina virtuale sono configurati come dischi virtuali e montati in macchine virtuali Oracle RAC.
    * Disco del sistema operativo
    * Disco per l'archiviazione di Oracle Grid installa i file
    * Disco per l'archiviazione dei file di installazione del database OracleDisk for storing Oracle database install files
* I dischi possono essere configurati come **Thin Provisioned**.
* Ogni disco è montato sul primo controller SCSI (SCSI0).  
* Condivisione è impostata su **Nessuna condivisione**.
* La ridondanza viene definita nell'archiviazione utilizzando i criteri vSAN.  

![Configurazione del gruppo di dischi dati Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configurazione del disco dati

I dischi dati vengono utilizzati principalmente per l'archiviazione dei file di database.  

* Quattro dischi sono configurati come dischi virtuali e montati in tutte le macchine virtuali Oracle RAC.
* Ogni disco è montato su un controller SCSI diverso.
* Ogni disco virtuale è configurato come **Thick Provision Eager zeroed**.  
* La condivisione è impostata su **Multi-writer**.  
* I dischi devono essere configurati come gruppo di dischi ASM (Automatic Storage Management).  
* La ridondanza viene definita nell'archiviazione utilizzando i criteri vSAN.  
* La ridondanza ASM è impostata su Ridondanza **esterna.**

![Configurazione del gruppo di dischi dati Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Eseguire il backup della configurazione del disco del registro

I file di registro di ripristino vengono utilizzati per archiviare una copia delle modifiche apportate al database.  I file di log vengono utilizzati quando è necessario recuperare i dati dopo eventuali errori.

* I dischi di log di ripetizione devono essere configurati come più gruppi di dischi.  
* Vengono creati e montati su tutte le macchine virtuali Oracle RAC, vengono creati e installati sei dischi.
* I dischi sono montati su diversi controller SCSI
* Ogni disco virtuale è configurato come **Thick Provision Eager zeroed**.
* La condivisione è impostata su **Multi-writer**.  
* I dischi devono essere configurati come due gruppi di dischi ASM.
* Ogni gruppo di dischi ASM contiene tre dischi, che si trovano su controller SCSI diversi.  
* Ridondanza ASM è impostata su **Ridondanza normale.**
* Vengono creati cinque file di log di ricreazione in entrambi i gruppi di log di ripetizione ASM

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Configurazione del gruppo di dischi di log di rieseguire Oracle RAC](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configurazione del disco di voto Oracle

I dischi di voto forniscono la funzionalità del disco quorum come canale di comunicazione aggiuntivo per evitare qualsiasi situazione di split brain.

* In tutte le macchine virtuali Oracle RAC vengono creati e installati cinque dischi.
* I dischi sono montati su un controller SCSI
* Ogni disco virtuale è configurato come **Thick Provision Eager zeroed**.
* La condivisione è impostata su **Multi-writer**.  
* I dischi devono essere configurati come gruppo di dischi ASM.  
* Ridondanza ASM è impostata su **Ridondanza elevata.**

![Configurazione del gruppo di dischi di voto Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Configurazione del disco dell'area di ripristino rapido Oracle (opzionale)

L'area di ripristino rapido (FRA) è il file system gestito dal gruppo di dischi Oracle ASM.  FRA fornisce un percorso di archiviazione condiviso per i file di backup e ripristino. Oracle crea registri archiviati e log flashback nell'area di ripristino rapido. Oracle Recovery Manager (RMAN) può facoltativamente archiviare i set di backup e le copie delle immagini nell'area di ripristino rapido e utilizzarla durante il ripristino dei file durante il ripristino multimediale.

* Due dischi vengono creati e montati su tutte le macchine virtuali Oracle RAC.
* I dischi sono montati su un controller SCSI diverso
* Ogni disco virtuale è configurato come **Thick Provision Eager zeroed**.
* La condivisione è impostata su **Multi-writer**.  
* I dischi devono essere configurati come gruppo di dischi ASM.  
* La ridondanza ASM è impostata su Ridondanza **esterna.**

![Configurazione del gruppo di dischi di voto Oracle RAC](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Distribuire il cluster CloudSimple Private Cloud vSphere

Per distribuire un cluster vSphere nel cloud privato, eseguire questa procedura:To deploy a vSphere cluster on your Private Cloud, follow this process:

1. Dal portale CloudSimple, [creare un cloud privato](create-private-cloud.md). CloudSimple crea un utente vCenter predefinito denominato 'cloudowner' nel cloud privato appena creato. Per informazioni dettagliate sull'utente e sul modello di autorizzazione predefiniti di Private Cloud, consultate Informazioni sul modello di [autorizzazione Cloud privato.](learn-private-cloud-permissions.md)  Questo passaggio consente di creare il cluster di gestione primario per il cloud privato.

2. Dal portale CloudSimple, [espandere il cloud privato](expand-private-cloud.md) con un nuovo cluster.  Questo cluster verrà utilizzato per distribuire Oracle RAC.  Selezionare il numero di nodi in base alla tolleranza di errore desiderata (minimo tre nodi).

## <a name="set-up-networking-for-oracle-rac"></a>Configurare la rete per Oracle RAC

1. Nel cloud privato [creare due VLAN,](create-vlan-subnet.md)una per la rete pubblica Oracle e una per la rete privata Oracle e assegnare i CIDR di subnet appropriati.
2. Dopo aver creato le VLAN, creare i gruppi di [porte distribuite nel cloud privato vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Configurare una [macchina virtuale del server DHCP e DNS](dns-dhcp-setup.md) nel cluster di gestione per l'ambiente Oracle.
4. [Configurare l'inoltro DNS nel server DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) installato nel cloud privato.

## <a name="set-up-vsan-storage-policies"></a>Configurare i criteri di archiviazione vSAN

I criteri vSAN definiscono gli errori da tollerare e lo striping dei dischi per i dati archiviati nei dischi della macchina virtuale.  I criteri di archiviazione creati devono essere applicati ai dischi della macchina virtuale durante la creazione della macchina virtuale.

1. [Accedi al client vSphere](https://docs.azure.cloudsimple.com/vsphere-access) del tuo cloud privato.
2. Dal menu in alto, selezionare **Criteri e profili**.
3. Nel menu a sinistra selezionare **Criteri di archiviazione macchina virtuale** e quindi **selezionare Crea criteri di archiviazione macchina virtuale**.
4. Immettere un nome significativo per il criterio e fare clic su **AVANTI**.
5. Nella sezione **Struttura criteri** selezionare Abilita regole per l'archiviazione **vSAN** e fare clic su **AVANTI**.
6. Nella sezione**Disponibilità** **vSAN** > selezionare **Nessuno** per Tolleranza di emergenza del sito. Per Errori da tollerare, selezionare l'opzione **RAID - Mirroring** per l'ITF desiderato.
    ![impostazioni vSAN](media/oracle-rac-storage-wizard-vsan.png).
7. Nella sezione **Avanzate** selezionare il numero di stripe disco per oggetto. Per Prenotazione spazio oggetti, selezionare **Thick Provisioned**. Selezionare **Disattiva checksum oggetto**. Fare clic su **AVANTI**.
8. Seguire le istruzioni visualizzate per visualizzare l'elenco dei datastore vSAN compatibili, rivedere le impostazioni e completare la configurazione.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Create Oracle VMs and create shared VM disks for Oracle

Per creare una macchina virtuale per Oracle, clonare una macchina virtuale esistente o crearne una nuova.  In questa sezione viene descritto come creare una nuova macchina virtuale e quindi clonarla per crearne una seconda dopo l'installazione del sistema operativo di base.  Dopo aver creato le macchine virtuali, è possibile creare un'aggiunta di dischi.  Il cluster Oracle utilizza dischi condivisi per l'archiviazione, i dati, i log e i registri di ripetizione.

### <a name="create-vms"></a>Creare VM

1. In vCenter, fare clic sull'icona **Host e cluster.** Selezionare il cluster creato per Oracle.Select the cluster that you created for Oracle.
2. Fare clic con il pulsante destro del mouse sul cluster e scegliere **Nuova macchina virtuale**.
3. Selezionare **Crea nuova macchina virtuale** e fare clic su **Avanti**.
4. Assegnare un nome al computer, selezionare il percorso di Oracle VM e fare clic su **Avanti**.
5. Selezionare la risorsa cluster e fare clic su **Avanti**.
6. Selezionare l'archivio dati vSAN per il cluster e fare clic su **Avanti**.
7. Mantenere la selezione di compatibilità predefinita di ESXi 6.5 e fare clic su **Avanti**.
8. Selezionare il sistema operativo guest dell'ISO per la macchina virtuale che si sta creando e fare clic su **Avanti**.
9. Selezionare la dimensione del disco rigido necessaria per l'installazione del sistema operativo.
10. Per installare l'applicazione su un dispositivo diverso, fare clic su **Aggiungi nuovo dispositivo**.
11. Selezionare le opzioni di rete e assegnare il gruppo di porte distribuite creato per la rete pubblica.
12. Per aggiungere altre interfacce di rete, fare clic su **Aggiungi nuovo dispositivo** e selezionare il gruppo di porte distribuite creato per la rete privata.
13. Per Nuova unità DC/DVD, selezionare il file ISO dell'archivio dati contenente l'ISO per l'installazione del sistema operativo preferito. Selezionare il file caricato in precedenza nella cartella ISO e modelli e fare clic su **OK**.
14. Rivedere le impostazioni e fare clic **su OK** per creare la nuova macchina virtuale.
15. Accendere la macchina virtuale. Installare il sistema operativo ed eventuali aggiornamenti necessari

Dopo aver installato il sistema operativo, è possibile clonare una seconda macchina virtuale. Fare clic con il pulsante destro del mouse sulla voce della macchina virtuale e selezionare l'opzione e clone.

### <a name="create-shared-disks-for-vms"></a>Creare dischi condivisi per le macchine virtualiCreate shared disks for VMs

Oracle utilizza il disco condiviso per archiviare i file di log di dati, log e di archiviazione.  È possibile creare un disco condiviso in vCenter e montarlo in entrambe le macchine virtuali.  Per migliorare le prestazioni, posizionare i dischi dati in diversi controller SCSI I passaggi seguenti illustrano come creare un disco condiviso in vCenter e quindi collegarlo a una macchina virtuale. il client Flash di vCenter viene utilizzato per modificare le proprietà della macchina virtuale.

#### <a name="create-disks-on-the-first-vm"></a>Creare dischi nella prima macchina virtualeCreate disks on the first VM

1. In vCenter fare clic con il pulsante destro del mouse su una delle macchine virtuali Oracle e scegliere **Modifica impostazioni**.
2. Nella sezione del nuovo dispositivo selezionare **Controller SCSI** e fare clic su **Aggiungi**.
3. Nella sezione nuovo dispositivo selezionare **Nuovo disco rigido** e fare clic su **Aggiungi**.
4. Espandere le proprietà di Nuovo disco rigido.
5. Specificare la dimensione del disco rigido.
6. Specificare i criteri di archiviazione della macchina virtuale come criteri di archiviazione vSAN definiti in precedenza.
7. Selezionare il percorso come cartella nell'archivio dati vSAN. Il percorso consente di esplorare e collegare i dischi a una seconda macchina virtuale.
8. Per il provisioning del disco, selezionare **Thick provisioning eager azzerato**.
9. Per la condivisione, specificare **Multi-writer**.
10. Per il nodo del dispositivo virtuale, selezionare il nuovo controller SCSI creato nel passaggio 2.For the virtual device node, select the new SCSI controller that was created in step 2.

    ![Creare dischi nella prima macchina virtualeCreate disks on first VM](media/oracle-rac-new-hard-disk.png)

Ripetere i passaggi da 2 a 10 per tutti i nuovi dischi necessari per i file di log di dati, log e di riazione Oracle.

#### <a name="attach-disks-to-second-vm"></a>Collegare i dischi alla seconda macchina virtualeAttach disks to second VM

1. In vCenter fare clic con il pulsante destro del mouse su una delle macchine virtuali Oracle e scegliere **Modifica impostazioni**.
2. Nella sezione del nuovo dispositivo selezionare **Controller SCSI** e fare clic su **Aggiungi**.
3. Nella sezione del nuovo dispositivo selezionare **Disco rigido esistente** e fare clic su **Aggiungi**.
4. Passare al percorso in cui è stato creato il disco per la prima macchina virtuale e selezionare il file VMDK.
5. Specificare i criteri di archiviazione della macchina virtuale come criteri di archiviazione vSAN definiti in precedenza.
6. Per il provisioning del disco, selezionare **Thick provisioning eager azzerato**.
7. Per la condivisione, specificare **Multi-writer**.
8. Per il nodo del dispositivo virtuale, selezionare il nuovo controller SCSI creato nel passaggio 2.For the virtual device node, select the new SCSI controller that was created in step 2.

    ![Creare dischi nella prima macchina virtualeCreate disks on first VM](media/oracle-rac-existing-hard-disk.png)

Ripetere i passaggi da 2 a 7 per tutti i nuovi dischi necessari per i file di log di dati, log e di riazione Oracle.

## <a name="set-up-vm-host-affinity-rules"></a>Configurare le regole di affinità host della macchina virtualeSet up VM host affinity rules

Le regole di affinità DA macchina virtuale a host assicurano che la macchina virtuale venga eseguita nell'host desiderato.  È possibile definire regole in vCenter per garantire che la macchina virtuale Oracle venga eseguita nell'host con risorse adeguate e per soddisfare eventuali requisiti di licenza specifici.

1. Nel portale CloudSimple [riassegnare i privilegi](escalate-private-cloud-privileges.md) dell'utente del proprietario del cloud.
2. [Accedi al client vSphere](https://docs.azure.cloudsimple.com/vsphere-access) del tuo cloud privato.
3. Nel client vSphere selezionare il cluster in cui vengono distribuite le macchine virtuali Oracle e fare clic su **Configura**.
4. In Configura selezionare **VM/Gruppi host**.
5. Fare **+** clic su .
6. Aggiungere un gruppo di macchine virtuali. Selezionare **il gruppo di macchine virtuali** come tipo. Immettere il nome del gruppo. Selezionare le macchine virtuali e quindi fare clic **su OK** per creare il gruppo.
6. Aggiungere un gruppo host. Selezionare **Gruppo host** come tipo. Immettere il nome del gruppo. Selezionare gli host in cui verranno eseguite le macchine virtuali e quindi fare clic **su OK** per creare il gruppo.
7. Per creare una regola, fare clic su **Regole VM/Host**.
8. Fare **+** clic su .
9. Immettere un nome per la regola e **selezionare Abilita**.
10. Per il tipo di regola, selezionare **Macchine virtuali da ospitare.**
11. Selezionare il gruppo di macchine virtuali che contiene le macchine virtuali Oracle.Select the VM group that contains the Oracle VMs.
12. Selezionare **Deve essere eseguito sugli host di questo gruppo.**
13. Selezionare il gruppo host creato.
14. Fare clic **su OK** per creare la regola.

## <a name="references"></a>Riferimenti

* [Informazioni sui criteri vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Attributo VMware Multi-Writer per VMDK condivisi](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
