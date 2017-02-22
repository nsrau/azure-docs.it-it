---
title: Failover in Site Recovery | Documentazione Microsoft
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: b70d2d709d0940964c4220b798207adaf3551d36
ms.openlocfilehash: d31e987e636b178b5aa05722ff08707a6e53c3cf


---
# <a name="failover-in-site-recovery"></a>Failover in Site Recovery
Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una rapida panoramica, leggere [Che cos'è Azure Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Panoramica
In questo articolo vengono fornite informazioni e istruzioni per il ripristino (failover e failback) di macchine virtuali e server fisici protetti con Site Recovery.

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="types-of-failover"></a>Tipi di failover
Quando la protezione delle macchine virtuali e dei server fisici è abilitata e questi sono in grado di eseguire repliche, è possibile eseguire failover in caso di necessità. Site Recovery supporta diversi tipi di failover.

| **Failover** | **Quando eseguire** | **Dettagli** | **Processo** |
| --- | --- | --- | --- |
| **Failover di test** |Eseguire per convalidare la strategia di replica o per eseguire un'esercitazione per il ripristino d'emergenza |Senza perdita di dati e tempi di inattività<br/><br/>Nessun impatto sulla replica<br/><br/>Nessun impatto sull'ambiente di produzione |Avviare il failover<br/><br/>Specificare come le macchine di test verranno connesse alle reti dopo il failover<br/><br/>Tenere traccia dell'avanzamento sulla scheda **Processi** . Le macchine di test vengono create e avviate nella posizione secondaria<br/><br/>Azure: connettersi alla macchina nel portale di Azure<br/><br/>Sito secondario: accedere alla macchina nello stesso host e cloud<br/><br/>Completare il test e rimuovere automaticamente le impostazioni del failover di test. |
| **Failover pianificato** |Eseguire per soddisfare i requisiti di conformità<br/><br/>Eseguire per la manutenzione pianificata<br/><br/>Eseguire per effettuare il failover dei dati per mantenere i carichi di lavoro in esecuzione per le interruzioni note, ad esempio un'interruzione dell'alimentazione prevista o bollettini meteo negativi<br/><br/>Eseguire per effettuare il failback dopo il failover da primario a secondario |Nessuna perdita di dati<br/><br/>Il tempo di inattività si verifica nel tempo necessario per arrestare la macchina virtuale nella posizione primaria e riavviarla nella posizione secondaria.<br/><br/>L'impatto sulle macchine virtuali si ha quando le macchine di destinazione diventano macchine di origine dopo il failover. |Avviare il failover<br/><br/>Tenere traccia dell'avanzamento sulla scheda **Processi** . Le macchine di origine vengono arrestate<br/><br/>Le macchine di replica vengono avviate nella posizione secondaria<br/><br/>Azure: connettersi alla macchina di replica nel portale di Azure<br/><br/>Sito secondario: accedere alla macchina sullo stesso host e nello stesso cloud<br/><br/>Eseguire il commit del failover |
| **Failover non pianificato** |Eseguire questo tipo di failover in modo reattivo quando un sito primario diventa inaccessibile a causa di un evento imprevisto, ad esempio un’interruzione dell’alimentazione o un attacco di virus. <br/><br/> È possibile eseguire un failover non pianificato anche se il sito primario non è disponibile. |Perdita di dati dovuta alle impostazioni della frequenza di replica<br/><br/>I dati verranno aggiornati in base all'ultima sincronizzazione |Avviare il failover<br/><br/>Tenere traccia dell'avanzamento sulla scheda **Processi** . Facoltativamente, provare ad arrestare le macchine virtuali e a sincronizzare gli ultimi dati<br/><br/>Le macchine di replica vengono avviate nella posizione secondaria<br/><br/>Azure: connettersi alla macchina di replica nel portale di Azure<br/><br/>Sito secondario: accedere alla macchina sullo stesso host e nello stesso cloud<br/><br/>Eseguire il commit del failover |

I tipi di failover supportati dipendono dallo scenario di distribuzione.

| **Direzione del failover** | **Failover di test** | **Failover pianificato** | **Failover non pianificato** |
| --- | --- | --- | --- |
| Da sito VMM primario a sito VMM |Supportato |Supportato |Supportato |
| Da sito VMM secondario a sito VMM primario |Supportato |Supportato |Supportato |
| Da cloud a cloud (singolo server VMM) |Supportato |Supportato |Supportato |
| Da sito VMM ad Azure |Supportato |Supportato |Supportato |
| Da Azure a sito VMM |Non supportato |Supportato |Non supportato |
| Da sito Hyper-V ad Azure |Supportato |Supportato |Supportato |
| Da Azure a sito Hyper-V |Non supportato |Supportato |Non supportato |
| Da sito VMware ad Azure |Supportato (scenario avanzato)<br/><br/> Non supportato (scenario legacy) |Non supportato |Supportato |
| Da server fisico ad Azure |Supportato (scenario avanzato)<br/><br/> Non supportato (scenario legacy) |Non supportato |Supportato |

## <a name="failover-and-failback"></a>Failover e failback
Si esegue il failover delle macchine virtuali in un sito secondario in locale o in Azure, a seconda della distribuzione. Un computer che esegue il failover in Azure viene creato come macchina virtuale di Azure. È possibile eseguire il failover di una singola macchina virtuale o server fisico o un piano di ripristino. Il piano di ripristino è costituito da uno o più gruppi ordinati che contengono macchine virtuali o server fisici protetti. Vengono utilizzati per orchestrare il failover di più macchine che eseguono il failover in base al gruppo a cui appartengono. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

Dopo aver completato il failover, quando le  macchine sono attive e in esecuzione in una posizione secondaria, si noti che:

* Se si è eseguito il failover in Azure, dopo il failover le macchine non sono protette e non eseguono la replica nella posizione primaria o secondaria. In Azure le macchine virtuali vengono archiviate in risorse di archiviazione replicate geograficamente, che forniscono la resilienza, ma non la replica.
* Se è stato eseguito un failover non pianificato in un sito secondario, dopo il failover le macchine della posizione secondaria non sono protette e non eseguono la replica.
* Se è stato eseguito un failover pianificato in un sito secondario, dopo il failover le macchine della posizione secondaria sono protette.

### <a name="failback-from-secondary-site"></a>Failback dal sito secondario
Il failback da un sito secondario a un sito primario utilizza lo stesso processo del failover da sito primario a secondario. Dopo aver eseguito il commit e completato il failover dal centro dati primario a quello secondario, è possibile avviare la replica inversa quando il sito primario diventa disponibile. La replica inversa avvia la replica tra il sito secondario e primario sincronizzando i dati differenziali. La replica inversa porta le macchine virtuali in uno stato protetto, ma il centro dati secondario resta la posizione attiva. Per rendere il sito primario la posizione attiva, occorre avviare un failover pianificato da sito secondario a primario, seguito da un'altra replica inversa.

### <a name="failback-from-azure"></a>Failback da Azure
Se è stato eseguito il failover in Azure le macchine virtuali sono protette dalle funzionalità di resilienza di Azure per le macchine virtuali. Per rendere il sito primario originale la posizione attiva, occorre eseguire un failover pianificato. Se il sito originale non è disponibile, è possibile eseguire il failback nella posizione originale o in una posizione alternativa. Per avviare di nuovo la replica dopo il failback nella posizione primaria è possibile avviare la replica inversa.

### <a name="failover-considerations"></a>Considerazioni sul failover
* **Indirizzo IP dopo il failover**: per impostazione predefinita, una macchina di failover avrà un indirizzo IP diverso dal computer di origine. Se si desidera mantenere lo stesso indirizzo IP, vedere:
  * **Sito secondario**: se si esegue il failover a un sito secondario e si desidera mantenere un indirizzo IP, [leggere](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) questo articolo. Si noti che, se il provider di servizi Internet lo consente, è possibile mantenere un indirizzo IP pubblico.
  * **Azure**: se si esegue il failover in Azure è possibile specificare l'indirizzo IP che si desidera assegnare nella scheda **Configura** delle proprietà della macchina virtuale. Non è possibile conservare un indirizzo IP pubblico dopo il failover ad Azure. È possibile mantenere gli spazi degli indirizzi RFC 1918 utilizzati come indirizzi interni.
* **Failover parziale**: se si desidera eseguire il failover di parte di un sito anziché di un intero sito, si noti che:

  * **Sito secondario**: se si esegue il failover di parte di un sito primario a un sito secondario e si desidera riconnettersi al sito primario, utilizzare una connessione VPN da sito a sito per collegare le applicazioni del failover nel sito secondario con i componenti infrastrutturali in esecuzione nel sito primario. In caso di failover di un'intera subnet, è possibile mantenere l'indirizzo IP della macchina virtuale. Se si esegue il failover su una subnet parziale, non è possibile mantenere l'indirizzo IP della macchina virtuale perché le subnet non possono essere suddivise tra siti.
  * **Azure**: se si esegue il failover parziale di un sito in Azure e si desidera riconnettersi al sito primario, è possibile utilizzare una VPN da sito a sito per la connessione di un’applicazione di failover in Azure ai componenti dell'infrastruttura in esecuzione nel sito primario. Si noti che in caso di failover dell'intera subnet, è possibile conservare l'indirizzo IP della macchina virtuale. Se si esegue il failover su una subnet parziale, non è possibile mantenere l'indirizzo IP della macchina virtuale perché le subnet non possono essere suddivise tra siti.
* **Lettera di unità**: se si vuole mantenere la lettera di unità nelle macchine virtuali dopo il failover è possibile impostare il criterio SAN per la macchina virtuale su **Attivo**. I dischi delle macchine virtuali passano automaticamente online. [Altre informazioni](https://technet.microsoft.com/library/gg252636.aspx).
* **Instradare le richieste client**: Site Recovery funziona con Gestione traffico di Azure per indirizzare le richieste client all'applicazione dopo il failover.

## <a name="run-a-test-failover"></a>Eseguire un failover di test
Fare riferimento al documento [Test failover to Azure](site-recovery-test-failover-to-azure.md) (Failover di test in Azure) se si esegue un failover di test in Azure. Fare riferimento al documento [Test Failover to VMM](site-recovery-test-failover-vmm-to-vmm.md) (Failover di test in VMM) se si esegue il failover di test a un altro sito locale gestito da un server VMM.  

## <a name="run-a-planned-failover-primary-to-secondary"></a>Eseguire un failover pianificato (da primario a secondario)
 Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuali** .

1. Prima di iniziare verificare che tutte le macchine virtuali di cui eseguire il failover abbiano completato la replica iniziale.
2. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Planned Failover**.
3. Nella pagina **Conferma failover pianificato **selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover.

   * Se il funzionamento dei failover è quello previsto e tutti i server di macchine virtuali si trovano nella posizione di origine o di destinazione, i dettagli della direzione del failover sono solo a scopo informativo.
   * Se le macchine virtuali sono attive sia nella posizione di origine che in quella di destinazione, viene visualizzato il pulsante **Cambia direzione** . Utilizzare questo pulsante per modificare e specificare la direzione in cui deve essere eseguito il failover.
4. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Quando si avvia un failover pianificato, il primo passaggio è l’arresto delle macchine virtuali per garantire che non si verifichino perdite di dati. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover. Se si verifica un errore nel failover (in una macchina virtuale o in uno script incluso nel piano di ripristino), il failover pianificato di un piano di ripristino si interrompe. È possibile avviare nuovamente il failover.
6. Dopo la replica le macchine virtuali create sono in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
7. Una volta completata la replica, le macchine virtuali vengono avviate nella posizione secondaria.

## <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato
Questa procedura descrive come eseguire un failover non pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale o server fisico nella scheda **Macchine virtuali** .

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Unplanned Failover**.
2. Nella pagina **Conferma failover non pianificato **selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover.

   * Se il funzionamento dei failover è quello previsto e tutti i server di macchine virtuali si trovano nella posizione di origine o di destinazione, i dettagli della direzione del failover sono solo a scopo informativo.
   * Se le macchine virtuali sono attive sia nella posizione di origine che in quella di destinazione, viene visualizzato il pulsante **Cambia direzione** . Utilizzare questo pulsante per modificare e specificare la direzione in cui deve essere eseguito il failover.
3. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
4. Selezionare **Arresta le macchine virtuali e sincronizza i dati più recenti** per specificare che Site Recovery deve tentare di arrestare le macchine virtuali protette e sincronizzare i dati in modo che venga eseguito il faiolver dei dati più recenti. Se non si seleziona questa opzione o il tentativo ha esito negativo, il failover avverrà dall'ultimo punto di ripristino disponibile per la macchina virtuale.
5. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover. Si noti che anche se si verificano errori durante un failover non pianificato, il piano di ripristino viene eseguito fino al completamento.
6. Dopo il failover le macchine virtuali sono uno stato di **attesa d commit** . Fare clic su **Commit** per eseguire il commit del failover.
7. Se si imposta la replica per l’uso di più punti di ripristino, in Modifica punto di ripristino è possibile scegliere di utilizzare un punto di ripristino che non sia la versione più recente (per impostazione  predefinita viene utilizzata la versione più recente). Dopo il commit i punti di ripristino aggiuntivi verranno rimossi.
8. Una volta completata la replica, le macchine virtuali vengono avviate ed eseguite nella posizione secondaria. Tuttavia, non sono protette e non eseguono repliche. Quando il sito primario è di nuovo disponibile con la stessa infrastruttura sottostante, fare clic su **Replica inversa** per iniziare la replica inversa. Ciò garantisce che tutti i dati vengono replicati nel sito primario e che la macchina virtuale sia nuovamente pronta per il failover. La replica inversa dopo un failover non pianificato comporta un sovraccarico nel trasferimento dei dati. Il trasferimento utilizzerà lo stesso metodo configurato per le impostazioni di replica iniziale per il cloud.

## <a name="failback-from-secondary-to-primary"></a>Failback da sito secondario a primario
 Dopo il failover dalla posizione primaria alla posizione secondaria, le macchine virtuali replicate non sono protette da Site Recovery e la posizione secondaria funge da posizione primaria. Utilizzare queste procedure per eseguire il failback al sito primario originale. Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuali** .

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Planned Failover**.
2. Nella pagina **Conferma failover pianificato **selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover. Se il failover dal sito primario funziona nel modo previsto e tutte le macchine virtuali si trovano nella posizione secondaria, la direzione ha solo scopo informativo.
3. Se si esegue il failback da Azure, selezionare le impostazioni in **Sincronizzazione dati**:

   * **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**: questa opzione riduce al minimo i tempi di inattività delle macchine virtuali poiché le sincronizza senza arrestarle. Effettua le seguenti operazioni:
     * Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
     * Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.

    - **Sincronizza i dati durante il failover (download completo)**: usare questa opzione se Azure è in esecuzione da molto tempo. Questa opzione è più veloce perché è prevedibile che la maggior parte del disco sia cambiata e non si intende sprecare tempo nel calcolo del checksum. Esegue il download del disco. Questa opzione è utile anche se la macchina virtuale locale è stata eliminata.

    > [!NOTE] 
    > È consigliabile usare questa opzione se si usa Azure da molto tempo (un mese o oltre) o se la macchina virtuale locale è stata eliminata. Questa opzione non esegue alcun calcolo del checksum.
    >
    >


1. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
2. Per impostazione predefinita viene utilizzato l'ultimo punto di ripristino, ma in **Modifica punto di ripristino** è possibile specificare un punto di ripristino diverso.
3. Fare clic sul segno di spunta per avviare il ripristino.  Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
4. Se è stata selezionata l'opzione per sincronizzare i dati prima del failover, dopo la sincronizzazione dati iniziale, quando si è pronti ad arrestare le macchine virtuali in Azure, fare clic su **Processi** > <planned failover job name> **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata.
5. A questo punto è possibile accedere alla macchina virtuale per verificare che sia disponibile come previsto.
6. La macchina virtuale è in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
7. Per completare il failback fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale nel sito primario.

## <a name="failback-to-an-alternate-location"></a>Failback in una posizione alternativa
Se è stata distribuita la protezione tra un [sito Hyper-V e Azure](site-recovery-hyper-v-site-to-azure.md) , è possibile eseguire il failback da Azure a una posizione alternativa locale. Ciò è utile se è necessario configurare nuovo hardware locale. Di seguito viene indicato come procedere.

1. Se si configura nuovo hardware, installare Windows Server 2012 R2 e il ruolo Hyper-V nel server.
2. Creare un commutatore di rete virtuale con lo stesso nome presente nel server originale.
3. Selezionare **Elementi protetti** -> **Gruppo protezione dati** -> <ProtectionGroupName> -> <VirtualMachineName>di cui si vuole eseguire il failback e selezionare **Failover pianificato**.
4. Fare clic su **Conferma failover pianificato** select **Crea macchina virtuale locale, se non esiste**.
5. In **Nome host** selezionare il nuovo server host Hyper-V in cui si desidera collocare la macchina virtuale.
6. In Sincronizzazione dati è consigliabile selezionare l'opzione **Sincronizzare i dati prima del failover**. Questa opzione riduce al minimo i tempi di inattività per le macchine virtuali senza arrestarle. Effettua le seguenti operazioni:

   * Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
   * Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Quando è stata completata la sincronizzazione iniziale e si è pronti per arrestare la macchina virtuale in Azure, fare clic su **Processi** > <planned failover job> > **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata.
9. È possibile accedere alla macchina virtuale locale per verificare che tutto funzioni come previsto. Fare clic su **Commit** per completare il failover.
10. Fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale in locale.

    > [!NOTE]
    > Se si annulla il processo di failback mentre durante la fase di sincronizzazione dei dati, la macchina virtuale locale verrà corrotta. Questo avviene perché la sincronizzazione dei dati copia i dati più recenti dai dischi della macchina virtuale di Azure sui dischi dei dati locali e, fino al completamento della sincronizzazione, il disco dati potrebbe non trovarsi in uno stato coerente. La macchina virtuale locale potrebbe non avviarsi dopo aver annullato la sincronizzazione dei dati. Riattivare il failover per completare la sincronizzazione dei dati.
    >
    >



<!--HONumber=Jan17_HO2-->


