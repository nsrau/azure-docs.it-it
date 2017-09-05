---
title: Pubblicare un'app Spring Boot come contenitore Docker usando il Toolkit di Azure per IntelliJ | Microsoft Docs
description: Informazioni su come pubblicare un'app Web in Microsoft Azure come contenitore Docker usando il Toolkit di Azure per IntelliJ.
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
ms.openlocfilehash: 4f9c118fbfe445dd38670e3b7549a0d89681e5db
ms.contentlocale: it-it
ms.lasthandoff: 07/22/2017

---

# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-intellij"></a>Pubblicare un'app Spring Boot come contenitore Docker usando il Toolkit di Azure per IntelliJ

[Spring Framework] è una soluzione open source che consente agli sviluppatori Java di creare applicazioni di livello enterprise. Uno dei progetti più comuni che si basa su questa piattaforma è [Spring Boot], che fornisce un approccio semplificato per la creazione di applicazioni Java autonome.

[Docker] è una soluzione open source che consente agli sviluppatori di automatizzare la distribuzione, il ridimensionamento e la gestione delle applicazioni in esecuzione nei contenitori.

Questa esercitazione illustra i passaggi per distribuire un'applicazione Spring Boot come contenitore Docker in Microsoft Azure usando il Toolkit di Azure per IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repo"></a>Clonare il repository predefinito per Docker Spring Boot

I passaggi seguenti illustrano la clonazione del repository Docker Spring Boot tramite IntelliJ. Se si intende usare una riga di comando, vedere [Deploy a Spring Boot application on Linux in Azure Container Service][Deploy Spring Boot on Linux in ACS] (Distribuire un'applicazione Spring Boot in Linux nel servizio contenitore di Azure).

1. Aprire IntelliJ.

1. Nella schermata iniziale scegliere l'opzione **GitHub** dall'elenco **Check out from version control** (Estrai da controllo della versione).

   ![Opzione GitHub per il controllo della versione][CL01]

1. Immettere le proprie credenziali se viene richiesto di accedere.

   * Se si usano un nome utente e una password per accedere a GitHub:

      ![Finestra di dialogo per l'immissione del nome utente e password GitHub][CL02a]

   * Se si usa un token per l'accesso a GitHub:

      ![Finestra di dialogo per l'immissione di un token GitHub][CL02b]

1. Immettere **https://github.com/spring-guides/gs-spring-boot-docker.git** per l'URL del repository, specificare il percorso locale e le informazioni sulla cartella e quindi fare clic su **Clone** (Clona).

   ![Finestra di dialogo per la clonazione del repository][CL03]

1. Scegliere **No** quando viene richiesto di creare un progetto IntelliJ.

   ![Rifiuto di creare un progetto IntelliJ][CL04]

1. Nella home page fare clic su **Import Project** (Importa progetto).

   ![Selezione del progetto da importare][CL05]

1. Individuare il percorso in cui è stato clonato il repository Spring Boot, evidenziare la cartella **Complete** (Completo) sotto la radice e quindi fare clic su **OK**.

   ![Selezionare una cartella per l'importazione][CL06]

1. Quando richiesto, scegliere **Create project from existing sources** (Crea progetto da origini esistenti).

   ![Opzione per creare un progetto da origini esistenti][CL07]

1. Specificare il nome del progetto o accettare il valore predefinito, verificare il percorso corretto della cartella **Complete** (Completo) e quindi fare clic su **Next** (Avanti).

   ![Specificare il nome del progetto][CL08]

1. Personalizzare le directory per l'importazione e quindi fare clic su **Next** (Avanti).

   ![Scegliere le directory][CL09]

1. Esaminare le librerie da importare e quindi fare clic su **Next** (Avanti).

   ![Esaminare le librerie del progetto][CL10]

1. Esaminare la struttura del modulo e quindi fare clic su **Next** (Avanti).

   ![Esaminare la struttura del modulo][CL11]

1. Specificare il JDK e quindi fare clic su **Next** (Avanti).

   ![Specificare un JDK][CL12]

1. Fare clic su **Finish**.

   ![Pulsante Fine][CL13]

IntelliJ importa l'app Spring Boot come un progetto e visualizza la struttura al termine dell'importazione.

![App Spring Boot in IntelliJ][CL14]

## <a name="build-your-spring-boot-app"></a>Compilare l'app Spring Boot

### <a name="build-the-app-by-using-the-maven-pom"></a>Compilare l'app con Maven POM

1. Se non è già aperta, aprire la finestra degli strumenti di Maven. Fare clic su **Visualizza** > **Finestre degli strumenti** > **Maven Projects** (Progetti Maven).

   ![Comandi Finestre degli strumenti e Maven Projects (Progetti Maven)][BU01]

1. Nella finestra degli strumenti di Maven fare clic con il pulsante destro del mouse su **package** (pacchetto) e scegliere **Run Maven Build** (Esegui compilazione Maven). Se il progetto Maven non viene visualizzato automaticamente, fare clic sull'icona **Reimport** (Reimporta) sulla barra degli strumenti di Maven.

   ![Comando Run Maven Build (Esegui compilazione Maven)][BU02]

1. Una volta creata correttamente l'app Spring Boot, IntelliJ visualizzerà un messaggio **BUILD SUCCESS** (COMPILAZIONE COMPLETATA).

   ![Messaggio BUILD SUCCESS (COMPILAZIONE COMPLETATA)][BU03]

### <a name="create-a-deployment-ready-artifact"></a>Creare un elemento pronto per la distribuzione

Per pubblicare l'app Spring Boot, è necessario creare un elemento pronto per la distribuzione. Seguire questa procedura:

1. Aprire il progetto dell'app Web in IntelliJ.

1. Fare clic su **File** e quindi fare clic su **Project Structure** (Struttura del progetto).

   ![Comando Project Structure (Struttura del progetto)][ART01]

1. Fare clic sul simbolo più di colore verde (**+**) per aggiungere un elemento, fare clic su **JAR** e quindi fare clic su **Empty** (Vuoto).

   ![Aggiungere un elemento][ART02]

1. Assegnare un nome all'elemento assicurandosi di non aggiungere l'estensione ".jar" e quindi specificare la cartella di destinazione per l'output di Maven.

   ![Specificare le proprietà dell'elemento][ART03]

1. Creare un manifesto per l'elemento (facoltativo):

   a. Fare clic su **Create Manifest** (Crea manifesto).

      ![Fare clic sul pulsante Create Manifest (Crea manifesto).][ART04a]

   b. Scegliere il percorso predefinito per l'elemento e quindi fare clic su **OK**.

      ![Specificare il percorso dell'elemento][ART04b]

   c. Fare clic sul pulsante con i puntini di sospensione **...** per individuare la classe principale.

      ![Individuare la classe principale][ART04c]

   d. Scegliere la classe principale e quindi fare clic su **OK**.

      ![Specificare la classe principale][ART04d]

1. Fare clic su **OK**.

   ![Chiudere la finestra di dialogo Project Structure (Struttura progetto)][ART05]

> [!NOTE]
> Per altre informazioni sulla creazione di elementi in IntelliJ, vedere [Configuring Artifacts] (Configurazione di elementi) sul sito Web JetBrains.
>

### <a name="build-the-artifact-for-deployment"></a>Compilare l'elemento per la distribuzione

1. Fare clic su **Build** (Compila) e quindi su **Artifacts** (Elementi).

   ![Comando Build Artifacts (Compila elementi)][BU04]

1. Quando viene visualizzato il menu di scelta rapida **Build Artifact** (Compila elemento), fare clic su **Build** (Compila).

   ![Menu di scelta rapida Build Artifact (Compila elemento)][BU05]

IntelliJ visualizzerà l'elemento completato per l'app Spring Boot nella finestra degli strumenti del progetto.

   ![Elemento creato][BU06]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Pubblicare l'app Web in Azure usando un contenitore Docker

1. Se non è stato eseguito l'accesso all'account Azure, eseguire la procedura descritta in [Istruzioni di accesso per Azure Toolkit for IntelliJ][Azure Sign In for IntelliJ].

1. Nella finestra degli strumenti di Esplora progetti fare clic con il pulsante destro del mouse sul progetto e quindi selezionare **Azure** > **Publish as Docker Container** (Pubblica come contenitore Docker).

   ![Comando Publish as Docker Container (Pubblica come contenitore Docker)][PU01]

1. Quando viene visualizzata la finestra di dialogo **Deploy Docker Container on Azure** (Distribuisci contenitore Docker in Azure), vengono mostrati tutti gli host Docker esistenti. Se si sceglie di eseguire la distribuzione in un host esistente, è possibile procedere al passaggio 4. In alternativa, per creare un host seguire questa procedura:

   a. Fare clic sul simbolo più di colore verde (**+**).

      ![Aggiungere un nuovo host Docker][PU02]

   b. Quando viene visualizzata la finestra di dialogo **Create Docker Host** (Crea host Docker), è possibile scegliere di accettare le impostazioni predefinite oppure specificare impostazioni personalizzate per il nuovo host Docker. Per una descrizione dettagliata delle varie impostazioni vedere [Pubblicare un'app Web come contenitore Docker usando il Toolkit di Azure per IntelliJ][Publish Container with Azure Toolkit]. Dopo aver specificato le impostazioni da usare, fare clic su **Next** (Avanti).

      ![Specificare le opzioni per l'host Docker][PU03a]

   c. È possibile scegliere di usare le credenziali di accesso esistenti da un insieme di credenziali delle chiavi di Azure oppure immettere nuove credenziali di accesso per l'host Docker. Dopo aver specificato le opzioni, fare clic su **Finish** (Fine).

      ![Specificare le credenziali per l'host Docker][PU03b]

1. Selezionare l'host Docker e quindi fare clic su **Next** (Avanti).

   ![Selezionare l'host Docker da usare][PU04]

1. Nell'ultima pagina della finestra di dialogo **Deploy Docker Container on Azure** (Distribuisci contenitore Docker in Azure) specificare le opzioni seguenti:

   a. È possibile scegliere di specificare un nome personalizzato per il contenitore che ospiterà il contenitore Docker oppure accettare il valore predefinito.

   b. Immettere le porte TCP per l'host Docker usando la sintassi seguente: *[porta esterna]*:*[porta interna]*. **80:8080** ad esempio specifica una porta esterna 80 e la porta interna predefinita di Spring Boot 8080.
   
      Se la porta interna è stata personalizzata, ad esempio modificando il file application.yml, è necessario specificare il numero di porta per il corretto funzionamento del routing in Azure.

   c. Dopo aver configurato queste opzioni, fare clic su **Finish** (Fine).

   ![Distribuire un contenitore Docker in Azure][PU05]

1. Al termine della pubblicazione da parte del Toolkit di Azure, nel Log attività di Azure viene indicato lo stato **Pubblicato**.

   ![Distribuzione dell'host Docker completata][PU06]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

Per informazioni su altri metodi per la creazione di app Spring Boot tramite IntelliJ, vedere [Creating Spring Boot Projects](https://www.jetbrains.com/help/idea/creating-spring-boot-projects.html) (Creazione di progetti Spring Boot) nel sito Web JetBrains.

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Sign In for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Configuring Artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html (Configurazione di elementi)
[Deploy Spring Boot on Linux in ACS]:container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL01.png
[CL02a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02a.png
[CL02b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02b.png
[CL03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL09.png
[CL10]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL10.png
[CL11]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL11.png
[CL12]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL12.png
[CL13]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL13.png
[CL14]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL14.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART03.png
[ART04a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04a.png
[ART04b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04b.png
[ART04c]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04c.png
[ART04d]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04d.png
[ART05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART05.png

[BU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU02.png
[BU03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU03.png
[BU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU04.png
[BU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU05.png
[BU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU06.png

[PU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU02.png
[PU03a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03a.png
[PU03b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03b.png
[PU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU06.png

