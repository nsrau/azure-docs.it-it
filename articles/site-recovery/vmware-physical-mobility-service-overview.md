---
title: Informazioni sul servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Informazioni sull'agente del servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure tramite il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81259799"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informazioni sul servizio Mobility per le macchine virtuali VMware e i server fisici

Quando si configura il ripristino di emergenza per le macchine virtuali VMware e i server fisici con [Azure Site Recovery](site-recovery-overview.md), si installa il servizio di Site Recovery Mobility in ogni macchina virtuale VMware locale e nel server fisico. Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery. Il servizio Mobility viene installato dal software dell'agente del servizio Mobility che è possibile distribuire usando i metodi seguenti:

- [Installazione push](#push-installation): quando la protezione viene abilitata tramite il portale di Azure, Site Recovery installa il servizio Mobility nel server.
- Installazione manuale: è possibile installare manualmente il servizio Mobility in ogni computer tramite l' [interfaccia utente](#install-the-mobility-service-using-ui) o il prompt dei [comandi](#install-the-mobility-service-using-command-prompt).
- [Distribuzione automatizzata](vmware-azure-mobility-install-configuration-mgr.md): è possibile automatizzare l'installazione del servizio Mobility con strumenti di distribuzione software come Configuration Manager.

> [!NOTE]
> Il servizio Mobility USA approssimativamente il 6%-10% di memoria nei computer di origine per le macchine virtuali VMware o i computer fisici.

## <a name="antivirus-on-replicated-machines"></a>Antivirus nei computer replicati

Se i computer che si desidera replicare eseguono software antivirus, escludere la cartella di installazione del servizio Mobility _C:\ProgramData\ASR\agent_ da operazioni antivirus. Questa esclusione garantisce che la replica funzioni come previsto.

## <a name="push-installation"></a>Installazione push

L'installazione push è parte integrante del processo eseguito dal portale di Azure per [abilitare la replica](vmware-azure-enable-replication.md#enable-replication). Dopo aver scelto il set di macchine virtuali che si vuole proteggere e abilitare la replica, il server di configurazione effettua il push dell'agente del servizio Mobility ai server, installa l'agente e completa la registrazione dell'agente con il server di configurazione.

### <a name="prerequisites"></a>Prerequisiti

- Verificare che tutti i [prerequisiti](vmware-azure-install-mobility-service.md) di installazione push siano soddisfatti.
- Verificare che tutte le configurazioni del server soddisfino i criteri della [matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure](vmware-physical-azure-support-matrix.md).

Il flusso di lavoro di installazione push è descritto nelle sezioni seguenti:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agente del servizio Mobility versione 9,23 e successive

Per ulteriori informazioni sulla versione 9,23, vedere [aggiornamento cumulativo 35 per Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Durante un'installazione push del servizio Mobility, vengono eseguiti i passaggi seguenti:

1. Viene eseguito il push dell'agente nel computer di origine. La copia dell'agente nel computer di origine può avere esito negativo a causa di più errori ambientali. Visitare [le linee guida](vmware-azure-troubleshoot-push-install.md) per risolvere gli errori di installazione push.
1. Dopo che l'agente è stato correttamente copiato nel server, viene eseguito un controllo dei prerequisiti sul server.
   - Se vengono soddisfatti tutti i prerequisiti, viene avviata l'installazione.
   - L'installazione non riesce se uno o più [prerequisiti](vmware-physical-azure-support-matrix.md) non vengono soddisfatti.
1. Nell'ambito dell'installazione dell'agente, viene installato il provider Servizio Copia Shadow del volume (VSS) per Azure Site Recovery. Il provider VSS viene utilizzato per generare punti di ripristino coerenti con l'applicazione. Se l'installazione del provider VSS ha esito negativo, questo passaggio viene ignorato e l'installazione dell'agente continua.
1. Se l'installazione dell'agente ha esito positivo ma l'installazione del provider VSS ha esito negativo, lo stato del processo è contrassegnato come **avviso**. Questo non incide sulla generazione del punto di ripristino coerente con l'arresto anomalo.

    - Per generare punti di ripristino coerenti con l'applicazione, fare riferimento alle [linee guida](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) per completare un'installazione manuale del provider Site Recovery VSS.
    - Se non si desidera generare punti di ripristino coerenti con l'applicazione, [modificare i criteri di replica](vmware-azure-set-up-replication.md#create-a-policy) per disattivare i punti di ripristino coerenti con l'applicazione.

### <a name="mobility-service-agent-version-922-and-below"></a>Agente del servizio Mobility versione 9,22 e versioni precedenti

1. Viene eseguito il push dell'agente nel computer di origine. La copia dell'agente nel computer di origine può avere esito negativo a causa di più errori ambientali. Per risolvere gli errori di installazione push, fare riferimento alle [linee guida](vmware-azure-troubleshoot-push-install.md) .
1. Dopo che l'agente è stato correttamente copiato nel server, viene eseguito un controllo dei prerequisiti sul server.
   - Se vengono soddisfatti tutti i prerequisiti, viene avviata l'installazione.
   - L'installazione non riesce se uno o più [prerequisiti](vmware-physical-azure-support-matrix.md) non vengono soddisfatti.

1. Nell'ambito dell'installazione dell'agente, viene installato il provider Servizio Copia Shadow del volume (VSS) per Azure Site Recovery. Il provider VSS viene utilizzato per generare punti di ripristino coerenti con l'applicazione.
   - Se l'installazione del provider VSS non riesce, l'installazione dell'agente avrà esito negativo. Per evitare errori durante l'installazione dell'agente, usare la [versione 9,23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) o una versione successiva per generare punti di ripristino coerenti con l'arresto anomalo del sistema ed eseguire un'installazione manuale del provider VSS.

## <a name="install-the-mobility-service-using-ui"></a>Installare il servizio Mobility usando l'interfaccia utente

### <a name="prerequisites"></a>Prerequisiti

- Verificare che tutte le configurazioni del server soddisfino i criteri della [matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure](vmware-physical-azure-support-matrix.md).
- [Individuare il programma di installazione](#locate-installer-files) per il sistema operativo del server.

>[!IMPORTANT]
> Non usare il metodo di installazione dell'interfaccia utente se si esegue la replica di una macchina virtuale IaaS (Infrastructure as a Service) di Azure da un'area di Azure a un'altra. Utilizzare l'installazione del [prompt dei comandi](#install-the-mobility-service-using-command-prompt) .

1. Copiare il file di installazione nella macchina ed eseguirlo.
1. In **Opzioni di installazione** selezionare **Install Mobility Service** (Installare il servizio Mobility).
1. Scegliere il percorso di installazione e selezionare **Installa**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Pagina delle opzioni di installazione del servizio Mobility.":::

1. Monitorare il processo di installazione in **Stato dell'installazione**. Al termine dell'installazione, selezionare **Proceed to Configuration** (Procedi con la configurazione) per registrare il servizio nel server di configurazione.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Pagina di registrazione del servizio Mobility.":::

1. In **Dettagli del server di configurazione**specificare l'indirizzo IP e la passphrase configurati.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Pagina di registrazione del servizio Mobility.":::

1. Selezionare **Registra** per completare la registrazione.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Pagina finale per la registrazione del servizio Mobility.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Installare il servizio Mobility usando il prompt dei comandi

### <a name="prerequisites"></a>Prerequisiti

- Verificare che tutte le configurazioni del server soddisfino i criteri della [matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure](vmware-physical-azure-support-matrix.md).
- [Individuare il programma di installazione](#locate-installer-files) per il sistema operativo del server.

### <a name="windows-machine"></a>Computer Windows

- Al prompt dei comandi eseguire i comandi seguenti per copiare il programma di installazione in una cartella locale, ad esempio _C:\Temp_, nel server che si desidera proteggere. Sostituire il nome file del programma di installazione con il nome del file effettivo.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Eseguire questo comando per installare l'agente.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Eseguire questi comandi per registrare l'agente con il server di configurazione.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Impostazioni di installazione

Impostazione | Dettagli
--- | ---
Sintassi | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Log di installazione | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
`/InstallLocation`| Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility (qualsiasi cartella).
`/Platform` | Mandatory. Specifica la piattaforma in cui è installato il servizio Mobility: <br/> **VMware** per macchine virtuali VMware/server fisici. <br/> **Azure** per macchine virtuali di Azure.<br/><br/> Se si tratta di macchine virtuali di Azure come macchine fisiche, specificare **VMware**.
`/Silent`| Facoltativa. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione

Impostazione | Dettagli
--- | ---
Sintassi | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Log di configurazione dell'agente | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parametro obbligatorio. `<CSIP>`Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
`/PassphraseFilePath` |  Mandatory. Percorso della passphrase. Usare qualsiasi percorso file locale o UNC valido.

### <a name="linux-machine"></a>Computer Linux

1. Da una sessione terminal, copiare il programma di installazione in una cartella locale, ad esempio _tmp_ , nel server che si vuole proteggere. Sostituire il nome file del programma di installazione con il nome effettivo del file della distribuzione Linux, quindi eseguire i comandi.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Eseguire l'installazione come indicato di seguito:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Al termine dell'installazione, il servizio Mobility deve essere registrato nel server di configurazione. Eseguire il comando seguente per registrare il servizio Mobility con il server di configurazione.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Impostazioni di installazione

Impostazione | Dettagli
--- | ---
Sintassi | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
`-d` | Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility: `/usr/local/ASR` .
`-v` | Mandatory. Specifica la piattaforma in cui è installato il servizio Mobility. <br/> **VMware** per macchine virtuali VMware/server fisici. <br/> **Azure** per macchine virtuali di Azure.
`-q` | Facoltativa. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione

Impostazione | Dettagli
--- | ---
Sintassi | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parametro obbligatorio. `<CSIP>`Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
`-P` |  Mandatory. Percorso completo del file in cui è stata salvata la passphrase. Usare qualsiasi cartella valida.

## <a name="azure-virtual-machine-agent"></a>Agente di macchina virtuale di Azure

- **Macchine virtuali Windows**: dalla versione 9.7.0.0 del servizio Mobility, l'[agente di macchina virtuale di Azure](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) viene installata dal programma di installazione del servizio Mobility. In questo modo, quando viene eseguito il failover della macchina in Azure, la VM di Azure soddisfa il prerequisito di installazione dell'agente per l'uso di qualsiasi estensione della macchina virtuale.
- **Macchine virtuale Linux**: l'istanza di [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) deve essere installata manualmente nella macchina virtuale di Azure dopo il failover.

## <a name="locate-installer-files"></a>Individuare i file del programma di installazione

Nel server di configurazione passare alla cartella _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Verificare il programma di installazione necessario in base al sistema operativo. Nella tabella seguente vengono riepilogati i file del programma di installazione per ogni macchina virtuale VMware e sistema operativo server fisico. Prima di iniziare, è possibile esaminare i [sistemi operativi supportati](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> I nomi file usano la sintassi illustrata nella tabella seguente con la _versione_ e la _Data_ come segnaposto per i valori reali. I nomi dei file effettivi sono simili a questi esempi:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

File del programma di installazione | Sistema operativo (solo a 64 bit)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Include SP2 e SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6,4 </br> Oracle Enterprise Linux 6,5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Server Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Passaggi successivi

[Set up push installation for the Mobility service](vmware-azure-install-mobility-service.md) (Configurare l'installazione push per il servizio Mobility).
