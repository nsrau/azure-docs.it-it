---
title: Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure | Microsoft Docs
description: Guida dell&quot;utente di Azure Site Recovery Deployment Planner.
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
ms.date: 06/05/2017
ms.author: nisoneji
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 3c72026a7a6c6b348a77560c7f35d76d93c75e17
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Questo articolo contiene la guida dell'utente di Azure Site Recovery Deployment Planner per distribuzioni di produzione da VMware ad Azure.

## <a name="overview"></a>Panoramica

Prima di iniziare a proteggere le macchine virtuali VMware con Site Recovery, allocare larghezza di banda sufficiente, in base alla frequenza di modifica dei dati giornaliera per soddisfare l'obiettivo del punto di ripristino (RPO) desiderato. Assicurarsi di distribuire il numero corretto di server di configurazione e di server di elaborazione in locale.

È anche necessario creare il tipo e il numero corretti di account di archiviazione di Azure di destinazione. Per creare account di archiviazione Standard o Premium, si considera la crescita nei server di produzione di origine a causa del maggiore utilizzo nel tempo. Si sceglie il tipo di archiviazione per ogni VM in base alle caratteristiche del carico di lavoro (ad esempio, le operazioni di I/O al secondo in lettura/scrittura o la varianza dati) e ai limiti di Site Recovery.

L'anteprima pubblica di Site Recovery Deployment Planner è uno strumento da riga di comando attualmente disponibile solo per lo scenario da VMware ad Azure. Questo strumento consente di profilare le VM VMware in modalità remota, senza alcun impatto sulla produzione, per determinare i requisiti di larghezza di banda e archiviazione di Azure per operazioni di replica e failover di test. È possibile eseguire lo strumento senza installare alcun componente di Site Recovery in locale. Per ottenere risultati accurati sulla velocità effettiva ottenuta, è tuttavia consigliabile eseguire lo strumento di pianificazione in un server Windows che soddisfi i requisiti minimi del server di configurazione di Site Recovery che dovrà essere successivamente distribuito nell'ambito di uno dei primi passaggi della distribuzione di produzione.

Lo strumento indica i dettagli seguenti:

**Valutazione della compatibilità**

* Valutazione dell'idoneità delle VM in base a numero di dischi, dimensioni dei dischi, operazioni di I/O al secondo, varianza e tipo di avvio (EFI/BIOS)
* Larghezza di banda stimata necessaria per la replica differenziale

**Valutazione della larghezza di banda di rete necessaria rispetto al valore RPO**

* Larghezza di banda stimata necessaria per la replica differenziale
* Velocità effettiva ottenibile da Site Recovery dall'ambiente locale ad Azure
* Numero di VM da riunire in batch in base alla larghezza di banda stimata per completare la replica iniziale in un determinato periodo di tempo

**Requisiti dell'infrastruttura di Azure**

* Requisito relativo al tipo di archiviazione (account di archiviazione Standard o Premium) per ogni VM
* Numero totale di account di archiviazione Standard e Premium da configurare per la replica
* Suggerimenti di denominazione degli account di archiviazione in base alle linee guida di archiviazione di Azure
* Selezione host degli account di archiviazione per tutte le VM
* Numero di core di Azure da configurare prima del failover di test o del failover nella sottoscrizione
* Dimensioni consigliate per ogni VM di Azure locale

**Requisiti dell'infrastruttura locale**
* Numero di server di configurazione e di server di elaborazione da distribuire in locale

>[!IMPORTANT]
>
>Poiché è probabile che l'utilizzo aumenti nel corso del tempo, tutti i calcoli dello strumento precedenti vengono eseguiti presumendo un fattore di crescita del 30% nelle caratteristiche del carico di lavoro e usando un valore di 95° percentile di tutte le metriche di profilatura (operazioni di I/O al secondo in lettura/scrittura, varianza e così via). Entrambi questi elementi (fattore di crescita e calcolo percentile) sono configurabili. Per altre informazioni sul fattore di crescita, vedere la sezione "Considerazioni sul fattore di crescita". Per altre informazioni sul valore percentile, vedere la sezione "Valore percentile usato per il calcolo".
>

## <a name="requirements"></a>Requisiti
Lo strumento prevede due fasi principali: profilatura e generazione di report. È anche disponibile una terza opzione per calcolare solo la velocità effettiva. I requisiti per il server da cui vengono avviate la profilatura e la misurazione della velocità effettiva sono elencati nella tabella seguente:

| Requisito server | Descrizione|
|---|---|
|Profilatura e misurazione della velocità effettiva| <ul><li>Sistema operativo: Microsoft Windows Server 2012 R2<br>(idealmente corrispondente almeno alle [dimensioni consigliate per il server di configurazione](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configurazione del computer: 8 vCPU, 16 GB di RAM, disco rigido da 300 GB</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)</li><li>[Microsoft Visual C++ Redistributable per Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Accesso Internet ad Azure da questo server</li><li>Account di archiviazione di Azure</li><li>Accesso di amministratore al server</li><li>Almeno 100 GB di spazio libero su disco (presumendo 1000 VM con una media di tre dischi ognuna, profilate per 30 giorni)</li><li>Le impostazioni a livello di statistiche di VMware vCenter devono essere impostate su 2 o su un valore superiore</li></ul>|
| Generazione di report | Un PC o server Windows con Microsoft Excel 2013 o versione successiva |
| Autorizzazioni utente | Autorizzazioni di sola lettura per l'account utente usato per accedere al server VMware vCenter o all'host VMware vSphere ESXi durante la profilatura |

> [!NOTE]
>
>Lo strumento può profilare solo VM con dischi VMDK e RDM. Non può profilare VM con dischi iSCSI o NFS. Site Recovery supporta dischi iSCSI e NFS per i server VMware, ma, poiché lo strumento di pianificazione della distribuzione non è nel guest ed esegue la profilatura usando solo i contatori delle prestazioni di vCenter, lo strumento non ha visibilità per questi tipi di dischi.
>

## <a name="download-and-extract-the-public-preview"></a>Scaricare ed estrarre l'anteprima pubblica
1. Scaricare la versione più recente dell'[anteprima pubblica di Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).  
Lo strumento è compresso in una cartella ZIP. La versione corrente dello strumento supporta solo lo scenario da VMware ad Azure.

2. Copiare la cartella ZIP nel server Windows dal quale si intende eseguire lo strumento.  
È possibile eseguire lo strumento da Windows Server 2012 R2 se il server ha accesso alla rete per connettersi al server vCenter o all'host vSphere ESXi in cui si trovano le VM da profilare. È tuttavia consigliabile eseguire lo strumento in un server la cui configurazione hardware soddisfi la [linea guida per il ridimensionamento del server di configurazione](https://aka.ms/asr-v2a-on-prem-components). Se i componenti di Site Recovery sono già stati distribuiti in locale, eseguire lo strumento dal server di configurazione.

 È consigliabile avere la stessa configurazione hardware del server di configurazione (che ha un server di elaborazione predefinito) per il server in cui si esegue lo strumento. Tale configurazione assicura che la velocità effettiva ottenuta segnalata dallo strumento corrisponda alla velocità effettiva che Site Recovery può raggiungere durante la replica. Il calcolo della velocità effettiva dipende dalla larghezza di banda di rete disponibile nel server e dalla configurazione hardware (CPU, memoria e così via) del server. Se si esegue lo strumento da un altro server, la velocità effettiva viene calcolata da tale server a Microsoft Azure. Poiché inoltre la configurazione hardware del server può essere diversa da quella del server di configurazione, la velocità effettiva ottenuta che viene segnalata dallo strumento può non essere corretta.

3. Estrarre la cartella ZIP.  
La cartella contiene più file e sottocartelle. Il file eseguibile è ASRDeploymentPlanner.exe e si trova nella cartella padre.

    Esempio:  
    Copiare il file ZIP nell'unità E:\ ed estrarlo.
   E:\ASR Deployment Planner-Preview_v1.2.zip

    E:\ASR Deployment Planner-Preview_v1.2\ ASR Deployment Planner-Preview_v1.2\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>Capabilities
È possibile eseguire lo strumento da riga di comando (ASRDeploymentPlanner.exe) in una delle tre modalità seguenti:

1. Profilatura  
2. Generazione di report
3. Misurare la velocità effettiva

Eseguire prima lo strumento in modalità di profilatura per raccogliere le operazioni di I/O al secondo e la varianza dati delle VM. Eseguire quindi lo strumento per generare il report e determinare i requisiti di larghezza di banda e archiviazione.

## <a name="profiling"></a>Profilatura
Nella modalità di profilatura lo strumento Deployment Planner si connette al server vCenter o all'host vSphere ESXi per raccogliere dati sulle prestazioni della VM.

* La profilatura non influisce sulle prestazioni delle VM di produzione perché non vengono stabilite connessioni dirette alle VM. Tutti i dati sulle prestazioni vengono raccolti dal server vCenter o dall'host vSphere ESXi.
* Per assicurarsi che l'impatto della profilatura sul server sia trascurabile, lo strumento effettua una query sul server vCenter o sull'host vSphere ESXi ogni 15 minuti. Questo intervallo di query non compromette tuttavia la precisione della profilatura perché lo strumento archivia i dati del contatore delle prestazioni di ogni minuto.

### <a name="create-a-list-of-vms-to-profile"></a>Creare un elenco delle VM da profilare
Prima di tutto è necessario un elenco delle VM da profilare. È possibile ottenere tutti i nomi delle VM in un server vCenter o in un host vSphere ESXi usando i comandi di VMware vSphere PowerCLI della procedura seguente. In alternativa, è possibile elencare in un file i nomi descrittivi o gli indirizzi IP delle VM che si vuole profilare manualmente.

1. Accedere alla VM in cui è installato VMware vSphere PowerCLI.
2. Aprire la console di VMware vSphere PowerCLI.
3. Verificare che i criteri di esecuzione siano abilitati per lo script. Se sono disabilitati, avviare la console di VMware vSphere PowerCLI in modalità amministratore e quindi abilitarli eseguendo questo comando:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Per ottenere tutti i nomi delle VM in un server vCenter/host vSphere ESXi e archiviare l'elenco in un file TXT, eseguire i due comandi elencati qui.
Sostituire &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo; e &lsaquo;outputfile.txt&rsaquo; con i propri input.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

5. Aprire il file di output nel Blocco note e quindi copiare i nomi di tutte le VM che si vuole profilare in un altro file (ad esempio, ProfileVMList.txt) inserendo un nome di VM per riga. Questo file viene usato come input per il parametro *-VMListFile* dello strumento da riga di comando.

    ![Elenco di nomi di VM in Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>Avviare la profilatura
Dopo aver ottenuto l'elenco di macchine virtuali da profilare, è possibile eseguire lo strumento in modalità di profilatura. Di seguito è riportato l'elenco dei parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità di profilatura.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Nome parametro | Descrizione |
|---|---|
| -Operation | StartProfiling |
| -Server | Nome di dominio completo o indirizzo IP del server vCenter o dell'host vSphere ESXi con le VM da profilare.|
| -User | Nome utente per la connessione al server vCenter o all'host vSphere ESXi. L'utente deve avere almeno l'accesso in sola lettura.|
| -VMListFile | File contenente l'elenco di VM da profilare. Il percorso del file può essere assoluto o relativo. Il file deve contenere un solo nome/indirizzo IP di VM per riga. Il nome della macchina virtuale specificato nel file deve corrispondere al nome della VM nel server vCenter o nell'host vSphere ESXi.<br>Ad esempio, il file VMList.txt contiene le VM seguenti:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
| -NoOfDaysToProfile | Numero di giorni per i quali eseguire la profilatura. È consigliabile eseguire la profilatura per più di 15 giorni per far sì che il modello di carico di lavoro nell'ambiente venga osservato nel periodo specificato e usato per fornire un'indicazione accurata. |
| -Directory | (Facoltativo) UNC (Universal Naming Convention) o percorso della directory locale per l'archiviazione dei dati generati durante la profilatura. Per impostazione predefinita, se non viene specificato un nome di directory, verrà usata la directory denominata "ProfiledData" nel percorso corrente. |
| -Password | (Facoltativo) Password da usare per connettersi al server vCenter o all'host vSphere ESXi. Se non se ne specifica una in questa fase, verrà chiesta all'esecuzione del comando.|
| -StorageAccountName | (Facoltativo) Nome dell'account di archiviazione usato per determinare la velocità effettiva ottenibile per la replica dei dati dall'ambiente locale ad Azure. Lo strumento carica i dati di test in questo account di archiviazione per calcolare la velocità effettiva.|
| -StorageAccountKey | (Facoltativo) Chiave dell'account di archiviazione usata per accedere all'account di archiviazione. Passare al portale di Azure e scegliere Account di archiviazione > <*nome account di archiviazione*> > Impostazioni > Chiavi di accesso > Key1 oppure Chiave di accesso primaria per l'account di archiviazione classico. |
| -Environment | (Facoltativo) Ambiente dell'account di archiviazione di Azure di destinazione. Può trattarsi di uno di tre valori: AzureCloud, AzureUSGovernment, AzureChinaCloud. Il valore predefinito è AzureCloud. Usare il parametro quando l'area di destinazione del cloud di Azure è Azure US Government o Azure Cina. |


È consigliabile profilare le VM per almeno 15-30 giorni. Durante il periodo di profilatura, ASRDeploymentPlanner.exe rimane in esecuzione. Lo strumento accetta l'input della durata della profilatura in giorni. Se si vuole eseguire la profilatura per alcune ore o alcuni minuti per un rapido test dello strumento, nell'anteprima pubblica sarà necessario convertire la durata nella misura equivalente in giorni. Per eseguire ad esempio la profilatura per 30 minuti, l'input deve essere 30/(60*24) = 0,021 giorni. La durata minima della profilatura è di 30 minuti.

Durante la profilatura è possibile passare il nome e la chiave di un account di archiviazione per determinare la velocità effettiva che Site Recovery può raggiungere al momento della replica dal server di configurazione o dal server di elaborazione ad Azure. Se il nome e la chiave dell'account di archiviazione non vengono passati durante la profilatura, lo strumento non calcola la velocità effettiva ottenibile.

È possibile eseguire più istanze dello strumento per diversi set di VM. Assicurarsi che i nomi delle VM non siano ripetuti nei set di profilatura. Se, ad esempio, sono state profilate dieci VM (da VM1 a VM10) e dopo alcuni giorni si vogliono profilare altre cinque VM (da VM11 a VM15), è possibile eseguire lo strumento da un'altra console della riga di comando per il secondo set di VM (da VM11 a VM15). Verificare che le VM del secondo set non abbiano nomi uguali a quelli della prima istanza di profilatura oppure usare una directory di output diversa per la seconda esecuzione. Se vengono usate due istanze dello strumento per profilare le stesse VM usando la stessa directory di output, il report generato sarà errato.

Le configurazioni delle VM vengono acquisite una volta all'inizio dell'operazione di profilatura e archiviate in un file denominato VMDetailList.xml. Queste informazioni vengono usate quando viene generato il report. Eventuali modifiche apportate alla configurazione delle VM (ad esempio, un incremento del numero di core, dischi o schede di interfaccia di rete) dall'inizio alla fine della profilatura non vengono acquisite. Se una VM profilata è stata modificata nel corso della profilatura, nell'anteprima pubblica è disponibile la soluzione alternativa seguente per ottenere i dettagli più recenti della VM durante la generazione del report:

* Eseguire il backup di VMdetailList.xml ed eliminare il file dal percorso corrente.
* Passare gli argomenti -User e -Password al momento della generazione di report.

Il comando di profilatura genera diversi file nella directory della profilatura. Non eliminare nessuno dei file, altrimenti la generazione del report verrà compromessa.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Esempio 1: Profilare le VM per 30 giorni e determinare la velocità effettiva dall'ambiente locale ad Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Esempio 2: Profilare le VM per 15 giorni

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>Esempio 3: Profilare le VM per 1 ora per un rapido test dello strumento
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* Se il server in cui viene eseguito lo strumento viene riavviato o si è arrestato in modo anomalo oppure si chiude lo strumento usando CTRL+C, i dati di profilatura vengono conservati. Potrebbero tuttavia andare persi gli ultimi 15 minuti di dati profilati. In tal caso, eseguire nuovamente lo strumento in modalità di profilatura dopo il riavvio del server.
>* Quando vengono passati il nome e la chiave di un account di archiviazione, lo strumento misura la velocità effettiva all'ultimo passaggio della profilatura. Se lo strumento viene chiuso prima del completamento della profilatura, la velocità effettiva non verrà calcolata. Per determinare la velocità effettiva prima di generare il report, è possibile eseguire l'operazione GetThroughput dalla console della riga di comando. In caso contrario, il report generato non conterrà le informazioni sulla velocità effettiva.


## <a name="generate-a-report"></a>Generare un report
Lo strumento genera un file di Microsoft Excel con attivazione macro (file XLSM) come output del report, con il riepilogo di tutte le indicazioni di distribuzione. Il report viene denominato DeploymentPlannerReport_<*identificatore numerico univoco*>.xlsm e inserito nella directory specificata.

Al termine della profilatura, è possibile eseguire lo strumento in modalità di generazione di report. La tabella seguente contiene un elenco dei parametri obbligatori e facoltativi dello strumento per l'esecuzione in modalità di generazione di report.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nome parametro | Descrizione |
|-|-|
| -Operation | GenerateReport |
| -Server |  Nome dominio completo o indirizzo IP (usare lo stesso nome o indirizzo IP usato al momento della profilatura) del server vCenter/vSphere in cui si trovano le VM profilate per le quali generare il report. Si noti che, se è stato usato un server vCenter al momento della profilatura, non è possibile usare un server vSphere per la generazione di report e viceversa.|
| -VMListFile | File contenente l'elenco di VM profilate per cui deve essere generato il report. Il percorso del file può essere assoluto o relativo. Il file deve contenere un solo nome o indirizzo IP di VM per riga. I nomi delle VM specificati nel file devono corrispondere ai nomi delle VM nel server vCenter o nell'host vSphere ESXi e usati durante la profilatura.|
| -Directory | (Facoltativo) UNC o percorso della directory locale in cui vengono archiviati i dati profilati, ovvero i file generati durante la profilatura. Questi dati sono necessari per la generazione di report. Se non è specificato nessun nome, verrà usata la directory "ProfiledData". |
| -GoalToCompleteIR | (Facoltativo) Numero di ore in cui deve essere completata la replica iniziale delle VM profilate. Il report generato indica il numero di VM per cui è possibile eseguire la replica iniziale nel tempo specificato. Il valore predefinito è 72 ore. |
| -User | (Facoltativo) Nome utente da usare per la connessione al server vCenter/vSphere. Il nome viene usato per recuperare le informazioni di configurazione più recenti per le VN, ad esempio numero di dischi, numero di core e numero di schede di interfaccia di rete, da usare nel report. Se il nome non viene specificato, verranno usate le informazioni di configurazione raccolte all'inizio della profilatura. |
| -Password | (Facoltativo) Password da usare per connettersi al server vCenter o all'host vSphere ESXi. Se la password non viene specificata come parametro, verrà chiesta in un secondo momento all'esecuzione del comando. |
| -DesiredRPO | (Facoltativo) Obiettivo del punto di ripristino desiderato, in minuti. Il valore predefinito è 15 minuti.|
| -Bandwidth | Larghezza di banda in Mbps. Il parametro viene usato per calcolare il valore RPO ottenibile per la larghezza di banda specificata. |
| -StartDate | (Facoltativo) Data e ora di inizio in MM-GG-AAAA:HH:MM, in formato 24 ore. *StartDate* deve essere specificato con *EndDate*. Quando StartDate è specificato, il report viene generato per i dati profilati raccolti nell'intervallo compreso tra StartDate ed EndDate. |
| -EndDate | (Facoltativo) Data e ora di fine in MM-GG-AAAA:HH:MM, in formato 24 ore. *EndDate* deve essere specificato con *StartDate*. Quando EndDate è specificato, il report viene generato per i dati profilati raccolti nell'intervallo compreso tra StartDate ed EndDate. |
| -GrowthFactor | (Facoltativo) Fattore di crescita, espresso come percentuale. Il valore predefinito è 30%. |
| -UseManagedDisks | (Facoltativo) UseManagedDisks: Yes/No. Il valore predefinito è Yes. Il numero di macchine virtuali che possono essere inserite in un singolo account di archiviazione viene calcolato a seconda che il failover e il failover di test delle macchine virtuali vengano eseguiti su un disco gestito anziché su un disco non gestito. |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Esempio 1: Generare un report con valori predefiniti quando i dati profilati si trovano nell'unità locale
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Esempio 2: Generare un report quando i dati profilati si trovano in un server remoto
È consigliabile avere l'accesso in lettura/scrittura alla directory remota.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Esempio 3: Generare un report con una larghezza di banda specificata e l'obiettivo di completare la replica iniziale nel tempo specificato
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Esempio 4: Generare un report con un fattore di crescita del 5% al posto del valore predefinito pari al 30%
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Esempio 5: Generare un report con un subset di dati profilati
Si supponga, ad esempio, di avere 30 giorni di dati profilati e di voler generare un report solo per 20 giorni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Esempio 6: Generare un report per un valore RPO di 5 minuti
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
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

* [Input](site-recovery-deployment-planner.md#input)
* [Raccomandazioni](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Raccomandazioni con la larghezza di banda disponibile come input](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [Selezione host di archiviazione delle VM](site-recovery-deployment-planner.md#vm-storage-placement)
* [VM compatibili](site-recovery-deployment-planner.md#compatible-vms)
* [VM incompatibili](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>Misurare la velocità effettiva

Per stimare la velocità effettiva che Site Recovery può ottenere dall'ambiente locale ad Azure durante la replica, eseguire lo strumento in modalità GetThroughput. Lo strumento calcola la velocità effettiva dal server in cui è in esecuzione. Idealmente, questo server è basato sulle linee guida di ridimensionamento del server di configurazione. Se i componenti dell'infrastruttura di Site Recovery sono già stati distribuiti in locale, eseguire lo strumento nel server di configurazione.

Aprire una console della riga di comando e passare alla cartella dello strumento di pianificazione della distribuzione di Site Recovery. Eseguire ASRDeploymentPlanner.exe con i parametri seguenti.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nome parametro | Descrizione |
|-|-|
| -Operation | GetThroughput |
| -Directory | (Facoltativo) UNC o percorso della directory locale in cui vengono archiviati i dati profilati, ovvero i file generati durante la profilatura. Questi dati sono necessari per la generazione di report. Se non viene specificato un nome di directory, viene usata la directory "ProfiledData". |
| -StorageAccountName | Nome dell'account di archiviazione usato per determinare la larghezza di banda utilizzata per la replica dei dati dall'ambiente locale ad Azure. Lo strumento carica i dati di test in questo account di archiviazione per determinare la larghezza di banda utilizzata. |
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

## <a name="recommendations-with-desired-rpo-as-input"></a>Raccomandazioni con valore RPO desiderato come input

### <a name="profiled-data"></a>Dati profilati

![Visualizzazione dei dati profilati in Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period**(Periodo profilatura dati): periodo durante il quale la profilatura è stata in esecuzione. Per impostazione predefinita, lo strumento include tutti i dati profilati nel calcolo, a meno che non generi il report per un periodo specifico usando le opzioni StartDate ed EndDate durante la generazione del report.

**Server Name** (Nome server): nome o indirizzo IP del server VMware vCenter o dell'host ESXi contenente le VM per le quali viene generato il report.

**Desired RPO** (RPO desiderato): obiettivo del punto di ripristino per la distribuzione. Per impostazione predefinita, la larghezza di banda di rete viene calcolata per i valori RPO di 15, 30 e 60 minuti. A seconda della selezione, i valori interessati verranno aggiornati nel foglio. Se è stato usato il parametro *DesiredRPOinMin* durante la generazione del report, tale valore verrà visualizzato nel risultato di Desired RPO (RPO desiderato).

### <a name="profiling-overview"></a>Panoramica della profilatura

![Risultati della profilatura in Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines** (Totale macchine virtuali profilate): numero totale di VM per le quali sono disponibili dati profilati. Se il parametro VMListFile presenta nomi di VM che non sono state profilate, tali VM non verranno considerate nella generazione di report e verranno escluse dal conteggio totale delle VM profilate.

**Compatible Virtual Machines** (Macchine virtuali compatibili): VM che possono essere protette in Azure con Site Recovery. Si tratta del numero totale di VM compatibili per le quali vengono calcolati larghezza di banda di rete, numero di account di archiviazione, numero di core di Azure, numero di server di configurazione e di server di elaborazione aggiuntivi. I dettagli di ogni VM compatibile sono disponibili nella sezione "VM compatibili".

**Incompatible Virtual Machines** (Macchine virtuali incompatibili): VM profilate incompatibili con la protezione con Site Recovery. I motivi dell'incompatibilità sono indicati nella sezione "Macchine virtuali incompatibili". Se VMListFile contiene nomi di VM non profilate, tali VM vengono escluse dal conteggio delle VM incompatibili. Queste VM sono elencate con la dicitura "Data not found" (Dati non trovati) alla fine della sezione Incompatible VMs (VM incompatibili).

**Desired RPO** (RPO desiderato): obiettivo del punto di ripristino desiderato, in minuti. Il report viene generato per tre valori RPO: 15 (impostazione predefinita), 30 e 60 minuti. La raccomandazione relativa alla larghezza di banda nel report varia in funzione della selezione nell'elenco a discesa Desired RPO (RPO desiderato) nella parte superiore destra del foglio. Se il report è stato generato usando il parametro *-DesiredRPO* con un valore personalizzato, questo valore personalizzato verrà visualizzato come predefinito nell'elenco a discesa Desired RPO (RPO desiderato).

### <a name="required-network-bandwidth-mbps"></a>Larghezza di banda di rete necessaria (Mbps)

![Larghezza di banda di rete necessaria in Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100% of the time** (Per soddisfare il valore RPO il 100% del tempo): larghezza di banda consigliata in Mbps per soddisfare il valore RPO desiderato il 100% del tempo. Questa larghezza di banda deve essere dedicata alla replica differenziale stazionaria di tutte le VM compatibili per evitare violazioni di RPO.

**To meet RPO 90% of the time** (Per soddisfare il valore RPO il 90% del tempo): se a causa dei prezzi della banda larga o per altri motivi non è possibile impostare la larghezza di banda necessaria per soddisfare il valore RPO desiderato il 100% del tempo, è possibile scegliere di passare a una larghezza di banda inferiore che possa soddisfare il valore RPO desiderato il 90% del tempo. Per comprendere le implicazioni dell'impostazione di questa larghezza di banda inferiore, il report offre un'analisi ipotetica del numero e della durata delle violazioni del valore RPO prevedibili.

**Achieved Throughput** (Velocità effettiva ottenuta): velocità effettiva dal server in cui è stato eseguito il comando GetThroughput all'area di Microsoft Azure in cui si trova l'account di archiviazione. Questo numero della velocità effettiva indica il livello stimato ottenibile quando si proteggono le VM compatibili con Site Recovery, se le caratteristiche di archiviazione e di rete del server di configurazione o del server di elaborazione rimangono uguali a quelle del server dal quale è stato eseguito lo strumento.

Per la replica, è opportuno impostare la larghezza di banda consigliata per soddisfare il valore RPO il 100% del tempo. Dopo avere impostato la larghezza di banda, se non è visibile un aumento della velocità effettiva ottenuta, come indicato dallo strumento, seguire questa procedura:

1. Verificare se sono presenti requisiti di qualità del servizio (QoS) che limitano la velocità effettiva di Site Recovery.

2. Verificare se l'insieme di credenziali di Site Recovery si trova nell'area fisica di Microsoft Azure più vicina supportata per ridurre al minimo la latenza di rete.

3. Controllare le caratteristiche di archiviazione locale per determinare se sia possibile migliorare l'hardware (ad esempio, passando da unità HDD a unità SSD).

4. Modificare le impostazioni di Site Recovery nel server di elaborazione per [aumentare la larghezza di banda di rete usata per la replica](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se si esegue lo strumento in un server di configurazione o in un server di elaborazione che ha già VM protette, eseguire lo strumento più volte. Il numero della velocità effettiva ottenuta cambia in base alla varianza elaborata in quel particolare momento.

Per tutte le distribuzioni aziendali di Site Recovery è consigliabile usare [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Account di archiviazione necessari
Il grafico seguente indica il numero totale di account di archiviazione (Standard e Premium) necessari per proteggere tutte le VM compatibili. Per informazioni sull'account di archiviazione da usare per ogni VM, vedere la sezione "Selezione host di archiviazione delle VM".

![Account di archiviazione necessari in Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>Numero di core Azure necessari
Questo risultato è il numero totale di core da configurare prima del failover o del failover di test di tutte le VM compatibili. Se i core disponibili nella sottoscrizione sono troppo pochi, Site Recovery non creerà le VM al momento del failover di test o del failover.

![Numero di core Azure necessari in Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>Infrastruttura locale necessaria
Questa cifra indica il numero totale di server di configurazione e di altri server di elaborazione da configurare in quantità sufficiente per proteggere tutte le VM compatibili. A seconda delle [dimensioni consigliate per il server di configurazione](https://aka.ms/asr-v2a-on-prem-components) supportate, lo strumento potrebbe consigliare altri server. L'indicazione si basa sul valore più grande tra quello della varianza giornaliera o del numero massimo di VM protette (presupponendo una media di tre dischi per VM), a seconda del valore raggiunto per primo nel server di configurazione o nel server di elaborazione aggiuntivo. I dettagli della varianza totale giornaliera e il numero totale di dischi protetti sono disponibili nella sezione "Input".

![Infrastruttura locale necessaria in Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>Analisi di simulazione
Questa analisi indica quante violazioni possono verificarsi durante il periodo di profilatura quando si imposta una larghezza di banda inferiore per soddisfare il valore RPO desiderato solo il 90% del tempo. Possono verificarsi una o più violazioni del valore RPO in un giorno. Il grafico illustra il valore RPO di picco del giorno.
In base a questa analisi è possibile stabilire se il numero di violazioni del valore RPO per il totale dei giorni e il valore RPO di picco raggiunto ogni giorno siano accettabili con la larghezza di banda inferiore specificata. Se sono accettabili, è possibile allocare la larghezza di banda inferiore per la replica, altrimenti allocare la larghezza di banda superiore come suggerito per soddisfare il valore RPO desiderato il 100% del tempo.

![Analisi di simulazione in Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Dimensioni raccomandate per i batch di VM per la replica iniziale
In questa sezione viene consigliato il numero di VM che possono essere protette in parallelo per completare la replica iniziale entro 72 ore con la larghezza di banda suggerita per soddisfare il valore RPO desiderato il 100% del tempo impostato. Questo valore è configurabile. Per modificarlo durante la generazione di report, usare il parametro *GoalToCompleteIR*.

Il grafico mostra un intervallo di valori di larghezza di banda e un numero calcolato di VM per batch per completare la replica iniziale in 72 ore, in base alle dimensioni medie rilevate per tutte le VM compatibili.

Nell'anteprima pubblica il report non specifica quali VM includere in un batch. È possibile usare lo spazio su disco visualizzato nella sezione "Compatible VM" (VM compatibili) per determinare le dimensioni di ogni VM e selezionarle per un batch oppure è possibile selezionare le VM in base alle caratteristiche note del carico di lavoro. Il tempo di completamento della replica iniziale varia in modo proporzionale allo spazio su disco effettivo della VM, allo spazio usato e alla velocità effettiva della rete disponibile.

![Dimensioni raccomandate per i batch di VM](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>Fattore di crescita e valori percentili usati
Questa sezione nella parte inferiore del foglio indica il valore percentile usato per tutti i contatori delle prestazioni delle VM profilate (il valore predefinito è il 95° percentile) e il fattore di crescita (il valore predefinito è il 30%) usato in tutti i calcoli.

![Fattore di crescita e valori percentili usati](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Raccomandazioni con la larghezza di banda disponibile come input

![Raccomandazioni con la larghezza di banda disponibile come input](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Si possono verificare situazioni in cui non è possibile impostare una larghezza di banda oltre un dato valore di Mbps per la replica di Site Recovery. Lo strumento consente di immettere la larghezza di banda disponibile (con il parametro -Bandwidth durante la generazione di report) e determinare il valore RPO ottenibile in minuti. Con questo valore RPO ottenibile è possibile decidere se è necessario configurare altra larghezza di banda o se è sufficiente una soluzione di ripristino di emergenza con questo valore RPO.

![Valore RPO ottenibile per 500 Mbps di larghezza di banda](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>Input
Il foglio di lavoro Input offre una panoramica dell'ambiente VMware profilato.

![Panoramica dell'ambiente VMware profilato](./media/site-recovery-deployment-planner/Input.png)

**Start Date** (Data di inizio) ed **End Date** (Data di fine): date di inizio e fine dei dati di profilatura considerati per la generazione di report. Per impostazione predefinita, la data di inizio è la data in cui inizia la profilatura e la data di fine è la data in cui termina. Può trattarsi dei valori di 'StartDate' ed 'EndDate' se il report viene generato con questi parametri.

**Total number of profiling days** (Numero totale di giorni di profilatura): numero totale di giorni di profilatura tra le date di inizio e fine per i quali viene generato il report.

**Number of compatible virtual machines** (Numero di macchine virtuali compatibili): numero totale di VM compatibili per le quali vengono calcolati la larghezza di banda di rete e il numero necessario di account di archiviazione, core di Microsoft Azure, server di configurazione e server di elaborazione aggiuntivi.

**Total number of disks across all compatible virtual machines** (Numero totale di dischi tra tutte le macchine virtuali compatibili): numero usato come uno degli input per stabilire il numero di server di configurazione e di server di elaborazione aggiuntivi da usare nella distribuzione.

**Average number of disks per compatible virtual machine** (Numero medio di dischi per macchina virtuale compatibile): numero medio di dischi calcolato tra tutte le VM compatibili.

**Average disk size (GB)** (Dimensioni medie disco - GB): dimensioni medie dei dischi calcolate tra tutte le VM compatibili.

**Desired RPO (minutes)** (RPO desiderato - minuti): obiettivo del punto di ripristino predefinito o valore passato per il parametro "DesiredRPO" al momento della generazione di report per stimare la larghezza di banda necessaria.

**Desired bandwidth (Mbps)** (Larghezza di banda desiderata - Mbps): valore passato per il parametro "Bandwidth" al momento della generazione di report per stimare il valore RPO ottenibile.

**Observed typical data churn per day (GB)** (Varianza dei dati giornaliera tipica osservata - GB): varianza media dei dati osservata in tutti i giorni di profilatura. Questo numero viene usato come uno degli input per stabilire il numero di server di configurazione e di server di elaborazione aggiuntivi da usare nella distribuzione.


## <a name="vm-storage-placement"></a>Selezione host di archiviazione delle VM

![Selezione host di archiviazione delle VM](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type** (Tipo di archiviazione disco): account di archiviazione Standard o Premium usato per replicare tutte le VM corrispondenti indicate nella colonna **VMs to Place** (VM da posizionare).

**Suggested Prefix** (Prefisso suggerito): prefisso di tre caratteri che può essere usato per la denominazione dell'account di archiviazione. È possibile usare un prefisso personalizzato, ma il suggerimento dello strumento segue la [convenzione di denominazione delle partizioni per gli account di archiviazione](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Nome account suggerito): nome dell'account di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Log Storage Account** (Account di archiviazione log): tutti i log di replica vengono archiviati in un account di archiviazione Standard. Per le VM che eseguono la replica in un account di archiviazione Premium, configurare un account di archiviazione Standard aggiuntivo per l'archiviazione log. Un singolo account di archiviazione log Standard può essere usato da più account di archiviazione di replica Premium. Le VM replicate negli account di archiviazione Standard usano lo stesso account di archiviazione per i log.

**Suggested Log Account Name** (Nome account log suggerito): nome dell'account log di archiviazione dopo avere incluso il prefisso suggerito. Sostituire il nome nelle parentesi acute (< e >) con l'input personalizzato.

**Placement Summary** (Riepilogo di selezione host): riepilogo del carico totale delle VM nell'account di archiviazione al momento della replica e del failover di test o del failover. Comprende il numero totale di VM mappate all'account di archiviazione, il numero totale di operazioni di I/O al secondo in lettura/scrittura per tutte le VM inserite in questo account di archiviazione, il totale delle operazioni di I/O al secondo in scrittura (replica), le dimensioni totali configurate per tutti i dischi e il numero totale di dischi.

**VMs to Place** (Macchine virtuali da inserire): elenco di tutte le VM da inserire nell'account di archiviazione specificato per ottenere uso e prestazioni ottimali.

## <a name="compatible-vms"></a>VM compatibili
![Foglio di calcolo di Excel delle VM compatibili](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name** (Nome macchina virtuale): nome o indirizzo IP della VM usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (VMDK) collegati alle VM. Per distinguere le VM vCenter con nomi o indirizzi IP duplicati, i nomi includono il nome dell'host ESXi. L'host ESXi elencato è quello in cui la VM è stata inserita quando lo strumento l'ha rilevata durante il periodo di profilatura.

**VM Compatibility** (Compatibilità VM): i valori sono **Yes** e **Yes**\*. **Yes**\* è per i casi in cui la VM è idonea per l'[Archiviazione Premium di Azure](https://aka.ms/premium-storage-workload). Qui, il disco con varianza elevata o operazioni di I/O al secondo profilato è idoneo per la categoria P20 o P30, ma le dimensioni del disco prevedono la mappatura alla categoria P10 o P20. In base alle dimensioni, l'account di archiviazione decide a quale tipo di disco di archiviazione Premium mappare un disco. Ad esempio:
* <128 GB rientrano nella categoria P10.
* Da 128 GB a 512 GB rientrano nella categoria P20.
* Da 512 GB a 1023 GB rientrano nella categoria P30.

Se in virtù delle caratteristiche di un carico di lavoro un disco appartiene alla categoria P20 o P30, ma le dimensioni lo associano a un tipo di disco di archiviazione Premium inferiore, lo strumento contrassegna tale VM con **Yes**\*. Lo strumento consiglia anche di modificare le dimensioni del disco di origine per renderlo idoneo al tipo di disco di archiviazione Premium raccomandato oppure di modificare il tipo di disco di destinazione dopo il failover.

**Storage Type** (Tipo di archiviazione): Standard o Premium.

**Suggested Prefix** (Prefisso suggerito): prefisso di tre caratteri dell'account di archiviazione.

**Storage Account** (Account di archiviazione): nome che usa il prefisso dell'account di archiviazione suggerito.

**R/W IOPS (with Growth Factor)** (Operazioni di I/O al secondo in lettura/scrittura - con fattore di crescita): picco di operazioni di I/O al secondo in lettura/scrittura del carico di lavoro nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che il numero totale di operazioni di I/O al secondo in lettura/scrittura di una VM non è sempre costituito dalla somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM, perché il picco di operazioni di I/O al secondo in lettura/scrittura della VM è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Data Churn in Mbps (with Growth Factor)** (Varianza dati in Mbps - con fattore di crescita): picco della frequenza di varianza nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza dei dati totale della VM non è sempre costituita dalla somma delle varianze dei singoli dischi della VM perché il picco della varianza dei dati corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Azure VM Size** (Dimensioni VM Azure): dimensioni ideali del mapping per Servizi cloud di Azure per questa VM locale. Il mapping si basa sulla memoria, sul numero di dischi/core/schede di interfaccia di rete e operazioni di I/O al secondo in lettura/scrittura della VM locale. La raccomandazione prevede sempre le dimensioni minime della VM di Azure corrispondenti a tutte le caratteristiche della VM locale.

**Number of Disks** (Numero di dischi): numero totale dei dischi nella VM (VMDK).

**Disk size (GB)** (Dimensioni disco - GB): dimensioni di installazione totale di tutti i dischi della VM. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Cores** (Core): numero di core CPU nella VM.

**Memory (MB)** (Memoria - MB): RAM della VM.

**NICs** (Schede di interfaccia di rete): numero di schede di interfaccia di rete della VM.

**Boot Type** (Tipo di avvio): tipo di avvio della macchina virtuale. Può essere BIOS o EFI. Azure Site Recovery supporta attualmente solo il tipo di avvio BIOS. Tutte le macchine virtuali con tipo di avvio EFI sono elencate nel foglio di lavoro VM incompatibili.

**OS Type** (Tipo di sistema operativo): tipo di sistema operativo della macchina virtuale. Può essere Windows, Linux o altro.

## <a name="incompatible-vms"></a>VM incompatibili

![Foglio di calcolo di Excel delle VM incompatibili](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name** (Nome macchina virtuale): nome o indirizzo IP della VM usato in VMListFile quando viene generato un report. Questa colonna elenca anche i dischi (VMDK) collegati alle VM. Per distinguere le VM vCenter con nomi o indirizzi IP duplicati, i nomi includono il nome dell'host ESXi. L'host ESXi elencato è quello in cui la VM è stata inserita quando lo strumento l'ha rilevata durante il periodo di profilatura.

**VM Compatibility** (Compatibilità VM): indica perché la VM è incompatibile per l'uso con Site Recovery. I motivi vengono descritti per ogni disco incompatibile della VM e, in base ai [limiti di archiviazione](https://aka.ms/azure-storage-scalbility-performance) pubblicati, possono essere uno dei seguenti:

* Dimensioni disco superiori a 1023 GB. Archiviazione di Azure non supporta attualmente dimensioni superiori a 1 TB.
* Il tipo di avvio è EFI. Azure Site Recovery supporta attualmente solo macchine virtuali con tipo di avvio BIOS.

* Le dimensioni totali della VM (replica + failover di test) superano i limiti supportati dall'account di archiviazione (35 TB). Questa incompatibilità si verifica in genere quando un singolo disco della VM ha una caratteristica di prestazioni che supera i limiti massimi supportati da Azure o da Site Recovery per l'archiviazione Standard. In questo caso la VM rientra nell'area dell'archiviazione Premium. Le dimensioni massime supportate da un account di archiviazione Premium sono tuttavia pari a 35 TB e non è possibile proteggere una singola VM su più account di archiviazione. Si noti anche che, quando un failover di test viene eseguito in una VM protetta, viene eseguito nello stesso account di archiviazione in cui è in corso la replica. In questo caso, configurare il doppio delle dimensioni del disco per far sì che la replica prosegua e il failover di test venga completato in parallelo.
* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 5000 operazioni per ogni disco.
* Le operazioni di I/O al secondo di origine superano il limite supportato di archiviazione di 80.000 operazioni per ogni VM.
* La varianza media dei dati supera il limite supportato da Site Recovery di 10 MBps per le dimensioni I/O medie del disco.
* La varianza totale dei dati per tutti i dischi della macchina virtuale supera il limite massimo supportato da Site Recovery di 54 MBps per VM.
* Le operazioni di I/O al secondo in scrittura superano il limite supportato da Site Recovery di 840 operazioni per disco.
* L'archiviazione snapshot calcolata supera il limite supportato di 10 TB.

**R/W IOPS (with Growth Factor)** (Operazioni di I/O al secondo in lettura/scrittura - con fattore di crescita): picco di operazioni di I/O al secondo del carico di lavoro nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che il numero totale di operazioni di I/O al secondo in lettura/scrittura della VM non è sempre costituito dalla somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi della VM, perché il picco di operazioni di I/O al secondo in lettura/scrittura della VM è il picco della somma delle operazioni di I/O al secondo in lettura/scrittura dei singoli dischi per ogni minuto del periodo di profilatura.

**Data Churn in Mbps (with Growth Factor)** (Varianza dati in Mbps - con fattore di crescita): picco della frequenza di varianza nel disco (il valore predefinito è 95° percentile), incluso il fattore di crescita futuro (il valore predefinito è 30%). Si noti che la varianza dei dati totale della VM non è sempre costituita dalla somma delle varianze dei singoli dischi della VM perché il picco della varianza dei dati corrisponde al picco della somma delle varianze dei singoli dischi per ogni minuto del periodo di profilatura.

**Number of Disks** (Numero di dischi): numero totale dei dischi VMDK nella VM.

**Disk size (GB)** (Dimensioni disco - GB): dimensioni di installazione totale di tutti i dischi della VM. Lo strumento indica anche le dimensioni dei singoli dischi della VM.

**Cores** (Core): numero di core CPU nella VM.

**Memory (MB)** (Memoria - MB): quantità di RAM della VM.

**NICs** (Schede di interfaccia di rete): numero di schede di interfaccia di rete della VM.

**Boot Type** (Tipo di avvio): tipo di avvio della macchina virtuale. Può essere BIOS o EFI. Azure Site Recovery supporta attualmente solo il tipo di avvio BIOS. Tutte le macchine virtuali con tipo di avvio EFI sono elencate nel foglio di lavoro VM incompatibili.

**OS Type** (Tipo di sistema operativo): tipo di sistema operativo della macchina virtuale. Può essere Windows, Linux o altro.


## <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery

**Destinazione archiviazione di replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MBps | 168 GB per disco
Premium, disco P10 | 8 KB | 2 MBps | 168 GB per disco
Premium, disco P10 | 16 KB | 4 MBps | 336 GB per disco
Premium, disco P10 | 32 KB o superiori | 8 MBps | 672 GB per disco
Disco P20 o P30 Premium | 8 KB  | 5 MBps | 421 GB per disco
Disco P20 o P30 Premium | 16 KB o superiori |10 MBps | 842 GB per disco

Si tratta di numeri medi presupponendo una sovrapposizione I/O del 30%. Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro. I numeri precedenti presuppongono un backlog tipico di circa cinque minuti, ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

Questi limiti si basano su test di Microsoft, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. Per risultati ottimali, anche dopo la pianificazione della distribuzione è sempre consigliabile eseguire test approfonditi con un failover di test per ottenere il quadro reale delle prestazioni.

## <a name="updating-the-deployment-planner"></a>Aggiornamento di Deployment Planner
Per aggiornare Deployment Planner, seguire questa procedura:

1. Scaricare la versione più recente di [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).

2. Copiare la cartella ZIP in un server in cui eseguirlo.

3. Estrarre la cartella ZIP.

4. Eseguire una di queste operazioni:
 * Se la versione più recente non contiene una correzione della profilatura e la profilatura è già in corso nella versione corrente dell'utilità di pianificazione, continuare la profilatura.
 * Se la versione più recente contiene una correzione della profilatura, è consigliabile arrestare la profilatura nella versione corrente e riavviare la profilatura con la nuova versione.

  >[!NOTE]
  >
  >Quando si avvia la profilatura con la nuova versione, passare lo stesso percorso della directory di output in modo che lo strumento accodi i dati del profilo nei file esistenti. Un set completo di dati profilati verrà usato per generare il report. Se si passa una directory di output diversa, vengono creati nuovi file e i dati profilati precedenti non vengono usati per generare il report.
  >
  >Ogni nuova utilità di pianificazione delle distribuzioni è un aggiornamento cumulativo del file ZIP. Non è necessario copiare i file più recenti nella cartella precedente. È possibile creare e usare una nuova cartella.


## <a name="version-history"></a>Cronologia delle versioni

### <a name="13"></a>1.3
Ultimo aggiornamento: 9 maggio 2017

È stata aggiunta la nuova funzionalità seguente:

* Aggiunta del supporto di dischi gestiti nella generazione di report. Il calcolo del numero di macchine virtuali che può essere inserito in un singolo account di archiviazione dipende dalla selezione o meno di un disco gestito per il failover e il failover di test.        


### <a name="12"></a>1.2
Ultimo aggiornamento: 7 aprile 2017

Sono state aggiunte le correzioni seguenti:

* Aggiunta la verifica del tipo di avvio (BIOS o EFI) per ogni macchina virtuale per determinare se la macchina virtuale è compatibile o incompatibile per la protezione.
* Aggiunte le informazioni sul tipo di sistema operativo nei fogli di lavoro VM compatibili e VM incompatibili.
* L'operazione GetThroughput è ora supportata nelle aree US Government e Cina di Microsoft Azure.
* Aggiunte alcune altre verifiche dei prerequisiti per server vCenter ed ESXi.
* Venivano generati report errati in caso di impostazioni locali diverse dall'inglese.


### <a name="11"></a>1.1
Ultimo aggiornamento: 9 marzo 2017

Sono stati corretti i problemi seguenti:

* Lo strumento non può profilare VM se vCenter ha due o più VM con lo stesso nome o indirizzo IP in host ESXi differenti.
* La copia e la ricerca sono disabilitate per i fogli di lavoro VM compatibili e VM incompatibili.

### <a name="10"></a>1.0
Ultimo aggiornamento: 23 febbraio 2017

L'anteprima pubblica di Azure Site Recovery Deployment Planner 1.0 presenta i problemi noti seguenti (da risolvere negli aggiornamenti futuri):

* Lo strumento funziona solo per scenari da VMware ad Azure, non per distribuzioni da Hyper-V ad Azure. Per gli scenari da Hyper-V ad Azure, usare lo [strumento di pianificazione della capacità di Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* L'operazione GetThroughput non è supportata nelle aree US Government e Cina di Microsoft Azure.
* Lo strumento non può profilare VM se il server vCenter ha due o più VM con lo stesso nome o indirizzo IP in host ESXi differenti. In questa versione, lo strumento ignora la profilatura di VM con nomi o indirizzi IP duplicati nel parametro VMListFile. La soluzione alternativa prevede di profilare le VM usando un host ESXi invece del server vCenter. È necessario eseguire una sola istanza per ogni host ESXi.

