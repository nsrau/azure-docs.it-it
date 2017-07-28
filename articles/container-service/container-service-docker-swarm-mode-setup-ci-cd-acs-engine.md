---
title: "CI/CD con il motore del servizio contenitore di Azure e la modalità Swarm | Microsoft Docs"
description: "Usare il motore del servizio contenitore di Azure con la modalità Docker Swarm, un Registro contenitori di Azure e Visual Studio Team Services per distribuire un&quot;applicazione .NET Core multi-contenitore in modo continuativo"
services: container-service
documentationcenter: " "
author: diegomrtnzg
manager: esterdnb
tags: acs, azure-container-service, acs-engine
keywords: Docker, contenitori, microservizi, Swarm, Azure, Visual Studio Team Services, DevOps, motore del servizio contenitore di Azure
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 188bb3da595ad829c4bf1159ed7969328d590701
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Pipeline CI/CD completa per distribuire un'applicazione multi-contenitore nel servizio contenitore di Azure con il motore del servizio contenitore di Azure e la modalità Docker Swarm tramite Visual Studio Team Services

*Questo articolo si basa sulla documentazione disponibile in [Full CI/CD pipeline to deploy a multi-container application on Azure Container Service with Docker Swarm using Visual Studio Team Services (Pipeline CI/CD completa per distribuire un'applicazione multi-contenitore nel servizio contenitore di Azure con Docker Swarm tramite Visual Studio Team Services)](https://github.com/Microsoft/azure-docs/blob/master/articles/container-service/container-service-docker-swarm-setup-ci-cd.md)*

Una delle sfide principali quando si sviluppano applicazioni moderne per il cloud è quella di riuscire a distribuire le applicazioni in modo continuativo. Questo articolo illustra come implementare una pipeline completa di integrazione e distribuzione continua (CI/CD) tramite: 
* Motore del servizio contenitore di Azure con la modalità Docker Swarm
* Registro di sistema del contenitore di Azure
* Visual Studio Team Services

Questo articolo si basa su una semplice applicazione, disponibile in [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux) e sviluppata con ASP.NET Core. L'applicazione è composta da quattro servizi diversi: tre API Web e un Web front-end:

![Applicazione MyShop di esempio](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

L'obiettivo è distribuire l'applicazione in modo continuativo in un cluster in modalità Docker Swarm attraverso Visual Studio Team Services. Nell'immagine seguente sono illustrati i dettagli della pipeline di distribuzione continuativa:

![Applicazione MyShop di esempio](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Una breve spiegazione dei passaggi:

1. Viene eseguito il commit delle modifiche al codice nel repository del codice sorgente (in questo caso GitHub) 
2. GitHub attiva una compilazione in Visual Studio Team Services 
3. Visual Studio Team Services recupera la versione più recente delle origini e compila tutte le immagini che compongono l'applicazione 
4. Visual Studio Team Services effettua il push di ogni immagine a un Registro di sistema Docker creato tramite il servizio Registro di sistema del contenitore di Azure 
5. Visual Studio Team Services attiva un nuovo rilascio 
6. Il rilascio esegue alcuni comandi tramite SSH nel nodo principale del cluster del servizio contenitore di Azure 
7. La modalità Docker Swarm nel cluster effettua il pull della versione più recente delle immagini 
8. La nuova versione dell'applicazione viene distribuita con Docker Stack 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario soddisfare i requisiti seguenti:

- [Creare un cluster in modalità Swarm nel servizio contenitore di Azure con il motore del servizio contenitore di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Connettersi a un cluster Swarm nel servizio contenitore di Azure](container-service-connect.md)
- [Creare un Registro di sistema del contenitore di Azure](../container-registry/container-registry-get-started-portal.md)
- [Disporre di un account Visual Studio Team Services e aver creato un progetto di team](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Creare una fork del repository GitHub nell'account GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> L'agente di orchestrazione Docker Swarm nel servizio contenitore di Azure usa Swarm autonomo legacy. La [modalità Swarm](https://docs.docker.com/engine/swarm/) integrata (in Docker 1.12 e versioni successive) attualmente non è un agente di orchestrazione supportato nel servizio contenitore di Azure. Per questo motivo viene usato il [motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), un [modello di avvio rapido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/) realizzato con il contributo della community o una soluzione Docker in [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Passaggio 1: Configurare l'account di Visual Studio Team Services 

In questa sezione viene configurato l'account di Visual Studio Team Services. Per configurare gli endpoint dei servizi VSTS, nel progetto di Visual Studio Team Services fare clic sull'icona **Impostazioni** nella barra degli strumenti e selezionare **Servizi**.

![Aprire l'endpoint dei servizi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Connettere Visual Studio Team Services e l'account Azure

Impostare una connessione tra il progetto VSTS e l'account Azure.

1. A sinistra fare clic su **Nuovo endpoint del servizio** > **Azure Resource Manager**.
2. Per autorizzare VSTS al funzionamento con l'account Azure, selezionare la **Sottoscrizione** e fare clic su **OK**.

    ![Visual Studio Team Services - Autorizzazione di Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Connettere Visual Studio Team Services e GitHub

Impostare una connessione tra il progetto VSTS e l'account GitHub.

1. A sinistra fare clic su **Nuovo endpoint del servizio** > **GitHub**.
2. Per autorizzare VSTS a lavorare con il proprio account GitHub, fare clic su **Autorizza** e attenersi alla procedura nella finestra visualizzata.

    ![Visual Studio Team Services - Autorizzazione GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Connettere VSTS al cluster del servizio contenitore di Azure

Prima di passare alla pipeline CI/CD occorre configurare le connessioni esterne al cluster Docker Swarm in Azure. 

1. Per il cluster Docker Swarm, aggiungere un endpoint di tipo **SSH**. Successivamente, immettere le informazioni di connessione SSH del cluster Swarm (nodo master).

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Tutti i passaggi di configurazione vengono eseguiti ora. Nei passaggi successivi viene creata la pipeline CI/CD che compila e distribuisce l'applicazione al cluster Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Passaggio 2: creare la definizione di compilazione

In questo passaggio viene configurata una definizione di compilazione per il progetto VSTS e viene definito il flusso di lavoro di compilazione per le immagini contenitore

### <a name="initial-definition-setup"></a>Configurazione iniziale della definizione

1. Per creare una definizione di compilazione, connettersi al progetto di Visual Studio Team Services e fare clic su **Build & Release** (Compilazione e rilascio). Nella sezione **Definizione di compilazione** fare clic su **+ New** (+Nuova). 

    ![Visual Studio Team Services - Nuova definizione di compilazione](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selezionare il **processo vuoto**.

    ![Visual Studio Team Services - Nuova definizione di compilazione vuota](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Fare clic sulla scheda **Variabili** e creare due nuove variabili: **RegistryURL** e **AgentURL**. Incollare i valori del registro contenitori e del DNS degli agenti cluster.

    ![Visual Studio Team Services - Configurazione variabili di compilazione](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Nella pagina **Definizioni di compilazione** aprire prima di tutto la scheda **Trigger** e configurare la compilazione in modo che venga usata l'integrazione continua con il fork del progetto MyShop creato nei prerequisiti. Selezionare quindi **Modifiche batch**. Assicurarsi di selezionare *docker-linux* come **Specifica rami**.

    ![Visual Studio Team Services - Configurazione repository di compilazione](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Infine, fare clic sulla scheda **Opzioni** e configurare la coda dell'agente predefinito come **anteprima Linux ospitata**.

    ![Visual Studio Team Services - Configurazione agente host](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definire il flusso di lavoro di compilazione
Nei passaggi successivi viene definito il flusso di lavoro di compilazione. In primo luogo, è necessario configurare l'origine del codice. A tale scopo, selezionare **GitHub**, il **repository** e il **ramo** (docker-linux).

![Visual Studio Team Services - Configurazione origine del codice](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Esistono cinque immagini contenitore per la compilazione dell'applicazione *MyShop*. Ogni immagine viene compilata attraverso il file Docker che si trova nelle cartelle del progetto:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Sono necessari due passaggi di Docker per ogni immagine, uno per compilare l'immagine, l'altro per effettuare il push dell'immagine nel registro contenitori di Azure. 

1. Per aggiungere un passaggio nel flusso di lavoro di compilazione fare clic su **+ Aggiungi istruzione di compilazione** e selezionare **Docker**.

    ![Visual Studio Team Services - Aggiunta passaggi di compilazione](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Per ogni immagine, configurare un passaggio che usi il comando `docker build`.

    ![Visual Studio Team Services - Compilazione Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Per l'operazione di compilazione, selezionare il registro contenitori di Azure, l'azione **Build an image** (Compila un'immagine) e il file Docker che definisce ogni immagine. Impostare la **directory di lavoro** come directory radice di Dockerfile, definire il **nome dell'immagine** e selezionare **Include Latest Tag** (Includi tag più recente).
    
    Il nome dell'immagine deve essere nel formato: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Sostituire **[NAME]** con il nome dell'immagine:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Per ogni immagine, configurare un secondo passaggio che usi il comando `docker push`.

    ![Visual Studio Team Services - Push Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Per l'operazione di push, selezionare il registro contenitori di Azure, l'azione **Push an image** (Push immagine), immettere il **nome dell'immagine** compilato nel passaggio precedente e selezionare **Include Latest Tag** (Includi tag più recente).

4. Dopo aver configurato i passaggi di compilazione e push per ognuna delle cinque immagini, aggiungere altri tre passaggi al flusso di lavoro di compilazione.

   ![Visual Studio Team Services - Aggiunta di attività della riga di comando](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Un'attività della riga di comando che usa uno script bash per sostituire l'occorrenza *RegistryURL* nel file docker-compose.yml con la variabile RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - Aggiornamento file Compose con URL del registro](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Un'attività della riga di comando che usa uno script bash per sostituire l'occorrenza *AgentURL* nel file docker-compose.yml con la variabile AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Un'attività che rimuove il file Compose aggiornato come elemento di compilazione per poterlo usare nel rilascio. Vedere i dettagli nella schermata di seguito.

         ![Visual Studio Team Services - Pubblicazione elemento](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - Pubblicazione file Compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Fare clic su **Salva e accoda** per testare la definizione di compilazione.

   ![Visual Studio Team Services - Salva e accoda](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services - Nuova coda](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Se la **compilazione**  è corretta, viene visualizzata la schermata seguente:

  ![Visual Studio Team Services - Compilazione completata](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Passaggio 3: creare la definizione di versione

Visual Studio Team Services consente di [gestire i rilasci nei vari ambienti](https://www.visualstudio.com/team-services/release-management/). È possibile abilitare la distribuzione continua per assicurarsi che l'applicazione venga distribuita in ambienti diversi (ad esempio sviluppo, test, preproduzione e produzione) in modo uniforme. È possibile creare un ambiente che rappresenti il cluster della modalità Docker Swarm del servizio contenitore di Azure.

![Visual Studio Team Services - Rilascio su ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configurazione iniziale del rilascio

1. Per creare una definizione di rilascio, fare clic su **Rilascio** > **+ Rilascio**

2. Per configurare l'origine dell'elemento, fare clic su **Elementi** > **Collega un'origine elemento**. Da qui collegare la nuova definizione di rilascio alla compilazione definita nel passaggio precedente. Successivamente, il file docker-compose.yml è disponibile nel processo di rilascio.

    ![Visual Studio Team Services - Rilascio elementi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Per configurare il trigger di rilascio, fare clic su **Trigger** e selezionare **Distribuzione continua**. Impostare il trigger sulla stessa origine dell'elemento. Questa impostazione garantisce l'avvio di un nuovo rilascio quando la compilazione viene completata.

    ![Visual Studio Team Services - Rilascio trigger](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Per configurare le variabili di rilascio, fare clic su **Variables** (Variabili) e selezionare **+Variable** (+Variabile) per creare tre nuove variabili con le informazioni del registro: **docker.username**, **docker.password** e **docker.registry**. Incollare i valori del registro contenitori e del DNS degli agenti cluster.

    ![Visual Studio Team Services - Configurazione repository di compilazione](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Come illustrato nella schermata precedente, fare clic sulla casella di controllo **Lock** (Blocco) in docker.password. Questa impostazione è importante per limitare la password.
    >

### <a name="define-the-release-workflow"></a>Definire il flusso di lavoro di rilascio

Il flusso di lavoro di rilascio è composto da due attività che vengono aggiunte.

1. Configurare un'attività per copiare in modo sicuro il file Compose in una cartella di *distribuzione* nel nodo master Docker Swarm, tramite la connessione SSH configurata in precedenza. Vedere i dettagli nella schermata di seguito.
    
    Cartella di origine: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - Rilascio SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configurare una seconda attività in modo che esegua un comando bash per i comandi `docker` e `docker stack deploy` nel nodo principale. Vedere i dettagli nella schermata di seguito.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - Rilascio bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Il comando eseguito nel nodo principale usa l'interfaccia della riga di comando di Docker e Docker-Compose per eseguire queste attività:

    - Accedere al registro contenitori di Azure, con tre variabili di compilazione definite nella scheda **Variabili**
    - Definire la variabile **DOCKER_HOST** in modo che sia compatibile con l'endpoint Swarm (:2375)
    - Accedere alla cartella di *distribuzione* che è stata creata dall'attività di copia sicura precedente e che contiene il file docker-compose.yml 
    - Eseguire comandi `docker stack deploy`, che effettuano il pull di nuove immagini e creano i contenitori.

    >[!IMPORTANT]
    > Come illustrato nella schermata precedente, lasciare deselezionata la casella **Interrompi in caso di STDERR**. Questa impostazione permette di completare il processo di rilascio, perché `docker-compose` stampa diversi messaggi di diagnostica, ad esempio in caso di contenitori arrestati o in fase di eliminazione, nell'output di errore standard. Se si seleziona la casella di controllo, Visual Studio Team Services segnala che si sono verificati errori durante il rilascio, anche se tutto va bene.
    >
3. Salvare la nuova definizione di rilascio.

## <a name="step-4-test-the-cicd-pipeline"></a>Passaggio 4: Testare la pipeline CI/CD

Dopo aver completato la configurazione, è il momento di testare la nuova pipeline CI/CD. Il modo più semplice per eseguire il test consiste nell'aggiornare il codice sorgente ed eseguire il commit delle modifiche nel repository GitHub. Pochi secondi dopo il push del codice, verrà visualizzata una nuova compilazione in esecuzione in Visual Studio Team Services. Dopo il suo completamento, viene attivato un nuovo rilascio e la nuova versione dell'applicazione viene distribuita nel cluster del servizio contenitore di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su CI/CD con Visual Studio Team Services, vedere la [panoramica sulla compilazione di VSTS](https://www.visualstudio.com/docs/build/overview).
* Per altre informazioni sul motore del servizio contenitore di Azure, vedere il [repository di GitHub sul motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine).
* Per altre informazioni sulla modalità Docker Swarm, vedere [Docker Swarm mode overview](https://docs.docker.com/engine/swarm/) (Panoramica della modalità Docker Swarm).

