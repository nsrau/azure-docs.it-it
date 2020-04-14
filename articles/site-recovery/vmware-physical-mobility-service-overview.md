---
title: Informazioni sul servizio Mobility per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici con Azure Site Recovery. Documenti Microsoft
description: Informazioni sull'agente del servizio Mobility per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici in Azure usando il servizio Azure Site Recovery.Learn about the Mobility service agent for disaster recovery of VMware VMs and physical servers to Azure using the Azure Site Recovery service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259799"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informazioni sul servizio Mobility per le macchine virtuali VMware e i server fisici

Quando si configura il ripristino di emergenza per le macchine virtuali (VM) VMware e i server fisici tramite [Azure Site Recovery,](site-recovery-overview.md)si installa il servizio Site Recovery Mobility in ogni macchina virtuale VMware e server fisici locali. Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery. Il servizio Mobility viene installato dal software dell'agente del servizio Mobility che è possibile distribuire utilizzando i metodi seguenti:

- [Installazione push:](#push-installation)quando la protezione è abilitata tramite il portale di Azure, Site Recovery installa il servizio Mobility nel server.
- Installazione manuale: è possibile installare manualmente il servizio Mobility su ogni computer tramite [l'interfaccia utente o](#install-the-mobility-service-using-ui) il [prompt dei comandi](#install-the-mobility-service-using-command-prompt).
- [Distribuzione automatizzata](vmware-azure-mobility-install-configuration-mgr.md): È possibile automatizzare l'installazione del servizio Mobility con strumenti di distribuzione software come Configuration Manager.Automated deployment : You can automate the Mobility service installation with software deployment tools such as Configuration Manager.

> [!NOTE]
> Il servizio Mobility utilizza circa il 6%-10% della memoria nei computer di origine per macchine virtuali VMware o computer fisici.

## <a name="antivirus-on-replicated-machines"></a>Antivirus su computer replicati

Se i computer che si desidera replicare eseguono software antivirus, escludere la cartella di installazione del servizio Mobility _C:_ Questa esclusione garantisce che la replica funzioni come previsto.

## <a name="push-installation"></a>Installazione push

L'installazione push è parte integrante del processo eseguito dal portale di Azure per abilitare la [replica.](vmware-azure-enable-replication.md#enable-replication) Dopo aver scelto il set di macchine virtuali che si desidera proteggere e abilitare la replica, il server di configurazione invia l'agente del servizio Mobility ai server, installa l'agente e completa la registrazione dell'agente con il server di configurazione.

### <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che tutti [i prerequisiti](vmware-azure-install-mobility-service.md) di installazione push siano soddisfatti.
- Verificare che tutte le configurazioni dei server soddisfino i criteri nella matrice Di supporto per il [ripristino di emergenza delle macchine virtuali VMware e dei server fisici](vmware-physical-azure-support-matrix.md)in Azure.

Il flusso di lavoro di installazione push è descritto nelle sezioni seguenti:The push installation workflow is described in the following sections:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agente del servizio Mobility versione 9.23 e successive

Per altre informazioni sulla versione 9.23, vedere [Aggiornamento cumulativo 35 per Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Durante un'installazione push del servizio Mobility, vengono eseguiti i passaggi seguenti:

1. L'agente viene inviato al computer di origine. La copia dell'agente nel computer di origine può non riuscire a causa di più errori ambientali. Consulta [le nostre linee guida](vmware-azure-troubleshoot-push-install.md) per risolvere gli errori di installazione push.
1. Dopo che l'agente è stato copiato correttamente nel server, viene eseguito un controllo dei prerequisiti sul server.
   - Se tutti i prerequisiti sono soddisfatti, inizia l'installazione.
   - L'installazione non riesce se uno o più [prerequisiti](vmware-physical-azure-support-matrix.md) non sono soddisfatti.
1. Come parte dell'installazione dell'agente, viene installato il provider del servizio Copia Shadow del volume (VSS) per Azure Site Recovery. Il provider VSS viene utilizzato per generare punti di ripristino coerenti con l'applicazione. Se l'installazione del provider VSS non riesce, questo passaggio viene ignorato e l'installazione dell'agente continua.
1. Se l'installazione dell'agente ha esito positivo ma l'installazione del provider VSS non riesce, lo stato del processo viene contrassegnato come **Avviso**. Ciò non influisce sulla generazione di punti di ripristino con coerenza di arresto anomalo del sistema.

    - Per generare punti di ripristino coerenti con l'applicazione, fare riferimento alle [linee guida](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) per completare un'installazione manuale del provider VSS di Site Recovery.
    - Se non si desidera generare punti di ripristino coerenti con l'applicazione, [modificare i criteri](vmware-azure-set-up-replication.md#create-a-policy) di replica per disattivare i punti di ripristino coerenti con l'applicazione.

### <a name="mobility-service-agent-version-922-and-below"></a>Agente del servizio Mobility versione 9.22 e successive

1. L'agente viene inviato al computer di origine. La copia dell'agente nel computer di origine può non riuscire a causa di più errori ambientali. Fare riferimento alle [linee guida](vmware-azure-troubleshoot-push-install.md) per la risoluzione dei problemi di installazione push.
1. Dopo che l'agente è stato copiato correttamente nel server, viene eseguito un controllo dei prerequisiti sul server.
   - Se tutti i prerequisiti sono soddisfatti, inizia l'installazione.
   - L'installazione non riesce se uno o più [prerequisiti](vmware-physical-azure-support-matrix.md) non sono soddisfatti.

1. Come parte dell'installazione dell'agente, viene installato il provider del servizio Copia Shadow del volume (VSS) per Azure Site Recovery. Il provider VSS viene utilizzato per generare punti di ripristino coerenti con l'applicazione.
   - Se l'installazione del provider VSS non riesce, l'installazione dell'agente avrà esito negativo. Per evitare errori durante l'installazione dell'agente, utilizzare la [versione 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) o successiva per generare punti di ripristino compatibili con gli arresti anomali ed eseguire un'installazione manuale del provider VSS.

## <a name="install-the-mobility-service-using-ui"></a>Installare il servizio Mobility usando l'interfaccia utenteInstall the Mobility service using UI

### <a name="prerequisites"></a>Prerequisiti

- Verificare che tutte le configurazioni dei server soddisfino i criteri nella matrice Di supporto per il [ripristino di emergenza delle macchine virtuali VMware e dei server fisici](vmware-physical-azure-support-matrix.md)in Azure.
- [Individuare il programma di installazione](#locate-installer-files) per il sistema operativo del server.

>[!IMPORTANT]
> Non usare il metodo di installazione dell'interfaccia utente se stai replicando una macchina virtuale di Azure Infrastructure as a Service (IaaS) da un'area di Azure a un'altra. Utilizzare l'installazione del [prompt dei comandi.](#install-the-mobility-service-using-command-prompt)

1. Copiare il file di installazione nella macchina ed eseguirlo.
1. In **Opzioni di installazione** selezionare **Install Mobility Service** (Installare il servizio Mobility).
1. Scegliere il percorso di installazione e selezionare **Installa**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Pagina delle opzioni di installazione del servizio Mobility.":::

1. Monitorare il processo di installazione in **Stato dell'installazione**. Al termine dell'installazione, selezionare **Proceed to Configuration** (Procedi con la configurazione) per registrare il servizio nel server di configurazione.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="pagina di registrazione del servizio Mobility.":::

1. In **Dettagli server**di configurazione specificare l'indirizzo IP e la passphrase configurati.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="pagina di registrazione del servizio Mobility.":::

1. Selezionare **Registra** per completare la registrazione.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Pagina finale di registrazione del servizio Mobility.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Installare il servizio Mobility tramite il prompt dei comandi

### <a name="prerequisites"></a>Prerequisiti

- Verificare che tutte le configurazioni dei server soddisfino i criteri nella matrice Di supporto per il [ripristino di emergenza delle macchine virtuali VMware e dei server fisici](vmware-physical-azure-support-matrix.md)in Azure.
- [Individuare il programma di installazione](#locate-installer-files) per il sistema operativo del server.

### <a name="windows-machine"></a>Macchina Windows

- Da un prompt dei comandi, eseguire i comandi riportati di seguito per copiare il programma di installazione in una cartella locale, ad esempio _C:,_ nel server che si desidera proteggere. Sostituire il nome del file del programma di installazione con il nome file effettivo.

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
log di installazione | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
`/InstallLocation`| Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility (qualsiasi cartella).
`/Platform` | Mandatory. Specifica la piattaforma in cui è installato il servizio Mobility: <br/> **VMware** per vMs/server fisici VMware. <br/> Azure per macchine virtuali di **Azure.Azure** for Azure VMs.<br/><br/> Se si considerano le macchine virtuali di Azure come computer fisici, specificare **VMware**.
`/Silent`| Facoltativa. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione

Impostazione | Dettagli
--- | ---
Sintassi | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Log di configurazione dell'agente | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parametro obbligatorio. `<CSIP>`specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
`/PassphraseFilePath` |  Mandatory. Percorso della passphrase. Usare qualsiasi percorso file locale o UNC valido.

### <a name="linux-machine"></a>Macchina Linux

1. Da una sessione terminale copiare il programma di installazione in una cartella locale, ad esempio _/tmp_ sul server che si desidera proteggere. Sostituire il nome del file del programma di installazione con il nome file effettivo della distribuzione Linux, quindi eseguire i comandi.

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
`-d` | Parametro facoltativo. Specifica il percorso di `/usr/local/ASR`installazione del servizio Mobility: .
`-v` | Mandatory. Specifica la piattaforma in cui è installato il servizio Mobility. <br/> **VMware** per vMs/server fisici VMware. <br/> Azure per macchine virtuali di **Azure.Azure** for Azure VMs.
`-q` | Facoltativa. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione

Impostazione | Dettagli
--- | ---
Sintassi | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parametro obbligatorio. `<CSIP>`specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
`-P` |  Mandatory. Percorso completo del file in cui è stata salvata la passphrase. Usare qualsiasi cartella valida.

## <a name="azure-virtual-machine-agent"></a>Agente di macchina virtuale di Azure

- **Macchine virtuali Windows**: dalla versione 9.7.0.0 del servizio Mobility, l'[agente di macchina virtuale di Azure](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) viene installata dal programma di installazione del servizio Mobility. Ciò garantisce che quando il computer esegue il failover in Azure, la macchina virtuale di Azure soddisfi il prerequisito di installazione dell'agente per l'utilizzo di qualsiasi estensione della macchina virtuale.
- **Macchine virtuale Linux**: l'istanza di [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) deve essere installata manualmente nella macchina virtuale di Azure dopo il failover.

## <a name="locate-installer-files"></a>Individuare i file del programma di installazione

Sul server di configurazione passare alla cartella _%ProgramData%_ Controllare quale programma di installazione è necessario in base al sistema operativo. Nella tabella seguente sono riepilogati i file di installazione per ogni VM VMware e sistema operativo del server fisico. Prima di iniziare, è possibile esaminare i [sistemi operativi supportati.](vmware-physical-azure-support-matrix.md#replicated-machines)

> [!NOTE]
> I nomi dei file utilizzano la sintassi illustrata nella tabella seguente con _la versione_ e la _data_ come segnaposto per i valori reali. I nomi dei file effettivi saranno simili a questi esempi:
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
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Server Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Passaggi successivi

[Set up push installation for the Mobility service](vmware-azure-install-mobility-service.md) (Configurare l'installazione push per il servizio Mobility).
