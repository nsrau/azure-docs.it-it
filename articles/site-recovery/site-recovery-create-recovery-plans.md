<properties
	pageTitle="Creazione dei piani di ripristino" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei server locali in Azure o in un data center secondario." 
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
	ms.date="10/07/2015" 
	ms.author="raynew"/>

# Creazione dei piani di ripristino

Il servizio Site Recovery fornisce un'efficace soluzione di ripristino di emergenza e continuità aziendale che consente di proteggere le macchine virtuali e i server fisici locali orchestrando e automatizzando la replica e il failover in Azure o in un centro dati locale secondario. Per un'introduzione agli scenari di distribuzione di Site Recovery, leggere [Panoramica di Site Recovery](site-recovery-overview.md).

## Informazioni sull’articolo

Nell'articolo vengono fornite informazioni sulla creazione e sulla personalizzazione dei piani di ripristino.

In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle nel [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

Il piano di ripristino è costituito da uno o più gruppi ordinati che contengono macchine virtuali o gruppi di replica protetti (per la replicazione SAN). Il failover dei computer viene eseguito in base al gruppo di appartenenza. Le macchine virtuali in un determinato gruppo eseguono il failover in parallelo. Per i piani di ripristino, procedere come segue:

- Definire gruppi di computer che eseguono il failover e si avviano contemporaneamente.
- Creare le dipendenze tra i computer raggruppandoli insieme in un gruppo di piano di ripristino. Ad esempio, per eseguire il failover e visualizzare un'applicazione specifica, raggruppare le macchine virtuali per l'applicazione nello stesso gruppo di piano di ripristino.
- Automatizzare ed estendere il failover. È possibile eseguire un failover non pianificato o un test, pianificato, in un piano di ripristino. È possibile personalizzare i piani di ripristino con gli script di automazione di Azure e con azioni manuali.

I piani di ripristino vengono visualizzati nella relativa **sezione** nel portale di ripristino del sito.


## Creare piani di ripristino

Il modo in cui viene creato un piano di ripristino dipende dalla distribuzione di Site Recovery.

- **Replica Hyper-V (VMM)**: se si esegue la replica da un sito VMM in un sito secondario locale o in Azure tramite la replica di Hyper-V, vengono aggiunte macchine virtuali Hyper-V protette da un cloud VMM a un piano di ripristino.
- **Replica Hyper-V (sito Hyper-V)**: se si esegue la replica da un sito di Hyper-V (senza un server VMM) in Azure, vengono aggiunte macchine virtuali Hyper-V protette da un gruppo di protezione a un piano di ripristino.
- **Replica SAN**: se si esegue la replica in un sito secondario locale con la replica SAN, al piano di ripristino viene aggiunto un gruppo di replica contenente macchine virtuali. Selezionare un gruppo di replica piuttosto che macchine virtuali specifiche perché tutte le macchine virtuali in un gruppo di replica devono eseguire il failover contemporaneamente (il failover viene prima eseguito a livello di archiviazione).
- **Replica VMware**: se si esegue la replica le macchine virtuali VMware in Azure, a un piano di ripristino vengono aggiunti gruppi di replica che contengono le macchine virtuali.

Creare un piano di ripristino come segue:

1. Nella scheda Piani di ripristino fare clic su Creare il piano di ripristino. Specificare un nome per il piano di ripristino, nonché per il server di origine e di destinazione Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino.

	- Se si esegue la replica da un server VMM in un altro server VMM, selezionare VMM nell'opzione relativa al **tipo di origine**, nonché il server VMM di origine e destinazione. Fare clic su **Hyper-V** per visualizzare i cloud configurati per l'utilizzo di Replica Hyper-V. 
	- Se si esegue la replica da un server VMM in un altro server VMM basato su SAN, selezionare VMM nell'opzione relativa al **tipo di origine**, nonché il server VMM di origine e destinazione. Fare clic su **SAN** per visualizzare solo i cloud configurati per la replica SAN.
	- Se si esegue la replica da VMM in Azure selezionare VMM in **tipo di origine**. Selezionare il server VMM di origine e **Azure** come destinazione.
	- Se si esegue la replica da un sito di Hyper-V, selezionare sito Hyper-V nel tipo di origine. Selezionare il sito come origine e **Azure** come destinazione.
	- Se si esegue la replica da VMware o un server fisico locale in Azure, selezionare un server di configurazione come origine e **Azure** come destinazione

2\. Nella **relativa opzione** selezionare le macchine virtuali (o il gruppo di replica) che si desidera aggiungere al gruppo predefinito (gruppo 1) nel piano di ripristino.

## Personalizzare piani di ripristino

Dopo aver aggiunto macchine virtuali protette o gruppi di replica al gruppo del piano di ripristino predefinito e dopo aver creato il piano, è possibile personalizzarlo:

- **Aggiungere nuovi gruppi**: è possibile aggiungere gruppi di piano di ripristino aggiuntivi. I gruppi vengono numerati nell'ordine in cui vengono aggiunti. È possibile aggiungere fino a sette gruppi. È possibile aggiungere più computer o gruppi di replica a questi nuovi gruppi. Si noti che una macchina virtuale o un gruppo di replica può essere incluso solo in un gruppo del piano di ripristino.
- **Aggiungere uno script**, è possibile aggiungere script prima o dopo un ripristino del gruppo di pianificazione. Quando si aggiunge uno script, viene aggiunto un set di azioni per il gruppo. Ad esempio, verrà creato un set di passaggi preliminari per il gruppo 1 denominato: Gruppo 1: passaggi preliminari. Tutti i passaggi preliminari verranno elencati in questo set. Si noti che se si dispone di un server VMM distribuito, è possibile aggiungere solo uno script nel sito primario.
- **Aggiungere un'azione manuale**: è possibile aggiungere azioni manuali da eseguire prima o dopo un gruppo del piano di ripristino. Quando viene eseguito, il piano di ripristino si interrompe nel punto in cui è stata inserita l'azione manuale e in una finestra di dialogo viene richiesto di specificare il completamento dell'azione.

### Estendere i piani di ripristino con gli script

È possibile aggiungere uno script per il piano di ripristino:

Se si dispone di un sito di origine VMM, è possibile creare uno script nel server VMM e includerlo nel piano di ripristino. Se si esegue la replica in Azure è possibile integrare i runbook di automazione di Azure nel piano di ripristino

#### Creare uno script VMM

Prima di iniziare, tenere presente quanto segue:

- Scrivere gli script con Windows PowerShell.
- I cmdlet di VMM vengono forniti in un modulo di Windows PowerShell. Il modulo Windows PowerShell di VMM viene installato quando si installa la console VMM. Il modulo VMM può essere caricato nello script utilizzando il seguente comando nello script: Import-Module-Name virtualmachinemanager. [Ulteriori informazioni](hhttps://technet.microsoft.com/library/hh875013.aspx).
- Assicurarsi di disporre di almeno un server di libreria nella distribuzione di VMM. Per impostazione predefinita il percorso della condivisione di libreria per un server VMM si trova in locale nel server VMM con il nome di cartella MSCVMMLibrary.
- Se il percorso della condivisione di libreria è remoto (o locale ma non condiviso con MSCVMMLibrary, configurare la condivisione come segue utilizzando \\libserver2.contoso.com\\share\\ come esempio):
	- Aprire l'Editor del Registro di sistema.
	- Passare a HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft System Center Virtual Machine Manager Server\\DRAdapter\\Registration.
	- Modificare il valore ScriptLibraryPath.
	- Inserire il valore come \\libserver2.contoso.com\\share. Specificare il nome di dominio completo.
	- Fornire le autorizzazioni per il percorso di condivisione.

- Gli script in un piano di ripristino vengono eseguiti nel contesto dell'account del servizio VMM. Verificare che questo account disponga delle autorizzazioni di lettura sulla condivisione remota in cui si trova lo script e testare lo script da eseguire a livello di privilegi di account di servizio VMM.
- 	Assicurarsi di testare lo script con un account utente che disponga delle stesse autorizzazioni dell'account del servizio VMM, per garantire che gli script testati autonomi vengano eseguiti nello stesso modo che nei piani di ripristino.
- 	Nel server VMM, impostare i criteri di esecuzione in modo venga ignorato quanto segue:
	- Aprire la console di Windows PowerShell a 64 bit con privilegi elevati.
	- Digitare: **Set-executionpolicy bypass**. [Ulteriori informazioni](https://technet.microsoft.com/library/ee176961.aspx).
- Assicurarsi di utilizzare blocchi try-catch, in modo che le eccezioni vengano gestite normalmente. Se si verifica un'eccezione nello script, l'esecuzione viene interrotta e l'attività viene illustrato come non riuscita. Se si verifica un errore, qualsiasi parte rimanente dello script non verrà eseguita. Se si verifica quando si esegue un failover non pianificato, il piano di ripristino continuerà. Se ciò si verifica quando si esegue un failover pianificato, il piano di ripristino verrà interrotto. In questo caso, correggere lo script, verificare che venga eseguito come previsto e quindi eseguire nuovamente il piano di ripristino.
- Il comando Write-Host non funziona in uno script del piano di ripristino e lo script avrà esito negativo. Se si desidera creare l'output, creare uno script proxy che a sua volta esegue lo script principale e verificare che tutto l'output viene reindirizzato utilizzando il comando > >.
- Lo script verifica il timeout se non viene restituito all'interno di 600 secondi.
- Se viene scritto in STDERR, lo script verrà classificato come non completato. Queste informazioni verranno visualizzate nei dettagli dell'esecuzione di script.

Creare lo script come segue:

1. Creare una nuova cartella nella condivisione di libreria, ad esempio <VMMServerName>\\MSSCVMMLibrary\\RPScripts. Posizionarla nei server VMM di origine e destinazione.
2. Creare lo script (ad esempio RPScript) e verificare che funzioni come previsto.
3. Inserire lo script nel percorso <Nomeservervmm>\\MSSCVMMLibrary nel server VMM di origine e di destinazione.

#### Creare un runbook di automazione di Azure

È possibile estendere il piano di ripristino eseguendo un runbook di automazione di Azure come parte del piano. [Altre informazioni](site-recovery-runbook-automation.md).


### Aggiungere impostazioni personalizzate a un piano di ripristino

1. Aprire il piano di ripristino che si desidera personalizzare.
2. Fare clic per aggiungere una macchina virtuale o un nuovo gruppo.
3. Per aggiungere uno script o manuale azione, fare clic su qualsiasi elemento nell’elenco dei **passaggio**, quindi fare clic sull’opzione relativa allo **Script** o all’**azione manuale**. Specificare se si desidera aggiungere lo script o l’azione prima o dopo l'elemento selezionato. Utilizzare i pulsanti per lo spostamento verso l’**alto** e verso il **basso** i pulsanti per spostare la posizione dello script verso l'alto o verso il basso di comando.
4. Se si aggiunge uno script VMM, selezionare l’opzione relativa al **failover nello script VMM** e al **percorso dello Script** digitare il percorso relativo alla condivisione. Quindi, ad esempio se la condivisione si trova in: \<VMMServerName>\\MSSCVMMLibrary\\RPScripts, specificare il percorso: \\rpscripts\\rpscript.ps1..
5. Se si aggiunge un runbook di automazione di Azure, specificare l’**account di automazione di Azure** in cui si trova il runbook e selezionare lo **script runbook appropriato per Azure**.
5. Eseguire un failover del piano di ripristino per assicurarsi che lo script funzioni come previsto.


## Eseguire un failover

È possibile eseguire diversi tipi di piano di ripristino di failover, inclusi un failover di test per verificare l'ambiente e un failover pianificato o non pianificato. Ulteriori informazioni sui failover e istruzioni per l'esecuzione di diversi tipi di failover sono disponibili [qui](site-recovery-failover.md).


 

<!----HONumber=Oct15_HO3-->