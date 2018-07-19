---
title: Risoluzione dei problemi relativi ai runbook di Automazione di Azure
description: Informazioni su come risolvere i problemi relativi ai runbook di Automazione di Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b96d723f6c7ca423343c0586f59770abb55ada9f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929350"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Risoluzione dei problemi relativi ai runbook

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Errori di autenticazione durante l'utilizzo di runbook di Automazione di Azure

### <a name="sign-in-failed"></a>Scenario: Accesso all'account Azure non riuscito

#### <a name="issue"></a>Problema

Usando i cmdlet `Add-AzureAccount` o `Connect-AzureRmAccount` verrà visualizzato il seguente errore.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Causa

Questo errore si verifica se il nome dell'asset delle credenziali non è valido o se il nome utente e la password usati per impostare l'asset delle credenziali di automazione non sono validi.

#### <a name="resolution"></a>Risoluzione

Per determinare la causa del problema, seguire questa procedura:  

1. Assicurarsi che non siano presenti caratteri speciali, ad esempio il carattere **@** nel nome dell'asset delle credenziali di automazione usato per connettersi ad Azure.  
2. Verificare che sia possibile usare il nome utente e la password archiviati nelle credenziali di Automazione di Azure nell'editor di PowerShell ISE locale. A questo scopo è possibile eseguire i cmdlet seguenti in PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se l'autenticazione non riesce in locale, significa che le credenziali di Azure Active Directory non sono state configurate correttamente. Per ottenere la configurazione corretta dell'account Azure Active Directory, vedere il post di blog relativo all' [autenticazione in Azure con Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) .  

### <a name="unable-to-find-subscription"></a>Scenario: Non è possibile trovare la sottoscrizione di Azure

#### <a name="issue"></a>Problema

Usando i cmdlet `Select-AzureSubscription` o `Select-AzureRmSubscription` verrà visualizzato il seguente errore:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Tipi di errore

Questo errore si verifica se il nome della sottoscrizione non è valido o se l'utente di Azure Active Directory che sta tentando di ottenere i dettagli della sottoscrizione non è configurato come amministratore della sottoscrizione.

#### <a name="resolution"></a>Risoluzione

Per determinare se l'autenticazione in Azure è stata eseguita correttamente e se si ha accesso alla sottoscrizione che si sta tentando di selezionare, seguire questa procedura:  

1. Assicurarsi di eseguire il cmdlet **Add-AzureAccount** prima del cmdlet **Select-AzureSubscription**.  
2. Se viene ancora visualizzato questo messaggio di errore, modificare il codice aggiungendo il cmdlet **Get-AzureSubscription** dopo **Add-AzureAccount**, quindi eseguire il codice. A questo punto, verificare se l'output di Get-AzureSubscription contiene i dettagli della sottoscrizione.  

   * Se nell'output non vengono visualizzati i dettagli della sottoscrizione, significa che non è ancora stata inizializzata.  
   * Se nell'output vengono visualizzati i dettagli della sottoscrizione, assicurarsi di usare il nome o l'ID della sottoscrizione corretto con il cmdlet **Select-AzureSubscription** .

### <a name="auth-failed-mfa"></a>Scenario: L'autenticazione in Azure non è riuscita perché è abilitata l'autenticazione a più fattori

#### <a name="issue"></a>Problema

Durante l'autenticazione in Azure con il nome utente e la password di Azure viene visualizzato il seguente errore:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Causa

Se nell'account Azure è abilitata l'autenticazione a più fattori, non è possibile usare un utente di Azure Active Directory per l'autenticazione in Azure. È invece necessario usare un certificato o un'entità servizio per l'autenticazione in Azure.

#### <a name="resolution"></a>Risoluzione

Per usare un certificato con i cmdlet del modello di distribuzione classica di Azure, vedere il blog relativo alla [creazione e all'aggiunta di un certificato per la gestione dei servizi Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Per usare un'entità servizio con i cmdlet di Azure Resource Manager, vedere l'argomento relativo alla [creazione di un'entità servizio tramite il portale di Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md) e quello relativo all'[autenticazione di un'entità servizio con Azure Resource Manager.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Errori comuni durante l'utilizzo di runbook

### <a name="job-attempted-3-times"></a>Scenario: l'avvio del processo runbook è stato tentato per tre volte, ma non è mai riuscito

#### <a name="issue"></a>Problema

Il runbook ha esito negativo con errore:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Causa

Questo errore può dipendere dalle cause seguenti:

1. Limite di memoria. Esistono dei limiti di quantità di memoria allocata a una sandbox con [limiti del servizio di automazione](../../azure-subscription-service-limits.md#automation-limits), pertanto un processo potrebbe non riuscire se usa più di 400 MB di memoria.

2. Modulo incompatibile. Ciò può verificarsi se le dipendenze del modulo non sono corrette, e se non lo sono il runbook restituisce in genere il messaggio "Comando non trovato" o "Non è possibile associare il parametro".

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Metodi consigliati per operare entro il limite di memoria sono, ad esempio, dividere il carico di lavoro tra diversi runbook, non elaborare tutti i dati in memoria, non scrivere output non necessari dai runbook oppure tenere in considerazione il numero di checkpoint scritti nei runbook del flusso di lavoro PowerShell.  

* Aggiornare i moduli di Azure eseguendo i passaggi [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Scenario: Runbook con esito negativo a causa di un oggetto deserializzato

#### <a name="issue"></a>Problema

Il runbook ha esito negativo con errore:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Causa

Se il runbook è un flusso di lavoro di PowerShell, archivia gli oggetti complessi in un formato deserializzato per rendere persistente lo stato del runbook quando il flusso di lavoro viene sospeso.

#### <a name="resolution"></a>Risoluzione

Una qualsiasi delle tre soluzioni seguenti consente di correggere questo problema:

1. Se si inviano tramite pipe oggetti complessi da un cmdlet a altro, eseguire il wrapping dei cmdlet in un InlineScript.
2. Passare il nome o il valore necessario dall'oggetto complesso invece di passare l'intero oggetto.
3. Usare un runbook di PowerShell invece di un runbook del flusso di lavoro PowerShell.

### <a name="quota-exceeded"></a>Scenario: Processo del Runbook non riuscito per il superamento della quota allocata

#### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando l'esecuzione del processo supera la quota disponibile di 500 minuti per l'account. Questa quota si applica a tutti i tipi di attività di esecuzione del processo, ad esempio il test di un processo, l'avvio di un processo dal portale, l'esecuzione di un processo con webhook e la pianificazione di un processo da eseguire tramite il portale di Azure o nel proprio data center. Per altre informazioni sui prezzi relativi all'automazione, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Risoluzione

Se si vogliono usare più di 500 minuti di elaborazione al mese, si deve modificare la sottoscrizione dal livello gratuito al livello Basic. È possibile eseguire l'aggiornamento al livello Basic seguendo questa procedura.  

1. Accedere alla sottoscrizione di Azure.  
2. Selezionare l'account di automazione che si vuole aggiornare.  
3. Fare clic su **Impostazioni** > **Prezzi**.
4. Fare clic su **Abilita** nella parte inferiore della pagina per aggiornare l'account al livello **Basic**.

### <a name="cmdlet-not-recognized"></a>Scenario: Cmdlet non riconosciuto durante l'esecuzione di un runbook

#### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando il motore di PowerShell non trova il cmdlet usato nel runbook. È possibile che il modulo che contiene il cmdlet non sia incluso nell'account, che esista un conflitto di nome con il nome di un runbook o che il cmdlet sia presente anche in un altro modulo e che Automazione non possa risolvere il nome.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:  

* Verificare di aver immesso correttamente il nome del cmdlet.  
* Assicurarsi che il cmdlet esista nell'account di automazione e che non siano presenti conflitti. Per verificare se il cmdlet è presente, aprire un runbook in modalità di modifica e cercare il cmdlet nella libreria o eseguire `Get-Command <CommandName>`. Dopo aver verificato che il cmdlet è disponibile per l'account e che non ci sono conflitti di nomi con altri cmdlet o runbook, aggiungerlo all'area di disegno e assicurarsi di usare un set di parametri valido nel runbook.  
* Nel caso di un conflitto di nomi e se il cmdlet è disponibile in due moduli diversi, è possibile risolvere questo problema usando il nome completo per il cmdlet. Ad esempio, usare **ModuleName\CmdletName**.  
* Se si eseguono i runbook in locale in un gruppo di lavoro ibrido, verificare che il modulo/cmdlet sia installato nel computer che ospita il processo di lavoro ibrido.

### <a name="evicted-from-checkpoint"></a>Scenario: un runbook a esecuzione prolungata ha esito negativo e restituisce un'eccezione simile alla seguente: "L'esecuzione del processo non può continuare perché è stato rimosso ripetutamente dallo stesso checkpoint"

#### <a name="issue"></a>Problema

Si tratta di un comportamento previsto, causato dalla "condivisione equa" del monitoraggio dei processi in Automazione di Azure, che prevede la sospensione automatica di un runbook se viene eseguito per più di tre ore. Tuttavia, il messaggio di errore restituito non offre opzioni relative ai passaggi successivi.

#### <a name="cause"></a>Causa

Un runbook può essere sospeso per una serie di motivi. Le sospensioni si verificano principalmente a causa di errori. Ad esempio, un'eccezione non rilevata in un runbook, un errore di rete o un arresto anomalo nel ruolo di lavoro Runbook che esegue il runbook: in tutti questi casi, il runbook viene sospeso e quando riprende viene avviato dall'ultimo checkpoint.

#### <a name="resolution"></a>Risoluzione

La soluzione documentata per evitare questo problema consiste nell'usare i checkpoint in un flusso di lavoro. Per altre informazioni, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](../automation-powershell-workflow.md#checkpoints). Una spiegazione più completa della "condivisione equa" e dei checkpoint è disponibile nell'articolo del blog dedicato all'[uso dei checkpoint nei runbook](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

### <a name="long-running-runbook"></a>Scenario: un runbook a esecuzione prolungata non viene completato

#### <a name="issue"></a>Problema

Si tratta di un comportamento previsto nei sandbox di Azure, causato dalla "condivisione equa" del monitoraggio dei processi in Automazione di Azure, che prevede la sospensione automatica di un runbook se viene eseguito per più di tre ore.

#### <a name="cause"></a>Causa

L'esecuzione del runbook supera il limite di 3 ore consentito dalla condivisione equa in una sandbox di Azure

#### <a name="resolution"></a>Risoluzione

La soluzione consigliata consiste nell'eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non sono limitati dal massimo di 3 ore del runbook per la [condivisione equa](../automation-runbook-execution.md#fair-share) rispetto a come vengono eseguiti nelle sandbox di Azure.

## <a name="common-errors-when-importing-modules"></a>Errori comuni durante l'importazione di moduli

### <a name="module-fails-to-import"></a>Scenario: L'importazione del modulo non riesce o i cmdlet non possono essere eseguiti dopo l'importazione

#### <a name="issue"></a>Problema

L'importazione di un modulo ha esito negativo oppure riesce ma non viene estratto alcun cmdlet.

#### <a name="cause"></a>Causa

Di seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure:

* La struttura non corrisponde a quella in cui il modulo dovrebbe essere incluso ai fini dell'automazione.
* Il modulo è dipendente da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il cmdlet `New-AzureRmAutomationModule` viene usato per caricare il modulo e non è stato specificato il percorso di archiviazione completo oppure il modulo non è stato caricato con un URL accessibile pubblicamente.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Assicurarsi che il modulo sia conforme al formato seguente: NomeModulo.Zip **->** NomeModulo o NumeroVersione **->** (NomeModulo.psm1, NomeModulo.psd1)
* Aprire il file con estensione psd1 e vedere se il modulo include dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Assicurarsi che le eventuali DLL a cui viene fatto riferimento siano presenti nella cartella del modulo.

## <a name="next-steps"></a>Passaggi successivi

Se il problema che si riscontra non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ricevere assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.