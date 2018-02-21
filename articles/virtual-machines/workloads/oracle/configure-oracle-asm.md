---
title: Configurare Oracle ASM su una macchina virtuale Linux in Azure | Microsoft Docs
description: Attivare e mettere in funzione rapidamente Oracle ASM nell'ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.openlocfilehash: 0e34a188271a5ac2fb6cb34a088ec3f650be6cab
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configurare Oracle ASM su una macchina virtuale Linux in Azure  

Le macchine virtuali di Azure offrono un ambiente di elaborazione completamente configurabile e flessibile. In questa esercitazione viene descritta la distribuzione della macchina virtuale di Azure base in combinazione con l'installazione e la configurazione di Oracle Automated Storage Management (ASM).  Si apprenderà come:

> [!div class="checklist"]
> * Creare e connettersi a una macchina virtuale per database Oracle
> * Installare e configurare Oracle Automated Storage Management
> * Installare e configurare l'infrastruttura di Oracle Grid
> * Inizializzare l'installazione di Oracle ASM
> * Creare un database Oracle gestito da ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Preparare l'ambiente

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per creare un gruppo di risorse, usare il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. In questo esempio, un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Creare una macchina virtuale

Per creare una macchina virtuale basata sull'immagine del database Oracle e configurarla per l'utilizzo di Oracle ASM, utilizzare il comando [az vm create](/cli/azure/vm#az_vm_create). 

Nell'esempio seguente viene creata una macchina virtuale denominata myVM che ha una dimensione Standard_DS2_v2 con quattro dischi dati collegati da 50 GB ciascuno. Crea anche le chiavi SSH se non esistono già nella posizione predefinita.  Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

In seguito alla creazione della VM, l'interfaccia della riga di comando di Azure visualizza informazioni simili a quelle dell'esempio seguente. Notare il valore di `publicIpAddress`. Questo indirizzo verrà usato per accedere alla macchina virtuale.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Per creare una sessione SSH con la macchina virtuale, usare il comando seguente. Sostituire l'indirizzo IP con il valore di `publicIpAddress` della macchina virtuale.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installare Oracle ASM

Per installare Oracle ASM, completare la procedura seguente. 

Per altre informazioni sull'installazione di Oracle ASM, vedere [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html) (Download di Oracle ASMLib per Oracle Linux 6).  

1. È necessario accedere come utente root per continuare l'installazione di ASM:

   ```bash
   sudo su -
   ```
   
2. Eseguire questi comandi aggiuntivi per installare i componenti Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Verificare che Oracle ASM sia installato:

   ```bash
   rpm -qa |grep oracleasm
   ```

    L'output di questo comando deve elencare i seguenti componenti:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM richiede utenti e ruoli specifici per il corretto funzionamento. I comandi seguenti creano gli account utente e i gruppi che fungono da prerequisito: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verificare che utenti e gruppi siano stati creati correttamente:

   ```bash
   id grid
   ```

    L'output di questo comando deve elencare i seguenti utenti e gruppi:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Creare una cartella per l’utente *grid* e modificare il proprietario:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configurare Oracle ASM

Per questa esercitazione, l'utente predefinito è *grid* mentre il gruppo predefinito è *asmadmin*. Assicurarsi che l'utente *oracle* faccia parte del gruppo asmadmin. Per configurare l'installazione di Oracle ASM, completare la procedura seguente:

1. L'impostazione della raccolta driver di Oracle ASM comporta la definizione di utente predefinito (grid) e gruppo predefinito (asmadmin) nonché la configurazione dell'avvio dell'unità all'accensione (selezione y) e l'analisi per i dischi all'accensione (selezione y). È necessario rispondere alle istruzioni del comando seguente:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   L'output di questo comando dovrebbe avere un aspetto simile al seguente, arrestando le richieste a cui rispondere.

    ```bash
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
   cat /proc/partitions
   ```

   L'output di questo comando dovrebbe avere un aspetto simile al seguente, elencando i dischi disponibili

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Formattare il disco */dev/sdc* eseguendo il comando seguente e rispondendo alle richieste con:
   - *n* per una nuova partizione
   - *p* per una partizione primaria
   - *1* per selezionare la prima partizione
   - Premere `enter` per il primo cilindro predefinito
   - Premere `enter` per l'ultimo cilindro predefinito
   - Premere *w* per scrivere le modifiche nella tabella delle partizioni  

   ```bash
   fdisk /dev/sdc
   ```
   
   Utilizzando le risposte fornite in precedenza, l'output del comando fdisk dovrebbe essere simile al seguente:

   ```bash
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

4. Ripetere il comando precedente fdisk per `/dev/sdd`, `/dev/sde` e `/dev/sdf`.

5. Controllare la configurazione del disco:

   ```bash
   cat /proc/partitions
   ```

   L'output del comando dovrebbe essere simile al seguente:

   ```bash
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
     11       0    1048575 sr0
   ```

6. Controllare lo stato del servizio Oracle ASM e avviare il servizio Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   L'output del comando dovrebbe essere simile al seguente:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Creare dischi Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   L'output del comando dovrebbe essere simile al seguente:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Elencare i dischi Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```   

   L'output di questo comando dovrebbe elencare i seguenti dischi Oracle ASM:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Modificare le password per gli utenti root, oracle e grid. **Prendere nota di queste nuove password** dal momento che verranno usate in un secondo momento durante l'installazione.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Modificare l'autorizzazione della cartella:

   ```bash
   chmod -R 775 /opt 
   chown grid:oinstall /opt 
   chown oracle:oinstall /dev/sdc1 
   chown oracle:oinstall /dev/sdd1 
   chown oracle:oinstall /dev/sde1 
   chown oracle:oinstall /dev/sdf1 
   chmod 600 /dev/sdc1 
   chmod 600 /dev/sdd1 
   chmod 600 /dev/sde1 
   chmod 600 /dev/sdf1
   ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Scaricare e preparare Oracle Grid Infrastructure

Per scaricare e preparare il software Oracle Grid Infrastructure, completare la procedura seguente:

1. Scaricare Oracle Grid Infrastructure dalla [pagina di download di Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Sotto il download denominato **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** (Oracle Database 12c versione 1 Grid Infrastructure (12.1.0.2.0) per Linux x86-64) scaricare i due file con estensione ZIP.

2. Dopo aver scaricato i file con estensione ZIP nel computer client, è possibile usare il protocollo per la copia di sicurezza, SCP, per copiare i file nella macchina virtuale:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH alla macchina virtuale Oracle in Azure per spostare i file con estensione zip nella cartella / zip. Quindi, modificare il proprietario dei file:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Decomprimere i file. A tale scopo, installare lo strumento di decompressione di Linux se non è già installato.
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Modificare l'autorizzazione:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aggiornare lo spazio di swapping configurato. I componenti Oracle Grid necessitano almeno di 6,8 GB di spazio di swapping per l’installazione di Grid. La dimensione predefinita dei file di scambio per le immagini di Oracle Linux in Azure è solo di 2048 MB. È necessario aumentare `ResourceDisk.SwapSizeMB` nel file `/etc/waagent.conf` e riavviare il servizio WALinuxAgent per rendere effettive le impostazioni aggiornate. Poiché si tratta di un file di sola lettura, è necessario modificare le autorizzazioni di file per consentire l'accesso alla scrittura.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Cercare `ResourceDisk.SwapSizeMB` e modificare il valore in **8192**. Sarà necessario premere `insert` per passare alla modalità di inserimento, digitare il valore **8192** e quindi premere `esc` per tornare alla modalità di comando. Per scrivere le modifiche e chiudere il file, digitare `:wq` e premere `enter`.
   
   > [!NOTE]
   > È consigliabile usare sempre `WALinuxAgent` per configurare lo spazio di swapping in modo che questo venga sempre creato nel disco temporaneo locale per garantire la massima prestazione. Per altre informazioni, vedere [Come aggiungere un file di scambio in macchine virtuali di Azure per Linux](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Preparare il client locale e la macchina virtuale per eseguire x11
La configurazione di Oracle ASM richiede un'interfaccia grafica per completare l'installazione e la configurazione. Si sta usando il protocollo x11 per agevolare l'installazione. Se si utilizza un sistema client (Mac o Linux) che dispone già di funzionalità X11 abilitate e configurate, è possibile ignorare questa configurazione e impostarla esclusivamente per i computer Windows. 

1. [Scaricare PuTTY](http://www.putty.org/) e [Xming](https://xming.en.softonic.com/) sul computer Windows. È necessario completare l'installazione di entrambe le applicazioni con i valori predefiniti prima di procedere.

2. Dopo aver installato PuTTY, aprire un prompt dei comandi, accedere alla cartella PuTTY (ad esempio, C:\Program Files\PuTTY) ed eseguire `puttygen.exe` per generare una chiave.

3. Nel generatore di chiavi PuTTY:
   
   1. Generare una chiave selezionando il pulsante `Generate`.
   2. Copiare il contenuto della chiave, con Ctrl+C.
   3. Selezionare il pulsante `Save private key`.
   4. Ignorare l'avviso sulla protezione della chiave con una passphrase e quindi selezionare `OK`.

   ![Screenshot del generatore di chiavi PuTTY](./media/oracle-asm/puttykeygen.png)

4. Nella macchina virtuale eseguire questi comandi:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Creare un file denominato `authorized_keys`. Incollare il contenuto della chiave in questo file e quindi salvare il file.

   > [!NOTE]
   > La chiave deve contenere la stringa `ssh-rsa`. In aggiunta, il contenuto della chiave deve essere una singola riga di testo.
   >  

6. Nel sistema client, avviare PuTTY. Nel pannello **Categoria** andare in **Connessione** > **SSH** > **Autenticazione**. Nella casella **Private key file for authentication** (File della chiave privata per l'autenticazione) individuare la chiave generata in precedenza.

   ![Screenshot delle opzioni di autenticazione SSH](./media/oracle-asm/setprivatekey.png)

7. Nel pannello **Categoria** andare in **Connessione** > **SSH** > **X11**. Selezionare la casella di controllo **Enable X11 forwarding** (Abilita l'inoltro di X11).

   ![Screenshot delle opzioni di inoltro X11 SSH](./media/oracle-asm/enablex11.png)

8. Nel pannello **Categoria** andare in **Sessione**. Immettere la macchina virtuale ASM Oracle `<publicIPaddress>` nella finestra di dialogo nome host, riempire un nuovo nome `Saved Session` e quindi fare clic su `Save`.  Dopo aver salvato, fare clic su `open` per connettersi alla macchina virtuale Oracle ASM.  Alla prima connessione viene segnalato che il Registro di sistema non è stato memorizzato nella cache del sistema remoto. Fare clic su `yes` per aggiungerlo e continuare.

   ![Screenshot delle opzioni per la sessione PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installare Oracle Grid Infrastructure

Per installare Oracle Grid Infrastructure, completare la procedura seguente:

1. Accedere come utente **grid**. L'accesso non dovrebbe richiedere una password. 

   > [!NOTE]
   > Se si esegue Windows, assicurarsi di aver avviato Xming prima di iniziare l'installazione.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Verrà visualizzata la finestra Oracle Grid Infrastructure 12c Release 1 Installer (Programma di installazione di Oracle Grid Infrastructure 12c versione 1). L'avvio del programma di installazione potrebbe richiedere alcuni minuti.

2. Nella pagina **Select Installation Option** (Selezionare le opzioni di installazione) selezionare **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Installare e configurare Oracle Grid Infrastructure su un server autonomo).

   ![Screenshot della pagina del programma di installazione Select Installation Option (Selezionare le opzioni di installazione)](./media/oracle-asm/install01.png)

3. Nella pagina **Select Product Languages** (Selezionare le lingue del prodotto) assicurarsi che sia selezionato **English** (Inglese) o la lingua desiderata.  Fare clic su `next`.

4. Nella pagina **Create ASM Disk Group** (Creare il gruppo di dischi ASM):
   - Immettere un nome per il gruppo di dischi.
   - In **Redundancy** (Ridondanza) selezionare **External** (Esterna).
   - In **Allocation Unit Size** (Dimensioni dell'unità di allocazione) selezionare **4**.
   - In **Add Disks** (Aggiungi dischi) selezionare **ORCLASMSP**.
   - Fare clic su `next`.

5. Nella pagina **Specify ASM Password** (Specificare la password ASM) selezionare l'opzione **Use same passwords for these accounts** (Usare le stesse password per questi account) e immettere una password.

   ![Screenshot della pagina del programma di installazione Specify ASM Password (Specificare la password ASM)](./media/oracle-asm/install04.png)

6. Nella pagina **Specificare opzioni di gestione** è possibile configurare il controllo Cloud EM. Questa opzione verrà ignorata: fare clic su `next` per continuare. 

7. Nella pagina **Privileged Operating System Groups** (Gruppi del sistema operativo con privilegi) usare le impostazioni predefinite. Fare quindi clic su `next` per continuare.

8. Nella pagina **Specify Installation Location** (Specificare il percorso di installazione) usare le impostazioni predefinite. Fare quindi clic su `next` per continuare.

9. Nella pagina **Crea inventario**, cambiare la directory inventario in `/u01/app/grid/oraInventory`. Fare quindi clic su `next` per continuare.

   ![Screenshot della pagina del programma di installazione Create Inventory (Creare l'inventario)](./media/oracle-asm/install08.png)

10. Nella pagina **Root script execution configuration** (Configurazione di esecuzione dello script principale) selezionare la casella di controllo **Automatically run configuration scripts** (Eseguire automaticamente gli script di configurazione). Selezionare quindi l'opzione **Use "root" user credential** (Usa le credenziali dell'utente "root") e immettere la password dell'utente root.

    ![Screenshot della pagina del programma di installazione Root script execution configuration (Configurazione di esecuzione dello script principale)](./media/oracle-asm/install09.png)

11. Nella pagina **Eseguire controlli dei prerequisiti**, l’impostazione corrente avrà esito negativo con errori. Si tratta di un comportamento previsto. Selezionare `Fix & Check Again`.

12. Nella finestra di dialogo **Fixup Script** (Correggi script) fare clic su `OK`.

13. Nella pagina **Summary** (Riepilogo) rivedere le impostazioni selezionate e quindi fare clic su `Install`.

    ![Screenshot della pagina del programma di installazione Summary (Riepilogo)](./media/oracle-asm/install12.png)

14. Viene visualizzata una finestra di dialogo che avvisa che gli script di configurazione devono essere eseguiti come un utente con privilegi. Fare quindi clic su `Yes` per continuare.

15. Nella pagina **Finish** (Fine) fare clic su `Close` per completare l'installazione.

## <a name="set-up-your-oracle-asm-installation"></a>Configurare l'installazione di Oracle ASM

Per configurare l'installazione di Oracle ASM, completare la procedura seguente:

1. Verificare che si è ancora connessi come **grid** dalla sessione X11. Potrebbe essere necessario accedere a `enter` per ripristinare i servizi terminal. Avviare quindi l’assistente alla configurazione Oracle Automated Storage Management:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Verrà visualizzato ASM Configuration Assistant (Assistente alla configurazione di ASM).

2. Nella finestra di dialogo **Configurare ASM: gruppi di dischi**, fare clic sul pulsante `Create` e quindi fare clic su `Show Advanced Options`.

3. Nella finestra di dialogo **Create Disk Group (Creare il gruppo di dischi)**:

   - Immettere il nome del gruppo di dischi **DATA**.
   - In **Select Member Disks** (Selezionare i dischi membri) selezionare **ORCL_DATA** e **ORCL_DATA1**.
   - In **Allocation Unit Size** (Dimensioni dell'unità di allocazione) selezionare **4**.
   - Fare clic su `ok` per creare il gruppo di dischi.
   - Per chiudere la finestra di conferma fare clic su `ok`.

   ![Screenshot della finestra di dialogo Create Disk Group (Creare il gruppo di dischi)](./media/oracle-asm/asm02.png)

4. Nella finestra di dialogo **Configurare ASM: gruppi di dischi**, fare clic sul pulsante `Create` e quindi fare clic su `Show Advanced Options`.

5. Nella finestra di dialogo **Create Disk Group (Creare il gruppo di dischi)**:

   - Immettere il nome del gruppo di dischi **FRA**.
   - In **Redundancy** (Ridondanza) selezionare **External (none)** (Esterna (nessuna)).
   - In **Select Member Disks** (Selezionare i dischi membri) selezionare **ORCL_DATA**.
   - In **Allocation Unit Size** (Dimensioni dell'unità di allocazione) selezionare **4**.
   - Fare clic su `ok` per creare il gruppo di dischi.
   - Per chiudere la finestra di conferma fare clic su `ok`.

   ![Screenshot della finestra di dialogo Create Disk Group (Creare il gruppo di dischi)](./media/oracle-asm/asm04.png)

6. Selezionare **Exit** (Esci) per chiudere ASM Configuration Assistant (Assistente alla configurazione di ASM).

   ![Screenshot della finestra di dialogo Configure ASM: Disk Groups (Configura ASM: gruppi di dischi) con il pulsante Exit (Esci)](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Creare il database

Il software del database Oracle è già installato nell'immagine di Azure Marketplace. Per creare un database seguire questa procedura:

1. Passare gli utenti all'utente principale di Oracle e inizializzare il listener per la registrazione:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Si apre Configuration Assistant (Assistente alla configurazione).

2. Nella pagina **Database Operation** (Operazioni del database), fare clic su `Create Database`.

3. Nella pagina **Creation Mode** (Modalità di creazione):

   - Immettere un nome per il database.
   - Per **Storage Type** (tipo di archiviazione) assicurarsi che **Automatic Storage Management (ASM)** (Gestione archiviazione automatica, ASM) sia selezionato.
   - Per **Percorso file di database**, utilizzare il percorso predefinito ASM suggerito.
   - Per **Area di ripristino rapido**, utilizzare il percorso predefinito ASM suggerito.
   - Digitare una **Password amministratore** e **confermare la password**.
   - Verificare che `create as container database` sia selezionato.
   - Digitare un valore `pluggable database name`.

4. Nella pagina **Summary** (Riepilogo) rivedere le impostazioni selezionate e quindi fare clic su `Finish` per creare il database.

   ![Schermata della pagina Summary (Riepilogo)](./media/oracle-asm/createdb03.png)

5. Il database è stato creato. Nella pagina **Fine** è possibile sbloccare gli account aggiuntivi per usare il database e modificare le password. Se si desidera eseguire questa operazione, selezionare **Gestione password**, in caso contrario fare clic su `close`.

## <a name="delete-the-vm"></a>Eliminare la macchina virtuale

Oracle Automated Storage Management è stato configurato correttamente sull'immagine del database Oracle da Azure Marketplace.  Quando la macchina virtuale non è più necessaria, è possibile usare il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Configurare Oracle DataGuard](configure-oracle-dataguard.md)

[Esercitazione: Configurare Oracle GoldenGate](Configure-oracle-golden-gate.md)

Revisione: [Definire l'architettura di un database Oracle](oracle-design.md)
