---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc
ms.lasthandoff: 02/17/2017


---



### <a name="install-the-mobility-service"></a>Installare il servizio Mobility
Per abilitare la protezione per le macchine virtuali e i server fisici è necessario prima di tutto installare il servizio Mobility. È possibile procedere in due modi:

* **Push del server di elaborazione**: quando si abilita la replica in un computer, effettuare il push e installare il componente del servizio Mobility dal server di elaborazione. 
*Si noti* che l'installazione push non viene eseguita se i computer eseguono già una versione aggiornata del componente.
* **Push aziendale**: installare automaticamente il componente usando il processo push aziendale, ad esempio WSUS o System Center Configuration Manager, oppure [Automazione di Azure e la configurazione dello stato desiderato](site-recovery-automate-mobility-service-install.md). Configurare il server di configurazione prima di eseguire questa operazione.
* **Installazione manuale**: installare il componente manualmente in ogni computer da replicare. Configurare il server di configurazione prima di eseguire questa operazione.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparare il push automatico nei computer Windows
Di seguito viene illustrato come preparare i computer Windows in modo che il servizio Mobility possa essere installato automaticamente dal server di elaborazione.

1. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere privilegi di amministratore (locale o dominio) e viene usato solo per l'installazione push.

   > [!NOTE]
   > Se non si usa un account di dominio, è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, aggiungere la voce DWORD entry LocalAccountTokenFilterPolicy con un valore di 1 nel Registro di sistema in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Per aggiungere la voce del Registro di sistema da un'interfaccia della riga di comando, digitare **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall**. Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Aggiungere l'account che è stato creato:

   * Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\home\svsystems\bin.
   * Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
   * Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per abilitare la replica per un computer sarà necessario fornire le credenziali.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparare il push automatico nei server Linux
1. Verificare che il computer Linux da proteggere sia supportato, come descritto in [Prerequisiti dei computer protetti](#protected-machine-prerequisites). Verificare che sia presente la connettività di rete tra il computer Linux e il server di elaborazione.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente root nel server Linux di origine e viene usato solo per l'installazione push.

   * Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\home\svsystems\bin.
   * Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
   * Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per abilitare la replica per un computer sarà necessario fornire le credenziali.
3. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
4. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
5. Assicurarsi che SSH sia abilitato e in esecuzione sulla porta 22.
6. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config, come segue:

   * Accedere come utente ROOT.
   * Nel file /etc/ssh/sshd_config trovare la riga che inizia con **PasswordAuthentication**.
   * Rimuovere il commento dalla riga e modificare il valore da **no** a **yes**.
   * Trovare la riga che inizia con **Subsystem** e rimuovere il commento.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Installare manualmente il servizio Mobility
I programmi di installazione sono disponibili nel server di configurazione in **C:\Programmi (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Sistema operativo di origine | File di installazione del servizio Mobility |
| --- | --- |
| Windows Server (solo&64; bit) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (solo 64 bit) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bit) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bit) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Installare il servizio Mobility in un server di Windows
1. Scaricare ed eseguire il relativo programma di installazione.
2. In **Prima di iniziare** selezionare **Servizio Mobility**.

    ![Servizio Mobility](./media/site-recovery-vmware-to-azure/mobility3.png)
3. In **Dettagli del nuovo server di configurazione** specificare l'indirizzo IP del server di configurazione e la passphrase generata durante l'esecuzione dell'Installazione unificata. Per recuperare la passphrase, eseguire **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** nel server di configurazione.

    ![Servizio Mobility](./media/site-recovery-vmware-to-azure/mobility6.png)
4. In **Percorso di installazione** lasciare l'impostazione predefinita e fare clic su **Avanti** per avviare l'installazione.
5. Monitorare lo stato dell'installazione in **Stato dell'installazione** e, se richiesto, riavviare il computer. Dopo l'installazione del servizio, potrebbero essere necessari circa 15 minuti per aggiornare lo stato nel portale.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installare il servizio Mobility in un server Windows usando il prompt dei comandi
1. Copiare il programma di installazione in una cartella locale (ad esempio C:\Temp) sul server che si desidera proteggere. Il programma di installazione è reperibile nel server di configurazione in **[Install Location]\home\svsystems\pushinstallsvc\repository**. Il pacchetto per i sistemi operativi Windows ha un nome simile a Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. Rinominare questo file in MobilitySvcInstaller.exe
3. Per estrarre il programma di installazione MSI usare il comando seguente:

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "Indirizzo IP del server di configurazione" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Sintassi della riga di comando completa
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parametri**

* **/Role.** Obbligatorio. Specifica se deve essere installato il servizio Mobility. Immettere l'agente di valori | MasterTarget
* **/InstallLocation.** Obbligatorio. Specifica dove installare il servizio.
* **/PassphraseFilePath.** Obbligatorio. Passphrase del server di configurazione.
* **/LogFilePath.** Obbligatorio. Posizione in cui devono essere creati i file di log dell'installazione.

#### <a name="uninstall-the-mobility-service-manually"></a>Disinstallare manualmente il servizio Mobility
Il servizio di mobilità può essere disinstallato mediante Installazione applicazioni nel Pannello di controllo o questa istruzione della riga di comando: MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Installare il servizio Mobility in un server Linux
1. Copiare l'archivio TAR appropriato in base alla tabella precedente nel computer Linux da replicare.
2. Aprire un programma shell ed estrarre l'archivio TAR compresso in un percorso locale eseguendo: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio TAR. A questo scopo, copiare la passphrase da C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase nel server di configurazione e salvarla nel file passphrase.txt eseguendo *`echo <passphrase> >passphrase.txt`* nella shell.
4. Eseguire *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*per installare il servizio Mobility.
5. Specificare l'indirizzo IP interno del server di configurazione e assicurarsi che la porta 443 sia selezionata. Dopo l'installazione del servizio, potrebbero essere necessari circa 15 minuti per aggiornare lo stato nel portale.

**È possibile eseguire l'installazione anche dalla riga di comando**:

Copiare la passphrase da C:\Programmi (x86)\InMage Systems\private\connection nel server di configurazione e salvarla come "passphrase.txt" nel server di configurazione. Quindi eseguire questi comandi: In questo esempio l'indirizzo IP del server di configurazione è 104.40.75.37 e la porta HTTPS deve essere la 443:


Per eseguire l'installazione in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Per eseguire l'installazione nel server di destinazione master:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt




