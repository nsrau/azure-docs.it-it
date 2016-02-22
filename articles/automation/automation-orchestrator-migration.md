<properties
   pageTitle="Migrazione da Orchestrator ad Automazione di Azure | Microsoft Azure"
   description="Descrive come eseguire la migrazione di runbook e Integration Pack da System Center Orchestrator in Automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# Migrazione da Orchestrator ad Automazione di Azure (Beta)

I runbook in [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) si basano su attività di Integration Pack scritte appositamente per Orchestrator, mentre i runbook di Automazione di Azure si basano su Windows PowerShell. I[Runbook grafici](automation-runbook-types#graphical-runbooks)di Automazione di Azure hanno un aspetto simile ai runbook di Orchestrator con attività che rappresentano i cmdlet di PowerShell, i runbook figlio e gli asset.

[System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) include strumenti che facilitano la conversione dei runbook da Orchestrator ad Automazione di Azure. Oltre ai runbook stessi, è necessario convertire gli Integration Pack con le attività usate dai runbook in moduli di integrazione con cmdlet di Windows PowerShell.

Di seguito è illustrato il processo di base per la conversione di runbook di Orchestrator in Automazione di Azure. Ognuno di questi passaggi viene descritto nei dettagli nelle sezioni seguenti.

1.  Scaricare [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all), che contiene gli strumenti e i moduli descritti in questo articolo.
2.  Importare il [modulo delle attività standard](#standard-activities-module) in Automazione di Azure. Questo modulo include versioni convertite di attività standard di Orchestrator che possono essere usate dai runbook convertiti.
3.  Importare i [moduli di integrazione di System Center Orchestrator](#system-center-orchestrator-integration-modules) in Automazione di Azure per gli Integration Pack usati dai runbook che accedono a System Center.
4.  Convertire gli Integration Pack personalizzati e di terze parti mediante [Integration Pack Converter](#integration-pack-converter) e importarli in Automazione di Azure.
5.  Convertire i runbook di Orchestrator usando [Runbook Converter](#runbook-converter) e installarli in Automazione di Azure.
6.  Creare manualmente gli asset di Orchestrator necessari in Automazione di Azure, poiché Runbook Converter non converte queste risorse.
7.  Configurare un [ruolo di lavoro ibrido per runbook](#hybrid-runbook-worker) nel data center locale per eseguire i runbook convertiti che accedono alle risorse locali.

## Service Management Automation

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) archivia ed esegue i runbook nel data center locale come Orchestrator e usa gli stessi moduli di integrazione di Automazione di Azure. [Runbook Converter](#runbook-converter) converte i runbook di Orchestrator in runbook grafici, che non sono supportati in SMA. È comunque possibile installare il [modulo delle attività standard](#standard-activities-module) e [System Center Orchestrator Integration Modules](#system-center-orchestrator-integration-modules) in SMA, ma in tal caso è necessario [riscrivere i runbook](http://technet.microsoft.com/library/dn469262.aspx) manualmente.

## Hybrid Runbook Worker

I runbook in Orchestrator sono archiviati in un server di database e vengono eseguiti in server runbook, tutti contenuti nel data center locale. I runbook in Automazione di Azure sono archiviati nel cloud Azure e possono essere eseguiti nel data center locale tramite un computer [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Questo è il modo in cui verranno eseguiti in genere i runbook convertiti da Orchestrator poiché sono designati per l'esecuzione in server locali.

## Integration Pack Converter

Integration Pack Converter converte gli Integration Pack creati mediante [Orchestrator Integration Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) in moduli di integrazione basati su Windows PowerShell che possono essere importati in Automazione di Azure o in Service Management Automation.

Quando si esegue Integration Pack Converter, viene visualizzata una procedura guidata che consente di selezionare un file Integration Pack (con estensione oip). La procedura guidata elenca quindi le attività incluse nell'Integration Pack e consente di selezionare quella da sottoporre a migrazione. Al termine della procedura guidata, viene creato un modulo di integrazione che include un cmdlet corrispondente per ogni attività dell'Integration Pack originale.


### Parametri

Tutte le proprietà di un'attività dell'Integration Pack vengono convertite in parametri del cmdlet corrispondente nel modulo di integrazione. I cmdlet di Windows PowerShell dispongono di un set di [parametri comuni](http://technet.microsoft.com/library/hh847884.aspx) che possono essere usati con tutti i cmdlet. Con il parametro -Verbose ad esempio un cmdlet restituisce informazioni dettagliate sul proprio funzionamento. Nessun cmdlet può usare un parametro con lo stesso nome di un parametro comune. Se un'attività dispone di una proprietà con lo stesso nome del parametro comune, la procedura guidata chiederà di specificare un altro nome per il parametro.

### Attività di monitoraggio

I runbook di monitoraggio in Orchestrator iniziano con un'[attività di monitoraggio](http://technet.microsoft.com/library/hh403827.aspx) e vengono eseguiti in modo continuativo in attesa di essere richiamati da un evento specifico. Automazione di Azure non supporta i runbook di monitoraggio, pertanto le attività di monitoraggio nell'Integration Pack non verranno convertite. Nel modulo di integrazione verrà creato invece un cmdlet segnaposto per l'attività di monitoraggio. Questo cmdlet è privo di funzionalità, ma consente l'installazione dei runbook convertiti che lo usano. Questo runbook non potrà essere eseguito in Automazione di Azure, ma può essere installato in modo che l'utente possa modificarlo.

### Integration Pack che non possono essere convertiti

Gli Integration Pack non creati con OIT non possono essere convertiti con Integration Pack Converter. Esistono anche alcuni Integration Pack di Microsoft che attualmente non è possibile convertire con questo strumento. Le versioni convertite di questi Integration Pack sono [disponibili per il download](#system-center-orchestrator-integration-modules), in modo da poterle installare in Automazione di Azure o in Service Management Automation.


## Modulo delle attività standard

Orchestrator contiene un insieme di [attività standard](http://technet.microsoft.com/library/hh403832.aspx) non incluse in un Integration Pack, ma usate da molti runbook. Il modulo delle attività standard è un modulo di integrazione che include un cmdlet equivalente per ognuna di queste attività. È necessario installare questo modulo di integrazione in Automazione di Azure prima di importare eventuali runbook convertiti che usano un'attività standard.

Oltre a supportare i runbook convertiti, i cmdlet del modulo delle attività standard possono essere usati da chiunque abbia familiarità con Orchestrator per creare nuovi runbook in Automazione di Azure. Benché le funzionalità di tutte le attività standard possano essere eseguite con i cmdlet, è possibile che funzionino in modo diverso. I cmdlet nel modulo delle attività standard convertite funzioneranno come le attività corrispondenti e useranno gli stessi parametri. Questo può rivelarsi utile per l'autore del runbook di Orchestrator esistente nella transizione ai runbook di Automazione di Azure.

## Moduli di integrazione di System Center Orchestrator

Microsoft mette a disposizione [Integration Pack](http://technet.microsoft.com/library/hh295851.aspx) per la creazione di runbook per l'automatizzazione di componenti di System Center e altri prodotti. Alcuni degli Integration Pack attualmente sono basati su OIT ma non possono essere convertiti in moduli di integrazione a causa di problemi noti. I [moduli di integrazione di System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) includono versioni convertite degli Integration Pack che possono essere importate in Automazione di Azure e in Service Management Automation.

Le versioni aggiornate degli Integration Pack basati su OIT che possono essere convertite con Integration Pack Converter verranno pubblicate tramite la versione RTM di questo strumento. Verranno anche offerte indicazioni per facilitare la conversione dei runbook mediante le attività degli Integration Pack non basati su OIT.

## Runbook Converter

Runbook Converter converte i runbook di Orchestrator in [runbook grafici](automation-runbook-types.md#graph-runbooks) che possono essere importati in Automazione di Azure.

Runbook Converter è implementato come modulo PowerShell con un cmdlet denominato **ConvertFrom-SCORunbook** che esegue la conversione. Quando si installa lo strumento, verrà creato un collegamento a una sessione di PowerShell che carica il cmdlet.

Di seguito è illustrato il processo di base per convertire un runbook di Orchestrator e importarlo in Automazione di Azure. Le sezioni seguenti offrono altre informazioni dettagliate sull'uso dello strumento con runbook convertiti.

1. Esportare uno o più runbook da Orchestrator.
2. Ottenere i moduli di integrazione per tutte le attività del runbook.
3. Convertire i runbook di Orchestrator nel file esportato.
4. Esaminare le informazioni nei log per convalidare la conversione e determinare eventuali attività manuali necessarie.
4. Importare i runbook convertiti in Automazione di Azure.
5. Creare gli eventuali asset necessari in Automazione di Azure.
6. Modificare il runbook in Automazione di Azure per modificare le attività necessarie.

### Uso di Runbook Converter

La sintassi per **ConvertFrom-SCORunbook** è la seguente:

	ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath: percorso del file di esportazione contenente i runbook da convertire.
- Module: elenco delimitato da virgole dei moduli di integrazione contenenti le attività nei runbook.
- OutputFolder: percorso della cartella per creare runbook grafici convertiti. 


Il comando dell'esempio seguente converte i runbook in un file di esportazione denominato **MyRunbooks.ois\_export**. Questi runbook usano gli Integration Pack di Active Directory e di Data Protection Manager.

	ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### File di log

Runbook Converter crea i file di log seguenti nello stesso percorso del runbook convertito. Se i file esistono già, verranno sovrascritti con le informazioni dell'ultima conversione.

| File | Sommario |
|:---|:---|
| Runbook Converter - Progress.log | Passaggi dettagliati della conversione, comprese le informazioni per ogni attività convertita correttamente e gli avvisi per ogni attività non convertita. |
| Runbook Converter - Summary.log | Riepilogo dell'ultima conversione, compresi eventuali avvisi e attività di follow-up da eseguire, ad esempio la creazione di una variabile necessaria per il runbook convertito. |

### Esportazione di runbook da Orchestrator

Runbook Converter funziona con un file di esportazione di Orchestrator che contiene uno o più runbook. Crea un runbook di Automazione di Azure corrispondente per ogni runbook di Orchestrator nel file di esportazione.

Per esportare un runbook da Orchestrator, fare clic con il pulsante destro del mouse sul nome del runbook in Runbook Designer e selezionare **Esporta**. Per esportare tutti i runbook in una cartella, fare clic con il pulsante destro del mouse sul nome della cartella e selezionare **Esporta**.


### Attività del Runbook

Runbook Converter converte ogni attività del runbook di Orchestrator in un'attività corrispondente in Automazione di Azure. Per le attività che non possono essere convertite, viene creata un'attività segnaposto nel runbook con un testo di avviso. Dopo aver importato il runbook convertito in Automazione di Azure, è necessario sostituire queste attività, se presenti, con attività valide che eseguono la funzionalità necessaria.

Le eventuali attività di Orchestrator nel [modulo delle attività standard](#standard-activities-module) verranno convertite. Tuttavia, vi sono alcune attività standard di Orchestrator che non sono in questo modulo e che non vengono convertite. Ad esempio, **Invia evento piattaforma** non ha un equivalente in Automazione di Azure, poiché l'evento è specifico di Orchestrator.

Le [attività di monitoraggio](https://technet.microsoft.com/library/hh403827.aspx) non vengono convertite, in quanto non hanno un equivalente in Automazione di Azure. L'eccezione sono le attività di monitoraggio negli [Integration Pack convertiti](#integration-pack-converter), che verranno convertite in attività segnaposto.

Eventuali attività di un [Integration Pack convertito](#integration-pack-converter) verranno convertite se si specifica il percorso del modulo di integrazione con il parametro **modules**. Per gli Integration Pack di System Center, è possibile usare i [moduli di integrazione di System Center Orchestrator](#system-center-orchestrator-integration-modules).


### Risorse di Orchestrator

Runbook Converter converte solo runbook, non altre risorse di Orchestrator, ad esempio contatori, variabili o connessioni. I contatori non sono supportati in Automazione di Azure. Le variabili e le connessioni sono supportate, ma è necessario crearle manualmente. I file di log indicano se il runbook richiede tali risorse e specificano le risorse corrispondenti che è necessario creare in Automazione di Azure per il corretto funzionamento del runbook convertito.

Ad esempio, un runbook potrebbe usare una variabile per popolare un determinato valore in un'attività. Il runbook convertito convertirà l'attività e specificherà un asset variabile in Automazione di Azure con lo stesso nome della variabile di Orchestrator. Questo verrà annotato nel file **Runbook Converter - Summary.log** creato dopo la conversione. È necessario creare manualmente questo asset variabile in Automazione di Azure prima di usare il runbook.

 
### Parametri di input

I runbook di Orchestrator accettano parametri di input con l'attività **Inizializza dati**. Se il runbook convertito include questa attività, viene creato un [parametro di input](automation-graphical-authoring-intro.md#runbook-input-and-output) nel runbook di Automazione di Azure per ogni parametro nell'attività. Viene creata un'attività di [controllo dello script del flusso di lavoro](automation-graphical-authoring-intro.md#activities) nel runbook convertito, che recupera e restituisce tutti i parametri. Eventuali attività del runbook che usano un parametro di input fanno riferimento all'output di questa attività.

Il motivo per cui viene usata questa strategia è riprodurre meglio la funzionalità nel runbook di Orchestrator. Le attività nei nuovi runbook grafici devono fare riferimento direttamente ai parametri di input usando un'origine dati di input del runbook.


### Richiamare l'attività del runbook

I runbook di Orchestrator avviano altri runbook con l'attività **Richiama Runbook**. Se il runbook convertito include questa attività e l'opzione **Attendi completamento** è impostata, viene creata un'attività del runbook per tale attività nel runbook convertito. Se l'opzione **Attendi completamento** non è impostata, viene creata un'attività dello script del flusso di lavoro che usa **Start-AzureAutomationRunbook** per avviare il runbook. Dopo aver importato il runbook convertito in Automazione di Azure, è necessario modificare questa attività con le informazioni specificate nell'attività.




## Articoli correlati

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
- [Attività Standard di Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=AcomDC_0211_2016-->