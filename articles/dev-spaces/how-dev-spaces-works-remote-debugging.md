---
title: Funzionamento del debug remoto del codice con Azure Dev SpacesHow remote debugging your code with Azure Dev Spaces works
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi per il debug remoto nel servizio Azure Kubernetes con Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241400"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Funzionamento del debug remoto del codice con Azure Dev SpacesHow remote debugging your code with Azure Dev Spaces works

Azure Dev Spaces offre diversi modi per scorrere rapidamente ed eseguire il debug delle applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes (AKS). Dopo che il progetto è in esecuzione in uno spazio di sviluppo, Azure Dev Spaces consente di connettersi ed eseguire il debug di un'applicazione in esecuzione in AKS.

In questo articolo viene descritto il funzionamento del debug remoto con Dev Spaces.This article describes how remote debugging with Dev Spaces works.

## <a name="debug-your-code"></a>Eseguire il debug del codice

Per le applicazioni Java, .NET Core e Node.js, è possibile eseguire il debug dell'applicazione in esecuzione direttamente nello spazio di sviluppo usando Visual Studio Code o Visual Studio. Visual Studio Code e Visual Studio forniscono strumenti per connettersi allo spazio di sviluppo, avviare l'applicazione e connettere un debugger. Dopo `azds prep`l'esecuzione , è possibile aprire il progetto in Visual Studio Code o Visual Studio. Visual Studio Code o Visual Studio genereranno i propri `azds prep`file di configurazione per la connessione che è separato dall'esecuzione . Dal codice di Visual Studio o Visual Studio, è possibile impostare punti di interruzione e avviare l'applicazione nello spazio di sviluppo.

![Debug del codice](media/get-started-node/debug-configuration-nodejs2.png)

Quando si avvia l'applicazione utilizzando Visual Studio Code o Visual Studio per il debug, `azds up`vengono gestiti l'avvio e la connessione allo spazio di sviluppo nello stesso modo in cui vengono eseguiti . Inoltre, gli strumenti lato client in Visual Studio Code e Visual Studio forniscono ciascuno un parametro aggiuntivo con informazioni specifiche per il debug. Il parametro contiene il nome dell'immagine del debugger, il percorso del debugger all'interno dell'immagine del debugger e il percorso di destinazione all'interno del contenitore dell'applicazione per montare la cartella del debugger.

L'immagine del debugger viene determinata automaticamente dagli strumenti sul lato client. Viene utilizzato un metodo simile a quello utilizzato durante dockerfile e Helm chart generare durante l'esecuzione `azds prep`di . Dopo che il debugger è stato montato nell'immagine dell'applicazione, viene eseguito utilizzando `azds exec`.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Dev Spaces per il debug remoto del progetto, vedere le guide introduttive seguenti:To get started using Azure Dev Spaces to for remote debugging your project, see the following quickstarts:

* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e Java][quickstart-java]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio Code e .NET][quickstart-netcore]
* [Eseguire rapidamente l'iterazione e il debug con Visual Studio Code e Node.js][quickstart-node]
* [Iterazione ed eseguire rapidamente il debug con Visual Studio e .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
