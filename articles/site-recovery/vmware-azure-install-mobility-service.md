---
title: Preparare i computer di origine per installare il servizio Mobility tramite installazione push per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure | Microsoft Docs
description: Informazioni su come preparare il server per l'installazione dell'agente di mobilità tramite installazione push per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure tramite il servizio Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524998"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Preparare il computer di origine per l'installazione push dell'agente Mobility

Quando si configura il ripristino di emergenza per le macchine virtuali VMware e i server fisici con [Azure Site Recovery](site-recovery-overview.md), si installa il [servizio di Site Recovery Mobility](vmware-physical-mobility-service-overview.md) in ogni macchina virtuale VMware locale e nel server fisico.  Il servizio Mobility acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione di Site Recovery.

## <a name="install-on-windows-machine"></a>Eseguire l'installazione in un computer Windows

In ogni computer Windows da proteggere eseguire le operazioni seguenti:

1. Verificare che sia presente la connettività di rete tra il computer e il server di elaborazione. Se non si è configurato un server di elaborazione separato, per impostazione predefinita l'elaborazione viene eseguita nel server di configurazione.
1. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere diritti di amministratore, locale o di dominio. Usarlo solo per l'installazione push e per gli aggiornamenti dell'agente.
2. Se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale seguendo questa procedura:
    - Nella chiave del Registro di sistema HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System aggiungere un nuovo valore DWORD: **LocalAccountTokenFilterPolicy**. Impostare il valore su **1**.
    -  Per eseguire questa operazione in un prompt dei comandi, eseguire il comando seguente:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. Nell'istanza di Windows Firewall del computer da proteggere selezionare **Allow an app or feature through Firewall** (Consenti app o funzionalità attraverso Firewall). Abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows (WMI)**. Per i computer appartenenti a un dominio, è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

   ![Impostazioni del firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Aggiungere l'account creato in CSPSConfigtool. A questo scopo, accedere al server di configurazione.
5. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella%ProgramData%\ASR\home\svsystems\bin.
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
4. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config. A tale scopo, accedere come **utente root**.
5. Nel file di **sshd_config nella/etc/ssh/** trovare la riga che inizia con **PasswordAuthentication**.
6. Rimuovere il commento dalla riga e modificare il valore in **Yes**.
7. Trovare la riga che inizia con il **sottosistema**e rimuovere il commento dalla riga.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Riavviare il servizio **sshd**.
9. Aggiungere l'account creato in CSPSConfigtool. A questo scopo, accedere al server di configurazione.
10. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.
11. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
12. Aggiungere l'account che è stato creato.
13. Immettere le credenziali usate quando si abilita la replica per un computer.
1. Passaggio aggiuntivo per l'aggiornamento o la protezione dei computer SUSE Linux Enterprise Server 11 SP3 o RHEL 5 o CentOS 5 o Debian 7. [Verificare che nel server di configurazione sia disponibile la versione più recente](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Antivirus nei computer replicati

Se nei computer da replicare è attivo e in esecuzione un software antivirus, assicurarsi di escludere la cartella di installazione del servizio Mobility dalle operazioni antivirus (*C:\ProgramData\ASR\agent*). Questa esclusione garantisce che la replica funzioni nel modo previsto.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver installato il servizio Mobility, nel portale di Azure selezionare **+ Replica** per iniziare a proteggere le VM. Altre informazioni sull'abilitazione della replica per le [macchine virtuali VMware](vmware-azure-enable-replication.md) e i [server fisici](physical-azure-disaster-recovery.md#enable-replication).


