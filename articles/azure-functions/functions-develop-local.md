---
title: Sviluppare ed eseguire localmente le funzioni di Azure | Documentazione Microsoft
description: Informazioni su come scrivere codice per le funzioni di Azure e testarle nel computer locale prima di eseguirle in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4721d87ab932f2e61346af3a48193e7075128622
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492852"
---
# <a name="code-and-test-azure-functions-locally"></a>Scrivere codici per Funzioni di Azure e testarle in locale

Benché sia possibile sviluppare e testare Funzioni di Azure nel [portale di Azure], molti sviluppatori preferiscono un'esperienza di sviluppo locale. Funzioni di Azure semplificano l'uso dell'editor di codice e degli strumenti di sviluppo locale preferiti per creare e testare le funzioni nel computer locale. Le funzioni locali possono connettersi ai servizi di Azure attivi ed è possibile eseguirne il debug nel computer locale usando il runtime completo di Funzioni di Azure.

## <a name="local-development-environments"></a>Ambienti di sviluppo locale

Il modo in cui si sviluppano le funzioni nel computer locale dipende dal [linguaggio](supported-languages.md) e dalle preferenze relative agli strumenti. Gli ambienti nella tabella seguente supportano lo sviluppo locale:

|Environment                              |Languages         |Descrizione|
|-----------------------------------------|------------|---|
| [Prompt dei comandi o terminal](functions-run-local.md) | [C# (libreria di classi)](functions-dotnet-class-library.md), [Script C# (file con estensione csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Strumenti di base di Funzioni di Azure] offre il runtime di base e i modelli per la creazione di funzioni che consentono lo sviluppo locale. La versione 2.x supporta lo sviluppo in Linux, MacOS e Windows. Tutti gli ambienti si basano su Strumenti di base per il runtime Funzioni di Azure locale. |
|[Visual Studio Code](functions-create-first-function-vs-code.md)| [C# (libreria di classi)](functions-dotnet-class-library.md), [Script C# (file con estensione csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | L'[estensione di Funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) aggiunge il supporto di Funzioni di Azure a Visual Studio Code. Richiede gli strumenti di base. Supporta lo sviluppo in Linux, MacOS e Windows quando si usa la versione 2.x degli strumenti di base. Per altre informazioni, vedere [Creare la prima funzione con Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (libreria di classi)](functions-dotnet-class-library.md) | Gli strumenti di funzioni di Azure sono inclusi nel **sviluppo di Azure** carico di lavoro di [Visual Studio 2019](https://www.visualstudio.com/vs/) e versioni successive. Consente di compilare le funzioni in una libreria di classi e di pubblicare il file con estensione dll in Azure. Include gli strumenti di base per il test locale. Per altre informazioni, vedere [Sviluppare Funzioni di Azure con Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (varie) | [Java](functions-reference-java.md) | Si integra con gli strumenti di base per consentire lo sviluppo di funzioni Java. La versione 2.x supporta lo sviluppo in Linux, MacOS e Windows. Per altre informazioni, vedere [Creare la prima funzione con Java e Maven](functions-create-first-java-maven.md). Supporta inoltre lo sviluppo con [Eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Ognuno di questi ambienti di sviluppo locale consente di creare progetti di app delle funzioni e usare i modelli di Funzioni di Azure predefiniti per creare nuove funzioni. Ognuno di essi usa gli strumenti di base per consentire di testare ed eseguire il debug delle funzioni con il runtime reale di Funzioni di Azure nel proprio computer esattamente come qualsiasi altra app. È anche possibile pubblicare il progetto di app delle funzioni da uno qualsiasi di questi ambienti in Azure.  

## <a name="next-steps"></a>Passaggi successivi

+ Per altre informazioni sullo sviluppo in locale di compilazione C# funzioni che utilizzano Visual Studio 2019, vedere [sviluppare funzioni di Azure usando Visual Studio](functions-develop-vs.md).
+ Per altre informazioni sullo sviluppo in locale di funzioni con Visual Studio Code in un computer Mac, Linux o Windows, vedere la [documentazione di Visual Studio Code per Funzioni di Azure](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Per altre informazioni sullo sviluppo di funzioni al prompt dei comandi o terminal, vedere la pagina relativa agli [strumenti di base per Funzioni di Azure](functions-run-local.md).

<!-- LINKS -->

[Strumenti di base di Funzioni di Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portale di Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
