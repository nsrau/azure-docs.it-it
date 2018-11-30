---
title: Informazioni sul servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs
description: Informazioni su come installare l'agente del servizio Mobility per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/04/2018
ms.author: ramamill
ms.openlocfilehash: efbbe048456c969f0045a0588bc5b64d1138ee15
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976962"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informazioni sul servizio Mobility per le macchine virtuali VMware e i server fisici

Quando si configura il ripristino di emergenza per macchine virtuali VMware e server fisici con [Azure Site Recovery](site-recovery-overview.md), si installa il servizio Mobility di Site Recovery in ogni macchina virtuale VMware locale e in ogni server fisico locale.  Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery. È possibile distribuire il servizio Mobility tramite i metodi seguenti:

[Installazione push](vmware-azure-install-mobility-service.md): configurare di Site Recovery per eseguire un'installazione push del servizio Mobility. A tale scopo, quando si configura il ripristino di emergenza, configurare anche un account che il server di elaborazione di Site Recovery possa usare per accedere alla macchina virtuale o al server fisico ai fini di installazione del servizio.
[Installazione manualmente](vmware-physical-mobility-service-install-manual.md): è possibile installare manualmente il servizio Mobility in ogni computer usando l'interfaccia utente o il prompt dei comandi.
[Distribuzione automatizzata](vmware-azure-mobility-install-configuration-mgr.md): è possibile automatizzare l'installazione con strumenti di distribuzione software, ad esempio System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Agente di macchina virtuale di Azure

- **Macchine virtuali Windows**: dalla versione 9.7.0.0 del servizio Mobility, l'[agente di macchina virtuale di Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) viene installata dal programma di installazione del servizio Mobility. In questo modo si garantisce che quando la macchina esegue il failover in Azure, la macchina virtuale di Azure soddisfa i prerequisiti dell'installazione dell'agente per usare dell'estensione di una macchina virtuale.
- **Macchine virtuale Linux**: l'istanza di [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve essere installata manualmente nella macchina virtuale di Azure dopo il failover.

## <a name="installer-files"></a>File del programma di installazione

La tabella riepiloga i file del programma di installazione per ogni sistema operativo di macchina virtuale VMware e di server fisico. È possibile esaminare i [sistemi operativi supportati](vmware-physical-azure-support-matrix.md#replicated-machines) prima di iniziare.


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

## <a name="anti-virus-on-replicated-machines"></a>Antivirus nei computer replicati

Se nei computer da replicare è attivo e in esecuzione un software antivirus, assicurarsi di escludere la cartella di installazione del servizio Mobility dalle operazioni antivirus (*C:\ProgramData\ASR\agent*). Questa esclusione garantisce che la replica funzioni nel modo previsto.

## <a name="update-the-mobility-service"></a>Aggiornare il servizio Mobility

1. Prima di iniziare, verificare che il server di configurazione, i server di elaborazione scale-out e i server di destinazione master che fanno parte della distribuzione siano aggiornati prima di aggiornare il servizio Mobility nei server protetti.
2. Nel portale aprire l'insieme di credenziali > **Elementi replicati**.
3. Se la versione del server di configurazione è la più recente, viene visualizzata una notifica con il messaggio "È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare."

     ![Finestra Elementi replicati](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)

4. Fare clic sulla notifica e in **Aggiornamento agente** selezionare i computer in cui si intende aggiornare il servizio Mobility. Fare quindi clic su **OK**.

     ![Elenco delle macchine virtuali in Elementi replicati](.\media\vmware-azure-install-mobility-service\update-okpng.png)

5. Viene avviato il processo di aggiornamento del servizio Mobility per ognuna delle macchine virtuali selezionate.

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>Aggiornare l'account usato per l'installazione push del servizio Mobility

Quando è stato distribuito Site Recovery, per consentire l'installazione push del servizio Mobility è stato specificato un account che il server di elaborazione di Site Recovery usa per accedere ai computer e installare il servizio quando è abilitata la replica per la macchina. Se si intende aggiornare le credenziali per questo account, seguire [queste istruzioni](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Disinstallare il servizio Mobility

### <a name="on-a-windows-machine"></a>In un computer Windows

Eseguire la disinstallazione dall'interfaccia utente o da un prompt dei comandi.

- **Dall'interfaccia utente**: nel Pannello di controllo della macchina selezionare **Programmi**. Selezionare **Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master** > **Disinstalla**.
- **Da un prompt dei comandi**: aprire una finestra dei comandi con privilegi di amministratore sul computer. Eseguire il comando seguente: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>In un computer Linux
1. In un computer Linux accedere come utente **ROOT**.
2. Nel terminale, passare a /user/local/ASR.
3. Eseguire il comando seguente:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Passaggi successivi

[Set up push installation for the Mobility service](vmware-azure-install-mobility-service.md) (Configurare l'installazione push per il servizio Mobility).
