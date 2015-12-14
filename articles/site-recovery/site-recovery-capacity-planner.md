<properties
	pageTitle="Strumento di pianificazione della capacità di Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un sito locale secondario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Strumento di pianificazione della capacità di Site Recovery

In questo documento viene illustrato come utilizzare lo strumento di pianificazione della capacità di Microsoft ASR, che fornisce informazioni sulle risorse di cui è necessario eseguire il provisioning per garantire operazioni di Site Recovery senza problemi. Lo strumento di pianificazione della capacità consente di analizzare l'ambiente di origine (carichi di lavoro), i requisiti di larghezza di banda, i requisiti delle risorse (VM, archiviazione) di destinazione e le risorse server aggiuntive necessarie sul lato di origine (SC VMM, server di configurazione, server di elaborazione e così via). Scaricare lo strumento di [Pianificazione della capacità di Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel)
 
Sono disponibili due modalità in cui è possibile utilizzare lo strumento di pianificazione della capacità:
 
- **Pianificazione rapida** consente di ottenere le proiezioni di server e rete in base al numero medio di VM, dischi, archiviazione e frequenza di modifica. 
- **Pianificazione dettagliata** consente specificare i dettagli di ciascun carico di lavoro a livello di VM. Consente di analizzare la compatibilità a livello di VM e di ottenere anche le proiezioni di server e rete.
     
In questo documento si presuppone l'utente abbia acquisito familiarità con Azure Site Recovery. Fare riferimento a [Panoramica di Azure Site Recovery](site-recovery-overview.md)

## Introduzione
###Prerequisiti
Le informazioni necessarie per procedere variano a seconda della modalità dello strumento pianificazione da utilizzare. Oltre ai dettagli dell'infrastruttura, ad esempio VM, dischi per VM, archiviazione per disco, sono necessarie alcune ulteriori informazioni. Quella principale è la frequenza di modifica giornaliera o frequenza di varianza. Se l'ambiente di origine è Hyper-V, utilizzare lo [strumento di pianificazione della capacità di Hyper-V](https://www.microsoft.com/en-in/download/details.aspx?id=39057) per ottenere la frequenza di varianza. Leggere le istruzioni di utilizzo dello [strumento di pianificazione della capacità di Hyper-v](site-recovery-capacity-planning-for-hyper-v-replication.md). Per VMWare, utilizzare lo [strumento dell'appliance di pianificazione della capacità di VMware](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)

##Strumento di pianificazione rapida
1.	Aprire il file **Strumento di pianificazione della capacità ASR.xlsm**. Questa operazione richiede l'esecuzione di macro. Dunque, selezionare **"Abilita modifica"** e **"Abilita contenuto"** quando richiesto 
1.	Selezionare **Strumento di pianificazione rapida** dalla casella di riepilogo. Si apre un altro foglio di lavoro denominato **Strumento di pianificazione della capacità**

	![Introduzione](./media/site-recovery-capacity-planner/getting-started.png)

1.	Nel foglio di lavoro "Strumento di pianificazione della capacità", immettere gli input in base alle esigenze. Tutti i campi cerchiati sono campi di input obbligatori.
	1.	Selezionare la casella di riepilogo di uno scenario mediante il quale è possibile modificare l'ambiente di origine tra "Hyper-V in Azure" e "VMware/fisico in Azure".
	1. 	È necessario misurare la frequenza di modifica giornaliera media dei dati. Negli ambienti Hyper-V, è possibile utilizzare lo strumento di pianificazione della capacità di Hyper-V. In caso di VMWare, è possibile utilizzare lo strumento di pianificazione della capacità di VMWare. Di solito, è consigliabile eseguire lo strumento per almeno una settimana al fine di rilevare i picchi e stabilire le medie. 
	1. 	Compressione: questa è la compressione offerta da ASR in uno scenario "VMWare/fisico in Azure". In caso di "Hyper-V in Azure", è possibile ottenere tale scenario mediante appliance di terzi come Riverbed. 
	1. In uno scenario VMWare/fisico in Azure, il numero di conservazione deve essere espresso in giorni. In uno scenario di Hyper-V, questo verrà espresso in ore. 
	1. Gli ultimi due input vengono utilizzati per calcolare la replica iniziale (IR). Quando viene distribuita la configurazione di ASR, è necessario caricare l'intero set di dati iniziale. È consigliabile utilizzare come input il numero di ore in cui la replica iniziale per il batch delle macchine virtuali deve essere completata e il numero di macchine virtuali per batch di replica iniziale. Tuttavia, è possibile modificare tali numeri per regolare la larghezza di banda esistente. 

	![Input](./media/site-recovery-capacity-planner/inputs.png)

1. Dopo aver immesso i dettagli dell'ambiente di origine, l'output visualizzato comprenderà indicazioni come
	1.	Requisiti di larghezza di banda di rete
		1. Larghezza di banda necessaria per la replica delta (in Megabit al secondo) Questa viene calcolata in base alla frequenza di modifica giornaliera media dei dati. 
		1. Viene anche presentata larghezza di banda necessaria per la replica iniziale (in Megabit al secondo). Questa viene calcolata in base agli input di replica iniziale presentati (ultime due righe). 
	1.	Requisiti di Azure
		1. 	In questa sezione vengono illustrati nel dettaglio i requisiti di archiviazione, IOPS, account di archiviazione e dischi in Azure. 
	1. 	Ulteriori requisiti di infrastruttura 
		1. Eventuali requisiti aggiuntivi nello scenario "VMware/fisico in Azure", ad esempio i requisiti dei server di configurazione e di elaborazione. 
		1. 	Eventuali requisiti aggiuntivi nello scenario "Hyper-V in Azure", ad esempio archiviazione aggiuntiva necessaria per l'origine.
			
	![Output](./media/site-recovery-capacity-planner/output.png)
 
##Strumento di pianificazione dettagliata

1.	Aprire il file **Strumento di pianificazione della capacità ASR.xlsm**. Questa operazione richiede l'esecuzione di macro. Dunque, selezionare **"Abilita modifica"** e **"Abilita contenuto"** quando richiesto 
1.	Selezionare **Strumento di pianificazione dettagliata** dalla casella di riepilogo. Si apre un altro foglio di lavoro denominato **Qualifica del carico di lavoro**

	![Introduzione](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	Nel foglio Qualifica del carico di lavoro, immettere gli input in base alle esigenze. Tutte le colonne contrassegnate in rosso sono obbligatorie. Le altre colonne sono facoltative.
	1.	Core di elaborazione: indicare il numero totale di core del server di origine
	1. Allocazione della memoria in MB: indicare la quantità di RAM del server di origine.
	1.	Numero di schede di interfaccia di rete: indicare il numero di schede di interfaccia di rete del server di origine.
	1. Archiviazione totale (in GB): indicare le dimensioni totali dello spazio di archiviazione della VM. Ad esempio, se il server di origine dispone di 3 dischi da 500 GB ciascuno, lo spazio di archiviazione totale sarà di 1.500 GB.
	1. Numero di dischi collegati: indicare il numero totale dei dischi del server di origine.
	1. Utilizzo della capacità del disco: indicare l'utilizzo medio 
	1. Frequenza di modifica giornaliera (%): indicare la frequenza di modifica giornaliera dei dati del server di origine.
	1. Dimensione del mapping di Azure: è possibile immettere le dimensioni della VM di Azure di cui eseguire il mapping o utilizzare il pulsante Calcola VM IaaS per calcolare la migliore corrispondenza possibile. 

	![Qualifica del carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. Facendo clic sul pulsante **Calcola VM IaaS** si convalidano gli input precedenti e si ottengono suggerimenti sulla migliore corrispondenza possibile di VM di Azure. Se è impossibile individuare la dimensione appropriata della VM di Azure per un server di origine, viene restituito un errore per il server. Ad esempio, quando il numero di dischi collegati per una VM di origine è pari a 65, viene restituito un errore poiché il numero massimo di dischi collegabili alla VM di Azure dalle dimensioni più grandi è 64


**Calcola VM IaaS** calcola anche se una VM richiede l'account di archiviazione standard o l'account di archiviazione Premium di Azure. Inoltre, suggerisce il numero di account di archiviazione standard e account di archiviazione Premium necessario per il carico di lavoro. Scorrere verso il basso e a destra per visualizzare il tipo di archiviazione e l'account di archiviazione di Azure utilizzabili per un server di origine
 
**Esempio** : per 5 VM con i valori seguenti, lo strumento ha calcolato e assegnato la migliore corrispondenza VM di Azure in termini di dimensioni e ha suggerito se la VM richiedesse l'archiviazione standard o Premium

![Qualifica del carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification-2.png)

Nell'esempio, sono necessari due account di archiviazione standard e un account di archiviazione Premium per cinque VM. VM1 e VM2 possono utilizzare il primo account di archiviazione standard e VM3 può utilizzare il secondo. VM4 e VM5 richiedono l'account di archiviazione Premium e possono essere entrambe ospitate in un unico account di questo tipo.

![Qualifica del carico di lavoro](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]L'IOPS viene calcolato a livello di VM e non di disco. Se l'IOPS di uno dei dischi di una VM di origine è maggiore di 500, ma l'IOPS totale della VM ricade all'interno della VM di Azure standard supportata, mentre tutti gli altri valori (numero di dischi, numero di schede di interfaccia di rete, numero di core della CPU, dimensione della memoria,) si trovano all'interno del limite di una VM standard, lo strumento rileva una VM standard anziché una che richiede l'archiviazione Premium. L'utente deve aggiornare manualmente la cella relativa al mapping della dimensione di Azure con le VM delle serie DS o GS appropriate


1.	La prima colonna è per la convalida di VM, dischi e varianza. 
1.	Una volta inseriti tutti i dettagli, fare clic sul pulsante **Invia dati allo strumento di pianificazione** nella parte superiore. Si apre il foglio di lavoro **Strumento di pianificazione della capacità** contenente le medie popolate automaticamente come illustrato nella figura seguente. 
1.	Questa azione evidenzierà inoltre i carichi di lavoro idonei e non idonei alla protezione.


###Strumento di pianificazione della capacità

1.	Nel foglio di lavoro **Strumento di pianificazione della capacità**, l'origine di input dell'infrastruttura della prima riga, **Carico di lavoro**, suggerisce che le informazioni di input vengono popolate dal foglio di lavoro **Qualifica del carico di lavoro**.  
1.	Quando è necessario apportare le modifiche, eseguire tali operazioni nel foglio **Qualifica del carico di lavoro** e premere il pulsante **Invia dati allo strumento di pianificazione**. 

	![Strumento di pianificazione della capacità](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->