---
title: Eseguire la migrazione da Orchestrator ad Automazione di Azure (Beta)
description: Questo articolo descrive come eseguire la migrazione di runbook e Integration Pack da Orchestrator in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a47f720344a16d0f77559d6aabfb2b0245e62976
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426334"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Eseguire la migrazione da Orchestrator ad Automazione di Azure (Beta)

I runbook in [System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) si basano su attività di Integration Pack scritte appositamente per Orchestrator, mentre i runbook di Automazione di Azure si basano su Windows PowerShell. [Runbook grafici](automation-runbook-types.md#graphical-runbooks) di Automazione di Azure hanno un aspetto simile ai runbook di Orchestrator con attività che rappresentano i cmdlet di PowerShell, i runbook figlio e gli asset. Oltre ai runbook stessi, è necessario convertire gli Integration Pack con le attività usate dai runbook in moduli di integrazione con cmdlet di Windows PowerShell. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) archivia ed esegue i runbook nel data center locale come Orchestrator e usa gli stessi moduli di integrazione di Automazione di Azure. Runbook Converter converte i runbook di Orchestrator in runbook grafici, che non sono supportati in SMA. È comunque possibile installare il modulo delle attività standard e i moduli di integrazione di System Center Orchestrator in SMA, ma in tal caso è necessario [riscrivere i runbook manualmente](/system-center/sma/authoring-automation-runbooks).

## <a name="download-the-orchestrator-migration-toolkit"></a>Scaricare il toolkit per la migrazione di Orchestrator

Il primo passaggio della migrazione consiste nello scaricare il [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Questo toolkit include strumenti che facilitano la conversione dei runbook da Orchestrator ad Automazione di Azure.  

## <a name="import-the-standard-activities-module"></a>Importare il modulo delle attività standard

Importare il [modulo delle attività standard](/system-center/orchestrator/standard-activities?view=sc-orch-2019) in Automazione di Azure. Questo modulo include versioni convertite di attività standard di Orchestrator che possono essere usate dai runbook grafici convertiti.

## <a name="import-orchestrator-integration-modules"></a>Importare moduli di integrazione di Orchestrator

Microsoft mette a disposizione [Integration Pack](/previous-versions/system-center/packs/hh295851(v=technet.10)) per la creazione di runbook per l'automatizzazione di componenti di System Center e altri prodotti. Alcuni degli Integration Pack attualmente sono basati su OIT ma non possono essere convertiti in moduli di integrazione a causa di problemi noti. Importare i [moduli di integrazione di System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) in Automazione di Azure per gli Integration Pack usati dai runbook che accedono a System Center. Questi pacchetti includono versioni convertite degli Integration Pack che possono essere importate in Automazione di Azure e in Service Management Automation.  

## <a name="convert-integration-packs"></a>Convertire gli Integration Pack

Usare [Integration Pack Converter](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard?view=sc-orch-2019) per convertire gli [Integration Pack creati mediante Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) in moduli di integrazione basati su PowerShell che possono essere importati in Automazione di Azure o in Service Management Automation. Quando si esegue Integration Pack Converter, viene visualizzata una procedura guidata che consente di selezionare un file Integration Pack (con estensione .oip). La procedura guidata elenca quindi le attività incluse nell'Integration Pack e consente di selezionare quelle da sottoporre a migrazione. Al termine della procedura guidata, viene creato un modulo di integrazione che include un cmdlet corrispondente per ogni attività dell'Integration Pack originale.

> [!NOTE]
> Gli Integration Pack non creati con OIT non possono essere convertiti con Integration Pack Converter. Esistono anche alcuni Integration Pack di Microsoft che attualmente non è possibile convertire con questo strumento. Le versioni convertite di questi Integration Pack sono disponibili per il download, in modo da poterle installare in Automazione di Azure o in Service Management Automation.

### <a name="parameters"></a>Parametri

Tutte le proprietà di un'attività dell'Integration Pack vengono convertite in parametri del cmdlet corrispondente nel modulo di integrazione.  I cmdlet di Windows PowerShell dispongono di un set di [parametri comuni](/powershell/module/microsoft.powershell.core/about/about_commonparameters) che possono essere usati con tutti i cmdlet. Con il parametro -Verbose ad esempio un cmdlet restituisce informazioni dettagliate sul proprio funzionamento.  Nessun cmdlet può usare un parametro con lo stesso nome di un parametro comune. Se un'attività dispone di una proprietà con lo stesso nome del parametro comune, la procedura guidata chiederà di specificare un altro nome per il parametro.

### <a name="monitor-activities"></a>Attività di monitoraggio

I runbook di monitoraggio in Orchestrator iniziano con un' [attività di monitoraggio](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) e vengono eseguiti in modo continuativo in attesa di essere richiamati da un evento specifico. Automazione di Azure non supporta i runbook di monitoraggio, pertanto le attività di monitoraggio nell'Integration Pack non verranno convertite. Nel modulo di integrazione verrà creato invece un cmdlet segnaposto per l'attività di monitoraggio.  Questo cmdlet è privo di funzionalità, ma consente l'installazione dei runbook convertiti che lo usano. Questo runbook non potrà essere eseguito in Automazione di Azure, ma può essere installato in modo che l'utente possa modificarlo.

Orchestrator contiene un insieme di [attività standard](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) non incluse in un Integration Pack, ma usate da molti runbook.  Il modulo delle attività standard è un modulo di integrazione che include un cmdlet equivalente per ognuna di queste attività. È necessario installare questo modulo di integrazione in Automazione di Azure prima di importare eventuali runbook convertiti che usano un'attività standard.

Oltre a supportare i runbook convertiti, i cmdlet del modulo delle attività standard possono essere usati da chiunque abbia familiarità con Orchestrator per creare nuovi runbook in Automazione di Azure. Benché le funzionalità di tutte le attività standard possano essere eseguite con i cmdlet, è possibile che funzionino in modo diverso. I cmdlet nel modulo delle attività standard convertite funzioneranno come le attività corrispondenti e useranno gli stessi parametri. Questo può essere utile per la transizione ai runbook di Automazione di Azure.

## <a name="convert-orchestrator-runbooks"></a>Convertire i runbook di Orchestrator

Orchestrator Runbook Converter converte i runbook di Orchestrator in [runbook grafici](automation-runbook-types.md#graphical-runbooks) che possono essere importati in Automazione di Azure. Runbook Converter viene implementato come modulo di PowerShell con il cmdlet `ConvertFrom-SCORunbook` che esegue la conversione. Quando si installa il convertitore, verrà creato un collegamento a una sessione di PowerShell che carica il cmdlet.   

Ecco i passaggi di base per convertire un runbook e importarlo in Automazione di Azure. I dettagli sull'uso del cmdlet sono disponibili più avanti in questa sezione.

1. Esportare uno o più runbook da Orchestrator.
2. Ottenere i moduli di integrazione per tutte le attività del runbook.
3. Convertire i runbook di Orchestrator nel file esportato.
4. Esaminare le informazioni nei log per convalidare la conversione e determinare eventuali attività manuali necessarie.
5. Importare i runbook convertiti in Automazione di Azure.
6. Creare gli eventuali asset necessari in Automazione di Azure.
7. Modificare il runbook in Automazione di Azure per modificare le attività necessarie.

La sintassi per `ConvertFrom-SCORunbook` è:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath: percorso del file di esportazione contenente i runbook da convertire.
* Module: elenco delimitato da virgole dei moduli di integrazione contenenti le attività nei runbook.
* OutputFolder: percorso della cartella per creare runbook grafici convertiti.

Il comando dell'esempio seguente converte i runbook in un file di esportazione denominato **MyRunbooks.ois_export**.  Questi runbook usano gli Integration Pack di Active Directory e di Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Usare i file di log di Runbook Converter

Runbook Converter crea i file di log seguenti nello stesso percorso del runbook convertito.  Se i file esistono già, verranno sovrascritti con le informazioni dell'ultima conversione.

| File | Sommario |
|:--- |:--- |
| Runbook Converter - Progress.log |Passaggi dettagliati della conversione, comprese le informazioni per ogni attività convertita correttamente e gli avvisi per ogni attività non convertita. |
| Runbook Converter - Summary.log |Riepilogo dell'ultima conversione, compresi eventuali avvisi e attività di follow-up da eseguire, ad esempio la creazione di una variabile necessaria per il runbook convertito. |

### <a name="export-runbooks-from-orchestrator"></a>Esportare runbook da Orchestrator

Runbook Converter funziona con un file di esportazione di Orchestrator che contiene uno o più runbook.  Crea un runbook di Automazione di Azure corrispondente per ogni runbook di Orchestrator nel file di esportazione.  

Per esportare un runbook da Orchestrator, fare clic con il pulsante destro del mouse sul nome del runbook in Runbook Designer e selezionare **Esporta**.  Per esportare tutti i runbook in una cartella, fare clic con il pulsante destro del mouse sul nome della cartella e selezionare **Esporta**.

### <a name="convert-runbook-activities"></a>Convertire attività Runbook

Runbook Converter converte ogni attività del runbook di Orchestrator in un'attività corrispondente in Automazione di Azure.  Per le attività che non possono essere convertite, viene creata un'attività segnaposto nel runbook con un testo di avviso.  Dopo aver importato il runbook convertito in Automazione di Azure, è necessario sostituire queste attività, se presenti, con attività valide che eseguono la funzionalità necessaria.

Le eventuali attività di Orchestrator nel modulo delle attività standard verranno convertite. Tuttavia, vi sono alcune attività standard di Orchestrator che non sono in questo modulo e che non vengono convertite. Ad esempio, `Send Platform Event` non ha un equivalente in Automazione di Azure, poiché l'evento è specifico di Orchestrator.

[Attività di monitoraggio](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) non vengono convertite, in quanto non hanno un equivalente in Automazione di Azure. L'eccezione sono le attività di monitoraggio negli Integration Pack convertiti , che verranno convertite in attività segnaposto.

Eventuali attività di un Integration Pack convertito verranno convertite se si specifica il percorso del modulo di integrazione con il parametro `modules`. Per gli Integration Pack di System Center, è possibile usare i moduli di integrazione di System Center Orchestrator.

### <a name="manage-orchestrator-resources"></a>Gestire le risorse di Orchestrator

Runbook Converter converte solo runbook, non altre risorse di Orchestrator, ad esempio contatori, variabili o connessioni.  I contatori non sono supportati in Automazione di Azure.  Le variabili e le connessioni sono supportate, ma è necessario crearle manualmente. I file di log indicano se il runbook richiede tali risorse e specificano le risorse corrispondenti che è necessario creare in Automazione di Azure per il corretto funzionamento del runbook convertito.

Ad esempio, un runbook potrebbe usare una variabile per popolare un determinato valore in un'attività.  Il runbook convertito convertirà l'attività e specificherà un asset variabile in Automazione di Azure con lo stesso nome della variabile di Orchestrator. Questa azione verrà annotata nel file **Runbook Converter - Summary.log** creato dopo la conversione. È necessario creare manualmente questo asset variabile in Automazione di Azure prima di usare il runbook.

### <a name="work-with-orchestrator-input-parameters"></a>Usare i parametri di input di Orchestrator

I runbook di Orchestrator accettano parametri di input con l'attività `Initialize Data`.  Se il runbook convertito include questa attività, viene creato un [parametro di input](automation-graphical-authoring-intro.md#handle-runbook-input) nel runbook di Automazione di Azure per ogni parametro nell'attività.  Viene creata un'attività di [controllo dello script del flusso di lavoro](automation-graphical-authoring-intro.md#use-activities) nel runbook convertito, che recupera e restituisce tutti i parametri. Eventuali attività del runbook che usano un parametro di input fanno riferimento all'output di questa attività.

Il motivo per cui viene usata questa strategia è riprodurre meglio la funzionalità nel runbook di Orchestrator.  Le attività nei nuovi runbook grafici devono fare riferimento direttamente ai parametri di input usando un'origine dati di input del runbook.

### <a name="invoke-runbook-activity"></a>Richiamare l'attività del runbook

I runbook di Orchestrator avviano altri runbook con l'attività `Invoke Runbook`. Se il runbook convertito include questa attività e l'opzione `Wait for completion` è impostata, viene creata un'attività del runbook per tale attività nel runbook convertito.  Se l'opzione `Wait for completion` non è impostata, viene creata un'attività dello script del flusso di lavoro che usa [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) per avviare il runbook. Dopo aver importato il runbook convertito in Automazione di Azure, è necessario modificare questa attività con le informazioni specificate nell'attività.

## <a name="create-orchestrator-assets"></a>Creare asset di Orchestrator

Runbook Converter non converte gli asset di Orchestrator. È necessario creare manualmente gli asset di Orchestrator necessari in Automazione di Azure.

## <a name="configure-hybrid-runbook-worker"></a>Configurare un ruolo di lavoro ibrido per runbook

Orchestrator archivia i runbook in un server di database e li esegue in server runbook, tutti contenuti nel data center locale. I runbook in Automazione di Azure sono archiviati nel cloud Azure e possono essere eseguiti nel data center locale tramite un computer [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Configurare un ruolo di lavoro per l'esecuzione di runbook convertiti da Orchestrator, perché sono progettati per l'esecuzione su server locali e per l'accesso alle risorse locali.

## <a name="related-articles"></a>Articoli correlati

* Per informazioni dettagliate su Orchestrator, vedere [System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* Altre informazioni sull'automazione della gestione dei servizi in [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Per informazioni dettagliate sulle attività di Orchestrator, vedere [Attività standard di Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Per ottenere il toolkit di migrazione di Orchestrator, vedere [Scaricare System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323).
* Per una panoramica del ruolo di lavoro ibrido per Runbook di Automazione di Azure, vedere [Panoramica del ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md).
