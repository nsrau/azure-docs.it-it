---
title: Informazioni sul servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Informazioni su come installare l'agente del servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 929a4e4366c9e94ed4e1915406914991624f6baa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565555"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informazioni sul servizio Mobility per le macchine virtuali VMware e i server fisici

Quando si configura il ripristino di emergenza per macchine virtuali VMware e server fisici con [Azure Site Recovery](site-recovery-overview.md), si installa il servizio Mobility di Site Recovery in ogni macchina virtuale VMware locale e in ogni server fisico locale.  Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery. È possibile distribuire il servizio Mobility tramite i metodi seguenti:

- [Installazione push](#push-installation): Site Recovery installa agente di mobilità nel server quando la protezione viene abilitata tramite il portale di Azure.
- Installare manualmente: È possibile installare manualmente il servizio Mobility in ogni computer attraverso [UI](#install-mobility-agent-through-ui) oppure [prompt dei comandi](#install-mobility-agent-through-command-prompt).
- [Distribuzione automatizzata](vmware-azure-mobility-install-configuration-mgr.md): è possibile automatizzare l'installazione con strumenti di distribuzione software, come System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Antivirus nei computer replicati

Se nei computer da replicare è attivo e in esecuzione un software antivirus, assicurarsi di escludere la cartella di installazione del servizio Mobility dalle operazioni antivirus (*C:\ProgramData\ASR\agent*). Questa esclusione garantisce che la replica funzioni nel modo previsto.

## <a name="push-installation"></a>Installazione push

Installazione push è parte integrante di "[abilitazione replica](vmware-azure-enable-replication.md#enable-replication)" processo attivato nel portale. Dopo aver scelto il set di macchine virtuali da proteggere e attivare "Abilitazione della replica", server di configurazione esegue il push dell'agente di mobilità a tali server, installa l'agente e completare la registrazione dell'agente con server di configurazione. Per assicurare il completamento di questa operazione,

- Assicurarsi che tutto l'installazione push [prerequisiti](vmware-azure-install-mobility-service.md) siano soddisfatti.
- Assicurarsi che tutte le configurazioni dei server rientrano [matrice di supporto di VMware per uno scenario di ripristino di emergenza di Azure](vmware-physical-azure-support-matrix.md).

Dettagli del flusso di lavoro di installazione push è stato descritto nelle sezioni seguenti.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Dal [9.23 versione](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) e versioni successive

Durante l'installazione push dell'agente di mobilità, vengono eseguiti i passaggi seguenti

1. Effettua il push dell'agente nella macchina di origine. Copia l'agente macchina di origine può non riuscire a causa di più errori dell'ambiente. Visita [la linea guida prescrive](vmware-azure-troubleshoot-push-install.md) risolvere gli errori di installazione push.
2. Dopo che l'agente è stato correttamente copiato i controlli dei prerequisiti del server vengono eseguiti nel server. Installazione non riesce se una o più i [prerequisiti](vmware-physical-azure-support-matrix.md) non vengono soddisfatti. Se tutti i prerequisiti sono soddisfatti, viene attivata l'installazione.
3. Azure Site Recovery VSS provider è installato nel server come parte dell'installazione dell'agente di mobilità. Questo provider viene usato per generare i punti di coerenza dell'applicazione. Se si verifica un errore di installazione del provider servizio Copia shadow, questo passaggio verrà ignorato e l'installazione dell'agente continuerà.
4. Se l'installazione dell'agente ha esito positivo ma l'installazione del provider VSS non riesce, lo stato del processo viene contrassegnato come "Warning". Ciò non influisce sulla generazione di punti di arresto anomalo del sistema della coerenza.

    a. Per generare i punti di coerenza dell'applicazione, fare riferimento a [la linea guida prescrive](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) per completare l'installazione del provider di Site Recovery VSS manualmente. </br>
    b.  Se non si desidera che punti coerenti con l'applicazione da generare [modificare il criterio di replica](vmware-azure-set-up-replication.md#create-a-policy) disattivare punti coerente dell'applicazione.

### <a name="before-922-versions"></a>Prima di 9.22 versioni

1. Effettua il push dell'agente nella macchina di origine. Copia l'agente macchina di origine può non riuscire a causa di più errori dell'ambiente. Visita [la linea guida prescrive](vmware-azure-troubleshoot-push-install.md) risolvere gli errori di installazione push.
2. Dopo che l'agente è stato correttamente copiato i controlli dei prerequisiti del server vengono eseguiti nel server. Installazione non riesce se una o più i [prerequisiti](vmware-physical-azure-support-matrix.md) non vengono soddisfatti. Se tutti i prerequisiti sono soddisfatti, viene attivata l'installazione.
3. Azure Site Recovery VSS provider è installato nel server come parte dell'installazione dell'agente di mobilità. Questo provider viene usato per generare i punti di coerenza dell'applicazione. Se l'installazione del provider servizio Copia shadow non riesce, l'installazione dell'agente avrà esito negativo. Per evitare errori di installazione dell'agente di mobilità, utilizzare [9.23 versione](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) o versione successiva per generare i punti di arresto anomalo del sistema coerenti e installare manualmente il provider VSS.

## <a name="install-mobility-agent-through-ui"></a>Installare l'agente di mobilità tramite l'interfaccia utente

### <a name="prerequisite"></a>Prerequisito

- Assicurarsi che tutte le configurazioni dei server rientrano [matrice di supporto di VMware per uno scenario di ripristino di emergenza di Azure](vmware-physical-azure-support-matrix.md).
- [Individuare il programma di installazione](#locate-installer-files) base al sistema operativo del server.

>[!IMPORTANT]
> Se si replicano VM IaaS di Azure da un'area di Azure a un altro, non usare questo metodo. Usare invece il metodo di installazione basato sulla riga di comando.

1. Copiare il file di installazione nella macchina ed eseguirlo.
2. In **Opzioni di installazione** selezionare **Install Mobility Service** (Installare il servizio Mobility).
3. Selezionare il percorso di installazione > **Installa**.

    ![Pagina dell'opzione di installazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitorare il processo di installazione in **Stato dell'installazione**. Al termine dell'installazione, selezionare **Proceed to Configuration** (Procedi con la configurazione) per registrare il servizio nel server di configurazione.

    ![Pagina di registrazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. nelle **dettagli di configurazione Server**, specificare l'indirizzo IP e la passphrase è stato configurato.  

    ![Pagina di registrazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selezionare **Registra** per completare la registrazione.

    ![Pagina finale di registrazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Installare l'agente di mobilità dal prompt dei comandi

### <a name="prerequisite"></a>Prerequisito

- Assicurarsi che tutte le configurazioni dei server rientrano [matrice di supporto di VMware per uno scenario di ripristino di emergenza di Azure](vmware-physical-azure-support-matrix.md).
- [Individuare il programma di installazione](#locate-installer-files) base al sistema operativo del server.

### <a name="on-a-windows-machine"></a>In un computer Windows

- Copiare il programma di installazione in una cartella locale (ad esempio C:\Temp) sul server che si vuole proteggere.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Eseguire l'installazione come indicato di seguito:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registrare l'agente nel server di configurazione.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Impostazioni di installazione
**Impostazione** | **Dettagli**
--- | ---
Uso | UnifiedAgent.exe. exe /Role < MS/MT > /InstallLocation <Install Location> /Platform "VmWare" /silent.
log di installazione | In %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
/InstallLocation| Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility (qualsiasi cartella).
/Platform | Obbligatorio. Specifica la piattaforma in cui viene installato il servizio Mobility. **VMware** per macchine virtuali VMware/server fisici; **Azure** per macchine virtuali di Azure.
/Silent| facoltativo. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione
**Impostazione** | **Dettagli**
--- | ---
Uso | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP > /PassphraseFilePath \<PassphraseFilePath >
Log di configurazione dell'agente | In %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
/PassphraseFilePath |  Obbligatorio. Percorso della passphrase. Usare qualsiasi percorso file locale o UNC valido.

### <a name="on-a-linux-machine"></a>In un computer Linux

1. Copiare il programma di installazione in una cartella locale (ad esempio /tmp) sul server che si vuole proteggere. Eseguire i comandi seguenti in un terminale:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Eseguire l'installazione come indicato di seguito:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Al termine dell'installazione è necessario registrare il servizio Mobility nel server di configurazione. Eseguire questo comando per registrare il servizio Mobility nel server di configurazione:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Impostazioni di installazione
**Impostazione** | **Dettagli**
--- | ---
Uso | . /Install -d <Install Location> VmWare - v - r < MS/MT > - q
-r | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
-d | Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility: /usr/local/ASR.
-v | Obbligatorio. Specifica la piattaforma in cui viene installato il servizio Mobility. **VMware** per macchine virtuali VMware/server fisici; **Azure** per macchine virtuali di Azure.
-q | facoltativo. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione
**Impostazione** | **Dettagli**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP > -P \<PassphraseFilePath >
-i | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
-P |  Obbligatorio. Percorso completo del file in cui è stata salvata la passphrase. Usare qualsiasi cartella valida.

## <a name="azure-virtual-machine-agent"></a>Agente di macchina virtuale di Azure

- **Macchine virtuali Windows**: dalla versione 9.7.0.0 del servizio Mobility, l'[agente di macchina virtuale di Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) viene installato dal programma di installazione del servizio Mobility. In questo modo si garantisce che quando la macchina esegue il failover in Azure, la macchina virtuale di Azure soddisfa i prerequisiti dell'installazione dell'agente per usare dell'estensione di una macchina virtuale.
- **Macchine virtuali Linux**: l'istanza di [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve essere installata manualmente nella macchina virtuale di Azure dopo il failover.

## <a name="locate-installer-files"></a>Individuare i file di programma di installazione

Passare alla cartella %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository nel server di configurazione. Controllare il programma di installazione è necessario basato sul sistema operativo. La tabella seguente riepiloga i file di programma di installazione per ogni VM VMware e server fisici in Azure. È possibile esaminare i [sistemi operativi supportati](vmware-physical-azure-support-matrix.md#replicated-machines) prima di iniziare.

**File del programma di installazione** | **Sistema operativo (solo a 64 bit)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Server Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Passaggi successivi

[Set up push installation for the Mobility service](vmware-azure-install-mobility-service.md) (Configurare l'installazione push per il servizio Mobility).
