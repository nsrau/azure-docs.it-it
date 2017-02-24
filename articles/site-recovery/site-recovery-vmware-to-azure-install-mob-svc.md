---
title: Installazione del servizio Mobility (VMware/fisico in Azure) | Microsoft Docs
description: In questo articolo viene descritto come installare l&quot;agente del servizio Mobility nei computer locali per iniziare a proteggerli.
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
ms.date: 12/9/2016
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: f3f9bc205cd038ae636face742292cb28654fd39
ms.openlocfilehash: 1212ab52afac346ce6c70e6e6f64b9bc87a13320

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>Installazione del servizio Mobility (VMware/fisico in Azure)
Il servizio Mobility deve essere distribuito in ogni computer, ovvero macchina virtuale VMware o server fisico, di cui si vuole eseguire la replica in Azure. Acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione.  È possibile distribuire il servizio Mobility nei server che richiedono la protezione con i metodi seguenti
1. [Installare il servizio Mobility con strumenti di distribuzione software come System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
2. [Installare il servizio Mobility tramite Automazione di Azure e la configurazione dello stato desiderato](site-recovery-automate-mobility-service-install.md)
3. [Installare manualmente il servizio Mobility tramite interfaccia utente grafica (GUI)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [Installare manualmente il servizio Mobility tramite la riga di comando](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [Installare il servizio Mobilità tramite installazione push da Azure Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)

## <a name="prerequisites"></a>Prerequisiti
Soddisfare questi prerequisiti prima di iniziare a installare manualmente il servizio Mobility nei server.
1. Accedere al server di configurazione e aprire un prompt dei comandi con privilegi di amministratore.
2. Spostarsi nella cartella bin e creare un file passphrase

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. Archiviare il file in un luogo sicuro, sarà necessario durante l'installazione del servizio Mobility.
4. I programmi di installazione del servizio Mobility per tutti i sistemi operativi supportati si trovano nella directory     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>Programma di installazione del servizio Mobility per il mapping del sistema operativo

| Nome del modello del file del programma di installazione| Sistema operativo |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 (64 bit) SP1</br> Windows Server 2012 (64 bit) </br> Windows Server 2012 R2 (64 bit) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4, 6.5, 6.6, 6.7, 6.8 (solo 64 bit) </br> CentOS 6.4, 6.5, 6.6, 6.7. 6.8 (solo&64; bit) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (solo 64 bit)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (solo 64 bit)|


## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>Installare manualmente il servizio Mobility tramite interfaccia utente grafica

>[!NOTE]
> L'installazione basata sull'interfaccia utente grafica è supportata solo per sistemi operativi Microsoft Windows.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>Installare manualmente il servizio Mobility tramite la riga di comando
### <a name="command-line-based-install-on-windows-computers"></a>Installazione basata sulla riga di comando nei computer Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Installazione basata sulla riga di comando nei computer Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>Installare il servizio Mobilità tramite installazione push da Azure Site Recovery
Per poter eseguire l'installazione push del servizio Mobility tramite Azure Site Recovery, è necessario soddisfare i seguenti prerequisiti in tutti i computer di destinazione.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato il servizio Mobility è possibile usare il pulsante **+Replica** pulsante nel portale di Azure per avviare l'abilitazione della protezione per queste macchine virtuali.



<!--HONumber=Feb17_HO2-->


