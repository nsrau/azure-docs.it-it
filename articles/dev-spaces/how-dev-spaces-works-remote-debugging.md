---
title: Come eseguire il debug remoto del codice con Azure Dev Spaces funziona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descrive i processi per il debug remoto nel servizio Azure Kubernetes con Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: fd984ff6a8ebe336f76643406c0957769dbfd3da
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213380"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Come eseguire il debug remoto del codice con Azure Dev Spaces funziona

Azure Dev Spaces offre diversi modi per eseguire rapidamente l'iterazione e il debug di applicazioni Kubernetes e collaborare con il team in un cluster del servizio Azure Kubernetes. Quando il progetto viene eseguito in uno spazio di sviluppo, Azure Dev Spaces fornisce un modo per connettersi ed eseguire il debug di un'applicazione in esecuzione in AKS.

Questo articolo descrive il funzionamento del debug remoto con spazi di sviluppo.

## <a name="debug-your-code"></a>Eseguire il debug del codice

Per le applicazioni Java, .NET Core e Node.js è possibile eseguire il debug dell'applicazione in esecuzione direttamente nello spazio di sviluppo usando Visual Studio Code o Visual Studio. Visual Studio Code e Visual Studio forniscono gli strumenti per connettersi allo spazio di sviluppo, avviare l'applicazione e collegare un debugger. Dopo `azds prep` l'esecuzione di, è possibile aprire il progetto in Visual Studio Code o in Visual Studio. Visual Studio Code o Visual Studio genererà i propri file di configurazione per la connessione, che è separato dall'esecuzione `azds prep` . Dall'interno Visual Studio Code o Visual Studio, è possibile impostare punti di interruzione e avviare l'applicazione nello spazio di sviluppo.

![Debug del codice](media/get-started-node/debug-configuration-nodejs2.png)

Quando si avvia l'applicazione con Visual Studio Code o Visual Studio per il debug, questi gestiscono l'avvio e la connessione allo spazio di sviluppo in modo analogo all'esecuzione di `azds up` . Inoltre, gli strumenti lato client in Visual Studio Code e Visual Studio forniscono ognuno un parametro aggiuntivo con informazioni specifiche per il debug. Il parametro contiene il nome dell'immagine del debugger, il percorso del debugger all'interno dell'immagine del debugger e il percorso di destinazione all'interno del contenitore dell'applicazione per montare la cartella del debugger.

L'immagine del debugger viene determinata automaticamente dagli strumenti lato client. Usa un metodo simile a quello usato durante l'esecuzione di Dockerfile e Helm Chart generate `azds prep` . Quando il debugger viene montato nell'immagine dell'applicazione, viene eseguito usando `azds exec` .

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](how-dev-spaces-works.md)
