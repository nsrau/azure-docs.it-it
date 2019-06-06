---
title: Eseguire il debug di funzioni di Azure PowerShell in locale
description: Informazioni su come sviluppare funzioni tramite PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: f02d2eed2030a5d2f54b3bee85885bbb572ac762
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476683"
---
# <a name="debug-powershell-azure-functions-locally"></a>Eseguire il debug di funzioni di Azure PowerShell in locale

Funzioni di Azure consente di sviluppare funzioni come gli script di PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

È possibile eseguire il debug delle funzioni di PowerShell in locale come si farebbe con gli strumenti di sviluppo standard seguenti degli script di PowerShell:

* [Visual Studio Code](https://code.visualstudio.com/): Editor di testo gratuiti, leggeri e open source di Microsoft con l'estensione di PowerShell che offre un'esperienza di sviluppo completa di PowerShell.
* Una console di PowerShell: Eseguire il debug usando gli stessi comandi che si utilizzerebbe per eseguire il debug di altri processi di PowerShell.

[Azure Functions Core Tools](functions-run-local.md) supporta il debug locale di funzioni di Azure, incluse le funzioni di PowerShell.

## <a name="example-function-app"></a>App di esempio (funzione)

L'app per le funzioni usate in questo articolo ha una singola funzione attivata tramite HTTP e presenta i seguenti file:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

App per le funzioni è simile a quella visualizzata quando si completa la [avvio rapido di PowerShell](functions-create-first-function-powershell.md).

Il codice della funzione in `run.ps1` appare come il seguente script:

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

## <a name="set-the-attach-point"></a>Impostare il punto di collegamento

Per eseguire il debug di qualsiasi funzione di PowerShell, la funzione deve arrestare il debugger deve essere collegato. Il `Wait-Debugger` cmdlet interrompe l'esecuzione e attende che il debugger.

È sufficiente fare è aggiungere una chiamata ai `Wait-Debugger` cmdlet subito sopra il `if` istruzione, come indicato di seguito:

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

All'avvio del debug di `if` istruzione. 

Con `Wait-Debugger` sul posto, è ora possibile eseguire il debug le funzioni usando Visual Studio Code o una console di PowerShell.

## <a name="debug-in-visual-studio-code"></a>Eseguire il debug in Visual Studio Code

Per eseguire il debug delle funzioni di PowerShell in Visual Studio Code, è necessario disporre dei seguenti prodotti installati:

* [Estensione di PowerShell per Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Estensione Funzioni di Azure per Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 o versione successiva](/powershell/scripting/install/installing-powershell#powershell-core)

Dopo l'installazione di queste dipendenze, caricare un progetto funzioni di PowerShell esistente, oppure [creare il primo progetto di funzioni di PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> Il progetto non necessario i file di configurazione necessari, viene chiesto per aggiungerli.

### <a name="set-the-powershell-version"></a>Impostare la versione di PowerShell

PowerShell Core viene installato side Windows PowerShell. PowerShell Core come la versione di PowerShell da usare con l'estensione di PowerShell per Visual Studio Code.

1. Premere F1 per visualizzare il gruppo di comandi e quindi cercare `Session`.

1. Scegliere **PowerShell: Mostra il Menu di sessione**.

1. Se il **sessione corrente** non viene **PowerShell Core 6**, scegliere **passare a: PowerShell Core 6**.

Quando è aperto un file di PowerShell, visualizzare la versione visualizzata in verde nella parte inferiore destra della finestra. Anche la selezione di questo testo Visualizza il menu di sessione. Per altre informazioni, vedere la [scelta di una versione di PowerShell da usare con l'estensione](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Avviare l'app per le funzioni

Verificare che `Wait-Debugger` è impostato nella funzione in cui si desidera collegare il debugger.  Con `Wait-Debugger` aggiunto, è possibile eseguire il debug di app per le funzioni usando Visual Studio Code.

Scegliere il **Debug** riquadro, quindi **collegare alla funzione di PowerShell**.

![Debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

È anche possibile premere il tasto F5 per avviare il debug.

Avvia debug operazione esegue le attività seguenti:

* Esecuzioni `func extensions install` nel terminale per installare le estensioni di funzioni di Azure necessarie per l'app di funzione.
* Esecuzioni `func host start` nel terminale per avviare l'app di funzione nell'host di funzioni.
* Collegare il debugger di PowerShell per lo spazio di esecuzione PowerShell all'interno del runtime di funzioni.

Con app per le funzioni in esecuzione, è necessario una console di PowerShell separata per chiamare la funzione attivata tramite HTTP.

In questo caso, la console di PowerShell è il client. Il `Invoke-RestMethod` viene usata per attivare la funzione.

Nella console di PowerShell, eseguire il comando seguente:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Si noterà che non viene restituita immediatamente una risposta. Infatti, `Wait-Debugger` è collegato il debugger e PowerShell è verificato un errore l'esecuzione in modalità di interruzione, non appena possibile. Ciò avviene perché il [concetto BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), descritta più avanti. Dopo aver premuto il `continue` pulsante, il debugger a questo punto si interrompe alla riga subito dopo `Wait-Debugger`.

A questo punto, il debugger è collegato ed è possibile eseguire tutti i debugger normali operazioni. Per altre informazioni sull'uso del debugger in Visual Studio Code, vedere [la documentazione ufficiale](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Dopo aver continuare e completamente richiamare lo script, si noterà che:

* La console di PowerShell che ha eseguito la `Invoke-RestMethod` ha restituito un risultato
* La Console di PowerShell integrata in Visual Studio Code è in attesa di uno script da eseguire

In un secondo momento quando si richiama la stessa funzione, il debugger nell'estensione di PowerShell si interrompe subito dopo il `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Debug in una Console di PowerShell

>[!NOTE]
> Questa sezione si presuppone di aver letto la [documentazione di Azure Functions Core Tools](functions-run-local.md) e sapere come usare il `func host start` comando per avviare l'app per le funzioni.

Aprire una console, `cd` nella directory dell'app di funzione, eseguire il comando seguente:

```sh
func host start
```

Con l'app per le funzioni in esecuzione e il `Wait-Debugger` sul posto, è possibile connettersi al processo. È necessario due ulteriori console di PowerShell.

Una delle console funge da client. Da questo, si chiama `Invoke-RestMethod` per attivare la funzione. Ad esempio, è possibile eseguire il comando seguente:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Si noti che non restituisce una risposta, ovvero un risultato del `Wait-Debugger`. Lo spazio di esecuzione PowerShell ora è in attesa un debugger da collegare. È possibile ottenere collegati.

In altre console di PowerShell, eseguire il comando seguente:

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

Assicurarsi di annotare il `ProcessId` per l'elemento della tabella con il `ProcessName` come `dotnet`. Questo processo è l'app per le funzioni.

Successivamente, eseguire il frammento di codice seguente:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Una volta avviata, il debugger si interrompe, unitamente a qualcosa di simile al seguente:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

A questo punto, arresto in corrispondenza di un punto di interruzione il [debugger PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). A questo punto, è possibile eseguire tutte le operazioni normali di debug, Esegui istruzione/routine, eseguire l'istruzione, continuare, chiudere e così via. Per visualizzare il set completo di comandi disponibili nella console di debug, eseguire la `h` o `?` comandi.

È anche possibile impostare i punti di interruzione a questo livello con il `Set-PSBreakpoint` cmdlet.

Dopo aver continuare e completamente richiamare lo script, si noterà che:

* La console di PowerShell in cui è stato eseguito `Invoke-RestMethod` ora ha restituito un risultato.
* La console di PowerShell in cui è stato eseguito `Debug-Runspace` è in attesa di uno script da eseguire.

È possibile richiamare di nuovo la stessa funzione (tramite `Invoke-RestMethod` ad esempio) e il debugger si interrompe subito dopo il `Wait-Debugger` comando.

## <a name="considerations-for-debugging"></a>Considerazioni per il debug

Tenere presente i seguenti problemi durante il debug di codice delle funzioni.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` potrebbe causare l'interruzione in una posizione non prevista del debugger

Gli utilizzi di estensione di PowerShell `Debug-Runspace`, che a sua volta si basa su PowerShell `BreakAll` funzionalità. Questa funzionalità indica a PowerShell venga interrotto in corrispondenza del primo comando che viene eseguito. Questo comportamento offre l'opportunità di impostare punti di interruzione all'interno dello spazio di esecuzione in fase di debug.

Il runtime di funzioni di Azure consente di eseguire alcuni comandi prima di richiamare effettivamente il `run.ps1` uno script, pertanto è possibile che il debugger finisce per ultime all'interno di `Microsoft.Azure.Functions.PowerShellWorker.psm1` o `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Questo tipo di interruzione deve verificarsi, eseguire la `continue` o `c` comando per ignorare il punto di interruzione. Quindi arresta nel punto di interruzione prevista.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di funzioni tramite PowerShell, vedere [Guida per gli sviluppatori di funzioni di Azure PowerShell](functions-reference-powershell.md).
