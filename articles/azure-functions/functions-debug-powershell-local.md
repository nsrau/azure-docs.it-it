---
title: Eseguire il debug di PowerShell Funzioni di Azure in localeDebug PowerShell Azure Functions locally
description: Comprendere come sviluppare funzioni tramite PowerShell.Understand how to develop functions by using PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163761"
---
# <a name="debug-powershell-azure-functions-locally"></a>Eseguire il debug di PowerShell Funzioni di Azure in localeDebug PowerShell Azure Functions locally

Funzioni di Azure consente di sviluppare le funzioni come script di PowerShell.Azure Functions lets you develop your functions as PowerShell scripts.

È possibile eseguire il debug delle funzioni di PowerShell in locale come qualsiasi script di PowerShell usando gli strumenti di sviluppo standard seguenti:You can debug your PowerShell functions locally as you any PowerShell scripts using the following standard development tools:

* [Codice di Visual Studio:](https://code.visualstudio.com/)editor di testo gratuito, leggero e open source di Microsoft con l'estensione PowerShell che offre un'esperienza di sviluppo PowerShell completa.
* Una console di PowerShell: eseguire il debug usando gli stessi comandi da usare per eseguire il debug di qualsiasi altro processo di PowerShell.A PowerShell console: Debug using the same commands you would use to debug any other PowerShell process.

[Gli](functions-run-local.md) strumenti di base di Funzioni di Azure supportano il debug locale di Funzioni di Azure, incluse le funzioni di PowerShell.Azure Functions Core Tools supports local debugging of Azure Functions, including PowerShell functions.

## <a name="example-function-app"></a>Esempio di app per funzioni

L'app per le funzioni utilizzata in questo articolo ha una singola funzione attivata HTTP e ha i seguenti file:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Questa app per le funzioni è simile a quella che si ottiene quando si completa la Guida introduttiva di [PowerShell.](functions-create-first-function-powershell.md)

Il codice `run.ps1` della funzione in è simile allo script seguente:The function code in looks like the following script:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Impostare il punto di attacco

Per eseguire il debug di qualsiasi funzione di PowerShell, la funzione deve essere interrotta per il debugger da collegare. Il `Wait-Debugger` cmdlet interrompe l'esecuzione e attende il debugger.

Tutto quello che dovete fare `Wait-Debugger` è aggiungere `if` una chiamata al cmdlet appena sopra l'istruzione, come segue:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Il debug `if` inizia in corrispondenza dell'istruzione. 

Con `Wait-Debugger` sul posto, è ora possibile eseguire il debug delle funzioni utilizzando Visual Studio Code o una console di PowerShell.With in place, you can now debug the functions using either Visual Studio Code or a PowerShell console.

## <a name="debug-in-visual-studio-code"></a>Debug in Visual Studio Code

Per eseguire il debug delle funzioni di PowerShell nel codice di Visual Studio, è necessario disporre di quanto segue:To debug your PowerShell functions in Visual Studio Code, you must have the following installed:

* [Estensione PowerShell per Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Estensione Funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 o versione successiva](/powershell/scripting/install/installing-powershell-core-on-windows)

Dopo aver installato queste dipendenze, caricare un progetto di Funzioni di PowerShell esistente o [creare il primo progetto Di PowerShell Funzioni.](functions-create-first-function-powershell.md)

>[!NOTE]
> Se il progetto non dispone dei file di configurazione necessari, viene richiesto di aggiungerli.

### <a name="set-the-powershell-version"></a>Impostare la versione di PowerShellSet the PowerShell version

PowerShell Core viene installato side-by-side con Windows PowerShell. Impostare PowerShell Core come versione di PowerShell da usare con l'estensione PowerShell per Visual Studio Code.Set PowerShell Core as the PowerShell version to use with the PowerShell extension for Visual Studio Code.

1. Premere F1 per visualizzare il pallet `Session`di comando, quindi cercare .

1. Scegliere **PowerShell: Mostra menu sessione**.

1. Se la **sessione corrente** non è **PowerShell Core 6,** scegliere **Passa a: PowerShell Core 6**.

Quando è aperto un file di PowerShell, viene visualizzata la versione visualizzata in verde nella parte inferiore destra della finestra. Selezionando questo testo viene visualizzato anche il menu della sessione. Per altre informazioni, vedere [Scelta di una versione di PowerShell da usare con l'estensione](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Avviare l'app per le funzioni

Verificare `Wait-Debugger` che sia impostato nella funzione in cui si desidera connettere il debugger.  Con `Wait-Debugger` l'aggiunta, puoi eseguire il debug dell'app per le funzioni usando Visual Studio Code.

Scegliere **il** debug riquadro e quindi **Connetti alla funzione PowerShell**.

![debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

È anche possibile premere il tasto F5 per avviare il debug.

L'operazione di avvio del debug esegue le attività seguenti:The start debugging operation does the following tasks:

* Viene `func extensions install` eseguito nel terminale per installare tutte le estensioni di Funzioni di Azure richieste dall'app per le funzioni.
* Viene `func host start` eseguito nel terminale per avviare l'app per le funzioni nell'host Funzioni.
* Connettere il debugger di PowerShell al runspace di PowerShell all'interno del runtime di Funzioni.Attach the PowerShell debugger to the PowerShell runspace within the Functions runtime.

>[!NOTE]
> È necessario assicurarsi che PSWorkerInProcConcurrencyUpperBound sia impostato su 1 per garantire la corretta esperienza di debug nel codice di Visual Studio.You need to ensure PSWorkerInProcConcurrencyUpperBound is set to 1 to ensure correct debugging experience in Visual Studio Code. Questa è la modalità predefinita.

Con l'app per le funzioni in esecuzione, è necessaria una console di PowerShell separata per chiamare la funzione attivata HTTP.

In questo caso, la console di PowerShell è il client. L'oggetto `Invoke-RestMethod` viene utilizzato per attivare la funzione.

In una console di PowerShell eseguire il comando seguente:In a PowerShell console, run the following command:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Si noterà che una risposta non viene restituita immediatamente. Questo perché `Wait-Debugger` ha collegato il debugger e l'esecuzione di PowerShell è entrata in modalità di interruzione non appena è possibile. Ciò è dovuto al [concetto BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), illustrato più avanti. Dopo aver `continue` premuto il pulsante , il `Wait-Debugger`debugger ora si interrompe sulla riga subito dopo .

A questo punto, il debugger è collegato ed è possibile eseguire tutte le normali operazioni del debugger. Per ulteriori informazioni sull'utilizzo del debugger in Visual Studio Code, vedere [la documentazione ufficiale](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Dopo aver continuato e richiamato completamente lo script, si noterà che:After you continue and fully invoke your script, you'll notice that:

* La console di PowerShell che ha eseguito l'oggetto `Invoke-RestMethod` ha restituito un risultato
* La console integrata di PowerShell nel codice di Visual Studio è in attesa dell'esecuzione di uno script

Successivamente, quando si richiama la stessa funzione, il `Wait-Debugger`debugger nell'estensione di PowerShell si interrompe subito dopo l'estensione .

## <a name="debugging-in-a-powershell-console"></a>Debug in una console di PowerShellDebugging in a PowerShell Console

>[!NOTE]
> Questa sezione presuppone che siano stati letti i documenti degli `func host start` strumenti di base di Funzioni di [Azure](functions-run-local.md) e che si sappia come usare il comando per avviare l'app per le funzioni.

Aprire una console `cd` nella directory dell'app per le funzioni ed eseguire il comando seguente:

```sh
func host start
```

Con l'app per `Wait-Debugger` le funzioni in esecuzione e l'app sul posto, è possibile connettersi al processo. Sono necessarie altre due console di PowerShell.You need two more consoles.

Una delle console funge da client. Da questo, `Invoke-RestMethod` si chiama per attivare la funzione. Ad esempio, è possibile eseguire il comando seguente:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Si noterà che non restituisce una risposta, che `Wait-Debugger`è un risultato del file . Lo spazio di esecuzione di PowerShell è ora in attesa di connettere un debugger. Mettiamolo attaccato.

Nell'altra console di PowerShell eseguire il comando seguente:In the other PowerShell console, run the following command:

```powershell
Get-PSHostProcessInfo
```

Questo cmdlet restituisce una tabella simile all'output seguente:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Prendere nota `ProcessId` di per l'elemento `ProcessName` `dotnet`nella tabella con il as . Questo processo è l'app per le funzioni.

Successivamente, eseguire il frammento di codice seguente:Next, run the following snippet:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Una volta avviato, il debugger si interrompe e mostra qualcosa di simile al seguente output:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

A questo punto, si è arrestati in corrispondenza di un punto di interruzione nel debugger di [PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). Da qui, è possibile eseguire tutte le normali operazioni di debug, eseguire un'istruzione alla volta, eseguire un'istruzione in, continuare, uscire e altri. Per visualizzare il set completo di comandi di `h` `?` debug disponibili nella console, eseguire i comandi o .

È inoltre possibile impostare punti `Set-PSBreakpoint` di interruzione a questo livello con il cmdlet.

Una volta continuato e richiamato completamente lo script, si noterà che:

* La console di PowerShell `Invoke-RestMethod` in cui è stata eseguita ha ora restituito un risultato.
* La console di PowerShell `Debug-Runspace` in cui è stata eseguita è in attesa dell'esecuzione di uno script.

È possibile richiamare nuovamente `Invoke-RestMethod` la stessa funzione (utilizzando ad `Wait-Debugger` esempio) e il debugger si interrompe subito dopo il comando.

## <a name="considerations-for-debugging"></a>Considerazioni per il debugConsiderations for debugging

Tenere presente i seguenti problemi durante il debug del codice di funzioni.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`potrebbe causare l'interruzione del debugger in un luogo imprevisto

L'estensione di `Debug-Runspace`PowerShell usa , che a `BreakAll` sua volta si basa sulla funzionalità di PowerShell.The PowerShell extension uses , which in turn relies on PowerShell's feature. Questa funzionalità indica a PowerShell di arrestarsi al primo comando eseguito. Questo comportamento consente di impostare punti di interruzione all'interno dello spazio di esecuzione sottoposto a debug.

Il runtime di Funzioni di Azure esegue `run.ps1` alcuni comandi prima di richiamare effettivamente `Microsoft.Azure.Functions.PowerShellWorker.psm1` lo `Microsoft.Azure.Functions.PowerShellWorker.psd1`script, pertanto è possibile che il debugger finisca per interrompersi all'interno di o .

Se si verifica questa `continue` `c` interruzione, eseguire il comando o per ignorare questo punto di interruzione. Si arresta quindi in corrispondenza del punto di interruzione previsto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di funzioni tramite PowerShell, vedere Guida per gli sviluppatori di Azure Functions PowerShell.To learn more about developing Functions using PowerShell, see [Azure Functions PowerShell developer guide.](functions-reference-powershell.md)
