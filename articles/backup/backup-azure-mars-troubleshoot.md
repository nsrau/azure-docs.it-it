---
title: Risolvere i problemi di Azure Backup agent
description: Risolvere i problemi di installazione e registrazione dell'agente di Backup di Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434019"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Risolvere i problemi dell'agente di Microsoft Azure Recovery Services (MARS)

Questo articolo descrive come risolvere errori che potrebbero verificarsi durante la configurazione, la registrazione, backup e ripristino.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

È consigliabile verificare quanto segue prima di iniziare la risoluzione dei problemi Microsoft l'agente di servizi di ripristino di Azure (MARS):

- [Verificare che l'agente MARS è aggiornato](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Assicurarsi di disporre della connettività di rete tra l'agente MARS e Azure](https://aka.ms/AB-A4dp50).
- Verificare che MARS è in esecuzione (nella console del servizio). Se è necessario, riavviare e ripetere l'operazione.
- [Verificare che lo spazio libero volume 5% al 10% è disponibile nel percorso della cartella scratch](https://aka.ms/AB-AA4dwtt).
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](https://aka.ms/AB-AA4dwtk).
- Se pianificati backup ha esito negativo ma funzionamento del backup manuale, vedere [non vengono eseguiti i backup in base alla pianificazione](https://aka.ms/ScheduledBackupFailManualWorks).
- Verificare che il sistema operativo siano installati gli aggiornamenti più recenti.
- [Verificare che le unità non supportate e i file con attributi non supportati vengono esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Verificare che l'orologio di sistema protetta è configurato per il fuso orario corretto.
- [Assicurarsi che .NET Framework 4.5.2 o versione successiva è installato nel server](https://www.microsoft.com/download/details.aspx?id=30653).
- Se si sta provando a registrare di nuovo il server a un insieme di credenziali:
  - Verificare che l'agente viene disinstallato sul server e che viene eliminato dal portale.
  - Usare la stessa passphrase usata inizialmente per registrare il server.
- Per i backup offline, verificare che Azure PowerShell 3.7.0 è installato nel computer di copia sia l'origine prima di avviare il backup.
- Se l'agente di Backup è in esecuzione in una macchina virtuale di Azure, vedere [questo articolo](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

**Messaggio di errore**: Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)

| Causa | Azioni consigliate |
| ---     | ---    |
| **Insieme di credenziali non valide** <br/> <br/> I file delle credenziali dell'insieme di credenziali potrebbero essere danneggiati o potrebbero essere scaduto. (Ad esempio, si potrebbe sono state scaricate più di 48 ore prima dell'ora di registrazione.)| Scaricare le nuove credenziali dall'insieme di credenziali di servizi di ripristino nel portale di Azure. (Vedere il passaggio 6 nel [scaricare l'agente MARS](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) sezione.) Quindi eseguire questi passaggi, come appropriato: <ul><li> Se già installato e registrato MARS, aprire la console di MMC dell'agente di Backup di Microsoft Azure e quindi selezionare **registra Server** nel **azioni** per completare la registrazione con il nuovo riquadro credenziali. <br/> <li> Se la nuova installazione non riesce, provare a reinstallare con le nuove credenziali.</ul> **Nota**: Se sono stati scaricati più file delle credenziali dell'insieme di credenziali, solo i file più recente è valido per 48 ore successive. Si consiglia di scaricare un nuovo file delle credenziali dell'insieme di credenziali.
| **Server proxy/firewall sta bloccando la registrazione** <br/>oppure <br/>**Nessuna connettività a internet** <br/><br/> Se il computer o un server proxy ha limitato la connettività internet e non garantire l'accesso per gli URL necessari, la registrazione avrà esito negativo.| Eseguire questi passaggi:<br/> <ul><li> Rivolgersi al team IT per assicurarsi che il sistema disponga della connettività internet.<li> Se non si dispone di un server proxy, assicurarsi che non è selezionata l'opzione proxy quando si registra l'agente. [Controllare le impostazioni del proxy](#verifying-proxy-settings-for-windows).<li> Se si dispone di un server proxy/firewall, lavorare con il team di rete per assicurarsi che questi URL e gli indirizzi IP dispongono di accesso:<br/> <br> **URL**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Indirizzi IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Ripetere la registrazione dopo aver completato i passaggi di risoluzione dei problemi precedenti.
| **Il software antivirus blocca la registrazione** | Se si dispone di un software antivirus installato nel server, aggiungere regole di esclusione necessari per l'analisi antivirus per questi file e cartelle: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> La cartella dei file temporanei. Il percorso predefinito è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Nella cartella bin in C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Consigli aggiuntivi
- Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.
- Assicurarsi che data del computer e corrispondenza ora al fuso orario locale.
- Assicurarsi [questi siti](backup-configure-vault.md#verify-internet-access) vengono aggiunti all'elenco dei siti attendibili in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifica per determinare se le impostazioni proxy per Windows

1. PsExec da scaricare il [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) pagina.
1. Eseguire `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` da un prompt dei comandi con privilegi elevati.

   Questo comando verrà aperto Internet Explorer.
1. Passare a **degli strumenti** > **Opzioni Internet** > **connessioni** > **impostazioni LAN**.
1. Controllare le impostazioni proxy per l'account di sistema.
1. Se è stato configurato alcun proxy e vengono forniti i dettagli proxy, rimuovere i dettagli.
1. Se è stato configurato un proxy e i dettagli del proxy non sono corrette, verificare che il **IP Proxy** e **porta** dettagli siano corretti.
1. Chiudere Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Tipi di errore   | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali tramite un browser diverso o eseguire questi passaggi: <ul><li> Avviare Internet Explorer. Selezionare F12. </li><li> Andare alla **rete** scheda e cancellare la cache e cookie. </li> <li> Aggiornare la pagina.<br></li></ul> <li> Controllare se la sottoscrizione è disabilitato o scaduto.<br></li> <li> Controllare se qualsiasi regola del firewall sta bloccando i download. <br></li> <li> Verificare che ancora stato esaurito il limite per l'insieme di credenziali (50 computer per ogni insieme di credenziali).<br></li>  <li> Verificare che l'utente disponga delle autorizzazioni di Backup di Azure necessarie per scaricare le credenziali dell'insieme di credenziali e registrare un server con l'insieme di credenziali. Visualizzare [controllo di accesso degli accessi in base a gestire i punti di ripristino di Backup di Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Tipi di errore  | Causa possibile | Azioni consigliate |
| ---     | ---     | ---    |
| <br /><ul><li>L'agente di Microsoft Azure Recovery Service non è riuscito a connettersi a Backup di Microsoft Azure. (ID: 100050) controllare le impostazioni di rete e verificare che sia in grado di connettersi a internet.<li>(407) autenticazione proxy necessaria. |La connessione è bloccata da un proxy. |  <ul><li>In Internet Explorer passare a **degli strumenti** > **Opzioni Internet** > **sicurezza** > **Internet**. Selezionare **livello personalizzato** e scorrere verso il basso il **download di File** sezione. Selezionare **Abilita**.<p>Si potrebbe anche essere necessario aggiungere [URL e indirizzi IP](backup-configure-vault.md#verify-internet-access) ai siti attendibili in Internet Explorer.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<li> Se il computer ha limitato l'accesso a internet, assicurarsi che le impostazioni del firewall nel computer o nel proxy consentano questi [URL e indirizzi IP](backup-configure-vault.md#verify-internet-access). <li>Se si dispone di un software antivirus installato nel server, escludere questi file dall'analisi dell'antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un CSC.exe per ogni versione di .NET Framework installata nel server. Escludi file CSC.exe per tutte le versioni di .NET Framework nel server interessato. <li>Il percorso cache o nella cartella file temporaneo. <br>Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Nella cartella bin in C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Tipi di errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| <br />Impossibile impostare la chiave di crittografia per i backup protetti. Attivazione non è stata completata, ma la passphrase di crittografia è stata salvata nel seguente file. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione di server dall'insieme di credenziali e registrarlo nuovamente con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Tipi di errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|<br />L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione in un secondo momento. Se il problema persiste, contattare il supporto tecnico Microsoft.     | <li> La cartella scratch si trova in un volume che non ha spazio sufficiente. <li> La cartella scratch è stata spostata in modo non corretto. <li> Il file OnlineBackup.KEK è mancante.         | <li>Aggiornamento per il [versione più recente](https://aka.ms/azurebackup_agent) dell'agente MARS.<li>Spostare il percorso cache o nella cartella file temporaneo in un volume con spazio libero è compreso tra 5 e 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere i passaggi descritti in [domande frequenti sul backup di file e cartelle](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Tipi di errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
| <br />Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente.    | <li> La cartella scratch si trova in un volume che non ha spazio sufficiente. <li> La cartella scratch è stata spostata in modo non corretto. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare il percorso cache o nella cartella file temporaneo in un volume con spazio libero è compreso tra 5 e 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere i passaggi descritti in [domande frequenti sul backup di file e cartelle](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>I backup non vengono eseguiti in base alla pianificazione
Se i backup pianificati non viene generati automaticamente, ma i backup manuali vengono eseguiti correttamente, effettuare le azioni seguenti:

- Verificare che la pianificazione di backup di Windows Server non è in conflitto con la pianificazione del backup cartelle e file di Azure.

- Verificare che lo stato del backup online è impostato su **abilitare**. Per verificare lo stato, eseguire questi passaggi:

  1. Nell'utilità di pianificazione, espandere **Microsoft** e selezionare **Backup Online**.
  1. Fare doppio clic su **Microsoft-OnlineBackup** e passare alle **trigger** scheda.
  1. Controllare se lo stato è impostato su **abilitato**. In caso contrario, selezionare **Edit**, selezionare **Enabled**, quindi selezionare **OK**.

- Assicurarsi che l'account utente selezionato per l'esecuzione dell'attività è uno **SYSTEM** oppure **gruppo locale Administrators** nel server. Per verificare l'account utente, passare al **generali** scheda e selezionare il **sicurezza** opzioni.

- Verificare che PowerShell 3.0 o versione successiva sia installato nel server. Per controllare la versione di PowerShell, eseguire questo comando e verificare che il `Major` numero di versione è 3 o versioni successive:

  `$PSVersionTable.PSVersion`

- Verificare che questo percorso fa parte di `PSMODULEPATH` variabile di ambiente:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di esecuzione PowerShell per `LocalMachine` è impostata su con restrizioni, il cmdlet di PowerShell che attiva l'attività di backup potrebbe non riuscire. Eseguire questi comandi con privilegi elevati per controllare e impostare i criteri di esecuzione a una delle due `Unrestricted` o `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Assicurarsi che non sono presenti file MSOnlineBackup del modulo di PowerShell mancante o danneggiati. Se sono presenti file mancanti o danneggiati, eseguire questi passaggi:

  1. Da qualsiasi computer che dispone di un agente di MARS che funziona correttamente, copiare la cartella MSOnlineBackup da C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Nel computer problematico, incollare i file copiati nella stessa posizione di cartella (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se è già presente una cartella MSOnlineBackup nel computer, incollare i file al suo interno oppure sostituire tutti i file esistenti.


> [!TIP]
> Per verificare che le modifiche vengono applicate in modo coerente, riavviare il server dopo aver eseguito i passaggi precedenti.


## <a name="troubleshoot-restore-problems"></a>Risolvere i problemi di ripristino

Backup di Azure potrebbe non montare il volume di ripristino in modo corretto, anche dopo diversi minuti. E si potrebbero ricevere messaggi di errore durante il processo. Per avviare normalmente il ripristino, eseguire questi passaggi:

1.  Annullare il processo di montaggio se è in esecuzione per alcuni minuti.

2.  Determinare se è la versione più recente dell'agente di Backup. Controllo della versione, scegliere il **azioni** riquadro della console di MARS, seleziona **su Microsoft Azure Recovery Services Agent**. Verificare che il numero di **versione** sia uguale a o superiore alla versione citata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). Selezionare questo collegamento per [scaricare la versione più recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Passare a **Gestione dispositivi** > **controller di archiviazione** individuare **iniziatore iSCSI Microsoft**. Se si individuarlo, andare direttamente al passaggio 7.

4.  Se non è possibile individuare il servizio iniziatore iSCSI Microsoft, provare a trovare una voce nella sezione **Gestione dispositivi** > **controller di archiviazione** denominata **dispositivo sconosciuto** con l'ID Hardware **ROOT\ISCSIPRT**.

5.  Fare doppio clic su **dispositivo sconosciuto** e selezionare **aggiornamento Software Driver**.

6.  Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Questo aggiornamento deve cambiare **dispositivo sconosciuto** al **iniziatore iSCSI Microsoft**:

    ![Schermata di Gestione dispositivi di Backup di Azure, con l'opzione Controller di archiviazione evidenziata](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Passare a **Task Manager** > **servizi (locale)**  > **servizio iniziatore iSCSI Microsoft**:

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, il servizio e scegliere **arrestare**. Quindi fare di nuovo doppio clic e selezionare **avviare**.

9.  Ripetere il ripristino usando l'opzione [Ripristino istantaneo](backup-instant-restore-capability.md).

Se il ripristino non riesce, riavviare il server o client. Se non si desidera riavviare, o se il ripristino non riesce anche dopo il riavvio del server, provare [il ripristino da un altro computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi
* Ottenere altre informazioni sulla [come eseguire il backup di Windows Server con Azure Backup agent](tutorial-backup-windows-server-to-azure.md).
* Se è necessario ripristinare un backup, vedere [ripristinare i file in una macchina Windows](backup-azure-restore-windows-server.md).
