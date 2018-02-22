---
title: Azure Site Recovery Deployment Planner per distribuzioni da Hyper-V ad Azure | Microsoft Docs
description: "Questo articolo descrive la modalità di esecuzione di Azure Site Recovery Deployment Planner quando si passa da Hyper-V ad Azure."
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: ae539f136578c6461ef7f680d553fbd76b10ae98
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Eseguire Azure Site Recovery Deployment Planner per distribuzioni da Hyper-V ad Azure

## <a name="modes-of-running-the-deployment-planner"></a>Modalità di esecuzione di Deployment Planner
È possibile eseguire lo strumento da riga di comando (ASRDeploymentPlanner.exe) in una delle quattro modalità seguenti: 
-   [Ottenere l'elenco delle macchine virtuali (VM)](#get-vm-list-for-profiling-hyper-v-vms)
-   [Profilo](#profile-hyper-v-vms)
-   [Generare un report](#generate-report)
-   [Misurare la velocità effettiva](#get-throughput)

Eseguire prima lo strumento per ottenere l'elenco delle VM da uno o più host Hyper-V. Eseguire quindi lo strumento in modalità di profilatura per raccogliere le operazioni di I/O al secondo e la varianza dati delle VM. Eseguire quindi lo strumento per generare il report e determinare i requisiti di larghezza di banda e archiviazione.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Ottenere l'elenco di VM per la profilatura delle VM Hyper-V
Prima di tutto è necessario un elenco delle VM da profilare. Usare la modalità GetVMList dello strumento Deployment Planner per generare con un unico comando l'elenco delle VM presenti in più host Hyper-V. Dopo aver generato l'elenco completo, è possibile rimuovere dal file di output le VM che non si intende profilare. Usare quindi il file di output per tutte le altre operazioni, ovvero per la profilatura, la generazione di report e la misurazione della velocità effettiva.

È possibile generare l'elenco di VM puntando lo strumento a un cluster Hyper-V, a un host Hyper-V autonomo o a una combinazione dei due.

### <a name="command-line-parameters"></a>Parametri della riga di comando
La tabella seguente contiene un elenco dei parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| Nome parametro | DESCRIZIONE |
|---|---|
| -Operation | GetVMList |
| -User | Nome utente per la connessione all'host Hyper-V o al cluster Hyper-V. L'utente deve avere l'accesso amministrativo.|
|-ServerListFile | File con l'elenco dei server contenenti le VM da profilare. Il percorso del file può essere assoluto o relativo. Questo file deve contenere uno degli elementi seguenti in ogni riga:<ul><li>Nome host o indirizzo IP Hyper-V</li><li>Nome cluster o indirizzo IP Hyper-V</li></ul><br>**Esempio**: ServerList.txt contiene i server seguenti:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Facoltativo) UNC (Universal Naming Convention) o percorso della directory locale per l'archiviazione dei dati generati durante l'operazione. Se non viene specificato un nome, come directory predefinita viene usata la directory denominata ProfiledData nel percorso corrente.|
|-OutputFile| (Facoltativo) File in cui viene salvato l'elenco di VM recuperato dai server Hyper-V. Se non viene indicato un nome, i dettagli vengono archiviati in VMList.txt.  Usare il file per avviare la profilatura dopo aver rimosso le VM da non profilare.|
|-Password|(Facoltativo) Password per la connessione all'host Hyper-V. Se non viene specificata come parametro, verrà richiesta quando si esegue il comando.|

### <a name="getvmlist-discovery"></a>Individuazione con GetVMList
**Cluster Hyper-V**: quando viene specificato il nome del cluster Hyper-V nel file dell'elenco dei server, lo strumento trova tutti i nodi Hyper-V del cluster e ottiene le VM presenti in ognuno degli host Hyper-V.

**Host Hyper-V**: quando viene specificato il nome dell'host Hyper-V, lo strumento verifica prima se l'host appartiene a un cluster. In caso affermativo, recupera i nodi appartenenti al cluster. Ottiene quindi le VM da ogni host Hyper-V. 

È possibile scegliere di elencare in un file i nomi descrittivi o gli indirizzi IP delle VM che si vuole profilare manualmente.

Aprire il file di output nel Blocco note e quindi copiare i nomi di tutte le VM da profilare in un altro file, ad esempio, ProfileVMList.txt. Usare un nome di VM per riga. Questo file viene usato come input per il parametro -VMListFile dello strumento per tutte le altre operazioni, ovvero per la profilatura, la generazione di report e la misurazione della velocità effettiva.

### <a name="examples"></a>Esempi

#### <a name="store-the-list-of-vms-in-a-file"></a>Archiviare l'elenco di VM in un file
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Archiviare l'elenco di VM nel percorso predefinito (percorso -Directory)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Profilare VM Hyper-V
Nella modalità di profilatura, lo strumento Deployment Planner si connette a ognuno degli host Hyper-V per raccogliere dati sulle prestazioni delle VM. 

La profilatura non influisce sulle prestazioni delle VM di produzione perché non vengono stabilite connessioni dirette a tali VM. Tutti i dati sulle prestazioni vengono raccolti dall'host Hyper-V.

Lo strumento esegue query sull'host Hyper-V ogni 15 secondi per garantire l'accuratezza della profilatura, quindi archivia la media dei dati dei contatori delle prestazioni di ogni minuto.

Lo strumento gestisce facilmente la migrazione delle VM da un nodo a un altro nel cluster e la migrazione dell'archiviazione all'interno di un host.

### <a name="getting-the-vm-list-to-profile"></a>Recupero dell'elenco delle VM da profilare
Per creare un elenco delle VM da profilare, vedere l'operazione [GetVMList](#get-vm-list-for-profiling-hyper-v-vms).

Dopo aver ottenuto l'elenco di macchine virtuali da profilare, è possibile eseguire lo strumento in modalità di profilatura. 

### <a name="command-line-parameters"></a>Parametri della riga di comando
La tabella seguente elenca i parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità di profilatura. Lo strumento supporta sia gli scenari in cui si passa da VMware ad Azure che quelli in cui si passa da Hyper-V ad Azure. Per Hyper-V sono applicabili questi parametri. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| Nome parametro | DESCRIZIONE |
|---|---|
| -Operation | StartProfiling |
| -User | Nome utente per la connessione all'host Hyper-V o al cluster Hyper-V. L'utente deve avere l'accesso amministrativo.|
| -VMListFile | File con l'elenco di VM da profilare. Il percorso del file può essere assoluto o relativo. Per Hyper-V, si tratta del file di output dell'operazione GetVMList. In caso di preparazione manuale, il file deve contenere un nome server o un indirizzo IP, seguito dal nome della VM separato con un carattere \ per riga. Il nome della VM specificato nel file deve essere uguale al nome della VM nell'host Hyper-V.<br><br>**Esempio**: VMList.txt contiene le VM seguenti:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Numero di minuti per i quali verrà eseguita la profilatura. Il valore minimo è 30 minuti.|
|-NoOfHoursToProfile|Numero di ore per le quali verrà eseguita la profilatura.|
|-NoOfDaysToProfile |Numero di giorni per i quali verrà eseguita la profilatura. È consigliabile eseguire la profilatura per più di 7 giorni. Tale durata garantisce che il modello di carico di lavoro dell'ambiente nel periodo specificato venga osservato e usato per offrire un'indicazione accurata.|
|-Virtualization|Tipo di virtualizzazione (VMware o Hyper-V).|
|-Directory|(Facoltativo) UNC o percorso di directory locale per l'archiviazione dei dati generati durante la profilatura. Se non viene specificato un nome, come directory predefinita verrà usata la directory denominata ProfiledData nel percorso corrente.|
|-Password|(Facoltativo) Password per la connessione all'host Hyper-V. Se non viene specificata come parametro, verrà richiesta quando si esegue il comando.|
|-StorageAccountName|(Facoltativo) Nome dell'account di archiviazione usato per determinare la velocità effettiva ottenibile per la replica dei dati dall'ambiente locale ad Azure. Lo strumento carica i dati di test in questo account di archiviazione per calcolare la velocità effettiva. L'account di archiviazione deve essere un account per utilizzo generico versione 1 o versione 2.|
|-StorageAccountKey|(Facoltativo) Chiave usata per accedere all'account di archiviazione. Passare al portale di Azure e selezionare **Account di archiviazione** > *nome-account-archiviazione* > **Impostazioni** > **Chiavi di accesso** > **Key1** oppure la chiave di accesso primaria per un account di archiviazione classico.|
|-Environment|(Facoltativo) Ambiente di destinazione per l'account di archiviazione di Azure. Può trattarsi di uno di tre valori: AzureCloud, AzureUSGovernment o AzureChinaCloud. Il valore predefinito è AzureCloud. Usare il parametro quando l'area di destinazione è Azure US Government o Azure Cina.|

È consigliabile profilare le VM per oltre 7 giorni. Se il modello di varianza varia in un mese, è consigliabile eseguire la profilatura durante la settimana in cui si rileva la varianza massima. La soluzione ottimale consiste nell'eseguire la profilatura per 31 giorni, per ottenere un'indicazione migliore. 

Durante il periodo di profilatura, ASRDeploymentPlanner.exe rimane in esecuzione. Lo strumento accetta l'input della durata della profilatura in giorni. Per un rapido test dello strumento o per il modello di verifica, è possibile eseguire la profilatura per alcune ore o minuti. La durata minima della profilatura è di 30 minuti. 

Durante la profilatura è possibile passare il nome e la chiave di un account di archiviazione per determinare la velocità effettiva che Azure Site Recovery può raggiungere al momento della replica dal server Hyper-V ad Azure. Se il nome e la chiave dell'account di archiviazione non vengono passati durante la profilatura, lo strumento non calcola la velocità effettiva ottenibile.

È possibile eseguire più istanze dello strumento per diversi set di VM. Assicurarsi che i nomi delle VM non siano ripetuti nei set di profilatura. Se ad esempio sono state profilate 10 VM (da VM1 a VM10) e dopo alcuni giorni si vogliono profilare altre 5 VM (da VM11 a VM15), è possibile eseguire lo strumento da un'altra console della riga di comando per il secondo set di VM (da VM11 a VM15). 

Verificare che il secondo set di VM non contenga nomi di VM della prima istanza di profilatura oppure usare una directory di output diversa per la seconda esecuzione. Se vengono usate due istanze dello strumento per profilare le stesse VM usando la stessa directory di output, il report generato sarà errato. 

Per impostazione predefinita, lo strumento è configurato per eseguire la profilatura e la generazione di report di un massimo di 1.000 VM. È possibile modificare questo limite cambiando il valore della chiave MaxVMsSupported nel file ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Per profilare 1.500 VM con le impostazioni predefinite, ad esempio, creare due file VMList.txt, uno con 1.000 VM e l'altro con 500 VM. Eseguire le due istanze di Azure Site Recovery Deployment Planner, una con VMList1.txt e l'altra con VMList2.txt. È possibile usare lo stesso percorso di directory per archiviare i dati di profilatura delle VM di entrambi i file VMList. 

In base alla configurazione hardware (in particolare alle dimensioni della RAM) del server da cui viene eseguito lo strumento per generare il report, l'operazione potrebbe non riuscire a causa di memoria insufficiente. Se l'hardware è appropriato, è possibile modificare MaxVMsSupported impostando un valore maggiore.  

Le configurazioni delle VM vengono acquisite una volta all'inizio dell'operazione di profilatura e archiviate in un file denominato VMDetailList.xml. Queste informazioni vengono usate quando viene generato il report. Eventuali modifiche apportate alla configurazione delle VM (ad esempio, un incremento del numero di core, dischi o schede di interfaccia di rete) dall'inizio alla fine della profilatura non vengono acquisite. Se la configurazione di una VM profilata è stata modificata nel corso della profilatura, la soluzione alternativa seguente consente di ottenere i dettagli più recenti della VM durante la generazione del report:

* Eseguire il backup di VMdetailList.xml ed eliminare il file dal percorso corrente.
* Passare gli argomenti -User e -Password al momento della generazione di report.

Il comando di profilatura genera diversi file nella directory della profilatura. Non eliminare nessuno dei file, altrimenti la generazione del report verrà compromessa.

### <a name="examples"></a>Esempi

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Profilare le VM per 30 giorni e determinare la velocità effettiva dall'ambiente locale ad Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Profilare le VM per 15 giorni
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Profilare le VM per 60 minuti per un rapido test dello strumento
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Profilare le VM per 2 ore per un modello di verifica
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Considerazioni sulla profilatura

Se il server in cui viene eseguito lo strumento viene riavviato o si è arrestato in modo anomalo oppure se si chiude lo strumento usando CTRL+C, i dati profilati vengono conservati. Potrebbero tuttavia andare persi gli ultimi 15 minuti di dati profilati. In tal caso, eseguire nuovamente lo strumento in modalità di profilatura dopo il riavvio del server.

Quando vengono passati il nome e la chiave di un account di archiviazione, lo strumento misura la velocità effettiva all'ultimo passaggio della profilatura. Se lo strumento viene chiuso prima del completamento della profilatura, la velocità effettiva non verrà calcolata. Per determinare la velocità effettiva prima di generare il report, è possibile eseguire l'operazione GetThroughput dalla console della riga di comando. In caso contrario, il report generato non conterrà le informazioni sulla velocità effettiva.

Azure Site Recovery non supporta le macchine virtuali con dischi iSCSI e pass-through. Lo strumento, tuttavia, non può rilevare e profilare dischi iSCSI e pass-through collegati alle VM.

## <a name="generate-a-report"></a>Generare un report
Lo strumento genera un file di Microsoft Excel con attivazione macro (file XLSM) come output del report, con il riepilogo di tutte le indicazioni di distribuzione. Il report è denominato DeploymentPlannerReport_*identificatore numerico univoco*.xlsm e viene inserito nella directory specificata.

Al termine della profilatura, è possibile eseguire lo strumento in modalità di generazione di report. 

### <a name="command-line-parameters"></a>Parametri della riga di comando
La tabella seguente contiene un elenco dei parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità di generazione di report. Lo strumento supporta il passaggio sia da VMware ad Azure che da Hyper-V ad Azure. Per Hyper-V sono applicabili i parametri seguenti.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| Nome parametro | DESCRIZIONE |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | File contenente l'elenco delle VM profilate per cui verrà generato il report. Il percorso del file può essere assoluto o relativo. Per Hyper-V, si tratta del file di output dell'operazione GetVMList. In caso di preparazione manuale, il file deve contenere un nome server o un indirizzo IP, seguito dal nome della VM separato con un carattere \ per riga. Il nome della VM specificato nel file deve essere uguale al nome della VM nell'host Hyper-V.<br><br>**Esempio**: VMList.txt contiene le VM seguenti:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Tipo di virtualizzazione (VMware o Hyper-V).|
|-Directory|(Facoltativo) UNC o percorso della directory locale in cui vengono archiviati i dati profilati, ovvero i file generati durante la profilatura. Questi dati sono necessari per la generazione di report. Se non viene specificato un nome, come directory predefinita verrà usata la directory denominata ProfiledData nel percorso corrente.|
| -User | (Facoltativo) Nome utente per la connessione all'host Hyper-V o al cluster Hyper-V. L'utente deve avere l'accesso amministrativo. Il nome utente e la password vengono usati per recuperare le più recenti informazioni di configurazione delle VM da usare nel report, come il numero di dischi, il numero di core e il numero di schede di interfaccia di rete. Se questo valore non viene specificato, verranno usate le informazioni di configurazione raccolte durante la profilatura.|
|-Password|(Facoltativo) Password per la connessione all'host Hyper-V. Se non viene specificata come parametro, verrà richiesta quando si esegue il comando.|
| -DesiredRPO | (Facoltativo) Obiettivo del punto di ripristino (RPO) desiderato, in minuti. Il valore predefinito è 15 minuti.|
| -Bandwidth | (Facoltativo) Larghezza di banda, in megabit al secondo. Usare questo parametro per calcolare il valore RPO ottenibile per la larghezza di banda specificata. |
| -StartDate | (Facoltativo) Data e ora di inizio in formato MM-GG-AAAA:HH:MM a 24 ore. StartDate deve essere specificato con EndDate. Quando StartDate è specificato, il report viene generato per i dati profilati raccolti nell'intervallo compreso tra StartDate ed EndDate. |
| -EndDate | (Facoltativo) Data e ora di fine in formato MM-GG-AAAA:HH:MM a 24 ore. EndDate deve essere specificato con StartDate. Quando EndDate è specificato, il report viene generato per i dati profilati raccolti nell'intervallo compreso tra StartDate ed EndDate. |
| -GrowthFactor | (Facoltativo) Fattore di crescita, espresso come percentuale. Il valore predefinito è 30%. |
| -UseManagedDisks | (Facoltativo) UseManagedDisks: Yes/No. Il valore predefinito è Yes. Il numero delle macchine virtuali che possono essere inserite in un singolo account di archiviazione viene calcolato in base all'esecuzione del failover e del failover di test delle macchine virtuali su un disco gestito anziché su un disco non gestito. |
|-SubscriptionId |(Facoltativo) GUID della sottoscrizione. Usare questo parametro per generare il report di stima dei costi con i prezzi più recenti in base alla sottoscrizione, all'offerta associata alla sottoscrizione e all'area di Azure di destinazione, nella valuta specificata.|
|-TargetRegion|(Facoltativo) Area di Azure di destinazione per la replica. Dato che Azure ha costi diversi per ogni area, usare questo parametro per generare report per una specifica area di Azure di destinazione. Il valore predefinito è WestUS2 o l'ultima area di destinazione usata. Vedere l'elenco delle [aree di destinazione supportate](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Facoltativo) Offerta associata alla sottoscrizione. Il valore predefinito è MS-AZR-0003P (pagamento in base al consumo).|
|-Currency|(Facoltativo) Valuta usata per visualizzare il costo nel report generato. Il valore predefinito è il dollaro USA ($) o l'ultima valuta usata. Vedere l'elenco delle [valute supportate](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Per impostazione predefinita, lo strumento è configurato per eseguire la profilatura e la generazione di report di un massimo di 1.000 VM. È possibile modificare questo limite cambiando il valore della chiave MaxVMsSupported nel file ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Esempi
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Generare un report con i valori predefiniti quando i dati profilati si trovano nell'unità locale
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Generare un report quando i dati profilati si trovano in un server remoto
È consigliabile avere l'accesso in lettura/scrittura alla directory remota.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Generare un report con una specifica larghezza di banda di cui verrà effettuato il provisioning per la replica
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Generare un report con un fattore di crescita del 5% al posto del valore predefinito pari al 30% 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Generare un report con un subset di dati profilati
Si supponga, ad esempio, di avere 30 giorni di dati profilati e di voler generare un report solo per 20 giorni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Generare un report per un valore RPO di 5 minuti
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Generare un report per l'area di Azure India meridionale con rupia indiana e ID offerta specifico
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Valore percentile usato per il calcolo
Quando lo strumento genera un report, usa il valore percentile predefinito di 95 per le operazioni di I/O al secondo in lettura/scrittura, le operazioni di I/O al secondo in scrittura e la varianza dei dati. Questi valori vendono raccolti durante la profilatura di tutte le VM. Questa metrica assicura che il picco percentile di 100 rilevabile nelle VM a causa di eventi temporanei non venga usato per determinare i requisiti in termini di account di archiviazione di destinazione e larghezza di banda di origine. Un evento temporaneo potrebbe essere, ad esempio, un processo di backup eseguito una volta al giorno, un'indicizzazione periodica di un database, un'attività di generazione di report di analisi o un altro evento temporizzato di breve durata.

L'uso di un valore percentile di 95 fornisce un quadro attendibile delle reali caratteristiche del carico di lavoro e offre prestazioni ottimali durante l'esecuzione dei carichi di lavoro in Azure. In genere non sarà necessario modificare questo numero. Se però si modifica il valore, ad esempio impostando un percentile di 90, è possibile aggiornare il file di configurazione ASRDeploymentPlanner.exe.config nella cartella predefinita e salvarlo per generare un nuovo report sui dati profilati esistenti.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Considerazioni sul fattore di crescita
È fondamentale tenere in considerazione la crescita nelle caratteristiche del carico di lavoro, supponendo un potenziale aumento dell'utilizzo nel tempo. Dopo aver applicato la protezione, se le caratteristiche del carico di lavoro cambiano, non è possibile passare a un account di archiviazione diverso per la protezione senza disabilitare e riabilitare la protezione stessa.

Si supponga, ad esempio, che attualmente la VM sia idonea per un account di replica di archiviazione Standard. Nei prossimi tre mesi è probabile che si verifichino questi cambiamenti:

1. Aumenterà il numero di utenti dell'applicazione eseguita nella VM.
2. In seguito all'aumento della varianza nella VM, la VM dovrà passare ad Archiviazione Premium per consentire alla replica di Azure Site Recovery di restare al passo.
3. Sarà necessario disabilitare e riabilitare la protezione per passare a un account di archiviazione Premium.

È consigliabile pianificare la crescita durante la pianificazione della distribuzione. Nonostante il valore predefinito sia pari al 30%, l'utente ha esperienza con il modello di utilizzo e le previsioni di crescita dell'applicazione e può modificare questo numero di conseguenza durante la generazione di un report. È anche possibile generare più report con vari fattori di crescita per gli stessi dati profilati e quindi determinare le raccomandazioni ottimali in termini di archiviazione di destinazione e larghezza di banda di origine. 

Il report di Microsoft Excel generato contiene le informazioni seguenti:

* [Riepilogo ambiente locale](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Raccomandazioni](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Selezione host di archiviazione delle VM](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [VM compatibili](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [VM incompatibili](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Requisito di archiviazione locale](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [Suddivisione in batch per la replica iniziale](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Stima dei costi](hyper-v-deployment-planner-cost-estimation.md)

![Report di Deployment Planner](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Misurare la velocità effettiva
Per stimare la velocità effettiva che Azure Site Recovery può ottenere dall'ambiente locale ad Azure durante la replica, eseguire lo strumento in modalità GetThroughput. Lo strumento calcola la velocità effettiva dal server in cui è in esecuzione. Idealmente, questo server è il server Hyper-V le cui VM verranno protette. 

### <a name="command-line-parameters"></a>Parametri della riga di comando 
Aprire una console della riga di comando e passare alla cartella dello strumento di pianificazione della distribuzione di Azure Site Recovery. Eseguire ASRDeploymentPlanner.exe con i parametri seguenti.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 Nome parametro | DESCRIZIONE |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|Tipo di virtualizzazione (VMware o Hyper-V).|
|-Directory|(Facoltativo) UNC o percorso della directory locale in cui vengono archiviati i dati profilati, ovvero i file generati durante la profilatura. Questi dati sono necessari per la generazione di report. Se non viene specificato un nome, come directory predefinita verrà usata la directory denominata ProfiledData nel percorso corrente.|
| -StorageAccountName | Nome dell'account di archiviazione usato per determinare la larghezza di banda utilizzata per la replica dei dati dall'ambiente locale ad Azure. Lo strumento carica i dati di test in questo account di archiviazione per determinare la larghezza di banda utilizzata. L'account di archiviazione deve essere un account per utilizzo generico versione 1 o versione 2.|
| -StorageAccountKey | Chiave dell'account di archiviazione usata per accedere all'account di archiviazione. Passare al portale di Azure e selezionare **Account di archiviazione** > *nome-account-archiviazione* > **Impostazioni** > **Chiavi di accesso** > **Key1**.|
| -VMListFile | File contenente l'elenco di VM da profilare per calcolare la larghezza di banda utilizzata. Il percorso del file può essere assoluto o relativo. Per Hyper-V, si tratta del file di output dell'operazione GetVMList. In caso di preparazione manuale, il file deve contenere un nome server o un indirizzo IP, seguito dal nome della VM separato con un carattere \ per riga. Il nome della VM specificato nel file deve essere uguale al nome della VM nell'host Hyper-V.<br><br>**Esempio**: VMList.txt contiene le VM seguenti:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Facoltativo) Ambiente di destinazione per l'account di archiviazione di Azure. Può trattarsi di uno di tre valori: AzureCloud, AzureUSGovernment o AzureChinaCloud. Il valore predefinito è AzureCloud. Usare il parametro quando l'area di Azure di destinazione è Azure US Government o Azure Cina.|

### <a name="example"></a>Esempio
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Considerazioni sulla velocità effettiva

Lo strumento crea diversi file asrvhdfile*numero*.vhd (dove *numero* è il numero di file) da 64 MB nella directory specificata. Lo strumento carica i file nell'account di archiviazione per determinare la velocità effettiva. Dopo aver misurato la velocità effettiva, lo strumento elimina tutti i file dall'account di archiviazione e dal server locale. Se per qualsiasi motivo lo strumento viene terminato mentre sta calcolando la velocità effettiva, non elimina i file dall'account di archiviazione o dal server locale. È necessario eliminarli manualmente.

La velocità effettiva viene misurata in un momento specificato ed è la velocità effettiva massima che Azure Site Recovery può raggiungere durante la replica, se tutti gli altri fattori rimangono invariati. Se, ad esempio, un'applicazione inizia a utilizzare più larghezza di banda nella stessa rete, la velocità effettiva varierà durante la replica. Il risultato della velocità effettiva misurata è diverso se l'operazione GetThroughput viene eseguita quando le VM protette hanno una varianza dei dati elevata. 

Per determinare i livelli di velocità effettiva raggiungibili in vari momenti, è consigliabile eseguire lo strumento in momenti diversi durante la profilatura. Nel report, lo strumento indica l'ultima velocità effettiva misurata.

> [!NOTE]
> Eseguire lo strumento in un server con le stesse caratteristiche di archiviazione e CPU di un server Hyper-V.

Per la replica, impostare la larghezza di banda consigliata per soddisfare il valore RPO il 100% del tempo. Dopo avere impostato la larghezza di banda appropriata, se non è visibile un aumento della velocità effettiva ottenuta indicata dallo strumento, seguire questa procedura:

1. Verificare se è presente un problema di qualità del servizio (QoS) della rete che limita la velocità effettiva di Azure Site Recovery.
2. Verificare se l'insieme di credenziali di Azure Site Recovery si trova nell'area fisica di Microsoft Azure più vicina supportata per ridurre al minimo la latenza di rete.
3. Controllare le caratteristiche di archiviazione locale per determinare se sia possibile migliorare l'hardware (ad esempio, passando da unità HDD a unità SSD).

    
## <a name="next-steps"></a>Passaggi successivi
* [Analizzare il report generato](hyper-v-deployment-planner-analyze-report.md)
