---
title: Funzionamento del codice di Visual Studio con gli spazi di sviluppo di AzureHow Visual Studio Code works with Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Scopri come Visual Studio Code e Azure Dev Spaces ti aiutano a eseguire il debug e a scorrere rapidamente le tue applicazioni Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240453"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Funzionamento del codice di Visual Studio con gli spazi di sviluppo di AzureHow Visual Studio Code works with Azure Dev Spaces

È possibile usare il codice di Visual Studio e [l'estensione Spazi di sviluppo di Azure][azds-extension] per preparare, eseguire ed eseguire il debug dei servizi con Azure Dev Spaces.You can use Visual Studio Code and the Azure Dev Spaces extension to prepare, run, and debug your services with Azure Dev Spaces. Con Visual Studio Code e l'estensione Azure Dev Spaces, è possibile:With Visual Studio Code and the Azure Dev Spaces extension, you can:

* Generare risorse per l'esecuzione e il debug di servizi in AKSGenerate assets for running and debugging services in AKS
* Eseguire i servizi Java, Node.js e .NET Core in uno spazio di sviluppoRun your Java, Node.js, and .NET Core services in a dev space
* Eseguire direttamente il debug dei servizi Java, Node.js e .NET Core in esecuzione in uno spazio di sviluppoDirectly debug your Java, Node.js, and .NET Core services running in a dev space

## <a name="generate-assets"></a>Generare risorse

Il codice di Visual Studio e l'estensione Azure Dev Spaces generano le risorse seguenti per il progetto:Visual Studio Code and the Azure Dev Spaces extension generate the following assets for your project:

* Dockerfiles per applicazioni Java che utilizzano applicazioni Maven, Node.js e .NET Core
* Grafici Helm per quasi tutte le lingue con un Dockerfile
* Un `azds.yaml` file, ovvero il file di configurazione di [Azure Dev Spaces][azds-yaml] per il progetto
* Una `.vscode` cartella con la configurazione di avvio del codice di Visual Studio del progetto per le applicazioni Java che utilizzano le applicazioni Maven, Node.js e .NET Core

Il Dockerfile, il `azds.yaml` grafico Helm e i `azds prep`file sono gli stessi asset generati durante l'esecuzione di . Tali file possono essere utilizzati anche all'esterno del codice di `azds up`Visual Studio per eseguire il progetto in AKS, ad esempio l'esecuzione di . La `.vscode` cartella viene utilizzata solo dal codice di Visual Studio per eseguire il progetto in AKS dal codice di Visual Studio.The folder is only used by Visual Studio code to run your project in AKS from Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Eseguire il servizio in AKSRun your service in AKS

Dopo aver generato le risorse per il progetto, è possibile eseguire i servizi Java, Node.js e .NET Core in uno spazio di sviluppo esistente da Visual Studio Code. Nella pagina *Debug* di Visual Studio Code è possibile `.vscode` richiamare la configurazione di avvio dalla directory per eseguire il progetto.

È necessario creare il cluster AKS e abilitare Azure Dev Spaces nel cluster all'esterno del codice di Visual Studio.You must create your AKS cluster and enable Azure Dev Spaces in your cluster outside of Visual Studio Code. Ad esempio, è possibile usare l'interfaccia della riga di comando di Azure o il portale di Azure per eseguire questa configurazione. È possibile riutilizzare i file Docker, Helm e `azds.yaml` i file creati all'esterno `azds prep`del codice di Visual Studio, ad esempio gli asset generati eseguendo . Se si riutilizzano gli asset generati all'esterno di `.vscode` Visual Studio Code, è comunque necessario disporre di una directory. Questa `.vscode` directory può essere rigenerata dal codice di Visual Studio e dall'estensione Azure Dev Spaces e non sovrascriverà gli asset esistenti.

Per i progetti .NET Core, è necessario che sia installata [l'estensione di C,][csharp-extension] per eseguire il servizio .NET da Visual Studio Code. Anche per i progetti Java che usano Maven, è necessario che [l'estensione Java Debugger for Azure Dev Spaces][java-extension] sia per l'installazione e la configurazione di [Maven][maven] per l'esecuzione del servizio Java da Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Debug your service in AKS

Dopo aver avviato il progetto, è possibile eseguire il debug dei servizi Java, Node.js e .NET Core in esecuzione in uno spazio di sviluppo direttamente dal codice di Visual Studio.After you launch your project, you can debug your Java, Node.js, and .NET Core services running in a dev space directly from Visual Studio Code. La configurazione di `.vscode` avvio nella directory fornisce informazioni di debug aggiuntive per l'esecuzione di un servizio con il debug abilitato in uno spazio di sviluppo. Visual Studio Code si connette anche al processo di debug nel contenitore in esecuzione negli spazi di sviluppo, consentendo di impostare punti di interruzione, controllare le variabili ed eseguire altre operazioni di debug.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Usare il codice di Visual Studio con gli spazi di sviluppo di AzureUse Visual Studio Code with Azure Dev Spaces

È possibile visualizzare Visual Studio Code e l'estensione Azure Dev Spaces che funzionacon azure Dev Spaces nelle guide introduttive seguenti:You can see Visual Studio Code and the Azure Dev Spaces extension working with Azure Dev Spaces in the following quickstarts:

* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e Java][quickstart-java]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e .NET][quickstart-netcore]
* [Eseguire rapidamente l'iterazione e il debug con Visual Studio Code e Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
