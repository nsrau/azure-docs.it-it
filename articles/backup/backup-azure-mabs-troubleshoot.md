---
title: Risolvere i problemi del server di Backup di Azure
description: Risolvere i problemi di installazione e registrazione del server di Backup di Azure ed eseguire backup e ripristino dei carichi di lavoro delle applicazioni.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 54b7295eaed5f04a118cf5097ebc7b25b18f67d2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522845"
---
# <a name="troubleshoot-azure-backup-server"></a>Risolvere i problemi del server di Backup di Azure

Fare riferimento alle informazioni elencate nelle tabelle seguenti possibile risolvere gli errori rilevati durante l'uso di server di Backup di Azure.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di iniziare la risoluzione dei problemi del server di Backup di Microsoft Azure, è consigliabile eseguire la verifica seguente:

- [Assicurarsi che l'agente di Servizi di ripristino di Microsoft Azure sia aggiornato](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verificare che sia presente la connettività di rete tra l'agente MARS e Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Verificare che i Servizi di ripristino di Microsoft Azure siano in esecuzione (nella console di Servizio). Se necessario, riavviare e ripetere l'operazione
- [Verificare che sia disponibile il 5-10% di volume libero nel percorso della cartella dei file temporanei](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Se la registrazione ha esito negativo, verificare che il server in cui si sta provando a installare server di Backup di Azure non sia già registrato con un altro insieme di credenziali
- Se l'installazione del push ha esito negativo, verificare se l'agente DPM è già presente. In caso affermativo, disinstallare l'agente e provare a ripetere l'installazione
- [Verificare che nessun altro processo o software antivirus interferisca con Backup di Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Verificare che il servizio SQL Agent sia in esecuzione automatica nel server di Backup di Microsoft Azure<br>

## <a name="configure-antivirus-for-mabs-server"></a>Configurare l'antivirus per il server MAB

MAB è compatibile con i prodotti software antivirus più diffusi. Per evitare conflitti, consigliamo i passaggi seguenti:

1. **Disabilitare il monitoraggio in tempo reale** : disabilitare il monitoraggio in tempo reale da parte del software antivirus per quanto segue:
    - `C:\Program Files<MABS Installation path>\XSD`
    - `C:\Program Files<MABS Installation path>\Temp`
    - Lettera di unità del volume Modern Backup Storage
    - Log di replica e trasferimento: per eseguire questa operazione, disabilitare il monitoraggio in tempo reale di **dpmra.exe**, disponibile nella cartella `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Il monitoraggio in tempo reale comporta un peggioramento delle prestazioni perché il software antivirus esegue l'analisi delle repliche ogni volta che l'oggetto MAB si sincronizza con il server protetto e analizza tutti i file interessati ogni volta che l'applicazione MAB applica le modifiche alle repliche.
    - Console di amministrazione: per evitare un effetto sulle prestazioni, disabilitare il monitoraggio in tempo reale del processo di **csc.exe** . Il processo **csc.exe** è il \# compilatore C e il monitoraggio in tempo reale può peggiorare le prestazioni perché il software antivirus analizza i file emessi dal processo di **csc.exe** quando genera messaggi XML. **CSC.exe** si trova nei percorsi seguenti:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - Per l'agente MARS installato nel server MAB, è consigliabile escludere i file e i percorsi seguenti:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` come processo
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Percorso dei file temporanei (se non si usa il percorso standard)
2. **Disabilitare il monitoraggio in tempo reale nel server protetto**: disabilitare il monitoraggio in tempo reale di **dpmra.exe**, che si trova nella cartella `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , nel server protetto.
3. **Configurare il software antivirus per eliminare i file infetti nei server protetti e nel server MAB**: per evitare il danneggiamento dei dati delle repliche e dei punti di ripristino, configurare il software antivirus in modo da eliminare i file infetti, anziché pulirli automaticamente o metterli in quarantena. La pulizia e la quarantena automatiche potrebbero causare la modifica dei file da parte del software antivirus, apportando modifiche non rilevabili da MAB.

È consigliabile eseguire regolarmente una sincronizzazione manuale con un processo di verifica della coerenza Controllare il processo ogni volta che il software antivirus elimina un file dalla replica, anche se la replica è contrassegnata come incoerente.

### <a name="mabs-installation-folders"></a>Cartelle di installazione di MAB

Le cartelle di installazione predefinite per DPM sono le seguenti:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

È anche possibile eseguire il comando seguente per trovare il percorso della cartella di installazione:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Registrazione in un insieme di credenziali | Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. | Azione consigliata: <br> <ul><li> Scaricare il file di credenziali più recente dall'insieme e riprovare. <br>OPPURE</li> <li> Se il problema non viene risolto, provare a scaricare le credenziali in una directory locale diversa o creare un nuovo insieme di credenziali. <br>OPPURE</li> <li> Provare ad aggiornare le impostazioni di data e ora come descritto in [questo articolo](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>OPPURE</li> <li> Controllare se il percorso c:\windows\temp contenga più di 65000 file. Spostare i file non aggiornati in un altro percorso o eliminare gli elementi nella cartella Temp. <br>OPPURE</li> <li> Controllare lo stato dei certificati. <br> a. Aprire **Gestisci i certificati computer** nel Pannello di controllo. <br> b. Espandere il nodo **Personale** e il relativo nodo figlio **Certificati**.<br> c.  Rimuovere il certificato **Strumenti di Windows Azure**. <br> d. Ripetere la registrazione nel client di Backup di Azure. <br> OPPURE </li> <li> Verificare se siano applicati eventuali criteri di gruppo. </li></ul> |

## <a name="replica-is-inconsistent"></a>La replica è incoerente

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | La replica è incoerente | Verificare che l'opzione relativa alla verifica di coerenza automatica nella procedura guidata Gruppo di protezione sia attivata. Per altre informazioni sulle opzioni di replica e sulle verifiche coerenza, vedere [questo articolo](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019).<br> <ol><li> In caso di backup dello stato del sistema o di ripristino bare metal, verificare che Windows Server Backup sia installato nel server protetto.</li><li> Verificare la presenza di problemi di spazio nel pool di archiviazione DPM del server DPM o di Backup di Microsoft Azure e allocare memoria in base alle necessità.</li><li> Controllare lo stato del servizio Copia Shadow del volume nel server protetto. Se è disabilitato, impostarlo per l'avvio manuale. Avviare il servizio sul server. Tornare quindi alla console di DPM o del server di Backup di Microsoft Azure e avviare la sincronizzazione con il processo di verifica della coerenza.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Impossibile creare il punto di ripristino online

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | Impossibile creare il punto di ripristino online | **Messaggio di errore**: L'agente di Backup di Microsoft Azure non è riuscito a creare uno snapshot del volume selezionato. <br> **Soluzione alternativa**: provare ad aumentare lo spazio nel volume di replica e del punto di ripristino.<br> <br> **Messaggio di errore**: L'agente di Backup di Microsoft Azure non è riuscito a connettersi al servizio OBEngine. <br> **Soluzione alternativa**: verificare che OBEngine sia presente nell'elenco dei servizi in esecuzione nel computer. Se il servizio OBEngine non è in esecuzione, usare il comando "net start OBEngine" per avviarlo. <br> <br> **Messaggio di errore**: Non è stata impostata la passphrase di crittografia per questo server. Configurare una passphrase di crittografia. <br> **Soluzione alternativa**: provare a configurare una passphrase di crittografia. Se l'operazione non riesce, seguire questa procedura: <br> <ol><li>Verificare che il percorso dei file temporanei esista. Questo è il percorso indicato nel Registro di sistema in **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** con il nome **ScratchLocation**.</li><li> Se il percorso dei file temporanei esiste, provare di nuovo a eseguire la registrazione con la passphrase precedente. *Quando si configura una passphrase di crittografia, salvarla in un luogo sicuro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>I server DPM originali e quelli esterni devono essere registrati nello stesso insieme di credenziali

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Restore | **Codice errore**: Errore CBPServerRegisteredVaultDontMatchWithCurrent/Credenziali dell'insieme di credenziali: 100110 <br/> <br/>**Messaggio di errore**: I server DPM originali e quelli esterni devono essere registrati nello stesso insieme di credenziali | **Causa**: questo problema si verifica se si cerca di ripristinare file nel server alternativo dal server originale usando l'opzione di ripristino DPM esterno e se il server da ripristinare e il server originale da cui è stato eseguito il backup dei dati non sono associati allo stesso insieme di credenziali di Servizi di ripristino.<br/> <br/>**Soluzione alternativa**: per risolvere questo problema verificare che il server originale e il server alternativo siano registrati nello stesso insieme di credenziali.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>I processi di creazione dei punti di ripristino online per VM VMware non riescono

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | I processi di creazione dei punti di recupero online per VM VMware non riescono. DPM ha riscontrato un errore di VMWare durante il tentativo di ottenere informazioni di rilevamento modifiche. Codice di errore: FileFaultFault (ID 33621) |  <ol><li> Reimpostare il rilevamento modifiche in VMWare per le VM interessate.</li> <li>Controllare che in VMWare non sia presente un disco indipendente.</li> <li>Arrestare la protezione per le VM interessate e riapplicare la protezione con il pulsante **Aggiorna**. </li><li>Eseguire una verifica di coerenza per le VM interessate.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operazione agente non riuscita a causa di un errore di comunicazione con il servizio coordinatore agenti DPM nel server

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Push degli agenti in server protetti | Operazione dell'agente non riuscita a causa di un errore di comunicazione con il servizio Coordinatore agenti DPM su \<ServerName> . | **Se l'azione consigliata visualizzata nel prodotto non funziona, seguire questa procedura**: <ul><li> Se si collega un computer da un dominio non trusted, seguire [questa procedura](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> OPPURE </li><li> Se si collega un computer da un dominio trusted, risolvere i problemi mediante i passaggi descritti in [questo blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>OPPURE</li><li> Provare a disabilitare l'antivirus come passaggio per la risoluzione dei problemi. Se il problema viene risolto, modificare le impostazioni dell'antivirus come suggerito in [questo articolo](/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Non è stato possibile aggiornare i metadati del Registro di sistema

| Operazione | Dettagli errore | Soluzione alternativa |
|-----------|---------------|------------|
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema ReFS Trimming. | Modificare la chiave del Registro di sistema **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Impostare il valore DWORD su 1. |
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema Volume SnapOptimization. | Creare la chiave del Registro di sistema **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** con un valore di stringa vuoto. |

## <a name="registration-and-agent-related-issues"></a>Problemi relativi alla registrazione e all'agente

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Push degli agenti in server protetti | Le credenziali specificate per il server non sono valide. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura**: <br> Provare a installare manualmente l'agente protezione nel server di produzione come specificato in [questo articolo](/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure (ID: 100050) | L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura**: <br>1. Eseguire il comando seguente da un prompt con privilegi elevati: **psexec -i -s "c:\Programmi\Internet Explorer\iexplore.exe**. Verrà aperta la finestra di Internet Explorer. <br/> 2. Passare a **Strumenti** > **Opzioni Internet** > **Connessioni** > **Impostazioni LAN**. <br/> 3. Modificare le impostazioni per l'utilizzo di un server proxy. Indicare quindi i dettagli del server proxy.<br/> 4. Se il computer ha un accesso a Internet limitato, verificare che le impostazioni del firewall nel computer o nel proxy consentano questi [URL](install-mars-agent.md#verify-internet-access) e questi [indirizzi IP](install-mars-agent.md#verify-internet-access).|
| Installazione dell'agente di Backup di Azure non riuscita | L'installazione di Servizi di ripristino di Microsoft Azure non è riuscita. È stato eseguito il rollback di tutte le modifiche apportate al sistema dall'installazione di Servizi di ripristino di Microsoft Azure. (ID: 4024) | Installare manualmente l'agente di Azure.

## <a name="configuring-protection-group"></a>Configurazione di un gruppo protezione dati

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Configurazione di gruppi di protezione | DPM non è in grado di enumerare il componente dell'applicazione nel computer protetto (nome computer protetto). | Selezionare **Aggiorna** nella schermata dell'interfaccia utente per la configurazione del gruppo protezione dati al livello di origine dati/componente appropriato. |
| Configurazione di gruppi di protezione | Impossibile configurare la protezione | Se il server protetto è un server SQL, verificare che le autorizzazioni del ruolo sysadmin siano state fornite all'account di sistema (NTAuthority\System) del computer protetto come indicato in [questo articolo](/system-center/dpm/back-up-sql-server?view=sc-dpm-2019).
| Configurazione di gruppi di protezione | Non c'è spazio sufficiente nel pool di archiviazione per questo gruppo protezione dati. | I dischi aggiunti al pool di archiviazione [non devono contenere una partizione](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019). Eliminare gli eventuali volumi presenti sui dischi. Quindi aggiungerli al pool di archiviazione.|
| Modifica dei criteri |Non è possibile modificare i criteri di backup. Errore: Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x29834]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft. | **Causa:**<br/>Questo errore si verifica in tre situazioni: quando sono abilitate le impostazioni di sicurezza, quando si prova a ridurre l'intervallo di conservazione dei dati al di sotto dei valori minimi specificati in precedenza e quando si usa una versione non supportata. Le versioni non supportate sono quelle precedenti al server di Backup di Microsoft Azure 2.0.9052 e server di Backup di Azure aggiornamento 1. <br/>**Azione consigliata:**<br/> per procedere con gli aggiornamenti relativi ai criteri, impostare un periodo di conservazione superiore al periodo di conservazione minimo specificato. Il periodo di conservazione minimo è sette giorni per i backup giornalieri, quattro settimane per quelli settimanali, tre settimane per quelli mensili e un anno per quelli annuali. <br><br>Facoltativamente, per sfruttare tutti gli aggiornamenti della sicurezza, un altro approccio consigliato è aggiornare l'agente di backup e il server di Backup di Azure. |

## <a name="backup"></a>Backup

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | Si è verificato un errore imprevisto durante l'esecuzione del processo. Il dispositivo non è pronto. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura:** <br> <ul><li>Impostare lo spazio di archiviazione della copia shadow come illimitato negli elementi del gruppo protezione dati e quindi avviare la verifica coerenza.<br></li> OPPURE <li>Provare a eliminare il gruppo protezione dati esistente e a creare più nuovi gruppi. Ogni nuovo gruppo protezione dati deve contenere un singolo elemento.</li></ul> |
| Backup | Se si sta eseguendo il backup solamente dello stato del sistema, verificare che ci sia spazio libero sufficiente nel computer protetto per archiviarlo. | <ol><li>Verificare che Windows Server Backup sia installato nel computer protetto.</li><li>Verificare che vi sia spazio sufficiente nel computer protetto per lo stato del sistema. Il modo più semplice per verificare questa condizione è passare al computer protetto, aprire Windows Server Backup, fare clic sulle selezioni e quindi selezionare il ripristino bare metal. Nell'interfaccia utente verrà indicato lo spazio necessario. Aprire **WSB** > **Backup locale** > **Pianificazione backup** > **Selezione configurazione di backup** > **Server completo** (vengono visualizzate le dimensioni). Usare queste dimensioni per la verifica.</li></ol>
| Backup | Errore di backup per il ripristino bare metal | Se le dimensioni del ripristino bare metal sono grandi, spostare alcuni file di applicazione nell'unità del sistema operativo e riprovare. |
| Backup | L'opzione per proteggere nuovamente una macchina virtuale VMware in un nuovo server di Backup di Microsoft Azure non viene visualizzata come disponibile per l'aggiunta. | Le proprietà VMware fanno riferimento a un'istanza precedente ritirata del server di Backup di Microsoft Azure. Per risolvere il problema:<br><ol><li>in VCenter (equivalente a SC VMM), passare alla scheda **Summary** e quindi a **Custom Attributes**.</li>  <li>Eliminare il vecchio nome del server di Backup di Microsoft Azure dal valore **DPMServer**.</li>  <li>Tornare al nuovo server di Backup di Microsoft Azure e modificare il PG.  Dopo aver selezionato il pulsante **Refresh**, la macchina virtuale verrà visualizzata con una casella di controllo come disponibile per l'aggiunta alla protezione.</li></ol> |
| Backup | Errore durante l'accesso a file o cartelle condivise | Provare a modificare le impostazioni antivirus come indicato nell'articolo [Eseguire software antivirus sul server DPM](/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).|

## <a name="change-passphrase"></a>Modificare la passphrase

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Modificare la passphrase |Il PIN di sicurezza immesso non è corretto. Specificare il PIN di sicurezza corretto per completare questa operazione. |**Causa:**<br/> Questo errore si verifica quando si immette un PIN di sicurezza non valido o scaduto durante l'esecuzione di un'operazione critica (ad esempio, se si modifica una passphrase). <br/>**Azione consigliata:**<br/> Per completare l'operazione, è necessario immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino. Passare quindi a **Impostazioni** > **Proprietà** > **Genera PIN di sicurezza**. Usare questo PIN per modificare la passphrase. |
| Modificare la passphrase |Operazione non riuscita. ID: 120002 |**Causa:**<br/>Questo errore si verifica quando sono abilitate le impostazioni di sicurezza o quando si tenta di modificare la passphrase se si usa una versione non supportata.<br/>**Azione consigliata:**<br/> Per modificare la passphrase, è innanzitutto necessario aggiornare l'agente di backup con la versione minima, 2.0.9052. È anche necessario aggiornare il server di Backup di Azure per con la versione minima dell'aggiornamento 1 e quindi immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino. Passare quindi a **Impostazioni** > **Proprietà** > **Genera PIN di sicurezza**. Usare questo PIN per modificare la passphrase. |

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Impostazione delle notifiche di posta elettronica tramite un account di Office 365 |ID errore: 2013| **Causa:**<br> Tentativo di usare l'account di Office 365 <br>**Azione consigliata:**<ol><li> Verificare prima di tutto che l'inoltro anonimo sia consentito in un connettore di ricezione per il server DPM in Exchange. Per altre informazioni su come configurare questa impostazione, vedere [Autorizzazione per l'inoltro anonimo su un connettore di ricezione](/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Se non è possibile usare un inoltro SMTP interno ed è necessario eseguire la configurazione con il server di Office 365, è possibile configurare IIS come inoltro. Configurare il server DPM per l'[inoltro SMTP a Office 365 tramite IIS](/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019).<br><br>  Assicurarsi di usare il formato user\@domain.com e *non* domain\user.<br><br><li>Impostare DPM in modo da usare il nome del server locale come server SMTP e la porta 587. Quindi impostarlo con l'indirizzo di posta elettronica dell'utente da cui dovrebbero provenire i messaggi.<li> Il nome utente e la password nella pagina di configurazione dell'SMTP per DPM devono corrispondere a un account di dominio nel dominio in cui è attivo DPM. </li><br> quando si modifica l'indirizzo del server SMTP, assicurarsi di apportare le modifiche alle nuove impostazioni, quindi chiudere la finestra delle impostazioni e riaprirla per verificare che rifletta il nuovo valore.  Se ci si limita ad apportare la modifica ed eseguire il test, è possibile che le nuove impostazioni non vengano applicate, pertanto questo tipo di verifica è la procedura consigliata.<br><br>In qualsiasi momento durante il processo è possibile cancellare queste impostazioni chiudendo la console DPM e modificando le chiavi del Registro di sistema seguenti: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Eliminare le chiavi SMTPPassword e SMTPUserName**. Sarà possibile aggiungerle di nuovo nell'interfaccia utente all'avvio successivo.

## <a name="common-issues"></a>Problemi comuni

Questa sezione illustra gli errori comuni che possono verificarsi durante l'uso del server di Backup di Azure.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Messaggio di errore | Azione consigliata |
-- | --
Il backup non è riuscito perché la replica di backup su disco non è valido o è mancante. | Per risolvere questo problema, verificare i passaggi seguenti e ripetere l'operazione: <br/> 1. Creare un punto di ripristino del disco<br/> 2. Eseguire la verifica coerenza sull'origine dati <br/> 3. Arrestare la protezione dell'origine dati e quindi riconfigurare la protezione di questa

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Messaggio di errore | Azione consigliata |
-- | --
Lo snapshot del volume di origine non è riuscito perché i metadati della replica non sono validi. | Creare un punto di ripristino del disco di questa origine dati e ripetere il backup online

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Messaggio di errore | Azione consigliata |
-- | --
Lo snapshot del volume di origine non è riuscito perché la replica dell'origine dati non è coerente. | Eseguire una verifica coerenza in questa origine dati e riprovare

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Messaggio di errore | Azione consigliata |
-- | --
Il backup non è riuscito perché non è stato possibile clonare la replica di backup su disco.| Assicurarsi che tutti i file della replica di backup su disco precedenti (con estensione vhdx) vengano smontati e che non sia in corso un backup da disco a disco durante i backup online
