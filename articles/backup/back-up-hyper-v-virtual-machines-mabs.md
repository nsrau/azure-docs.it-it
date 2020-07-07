---
title: Eseguire il backup di macchine virtuali Hyper-V con MAB
description: Questo articolo contiene le procedure per il backup e il ripristino di macchine virtuali con Backup di Microsoft Azure Server (MAB).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 71cf446472ef0cf4f50bf64e47d359ea08ccc087
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80420411"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Eseguire il backup di macchine virtuali Hyper-V con server di Backup di Azure

Questo articolo illustra come eseguire il backup di macchine virtuali Hyper-V con Backup di Microsoft Azure Server (MAB).

## <a name="supported-scenarios"></a>Scenari supportati

MAB può eseguire il backup di macchine virtuali in esecuzione nei server host Hyper-V negli scenari seguenti:

- **Macchine virtuali con archiviazione locale o diretta**: backup di macchine virtuali ospitate in server host autonomi Hyper-V che dispongono di archiviazione locale o collegata direttamente, Ad esempio: un disco rigido, un dispositivo SAN (Storage Area Network) o un dispositivo NAS (Network Attached Storage). È necessario installare l'agente protezione di Mab in tutti gli host.

- **Macchine virtuali in cluster con volumi condivisi del cluster**: backup di macchine virtuali ospitate in un cluster Hyper-V con volumi condivisi del cluster (CSV). L'agente protezione di Mab è installato in ogni nodo del cluster.

## <a name="host-versus-guest-backup"></a>Host rispetto al backup Guest

MAB può eseguire un backup a livello di host o Guest di macchine virtuali Hyper-V. A livello di host, l'agente protezione di Mab è installato nel server o nel cluster host Hyper-V e protegge tutte le macchine virtuali e i file di dati in esecuzione in tale host.   A livello di Guest, l'agente è installato in ogni macchina virtuale e protegge il carico di lavoro presente in tale computer.

Entrambi i metodi presentano vantaggi e svantaggi:

- I backup a livello di host sono flessibili perché funzionano indipendentemente dal tipo di sistema operativo in esecuzione nei computer guest e non richiedono l'installazione dell'agente protezione MAB in ogni macchina virtuale. Se si distribuisce il backup a livello di host, è possibile ripristinare un'intera macchina virtuale o file e cartelle (ripristino a livello di elemento).

- Il backup a livello di Guest è utile se vuoi proteggere specifici carichi di lavoro in esecuzione in una macchina virtuale. A livello di host è possibile ripristinare un'intera macchina virtuale o file specifici, ma non fornirà il ripristino nel contesto di un'applicazione specifica. Ad esempio, per ripristinare elementi specifici di SharePoint da una macchina virtuale di cui è stato eseguito il backup, è necessario eseguire il backup a livello di Guest della macchina virtuale. Usare il backup a livello di Guest se si vuole proteggere i dati archiviati su dischi pass-through. Passthrough consente alla macchina virtuale di accedere direttamente al dispositivo di archiviazione e non archivia i dati del volume virtuale in un file VHD.

## <a name="how-the-backup-process-works"></a>Funzionamento del processo di backup

MAB esegue il backup con VSS come segue. I passaggi descritti in questa descrizione sono numerati per facilitarne la comprensione.

1. Il motore di sincronizzazione basato su blocchi di MAB crea una copia iniziale della macchina virtuale protetta e garantisce che la copia della macchina virtuale sia completa e coerente.

2. Una volta eseguita e verificata la copia iniziale, MAB usa il VSS writer Hyper-V per acquisire i backup. Il VSS writer fornisce un set di blocchi disco coerenti con i dati sincronizzati con il server di MAB. Questo approccio offre il vantaggio di un "backup completo" con il server MAB, riducendo al minimo i dati di backup che devono essere trasferiti attraverso la rete.

3. L'agente protezione di Mab in un server che esegue Hyper-V usa le API Hyper-V esistenti per determinare se una macchina virtuale protetta supporta anche VSS.

   - Se una macchina virtuale soddisfa i requisiti per il backup online e in essa è installato il componente dei servizi di integrazione Hyper-V, il VSS writer di Hyper-V inoltra in modo ricorsivo la richiesta VSS attraverso tutti i processi che riconoscono VSS sulla macchina virtuale. Questa operazione si verifica senza che l'agente protezione di MAB sia installato nella macchina virtuale. La richiesta di VSS ricorsiva consente al writer del servizio Copia Shadow del volume di Hyper-V di garantire che le operazioni di scrittura su disco siano sincronizzate affinché venga acquisito uno snapshot VSS senza perdita di dati.

     Il componente dei servizi di integrazione Hyper-V richiama il writer VSS di Hyper-V in Servizio Copia Shadow del volume (VSS) sulle macchine virtuali per garantire che i relativi dati di applicazione siano in uno stato coerente.

   - Se la macchina virtuale non è conforme ai requisiti di backup online, MAB usa automaticamente le API di Hyper-V per sospendere la macchina virtuale prima di acquisire i file di dati.

4. Dopo la sincronizzazione della copia di base iniziale della macchina virtuale con il server MAB, tutte le modifiche apportate alle risorse della macchina virtuale vengono acquisite in un nuovo punto di ripristino. Il punto di ripristino rappresenta lo stato coerente della macchina virtuale in un momento specifico. Le acquisizioni del punto di ripristino possono verificarsi almeno una volta al giorno. Quando viene creato un nuovo punto di ripristino, MAB utilizza la replica a livello di blocco insieme al VSS writer Hyper-V per determinare quali blocchi sono stati alterati sul server che esegue Hyper-V dopo la creazione dell'ultimo punto di ripristino. Questi blocchi di dati vengono quindi trasferiti al server MAB e applicati alla replica dei dati protetti.

5. Il server MAB utilizza VSS sui volumi che ospitano i dati di ripristino in modo che siano disponibili più copie shadow. Ciascuna di queste copie shadow fornisce un ripristino separato. I punti di ripristino VSS vengono archiviati nel server MAB. La copia temporanea eseguita sul server che esegue Hyper-V viene archiviata solo per la durata della sincronizzazione di MAB.

>[!NOTE]
>
>A partire da Windows Server 2016, i dischi rigidi virtuali Hyper-V hanno un rilevamento delle modifiche incorporato noto come rilevamento delle modifiche resilienti (RCT). MAB USA RCT (il rilevamento delle modifiche nativo in Hyper-V), che riduce la necessità di verifiche di coerenza che richiedono molto tempo in scenari come gli arresti anomali delle macchine virtuali. RCT offre una migliore resilienza rispetto al rilevamento delle modifiche offerto dai backup basati su snapshot di VSS. MABS V3 ottimizza ulteriormente il consumo di archiviazione e rete trasferendo solo i dati modificati durante le verifiche della coerenza.

## <a name="backup-prerequisites"></a>Prerequisiti per il backup

Questi sono i prerequisiti per il backup di macchine virtuali Hyper-V con MAB:

|Prerequisito|Dettagli|
|------------|-------|
|Prerequisiti di MAB|-Se si vuole eseguire il ripristino a livello di elemento per le macchine virtuali (ripristino di file, cartelle, volumi), è necessario installare il ruolo Hyper-V nel server MAB.  Se si vuole solo ripristinare la macchina virtuale e non a livello di elemento, il ruolo non è necessario.<br />-È possibile proteggere fino a 800 macchine virtuali da 100 GB ognuna in un server MAB e consentire più server MAB che supportano cluster più grandi.<br />-MAB esclude il file di paging dai backup incrementali per migliorare le prestazioni del backup delle macchine virtuali.<br />-MAB può eseguire il backup di un server o di un cluster Hyper-V nello stesso dominio del server MAB oppure in un dominio figlio o trusted. Se si vuole eseguire il backup di Hyper-V in un gruppo di lavoro o in un dominio non trusted, è necessario configurare l'autenticazione. Per un server Hyper-V singolo è possibile usare NTLM o l'autenticazione del certificato. Per un cluster, è possibile usare solo l'autenticazione del certificato.<br />-   Non è supportato l'uso del backup a livello di host per eseguire il backup dei dati della macchina virtuale su dischi pass-through. In questo scenario è consigliabile usare il backup a livello di host per eseguire il backup dei file VHD e del backup a livello di Guest per eseguire il backup degli altri dati non visibili nell'host.<br />   -È possibile eseguire il backup delle macchine virtuali archiviate in volumi deduplicati.|
|Prerequisiti delle macchine virtuali Hyper-V|-La versione dei componenti di integrazione in esecuzione nella macchina virtuale deve corrispondere alla versione dell'host Hyper-V. <br />-   Per ogni backup di macchina virtuale sarà necessario avere spazio libero nel volume che ospita i file di disco rigido virtuale per ottenere spazio sufficiente per Hyper-V per i dischi differenze (AVHD) durante il backup. Lo spazio deve essere almeno pari al calcolo **Dimensioni disco iniziali\*Varianza\*Durata backup**. Se si eseguono più backup in un cluster, sarà necessaria una capacità di archiviazione sufficiente per i file AVHD di ogni macchina virtuale, in base a questo calcolo.<br />-Per eseguire il backup di macchine virtuali che si trovano in server host Hyper-V che eseguono Windows Server 2012 R2, è necessario che nella macchina virtuale sia specificato un controller SCSI, anche se non è connesso ad alcun elemento. (In Windows Server 2012 R2 online backup, l'host Hyper-V monta un nuovo disco rigido virtuale nella macchina virtuale e quindi lo smonta in un secondo momento. Solo il controller SCSI può supportare questa operazione e pertanto è necessario per il backup online della macchina virtuale.  Senza questa impostazione, verrà generato l'evento con ID 10103 quando si tenta di eseguire il backup della macchina virtuale.|
|Prerequisiti di Linux|-È possibile eseguire il backup di macchine virtuali Linux con MAB. Sono supportati solo snapshot coerenti a livello di file.|
|Backup di macchine virtuali con archiviazione CSV|-   Per l'archiviazione CSV, installare il provider hardware del Servizio Copia Shadow del volume nel server Hyper-V. Contatta il fornitore della rete di archiviazione (SAN) per ottenere il provider hardware del Servizio Copia Shadow del volume.<br />-Se un singolo nodo si arresta in modo imprevisto in un cluster CSV, viene eseguita una verifica di coerenza per le macchine virtuali in esecuzione in tale nodo.<br />-   Per riavviare un server Hyper-V in cui è abilitato Crittografia unità BitLocker nel server CSV, è necessario eseguire una verifica coerenza per le macchine virtuali Hyper-V.|
|Backup di macchine virtuali con archiviazione SMB|-   Attivare il montaggio automatico sul server che esegue Hyper-V per abilitare la protezione delle macchine virtuali.<br />   -   Disabilitare TCP Chimney Offload.<br />-   Accertarsi che tutti gli account machine$ di Hyper-V dispongano di autorizzazioni complete per le specifiche condivisioni di file SMB remote.<br />-Verificare che il percorso del file per tutti i componenti della macchina virtuale durante il ripristino in un percorso alternativo sia inferiore a 260 caratteri. In caso contrario, il ripristino potrebbe avere esito positivo, ma Hyper-V non sarà in grado di montare la macchina virtuale.<br />-Gli scenari seguenti non sono supportati:<br />     Distribuzioni in cui alcuni componenti della macchina virtuale sono in volumi locali e alcuni componenti si trovano su volumi remoti. un indirizzo IPv4 o IPv6 per il percorso di archiviazione file server e il ripristino di una macchina virtuale in un computer che utilizza condivisioni SMB remote.<br />-È necessario abilitare il servizio file Server VSS Agent in ogni server SMB. aggiungerlo in **Aggiungi ruoli e funzionalità**  >  **Selezione ruoli server**Servizi file  >  **e archiviazione**Servizi file  >  **File Services**  >  **servizio file servizio**file  >  **Server VSS Agent**.|

## <a name="back-up-virtual-machines"></a>Backup di macchine virtuali

1. Configurare il [Server MAB](backup-azure-microsoft-azure-backup.md) e [l'archiviazione](backup-mabs-add-storage.md). Per la configurazione dello spazio di archiviazione, usare queste linee guida sulla capacità di archiviazione.
   - Dimensione media macchina virtuale: 100 GB
   - Numero di macchine virtuali per server MAB-800
   - Dimensione totale di 800 macchine virtuali: 80 TB
   - Spazio richiesto per l'archiviazione di backup: 80 TB

2. Configurare l'agente protezione di MAB nel server Hyper-V o nei nodi del cluster Hyper-V. Se si esegue un backup a livello di Guest, l'agente verrà installato nelle macchine virtuali di cui si vuole eseguire il backup a livello di Guest.

3. Nella console di amministrazione di MAB fare clic su **protezione**dati  >  **Crea gruppo protezione** dati per aprire la procedura guidata **Crea nuovo gruppo protezione** dati.

4. Nella pagina **Selezione membri del gruppo** selezionare le macchine virtuali da proteggere dai server host Hyper-V in cui si trovano. È consigliabile includere tutte le macchine virtuali che avranno gli stessi criteri di protezione in un unico gruppo protezione dati. Per un uso efficiente dello spazio, abilitare la condivisione percorso. La condivisione percorso consente di individuare i dati da diversi gruppi protezione dati nello stesso disco o nella stessa archiviazione su nastro in modo che più origini dati abbiano soltanto una replica e un volume del punto di ripristino.

5. Nella pagina **Seleziona metodo protezione dati** specificare un nome di gruppo di protezione. Selezionare **Protezione dati a breve termine tramite Disco** e **Protezione dati online** se si vuole eseguire il backup in Azure con il servizio Azure Backup.

6. In specificare il periodo di mantenimento degli **obiettivi a breve termine**  >  **Retention range**specificare per quanto tempo si vogliono mantenere i dati del disco. In **frequenza di sincronizzazione**specificare la frequenza con cui eseguire i backup incrementali dei dati. In alternativa, invece di selezionare un intervallo per i backup incrementali, è possibile abilitare **Immediatamente prima di un punto di ripristino**. Se questa impostazione è abilitata, MAB eseguirà un backup completo rapido appena prima di ogni punto di ripristino pianificato.

    > [!NOTE]
    >
    >Se si intende proteggere i carichi di lavoro dell'applicazione, i punti di ripristino vengono creati in base al valore di Frequenza di sincronizzazione se l'applicazione supporta i backup incrementali. In caso contrario, MAB esegue un backup completo rapido, anziché un backup incrementale, e crea punti di ripristino in base alla pianificazione del backup rapido.

7. Nella pagina **Verifica allocazione dischi** esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.

   Le **dimensioni dati totali** sono le dimensioni dei dati di cui si esegue il backup e lo **spazio su disco per il provisioning nel server di Backup di Microsoft Azure** è lo spazio consigliato dal server per il gruppo protezione dati. Il server di Backup di Microsoft Azure sceglie il volume di backup in base alle impostazioni. Tuttavia è possibile modificare le opzioni del volume di backup nei **dettagli di allocazione del disco**. Per i carichi di lavoro, selezionare la risorsa di archiviazione preferita nel menu a discesa. Le modifiche modificano i valori per **Totale spazio di archiviazione** e **Spazio libero di archiviazione** nel riquadro **Spazio di archiviazione su disco disponibile**. Per spazio con provisioning insufficiente si intende la quantità di spazio di archiviazione che il server di Backup di Microsoft Azure suggerisce di aggiungere al volume per continuare a eseguire backup uniformi in futuro.

8. Nella pagina **Scelta del metodo per la creazione della replica** specificare come verrà eseguita la replica iniziale dei dati nel gruppo protezione dati. Se si sceglie di eseguire **la replica automatica in rete**, è consigliabile scegliere un orario di minore traffico. Per grandi quantità di dati o condizioni di rete non ottimali, è consigliabile selezionare **manualmente**, che richiede la replica dei dati offline mediante supporti rimovibili.

9. Nella pagina **Opzioni di verifica coerenza** scegliere come automatizzare le verifiche della coerenza. È possibile fare in modo che una verifica venga eseguita solo quando i dati della replica diventano incoerenti o in base a una pianificazione. Se non si vuole configurare la verifica coerenza automatica, è possibile eseguire una verifica manuale in qualsiasi momento facendo clic con il pulsante destro del mouse sul gruppo protezione dati e scegliendo **Esegui verifica coerenza**.

    Dopo aver creato il gruppo protezione dati, viene eseguita la replica iniziale dei dati in base al metodo selezionato. Dopo la replica iniziale, ogni backup viene eseguito in base alle impostazioni del gruppo protezione dati. Se è necessario ripristinare i dati di cui è stato eseguito il backup, tenere presente quanto segue:

## <a name="back-up-replica-virtual-machines"></a>Backup di macchine virtuali di replica

Se MAB è in esecuzione in Windows Server 2012 R2 o versione successiva, è possibile eseguire il backup di macchine virtuali di replica. Questo passaggio risulta utile per diversi motivi:

**Si riduce l'impatto del backup sull'esecuzione del carico di lavoro**: l'esecuzione del backup di una macchina virtuale comporta un sovraccarico quando viene creato uno snapshot. Con l'offload del processo di backup in un sito remoto secondario, il carico di lavoro in esecuzione non è più interessato dall'operazione di backup. Questo è applicabile solo alle distribuzioni in cui la copia di backup è archiviata in un sito remoto. Ad esempio, potresti eseguire backup giornalieri e archiviare i dati localmente per assicurarti tempi di ripristino rapidi, ma eseguire backup mensili o trimestrali dalle macchine virtuali di replica archiviate in remoto per la conservazione a lungo termine.

**Si risparmia larghezza di banda**: in una distribuzione tipica con sedi centrali/filiali remote, è necessario il provisioning di una quantità appropriata di larghezza di banda per trasferire i dati di backup da un sito a un altro. Se si crea una strategia di replica e di failover, oltre alla strategia di backup dei dati, è possibile ridurre la quantità di dati ridondanti inviati in rete. Eseguendo il backup dei dati della macchina virtuale di replica anziché di quelli primari, si evita il sovraccarico dovuto all'invio dei dati di cui è stato eseguito il backup tramite la rete.

**Si abilita il backup di host**: è possibile usare un data center ospitato come sito di replica, senza che sia necessario un data center secondario. In questo caso, il contratto di Service del provider di hosting richiede il backup coerente delle macchine virtuali di replica.

Una macchina virtuale di replica è disattivata fino a quando non viene avviato un failover e il servizio Copia Shadow del volume non può garantire un backup coerente con le applicazioni per una macchina virtuale di replica. Il backup di una macchina virtuale di replica sarà quindi solo coerente con un arresto anomalo del sistema. Se non è possibile garantire la coerenza con un arresto anomalo del sistema, il backup avrà esito negativo. Questa situazione può verificarsi in una serie di condizioni:

- La macchina virtuale di replica non è integra e si trova in uno stato critico.

- La macchina virtuale di replica sta eseguendo la risincronizzazione, si trova cioè nello stato Risincronizzazione in corso o Risincronizzazione richiesta.

- La replica iniziale tra il sito primario e il sito secondario è in corso o in sospeso per la macchina virtuale.

- è in corso l'applicazione dei log HRL alla macchina virtuale di replica oppure un'azione precedente per applicare i log HRL nel disco virtuale non è riuscita oppure è stata annullata o interrotta.

- È in corso la migrazione o il failover della macchina virtuale di replica.

## <a name="recover-backed-up-virtual-machines"></a>Ripristinare il backup di macchine virtuali

Quando è possibile ripristinare una macchina virtuale sottoposto a backup, usare il Ripristino guidato per selezionare la macchina virtuale e il punto di ripristino specifico. Per aprire il Ripristino guidato e ripristinare una macchina virtuale:

1. Nella console di amministrazione di MAB digitare il nome della macchina virtuale oppure espandere l'elenco degli elementi protetti e selezionare la macchina virtuale che si desidera ripristinare.

2. Nel riquadro **Punti di ripristino per**, nel calendario, fare clic su una data per visualizzare i punti di ripristino disponibili. Quindi nel riquadro **Percorso** selezionare il punto di ripristino da usare nel Ripristino guidato.

3. Nel menu **Azioni** fare clic su **Ripristina** per aprire il Ripristino guidato.

    La macchina virtuale e il punto di ripristino selezionato vengono visualizzati nella schermata **Verifica selezione per ripristino**. Fare clic su **Avanti**.

4. Nella schermata **Selezione tipo di ripristino** selezionare la posizione in cui si vuole ripristinare i dati e quindi fare clic su **Avanti**.

    - **Ripristina nell'istanza originale**: quando viene ripristinato nel percorso originale, il file VHD originale viene eliminato. MAB Recupera il VHD e altri file di configurazione nel percorso originale usando Hyper-V VSS writer. Alla fine del processo di ripristino le macchine virtuali risultano ancora a disponibilità elevata.
        Per il ripristino, è necessario che sia presente il gruppo di risorse. Se non è disponibile, eseguire il ripristino in un percorso alternativo e quindi rendere la macchina virtuale a disponibilità elevata.

    - **Ripristina come macchina virtuale in qualsiasi host**: MAB supporta il ripristino in un percorso alternativo (ALR), che fornisce un ripristino trasparente di una macchina virtuale Hyper-v protetta in un host Hyper-v diverso, indipendentemente dall'architettura del processore. Le macchine virtuali Hyper-V ripristinate in un nodo cluster non saranno a disponibilità elevata. Se si sceglie questa opzione, il Ripristino guidato visualizza una schermata aggiuntiva per identificare la destinazione e il percorso di destinazione.

    - **Copia in una cartella di rete**: MAB supporta il ripristino a livello di elemento (ILR), che consente di eseguire il ripristino a livello di elemento di file, cartelle, volumi e dischi rigidi virtuali (VHD) da un backup a livello di host di macchine virtuali Hyper-V in una condivisione di rete o in un volume di un server protetto da MAB. Non è necessario installare l'agente protezione di MAB nel guest per eseguire il ripristino a livello di elemento. Se si sceglie questa opzione, il Ripristino guidato visualizza una schermata aggiuntiva per identificare la destinazione e il percorso di destinazione.

5. In **Specifica opzioni di ripristino** configurare le opzioni di ripristino e fare clic su **Avanti**:

    - Se si ripristina una macchina virtuale sulla larghezza di banda ridotta, fare clic su **Modifica** per abilitare **Limitazione all'utilizzo della larghezza di banda**. Dopo aver attivato l'opzione di limitazione, è possibile specificare la quantità di larghezza di banda che si vuole rendere disponibile e l'ora in cui deve essere disponibile.
    - Selezionare **Abilita recupero basato su SAN tramite snapshot dell'hardware** se è stata configurata la rete.
    - Selezionare **Invia messaggio di posta elettronica al termine del ripristino** e quindi specificare gli indirizzi di posta elettronica, se si vuole che le notifiche di posta elettronica vengano inviate al termine del processo di ripristino.

6. Nella schermata di riepilogo, verificare che tutti i dettagli siano corretti. Se i dettagli non sono corretti o si vuole apportare una modifica, fare clic su **Indietro**. Se si è soddisfatti con le impostazioni, fare clic su **Ripristina** per avviare il processo di ripristino.

7. La schermata **Stato ripristino** fornisce informazioni sul processo di ripristino.

## <a name="next-steps"></a>Passaggi successivi

[Ripristinare i dati da un server di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
