---
title: Risolvere i problemi dell'agente di backup di Azure
description: Questo articolo illustra come risolvere i problemi di installazione e registrazione dell'agente di backup di Azure.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: dacurwin
ms.openlocfilehash: abd4e91b8fd3332191b58acf38daed06d03801be
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012847"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Risolvere i problemi relativi all'agente Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive come risolvere gli errori che potrebbero essere visualizzati durante la configurazione, la registrazione, il backup e il ripristino.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di iniziare la risoluzione dei problemi relativi all'agente di servizi di ripristino di Azure (MARS), è consigliabile verificare quanto segue:

- [Verificare che l'agente Mars sia](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)aggiornato.
- [Assicurarsi di disporre della connettività di rete tra l'agente Mars e Azure](https://aka.ms/AB-A4dp50).
- Verificare che MARS sia in esecuzione nella console del servizio. Se necessario, riavviare e ripetere l'operazione.
- [Verificare che nel percorso della cartella dei file temporanei sia disponibile il 5% fino al 10% di spazio libero](https://aka.ms/AB-AA4dwtt).
- [Controllare se un altro processo o software antivirus interferisce con backup di Azure](https://aka.ms/AB-AA4dwtk).
- Se il backup pianificato non riesce ma il backup manuale funziona, vedere [backup non eseguiti in base alla pianificazione](https://aka.ms/ScheduledBackupFailManualWorks).
- Verificare che il sistema operativo disponga degli aggiornamenti più recenti.
- [Verificare che le unità e i file non supportati con attributi non supportati siano esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Verificare che l'orologio del sistema protetto sia configurato sul fuso orario corretto.
- [Verificare che nel server sia installato .NET Framework 4.5.2 o versione successiva](https://www.microsoft.com/download/details.aspx?id=30653).
- Se si sta provando a registrare di nuovo il server in un insieme di credenziali:
  - Verificare che l'agente sia stato disinstallato sul server e che sia stato eliminato dal portale.
  - Utilizzare la stessa passphrase inizialmente utilizzata per registrare il server.
- Per i backup non in linea, verificare Azure PowerShell 3.7.0 sia installato sia nel computer di origine che in quello di copia prima di avviare il backup.
- Se l'agente di backup è in esecuzione in una macchina virtuale di Azure, vedere [questo articolo](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

**Messaggio di errore**: non sono state specificate credenziali dell'insieme di credenziali. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)

| Causa | Azioni consigliate |
| ---     | ---    |
| **Le credenziali dell'insieme di credenziali non sono valide** <br/> <br/> È possibile che i file dell'insieme di credenziali siano danneggiati o che siano scaduti. (Ad esempio, potrebbero essere state scaricate più di 48 ore prima dell'ora di registrazione).| Scaricare le nuove credenziali dall'insieme di credenziali di servizi di ripristino nel portale di Azure. Vedere il passaggio 6 nella sezione [scaricare l'agente Mars](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) . Eseguire quindi la procedura seguente, in base alle esigenze: <ul><li> Se è già stato installato e registrato MARS, aprire la console MMC di Backup di Microsoft Azure Agent, quindi selezionare **Registra server** nel riquadro **azioni** per completare la registrazione con le nuove credenziali. <br/> <li> Se la nuova installazione non riesce, provare a reinstallare con le nuove credenziali.</ul> **Nota**: se sono stati scaricati più file di credenziali dell'insieme di credenziali, solo il file più recente è valido per le ore 48 successive. Si consiglia di scaricare un nuovo file dell'insieme di credenziali.
| **Il server proxy/firewall sta bloccando la registrazione** <br/>oppure <br/>**Nessuna connettività Internet** <br/><br/> Se il computer o il server proxy ha una connettività Internet limitata e non si garantisce l'accesso per gli URL necessari, la registrazione avrà esito negativo.| Eseguire questi passaggi:<br/> <ul><li> Collaborare con il team IT per verificare che il sistema abbia la connettività Internet.<li> Se non si dispone di un server proxy, assicurarsi che l'opzione proxy non sia selezionata quando si registra l'agente. [Controllare le impostazioni del proxy](#verifying-proxy-settings-for-windows).<li> Se si dispone di un server proxy/firewall, collaborare con il team di rete per assicurarsi che gli URL e gli indirizzi IP abbiano accesso:<br/> <br> **URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Indirizzi IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Ripetere la registrazione dopo aver completato i passaggi precedenti per la risoluzione dei problemi.
| **Il software antivirus sta bloccando la registrazione** | Se nel server è installato un software antivirus, aggiungere le regole di esclusione necessarie all'analisi antivirus per i file e le cartelle seguenti: <br/><ul> <li> CBengine.exe <li> CSC. exe<li> Cartella Scratch. Il percorso predefinito è c:\Programmi\Microsoft Azure Recovery Services Agent\Scratch. <li> La cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Suggerimenti aggiuntivi

- Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.
- Verificare che la data e l'ora del computer corrispondano al fuso orario locale.
- Assicurarsi che [questi siti](backup-configure-vault.md#verify-internet-access) vengano aggiunti ai siti attendibili in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifica delle impostazioni proxy per Windows

1. Scaricare PsExec dalla pagina [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) .
1. Eseguire `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` da un prompt dei comandi con privilegi elevati.

   Tramite questo comando verrà aperto Internet Explorer.
1. Passare a **strumenti** > **Opzioni Internet** > **connessioni** > **Impostazioni LAN**.
1. Controllare le impostazioni proxy per l'account di sistema.
1. Se non è configurato alcun proxy e vengono forniti i dettagli del proxy, rimuovere i dettagli.
1. Se è stato configurato un proxy e i dettagli del proxy non sono corretti, verificare che i dettagli relativi all' **indirizzo IP** e alla **porta** del proxy siano corretti.
1. Chiudere Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Tipi di errore   | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali usando un browser diverso o eseguire le operazioni seguenti: <ul><li> Avviare Internet Explorer. Selezionare F12. </li><li> Passare alla scheda **rete** e deselezionare la cache e i cookie. </li> <li> Aggiornare la pagina.<br></li></ul> <li> Controllare se la sottoscrizione è disabilitata/scaduta.<br></li> <li> Controllare se una regola del firewall sta bloccando il download. <br></li> <li> Assicurarsi di non aver esaurito il limite nell'insieme di credenziali (50 computer per ogni insieme di credenziali).<br></li>  <li> Verificare che l'utente disponga delle autorizzazioni di backup di Azure necessarie per scaricare le credenziali dell'insieme di credenziali e registrare un server con l'insieme di credenziali. Vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Tipi di errore  | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| <br /><ul><li>L'agente del servizio di ripristino Microsoft Azure non è riuscito a connettersi al Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e assicurarsi di essere in grado di connettersi a Internet.<li>(407) autenticazione proxy obbligatoria. |Un proxy blocca la connessione. |  <ul><li>In Internet Explorer passare a **strumenti** > **Opzioni internet** > **sicurezza** > **Internet**. Selezionare **livello personalizzato** e scorrere verso il basso fino alla sezione **download del file** . Selezionare **Abilita**.<p>Potrebbe inoltre essere necessario aggiungere [URL e indirizzi IP](backup-configure-vault.md#verify-internet-access) ai siti attendibili in Internet Explorer.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<li> Se il computer ha accesso a Internet limitato, verificare che le impostazioni del firewall nel computer o nel proxy consentano questi [URL e indirizzi IP](backup-configure-vault.md#verify-internet-access). <li>Se nel server è installato un software antivirus, escludere questi file dall'analisi antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un file CSC. exe per ogni versione di .NET Framework installata nel server. Escludere i file CSC. exe per tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella scratch o della cache. <br>Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch.<li>La cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Tipi di errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| <br />Impossibile impostare la chiave di crittografia per i backup protetti. L'attivazione non è stata completata, ma la passphrase di crittografia è stata salvata nel file seguente. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server dall'insieme di credenziali e registrarlo nuovamente con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Tipi di errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|<br />L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft.     | <li> La cartella Scratch si trova in un volume che non dispone di spazio sufficiente. <li> La cartella Scratch è stata spostata in modo errato. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'agente Mars.<li>Spostare la cartella scratch o la posizione della cache in un volume con spazio libero compreso tra 5% e 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere i passaggi in [domande frequenti sul backup di file e cartelle](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Tipi di errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
| <br />Errore 34506. La passphrase di crittografia archiviata nel computer non è configurata correttamente.    | <li> La cartella Scratch si trova in un volume che non dispone di spazio sufficiente. <li> La cartella Scratch è stata spostata in modo errato. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella scratch o la posizione della cache in un volume con spazio libero compreso tra 5% e 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere i passaggi in [domande frequenti sul backup di file e cartelle](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>I backup non vengono eseguiti in base alla pianificazione

Se i backup pianificati non vengono attivati automaticamente, ma i backup manuali funzionano correttamente, provare a eseguire le azioni seguenti:

- Assicurarsi che la pianificazione di Windows Server backup non sia in conflitto con la pianificazione del backup di file e cartelle di Azure.

- Verificare che lo stato del backup online sia impostato su **Abilita**. Per verificare lo stato, seguire questa procedura:

  1. In Utilità di pianificazione espandere **Microsoft** e selezionare **backup online**.
  1. Fare doppio clic su **Microsoft-onlinebackup** e passare alla scheda **trigger** .
  1. Verificare che lo stato sia impostato su **abilitato**. In caso contrario, selezionare **modifica**, selezionare **abilitato**e quindi fare clic su **OK**.

- Verificare che l'account utente selezionato per l'esecuzione dell'attività sia il **gruppo di amministratori** di **sistema** o locale nel server. Per verificare l'account utente, passare alla scheda **generale** e controllare le opzioni di **sicurezza** .

- Verificare che nel server sia installato PowerShell 3,0 o versione successiva. Per controllare la versione di PowerShell, eseguire questo comando e verificare che il numero di versione del `Major` sia 3 o successivo:

  `$PSVersionTable.PSVersion`

- Verificare che questo percorso faccia parte della variabile di ambiente `PSMODULEPATH`:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di esecuzione di PowerShell per `LocalMachine` sono impostati su `restricted`, il cmdlet di PowerShell che attiva l'attività di backup potrebbe non riuscire. Eseguire questi comandi in modalità con privilegi elevati per controllare e impostare i criteri di esecuzione su `Unrestricted` o `RemoteSigned`:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Assicurarsi che non siano presenti file MSOnlineBackup del modulo di PowerShell mancanti o danneggiati. Se sono presenti file danneggiati o mancanti, seguire questa procedura:

  1. Da qualsiasi computer in cui è presente un agente MARS che funziona correttamente, copiare la cartella MSOnlineBackup da C:\Programmi\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Nel computer problematico incollare i file copiati nello stesso percorso della cartella (C:\Programmi\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se è già presente una cartella MSOnlineBackup nel computer, incollarvi i file o sostituire eventuali file esistenti.

> [!TIP]
> Per garantire che le modifiche vengano applicate in modo coerente, riavviare il server dopo aver eseguito i passaggi precedenti.

## <a name="troubleshoot-restore-problems"></a>Risolvere i problemi di ripristino

Backup di Azure potrebbe non montare il volume di ripristino in modo corretto, anche dopo diversi minuti. È possibile che vengano visualizzati messaggi di errore durante il processo. Per avviare normalmente il ripristino, seguire questa procedura:

1. Annullare il processo di montaggio se è in esecuzione per diversi minuti.

2. Controllare se è presente la versione più recente dell'agente di backup. Per controllare la versione, nel riquadro **azioni** della console Mars selezionare **about Servizi di ripristino di Microsoft Azure Agent**. Verificare che il numero di **versione** sia uguale a o superiore alla versione citata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). Selezionare questo collegamento per [scaricare la versione più recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Passare a **Device Manager** > **controller di archiviazione** e individuare **iniziatore iSCSI Microsoft**. Se viene individuato, andare direttamente al passaggio 7.

4. Se non è possibile individuare il servizio iniziatore iSCSI Microsoft, provare a trovare una voce in **Device Manager** > **controller di archiviazione** denominati **Device Unknown** con ID hardware **ROOT\ISCSIPRT**.

5. Fare clic con il pulsante destro del mouse su **dispositivo sconosciuto** e selezionare **Aggiorna software driver**.

6. Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Questo aggiornamento deve modificare il **dispositivo sconosciuto** nell' **iniziatore iSCSI Microsoft**:

    ![Schermata di Gestione dispositivi di Backup di Azure, con l'opzione Controller di archiviazione evidenziata](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Passare a **Gestione attività** > **Servizi (locale)**  > **servizio iniziatore iSCSI Microsoft**:

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio e scegliere **Arresta**. Quindi fare di nuovo clic con il pulsante destro del mouse e scegliere **Avvia**.

9. Ripetere il ripristino usando l'opzione [Ripristino istantaneo](backup-instant-restore-capability.md).

Se il ripristino ha ancora esito negativo, riavviare il server o il client. Se non si vuole riavviare il sistema o se il ripristino ha esito negativo anche dopo il riavvio del server, provare a eseguire il ripristino [da un altro computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Risolvere i problemi relativi alla cache

L'operazione di backup potrebbe non riuscire se la cartella della cache (detta anche cartella Scratch) non è configurata correttamente, mancano i prerequisiti o l'accesso è limitato.

### <a name="prerequisites"></a>prerequisiti

Affinché le operazioni dell'agente MARS abbiano esito positivo, la cartella della cache deve rispettare i requisiti seguenti:

- [Verificare che il 5% del 10% di spazio disponibile nel percorso della cartella Scratch](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Verificare che il percorso della cartella Scratch sia valido e accessibile](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Verificare che gli attributi di file nella cartella della cache siano supportati](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Verificare che lo spazio di archiviazione della copia shadow allocato sia sufficiente per il processo di backup](#increase-shadow-copy-storage)
- [Assicurarsi che non vi siano altri processi (ad esempio, software antivirus) che limitano l'accesso alla cartella della cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumenta l'archiviazione delle copie shadow

Le operazioni di backup potrebbero non riuscire se lo spazio di archiviazione della copia shadow non è sufficiente per proteggere l'origine dati. Per risolvere questo problema, aumentare lo spazio di archiviazione della copia shadow nel volume protetto usando vssadmin, come illustrato di seguito:

- Controllare lo spazio di archiviazione shadow corrente dal prompt dei comandi con privilegi elevati:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumentare lo spazio di archiviazione shadow usando il comando seguente:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Un altro processo o un software antivirus che blocca l'accesso alla cartella della cache

Se nel server è installato un software antivirus, aggiungere le regole di esclusione necessarie all'analisi antivirus per i file e le cartelle seguenti:  

- Cartella Scratch. Il percorso predefinito è c:\Programmi\Microsoft Azure Recovery Services Agent\Scratch
- La cartella bin in C:\Programmi\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- CSC. exe

## <a name="common-issues"></a>Problemi comuni

Questa sezione descrive gli errori comuni che si verificano durante l'uso dell'agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Messaggio di errore | Azione consigliata |
-- | --
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al checksum di backup archiviato nell'area di lavoro | Per risolvere il problema, eseguire le operazioni seguenti e riavviare il server <br/> - [verificare se è presente un antivirus o altri processi che bloccano i file del percorso dei file](#another-process-or-antivirus-software-blocking-access-to-cache-folder) temporanei<br/> - [controllare se il percorso dei file temporanei è valido e accessibile all'agente Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Messaggio di errore | Azione consigliata |
-- | --
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al percorso dei file temporanei per inizializzare VHD | Per risolvere il problema, eseguire le operazioni seguenti e riavviare il server <br/> - [verificare se è presente un antivirus o altri processi che bloccano i file del percorso dei file](#another-process-or-antivirus-software-blocking-access-to-cache-folder) temporanei<br/> - [controllare se il percorso dei file temporanei è valido e accessibile all'agente Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Messaggio di errore | Azione consigliata |
-- | --
Il backup non è riuscito a causa di spazio di archiviazione insufficiente nel volume in cui si trova la cartella Scratch | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/>- [verificare che l'agente Mars sia più recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [verificare e risolvere i problemi di archiviazione che incidono sull'area scratch del backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Messaggio di errore | Azione consigliata |
-- | --
Impossibile trovare le modifiche in un file. Il problema potrebbe essere legato a vari motivi. Ripetere l'operazione | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/> - [verificare che l'agente Mars sia più recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [verificare e risolvere i problemi di archiviazione che incidono sull'area scratch del backup](#prerequisites)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [come eseguire il backup di Windows Server con l'agente di backup di Azure](tutorial-backup-windows-server-to-azure.md).
- Se è necessario ripristinare un backup, vedere [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
