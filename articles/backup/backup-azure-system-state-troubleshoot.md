---
title: Risoluzione dei problemi relativi al backup dello stato del sistema
description: In questo articolo viene illustrato come risolvere i problemi in Backup dello stato del sistema per i server Windows locali.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969568"
---
# <a name="troubleshoot-system-state-backup"></a>Risoluzione dei problemi relativi al backup dello stato del sistema

In questo articolo vengono descritte le soluzioni per i problemi che potrebbero verificarsi durante l'utilizzo di Backup dello stato del sistema.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di avviare la risoluzione dei problemi relativi al backup dello stato del sistema, è consigliabile eseguire la convalida riportata di seguito:

- [Verificare che l'agente di Microsoft Azure Recovery Services (MARS) sia aggiornatoEnsure Microsoft Azure Recovery Services (MARS) Agent is up to date](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verificare la presenza di connettività di rete tra l'agente dei Servizi di ripristino di Microsoft Azure e Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Verificare che i Servizi di ripristino di Microsoft Azure siano in esecuzione (nella console di Servizio). Se necessario, riavviare e ritentare l'operazione
- [Verificare che sia disponibile il 5-10% di volume libero nel percorso della cartella dei file temporanei](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Il backup pianificato ha esito negativo, ma il backup manuale funziona](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Verificare che nel sistema operativo siano installati gli aggiornamenti più recenti
- [Assicurarsi che le unità e i file non supportati siano esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Assicurarsi che l'**orologio** del sistema protetto sia configurato sul fuso orario corretto <br>
- [Assicurarsi che il server disponga almeno di .NET Framework versione 4.5.2 e versioni successive](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se si sta tentando di **registrare nuovamente il server** in un vault, eseguire le opzioni: <br>
  - Verificare che l'agente venga disinstallato nel server e che venga eliminato dal portale <br>
  - Usare la stessa passphrase che è stata usata inizialmente per la registrazione del server <br>
- Se si tratta di un backup offline, verificare che Azure PowerShell versione 3.7.0 sia installato nel computer di origine e di copia prima di avviare l'operazione di backup offline
- [Considerazione quando l'agente di backup è in esecuzione in una macchina virtuale di AzureConsideration when Backup agent is running on an Azure virtual machine](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Limitazione

- Il ripristino dello stato del sistema in un hardware diverso non è consigliato
- Il backup dello stato del sistema supporta attualmente i server Windows "locali". Questa funzionalità non è disponibile per le macchine virtuali di Azure.This functionality isn't available for Azure VMs.

## <a name="prerequisites"></a>Prerequisiti

Prima di risolvere i problemi relativi a Backup dello stato del sistema con Backup di Azure, eseguire la verifica dei prerequisiti riportata di seguito.  

### <a name="verify-windows-server-backup-is-installed"></a>Verificare che Windows Server Backup sia installato

Verificare che Windows Server Backup sia installato e abilitato nel server. Per controllare lo stato dell'installazione, eseguire questo comando di PowerShell:To check the installation status, run this PowerShell command:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se l'output visualizza **lo stato** di installazione **come disponibile**, significa che la funzionalità di backup di Windows Server è disponibile per l'installazione ma non installata nel server. Tuttavia, se Windows Server Backup non è installato, utilizzare uno dei metodi descritti di seguito per installarlo.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metodo 1: Installare Windows Server Backup utilizzando PowerShell

Per installare Windows Server Backup tramite PowerShell, eseguire il comando seguente:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metodo 2: Installare Windows Server Backup utilizzando Server Manager

Per installare Windows Server Backup utilizzando Server Manager, attenersi alla seguente procedura:

1. In **Server Manager ,** fare clic su Aggiungi ruoli e **funzionalità**. Viene visualizzata la **procedura guidata Aggiungi ruoli e funzionalità.**

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selezionare **Tipo di installazione** e fare clic su **Avanti**.

    ![Tipo di installazione](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selezionare un server dal pool di server e fare clic su **Avanti**. Nel ruolo server lasciare la selezione predefinita e fare clic su **Avanti**.
4. Selezionare **Windows Server Backup** nella scheda **Funzionalità** e fare clic su **Avanti**.

    ![funzionalità](./media/backup-azure-system-state-troubleshoot/features.png)

5. Nella scheda **Conferma,** fare clic su **Installa** per avviare il processo di installazione.
6. Nella scheda **Risultati,** verrà visualizzata la funzionalità Windows Server Backup è stata installata correttamente in Windows Server.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Autorizzazione Per le informazioni sul volume di sistema

Assicurarsi che il sistema locale disponga del controllo completo sulla cartella **System Volume Information** che si trova nel volume in cui è installato Windows. Di solito si tratta **di C: .** Backup di Windows Server può non riuscire se le autorizzazioni precedenti non sono impostate correttamente

### <a name="dependent-services"></a>Servizi dipendenti

Verificare che i servizi seguenti siano in esecuzione:

**Nome servizio** | **Tipo di avvio**
--- | ---
Chiamata di procedura remota (RPC) | Automatico
Sistema di eventi COM (EventSystem) | Automatico
Servizio di notifica eventi di sistema (SENS) | Automatico
Copia Shadow del volume (VSS) | Manuale
Provider di copie shadow del software Microsoft (SWPRV) | Manuale

### <a name="validate-windows-server-backup-status"></a>Convalidare lo stato di Windows Server Backup

Per convalidare lo stato di Windows Server Backup, attenersi alla seguente procedura:

- Verificare che WSB PowerShell sia in esecuzioneEnsure WSB PowerShell is running

  - Eseguire `Get-WBJob` da un PowerShell con privilegi elevati e assicurarsi che non restituisca l'errore seguente:Run from an elevated PowerShell and make sure it doesn't return the following error:

    > [!WARNING]
    > Get-WBJob: il termine 'Get-WBJob' non è riconosciuto come nome di un cmdlet, una funzione, un file di script o un programma funzionante. Verificare l'ortografia del nome, che il percorso sia incluso e corretto, quindi riprovare.

    - Se si verifica un errore con questo errore, reinstallare la funzionalità Windows Server Backup nel computer server come indicato nel passaggio 1 dei prerequisiti.

  - Verificare che il backup WSB funzioni correttamente eseguendo il comando seguente dal prompt dei comandi con privilegi elevati:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Sostituire X con la lettera dell'unità del volume in cui si desidera memorizzare l'immagine di backup dello stato del sistema.

    - Controllare periodicamente lo stato `Get-WBJob` del processo eseguendo il comando da PowerShell con privilegi elevati
    - Al termine del processo di backup, controllare `Get-WBJob -Previous 1` lo stato finale del processo eseguendo il comando

Se il processo non riesce, indica un problema WSB che provocherebbe un errore di backup dello stato di sistema dell'agente MARS.

## <a name="common-errors"></a>Errori comuni

### <a name="vss-writer-timeout-error"></a>Errore di timeout del writer VSS

| Sintomo | Causa | Risoluzione
| -- | -- | --
| - L'agente MARS non riesce con il messaggio di errore:"Processo WSB non riuscito con errori VSS. Controllare i registri eventi VSS per risolvere l'errore"<br/><br/> - Seguente log degli errori è presente nei registri eventi dell'applicazione VSS: "Un writer VSS ha rifiutato un evento con errore 0x800423f2, il timeout del writer scaduto tra gli eventi Freeze e Thaw."| Il writer VSS non è in grado di completare in tempo a causa della mancanza di risorse di CPU e memoria nel computer <br/><br/> Un altro software di backup sta già utilizzando il writer VSS, come risultato un'operazione snapshot non è stato possibile completare per questo backup | Attendere che la CPU/memoria venga liberata nel sistema o interrompere i processi che impiegano troppa memoria/CPU e riprovare. <br/><br/>  Attendere il completamento del backup in corso e provare l'operazione in un secondo momento quando nel computer non sono in esecuzione backup.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Spazio su disco insufficiente per l'aumento delle copie shadow

| Sintomo | Risoluzione
| -- | --
| - L'agente MARS non riesce con il messaggio di errore: Backup non riuscito poiché il volume della copia shadow non può aumentare a causa di spazio su disco insufficiente su volumi contenenti file di sistema <br/><br/> - Il seguente registro di errore/avviso è presente nei registri eventi di sistema volsnap: "Spazio su disco insufficiente nel volume C: per aumentare l'archiviazione delle copie shadow per le copie shadow di C: a causa di questo errore tutte le copie shadow del volume C: rischiano di essere eliminate" | - Liberare spazio nel volume evidenziato nel registro eventi in modo che vi sia spazio sufficiente per l'aumento delle copie shadow mentre il backup è in corso <br/><br/> - Durante la configurazione dello spazio delle copie shadow possiamo limitare la quantità di spazio utilizzato per la copia shadow. Per altre informazioni, vedere questo [articoloFor](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage) more information, see this article

### <a name="efi-partition-locked"></a>Partizione EFI bloccata

| Sintomo | Risoluzione
| -- | --
| Agente MARS ha esito negativo con il messaggio di errore: "Backup dello stato del sistema non riuscito come la partizione di sistema EFI è bloccata. Ciò può essere dovuto all'accesso alla partizione di sistema da parte di un software di sicurezza di terze parti o di backup" | - Se il problema è dovuto a un software di sicurezza di terze parti, è necessario contattare il fornitore Anti Virus in modo che possano consentire l'agente MARS <br/><br/> - Se è in esecuzione un software di backup di terze parti, attendere che venga completato e quindi riprovare a eseguire il backup

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sullo stato del sistema Windows nella distribuzione di Resource Manager, vedere [Eseguire il backup dello stato del sistema di Windows ServerFor more](backup-azure-system-state.md) information about Windows system state in Resource Manager deployment, see Back up Windows Server System State
