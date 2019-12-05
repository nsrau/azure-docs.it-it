---
title: Sviluppare ed eseguire funzioni di Azure in locale
description: Informazioni su come scrivere codice per le funzioni di Azure e testarle nel computer locale prima di eseguirle in Funzioni di Azure.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230640"
---
# <a name="code-and-test-azure-functions-locally"></a>Scrivere codici per Funzioni di Azure e testarle in locale

Benché sia possibile sviluppare e testare Funzioni di Azure nel [portale di Azure], molti sviluppatori preferiscono un'esperienza di sviluppo locale. Funzioni di Azure semplificano l'uso dell'editor di codice e degli strumenti di sviluppo locale preferiti per creare e testare le funzioni nel computer locale. Le funzioni locali possono connettersi ai servizi di Azure attivi ed è possibile eseguirne il debug nel computer locale usando il runtime completo di Funzioni di Azure.

## <a name="local-development-environments"></a>Ambienti di sviluppo locale

Il modo in cui si sviluppano le funzioni nel computer locale dipende dal [linguaggio](supported-languages.md) e dalle preferenze relative agli strumenti. Gli ambienti nella tabella seguente supportano lo sviluppo locale:

|Environment                              |Lingue         |DESCRIZIONE|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-create-first-function-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(libreria di classi), script (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | L'[estensione di Funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) aggiunge il supporto di Funzioni di Azure a Visual Studio Code. Richiede gli strumenti di base. Supporta lo sviluppo in Linux, MacOS e Windows quando si usa la versione 2.x degli strumenti di base. Per altre informazioni, vedere [Creare la prima funzione con Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Prompt dei comandi o terminal](functions-run-local.md) | [](functions-reference-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(libreria di classi), script (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Strumenti di base di Funzioni di Azure] fornisce il runtime di base e i modelli per la creazione di funzioni che consentono lo sviluppo locale. La versione 2.x supporta lo sviluppo in Linux, MacOS e Windows. Tutti gli ambienti si basano su Strumenti di base per il runtime Funzioni di Azure locale. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (libreria di classi)](functions-dotnet-class-library.md) | Gli strumenti di funzioni di Azure sono inclusi nel carico di lavoro di **sviluppo di Azure** di [Visual Studio 2019](https://www.visualstudio.com/vs/) e versioni successive. Consente di compilare le funzioni in una libreria di classi e di pubblicare il file con estensione dll in Azure. Include gli strumenti di base per il test locale. Per altre informazioni, vedere [Sviluppare Funzioni di Azure con Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (varie) | [Java](functions-reference-java.md) | Si integra con gli strumenti di base per consentire lo sviluppo di funzioni Java. La versione 2.x supporta lo sviluppo in Linux, MacOS e Windows. Per altre informazioni, vedere [Creare la prima funzione con Java e Maven](functions-create-first-java-maven.md). Supporta inoltre lo sviluppo con [Eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Ognuno di questi ambienti di sviluppo locale consente di creare progetti di app delle funzioni e usare i modelli di Funzioni di Azure predefiniti per creare nuove funzioni. Ognuno di essi usa gli strumenti di base per consentire di testare ed eseguire il debug delle funzioni con il runtime reale di Funzioni di Azure nel proprio computer esattamente come qualsiasi altra app. È anche possibile pubblicare il progetto di app per le funzioni da uno di questi ambienti ad Azure.  

## <a name="next-steps"></a>Passaggi successivi

+ Per altre informazioni sullo sviluppo locale di funzioni C# compilate con visual studio 2019, vedere [sviluppare funzioni di Azure con Visual Studio](functions-develop-vs.md).
+ Per altre informazioni sullo sviluppo locale di funzioni con VS Code in un computer Mac, Linux o Windows, vedere [distribuire funzioni di Azure da vs code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Per altre informazioni sullo sviluppo di funzioni al prompt dei comandi o terminal, vedere la pagina relativa agli [strumenti di base per Funzioni di Azure](functions-run-local.md).

<!-- LINKS -->

[Strumenti di base di Funzioni di Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portale di Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
