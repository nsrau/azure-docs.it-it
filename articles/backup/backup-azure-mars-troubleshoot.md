---
title: Risoluzione dei problemi di Azure Backup Agent | Documenti Microsoft
description: Risoluzione dei problemi di installazione e la registrazione di Azure Backup Agent
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: 52a32d61dd69110ace560fd1e52389140f322678
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Risoluzione dei problemi di registrazione e la configurazione dell'agente di Backup di Azure
## <a name="recommended-steps"></a>Procedure consigliate
Vedere le azioni consigliate nelle tabelle seguenti per risolvere gli errori che potrebbero verificarsi durante la configurazione o la registrazione di Azure Backup Agent.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino.
| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br> *Sono state specificate credenziali dell'insieme di credenziali non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)* | <ul><li> L'insieme di credenziali non è validi (vale a dire, sono state scaricate più di 48 ore prima che l'ora di registrazione).<li>L'agente Azure Backup non è in grado di scaricare un file temporaneo nella cartella temporanea di Windows. <li>L'insieme di credenziali è in un percorso di rete. <li>TLS 1.0 è disabilitato<li> La connessione è bloccata da un server proxy configurato. <br> |  <ul><li>Scaricare i nuovo insieme di credenziali.<li>Passare a **Opzioni Internet** > **sicurezza** > **Internet**. Selezionare quindi **livello personalizzato**e scorrere fino a visualizzare il file di download di sezione. Selezionare quindi **abilitare**.<li>Potrebbe anche essere necessario aggiungere il sito alla [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modificare le impostazioni per l'utilizzo di un server proxy. Fornire quindi il proxy i dettagli del server. <li> Corrispondenza data e ora con il computer.<li>Andare in c: / Windows/Temp e verificare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, è possibile eliminare questi file.<li>È possibile eseguire il test eseguendo il pacchetto SDP sul server. Se si verifica un errore che informa che il download di file non è consentito, è molto probabile che non esistono un numero elevato di file nella directory c: / Windows/Temp.<li>Assicurarsi di aver installato .NET framework 4.6.2. <li>Se è disabilitata a causa di conformità PCI TLS 1.0, fare riferimento a questo [risoluzione dei problemi di pagina](https://support.microsoft.com/help/4022913). <li>Se si dispone di software antivirus installato sul server, escludere i file seguenti dall'analisi antivirus: <ul><li>CBengine.exe<li>CSC.exe, che è correlata a .NET Framework. Non vi è un CSC.exe per ogni versione di .NET installata nel server. Escludere i file di CSC.exe associati a tutte le versioni di .NET framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito per la cartella di lavoro o il percorso della cache è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agente servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Microsoft Azure Backup

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br><ol><li>*L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi al servizio Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e verificare che sia possibile connettersi a Internet*<li>*(407) Autenticazione proxy obbligatoria* |Proxy blocchi la connessione. |  <ul><li>Passare **Opzioni Internet** > **sicurezza** > **Internet**. Selezionare quindi **livello personalizzato** e scorrere fino a visualizzare il file di download di sezione. Selezionare **Abilita**.<li>Potrebbe anche essere necessario aggiungere il sito alla [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modificare le impostazioni per l'utilizzo di un server proxy. Fornire quindi il proxy i dettagli del server. <li>Se si dispone di software antivirus installato sul server, è possibile escludere i file seguenti dall'analisi antivirus. <ul><li>CBEngine.exe (anziché dpmra.exe).<li>CSC.exe (relativi a .NET Framework). Non vi è un CSC.exe per ogni versione di .NET installata nel server. Escludere i file di CSC.exe associati a tutte le versioni di .NET framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito per la cartella di lavoro o il percorso della cache è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |      
| **Error (Errore) (Error (Errore)e)** </br>*Impossibile impostare la chiave di crittografia per i backup protetti. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno 'Errore di input non valido'. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft*. |Server è già registrato con un altro insieme di credenziali.| Annullare la registrazione di server dall'insieme di credenziali e ripetere la registrazione.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |          
| **Error (Errore) (Error (Errore)e)** </br><ol><li>*L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft* <li>*Errore 34506. La passphrase di crittografia archiviata nel computer non è configurata correttamente*. | <li> La cartella di lavoro si trova in un volume che dispone di spazio sufficiente. <li> La cartella di lavoro è stata spostata correttamente in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante. | <li>Spostare la cartella dei file temporanei o percorso della cache in un volume con spazio libero equivale a 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [domande su Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che il file OnlineBackup.KEK sia presente. <br>*Il percorso predefinito per la cartella di lavoro o il percorso della cache è C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni su [come eseguire il backup di Windows Server con l'agente Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
