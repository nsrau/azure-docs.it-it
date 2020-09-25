---
title: 'Esercitazione: Usare IntelliJ per distribuire applicazioni Azure Spring Cloud'
description: Usare IntelliJ per distribuire applicazioni in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: e5b8dc325fa7eaceaa0274029049f546db5b6995
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888536"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Usare IntelliJ per distribuire applicazioni Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java

Il plug-in IntelliJ per Azure Spring Cloud supporta la distribuzione di applicazioni da IntelliJ IDEA.  

Prima di eseguire questo esempio, è possibile provare la [guida di avvio rapido di base](spring-cloud-quickstart.md).

## <a name="prerequisites"></a>Prerequisiti
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable&preserve-view=true)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA, Community/Ultimate Edition, versione 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Installare il plug-in
È possibile aggiungere Azure Toolkit for IntelliJ IDEA 3.35.0 dall'interfaccia utente **Plugins** (Plug-in) di IntelliJ.

1. Avviare IntelliJ.  Se è stato aperto un progetto in precedenza, chiudere il progetto per visualizzare la finestra di dialogo di benvenuto. Selezionare **Configure** (Configura) dal collegamento in basso a destra e quindi fare clic su **Plugins** (Plug-in) per aprire la finestra di dialogo di configurazione del plug-in e selezionare **Install Plugins from disk** (Installa plug-in da disco).

    ![Selezionare Configure (Configura)](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Cercare Azure Toolkit for IntelliJ.  Fare clic su **Installa**.

    ![Installare il plug-in](media/spring-cloud-intellij-howto/install-plugin.png)

1. Fare clic su **Restart IDE** (Riavvia IDE).

## <a name="tutorial-procedures"></a>Procedure dell'esercitazione
Le procedure seguenti consentono di distribuire un'applicazione Hello World usando IntelliJ IDEA.

* Aprire il progetto gs-spring-boot
* Distribuire in Azure Spring Cloud
* Visualizzare i log in streaming

## <a name="open-gs-spring-boot-project"></a>Aprire il progetto gs-spring-boot

1. Scaricare e decomprimere il repository di origine per questa esercitazione oppure clonarlo con Git: git clone https://github.com/spring-guides/gs-spring-boot.git 
1. Passare alla directory gs-spring-boot\complete.
1. Aprire la finestra di dialogo di **benvenuto** di IntelliJ, selezionare **Import Project** (Importa progetto) per aprire l'importazione guidata.
1. Selezionare la cartella `gs-spring-boot\complete`.

    ![Importare il progetto](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Distribuire in Azure Spring Cloud
Per eseguire la distribuzione in Azure, è necessario accedere con l'account Azure e scegliere la sottoscrizione.  Per informazioni dettagliate sull'accesso, vedere [Installazione e accesso](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Fare clic con il pulsante destro del mouse sul progetto nella finestra di esplorazione progetti IntelliJ, quindi scegliere **Azure** -> **Deploy to Azure Spring Cloud** (Distribuisci in Azure Spring Cloud).

    ![Distribuzione in Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Accettare il nome dell'app nel campo **Name** (Nome). Il **nome** fa riferimento alla configurazione, non al nome dell'app. Non è in genere necessario modificarlo.
1. Accettare l'identificatore del progetto per **Artifact** (Artefatto).
1. Selezionare **App:** e quindi fare clic su **Create app...** (Crea app).

    ![Distribuzione in Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Immettere il nome dell'app in **App name** e quindi fare clic su **OK**.

    ![Distribuzione in Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Avviare la distribuzione facendo clic sul pulsante **Run** (Esegui). 

    ![Distribuzione in Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Il plug-in eseguirà il comando `mvn package` nel progetto e quindi creerà la nuova app e distribuirà il file jar generato dal comando `package`.

1. Se l'URL dell'app non viene visualizzato nella finestra di output, ottenerlo dal portale di Azure. Passare dal gruppo di risorse all'istanza di Azure Spring Cloud.  Fare quindi clic su **Apps** (App).  Verrà visualizzata l'app in esecuzione.

    ![Ottenere l'URL di test](media/spring-cloud-intellij-howto/get-test-url.png)

1. Passare all'URL in un browser.

    ![Navigare nel browser 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Visualizzare i log in streaming
Per ottenere i log:
1. Selezionare **Azure Explorer**, quindi **Spring Cloud**.
1. Fare clic con il pulsante destro del mouse sull'app in esecuzione.
1. Selezionare **Streaming Logs** (Log in streaming) dall'elenco a discesa.

    ![Selezionare log in streaming](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selezione l'istanza.

    ![Selezionare l'istanza](media/spring-cloud-intellij-howto/select-instance.png)

1. Il log in streaming sarà visibile nella finestra output.

    ![Output del log in streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Passaggi successivi
* [Preparare l'applicazione Spring per Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Altre informazioni su Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
