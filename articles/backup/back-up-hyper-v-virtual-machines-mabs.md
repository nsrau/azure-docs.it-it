---
title: Eseguire il backup di macchine virtuali Hyper-V con server di Backup di Azure
description: Questo articolo contiene le procedure per il backup e il ripristino di macchine virtuali con Backup di Microsoft Azure server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: dacurwin
ms.openlocfilehash: 46d9f33dedff5a5682385b9cb22cf310581eefde
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466850"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Eseguire il backup di macchine virtuali Hyper-V con server di Backup di Azure

Questo articolo illustra come eseguire il backup di macchine virtuali Hyper-V con Backup di Microsoft Azure Server (MAB).

## <a name="supported-scenarios"></a>Scenari supportati
MAB può eseguire il backup di macchine virtuali in esecuzione nei server host Hyper-V negli scenari seguenti:

-   **Macchine virtuali con archiviazione locale o diretta** -backup di macchine virtuali ospitate in server autonomi host Hyper-V che dispongono di archiviazione locale o collegata direttamente. Ad esempio: un disco rigido, un dispositivo SAN (Storage Area Network) o un dispositivo NAS (Network Attached Storage). È necessario installare l'agente protezione di Mab in tutti gli host.

-   **Macchine virtuali in un cluster con archiviazione CSV** -eseguire il backup di macchine virtuali ospitate in un cluster Hyper-V con archiviazione volume condiviso cluster (CSV). L'agente protezione di Mab è installato in ogni nodo del cluster.



## <a name="host-versus-guest-backup"></a>Host rispetto al backup Guest
MAB può eseguire un backup a livello di host o Guest di macchine virtuali Hyper-V. A livello di host, l'agente protezione di Mab è installato nel server o nel cluster host Hyper-V e protegge tutte le macchine virtuali e i file di dati in esecuzione in tale host.   A livello di Guest, l'agente è installato in ogni macchina virtuale e protegge il carico di lavoro presente in tale computer.

Entrambi i metodi hanno vantaggi e svantaggi:

-   I backup a livello di host sono flessibili perché funzionano indipendentemente dal tipo di sistema operativo in esecuzione nei computer guest e non richiedono l'installazione dell'agente protezione MAB in ogni macchina virtuale. Se si distribuisce il backup a livello di host, è possibile ripristinare un'intera macchina virtuale o file e cartelle (ripristino a livello di elemento).

-   Il backup a livello di Guest è utile se vuoi proteggere specifici carichi di lavoro in esecuzione in una macchina virtuale. A livello di host è possibile ripristinare un'intera macchina virtuale o file specifici, ma non fornirà il ripristino nel contesto di un'applicazione specifica. Ad esempio, per ripristinare elementi specifici di SharePoint da una macchina virtuale di cui è stato eseguito il backup, è necessario eseguire il backup a livello di Guest della macchina virtuale. Usare il backup a livello di Guest se si vuole proteggere i dati archiviati su dischi pass-through. Passthrough consente alla macchina virtuale di accedere direttamente al dispositivo di archiviazione e non archivia i dati del volume virtuale in un file VHD.



## <a name="how-the-backup-process-works"></a>Funzionamento del processo di backup
MAB esegue il backup con VSS come segue. I passaggi descritti in questa descrizione sono numerati per semplificare la chiarezza.

1. Il motore di sincronizzazione basato su blocchi di MAB crea una copia iniziale della macchina virtuale protetta e garantisce che la copia della macchina virtuale sia completa e coerente.

2. Una volta eseguita e verificata la copia iniziale, MAB usa il VSS writer Hyper-V per acquisire i backup. Il VSS writer fornisce un set di blocchi disco coerenti con i dati sincronizzati con il server di MAB. Questo approccio offre il vantaggio di un "backup completo" con il server MAB, riducendo al minimo i dati di backup che devono essere trasferiti attraverso la rete.

3. L'agente protezione di Mab in un server che esegue Hyper-V usa le API Hyper-V esistenti per determinare se una macchina virtuale protetta supporta anche VSS.

   - Se una macchina virtuale è conforme ai requisiti per il backup online ed è installato il componente dei servizi di integrazione Hyper-V, il VSS writer Hyper-V esegue in modo ricorsivo la richiesta VSS attraverso tutti i processi che sono compatibili con VSS nella macchina virtuale. Questa operazione si verifica senza che l'agente protezione di MAB sia installato nella macchina virtuale. La richiesta di VSS ricorsiva consente al VSS writer Hyper-V di garantire che le operazioni di scrittura su disco siano sincronizzate in modo da acquisire uno snapshot VSS senza perdita di dati.

     Il componente servizi di integrazione Hyper-V richiama il VSS writer Hyper-V in servizio Copia Shadow del volume (VSS) sulle macchine virtuali per garantire che i dati dell'applicazione siano in uno stato coerente.

   - Se la macchina virtuale non è conforme ai requisiti di backup online, MAB usa automaticamente le API di Hyper-V per sospendere la macchina virtuale prima di acquisire i file di dati.

4. Dopo la sincronizzazione della copia di base iniziale della macchina virtuale con il server MAB, tutte le modifiche apportate alle risorse della macchina virtuale vengono acquisite in un nuovo punto di ripristino. Il punto di ripristino rappresenta lo stato coerente della macchina virtuale in un momento specifico. Le acquisizioni del punto di ripristino possono verificarsi almeno una volta al giorno. Quando viene creato un nuovo punto di ripristino, MAB utilizza la replica a livello di blocco insieme al VSS writer Hyper-V per determinare quali blocchi sono stati alterati sul server che esegue Hyper-V dopo la creazione dell'ultimo punto di ripristino. Questi blocchi di dati vengono quindi trasferiti al server MAB e applicati alla replica dei dati protetti.

5. Il server MAB utilizza VSS sui volumi che ospitano i dati di ripristino in modo che siano disponibili più copie shadow. Ognuna di queste copie shadow fornisce un ripristino separato. I punti di ripristino VSS vengono archiviati nel server MAB. La copia temporanea eseguita sul server che esegue Hyper-V viene archiviata solo per la durata della sincronizzazione di MAB.

>[!NOTE]
>
>A partire da Windows Server 2016, i dischi rigidi virtuali Hyper-V hanno un rilevamento delle modifiche incorporato noto come rilevamento delle modifiche resilienti (RCT). MAB USA RCT (il rilevamento delle modifiche nativo in Hyper-V), che riduce la necessità di verifiche di coerenza che richiedono molto tempo in scenari come gli arresti anomali delle macchine virtuali. RCT offre una migliore resilienza rispetto al rilevamento delle modifiche offerto dai backup basati su snapshot di VSS. MABS V3 ottimizza ulteriormente il consumo di archiviazione e rete trasferendo solo i dati modificati durante le verifiche della coerenza.


## <a name="backup-prerequisites"></a>Prerequisiti di backup
Questi sono i prerequisiti per il backup di macchine virtuali Hyper-V con MAB:

|Prerequisito|Dettagli|
|------------|-------|
|Prerequisiti di MAB|-Se si vuole eseguire il ripristino a livello di elemento per le macchine virtuali (ripristino di file, cartelle, volumi), è necessario installare il ruolo Hyper-V nel server MAB.  Se si vuole solo ripristinare la macchina virtuale e non a livello di elemento, il ruolo non è necessario.<br />-È possibile proteggere fino a 800 macchine virtuali da 100 GB ognuna in un server MAB e consentire più server MAB che supportano cluster più grandi.<br />-MAB esclude il file di paging dai backup incrementali per migliorare le prestazioni del backup delle macchine virtuali.<br />-MAB può eseguire il backup di un server o di un cluster Hyper-V nello stesso dominio del server MAB oppure in un dominio figlio o trusted. Se si vuole eseguire il backup di Hyper-V in un gruppo di lavoro o in un dominio non trusted, è necessario configurare l'autenticazione. Per un server Hyper-V singolo è possibile usare NTLM o l'autenticazione del certificato. Per un cluster, è possibile usare solo l'autenticazione del certificato.<br />-L'uso del backup a livello di host per eseguire il backup dei dati delle macchine virtuali su dischi pass-through non è supportato. In questo scenario è consigliabile usare il backup a livello di host per eseguire il backup dei file VHD e del backup a livello di Guest per eseguire il backup degli altri dati non visibili nell'host.<br />   -È possibile eseguire il backup delle macchine virtuali archiviate in volumi deduplicati.|
|Prerequisiti della macchina virtuale Hyper-V|-La versione dei componenti di integrazione in esecuzione nella macchina virtuale deve corrispondere alla versione dell'host Hyper-V. <br />-Per ogni backup della macchina virtuale è necessario spazio libero nel volume che ospita i file del disco rigido virtuale per consentire a Hyper-V lo spazio sufficiente per i dischi differenze (AVHD) durante il backup. Lo spazio deve essere almeno uguale all'ora della finestra di **backup della\*frequenza\*di varianza del disco iniziale** di calcolo. Se si eseguono più backup in un cluster, sarà necessaria una capacità di archiviazione sufficiente per gestire il AVHDs per ogni macchina virtuale usando questo calcolo.<br />-Per eseguire il backup di macchine virtuali che si trovano in server host Hyper-V che eseguono Windows Server 2012 R2, è necessario che nella macchina virtuale sia specificato un controller SCSI, anche se non è connesso ad alcun elemento. (In Windows Server 2012 R2 online backup, l'host Hyper-V monta un nuovo disco rigido virtuale nella macchina virtuale e quindi lo smonta in un secondo momento. Solo il controller SCSI può supportare questa operazione e pertanto è necessario per il backup online della macchina virtuale.  Senza questa impostazione, verrà generato l'evento con ID 10103 quando si tenta di eseguire il backup della macchina virtuale.|
|Prerequisiti per Linux|-È possibile eseguire il backup di macchine virtuali Linux con MAB 2012 R2. Sono supportati solo snapshot coerenti con i file.|
|Eseguire il backup di macchine virtuali con archiviazione CSV|-Per l'archiviazione CSV, installare il provider hardware servizio Copia Shadow del volume (VSS) nel server Hyper-V. Contattare il fornitore della rete di archiviazione (SAN) per il provider hardware VSS.<br />-Se un singolo nodo si arresta in modo imprevisto in un cluster CSV, viene eseguita una verifica di coerenza per le macchine virtuali in esecuzione in tale nodo.<br />-Se è necessario riavviare un server Hyper-V in cui Crittografia unità BitLocker è abilitato nel cluster CSV, è necessario eseguire una verifica di coerenza per le macchine virtuali Hyper-V.|
|Eseguire il backup di macchine virtuali con archiviazione SMB|-Attivare il montaggio automatico sul server che esegue Hyper-V per abilitare la protezione della macchina virtuale.<br />   -Disabilitare TCP Chimney Offload.<br />-Assicurarsi che tutti gli account machine $ di Hyper-V dispongano di autorizzazioni complete per le specifiche condivisioni file SMB remote.<br />-Verificare che il percorso del file per tutti i componenti della macchina virtuale durante il ripristino in un percorso alternativo sia inferiore a 260 caratteri. In caso contrario, il ripristino potrebbe avere esito positivo, ma Hyper-V non sarà in grado di montare la macchina virtuale.<br />-Gli scenari seguenti non sono supportati:<br />     Distribuzioni in cui alcuni componenti della macchina virtuale sono in volumi locali e alcuni componenti si trovano su volumi remoti. un indirizzo IPv4 o IPv6 per il percorso di archiviazione file server e il ripristino di una macchina virtuale in un computer che utilizza condivisioni SMB remote.<br />-È necessario abilitare il servizio file Server VSS Agent in ogni server SMB. aggiungerlo in **Aggiungi ruoli e funzionalità** > **Selezione ruoli** > server Servizi file**e archiviazione Servizi** > **file**  >  **Servizio file** **Servizio file Server VSS Agent.**  > |

## <a name="back-up-virtual-machines"></a>Eseguire il backup di macchine virtuali

1. Configurare il [Server MAB](backup-azure-microsoft-azure-backup.md) e [l'archiviazione](backup-mabs-add-storage.md). Quando si configura l'archiviazione, usare queste linee guida per la capacità di archiviazione.
   - Dimensioni medie macchina virtuale-100 GB
   - Numero di macchine virtuali per server MAB-800
   - Dimensioni totali di 800 VM-80 TB
   - Spazio richiesto per l'archiviazione di backup-80 TB

2. Configurare l'agente protezione di MAB nel server Hyper-V o nei nodi del cluster Hyper-V. Se si esegue un backup a livello di Guest, l'agente verrà installato nelle macchine virtuali di cui si vuole eseguire il backup a livello di Guest.

3. Nella console di amministrazione di MAB fare clic su **protezione** > dati**Crea gruppo protezione** dati per aprire la procedura guidata **Crea nuovo gruppo protezione** dati.

4. Nella pagina **Seleziona membri del gruppo** selezionare le macchine virtuali che si desidera proteggere dai server host Hyper-V in cui si trovano. Si consiglia di inserire tutte le macchine virtuali che avranno gli stessi criteri di protezione in un solo gruppo protezione dati. Per usare lo spazio in modo efficiente, abilitare la condivisione percorso. La condivisione percorso consente di individuare i dati da diversi gruppi protezione dati sullo stesso spazio di archiviazione su disco o su nastro, in modo che più origini dati dispongano di un singolo volume di replica e di punto di ripristino.

5. Nella pagina **Selezione metodo protezione dati** specificare un nome per il gruppo protezione dati. Selezionare **protezione a breve termine tramite disco** e selezionare protezione dati **online** se si vuole eseguire il backup dei dati in Azure usando il servizio backup di Azure. 


6. In **specificare il periodo** > di**mantenimento**degli obiettivi a breve termine specificare per quanto tempo si vogliono mantenere i dati del disco. In **frequenza di sincronizzazione**specificare la frequenza con cui eseguire i backup incrementali dei dati. In alternativa, invece di selezionare un intervallo per i backup incrementali, è possibile abilitare **immediatamente prima di un punto di ripristino**. Se questa impostazione è abilitata, MAB eseguirà un backup completo rapido appena prima di ogni punto di ripristino pianificato.

    > [!NOTE]
    >
    >Se si stanno proteggendo i carichi di lavoro dell'applicazione, i punti di ripristino vengono creati in base alla frequenza di sincronizzazione, purché l'applicazione supporti i backup incrementali. In caso contrario, MAB esegue un backup completo rapido, anziché un backup incrementale, e crea punti di ripristino in base alla pianificazione del backup rapido.

    

7. Nella pagina **Verifica allocazione dischi** esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.

   La **dimensione totale dei dati** corrisponde alle dimensioni dei dati di cui si vuole eseguire il backup e lo spazio **su disco di** cui eseguire il provisioning su MAB è lo spazio consigliato da MAB per il gruppo protezione dati. MAB sceglie il volume di backup ideale, in base alle impostazioni. Tuttavia, è possibile modificare le opzioni del volume di backup nei dettagli sull'allocazione dei **dischi**. Per i carichi di lavoro, selezionare la risorsa di archiviazione preferita nel menu a discesa. Le modifiche modificano i valori per **Totale spazio di archiviazione** e **Spazio libero di archiviazione** nel riquadro **Spazio di archiviazione su disco disponibile**. Lo spazio sottoposto a provisioning è la quantità di MAB di archiviazione che suggerisce di aggiungere al volume, per continuare a eseguire i backup in modo semplice in futuro.

8. Nella pagina **scelta del metodo** per la creazione della replica specificare il modo in cui verrà eseguita la replica iniziale dei dati nel gruppo protezione dati. Se si sceglie di eseguire **la replica automatica in rete**, è consigliabile scegliere un orario di minore traffico. Per grandi quantità di dati o condizioni di rete non ottimali, è consigliabile selezionare **manualmente**, che richiede la replica dei dati offline mediante supporti rimovibili.

9. Nella pagina **Opzioni di verifica coerenza** selezionare il modo in cui si desidera automatizzare le verifiche della coerenza. È possibile abilitare l'esecuzione di un controllo solo quando i dati di replica diventano incoerenti o in base a una pianificazione. Se non si vuole configurare la verifica coerenza automatica, è possibile eseguire una verifica manuale in qualsiasi momento facendo clic con il pulsante destro del mouse sul gruppo protezione dati e scegliendo **Esegui verifica coerenza**.

    Dopo aver creato il gruppo protezione dati, la replica iniziale dei dati viene eseguita in base al metodo selezionato. Dopo la replica iniziale, ogni backup viene eseguito in linea con le impostazioni del gruppo protezione dati. Se è necessario ripristinare i dati di cui è stato eseguito il backup, tenere presente quanto segue:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Eseguire il backup di macchine virtuali configurate per Live Migration
Quando le macchine virtuali sono incluse nella migrazione in tempo reale, MAB continua a proteggere le macchine virtuali, purché l'agente protezione di MAB sia installato nell'host Hyper-V. Il modo in cui MAB protegge le macchine virtuali dipende dal tipo di migrazione in tempo reale.

**Migrazione in tempo reale in un cluster** : quando viene eseguita la migrazione di una macchina virtuale all'interno di un cluster, viene rilevata la migrazione e viene eseguito il backup della macchina virtuale dal nuovo nodo del cluster senza richiedere l'intervento dell'utente. Poiché il percorso di archiviazione non è stato modificato, MAB continua con backup completi rapidi. 

**Migrazione in tempo reale all'esterno del cluster** : quando viene eseguita la migrazione di una macchina virtuale tra server autonomi, cluster diversi o tra un server autonomo e un cluster, MAB rileva la migrazione ed è in grado di eseguire il backup della macchina virtuale senza utente intervento.

### <a name="requirements-for-maintaining-protection"></a>Requisiti per la gestione della protezione

Di seguito sono riportati i requisiti per la gestione della protezione durante la migrazione in tempo reale:

- Gli host Hyper-V per le macchine virtuali devono trovarsi in un Cloud System Center VMM in un server VMM che esegue almeno System Center 2012 con SP1.

- È necessario installare l'agente protezione di Mab in tutti gli host Hyper-V.

- I server MAB devono essere connessi al server VMM. Anche tutti i server host Hyper-V nel cloud VMM devono essere connessi ai server MAB. Questo consente a MAB di comunicare con il server VMM, in modo che i server host Hyper-V in cui è attualmente in esecuzione la macchina virtuale non siano in grado di creare un nuovo backup da tale server Hyper-V. Se non è possibile stabilire una connessione al server Hyper-V, il backup ha esito negativo con un messaggio indicante che l'agente protezione di MAB non è raggiungibile.

- Tutti i server MAB, i server VMM e i server host Hyper-V devono trovarsi nello stesso dominio.

### <a name="details-about-live-migration"></a>Informazioni dettagliate sulla migrazione in tempo reale

Tenere presente quanto segue per il backup durante la migrazione in tempo reale:


- Se una migrazione in tempo reale trasferisce l'archiviazione, MAB esegue una verifica di coerenza completa della macchina virtuale e continua quindi con backup rapidi completi. Quando si verifica la migrazione in tempo reale dell'archiviazione, Hyper-V riorganizza il disco rigido virtuale (VHD) o VHDX, causando un picco monouso nelle dimensioni dei dati di backup di MAB.

- Nell'host macchina virtuale attivare il montaggio automatico per abilitare la protezione virtuale e disabilitare TCP Chimney Offload.

- MAB utilizza la porta 6070 come porta predefinita per l'hosting del servizio Helper DPM-VMM. Per modificare il registro di sistema:

    1. Passare a **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Creare un valore DWORD a 32 bit: DpmVmmHelperServicePort e scrivere il numero di porta aggiornato come parte della chiave del registro di sistema.
    3.  Aprire ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```e modificare il numero di porta da 6070 alla nuova porta. Ad esempio: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Riavviare il servizio Helper DPM-VMM e riavviare il servizio DPM.

### <a name="set-up-protection-for-live-migration"></a>Configurare la protezione per la migrazione in tempo reale

Per configurare la protezione per la migrazione in tempo reale:

1. Configurare il server MAB e la relativa archiviazione e installare l'agente protezione MAB in ogni server host o nodo del cluster Hyper-V nel cloud VMM. Se si usa l'archiviazione SMB in un cluster, installare l'agente protezione di Mab in tutti i nodi del cluster.

2. Installare la console VMM come componente client nel server MAB per consentire a MAB di comunicare con il server VMM. La console deve essere della stessa versione di quella in esecuzione nel server VMM.

3. Assegnare l'account MABSMachineName $ come account amministrativo di sola lettura nel server di gestione VMM.

4. Connettere tutti i server host Hyper-V a tutti i server MAB `Set-DPMGlobalProperty` con il cmdlet di PowerShell. Il cmdlet accetta più nomi di server MAB. Usare il formato: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Per ulteriori informazioni, vedere [Set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx).

5. Dopo che tutte le macchine virtuali in esecuzione negli host Hyper-V nei cloud VMM vengono individuate in VMM, configurare un gruppo protezione dati e aggiungere le macchine virtuali che si desidera proteggere. Le verifiche di coerenza automatiche devono essere abilitate a livello di gruppo protezione dati per la protezione in scenari di mobilità delle macchine virtuali.

6. Una volta configurate le impostazioni, quando viene eseguita la migrazione di una macchina virtuale da un cluster a un altro, tutti i backup continuano come previsto. È possibile verificare che la migrazione in tempo reale sia abilitata come previsto come indicato di seguito:

   1. Controllare che il servizio Helper DPM-VMM sia in esecuzione. In caso contrario, avviarlo.

   2. Aprire Microsoft SQL Server Management Studio e connettersi all'istanza che ospita il database MAB (DPMDB). In DPMDB eseguire la query seguente: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Questa query contiene una proprietà denominata `KnownVMMServer`. Questo valore deve corrispondere a quello fornito con il `Set-DPMGlobalProperty` cmdlet.

   3. Eseguire la query seguente per convalidare il parametro *VMMIdentifier* in `PhysicalPathXML` per una determinata macchina virtuale. Sostituire `VMName` con il nome della macchina virtuale.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Aprire il file XML restituito dalla query e verificare che il campo *VMMIdentifier* contenga un valore.


### <a name="run-manual-migration"></a>Eseguire la migrazione manuale

Dopo aver completato i passaggi nelle sezioni precedenti e completato il processo di gestione del riepilogo di MAB, la migrazione è abilitata. Per impostazione predefinita, questo processo inizia a mezzanotte e viene eseguito ogni mattina. Se si vuole eseguire una migrazione manuale, per verificare che tutto funzioni come previsto, eseguire le operazioni seguenti:

1. Aprire SQL Server Management Studio e connettersi all'istanza che ospita il database di MAB.

2. Eseguire la query seguente: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Questa query restituisce **idpianificazione**. Si noti questo ID come verrà usato nel passaggio successivo.

3. Nella SQL Server Management Studio espandere **SQL Server Agent**, quindi espandere **processi**. Fare clic con il pulsante destro del mouse su **idpianificazione** annotato e selezionare **inizia processo al passaggio**.

Le prestazioni di backup vengono influenzate durante l'esecuzione del processo. Le dimensioni e la scala della distribuzione determinano il tempo necessario per il completamento del processo.


## <a name="back-up-replica-virtual-machines"></a>Eseguire il backup di macchine virtuali di replica

Se MAB è in esecuzione in Windows Server 2012 R2 o versione successiva, è possibile eseguire il backup di macchine virtuali di replica. Questa operazione è utile per diversi motivi:

**Riduce l'impatto dei backup sul carico di lavoro in esecuzione** . l'esecuzione di un backup di una macchina virtuale comporta un sovraccarico quando viene creato uno snapshot. Con l'offload del processo di backup in un sito remoto secondario, il carico di lavoro in esecuzione non è più interessato dall'operazione di backup. Questa operazione è applicabile solo alle distribuzioni in cui la copia di backup è archiviata in un sito remoto. Ad esempio, è possibile eseguire backup giornalieri e archiviare i dati localmente per garantire tempi di ripristino rapidi, ma eseguire backup mensili o trimestrali dalle macchine virtuali di replica archiviate in remoto per la conservazione a lungo termine.

**Risparmio di larghezza di banda** : in una tipica distribuzione di succursali/sedi remote è necessario disporre di una quantità appropriata di larghezza di banda con provisioning per trasferire i dati di backup tra siti. Se si crea una strategia di replica e failover, oltre alla strategia di backup dei dati, è possibile ridurre la quantità di dati ridondanti inviati in rete. Eseguendo il backup dei dati della macchina virtuale di replica anziché di quelli primari, si evita il sovraccarico dovuto all'invio dei dati di cui è stato eseguito il backup tramite la rete.

**Abilita il backup** del provider di hosting: è possibile usare un data center ospitato come sito di replica, senza necessità di un data center secondario. In questo caso, il contratto di Service del provider di hosting richiede il backup coerente delle macchine virtuali di replica.

Una macchina virtuale di replica è disattivata fino a quando non viene avviato un failover e VSS non è in grado di garantire un backup coerente con l'applicazione per una macchina virtuale di replica. Il backup di una macchina virtuale di replica sarà quindi solo coerente con l'arresto anomalo del sistema. Se non è possibile garantire la coerenza dell'arresto anomalo del sistema, il backup avrà esito negativo e ciò potrebbe verificarsi in una serie di condizioni:

- La macchina virtuale di replica non è integra e si trova in uno stato critico.

- La macchina virtuale di replica sta eseguendo la risincronizzazione (nello stato di risincronizzazione in corso o risincronizzazione richiesta).

- La replica iniziale tra il sito primario e quello secondario è in corso o in sospeso per la macchina virtuale.

- è in corso l'applicazione dei log HRL alla macchina virtuale di replica oppure un'azione precedente per applicare i log HRL nel disco virtuale non è riuscita oppure è stata annullata o interrotta.

- È in corso la migrazione o il failover della macchina virtuale di replica

## <a name="recover-backed-up-virtual-machines"></a>Ripristinare le macchine virtuali di cui è stato eseguito il backup

Quando è possibile ripristinare una macchina virtuale di cui è stato eseguito il backup, utilizzare il ripristino guidato per selezionare la macchina virtuale e il punto di ripristino specifico. Per aprire il ripristino guidato e ripristinare una macchina virtuale:

1. Nella console di amministrazione di MAB digitare il nome della macchina virtuale oppure espandere l'elenco degli elementi protetti e selezionare la macchina virtuale che si desidera ripristinare.

2. Nel riquadro **punti di ripristino per** , nel calendario, fare clic su una data per visualizzare i punti di ripristino disponibili. Quindi, nel riquadro **percorso** , selezionare il punto di ripristino che si desidera utilizzare nel ripristino guidato.

3. Dal menu **azioni** , fare clic su **Ripristina** per aprire il ripristino guidato.

    La macchina virtuale e il punto di ripristino selezionato vengono visualizzati nella schermata **Verifica selezione per ripristino** . Fare clic su **Avanti**.

4. Nella schermata **Selezione tipo di ripristino** selezionare la posizione in cui si desidera ripristinare i dati e quindi fare clic su **Avanti**.

    -   **Ripristina nell'istanza originale**: Quando si esegue il ripristino nell'istanza originale, il disco rigido virtuale originale viene eliminato. MAB Recupera il VHD e altri file di configurazione nel percorso originale usando Hyper-V VSS writer. Al termine del processo di ripristino, le macchine virtuali sono ancora a disponibilità elevata.
        Il gruppo di risorse deve essere presente per il ripristino. Se non è disponibile, eseguire il ripristino in un percorso alternativo, quindi rendere la macchina virtuale a disponibilità elevata.

    -   **Ripristina come macchina virtuale in qualsiasi host**: MAB supporta il ripristino in un percorso alternativo (ALR), che consente un ripristino trasparente di una macchina virtuale Hyper-V protetta in un host Hyper-V diverso, indipendentemente dall'architettura del processore. Le macchine virtuali Hyper-V ripristinate in un nodo cluster non saranno a disponibilità elevata. Se si sceglie questa opzione, il ripristino guidato Visualizza una schermata aggiuntiva per l'identificazione del percorso di destinazione e di destinazione.

    -   **Copia in una cartella di rete**: MAB supporta il ripristino a livello di elemento (ILR), che consente di eseguire il ripristino a livello di elemento di file, cartelle, volumi e dischi rigidi virtuali (VHD) da un backup a livello di host di macchine virtuali Hyper-V in una condivisione di rete o in un volume di un server protetto da MAB. Non è necessario installare l'agente protezione di MAB nel guest per eseguire il ripristino a livello di elemento. Se si sceglie questa opzione, il ripristino guidato Visualizza una schermata aggiuntiva per l'identificazione del percorso di destinazione e di destinazione.

5. In **specificare le opzioni di ripristino** configurare le opzioni di ripristino e fare clic su **Avanti**:

    - Se si esegue il ripristino di una macchina virtuale su una larghezza di banda ridotta, fare clic su **modifica** per abilitare la **limitazione all'utilizzo della larghezza di banda**. Dopo aver attivato l'opzione di limitazione, è possibile specificare la quantità di larghezza di banda che si vuole rendere disponibile e l'ora in cui è disponibile la larghezza di banda.
    - Selezionare **Abilita ripristino basato su San tramite snapshot dell'hardware** se la rete è stata configurata.
    - Selezionare **Invia messaggio di posta elettronica al termine del ripristino** e quindi specificare gli indirizzi di posta elettronica, se si desidera inviare notifiche tramite posta elettronica al termine del processo di ripristino.

6. Nella schermata Riepilogo verificare che tutti i dettagli siano corretti. Se i dettagli non sono corretti o si vuole apportare una modifica, fare clic su **indietro**. Se si è soddisfatti delle impostazioni, fare clic su **Ripristina** per avviare il processo di ripristino.

7. Nella schermata **Stato ripristino** vengono fornite informazioni sul processo di ripristino.


## <a name="next-steps"></a>Passaggi successivi
[Ripristinare i dati da un server di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
