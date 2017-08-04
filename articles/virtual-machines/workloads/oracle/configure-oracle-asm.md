---
title: Configurare Oracle ASM su una macchina virtuale Linux in Azure | Microsoft Docs
description: Attivare e mettere in funzione rapidamente Oracle ASM nell'ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8290885cd4f3d2faede2900971cd7a5b5a59f5bc
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configurare Oracle ASM su una macchina virtuale Linux in Azure  

Questo articolo descrive come installare e configurare Oracle Automatic Storage Management (Oracle ASM) in una macchina virtuale Linux di Oracle in Azure.

Prima di iniziare, assicurarsi che l'interfaccia della riga di comando di Azure sia installata. Per altre informazioni, vedere la [guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparare l'ambiente

### <a name="sign-in-to-azure"></a>Accedere ad Azure 

Per accedere alla sottoscrizione di Azure nell'interfaccia della riga di comando di Azure, usare il comando [az login](/cli/azure/#login). Seguire quindi le istruzioni visualizzate sullo schermo.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un gruppo di risorse, usare il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella località westus.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>Creare una macchina virtuale

Per creare una macchina virtuale da usare con Oracle ASM, completare la procedura seguente:

1. Per crea una macchina virtuale usare il comando [az vm create](/cli/azure/vm#create). 

   L'esempio seguente crea una macchina virtuale denominata myVM. Crea anche le chiavi SSH se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

   ```azurecli
   az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
   ```

   In seguito alla creazione della VM, l'interfaccia della riga di comando di Azure visualizza informazioni simili a quelle dell'esempio seguente. Notare il valore di `publicIpAddress`. Questo indirizzo verrà usato per accedere alla macchina virtuale.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "westus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

2. Aggiungere i dischi da usare per la configurazione di Oracle ASM:

   ```azurecli
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM  --disk myDataDisk4 --new --size-gb 50
   ```

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Per creare una sessione SSH con la VM, usare il comando seguente. Sostituire l'indirizzo IP con il valore di `publicIpAddress` della macchina virtuale.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installare Oracle ASM

Per installare Oracle ASM, completare la procedura seguente. 

Per altre informazioni sull'installazione di Oracle ASM, vedere [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html) (Download di Oracle ASMLib per Oracle Linux 6).  

1. Eseguire `yum list`:

   ```bash
   $ sudo su -
   # yum list
   ```
   La prima volta che si esegue `yum list`, il caricamento potrebbe richiedere alcuni minuti.

2. Eseguire questi comandi aggiuntivi:

   ```bash
   # yum list | grep oracleasm
   # yum -y install kmod-oracleasm.x86_64
   # yum -y install oracleasm-support.x86_64
   # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
   # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
   # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Verificare che Oracle ASM sia installato:

   ```bash
   # rpm -qa |grep oracleasm
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
   ```

4. Aggiungere utenti e gruppi:

   ```bash
   # groupadd -g 54345 asmadmin
   # groupadd -g 54346 asmdba
   # groupadd -g 54347 asmoper
   # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
   # usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verificare gli utenti e i gruppi:

   ```bash
   # id grid
   uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
   ```

6. Creare una cartella e modificare il proprietario:

   ```bash
   # mkdir /u01/app/grid
   # chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurare Oracle ASM

Per questa esercitazione, l'utente predefinito è *grid* mentre il gruppo predefinito è *asmadmin*. Assicurarsi che l'utente *oracle* faccia parte del gruppo asmadmin. Per configurare l'installazione di Oracle ASM, completare la procedura seguente:

1. Impostare il driver della libreria di Oracle ASM:

   ```bash
   # /usr/sbin/oracleasm configure -i

   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Visualizzare la configurazione del disco:
   ```bash
   # cat /proc/partitions
   ```

3. Formattare il disco:

   ```bash
   # fdisk /dev/sdc
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Ripetere il passaggio precedente per /dev/sdd, /dev/sde e /dev/sdf.

5. Controllare la configurazione del disco:

   ```bash
   # cat /proc/partitions
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11        0    1048575 sr0
   ```

6. Controllare lo stato del servizio Oracle ASM:

   ```bash
   # service oracleasm status
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   ```

7. Avviare il servizio Oracle ASM:

   ```bash
   # service oracleasm start
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

8. Creare dischi Oracle ASM:

   ```bash
   # service oracleasm createdisk ASMSP /dev/sdc1
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

   # service oracleasm createdisk DATA /dev/sdd1
   Marking disk "DATA" as an ASM disk:                        [  OK  ]

   # service oracleasm createdisk DATA1 /dev/sde1
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]

   # service oracleasm createdisk FRA /dev/sdf1
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

9. Elencare i dischi Oracle ASM:

   ```bash
   # service oracleasm listdisks
   ASMSP
   DATA
   DATA1
   FRA
   ```

10. Modificare le password per gli utenti root, oracle e grid. Le password verranno usate in un secondo momento, durante l'installazione.

    ```bash
    # passwd oracle
    # passwd grid
    # passwd root
    ```

11. Modificare l'autorizzazione della cartella:

    ```bash
    # chmod -R 775 /opt
    # chown grid:oinstall /opt
    # chown oracle:oinstall /dev/sdc1
    # chown oracle:oinstall /dev/sdd1
    # chown oracle:oinstall /dev/sde1
    # chown oracle:oinstall /dev/sdf1
    # chmod 600 /dev/sdc1
    # chmod 600 /dev/sdd1
    # chmod 600 /dev/sde1
    # chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Scaricare e preparare Oracle Grid Infrastructure

Per scaricare e preparare il software Oracle Grid Infrastructure, completare la procedura seguente:

1. Scaricare Oracle Grid Infrastructure dalla [pagina di download di Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Sotto il download denominato **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** (Oracle Database 12c versione 1 Grid Infrastructure (12.1.0.2.0) per Linux x86-64) scaricare i due file con estensione ZIP.

2. Dopo aver scaricato i file con estensione ZIP nel computer client, è possibile usare il protocollo per la copia di sicurezza, SCP, per copiare i file nella macchina virtuale:

   ```bash
   scp *.zip <publicIpAddress>:<folder>
   ```

3. Spostare i file con estensione ZIP nella cartella /opt. Quindi, modificare il proprietario dei file:

   ```bash
   # mv <folder>/*.zip /opt
   # cd /opt
   # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Decomprimere i file. A tale scopo, installare lo strumento di decompressione di Linux se non è già installato.
   
   ```bash
   # yum install unzip
   # unzip linuxamd64_12102_grid_1of2.zip
   # unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Modificare l'autorizzazione:
   
   ```bash
   # chown -R grid:oinstall /opt/grid
   ```

6. Disattivare il firewall:
   
   ```bash
   # service iptables status
   # service iptables stop
   ```

7. Controllare lo spazio di swapping disponibile. Per installare Grid sono necessari almeno 6,8 GB di spazio di swapping. Nelle macchine virtuali Linux in Azure lo swapping non è abilitato e configurato per impostazione predefinita.

È consigliabile usare waagent per configurare lo spazio di swapping in modo che venga sempre creato nel disco temporaneo. Per altre informazioni, vedere [Come aggiungere un file di scambio in macchine virtuali di Azure per Linux](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Preparare il client e la VM per l'esecuzione di x11, solo per i client di Windows
Si tratta di un passaggio facoltativo. Può essere ignorato se si usa un client Linux o se x11 è già configurato.

1. [Scaricare PuTTY](http://www.putty.org/) e [Xming](https://xming.en.softonic.com/) sul computer Windows.

2. Dopo aver installato PuTTY, nella cartella PuTTY, ad esempio, C:\Programmi\PuTTY, eseguire puttygen.exe, il generatore di chiavi PuTTY.

3. Nel generatore di chiavi PuTTY:
   
   1. Generare una chiave selezionando il pulsante **Genera**.
   2. Copiare il contenuto della chiave, con Ctrl+C.
   3. Selezionare il pulsante **Save private key** (Salva la chiave privata).
   4. Ignorare l'avviso che viene visualizzato e quindi selezionare **OK**.

   ![Screenshot del generatore di chiavi PuTTY](./media/oracle-asm/puttykeygen.png)

4. Nella macchina virtuale eseguire questi comandi:

   ```bash
   # sudo su - grid
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Creare un file denominato authorized_keys. Incollare il contenuto della chiave in questo file e quindi salvare il file.

   > [!NOTE]
   > La chiave deve contenere la stringa `ssh-rsa`. In aggiunta, il contenuto della chiave deve essere una singola riga di testo.
   >  

6. Avviare PuTTY. Nel pannello **Categoria** andare in **Connessione** > **SSH** > **Autenticazione**. Nella casella **Private key file for authentication** (File della chiave privata per l'autenticazione) individuare la chiave generata in precedenza.

   ![Screenshot delle opzioni di autenticazione SSH](./media/oracle-asm/setprivatekey.png)

7. Nel pannello **Categoria** andare in **Connessione** > **SSH** > **X11**. Selezionare la casella di controllo **Enable X11 forwarding** (Abilita l'inoltro di X11).

   ![Screenshot delle opzioni di inoltro X11 SSH](./media/oracle-asm/enablex11.png)

8. Nel pannello **Categoria** andare in **Sessione**. Immettere le informazioni sull'host e quindi selezionare **Apri**.

   ![Screenshot delle opzioni per la sessione PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installare Oracle Grid Infrastructure

Per installare Oracle Grid Infrastructure, completare la procedura seguente:

1. Accedere come utente grid. L'accesso non dovrebbe richiedere una password. 

   > [!NOTE]
   > Assicurarsi che Xming sia in esecuzione prima di iniziare l'installazione.

   ```bash
   $ cd /opt/grid
   $ ./runInstaller
   ```

   Verrà visualizzata la finestra Oracle Grid Infrastructure 12c Release 1 Installer (Programma di installazione di Oracle Grid Infrastructure 12c versione 1). L'avvio del programma di installazione potrebbe richiedere alcuni minuti.

2. Nella pagina **Select Installation Option** (Selezionare le opzioni di installazione) selezionare **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Installare e configurare Oracle Grid Infrastructure su un server autonomo).

   ![Screenshot della pagina del programma di installazione Select Installation Option (Selezionare le opzioni di installazione)](./media/oracle-asm/install01.png)

3. Nella pagina **Select Product Languages** (Selezionare le lingue del prodotto) selezionare **English** (Inglese) o la lingua desiderata.

   ![Screenshot della pagina del programma di installazione Select Product Language (Selezionare le lingue del prodotto)](./media/oracle-asm/install02.png)

4. Nella pagina **Create ASM Disk Group** (Creare il gruppo di dischi ASM):
   - Immettere un nome per il gruppo di dischi.
   - In **Redundancy** (Ridondanza) selezionare **External** (Esterna).
   - In **Allocation Unit Size** (Dimensioni dell'unità di allocazione) selezionare **4**.
   - In **Add Disks** (Aggiungi dischi) selezionare **ORCLASMSP**.

   ![Screenshot della pagina del programma di installazione Create ASM Disk Group (Creare il gruppo di dischi ASM)](./media/oracle-asm/install03.png)

5. Nella pagina **Specify ASM Password** (Specificare la password ASM) selezionare l'opzione **Use same passwords for these accounts** (Usare le stesse password per questi account) e immettere una password.

   ![Screenshot della pagina del programma di installazione Specify ASM Password (Specificare la password ASM)](./media/oracle-asm/install04.png)

6. Questa operazione è facoltativa. Nella pagina **Specify Management Options** (Specificare le opzioni di gestione) selezionare la casella di controllo **Register with Enterprise Manager (EM) Cloud Control** (Registrati al controllo cloud Enterprise Manager, EM).

   ![Screenshot della pagina del programma di installazione Specify Management Options (Specificare le opzioni di gestione)](./media/oracle-asm/install05.png)

7. Nella pagina **Privileged Operating System Groups** (Gruppi del sistema operativo con privilegi) usare le impostazioni predefinite.

   ![Screenshot della pagina del programma di installazione Privileged Operating System Groups (Gruppi del sistema operativo con privilegi)](./media/oracle-asm/install06.png)

8. Nella pagina **Specify Installation Location** (Specificare il percorso di installazione) usare le impostazioni predefinite.

   ![Screenshot della pagina del programma di installazione Specify Installation Location (Specificare il percorso di installazione)](./media/oracle-asm/install07.png)

9. Nella pagina **Create Inventory** (Creare l'inventario) immettere o passare al percorso della cartella.

   ![Screenshot della pagina del programma di installazione Create Inventory (Creare l'inventario)](./media/oracle-asm/install08.png)

10. Nella pagina **Root script execution configuration** (Configurazione di esecuzione dello script principale) selezionare la casella di controllo **Automatically run configuration scripts** (Eseguire automaticamente gli script di configurazione). Selezionare quindi l'opzione **Use "root" user credential** (Usa le credenziali dell'utente "root") e immettere la password dell'utente root.

    ![Screenshot della pagina del programma di installazione Root script execution configuration (Configurazione di esecuzione dello script principale)](./media/oracle-asm/install09.png)

11. Nella pagina **Perform Prerequisite Checks** (Eseguire i controlli dei prerequisiti) selezionare **Fix & Check Again** (Correggi e controlla di nuovo).

    ![Screenshot della pagina del programma di installazione Perform Prerequisite Checks (Eseguire i controlli dei prerequisiti)](./media/oracle-asm/install10.png)

12. Nella finestra di dialogo **Fixup Script** (Correggi script) selezionare **OK**.

    ![Screenshot della finestra di dialogo Fixup Script (Correggi script)](./media/oracle-asm/install11.png)

13. Nella pagina **Summary** (Riepilogo) rivedere le impostazioni selezionate e quindi selezionare **Install** (Installa).

    ![Screenshot della pagina del programma di installazione Summary (Riepilogo)](./media/oracle-asm/install12.png)

14. Viene visualizzata una finestra di dialogo di avviso. Selezionare **Yes** (Sì) per continuare.

    ![Screenshot della finestra di dialogo di avviso](./media/oracle-asm/install14.png)

15. Nella pagina **Finish** (Fine) selezionare **Chiudi** per completare l'installazione.

    ![Screenshot della pagina del programma di installazione Finish (Fine)](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Configurare l'installazione di Oracle ASM

Per configurare l'installazione di Oracle ASM, completare la procedura seguente:

1. Accedere come utente grid dalla sessione X11:

   ```bash
   $ cd /u01/app/grid/product/12.1.0/grid/bin
   $ ./asmca
   ```

   Verrà visualizzato ASM Configuration Assistant (Assistente alla configurazione di ASM).

2. Nella finestra di dialogo **Configure ASM: Disk Groups** (Configura ASM: gruppi di dischi) selezionare il pulsante **Create** (Crea) e quindi selezionare **Show Advanced Options** (Mostra opzioni avanzate).

   ![Screenshot della finestra di dialogo Configure ASM: Disk Groups (Configura ASM: gruppi di dischi)](./media/oracle-asm/asm01.png)

3. Nella finestra di dialogo **Create Disk Group (Creare il gruppo di dischi)**:

   - Immettere il nome del gruppo di dischi.
   - In **Select Member Disks** (Selezionare i dischi membri) selezionare **ORCL_DATA** e **ORCL_DATA1**.
   - In **Allocation Unit Size** (Dimensioni dell'unità di allocazione) selezionare **4**.

   ![Screenshot della finestra di dialogo Create Disk Group (Creare il gruppo di dischi)](./media/oracle-asm/asm02.png)

4. Nella finestra di dialogo **Configure ASM: Disk Groups** (Configura ASM: gruppi di dischi) selezionare il pulsante **Create** (Crea) e quindi selezionare **Show Advanced Options** (Mostra opzioni avanzate).

   ![Screenshot della finestra di dialogo Configure ASM: Disk Groups (Configura ASM: gruppi di dischi)](./media/oracle-asm/asm03.png)

5. Nella finestra di dialogo **Create Disk Group (Creare il gruppo di dischi)**:

   - Immettere il nome del gruppo di dischi.
   - In **Redundancy** (Ridondanza) selezionare **External** (Esterna).
   - In **Select Member Disks** (Selezionare i dischi membri) selezionare **ORCL_DATA**.
   - In **Allocation Unit Size** (Dimensioni dell'unità di allocazione) selezionare **4**.

   ![Screenshot della finestra di dialogo Create Disk Group (Creare il gruppo di dischi)](./media/oracle-asm/asm04.png)

6. Selezionare **Exit** (Esci) per chiudere ASM Configuration Assistant (Assistente alla configurazione di ASM).

   ![Screenshot della finestra di dialogo Configure ASM: Disk Groups (Configura ASM: gruppi di dischi) con il pulsante Exit (Esci)](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Creare il database

Il software Oracle è già installato nell'immagine di Azure Marketplace. Per installare il database, completare i passaggi seguenti:

1. Passare gli utenti all'utente principale di Oracle e inizializzare il listener per la registrazione:

   ```bash
   $ su - oracle
   Password:
   $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   $ ./dbca
    ```
   Si apre Configuration Assistant (Assistente alla configurazione).

2. Nella pagina **Database Operation** (Operazioni del database) selezionare **Create Database** (Crea database).

   ![Schermata della pagina Database Operation (Operazioni del database)](./media/oracle-asm/createdb01.png)
  
3. Nella pagina **Creation Mode** (Modalità di creazione):

   - Immettere un nome per il database.
   - Per **Storage Type** (tipo di archiviazione) selezionare **Automatic Storage Management (ASM)** (Gestione archiviazione automatica, ASM).
   - Per **Database Files Location** (Percorso file del database) passare alla cartella che si intende usare.
   - Per **Fast Recovery Area** (Area di ripristino rapido) passare alla cartella che si intende usare.

   ![Schermata della pagina Creation Mode (Modalità di creazione)](./media/oracle-asm/createdb02.png)

4. Nella pagina **Summary** (Riepilogo) rivedere le impostazioni selezionate e quindi selezionare **Finish** (Fine) per creare il database.

   ![Schermata della pagina Summary (Riepilogo)](./media/oracle-asm/createdb03.png)

5. Questa operazione è facoltativa. Nella pagina **Finish** (Fine) per modificare le password, selezionare **Password Management** (Gestione delle password).

   ![Schermata della pagina Finish (Fine)](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Quando la VM non è più necessaria, è possibile usare il comando seguente per rimuovere il gruppo di risorse, la VM e tutte le risorse correlate:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: creare macchine virtuali a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)

