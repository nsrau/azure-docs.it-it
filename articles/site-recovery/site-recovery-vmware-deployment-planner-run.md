---
title: Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure | Microsoft Docs
description: Questo articolo descrive l'esecuzione di Azure Site Recovery Deployment Planner per lo scenario da VMware ad Azure.
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: 87e124b1dc14ad34d1d790d463ce1f5ded18f74b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Eseguire Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure
Questo articolo contiene la guida dell'utente di Azure Site Recovery Deployment Planner per distribuzioni di produzione da VMware ad Azure.


## <a name="modes-of-running-deployment-planner"></a>Modalità di esecuzione di Deployment Planner
È possibile eseguire lo strumento da riga di comando (ASRDeploymentPlanner.exe) in una delle quattro modalità seguenti:

1.  [Profilatura](#profile-vmware-vms)
2.  [Generazione di report](#generate-report)
3.  [Misurare la velocità effettiva](#get-throughput)

Eseguire prima lo strumento in modalità di profilatura per raccogliere le operazioni di I/O al secondo e la varianza dati delle VM. Eseguire quindi lo strumento per generare il report e determinare i requisiti di larghezza di banda e archiviazione e il costo del ripristino di emergenza.

## <a name="profile-vmware-vms"></a>Profilare VM VMware
Nella modalità di profilatura lo strumento Deployment Planner si connette al server vCenter o all'host vSphere ESXi per raccogliere dati sulle prestazioni della VM.

* La profilatura non influisce sulle prestazioni delle VM di produzione perché non vengono stabilite connessioni dirette alle VM. Tutti i dati sulle prestazioni vengono raccolti dal server vCenter o dall'host vSphere ESXi.
* Per assicurarsi che l'impatto della profilatura sul server sia trascurabile, lo strumento effettua una query sul server vCenter o sull'host vSphere ESXi ogni 15 minuti. Questo intervallo di query non compromette tuttavia la precisione della profilatura perché lo strumento archivia i dati del contatore delle prestazioni di ogni minuto.

### <a name="create-a-list-of-vms-to-profile"></a>Creare un elenco delle VM da profilare
Prima di tutto è necessario un elenco delle VM da profilare. È possibile ottenere tutti i nomi delle VM in un server vCenter o in un host vSphere ESXi usando i comandi di VMware vSphere PowerCLI della procedura seguente. In alternativa, è possibile elencare in un file i nomi descrittivi o gli indirizzi IP delle VM che si vuole profilare manualmente.

1. Accedere alla VM in cui è installato VMware vSphere PowerCLI.
2. Aprire la console di VMware vSphere PowerCLI.
3. Verificare che i criteri di esecuzione siano abilitati per lo script. Se sono disabilitati, avviare la console di VMware vSphere PowerCLI in modalità amministratore e quindi abilitarli eseguendo questo comando:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Se Connect-VIServer non viene riconosciuto come nome di cmdlet, potrebbe essere necessario eseguire questo comando.

            Add-PSSnapin VMware.VimAutomation.Core

5. Per ottenere tutti i nomi delle VM in un server vCenter/host vSphere ESXi e archiviare l'elenco in un file TXT, eseguire i due comandi elencati qui.
Sostituire &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo; e &lsaquo;outputfile.txt&rsaquo; con i propri input.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. Aprire il file di output nel Blocco note e quindi copiare i nomi di tutte le VM che si vuole profilare in un altro file (ad esempio, ProfileVMList.txt) inserendo un nome di VM per riga. Questo file viene usato come input per il parametro *-VMListFile* dello strumento da riga di comando.

    ![Elenco di nomi di VM in Deployment Planner
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Avviare la profilatura
Dopo aver ottenuto l'elenco di macchine virtuali da profilare, è possibile eseguire lo strumento in modalità di profilatura. Di seguito è riportato l'elenco dei parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità di profilatura.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nome parametro | DESCRIZIONE |
|---|---|
| -Operation | StartProfiling |
| -Server | Nome di dominio completo o indirizzo IP del server vCenter o dell'host vSphere ESXi con le VM da profilare.|
| -User | Nome utente per la connessione al server vCenter o all'host vSphere ESXi. L'utente deve avere almeno l'accesso in sola lettura.|
| -VMListFile | File contenente l'elenco di VM da profilare. Il percorso del file può essere assoluto o relativo. Il file deve contenere un solo nome/indirizzo IP di VM per riga. Il nome della macchina virtuale specificato nel file deve corrispondere al nome della VM nel server vCenter o nell'host vSphere ESXi.<br>Ad esempio, il file VMList.txt contiene le VM seguenti:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|Numero di minuti per i quali eseguire la profilatura. Il valore minimo è 30 minuti.|
|-NoOfHoursToProfile|Numero di ore per le quali eseguire la profilatura.|
| -NoOfDaysToProfile | Numero di giorni per i quali eseguire la profilatura. È consigliabile eseguire la profilatura per più di 7 giorni per far sì che il modello di carico di lavoro nell'ambiente venga osservato nel periodo specificato e usato per fornire un'indicazione accurata. |
|-Virtualization|Specificare il tipo di virtualizzazione (VMware o Hyper-V).|
| -Directory | (Facoltativo) UNC (Universal Naming Convention) o percorso della directory locale per l'archiviazione dei dati generati durante la profilatura. Per impostazione predefinita, se non viene specificato un nome di directory, verrà usata la directory denominata "ProfiledData" nel percorso corrente. |
| -Password | (Facoltativo) Password da usare per connettersi al server vCenter o all'host vSphere ESXi. Se non se ne specifica una in questa fase, verrà chiesta all'esecuzione del comando.|
|-Port|(Facoltativo) Numero di porta per la connessione all'host vCenter/ESXi. La porta predefinita è 443.|
|-Protocol| (Facoltativo) Specificare il protocollo 'http' o 'https' per la connessione a vCenter. Il protocollo predefinito è https.|
| -StorageAccountName | (Facoltativo) Nome dell'account di archiviazione usato per determinare la velocità effettiva ottenibile per la replica dei dati dall'ambiente locale ad Azure. Lo strumento carica i dati di test in questo account di archiviazione per calcolare la velocità effettiva. L'account di archiviazione deve essere un account per utilizzo generico versione 1 o di archiviazione versione 2 (per utilizzo generico versione 2)|
| -StorageAccountKey | (Facoltativo) Chiave dell'account di archiviazione usata per accedere all'account di archiviazione. Passare al portale di Azure e scegliere Account di archiviazione > <*nome account di archiviazione*> > Impostazioni > Chiavi di accesso > Key1. |
| -Environment | (Facoltativo) Ambiente dell'account di archiviazione di Azure di destinazione. Può trattarsi di uno di tre valori: AzureCloud, AzureUSGovernment, AzureChinaCloud. Il valore predefinito è AzureCloud. Usare il parametro quando l'area di destinazione del cloud di Azure è Azure US Government o Azure Cina. |


È consigliabile profilare le VM per oltre 7 giorni. Se il modello di varianza varia in un mese, è consigliabile eseguire la profilatura durante la settimana quando si osserva la varianza massima. La soluzione ottimale consiste nell'eseguire la profilatura per 31 giorni per ottenere l'indicazione migliore. Durante il periodo di profilatura, ASRDeploymentPlanner.exe rimane in esecuzione. Lo strumento accetta l'input della durata della profilatura in giorni. Per un rapido test dello strumento o per il modello di verifica è possibile eseguire la profilatura per alcune ore o minuti. La durata minima della profilatura è di 30 minuti.

Durante la profilatura è possibile passare il nome e la chiave di un account di archiviazione per determinare la velocità effettiva che Site Recovery può raggiungere al momento della replica dal server di configurazione o dal server di elaborazione ad Azure. Se il nome e la chiave dell'account di archiviazione non vengono passati durante la profilatura, lo strumento non calcola la velocità effettiva ottenibile.

È possibile eseguire più istanze dello strumento per diversi set di VM. Assicurarsi che i nomi delle VM non siano ripetuti nei set di profilatura. Se, ad esempio, sono state profilate dieci VM (da VM1 a VM10) e dopo alcuni giorni si vogliono profilare altre cinque VM (da VM11 a VM15), è possibile eseguire lo strumento da un'altra console della riga di comando per il secondo set di VM (da VM11 a VM15). Verificare che le VM del secondo set non abbiano nomi uguali a quelli della prima istanza di profilatura oppure usare una directory di output diversa per la seconda esecuzione. Se vengono usate due istanze dello strumento per profilare le stesse VM usando la stessa directory di output, il report generato sarà errato.

Per impostazione predefinita, lo strumento è configurato per profilare, generando il report corrispondente, fino a 1000 VM. È possibile modificare questo limite cambiando il valore della chiave MaxVMsSupported nel file *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Per profilare, ad esempio, 1500 VM con le impostazioni predefinite, creare due file VMList.txt, uno con l'elenco di 1000 VM e l'altro di 500 VM. Eseguire le due istanze di Azure Site Recovery Deployment Planner, una con VMList1.txt e l'altra con VMList2.txt. È possibile usare lo stesso percorso di directory per archiviare i dati di profilatura delle VM di entrambi i file VMList.

È stato osservato che in base alla configurazione hardware, in particolare alle dimensioni della RAM del server da cui viene eseguito lo strumento per generare il report, l'operazione può non riuscire a causa di memoria insufficiente. Se l'hardware è appropriato, è possibile modificare MaxVMsSupported in base a un valore maggiore.  

In presenza di più server vCenter, è necessario eseguire un'istanza di ASRDeploymentPlanner per ogni server vCenter per la profilatura.

Le configurazioni delle VM vengono acquisite una volta all'inizio dell'operazione di profilatura e archiviate in un file denominato VMDetailList.xml. Queste informazioni vengono usate quando viene generato il report. Eventuali modifiche apportate alla configurazione delle VM (ad esempio, un incremento del numero di core, dischi o schede di interfaccia di rete) dall'inizio alla fine della profilatura non vengono acquisite. Se una VM profilata è stata modificata nel corso della profilatura, nell'anteprima pubblica è disponibile la soluzione alternativa seguente per ottenere i dettagli più recenti della VM durante la generazione del report:

* Eseguire il backup di VMdetailList.xml ed eliminare il file dal percorso corrente.
* Passare gli argomenti -User e -Password al momento della generazione di report.

Il comando di profilatura genera diversi file nella directory della profilatura. Non eliminare nessuno dei file, altrimenti la generazione del report verrà compromessa.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Esempio 1: Profilare le VM per 30 giorni e determinare la velocità effettiva dall'ambiente locale ad Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Esempio 2: Profilare le VM per 15 giorni

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Esempio 3: Profilare le VM per 60 minuti per un rapido test dello strumento
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Esempio 4: Profilare le VM per 2 ore per un modello di verifica
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Se il server in cui viene eseguito lo strumento viene riavviato o si è arrestato in modo anomalo oppure si chiude lo strumento usando CTRL+C, i dati di profilatura vengono conservati. Potrebbero tuttavia andare persi gli ultimi 15 minuti di dati profilati. In tal caso, eseguire nuovamente lo strumento in modalità di profilatura dopo il riavvio del server.
>* Quando vengono passati il nome e la chiave di un account di archiviazione, lo strumento misura la velocità effettiva all'ultimo passaggio della profilatura. Se lo strumento viene chiuso prima del completamento della profilatura, la velocità effettiva non verrà calcolata. Per determinare la velocità effettiva prima di generare il report, è possibile eseguire l'operazione GetThroughput dalla console della riga di comando. In caso contrario, il report generato non conterrà le informazioni sulla velocità effettiva.


## <a name="generate-report"></a>Generare il report
Lo strumento genera un file di Microsoft Excel con attivazione macro (file XLSM) come output del report, con il riepilogo di tutte le indicazioni di distribuzione. Il report viene denominato DeploymentPlannerReport_<unique numeric identifier>.xlsm e inserito nella directory specificata.

Al termine della profilatura, è possibile eseguire lo strumento in modalità di generazione di report. La tabella seguente contiene un elenco dei parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità di generazione di report.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nome parametro | DESCRIZIONE |
|-|-|
| -Operation | GenerateReport |
| -Server |  Nome dominio completo o indirizzo IP (usare lo stesso nome o indirizzo IP usato al momento della profilatura) del server vCenter/vSphere in cui si trovano le VM profilate per le quali generare il report. Si noti che, se è stato usato un server vCenter al momento della profilatura, non è possibile usare un server vSphere per la generazione di report e viceversa.|
| -VMListFile | File contenente l'elenco di VM profilate per cui deve essere generato il report. Il percorso del file può essere assoluto o relativo. Il file deve contenere un solo nome o indirizzo IP di VM per riga. I nomi delle VM specificati nel file devono corrispondere ai nomi delle VM nel server vCenter o nell'host vSphere ESXi e usati durante la profilatura.|
|-Virtualization|Specificare il tipo di virtualizzazione (VMware o Hyper-V).|
| -Directory | (Facoltativo) UNC o percorso della directory locale in cui vengono archiviati i dati profilati, ovvero i file generati durante la profilatura. Questi dati sono necessari per la generazione di report. Se non è specificato nessun nome, verrà usata la directory "ProfiledData". |
| -GoalToCompleteIR | (Facoltativo) Numero di ore in cui deve essere completata la replica iniziale delle VM profilate. Il report generato indica il numero di VM per cui è possibile eseguire la replica iniziale nel tempo specificato. Il valore predefinito è 72 ore. |
| -User | (Facoltativo) Nome utente da usare per la connessione al server vCenter/vSphere. Il nome viene usato per recuperare le informazioni di configurazione più recenti per le VN, ad esempio numero di dischi, numero di core e numero di schede di interfaccia di rete, da usare nel report. Se il nome non viene specificato, verranno usate le informazioni di configurazione raccolte all'inizio della profilatura. |
| -Password | (Facoltativo) Password da usare per connettersi al server vCenter o all'host vSphere ESXi. Se la password non viene specificata come parametro, verrà chiesta in un secondo momento all'esecuzione del comando. |
|-Port|(Facoltativo) Numero di porta per la connessione all'host vCenter/ESXi. La porta predefinita è 443.|
|-Protocol|(Facoltativo) Specificare il protocollo 'http' o 'https' per la connessione a vCenter. Il protocollo predefinito è https.|
| -DesiredRPO | (Facoltativo) Obiettivo del punto di ripristino desiderato, in minuti. Il valore predefinito è 15 minuti.|
| -Bandwidth | Larghezza di banda in Mbps. Il parametro viene usato per calcolare il valore RPO ottenibile per la larghezza di banda specificata. |
| -StartDate | (Facoltativo) Data e ora di inizio in MM-GG-AAAA:HH:MM, in formato 24 ore. *StartDate* deve essere specificato con *EndDate*. Quando StartDate è specificato, il report viene generato per i dati profilati raccolti nell'intervallo compreso tra StartDate ed EndDate. |
| -EndDate | (Facoltativo) Data e ora di fine in MM-GG-AAAA:HH:MM, in formato 24 ore. *EndDate* deve essere specificato con *StartDate*. Quando EndDate è specificato, il report viene generato per i dati profilati raccolti nell'intervallo compreso tra StartDate ed EndDate. |
| -GrowthFactor | (Facoltativo) Fattore di crescita, espresso come percentuale. Il valore predefinito è 30%. |
| -UseManagedDisks | (Facoltativo) UseManagedDisks: Yes/No. Il valore predefinito è Yes. Il numero di macchine virtuali che possono essere inserite in un singolo account di archiviazione viene calcolato a seconda che il failover e il failover di test delle macchine virtuali vengano eseguiti su un disco gestito anziché su un disco non gestito. |
|-SubscriptionId |(Facoltativo) GUID della sottoscrizione. Usare questo parametro per generare un report di stima dei costi con i prezzi più recenti in base alla sottoscrizione, all'offerta associata alla sottoscrizione e alla specifica area di destinazione di Azure nella valuta indicata.|
|-TargetRegion|(Facoltativo) Area di Azure di destinazione per la replica. Azure ha costi diversi per ogni area, quindi usare questo parametro per generare report per la specifica area di destinazione di Azure.<br>Il valore predefinito è WestUS2 o l'ultima area di destinazione usata.<br>Vedere l'elenco delle [aree di destinazione supportate](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Facoltativo) Offerta associata alla sottoscrizione. Il valore predefinito è MS-AZR-0003P (pagamento in base al consumo).|
|-Currency|(Facoltativo) Valuta usata per visualizzare il costo nel report generato. Il valore predefinito è Dollaro USA ($) o l'ultima valuta usata.<br>Vedere l'elenco delle [valute supportate](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Per impostazione predefinita, lo strumento è configurato per profilare, generando il report corrispondente, fino a 1000 VM. È possibile modificare questo limite cambiando il valore della chiave MaxVMsSupported nel file *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Esempio 1: Generare un report con valori predefiniti quando i dati profilati si trovano nell'unità locale
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualiztion VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Esempio 2: Generare un report quando i dati profilati si trovano in un server remoto
È consigliabile avere l'accesso in lettura/scrittura alla directory remota.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Esempio 3: Generare un report con una larghezza di banda specificata e l'obiettivo di completare la replica iniziale nel tempo specificato
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Esempio 4: Generare un report con un fattore di crescita del 5% al posto del valore predefinito pari al 30%
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualzation VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Esempio 5: Generare un report con un subset di dati profilati
Si supponga, ad esempio, di avere 30 giorni di dati profilati e di voler generare un report solo per 20 giorni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Esempio 6: Generare un report per un valore RPO di 5 minuti
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Esempio 7: Generare un report per l'area di Azure India meridionale con rupia indiana e ID offerta specifico
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Valore percentile usato per il calcolo
**Valore percentile predefinito delle metriche delle prestazioni raccolte durante la profilatura usato dallo strumento quando genera un report**

Lo strumento usa come valore predefinito il 95° percentile di operazioni di I/O al secondo in lettura/scrittura, operazioni di I/O al secondo in scrittura e varianza dei dati raccolte durante la profilatura di tutte le macchine virtuali. Questa metrica assicura che lo spike del 100° percentile che può essere rilevato dalle VM a causa di eventi temporanei non venga usato per determinare i requisiti dell'account di archiviazione di destinazione e della larghezza di banda di origine. Un evento temporaneo può essere, ad esempio, un processo di backup eseguito una volta al giorno, un'indicizzazione periodica di un database, un'attività di generazione di report analitici o altri eventi simili temporizzati di breve durata.

L'uso dei valori di 95° percentile fornisce un quadro attendibile delle reali caratteristiche del carico di lavoro e offre prestazioni ottimali quando i carichi di lavoro sono in esecuzione in Azure. In genere questo numero non viene modificato. Se invece si sostituisce il valore, ad esempio con il 90° percentile, è possibile aggiornare il file di configurazione *ASRDeploymentPlanner.exe.config* nella cartella predefinita e salvarlo per generare un nuovo report sui dati profilati esistenti.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Considerazioni sul fattore di crescita
**Perché considerare il fattore di crescita quando si pianificano le distribuzioni**

È fondamentale prendere in considerazione la crescita nelle caratteristiche del carico di lavoro, supponendo un potenziale aumento dell'utilizzo nel tempo. Dopo aver applicato la protezione, se le caratteristiche del carico di lavoro cambiano, non è possibile passare a un account di archiviazione diverso per la protezione senza disabilitare e riabilitare la protezione stessa.

Si supponga, ad esempio, che attualmente la VM sia idonea per un account di replica di archiviazione Standard. Nei tre mesi successivi, potrebbero verificarsi diversi cambiamenti:

* Aumenterà il numero di utenti dell'applicazione eseguita nella VM.
* In seguito all'aumento della varianza risultante, la VM dovrà passare all'Archiviazione Premium per consentire alla replica di Site Recovery di restare al passo.
* Sarà quindi necessario disabilitare e riabilitare la protezione per passare a un account di archiviazione Premium.

È consigliabile pianificare la crescita durante la pianificazione della distribuzione e mentre il valore predefinito è pari al 30%. L'utente ha esperienza con il modello di utilizzo dell'applicazione e le previsioni di crescita e può modificare questo valore di conseguenza durante la generazione di un report. È anche possibile generare più report con svariati fattori di crescita per gli stessi dati profilati e determinare le raccomandazioni ottimali in termini di archiviazione di destinazione e larghezza di banda di origine.

Il report di Microsoft Excel generato contiene le informazioni seguenti:

* [Riepilogo ambiente locale](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Raccomandazioni](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [Selezione host di archiviazione delle VM](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [VM compatibili](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [VM incompatibili](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Stima dei costi](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Misurare la velocità effettiva

Per stimare la velocità effettiva che Site Recovery può ottenere dall'ambiente locale ad Azure durante la replica, eseguire lo strumento in modalità GetThroughput. Lo strumento calcola la velocità effettiva dal server in cui è in esecuzione. Idealmente, questo server è basato sulle linee guida di ridimensionamento del server di configurazione. Se i componenti dell'infrastruttura di Site Recovery sono già stati distribuiti in locale, eseguire lo strumento nel server di configurazione.

Aprire una console della riga di comando e passare alla cartella dello strumento di pianificazione della distribuzione di Site Recovery. Eseguire ASRDeploymentPlanner.exe con i parametri seguenti.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nome parametro | DESCRIZIONE |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Specificare il tipo di virtualizzazione (VMware o Hyper-V).|
| -Directory | (Facoltativo) UNC o percorso della directory locale in cui vengono archiviati i dati profilati, ovvero i file generati durante la profilatura. Questi dati sono necessari per la generazione di report. Se non viene specificato un nome di directory, viene usata la directory "ProfiledData". |
| -StorageAccountName | Nome dell'account di archiviazione usato per determinare la larghezza di banda utilizzata per la replica dei dati dall'ambiente locale ad Azure. Lo strumento carica i dati di test in questo account di archiviazione per determinare la larghezza di banda utilizzata. L'account di archiviazione deve essere un account per utilizzo generico versione 1 o di archiviazione versione 2 (per utilizzo generico versione 2).|
| -StorageAccountKey | Chiave dell'account di archiviazione usata per accedere all'account di archiviazione. Passare al portale di Azure e scegliere Account di archiviazione > <*nome account di archiviazione*> > Impostazioni > Chiavi di accesso > Key1 oppure una chiave di accesso primaria per un account di archiviazione classico. |
| -VMListFile | File contenente l'elenco di VM da profilare per calcolare la larghezza di banda utilizzata. Il percorso del file può essere assoluto o relativo. Il file deve contenere un solo nome/indirizzo IP di VM per riga. I nomi delle VM specificati nel file devono corrispondere ai nomi nel server vCenter o nell'host vSphere ESXi.<br>Ad esempio, il file VMList.txt contiene le VM seguenti:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (Facoltativo) Ambiente dell'account di archiviazione di Azure di destinazione. Può trattarsi di uno di tre valori: AzureCloud, AzureUSGovernment, AzureChinaCloud. Il valore predefinito è AzureCloud. Usare il parametro quando l'area di destinazione del cloud di Azure è Azure US Government o Azure Cina. |

Lo strumento crea diversi file asrvhdfile<#>.vhd (dove "#" è il numero di file) da 64 MB nella directory specificata. Lo strumento carica i file nell'account di archiviazione per determinare la velocità effettiva. Dopo aver misurato la velocità effettiva, lo strumento elimina tutti i file dall'account di archiviazione e dal server locale. Se per qualsiasi motivo lo strumento viene terminato mentre sta calcolando la velocità effettiva, non elimina i file dalla risorsa di archiviazione o dal server locale. Sarà necessario eliminarli manualmente.

La velocità effettiva viene misurata in un momento specificato ed è la velocità effettiva massima che Site Recovery può raggiungere durante la replica se tutti gli altri fattori rimangono invariati. Se, ad esempio, un'applicazione inizia a utilizzare più larghezza di banda nella stessa rete, la velocità effettiva varierà durante la replica. Se si esegue il comando GetThroughput da un server di configurazione, lo strumento non rileva le VM protette e la replica in corso. Il risultato della velocità effettiva misurata è diverso se l'operazione GetThroughput viene eseguita quando le VM protette hanno una varianza dei dati elevata. È consigliabile eseguire lo strumento in momenti diversi durante la profilatura per determinare i livelli di velocità effettiva ottenibili in più momenti. Nel report, lo strumento indica l'ultima velocità effettiva misurata.

### <a name="example"></a>Esempio
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Eseguire lo strumento in un server con le stesse caratteristiche di archiviazione e CPU del server di configurazione.
>
> Per la replica, impostare la larghezza di banda consigliata per soddisfare il valore RPO il 100% del tempo. Dopo avere impostato la larghezza di banda appropriata, se non è visibile un aumento della velocità effettiva ottenuta indicata dallo strumento, seguire questa procedura:
>
>  1. Verificare se sono presenti requisiti di qualità del servizio (QoS) che limitano la velocità effettiva di Site Recovery.
>
>  2. Verificare se l'insieme di credenziali di Site Recovery si trova nell'area fisica di Microsoft Azure più vicina supportata per ridurre al minimo la latenza di rete.
>
>  3. Controllare le caratteristiche di archiviazione locale per determinare se sia possibile migliorare l'hardware (ad esempio, passando da unità HDD a unità SSD).
>
>  4. Modificare le impostazioni di Site Recovery nel server di elaborazione per [aumentare la larghezza di banda di rete usata per la replica](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Passaggi successivi
* [Analizzare il report generato](site-recovery-vmware-deployment-planner-analyze-report.md).
