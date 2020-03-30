---
title: Eseguire il backup di macchine virtuali Hyper-V con MABS
description: Questo articolo contiene le procedure per il backup e il ripristino delle macchine virtuali tramite il server di backup di Microsoft Azure (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255055"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Eseguire il backup di macchine virtuali Hyper-V con il server di backup di AzureBack up Hyper-V virtual machines with Azure Backup Server

In questo articolo viene illustrato come eseguire il backup di macchine virtuali Hyper-V utilizzando Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Scenari supportati

MABS può eseguire il backup delle macchine virtuali in esecuzione nei server host Hyper-V nei seguenti scenari:

- **Macchine virtuali con archiviazione locale o diretta**: backup di macchine virtuali ospitate in server host autonomi Hyper-V che dispongono di archiviazione locale o collegata direttamente, Ad esempio: un disco rigido, un dispositivo SAN (Storage Area Network) o un dispositivo NAS (Network Attached Storage). L'agente protezione MABS deve essere installato in tutti gli host.

- **Macchine virtuali in cluster con volumi condivisi del cluster**: backup di macchine virtuali ospitate in un cluster Hyper-V con volumi condivisi del cluster (CSV). L'agente protezione MABS viene installato in ogni nodo del cluster.

## <a name="host-versus-guest-backup"></a>Backup tra host e ospite

MABS può eseguire un backup a livello di host o guest delle macchine virtuali Hyper-V. A livello di host, l'agente protezione MABS è installato nel server host Hyper-V o nel cluster e protegge tutte le macchine virtuali e i file di dati in esecuzione su tale host.   A livello guest, l'agente viene installato in ogni macchina virtuale e protegge il carico di lavoro presente in tale macchina.

Entrambi i metodi presentano vantaggi e svantaggi:

- I backup a livello di host sono flessibili perché funzionano indipendentemente dal tipo di sistema operativo in esecuzione nei computer guest e non richiedono l'installazione dell'agente protezione MABS in ogni macchina virtuale. Se si distribuisce il backup a livello di host, è possibile ripristinare un'intera macchina virtuale o file e cartelle (ripristino a livello di elemento).

- Il backup a livello di guest è utile se si desidera proteggere carichi di lavoro specifici in esecuzione in una macchina virtuale. A livello di host è possibile ripristinare un'intera macchina virtuale o file specifici, ma non fornirà il ripristino nel contesto di un'applicazione specifica. Ad esempio, per ripristinare elementi di SharePoint specifici da una macchina virtuale di cui è stato eseguito il backup, è consigliabile eseguire il backup a livello di guest di tale macchina virtuale. Utilizzare il backup a livello di guest se si desidera proteggere i dati archiviati su dischi pass-through. Passthrough consente alla macchina virtuale di accedere direttamente al dispositivo di archiviazione e non archivia i dati del volume virtuale in un file VHD.

## <a name="how-the-backup-process-works"></a>Funzionamento del processo di backup

MABS esegue il backup con VSS come indicato di seguito. I passaggi descritti in questa descrizione sono numerati per facilitarne la comprensione.

1. Il motore di sincronizzazione basato su blocchi MABS crea una copia iniziale della macchina virtuale protetta e garantisce che la copia della macchina virtuale sia completa e coerente.

2. Dopo la copia iniziale è stata eseguita e verificata, MABS utilizza il writer VSS Hyper-V per acquisire i backup. Il writer VSS fornisce un set coerente di dati di blocchi del disco che vengono sincronizzati con il server MABS. Questo approccio offre il vantaggio di un "backup completo" con il server MABS, riducendo al minimo i dati di backup che devono essere trasferiti attraverso la rete.

3. L'agente protezione MABS in un server che esegue Hyper-V utilizza le API di Hyper-V esistenti per determinare se una macchina virtuale protetta supporta anche VSS.

   - Se una macchina virtuale soddisfa i requisiti per il backup online e in essa è installato il componente dei servizi di integrazione Hyper-V, il VSS writer di Hyper-V inoltra in modo ricorsivo la richiesta VSS attraverso tutti i processi che riconoscono VSS sulla macchina virtuale. Questa operazione si verifica senza l'agente protezione MABS installato nella macchina virtuale. La richiesta di VSS ricorsiva consente al writer del servizio Copia Shadow del volume di Hyper-V di garantire che le operazioni di scrittura su disco siano sincronizzate affinché venga acquisito uno snapshot VSS senza perdita di dati.

     Il componente dei servizi di integrazione Hyper-V richiama il writer VSS di Hyper-V in Servizio Copia Shadow del volume (VSS) sulle macchine virtuali per garantire che i relativi dati di applicazione siano in uno stato coerente.

   - Se la macchina virtuale non è conforme ai requisiti di backup online, MABS utilizza automaticamente le API hyper-V per sospendere la macchina virtuale prima che acquisiscano i file di dati.

4. Dopo la copia di base iniziale della macchina virtuale sincronizzata con il server MABS, tutte le modifiche apportate alle risorse della macchina virtuale vengono acquisite in un nuovo punto di ripristino. Il punto di ripristino rappresenta lo stato coerente della macchina virtuale in un momento specifico. Le acquisizioni del punto di ripristino possono verificarsi almeno una volta al giorno. Quando viene creato un nuovo punto di ripristino, MABS utilizza la replica a livello di blocco insieme al writer VSS Hyper-V per determinare quali blocchi sono stati modificati nel server che esegue Hyper-V dopo la creazione dell'ultimo punto di ripristino. Questi blocchi di dati vengono quindi trasferiti al server MABS e applicati alla replica dei dati protetti.

5. Il server MABS utilizza VSS sui volumi che ospitano i dati di ripristino in modo che siano disponibili più copie shadow. Ciascuna di queste copie shadow fornisce un ripristino separato. I punti di ripristino VSS vengono archiviati nel server MABS. La copia temporanea eseguita nel server che esegue Hyper-V viene archiviata solo per la durata della sincronizzazione MABS.

>[!NOTE]
>
>A partire da Windows Server 2016, i dischi rigidi virtuali Hyper-V dispongono di rilevamento delle modifiche incorporato noto come rilevamento delle modifiche resilienti (RCT). MABS utilizza RCT (il rilevamento delle modifiche nativo in Hyper-V), che riduce la necessità di controlli di coerenza che richiedono molto tempo in scenari come gli arresti anomali delle macchine virtuali. RCT offre una migliore resilienza rispetto al rilevamento delle modifiche offerto dai backup basati su snapshot di VSS. MABS V3 ottimizza ulteriormente il consumo di archiviazione e rete trasferendo solo i dati modificati durante le verifiche della coerenza.

## <a name="backup-prerequisites"></a>Prerequisiti per il backup

Di seguito sono riportati i prerequisiti per il backup di macchine virtuali Hyper-V con MABS:

|Prerequisito|Dettagli|
|------------|-------|
|Prerequisiti MABS|- Se si desidera eseguire il ripristino a livello di elemento per le macchine virtuali (ripristino di file, cartelle, volumi), sarà necessario installare il ruolo Hyper-V nel server MABS.  Se si desidera ripristinare solo la macchina virtuale e non a livello di elemento, il ruolo non è necessario.<br />- È possibile proteggere fino a 800 macchine virtuali di 100 GB ciascuna su un server MABS e consentire più server MABS che supportano cluster di dimensioni maggiori.<br />- MABS esclude il file di paging dai backup incrementali per migliorare le prestazioni di backup della macchina virtuale.- MABS excludes the page file from incremental backups to improve virtual machine backup performance.<br />- MABS può eseguire il backup di un server Hyper-V o di un cluster nello stesso dominio del server MABS o in un dominio figlio o trusted. Se si desidera eseguire il backup di Hyper-V in un gruppo di lavoro o in un dominio non attendibile, è necessario configurare l'autenticazione. Per un singolo server Hyper-V, è possibile utilizzare NTLM o l'autenticazione del certificato. Per un cluster, è possibile usare solo l'autenticazione del certificato.<br />-   Non è supportato l'uso del backup a livello di host per eseguire il backup dei dati della macchina virtuale su dischi pass-through. In questo scenario, è consigliabile utilizzare il backup a livello di host per eseguire il backup dei file VHD e del backup a livello di guest per eseguire il backup degli altri dati non visibili nell'host.<br />   -È possibile eseguire il backup delle macchine virtuali archiviate in volumi deduplicati.-You can back up VMs stored on deduplicated volumes.|
|Prerequisiti delle macchine virtuali Hyper-V|- La versione dei componenti di integrazione in esecuzione nella macchina virtuale deve essere la stessa dell'host Hyper-V. <br />-   Per ogni backup di macchina virtuale sarà necessario avere spazio libero nel volume che ospita i file di disco rigido virtuale per ottenere spazio sufficiente per Hyper-V per i dischi differenze (AVHD) durante il backup. Lo spazio deve essere almeno pari al calcolo **Dimensioni disco iniziali\*Varianza\*Durata backup**. Se si eseguono più backup in un cluster, sarà necessaria una capacità di archiviazione sufficiente per i file AVHD di ogni macchina virtuale, in base a questo calcolo.<br />- Per eseguire il backup di macchine virtuali situate in server host Hyper-V che eseguono Windows Server 2012 R2, la macchina virtuale deve avere un controller SCSI specificato, anche se non è connesso a nulla. Nel backup in linea di Windows Server 2012 R2, l'host Hyper-V monta un nuovo disco rigido virtuale nella macchina virtuale e quindi lo smonta in un secondo momento. Solo il controller SCSI può supportarlo e pertanto è necessario per il backup in linea della macchina virtuale.  Senza questa impostazione, verrà generato l'ID evento 10103 quando si tenta di eseguire il backup della macchina virtuale.)|
|Prerequisiti di Linux|- È possibile eseguire il backup di macchine virtuali Linux utilizzando MABS. Sono supportati solo snapshot coerenti a livello di file.|
|Backup di macchine virtuali con archiviazione CSV|-   Per l'archiviazione CSV, installare il provider hardware del Servizio Copia Shadow del volume nel server Hyper-V. Contatta il fornitore della rete di archiviazione (SAN) per ottenere il provider hardware del Servizio Copia Shadow del volume.<br />- Se un singolo nodo viene arrestato in modo imprevisto in un cluster CSV, MABS eseguirà una verifica di coerenza sulle macchine virtuali in esecuzione su tale nodo.<br />-   Per riavviare un server Hyper-V in cui è abilitato Crittografia unità BitLocker nel server CSV, è necessario eseguire una verifica coerenza per le macchine virtuali Hyper-V.|
|Backup di macchine virtuali con archiviazione SMB|-   Attivare il montaggio automatico sul server che esegue Hyper-V per abilitare la protezione delle macchine virtuali.<br />   -   Disabilitare TCP Chimney Offload.<br />-   Accertarsi che tutti gli account machine$ di Hyper-V dispongano di autorizzazioni complete per le specifiche condivisioni di file SMB remote.<br />- Assicurarsi che il percorso del file per tutti i componenti della macchina virtuale durante il ripristino in un percorso alternativo sia inferiore a 260 caratteri. In caso contrario, il ripristino potrebbe avere esito positivo, ma Hyper-V non sarà in grado di montare la macchina virtuale.<br />- Gli scenari seguenti non sono supportati:- The following scenarios aren't supported:<br />     Distribuzioni in cui alcuni componenti della macchina virtuale si trovano in volumi locali e in volumi remoti. un indirizzo IPv4 o IPv6 per il file server dei percorsi di archiviazione e il ripristino di una macchina virtuale in un computer che utilizza condivisioni SMB remote.<br />- È necessario abilitare il servizio Agente VSS File Server in ogni server SMB - aggiungerlo in **Aggiungi ruoli e funzionalità** > **Selezionare i ruoli** > del server File e Servizi**di** > **archiviazione** > **File Server** > file**Server VSS Agent Service**.|

## <a name="back-up-virtual-machines"></a>Backup di macchine virtuali

1. Configurare il [server MABS](backup-azure-microsoft-azure-backup.md) e [l'archiviazione](backup-mabs-add-storage.md). Per la configurazione dello spazio di archiviazione, usare queste linee guida sulla capacità di archiviazione.
   - Dimensione media macchina virtuale: 100 GB
   - Numero di macchine virtuali per server MABS - 800
   - Dimensione totale di 800 macchine virtuali: 80 TB
   - Spazio richiesto per l'archiviazione di backup: 80 TB

2. Configurare l'agente protezione MABS nei nodi del server Hyper-V o del cluster Hyper-V. Se si esegue il backup a livello di guest, si installerà l'agente nelle macchine virtuali di cui si vuole eseguire il backup a livello di guest.

3. Nella console di amministrazione MABS, fare clic su **Protezione dati** > **Crea gruppo protezione dati** per aprire la procedura guidata Crea nuovo gruppo **protezione dati.**

4. Nella pagina **Selezione membri del gruppo** selezionare le macchine virtuali da proteggere dai server host Hyper-V in cui si trovano. È consigliabile includere tutte le macchine virtuali che avranno gli stessi criteri di protezione in un unico gruppo protezione dati. Per un uso efficiente dello spazio, abilitare la condivisione percorso. La condivisione percorso consente di individuare i dati da diversi gruppi protezione dati nello stesso disco o nella stessa archiviazione su nastro in modo che più origini dati abbiano soltanto una replica e un volume del punto di ripristino.

5. Nella pagina **Seleziona metodo protezione dati** specificare un nome di gruppo di protezione. Selezionare **Protezione dati a breve termine tramite Disco** e **Protezione dati online** se si vuole eseguire il backup in Azure con il servizio Azure Backup.

6. In **Specificare** > **l'intervallo**di conservazione degli obiettivi a breve termine specificare per quanto tempo si desidera conservare i dati del disco. In **Frequenza di sincronizzazione**specificare la frequenza di esecuzione dei backup incrementali dei dati. In alternativa, invece di selezionare un intervallo per i backup incrementali, è possibile abilitare **Immediatamente prima di un punto di ripristino**. Con questa impostazione attivata, MABS eseguirà un backup completo rapido appena prima di ogni punto di ripristino pianificato.

    > [!NOTE]
    >
    >Se si intende proteggere i carichi di lavoro dell'applicazione, i punti di ripristino vengono creati in base al valore di Frequenza di sincronizzazione se l'applicazione supporta i backup incrementali. In caso contrario, MABS esegue un backup completo rapido, anziché un backup incrementale, e crea punti di ripristino in base alla pianificazione del backup rapido.

7. Nella pagina **Verifica allocazione dischi** esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.

   **Dimensione totale dei dati** è la dimensione dei dati di cui si desidera eseguire il backup e **spazio su disco di cui eseguire il provisioning in MABS** è lo spazio consigliato da MABS per il gruppo protezione dati. MABS sceglie il volume di backup ideale, in base alle impostazioni. Tuttavia, è possibile modificare le opzioni del volume di backup in **Dettagli allocazione disco**. Per i carichi di lavoro, selezionare la risorsa di archiviazione preferita nel menu a discesa. Le modifiche modificano i valori per **Totale spazio di archiviazione** e **Spazio libero di archiviazione** nel riquadro **Spazio di archiviazione su disco disponibile**. Lo spazio sottoprovisioning è la quantità di spazio di archiviazione che MABS suggerisce di aggiungere al volume, per continuare i backup senza problemi in futuro.

8. Nella pagina **Scelta del metodo per la creazione della replica** specificare come verrà eseguita la replica iniziale dei dati nel gruppo protezione dati. Se si sceglie di **eseguire la replica automatica in rete,** è consigliabile scegliere un orario non di punta. Per grandi quantità di dati o condizioni di rete non ottimali, è consigliabile **selezionare Manualmente**, che richiede la replica dei dati non in linea utilizzando supporti rimovibili.

9. Nella pagina **Opzioni di verifica coerenza** scegliere come automatizzare le verifiche della coerenza. È possibile impostare l'esecuzione di una verifica solo quando i dati di replica diventano incoerenti o in base a una pianificazione. Se non si vuole configurare la verifica coerenza automatica, è possibile eseguire una verifica manuale in qualsiasi momento facendo clic con il pulsante destro del mouse sul gruppo protezione dati e scegliendo **Esegui verifica coerenza**.

    Dopo aver creato il gruppo protezione dati, viene eseguita la replica iniziale dei dati in base al metodo selezionato. Dopo la replica iniziale, ogni backup viene eseguito in base alle impostazioni del gruppo protezione dati. Se è necessario ripristinare i dati di cui è stato eseguito il backup, tenere presente quanto segue:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Backup di macchine virtuali configurate per la migrazione in tempo reale

Quando le macchine virtuali sono coinvolte nella migrazione in tempo reale, MABS continua a proteggere le macchine virtuali finché l'agente protezione MABS è installato nell'host Hyper-V. Il modo in cui MABS protegge le macchine virtuali dipende dal tipo di migrazione in tempo reale coinvolto.

Migrazione in tempo reale **all'interno** di un cluster: quando viene eseguita la migrazione di una macchina virtuale all'interno di un cluster, MABS rileva la migrazione ed esegue il backup della macchina virtuale dal nuovo nodo del cluster senza alcun requisito per l'intervento dell'utente. Poiché il percorso di archiviazione non è stato modificato, MABS continua con backup completi rapidi.

Migrazione in tempo reale **all'esterno del cluster:** quando viene eseguita la migrazione di una macchina virtuale tra server autonomi, cluster diversi o tra un server autonomo e un cluster, MABS rileva la migrazione ed è possibile eseguire il backup della macchina virtuale senza l'intervento dell'utente.

### <a name="requirements-for-maintaining-protection"></a>Requisiti per preservare la protezione

Di seguito sono indicati i requisiti per preservare la protezione durante la migrazione in tempo reale:

- Gli host Hyper-V per le macchine virtuali devono trovarsi in un cloud VMM di System Center, in un server VMM, che esegue almeno System Center 2012 con SP1.

- L'agente protezione MABS deve essere installato in tutti gli host Hyper-V.

- I server MABS devono essere connessi al server VMM. Tutti i server host Hyper-V nel cloud VMM devono inoltre essere connessi ai server MABS. Ciò consente a MABS di comunicare con il server VMM in modo che MABS possa scoprire in quale server host Hyper-V la macchina virtuale è attualmente in esecuzione e di creare un nuovo backup da tale server Hyper-V. Se non è possibile stabilire una connessione al server Hyper-V, il backup non riesce con un messaggio che indica che l'agente protezione MABS non è raggiungibile.

- Tutti i server MABS, i server VMM e i server host Hyper-V devono trovarsi nello stesso dominio.

### <a name="details-about-live-migration"></a>Informazioni dettagliate sulla migrazione in tempo reale

Per il backup durante la migrazione in tempo reale, tenere presente quanto segue:

- Se una migrazione in tempo reale trasferisce l'archiviazione, MABS esegue una verifica di coerenza completa della macchina virtuale e quindi continua con backup completi rapidi. Quando si verifica la migrazione in tempo reale dell'archiviazione, Hyper-V riorganizza il disco rigido virtuale (VHD) o VHDX, che causa un picco una tantera nelle dimensioni dei dati di backup MABS.

- Sull'host della macchina virtuale attivare il montaggio automatico per abilitare la protezione virtuale, nonché disabilitare TCP Chimney Offload.

- MABS utilizza la porta 6070 come porta predefinita per l'hosting del servizio DPM-VMM Helper. Per modificare il Registro di sistema:

    1. Passare a **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Creare un valore DWORD a 32 bit: DpmVmmHelperServicePort e scrivere il numero di porta aggiornato nella chiave del Registro di sistema.
    3. Aprire ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config``` e modificare il numero di porta da 6070 alla nuova porta. Ad esempio: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Riavviare il servizio helper DPM-VMM e riavviare il servizio DPM.

### <a name="set-up-protection-for-live-migration"></a>Impostare la protezione per la migrazione in tempo reale

Per impostare la protezione per la migrazione in tempo reale:

1. Configurare il server MABS e la relativa risorsa di archiviazione e installare l'agente protezione MABS in ogni nodo del cluster o del server host Hyper-V nel cloud VMM. Se si usa l'archiviazione SMB in un cluster, installare l'agente protezione MABS in tutti i nodi del cluster.

2. Installare la console VMM come componente client nel server MABS in modo che MABS possa comunicare con il server VMM. La console deve essere della stessa versione in esecuzione nel server VMM.

3. Assegnare l'account MABSMachineName come account amministratore di sola lettura nel server di gestione VMM.

4. Connettere tutti i server host Hyper-V `Set-DPMGlobalProperty` a tutti i server MABS con il cmdlet PowerShell. Il cmdlet accetta più nomi di server MABS. Usare il formato: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Per ulteriori informazioni, vedere [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Dopo che sono state individuate in VMM tutte le macchine virtuali in esecuzione negli host Hyper-V nei cloud VMM, configurare un gruppo protezione dati e aggiungere le macchine virtuali che si vuole proteggere. Le verifiche di coerenza automatiche devono essere abilitate a livello di gruppo protezione dati per la protezione in scenari di mobilità delle macchine virtuali.

6. Dopo aver configurato le impostazioni, quando una macchina virtuale esegue la migrazione da un cluster a un altro tutti i backup continuano come previsto. Puoi verificare che Live Migration sia abilitata come previsto, come segue:

   1. Controlla che il servizio di supporto di DPM VMM sia in esecuzione. Se non lo è, avvialo.

   2. Aprire Microsoft SQL Server Management Studio e connettersi all'istanza che ospita il database MABS (DPMDB). Su DPMDB eseguire la query seguente: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Questa query contiene `KnownVMMServer`una proprietà denominata . Questo valore deve corrispondere a quello fornito con il cmdlet `Set-DPMGlobalProperty`.

   3. Eseguire la query seguente per convalidare il parametro *VMMIdentifier* in `PhysicalPathXML` per una determinata macchina virtuale. Sostituire `VMName` con il nome della macchina virtuale.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Aprire il file XML restituito dalla query e verificare che il campo *VMMIdentifier* contenga un valore.

### <a name="run-manual-migration"></a>Eseguire la migrazione manuale

Dopo aver completato i passaggi nelle sezioni precedenti e il processo di Gestione riepilogo MABS viene completato, la migrazione è abilitata. Per impostazione predefinita, questo processo inizia a mezzanotte e viene eseguito ogni mattina. Se si vuole eseguire una migrazione manuale per controllare che tutto stia funzionando come previsto, eseguire le operazioni seguenti:

1. Aprire SQL Server Management Studio e connettersi all'istanza che ospita il database MABS.

2. Eseguire la query seguente: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. La query restituisce l'**IDPianificazione**. Questo ID verrà usato nel passaggio successivo.

3. In SQL Server Management Studio, espandere **SQL Server Agent** e quindi espandere **Processi**. Fare clic con il pulsante destro del mouse sull'**IDPianificazione** annotato e selezionare **Inizia processo al passaggio**.

Le prestazioni del backup sono influenzate durante l'esecuzione del processo. Le dimensioni e la scala della distribuzione determineranno il tempo necessario per completare questo processo.

## <a name="back-up-replica-virtual-machines"></a>Backup di macchine virtuali di replica

Se MABS è in esecuzione su Windows Server 2012 R2 o versione successiva, è possibile eseguire il backup delle macchine virtuali di replica. Questo passaggio risulta utile per diversi motivi:

**Si riduce l'impatto del backup sull'esecuzione del carico di lavoro**: l'esecuzione del backup di una macchina virtuale comporta un sovraccarico quando viene creato uno snapshot. Eseguendo l'offload del processo di backup in un sito remoto secondario, il carico di lavoro in esecuzione non è più interessato dall'operazione di backup. Questo è applicabile solo alle distribuzioni in cui la copia di backup è archiviata in un sito remoto. Ad esempio, potresti eseguire backup giornalieri e archiviare i dati localmente per assicurarti tempi di ripristino rapidi, ma eseguire backup mensili o trimestrali dalle macchine virtuali di replica archiviate in remoto per la conservazione a lungo termine.

**Si risparmia larghezza di banda**: in una distribuzione tipica con sedi centrali/filiali remote, è necessario il provisioning di una quantità appropriata di larghezza di banda per trasferire i dati di backup da un sito a un altro. Se si crea una strategia di replica e di failover, oltre alla strategia di backup dei dati, è possibile ridurre la quantità di dati ridondanti inviati in rete. Eseguendo il backup dei dati della macchina virtuale di replica anziché primaria, si salva l'overhead dell'invio dei dati di cui è stato eseguito il backup in rete.

**Si abilita il backup di host**: è possibile usare un data center ospitato come sito di replica, senza che sia necessario un data center secondario. In questo caso, il lasso di servizio hoster richiede un backup coerente delle macchine virtuali di replica.

Una macchina virtuale di replica è disattivata fino a quando non viene avviato un failover e il servizio Copia Shadow del volume non può garantire un backup coerente con le applicazioni per una macchina virtuale di replica. Il backup di una macchina virtuale di replica sarà quindi solo coerente con un arresto anomalo del sistema. Se non è possibile garantire la coerenza con un arresto anomalo del sistema, il backup avrà esito negativo. Questa situazione può verificarsi in una serie di condizioni:

- La macchina virtuale di replica non è integra e si trova in uno stato critico.

- La macchina virtuale di replica sta eseguendo la risincronizzazione, si trova cioè nello stato Risincronizzazione in corso o Risincronizzazione richiesta.

- La replica iniziale tra il sito primario e il sito secondario è in corso o in sospeso per la macchina virtuale.

- I log con estensione hrl vengono applicati alla macchina virtuale di replica o un'azione precedente per applicare i registri con estensione hrl sul disco virtuale non riuscita o è stata annullata o interrotta.

- È in corso la migrazione o il failover della macchina virtuale di replica.

## <a name="recover-backed-up-virtual-machines"></a>Ripristinare il backup di macchine virtuali

Quando è possibile ripristinare una macchina virtuale sottoposto a backup, usare il Ripristino guidato per selezionare la macchina virtuale e il punto di ripristino specifico. Per aprire il Ripristino guidato e ripristinare una macchina virtuale:

1. Nella console di amministrazione MABS digitare il nome della macchina virtuale oppure espandere l'elenco di elementi protetti e selezionare la macchina virtuale da ripristinare.

2. Nel riquadro **Punti di ripristino per**, nel calendario, fare clic su una data per visualizzare i punti di ripristino disponibili. Quindi nel riquadro **Percorso** selezionare il punto di ripristino da usare nel Ripristino guidato.

3. Nel menu **Azioni** fare clic su **Ripristina** per aprire il Ripristino guidato.

    La macchina virtuale e il punto di ripristino selezionato vengono visualizzati nella schermata **Verifica selezione per ripristino**. Fare clic su **Avanti**.

4. Nella schermata **Selezione tipo di ripristino** selezionare la posizione in cui si vuole ripristinare i dati e quindi fare clic su **Avanti**.

    - **Ripristina nell'istanza originale**: quando viene ripristinato nel percorso originale, il file VHD originale viene eliminato. MABS ripristina il disco rigido virtuale e altri file di configurazione nel percorso originale utilizzando il writer VSS Hyper-V. Alla fine del processo di ripristino le macchine virtuali risultano ancora a disponibilità elevata.
        Per il ripristino, è necessario che sia presente il gruppo di risorse. Se non è disponibile, eseguire il ripristino in un percorso alternativo e quindi rendere la macchina virtuale a disponibilità elevata.

    - **Ripristina come macchina virtuale in qualsiasi host:** MABS supporta il ripristino alternativo della posizione (ALR), che fornisce un ripristino senza soluzione di continuità di una macchina virtuale Hyper-V protetta in un host Hyper-V diverso, indipendentemente dall'architettura del processore. Le macchine virtuali Hyper-V ripristinate in un nodo cluster non saranno a disponibilità elevata. Se si sceglie questa opzione, il Ripristino guidato visualizza una schermata aggiuntiva per identificare la destinazione e il percorso di destinazione.

    - **Copia in una cartella**di rete: MABS supporta il ripristino a livello di elemento (ILR, Item Level ing itemlevel Recovery) di file, cartelle, volumi e dischi rigidi virtuali (VHD) da un backup a livello di host delle macchine virtuali Hyper-V a una condivisione di rete o un volume in un server protetto da MABS. L'agente protezione MABS non deve essere installato all'interno del guest per eseguire il ripristino a livello di elemento. Se si sceglie questa opzione, il Ripristino guidato visualizza una schermata aggiuntiva per identificare la destinazione e il percorso di destinazione.

5. In **Specifica opzioni di ripristino** configurare le opzioni di ripristino e fare clic su **Avanti**:

    - Se si ripristina una macchina virtuale sulla larghezza di banda ridotta, fare clic su **Modifica** per abilitare **Limitazione all'utilizzo della larghezza di banda**. Dopo aver attivato l'opzione di limitazione, è possibile specificare la quantità di larghezza di banda che si vuole rendere disponibile e l'ora in cui deve essere disponibile.
    - Selezionare **Abilita recupero basato su SAN tramite snapshot dell'hardware** se è stata configurata la rete.
    - Selezionare **Invia messaggio di posta elettronica al termine del ripristino** e quindi specificare gli indirizzi di posta elettronica, se si vuole che le notifiche di posta elettronica vengano inviate al termine del processo di ripristino.

6. Nella schermata di riepilogo, verificare che tutti i dettagli siano corretti. Se i dettagli non sono corretti o si vuole apportare una modifica, fare clic su **Indietro**. Se si è soddisfatti con le impostazioni, fare clic su **Ripristina** per avviare il processo di ripristino.

7. La schermata **Stato ripristino** fornisce informazioni sul processo di ripristino.

## <a name="next-steps"></a>Passaggi successivi

[Ripristinare i dati da un server di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
