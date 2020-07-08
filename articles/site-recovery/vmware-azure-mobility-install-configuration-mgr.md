---
title: Automatizzare il servizio Mobility per il ripristino di emergenza dell'installazione in Azure Site Recovery
description: Come installare automaticamente il servizio Mobility per il ripristino di emergenza di VMware/Physical Server con Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77252228"
---
# <a name="automate-mobility-service-installation"></a>Automatizzare l'installazione del servizio Mobility

Questo articolo descrive come automatizzare l'installazione e gli aggiornamenti per l'agente del servizio Mobility in [Azure Site Recovery](site-recovery-overview.md).

Quando si distribuisce Site Recovery per il ripristino di emergenza di server fisici e macchine virtuali VMware locali in Azure, si installa l'agente del servizio Mobility in ogni computer che si vuole replicare. Il servizio Mobility acquisisce le scritture dei dati nel computer e le invia al server di elaborazione Site Recovery per la replica. È possibile distribuire il servizio Mobility in diversi modi:

- **Installazione push**: consente di Site Recovery installare l'agente del servizio Mobility quando si Abilita la replica per un computer nel portale di Azure.
- **Installazione manuale**: installare manualmente il servizio Mobility in ogni computer. [Altre](vmware-physical-mobility-service-overview.md) informazioni sull'installazione push e manuale.
- **Distribuzione automatizzata**: automatizzare l'installazione con strumenti di distribuzione software, ad esempio Microsoft endpoint Configuration Manager o strumenti di terze parti, ad esempio JetPatch.

L'installazione e l'aggiornamento automatici forniscono una soluzione se:

- L'organizzazione non consente l'installazione push nei server protetti.
- Per i criteri aziendali è necessario modificare periodicamente le password. È necessario specificare una password per l'installazione push.
- I criteri di sicurezza non consentono di aggiungere eccezioni del firewall per computer specifici.
- Si sta fungendo da provider di servizi di hosting e non si desidera fornire le credenziali del computer del cliente necessarie per l'installazione push con Site Recovery.
- È necessario ridimensionare le installazioni degli agenti in un numero elevato di server contemporaneamente.
- Si desidera pianificare le installazioni e gli aggiornamenti durante le finestre di manutenzione pianificata.

## <a name="prerequisites"></a>Prerequisiti

Per automatizzare l'installazione, sono necessari gli elementi seguenti:

- Una soluzione di installazione software distribuita, ad esempio [Configuration Manager](/configmgr/) o [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Prerequisiti di distribuzione sul posto in [Azure](tutorial-prepare-azure.md) e in [locale per il](vmware-azure-tutorial-prepare-on-premises.md) ripristino di emergenza di VMware o per il ripristino di emergenza di [server fisici](physical-azure-disaster-recovery.md) . Esaminare i [requisiti di supporto](vmware-physical-azure-support-matrix.md) per il ripristino di emergenza.

## <a name="prepare-for-automated-deployment"></a>Preparare la distribuzione automatica

Nella tabella seguente sono riepilogati gli strumenti e i processi per l'automazione della distribuzione del servizio Mobility.

**Strumento** | **Dettagli** | **Istruzioni**
--- | --- | ---
**Configuration Manager** | 1. Verificare di disporre dei [prerequisiti](#prerequisites) elencati in precedenza. <br/><br/> 2. distribuire il ripristino di emergenza configurando l'ambiente di origine, incluso il download di un file OVA per distribuire il server di configurazione Site Recovery come macchina virtuale VMware usando un modello OVF.<br/><br/> 3. registrare il server di configurazione con il servizio Site Recovery, configurare l'ambiente di Azure di destinazione e configurare un criterio di replica.<br/><br/> 4. per la distribuzione automatizzata del servizio Mobility, è possibile creare una condivisione di rete contenente i file di installazione del servizio Mobility e la passphrase del server di configurazione.<br/><br/> 5. si crea un pacchetto di Configuration Manager contenente l'installazione o gli aggiornamenti e si prepara la distribuzione del servizio Mobility.<br/><br/> 6. è quindi possibile abilitare la replica in Azure per i computer in cui è installato il servizio Mobility. | [Automatizzare con Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Verificare di disporre dei [prerequisiti](#prerequisites) elencati in precedenza. <br/><br/> 2. distribuire il ripristino di emergenza configurando l'ambiente di origine, incluso il download e la distribuzione di JetPatch Agent Manager per Azure Site Recovery nell'ambiente Site Recovery, usando un modello OVF.<br/><br/> 3. registrare il server di configurazione con Site Recovery, configurare l'ambiente di Azure di destinazione e configurare un criterio di replica.<br/><br/> 4. per la distribuzione automatizzata, inizializzare e completare la configurazione del Agent Manager JetPatch.<br/><br/> 5. in JetPatch è possibile creare un criterio di Site Recovery per automatizzare la distribuzione e l'aggiornamento dell'agente del servizio Mobility. <br/><br/> 6. è quindi possibile abilitare la replica in Azure per i computer in cui è installato il servizio Mobility. | [Automatizzare con JetPatch Agent Manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Risolvere i problemi di installazione dell'agente in JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizzare con Configuration Manager

### <a name="prepare-the-installation-files"></a>Preparare i file di installazione

1. Assicurarsi di disporre dei prerequisiti.
1. Creare una condivisione file di rete sicura (condivisione SMB) a cui è possibile accedere dal computer che esegue il server di configurazione.
1. In Configuration Manager [categorizzare i server](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) in cui si vuole installare o aggiornare il servizio Mobility. Una raccolta deve contenere tutti i server Windows, gli altri server Linux.
1. Nella condivisione di rete creare una cartella:

   - Per l'installazione nei computer Windows, creare una cartella denominata _nella mobsvcwindows_.
   - Per l'installazione nei computer Linux, creare una cartella denominata _MobSvcLinux_.

1. Accedere al computer del server di configurazione.
1. Nel computer del server di configurazione aprire un prompt dei comandi amministrativo.
1. Per generare il file passphrase, eseguire questo comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Copiare il file _file mobsvc. passphrase_ nella cartella Windows e nella cartella Linux.
1. Per passare alla cartella che contiene i file di installazione, eseguire questo comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Copiare i file di installazione nella condivisione di rete:

   - Per Windows, copiare _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ in _nella mobsvcwindows_.
   - Per Linux, copiare i file seguenti in _MobSvcLinux_:
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Come descritto nelle procedure seguenti, copiare il codice nelle cartelle Windows o Linux. Il presupposto è che:

   - L'indirizzo IP del server di configurazione è `192.168.3.121` .
   - La condivisione file di rete protetta è `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Copiare il codice nella cartella Windows

Copiare il codice seguente:

- Salvare il codice nella cartella _nella mobsvcwindows_ come _install.bat_.
- Sostituire i `[CSIP]` segnaposto in questo script con i valori effettivi dell'indirizzo IP del server di configurazione.
- Lo script supporta le nuove installazioni dell'agente del servizio Mobility e gli aggiornamenti agli agenti già installati.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
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

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Copiare il codice nella cartella Linux

Copiare il codice seguente:

- Salvare il codice nella cartella _MobSvcLinux_ come _install_linux. sh_.
- Sostituire i `[CSIP]` segnaposto in questo script con i valori effettivi dell'indirizzo IP del server di configurazione.
- Lo script supporta le nuove installazioni dell'agente del servizio Mobility e gli aggiornamenti agli agenti già installati.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Creare un pacchetto

1. Accedere alla console di Configuration Manager e passare a **raccolta software**  >  **Gestione applicazioni**  >  **pacchetti**.
1. Fare clic con il pulsante destro del mouse su **pacchetti**  >  **Crea pacchetto**
1. Specificare i dettagli del pacchetto, inclusi nome, descrizione, produttore, lingua e versione.
1. Selezionare **questo pacchetto contiene i file di origine**.
1. Fare clic su **Sfoglia**e selezionare la condivisione di rete e la cartella che contiene il programma di installazione pertinente (_nella mobsvcwindows_ o _MobSvcLinux_). Quindi selezionare **Avanti**.

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. In **scegliere il tipo di programma da creare** pagina selezionare **programma standard**  >  **Avanti**.

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Nella pagina **specificare le informazioni su questo programma standard** specificare i valori seguenti:

    **Parametro** | **Valore di Windows** | **Valore Linux**
    --- | --- | ---
    **Nome** | Installare il servizio Mobility di Microsoft Azure (Windows) | Installare Microsoft Azure servizio Mobility (Linux).
    **Riga di comando** | install.bat | ./install_linux.sh
    **È possibile eseguire il programma** | anche se non ci sono utenti connessi | anche se non ci sono utenti connessi
    **Altri parametri** | Usa impostazione predefinita | Usa impostazione predefinita

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. In **specificare i requisiti per il programma standard**eseguire le attività seguenti:

   - Per i computer Windows, selezionare **questo programma può essere eseguito solo su piattaforme specifiche**. Selezionare quindi i [sistemi operativi Windows supportati](vmware-physical-azure-support-matrix.md#replicated-machines) e selezionare **Avanti**.
   - Per i computer Linux, selezionare **questo programma può essere eseguito su qualsiasi piattaforma**. Selezionare quindi **Avanti**.

1. Completare la procedura guidata.

### <a name="deploy-the-package"></a>Distribuire il pacchetto

1. Nella console di Configuration Manager, fare clic con il pulsante destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**.

   ![Schermata della console di Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selezionare i punti di distribuzione in cui copiare i pacchetti. [Altre informazioni](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points)
1. Completare la procedura guidata. Il pacchetto inizia la replica nei punti di distribuzione specificati.
1. Al termine della distribuzione del pacchetto, fare clic con il pulsante destro del mouse sul pacchetto > **Distribuisci**.

   ![Schermata della console di Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selezionare la raccolta di dispositivi Windows o Linux creata in precedenza.
1. Nella pagina **specificare la destinazione del contenuto** selezionare **punti di distribuzione**.
1. In **specificare le impostazioni per controllare la modalità di distribuzione del software** , impostare **scopo** su **obbligatorio**.

   ![Schermata della Distribuzione guidata del software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. In **specificare la pianificazione per questa distribuzione**, impostare una pianificazione. [Altre informazioni](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched)

   - Il servizio Mobility viene installato in base alla pianificazione specificata.
   - Per evitare riavvii non necessari, pianificare l'installazione del pacchetto durante la finestra di manutenzione mensile o degli aggiornamenti software.

1. Nella pagina **punti di distribuzione** configurare le impostazioni e completare la procedura guidata.
1. Monitorare lo stato di avanzamento della distribuzione nella console di Configuration Manager. Passare a **monitoraggio**  >  **distribuzioni**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Disinstallare il servizio Mobility

È possibile creare pacchetti di Configuration Manager per disinstallare il servizio Mobility. Ad esempio, lo script seguente Disinstalla il servizio Mobility:

```DOS
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

[Abilitare la replica](vmware-azure-enable-replication.md) per le macchine virtuali.
