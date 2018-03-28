---
title: Eseguire il debug di un'applicazione Java nel cluster di Azure Service Fabric locale | Microsoft Docs
description: In questa esercitazione si apprenderà come eseguire il debug e ottenere log da un'applicazione Java di Service Fabric in esecuzione in un cluster locale.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 7e06048da4db121136bbbb7cd155532f86015da1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
#  <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Esercitazione: Eseguire il debug di un'applicazione Java distribuita in un cluster di Azure Service Fabric locale 
Questa è la seconda di una serie di esercitazioni. Si apprenderà come collegare un debugger remoto usando Eclipse per l'applicazione Service Fabric. Si apprenderà anche come reindirizzare i log dalle applicazioni in esecuzione a un percorso pratico per lo sviluppatore.

Nella seconda parte della serie si apprenderà come:
> [!div class="checklist"]
> * Eseguire il debug di un'applicazione Java con Eclipse
> * Reindirizzare i log a un percorso configurabile

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> *  [Compilare un'applicazione Reliable Services di Service Fabric per Java](service-fabric-tutorial-create-java-app.md)
> * Distribuire l'applicazione ed eseguirne il debug in un cluster locale
> * [Distribuire l'applicazione in un cluster di Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-java-elk.md)
> * [Configurare CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Configurare l'ambiente di sviluppo per [Mac](service-fabric-get-started-mac.md) o [Linux](service-fabric-get-started-linux.md). Seguire le istruzioni per installare il plug-in Eclipse, Gradle, Service Fabric SDK e l'interfaccia della riga di comando di Service Fabric (sfctl).

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio
Se l'applicazione di voto di esempio non è stata compilata nella [parte 1 di questa serie di esercitazioni](service-fabric-tutorial-create-java-app.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Compilare e distribuire](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) l'applicazione nel cluster di sviluppo locale.

## <a name="debug-java-application-using-eclipse"></a>Eseguire il debug di un'applicazione Java con Eclipse

1. Aprire l'IDE di Eclipse nel computer e fare clic su **File -> Import**  (Importa)

2. Nella finestra popup selezionare l'opzione **General -> Existing Projects into Workspace (Generale -> Progetti esistenti nell'area di lavoro)** e fare clic su Next (Avanti). 

3. Nella finestra Import Projects (Importa progetti) scegliere l'opzione **Select root directory** (Seleziona directory radice) e selezionare la directory **Voting**. Se è stata eseguita la prima serie di esercitazioni, la directory **Voting** si trova nella directory **Eclipse-workspace**. 

4. Aggiornare l'elemento entryPoint.sh del servizio di cui eseguire il debug in modo che avvii il processo Java con i parametri di debug remoto. Per questa esercitazione viene usato il front-end senza stato *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*. In questo esempio la porta 8001 è impostata per il debug.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Aggiornare il manifesto dell'applicazione impostando il numero di istanze o di repliche per il servizio di cui eseguire il debug su uno. Questa impostazione evita che si verifichino conflitti per la porta usata per il debug. Per i servizi senza stato, ad esempio, impostare ``InstanceCount="1"``, mentre per i servizi con stato impostare la destinazione e le dimensioni minime del set di repliche su 1 nel modo seguente: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. Nell'IDE di Eclipse selezionare **Run (Esegui) -> Debug Configurations (Configurazioni di debug) -> Remote Java Application (Applicazione Java remota)**, fare clic sul pulsante **New** (Nuovo), impostare le proprietà come segue e fare clic su **Appy** (Applica).

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Inserire un punto di interruzione nella riga 109 del file *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

8. In Package Explorer (Esplora pacchetti) fare clic con il pulsante destro del mouse sul progetto **Voting** e scegliere **Service Fabric -> Publish Application** (Pubblica applicazione) 

9. Nella finestra **Publish Application** (Pubblica applicazione) selezionare **Local.json** dall'elenco a discesa e fare clic su **Publish** (Pubblica).

10. Nell'IDE di Eclipse selezionare **Run (Esegui) -> Debug Configurations (Configurazioni di debug) -> Remote Java Application (Applicazione Java remota)**, fare clic sulla configurazione **Voting** creata e quindi su **Debug**.

11. Passare al Web browser e accedere a **localhost: 8080** per raggiungere il punto di interruzione e accedere alla **prospettiva di debug** in Eclipse.

## <a name="redirect-application-logs-to-custom-location"></a>Reindirizzare i log applicazioni a un percorso personalizzato

La procedura seguente illustra come reindirizzare i log applicazioni dal percorso */var/log/syslog* predefinito a un percorso personalizzato. 

1. Le applicazioni in esecuzione nei cluster Linux di Service Fabric supportano attualmente la selezione di un singolo file di log. I log vengono quindi sempre indirizzati a */tmp/mysfapp0.0.log*. Creare un file denominato logging.properties nel percorso *Voting/VotingApplication/VotingWebPkg/Code/logging.properties* e aggiungere il contenuto seguente.

    ```
    handlers = java.util.logging.FileHandler
    
    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
    
    # This value specifies your custom location. You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Aggiungere il parametro seguente nel file *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* per il comando di esecuzione Java:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```
    
    L'esempio seguente riporta un'esecuzione di esempio:
    
    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

In questa fase si è appreso come eseguire il debug e l'accesso ai log applicazioni durante lo sviluppo di applicazioni Java di Service Fabric. 

## <a name="next-steps"></a>Passaggi successivi
In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Eseguire il debug di un'applicazione Java con Eclipse
> * Reindirizzare i log a un percorso configurabile

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Distribuire l'applicazione in Azure](service-fabric-tutorial-java-deploy-azure.md)