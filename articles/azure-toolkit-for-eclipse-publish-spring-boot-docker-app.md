---
title: Pubblicare un'app Spring Boot come contenitore Docker usando il Toolkit di Azure per Eclipse | Microsoft Docs
description: Informazioni su come pubblicare un'app Web in Microsoft Azure come contenitore Docker usando il Toolkit di Azure per Eclipse.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: a4d596367506d2ea983f24ab1f53e58fa180cf52
ms.contentlocale: it-it
ms.lasthandoff: 07/22/2017

---

# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse"></a>Pubblicare un'app Spring Boot come contenitore Docker usando il Toolkit di Azure per Eclipse

[Spring Framework] è una soluzione open source che consente agli sviluppatori Java di creare applicazioni di livello enterprise. Uno dei progetti più comuni che si basa su questa piattaforma è [Spring Boot], che fornisce un approccio semplificato per la creazione di applicazioni Java autonome.

[Docker] è una soluzione open source che consente agli sviluppatori di automatizzare la distribuzione, il ridimensionamento e la gestione delle applicazioni in esecuzione nei contenitori.

Questa esercitazione illustra i passaggi per distribuire un'applicazione Spring Boot come contenitore Docker in Microsoft Azure usando il Toolkit di Azure per Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repository"></a>Clonare il repository Docker Spring Boot

### <a name="import-the-public-repository"></a>Importare il repository pubblico

I passaggi seguenti illustrano la clonazione del repository Docker Spring Boot nel computer locale tramite IntelliJ. Se si intende usare una riga di comando, vedere [Deploy a Spring Boot application on Linux in Azure Container Service][Deploy Spring Boot on Linux in ACS] (Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure).

1. Aprire Eclipse.

1. Fare clic su **File** > **Import** (Importa).

   ![Opzione Import (Importa) del menu File][CL01]

1. Quando viene visualizzata la finestra di dialogo **Import** (Importa):

   a. Espandere **Git**.

   b. Selezionare **Projects from Git** (Progetti da Git).
   
   c. Fare clic su **Avanti**.

   ![Finestra di dialogo Import (Importa)][CL02]

1. Nella pagina  **Select Repository Source** (Seleziona origine repository):

   a. Selezionare **Clone URI** (Clona URI).
   
   b. Fare clic su **Avanti**.

   ![Selezionare la pagina origine del repository][CL03]

1. Nella pagina **Select Repository Source** (Seleziona origine repository):

   a. Per **URI**, immettere `https://github.com/spring-guides/gs-spring-boot-docker.git`. Questo passaggio inserisce automaticamente i valori corretti nei campi **Host** e **Repository path** (Percorso repository).
   
   b. Il repository Spring Boot è pubblico, quindi non è necessario immettere il nome utente e la password Git.
   
   c. Fare clic su **Avanti**.

   ![Pagina del repository Git di origine][CL04]

1. Nella pagina **Branch selection** (Selezione ramo) fare clic su **Next** (Avanti).

   ![Pagina Branch Selection (Selezione ramo)][CL05]

1. Nella pagina **Local Destination** (Destinazione locale):

   a. Specificare la cartella locale per il repository locale.
   
   b. Fare clic su **Avanti**.

   ![Pagina Local Destination (Destinazione locale)][CL06]

1. Nella pagina **Select a wizard to use for importing projects (Selezionare una procedura guidata per l'importazione di progetti)**:

   a. Selezionare **Import as a general project** (Importa come progetto generale).
   
   b. Fare clic su **Avanti**.

   ![Pagina Select a wizard to use for importing projects (Selezionare una procedura guidata per l'importazione di progetti)][CL07]

1. Nella pagina **Import Projects** (Importa progetti):

   a. Specificare il nome del progetto.
   
   b. Fare clic su **Finish**.

   ![Pagina Import Projects (Importa progetti)][CL08]

1. Dopo aver clonato il repository, tutti i file vengono elencati in Eclipse.

   ![Repository locale][CL09]

### <a name="create-a-maven-project-from-your-local-repository"></a>Creare un progetto Maven dal repository locale

Il repository Docker Spring Boot contiene un progetto Maven completato che verrà usato per questa esercitazione. 

1. Fare clic su **File** > **Import** (Importa).

   ![Importare i comandi del menu File][CL01]

1. Quando viene visualizzata la finestra di dialogo **Import** (Importa):

   a. Espandere **Maven**.
   
   b. Selezionare **Existing Maven Projects** (Progetti Maven esistenti).
   
   c. Fare clic su **Avanti**.

   ![Finestra di dialogo Import (Importa)][MV01]

1. Nella pagina **Maven Projects** (Progetti Maven):

   a. Per **Root Directory** (Directory radice) specificare la cartella **complete** (completo) nel repository locale.
   
   b. Espandere la sezione **Advanced** (Avanzate) e immettere un nome personalizzato per **Name template** (Modello nome).
   
   c. Selezionare la casella per il file **pom.xml** nel progetto.
   
   d. Fare clic su **Finish**.

   ![Pagina Maven Projects (Progetti Maven)][MV02]

1. Dopo aver aperto correttamente il progetto Maven, viene visualizzato un secondo progetto in Eclipse.

   ![Progetto Maven locale][MV03]

## <a name="build-your-spring-boot-app-by-using-maven"></a>Compilazione dell'app Spring Boot mediante Maven

1. In Project Explorer di Eclipse selezionare il progetto Maven.

1. Fare clic su **Run** > **Run As** > **Maven build** (Esegui > Esegui come > Compilazione Maven)

   ![Comandi per esecuzione come compilazione Maven][BU01]

1. Dopo la compilazione corretta dell'applicazione, viene elencato lo stato nella finestra della console.

   ![Completamento della compilazione Maven][BU02]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Pubblicare l'app Web in Azure usando un contenitore Docker

1. In Project Explorer di Eclipse selezionare il progetto Maven.

1. Fare clic sul menu **Publish** (Pubblica) di Azure e quindi su **Publish as Docker container** (Pubblica come contenitore Docker).

   ![Comando Publish as Docker Container (Pubblica come contenitore Docker)][PU01]

1. Quando viene visualizzata la finestra di dialogo **Deploy Docker Container on Azure** (Distribuisci contenitore Docker in Azure):

   a. Immettere il nome di un'immagine Docker personalizzata.
   
   b. Per **Artifact to deploy** (Elemento da distribuire), specificare il percorso del file **gs-spring-boot-docker-0.1.0.jar** appena compilato.

   ![Specificare le opzioni per Docker][PU02]

   Verranno visualizzati tutti gli host Docker esistenti. 

1. Se si sceglie di eseguire la distribuzione in un host esistente, è possibile procedere al passaggio 5. In alternativa, per creare un host seguire questa procedura:

   a. Fare clic su **Aggiungi**.

      ![Aggiungere un nuovo host Docker][PU03]

   b. Quando viene visualizzata la finestra di dialogo **Create Docker Host** (Crea host Docker), è possibile scegliere di accettare le impostazioni predefinite oppure specificare impostazioni personalizzate per il nuovo host Docker. Per una descrizione dettagliata delle varie impostazioni vedere [Pubblicare un'app Web come contenitore Docker usando il Toolkit di Azure per IntelliJ][Publish Container with Azure Toolkit]. Dopo aver specificato le impostazioni da usare, fare clic su **Next** (Avanti).

      ![Specificare le opzioni per l'host Docker][PU04]

   c. È possibile scegliere di usare le credenziali di accesso esistenti da un insieme di credenziali delle chiavi di Azure oppure immettere nuove credenziali di accesso per l'host Docker. Dopo aver specificato le opzioni, fare clic su **Finish** (Fine).

      ![Specificare le credenziali per l'host Docker][PU05]

1. Selezionare l'host Docker e quindi fare clic su **Next** (Avanti).

   ![Selezionare l'host Docker da usare][PU06]

1. Nell'ultima pagina della finestra di dialogo **Deploying Docker Container on Azure** (Distribuzione contenitore Docker in Azure), specificare le opzioni seguenti:

   a. È possibile scegliere di specificare un nome personalizzato per il contenitore che ospiterà il contenitore Docker oppure accettare il valore predefinito.

   b. Immettere le porte TCP per l'host Docker usando la sintassi seguente: *[porta esterna]*:*[porta interna]*. **80:8080** ad esempio specifica una porta esterna 80 e la porta interna predefinita di Spring Boot 8080.
   
      Se la porta interna è stata personalizzata, ad esempio modificando il file application.yml, è necessario specificare il numero di porta per il corretto funzionamento del routing in Azure.

   c. Dopo aver configurato queste opzioni, fare clic su **Finish** (Fine).

   ![Distribuire un contenitore Docker in Azure][PU07]

1. Al termine della pubblicazione da parte del Toolkit di Azure, nel Log attività di Azure viene indicato lo stato **Pubblicato**.

   ![Distribuzione dell'host Docker completata][PU08]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]:container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

