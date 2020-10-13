---
title: Risolvere i problemi di replica nella migrazione di macchine virtuali VMware senza agente
description: Ottenere supporto per gli errori del ciclo di replica
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 6318f426e42612f21da7a43c9857894ae610f68e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871182"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Risoluzione dei problemi di replica nella migrazione di macchine virtuali VMware senza agente

Questo articolo descrive alcuni problemi comuni ed errori specifici che possono verificarsi quando si esegue la replica di VM VMware locali usando il metodo di migrazione Azure Migrate: server senza agente.

Quando si esegue la replica di una macchina virtuale VMware con il metodo di replica senza agenti, i dati dei dischi della macchina virtuale&#39;s (VMDK) vengono replicati nei dischi gestiti di replica nella sottoscrizione di Azure. Quando viene avviata la replica per una macchina virtuale, si verifica un ciclo di replica iniziale in cui vengono replicate le copie complete dei dischi. Al termine della replica iniziale, i cicli di replica incrementali vengono pianificati periodicamente per trasferire le modifiche apportate dopo il ciclo di replica precedente.

Occasionalmente è possibile che si verifichino errori di cicli di replica per una macchina virtuale. Questi errori possono verificarsi a causa di problemi relativi alla configurazione della rete locale ai problemi del back-end del servizio Cloud Azure Migrate. In questo articolo verrà:

 - Mostra come è possibile monitorare lo stato della replica e risolvere gli errori.
 - Elencare alcuni degli errori di replica comunemente presenti e suggerire ulteriori passaggi per risolverli.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Monitorare lo stato della replica usando il portale di Azure

Per monitorare lo stato della replica per le macchine virtuali, attenersi alla procedura seguente:

  1. Passare alla pagina Server Azure Migrate nella portale di Azure.
  2. Passare alla pagina "replica di computer" facendo clic su "replica dei server" nel riquadro migrazione server.
  3. Verrà visualizzato un elenco di server di replica insieme a informazioni aggiuntive, ad esempio stato, integrità, ora dell'ultima sincronizzazione e così via. La colonna Health indica lo stato di replica corrente della macchina virtuale. Un valore ' critical ' o ' Warning ' nella colonna Health indica in genere che il ciclo di replica precedente per la macchina virtuale non è riuscito. Per ottenere altri dettagli, fare clic con il pulsante destro del mouse sulla macchina virtuale e selezionare "Dettagli errore". La pagina dei dettagli dell'errore contiene informazioni sull'errore e informazioni aggiuntive su come risolvere i problemi. Verrà anche visualizzato un collegamento "eventi recenti" che può essere usato per passare alla pagina eventi per la macchina virtuale.
  4. Fare clic su "eventi recenti" per visualizzare gli errori precedenti del ciclo di replica per la macchina virtuale. Nella pagina eventi, cercare l'evento più recente di tipo "ciclo di replica non riuscito" o "ciclo di replica non riuscito per il disco" per la macchina virtuale.
  5. Fare clic sull'evento per comprendere le possibili cause dell'errore e le procedure consigliate per la correzione. Utilizzare le informazioni fornite per risolvere i problemi e correggere l'errore.
    
## <a name="common-replication-errors"></a>Errori di replica comuni

In questa sezione vengono descritti alcuni degli errori comuni e il modo in cui è possibile risolverli.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Errore durante il tentativo di replicare le macchine virtuali Key Vault operazione non riuscita

**Errore:** "Operazione Key Vault non riuscita. Operazione: configurare l'account di archiviazione gestito, Key Vault: Key-Vault-Name, account di archiviazione: il nome dell'account di archiviazione non è riuscito con l'errore: "

**Errore:** "Operazione Key Vault non riuscita. Operazione: generare la definizione della firma di accesso condiviso, Key Vault: Key-Vault-Name, account di archiviazione: nome dell'account di archiviazione non riuscito con l'errore: "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Questo errore si verifica in genere perché i criteri di accesso utente per il Key Vault non forniscono all'utente attualmente connesso le autorizzazioni necessarie per configurare gli account di archiviazione da Key Vault gestire. Per verificare i criteri di accesso utente nell'insieme di credenziali delle chiavi, passare alla pagina dell'insieme di credenziali delle chiavi nel portale per l'insieme di credenziali delle chiavi e selezionare criteri di accesso 

Quando il portale crea l'insieme di credenziali delle chiavi, aggiunge anche un criterio di accesso utente che concede le autorizzazioni dell'utente attualmente connesso per configurare gli account di archiviazione in modo che vengano Key Vault gestiti. Questa operazione può avere esito negativo per due motivi

- L'utente connesso è un'entità remota nel tenant di Azure Customers (sottoscrizione CSP) e l'utente connesso è l'amministratore del partner. In questo caso la soluzione alternativa consiste nell'eliminare l'insieme di credenziali delle chiavi, disconnettersi dal portale e quindi accedere con un account utente dal tenant Customers (non un'entità remota) e ripetere l'operazione. Il partner CSP ha in genere un account utente nel tenant di Azure Active Directory dei clienti che possono usare. In caso contrario, è possibile creare un nuovo account utente nel tenant di Azure Active Directory dei clienti, accedere al portale come nuovo utente, quindi ripetere l'operazione di replica. L'account usato deve avere le autorizzazioni proprietario o collaboratore e amministratore accesso utenti concesse all'account nel gruppo di risorse (migrazione del gruppo di risorse del progetto)

- L'altro caso in cui si verifica questo problema è che un utente (User1) ha tentato inizialmente di configurare la replica e si è verificato un errore, ma l'insieme di credenziali delle chiavi è già stato creato e i criteri di accesso utente sono stati assegnati in modo appropriato all'utente. A questo punto un altro utente (User2) tenterà di configurare la replica, ma l'operazione di configurazione dell'account di archiviazione gestito o di generazione della definizione di firma di accesso condiviso non riuscirà perché non sono presenti criteri di accesso utente corrispondenti a User2 nell'insieme di credenziali delle chiavi.

**Soluzione**: per risolvere questo problema, creare un criterio di accesso utente per User2 nell'insieme di credenziali delle credenziali che concede l'autorizzazione User2 per configurare l'account di archiviazione gestito e generare le definizioni SAS. User2 può eseguire questa operazione da Azure PowerShell usando i cmdlet seguenti:

$userPrincipalId = $ (Get-AzureRmADUser-UserPrincipalName "user2_email_address"). ID

Set-AzureRmKeyVaultAccessPolicy-VAULTNAME "filevaultname"-ObjectId $userPrincipalId-PermissionsToStorage Get, List, DELETE, set, Update, RegenerateKey, getss, lists, Deletes, setss, Recover, backup, Restore, Purge


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**ID errore:** 181008

**Messaggio di errore:** Macchina virtuale: VMName. Errore: rilevato evento timeout ' DisposeArtefactsTimeout ' nello stato &' [' gateway. Service. StateMachine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle ' (' WaitingForArtefactsDisposalPreCycle ')]'.

**Possibili cause:**

Il componente che tenta di replicare i dati in Azure è inattivo o non risponde. Di seguito sono riportate le possibili cause:

- Il servizio gateway in esecuzione nell'appliance Azure Migrate è inattivo.
- Si sono verificati problemi di connettività del servizio gateway con l'account di archiviazione Service Bus/Hub eventi/Appliance.

**Identificazione della cause esatta per DisposeArtefactsTimedOut e della risoluzione corrispondente:**

1. Assicurarsi che l'appliance Azure Migrate sia operativa.
2. Controllare se il servizio gateway è in esecuzione nel dispositivo:
   1.  Accedere al dispositivo Azure Migrate usando desktop remoto ed eseguire le operazioni seguenti.

   2.  Aprire lo snap-in MMC Servizi Microsoft (eseguire > Services. msc) e verificare se il "Microsoft Azure gateway servizio" è in esecuzione. Se il servizio è stato interrotto o non è in esecuzione, avviare il servizio. In alternativa, è possibile aprire il prompt dei comandi o PowerShell e procedere come segue: "net start asrgwy"

3. Verificare la presenza di problemi di connettività tra Azure Migrate Appliance e l'account di archiviazione Appliance: 

    Eseguire il comando seguente dopo aver scaricato azcopy nell'appliance Azure Migrate:
    
    _azcopy BENCH https://[account]. blob. Core. Windows. NET/[Container]? FIRMA_
    
    **Passaggi per eseguire il test del benchmark delle prestazioni:**
    
      1. [Scarica](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy
        
      2. Cercare l'account di archiviazione Appliance nel gruppo di risorse. Il nome dell'account di archiviazione è simile a migrategwsa \* \* \* \* \* \* \* \* \* \* . Si tratta del valore del parametro [account] del comando precedente.
        
      3. Cercare l'account di archiviazione nell'portale di Azure. Assicurarsi che la sottoscrizione usata per la ricerca sia la stessa sottoscrizione (sottoscrizione di destinazione) in cui viene creato l'account di archiviazione. Passare a contenitori nella sezione servizio BLOB. Fare clic su + contenitore e creare un contenitore. Lasciare il livello di accesso pubblico al valore predefinito selezionato.
        
      4. Passare a firma di accesso condiviso in impostazioni. Selezionare il contenitore in "tipo di risorsa consentito". Fare clic su genera SAS e stringa di connessione. Copiare il valore SAS.
        
      5. Eseguire il comando precedente nel prompt dei comandi sostituendo account, container, SAS con i valori ottenuti nei passaggi 2, 3 e 4 rispettivamente.
        
      In alternativa, [scaricare](https://go.microsoft.com/fwlink/?linkid=2138967) l'esplorazione di archiviazione di Azure nel dispositivo e provare a caricare 10 BLOB di ~ 64 MB negli account di archiviazione. Se non si verificano problemi, il caricamento dovrebbe avere esito positivo.
        
    **Risoluzione:** Se il test ha esito negativo, si&#39;un problema di rete. Coinvolgere il team di rete locale per verificare i problemi di connettività. In genere, è possibile che alcune impostazioni del Firewall causino errori.
    
4.  Verificare la presenza di problemi di connettività tra Azure Migrate Appliance e il bus di servizio:

    Questo test controlla se l'appliance Azure Migrate può comunicare con il back-end del servizio Cloud Azure Migrate. L'appliance comunica con il back-end del servizio tramite le code di messaggi del bus di servizio e dell'hub eventi. Per convalidare la connettività dal dispositivo al bus di servizio, [scaricare](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer, provare a connettersi al bus di servizio del dispositivo ed eseguire un messaggio di invio/ricezione. Se non si verificano problemi, questa operazione dovrebbe avere esito positivo.

    **Passaggi per eseguire il test:**

    1. Copiare la stringa di connessione dal bus di servizio che è stato creato nel progetto migrate
    2. Aprire Service Bus Explorer
    3. Vai al file e Connetti
    4. Incollare la stringa di connessione e fare clic su Connetti
    5. Verrà aperto lo spazio del nome del bus di servizio
    6. Selezionare Snapshot Manager nell'argomento. Fare clic con il pulsante destro del mouse su Snapshot Manager, selezionare "Ricevi messaggi" > selezionare "Visualizza" e fare clic su OK.
    7. Se la connessione ha esito positivo, verrà visualizzato "[x] messaggi ricevuti" nell'output della console. Se la connessione non riesce, verrà visualizzato un messaggio che informa che la connessione non è riuscita.
    
    **Risoluzione:** Se il test ha esito negativo, si verifica un problema di rete. Coinvolgere il team di rete locale per verificare i problemi di connettività. In genere, è possibile che alcune impostazioni del Firewall causino errori.

5. Problemi di connettività tra Azure Migrate Appliance e Azure Key Vault:

    Questo test verifica la presenza di problemi di connettività tra l'appliance Azure Migrate e il Azure Key Vault. Il Key Vault viene usato per gestire l'accesso all'account di archiviazione usato per la replica.
    
    **Passaggi per controllare la connettività:**
    
    1. Recuperare l'URI Key Vault dall'elenco di risorse nel gruppo di risorse corrispondente a Azure Migrate progetto.
    
    1. Aprire PowerShell nell'appliance Azure Migrate ed eseguire il comando seguente:
    
    _test-NetConnection Key Vault URI-P 443_
    
    Questo comando tenterà una connessione TCP e restituirà un output.
    
     - Nell'output selezionare il campo "_TcpTestSucceeded_". Se il valore è "_true_", non esiste alcun problema di connettività tra l'appliance Azure migrate e il Azure Key Vault. Se il valore è "false", si verifica un problema di connettività.
    
    **Risoluzione:** Se il test ha esito negativo, si verifica un problema di connettività tra l'appliance Azure Migrate e il Azure Key Vault. Coinvolgere il team di rete locale per verificare i problemi di connettività. In genere, è possibile che alcune impostazioni del Firewall causino errori.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**ID errore:** 1011

**Messaggio di errore:** Caricamento dei dati per il disco DiskPath, DiskID della macchina virtuale VMName; VMId non è stato completato entro il tempo previsto.

Questo errore indica in genere che l'appliance di Azure Migrate che esegue la replica non è in grado di connettersi ai servizi cloud di Azure o che la replica sta avanzando lentamente causando il timeout del ciclo di replica.

Di seguito sono riportate le possibili cause:

- Il dispositivo Azure Migrate è inattivo.
- Il servizio del gateway di replica nell'appliance non è in esecuzione.
- Il servizio del gateway di replica sta riscontrando problemi di connettività a uno dei componenti del servizio di Azure seguenti usati per la replica: bus di servizio/Hub eventi/account di archiviazione della cache di Azure/Azure Key Vault.
- Il servizio gateway viene limitato a livello di vCenter durante il tentativo di lettura del disco.

**Identificazione della causa radice e risoluzione del problema:**

1. Assicurarsi che l'appliance Azure Migrate sia operativa.
2. Controllare se il servizio gateway è in esecuzione nel dispositivo:
   1.  Accedere al dispositivo Azure Migrate usando desktop remoto ed eseguire le operazioni seguenti.

   2.  Aprire lo snap-in MMC Servizi Microsoft (eseguire > Services. msc) e verificare se il "Microsoft Azure gateway servizio" è in esecuzione. Se il servizio è stato interrotto o non è in esecuzione, avviare il servizio. In alternativa, è possibile aprire il prompt dei comandi o PowerShell e procedere come segue: "net start asrgwy".


3. **Verificare la presenza di problemi di connettività tra Azure Migrate Appliance e l'account di archiviazione della cache:** 

    Eseguire il comando seguente dopo aver scaricato azcopy nell'appliance Azure Migrate:
    
    _azcopy BENCH https://[account]. blob. Core. Windows. NET/[Container]? FIRMA_
    
    **Passaggi per eseguire il test del benchmark delle prestazioni:**
    
      1. [Scarica](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy
        
      2. Cercare l'account di archiviazione Appliance nel gruppo di risorse. Il nome dell'account di archiviazione è simile a migratelsa \* \* \* \* \* \* \* \* \* \* . Si tratta del valore del parametro [account] del comando precedente.
        
      3. Cercare l'account di archiviazione nell'portale di Azure. Assicurarsi che la sottoscrizione usata per la ricerca sia la stessa sottoscrizione (sottoscrizione di destinazione) in cui viene creato l'account di archiviazione. Passare a contenitori nella sezione servizio BLOB. Fare clic su + contenitore e creare un contenitore. Lasciare il livello di accesso pubblico al valore predefinito selezionato.
        
      4. Passare a firma di accesso condiviso in impostazioni. Selezionare il contenitore in "tipo di risorsa consentito". Fare clic su genera SAS e stringa di connessione. Copiare il valore SAS.
        
      5. Eseguire il comando precedente nel prompt dei comandi sostituendo account, container, SAS con i valori ottenuti nei passaggi 2, 3 e 4 rispettivamente.
        
      In alternativa, [scaricare](https://go.microsoft.com/fwlink/?linkid=2138967) l'esplorazione di archiviazione di Azure nel dispositivo e provare a caricare 10 BLOB di ~ 64 MB negli account di archiviazione. Se non si verificano problemi, il caricamento dovrebbe avere esito positivo.
        
    **Risoluzione:** Se il test ha esito negativo, si&#39;un problema di rete. Coinvolgere il team di rete locale per verificare i problemi di connettività. In genere, è possibile che alcune impostazioni del Firewall causino errori.
                
4.  **Problemi di connettività tra Azure Migrate Appliance e il bus di servizio di Azure:**

    Questo test verificherà se l'appliance Azure Migrate può comunicare con il back-end del servizio Cloud Azure Migrate. L'appliance comunica con il back-end del servizio tramite le code di messaggi del bus di servizio e dell'hub eventi. Per convalidare la connettività dal dispositivo al bus di servizio, [scaricare](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer, provare a connettersi al bus di servizio del dispositivo ed eseguire un messaggio di invio/ricezione. Se non si verificano problemi, questa operazione dovrebbe avere esito positivo.

    **Passaggi per eseguire il test:**
    
    1. Copiare la stringa di connessione dal bus di servizio che è stato creato nel gruppo di risorse corrispondente a Azure Migrate progetto
    
    1. Apri Service Bus Explorer
    
    1. Vai a file > Connetti
    
    1. Incollare la stringa di connessione copiata nel passaggio 1 e fare clic su Connetti
    
    1. Verrà aperto lo spazio dei nomi del bus di servizio.
    
    1. Selezionare Snapshot Manager nell'argomento nello spazio dei nomi. Fare clic con il pulsante destro del mouse su Snapshot Manager, selezionare "receive messages" > selezionare "Peek" e fare clic su OK.
    
    Se la connessione ha esito positivo, verrà visualizzato "[x] messaggi ricevuti" nell'output della console. Se la connessione non riesce, verrà visualizzato un messaggio che informa che la connessione non è riuscita.
    
    **Risoluzione:** Se il test ha esito negativo, si verifica un problema di connettività tra l'appliance Azure Migrate e il bus di servizio. Coinvolgere il team di rete locale per controllare questi problemi di connettività. In genere, è possibile che alcune impostazioni del Firewall causino errori.
    
 5. **Problemi di connettività tra Azure Migrate Appliance e Azure Key Vault:**

    Questo test verifica la presenza di problemi di connettività tra l'appliance Azure Migrate e il Azure Key Vault. Il Key Vault viene usato per gestire l'accesso all'account di archiviazione usato per la replica.
    
    **Passaggi per controllare la connettività:**
    
    1. Recuperare l'URI Key Vault dall'elenco di risorse nel gruppo di risorse corrispondente a Azure Migrate progetto.
    
    1. Aprire PowerShell nell'appliance Azure Migrate ed eseguire il comando seguente:
    
    _test-NetConnection Key Vault URI-P 443_
    
    Questo comando tenterà una connessione TCP e restituirà un output.
    
    1. Nell'output selezionare il campo "_TcpTestSucceeded_". Se il valore è "_true_", non esiste alcun problema di connettività tra l'appliance Azure migrate e il Azure Key Vault. Se il valore è "false", si verifica un problema di connettività.
    
    **Risoluzione:** Se il test ha esito negativo, si verifica un problema di connettività tra l'appliance Azure Migrate e il Azure Key Vault. Coinvolgere il team di rete locale per verificare i problemi di connettività. In genere, è possibile che alcune impostazioni del Firewall causino errori.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Si è verificato un errore durante il tentativo di recuperare i blocchi modificati

Messaggio di errore:' si è verificato un errore durante il tentativo di recuperare i blocchi di modifica '

Il metodo di replica senza agente usa il token di rilevamento dei blocchi modificato di VMware per replicare i dati in Azure. CBT consente allo strumento di migrazione del server di tenere traccia e replicare solo i blocchi modificati dopo l'ultimo ciclo di replica. Questo errore si verifica se il monitoraggio dei blocchi modificati per una macchina virtuale in fase di replica viene reimpostato o se il file di monitoraggio dei blocchi modificati è danneggiato.

Questo errore può essere risolto nei due modi seguenti:

- Se è stato scelto "Ripristina automaticamente la replica" selezionando "Sì" quando è stata attivata la replica della macchina virtuale, lo strumento tenterà di ripristinarlo automaticamente. Fare clic con il pulsante destro del mouse sulla macchina virtuale e selezionare "Ripristina replica".
- Se non si è optato per "Ripristina automaticamente replica" o se il passaggio precedente non funziona, arrestare la replica per la macchina virtuale, [reimpostare il rilevamento dei blocchi modificato](https://go.microsoft.com/fwlink/?linkid=2139203) nella macchina virtuale e quindi riconfigurare la replica.

Uno di questi problemi noti che può causare il ripristino di un CBT della macchina virtuale su VMware vSphere 5,5 è descritto in [VMware KB 2048201: il rilevamento dei blocchi modificati](https://go.microsoft.com/fwlink/?linkid=2138888) viene reimpostato dopo un'operazione di Storage vMotion in vSphere 5. x. In VMware vSphere 5.5 assicurarsi di applicare gli aggiornamenti descritti in questo articolo della Knowledge Base.

In alternativa, è possibile [reimpostare VMware Changed Block Tracking in una macchina virtuale con VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Si è verificato un errore interno

In alcuni casi è possibile che si verifichi un errore che si verifica a causa di problemi nell'ambiente/API VMware. È stato identificato il seguente set di errori come errori correlati all'ambiente VMware. Questi errori hanno un formato fisso.

_Messaggio di errore: si è verificato un errore interno. [Messaggio di errore]_

Esempio: messaggio di errore: si è verificato un errore interno. [È stata rilevata una configurazione di snapshot non valida].

Nella sezione seguente sono elencati alcuni degli errori più comuni di VMware e il modo in cui è possibile attenuarli.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Messaggio di errore: si è verificato un errore interno. [Connessione rifiutata dal server]

Il problema è un problema noto di VMware e si verifica in VDDK 6,7. È necessario arrestare il servizio gateway in esecuzione nell'appliance Azure Migrate, [scaricare un aggiornamento da VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)e riavviare il servizio gateway.

Passaggi per arrestare il servizio gateway:

1. Premere Windows + R, aprire Services. msc. Fare clic su "Microsoft Azure servizio gateway" e arrestarlo.
2. In alternativa, è possibile aprire il prompt dei comandi o PowerShell e: net stop asrgwy. Assicurarsi di attendere fino a quando non viene ricevuto il messaggio che indica che il servizio non è più in esecuzione.

Passaggi per avviare il servizio gateway:

1. Premere Windows + R, aprire Services. msc. Fare clic con il pulsante destro del mouse su "Microsoft Azure servizio gateway" e avviarlo.
2. In alternativa, è possibile aprire il prompt dei comandi o PowerShell e: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Messaggio di errore: si è verificato un errore interno. [' È stata rilevata una configurazione di snapshot non valida .']

Se si dispone di una macchina virtuale con più dischi, è possibile che si verifichi questo errore se si rimuove un disco dalla macchina virtuale. Per risolvere il problema, fare riferimento ai passaggi descritti in [questo articolo di VMware](https://go.microsoft.com/fwlink/?linkid=2138890).

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Messaggio di errore: si è verificato un errore interno. [Genera snapshot bloccato]

Questo problema si verifica quando la generazione dello snapshot smette di rispondere. Quando si verifica questo problema, è possibile vedere la pagina relativa all'interruzione dell'attività create snapshot al 95% o al 99%. Per risolvere il problema, fare riferimento a questa [KB di VMware](https://go.microsoft.com/fwlink/?linkid=2138969) .

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Messaggio di errore: si è verificato un errore interno. [Non è stato possibile consolidare i dischi nella macchina virtuale _[motivi]_]

Quando si consolidano i dischi alla fine del ciclo di replica, l'operazione ha esito negativo. Seguire le istruzioni in [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) selezionando il _motivo_ appropriato per risolvere il problema.

Si verificano gli errori seguenti quando le operazioni relative agli snapshot di VMware, ovvero creazione, eliminazione o consolidamento dei dischi, hanno esito negativo. Seguire le istruzioni nella sezione successiva per correggere gli errori:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Messaggio di errore: si è verificato un errore interno. [Un'altra attività è già in corso]

Questo problema si verifica in presenza di attività di macchine virtuali in conflitto in esecuzione in background o quando si verifica il timeout di un'attività all'interno del server vCenter. Seguire la risoluzione fornita nei seguenti [KB di VMware](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Messaggio di errore: si è verificato un errore interno. [Operazione non consentita nello stato corrente]

Questo problema si verifica quando server vCenter gli agenti di gestione smettono di funzionare. Per risolvere questo problema, fare riferimento alla soluzione nei seguenti [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971).

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Messaggio di errore: si è verificato un errore interno. [Dimensioni del disco snapshot non valide]

Si tratta di un problema noto di VMware in cui le dimensioni del disco indicate dallo snapshot diventeranno zero. Seguire la risoluzione fornita in [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138972).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Messaggio di errore: si è verificato un errore interno. [Allocazione di memoria non riuscita. Memoria insufficiente.]

Questo errore si verifica quando il buffer dell'host NFC ha esaurito la memoria. Per risolvere questo problema, è necessario spostare la VM (Compute vMotion) in un host diverso, che dispone di risorse gratuite.

## <a name="next-steps"></a>Passaggi successivi

Continuare la replica della macchina virtuale ed eseguire la [migrazione di test](https://go.microsoft.com/fwlink/?linkid=2139333).