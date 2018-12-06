---
title: Installare manualmente il servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Informazioni su come installare l'agente del servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: ramamill
ms.openlocfilehash: b3b6d7a64873d77336b2fcf14e22f4d6ae33e3a2
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976792"
---
ms.author: ramamill

# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Installare manualmente il servizio Mobility su macchine virtuali VMware e server fisici

Quando si configura il ripristino di emergenza per macchine virtuali VMware e server fisici con [Azure Site Recovery](site-recovery-overview.md), si installa il [servizio Mobility di Site Recovery](vmware-physical-mobility-service-overview.md) in ogni macchina virtuale VMware locale e in ogni server fisico locale.  Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery.

Questo articolo descrive come installare manualmente il servizio Mobility in ogni macchina da proteggere.

## <a name="create-a-passphrase"></a>Creare una passphrase

Prima di eseguire l'installazione, creare una passphrase che verrà usata durante l'installazione.

1. Accedere al server di configurazione.
2. Aprire un prompt dei comandi come amministratore.
3. Cambiare la directory nella cartella bin e creare un file passphrase.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Salvare il file passphrase in un luogo sicuro. 


## <a name="install-the-service-from-the-ui"></a>Installare il servizio dall'interfaccia utente

>[!IMPORTANT]
> Se si esegue la replica di una macchina virtuale IaaS di Azure da un'area di Azure a un'altra, non usare questo metodo, ma il metodo di installazione basato sulla riga di comando.

1. Individuare la versione del programma di installazione necessaria per il sistema operativo della macchina. I programmi di installazione si trovano nella cartella %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Verificare](vmware-physical-mobility-service-overview.md#installer-files) il programma di installazione necessario.
2. Copiare il file di installazione nella macchina ed eseguirlo.
3. In **Opzioni di installazione** selezionare **Install Mobility Service** (Installare il servizio Mobility).
4. Selezionare il percorso di installazione > **Installa**.

    ![Pagina dell'opzione di installazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Monitorare il processo di installazione in **Stato dell'installazione**. Al termine dell'installazione, selezionare **Proceed to Configuration** (Procedi con la configurazione) per registrare il servizio nel server di configurazione.

    ![Pagina di registrazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  In **Dettagli del server di configurazione** specificare l'indirizzo IP e la passphrase configurati.  

    ![Pagina di registrazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Selezionare **Registra** per completare la registrazione.

    ![Pagina finale di registrazione del servizio Mobility](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Installare il servizio dal prompt dei comandi

### <a name="on-a-windows-machine"></a>In un computer Windows

1. Copiare il programma di installazione in una cartella locale (ad esempio C:\Temp) sul server che si vuole proteggere. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Eseguire l'installazione come indicato di seguito:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Registrare l'agente nel server di configurazione.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Impostazioni di installazione
**Impostazione** | **Dettagli**
--- | ---
Uso | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
log di installazione | In %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
/InstallLocation| Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility (qualsiasi cartella).
/Platform | Obbligatorio. Specifica la piattaforma in cui viene installato il servizio Mobility. **VMware** per macchine virtuali Mware/server fisici; **Azure** per macchine virtuali di Azure. 
/Silent| facoltativo. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione
**Impostazione** | **Dettagli**
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Log di configurazione dell'agente | In %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
/PassphraseFilePath |  Obbligatorio. Percorso della passphrase. Usare qualsiasi percorso file locale o UNC valido.


### <a name="on-a-linux-machine"></a>In un computer Linux

1. Copiare il programma di installazione in una cartella locale (ad esempio /tmp) sul server che si vuole proteggere. Eseguire i comandi seguenti in un terminale:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
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
Uso | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | Parametro di installazione obbligatorio. Specifica se deve essere installato il servizio Mobility o la destinazione master.
-d | Parametro facoltativo. Specifica il percorso di installazione del servizio Mobility: /usr/local/ASR.
-v | Obbligatorio. Specifica la piattaforma in cui viene installato il servizio Mobility. **VMware** per macchine virtuali Mware/server fisici; **Azure** per macchine virtuali di Azure. 
-q | facoltativo. Specifica se il programma di installazione deve essere eseguito in modalità non interattiva.

#### <a name="registration-settings"></a>Impostazioni di registrazione
**Impostazione** | **Dettagli**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Parametro obbligatorio. Specifica l'indirizzo IP del server di configurazione. Qualsiasi indirizzo IP valido.
-P |  Obbligatorio. Percorso completo del file in cui è stata salvata la passphrase. Usare qualsiasi cartella valida

## <a name="next-steps"></a>Passaggi successivi
- [Configurare il ripristino di emergenza per macchine virtuali VMware](vmware-azure-tutorial.md)
- [Configurare il ripristino di emergenza per server fisici](physical-azure-disaster-recovery.md)
