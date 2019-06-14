---
title: Risolvere i problemi dell'agente di Backup di Azure
description: Risolvere i problemi di installazione e registrazione dell'agente di Backup di Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060210"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Risoluzione dei problemi dell'agente di Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive come risolvere gli errori che potrebbero essere visualizzati durante configurazione, registrazione, backup e ripristino.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Si consiglia di eseguire la convalida, di seguito prima di iniziare la risoluzione dei problemi dell'agente di Microsoft Azure Recovery Services (MARS):

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

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

**Messaggio di errore**: Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)

| Causa | Azione consigliata |
| ---     | ---    |
| **L'insieme di credenziali non è validi** <br/> <br/> I file delle credenziali dell'insieme di credenziali potrebbero essere danneggiati oppure potrebbero essere scaduto (ad esempio scaricata più di 48 ore prima dell'ora di registrazione)| Scaricare nuove credenziali dall'insieme di credenziali di servizi di ripristino dal portale di Azure (vedere *passaggio 6* sotto [ **scaricare l'agente MARS** ](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) sezione) ed eseguire il seguente: <ul><li> Se già installato e registrato di Microsoft Azure Backup Agent, quindi aprire la console MMC di Microsoft Azure Backup Agent e scegliere **registra Server** dal riquadro azioni per completare la registrazione con appena scaricato credenziali <br/> <li> Se non è riuscita nella nuova installazione quindi provare a reinstallare usando le nuove credenziali</ul> **Nota**: Se più file delle credenziali dell'insieme di credenziali vengono scaricati in precedenza, la versione più recente scaricato è valido solo file entro 48 ore. Di conseguenza è consigliabile scaricare aggiornata nuovo file di credenziali di insieme di credenziali.
| **Server proxy/firewall sta bloccando <br/>o <br/>connettività Internet No** <br/><br/> Se la macchina o il Server Proxy con accesso Internet limitato, senza che vengano elencati gli URL necessari la registrazione avrà esito negativo.| Per risolvere questo problema, effettuare le seguenti:<br/> <ul><li> Rivolgersi al team IT per assicurarsi che il sistema disponga della connettività Internet<li> Se non è il server Proxy, quindi verificare che l'opzione proxy sia deselezionato quando si registra l'agente, verificare i passaggi di impostazione proxy elencati [qui](#verifying-proxy-settings-for-windows)<li> Se si dispone di un server proxy/firewall quindi funzionano con il team di rete per garantire che sotto URL e l'IP indirizzo avere accesso<br/> <br> **URL**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**Indirizzo IP**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>Provare a registrare nuovamente dopo aver completato i passaggi di risoluzione dei problemi precedenti
| **Il software antivirus blocca** | Se si dispone di un antivirus installato nel server, quindi aggiungere le regole di esclusione necessari per i file seguenti dall'analisi dell'antivirus: <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> Cartella dei file temporanei, il percorso predefinito è *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* <li> Cartella bin al *C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>Consigli aggiuntivi
- Passare a *c: / Windows/Temp* e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file
- Verificare che data e ora del computer corrisponda al fuso orario locale
- Verificare che il [seguente](backup-configure-vault.md#verify-internet-access) siti vengono aggiunti ai siti attendibili di Internet Explorer

### <a name="verifying-proxy-settings-for-windows"></a>Verifica per determinare se le impostazioni proxy per Windows

- Scaricare **psexec** da [qui](https://docs.microsoft.com/sysinternals/downloads/psexec)
- Eseguire questo `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` comando dal prompt dei comandi con privilegi elevati:
- Verrà avviata *Internet Explorer* finestra
- Passare a *degli strumenti* -> *Opzioni Internet* -> *connessioni* -> *impostazioni LAN*
- Verificare le impostazioni proxy per *sistema* account
- Se è stato configurato alcun proxy e vengono forniti i dettagli proxy, quindi rimuovere i dettagli
-   Se il proxy è configurato e dettagli del proxy non sono corrette, quindi assicurarsi *IP Proxy* e *porta* dettagli sono accurati
- Chiudi *Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Dettagli errore | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali usando un altro browser o eseguendo questa procedura: <ul><li> Avviare Internet Explorer e premere F12. </li><li> Passare alla scheda **Rete** per cancellare la cache e i cookie di Internet Explorer </li> <li> Aggiornare la pagina<br>OPPURE</li></ul> <li> Controllare se la sottoscrizione è disabilitata o scaduta<br>OPPURE</li> <li> Controllare se una regola del firewall sta bloccando il download del file di credenziali dell'insieme di credenziali <br>OPPURE</li> <li> Verificare di non aver esaurito il limite nell'insieme di credenziali (50 computer per insieme di credenziali)<br>OPPURE</li>  <li> Verificare che l'utente disponga dell'autorizzazione di Backup di Azure per scaricare le credenziali dell'insieme di credenziali e registrare server con l'insieme di credenziali, vedere necessaria [articolo](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** <br /><ol><li>*L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi al servizio Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e verificare che sia possibile connettersi a Internet*<li>*(407) Autenticazione proxy obbligatoria* |Il proxy blocca la connessione. |  <ul><li>Avviare **IE** > **Impostazione** > **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare **Abilita**.<li>Potrebbe anche essere necessario aggiungere questi siti ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins) di IE.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<li> Se il computer ha limitato l'accesso a internet, assicurarsi che le impostazioni del firewall nel computer o nel proxy consentano questi [URL](backup-configure-vault.md#verify-internet-access) e [indirizzi IP](backup-configure-vault.md#verify-internet-access). <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.<li>Cartella Bin C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** <br />*Impossibile impostare la chiave di crittografia per i backup protetti. L'attivazione non è stata completata, ma la passphrase di crittografia è stata salvata nel file seguente*. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server nell'insieme di credenziali e registrarlo con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Dettagli errore | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|**Error (Errore) (Error (Errore)e)** <br />*L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione in un secondo momento. Se il problema persiste, contattare il supporto tecnico Microsoft*     | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero uguale al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Dettagli errore | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|**Error (Errore) (Error (Errore)e)** <br />*Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente*.    | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero equivalente al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>I backup non vengono eseguiti in base alla pianificazione
Se i backup pianificati non vengono generati automaticamente, mentre i backup manuali funzionano senza problemi, eseguire queste operazioni:

- Assicurarsi di pianificazione di Windows Server backup non in conflitto con Azure pianificazione di backup di file e cartelle.

- Verificare che lo stato di Backup in linea è impostato su **abilitare**. Per verificare lo stato di eseguire il seguente:

  - Aprire **utilità di pianificazione** ed espandere **Microsoft**e selezionare **Backup Online**.
  - Fare doppio clic su **Microsoft-Backup online** e andare alla scheda **Trigger**.
  - Verificare se lo stato è impostato su **abilitato**. Se non lo è, quindi selezionare **Edit** > **Enabled** casella di controllo e fare clic su **OK**.

- Assicurarsi che l'account utente selezionato per l'esecuzione dell'attività è uno **SYSTEM** oppure **gruppo locale Administrators** nel server. Per verificare l'account utente, passare al **generali** scheda e selezionare il **sicurezza** opzioni.

- Verificare che PowerShell 3.0 o versione successiva sia installato nel server. Per controllare la versione di PowerShell, eseguire il comando seguente e verificare che il numero di versione *principale* sia uguale o maggiore di 3.

  `$PSVersionTable.PSVersion`

- Verificare che il percorso seguente fa parte di *PSMODULEPATH* variabile di ambiente

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di esecuzione PowerShell per *LocalMachine* sono impostati con restrizioni, il cmdlet di PowerShell che attiva il backup potrebbe non avere esito positivo. Eseguire i comandi seguenti in modalità con privilegi elevate, per controllare e impostare i criteri di esecuzione per uno *Unrestricted* o *RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Verificare che vi siano mancanti o danneggiati **PowerShell** modulo **MSonlineBackup**. In caso di qualsiasi file mancanti o danneggiati, per risolvere questo problema, procedere con il seguente:

  - Da qualsiasi computer con l'agente MARS che funziona correttamente e copiare la cartella MSOnlineBackup dal *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* percorso.
  - Incollare questa problematica macchina nello stesso percorso *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* .
  - Se **MSOnlineBackup** cartella esiste già nella macchina, incollare o sostituire i file di contenuto all'interno.


> [!TIP]
> Per garantire che le modifiche vengano applicate in modo coerente, riavviare il server dopo aver eseguito i passaggi precedenti.


## <a name="troubleshoot-restore-issues"></a>Risolvere i problemi di ripristino

Backup di Azure potrebbe non montare il volume di ripristino in modo corretto, anche dopo diversi minuti. È anche possibile ricevere i messaggi di errore durante il processo. Per avviare normalmente il ripristino, seguire questa procedura:

1.  Annullare il processo di montaggio in corso se è in esecuzione da diversi minuti.

2.  Verificare se si usa la versione più recente dell'agente di backup. Per trovare la versione, nel riquadro **Azioni** della console MARS selezionare **Informazioni sull'agente di Servizi di ripristino di Microsoft Azure**. Verificare che il numero di **versione** sia uguale a o superiore alla versione citata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). È possibile scaricare l'ultima versione [qui](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Passare a **Gestione dispositivi** > **Controller di archiviazione** e individuare **Iniziatore iSCSI Microsoft**. Se non è possibile individuarlo, andare direttamente al passaggio 7.

4.  Se non è possibile individuare il servizio Iniziatore iSCSI Microsoft, provare a trovare una voce in **Gestione dispositivi** > **Controller di archiviazione** denominata **Dispositivo sconosciuto**, con ID hardware **ROOT\ISCSIPRT**.

5.  Fare clic con il pulsante destro del mouse su **Dispositivo sconosciuto** e scegliere **Aggiornamento software driver**.

6.  Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Al termine dell'aggiornamento, **Dispositivo sconosciuto** viene modificato in **Iniziatore iSCSI Microsoft**, come illustrato di seguito.

    ![Schermata di Gestione dispositivi di Backup di Azure, con l'opzione Controller di archiviazione evidenziata](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Passare a **Gestione attività** > **Servizi (computer locale)**  > **Servizio iniziatore iSCSI Microsoft**.

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio, selezionare **Arresta**, fare clic con il pulsante destro di nuovo e quindi selezionare **Avvia**.

9.  Ripetere il ripristino usando l'opzione [**Ripristino istantaneo**](backup-instant-restore-capability.md).

Se il ripristino ha ancora esito negativo, riavviare il server o il client. Se non si intende eseguire il riavvio o se il ripristino non riesce anche dopo il riavvio del server, ripetere il ripristino da un altro computer. Seguire i passaggi descritti in [questo articolo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [backup del server di Windows con l'agente di Backup di Azure](tutorial-backup-windows-server-to-azure.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
