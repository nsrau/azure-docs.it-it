---
title: Risolvere i problemi del server di Backup di Azure | Microsoft Docs
description: Risolvere i problemi di installazione e registrazione del server di Backup di Azure ed eseguire backup e ripristino dei carichi di lavoro delle applicazioni
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: 71da98bf6d53ab50df4f6e40cf0b548752d10f93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Risolvere i problemi del server di Backup di Azure

È possibile risolvere gli errori rilevati durante l'uso di server di Backup di Azure con le informazioni elencate nella tabella seguente.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Errore: È stato fornito un insieme di credenziali non valido. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino 

Per la risoluzione del problema, seguire questa [procedura] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="error-the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>Errore: Operazione dell'agente protezione non riuscita. Errore di comunicazione con il servizio Coordinatore agenti Data Protection Manager in <Server> 

Per la risoluzione del problema, seguire questa [procedura] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="error-setup-could-not-update-registry-metadata"></a>Errore: Non è stato possibile aggiornare i metadati del Registro di sistema

Per la risoluzione del problema, seguire questa [procedura] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues).


## <a name="installation-issues"></a>Problemi di installazione

| Operazione | Dettagli errore | Soluzione alternativa |
|-----------|---------------|------------|
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema ReFS Trimming. | Modificare la chiave del Registro di sistema, SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Impostare il valore DWORD su 1. |
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema Volume SnapOptimization. | Creare la chiave del Registro di sistema, SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds, con un valore di stringa vuoto. |

## <a name="registration-and-agent-related-issues"></a>Problemi relativi alla registrazione e all'agente
| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Registrazione in un insieme di credenziali | Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino | Per correggere l'errore:  <ol><li> Scaricare il file di credenziali più recente dall'insieme e riprovare <br>OPPURE</li> <li> Se il problema non viene risolto, provare a scaricare le credenziali in una directory locale diversa o creare un nuovo insieme <br>OPPURE</li> <li> Provare ad aggiornare le impostazioni di data e ora come illustrato in [questo blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/) <br>OPPURE</li> <li> Controllare se il percorso c:\windows\temp contenga più di 65.000 file. Spostare i file non aggiornati in un altro percorso o eliminare gli elementi nella cartella Temp <br>OPPURE</li> <li> Controllare lo stato dei certificati <br> a. Aprire "Gestisci i certificati computer" nel Pannello di controllo <br> b. Espandere il nodo "Personale" e il relativo nodo figlio "Certificati" <br> c.  Rimuovere il certificato "Strumenti di Windows Azure" <br> d. Ripetere la registrazione nel client di Backup di Azure <br> OPPURE </li> <li> Verificare se siano applicati eventuali criteri di gruppo </li></ol> |
| Push degli agenti in server protetti | Operazione agente non riuscita a causa di un errore di comunicazione con il servizio Coordinatore agenti DPM in \<ServerName> | **Se l'azione consigliata nel prodotto non funziona**, <ol><li> Se si collega un computer da un dominio non trusted, seguire [questi](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) passaggi <br> OPPURE </li><li> Se si collega un computer da un dominio trusted, risolvere i problemi mediante i passaggi descritti in [questo post di blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/) <br>OPPURE</li><li> Provare a disabilitare l'antivirus come passaggio per la risoluzione dei problemi. Se il problema viene risolto, modificare le impostazioni dell'antivirus come suggerito in [questo articolo] (https://technet.microsoft.com/library/hh757911.aspx)</li></ol> |
| Push degli agenti in server protetti | Le credenziali specificate per il server non sono valide | **Se l'azione consigliata nel prodotto non funziona**, <br> provare a installare manualmente l'agente protezione nel server di produzione come specificato in [questo articolo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)|
| L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure (ID: 100050) | L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure. | **Se l'azione consigliata nel prodotto non funziona**, <br>1. Eseguire il comando seguente da un prompt con privilegi elevati: psexec -i -s "c:\Programmi\Internet Explorer\iexplore.exe". Verrà aperta la finestra di Internet Explorer. <br/> 2. Scegliere Strumenti -> Opzioni Internet -> Connessioni -> Impostazioni LAN. <br/> 3. Verificare le impostazioni del proxy per l'account di sistema. Impostare l'IP del Proxy e la porta. <br/> 4. Chiudere Internet Explorer.|
| Installazione dell'agente di Backup di Azure non riuscita | L'installazione di Servizi di ripristino di Microsoft Azure non è riuscita. È stato eseguito il rollback di tutte le modifiche apportate al sistema dall'installazione di Servizi di ripristino di Microsoft Azure. (ID: 4024) | Installare manualmente l'agente di Azure


## <a name="configuring-protection-group"></a>Configurazione di un gruppo protezione dati
| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Configurazione di gruppi di protezione | DPM non è in grado di enumerare il componente dell'applicazione nel computer protetto (Nome computer protetto) | Fare clic su "Aggiorna"' nella schermata dell'interfaccia utente per la configurazione del gruppo protezione dati al livello di origine dati/componente appropriato |
| Configurazione di gruppi di protezione | Impossibile configurare la protezione | Se il server protetto è un server SQL, verificare se le autorizzazioni del ruolo sysadmin siano state fornite all'account di sistema (NTAuthority\System) nel computer protetto come indicato in [questo articolo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)
| Configurazione di gruppi di protezione | Non c'è spazio sufficiente nel pool di archiviazione per questo gruppo protezione dati | I dischi aggiunti al pool di archiviazione [non devono contenere una partizione](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Eliminare i volumi presenti nei dischi e quindi aggiungerlo al pool di archiviazione|
| Modifica dei criteri |Non è possibile modificare i criteri di backup. Errore: impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x29834]. Ripetere l'operazione in un secondo momento. Se il problema persiste, contattare il supporto tecnico Microsoft. |**Causa:**<br/>Questo errore si verifica quando sono abilitate le impostazioni di sicurezza, si prova a ridurre l'intervallo di conservazione dei dati al sotto dei valori minimi specificati in precedenza e si usa una versione non supportata, ovvero precedente a MAB 2.0.9052 o al server di Backup di Azure Update 1. <br/>**Azione consigliata:**<br/> In questo caso, per procedere con gli aggiornamenti relativi ai criteri è necessario impostare il periodo di conservazione dei dati al di sopra del valore minimo specificato, ovvero sette giorni per il backup giornaliero, quattro settimane per il backup settimanale, tre settimane per il backup mensile o un anno per il backup annuale. Facoltativamente, per sfruttare tutti gli aggiornamenti della sicurezza un approccio consigliato è aggiornare l'agente di backup e il server di Backup di Azure. |

## <a name="backup"></a>Backup
| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | La replica è incoerente | Altri dettagli sulle cause di incoerenza nella replica e relativi suggerimenti sono disponibili [qui](https://technet.microsoft.com/library/cc161593.aspx) <br> <ol><li> In caso di backup dello stato del sistema o di ripristino bare metal, verificare se Windows Server Backup sia installato o meno nel server protetto </li><li> Verificare la presenza di problemi di spazio nel pool di archiviazione DPM nel server DPM/MABS e allocare ulteriore memoria in base alle necessità </li><li> Controllare lo stato del servizio Copia Shadow del volume nel server protetto. Se è disabilitato, impostarlo per l'avvio manuale e avviare il servizio nel server. Tornare quindi alla console di DPM/MABS e avviare la sincronizzazione con verifica della coerenza.</li></ol>|
| Backup | Si è verificato un errore imprevisto durante l'esecuzione del processo, il dispositivo non è pronto | **Se l'azione consigliata nel prodotto non funziona**, <br> <ol><li>impostare lo spazio di archiviazione della Copia Shadow come illimitato negli elementi del gruppo protezione dati e avviare la verifica coerenza <br></li> OPPURE <li>Provare a eliminare il gruppo protezione dati esistente e crearne di nuovi, ciascuno contenente ogni elemento singolo</li></ol> |
| Backup | Se si sta eseguendo il backup solamente dello stato del sistema, verificare che ci sia spazio libero sufficiente nel computer protetto per archiviarlo | <ol><li>Verificare che WSB sia installato nel computer protetto</li><li>Verificare che ci sia spazio sufficiente nel computer protetto per lo stato del sistema: il modo più semplice per farlo è aprire WSB nel computer protetto, fare clic nelle varie selezioni e selezionare il ripristino bare metal. A quel punto, l'interfaccia utente indicherà lo spazio richiesto per l'operazione. Aprire WSB -> Backup locale -> Pianificazione backup -> Selezione configurazione di backup -> Server completo (vengono visualizzate le dimensioni). Usare queste dimensioni per la verifica.</li></ol>
| Backup | Impossibile creare il punto di ripristino online | Se viene visualizzato il messaggio di errore "L'agente di Backup di Microsoft Azure non è riuscito a creare uno snapshot del volume selezionato.", provare ad aumentare lo spazio nel volume del punto di ripristino e della replica.
| Backup | Impossibile creare il punto di ripristino online | Se il messaggio di errore indica che l'agente di Backup di Microsoft Azure non può connettersi al servizio OBEngine, verificare che OBEngine sia incluso nell'elenco dei servizi in esecuzione nel computer. Se il servizio OBEngine non è in esecuzione, usare il comando "net start OBEngine" per avviarlo.
| Backup | Impossibile creare il punto di ripristino online | Se viene visualizzato il messaggio di errore "Non è stata impostata la passphrase di crittografia per questo server. Configurare una passphrase di crittografia.", provare a configurare una passphrase di crittografia. In caso di esito negativo, <br> <ol><li>verificare se esista o meno il percorso dei file temporanei. Dovrebbe esistere il percorso indicato nel Registro di sistema HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config con nome "ScratchLocation".</li><li> Se il percorso dei file temporanei esiste, provare nuovamente a eseguire la registrazione con la passphrase precedente. **Quando si configura una passphrase di crittografia, salvarla in un luogo sicuro**</li><ol>
| Backup | Errore di backup per il ripristino bare metal | Se il ripristino bare metal ha dimensioni elevate, riprovare dopo aver spostato alcuni file di applicazione nell'unità del sistema operativo |
| Backup | Errore durante l'accesso a file o cartelle condivise | Provare a modificare le impostazioni dell'antivirus come suggerito [qui](https://technet.microsoft.com/library/hh757911.aspx)|
| Backup | I processi di creazione dei punti di recupero online per VM VMware non riescono. DPM ha riscontrato un errore di VMWare durante il tentativo di ottenere informazioni di rilevamento modifiche. Codice di errore: FileFaultFault (ID 33621) |  1. Reimpostare il rilevamento modifiche in VMWare per le VM interessate <br/> Controllare che in VMWare non sia presente un disco indipendente <br/> Arrestare la protezione per le VM interessate e riapplicare la protezione con il pulsante Aggiorna <br/> Eseguire una verifica di coerenza per le VM interessate|


## <a name="change-passphrase"></a>Modificare la passphrase
| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Modificare la passphrase |Il PIN di sicurezza immesso non è corretto. Specificare il PIN di sicurezza corretto per completare questa operazione. |**Causa:**<br/> Questo errore si verifica quando si immette un PIN di sicurezza non valido o scaduto durante l'esecuzione di operazioni critiche, ad esempio la modifica della passphrase. <br/>**Azione consigliata:**<br/> Per completare l'operazione, è necessario immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare a Insieme di credenziali di Servizi di ripristino > Impostazioni > Proprietà > Genera PIN di sicurezza. Usare questo PIN per modificare la passphrase. |
| Modificare la passphrase |Operazione non riuscita. ID: 120002 |**Causa:**<br/>Questo errore si verifica quando sono abilitate impostazioni di sicurezza, si prova a modificare la passphrase e si usa una versione non supportata.<br/>**Azione consigliata:**<br/> Per modificare la passphrase è prima necessario aggiornare l'agente di backup almeno alla versione 2.0.9052 e il server di Backup di Azure almeno all'Update 1, quindi immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare a Insieme di credenziali di Servizi di ripristino > Impostazioni > Proprietà > Genera PIN di sicurezza. Usare questo PIN per modificare la passphrase. |


## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica
| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Tentativo di configurazione delle notifiche di posta elettronica tramite l'account di Office365. | Viene restituito l'ID errore 2013| **Causa:**<br/> Tentativo di usare l'account di Office 365 <br/> **Azione consigliata:**<br/> Verificare prima di tutto che l'inoltro anonimo sia consentito in un connettore di ricezione per il server DPM in Exchange. Ecco un collegamento con informazioni su come configurare questa opzione: http://technet.microsoft.com/it-it/library/bb232021.aspx <br/> Se non è possibile usare un inoltro SMTP interno ed è necessario eseguire la configurazione con il server di Office 365, è possibile configurare IIS come inoltro a questo scopo. <br/> Sarà necessario configurare il server DPM perché sia in grado di inoltrare l'SMTP a Office 365 tramite IIS https://technet.microsoft.com/it-it/library/aa995718(v=exchg.65).aspx per configurare IIS per l'inoltro a Office 365 <br/> Nota importante: nel passaggio 3 -> g -> ii, assicurarsi di usare il formato user@domain.com e NON dominio\utente <br/> Impostare DPM in modo da usare il nome del server locale come server SMTP, porta 587, e quindi l'indirizzo e-mail dell'utente da cui devono provenire i messaggi di posta elettronica. <br/> Il nome utente e la password nella pagina di configurazione dell'SMTP per DPM devono corrispondere a un account di dominio nel dominio in cui è attivo DPM. <br/> NOTA: quando si modifica l'indirizzo del server SMTP, assicurarsi di apportare le modifiche alle nuove impostazioni, quindi chiudere la finestra delle impostazioni e riaprirla per verificare che rifletta il nuovo valore.  Se ci si limita semplicemente a modificare e testare il valore, non sempre viene applicata la nuova impostazione, per cui questo tipo di verifica è la procedura consigliata. <br/> In qualsiasi momento durante il processo è possibile cancellare queste impostazioni chiudendo la console DPM e modificando le chiavi del Registro di sistema seguenti:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Eliminare le chiavi SMTPPassword e SMTPUserName. <br/> Sarà possibile aggiungerle di nuovo nell'interfaccia utente all'avvio successivo.
