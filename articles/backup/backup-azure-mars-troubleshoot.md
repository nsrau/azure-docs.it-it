---
title: Risolvere i problemi relativi all'agente di Backup di AzureTroubleshoot the Azure Backup agent
description: In questo articolo viene illustrato come risolvere i problemi relativi all'installazione e alla registrazione dell'agente di Backup di Azure.In this article s' learn how to troubleshoot the installation and registration of the Azure Backup agent.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: a15f8a4531bc31dab5b99e125454b0d9c4fd4521
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421283"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Risolvere i problemi relativi all'agente di Microsoft Azure Recovery Services (MARS)

In questo articolo viene descritto come risolvere gli errori che potrebbero verificarsi durante la configurazione, la registrazione, il backup e il ripristino.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di iniziare a risolvere i problemi relativi all'agente di Microsoft Azure Recovery Services (MARS), è consigliabile verificare quanto segue:

- [Assicurarsi che l'agente MARS sia aggiornato.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Assicurarsi di disporre della connettività di rete tra l'agente MARS e Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Verificare che MARS sia in esecuzione (nella console del servizio). Se necessario, riavviare e ritentare l'operazione.
- [Assicurarsi che nella cartella scratch sia disponibile dal 5% al 10%](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)di spazio libero sul volume.
- [Verificare se un altro processo o un altro software antivirus interferisce con Backup di Azure.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- Se il backup pianificato non riesce ma il backup manuale funziona, vedere [I backup non vengono eseguiti in base alla pianificazione](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule).
- Assicurati che il tuo sistema operativo disponga degli aggiornamenti più recenti.
- [Assicurarsi che le unità e i file non supportati siano esclusi dal backup.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Assicurarsi che l'orologio del sistema protetto sia configurato per il fuso orario corretto.
- [Verificare che .NET Framework 4.5.2 o versione successiva sia installato nel server.](https://www.microsoft.com/download/details.aspx?id=30653)
- Se si sta tentando di registrare nuovamente il server in un vault:
  - Verificare che l'agente venga disinstallato nel server e che sia stato eliminato dal portale.
  - Utilizzare la stessa passphrase utilizzata inizialmente per registrare il server.
- Per i backup offline, verificare che Azure PowerShell 3.7.0 sia installato sia nel computer di origine che in quello di copia prima di avviare il backup.
- Se l'agente di backup è in esecuzione in una macchina virtuale di Azure, vedere [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

**Messaggio di errore:** Credenziali dell'insieme di credenziali non valide fornite. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)

| Causa | Azioni consigliate |
| ---     | ---    |
| **Le credenziali del Vault non sono valide** <br/> <br/> I file delle credenziali del Vault potrebbero essere danneggiati o essere scaduti. (Ad esempio, potrebbero essere stati scaricati più di 48 ore prima dell'ora di registrazione.)| Scaricare nuove credenziali dall'insieme di credenziali di Servizi di ripristino nel portale di Azure.Download new credentials from Recovery Services vault on the Azure portal. Vedere il passaggio 6 nella sezione [Scaricare l'agente MARS.](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) Quindi eseguire questi passaggi, come appropriato:Then take these steps, as appropriate: <ul><li> Se MARS è già stato installato e registrato, aprire la console MMC di Microsoft Azure Backup Agent e quindi selezionare **Registra server** nel riquadro **Azioni** per completare la registrazione con le nuove credenziali. <br/> <li> Se la nuova installazione non riesce, provare a reinstallare con le nuove credenziali.</ul> **Nota:** se sono stati scaricati più file di credenziali del vault, solo il file più recente è valido per le successive 48 ore. Si consiglia di scaricare un nuovo file delle credenziali del vault.
| **Il server proxy/firewall blocca la registrazione** <br/>o <br/>**Nessuna connettività internet** <br/><br/> Se il computer o il server proxy dispone di connettività Internet limitata e non si garantisce l'accesso per gli URL necessari, la registrazione avrà esito negativo.| Seguire questa procedura:<br/> <ul><li> Collaborare con il team IT per assicurarsi che il sistema disponga di connettività Internet.<li> Se non si dispone di un server proxy, assicurarsi che l'opzione proxy non sia selezionata al momento della registrazione dell'agente. [Controllare le impostazioni del proxy](#verifying-proxy-settings-for-windows).<li> Se si dispone di un server firewall/proxy, collaborare con il team di rete per assicurarsi che questi URL e indirizzi IP abbiano accesso:<br/> <br> **Url**<br> `www.msftncsi.com` <br> . Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Indirizzi IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Provare a registrarsi nuovamente dopo aver completato i passaggi precedenti per la risoluzione dei problemi.<br></br> Se la connessione avviene tramite Azure ExpressRoute, assicurarsi che le impostazioni siano configurate come descritto in Supporto di Azure ExpressRoute.If you're connection is via Azure ExpressRoute, make sure the settings are configured as described in [Azure ExpressRoute support](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Il software antivirus sta bloccando la registrazione** | Se sul server è installato un software antivirus, aggiungere le regole di esclusione necessarie alla scansione antivirus per questi file e cartelle: <br/><ul> <li> CBengine.exe <li> Csc<li> La cartella scratch. Il percorso predefinito è C:. <li> La cartella bin in C: .

### <a name="additional-recommendations"></a>Suggerimenti aggiuntivi

- Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.
- Assicurarsi che la data e l'ora del computer corrispondano al fuso orario locale.
- Verificare che [questi siti](install-mars-agent.md#verify-internet-access) vengano aggiunti ai siti attendibili in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verifica delle impostazioni proxy per Windows

1. Scaricare PsExec dalla pagina [Sysinternals.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Eseguire `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` da un prompt dei comandi con privilegi elevati.

   Questo comando aprirà Internet Explorer.
1. Passare a Opzioni**Internet** >  **di Strumenti** > **Impostazioni** > **lAN per le connessioni**.
1. Controllare le impostazioni proxy per l'account di sistema.
1. Se non è configurato alcun proxy e vengono forniti i dettagli del proxy, rimuovere i dettagli.
1. Se è configurato un proxy e i dettagli del proxy non sono corretti, verificare che i dettagli **dell'indirizzo IP** e della **porta** del proxy siano corretti.
1. Chiudere Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Errore   | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali utilizzando un browser diverso oppure eseguire la procedura seguente: <ul><li> Avviare Internet Explorer. Selezionare F12. </li><li> Vai alla scheda **Rete** e cancella la cache e i cookie. </li> <li> Aggiornare la pagina.<br></li></ul> <li> Verificare se l'abbonamento è disabilitato/scaduto.<br></li> <li> Verificare se una regola del firewall blocca il download. <br></li> <li> Assicurarsi di non aver esaurito il limite sul vault (50 macchine per vault).<br></li>  <li> Verificare che l'utente disponga delle autorizzazioni di Backup di Azure necessarie per scaricare le credenziali dell'insieme di credenziali e registrare un server con l'insieme di credenziali. Vedere Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di [Backup di Azure.See Use Role-Based Access Control to manage Azure Backup recovery points](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Errore  | Causa possibile | Azioni consigliate |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure Recovery Service Agent non è riuscito a connettersi a Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e assicurarsi di essere in grado di connettersi a Internet.<li>(407) Necessaria autenticazione proxy. |Un proxy sta bloccando la connessione. |  <ul><li>In Internet Explorer, vai a **Opzioni Internet Strumenti** > **-** > **Sicurezza** > **Internet**. Selezionare **Livello personalizzato** e scorrere verso il basso fino alla sezione **Download file.** Selezionare **Abilita**.<p>Potrebbe anche essere necessario aggiungere [URL e indirizzi IP ai](install-mars-agent.md#verify-internet-access) siti attendibili in Internet Explorer.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<li> Se il computer dispone di un accesso a Internet limitato, verificare che le impostazioni del firewall del computer o del proxy consentano questi [URL e indirizzi IP.](install-mars-agent.md#verify-internet-access) <li>Se sul server è installato un software antivirus, escludere questi file dalla scansione antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È disponibile un file CSC.exe per ogni versione di .NET Framework installata nel server. Escludere i file CSC.exe per tutte le versioni di .NET Framework nel server interessato. <li>La cartella scratch o il percorso della cache. <br>Il percorso predefinito per la cartella scratch o il percorso della cache è C:.<li>La cartella bin in C: .

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| <br />Impossibile impostare la chiave di crittografia per i backup protetti. L'attivazione non è stata completata, ma la passphrase di crittografia è stata salvata nel file seguente. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server dall'insieme di credenziali e registrarlo nuovamente con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|<br />L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft.     | <li> La cartella scratch si trova su un volume che non dispone di spazio sufficiente. <li> La cartella scratch è stata spostata in modo non corretto. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'agente MARS.<li>Spostare la cartella scratch o la posizione della cache in un volume con spazio libero compreso tra il 5% e il 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, fare riferimento alla procedura descritta in [Domande comuni sul backup di file e cartelle](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:.*        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Errore  | Possibili cause | Azioni consigliate |
|---------|---------|---------|
| <br />Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente.    | <li> La cartella scratch si trova su un volume che non dispone di spazio sufficiente. <li> La cartella scratch è stata spostata in modo non corretto. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella scratch o la posizione della cache in un volume con spazio libero compreso tra il 5% e il 10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, fare riferimento alla procedura descritta in [Domande comuni sul backup di file e cartelle](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito per la cartella scratch o il percorso della cache è C:.*         |

## <a name="backups-dont-run-according-to-schedule"></a>I backup non vengono eseguiti in base alla pianificazione

Se i backup pianificati non vengono attivati automaticamente ma i backup manuali funzionano correttamente, provare le seguenti azioni:

- Verificare che la pianificazione del backup di Windows Server non sia in conflitto con la pianificazione del backup di file e cartelle di Azure.Ensure the Windows Server backup schedule doesn't conflict with the Azure files and folders backup schedule.

- Verificare che lo stato del backup online sia impostato su **Abilita**. Per verificare lo stato, attenersi alla seguente procedura:

  1. In Utilità di pianificazione espandere **Microsoft** e selezionare **Backup online**.
  1. Fare doppio clic su **Microsoft-OnlineBackup** e passare alla scheda **Trigger.**
  1. Verificare se lo stato è impostato su **Abilitato**. In caso contrario, selezionare **Modifica**, **selezionare Abilitato**, quindi **SCEGLIERE OK**.

- Verificare che l'account utente selezionato per l'esecuzione dell'attività sia **SYSTEM** o **gruppo Administrators locale** sul server. Per verificare l'account utente, passare alla scheda **Generale** e selezionare le opzioni **di protezione.**

- Verificare che PowerShell 3.0 o versione successiva sia installato nel server. Per controllare la versione di PowerShell, `Major` eseguire questo comando e verificare che il numero di versione sia 3 o versione successiva:To check the PowerShell version, run this command and verify that the version number is 3 or later:

  `$PSVersionTable.PSVersion`

- Assicurarsi che questo `PSMODULEPATH` percorso faccia parte della variabile di ambiente:Ensure this path is part of the environment variable:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di `LocalMachine` esecuzione `restricted`di PowerShell per è impostato su , il cmdlet PowerShell che attiva l'attività di backup potrebbe non riuscire. Eseguire questi comandi in modalità con privilegi elevati `Unrestricted` `RemoteSigned`per controllare e impostare i criteri di esecuzione su o:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Verificare che non siano presenti file MSOnlineBackup del modulo di PowerShell mancanti o danneggiati. Se sono presenti file mancanti o danneggiati, attenersi alla seguente procedura:

  1. Da qualsiasi computer che dispone di un agente MARS che funziona correttamente, copiare la cartella MSOnlineBackup da C: .
  1. Nel computer problematico incollare i file copiati nello stesso percorso della cartella (C:.

     Se nel computer è già presente una cartella MSOnlineBackup, incollare i file in essa presenti o sostituire i file esistenti.

> [!TIP]
> Per garantire che le modifiche vengano applicate in modo coerente, riavviare il server dopo aver eseguito i passaggi precedenti.

## <a name="resource-not-provisioned-in-service-stamp"></a>Risorsa di cui non è stato eseguito il provisioning nel timbro di servizio

Errore | Possibili cause | Azioni consigliate
--- | --- | ---
L'operazione corrente non è riuscita a causa di un errore interno del servizio "Risorsa non sottoposta a provisioning nel timbro del servizio". Ripetere l'operazione dopo alcuni minuti. (ID: 230006) | Il server protetto è stato rinominato. | <li> Rinominare il server con il nome originale registrato nel vault. <br> <li> Registrare nuovamente il server nell'insieme di credenziali con il nuovo nome.


## <a name="troubleshoot-restore-problems"></a>Risolvere i problemi di ripristino

Backup di Azure potrebbe non montare il volume di ripristino in modo corretto, anche dopo diversi minuti. E si potrebbero ricevere messaggi di errore durante il processo. Per iniziare il ripristino normale, attenersi alla seguente procedura:

1. Annullare il processo di montaggio se è in esecuzione per alcuni minuti.

2. Verificare se si dispone della versione più recente dell'agente di backup. Per verificare la versione, nel riquadro **Azioni** della console MARS selezionare **Informazioni su Microsoft Azure Recovery Services Agent**. Verificare che il numero di **versione** sia uguale a o superiore alla versione citata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). Selezionare questo collegamento per [scaricare la versione più recente.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Passare a**Controller di archiviazione** di Gestione **periferiche** > e individuare **Microsoft iSCSI Initiator**. Se lo si trova, andare direttamente al passaggio 7.

4. Se non è possibile individuare il servizio Initiator iSCSI Microsoft, provare a trovare una voce in**Controller di archiviazione** di Gestione >  **periferiche**denominati **Periferica sconosciuta** con ID hardware **ROOT , ISCSIPRT**.

5. Fare clic con il pulsante destro del mouse su **Dispositivo sconosciuto** e selezionare **Aggiorna software driver**.

6. Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Questo aggiornamento dovrebbe modificare **Periferica sconosciuta** in **Microsoft iSCSI Initiator**:

    ![Schermata di Gestione dispositivi di Backup di Azure, con l'opzione Controller di archiviazione evidenziata](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Passare a **Servizi gestione attività** > **(locale)** > **Servizio di invitatore iSCSI Microsoft iSCSI**:

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio e scegliere **Arresta**. Quindi fare di nuovo clic con il pulsante destro del mouse e **scegliere Avvia**.

9. Ripetere il ripristino usando l'opzione [Ripristino istantaneo](backup-instant-restore-capability.md).

Se il ripristino persiste, riavviare il server o il client. Se non si desidera riavviare o se il ripristino continua a non riuscire anche dopo il [riavvio](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)del server, provare a eseguire il ripristino da un altro computer .

## <a name="troubleshoot-cache-problems"></a>Risolvere i problemi relativi alla cache

L'operazione di backup potrebbe non riuscire se la cartella della cache (denominata anche cartella scratch) non è configurata correttamente, manca i prerequisiti o ha accesso limitato.

### <a name="prerequisites"></a>Prerequisiti

Affinché le operazioni dell'agente MARS abbiano esito positivo, la cartella della cache deve rispettare i requisiti seguenti:For MARS agent operations to succeed the cache folder needs to adhere to the below requirements:

- [Assicurarsi che nella posizione della cartella scratch sia disponibile dal 5% al 10% di spazio libero sul volume](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Assicurarsi che il percorso della cartella scratch sia valido e accessibile](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Verificare che gli attributi dei file nella cartella della cache siano supportatiEnsure file attributes on the cache folder are supported](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Verificare che lo spazio di archiviazione delle copie shadow allocato sia sufficiente per il processo di backup](#increase-shadow-copy-storage)
- [Assicurarsi che non vi siano altri processi (ad es. software antivirus) che limitano l'accesso alla cartella della cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumentare l'archiviazione delle copie shadow

Le operazioni di backup potrebbero non riuscire se lo spazio di archiviazione delle copie shadow è insufficiente per proteggere l'origine dati. Per risolvere questo problema, aumentare lo spazio di archiviazione della copia shadow nel volume protetto utilizzando vssadmin come illustrato di seguito:

- Controllare lo spazio di archiviazione shadow corrente dal prompt dei comandi con privilegi elevati:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumentare lo spazio di archiviazione shadow utilizzando il comando seguente:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Un altro processo o software antivirus che blocca l'accesso alla cartella della cache

Se sul server è installato un software antivirus, aggiungere le regole di esclusione necessarie alla scansione antivirus per questi file e cartelle:  

- La cartella scratch. Il percorso predefinito è C:.
- La cartella bin in C: . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .
- CBengine.exe
- Csc

## <a name="common-issues"></a>Problemi comuni

In questa sezione vengono illustrati gli errori comuni che si verificano durante l'utilizzo dell'agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Messaggio di errore | Azione consigliata |
-- | --
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al checksum di backup archiviato nell'area di lavoro | Per risolvere questo problema, eseguire le operazioni riportate di seguito e riavviare il server <br/> - [Verificare se è presente un antivirus o altri processi che bloccano i file di percorso scratch](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controllare se la posizione scratch è valida e accessibile all'agente di Marte.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Messaggio di errore | Azione consigliata |
-- | --
Agente di Servizi di ripristino di Microsoft Azure non è riuscito ad accedere al percorso dei file temporanei per inizializzare VHD | Per risolvere questo problema, eseguire le operazioni riportate di seguito e riavviare il server <br/> - [Verificare se è presente un antivirus o altri processi che bloccano i file di percorso scratch](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controllare se la posizione scratch è valida e accessibile all'agente di Marte.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Messaggio di errore | Azione consigliata |
-- | --
Backup non riuscito a causa di spazio di archiviazione insufficiente nel volume in cui si trova la cartella scratch | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/>- [Assicurarsi che l'agente MARS sia l'ultima](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verificare e risolvere i problemi di archiviazione che influiscono sull'area di memoria virtuale del backup](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Messaggio di errore | Azione consigliata |
-- | --
Impossibile trovare le modifiche in un file. Il problema potrebbe essere legato a vari motivi. Ripetere l'operazione | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione:<br/> - [Assicurarsi che l'agente MARS sia l'ultima](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verificare e risolvere i problemi di archiviazione che influiscono sull'area di memoria virtuale del backup](#prerequisites)

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su come eseguire il backup di [Windows Server con l'agente di Backup di Azure.](tutorial-backup-windows-server-to-azure.md)
- Se è necessario ripristinare un backup, vedere Ripristinare i file in [un computer Windows](backup-azure-restore-windows-server.md).
