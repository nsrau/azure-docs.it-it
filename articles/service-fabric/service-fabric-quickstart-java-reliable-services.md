---
title: "Guida introduttiva: Creare un'app Java in Azure Service Fabric"
description: In questa guida introduttiva viene creata un'applicazione Java per Azure usando un'applicazione Reliable Services Service Fabric di esempio.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ca6a1063f6ddd5c42d0d08f43b87a3387cc46a14
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859261"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Guida introduttiva:  Distribuire un'app Java in Azure Service Fabric in Linux

Questa guida di avvio rapido illustra come distribuire la prima applicazione Java in Azure Service Fabric tramite l'IDE Eclipse in un computer di sviluppo Linux. Al termine, sarà disponibile un'applicazione di voto con un front-end Web Java che salva i risultati delle votazioni in un servizio back-end con stato nel cluster.

Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori.

![Screenshot dell'applicazione](./media/service-fabric-quickstart-java/votingapp.png)

In questa guida introduttiva si apprende come:

* Usare Eclipse come strumento per le applicazioni Java di Service Fabric
* Distribuire l'applicazione nel cluster locale
* Scalare orizzontalmente l'applicazione in più nodi

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

1. [Installare Service Fabric SDK e l'interfaccia della riga di comando di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installare Git](https://git-scm.com/)
3. [Installare Eclipse](https://www.eclipse.org/downloads/)
4. [Configurare l'ambiente Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), assicurandosi di seguire i passaggi facoltativi per installare il plug-in Eclipse

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale

1. Avviare il cluster locale eseguendo il comando seguente:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    L'avvio del cluster locale può richiedere tempo. Per verificare se il cluster è in esecuzione, accedere a Service Fabric Explorer in **http://localhost:19080** . I cinque nodi integri indicano che il cluster locale è in esecuzione.

    ![Cluster locale integro](./media/service-fabric-quickstart-java/localclusterup.png)

2. Aprire Eclipse.
3. Selezionare **File** > **Import** (Importa)  > **Gradle** > **Existing Gradle Project** (Progetto Gradle esistente) e seguire la procedura guidata.
4. Selezionare **Directory** e scegliere la directory `Voting` nella cartella `service-fabric-java-quickstart` clonata da GitHub. Selezionare **Fine**.

    ![Finestra di dialogo Import (Importa) di Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)

5. A questo punto, il progetto `Voting` si trova nella finestra Package Explorer (Esplora pacchetti) di Eclipse.
6. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Publish Application** (Pubblica applicazione) nell'elenco a discesa **Service Fabric**. Scegliere **PublishProfiles/Local.json** come profilo target e selezionare **Publish** (Pubblica).

    ![Finestra di dialogo Publish (Pubblica) locale](./media/service-fabric-quickstart-java/localjson.png)

7. Aprire il Web browser preferito e accedere all'applicazione tramite `http://localhost:8080`.

    ![Front-end locale dell'applicazione](./media/service-fabric-quickstart-java/runninglocally.png)

È ora possibile aggiungere un set di opzioni per le votazioni e iniziare a raccogliere i voti. L'applicazione viene eseguita e archivia tutti i dati nel cluster di Service Fabric, senza che sia necessario un database separato.

## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster

I servizi possono essere facilmente ridimensionati in un cluster per supportare le modifiche del carico sui servizi. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster. Sono disponibili diversi sistemi per garantire il ridimensionamento dei servizi. È ad esempio possibile usare gli script o i comandi dell'interfaccia della riga di comando di Service Fabric (sfctl). I passaggi seguenti usano Service Fabric Explorer.

Service Fabric Explorer è in esecuzione in tutti i cluster di Service Fabric ed è accessibile da un browser passando alla porta di gestione HTTP (19080) del cluster, ad esempio `http://localhost:19080`.

Per ridimensionare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `https://localhost:19080`.
2. Selezionare i puntini di sospensione ( **...** ) accanto al nodo **fabric:/Voting/VotingWeb** nella visualizzazione ad albero e selezionare **Scale Service** (Ridimensiona servizio).

    ![Ridimensionamento dei servizi in Service Fabric Explorer](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Ora è possibile scegliere di modificare il numero di istanze del servizio front-end Web.

3. Impostare il numero su **2** e selezionare **Scale Service** (Ridimensiona servizio).
4. Selezionare il nodo **fabric:/Voting/VotingWeb** nella visualizzazione ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Ridimensionamento dei servizi in Service Fabric Explorer completato](./media/service-fabric-quickstart-java/servicescaled.png)

    È ora possibile vedere che il servizio dispone di due istanze. Nella visualizzazione ad albero viene indicato su quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione, sono state raddoppiate le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

* Usare Eclipse come strumento per le applicazioni Java di Service Fabric
* Distribuire le applicazioni Java nel cluster locale
* Scalare orizzontalmente l'applicazione in più nodi

Per altre informazioni sull'uso di app Java in Service Fabric, continuare con l'esercitazione sulle app Java.

> [!div class="nextstepaction"]
> [Distribuire un'app Java](./service-fabric-tutorial-create-java-app.md)
