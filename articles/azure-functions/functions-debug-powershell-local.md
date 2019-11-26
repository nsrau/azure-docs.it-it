---
title: Eseguire il debug di funzioni di Azure PowerShell in locale
description: Informazioni su come sviluppare funzioni usando PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d15858e1fb535189891303a89b00021027f1e8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227032"
---
# <a name="debug-powershell-azure-functions-locally"></a>Eseguire il debug di funzioni di Azure PowerShell in locale

Funzioni di Azure consente di sviluppare funzioni come script di PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

È possibile eseguire il debug delle funzioni di PowerShell in locale come tutti gli script di PowerShell usando gli strumenti di sviluppo standard seguenti:

* [Visual Studio Code](https://code.visualstudio.com/): editor di testo gratuito, leggero e open source di Microsoft con l'estensione PowerShell che offre un'esperienza di sviluppo completa di PowerShell.
* Una console di PowerShell: eseguire il debug usando gli stessi comandi usati per eseguire il debug di qualsiasi altro processo di PowerShell.

[Azure Functions Core Tools](functions-run-local.md) supporta il debug locale di funzioni di Azure, incluse le funzioni di PowerShell.

## <a name="example-function-app"></a>App per le funzioni di esempio

L'app per le funzioni usata in questo articolo ha una singola funzione attivata tramite HTTP e include i file seguenti:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Questa app per le funzioni è simile a quella che si ottiene quando si completa la [Guida introduttiva di PowerShell](functions-create-first-function-powershell.md).

Il codice di funzione in `run.ps1` è simile allo script seguente:

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

## <a name="set-the-attach-point"></a>Impostare il punto di connessione

Per eseguire il debug di qualsiasi funzione di PowerShell, è necessario arrestare la funzione affinché il debugger venga collegato. Il cmdlet `Wait-Debugger` arresta l'esecuzione e attende il debugger.

È sufficiente aggiungere una chiamata al cmdlet `Wait-Debugger` appena sopra l'istruzione `if`, come indicato di seguito:

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

Il debug inizia in corrispondenza dell'istruzione `if`. 

Con `Wait-Debugger` sul posto, è ora possibile eseguire il debug delle funzioni utilizzando Visual Studio Code o una console di PowerShell.

## <a name="debug-in-visual-studio-code"></a>Debug in Visual Studio Code

Per eseguire il debug delle funzioni di PowerShell in Visual Studio Code, è necessario che siano installati gli elementi seguenti:

* [Estensione PowerShell per Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Estensione Funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6,2 o versione successiva](/powershell/scripting/install/installing-powershell-core-on-windows)

Dopo l'installazione di queste dipendenze, caricare un progetto di funzioni di PowerShell esistente o [creare il primo progetto di funzioni di PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> Se il progetto non dispone dei file di configurazione necessari, viene richiesto di aggiungerli.

### <a name="set-the-powershell-version"></a>Impostare la versione di PowerShell

PowerShell core viene installato side-by-side con Windows PowerShell. Impostare PowerShell core come versione di PowerShell da usare con l'estensione PowerShell per Visual Studio Code.

1. Premere F1 per visualizzare il comando pallet, quindi cercare `Session`.

1. Scegliere **PowerShell: Mostra menu sessione**.

1. Se la **sessione corrente** non è **PowerShell Core 6**, scegliere **passa a: PowerShell Core 6**.

Quando si apre un file di PowerShell, viene visualizzata la versione in verde nella parte inferiore destra della finestra. Selezionando questo testo viene visualizzato anche il menu sessione. Per altre informazioni, vedere la pagina relativa alla [scelta di una versione di PowerShell da usare con l'estensione](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Avviare l'app per le funzioni

Verificare che `Wait-Debugger` sia impostato nella funzione in cui si desidera alleghi il debugger.  Con `Wait-Debugger` aggiunto, è possibile eseguire il debug dell'app per le funzioni usando Visual Studio Code.

Scegliere il riquadro **debug** e quindi **connettersi alla funzione PowerShell**.

![Debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

È anche possibile premere il tasto F5 per avviare il debug.

L'operazione Avvia debug esegue le attività seguenti:

* Esegue `func extensions install` nel terminale per installare le estensioni di funzioni di Azure richieste dall'app per le funzioni.
* Esegue `func host start` nel terminale per avviare l'app per le funzioni nell'host di funzioni.
* Alleghi il debugger di PowerShell ai spazio di PowerShell nel runtime di funzioni.

>[!NOTE]
> È necessario assicurarsi che PSWorkerInProcConcurrencyUpperBound sia impostato su 1 per garantire la corretta esperienza di debug in Visual Studio Code. Questa è la modalità predefinita.

Con l'app per le funzioni in esecuzione, è necessaria una console di PowerShell separata per chiamare la funzione attivata tramite HTTP.

In questo caso, la console di PowerShell è il client. Il `Invoke-RestMethod` viene utilizzato per attivare la funzione.

In una console di PowerShell eseguire il comando seguente:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Si noterà che non viene immediatamente restituita una risposta. Questo perché `Wait-Debugger` ha associato il debugger e l'esecuzione di PowerShell è passata alla modalità di interruzione non appena possibile. Questo è dovuto al [concetto di BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), illustrato più avanti. Dopo aver premuto il pulsante `continue`, il debugger si interrompe ora alla riga immediatamente dopo `Wait-Debugger`.

A questo punto, il debugger è collegato ed è possibile eseguire tutte le normali operazioni del debugger. Per ulteriori informazioni sull'utilizzo del debugger in Visual Studio Code, vedere [la documentazione ufficiale](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Quando si continua e si richiama completamente lo script, si noterà che:

* La console di PowerShell che ha fatto il `Invoke-RestMethod` ha restituito un risultato
* La console integrata di PowerShell in Visual Studio Code è in attesa dell'esecuzione di uno script

In un secondo momento, quando si richiama la stessa funzione, il debugger nell'estensione PowerShell si interrompe subito dopo il `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Debug in una console di PowerShell

>[!NOTE]
> Questa sezione presuppone che siano stati letti i [documenti Azure Functions Core Tools](functions-run-local.md) e che sia possibile usare il `func host start` comando per avviare l'app per le funzioni.

Aprire una console, `cd` nella directory dell'app per le funzioni ed eseguire il comando seguente:

```sh
func host start
```

Con l'app per le funzioni in esecuzione e il `Wait-Debugger` sul posto, è possibile connettersi al processo. Sono necessarie altre due console di PowerShell.

Una delle console funge da client. Da questo, viene chiamato `Invoke-RestMethod` per attivare la funzione. Ad esempio, è possibile eseguire il comando seguente:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Si noterà che non restituisce una risposta, che è il risultato della `Wait-Debugger`. Il spazio di PowerShell è ora in attesa di un debugger da collegare. Si otterrà questo allegato.

Nell'altra console di PowerShell eseguire il comando seguente:

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

Prendere nota del `ProcessId` per l'elemento nella tabella con la `ProcessName` come `dotnet`. Questo processo è l'app per le funzioni.

Eseguire quindi il frammento di codice seguente:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Una volta avviata, il debugger si interrompe e viene visualizzato un output simile al seguente:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

A questo punto, si è arrestato in corrispondenza di un punto di interruzione nel [debugger di PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). Da qui è possibile eseguire tutte le normali operazioni di debug, Esegui istruzione/routine, Esegui istruzione, continua, Esci e altro. Per visualizzare il set completo di comandi di debug disponibili nella console, eseguire i comandi `h` o `?`.

È anche possibile impostare punti di interruzione a questo livello con il cmdlet `Set-PSBreakpoint`.

Quando si continua e si richiama completamente lo script, si noterà che:

* La console di PowerShell in cui è stato eseguito `Invoke-RestMethod` ha ora restituito un risultato.
* La console di PowerShell in cui è stato eseguito `Debug-Runspace` è in attesa dell'esecuzione di uno script.

È possibile richiamare di nuovo la stessa funzione (usando `Invoke-RestMethod` ad esempio) e il debugger si interrompe subito dopo il comando `Wait-Debugger`.

## <a name="considerations-for-debugging"></a>Considerazioni per il debug

Tenere presente i seguenti problemi durante il debug del codice di funzioni.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` possibile che il debugger si interrompa in una posizione non prevista

L'estensione PowerShell usa `Debug-Runspace`, che a sua volta si basa sulla funzionalità `BreakAll` di PowerShell. Questa funzionalità indica a PowerShell di arrestarsi al primo comando eseguito. Questo comportamento consente di impostare punti di interruzione all'interno della spazio sottoposta a debug.

Il runtime di funzioni di Azure esegue alcuni comandi prima di richiamare lo script di `run.ps1`, quindi è possibile che il debugger si rompa all'interno della `Microsoft.Azure.Functions.PowerShellWorker.psm1` o `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Se si verifica questo problema, eseguire il comando `continue` o `c` per ignorare il punto di interruzione. Si arresta quindi in corrispondenza del punto di interruzione previsto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di funzioni con PowerShell, vedere Guida per gli [sviluppatori di Azure Functions PowerShell](functions-reference-powershell.md).
