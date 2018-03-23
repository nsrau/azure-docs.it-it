---
title: Risolvere i problemi dell'agente di Backup di Azure | Documentazione Microsoft
description: Risolvere i problemi di installazione e registrazione dell'agente di Backup di Azure
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shreeshd
editor: ''
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: f7f4ac328c4e35f52bcc9708faf96d06189dd9ac
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Risolvere i problemi di configurazione e registrazione dell'agente di Backup di Azure
## <a name="recommended-steps"></a>Procedure consigliate
Vedere le azioni consigliate nelle tabelle seguenti per risolvere gli errori che si possono verificare durante la configurazione o la registrazione dell'agente di Backup di Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino.
| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br> *Sono state specificate credenziali dell'insieme di credenziali non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. (ID: 34513)* | <ul><li> Le credenziali dell'insieme delle credenziali non sono valide, ovvero sono state scaricate più di 48 ore prima dell'ora di registrazione.<li>L'agente di Backup di Azure non è in grado di scaricare un file temporaneo nella cartella Temp di Windows. <li>Le credenziali dell'insieme di credenziali si trovano in un percorso di rete. <li>TLS 1.0 è disabilitato<li> La connessione è bloccata da un server proxy configurato. <br> |  <ul><li>Scaricare le nuove credenziali dell'insieme di credenziali.<li>Passare a **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare quindi **Abilita**.<li>Potrebbe anche essere necessario aggiungere il sito ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy. <li> Mettere in corrispondenza la data e l'ora con il computer.<li>Andare a C:/Windows/Temp e controllare se sono presenti più di 60.000 o 65.000 file con estensione tmp. Se sono presenti, eliminare questi file.<li>È possibile eseguire il test eseguendo il pacchetto SDP sul server. Se viene visualizzato un errore indicante che non è consentito scaricare file, è probabile che sia presente un numero elevato di file nella directory C:/Windows/Temp.<li>Assicurarsi che .NET Framework 4.6.2 sia installato. <li>Se TLS 1.0 è stato disabilitato per conformità PCI, fare riferimento a questa [pagina per la risoluzione dei problemi](https://support.microsoft.com/help/4022913). <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe<li>CSC.exe, correlato a .NET Framework. È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi a Backup di Microsoft Azure

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |
| **Error (Errore) (Error (Errore)e)** </br><ol><li>*L'agente di Servizi di ripristino di Microsoft Azure non è riuscito a connettersi al servizio Backup di Microsoft Azure. (ID: 100050) Controllare le impostazioni di rete e verificare che sia possibile connettersi a Internet*<li>*(407) Autenticazione proxy obbligatoria* |Il proxy blocca la connessione. |  <ul><li>Passare a **Opzioni Internet** > **Sicurezza** > **Internet**. Selezionare quindi **Livello personalizzato** e scorrere per visualizzare la sezione di download del file. Selezionare **Abilita**.<li>Potrebbe anche essere necessario aggiungere il sito ai [siti attendibili](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy. <li>Se si dispone di un antivirus installato nel server, escludere i file seguenti dall'analisi dell'antivirus: <ul><li>CBengine.exe (anziché dpmra.exe).<li>CSC.exe (correlato a .NET Framework). È presente un file CSC.exe per ogni versione di .NET installata nel server. Escludere i file CSC.exe associati a tutte le versioni di .NET Framework nel server interessato. <li>Percorso della cartella Scratch o della cache. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Impossibile impostare la chiave di crittografia per i backup protetti

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |      
| **Error (Errore) (Error (Errore)e)** </br>*Impossibile impostare la chiave di crittografia per i backup protetti. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno 'Errore di input non valido'. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft*. |Il server è già registrato con un altro insieme di credenziali.| Annullare la registrazione del server nell'insieme di credenziali e registrarlo nuovamente.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]

| Dettagli errore | Possibili cause | Azioni consigliate |
| ---     | ---     | ---    |          
| **Error (Errore) (Error (Errore)e)** </br><ol><li>*L'attivazione non è stata completata. Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x1FC07]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft* <li>*Errore 34506. La passphrase di crittografia archiviata su questo computer non è configurata correttamente*. | <li> La cartella Scratch si trova in un volume che dispone di spazio insufficiente. <li> La cartella Scratch viene spostata in modo errato in un'altra posizione. <li> Il file OnlineBackup.KEK è mancante. | <li>Spostare la cartella Scratch o la posizione della cache in un volume con spazio libero equivalente al 5-10% delle dimensioni totali dei dati di backup. Per spostare correttamente il percorso della cache, vedere le procedure in [Domande sull'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Verificare che sia presente il file OnlineBackup.KEK. <br>*Il percorso predefinito della cartella Scratch o della posizione della cache è C:\Programmi\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [backup del server di Windows con l'agente di Backup di Azure](tutorial-backup-windows-server-to-azure.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
