---
title: Introduzione alle Funzioni di Azure
description: Primi passaggi per usare Funzioni di Azure.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975009"
---
# <a name="getting-started-with-azure-functions"></a>Introduzione alle Funzioni di Azure

## <a name="introduction"></a>Introduzione

La soluzione [Funzioni di Azure](./functions-overview.md) consente di implementare logica di sistema personalizzata in blocchi di codice immediatamente disponibili. Questi blocchi di codice si chiamano "funzioni".

Per iniziare, usare le risorse seguenti.

::: zone pivot="programming-language-csharp"

| Azione | Risorse |
| --- | --- |
| **Creare la prima funzione** | Usando uno degli strumenti seguenti:<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Riga di comando](./create-first-function-cli-csharp.md) |
| **Vedere una funzione in esecuzione** | <li>[Browser di esempi di Azure](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Libreria della community di Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Esplorare un'esercitazione interattiva**| <li>[Scegliere la tecnologia serverless di Azure migliore per lo scenario aziendale](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework - Efficienza delle prestazioni](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Eseguire una funzione di Azure con trigger](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Per l'[elenco completo di esercitazioni interattive](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions), vedere Microsoft Learn.|
| **Approfondimenti** | <li>Vedere come le funzioni [aumentano o diminuiscono automaticamente](./functions-scale.md) le istanze in base alla domanda<li>Esplorare i diversi [metodi di distribuzione](./functions-deployment-technologies.md) disponibili<li>Usare gli [strumenti di monitoraggio](./functions-monitoring.md) predefiniti per analizzare le funzioni<li>Leggere le [informazioni di riferimento sul linguaggio C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Azione | Risorse |
| --- | --- |
| **Creare la prima funzione** | Usando uno degli strumenti seguenti:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Funzione Java/Maven con terminale/prompt dei comandi](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Vedere una funzione in esecuzione** | <li>[Browser di esempi di Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Libreria della community di Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Esplorare un'esercitazione interattiva**| <li>[Scegliere la tecnologia serverless di Azure migliore per lo scenario aziendale](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework - Efficienza delle prestazioni](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Sviluppare un'app usando il plug-in Maven per Funzioni di Azure](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Per l'[elenco completo di esercitazioni interattive](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions), vedere Microsoft Learn.|
| **Approfondimenti** | <li>Vedere come le funzioni [aumentano o diminuiscono automaticamente](./functions-scale.md) le istanze in base alla domanda<li>Esplorare i diversi [metodi di distribuzione](./functions-deployment-technologies.md) disponibili<li>Usare gli [strumenti di monitoraggio](./functions-monitoring.md) predefiniti per analizzare le funzioni<li>Leggere le [informazioni di riferimento sul linguaggio Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Azione | Risorse |
| --- | --- |
| **Creare la prima funzione** | Usando uno degli strumenti seguenti:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Terminale/prompt dei comandi di Node.js](./create-first-function-cli-java.md) |
| **Vedere una funzione in esecuzione** | <li>[Browser di esempi di Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Libreria della community di Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Esplorare un'esercitazione interattiva** | <li>[Scegliere la tecnologia serverless di Azure migliore per lo scenario aziendale](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework - Efficienza delle prestazioni](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Creare API serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Creare logica serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Effettuare il refactoring di API Node.js ed Express in API serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Per l'[elenco completo di esercitazioni interattive](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions), vedere Microsoft Learn.|
| **Approfondimenti** | <li>Vedere come le funzioni [aumentano o diminuiscono automaticamente](./functions-scale.md) le istanze in base alla domanda<li>Esplorare i diversi [metodi di distribuzione](./functions-deployment-technologies.md) disponibili<li>Usare gli [strumenti di monitoraggio](./functions-monitoring.md) predefiniti per analizzare le funzioni<li>Leggere le informazioni di riferimento sul linguaggio [JavaScript](./functions-reference-node.md) o [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Azione | Risorse |
| --- | --- |
| **Creare la prima funzione** | <li>Usando [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Vedere una funzione in esecuzione** | <li>[Browser di esempi di Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Libreria della community di Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Esplorare un'esercitazione interattiva** | <li>[Scegliere la tecnologia serverless di Azure migliore per lo scenario aziendale](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework - Efficienza delle prestazioni](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Creare API serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Creare logica serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Eseguire una funzione di Azure con trigger](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Per l'[elenco completo di esercitazioni interattive](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions), vedere Microsoft Learn.|
| **Approfondimenti** | <li>Vedere come le funzioni [aumentano o diminuiscono automaticamente](./functions-scale.md) le istanze in base alla domanda<li>Esplorare i diversi [metodi di distribuzione](./functions-deployment-technologies.md) disponibili<li>Usare gli [strumenti di monitoraggio](./functions-monitoring.md) predefiniti per analizzare le funzioni<li>Leggere le [informazioni di riferimento sul linguaggio PowerShell](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| Azione | Risorse |
| --- | --- |
| **Creare la prima funzione** | Usando uno degli strumenti seguenti:<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Terminale/prompt dei comandi](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Vedere una funzione in esecuzione** | <li>[Browser di esempi di Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Libreria della community di Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Esplorare un'esercitazione interattiva** | <li>[Scegliere la tecnologia serverless di Azure migliore per lo scenario aziendale](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework - Efficienza delle prestazioni](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Creare API serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Creare logica serverless con Funzioni di Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Per l'[elenco completo di esercitazioni interattive](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions), vedere Microsoft Learn.|
| **Approfondimenti** | <li>Vedere come le funzioni [aumentano o diminuiscono automaticamente](./functions-scale.md) le istanze in base alla domanda<li>Esplorare i diversi [metodi di distribuzione](./functions-deployment-technologies.md) disponibili<li>Usare gli [strumenti di monitoraggio](./functions-monitoring.md) predefiniti per analizzare le funzioni<li>Leggere le [informazioni di riferimento sul linguaggio Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'anatomia di un'applicazione di Funzioni di Azure](./functions-reference.md)
