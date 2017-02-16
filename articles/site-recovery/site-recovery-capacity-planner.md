---
title: "Calcolare la capacità di replica in Azure | Documentazione Microsoft"
description: "Utilizzare questo articolo per calcolare la capacità durante la replica con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: 7ec48138cf18cf50dc34f28e177c8d774034090b


---
# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Pianificare la capacità per la protezione delle macchine virtuali e dei server fisici in Azure Site Recovery

Lo strumento Azure Site Recovery Capacity Planner consente di determinare i requisiti di capacità per la replica di VM Hyper-V e VMware e di server fisici Windows/Linux con Azure Site Recovery.

Usare Site Recovery Capacity Planner per analizzare l'ambiente di origine e i carichi di lavoro e stimare le esigenze di larghezza di banda, le risorse server necessarie per la posizione di origine e le risorse (macchine virtuali, archiviazione e così via) necessarie nella posizione di destinazione.

È possibile eseguire lo strumento nelle due modalità descritte di seguito:

* **Pianificazione rapida**: eseguire lo strumento in questa modalità per ottenere le proiezioni di rete e server in base a un numero medio di macchine virtuali, dischi, archiviazione e frequenza di modifica.
* **Pianificazione dettagliata**: eseguire lo strumento in questa modalità e specificare i dettagli di ogni carico di lavoro a livello di VM. Analizzare la compatibilità della macchina virtuale e ottenere le proiezioni di rete e server.

## <a name="before-you-start"></a>Prima di iniziare


1. Raccogliere informazioni sull'ambiente, incluse le macchine virtuali, i dischi per ogni macchina virtuale e l'archiviazione per ogni disco.
2. Identificare la frequenza di modifica giornaliera (varianza) per i dati replicati. A tale scopo, seguire questa procedura:

   * Se si esegue la replica di VM Hyper-V, scaricare lo [strumento di pianificazione della capacità per Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) per ottenere la frequenza di modifica. [Altre informazioni](site-recovery-capacity-planning-for-hyper-v-replication.md) su questo strumento. È consigliabile eseguire lo strumento nel corso di una settimana per acquisire le medie.
   * Se si esegue la replica di macchine virtuali VMware, usare lo [strumento di pianificazione della capacità di vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) per determinare la varianza.
   * Per la replica di server fisici, è necessario eseguire la stima manualmente.

## <a name="run-the-quick-planner"></a>Eseguire lo strumento di pianificazione rapida
1. Scaricare e aprire lo strumento [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) . Dato che è necessario eseguire macro, selezionare le opzioni per abilitare la modifica e il contenuto quando richiesto.
2. Nella casella di riepilogo **Select a planner type** (Selezionare un tipo di strumento di pianificazione) selezionare **Quick Planner** (Strumento di pianificazione rapida).

   ![Introduzione](./media/site-recovery-capacity-planner/getting-started.png)
3. Nel foglio di lavoro **Capacity Planner** (Pianificazione capacità) immettere le informazioni necessarie. È necessario compilare tutti i campi contrassegnati con un cerchio rosso nella schermata seguente.

   * In **Select your scenario** (Selezionare uno scenario) scegliere **Hyper-V to Azure** (Da Hyper-V ad Azure) o **VMware/Physical to Azure** (Da fisico/VMware ad Azure).
   * In **Average daily data change rate (%)** (Frequenza di modifica dei dati giornaliera media - %) inserire le informazioni raccolte con lo [strumento di pianificazione della capacità per Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) o lo [strumento di pianificazione della capacità per vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
   * **Compression** si applica solo alla compressione disponibile quando si esegue la replica di macchine virtuali VMware o server fisici in Azure. È stimata una compressione minima del 30%, ma è possibile modificare l'impostazione in base alle esigenze. Per la replica di VM Hyper-V in Azure con compressione, è possibile usare uno strumento di terze parti come Riverbed.
   * In **Retention Inputs** (Input per conservazione) specificare per quanto tempo devono essere conservate le repliche. Se si esegue la replica di server fisici o VMware, inserire il valore in giorni. Se si esegue la replica di Hyper-V, specificare il tempo in ore.
   * In **Number of hours in which initial replication for the batch of virtual machines should complete** (Numero di ore in cui deve essere completata la replica iniziale per il batch delle macchine virtuali) e in **Number of virtual machines per initial replication batch** (Numero di macchine virtuali per batch di replica iniziale) inserire le impostazioni usate per calcolare i requisiti della replica iniziale.  Quando viene distribuito Site Recovery, è necessario caricare l'intero set di dati iniziale.

   ![Input](./media/site-recovery-capacity-planner/inputs.png)
4. Dopo aver inserito i valori per l'ambiente di origine, l'output visualizzato include:

   * **Bandwidth required for delta replication** (Larghezza di banda necessaria per la replica differenziale), in megabit al secondo. La larghezza di banda di rete per la replica delta viene calcolata in base alla frequenza di modifica giornaliera media dei dati.
   * **Bandwidth required for initial replication** (Larghezza di banda necessaria per la replica iniziale), in megabit al secondo. La larghezza di banda di rete per la replica iniziale viene calcolata in base ai valori di replica iniziale inseriti.
   * **Storage required (in GBs)** : lo spazio di archiviazione di Azure totale richiesto.
   * **Total IOPS on standard storage accounts** : valore calcolato in base alla dimensione unitaria di 8.000 operazioni totali di input/output sul totale degli account di archiviazione standard.  Per lo strumento di pianificazione rapida, il numero viene calcolato in base a tutti i dischi di VM di origine e alla frequenza di modifica dei dati giornaliera. Per lo strumento di pianificazione dettagliata, il numero viene calcolato in base al numero totale di VM mappate a VM standard di Azure e alla frequenza di modifica dei dati in tali VM.
   * **Number of standard storage accounts** : fornisce il numero totale di account di archiviazione standard necessari per proteggere le macchine virtuali. Un account di archiviazione standard può contenere fino a 20000 operazioni di I/O al secondo tra tutte le VM in una risorsa di archiviazione standard e supporta un massimo di 500 operazioni di I/O al secondo per disco.
   * **Number of blob disks required** : fornisce il numero di dischi che verranno creati nell'archiviazione di Azure.
   * **Number of premium storage accounts required** : fornisce il numero totale di account di archiviazione premium necessari per proteggere le macchine virtuali. Per una VM di origine con numero di operazioni di I/O al secondo elevato (superiore a 20000) è necessario un account di archiviazione premium. Un account di archiviazione premium può contenere fino a 80.000 IOPS.
   * **Total IOPS on premium storage** : valore calcolato in base alla dimensione unitaria di 256.000 operazioni totali di input/output sul totale degli account di archiviazione premium.  Per lo strumento di pianificazione rapida, il numero viene calcolato in base a tutti i dischi di VM di origine e alla frequenza di modifica dei dati giornaliera. Per lo strumento di pianificazione dettagliata, il numero viene calcolato in base al numero totale di VM mappate a VM premium di Azure (serie DS e GS) e alla frequenza di modifica dei dati in tali VM.
   * **Number of configuration servers required** (Numero di server di configurazione necessari) indica il numero di server di configurazione necessari per la distribuzione.
   * **Number of additional process servers required** (Numero di server di elaborazione aggiuntivi necessari) indica se sono necessari server di elaborazione aggiuntivi oltre al server di elaborazione in esecuzione nel server di configurazione per impostazione predefinita.
   * **100% additional storage on the source** mostra se è necessario ulteriore spazio di archiviazione nel percorso di origine.

   ![Output](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Eseguire lo strumento di pianificazione dettagliata

1. Scaricare e aprire lo strumento [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) . Dato che è necessario eseguire macro, selezionare le opzioni per abilitare la modifica e il contenuto quando richiesto.
2. Nella casella di riepilogo **Select a planner type** (Selezionare un tipo di strumento di pianificazione) selezionare **Detailed Planner** (Strumento di pianificazione dettagliata).

   ![Introduzione](./media/site-recovery-capacity-planner/getting-started-2.png)
3. Nel foglio di lavoro **Workload Qualification** (Qualifica carico di lavoro) immettere le informazioni richieste. È necessario compilare tutti i campi contrassegnati.

   * In **Processor Cores** (Core processore) specificare il numero totale di core in un server di origine.
   * In **Memory allocation (in MBs)** (Allocazione memoria - in MB) specificare le dimensioni della RAM di un server di origine.
   * In **Number of NICs** (Numero di schede di interfaccia di rete) specificare il numero di schede di rete in un server di origine.
   * In **Total Storage (in GBs)** (Spazio di archiviazione totale - in GB) specificare lo spazio di archiviazione totale delle VM. Se il server di origine ha 3 dischi da 500 GB ognuno, ad esempio, lo spazio di archiviazione totale sarà di 1500 GB.
   * In **Number of disks attached** (Numero di dischi collegati) specificare il numero totale dei dischi di un server di origine.
   * In **Disk capacity utilization** (Utilizzo capacità disco) specificare l'utilizzo medio.
   * In **Daily data change rate (%)** (Frequenza di modifica dei dati giornaliera - %) specificare la frequenza di modifica dei dati giornaliera di un server di origine.
   * In **Mapping Azure VM size** (Dimensioni VM di Azure per mapping) immettere le dimensioni di VM di Azure da mappare. Se non si vuole eseguire questa operazione manualmente, fare clic su **Compute IaaS VMs** (Calcola VM IaaS). Se si inserisce un'impostazione manuale e quindi si fa clic su Compute IaaS VMs (Calcola VM IaaS), l'impostazione manuale potrebbe essere sovrascritta perché il processo di calcolo identifica automaticamente la corrispondenza migliore nelle dimensioni di VM di Azure.

   ![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Se si sceglie **Compute IaaS VMs** , lo strumento esegue queste operazioni:

   * Convalida gli input obbligatori.
   * Calcola IOPS e suggerisce la migliore corrispondenza in base alle dimensioni della macchina virtuale di Azure per ogni macchina virtuale idonea alla replica in Azure. Se non viene trovata una macchina virtuale di Azure della dimensione appropriata viene visualizzato un errore. Se il numero di dischi collegati è 65, ad esempio, viene visualizzato un errore perché la dimensione massima di VM di Azure è 64.
   * Suggerisce un account di archiviazione da usare per una macchina virtuale di Azure.
   * Calcola il numero totale di account di archiviazione standard e account di archiviazione premium necessari per il carico di lavoro. Scorrere verso il basso e a destra per visualizzare il tipo di archiviazione di Azure e l'account di archiviazione che può essere usato per un server di origine.
   * Completa e ordina il resto della tabella in base al tipo di archiviazione necessaria (standard o premium) assegnata per una macchina virtuale e al numero di dischi collegati. Per tutte le VM che soddisfano i requisiti per Azure, la colonna **Is VM qualified?** (VM qualificata?) contiene **Yes** (Sì). Se non è possibile eseguire il backup di una VM in Azure, viene visualizzato un errore.

Vengono generate le colonne da AA ad AE, contenenti informazioni per ogni VM.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Esempio
Ad esempio, per sei macchine virtuali con i valori riportati nella tabella, lo strumento calcola e assegna la migliore corrispondenza di macchina virtuale di Azure e i requisiti di archiviazione di Azure.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Nell'output dell'esempio si noti quanto segue:

  * La prima colonna è per la convalida di macchine virtuali, dischi e varianza.
  * Per cinque macchine virtuali sono necessari due account di archiviazione standard e un account di archiviazione premium.
  * La VM3 non è idonea alla protezione perché uno o più dischi sono superiori a 1 TB.
  * La VM1 e la VM2 possono usare il primo account di archiviazione standard.
  * La VM4 può usare il secondo account di archiviazione standard.
  * Per la VM5 e la VM6 è necessario un account di archiviazione premium ed entrambe possono usare un singolo account.

    > [!NOTE]
    > Le operazioni di I/O al secondo nell'archiviazione standard e premium vengono calcolate a livello di VM e non a livello di disco. Una macchina virtuale standard è in grado di gestire fino a 500 operazioni totali di input/output per disco. Se le operazioni di I/O al secondo per disco sono superiori a 500, è necessaria l'archiviazione premium. Se le operazioni di I/O al secondo per un disco sono superiori a 500 ma le operazioni di I/O al secondo per i dischi totali della VM rientrano nei limiti di supporto delle VM di Azure standard (dimensioni di VM, numero di dischi, numero di schede, CPU, memoria), tuttavia, lo strumento di pianificazione sceglie una VM standard e non serie DS o GS. È necessario aggiornare manualmente la cella relativa alle dimensioni di Azure per il mapping con le VM serie DS o GS appropriate.


1. Dopo aver specificato tutti i dettagli, fare clic su **Submit data to the planner tool** (Invia dati a strumento di pianificazione) per aprire **Capacity Planner**. I carichi di lavoro sono evidenziati per mostrare l'idoneità o meno alla protezione.

### <a name="submit-data-in-the-capacity-planner"></a>Inviare i dati nello strumento di pianificazione
1. Quando si apre il foglio di lavoro **Capacity Planner** viene compilato in base alle impostazioni specificate. Il termine "Workload" (Carico di lavoro) visualizzato nel campo **Infra inputs source** (Origine input infrastruttura) indica che l'input è costituito dal foglio di lavoro **Workload Qualification** (Qualifica carico di lavoro).
2. Per apportare modifiche è necessario modificare il foglio di lavoro **Workload Qualification** (Qualifica carico di lavoro) e fare clic di nuovo su **Submit data To the planner tool** (Invia dati a strumento di pianificazione).  

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)



<!--HONumber=Jan17_HO5-->


