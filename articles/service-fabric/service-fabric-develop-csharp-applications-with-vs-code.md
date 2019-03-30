---
title: Sviluppare applicazioni .NET Core di Azure Service Fabric con Visual Studio Code | Microsoft Docs
description: Questo articolo illustra come compilare e distribuire applicazioni .NET Core di Service Fabric ed eseguirne il debug usando Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 680c141e32333c4747ee69919229bd9381f536a4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664243"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Sviluppare applicazioni C# di Service Fabric con Visual Studio Code

L'[estensione Reliable Services di Service Fabric per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) consente di creare con facilità applicazioni .NET Core di Service Fabric su sistemi operativi Windows, Linux e macOS.

Questo articolo illustra come compilare e distribuire un'applicazione .NET Core di Service Fabric ed eseguirne il debug usando Visual Studio Code.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che siano già installati Visual Studio Code, l'estensione Reliable Services di Service Fabric per Visual Studio Code e le dipendenze necessarie per l'ambiente di sviluppo. Per altre informazioni, vedere l'[introduzione](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Scaricare l'esempio
In questo articolo viene usata l'applicazione CounterService inclusa nel [repository GitHub degli esempi di avvio rapido per applicazioni .NET Core di Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Per clonare il repository nel computer di sviluppo, eseguire il comando seguente da una finestra di terminale (finestra di comando in Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Aprire l'applicazione in Visual Studio Code

### <a name="windows"></a>Windows
Fare clic con il pulsante destro del mouse sull'icona di Visual Studio Code nel menu Start e scegliere **Esegui come amministratore**. Per associare il debugger ai servizi, è necessario eseguire Visual Studio Code come amministratore.

### <a name="linux"></a>Linux
Usando il terminale spostarsi nel percorso /service-fabric-dotnet-core-getting-started/Services/CounterService dalla directory locale in cui è stata clonata l'applicazione.

Eseguire il comando seguente per aprire Visual Studio Code come utente root e consentire così l'associazione del debugger ai servizi.
```
sudo code . --user-data-dir='.'
```

L'applicazione dovrebbe ora essere visualizzata nell'area di lavoro di Visual Studio Code.

![Applicazione CounterService nell'area di lavoro](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Compilare l'applicazione
1. In Visual Studio Code premere CTRL+MAIUSC+P per aprire il **riquadro comandi**.
2. Cercare e selezionare il comando **Service Fabric: Build Application** (Service Fabric: Compila applicazione). L'output della compilazione viene inviato al terminale integrato.

   ![Comando per la compilazione dell'applicazione in Visual Studio Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuire l'applicazione nel cluster locale
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale. 

1. Dal **Riquadro comandi** selezionare il comando **Service Fabric: Deploy Application (Localhost)** (Service Fabric: Distribuisci applicazione (localhost)). L'output del processo di installazione viene inviato al terminale integrato.

   ![Comando per la distribuzione dell'applicazione in Visual Studio Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Una volta completata la distribuzione, avviare un browser e aprire Service Fabric Explorer: http:\//localhost:19080 / Explorer. Si noterà che l'applicazione è in esecuzione. Questa operazione potrebbe richiedere tempo. 

   ![Applicazione CounterService in Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Dopo aver verificato l'applicazione è in esecuzione, avviare un browser e aprire questa pagina: http:\//localhost:31002. Questo è il front-end Web dell'applicazione. Aggiornare la pagina per visualizzare il valore corrente del contatore mentre viene incrementato automaticamente.

   ![Applicazione CounterService nel browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Eseguire il debug dell'applicazione
Quando si esegue il debug in Visual Studio Code, l'applicazione deve essere in esecuzione su un cluster locale. È quindi possibile aggiungere punti di interruzione al codice.

Per impostare un punto di interruzione ed eseguire il debug, seguire questa procedura:
1. In Explorer aprire il file */src/CounterServiceApplication/CounterService/CounterService.cs* e impostare un punto di interruzione in corrispondenza della riga 62 all'interno del metodo `RunAsync`.
3. Fare clic sull'icona di debug sulla **barra delle attività** per aprire la visualizzazione del debugger in Visual Studio Code. Fare clic sull'icona a forma di ingranaggio nella parte superiore della visualizzazione del debugger e selezionare **.NET Core** dal menu a discesa degli ambienti. Viene aperto il file launch.json. È possibile chiudere questo file. A questo punto, nel menu di configurazione del debug accanto al pulsante di esecuzione (freccia verde) dovrebbero essere visualizzate le scelte di configurazione.

   ![Icona di debug nell'area di lavoro di Visual Studio Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selezionare **.NET Core Attach** dal menu delle configurazioni di debug.

   ![Icona di debug nell'area di lavoro di Visual Studio Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Aprire Service Fabric Explorer in un browser: http:\//localhost:19080 / Explorer. Fare clic su **Applicazioni** ed eseguire il drill down per determinare il nodo primario in cui è in esecuzione CounterService. Nell'immagine seguente il nodo primario per CounterService è il nodo 0.

   ![Nodo primario per CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. In Visual Studio Code fare clic sull'icona di esecuzione (freccia verde) accanto alla configurazione di debug **.NET Core Attach**. Nella finestra di dialogo di selezione del processo, selezionare il processo di CounterService in esecuzione sul nodo primario identificato nel passaggio 4.

   ![Processo primario](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Il punto di interruzione nel file *CounterService.cs* verrà raggiunto molto rapidamente. È quindi possibile esplorare i valori delle variabili locali. Usare la barra degli strumenti di debug nella parte superiore di Visual Studio Code per procedere al debug, eseguire le istruzioni, entrare nei metodi o uscire dal metodo corrente. 

   ![Debug dei valori](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Per terminare la sessione di debug, scegliere l'icona a forma di spina sulla barra degli strumenti di debug nella parte superiore di Visual Studio Code.
   
   ![Disconnessione dal debugger](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Al termine del debug, è possibile usare il comando **Service Fabric: Remove Application** (Service Fabric: Rimuovi applicazione) per rimuovere l'applicazione CounterService dal cluster locale. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [sviluppare applicazioni Java di Service Fabric ed eseguirne il debug con Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md).



