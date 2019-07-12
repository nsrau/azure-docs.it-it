---
title: Funzionamento di Visual Studio Code con spazi di sviluppo di Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Funzionamento di Visual Studio Code con spazi di sviluppo di Azure
keywords: Azure Dev spazi Dev, Docker, Kubernetes, Azure, servizio contenitore di AZURE, Azure Kubernetes Service, i contenitori
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712150"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Funzionamento di Visual Studio Code con spazi di sviluppo di Azure

È possibile usare Visual Studio Code e il [estensione Azure Dev spazi][azds-extension] a tale scopo, eseguire e il debug dei servizi con spazi di sviluppo di Azure. Con Visual Studio Code e l'estensione Azure Dev spazi, è possibile:

* Generare gli asset per l'esecuzione e debug dei servizi nel servizio contenitore di AZURE
* Eseguire i servizi Java, Node. js e .NET Core in un'area di sviluppo
* Eseguire direttamente il debug di servizi Java, Node. js e .NET Core in esecuzione in uno spazio di sviluppo

## <a name="generate-assets"></a>Generare gli asset

Visual Studio Code e l'estensione Azure Dev spazi generare gli asset seguenti per il progetto:

* Dockerfile per applicazioni Java con Maven, le applicazioni Node. js e le applicazioni .NET Core
* Grafici Helm per quasi tutte le lingue con un Dockerfile
* Un' `azds.yaml` file, ovvero il [file di configurazione di spazi di sviluppo Azure][azds-yaml] per il progetto
* Oggetto `.vscode` cartella con la configurazione di avvio di Visual Studio Code del progetto per applicazioni Java con Maven, le applicazioni Node. js e le applicazioni .NET Core

Il Dockerfile, il grafico Helm, e `azds.yaml` i file sono le stesse risorse generate durante l'esecuzione `azds prep`. Tali file possono essere utilizzati anche all'esterno di Visual Studio code per eseguire il progetto nel servizio contenitore di AZURE, ad esempio che esegue `azds up`. Il `.vscode` cartella viene usata solo da Visual Studio code per eseguire il progetto nel servizio contenitore di AZURE da Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Eseguire il servizio nel servizio contenitore di AZURE

Dopo aver generato l'asset per il progetto, è possibile eseguire i servizi Java, Node. js e .NET Core in uno spazio di sviluppo esistente da Visual Studio Code. Nel *Debug* pagina di Visual Studio Code, è possibile richiamare la configurazione di avvio dal `.vscode` directory per eseguire il progetto.

È necessario creare il cluster AKS e abilitare spazi di sviluppo di Azure nel cluster all'esterno di Visual Studio Code. Ad esempio, è possibile utilizzare il comando di Azure o il portale di Azure per eseguire il programma di installazione. È possibile riutilizzare i Dockerfile esistenti, i grafici Helm, e `azds.yaml` i file creati all'esterno di Visual Studio Code, ad esempio l'asset generati eseguendo `azds prep`. Se si riutilizza asset generati all'esterno di Visual Studio Code, è necessario disporre una `.vscode` directory. Ciò `.vscode` directory può essere rigenerato da Visual Studio code e l'estensione Azure Dev spazi e non sovrascrive gli asset esistenti.

Per i progetti .NET Core, è necessario disporre di [ C# estensione][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] installati, nonché [Maven installato e configurato][maven] eseguire Java servizio da Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Eseguire il debug del servizio nel servizio contenitore di AZURE

Dopo avere avviato il progetto, è possibile eseguire il debug di servizi Java, Node. js e .NET Core in esecuzione in uno spazio di sviluppo direttamente da Visual Studio Code. La configurazione di avvio nel `.vscode` directory fornisce le informazioni di debug aggiuntive per l'esecuzione di un servizio con il debug abilitato in uno spazio di sviluppo. Visual Studio Code associa inoltre per il processo di debug nel contenitore in esecuzione gli spazi di sviluppo, consentendo di impostare punti di interruzione, controllare variabili ed eseguire altre operazioni di debug.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Usare Visual Studio Code con spazi di sviluppo di Azure

È possibile visualizzare Visual Studio Code e l'estensione di spazi di sviluppo di Azure funziona con spazi di sviluppo di Azure nelle guide introduttive seguenti:

* [Sviluppare con Java][quickstart-java]
* [Sviluppare con .NET][quickstart-netcore]
* [Sviluppare con Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md