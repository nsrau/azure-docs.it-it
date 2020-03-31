---
title: Preparare i computer di origine per l'installazione del servizio Mobility tramite l'installazione push per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici in Azure. Documenti Microsoft
description: Informazioni su come preparare il server per installare l'agente Mobility tramite l'installazione push per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici in Azure usando il servizio Azure Site Recovery.Learn how to prepare your server to install Mobility agent through push installation for disaster recovery of VMware VMs and physical servers to Azure using the Azure Site Recovery service.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186460"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Preparare il computer di origine per l'installazione push dell'agente mobilityPrepare source machine for push installation of mobility agent

Quando si configura il ripristino di emergenza per le macchine virtuali VMware e i server fisici tramite [Azure Site Recovery,](site-recovery-overview.md)si installa il [servizio Site Recovery Mobility](vmware-physical-mobility-service-overview.md) in ogni macchina virtuale VMware e server fisici locali.  Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery.

## <a name="install-on-windows-machine"></a>Eseguire l'installazione in un computer Windows

In ogni computer Windows da proteggere eseguire le operazioni seguenti:

1. Verificare che sia presente la connettività di rete tra il computer e il server di elaborazione. Se non si è configurato un server di elaborazione separato, per impostazione predefinita l'elaborazione viene eseguita nel server di configurazione.
1. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere diritti di amministratore, locale o di dominio. Usarlo solo per l'installazione push e per gli aggiornamenti dell'agente.
2. Se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale seguendo questa procedura:
    - Nella chiave del Registro di sistema HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System aggiungere un nuovo valore DWORD: **LocalAccountTokenFilterPolicy**. Impostare il valore su **1**.
    -  Per eseguire questa operazione in un prompt dei comandi, eseguire il comando seguente:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Allow an app or feature through Firewall** (Consenti app o funzionalità attraverso Firewall). Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows (WMI)**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Aggiungere l'account creato in CSPSConfigtool. A questo scopo, accedere al server di configurazione.
5. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%.
6. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
7. Aggiungere l'account che è stato creato.
8. Immettere le credenziali usate quando si abilita la replica per un computer.

## <a name="install-on-linux-machine"></a>Eseguire l'installazione in un computer Linux

In ogni computer Linux da proteggere eseguire le operazioni seguenti:

1. Verificare che sia presente la connettività di rete tra il computer Linux e il server di elaborazione.
2. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente **root** nel server Linux di origine. Usarlo solo per l'installazione push e per gli aggiornamenti.
3. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
4. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
5. Assicurarsi che SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
4. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config. A tale scopo, accedere come **root**.
5. Nel file **/etc/ssh/sshd_config** individuare la riga che inizia con **PasswordAuthentication**.
6. Rimuovere il commento dalla riga e modificare il valore in **yes**.
7. Individuare la riga che inizia con **Subsystem**e rimuovere il commento dalla riga.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Riavviare il servizio **sshd.**
9. Aggiungere l'account creato in CSPSConfigtool. A questo scopo, accedere al server di configurazione.
10. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.
11. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
12. Aggiungere l'account che è stato creato.
13. Immettere le credenziali usate quando si abilita la replica per un computer.

## <a name="anti-virus-on-replicated-machines"></a>Antivirus nei computer replicati

Se nei computer da replicare è attivo e in esecuzione un software antivirus, assicurarsi di escludere la cartella di installazione del servizio Mobility dalle operazioni antivirus (*C:\ProgramData\ASR\agent*). Questa esclusione garantisce che la replica funzioni nel modo previsto.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver installato il servizio Mobility, nel portale di Azure selezionare **+ Replica** per iniziare a proteggere le VM. Ulteriori informazioni sull'abilitazione della replica per [le macchine virtuali VMware](vmware-azure-enable-replication.md) e i [server fisici.](physical-azure-disaster-recovery.md#enable-replication)


