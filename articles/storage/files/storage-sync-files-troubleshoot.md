---
title: Risolvere i problemi di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sulla risoluzione di problemi comuni di Sincronizzazione file di Azure.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 09/06/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 0f6075bcbaae14fc60df6f33f4e65cd4abcec731
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409463"
---
# <a name="troubleshoot-azure-file-sync"></a>Risolvere i problemi di Sincronizzazione file di Azure
Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

L'obiettivo di questo articolo è aiutare l'utente a individuare e risolvere i problemi che si possono incontrare con la distribuzione di Sincronizzazione file di Azure. Viene inoltre spiegato come raccogliere i log importanti dal sistema per effettuare un'analisi più approfondita dei problemi. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. [Forum di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Supporto tecnico Microsoft. Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida**, selezionare **Guida e supporto** e quindi selezionare **Nuova richiesta di supporto**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Si è verificato un problema di Sincronizzazione file di Azure nel server (sincronizzazione, suddivisione in livelli cloud e così via). È consigliabile rimuovere l'endpoint server e ricrearlo?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Installazione dell'agente e registrazione del server
<a id="agent-installation-failures"></a>**Risolvere gli errori di installazione dell'agente**  
Se l'installazione dell'agente Sincronizzazione file di Azure ha esito negativo, eseguire il comando seguente per abilitare la registrazione durante l'installazione dell'agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Esaminare il file installer.log per determinare la causa dell'errore di installazione.

<a id="agent-installation-on-DC"></a>**L'installazione dell'agente ha esito negativo nel controller di dominio di Active Directory**  
Se si tenta di installare l'agente di sincronizzazione in un controller di dominio di Active Directory in cui il proprietario di ruolo si trova in un Windows Server 2008 R2 o una versione precedente del sistema operativo, è possibile riscontrare il problema dell'esito negativo dell'installazione dell'agente.

Per risolverlo, trasferire il ruolo PDC in un altro controller di dominio su cui è in esecuzione Windows Server 2012 R2 o una versione più recente, quindi installare la sincronizzazione.

<a id="server-registration-missing"></a>**Il server non è elencato in Server registrati nel portale di Azure**  
Se un server non è presente nell'elenco **Server registrati** per un servizio di sincronizzazione archiviazione, seguire questa procedura:
1. Accedere al server da registrare.
2. Aprire Esplora File e quindi passare alla directory di installazione dell'agente di sincronizzazione archiviazione (il percorso predefinito è C:\Program Files\Azure\StorageSyncAgent). 
3. Eseguire il file ServerRegistration.exe e completare la registrazione guidata del server con un servizio di sincronizzazione archiviazione.

<a id="server-already-registered"></a>**Durante l'installazione dell'agente Sincronizzazione file di Azure, Registrazione server visualizza il messaggio seguente: "Il server è già registrato con un altro insieme di credenziali"** 

![Schermata della finestra di dialogo Registrazione server con messaggio di errore che indica che il server è già registrato](media/storage-sync-files-troubleshoot/server-registration-1.png)

Questo messaggio viene visualizzato se il server è stato registrato in precedenza con un servizio di sincronizzazione archiviazione. Per annullare la registrazione del server con il servizio di sincronizzazione archiviazione corrente e registrarlo con un nuovo servizio di sincronizzazione archiviazione, seguire la procedura descritta in [Annullare la registrazione del server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se il server non è presente nell'elenco **Server registrati** del servizio di sincronizzazione archiviazione, nel server di cui si vuole annullare la registrazione eseguire i comandi PowerShell seguenti:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se il server fa parte di un cluster, è possibile usare il parametro facoltativo *Reset-StorageSyncServer -CleanClusterRegistration* per rimuovere anche la registrazione del cluster.

<a id="web-site-not-trusted"></a>**Perché quando si registra un server si ricevono numerose risposte indicanti che il sito Web non è attendibile. Perché?**  
Questo errore si verifica perché durante la registrazione del server sono abilitati i criteri **Sicurezza avanzata di Internet Explorer**. Per altre informazioni su come disabilitare correttamente i criteri **Sicurezza avanzata di Internet Explorer**, vedere [Preparare Windows Server per l'uso con Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestione dei gruppi di sincronizzazione
<a id="cloud-endpoint-using-share"></a>**La creazione dell'endpoint cloud ha esito negativo e viene restituito il messaggio di errore: "La condivisione file di Azure specificata è già usata da un endpoint cloud diverso"**  
Questo errore si verifica se la condivisione file di Azure è già in uso da parte di un altro endpoint cloud. 

Se viene visualizzato questo messaggio e la condivisione file di Azure non è attualmente in uso da alcun endpoint cloud, attenersi ai passaggi seguenti per cancellare i metadati di Sincronizzazione file di Azure nella condivisone file di Azure:

> [!Warning]  
> L'eliminazione dei metadati nella condivisione file di Azure attualmente in uso da un endpoint cloud impedisce il corretto funzionamento di Sincronizzazione file di Azure. 

1. Nel portale di Azure passare alla condivisione file di Azure.  
2. Fare clic con il pulsante destro del mouse sulla condivisione file di Azure e scegliere **Modifica metadati**.
3. Fare clic con il pulsante destro del mouse su **SyncService** e quindi fare clic su **Elimina**.

<a id="cloud-endpoint-authfailed"></a>**La creazione dell'endpoint cloud ha esito negativo e viene restituito il messaggio di errore: "AuthorizationFailed"**  
Questo problema si verifica se l'account utente non ha diritti sufficienti per creare un endpoint cloud. 

Per creare un endpoint cloud, l'account utente deve avere le autorizzazioni Microsoft seguenti:  
* Lettura: Ottenere la definizione del ruolo
* Scrittura: Crea o aggiorna la definizione del ruolo personalizzata
* Lettura: Ottenere l'assegnazione del ruolo
* Scrittura: Crea assegnazione ruolo

I seguenti ruoli predefiniti dispongono delle necessarie autorizzazioni Microsoft:  
* Proprietario
* Amministratore accessi utente

Per determinare se il ruolo dell'account utente in uso dispone delle autorizzazioni necessarie:  
1. Nel portale di Azure fare clic su **Gruppi di risorse**.
2. Selezionare il gruppo di risorse in cui si trova l'account di archiviazione e quindi selezionare **Controllo di accesso (IAM)**.
3. Selezionare la scheda **Assegnazioni di ruolo**.
4. Selezionare il **Ruolo** per l'account utente, ad esempio proprietario o collaboratore.
5. Nell'elenco **Provider di risorse** selezionare **Autorizzazione Microsoft**. 
    * In **Assegnazione ruolo** devono essere impostate le autorizzazioni **Lettura** e **Scrittura**.
    * In **Definizione ruolo** devono essere impostate le autorizzazioni **Lettura** e **Scrittura**.

<a id="server-endpoint-createjobfailed"></a>**La creazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobFailed" (Codice errore: -2134375898)**  
Questo problema si verifica se il percorso dell'endpoint server si trova sul volume di sistema ed è abilitata la suddivisione in livelli nel cloud. La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.

<a id="server-endpoint-deletejobexpired"></a>**L'eliminazione dell'endpoint del server ha esito negativo e viene restituito il messaggio di errore: "MgmtServerJobExpired"**                
Questo problema si verifica se il server è offline o non si dispone di connettività di rete. Se il server non è più disponibile, annullare la registrazione del server nel portale. In questo modo, si annullano gli endpoint server. Per eliminare gli endpoint server, seguire la procedura descritta in [Annullare la registrazione del server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Impossibile aprire la pagina delle proprietà dell'endpoint server o aggiornare i criteri di suddivisione in livelli nel cloud**  
Questo problema può verificarsi se un'operazione di gestione nell'endpoint server ha esito negativo. Se la pagina delle proprietà dell'endpoint server non si apre nel portale di Azure, l'aggiornamento dell'endpoint server mediante i comandi di PowerShell dal server potrebbe risolvere il problema. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**L'endpoint server ha uno stato di integrità "Nessuna attività" o "In sospeso" e lo stato del server nel pannello dei server registrati è "Risulta offline"**  

Questo problema può verificarsi se il processo di monitoraggio della sincronizzazione dell'archiviazione non è in esecuzione o se il server non riesce a comunicare con il servizio Sincronizzazione file di Azure a causa di un proxy o un firewall.

Per risolvere il problema, eseguire la procedura seguente:

1. Aprire Gestione attività sul server e verificare che il processo di monitoraggio della sincronizzazione dell'archiviazione (AzureStorageSyncMonitor.exe) sia in esecuzione. Se il processo non è in esecuzione, provare a riavviare il server. Se il riavvio del server non risolve il problema, aggiornare all'ultima[versione dell'agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) della Sincronizzazione file di Azure.
2. Verificare che le impostazioni del proxy e del firewall siano configurate correttamente:
    - Se il server si trova dietro un firewall, verificare che la porta 443 in uscita sia consentita. Se il firewall limita il traffico a domini specifici, verificare che i domini elencati nella [documentazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) del firewall siano accessibili.
    - Se il server si trova dietro un proxy, configurare le impostazioni del proxy a livello di computer o specifiche dell'app seguendo la procedura descritta nella [documentazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy) del proxy.

<a id="endpoint-noactivity-sync"></a>**Lo stato di integrità dell'endpoint server è "Nessuna attività" e lo stato del server nel pannello dei server registrati è "Risulta offline"**  

Se lo stato di integrità dell'endpoint server è impostato su "Nessuna attività" significa che, nelle ultime due ore, nell'endpoint server non sono state registrate attività di sincronizzazione.

È possibile che in un endpoint server non vengano registrate attività di sincronizzazione per i motivi seguenti:

- Il server ha raggiunto il numero massimo di sessioni di sincronizzazione simultanee. Sincronizzazione file di Azure supporta attualmente due sessioni di sincronizzazione attive per ogni processore o fino a otto sessioni di sincronizzazione attive per ogni server.

- Il server ha una sessione di sincronizzazione VSS (SnapshotSync) attiva. Se in un endpoint server è attiva una sessione di sincronizzazione VSS, gli altri endpoint presenti nel server non possono avviare una sessione di sincronizzazione fino al completamento della sessione di sincronizzazione VSS.

Per verificare le attività di sincronizzazione attualmente in esecuzione su un server, vedere [Come monitorare lo stato di avanzamento di una sessione di sincronizzazione corrente?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

> [!Note]  
> Se lo stato del server nel pannello dei server registrati è "Risulta offline", eseguire i passaggi documentati nella sezione [L'endpoint server ha uno stato di integrità "Nessuna attività" o "In sospeso" e lo stato del server nel pannello dei server registrati è "Risulta offline"](#server-endpoint-noactivity).

## <a name="sync"></a>Sincronizzazione
<a id="afs-change-detection"></a>**Se è stato creato un file direttamente nella condivisione file di Azure su SMB o nel portale, quanto tempo richiede la sincronizzazione del file nei server nel gruppo di sincronizzazione?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**L'integrità dell'endpoint server è in sospeso per diverse ore**  
Questo errore è previsto se si crea un endpoint cloud e si usa una condivisione file di Azure contenente dati. Il processo di enumerazione di modifiche che esegue l'analisi per rilevare modifiche nella condivisione file di Azure deve essere completato prima che i file possano eseguire la sincronizzazione tra gli endpoint server e cloud. Il tempo necessario per il complemento del processo dipende dalle dimensioni dello spazio dei nomi nella condivisione file di Azure. L'integrità dell'endpoint server deve aggiornarsi dopo aver completato il processo di enumerazione di modifiche.

### <a id="broken-sync"></a>Come si monitorano le risorse di integrità?
# <a name="portaltabportal1"></a>[Portale](#tab/portal1)
All'interno di ogni gruppo di sincronizzazione è possibile risalire ai relativi endpoint server singoli per visualizzare lo stato delle ultime sessioni di sincronizzazione completate. Una colonna di integrità verde e un valore per i file che non eseguono la sincronizzazione pari a 0 indicano che la sincronizzazione funziona come previsto. Se ciò non avviene, vedere di seguito un elenco di errori di sincronizzazione comuni e come gestire i file che non eseguono la sincronizzazione. 

![Schermata del portale di Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Server](#tab/server)
Passare ai registri dei dati di telemetria del server che si trovano in Visualizzatore eventi in `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. L'evento 9102 corrisponde a una sessione di sincronizzazione completata; per lo stato più recente del servizio di sincronizzazione, cercare l'evento più recente con ID 9102. SyncDirection indica se la sessione rappresenta un caricamento o un download. Se il valore HResult è 0, la sessione di sincronizzazione è riuscita. Un valore HResult diverso da zero indica che si è verificato un errore durante la sincronizzazione; per un elenco di errori comuni, vedere di seguito. Se PerItemErrorCount è maggiore di 0, significa che alcuni file o cartelle non sono stati sincronizzati correttamente. È possibile ricevere un valore HResult pari a 0, ma al contempo un PerItemErrorCount maggiore di 0.

Di seguito, un esempio di caricamento corretto. Per ragioni di brevità, solo alcuni dei valori contenuti in ciascun evento 9102 sono elencati di seguito. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Al contrario, un'operazione di caricamento riuscita potrebbe essere simile alla seguente:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

In alcuni casi le sessioni di sincronizzazione hanno un esito completamente negativo o hanno un PerItemErrorCount diverso da zero, ma compiono ancora progressi, ovvero alcuni file eseguono correttamente la sincronizzazione. Si può notare nei campi Applied* (AppliedFileCount AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), che indicano quanti file della sessione vengono completati. Se vengono visualizzate più sessioni di sincronizzazione in una riga con esito negativo, ma con un numero crescente di Applied*, è necessario assegnare l'ora della sincronizzazione per tentare nuovamente l'operazione prima di aprire un ticket di supporto.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Come monitorare lo stato di avanzamento di una sessione di sincronizzazione corrente?
# <a name="portaltabportal1"></a>[Portale](#tab/portal1)
All'interno del gruppo di sincronizzazione, accedere all'endpoint server in questione ed esaminare la sezione delle attività di sincronizzazione per visualizzare il numero di file caricati o scaricati nella sessione di sincronizzazione corrente. Si noti che questo stato ritarderà di circa 5 minuti e, se la sessione di sincronizzazione è sufficientemente ridotta da essere completata entro questo periodo, potrebbe non essere segnalata nel portale. 

# <a name="servertabserver"></a>[Server](#tab/server)
Cercare gli eventi 9302 più recenti nei dati di telemetria del registro eventi sul server (nel Visualizzatore eventi, passare ad Registri applicazioni e servizi\Microsoft\FileSync\Agente\Telemetria). Questo evento indica lo stato della sessione di sincronizzazione corrente. TotalItemCount denota il numero di file che devono essere sincronizzati, AppliedItemCount il numero di file che sono stati sincronizzati finora e PerItemErrorCount il numero di file che hanno esito negativo per la sincronizzazione (vedere di seguito per la procedura risolvere questo problema).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Come si capisce se i server sono sincronizzati tra loro?
# <a name="portaltabportal1"></a>[Portale](#tab/portal1)
Per ogni server in un gruppo di sincronizzazione specificato, assicurarsi che:
- I timestamp dell'ultimo tentativo di sincronizzazione per il caricamento e il download siano recenti.
- Lo stato sia verde per il caricamento e per il download.
- Nel campo di attività di sincronizzazione venga visualizzato un numero molto ridotto o non esistano file rimanenti per la sincronizzazione.
- Il campo di file che non eseguono la sincronizzazione sia 0 per il caricamento e il download.

# <a name="servertabserver"></a>[Server](#tab/server)
Esaminare le sessioni di sincronizzazione completate che sono contrassegnate da eventi 9102 nel registro eventi di telemetria per ogni server (nel Visualizzatore eventi, passare a `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. In qualsiasi server specificato, si desidera assicurarsi che il caricamento più recente e le sessioni di download siano completati correttamente. A questo scopo, verificare che il valore HResult e PerItemErrorCount sia 0 per il caricamento e per il download (il campo SyncDirection indica se una sessione specifica è una sessione di caricamento o di download). Si noti che se non è possibile visualizzare una sessione di sincronizzazione completata di recente, è probabile che una sessione di sincronizzazione sia attualmente in corso, presumibilmente se è stata appena aggiunta o modificata una grande quantità di dati.
2. Quando un server è completamente aggiornato con il cloud e non include modifiche per la sincronizzazione in entrambe le direzioni, è possibile visualizzare le sessioni di sincronizzazione vuote. Queste sono indicate da eventi di caricamento e download in cui tutti i camp Sync* (SyncFileCount SyncDirCount, SyncTombstoneCount e SyncSizeBytes) sono zero, ovvero non erano presenti file da sincronizzare. Si noti che queste sessioni di sincronizzazione vuote potrebbero non verificarsi sui server di varianza elevata poiché vi sono sempre file nuovi da sincronizzare. Se è assente l'attività di sincronizzazione, le sessioni dovranno essere eseguite ogni 30 minuti. 
3. Se tutti i server sono aggiornati con il cloud, vale a dire le loro sessioni di caricamento e di download recenti sono sessioni di sincronizzazione vuote, è possibile affermare con ragionevole certezza che il sistema nel suo complesso è sincronizzato. 
    
Si noti che se sono state apportate modifiche direttamente nella condivisione file di Azure, Sincronizzazione file di Azure non rileverà questa modifica fino all'esecuzione dell'enumerazione di modifiche, che avviene una volta ogni 24 ore. È possibile che un server venga indicato come aggiornato con il cloud quando in realtà non contiene modifiche recenti apportate direttamente nella condivisione file di Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Come capire se sono presenti determinati file o cartelle che non eseguono la sincronizzazione?
Se il conteggio di PerItemErrorCount nel server o dei file che non eseguono la sincronizzazione nel portale è maggiore a 0 per qualsiasi sessione di sincronizzazione specificato, significa che non è possibile sincronizzare alcuni elementi. I file e le cartelle possono presentare caratteristiche che ne impediscono la sincronizzazione. Queste caratteristiche possono essere persistenti e richiedono un'azione esplicita per ripristinare la sincronizzazione, ad esempio la rimozione di caratteri non supportati dal nome del file o della cartella. Possono anche avere caratteristiche temporanee per cui la sincronizzazione del file o della cartella verrà ripristinata automaticamente, ad esempio file con handle aperti riprenderanno automaticamente la sincronizzazione alla chiusura dell'handle di file. Quando il motore di Sincronizzazione file di Azure rileva un problema di questo tipo, viene generato un log degli errori che può essere analizzato per elencare gli elementi attualmente non sincronizzati correttamente.

Per visualizzare questi errori, eseguire lo script **FileSyncErrorsReport.ps1** di PowerShell (che si trova nella directory di installazione dell'agente Sincronizzazione file di Azure) per identificare i file che non sono stati sincronizzati a causa di handle aperti, caratteri non supportati o altri problemi. Il campo ItemPath indica il percorso del file in relazione alla cartella per la sincronizzazione della radice. Vedere l'elenco degli errori di sincronizzazione comuni di seguito per la procedura di correzione.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Risoluzione dei problemi per gli errori di sincronizzazione di file e directory
**Log ItemResults - errori di sincronizzazione per ogni elemento**  
| HRESULT | HRESULT (decimale) | Stringa di errore | Problema | Correzione |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80065 | -2134376347 | ECS_E_DATA_TRANSFER_BLOCKED | Il file ha prodotto errori persistenti durante la sincronizzazione e pertanto tenterà di eseguire la sincronizzazione solo una volta al giorno. L'errore sottostante è rintracciabile in un registro eventi precedenti. | Negli agenti R2 (2.0) e versioni successive, viene presentato l'errore originale anziché quest'ultimo. Aggiornare l'agente più recente per visualizzare l'errore sottostante o esaminare i registri eventi precedenti per individuare la causa dell'errore originale. |
| 0x7b | 123 | ERROR_INVALID_NAME | Il nome della directory o dei file non è valido. | Rinominare il file o la directory in questione. Visualizzare le [Linee guida per la denominazione di File di Azure](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) e l'elenco dei caratteri non supportati qui di seguito. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | Il nome della directory o dei file non è valido. | Rinominare il file o la directory in questione. Visualizzare le [Linee guida per la denominazione di File di Azure](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) e l'elenco dei caratteri non supportati qui di seguito. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Un file è stato modificato, ma la modifica non è ancora stata rilevata dalla sincronizzazione. La sincronizzazione verrà ripristinata dopo il rilevamento di questa modifica. | Non è necessaria alcuna azione. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Impossibile sincronizzare un file poiché in uso. Il file verrà sincronizzato quando non sarà più in uso. | Non è necessaria alcuna azione. Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti. |
| 0x20 | 32 | ERROR_SHARING_VIOLATION | Impossibile sincronizzare un file poiché in uso. Il file verrà sincronizzato quando non sarà più in uso. | Non è necessaria alcuna azione. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Impossibile sincronizzare una modifica di file o directory al momento poiché una cartella dipendente non è ancora stata sincronizzata. Questo elemento verrà sincronizzato dopo la sincronizzazione delle modifiche dipendenti. | Non è necessaria alcuna azione. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Un file è stato modificato durante la sincronizzazione ed è quindi necessario sincronizzarlo di nuovo. | Non è necessaria alcuna azione. |

#### <a name="handling-unsupported-characters"></a>Gestione dei caratteri non supportati
Se lo script **FileSyncErrorsReport.ps1**di PowerShell mostra errori causati da caratteri non supportati (codici di errore 0x7b e 0x8007007b), è necessario rimuovere o rinominare i caratteri difettosi dai rispettivi nomi di file. PowerShell probabilmente stamperà i caratteri come punti interrogativi o rettangoli vuoti poiché la maggior parte di questi caratteri non ha alcuna codifica visiva standard. È possibile usare lo [strumento di valutazione](storage-sync-files-planning.md#evaluation-tool) per identificare i caratteri non supportati.

La tabella seguente contiene tutti i caratteri unicode che Sincronizzazione file di Azure non supporta ancora.

| Set di caratteri | Conteggio di caratteri |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operating system command)</li><li>0x00000090 (dcs device control string)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (high octet preset)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (Forme di presentazione arabo di tipo A) | 32 |
| 0x0000FFF0 - 0x0000FFFF (caso speciale) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (non carattere)</li><li>0x0002FFFE - 0x0002FFFF = 2 (non carattere)</li><li>0x0003FFFE - 0x0003FFFF = 2 (non carattere)</li><li>0x0004FFFE - 0x0004FFFF = 2 (non carattere)</li><li>0x0005FFFE - 0x0005FFFF = 2 (non carattere)</li><li>0x0006FFFE - 0x0006FFFF = 2 (non carattere)</li><li>0x0007FFFE - 0x0007FFFF = 2 (non carattere)</li><li>0x0008FFFE - 0x0008FFFF = 2 (non carattere)</li><li>0x0009FFFE - 0x0009FFFF = 2 (non carattere)</li><li>0x000AFFFE - 0x000AFFFF = 2 (non carattere)</li><li>0x000BFFFE - 0x000BFFFF = 2 (non carattere)</li><li>0x000CFFFE - 0x000CFFFF = 2 (non carattere)</li><li>0x000DFFFE - 0x000DFFFF = 2 (non carattere)</li><li>0x000EFFFE - 0x000EFFFF = 2 (non definito)</li><li>0x000FFFFE - 0x000FFFFF = 2 (area supplementare per uso privato)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Errori di sincronizzazione comuni
<a id="-2147023673"></a>**La sessione di sincronizzazione è stata annullata.**  
| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimale)** | -2147023673 | 
| **Stringa di errore** | ERROR_CANCELLED |
| **Rimedio necessario** | No  |

Le sessioni di sincronizzazione potrebbero non avvenire per vari motivi, tra cui il server che viene riavviato o aggiornato, gli snapshot VSS e così via. Anche se questo errore sembra richiedere un promemoria, è possibile ignorarlo a meno che non venga mantenuto per un periodo di diverse ore.

<a id="-2147012889"></a>**Non è stato possibile stabilire una connessione al servizio.**    
| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimale)** | -2147012889 | 
| **Stringa di errore** | WININET_E_NAME_NOT_RESOLVED |
| **Rimedio necessario** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Una richiesta dell'utente è stata limitata dal servizio.**  
| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimale)** | -2134376372 |
| **Stringa di errore** | ECS_E_USER_REQUEST_THROTTLED |
| **Rimedio necessario** | No  |

Non è necessaria alcuna azione, il server tenterà nuovamente. Se l'errore persiste per più di un paio d'ore, creare una richiesta di supporto.

<a id="-2134364065"></a>**La sincronizzazione non può accedere alla condivisione file di Azure specificata nell'endpoint cloud.**  
| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimale)** | -2134364065 |
| **Stringa di errore** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Rimedio necessario** | Yes |

Questo errore si verifica perché l'agente Sincronizzazione file di Azure non accede alla condivisione di file di Azure, possibilmente poiché la condivisione file di Azure o l'account di archiviazione che lo ospita non è più presente. È possibile risolvere questo errore eseguendo questa procedura:

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Verificare questa opzione per assicurarsi che l'account di archiviazione non contenga eventuali regole di rete.](#troubleshoot-network-rules)
3. [Garantire l'esistenza della condivisione file di Azure.](#troubleshoot-azure-file-share)
4. [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Non è stato possibile risolvere il nome dell'account di archiviazione usato.**  
| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimale)** | -2134364064 |
| **Stringa di errore** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Rimedio necessario** | Yes |

1. Verificare che sia possibile risolvere il nome DNS di archiviazione dal server.

    ```PowerShell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
3. [Verificare questa opzione per assicurarsi che l'account di archiviazione non contenga eventuali regole di rete.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**La sincronizzazione non è riuscita a causa di un problema con il database di sincronizzazione.**  
| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimale)** | -1906441138 |
| **Stringa di errore** | JET_errWriteConflict |
| **Rimedio necessario** | Yes |

Questo errore si verifica quando è presente un problema con il database interno usato da Sincronizzazione file di Azure. Quando si verifica questo problema, creare una richiesta di supporto per ricevere assistenza durante la risoluzione del problema.

<a id="-2134364053"></a>**La versione dell'agente di Sincronizzazione file di Azure installata nel server non è supportata.**  
| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimale)** | -2134364053 |
| **Stringa di errore** | ECS_E_AGENT_VERSION_BLOCKED |
| **Rimedio necessario** | Yes |

Questo errore si verifica se la versione dell'agente di Sincronizzazione file di Azure installata nel server non è supportata. Per risolvere questo problema, eseguire l'[aggiornamento]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) a una [versione dell'agente supportata]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**È stato raggiunto il limite di archiviazione per le condivisioni file di Azure.**  
| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimale)** | -2134351810 |
| **Stringa di errore** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Rimedio necessario** | Yes |

Questo errore si verifica quando il limite di archiviazione di condivisione file di Azure è stato raggiunto, il che può succedere se viene applicata una quota per una condivisione file di Azure o se l'utilizzo supera i limiti per una condivisione file di Azure. Per altre informazioni, vedere i [limiti attuali per una condivisione file di Azure](storage-files-scale-targets.md).

1. Passare al gruppo di sincronizzazione all'interno del servizio di sincronizzazione archiviazione.
2. Selezionare l'endpoint cloud all'interno del gruppo di sincronizzazione.
3. Si noti il nome della condivisione file di Azure nel riquadro aperto.
4. Selezionare l'account di archiviazione collegato. Se questo collegamento ha esito negativo, l'account di archiviazione a cui viene fatto riferimento è stato rimosso.

    ![Schermata che mostra il riquadro dei dettagli dell'endpoint cloud con un collegamento all'account di archiviazione.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selezionare **File** per visualizzare l'elenco delle condivisioni file.
6. Fare clic sui tre puntini alla fine della riga per la condivisione file di Azure a cui fa riferimento l'endpoint cloud.
7. Verificare che l'**Utilizzo** si trovi sotto a **Quota**. Nota: a meno che non sia stata specificata una quota alternativa, la quota corrisponderà alle [dimensioni massime della condivisione file di Azure](storage-files-scale-targets.md).

    ![Schermata delle proprietà della condivisione file di Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se la condivisione è piena e non è impostata una quota, uno dei possibili metodi per correggere il problema è inserire ogni sottocartella dell'endpoint server corrente nel relativo endpoint server all'interno degli specifici gruppi di sincronizzazione. In questo modo ogni sottocartella verrà sincronizzata con le singole condivisioni di file di Azure.

<a id="-2134351824"></a>**Impossibile trovare la condivisione file di Azure.**  
| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimale)** | -2134351824 |
| **Stringa di errore** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Rimedio necessario** | Yes |

Questo errore si verifica quando la condivisione file di Azure non è accessibile. Per risolvere i problemi:

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Garantire l'esistenza della condivisione file di Azure.](#troubleshoot-azure-file-share)

Se la condivisione file di Azure è stata eliminata, creare una nuova condivisione file e quindi ricreare il gruppo di sincronizzazione. 

<a id="-2134364042"></a>**La sincronizzazione è in pausa durante la sospensione della sottoscrizione di Azure.**  
| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimale)** | -2134364042 |
| **Stringa di errore** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Rimedio necessario** | Yes |

Questo errore si verifica quando viene sospesa la sottoscrizione di Azure. La sincronizzazione verrà riabilitata quando verrà ripristinata la sottoscrizione di Azure. Per altre informazioni, vedere [Perché la sottoscrizione di Azure è disabilitata e cosa occorre fare per riattivarla?](../../billing/billing-subscription-become-disable.md)

<a id="-2134364052"></a>**Per l'account di archiviazione sono configurati un firewall o reti virtuali.**  
| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimale)** | -2134364052 |
| **Stringa di errore** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Rimedio necessario** | Yes |

Questo errore si verifica quando la condivisione file di Azure è inaccessibile a causa di un firewall di account di archiviazione o perché l'account di archiviazione appartiene a una rete virtuale. Sincronizzazione file di Azure non dispone ancora di supporto a questa funzionalità. Per risolvere i problemi:

1. [Verificare l'esistenza dell'account di archiviazione.](#troubleshoot-storage-account)
2. [Verificare questa opzione per assicurarsi che l'account di archiviazione non contenga eventuali regole di rete.](#troubleshoot-network-rules)

Rimuovere le regole per risolvere questo problema. 

<a id="-2134375911"></a>**La sincronizzazione non è riuscita a causa di un problema con il database di sincronizzazione.**  
| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimale)** | -2134375911 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Rimedio necessario** | No  |

Questo errore in genere si risolve automaticamente. Può verificarsi in presenza di:

* Un numero elevato di modifiche ai file tra i server nel gruppo di sincronizzazione.
* Un numero elevato di errori in singoli file e directory.

Se il problema persiste per più di due ore, creare una richiesta di supporto per ricevere assistenza durante la risoluzione del problema.

<a id="-2146762487"></a>**Il server non è riuscito a stabilire una connessione sicura. Il servizio cloud ha ricevuto un certificato imprevisto.**  
| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimale)** | -2146762487 |
| **Stringa di errore** | CERT_E_UNTRUSTEDROOT |
| **Rimedio necessario** | Yes |

Questo errore può verificarsi se l'organizzazione usa un proxy di terminazione SSL o se un'entità dannosa intercetta il traffico tra il server e il servizio Sincronizzazione file di Azure. Se si è certi che questo comportamento sia previsto (perché l'organizzazione usa un proxy di terminazione SSL ), ignorare la verifica dei certificati con un override del registro di sistema.

1. Creare il valore del registro SkipVerifyingPinnedRootCertificate.

    ```PowerShell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Riavviare il servizio di sincronizzazione nel server registrato.

    ```PowerShell
    Restart-Service -Name FileSyncSvc -Force
    ```

Impostando questo valore del registro, l'agente di Sincronizzazione file di Azure accetterà qualsiasi certificato SSL attendibile locale durante il trasferimento dei dati tra il server e il servizio cloud.

<a id="-2147012894"></a>**Non è stato possibile stabilire una connessione al servizio.**  
| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimale)** | -2147012894 |
| **Stringa di errore** | WININET_E_TIMEOUT |
| **Rimedio necessario** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**La sincronizzazione non è riuscita a causa di un problema di autenticazione.**  
| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimale)** | -2134375680 |
| **Stringa di errore** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Rimedio necessario** | Yes |

In genere questo errore si verifica perché l'ora del server non è corretta oppure il certificato usato per l'autenticazione è scaduto. Se l'ora del server è corretta, seguire la procedura seguente per rinnovare il certificato scaduto:

1. Aprire lo snap-in MMC dei certificati, selezionare l'account del computer e passare a certificati (Computer locale)\Personale\Certificati.
2. Controllare se il certificato di autenticazione del client è scaduto. Se il certificato è scaduto, chiudere lo snap-in di MMC Certificati e quindi procedere con i passaggi rimanenti. 
3. Verificare che sia installata la versione 4.0.1.0 o versione successiva dell'agente Sincronizzazione file di Azure.
4. Eseguire i comandi di PowerShell seguenti sul server:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
    Reset-AzureRmStorageSyncServerCertificate -SubscriptionId <guid> -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Lo spazio su disco del volume in cui risiede l'endpoint server è insufficiente.**  
| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimale)** | -1906441711 |
| **Stringa di errore** | JET_errLogDiskFull |
| **Rimedio necessario** | Yes |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimale)** | -2134375654 |
| **Stringa di errore** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Rimedio necessario** | Yes |

Questo errore si verifica perché il volume è esaurito. Questo errore si verifica generalmente perché i file all'esterno di endpoint del server usano spazio nel volume. Liberare spazio nel volume aggiungendo altri endpoint server, spostando i file in un altro volume o aumentando le dimensioni del volume in cui l'endpoint server è attivo.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Il servizio non è ancora pronto per la sincronizzazione con questo endpoint server.**  
| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimale)** | -2134364145 |
| **Stringa di errore** | ECS_E_REPLICA_NOT_READY |
| **Rimedio necessario** | No  |

Questo errore si verifica perché sono state apportate modifiche nella condivisione di file di Azure direttamente e il rilevamento delle modifiche è in corso. La sincronizzazione verrà avviata al termine del rilevamento delle modifiche.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Sincronizzazione non riuscita a causa di problemi con svariati file singoli.**  
| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimale)** | -2134364145 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Rimedio necessario** | Yes |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimale)** | -2134375908 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Rimedio necessario** | Yes |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimale)** | -2134375853 |
| **Stringa di errore** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Rimedio necessario** | Yes |

Nei casi in cui sono presenti numerosi errori di sincronizzazione file, le sessioni di sincronizzazione potrebbero presentare errori. Per risolvere questo stato, vedere [Risoluzione dei problemi per gli errori di sincronizzazione di file e directory](#troubleshooting-per-file-directory-sync-errors).

> [!NOTE]
> Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti.

<a id="-2134376423"></a>**La sincronizzazione non è riuscita a causa di un problema con il percorso dell'endpoint server.**  
| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimale)** | -2134376423 |
| **Stringa di errore** | ECS_E_SYNC_INVALID_PATH |
| **Rimedio necessario** | Yes |

Assicurarsi che il percorso esista, che si trovi in un volume NTFS locale e che non sia un reparse point o un endpoint server esistente.

<a id="-2134376373"></a>**Il servizio non è attualmente disponibile.**  
| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimale)** | -2134376373 |
| **Stringa di errore** | ECS_E_SERVICE_UNAVAILABLE |
| **Rimedio necessario** | No  |

Questo errore si verifica perché il servizio di Sincronizzazione file di Azure è disponibile. Questo errore si risolve automaticamente quando il servizio Sincronizzazione file di Azure torna disponibile.

<a id="-2134375922"></a>**La sincronizzazione non è riuscita a causa di un problema temporaneo con il database di sincronizzazione.**  
| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimale)** | -2134375922 |
| **Stringa di errore** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Rimedio necessario** | No  |

Questo errore si verifica a causa di un problema interno con il database di sincronizzazione. Questo errore si risolve automaticamente quando Sincronizzazione file di Azure ripete l'operazione. Se il problema persiste per un periodo di tempo prolungato, creare una richiesta di supporto per ricevere assistenza durante la risoluzione del problema.

### <a name="common-troubleshooting-steps"></a>Normale procedura di risoluzione dei problemi
<a id="troubleshoot-storage-account"></a>**Verificare l'esistenza dell'account di archiviazione.**  
# <a name="portaltabportal"></a>[Portale](#tab/portal)
1. Passare al gruppo di sincronizzazione all'interno del servizio di sincronizzazione archiviazione.
2. Selezionare l'endpoint cloud all'interno del gruppo di sincronizzazione.
3. Si noti il nome della condivisione file di Azure nel riquadro aperto.
4. Selezionare l'account di archiviazione collegato. Se questo collegamento ha esito negativo, l'account di archiviazione a cui viene fatto riferimento è stato rimosso.
    ![Schermata che mostra il riquadro dei dettagli dell'endpoint cloud con un collegamento all'account di archiviazione.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzureRmAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzureRmLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzureRmStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzureRmStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzureRmStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**Verificare questa opzione per assicurarsi che l'account di archiviazione non contenga eventuali regole di rete.**  
# <a name="portaltabportal"></a>[Portale](#tab/portal)
1. Una volta nell'account di archiviazione, selezionare **Firewall e reti virtuali** sul lato sinistro dell'account di archiviazione.
2. All'interno dell'account di archiviazione, il pulsante di opzione **Consenti l'accesso da tutte le reti** deve essere selezionato.
    ![Schermata che illustra le regole account di archiviazione firewall e della rete disabilitate.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Garantire l'esistenza della condivisione file di Azure.**  
# <a name="portaltabportal"></a>[Portale](#tab/portal)
1. Fare clic su **Panoramica** nel sommario a sinistra per tornare alla pagina dell'account di archiviazione principale.
2. Selezionare **File** per visualizzare l'elenco delle condivisioni file.
3. Verificare che la condivisione file a cui fa riferimento l'endpoint cloud venga visualizzata nell'elenco delle condivisioni di file (come si può vedere dal passaggio 1 in alto).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione.**  
# <a name="portaltabportal"></a>[Portale](#tab/portal)
1. Fare clic su **Controllo di accesso (IAM)** nel sommario a sinistra.
1. Fare clic sulla scheda **Assegnazioni del ruolo** per passare all'elenco di utenti e applicazioni (*entità servizio*) che possono accedere all'account di archiviazione.
1. Verificare che **servizio di Sincronizzazione file di ibrido** venga visualizzato nell'elenco con il ruolo di **Lettore e accesso ai dati**. 

    ![Schermata dell'entità servizio del servizio Sincronizzazione file ibrida nella scheda di controllo di accesso dell'account di archiviazione](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se il **servizio Sincronizzazione file ibrida** non è visualizzato nell'elenco, procedere come segue:

    - Fare clic su **Aggiungi**.
    - Nel campo **Ruolo** selezionare **Lettore e accesso ai dati**.
    - Nel campo **Seleziona** digitare **Servizio Sincronizzazione file ibrida**, selezionare il ruolo e fare clic su **Salva**.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell    
$foundSyncPrincipal = $false
Get-AzureRmRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Come impedire agli utenti di creare i file contenenti caratteri non supportati nel server?
È possibile usare [screening dei file di Gestione risorse File server](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) per impedire ai file con caratteri non supportati nei nomi di essere creati nel server. È possibile eseguire questa operazione tramite PowerShell, poiché la maggior parte dei caratteri non supportati non è stampabile e pertanto è necessario prima di tutto eseguire il cast nelle rispettive rappresentazioni esadecimali come caratteri.

Prima di tutto creare un gruppo di file di Gestione risorse file server usando il [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Questo esempio definisce il gruppo affinché contenga solo due dei caratteri non supportati, ma è possibile includere il numero dei caratteri in base alle esigenze nel gruppo di file.

```PowerShell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Dopo aver definito un gruppo di file di Gestione risorse file server, è possibile creare uno screening dei file di Gestione risorse file server usando il cmdlet New-FsrmFileScreen.

```PowerShell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Si noti che gli screening dei file devono essere usati solo per bloccare la creazione di caratteri non supportati da Sincronizzazione file di Azure. Se gli screening dei file vengono usati in altri scenari, la sincronizzazione tenterà continuamente di scaricare i file dalla condivisione file di Azure nel server e verrà bloccata a causa dello screening dei file, comportando un elevato traffico in uscita dei dati. 

## <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud 
Nell'archiviazione a livelli nel cloud possono prendere forma due percorsi di errore:

- È possibile che i file non possano essere archiviati a livelli, ovvero che Sincronizzazione file di Azure non riesca ad archiviare a livelli i file in File di Azure.
- È possibile che i file non possano essere richiamati, ovvero che il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) non riesca a scaricare i dati quando un utente tenta di accedere a un file archiviato a livelli.

Dai questi due percorsi di errore possono essere generate due principali classi di errore:

- Errori di archiviazione cloud
    - *Problemi di disponibilità del servizio di archiviazione temporanea*. Per altre informazioni, vedere [Contratto di servizio per Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Condivisione file di Azure non accessibile*. Questo errore si verifica in genere quando si elimina una condivisione file di Azure che si trova ancora in un endpoint cloud di un gruppo di sincronizzazione.
    - *Account di archiviazione inaccessibile*. Questo errore si verifica in genere quando si elimina un account di archiviazione che ha ancora una condivisione file di Azure costituita da un endpoint cloud di un gruppo di sincronizzazione. 
- Errori del server 
    - *Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) non è caricato*. Per rispondere alle richieste di archiviazione a livelli/richiamo, è necessario che il filtro del file system di Sincronizzazione file di Azure sia caricato. È possibile che un filtro non risulti caricato per vari motivi, ma la causa più comune è che un amministratore lo abbia scaricato manualmente. Il filtro del file system di Sincronizzazione file di Azure deve risultare sempre caricato per garantire il corretto funzionamento di Sincronizzazione file di Azure.
    - *Reparse point mancante, danneggiato o non funzionante*. Un reparse point è una speciale struttura di dati in un file costituita da due parti:
        1. Un tag di reparse, che indica al sistema operativo che per eseguire alcune operazioni di I/O sul file è possibile che sia necessario il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). 
        2. I dati di reparse, che indicano al filtro del file system l'URI del file nell'endpoint cloud associato (la condivisione file di Azure). 
        
        Nella maggior parte dei casi, un reparse point viene danneggiato quando un amministratore tenta di modificarne il tag o i dati. 
    - *Problemi di connettività di rete*. Per archiviare a livelli o richiamare un file, il server deve avere la connettività Internet.

Le sezioni seguenti indicano come risolvere problemi di archiviazione a livelli nel cloud e determinare se si tratta di un problema di archiviazione cloud o di un problema del server.

<a id="monitor-tiering-activity"></a>**Come monitorare l'attività di suddivisione in livelli in un server**  
Per monitorare l'attività di suddivisione in livelli in un server, usare l'evento ID 9002, 9003, 9016 e 9029 nel registro eventi di telemetria (che si trova in Registri applicazioni e servizi\Microsoft\FileSync\Agente in Visualizzatore eventi).

- L'ID evento 9002 fornisce inoltre statistiche di ghosting per un endpoint server. Ad esempio, TotalGhostedFileCount, SpaceReclaimedMB e così via.

- L'ID evento 9003 fornisce inoltre la distribuzione di un errore per un endpoint server. Ad esempio, Total Error Count, ErrorCode e così via. Si noti che per ogni codice di errore viene registrato un evento.

- L'ID evento 9016 fornisce inoltre risultati di ghosting per un volume. Ad esempio, percentuale di spazio disponibile, numero di file fantasma nella sessione, numero di file in cui non è stato possibile eseguire il ghosting e così via.

- L'ID evento 9029 fornisce informazioni sulla sessione di ghosting. Ad esempio, numero di file che hanno eseguito un tentativo nella sessione, numero di file archiviati a livelli nella sessione, numero di file già archiviati a livelli e così via.

<a id="monitor-recall-activity"></a>**Come monitorare l'attività di richiamo in livelli in un server**  
Per monitorare l'attività di richiamo in un server, usare l'evento ID 9005, 9006 e 9007 nel registro eventi di telemetria (che si trova in Registri applicazioni e servizi\Microsoft\FileSync\Agente in Visualizzatore eventi). Si noti che questi eventi vengono registrati ogni ora.

- L'ID evento 9005 offre affidabilità di richiamo per un endpoint server. Ad esempio, numero totale di file univoci a cui si ha avuto accesso, numero totale di file univoci con accessi non riusciti e così via.

- L'ID evento 9006 fornisce inoltre la distribuzione di un errore di richiamo per un endpoint server. Ad esempio, Totale richieste non riuscite, ErrorCode e così via. Si noti che per ogni codice di errore viene registrato un evento.

- L'ID evento 9007 fornisce inoltre prestazioni di richiamo per un endpoint server. Ad esempio, TotalRecallIOSize, TotalRecallTimeTaken e così via.

<a id="files-fail-tiering"></a>**Risolvere i problemi dei file che non è possibile archiviare a livelli**  
Se non è possibile archiviare a livelli i file in File di Azure:

1. Nel Visualizzatore eventi esaminare i registri i dati di telemetria e gli eventi operativi e diagnostici, i quali si trovano in Registri applicazioni e servizi\Microsoft\FileSync\Agente. 
    1. Verificare che i file esistano nella condivisione di file di Azure.

    > [!NOTE]
    > È necessario che un file venga sincronizzato con una condivisione di file di Azure per poter essere archiviato a livelli.

    2. Verificare che il server disponga della connettività Internet. 
    3. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
        - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

> [!NOTE]
> L'ID evento 9003 viene registrato dopo un'ora nel registro eventi di telemetria, se un file non riesce a eseguire la suddivisione in livelli (per ogni codice di errore viene registrato un evento). I registri di eventi operativi e diagnostici devono essere usati se sono necessarie altre informazioni per diagnosticare un problema.

<a id="files-fail-recall"></a>**Risolvere i problemi di file che non è possibile richiamare**  
Se il richiamo di file ha esito negativo:
1. Nel Visualizzatore eventi esaminare i registri i dati di telemetria e gli eventi operativi e diagnostici, i quali si trovano in Registri applicazioni e servizi\Microsoft\FileSync\Agente.
    1. Verificare che i file esistano nella condivisione di file di Azure.
    2. Verificare che il server disponga della connettività Internet. 
    3. Aprire lo snap-in Servizi di MMC e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione.
    4. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
        - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

> [!NOTE]
> L'ID evento 9006 viene registrato una volta all'ora nel registro eventi di telemetria se un file non riesce a eseguire il richiamo (per ogni codice di errore viene registrato un evento). I registri di eventi operativi e diagnostici devono essere usati se sono necessarie altre informazioni per diagnosticare un problema.

<a id="files-unexpectedly-recalled"></a>**Risolvere i problemi dei file richiamati in modo imprevisto in un server**  
Antivirus, backup e altre applicazioni che leggono quantità elevate di file causano richiami indesiderati a meno che non rispettino l'attributo per ignorare i file offline e ignorare la lettura del contenuto di tali file. Se si ignorano i file offline per i prodotti che supportano questa opzione, è possibile evitare richiami imprevisti durante operazioni come le analisi antivirus o i processi di backup.

Per informazioni su come configurare la soluzione in modo che non legga i file offline, rivolgersi al produttore del software.

I richiami imprevisti possono verificarsi anche in altri scenari, ad esempio quando si visualizzano file in Esplora File. Se si apre una cartella contenente file archiviati a livelli nel cloud in Esplora File nel server, è possibile che si verifichino richiami non previsti. Questa situazione si verifica con ancora maggiore probabilità se nel server è abilitata una soluzione antivirus.

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali
Se si verificano problemi con Sincronizzazione file di Azure in un server, per prima cosa eseguire questi passaggi:
1. Nel Visualizzatore eventi esaminare i dati di telemetria e i registri eventi operativi e diagnostici.
    - I problemi di sincronizzazione, archiviazione a livelli e richiamo vengono registrati nei dati di telemetria nella sezione dei registri eventi operativi e diagnostici in Registri applicazioni e servizi\Microsoft\FileSync\Agent.
    - I problemi correlati alla gestione di un server (ad esempio le impostazioni di configurazione) vengono registrati nei registri eventi operativi e diagnostici in Applications e Services\Microsoft\FileSync\Management.
2. Verificare che il servizio Sincronizzazione file di Azure sia in esecuzione nel server:
    - Aprire lo snap-in di MMC di Servizi e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione.
3. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
    - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

Se il problema persiste, eseguire lo strumento AFSDiag:
1. Creare una directory in cui verranno salvati i risultati di AFSDiag, ad esempio C:\Output.
2. Aprire una finestra di PowerShell con privilegi elevati ed eseguire i comandi seguenti, premendo INVIO dopo ogni comando:

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Per il livello di traccia nella modalità kernel di Sincronizzazione file di Azure, immettere **1** (salvo diversamente specificato, per creare tracce più dettagliate) e quindi premere INVIO.
4. Per il livello di traccia nella modalità utente di Sincronizzazione file di Azure, immettere **1** (salvo diversamente specificato, per creare tracce più dettagliate) e quindi premere INVIO.
5. Riprodurre il problema. Al termine immettere **D**.
6. Nella directory di output specificata verrà salvato un file con estensione zip contenente i registri e i file di traccia.

## <a name="see-also"></a>Vedere anche 
- [Domande frequenti su File di Azure](storage-files-faq.md)
- [Risolvere i problemi di File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Risolvere i problemi di File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
