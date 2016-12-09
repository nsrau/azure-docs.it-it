---
title: Automatizzare il ripristino di emergenza per le condivisioni file in StorSimple usando Azure Site Recovery| Microsoft Docs
description: Descrive i passaggi e le procedure per la creazione di una soluzione di ripristino di emergenza per le condivisioni file nell&quot;archiviazione StorSimple.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 9cf39b922e51dd60b5b2c51cb7030436c60232a5
ms.openlocfilehash: d454c138b75bbf3227dd1d5f52594e3c78e15766


---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Soluzione di ripristino di emergenza automatizzato usando Azure Site Recovery per le condivisioni file ospitate su StorSimple
## <a name="overview"></a>Panoramica
Microsoft Azure StorSimple è una soluzione di archiviazione cloud ibrida che risolve le complessità dei dati non strutturati comunemente associate alle condivisioni file. StorSimple usa l'archiviazione cloud come un'estensione della soluzione locale e organizza automaticamente i dati in livelli tra archiviazione locale e archiviazione cloud. La protezione integrata dei dati, con snapshot locali e cloud, elimina la necessità di un'infrastruttura di archiviazione complessa.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) è un servizio basato su Azure che fornisce funzionalità di ripristino di emergenza per il coordinamento di replica, failover e ripristino delle macchine virtuali. Azure Site Recovery supporta una serie di tecnologie di replica per la replica, la protezione e il failover semplice di macchine virtuali e applicazioni nei cloud pubblici, privati oppure ospitati.

Usando Azure Site Recovery, la replica delle macchine virtuali e le funzionalità di snapshot cloud di StorSimple, è possibile proteggere l'ambiente di server di file completo. In caso di interruzione, con un semplice clic è possibile portare le condivisioni di file online in Azure in pochi minuti.

Questo documento illustra in dettaglio come creare una soluzione di ripristino di emergenza per le condivisioni file ospitate nell'archiviazione di StorSimple ed eseguire failover pianificati, non pianificati e di test usando un piano di ripristino con un solo clic. In pratica, mostra come modificare il piano di ripristino dell'insieme di credenziali di Azure Site Recovery per attivare i failover StorSimple durante gli scenari di emergenza. Inoltre, descrive le configurazioni supportate e i prerequisiti. Questo documento presuppone la conoscenza delle nozioni di base delle architetture di Azure Site Recovery e StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opzioni di distribuzione di Azure Site Recovery
I clienti possono distribuire file server come server fisici o macchine virtuali (VM) in esecuzione in Hyper-V o VMware e quindi creare le condivisioni file da volumi ottenuti dall'archiviazione StorSimple. Azure Site Recovery può proteggere le distribuzioni fisiche e virtuali in un sito secondario o in Azure. Questo documento descrive in dettaglio una soluzione di ripristino di emergenza che usa Azure come sito di ripristino per una VM di file server ospitata in Hyper-V e con condivisioni file nell'archiviazione di StorSimple. Altri scenari in cui la VM del file server si trova in una VM VMware o in un computer fisico possono essere implementati in modo analogo.

## <a name="prerequisites"></a>Prerequisiti
I prerequisiti di implementazione di una soluzione di ripristino di emergenza in un solo clic che usa Azure Site Recovery per le condivisioni file ospitate nell'archiviazione StorSimple sono i seguenti:

* VM del file server Windows Server 2012 R2 locale ospitata in Hyper-V, VMware o in un computer fisico
* Dispositivo di archiviazione StorSimple locale registrato con Azure StorSimple Manager
* Appliance cloud StorSimple creata in Azure StorSimple Manager (può rimanere in stato di arresto)
* Condivisioni file ospitate nei volumi configurati sul dispositivo di archiviazione StorSimple
* [insieme di credenziali dei servizi di Azure Site Recovery](../site-recovery/site-recovery-vmm-to-vmm.md) creato in una sottoscrizione di Microsoft Azure

Inoltre, se Azure è il sito di ripristino, eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://azure.microsoft.com/downloads/vm-readiness-assessment/) nelle VM per assicurarsi che siano compatibili con le VM di Azure e i Servizi di Azure Site Recovery.

Per evitare problemi di latenza (che potrebbero causare un aumento dei costi), assicurarsi di creare l'appliance cloud StorSimple, l'account di automazione e gli account di archiviazione nella stessa area.

## <a name="enable-dr-for-storsimple-file-shares"></a>Abilitare il ripristino di emergenza per le condivisioni file di StorSimple
Ogni componente dell'ambiente locale deve essere protetto per abilitare la replica e il ripristino completi. Questa sezione illustra come:

* Configurare la replica di Active Directory e DNS (facoltativo)
* Usare Azure Site Recovery per abilitare la protezione della VM del file server
* Abilitare la protezione dei volumi StorSimple
* Configurare la rete

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurare la replica di Active Directory e DNS (facoltativo)
Se si vuole proteggere i computer che eseguono Active Directory e DNS in modo che siano disponibili nel sito di ripristino di emergenza, è necessario proteggerli in modo esplicito (in modo che i file server siano accessibili dopo il failover con autenticazione). Sono consigliate due opzioni a seconda della complessità dell'ambiente locale del cliente.

#### <a name="option-1"></a>Opzione 1
Se il cliente ha un numero limitato di applicazioni, un singolo controller di dominio per l'intero sito locale ed eseguirà il failover dell'intero sito, è consigliabile usare la replica di Azure Site Recovery per replicare il computer del controller di dominio nel sito secondario (applicabile sia per l'operazione da sito a sito, che da sito ad Azure).

#### <a name="option-2"></a>Opzione 2
Se il cliente ha un numero elevato di applicazioni, esegue una foresta Active Directory ed eseguirà il failover di poche applicazioni alla volta, è consigliabile configurare un controller di dominio aggiuntivo nel sito di ripristino di emergenza (indipendentemente dal fatto che si esegua il failover in un sito secondario o in Azure).

Consultare [Proteggere Active Directory e DNS con Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) per informazioni su come procedere quando si rende disponibile un controller di dominio nel sito di ripristino di emergenza. Nella parte restante di questo documento, si presuppone che sia disponibile un controller di dominio nel sito di ripristino di emergenza.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Usare Azure Site Recovery per abilitare la protezione della VM del file server
Questo passaggio richiede la preparazione dell'ambiente di file server locale, la creazione e la preparazione di un insieme di credenziali di Azure Site Recovery e l'abilitazione della protezione di file della VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Per preparare l'ambiente di file server locale
1. Impostare **Controllo dell'account utente** su **Never Notify** (Non notificare mai). Questa operazione è necessaria per poter usare gli script di automazione di Azure per connettere le destinazioni iSCSI dopo il failover eseguito da Azure Site Recovery.

   1. Premere il tasto Windows + Q e cercare **UAC**.
   2. Selezionare **Modifica impostazioni di Controllo dell'account utente**.
   3. Trascinare la barra in basso verso **Non notificare mai**.
   4. Fare clic su **OK**, quindi selezionare **Sì** quando richiesto.

      ![](./media/storsimple-dr-using-asr/image1.png)
2. Installare l'agente VM su ciascuna delle VM del file server. Questa operazione è necessaria per poter eseguire gli script di automazione di Azure sulle VM sottoposte a failover.

   1. [Scaricare l'agente](http://aka.ms/vmagentwin) in `C:\\Users\\<username>\\Downloads`.
   2. Aprire Windows PowerShell in modalità amministratore (Esegui come amministratore) e quindi immettere il comando seguente per passare al percorso di download:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > Il nome file può cambiare a seconda della versione.
      >
      >
3. Fare clic su **Next**.
4. Accettare le **condizioni del contratto** e quindi fare clic su **Avanti**.
5. Fare clic su **Fine**.
6. Creare condivisioni file usando volumi ottenuti dall'archiviazione StorSimple. Per maggiori informazioni, vedere l'articolo [Usare il servizio StorSimple Manager per gestire i volumi](storsimple-manage-volumes.md).

   1. Nelle macchine virtuali locali premere il tasto Windows + Q e cercare **iSCSI**.
   2. Selezionare **Iniziatore iSCSI**.
   3. Selezionare la scheda **Configurazione** e copiare il nome dell'iniziatore.
   4. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
   5. Selezionare la scheda **StorSimple** e quindi selezionare il servizio StorSimple Manager che contiene il dispositivo fisico.
   6. Creare i contenitori di volumi e quindi creare i volumi (questi volumi sono destinati alle condivisione file nella macchine virtuali del file server). Copiare il nome dell'iniziatore e assegnare un nome appropriato per i record di controllo di accesso quando si creano i volumi.
   7. Selezionare la scheda **Configura** e prendere nota dell'indirizzo IP del dispositivo.
   8. Nelle macchine virtuali locali tornare a **Iniziatore iSCSI** e immettere l'indirizzo IP nella sezione connessione rapida. Fare clic su **Connessione rapida** (il dispositivo ora deve essere connesso).
   9. Aprire il portale di gestione di Azure e selezionare la scheda **Volumi e dispositivi** . Fare clic su **Configura automaticamente**. Viene visualizzato il volume appena creato.
   10. Nel portale selezionare la scheda **Dispositivi** e quindi selezionare **Create a New Virtual Device** (Crea un nuovo dispositivo virtuale)  (il dispositivo virtuale verrà usato se si verifica un failover). Il nuovo dispositivo virtuale può essere mantenuto in uno stato offline per evitare costi aggiuntivi. Per portare offline il dispositivo virtuale, passare alla sezione **Macchine virtuali** nel portale e arrestarlo.
   11. Tornare alle macchine virtuali locali e aprire Gestione disco (premere il tasto Windows + X, quindi selezionare **Gestione disco**).
   12. Si noteranno alcuni dischi aggiuntivi (a seconda del numero di volumi creati). Fare clic con il pulsante destro del mouse sul primo disco e scegliere **Inizializza disco**, quindi fare clic su **OK**. Fare doppio clic sulla sezione **Non allocato**, selezionare **Nuovo volume semplice**, assegnare una lettera di unità al volume e completare la procedura guidata.
   13. Ripetere il primo passaggio per tutti i dischi. È ora possibile visualizzare tutti i dischi in **Computer** in Esplora risorse di Windows.
   14. Usare il ruolo Servizi file e archiviazione per creare condivisioni file in tali volumi.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Per creare un insieme di credenziali di Azure Site Recovery
Vedere la [documentazione di Azure Site Recovery](../site-recovery/site-recovery-hyper-v-site-to-azure.md) per iniziare a usare Azure Site Recovery prima di proteggere la VM del file server.

#### <a name="to-enable-protection"></a>Per abilitare la protezione
1. Disconnettere le destinazioni iSCSI dalle macchine virtuali locali che si vuole proteggere con Azure Site Recovery:

   1. Premere il tasto Windows + Q e cercare **iSCSI**.
   2. Selezionare **Configura iniziatore iSCSI**.
   3. Disconnettere il dispositivo StorSimple connesso in precedenza. In alternativa, è possibile disattivare il file server per alcuni minuti quando si abilita la protezione.

   > [!NOTE]
   > In questo modo le condivisioni file saranno temporaneamente non disponibili
   >
   >
2. [Abilitare la protezione delle macchine virtuali](../site-recovery/site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication) della VM del file server dal Portale di Azure Site Recovery.
3. Quando viene avviata la sincronizzazione iniziale, è possibile riconnettere nuovamente la destinazione. Passare all'iniziatore iSCSI, selezionare un dispositivo StorSimple e fare clic su **Connetti**.
4. Quando la sincronizzazione è completa e lo stato della VM è **Protetta**, selezionare la VM, selezionare la scheda **Configura** e aggiornare di conseguenza la rete della VM (si tratta della rete di cui faranno parte le VM sottoposte a failover). Se la scheda non viene visualizzata, la sincronizzazione è ancora in corso.

### <a name="enable-protection-of-storsimple-volumes"></a>Abilitare la protezione dei volumi StorSimple
Se non è stata selezionata l'opzione **Abilita un criterio di backup predefinito per questo volume** per i volumi StorSimple, andare su **Criteri di backup** nel servizio StorSimple Manager e creare un criterio di backup appropriato per tutti i volumi. È consigliabile impostare la frequenza dei backup per l'obiettivo del punto di ripristino (RPO) che si vuole visualizzare per l'applicazione.

### <a name="configure-the-network"></a>Configurare la rete
Per la VM del file server configurare le impostazioni di rete in Azure Site Recovery in modo che le reti di VM siano collegate alla rete di ripristino di emergenza corretta dopo il failover.

È possibile selezionare la VM in **VMM Cloud** (Cloud VMM) o in **Gruppo di protezione** per configurare le impostazioni di rete, come mostrato nell'illustrazione seguente.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino
È possibile creare un piano di ripristino in ASR per automatizzare il processo di failover delle condivisioni file. Se si verifica un'interruzione, è possibile visualizzare le condivisioni file in pochi minuti con un semplice clic. Per abilitare l'automazione, è necessario un account di Automazione di Azure.

#### <a name="to-create-the-account"></a>Per creare l'account
1. Accedere al portale di Azure classico e passare alla sezione **Automazione** .
2. Creare un nuovo account di automazione. Usare la stessa area geografica/area in cui sono stati creati gli account dell'appliance cloud StorSimple e di archiviazione.
3. Fare clic su **Nuovo** &gt; **Servizi app** &gt; **Automazione** &gt; **Runbook** &gt; **From Gallery** (Da raccolta) per importare tutti i runbook obbligatori nell'account di automazione.

   ![](./media/storsimple-dr-using-asr/image3.png)
4. Aggiungere i runbook seguenti dal riquadro **Ripristino di emergenza** nella raccolta:

   * Eseguire il failover dei contenitori dei volumi StorSimple
   * Eliminare i volumi StorSimple dopo il failover di test
   * Montare i volumi nel dispositivo StorSimple dopo il failover
   * Avviare l'appliance virtuale StorSimple
   * Disinstallare l'estensione script personalizzata in una VM di Azure

     ![](./media/storsimple-dr-using-asr/image4.png)
5. Pubblicare tutti gli script selezionando il runbook nell'account di automazione e passando alla scheda **Autore** . Dopo questo passaggio, la scheda **Runbook** verrà visualizzata come segue:

    ![](./media/storsimple-dr-using-asr/image5.png)
6. Nell'account di automazione andare nella scheda **Asset**, fare clic su **Aggiungi impostazione** &gt; **Aggiungi credenziali** e aggiungere le credenziali di Azure (denominare l'asset AzureCredential).

   Usare le credenziali di Windows PowerShell. Queste credenziali devono contenere un nome utente ID organizzazione e una password con accesso alla sottoscrizione di Azure e Multi-Factor Authentication disabilitata. Si tratta di un passaggio necessario per eseguire l'autenticazione per conto dell'utente durante i failover e per visualizzare i volumi del file server nel sito di ripristino di emergenza.
7. Nell'account di automazione selezionare la scheda **Asset**, quindi fare clic su **Aggiungi impostazione** &gt; **Aggiungi variabile** e aggiungere le variabili seguenti. È possibile scegliere di crittografare questi asset. Queste variabili sono specifiche del piano di ripristino. Se il nome del piano di ripristino (che verrà creato nel passaggio successivo) è TestPlan, le variabili devono essere TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName e così via.

   * *RecoveryPlanName***-StorSimRegKey**: la chiave di registrazione per il servizio StorSimple Manager.
   * *RecoveryPlanName***-AzureSubscriptionName**: il nome della sottoscrizione Azure.
   * *RecoveryPlanName***-ResourceName**: il nome della risorsa StorSimple con il dispositivo StorSimple.
   * *RecoveryPlanName***-DeviceName**: il dispositivo che deve essere sottoposto a failover.
   * *RecoveryPlanName***-TargetDeviceName**: l'appliance cloud di StorSimple in cui eseguire il failover dei contenitori.
   * *RecoveryPlanName***-VolumeContainers**: una stringa con valori delimitati da virgole dei contenitori di volumi presenti sul dispositivo che devono essere sottoposti a failover, ad esempio, volcon1, volcon2, volcon3.
   * RecoveryPlanName**-TargetDeviceDnsName**: il nome del servizio del dispositivo di destinazione (disponibile nella sezione **Macchina virtuale**: il nome del servizio è uguale al nome DNS).
   * *RecoveryPlanName***-StorageAccountName**: il nome dell'account di archiviazione in cui verrà archiviato lo script (che deve essere eseguito nella VM sottoposta a failover). Può trattarsi di qualsiasi account di archiviazione in cui sia disponibile spazio per l'archiviazione temporanea dello script.
   * *RecoveryPlanName***-StorageAccountKey**: la chiave di accesso per l'account di archiviazione indicato in precedenza.
   * *RecoveryPlanName***-ScriptContainer**: il nome del contenitore in cui lo script verrà archiviato nel cloud. Se il contenitore non esiste, verrà creato.
   * *RecoveryPlanName***-VMGUIDS**: al momento di proteggere una VM, Azure Site Recovery assegna a ogni VM virtuale un ID univoco che fornisce i dettagli della VM sottoposta a failover. Per ottenere il VMGUID, selezionare la scheda **Servizi di ripristino** e quindi fare clic su **Elemento protetto** &gt; **Gruppi di protezione** &gt; **Macchine** &gt; **Proprietà**. Se sono presenti più VM, aggiungere i GUID come stringa con valori delimitati da virgole.
   * *RecoveryPlanName***-AutomationAccountName**: il nome dell'account di automazione in cui sono stati aggiunti i runbook e gli asset.

   Ad esempio, se il nome del piano di ripristino è fileServerpredayRP, la scheda **Asset** dovrebbe essere visualizzata come segue dopo aver aggiunto tutti gli asset.

   ![](./media/storsimple-dr-using-asr/image6.png)
8. Passare alla sezione **Servizi di ripristino** e selezionare l'insieme di credenziali di Azure Site Recovery creato in precedenza.
9. Selezionare la scheda **Piani di ripristino** e creare un nuovo piano di ripristino come segue:

   a.  Specificare un nome e selezionare un **Gruppo di protezione**appropriato.

   b.  Selezionare le VM dal gruppo di protezione che si vuole includere nel piano di ripristino.

   c.  Dopo la creazione del piano di ripristino selezionarlo per aprire la visualizzazione di personalizzazione del piano di ripristino.

   d.  Selezionare **Arresto di tutti i gruppi**, fare clic su **Script** e scegliere **Add a primary side script before all Group shutdown** (Aggiungi script lato primario prima dell'operazione di arresto di tutti i gruppi).

   e.  Selezionare l'account di automazione (in cui sono stati aggiunti i runbook) e quindi selezionare il runbook **Fail over-StorSimple-Volume-Containers** .

   f.  Fare clic su **Gruppo 1: avvio**, scegliere **Macchine virtuali** e aggiungere le VM che devono essere protette nel piano di ripristino.

   g.  Fare clic su **Gruppo 1: avvio**, scegliere **Script** e aggiungere tutti gli script seguenti nell'ordine come passaggi di **Dopo il gruppo 1**.

   * Runbook Start-StorSimple-Virtual-Appliance
   * Runbook Fail over-StorSimple-volume-containers
   * Runbook Mount-volumes-after-failover
   * Runbook Uninstall-custom-script-extension
10. Aggiungere un'azione manuale dopo i quattro script precedenti nella stessa sezione **Gruppo 1: passaggi successivi** . Questa azione corrisponde al punto in cui è possibile verificare il corretto funzionamento. Questa azione deve essere aggiunta solo come parte del failover di test (selezionare, quindi, solo la casella di controllo **Failover di test** ).
11. Dopo l'azione manuale, aggiungere lo script di pulizia con la stessa procedura usata per gli altri runbook. Salvare il piano di ripristino.

    > [!NOTE]
    > Quando si esegue un failover di test, è necessario verificare tutti gli aspetti del passaggio relativo all'azione manuale perché i volumi StorSimple clonati sul dispositivo di destinazione verranno eliminati come parte della pulizia dopo il completamento dell'azione manuale.
    >
    >

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Eseguire un failover di test
Consultare la guida complementare relativa alla [soluzione di ripristino di emergenza di Active Directory](../site-recovery/site-recovery-active-directory.md) per considerazioni specifiche relative a Active Directory durante il failover di test. L'installazione locale non viene disturbata in alcun modo quando si verifica il failover di test. I volumi StorSimple che erano collegati alla VM locale vengono clonati nell'appliance cloud StorSimple in Azure. A scopo di test, in Azure viene visualizzata una VM alla quale vengono collegati i volumi clonati.

#### <a name="to-perform-the-test-failover"></a>Per eseguire il failover di test
1. Nel portale di Azure classico selezionare il proprio insieme di credenziali di ripristino del sito.
2. Fare clic sul piano di ripristino creato per la VM del file server.
3. Fare clic su **Failover di test**.
4. Selezionare la rete virtuale per avviare il processo di failover di test.

   ![](./media/storsimple-dr-using-asr/image8.png)
5. Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.
6. Al termine delle convalide, fare clic su **Convalide complete**. L'ambiente di failover di test verrà eliminato e sarà possibile completare l'operazione di failover di test.

## <a name="perform-an-unplanned-failover"></a>Eseguire un failover non pianificato
Durante un failover non pianificato, i volumi StorSimple vengono sottoposti a failover nel dispositivo virtuale, una VM viene visualizzata in Azure e i volumi vengono collegati alla VM.

#### <a name="to-perform-an-unplanned-failover"></a>Per eseguire un failover non pianificato
1. Nel portale di Azure classico selezionare il proprio insieme di credenziali di ripristino del sito.
2. Fare clic sul piano di ripristino creato per la VM del file server.
3. Fare clic su **Failover** e quindi selezionare **Failover non pianificato**.

   ![](./media/storsimple-dr-using-asr/image9.png)
4. Selezionare la rete di destinazione e quindi fare clic sull'icona del segno di spunta per avviare il processo di failover.

## <a name="perform-a-planned-failover"></a>Eseguire un failover pianificato
Durante un failover pianificato la VM del file server locale viene arrestata correttamente e viene eseguita una snapshot di un backup su cloud dei volumi nel dispositivo StorSimple. I volumi StorSimple vengono sottoposti a failover nel dispositivo virtuale, una VM di replica viene visualizzata in Azure e i volumi vengono collegati alla VM.

#### <a name="to-perform-a-planned-failover"></a>Per eseguire un failover pianificato
1. Nel portale di Azure classico selezionare il proprio insieme di credenziali di ripristino del sito.
2. Fare clic sul piano di ripristino creato per la VM del file server.
3. Fare clic su **Failover** e quindi selezionare **Failover pianificato**.
4. Selezionare la rete di destinazione e quindi fare clic sull'icona del segno di spunta per avviare il processo di failover.

## <a name="perform-a-failback"></a>Eseguire il failback
Durante il failback, i contenitori di volumi StorSimple vengono sottoposti a failover nel dispositivo fisico dopo un backup.

#### <a name="to-perform-a-failback"></a>Per eseguire un failback
1. Nel portale di Azure classico selezionare il proprio insieme di credenziali di ripristino del sito.
2. Fare clic sul piano di ripristino creato per la VM del file server.
3. Fare clic su **Failover** e selezionare **Failover pianificato** o **Failover non pianificato**.
4. Fare clic su **Cambia direzione**.
5. Selezionare le opzioni di sincronizzazione dei dati e di creazione di VM appropriate.
6. Fare clic sull'icona del segno di spunta per avviare il processo di failback.

   ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Procedure consigliate
### <a name="capacity-planning-and-readiness-assessment"></a>Pianificazione della capacità e valutazione della conformità
#### <a name="hyper-v-site"></a>Sito di Hyper-V
Usare lo [strumento Capacity Planner](http://www.microsoft.com/download/details.aspx?id=39057) per progettare l'infrastruttura di server, archiviazione e rete per l'ambiente di replica Hyper-V.

#### <a name="azure"></a>Azure
È possibile eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://azure.microsoft.com/downloads/vm-readiness-assessment/) nelle VM per assicurarsi che siano compatibili con le VM di Azure e i Servizi di Azure Site Recovery. Lo strumento Readiness Assessment controlla le configurazioni delle macchine virtuali e visualizza un avviso quando le configurazioni non sono compatibili con Azure. Ad esempio, genera un avviso se un'unità C: è maggiore di 127 GB.

La pianificazione della capacità prevede almeno due processi importanti:

* Mapping delle VM Hyper-V locali alle dimensioni delle VM di Azure (ad esempio A6, A7, A8 e A9).
* Determinazione della larghezza di banda Internet necessaria.

## <a name="limitations"></a>Limitazioni
* Attualmente, solo un dispositivo StorSimple può essere sottoposto a failover (per un singolo appliance cloud StorSimple). Lo scenario di un file server che si estende a più dispositivi StorSimple non è ancora supportato.
* Se si verifica un errore durante l'abilitazione della protezione per una VM, assicurarsi di aver disconnesso le destinazioni iSCSI.
* Tutti i contenitori di volumi che sono stati raggruppati per effetto dei criteri di backup estesi ai vari contenitori di volumi verranno sottoposti a failover insieme.
* Tutti i volumi dei contenitori di volumi scelti verranno sottoposti a failover.
* I volumi superiori a 64 TB non possono essere sottoposti a failover perché la capacità massima di una singola appliance cloud StorSimple è di 64 TB.
* Se il failover pianificato o non pianificato non riesce e le VM vengono create in Azure, non eliminare le VM, ma eseguire un failback. Se si eliminano le VM, le VM locali non possono essere riattivate.
* Dopo un failover, se non si riesce a visualizzare i volumi, passare alle VM, aprire Gestione disco, eseguire nuovamente la scansione dei dischi e portarli online.
* In alcuni casi, le lettere delle unità nel sito di ripristino di emergenza potrebbero essere diverse da quelle delle VM locali. In questo caso, sarà necessario correggere manualmente il problema al termine del failover.
* Multi-Factor Authentication deve essere disabilitata per le credenziali di Azure immesse nell'account di automazione come asset. Se il processo di autenticazione non viene disabilitato, gli script non potranno essere eseguiti automaticamente e il piano di ripristino non riuscirà.
* Timeout del processo di failover: si verifica il timeout dello script StorSimple se il failover dei contenitori di volumi impiega più tempo rispetto al limite di Azure Site Recovery per ogni script (attualmente è di 120 minuti).
* Timeout del processo di backup: si verifica il timeout dello script StorSimple se il backup dei volumi impiega più tempo rispetto al limite di Azure Site Recovery per ogni script (attualmente è di 120 minuti).

  > [!IMPORTANT]
  > Eseguire il backup manualmente dal portale di Azure e quindi eseguire nuovamente il piano di ripristino.
  >
  >
* Timeout del processo di clonazione: si verifica il timeout dello script StorSimple se la clonazione dei volumi impiega più tempo rispetto al limite di Azure Site Recovery per ogni script (attualmente è di 120 minuti).
* Errore di sincronizzazione dell'ora: si verifica un errore dello script StorSimple che informa dell'esito negativo dei backup anche in caso di esito positivo del backup nel portale. Una possibile causa dell'errore potrebbe essere la mancata sincronizzazione dell'ora dell'appliance StorSimple con l'ora corrente del fuso orario.

  > [!IMPORTANT]
  > Sincronizzare l'ora dell'appliance con l'ora corrente nel fuso orario.
  >
  >
* Errore di failover dell'appliance: lo script di StorSimple potrebbe non riuscire se si verifica un failover dell'appliance durante l'esecuzione del piano di ripristino.

  > [!IMPORTANT]
  > Rieseguire il piano di ripristino al termine del failover dell'appliance.
  >
  >

## <a name="summary"></a>Riepilogo
Usando Azure Site Recovery è possibile creare un piano di ripristino di emergenza automatizzato completo per una VM del server file con condivisioni file ospitate nell'archiviazione StorSimple. È possibile avviare il failover in pochi secondi da qualsiasi luogo in caso di un'interruzione e fare in modo che l'applicazione sia operativa in pochi minuti.



<!--HONumber=Nov16_HO3-->


