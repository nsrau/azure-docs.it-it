---
title: Gestione degli errori di Automazione di Azure | Microsoft Docs
description: Questo articolo fornisce le procedure di base sulla gestione degli errori al fine di risolvere e correggere gli errori più comuni di Automazione di Azure.
services: automation
documentationcenter: ''
author: mgoedtel
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: errore di automazione, gestione degli errori

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2016
ms.author: sngun; v-reagie

---
# Suggerimenti sulla gestione degli errori comuni relativi ad Automazione di Azure
Questo articolo illustra alcuni degli errori più comuni che possono verificarsi in Automazione di Azure e suggerisce le possibili procedure di gestione di tali errori.

## Risolvere gli errori di autenticazione durante l'utilizzo runbook di Automazione di Azure
### Scenario: Accesso all'account Azure non riuscito
**Errore:**
viene visualizzato l'errore "Unknown\_user\_type: tipo di utente sconosciuto" quando si utilizza il cmdlet Add-AzureAccount o Login-AzureRmAccount.

**Motivo dell'errore:**
questo errore si verifica se il nome dell'asset delle credenziali non è valido o se il nome utente e la password usati per impostare l'asset delle credenziali di automazione non sono validi.

**Suggerimenti sulla risoluzione dei problemi:**
per determinare la causa del problema, seguire questa procedura:

1. Assicurarsi che non siano presenti caratteri speciali, ad esempio il carattere **@** nel nome dell'asset delle credenziali di automazione usato per connettersi ad Azure.
2. Verificare che sia possibile usare il nome utente e la password archiviati nelle credenziali di Automazione di Azure nell'editor di PowerShell ISE locale. A questo scopo è possibile eseguire i cmdlet seguenti in PowerShell ISE:
   
        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. Se l'autenticazione non riesce in locale, significa che le credenziali di Azure Active Directory non sono state configurate correttamente. Per ottenere la configurazione corretta dell'account Azure Active Directory, vedere il post di blog relativo all'[autenticazione in Azure con Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).

### Scenario: Non è possibile trovare la sottoscrizione di Azure
**Errore:**
viene visualizzato l'errore relativo alla "impossibilità di trovare la sottoscrizione denominata ``<subscription name>``" quando si utilizzano i cmdlet Select-AzureSubscription o Select-AzureRmSubscription.

**Motivo dell'errore:**
questo errore si verifica se il nome della sottoscrizione non è valido o se l'utente di Azure Active Directory che sta tentando di ottenere i dettagli della sottoscrizione non è configurato come amministratore della sottoscrizione.

**Suggerimenti per la risoluzione dei problemi:**
per determinare se l'autenticazione in Azure è stata eseguita correttamente e se si ha accesso alla sottoscrizione che si sta tentando di selezionare, seguire questa procedura:

1. Assicurarsi di eseguire il cmdlet **Add-AzureAccount** prima del cmdlet **Select-AzureSubscription**.
2. Se viene ancora visualizzato questo messaggio di errore, modificare il codice aggiungendo il cmdlet **Get-AzureSubscription** dopo **Add-AzureAccount** e quindi eseguire il codice. A questo punto, verificare se l'output di Get-AzureSubscription contiene i dettagli della sottoscrizione.
   
   * Se nell'output non vengono visualizzati i dettagli della sottoscrizione, significa che non è ancora stata inizializzata.
   * Se nell'output vengono visualizzati i dettagli della sottoscrizione, assicurarsi di usare il nome o l'ID della sottoscrizione corretto con il cmdlet **Select-AzureSubscription**.

### Scenario: L'autenticazione in Azure non è riuscita perché è abilitata l'autenticazione a più fattori
**Errore:**
viene visualizzato un errore simile al seguente "Add-AzureAccount: AADSTS50079: è necessario eseguire la registrazione di autenticazione avanzata (pagina di registrazione)" durante l'autenticazione in Azure con il nome utente e la password di Azure.

**Motivo dell'errore:**
se nell'account Azure è abilitata l'autenticazione a più fattori, non è possibile usare un utente di Azure Active Directory per l'autenticazione in Azure. È invece necessario usare un certificato o un'entità servizio per l'autenticazione in Azure.

**Suggerimenti per la risoluzione dei problemi:**
per usare un certificato con i cmdlet di gestione del servizio di Azure, vedere il blog relativo alla [creazione e all'aggiunta di un certificato per la gestione dei servizi Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Per usare un'entità servizio con i cmdlet di Azure Resource Manager, vedere l'argomento relativo alla [creazione di un'entità servizio tramite il portale di Azure](../resource-group-create-service-principal-portal.md) e quello relativo all'[autenticazione di un'entità servizio con Azure Resource Manager.](../resource-group-authenticate-service-principal.md)

## Risolvere gli errori comuni durante l'utilizzo di runbook
### Scenario: Runbook con esito negativo a causa di un oggetto deserializzato
**Errore:**
il runbook non riesce con l'errore "Non è possibile associare il parametro ``<ParameterName>``. Non è possibile convertire il valore ``<ParameterType>`` del tipo ``<ParameterType>`` deserializzato nel tipo``<ParameterType>``".

**Motivo dell'errore:**
se il runbook è un flusso di lavoro di PowerShell, archivia gli oggetti complessi in un formato deserializzato per rendere persistente lo stato del runbook se il flusso di lavoro viene sospeso.

**Suggerimenti sulla risoluzione dei problemi:**  
una qualsiasi delle tre soluzioni seguenti consente di correggere questo problema.

1. Se si inviano tramite pipe oggetti complessi da un cmdlet a altro, eseguire il wrapping dei cmdlet in un InlineScript.  
2. Passare il nome o il valore necessario dall'oggetto complesso invece di passare l'intero oggetto.  
3. Usare un runbook di PowerShell invece di un runbook del flusso di lavoro PowerShell.  

### Scenario: Processo del Runbook non riuscito per il superamento della quota allocata
**Errore:**
il processo del runbook non riesce con l'errore "È stata raggiunta la quota per il tempo di esecuzione totale mensile dei processi per la sottoscrizione".

**Motivo dell'errore:**
questo errore si verifica quando l'esecuzione del processo supera la quota disponibile di 500 minuti per l'account. Questa quota si applica a tutti i tipi di attività di esecuzione del processo, ad esempio il test di un processo, l'avvio di un processo dal portale, l'esecuzione di un processo con webhook e la pianificazione di un processo da eseguire tramite il portale di Azure o nel proprio data center. Per altre informazioni sui prezzi relativi all'automazione, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

**Suggerimenti sulla risoluzione dei problemi:**
se si vogliono usare più di 500 minuti di elaborazione al mese, si dovrà modificare la sottoscrizione dal livello gratuito al livello Basic. È possibile eseguire l'aggiornamento al livello Basic seguendo questa procedura.

1. Accedere alla sottoscrizione di Azure.
2. Selezionare l'account di automazione che si vuole aggiornare.
3. Fare clic su **Impostazioni** > **Piano tariffario e utilizzo** > **Piano tariffario**.
4. Nel pannello **Scegliere il piano tariffario** selezionare **Basic**.

### Scenario: Cmdlet non riconosciuto durante l'esecuzione di un runbook
**Errore:**
il processo del runbook ha esito negativo e restituisce un errore simile al seguente "``<cmdlet name>``: il termine ``<cmdlet name>`` non viene riconosciuto come nome di cmdlet, funzione, file di script o programma eseguibile".

**Motivo dell'errore:**
questo errore si verifica quando il motore di PowerShell non trova il cmdlet usato nel runbook. È possibile che il modulo che contiene il cmdlet non sia incluso nell'account, che esista un conflitto di nome con il nome di un runbook o che il cmdlet sia presente anche in un altro modulo e che Automazione non possa risolvere il nome.

**Suggerimenti per la risoluzione dei problemi:**
una qualsiasi delle soluzioni seguenti consente di correggere il problema.  

* Verificare di aver immesso correttamente il nome del cmdlet.
* Assicurarsi che il cmdlet esista nell'account di automazione e che non siano presenti conflitti. Per verificare se il cmdlet è presente, aprire un runbook in modalità di modifica e cercare il cmdlet nella libreria o eseguire **Get-Command ``<CommandName>``**. Dopo aver verificato che il cmdlet è disponibile per l'account e che non ci sono conflitti di nomi con altri cmdlet o runbook, aggiungerlo all'area di disegno e assicurarsi di usare un set di parametri valido nel runbook.
* Nel caso di un conflitto di nomi e se il cmdlet è disponibile in due moduli diversi, è possibile risolvere questo problema usando il nome completo per il cmdlet. Ad esempio, usare **NomeModulo\\NomeCmdlet**.
* Se si eseguono i runbook in locale in un gruppo di lavoro ibrido, verificare che il modulo/cmdlet sia installato nel computer che ospita il processo di lavoro ibrido.

### Scenario: un runbook a esecuzione prolungata ha esito negativo e restituisce un'eccezione simile alla seguente "L'esecuzione del processo non può continuare perché è stato rimosso ripetutamente dallo stesso checkpoint".
**Motivo dell'errore:**
si tratta di un comportamento previsto, causato dalla "condivisione equa" del monitoraggio dei processi in Automazione di Azure, che prevede la sospensione automatica di un runbook se viene eseguito per più di 3 ore. Tuttavia, il messaggio di errore restituito non offre opzioni relative ai passaggi successivi. Un runbook può essere sospeso per una serie di motivi. Le sospensioni si verificano principalmente a causa di errori. Ad esempio, un'eccezione non rilevata in un runbook, un errore di rete o un arresto anomalo nel ruolo di lavoro Runbook che esegue il runbook: in tutti questi casi, il runbook viene sospeso e quando riprende viene avviato dall'ultimo checkpoint.

**Suggerimenti per la risoluzione dei problemi:**
la soluzione documentata per evitare questo problema consiste nell'usare i checkpoint in un flusso di lavoro. Per altre informazioni, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md#Checkpoints). Una spiegazione più completa della "condivisione equa" e dei checkpoint è disponibile nell'articolo del blog dedicato all'[uso dei checkpoint nei runbook](https://azure.microsoft.com/it-IT/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## Risolvere gli errori comuni durante l'importazione di moduli
### Scenario: L'importazione del modulo non riesce o i cmdlet non possono essere eseguiti dopo l'importazione
**Errore:**
l'importazione di un modulo ha esito negativo oppure riesce ma non viene estratto alcun cmdlet.

**Motivo dell'errore:**
seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure.

* La struttura non corrisponde a quella in cui il modulo dovrebbe essere incluso ai fini dell'automazione.
* Il modulo è dipendente da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il cmdlet **New AzureRmAutomationModule** viene usato per caricare il modulo e non è stato specificato il percorso di archiviazione completo oppure il modulo non è stato caricato con un URL accessibile pubblicamente.

**Suggerimenti per la risoluzione dei problemi:**  
una qualsiasi delle soluzioni seguenti consente di correggere il problema.  

* Verificare che il modulo segua questo formato:  
  NomeModulo.Zip **->** NomeModulo o NumeroVersione **->** (NomeModulo.psm1, NomeModulo.psd1)
* Aprire il file con estensione psd1 e vedere se il modulo include dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Assicurarsi che le eventuali DLL a cui viene fatto riferimento siano presenti nella cartella del modulo.

## Risolvere gli errori comuni quando si utilizza Desired State Configuration (DSC)
### Scenario: Lo stato di Node risulta non riuscito con un errore "Non trovato"
**Errore:**
il nodo presenta un report con stato **Non riuscito** contenente un errore simile al seguente "Non è stato possibile recuperare l’azione dal server https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction failed because a valid configuration ``<guid>".

**Motivo dell'errore:**
questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio ABC, anziché un nome di configurazione nodo, ad esempio ABC.WebServer.

**Suggerimenti per la risoluzione dei problemi:**  

* Assicurarsi di assegnare al nodo un "nome di configurazione di nodo" e non un "nome di configurazione".  
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.
  
  * Per assegnare a un nodo una configurazione nodo mediante il portale di Azure, aprire il pannello **Nodi DSC**, selezionare un nodo e quindi fare clic sul pulsante **Assegna configurazione nodo**.
  * Per assegnare a un nodo una configurazione nodo mediante PowerShell, usare il cmdlet **Set-AzureRmAutomationDscNode**.

### Scenario: Non sono state prodotte configurazioni di nodo (file con estensione MOF) durante la compilazione di una configurazione.
**Errore:**
il processo di compilazione di DSC viene sospeso e restituisce un errore simile al seguente "La compilazione è stata completata, ma non sono stati generati file con estensione mof di configurazione nodo".

**Motivo dell'errore:**
quando l'espressione accanto alla parola chiave **Node** nella configurazione di DSC restituisce $null, non vengono prodotte configurazioni di nodo.

**Suggerimenti per la risoluzione dei problemi:**  
una qualsiasi delle soluzioni seguenti consente di correggere il problema.

* Verificare che l'espressione accanto alla parola chiave **Node** nella definizione di configurazione non restituisca $null.
* Se durante la compilazione della configurazione si passano dei dati di configurazione, verificare di specificare i valori previsti necessari per la configurazione da [ConfigurationData](automation-dsc-compile.md#configurationdata).

### Scenario: Il report relativo al nodo DSC rimane bloccato nello stato "in corso".
**Errore:**
l'agente DSC restituisce un errore simile al seguente "Nessuna istanza trovata con i valori di proprietà specificati".

**Motivo dell'errore:**
è stato eseguito l'aggiornamento alla versione WMF e WMI è stato danneggiato.

**Suggerimenti per la risoluzione dei problemi:**
seguire le istruzioni riportate nel post del blog relativo a [limitazioni e problemi noti di DSC](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) per risolvere il problema.

### Scenario: Non è possibile usare le credenziali in una configurazione DSC
**Errore:** il processo di compilazione di DSC è stato sospeso con un errore simile al seguente "Si è verificato un errore System.InvalidOperationException durante l'elaborazione della proprietà "Credential" di tipo ``<some resource name>``: la conversione e l'archiviazione di una password crittografata come testo non crittografato sono consentite solo se PSDscAllowPlainTextPassword è impostato su true".

**Motivo dell'errore:** in una configurazione sono state usate credenziali, ma non è stato passato l'oggetto **ConfigurationData** corretto per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo.

**Suggerimenti per la risoluzione dei problemi:**  

* Verificare di passare l'oggetto **ConfigurationData** corretto per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo indicata nella configurazione. Per altre informazioni, vedere [la sezione relativa agli asset in Azure Automation DSC](automation-dsc-compile.md#assets).

## Passaggi successivi
Se sono state seguite le procedure precedenti per la risoluzione dei problemi e in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile:

* Ottenere assistenza dagli esperti di Azure. Inviare il problema al [forum MSDN dedicato ad Azure o a quello relativo all'overflow dello stack](https://azure.microsoft.com/support/forums/).
* Archiviare un incidente del supporto tecnico di Azure. Aprire il [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto** in **Supporto tecnico e alla fatturazione**.
* Se si sta cercando una soluzione per i runbook oppure un modulo di integrazione di Automazione di Azure, inviare una richiesta di script a [Script Center](https://azure.microsoft.com/documentation/scripts/).
* Inviare commenti o suggerimenti oppure richieste di funzionalità per Automazione di Azure al forum dedicato ai [suggerimenti degli utenti](https://feedback.azure.com/forums/34192--general-feedback).

<!---HONumber=AcomDC_0713_2016-->
