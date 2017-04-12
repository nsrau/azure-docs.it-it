---
title: Installare il servizio Mobility (VMware o fisico in Azure) | Microsoft Docs
description: Informazioni su come installare l&quot;agente del Servizio Mobility per proteggere i computer locali.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6437190ac58a021ce84993f667bbb5fad6031bb3
ms.lasthandoff: 03/31/2017

---

# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>Installare il Servizio Mobility (VMware o fisico in Azure)
Il Servizio Mobility di Azure Site Recovery acquisisce le scritture dei dati in un computer e le inoltra al server di elaborazione. Distribuire il servizio Mobility in ogni computer, ovvero macchina virtuale VMware o server fisico, di cui si vuole eseguire la replica in Azure. È possibile distribuire il Servizio Mobility per i server che si desidera proteggere tramite i metodi seguenti:


* [Installare il Servizio Mobility con strumenti di distribuzione software come System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
* [Installare il Servizio Mobility tramite Automazione di Azure e la configurazione dello stato desiderato (Automation DSC)](site-recovery-automate-mobility-service-install.md)
* [Installare manualmente il Servizio Mobility tramite interfaccia utente grafica (GUI)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [Installare manualmente il Servizio Mobility tramite un prompt di comando](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Installare il Servizio Mobility tramite installazione push da Azure Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> A partire dalla versione 9.7.0.0, nelle macchine virtuali Windows il programma di installazione del Servizio Mobility installa anche l'[agente di macchine virtuali di Azure](../virtual-machines/windows/extensions-features.md#azure-vm-agent) più recente. Quando un computer esegue il failover in Azure, il computer soddisfa i prerequisiti per l'installazione dell'agente per l'uso dell'estensione di una macchina virtuale.

## <a name="prerequisites"></a>Prerequisiti
Completare questa procedura per i prerequisiti prima di iniziare a installare manualmente il Servizio Mobility nel server:
1. Accedere al server di configurazione e quindi aprire una finestra del prompt dei comandi come amministratore.
2. Cambiare la directory nella cartella bin e creare un file passphrase:

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Salvare il file passphrase in un luogo sicuro. Usare il file durante l'installazione del Servizio Mobility.
4. I programmi di installazione del Servizio Mobility per tutti i sistemi operativi supportati sono nella cartella %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository folder.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Programma di installazione del servizio Mobility per il mapping del sistema operativo

| Nome del modello del file del programma di installazione| Sistema operativo |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bit) </br> Windows Server 2012 (64 bit) </br> Windows Server 2012 R2 (64 bit) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8 (solo a 64 bit) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8 (solo a 64 bit) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (solo a 64 bit)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (solo a 64 bit)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Installare manualmente il Servizio Mobility tramite la GUI

>[!NOTE]
> L'installazione basata su GUI funziona solo con sistemi operativi Windows.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Installare manualmente il Servizio Mobility tramite un prompt di comando

### <a name="command-line-installation-on-a-windows-computer"></a>Installazione dalla riga di comando in un computer Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Installazione dalla riga di comando in un computer Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Installare il Servizio Mobility tramite installazione push da Azure Site Recovery
Per eseguire un'installazione push del Servizio Mobility tramite Site Recovery, tutti i computer di destinazione devono soddisfare i prerequisiti seguenti.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Dopo l'installazione del Servizio Mobility, nel portale di Azure, selezionare il pulsante **Replica** per iniziare a proteggere le macchine virtuali.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Disinstallare il servizio Mobility in un computer Windows Server
Usare uno dei metodi seguenti per disinstallare il Servizio Mobility in un computer Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Disinstallare usando la GUI
1. Nel Pannello di controllo, selezionare **Programmi**.
2. Selezionare **Microsoft Azure Site Recovery Mobility Service/Master Target server** (Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master) e fare clic su **Disinstalla**.

### <a name="uninstall-at-a-command-prompt"></a>Disinstallare dal prompt dei comandi
1. Aprire una finestra del Prompt dei comandi come amministratore.
2. Eseguire il comando seguente per disinstallare il servizio Mobility:

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Disinstallare il servizio Mobility in computer Linux
1. Sul server Linux, accedere come utente **Root**.
2. Nel terminale, passare a /user/local/ASR.
3. Eseguire il comando seguente per disinstallare il servizio Mobility:

```
uninstall.sh -Y
```

