---
title: Distribuzione della piattaforma SAP BusinessObjects BI in Azure per Linux | Microsoft Docs
description: Distribuire e configurare la piattaforma SAP BusinessObjects BI in Azure per Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 1f15a3b4d8f51ec79fffce09bc006942d08096a6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427463"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Guida alla distribuzione della piattaforma di business intelligence SAP BusinessObjects per Linux in Azure

Questo articolo descrive la strategia di distribuzione della piattaforma SAP BOBI in Azure per Linux. In questo esempio vengono configurate due macchine virtuali con SSD Premium Managed Disks come directory di installazione. Database di Azure per MySQL viene usato per il database CMS e Azure NetApp Files per il server repository file è condiviso tra entrambi i server. L'applicazione Web Java Tomcat predefinita e l'applicazione della piattaforma BI vengono installate insieme in entrambe le macchine virtuali. Per bilanciare il carico della richiesta dell'utente, viene usato il gateway applicazione con funzionalità di offload TLS/SSL native.

Questo tipo di architettura è efficace per la distribuzione di piccole dimensioni o per un ambiente non di produzione. Per la distribuzione in produzione o su larga scala, è possibile avere host distinti per l'applicazione Web e possono avere più host applicazioni BOBI che consentono al server di elaborare altre informazioni.

![Distribuzione di SAP BOBI in Azure per Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

In questo esempio viene usata la versione del prodotto e il layout file system

- Piattaforma SAP BusinessObjects 4,3
- SUSE Linux Enterprise Server 12 SP5
- Database di Azure per MySQL (versione: 8.0.15)
- Connettore API MySQL C-libmysqlclient (versione: 6.1.11)

| File system        | Descrizione                                                                                                               | Dimensioni (GB)             | Proprietario  | Gruppo  | Archiviazione                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Il file system per l'installazione dell'istanza di SAP BOBI, dell'applicazione Web Tomcat predefinita e dei driver di database (se necessario) | Linee guida per il ridimensionamento di SAP | bl1adm | sapsys | Disco Premium gestito-SSD |
| /usr/sap/frsinput  | La directory di montaggio è per i file condivisi in tutti gli host BOBI che verranno usati come directory del repository del file di input  | Esigenze aziendali         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | La directory di montaggio è per i file condivisi in tutti gli host BOBI che verranno usati come directory del repository dei file di output | Esigenze aziendali         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuire una macchina virtuale Linux tramite portale di Azure

In questa sezione verranno create due macchine virtuali (VM) con l'immagine del sistema operativo Linux per la piattaforma SAP BOBI. Di seguito sono riportati i passaggi di alto livello per creare macchine virtuali:

1. Creare un [gruppo di risorse](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Creare una [rete virtuale](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Non usare una singola subnet per tutti i servizi di Azure nella distribuzione della piattaforma SAP BI. In base all'architettura della piattaforma SAP BI, è necessario creare più subnet. In questa distribuzione verranno create tre subnet, ovvero subnet applicazione, subnet archivio repository file e subnet del gateway applicazione.
   - In Azure, il gateway applicazione e Azure NetApp Files devono sempre trovarsi in una subnet separata. Per altri dettagli, vedere [applicazione Azure gateway](../../../application-gateway/configuration-overview.md) e [linee guida per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) articolo sulla pianificazione della rete.

3. Creare un set di disponibilità.

   - Per ottenere la ridondanza per ogni livello nella distribuzione a istanze diverse, inserire le macchine virtuali per ogni livello in un set di disponibilità. Assicurarsi di separare i set di disponibilità per ogni livello in base all'architettura.

4. Creazione della macchina virtuale 1 **(azusbosl1).**

   - È possibile usare un'immagine personalizzata o scegliere un'immagine da Azure Marketplace. Vedere [distribuzione di una VM da Azure Marketplace per SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) o [distribuzione di una VM con un'immagine personalizzata per SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) in base alle esigenze.

5. Creazione della macchina virtuale 2 **(azusbosl2).**
6. Aggiungere un disco SSD Premium. Verrà usata come directory di installazione di SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Provisioning Azure NetApp Files

Prima di continuare con la configurazione di Azure NetApp Files, acquisire familiarità con la [documentazione dei file](../../../azure-netapp-files/azure-netapp-files-introduction.md)di Azure NetApp.

Azure NetApp Files è disponibile in diverse [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verificare se l'area di Azure selezionata offre Azure NetApp Files.

Usare la pagina [disponibilità Azure NetApp files per area di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) per verificare la disponibilità di Azure NetApp files in base all'area.

Richiedere l'onboarding a Azure NetApp Files eseguendo [la registrazione per Azure NetApp files le istruzioni](../../../azure-netapp-files/azure-netapp-files-register.md) prima di distribuire Azure NetApp files.

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files

Le istruzioni seguenti presuppongono che la [rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md)sia già stata distribuita. Le risorse Azure NetApp Files e le macchine virtuali in cui verranno montate le risorse di Azure NetApp Files devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering.

1. Se le risorse non sono già state distribuite, richiedere l' [onboarding per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Per creare un account NetApp nell'area di Azure selezionata, seguire le istruzioni riportate in [creare un account NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. Configurare un pool di capacità Azure NetApp Files seguendo le istruzioni riportate in [configurare un pool di capacità Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - L'architettura della piattaforma SAP BI presentata in questo articolo usa un singolo pool di capacità Azure NetApp Files al livello di servizio *Premium* . Per il server di repository di file di SAP BI in Azure, è consigliabile usare un [livello di servizio](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp files *Premium* o *ultra* .

4. Delegare una subnet a Azure NetApp Files, come descritto nelle istruzioni in [delegare una subnet a Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Distribuire Azure NetApp Files volumi seguendo le istruzioni riportate in [creare un volume NFS per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   Il volume e può essere distribuito come NFSv3 e NFSv 4.1, perché entrambi i protocolli sono supportati per la piattaforma SAP BOBI. Distribuire i volumi nella rispettiva subnet Azure NetApp Files. Gli indirizzi IP dei volumi di Azure NetApp vengono assegnati automaticamente.

Tenere presente che le risorse Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. Ad esempio, azusbobi-frsinput, azusbobi-frsoutput sono i nomi di volume e nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput sono i percorsi di file per i volumi Azure NetApp Files.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Considerazioni importanti

Quando si sta creando il Azure NetApp Files per il server del repository di file della piattaforma SAP BOBI, tenere presente quanto segue:

1. Il pool di capacità minimo è 4 TB (TiB).
2. La dimensione minima del volume è 100 gibibyte (GiB).
3. Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi di Azure NetApp Files devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peering](../../../virtual-network/virtual-network-peering-overview.md) nella stessa area. L'accesso ad Azure NetApp Files mediante peering VNET nella stessa area è ora supportato. L'accesso a NetApp di Azure sul peering globale non è ancora supportato.
4. La rete virtuale selezionata deve avere una subnet delegata a Azure NetApp Files.
5. Con i [criteri di esportazione](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)Azure NetApp files, è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via).
6. La funzionalità Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente, la funzionalità non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.
7. I volumi Azure NetApp Files possono essere distribuiti come volumi NFSv3 o NFSv4.1. Entrambi i protocolli sono supportati per le applicazioni della piattaforma SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Configurare i file System nei server Linux

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

**[A]** : il passaggio si applica a tutti gli host

### <a name="format-and-mount-sap-file-system"></a>Formattare e montare file system SAP

1. **[A]** elencare tutti i dischi collegati

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A] formattare un** dispositivo a blocchi per/usr/SAP

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A] creare la directory di** montaggio

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Ottiene l'UUID del dispositivo a blocchi

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A] mantenere la** voce di montaggio file System in/etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** montare file System

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Montare Azure NetApp Files volume

1. **[A]** creare le directory di montaggio

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A] configurare il** sistema operativo client per supportare il montaggio NFSv 4.1 **(applicabile solo se si usa NFSv 4.1)**

   Se si usano Azure NetApp Files volumi con il protocollo NFSv 4.1, eseguire la configurazione seguente in tutte le macchine virtuali, in cui è necessario montare Azure NetApp Files volumi NFSv 4.1.

   **Verificare le impostazioni del dominio NFS**

   Verificare che il dominio sia configurato come predefinito Azure NetApp Files dominio,  **defaultv4iddomain.com** e che il mapping sia impostato su **nessuno**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Assicurarsi di impostare il dominio NFS in/etc/idmapd.conf nella macchina virtuale in modo che corrisponda alla configurazione del dominio predefinito su Azure NetApp Files: **defaultv4iddomain.com**. Se si verifica una mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e il server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi NetApp di Azure montate nelle VM verranno visualizzate come nessuno.

   Verificare `nfs4_disable_idmapping`. Il valore deve essere impostato su **Y**. Per creare la struttura di directory in cui si trova `nfs4_disable_idmapping`, eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Aggiungere le voci di montaggio

   Se si usa NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Se si usa NFSv 4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** montare volumi NFS

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Configurare il database CMS-database di Azure per MySQL

Questa sezione fornisce informazioni dettagliate su come eseguire il provisioning di database di Azure per MySQL con portale di Azure. Vengono inoltre fornite istruzioni su come creare i database CMS e di controllo per la piattaforma SAP BOBI e un account utente per accedere al database.

Le linee guida sono applicabili solo se si usa il database di Azure per MySQL. Per altri database, vedere la documentazione di SAP o specifica del database per istruzioni.

### <a name="create-an-azure-database-for-mysql"></a>Creare un database di Azure per MySQL

Accedere a portale di Azure e seguire i passaggi descritti in questa [Guida introduttiva di database di Azure per MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Pochi punti da notare durante il provisioning di database di Azure per MySQL-

1. Selezionare la stessa area per database di Azure per MySQL in cui sono in esecuzione i server applicazioni della piattaforma SAP BI.

2. Scegliere una versione del database supportata basata su [Product Availability Matrix (PAM) per SAP BI](https://support.sap.com/pam) specifico per la versione di SAP Bobi. Segui le stesse linee guida per la compatibilità di MySQL AB in SAP PAM

3. In "calcolo e archiviazione" selezionare **Configura server** e selezionare il piano tariffario appropriato in base all'output di ridimensionamento.

4. L' **aumento automatico delle dimensioni di archiviazione** è abilitato per impostazione predefinita. Tenere presente che lo [spazio di archiviazione](../../../mysql/concepts-pricing-tiers.md#storage) può essere solo scalabile, non inattivo.

5. Per impostazione predefinita, il **periodo di conservazione del backup** è di sette giorni, ma è possibile [facoltativamente configurarlo](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) fino a 35 giorni.

6. Per impostazione predefinita, i backup di database di Azure per MySQL sono ridondanti localmente, quindi se si vuole eseguire il backup del server in un archivio con ridondanza **geografica** , selezionare con ridondanza geografica dalle **Opzioni di ridondanza dei backup**.

> [!NOTE]
> La modifica delle [Opzioni di ridondanza del backup](../../../mysql/concepts-backup.md#backup-redundancy-options) dopo la creazione del server non è supportata.

### <a name="configure-connection-security"></a>Configurare la sicurezza della connessione

Per impostazione predefinita, il server creato è protetto da un firewall e non è accessibile pubblicamente. Per consentire l'accesso alla rete virtuale in cui sono in esecuzione i server applicazioni SAP BI Platform, attenersi alla procedura seguente:  

1. Passare a risorse server nel portale di Azure e selezionare **sicurezza connessione** dal menu a sinistra per la risorsa server.
2. Selezionare **Sì** per **consentire l'accesso ai servizi di Azure**.
3. In regole VNET selezionare **Aggiungi rete virtuale esistente**. Selezionare la rete virtuale e la subnet del server applicazioni SAP BI Platform. È anche necessario fornire l'accesso a jump box o ad altri server da cui è possibile connettere [MySQL Workbench](../../../mysql/connect-workbench.md) a database di Azure per MySQL. MySQL Workbench verrà usato per creare un database CMS e di controllo
4. Una volta aggiunte le reti virtuali, selezionare **Salva**.

### <a name="create-cms-and-audit-database"></a>Creare un database CMS e di controllo

1. Scaricare e installare MySQL Workbench dal [sito web MySQL](https://dev.mysql.com/downloads/workbench/). Assicurarsi di installare MySQL Workbench nel server che può accedere al database di Azure per MySQL.

2. Connettersi al server con MySQL Workbench. Seguire le istruzioni descritte in questo [articolo](../../../mysql/connect-workbench.md#get-connection-information). Se il test della connessione ha esito positivo, si otterrà il messaggio seguente:

   ![Connessione SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Nella scheda query SQL eseguire la query seguente per creare lo schema per CMS e database di controllo.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;

4. Create user account to connect to schema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Per verificare i privilegi e i ruoli dell'account utente MySQL

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Installare il connettore API C MySQL (libmysqlclient) nel server Linux

Per accedere al database, il server applicazioni SAP BOBI richiede client/driver di database. Il connettore API MySQL C per Linux deve essere usato per accedere ai database CMS e di controllo. La connessione ODBC al database CMS non è supportata. Questa sezione fornisce istruzioni su come configurare il connettore API di MySQL C in Linux.

1. Vedere l'articolo [driver MySQL e strumenti di gestione compatibili con database di Azure per MySQL](../../../mysql/concepts-compatibility.md) , che descrive i driver compatibili con database di Azure per MySQL. Verificare il driver **MySQL Connector/C (libmysqlclient)** nell'articolo.

2. Fare riferimento a questo [collegamento](https://downloads.mysql.com/archives/c-c/) per scaricare i driver.

3. Selezionare il sistema operativo e scaricare il pacchetto RPM dei componenti condivisi del connettore MySQL. In questo esempio viene usata la versione del connettore MySQL-Connector-c-Shared-6.1.11.

4. Installare il connettore in tutte le istanze dell'applicazione SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Controllare il percorso di libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Impostare LD_LIBRARY_PATH per puntare alla `/usr/lib64` Directory per l'account utente che verrà utilizzato per l'installazione.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Preparazione server

Per i passaggi in questa sezione vengono usati i prefissi seguenti:

**[A]** : il passaggio si applica a tutti gli host.

1. **[A]** in base alla versione di Linux (SLES o RHEL), è necessario impostare i parametri del kernel e installare le librerie necessarie. Vedere la sezione **requisiti di sistema** nella Guida all'installazione della [piattaforma di Business Intelligence per UNIX](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** verificare che il fuso orario nel computer sia impostato correttamente. Vedere la [sezione requisiti aggiuntivi per UNIX e Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) nella Guida all'installazione.

3. **[A] creare un** account utente ( **BL1** ADM) e un gruppo (sapsys) in cui è possibile eseguire i processi in background del software. Utilizzare questo account per eseguire l'installazione di ed eseguire il software. L'account non richiede privilegi radice.

4. **[A]** impostare l'ambiente dell'account utente ( **BL1** ADM) per utilizzare le impostazioni locali UTF-8 supportate e assicurarsi che il software della console supporti i set di caratteri UTF-8. Per assicurarsi che il sistema operativo usi le impostazioni locali corrette, impostare le variabili di ambiente LC_ALL e LANG sulle impostazioni locali preferite nell'ambiente utente ( **BL1** ADM).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A] configurare l'** account utente ( **BL1** ADM).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Scaricare ed estrarre i supporti per la piattaforma SAP BusinessObjects BI da SAP Service Marketplace.

## <a name="installation"></a>Installazione

Controllare le impostazioni locali per l'account utente **BL1** ADM nel server

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Passare al supporto della piattaforma SAP BusinessObjects BI ed eseguire il comando seguente con l'utente **BL1** ADM-

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Seguire la Guida all'installazione della [piattaforma SAP Bobi](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) per UNIX, specifica della versione in uso. Pochi punti da notare durante l'installazione di SAP BOBI Platform.

- Nella schermata **Configura registrazione prodotto** è possibile usare la chiave di licenza temporanea per le soluzioni SAP BusinessObjects dalla nota SAP [1288121](https://launchpad.support.sap.com/#/notes/1288121) o è possibile generare il codice di licenza in SAP Service Marketplace

- Nella schermata **Selezione tipo di installazione** selezionare installazione **completa** sul primo server (azusbosl1), per altro server (azusbosl2) selezionare **personalizzato/Espandi** che espanderà l'installazione di bobi esistente.

- Nella schermata **Selezione database predefinito o esistente** selezionare **configura un database esistente**. verrà richiesto di selezionare CMS e database di controllo. Selezionare **MySQL** per il tipo di database CMS e controllare il tipo di database.

  È anche possibile selezionare nessun database di controllo, se non si vuole configurare il controllo durante l'installazione.

- Selezionare le opzioni appropriate nella **schermata selezionare il server applicazioni Web Java** in base all'architettura di SAP Bobi. In questo esempio è stata selezionata l'opzione 1, che installa il server Tomcat sulla stessa piattaforma SAP BOBI.

- Immettere le informazioni sul database CMS in **configurare il database del repository CMS-MySQL**. Input di esempio per le informazioni sul database CMS per l'installazione di Linux. Database di Azure per MySQL viene usato sulla porta predefinita 3306
  
  ![Distribuzione di SAP BOBI in Linux-database CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Opzionale Immettere le informazioni sul database di controllo in **Configure audit repository database-MySQL**. Input di esempio per le informazioni sul database di controllo per l'installazione di Linux.

  ![Distribuzione di SAP BOBI in Linux-database di controllo](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Per completare l'installazione, seguire le istruzioni e immettere gli input necessari.

Per la distribuzione a istanze diverse, eseguire il programma di installazione del secondo host (azusbosl2). Nella schermata **Selezione tipo di installazione** selezionare **personalizzata/Espandi** per espandere l'installazione di bobi esistente.

Nell'offerta database di Azure per MySQL viene usato un gateway per reindirizzare le connessioni alle istanze del server. Dopo che è stata stabilita la connessione, il client MySQL visualizza la versione di MySQL impostata nel gateway e non la versione effettiva in esecuzione nell'istanza del server MySQL. Per determinare la versione dell'istanza del server MySQL, usare il comando `SELECT VERSION();` dal prompt di MySQL. Quindi, nella console di gestione centrale (CMC), è possibile trovare una versione del database diversa, fondamentalmente la versione impostata sul gateway. Per altri dettagli, vedere le [versioni supportate del database di Azure per il server MySQL](../../../mysql/concepts-supported-versions.md) .

![Distribuzione di SAP BOBI nelle impostazioni di Linux-CMC](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Post-installazione

### <a name="tomcat-clustering---session-replication"></a>Clustering Tomcat-replica della sessione

Tomcat supporta il clustering di due o più server applicazioni per la replica e il failover della sessione. Le sessioni della piattaforma SAP BOBI vengono serializzate, una sessione utente può eseguire il failover senza problemi in un'altra istanza di Tomcat, anche in caso di errore del server applicazioni.

Ad esempio, se un utente è connesso a un server Web che ha esito negativo mentre l'utente si sposta in una gerarchia di cartelle nell'applicazione SAP BI. Con un cluster configurato correttamente, l'utente può continuare a spostarsi nella gerarchia di cartelle senza che venga reindirizzato alla pagina di accesso.

Nella nota SAP [2808640](https://launchpad.support.sap.com/#/notes/2808640), la procedura per configurare il clustering Tomcat viene fornita tramite il multicast. Tuttavia, in Azure il multicast non è supportato. Per fare in modo che il cluster Tomcat funzioni in Azure, è necessario usare [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (nota SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Per configurare il cluster Tomcat in Azure, vedere il Blog relativo al [clustering Tomcat usando l'appartenenza statica per SAP BUSINESSOBJECTS BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) nel Blog SAP.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Bilanciamento del carico del livello Web della piattaforma SAP BI

Nella distribuzione a istanze diverse di SAP BOBI, i server applicazioni Web Java (livello Web) sono in esecuzione in due o più host. Per distribuire il carico utente in modo uniforme tra i server Web, è possibile usare un servizio di bilanciamento del carico tra gli utenti finali e i server Web. In Azure è possibile usare Azure Load Balancer o applicazione Azure gateway per gestire il traffico verso i server applicazioni Web. Le informazioni dettagliate su ogni offerta sono illustrate nella sezione seguente.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (servizio di bilanciamento del carico basato su rete)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) è un servizio di bilanciamento del carico di livello 4 (TCP, UDP) ad alte prestazioni e a bassa latenza che distribuisce il traffico tra macchine virtuali integre. Un probe di integrità del servizio di bilanciamento del carico monitora una determinata porta in ogni VM e distribuisce il traffico solo a una o più macchine virtuali operative. È possibile scegliere un servizio di bilanciamento del carico pubblico o un servizio di bilanciamento del carico interno a seconda che si voglia che la piattaforma SAP BI sia accessibile da Internet o meno. Con ridondanza della zona, garantendo una disponibilità elevata tra zone di disponibilità.

Vedere la sezione Load Balancer interna nella figura seguente, in cui il server applicazioni Web viene eseguito sulla porta 8080, porta HTTP Tomcat predefinita, che verrà monitorata da Probe di integrità. Quindi, tutte le richieste in ingresso provenienti dagli utenti finali vengono reindirizzate ai server applicazioni Web (azusbosl1 o azusbosl2) nel pool back-end. Load Balancer non supporta la terminazione TLS/SSL, nota anche come offload TLS/SSL. Se si usa il servizio di bilanciamento del carico di Azure per distribuire il traffico tra server Web, è consigliabile usare Load Balancer Standard.

> [!NOTE]
> Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer per bilanciare il traffico tra server Web](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Gateway applicazione di Azure (servizio di bilanciamento del carico dell'applicazione Web)

Il [Gateway applicazione Azure (AGW)](../../../application-gateway/overview.md) fornisce il controller di distribuzione delle applicazioni (ADC) come servizio, che consente all'applicazione di indirizzare il traffico utente a uno o più server di applicazioni Web. Offre diverse funzionalità di bilanciamento del carico di livello 7, ad esempio offload TLS/SSL, Web Application Firewall (WAF), affinità di sessione basata su cookie e altre per le applicazioni.

Nella piattaforma SAP BI il gateway applicazione indirizza il traffico Web delle applicazioni alle risorse specificate in un pool back-end-azusbosl1 o azusbos2. Si assegna un listener a una porta, si creano regole e si aggiungono risorse a un pool back-end. Nella figura seguente, il gateway applicazione con indirizzo IP front-end privato (10.31.3.20) funge da punto di ingresso per gli utenti, gestisce le connessioni TLS/SSL (HTTPS-TCP/443) in ingresso, decrittografa TLS/SSL e passa la richiesta non crittografata (HTTP-TCP/8080) ai server nel pool back-end. Con la funzionalità di terminazione TLS/SSL incorporata, è sufficiente mantenere un certificato TLS/SSL nel gateway applicazione, che semplifica le operazioni.

![Gateway applicazione per il bilanciamento del traffico tra server Web](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Per configurare il gateway applicazione per il server Web SAP BOBI, è possibile fare riferimento al [bilanciamento del carico dei server Web SAP Bobi usando applicazione Azure gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) nel Blog di SAP.

> [!NOTE]
> Si consiglia di usare applicazione Azure gateway per bilanciare il carico del traffico verso il server Web, poiché fornisce funzionalità simili a quelle di offload SSL, centralizzare la gestione SSL per ridurre l'overhead di crittografia e decrittografia nel server, Round-Robin algoritmo per la distribuzione del traffico, funzionalità Web Application Firewall (WAF), disponibilità elevata e così via.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>Piattaforma SAP BusinessObjects BI-backup e ripristino

Backup e ripristino è un processo di creazione di copie periodiche di dati e applicazioni in un percorso separato. Quindi, può essere ripristinato o ripristinato allo stato precedente se i dati o le applicazioni originali vengono persi o danneggiati. È anche un componente essenziale di qualsiasi strategia di ripristino di emergenza aziendale.

Per sviluppare una strategia di backup e ripristino completa per la piattaforma BOBI di SAP, identificare i componenti che causano interruzioni del sistema o interruzioni nell'applicazione. Nella piattaforma SAP BOBI, il backup dei componenti seguenti è essenziale per proteggere l'applicazione.

- Directory di installazione di SAP BOBI (dischi Premium gestiti)
- Server repository file (Azure NetApp Files o file Premium di Azure)
- Database CMS (database di Azure per MySQL o database in una macchina virtuale di Azure)

La sezione seguente descrive come implementare la strategia di backup e ripristino per ogni componente nella piattaforma SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Backup & Restore per la directory di installazione di SAP BOBI

In Azure, il modo più semplice per eseguire il backup dei server applicazioni e di tutti i dischi collegati consiste nell'usare il servizio [backup di Azure](../../../backup/backup-overview.md) . Fornisce backup indipendenti e isolati per salvaguardare la distruzione accidentale dei dati nelle macchine virtuali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con la gestione predefinita dei punti di ripristino. La configurazione e la scalabilità sono semplici, i backup sono ottimizzati e possono essere ripristinati facilmente quando necessario.

Come parte del processo di backup, lo snapshot viene effettuato e i dati vengono trasferiti nell'insieme di credenziali dei servizi di ripristino senza alcun effetto sui carichi di lavoro di produzione. Lo snapshot offre un livello di coerenza diverso, come descritto nell'articolo [coerenza dello snapshot](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) . È anche possibile scegliere di eseguire il backup del subset dei dischi dati nella macchina virtuale usando la funzionalità di backup e ripristino dei dischi selettivi. Per altre informazioni, vedere documento di [backup delle VM di Azure](../../../backup/backup-azure-vms-introduction.md) e [domande frequenti-backup di macchine virtuali di Azure](../../../backup/backup-azure-vm-backup-faq.md).

#### <a name="backup--restore-for-file-repository-server"></a>Backup & Restore per il server del repository di file

Per **Azure NetApp files** , è possibile creare snapshot su richiesta e pianificare snapshot automatici usando i criteri di snapshot. Le copie snapshot forniscono una copia temporizzata del volume e. Per altre informazioni, vedere [gestire gli snapshot con Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**File di Azure** backup è integrato con il servizio [backup di Azure](../../../backup/backup-overview.md) nativo, che centralizza la funzione di backup e ripristino insieme al backup delle macchine virtuali e semplifica il lavoro operativo. Per altre informazioni, vedere backup e domande frequenti su [condivisione file di Azure](../../../backup/azure-file-share-backup-overview.md) [-eseguire il backup file di Azure](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Backup & Restore per il database CMS

Il database di Azure di MySQL è un'offerta DBaaS in Azure che crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o con ridondanza geografica configurato dall'utente. Il database di Azure di MySQL esegue il backup dei file di dati e del log delle transazioni. A seconda delle dimensioni massime di archiviazione supportate, accetta backup completi e differenziali (server di archiviazione max da 4 TB) o backup di snapshot (fino a un massimo di 16 TB di server di archiviazione). Questi backup consentono di ripristinare un server in qualsiasi punto nel tempo entro il periodo di memorizzazione del backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni, che è possibile [configurare facoltativamente](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) fino a tre giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Questi file di backup non sono esposti dall'utente e non possono essere esportati. Questi backup possono essere usati solo per le operazioni di ripristino nel database di Azure per MySQL. Per copiare un database, è possibile usare [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) . Per altre informazioni, vedere [backup e ripristino nel database di Azure per MySQL](../../../mysql/concepts-backup.md).

Per il database installato nelle macchine virtuali, è possibile usare gli strumenti di backup standard o [backup di Azure](../../../backup/sap-hana-db-about.md) per il database Hana. Anche se i servizi e gli strumenti di Azure non soddisfano i requisiti, è possibile usare altri strumenti o script di backup per creare un backup dei dischi.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Affidabilità della piattaforma SAP BusinessObjects BI

La piattaforma SAP BusinessObjects BI include livelli diversi, ottimizzati per attività e operazioni specifiche. Quando un componente di un livello non è più disponibile, l'applicazione SAP BOBI diventerà inaccessibile o alcune funzionalità dell'applicazione non funzioneranno. Quindi, è necessario assicurarsi che ogni livello sia progettato per essere affidabile per garantire l'operatività delle applicazioni senza alcuna distorsione dell'azienda.

Questa sezione è incentrata sulle opzioni seguenti per la piattaforma SAP BOBI:

- **Disponibilità elevata:** Una piattaforma a disponibilità elevata ha almeno due elementi all'interno dell'area di Azure per garantire l'operatività dell'applicazione se uno dei server diventa non disponibile.
- **Ripristino di emergenza:** Si tratta di un processo di ripristino della funzionalità dell'applicazione in caso di perdita irreversibile, ad esempio se l'intera area di Azure diventa non disponibile a causa di una calamità naturale.

L'implementazione di questa soluzione varia a seconda della natura della configurazione del sistema in Azure. Il cliente deve quindi personalizzare la disponibilità elevata e la soluzione di ripristino di emergenza in base ai requisiti aziendali.

### <a name="high-availability"></a>Disponibilità elevata

La disponibilità elevata si riferisce a un set di tecnologie che consente di ridurre al minimo le interruzioni IT offrendo la continuità aziendale di applicazioni/servizi tramite componenti ridondanti, a tolleranza di errore o protetti con failover all'interno dello stesso data center. In questo caso, i Data Center si trovano all'interno di un'area di Azure. Nell'articolo [architettura e scenari di disponibilità elevata per SAP](sap-high-availability-architecture-scenarios.md) sono disponibili informazioni iniziali sulle diverse tecniche di disponibilità elevata e sulle raccomandazioni offerte in Azure per le applicazioni SAP, che sono complementari alle istruzioni riportate in questa sezione.

A seconda del risultato di ridimensionamento della piattaforma SAP BOBI, è necessario progettare il paesaggio e determinare la distribuzione dei componenti di business intelligence in macchine virtuali e subnet di Azure. Il livello di ridondanza nell'architettura distribuita dipende dall'obiettivo del tempo di ripristino (RTO) e dall'obiettivo del punto di ripristino (RPO) dell'azienda. La piattaforma SAP BOBI include diversi livelli e componenti in ogni livello, che devono essere progettati per garantire la ridondanza. Quindi, in caso di errore di un componente, non ci sono interruzioni per l'applicazione SAP BOBI. Ad esempio,

- Server applicazioni ridondanti, ad esempio server applicazioni BI e server Web
- Componenti univoci come database CMS, server repository file, Load Balancer

La sezione seguente descrive come ottenere la disponibilità elevata in ogni componente della piattaforma SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Disponibilità elevata per i server applicazioni

Per i server di applicazioni Web e BI, indipendentemente dal fatto che siano installati separatamente o insieme, non è necessaria una soluzione a disponibilità elevata specifica. È possibile ottenere la disponibilità elevata grazie alla ridondanza, ovvero configurando più istanze di server Web e BI in diverse macchine virtuali di Azure.

Per ridurre l'effetto del tempo di inattività dovuto a uno o più eventi, è consigliabile seguire la procedura di disponibilità elevata per i server applicazioni in esecuzione in più macchine virtuali.

- Usare zone di disponibilità per proteggere gli errori del Data Center.
- Configurare più macchine virtuali in un set di disponibilità per la ridondanza.
- Usare Managed Disks per le macchine virtuali in un set di disponibilità.
- Configurare ogni livello dell'applicazione in set di disponibilità separati.

Per altre informazioni, vedere [gestire la disponibilità delle macchine virtuali Linux](../../manage-availability.md)

#### <a name="high-availability-for-cms-database"></a>Disponibilità elevata per il database CMS

Se si usa il servizio database As a Service (DBaaS) di Azure per il database CMS, per impostazione predefinita viene fornito un Framework a disponibilità elevata. È sufficiente selezionare le funzionalità relative a disponibilità elevata, ridondanza e resilienza di area e servizio senza che sia necessario configurare componenti aggiuntivi. Per altre informazioni sul contratto di contratto dell'offerta DBaaS supportata in Azure, vedere [disponibilità elevata in database di Azure per MySQL](../../../mysql/concepts-high-availability.md) e [disponibilità elevata per il database SQL di Azure](../../../azure-sql/database/high-availability-sla.md)

Per altre distribuzioni DBMS per database CMS, vedere le [guide alla distribuzione di DBMS per il carico di lavoro SAP](dbms_guide_general.md), che fornisce informazioni sulla distribuzione DBMS diversa e il relativo approccio per ottenere la disponibilità elevata.

#### <a name="high-availability-for-file-repository-server"></a>Disponibilità elevata per il server del repository di file

File repository server (FRS) si riferisce alle directory del disco in cui vengono archiviati contenuti come report, universi e connessioni. Viene condiviso tra tutti i server applicazioni del sistema. Quindi, è necessario assicurarsi che sia a disponibilità elevata.

In Azure è possibile scegliere [file Premium di Azure](../../../storage/files/storage-files-introduction.md) o [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) per la condivisione file progettata per essere a disponibilità elevata e durevole per natura. Per ulteriori informazioni, vedere la sezione relativa alla [ridondanza](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy) per file di Azure.

> [!NOTE]
> Il protocollo SMB per File di Azure è disponibile a livello generale, ma il supporto del protocollo NFS per File di Azure è attualmente in fase di anteprima. Per ulteriori informazioni, vedere il [supporto per NFS 4,1 per file di Azure è ora disponibile in anteprima](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Poiché questo servizio di condivisione file non è disponibile in tutte le aree, assicurarsi di fare riferimento ai [prodotti disponibili in base al sito dell'area](https://azure.microsoft.com/en-us/global-infrastructure/services/) per trovare informazioni aggiornate. Se il servizio non è disponibile nella propria area geografica, è possibile creare un server NFS dal quale è possibile condividere il file system all'applicazione SAP BOBI. Tuttavia, sarà necessario prendere in considerazione anche la disponibilità elevata.

#### <a name="high-availability-for-load-balancer"></a>Disponibilità elevata per il servizio di bilanciamento del carico

Per distribuire il traffico tra server Web, è possibile usare Azure Load Balancer o applicazione Azure gateway. La ridondanza per uno dei due del servizio di bilanciamento del carico può essere eseguita in base allo SKU scelto per la distribuzione.

- Per Azure Load Balancer, è possibile ottenere la ridondanza configurando Load Balancer Standard front-end con ridondanza della zona. Per ulteriori informazioni, vedere [Load Balancer standard e zone di disponibilità](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Per il gateway applicazione, è possibile ottenere la disponibilità elevata in base al tipo di livello selezionato durante la distribuzione.
  - lo SKU V1 supporta scenari a disponibilità elevata quando sono state distribuite due o più istanze. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Con questo SKU è quindi possibile ottenere la ridondanza all'interno della zona
  - lo SKU V2 garantisce automaticamente che le nuove istanze vengano distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza della zona, le istanze più recenti vengono anche distribuite tra le zone di disponibilità per offrire resilienza dagli errori delle zone. Per altri dettagli, vedere la [scalabilità automatica e il gateway applicazione con ridondanza della zona V2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Architettura di riferimento a disponibilità elevata per la piattaforma SAP BusinessObjects BI

Sotto l'architettura di riferimento viene descritta la configurazione della piattaforma SAP BOBI usando il set di disponibilità, che fornisce la ridondanza e la disponibilità delle VM all'interno della zona. L'architettura illustra l'uso di diversi servizi di Azure, come applicazione Azure gateway, Azure NetApp Files e database di Azure per MySQL per la piattaforma BOBI di SAP, che offre ridondanza incorporata, che riduce la complessità della gestione di soluzioni a disponibilità elevata diverse.

Nella figura seguente il traffico in ingresso (HTTPS-TCP/443) viene sottoposto a bilanciamento del carico usando applicazione Azure SKU del gateway V1, che è a disponibilità elevata quando viene distribuito in due o più istanze. Più istanze di server Web, server di gestione e server di elaborazione vengono distribuite in macchine virtuali separate per ottenere la ridondanza e ogni livello viene distribuito in set di disponibilità separati. Azure NetApp Files dispone di ridondanza incorporata all'interno data center, quindi i volumi e per il server repository file saranno a disponibilità elevata. Il provisioning del database CMS viene effettuato nel database di Azure per MySQL (DBaaS) con disponibilità elevata intrinseca. Per altre informazioni, vedere la guida alla [disponibilità elevata in database di Azure per MySQL](../../../mysql/concepts-high-availability.md) .

![Ridondanza della piattaforma SAP BusinessObjects BI con i set di disponibilità](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

L'architettura precedente fornisce informazioni dettagliate sul modo in cui è possibile eseguire la distribuzione di SAP BOBI in Azure. Ma non copre tutte le opzioni di configurazione possibili per la piattaforma SAP BOBI in Azure. I clienti possono personalizzare la distribuzione in base ai requisiti aziendali, scegliendo prodotti/servizi diversi per diversi componenti, ad esempio Load Balancer, server repository file e DBMS.

In diverse aree di Azure sono disponibili zone di disponibilità, che significa che dispone di una fornitura indipendente di alimentazione, raffreddamento e rete. Consente ai clienti di distribuire l'applicazione tra due o tre zone di disponibilità. Per il cliente che desidera ottenere una disponibilità elevata tra AZs può distribuire la piattaforma SAP BOBI tra le zone di disponibilità, assicurandosi che ogni componente nell'applicazione sia con ridondanza della zona.

### <a name="disaster-recovery"></a>Ripristino di emergenza

Le istruzioni in questa sezione illustrano la strategia per fornire protezione per il ripristino di emergenza per la piattaforma SAP BOBI. Integra il [ripristino di emergenza per](../../../site-recovery/site-recovery-sap.md) il documento SAP, che rappresenta le risorse primarie per l'approccio globale di ripristino di emergenza di SAP.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Architettura di ripristino di emergenza di riferimento per la piattaforma SAP BusinessObjects BI

Questa architettura di riferimento sta eseguendo la distribuzione a istanze diverse della piattaforma SAP BOBI con server applicazioni ridondanti. Per il ripristino di emergenza, è consigliabile eseguire il failover di tutti i livelli in un'area secondaria. Ogni livello usa una strategia diversa per fornire protezione per il ripristino di emergenza.

![Ripristino di emergenza della piattaforma SAP BusinessObjects BI](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Bilanciamento del carico

Load Balancer viene usato per distribuire il traffico tra i server applicazioni Web della piattaforma SAP BOBI. Per ottenere il ripristino di emergenza per applicazione Azure gateway, implementare l'installazione parallela del gateway applicazione nell'area secondaria.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Macchine virtuali che eseguono server applicazioni Web e BI

Azure Site Recovery servizio può essere usato per replicare le macchine virtuali che eseguono i server applicazioni Web e BI nell'area secondaria. Replica i server nell'area secondaria, in modo che, in caso di emergenze e interruzioni, sia possibile eseguire facilmente il failover sull'ambiente replicato e continuare a lavorare

#### <a name="file-repository-servers"></a>Server repository di file

- **Azure NetApp files** fornisce volumi NFS e SMB, quindi è possibile usare qualsiasi strumento di copia basato su file per replicare i dati tra le aree di Azure. Per altre informazioni su come copiare un volume e in un'altra area, vedere [domande frequenti sulla Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  È possibile usare Azure NetApp Files la replica tra aree, attualmente in [Anteprima](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) che usa la tecnologia NetApp SnapMirror®. Quindi, solo i blocchi modificati vengono inviati in rete in un formato compresso ed efficiente. Questa tecnologia proprietaria riduce al minimo la quantità di dati necessari per la replica tra le aree, che consente di risparmiare sui costi di trasferimento dei dati. Consente inoltre di ridurre il tempo di replica, in modo che sia possibile ottenere un obiettivo del punto di ripristino più piccolo (RPO). Per ulteriori informazioni, vedere [requisiti e considerazioni per l'utilizzo della replica tra aree](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) .

- **I file Premium di Azure** supportano solo localmente ridondante (con ridondanza locale) e l'archiviazione con ridondanza della zona (ZRS). Per la strategia di ripristino di emergenza di file Premium di Azure, è possibile usare [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/) per copiare i file in un altro account di archiviazione in un'area diversa. Per altre informazioni, vedere [ripristino di emergenza e failover dell'account di archiviazione](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>Database CMS

Database di Azure per MySQL offre più opzioni per ripristinare il database in caso di emergenza. Scegliere l'opzione appropriata per l'azienda.

- Abilita le repliche di lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. È possibile eseguire la replica dal server di origine a un massimo di cinque repliche. Le repliche di lettura vengono aggiornate in modo asincrono usando la tecnologia di replica dei log binari di MySQL. Le repliche sono nuovi server da gestire in modo simile ai normali server di Database di Azure per MySQL. Per altre informazioni sulle repliche di lettura, sulle aree disponibili, sulle restrizioni e su come eseguire il failover, [vedere l'articolo sui concetti relativi alle repliche](../../../mysql/concepts-read-replicas.md).

- Usare la funzionalità di ripristino geografico di database di Azure per MySQL che ripristina il server usando i backup con ridondanza geografica. Questi backup sono accessibili anche quando l'area in cui è ospitato il server è offline. È possibile eseguire il ripristino da questi backup a qualsiasi altra area e riportare il server online.

  > [!NOTE]
  > Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica. La modifica delle **Opzioni di ridondanza del backup** dopo la creazione del server non è supportata. Per altre informazioni, vedere l'articolo relativo alla [ridondanza dei backup](../../../mysql/concepts-backup.md#backup-redundancy-options) .

Di seguito è riportata la raccomandazione per il ripristino di emergenza di ogni livello utilizzato in questo esempio.

| Livelli della piattaforma SAP BOBI   | Recommendation                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Gateway applicazione di Azure | Configurazione parallela del gateway applicazione nell'area secondaria                                                |
| Server applicazioni Web   | Eseguire la replica tramite Site Recovery                                                                         |
| Server applicazioni BI    | Eseguire la replica tramite Site Recovery                                                                         |
| Azure NetApp Files        | Strumento di copia basato su file per replicare i dati nell'area secondaria **o** nella replica e tra aree (anteprima) |
| Database di Azure per MySQL  | Repliche di lettura tra più aree **o** ripristino del backup da backup con ridondanza geografica.                             |

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il ripristino di emergenza per una distribuzione di app SAP a più livelli](../../../site-recovery/site-recovery-sap.md)
- [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP](planning-guide.md)
- [Distribuzione di Macchine virtuali di Azure per SAP](deployment-guide.md)
- [Distribuzione DBMS di Macchine virtuali di Azure per SAP](dbms-guide.md)
