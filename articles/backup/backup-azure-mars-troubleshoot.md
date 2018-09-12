---
title: Risolvere i problemi dell'agente di Backup di Azure
description: Risolvere i problemi di installazione e registrazione dell'agente di Backup di Azure
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 2c8978cfba8fc56d4dbc565cb3a91c75d9d54679
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700196"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>Risoluzione dei problemi dell'agente di Servizi di ripristino di Microsoft Azure (MARS)
## <a name="recommended-steps"></a>Procedure consigliate
Fare riferimento a questo articolo per risolvere gli errori durante la configurazione, la registrazione, il backup e il ripristino usando l'agente MARS.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino.
| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br> *Sono state specificate credenziali dell'insieme di credenziali non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)* | <ul><li> Le credenziali dell'insieme delle credenziali non sono valide, ovvero sono state scaricate più di 48 ore prima dell'ora di registrazione.<li>L'agente MARS è in grado di scaricare i file nella directory Temp di Windows. <li>Le credenziali dell'insieme di credenziali si trovano in un percorso di rete. <li>TLS 1.0 è disabilitato<li> La connessione è bloccata da un server proxy configurato. <br> |  <ul><li>Scaricare le nuove credenziali dell'insieme di credenziali.<li>Passare a **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare quindi **Abilita**.<li>Potrebbe anche essere necessario aggiungere il sito ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy. <li> Mettere in corrispondenza la data e l'ora con il computer.<li>Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.<li>È possibile verificarlo eseguendo il pacchetto SDP sul server. Se viene visualizzato un errore indicante che non è consentito scaricare file, è probabile che sia presente un numero elevato di file nella directory C:/Windows/Temp.<li>Assicurarsi che .NET Framework 4.6.2 sia installato. <li>Se TLS 1.0 è stato disabilitato per conformità PCI, fare riferimento a questa [pagina per la risoluzione dei problemi](https://support.microsoft.com/help/4022913). <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe<li>CSC.exe, correlato a .NET Framework. È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br><ol><li>*L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi al servizio Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e verificare che sia possibile connettersi a Internet*<li>*(407) Autenticazione proxy obbligatoria* |Il proxy blocca la connessione. |  <ul><li>Passare a **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare **Abilita**.<li>Potrebbe anche essere necessario aggiungere il sito ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy. <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |      
| **Error (Errore) (Error (Errore)e)** </br>*Impossibile configurare la chiave di crittografia per i backup protetti. L'attivazione non è riuscita ma la passphrase di crittografia è stata salvata nel file*  seguente. |<li>Il server è già registrato con un altro insieme di credenziali.<li>Durante la configurazione la passphrase è stata danneggiata.| Annullare la registrazione del server nell'insieme di credenziali e registrarlo con una nuova passphrase.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]

| Dettagli errore | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|**Error (Errore) (Error (Errore)e)** </br><ol>*L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft*     | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante.         | <li>Eseguire l'aggiornamento alla [versione più recente](http://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero uguale al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente

| Dettagli errore | Possibili cause | Azioni consigliate |
|---------|---------|---------|
|**Error (Errore) (Error (Errore)e)** </br><ol>*Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente*.    | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante.        | <li>Eseguire l'aggiornamento alla [versione più recente](http://aka.ms/azurebackup_agent) dell'Agente di Servizi di ripristino di Microsoft Azure.<li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero equivalente al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>I backup non vengono eseguiti in base alla pianificazione
Eseguire i passaggi seguenti quando i backup pianificati non vengono generati automaticamente, mentre i backup manuali funzionano senza problemi. 
 
<li>Verificare che PowerShell 3.0 o versione successiva sia installato nel server. Per controllare la versione di PowerShell, eseguire il comando seguente e verificare che il numero di versione *principale* sia uguale o maggiore di 3.

`$PSVersionTable.PSVersion`
<li>Assicurarsi che il percorso seguente faccia parte della variabile di ambiente *PSMODULEPATH*.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>Se i criteri di esecuzione powershell per il *ComputerLocale* sono impostati con restrizioni, il cmdlet di powershell che attiva l'attività di backup potrebbe non avere esito positivo. Eseguire i comandi seguenti in modalità con privilegi elevati per controllare e impostare i criteri di esecuzione per *Unrestricted* o *RemoteSigned*

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>Passare al pannello di controllo -> Strumenti di amministrazione -> Utilità di pianificazione -> espandere Microsoft -> selezionare i Backup online
<li>Fare doppio clic sull'attività 'Microsoft-OnlineBackup' e passare alla scheda 'Trigger'.
<li>Verificare che lo stato dell'attività sia impostato su 'Attivato'. In caso contrario, fare clic su 'Modifica' e selezionare la casella di controllo 'Attivato'
<li>Passare alla sezione *Opzioni di sicurezza* della scheda *Generali*
<li>Assicurarsi che l'account utente selezionato per l'esecuzione dell'attività sia *SISTEMA* o il gruppo Amministratori locali nel server

> [!TIP]
> È consigliabile riavviare il server dopo aver eseguito i passaggi precedenti per assicurarsi che le modifiche apportate vengano applicate in modo coerente


## <a name="troubleshooting-restore-issues"></a>Risoluzione dei problemi di ripristino

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>Tentativo di montare il Volume di ripristino durante il ripristino di file e cartelle
Se il montaggio del volume di ripristino non è stato completato da Backup di Azure diversi minuti dopo il clic su **Monta** o ha esito negativo con uno o più errori, seguire questa procedura per avviare normalmente il ripristino.

1.  Annullare il processo di montaggio in corso se è in esecuzione da diversi minuti.

2.  Verificare di usare l'ultima versione dell'agente di Backup di Azure. Per trovare le informazioni relative alla versione dell'agente di Backup di Azure, fare clic su **Informazioni su Agente di Servizi di ripristino di Microsoft Azure** nel riquadro **Azioni** della console di Backup di Microsoft Azure e verificare che il numero in **Versione** sia uguale o superiore alla versione indicata in [questo articolo](https://go.microsoft.com/fwlink/?linkid=229525). È possibile scaricare l'ultima versione da [qui](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Passare a **Gestione dispositivi** -> **Controller di archiviazione** e verificare di poter individuare **Iniziatore iSCSI Microsoft**. Se si riesce a individuarlo, andare direttamente al passaggio 7 seguente. 

4.  Se non si riesce a individuare il servizio iniziatore iSCSI Microsoft come illustrato nel passaggio 3, verificare se in **Gestione dispositivi** -> **Controller di archiviazione** è presente una voce **Dispositivo sconosciuto** con ID hardware **ROOT\ISCSIPRT**.

5.  Fare clic con il pulsante destro del mouse su **Dispositivo sconosciuto** e scegliere **Aggiornamento software driver**.

6.  Aggiornare il driver selezionando l'opzione **Cerca automaticamente un driver aggiornato**. Al termine dell'aggiornamento, **Dispositivo sconosciuto** verrà modificato in **Iniziatore iSCSI Microsoft**, come illustrato di seguito. 

    ![Crittografia](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Passare a **Gestione attività** -> **Servizi (computer locale)** -> **Servizio iniziatore iSCSI Microsoft**. 

    ![Crittografia](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Riavviare il servizio iniziatore iSCSI Microsoft. A tale scopo, fare clic con il pulsante destro del mouse sul servizio e scegliere Arresta, quindi fare di nuovo clic con il pulsante destro del mouse e scegliere **Avvia**.

9.  Riprovare il ripristino istantaneo. 

Se il ripristino ha ancora esito negativo, riavviare il server o il client. Se un riavvio non è auspicabile o il ripristino non riesce anche dopo il riavvio del server, provare a eseguire il ripristino da un altro computer seguendo i passaggi elencati in [questo articolo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [backup del server di Windows con l'agente di Backup di Azure](tutorial-backup-windows-server-to-azure.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
