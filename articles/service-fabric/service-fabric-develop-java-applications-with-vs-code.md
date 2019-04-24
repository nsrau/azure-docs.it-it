---
title: Sviluppare applicazioni Java di Azure Service Fabric con Visual Studio Code | Microsoft Docs
description: Questo articolo illustra come compilare e distribuire applicazioni Java di Service Fabric ed eseguirne il debug usando Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 7f60371fb533526ef5bdb154d0c08dface9c0d1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393953"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Sviluppare applicazioni Java di Service Fabric con Visual Studio Code

L'[estensione Reliable Services di Service Fabric per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) consente di creare con facilità applicazioni Java di Service Fabric su sistemi operativi Windows, Linux e macOS.

Questo articolo illustra come compilare e distribuire un'applicazione Java di Service Fabric ed eseguirne il debug usando Visual Studio Code.

> [!IMPORTANT]
> Le applicazioni Java di Service Fabric possono essere sviluppate su computer Windows, ma possono essere distribuite solo in cluster Linux di Azure. Il debug di applicazioni Java non è supportato in Windows.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che siano già installati Visual Studio Code, l'estensione Reliable Services di Service Fabric per Visual Studio Code e le dipendenze necessarie per l'ambiente di sviluppo. Per altre informazioni, vedere l'[introduzione](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Scaricare l'esempio
In questo articolo viene usata l'applicazione Voting inclusa nel [repository GitHub degli esempi di avvio rapido per applicazioni Java di Service Fabric](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Per clonare il repository nel computer di sviluppo, eseguire il comando seguente da una finestra di terminale (finestra di comando in Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Aprire l'applicazione in Visual Studio Code

Aprire Visual Studio Code.  Fare clic sull'icona di esplorazione sulla **barra delle attività** e quindi su **Open Folder** (Apri cartella) o **File -> Open Folder** (File -> Apri cartella). Passare alla directory *./service-fabric-java-quickstart/Voting* nella cartella in cui è stato clonato il repository e quindi fare clic su **OK**. L'area di lavoro dovrebbe contenere gli stessi file illustrati nello screenshot seguente.

![Applicazione Java Voting nell'area di lavoro](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Compilare l'applicazione.

1. In Visual Studio Code premere CTRL+MAIUSC+P per aprire il **riquadro comandi**.
2. Cercare e selezionare il comando **Service Fabric: Build Application** (Service Fabric: Compila applicazione). L'output della compilazione viene inviato al terminale integrato.

   ![Comando per la compilazione dell'applicazione in Visual Studio Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuire l'applicazione nel cluster locale
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale. 

> [!IMPORTANT]
> La distribuzione di applicazioni Java nel cluster locale non è supportata nei computer Windows.

1. Dal **Riquadro comandi** selezionare il comando **Service Fabric: Deploy Application (Localhost)** (Service Fabric: Distribuisci applicazione (localhost)). L'output del processo di installazione viene inviato al terminale integrato.

   ![Comando per la distribuzione dell'applicazione in Visual Studio Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Al termine della distribuzione, avviare un browser e aprire Service Fabric Explorer: `http://localhost:19080/Explorer`. Si noterà che l'applicazione è in esecuzione. Questa operazione potrebbe richiedere tempo. 

   ![Applicazione Voting in Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Dopo aver verificato se l'applicazione è in esecuzione, avviare un browser e aprire questa pagina: `http://localhost:8080`. Questo è il front-end Web dell'applicazione. È possibile aggiungere elementi e fare clic su di essi per votare.

   ![Applicazione Voting nel browser](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Per rimuovere l'applicazione dal cluster, selezionare il **Service Fabric: Rimuovi applicazione** comando il **riquadro comandi**. L'output del processo di disinstallazione viene inviato al terminale integrato. È possibile usare Service Fabric Explorer per verificare che l'applicazione sia stata rimossa dal cluster locale.

## <a name="debug-the-application"></a>Eseguire il debug dell'applicazione
Quando si esegue il debug in Visual Studio Code, l'applicazione deve essere in esecuzione su un cluster locale. È quindi possibile aggiungere punti di interruzione al codice.

> [!IMPORTANT]
> Il debug di applicazioni Java non è supportato nei computer Windows.

Per preparare il pacchetto VotingDataService e l'applicazione Voting per il debug, seguire questa procedura:

1. Aggiornare il file *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh*.
Impostare come commento il comando sulla riga 6 usando il simbolo di cancelletto (#) e aggiungere il comando seguente alla fine del file:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aggiornare il file *Voting/VotingApplication/ApplicationManifest.xml*. Impostare su "1" gli attributi **MinReplicaSetSize** e **TargetReplicaSetSize** nell'elemento **StatefulService**:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Fare clic sull'icona di debug sulla **barra delle attività** per aprire la visualizzazione del debugger in Visual Studio Code. Fare clic sull'icona a forma di ingranaggio nella parte superiore della visualizzazione del debugger e selezionare **Java** dal menu a discesa degli ambienti. Viene aperto il file launch.json. 

   ![Icona di debug nell'area di lavoro di Visual Studio Code](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. Nel file launch.json impostare il valore della porta nella configurazione denominata **Debug (Attach)** su **8001**. Salvare il file.

   ![Configurazione di debug per launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Distribuire l'applicazione nel cluster locale tramite la **Service Fabric: Distribuire l'applicazione (Localhost)** comando. Verificare se l'applicazione è in esecuzione in Service Fabric Explorer. L'applicazione è ora pronta per il debug.

Per impostare un punto di interruzione, seguire questa procedura:

1. In Explorer aprire il file */Voting/VotingDataService/src/statefulservice/VotingDataService.java*. Impostare un punto di interruzione sulla prima riga di codice nel blocco `try` del metodo `addItem` (riga 80).
   
   ![Impostare un punto di interruzione in VotingDataService](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Assicurarsi di impostare i punti di interruzione sulle righe eseguibili del codice. Ad esempio, i punti di interruzione impostati su dichiarazioni di metodo, istruzioni `try` o istruzioni `catch` non vengono rilevati dal debugger.
2. Per avviare il debug, scegliere l'icona di debug sulla **barra delle attività**, selezionare la configurazione **Debug (Attach)** dal menu del debug e fare clic sul pulsante di esecuzione (freccia verde).

   ![Configurazione Debug (Attach)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. In un Web browser passare a `http://localhost:8080`. Digitare un nuovo elemento nella casella di testo e fare clic su **+ Aggiungi**. Verrà raggiunto il punto di interruzione. È possibile usare la barra degli strumenti di debug nella parte superiore di Visual Studio Code per procedere al debug, eseguire le istruzioni, entrare nei metodi o uscire dal metodo corrente. 
   
   ![Punto di interruzione raggiunto](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Per terminare la sessione di debug, scegliere l'icona a forma di spina sulla barra degli strumenti di debug nella parte superiore di Visual Studio Code.
   
   ![Disconnessione dal debugger](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Al termine del debug, è possibile usare il comando **Service Fabric: Rimuovi applicazione** comando per rimuovere l'applicazione di voto nel cluster locale. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [sviluppare applicazioni C# di Service Fabric ed eseguirne il debug con Visual Studio Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
