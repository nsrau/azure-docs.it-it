---
title: Risolvere i problemi dell'agente di Backup di Azure
description: Risolvere i problemi di installazione e registrazione dell'agente di Backup di Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: saurse
ms.openlocfilehash: ce6293e63e672df9683ab607a304f8c7275911c5
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446614"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Risoluzione dei problemi dell'agente di Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive come risolvere gli errori che potrebbero essere visualizzati durante configurazione, registrazione, backup e ripristino.

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide
| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br> *Sono state specificate credenziali dell'insieme di credenziali non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)* | <ul><li> Le credenziali dell'insieme delle credenziali non sono valide, ovvero sono state scaricate più di 48 ore prima dell'ora di registrazione.<li>L'agente MARS è in grado di scaricare i file nella directory Temp di Windows. <li>Le credenziali dell'insieme di credenziali si trovano in un percorso di rete. <li>TLS 1.0 è disabilitato<li> La connessione è bloccata da un server proxy configurato. <br> |  <ul><li>Scaricare le nuove credenziali dell'insieme di credenziali (**Nota**: se in precedenza sono stati scaricati più file di credenziali dell'insieme di credenziali, solo il file scaricato più recente è valido entro 48 ore.) <li>Avviare **IE** > **Impostazione** > **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare quindi **Abilita**.<li>Potrebbe anche essere necessario aggiungere questi siti ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements) di IE.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy. <li> Mettere in corrispondenza la data e l'ora con il computer.<li>Se viene visualizzato un errore indicante che non è consentito scaricare file, è probabile che sia presente un numero elevato di file nella directory C:/Windows/Temp.<li>Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.<li>Assicurarsi che .NET Framework 4.6.2 sia installato. <li>Se TLS 1.0 è stato disabilitato per conformità PCI, fare riferimento a questa [pagina per la risoluzione dei problemi](https://support.microsoft.com/help/4022913). <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe<li>CSC.exe, correlato a .NET Framework. È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>Cartella Bin C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\Bin

## <a name="unable-to-download-vault-credential-file"></a>Non è possibile scaricare il file di credenziali dell'insieme di credenziali

| Dettagli errore | Azioni consigliate |
| ---     | ---    |
|Non è stato possibile scaricare il file dell'insieme di credenziali. (ID: 403) | <ul><li> Provare a scaricare le credenziali dell'insieme di credenziali usando un altro browser o eseguendo questa procedura: <ul><li> Avviare Internet Explorer e premere F12. </li><li> Passare alla scheda **Rete** per cancellare la cache e i cookie di Internet Explorer </li> <li> Aggiornare la pagina<br>OPPURE</li></ul> <li> Controllare se la sottoscrizione è disabilitata o scaduta<br>OPPURE</li> <li> Controllare se una regola del firewall sta bloccando il download del file di credenziali dell'insieme di credenziali <br>OPPURE</li> <li> Verificare di non aver esaurito il limite nell'insieme di credenziali (50 computer per insieme di credenziali)<br>OPPURE</li>  <li> Verificare che l'utente abbia l'autorizzazione di Backup di Azure necessaria per scaricare le credenziali dell'insieme di credenziali e registrare il server con l'insieme di credenziali (vedere questo  [articolo](backup-rbac-rs-vault.md))</li></ul> | 

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br><ol><li>*L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi al servizio Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e verificare che sia possibile connettersi a Internet*<li>*(407) Autenticazione proxy obbligatoria* |Il proxy blocca la connessione. |  <ul><li>Avviare **IE** > **Impostazione** > **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare **Abilita**.<li>Potrebbe anche essere necessario aggiungere questi siti ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements) di IE.<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy. <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.<li>Cartella Bin C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |      
| **Error (Errore) (Error (Errore)e)** </br>*Impossibile impostare la chiave di crittografia per i backup protetti. L'attivazione non è stata completata, ma la passphrase di crittografia è stata salvata nel file seguente*. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server nell'insieme di credenziali e registrarlo con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>L'attivazione non è stata completata

| Dettagli errore | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|**Error (Errore) (Error (Errore)e)** </br><ol>*L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft*     | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero uguale al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La passphrase di crittografia non è configurata correttamente

| Dettagli errore | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|**Error (Errore) (Error (Errore)e)** </br><ol>*Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente*.    | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](https://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero equivalente al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>I backup non vengono eseguiti in base alla pianificazione
Se i backup pianificati non vengono generati automaticamente, mentre i backup manuali funzionano senza problemi, eseguire queste operazioni:

- Verificare che la pianificazione di backup di Windows Server non sia in conflitto con quella di file e cartelle di Azure.
- Andare a **Pannello di controllo** > **Strumenti di amministrazione** > **Utilità di pianificazione**. Espandere **Microsoft** e selezionare **Backup online**. Fare doppio clic su **Microsoft-Backup online** e andare alla scheda **Trigger**. Verificare che lo stato sia impostato su **Abilitato**. In caso contrario, selezionare **Modifica**, quindi selezionare la casella di controllo **Abilitata** e fare clic su **OK**. Nella scheda **Generale** passare a **Opzioni di sicurezza** e assicurarsi che l'account utente selezionato per l'esecuzione dell'attività sia **SISTEMA** o il **gruppo Amministratori locali** nel server.

- Verificare se PowerShell 3.0 o versione successiva è installato nel server. Per controllare la versione di PowerShell, eseguire il comando seguente e verificare che il numero di versione *principale* sia uguale o maggiore di 3.

  `$PSVersionTable.PSVersion`

- Verificare che il percorso seguente appartenga alla variabile di ambiente *PSMODULEPATH*.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se i criteri di esecuzione PowerShell per *LocalMachine* sono impostati con restrizioni, il cmdlet di PowerShell che attiva il backup potrebbe non avere esito positivo. Eseguire questi comandi in modalità con privilegi elevati per controllare e impostare i criteri di esecuzione per *Unrestricted* o *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

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

7.  Passare a **Gestione attività** > **Servizi (computer locale)** > **Servizio iniziatore iSCSI Microsoft**.

    ![Schermata di Gestione attività di Backup di Azure con l'opzione Servizi (computer locale) evidenziata](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Riavviare il servizio Iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio, selezionare **Arresta**, fare clic con il pulsante destro di nuovo e quindi selezionare **Avvia**.

9.  Ripetere il ripristino usando l'opzione [**Ripristino istantaneo**](backup-instant-restore-capability.md).

Se il ripristino ha ancora esito negativo, riavviare il server o il client. Se non si intende eseguire il riavvio o se il ripristino non riesce anche dopo il riavvio del server, ripetere il ripristino da un altro computer. Seguire i passaggi descritti in [questo articolo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [backup del server di Windows con l'agente di Backup di Azure](tutorial-backup-windows-server-to-azure.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
