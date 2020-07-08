---
title: Funzionamento di Visual Studio Code con Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Informazioni su come Visual Studio Code e Azure Dev Spaces consentono di eseguire il debug e di eseguire rapidamente l'iterazione delle applicazioni Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 73a2ae1f603626acf7ec4d42838b301c88ee21da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84307351"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Funzionamento di Visual Studio Code con Azure Dev Spaces

È possibile utilizzare Visual Studio Code e l' [estensione Azure Dev Spaces][azds-extension] per preparare, eseguire ed eseguire il debug dei servizi con Azure Dev Spaces. Con Visual Studio Code e l'estensione Azure Dev Spaces, è possibile:

* Generare asset per l'esecuzione e il debug dei servizi in AKS
* Eseguire i servizi Java, Node.js e .NET Core in uno spazio di sviluppo
* Esegui il debug diretto dei servizi Java, Node.js e .NET Core in esecuzione in uno spazio di sviluppo

## <a name="generate-assets"></a>Genera asset

Visual Studio Code e l'estensione Azure Dev Spaces generano gli asset seguenti per il progetto:

* Dockerfile per applicazioni Java con Maven, applicazioni Node.js e applicazioni .NET Core
* Grafici Helm per quasi tutti i linguaggi con Dockerfile
* Un `azds.yaml` file, ovvero il [file di configurazione Azure Dev Spaces][azds-yaml] per il progetto
* Una `.vscode` cartella con la Visual Studio Code avviare la configurazione del progetto per le applicazioni Java con Maven, le applicazioni Node.js e le applicazioni .NET Core

Il Dockerfile, il grafico Helm e i `azds.yaml` file sono le stesse risorse generate durante l'esecuzione di `azds prep` . Questi file possono essere usati anche all'esterno di Visual Studio Code per eseguire il progetto in AKS, ad esempio in esecuzione `azds up` . La `.vscode` cartella viene usata solo da Visual Studio Code per eseguire il progetto in AKS da Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Eseguire il servizio in AKS

Dopo aver generato gli asset per il progetto, è possibile eseguire i servizi Java, Node.js e .NET Core in uno spazio di sviluppo esistente da Visual Studio Code. Nella pagina *debug* di Visual Studio Code è possibile richiamare la configurazione di avvio dalla `.vscode` Directory per eseguire il progetto.

È necessario creare il cluster AKS e abilitare Azure Dev Spaces nel cluster al di fuori della Visual Studio Code. È possibile riutilizzare Dockerfile, i grafici Helm e `azds.yaml` i file esistenti creati al di fuori di Visual Studio Code, ad esempio gli asset generati eseguendo `azds prep` . Se si riutilizzano risorse generate all'esterno di Visual Studio Code, è comunque necessario disporre di una `.vscode` Directory. Questa `.vscode` Directory può essere rigenerata da Visual Studio Code e dall'estensione Azure Dev Spaces e non sovrascrive gli asset esistenti.

Per i progetti .NET Core, è necessario che sia installata l' [estensione C#][csharp-extension] per eseguire il servizio .net da Visual Studio Code. Inoltre, per i progetti Java con Maven, è necessario che sia installato il [Debugger Java per Azure Dev Spaces estensione][java-extension] [e Maven installato e configurato][maven] per eseguire il servizio Java da Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Eseguire il debug del servizio in AKS

Dopo aver avviato il progetto, è possibile eseguire il debug dei servizi Java, Node.js e .NET Core in esecuzione in uno spazio di sviluppo direttamente da Visual Studio Code. La configurazione di avvio nella `.vscode` Directory fornisce le informazioni di debug aggiuntive per l'esecuzione di un servizio con il debug abilitato in uno spazio di sviluppo. Visual Studio Code inoltre si connette al processo di debug nel contenitore in esecuzione negli spazi di sviluppo, consentendo di impostare i punti di rottura, controllare le variabili ed eseguire altre operazioni di debug.

## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Usare Visual Studio Code con Azure Dev Spaces

È possibile vedere Visual Studio Code e l'estensione Azure Dev Spaces uso di Azure Dev Spaces nelle guide introduttive seguenti:

* [Iterazione e debug rapidi con Visual Studio Code e Java][quickstart-java]
* [Iterazione e debug rapidi con Visual Studio Code e .NET][quickstart-netcore]
* [Iterazione e debug rapide con Visual Studio Code e Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
