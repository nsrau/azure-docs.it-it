<properties
	pageTitle="Pianificare la capacità per la protezione delle macchine virtuali e dei server fisici in Azure Site Recovery | Microsoft Azure"
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un sito locale secondario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/12/2016" 
	ms.author="raynew"/>

# Pianificare la capacità per la protezione delle macchine virtuali e dei server fisici in Azure Site Recovery

Lo strumento Azure Site Recovery Capacity Planner consente di determinare i requisiti di capacità per la protezione di macchine virtuali Hyper-V e VMware e di server fisici Windows/Linux con Azure Site Recovery.


## Panoramica

È possibile usare Site Recovery Capacity Planner per analizzare l'ambiente di origine e i carichi di lavoro e determinare le esigenze di larghezza di banda, le risorse server necessarie nel percorso di origine e le risorse (macchine virtuali, archiviazione e così via) necessarie nel percorso di destinazione.

È possibile eseguire lo strumento nelle due modalità descritte di seguito:

- **Pianificazione rapida**: eseguire lo strumento in questa modalità per ottenere le proiezioni di rete e server in base a un numero medio di macchine virtuali, dischi, archiviazione e frequenza di modifica.
- **Pianificazione dettagliata**: eseguire lo strumento in questa modalità e fornire i dettagli di ogni carico di lavoro a livello di macchina virtuale. Analizzare la compatibilità della macchina virtuale e ottenere le proiezioni di rete e server.

## Prima di iniziare

Prima di eseguire lo strumento:

1. Raccogliere informazioni sull'ambiente, incluse le macchine virtuali, i dischi per ogni macchina virtuale e l'archiviazione per ogni disco.
2. Identificare la frequenza di modifica giornaliera (varianza) per i dati replicati. A tale scopo, effettuare l'operazione seguente:

	- Se si esegue la replica di macchine virtuali Hyper-V, scaricare lo [strumento di pianificazione della capacità di Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) per ottenere la frequenza di modifica. [Altre informazioni](site-recovery-capacity-planning-for-hyper-v-replication.md) su questo strumento. È consigliabile eseguire lo strumento nel corso di una settimana per acquisire le medie.
	- Se si esegue la replica di macchine virtuali VMware, usare lo [strumento di pianificazione della capacità di vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) per determinare la varianza.
	- Per la replica di server fisici, è necessario eseguire la stima manualmente.

## Eseguire lo strumento di pianificazione rapida
1.	Scaricare e aprire lo strumento [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Sarà necessario eseguire macro, selezionare quindi le opzioni per abilitare la modifica e il contenuto quando richiesto.
2.	Nella casella di riepilogo **Select a planner type** scegliere **Quick Planner**.

	![Introduzione](./media/site-recovery-capacity-planner/getting-started.png)

3.	Nel foglio di lavoro **Capacity Planner** immettere le informazioni richieste. È necessario compilare tutti i campi contrassegnati con un cerchio rosso nella schermata seguente.

	- In **Select your scenario** scegliere **Hyper-V to Azure** o **VMware/Physical to Azure**.
	- In **Average daily data change rate (%)** inserire le informazioni raccolte con lo [strumento di pianificazione della capacità di Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) o lo [strumento di pianificazione della capacità di vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).
	- L'impostazione **Compression** si applica solo alla compressione disponibile quando si esegue la replica di macchine virtuali VMware o server fisici in Azure. È stimata una compressione minima del 30%, ma è possibile modificare l'impostazione in base alle esigenze. Per la replica di macchine virtuali Hyper-V in Azure e la compressione è possibile usare uno strumento di terze parti, ad esempio Riverbed.
	-  In **Retention Inputs** specificare per quanto tempo devono essere conservate le repliche. Se si esegue la replica di macchine virtuali VMware o server fisici, inserire il valore in giorni. Se si esegue la replica di macchine virtuali Hyper-V, specificare il tempo in ore.
	-  In **Number of hours in which initial replication for the batch of virtual machines should complete** e in **Number of virtual machines per initial replication batch** inserire le impostazioni usate per calcolare i requisiti di replica iniziale. Quando viene distribuito Site Recovery, è necessario caricare l'intero set di dati iniziale.

	![Input](./media/site-recovery-capacity-planner/inputs.png)

2.	Dopo aver inserito i valori per l'ambiente di origine, l'output visualizzato include:

	- **Bandwidth required for delta replication** (MB/secondo): La larghezza di banda di rete per la replica delta viene calcolata in base alla frequenza di modifica giornaliera media dei dati.
	- **Bandwidth required for initial replication** (MB/secondo): La larghezza di banda di rete per la replica iniziale viene calcolata in base ai valori di replica iniziale inseriti.
	- **Storage required (in GBs)**: lo spazio di archiviazione di Azure totale richiesto.
	- **Total IOPS on standard storage accounts**: valore calcolato in base alla dimensione unitaria di 8.000 operazioni totali di input/output sul totale degli account di archiviazione standard. Per lo strumento Quick Planner il numero viene calcolato in base a tutti i dischi di macchine virtuali di origine e alla frequenza di modifica giornaliera dei dati. Per lo strumento Detailed Planner il numero viene calcolato in base al numero totale di macchine virtuali con mapping a macchine virtuali standard di Azure e alla frequenza di modifica dei dati su tali macchine virtuali.
	- **Number of standard storage accounts**: fornisce il numero totale di account di archiviazione standard necessari per proteggere le macchine virtuali. Si noti che un account di archiviazione standard può contenere fino a 20.000 IOPS tra tutte le macchine virtuali in una risorsa di archiviazione standard e supporta un massimo di 500 IOPS per disco.
	- **Number of blob disks required**: fornisce il numero di dischi che verranno creati nell'archiviazione di Azure.
	- **Number of premium storage accounts required**: fornisce il numero totale di account di archiviazione premium necessari per proteggere le macchine virtuali. Si noti che per una macchina virtuale di origine con IOPS elevato (maggiore di 20.000) è necessario un account di archiviazione premium. Un account di archiviazione premium può contenere fino a 80.000 IOPS.
	- **Total IOPS on premium storage**: valore calcolato in base alla dimensione unitaria di 256.000 operazioni totali di input/output sul totale degli account di archiviazione premium. Per lo strumento Quick Planner il numero viene calcolato in base a tutti i dischi di macchine virtuali di origine e alla frequenza di modifica giornaliera dei dati. Per lo strumento Detailed Planner il numero viene calcolato in base al numero totale di macchine virtuali con mapping a macchine virtuali premium di Azure (serie DS e GS) e alla frequenza di modifica dei dati su tali macchine virtuali.
	- **Number of configuration servers required** mostra il numero di server di configurazione necessari per la distribuzione (1)
	- **Number of additional process servers required** mostra se sono necessari server di elaborazione aggiuntivi oltre al server di processo configurato nel server di configurazione per impostazione predefinita.
	- **100% additional storage on the source** mostra se è necessario ulteriore spazio di archiviazione nel percorso di origine.
			
	![Output](./media/site-recovery-capacity-planner/output.png)
 
## Eseguire lo strumento di pianificazione dettagliata


1.	Scaricare e aprire lo strumento [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Sarà necessario eseguire macro, selezionare quindi le opzioni per abilitare la modifica e il contenuto quando richiesto.
2.	Nella casella di riepilogo **Select a planner type** scegliere **Detailed Planner**.

	![Introduzione](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	Nel foglio di lavoro **Workload Qualification** immettere le informazioni richieste. È necessario compilare tutti i campi contrassegnati.

	- In **Processor Cores** specificare il numero totale di core in un server di origine.
	- In **Memory allocation (in MBs)** specificare la dimensione della RAM di un server di origine.
	- In **Number of NICs** specificare il numero di schede di rete in un server di origine.
	-  In **Total Storage (in GB)** specificare lo spazio di archiviazione totale della macchina virtuale. Ad esempio, se il server di origine ha 3 dischi da 500 GB ognuno, lo spazio di archiviazione totale sarà di 1.500 GB.
	-  In **Number of disks attached** specificare il numero totale dei dischi di un server di origine.
	-  In **Disk capacity utilization (%)** specificare l'utilizzo medio.
	-  In **Daily data change rate (%)** specificare la frequenza di modifica giornaliera dei dati di un server di origine.
	-  In **Mapping Azure VM size** immettere manualmente la dimensione della macchina virtuale di Azure di cui eseguire il mapping. In alternativa all'immissione manuale, fare clic su **Compute IaaS VMs**. Si noti che, inserendo un'impostazione manuale e facendo clic su Compute IaaS VMs, l'impostazione manuale potrebbe essere sovrascritta perché il processo di calcolo identifica automaticamente la corrispondenza migliore in base alle dimensioni della macchina virtuale.

	![Qualifica del carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification.png)

4.	Se si sceglie **Compute IaaS VMs**, lo strumento esegue queste operazioni:

	- Convalida gli input obbligatori.
	- Calcola IOPS e suggerisce la migliore corrispondenza in base alle dimensioni della macchina virtuale di Azure per ogni macchina virtuale idonea alla replica in Azure. Se non viene trovata una macchina virtuale di Azure della dimensione appropriata viene visualizzato un errore. Ad esempio, se il numero di dischi collegati è 65 viene visualizzato un errore perché la dimensione massima di macchina virtuale di Azure è 64.
	- Suggerisce un account di archiviazione da usare per una macchina virtuale di Azure.
	- Calcola il numero totale di account di archiviazione standard e account di archiviazione premium necessari per il carico di lavoro. Scorrere verso il basso a destra per visualizzare il tipo di archiviazione e l'account di archiviazione di Azure che possono essere usati per un server di origine.
	- Completa e ordina il resto della tabella in base al tipo di archiviazione necessaria (standard o premium) assegnata per una macchina virtuale e al numero di dischi collegati. Per tutte le macchine virtuali che soddisfano i requisiti di backup in Azure, la colonna A (Is VM qualified?) indica Yes. Se non è possibile eseguire il backup di una macchina virtuale in Azure, viene visualizzato un errore.

Vengono visualizzate le colonne da AA ad AE che forniscono informazioni per ogni macchina virtuale.

![Qualifica del carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### Esempio
Ad esempio, per sei macchine virtuali con i valori riportati nella tabella, lo strumento calcola e assegna la migliore corrispondenza di macchina virtuale di Azure e i requisiti di archiviazione di Azure.

![Qualifica del carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- Si noti quanto segue nell'output dell'esempio:
	
	- La prima colonna è per la convalida di macchine virtuali, dischi e varianza.
	- Per cinque macchine virtuali sono necessari due account di archiviazione standard e un account di archiviazione premium. 
	-  La VM3 non è idonea alla protezione perché uno o più dischi sono superiori a 1 TB.
	-  La VM1 e la VM2 possono usare il primo account di archiviazione standard.
	-  La VM4 può usare il secondo account di archiviazione standard.
	-  Per la VM5 e la VM6 è necessario un account di archiviazione premium ed entrambe possono usare un singolo account.

	>[AZURE.NOTE]Le operazioni totali di input/output in archiviazione standard e premium vengono calcolate a livello di macchina virtuale e non a livello di disco. Una macchina virtuale standard è in grado di gestire fino a 500 operazioni totali di input/output per disco. Se le operazioni totali di input/output per disco sono superiori a 500, sarà necessaria l’archiviazione premium. Tuttavia se le operazioni totali di input/output per disco sono superiori a 500 ma le operazioni totali di input/output per il numero totale di dischi della macchina virtuale rientrano nei limiti di supporto standard delle macchine virtuali di Azure (dimensione VM, numero di dischi, numero di schede di rete, CPU, memorie), la pianificazione sceglie una macchina virtuale standard e non le serie DS o GS. Sarà necessario aggiornare manualmente la cella relativa al mapping della dimensione di Azure con le macchine virtuali delle serie DS o GS appropriate

5. Dopo aver fornito tutti i dettagli, fare clic su **Submit data to the planner tool** per aprire il foglio di lavoro **Capacity Planner**. I carichi di lavoro sono evidenziati per mostrare l'idoneità o meno alla protezione.


### Inviare i dati nello strumento di pianificazione

1.	Quando si apre il foglio di lavoro **Capacity Planner** viene compilato in base alle impostazioni specificate. Il termine "Workload" viene visualizzato nel campo **Infra inputs source** per indicare che l'input è dato dal foglio di lavoro **Workload Qualification**.
2.	Per apportare modifiche è necessario modificare il foglio di lavoro **Workload Qualification** e fare clic di nuovo su Submit data To the planner tool.  

	![Strumento di pianificazione della capacità](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_0720_2016-->