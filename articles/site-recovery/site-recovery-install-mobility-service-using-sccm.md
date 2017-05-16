---
title: Automatizzare l&quot;installazione del servizio Mobility per Azure Site Recovery con gli strumenti di distribuzione software | Microsoft Docs
description: Questo articolo consente di automatizzare l&quot;installazione del servizio Mobility con strumenti di distribuzione software come System Center Configuration Manager.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ded45356e6dd22b485adfe7f85ddd0abb21280e5
ms.lasthandoff: 04/03/2017

---
# <a name="automate-mobility-service-installation-by-using-software-deployment-tools"></a>Automatizzare l'installazione del servizio Mobility tramite strumenti di distribuzione software

In questo articolo viene descritto un esempio di come è possibile usare System Center Configuration Manager per distribuire il servizio Mobility di Azure Site Recovery nel data center. L'uso di uno strumento di distribuzione software come Configuration Manager offre i seguenti vantaggi:
* Pianificazione della distribuzione di nuove installazioni e aggiornamenti durante la finestra di manutenzione pianificata per gli aggiornamenti software
* Distribuzione a elevata scalabilità a centinaia di server simultaneamente


> [!NOTE]
> In questo articolo si usa System Center Configuration Manager 2012 R2 per illustrare l'attività di distribuzione. È anche possibile automatizzare l'installazione del servizio Mobility tramite [Automazione di Azure e la configurazione dello stato desiderato](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Prerequisiti
1. Uno strumento di distribuzione software come Configuration Manager già distribuito nell'ambiente.
  Creare due [Raccolte dispositivi](https://technet.microsoft.com/library/gg682169.aspx), una per tutti i **server Windows** e una per tutti i **server Linux** da proteggere con Site Recovery.
3. Un server di configurazione già registrato con Site Recovery.
4. Una condivisione file di rete sicura (condivisione Server Message Block) che sia accessibile dal server di Configuration Manager.

## <a name="deploy-mobility-service-on-computers-running-windows"></a>Distribuire il servizio Mobility nei computer che eseguono Windows
> [!NOTE]
> In questo articolo si presuppone che l'indirizzo IP del server di configurazione sia 192.168.3.121 e che la condivisione di file di rete sicura sia \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Passaggio 1: preparare la distribuzione
1. Creare una cartella nella condivisione di rete chiamandola **MobSvcWindows**.
2. Accedere al server di configurazione e aprire un prompt dei comandi amministrativo.
3. Eseguire i comandi seguenti per generare un file passphrase:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiare il file **MobSvc.passphrase** nella cartella **MobSvcWindows** nella condivisione di rete.
5. Accedere al repository del programma di installazione nel server di configurazione eseguendo il comando seguente:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Copiare **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** nella cartella **MobSvcWindows** nella condivisione di rete.
7. Copiare il codice seguente e salvarlo come **install.bat** nella cartella **MobSvcWindows**.

   > [!NOTE]
   > Sostituire i segnaposto [CSIP] nello script seguente con i valori effettivi dell'indirizzo IP del server di configurazione.

```
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
REM ==================================================
REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================
REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================
REM ==== Extract the installer ======================
CD %Temp%\MobSvc\Extracted
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed run install command ========
REM ==== Else run upgrade command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\temp\logfile.log
REM SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1} >> C:\Temp\logfile.log 2>&1
REM REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
REM IF NOT %ERRORLEVEL% EQU 0 (GOTO :INSTALL) ELSE GOTO :UPDATE
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UPDATE
:INSTALL
    echo "Install" >> c:\Temp\logfile.log
     UnifiedAgent.exe /Role "Agent" /CSEndpoint "10.10.20.168" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
GOTO :ENDSCRIPT
:UPDATE
    echo "Update" >> C:\Temp\logfile.log
    UnifiedAgent.exe /upgrade
:ENDSCRIPT

```

### <a name="step-2-create-a-package"></a>Passaggio 2: creare un pacchetto

1. Accedere alla console di Configuration Manager.
2. Passare a **Raccolta software** > **Gestione applicazioni** > **Pacchetti**.
3. Fare clic con il pulsante destro del mouse su **Pacchetti** e selezionare **Crea pacchetto**.
4. Indicare i valori per nome, descrizione, produttore, lingua e versione.
5. Selezionare la casella di controllo **Questo pacchetto contiene file di origine**.
6. Fare clic su **Sfoglia** e selezionare la condivisione di rete in cui è archiviato il programma di installazione (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. Nella pagina **Scegliere il tipo di programma da creare** selezionare **Programma standard** e fare clic su **Avanti**.

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. Nella pagina **Specificare le informazioni sul programma standard** indicare gli input seguenti e fare clic su **Avanti**. Gli altri input possono mantenere i valori predefiniti.
 
  | **Nome parametro** | **Valore** |
  |--|--|
  | Nome | Installare il servizio Mobility di Microsoft Azure (Windows) |
  | Riga di comando | install.bat |
  | Il programma può essere eseguito | anche se non ci sono utenti connessi |

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)

9. Nella pagina successiva selezionare i sistemi operativi di destinazione. Il servizio Mobility può essere installato solo su Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png) 

10. Fare clic su **Avanti** due volte per completare la procedura guidata.


> [!NOTE]
> Lo script supporta sia le nuove installazioni degli agenti del servizio Mobility che l'aggiornamento degli agenti già installati.

### <a name="step-3-deploy-the-package"></a>Passaggio 3: distribuire il pacchetto
1. Nella console di Configuration Manager fare clic con il pulsante destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**.
  ![Schermata della console di Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selezionare i **[punti di distribuzione](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in cui copiare i pacchetti.
3. Completare la procedura guidata. Il pacchetto inizia la replica nei punti di distribuzione specificati.
4. Dopo aver completato la distribuzione del pacchetto, fare clic su quest'ultimo con il pulsante destro del mouse e selezionare **Distribuisci**.
  ![Schermata della console di Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selezionare la raccolta di dispositivi di Windows Server creata nella sezione dei prerequisiti come raccolta di destinazione per la distribuzione.

  ![Schermata della Distribuzione guidata del software](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)

6. Nella pagina **Specificare la destinazione del contenuto** selezionare i **Punti di distribuzione**.
7. Nella pagina **Specificare le impostazioni per controllare la modalità di distribuzione del software** verificare che lo scopo sia **Obbligatorio**.

  ![Schermata della Distribuzione guidata del software](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. Specificare una pianificazione nella pagina **Specificare la pianificazione per questa distribuzione**. Per altre informazioni, vedere la sezione relativa alla [pianificazione dei pacchetti](https://technet.microsoft.com/library/gg682178.aspx).
9. Configurare le proprietà nella pagina **Punti di distribuzione** in base alle necessità del data center. Completare quindi la procedura guidata.

> [!TIP]
> Per evitare riavvii non necessari, pianificare l'installazione del pacchetto durante la finestra di manutenzione mensile o degli aggiornamenti software.

È possibile monitorare l'avanzamento della distribuzione tramite la console di Configuration Manager. Passare a **Monitoraggio** > **Distribuzioni** > *[nome pacchetto]*.

  ![Schermata dell'opzione di Configuration Manager per monitorare le distribuzioni](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux"></a>Distribuire il servizio Mobility nei computer che eseguono Linux
> [!NOTE]
> In questo articolo si presuppone che l'indirizzo IP del server di configurazione sia 192.168.3.121 e che la condivisione di file di rete sicura sia \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="step-1-prepare-for-deployment"></a>Passaggio 1: preparare la distribuzione
1. Creare una cartella nella condivisione di rete chiamandola **MobSvcLinux**.
2. Accedere al server di configurazione e aprire un prompt dei comandi amministrativo.
3. Eseguire i comandi seguenti per generare un file passphrase:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiare il file **MobSvc.passphrase** nella cartella **MobSvcLinux** nella condivisione di rete.
5. Accedere al repository del programma di installazione nel server di configurazione eseguendo il comando:

   `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`

6. Copiare i file seguenti nella cartella **MobSvcLinux** nella condivisione di rete:
   * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
   * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. Copiare il codice seguente e salvarlo come **install_linux.sh** nella cartella **MobSvcLinux**.
   > [!NOTE]
   > Sostituire i segnaposto [CSIP] nello script seguente con i valori effettivi dell'indirizzo IP del server di configurazione.

```
#!/bin/sh

rm -rf /tmp/MobSvc

mkdir -p /tmp/MobSvc

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
        cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            cp *RHEL7*.tar.gz /tmp/MobSvc
    fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
        echo $OS >> /tmp/MobSvc/sccm.log
        cp *SLES11*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
       cp *UBUNTU*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi
if [ "${OS}" ==  "" ]; then
    exit 1
fi
cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz


if [ -e /usr/local/.vx_version ];
then
    ./install -A u
    echo "Errorcode:$?"
    Error=$?

else
    ./install -t both -a host -R Agent -d /usr/local/ASR -i [CS IP] -p 443 -s y -c https -P MobSvc.passphrase >> /tmp/MobSvc/sccm.log 2>&1 && echo "Install Progress"
    Error=$?
fi
cd /tmp
rm -rf /tm/MobSvc
exit ${Error}
```

### <a name="step-2-create-a-package"></a>Passaggio 2: creare un pacchetto

1. Accedere alla console di Configuration Manager.
2. Passare a **Raccolta software** > **Gestione applicazioni** > **Pacchetti**.
3. Fare clic con il pulsante destro del mouse su **Pacchetti** e selezionare **Crea pacchetto**.
4. Indicare i valori per nome, descrizione, produttore, lingua e versione.
5. Selezionare la casella di controllo **Questo pacchetto contiene file di origine**.
6. Fare clic su **Sfoglia** e selezionare la condivisione di rete in cui è archiviato il programma di installazione (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. Nella pagina **Scegliere il tipo di programma da creare** selezionare **Programma standard** e fare clic su **Avanti**.

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)

8. Nella pagina **Specificare le informazioni sul programma standard** indicare gli input seguenti e fare clic su **Avanti**. Gli altri input possono mantenere i valori predefiniti.

    | **Nome parametro** | **Valore** |
  |--|--|
  | Nome | Installare il servizio Mobility di Microsoft Azure (Linux) |
  | Riga di comando | ./install_linux.sh |
  | Il programma può essere eseguito | anche se non ci sono utenti connessi |

  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)

9. Nella pagina successiva selezionare **Questo programma può essere eseguito in qualsiasi piattaforma**.
  ![Schermata di Creazione guidata pacchetto e programma](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)

10. Fare clic su **Avanti** due volte per completare la procedura guidata. 
 
> [!NOTE]
> Lo script supporta sia le nuove installazioni degli agenti del servizio Mobility che l'aggiornamento degli agenti già installati.

### <a name="step-3-deploy-the-package"></a>Passaggio 3: distribuire il pacchetto
1. Nella console di Configuration Manager fare clic con il pulsante destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**.
  ![Schermata della console di Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selezionare i **[punti di distribuzione](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in cui copiare i pacchetti.
3. Completare la procedura guidata. Il pacchetto inizia la replica nei punti di distribuzione specificati.
4. Dopo aver completato la distribuzione del pacchetto, fare clic su quest'ultimo con il pulsante destro del mouse e selezionare **Distribuisci**.
  ![Schermata della console di Configuration Manager](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selezionare la raccolta di dispositivi server Linux creata nella sezione dei prerequisiti come raccolta di destinazione per la distribuzione.

  ![Schermata della Distribuzione guidata del software](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)

6. Nella pagina **Specificare la destinazione del contenuto** selezionare i **Punti di distribuzione**.
7. Nella pagina **Specificare le impostazioni per controllare la modalità di distribuzione del software** verificare che lo scopo sia **Obbligatorio**.

  ![Schermata della Distribuzione guidata del software](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)

8. Specificare una pianificazione nella pagina **Specificare la pianificazione per questa distribuzione**. Per altre informazioni, vedere la sezione relativa alla [pianificazione dei pacchetti](https://technet.microsoft.com/library/gg682178.aspx).
9. Configurare le proprietà nella pagina **Punti di distribuzione** in base alle necessità del data center. Completare quindi la procedura guidata.

Il servizio Mobility viene installato nella raccolta di dispositivi server Linux in base alla pianificazione configurata.

## <a name="other-methods-to-install-mobility-service"></a>Altri metodi per installare il servizio Mobility
Di seguito sono riportate altre opzioni per l'installazione del servizio Mobility:
* [Installazione manuale tramite interfaccia utente grafica](http://aka.ms/mobsvcmanualinstall)
* [Installazione manuale tramite la riga di comando](http://aka.ms/mobsvcmanualinstallcli)
* [Installazione push tramite il server di configurazione](http://aka.ms/pushinstall)
* [Installazione automatica tramite Automazione di Azure e la configurazione dello stato desiderato](http://aka.ms/mobsvcdscinstall)

## <a name="uninstall-mobility-service"></a>Disinstallare il servizio Mobility
È possibile creare pacchetti di Configuration Manager per disinstallare il servizio Mobility. A tale scopo, seguire questa procedura:

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Passaggi successivi
È ora possibile [abilitare la protezione](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) per le macchine virtuali.

