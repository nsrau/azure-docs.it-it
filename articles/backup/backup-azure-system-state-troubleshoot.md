---
title: Risolvere i problemi di backup dello stato del sistema
description: Questo articolo illustra come risolvere i problemi relativi al backup dello stato del sistema per i server Windows locali.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: f311de435d813cb0e6f8a2c3d932e05d695603f3
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583300"
---
# <a name="troubleshoot-system-state-backup"></a>Risolvere i problemi di backup dello stato del sistema

Questo articolo descrive le soluzioni per i problemi che potrebbero verificarsi durante l'uso del backup dello stato del sistema.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di iniziare la risoluzione dei problemi relativi al backup dello stato del sistema, è consigliabile eseguire la convalida seguente:

- [Verificare che l'agente di Servizi di ripristino di Microsoft Azure (MARS) sia aggiornato](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verificare la presenza di connettività di rete tra l'agente dei Servizi di ripristino di Microsoft Azure e Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Verificare che i Servizi di ripristino di Microsoft Azure siano in esecuzione (nella console di Servizio). Se necessario, riavviare e ripetere l'operazione
- [Verificare che sia disponibile il 5-10% di volume libero nel percorso della cartella dei file temporanei](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Il backup pianificato ha esito negativo, ma il backup manuale funziona](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Verificare che nel sistema operativo siano installati gli aggiornamenti più recenti
- [Verificare che le unità e i file non supportati con attributi non supportati siano esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Assicurarsi che l'**orologio** del sistema protetto sia configurato sul fuso orario corretto <br>
- [Assicurarsi che il server disponga almeno di .NET Framework versione 4.5.2 e versioni successive](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se si sta provando a **registrare di nuovo il server** in un insieme di credenziali, eseguire le operazioni seguenti: <br>
  - Verificare che l'agente sia stato disinstallato nel server e che sia stato eliminato dal portale <br>
  - Usare la stessa passphrase che è stata usata inizialmente per la registrazione del server <br>
- Se si tratta di un backup non in linea, verificare che Azure PowerShell versione 3.7.0 sia installato sia nel computer di origine che in quello di copia prima di iniziare l'operazione di backup offline
- [Considerazione quando l'agente di backup è in esecuzione in una macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Limitazione

- Il ripristino dello stato del sistema in un hardware diverso non è consigliato
- Il backup dello stato del sistema supporta attualmente i server Windows "locali". Questa funzionalità non è disponibile per le macchine virtuali di Azure.

## <a name="prerequisites"></a>Prerequisites

Prima di risolvere i problemi relativi al backup dello stato del sistema con backup di Azure, eseguire il controllo dei prerequisiti riportato di seguito.  

### <a name="verify-windows-server-backup-is-installed"></a>Verificare che Windows Server Backup sia installato

Assicurarsi che Windows Server Backup sia installato e abilitato nel server. Per verificare lo stato dell'installazione, eseguire questo comando di PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se l'output Visualizza lo **stato di installazione** come **disponibile**, significa che la funzionalità Windows Server backup è disponibile per l'installazione, ma non è installata nel server. Tuttavia, se Windows Server Backup non è installato, usare uno dei metodi seguenti per installarlo.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metodo 1: installare Windows Server Backup tramite PowerShell

Per installare Windows Server Backup usando PowerShell, eseguire il comando seguente:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metodo 2: installare Windows Server Backup usando Server Manager

Per installare Windows Server Backup usando Server Manager, seguire questa procedura:

1. In **Server Manager**fare clic su **Aggiungi ruoli e funzionalità**. Verrà visualizzata la **procedura guidata Aggiungi ruoli e funzionalità** .

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selezionare **tipo di installazione** e fare clic su **Avanti**.

    ![Tipo di installazione](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selezionare un server dal pool di server e fare clic su **Avanti**. Nel ruolo del server lasciare la selezione predefinita e fare clic su **Avanti**.
4. Selezionare **Windows Server Backup** nella scheda **funzionalità** e fare clic su **Avanti**.

    ![funzionalità](./media/backup-azure-system-state-troubleshoot/features.png)

5. Nella scheda **conferma** fare clic su **Installa** per avviare il processo di installazione.
6. Nella scheda **risultati** verrà visualizzata la funzionalità Windows Server backup viene installata correttamente in Windows Server.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Autorizzazione informazioni volume di sistema

Verificare che il sistema locale disponga del controllo completo sulla cartella **System Volume Information** che si trova nel volume in cui è installato Windows. Si tratta in genere di **informazioni sul volume c: System**. Windows Server backup può avere esito negativo se le autorizzazioni indicate sopra non sono impostate correttamente

### <a name="dependent-services"></a>Servizi dipendenti

Verificare che i servizi seguenti siano in stato di esecuzione:

**Nome servizio** | **Tipo di avvio**
--- | ---
RPC (Remote Procedure Call) | Automatico
Sistema di eventi COM+ (EventSystem) | Automatico
Servizio di notifica eventi di sistema (SENS) | Automatico
Copia shadow del volume (VSS) | Manuale
Microsoft Software Shadow Copy Provider (SWPRV) | Manuale

### <a name="validate-windows-server-backup-status"></a>Convalida stato Windows Server Backup

Per convalidare Windows Server Backup stato, seguire questa procedura:

- Verificare che Windows PowerShell sia in esecuzione

  - Eseguire `Get-WBJob` da un PowerShell con privilegi elevati e verificare che non venga restituito l'errore seguente:

    > [!WARNING]
    > Get-WBJob: il termine ' Get-WBJob ' non è riconosciuto come nome di un cmdlet, di una funzione, di un file di script o di un programma eseguibile. Verificare l'ortografia del nome, che il percorso sia incluso e corretto, quindi riprovare.

    - Se il problema persiste, reinstallare la funzionalità Windows Server Backup nel computer server come indicato nel passaggio 1 dei prerequisiti.

  - Verificare che il backup WSB funzioni correttamente eseguendo il comando seguente da un prompt dei comandi con privilegi elevati:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Sostituire X con la lettera di unità del volume in cui si desidera archiviare l'immagine di backup dello stato del sistema.

    - Controllare periodicamente lo stato del processo eseguendo `Get-WBJob` comando da PowerShell con privilegi elevati
    - Al termine del processo di backup, verificare lo stato finale del processo eseguendo `Get-WBJob -Previous 1` comando

Se il processo ha esito negativo, indica un problema di WSB che comporterebbe un errore di backup dello stato del sistema dell'agente MARS.

## <a name="common-errors"></a>Errori comuni

### <a name="vss-writer-timeout-error"></a>Errore di timeout del writer VSS

| Sintomo | Causa | Risoluzione
| -- | -- | --
| -L'agente MARS ha esito negativo con messaggio di errore: "processo WSB non riuscito con errori VSS. Controllare i registri eventi VSS per risolvere l'errore "<br/><br/> -Il log degli errori seguente è presente nei registri eventi dell'applicazione VSS: "un VSS writer ha rifiutato un evento con errore 0x800423f2, il timeout del writer è scaduto tra gli eventi Freeze e scongela".| Non è possibile completare il VSS writer nel tempo a causa della mancanza di risorse di CPU e memoria nel computer <br/><br/> Un altro software di backup sta già usando il VSS writer, perché non è stato possibile completare l'operazione di snapshot dei risultati per questo backup | Attendere che la CPU/memoria venga liberata nel sistema o interrompere i processi che richiedono una quantità eccessiva di memoria/CPU, quindi ripetere l'operazione. <br/><br/>  Attendere il completamento del backup in corso e riprovare a eseguire l'operazione in un secondo momento, se nel computer non è in esecuzione alcun backup.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Spazio su disco insufficiente per l'aumento delle copie shadow

| Sintomo | Risoluzione
| -- | --
| -Errore dell'agente MARS con messaggio di errore: il backup non è riuscito perché non è possibile aumentare il volume della copia shadow a causa di spazio su disco insufficiente nei volumi che contengono file di sistema <br/><br/> -Il registro errori/avvisi seguente è presente nei registri eventi di sistema di VolSnap: "spazio su disco insufficiente nel volume C: per aumentare l'archiviazione delle copie shadow per le copie shadow di C: a causa di questo errore, tutte le copie shadow del volume C: rischiano di essere eliminate" | -Liberare spazio nel volume evidenziato nel registro eventi in modo che sia disponibile spazio sufficiente per la crescita delle copie shadow mentre è in corso il backup. <br/><br/> -Durante la configurazione dello spazio di copia shadow è possibile limitare la quantità di spazio usata per la copia shadow. Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)

### <a name="efi-partition-locked"></a>Partizione EFI bloccata

| Sintomo | Risoluzione
| -- | --
| Errore dell'agente MARS con messaggio di errore: "il backup dello stato del sistema non è riuscito perché la partizione di sistema EFI è bloccata. Questo problema può essere dovuto all'accesso alla partizione di sistema da parte di una sicurezza di terze parti o di un backup software " | -Se il problema è dovuto a un software di protezione di terze parti, è necessario contattare il fornitore dell'antivirus in modo da consentire l'uso dell'agente MARS <br/><br/> -Se è in esecuzione un software di backup di terze parti, attenderne il completamento, quindi riprovare a eseguire il backup

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sullo stato del sistema Windows nella distribuzione di Gestione risorse, vedere [eseguire il backup dello stato del sistema Windows Server](backup-azure-system-state.md)
