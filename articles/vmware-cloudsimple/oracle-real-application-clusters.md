---
title: 'Soluzione VMware di Azure di CloudSimple: ottimizzare il cloud privato CloudSimple per Oracle RAC'
description: Viene descritto come distribuire un nuovo cluster e ottimizzare una macchina virtuale per l'installazione e la configurazione di Oracle Real Application Clusters (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 109cad9988645b4033d7d3da10c6da09540fa811
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881112"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Ottimizzare il cloud privato di CloudSimple per l'installazione di Oracle RAC

È possibile distribuire Oracle Real Application Clusters (RAC) nell'ambiente cloud privato CloudSimple. Questa guida descrive come distribuire un nuovo cluster e ottimizzare una macchina virtuale per la soluzione Oracle RAC. Dopo aver completato i passaggi descritti in questo argomento, è possibile installare e configurare Oracle RAC.

## <a name="storage-policy"></a>Criteri di archiviazione

Una corretta implementazione di Oracle RAC richiede un numero adeguato di nodi nel cluster.  Nei criteri di archiviazione rete VSAN, gli errori di tolleranza (ITF) vengono applicati ai dischi dati utilizzati per archiviare i dischi del database, del log e del ripristino.  Il numero di nodi necessario per tollerare efficacemente gli errori è 2N + 1, dove N è il valore di ITF.

Esempio: Se l'ITF desiderata è 2, il numero totale di nodi nel cluster deve essere 2 * 2 + 1 = 5.

## <a name="overview-of-deployment"></a>Panoramica della distribuzione

Le sezioni seguenti descrivono come configurare l'ambiente cloud privato CloudSimple per Oracle RAC.

1. Procedure consigliate per la configurazione del disco
2. Distribuire un cluster vSphere del cloud privato CloudSimple
3. Configurare la rete per Oracle RAC
4. Configurare i criteri di archiviazione rete VSAN
5. Creare macchine virtuali Oracle e creare dischi di VM condivisi
6. Configurare regole di affinità da macchina virtuale a host

## <a name="best-practices-for-disk-configuration"></a>Procedure consigliate per la configurazione del disco

Le macchine virtuali Oracle RAC hanno più dischi, che vengono usati per una funzione specifica.  I dischi condivisi sono montati in tutte le macchine virtuali, che vengono usate dal cluster Oracle RAC.  I dischi di installazione del sistema operativo e del software sono montati solo nelle singole macchine virtuali.  

![Panoramica sui dischi delle macchine virtuali Oracle RAC](media/oracle-vm-disks-overview.png)

Nell'esempio seguente vengono usati i dischi definiti nella tabella seguente.

| Disco                                      | Scopo                                       | Disco condiviso |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | Disco del sistema operativo                         | No          |
| GRIGLIA                                      | Percorso di installazione per il software Grid Oracle     | No          |
| DATABASE                                  | Percorso di installazione per il software Oracle database | No          |
| ORAHOME                                   | Percorso di base per i file binari del database Oracle    | No          |
| DATA1, DATA2, DATA3, DATA4                | Disco in cui sono archiviati i file di database Oracle   | Sì         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Dischi di log di rollforward                                | Sì         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Dischi votanti                                  | Yes         |
| FRA1, FRA2                                | Dischi dell'area di ripristino rapido                      | Sì         |

![Configurazione del disco della macchina virtuale Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configurazione macchina virtuale

* Ogni macchina virtuale è configurata con quattro controller SCSI.
* Il tipo di controller SCSI è impostato su VMware paravirtual.
* Vengono creati più dischi virtuali (con estensione VMDK).
* I dischi sono montati su controller SCSI diversi.
* Il tipo di condivisione MultiWriter è impostato per i dischi cluster condivisi.
* i criteri di archiviazione rete VSAN sono definiti per garantire la disponibilità elevata dei dischi.

### <a name="operating-system-and-software-disk-configuration"></a>Configurazione del sistema operativo e del disco software

Ogni macchina virtuale Oracle è configurata con più dischi per il sistema operativo host, lo scambio, l'installazione del software e altre funzioni del sistema operativo.  Questi dischi non sono condivisi tra le macchine virtuali.  

* Tre dischi per ogni macchina virtuale sono configurati come dischi virtuali e montati in macchine virtuali Oracle RAC.
    * Disco sistema operativo
    * Disco per l'archiviazione dei file di installazione della griglia Oracle
    * Disco per l'archiviazione dei file di installazione del database Oracle
* I dischi possono essere configurati con **Thin**provisioning.
* Ogni disco viene montato sul primo controller SCSI (SCSI0).  
* La condivisione è impostata su **No sharing**.
* La ridondanza è definita nell'archiviazione mediante i criteri di rete VSAN.  

![Configurazione gruppo dischi dati Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configurazione del disco dati

I dischi dati vengono utilizzati principalmente per archiviare i file di database.  

* Quattro dischi sono configurati come dischi virtuali e montati in tutte le macchine virtuali Oracle RAC.
* Ogni disco viene montato in un controller SCSI diverso.
* Ogni disco virtuale è configurato come provisioning a spessore con **desiderio zero**.  
* La condivisione è impostata su più **writer**.  
* I dischi devono essere configurati come gruppo di dischi di gestione archiviazione automatica (ASM).  
* La ridondanza è definita nell'archiviazione mediante i criteri di rete VSAN.  
* La ridondanza ASM è impostata su ridondanza **esterna** .

![Configurazione gruppo dischi dati Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Configurazione del disco del log di rollforward

I file di log di rollforward vengono utilizzati per archiviare una copia delle modifiche apportate al database.  I file di log vengono usati quando è necessario ripristinare i dati dopo eventuali errori.

* I dischi di log di rollforward devono essere configurati come più gruppi di dischi.  
* Sei dischi creati e montati in tutte le macchine virtuali Oracle RAC.
* I dischi sono montati su controller SCSI diversi
* Ogni disco virtuale è configurato come provisioning a spessore con **desiderio zero**.
* La condivisione è impostata su più **writer**.  
* I dischi devono essere configurati come due gruppi di dischi ASM.
* Ogni gruppo di dischi ASM contiene tre dischi che si trovano su controller SCSI diversi.  
* La ridondanza ASM è impostata su una ridondanza **normale** .
* Cinque file di log di rollforward vengono creati nel gruppo di log di rollforward di ASM

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

![Configurazione del gruppo di dischi di log di rollforward Oracle RAC](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configurazione del disco di voto Oracle

I dischi di voto forniscono la funzionalità del disco quorum come canale di comunicazione aggiuntivo per evitare qualsiasi situazione di Split Brain.

* Cinque dischi vengono creati e montati in tutte le macchine virtuali Oracle RAC.
* I dischi sono montati in un controller SCSI
* Ogni disco virtuale è configurato come provisioning a spessore con **desiderio zero**.
* La condivisione è impostata su più **writer**.  
* I dischi devono essere configurati come gruppo di dischi ASM.  
* La ridondanza ASM è impostata su una ridondanza **elevata** .

![Configurazione del gruppo di dischi per il voto Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Configurazione del disco dell'area di ripristino rapido Oracle (facoltativo)

L'area di ripristino rapido (FRA) è file system gestita dal gruppo di dischi Oracle ASM.  FRA è disponibile un percorso di archiviazione condiviso per i file di backup e ripristino. Oracle crea log archiviati e log di flashback nell'area di ripristino rapido. Oracle Recovery Manager (RMAN) può facoltativamente archiviare i set di backup e le copie di immagini nell'area di ripristino rapido e lo usa per il ripristino dei file durante il ripristino del supporto.

* Due dischi vengono creati e montati in tutte le macchine virtuali Oracle RAC.
* I dischi sono montati in un controller SCSI diverso
* Ogni disco virtuale è configurato come provisioning a spessore con **desiderio zero**.
* La condivisione è impostata su più **writer**.  
* I dischi devono essere configurati come gruppo di dischi ASM.  
* La ridondanza ASM è impostata su ridondanza **esterna** .

![Configurazione del gruppo di dischi per il voto Oracle RAC](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Distribuire un cluster vSphere del cloud privato CloudSimple

Per distribuire un cluster vSphere nel cloud privato, seguire questa procedura:

1. Dal portale di CloudSimple [creare un cloud privato](create-private-cloud.md). CloudSimple crea un utente vCenter predefinito denominato ' cloudowner ' nel cloud privato appena creato. Per informazioni dettagliate sull'utente e sul modello di autorizzazione del cloud privato predefinito, vedere [informazioni sul modello di autorizzazione per il cloud privato](learn-private-cloud-permissions.md).  Questo passaggio consente di creare il cluster di gestione primario per il cloud privato.

2. Dal portale di CloudSimple [espandere il cloud privato](expand-private-cloud.md) con un nuovo cluster.  Questo cluster verrà usato per distribuire Oracle RAC.  Selezionare il numero di nodi in base alla tolleranza di errore desiderata (minimo tre nodi).

## <a name="set-up-networking-for-oracle-rac"></a>Configurare la rete per Oracle RAC

1. Nel cloud privato [creare due VLAN](create-vlan-subnet.md), una per la rete pubblica Oracle e una per la rete privata Oracle e assegnare la subnet CIDRs appropriata.
2. Dopo aver creato le VLAN, creare i [gruppi di porte distribuite nel cloud privato vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Configurare una [macchina virtuale server DHCP e DNS](dns-dhcp-setup.md) nel cluster di gestione per l'ambiente Oracle.
4. [Configurare l'invio DNS nel server DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) installato nel cloud privato.

## <a name="set-up-vsan-storage-policies"></a>Configurare i criteri di archiviazione rete VSAN

i criteri rete VSAN definiscono gli errori da tollerare e lo striping del disco per i dati archiviati nei dischi delle macchine virtuali.  I criteri di archiviazione creati devono essere applicati ai dischi della VM durante la creazione della macchina virtuale.

1. [Accedere al client vSphere](https://docs.azure.cloudsimple.com/vsphere-access) del cloud privato.
2. Scegliere **criteri e profili**dal menu in alto.
3. Nel menu a sinistra selezionare **criteri di archiviazione della macchina virtuale** e quindi selezionare **Crea criteri di archiviazione delle macchine virtuali**.
4. Immettere un nome significativo per il criterio e fare clic su **Avanti**.
5. Nella sezione **struttura dei criteri** selezionare **Abilita regole per l'archiviazione rete VSAN** e fare clic su **Avanti**.
6. Nella sezione**disponibilità** **rete VSAN** > selezionare **nessuno** per la tolleranza di emergenza del sito. Per gli errori da tollerare, selezionare l'opzione di **mirroring RAID** per l'ITF desiderata.
    ![impostazioni](media/oracle-rac-storage-wizard-vsan.png)rete VSAN.
7. Nella sezione **Avanzate** selezionare il numero di striping del disco per oggetto. Per prenotazione spazio oggetto selezionare con provisioning di **spessore**. Selezionare **Disattiva checksum oggetti**. Fare clic su **Avanti**.
8. Seguire le istruzioni visualizzate per visualizzare l'elenco di archivi dati rete VSAN compatibili, rivedere le impostazioni e completare la configurazione.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Creare macchine virtuali Oracle e creare dischi di VM condivisi per Oracle

Per creare una VM per Oracle, clonare una macchina virtuale esistente o crearne una nuova.  Questa sezione descrive come creare una nuova macchina virtuale e quindi clonarla per crearne una seconda dopo l'installazione del sistema operativo di base.  Dopo aver creato le macchine virtuali, è possibile creare un disco aggiuntivo.  Il cluster Oracle USA dischi condivisi per archiviare, dati, log e log di rollforward.

### <a name="create-vms"></a>Creare VM

1. In vCenter fare clic sull'icona **host e cluster** . Selezionare il cluster creato per Oracle.
2. Fare clic con il pulsante destro del mouse sul cluster e scegliere **nuova macchina virtuale**.
3. Selezionare **Crea nuova macchina virtuale** e fare clic su **Avanti**.
4. Assegnare un nome al computer, selezionare il percorso della VM Oracle e fare clic su **Avanti**.
5. Selezionare la risorsa cluster e fare clic su **Avanti**.
6. Selezionare l'archivio dati rete VSAN per il cluster e fare clic su **Avanti**.
7. Mantieni la selezione di compatibilità predefinita ESXi 6,5 e fai clic su **Avanti**.
8. Selezionare il sistema operativo guest dell'ISO per la macchina virtuale che si sta creando e fare clic su **Avanti**.
9. Selezionare le dimensioni del disco rigido necessarie per l'installazione del sistema operativo.
10. Per installare l'applicazione in un dispositivo diverso, fare clic su **Aggiungi nuovo dispositivo**.
11. Selezionare le opzioni di rete e assegnare il gruppo di porte distribuite creato per la rete pubblica.
12. Per aggiungere altre interfacce di rete, fare clic su **Aggiungi nuovo dispositivo** e selezionare il gruppo di porte distribuite creato per la rete privata.
13. Per la nuova unità DC/DVD selezionare il file ISO dell'archivio dati che contiene l'immagine ISO per l'installazione del sistema operativo preferita. Selezionare il file caricato in precedenza nella cartella ISO e modelli e fare clic su **OK**.
14. Verificare le impostazioni e fare clic su **OK** per creare la nuova macchina virtuale.
15. Accendere la macchina virtuale. Installare il sistema operativo ed eventuali aggiornamenti necessari

Dopo l'installazione del sistema operativo, è possibile clonare una seconda macchina virtuale. Fare clic con il pulsante destro del mouse sulla voce della macchina virtuale e selezionare l'opzione e clona.

### <a name="create-shared-disks-for-vms"></a>Creare dischi condivisi per le macchine virtuali

Oracle USA il disco condiviso per archiviare i file di log di dati, log e rollforward.  È possibile creare un disco condiviso in vCenter e montarlo in entrambe le macchine virtuali.  Per ottenere prestazioni più elevate, inserire i dischi dati in diversi passaggi di controller SCSI seguenti illustrano come creare un disco condiviso in vCenter e quindi collegarlo a una macchina virtuale. il client Flash vCenter viene usato per modificare le proprietà della macchina virtuale.

#### <a name="create-disks-on-the-first-vm"></a>Creare dischi nella prima VM

1. In vCenter, fare clic con il pulsante destro del mouse su una delle macchine virtuali Oracle e scegliere **Modifica impostazioni**.
2. Nella sezione nuovo dispositivo selezionare **controller SCSI** e fare clic su **Aggiungi**.
3. Nella sezione nuovo dispositivo selezionare **nuovo disco rigido** e fare clic su **Aggiungi**.
4. Espandere le proprietà del nuovo disco rigido.
5. Specificare le dimensioni del disco rigido.
6. Specificare i criteri di archiviazione delle macchine virtuali come criteri di archiviazione rete VSAN definiti in precedenza.
7. Selezionare la località come cartella nell'archivio dati rete VSAN. Il percorso consente di esplorare e associare i dischi a una seconda macchina virtuale.
8. Per il provisioning del disco, selezionare provisioning con **spessore eager zero**.
9. Per la condivisione, specificare la **funzionalità MultiWriter**.
10. Per il nodo del dispositivo virtuale selezionare il nuovo controller SCSI creato nel passaggio 2.

    ![Creare dischi nella prima macchina virtuale](media/oracle-rac-new-hard-disk.png)

Ripetere i passaggi da 2 a 10 per tutti i nuovi dischi necessari per i file di log di rollforward, i dati e i log di Oracle.

#### <a name="attach-disks-to-second-vm"></a>Connetti i dischi alla seconda macchina virtuale

1. In vCenter, fare clic con il pulsante destro del mouse su una delle macchine virtuali Oracle e scegliere **Modifica impostazioni**.
2. Nella sezione nuovo dispositivo selezionare **controller SCSI** e fare clic su **Aggiungi**.
3. Nella sezione nuovo dispositivo selezionare **disco rigido esistente** e fare clic su **Aggiungi**.
4. Passare al percorso in cui è stato creato il disco per la prima macchina virtuale e selezionare il file VMDK.
5. Specificare i criteri di archiviazione delle macchine virtuali come criteri di archiviazione rete VSAN definiti in precedenza.
6. Per il provisioning del disco, selezionare provisioning con **spessore eager zero**.
7. Per la condivisione, specificare la **funzionalità MultiWriter**.
8. Per il nodo del dispositivo virtuale selezionare il nuovo controller SCSI creato nel passaggio 2.

    ![Creare dischi nella prima macchina virtuale](media/oracle-rac-existing-hard-disk.png)

Ripetere i passaggi da 2 a 7 per tutti i nuovi dischi necessari per i file di log di rollforward, i dati e i log di Oracle.

## <a name="set-up-vm-host-affinity-rules"></a>Configurare regole di affinità host VM

Le regole di affinità da macchina virtuale a host assicurano che la macchina virtuale venga eseguita nell'host desiderato.  È possibile definire regole in vCenter per assicurarsi che la macchina virtuale Oracle venga eseguita nell'host con risorse appropriate e soddisfi i requisiti di licenza specifici.

1. Nel portale di CloudSimple, [inoltrare i privilegi](escalate-private-cloud-privileges.md) dell'utente cloudowner.
2. [Accedere al client vSphere](https://docs.azure.cloudsimple.com/vsphere-access) del cloud privato.
3. Nel client di vSphere selezionare il cluster in cui vengono distribuite le macchine virtuali Oracle e fare clic su **Configura**.
4. In Configure selezionare **VM/host groups**.
5. Fare clic su **+**
6. Aggiungere un gruppo di macchine virtuali. Selezionare **gruppo di macchine virtuali** come tipo. Immettere il nome del gruppo. Selezionare le macchine virtuali e quindi fare clic su **OK** per creare il gruppo.
6. Aggiungere un gruppo host. Selezionare **gruppo host** come tipo. Immettere il nome del gruppo. Selezionare gli host in cui vengono eseguite le macchine virtuali e quindi fare clic su **OK** per creare il gruppo.
7. Per creare una regola, fare clic su **regole VM/host**.
8. Fare clic su **+**
9. Immettere un nome per la regola e selezionare **Abilita**.
10. Per tipo di regola selezionare **macchine virtuali da ospitare**.
11. Selezionare il gruppo di VM che contiene le macchine virtuali Oracle.
12. Selezionare **deve essere eseguito negli host del gruppo**.
13. Selezionare il gruppo host creato.
14. Fare clic su **OK** per creare la regola.

## <a name="references"></a>Riferimenti

* [Informazioni sui criteri di rete VSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Attributo VMware MultiWriter per VMDK condivisi](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
