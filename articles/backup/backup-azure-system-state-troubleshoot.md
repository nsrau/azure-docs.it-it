---
title: Risolvere i problemi dello stato del sistema di Backup con Backup di Azure
description: Risolvere i problemi nel Backup dello stato del sistema.
services: backup
author: srinathvasireddy
manager: sivan
keywords: modalità di backup; stato del sistema di backup
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathv
ms.openlocfilehash: 87b5fff58ecf9e89bc94f31a0bc3a591c146c88f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705008"
---
# <a name="troubleshoot-system-state-backup"></a>Risolvere i problemi di Backup dello stato del sistema

Questo articolo vengono descritte soluzioni per problemi che possono verificarsi durante l'utilizzo di Backup dello stato del sistema.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base
Si consiglia di eseguire il seguente convalida, prima di iniziare la risoluzione dei problemi di backup dello stato del sistema:

- [Verificare che l'agente di Microsoft Azure Recovery Services (MARS) sia aggiornato](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verificare la presenza di connettività di rete tra l'agente dei Servizi di ripristino di Microsoft Azure e Azure](https://aka.ms/AB-A4dp50)
- Verificare che i Servizi di ripristino di Microsoft Azure siano in esecuzione (nella console di Servizio). Se richiesto, riavviare e ripetere l'operazione
- [Verificare che sia disponibile il 5-10% di volume libero nel percorso della cartella dei file temporanei](https://aka.ms/AB-AA4dwtt)
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](https://aka.ms/AB-AA4dwtk)
- [Il backup pianificato ha esito negativo, ma il backup manuale funziona](https://aka.ms/ScheduledBackupFailManualWorks)
- Verificare che nel sistema operativo siano installati gli aggiornamenti più recenti
- [Verificare che le unità non supportate e i file con attributi non supportati vengono esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Assicurarsi che l'**orologio** del sistema protetto sia configurato sul fuso orario corretto <br>
- [Assicurarsi che il server disponga almeno di .NET Framework versione 4.5.2 e versioni successive](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se si sta tentando di **ripetere la registrazione del server** in un insieme di credenziali, allora: <br>
  - Verificare che l'agente sia disinstallato nel server e sia eliminato dal portale <br>
  - Usare la stessa passphrase che è stata usata inizialmente per la registrazione del server <br>
- In caso di backup non in linea, assicurarsi che Azure PowerShell versione 3.7.0 sia installato nel computer di origine e di copia prima di iniziare l'operazione di backup offline
- [Considerazione quando viene eseguito l'agente di Backup in una macchina virtuale di Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Limitazione
- Il ripristino dello stato del sistema in un hardware diverso non è consigliato
- Backup dello stato del sistema supporta attualmente "locale" Windows Server, questa funzionalità non è disponibile per macchine virtuali di Azure.

## <a name="pre-requisite"></a>Prerequisito.

Prima di iniziare è risolvere i problemi di Backup dello stato del sistema con Backup di Azure, assicurarsi eseguire i seguenti prerequisiti controllare.  

### <a name="verify-windows-server-backup-is-installed"></a>Verificare che sia installato Windows Server Backup

Verificare che Windows Server Backup sia installato e abilitato nel server. Per controllare lo stato dell'installazione, eseguire il comando PowerShell seguente:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Se l'output visualizza il **lo stato di installazione** come **disponibili**, significa che funzionalità di Windows Server backup è disponibile per l'installazione, ma non è installata nel server. Tuttavia se non è installato Windows Server Backup, quindi usare uno dei seguenti metodi per installarlo.

**Metodo 1: Installare Windows Server Backup con PowerShell**

Per installare Windows Server Backup con PowerShell, eseguire il comando seguente:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Metodo 2: Installare Windows Server Backup con Server Manager**

Per installare Windows Server Backup con Server Manager, eseguire il seguente:

1. Nel **Sever Manger** e fare clic su **Aggiungi ruoli e funzionalità**. Il **Aggiunta guidata ruoli e funzionalità** viene visualizzata.

    ![dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selezionare **tipo di installazione** e fare clic su **successivo**.

    ![Tipo di installazione](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selezionare un server dal pool di server e fare clic su **successivo**. Ruolo del Server, lasciare la selezione predefinita e fare clic su **successivo**.
4. Selezionare **Windows Server Backup** nelle **caratteristiche** scheda e fare clic su **Avanti**.

    ![funzionalità](./media/backup-azure-system-state-troubleshoot/features.png)

5. Nel **conferma** scheda, fare clic su **installare** per avviare il processo di installazione.
6. Nel **risultati** scheda, verrà visualizzata la funzionalità è installata correttamente nel Server Windows di Windows Server Backup.

    ![risultato](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Autorizzazione di informazioni di sistema Volume

Assicurarsi che il sistema locale abbia il controllo completo sui **System Volume Information** cartella si trova nel volume in cui è installato windows. Si tratta in genere **C:\System Volume Information**. Windows Server backup può non riuscire se le autorizzazioni indicate in precedenza non sono impostate correttamente

### <a name="dependent-services"></a>Servizi dipendenti

Verificare che i servizi seguenti siano in stato di esecuzione:

**Nome del servizio** | **Tipo di avvio**
--- | ---
Call(RPC) RPC | Automatico
System(EventSystem) eventi COM+ | Automatico
Service(SENS) notifica eventi di sistema | Automatico
Volume Shadow Copy(VSS) | Manuale
Provider(SWPRV) di copia Shadow Software Microsoft | Manuale

### <a name="validate-windows-server-backup-status"></a>Convalidare lo stato di Windows Server Backup

Per convalidare lo stato di Windows Server Backup, eseguire il seguente:

  * Verificare che WSB di PowerShell è in esecuzione

    -   Eseguire `Get-WBJob` da un'elevata PowerShell e assicurarsi che non viene restituito l'errore seguente:

    > [!WARNING]
    > Get-WBJob: Il termine 'Get-WBJob' non è riconosciuto come il nome di un cmdlet, funzione, file di script o programma eseguibile. Controllare l'ortografia del nome o verificare che il percorso sia incluso e corretto, quindi riprovare.

    -   In caso di errore con l'errore quindi reinstallare la funzionalità di Windows Server Backup nel computer server come indicato nei prerequisiti passaggio 1.

  * Assicurarsi che funzioni correttamente, il backup di Windows Server Backup eseguendo il seguente comando dal prompt dei comandi con privilegi elevati:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Sostituire X con la lettera di unità del volume in cui si desidera archiviare lo stato del sistema di eseguire il backup di immagine.

    - Controllare periodicamente lo stato del processo eseguendo `Get-WBJob` comando di PowerShell con privilegi elevati        
    - Termine del processo di backup, controllare lo stato del processo finale eseguendo `Get-WBJob -Previous 1` comando

Se il processo non riesce, indica un problema di Windows Server Backup con la conseguenza di un errore di backup dello stato di sistema dell'agente di MARS.

## <a name="common-errors"></a>Errori comuni

### <a name="vss-writer-timeout-error"></a>Errore di timeout del servizio VSS Writer

| Sintomo | Causa | Risoluzione
| -- | -- | --
| -Agente di MARS ha esito negativo con messaggio di errore: "Il processo di Windows Server Backup non è riuscita con errori VSS. Controllare i registri eventi VSS per risolvere l'errore"<br/><br/> -Errore di log è presente nel registro eventi dell'applicazione VSS: "Un writer VSS ha rifiutato un evento con l'errore 0x800423f2, il timeout del writer è scaduto tra gli eventi blocca e sblocca".| Servizio VSS writer non riesce a viene completata nel tempo a causa di mancanza di risorse di CPU e memoria nel computer <br/><br/> Un altro software di backup sta già usando il VSS writer, di conseguenza l'operazione di snapshot non è stato possibile completare per il backup | In attesa di CPU/memoria è possibile liberare nel sistema o interrompere i processi richiede una quantità eccessiva di CPU/memoria e ripetere l'operazione <br/><br/>  Attendere che il backup in corso completare e ripetere l'operazione in un secondo momento quando nessun backup è in esecuzione nel computer


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Spazio su disco insufficiente a crescere le copie shadow

| Sintomo | Risoluzione
| -- | --
| -Agente di MARS ha esito negativo con messaggio di errore: Backup non è riuscito perché il volume copia shadow non è stato possibile aumentare a causa di spazio su disco insufficiente per i volumi contenenti i file di sistema <br/><br/> -Log di errore/avviso successivo è presente nei registri eventi di sistema volsnap: "Si è verificato lo spazio su disco insufficiente nel volume c: aumento delle dimensioni di archiviazione della copia shadow per copie shadow di c: a causa di questo errore tutte le copie shadow del volume c: esposte al rischio di eliminazione" | -Liberare spazio nel volume evidenziato nel registro eventi in modo che sia disponibile spazio sufficiente per l'aumento delle dimensioni durante l'esecuzione di backup delle copie shadow <br/><br/> -Quando si configura lo spazio di copia shadow è possibile limitare la quantità di spazio utilizzato per la copia shadow, per altre informazioni vedere questo [articolo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>Partizione EFI bloccata

| Sintomo | Risoluzione
| -- | --
| L'agente MARS ha esito negativo con messaggio di errore: "Nuovamente lo stato del sistema massimo non è riuscita perché la partizione di sistema EFI è bloccata. Ciò può essere a causa dell'accesso di partizione di sistema per un sicurezza di terze parti o software di backup" | -Se il problema è dovuto a un software di sicurezza di terze parti, quindi è necessario contattare il fornitore Anti antivirus in modo che possono consentire l'agente MARS <br/><br/> -Se è in esecuzione un software di backup di terze parti, quindi attendere il completamento, quindi ripetere back backup


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sullo stato del sistema Windows nella distribuzione di Resource Manager, vedere [eseguire il backup dello stato del sistema di Windows Server](backup-azure-system-state.md)
