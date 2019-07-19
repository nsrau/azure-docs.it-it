---
title: Funzionamento di Visual Studio Code con Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Funzionamento di Visual Studio Code con Azure Dev Spaces
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297860"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Funzionamento di Visual Studio Code con Azure Dev Spaces

È possibile utilizzare Visual Studio Code e l' [estensione Azure Dev Spaces][azds-extension] per preparare, eseguire ed eseguire il debug dei servizi con Azure Dev Spaces. Con Visual Studio Code e l'estensione Azure Dev Spaces, è possibile:

* Generare asset per l'esecuzione e il debug dei servizi in AKS
* Eseguire i servizi Java, node. js e .NET Core in uno spazio di sviluppo
* Esegui il debug diretto dei servizi Java, node. js e .NET Core in esecuzione in uno spazio di sviluppo

## <a name="generate-assets"></a>Genera asset

Visual Studio Code e l'estensione Azure Dev Spaces generano gli asset seguenti per il progetto:

* Dockerfile per applicazioni Java con Maven, applicazioni node. js e applicazioni .NET Core
* Grafici Helm per quasi tutti i linguaggi con Dockerfile
* Un `azds.yaml` file, ovvero il [file di configurazione Azure Dev Spaces][azds-yaml] per il progetto
* Una `.vscode` cartella con la Visual Studio Code avviare la configurazione del progetto per le applicazioni Java con Maven, le applicazioni node. js e le applicazioni .NET Core

Il Dockerfile, il grafico Helm e `azds.yaml` i file sono le stesse risorse generate `azds prep`durante l'esecuzione di. Questi file possono essere usati anche all'esterno di Visual Studio Code per eseguire il progetto in AKS, ad esempio `azds up`in esecuzione. La `.vscode` cartella viene usata solo da Visual Studio Code per eseguire il progetto in AKS da Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Eseguire il servizio in AKS

Dopo aver generato gli asset per il progetto, è possibile eseguire i servizi Java, node. js e .NET Core in uno spazio di sviluppo esistente da Visual Studio Code. Nella pagina *debug* di Visual Studio Code è possibile richiamare la configurazione di avvio dalla `.vscode` directory per eseguire il progetto.

È necessario creare il cluster AKS e abilitare Azure Dev Spaces nel cluster al di fuori della Visual Studio Code. Ad esempio, è possibile usare l'interfaccia della riga di comando di Azure o il portale di Azure per eseguire questa configurazione. È possibile riutilizzare dockerfile, i grafici Helm e `azds.yaml` i file esistenti creati al di fuori di Visual Studio Code, ad esempio gli `azds prep`asset generati eseguendo. Se si riutilizzano risorse generate all'esterno di Visual Studio Code, è comunque necessario `.vscode` disporre di una directory. Questa `.vscode` directory può essere rigenerata da Visual Studio Code e dall'estensione Azure Dev Spaces e non sovrascrive gli asset esistenti.

Per i progetti .NET Core, è necessario avere installato l' [ C# estensione][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] , nonché [Maven installato e configurato][Maven] per eseguire il servizio Java da Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Eseguire il debug del servizio in AKS

Dopo aver avviato il progetto, è possibile eseguire il debug dei servizi Java, node. js e .NET Core in esecuzione in uno spazio di sviluppo direttamente da Visual Studio Code. La configurazione di avvio nella `.vscode` Directory fornisce le informazioni di debug aggiuntive per l'esecuzione di un servizio con il debug abilitato in uno spazio di sviluppo. Visual Studio Code inoltre si connette al processo di debug nel contenitore in esecuzione negli spazi di sviluppo, consentendo di impostare i punti di rottura, controllare le variabili ed eseguire altre operazioni di debug.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Usare Visual Studio Code con Azure Dev Spaces

È possibile vedere Visual Studio Code e l'estensione Azure Dev Spaces uso di Azure Dev Spaces nelle guide introduttive seguenti:

* [Sviluppare con Java][quickstart-java]
* [Sviluppare con .NET][quickstart-netcore]
* [Sviluppare con node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
