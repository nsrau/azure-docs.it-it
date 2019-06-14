---
title: (DEPRECATO) CI/CD con il servizio Azure Container e Swarm
description: Usare il servizio Azure Container con Docker Swarm, un'istanza di Registro Azure Container e Azure DevOps per distribuire un'applicazione .NET Core multi-contenitore in modo continuativo
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: f28ea3dd2837a241c538057bd118409d4f5b858a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60643768"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(DEPRECATO) Pipeline CI/CD completa per distribuire un'applicazione multi-contenitore nel servizio Azure Container con Docker Swarm usando Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Una delle sfide principali quando si sviluppano applicazioni moderne per il cloud è quella di riuscire a distribuire le applicazioni in modo continuativo. In questo articolo viene descritto come implementare una pipeline completa di distribuzione e di integrazione continua (CI/CD) tramite il servizio Azure Container con Docker Swarm, Registro Azure Container e la gestione Azure Pipelines.

Questo articolo si basa su una semplice applicazione, disponibile in [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) e sviluppata con ASP.NET Core. L'applicazione è composta da quattro servizi diversi: tre API Web e un Web front-end:

![Applicazione MyShop di esempio](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

L'obiettivo è distribuire l'applicazione in modo continuativo in un cluster Docker Swarm, tramite Azure DevOps Services. Nell'immagine seguente sono illustrati i dettagli della pipeline di distribuzione continuativa:

![Applicazione MyShop di esempio](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Una breve spiegazione dei passaggi:

1. Viene eseguito il commit delle modifiche al codice nel repository del codice sorgente (in questo caso GitHub) 
1. GitHub attiva una compilazione in Azure DevOps Services 
1. Ottenere la versione più recente delle origini e compilare tutte le immagini che compongono l'applicazione di servizi di Azure DevOps 
1. Servizi di Azure DevOps effettua il push di ogni immagine in un registro Docker creato con il servizio Registro Azure Container 
1. Azure DevOps Services attiva un nuovo rilascio 
1. Il rilascio esegue alcuni comandi tramite SSH nel nodo principale del cluster del servizio Azure Container 
1. Docker Swarm nel cluster effettua il pull della versione più recente delle immagini 
1. La nuova versione dell'applicazione viene distribuita mediante Docker Compose 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario soddisfare i requisiti seguenti:

- [Creare un cluster Swarm nel servizio Azure Container](container-service-deployment.md)
- [Connettersi a un cluster Swarm nel servizio Azure Container](../container-service-connect.md)
- [Creare un registro contenitori di Azure](../../container-registry/container-registry-get-started-portal.md)
- [Disporre di un'organizzazione di Azure DevOps Services e di un progetto creato](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Creare una fork del repository GitHub nell'account GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

È inoltre necessario disporre di un computer Ubuntu (14.04 o 16.04) con Docker. Il computer viene usato da servizi Azure DevOps Services durante i processi Azure Pipelines. Un modo per creare questo computer consiste nell'usare l'immagine disponibile in [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Passaggio 1: Configurare l'organizzazione di Azure DevOps Services 

In questa sezione si configura l'organizzazione di Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configurare un agente di compilazione Linux di Azure DevOps Services

Per creare immagini Docker ed effettuare il push in Registro Azure Container da una compilazione di Azure DevOps Services, è necessario registrare un agente Linux. Sono disponibili queste opzioni di installazione:

* [Distribuire un agente in Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Usare Docker per eseguire l'agente di Azure DevOps Services](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Installare l'estensione Integrazione di Docker in Azure DevOps Services

Microsoft fornisce un'estensione di Azure DevOps Services per lavorare con Docker nei processi di Azure Pipelines. Questa estensione è disponibile nel [Marketplace di Azure DevOps Services](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Fare clic su **Installa** per aggiungere questa estensione per l'organizzazione di Azure DevOps Services:

![Installare Docker Integration](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Viene chiesto di connettersi all'organizzazione di Azure DevOps Services con le credenziali. 

### <a name="connect-azure-devops-services-and-github"></a>Connettere Azure DevOps Services e GitHub

Configurare una connessione tra il progetto di Azure DevOps Services e l'account GitHub.

1. Nel progetto di Azure DevOps Services, fare clic sull'icona **Impostazioni** nella barra degli strumenti e selezionare **Servizi**.

    ![Azure DevOps Services - Connessione esterna](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. A sinistra fare clic su **Nuovo endpoint del servizio** > **GitHub**.

    ![Azure DevOps Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Per autorizzare Azure DevOps Services a lavorare con il proprio account GitHub, fare clic su **Autorizza** e seguire la procedura nella finestra visualizzata.

    ![Azure DevOps Services - Autorizzare GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Connettere Azure DevOps Services al Registro Azure Container e a un cluster del servizio Azure Container

Gli ultimi passaggi prima di approfondire la pipeline CI/CD consistono nel configurare le connessioni esterne nel registro contenitori e nel cluster Docker Swarm in Azure. 

1. Nelle impostazioni **Servizi** del progetto di Azure DevOps Services, aggiungere un endpoint di servizio di tipo **registro Docker**. 

1. Nel popup che viene visualizzato immettere l'URL e le credenziali del registro contenitori di Azure.

    ![Azure DevOps Services - Registro Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Per il cluster Docker Swarm, aggiungere un endpoint di tipo **SSH**. Successivamente, immettere le informazioni di connessione SSH del cluster Swarm.

    ![Azure DevOps Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Tutti i passaggi di configurazione vengono eseguiti ora. Nei passaggi successivi viene creata la pipeline CI/CD che compila e distribuisce l'applicazione al cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passaggio 2: Creare la pipeline di compilazione

In questo passaggio, viene impostata una pipeline di compilazione per il progetto di Azure DevOps Services e viene definito il flusso di lavoro di compilazione per le immagini contenitore

### <a name="initial-pipeline-setup"></a>Configurazione iniziale della pipeline

1. Per creare una pipeline di compilazione, connettersi al progetto di Azure DevOps Services e fare clic su **Compila e rilascia**. 

1. Nella sezione **Definizione di compilazione** fare clic su **+ New** (+Nuova). Selezionare il modello **Vuoto**.

    ![Azure DevOps - Nuova pipeline di compilazione](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configurare la nuova compilazione con un'origine di repository GitHub, selezionare **Integrazione continua**, quindi scegliere la coda dell'agente in cui è registrato l'agente Linux. Fare clic su **Crea** per creare la pipeline di compilazione.

    ![Azure DevOps Services - Creazione della pipeline di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Nella pagina **Definizioni di compilazione** innanzitutto aprire la scheda **Repository** e configurare la compilazione in modo che usi la fork del progetto MyShop creata nei prerequisiti. Assicurarsi di selezionare *acs-docs* come **ramo predefinito**.

    ![Azure DevOps Services - Configurazione del Repository di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Nella scheda **Trigger** configurare la compilazione in modo che venga attivata dopo ciascun commit. Selezionare **Integrazione continua** e **Modifiche bacth**.

    ![Azure DevOps Services - Configurazione del trigger di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definire il flusso di lavoro di compilazione
Nei passaggi successivi viene definito il flusso di lavoro di compilazione. Esistono cinque immagini contenitore per la compilazione dell'applicazione *MyShop*. Ogni immagine viene compilata attraverso il file Docker che si trova nelle cartelle del progetto:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

È necessario aggiungere due passaggi di Docker per ogni immagine, uno per compilare l'immagine, l'altro per effettuare il push dell'immagine nel registro contenitori di Azure. 

1. Per aggiungere un passaggio nel flusso di lavoro di compilazione fare clic su **+ Aggiungi istruzione di compilazione** e selezionare **Docker**.

    ![Azure DevOps Services - Aggiunta di passaggi di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Per ogni immagine, configurare un passaggio che usi il comando `docker build`.

    ![Azure DevOps Services - Registro Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Per l'operazione di compilazione, selezionare il registro contenitori di Azure, l'azione **Build an image** (Compila un'immagine) e il file Docker che definisce ogni immagine. Impostare **Build context** (Contesto compilazione) come directory radice del file Docker e definire **Nome immagine**. 
    
    Come mostrato nella schermata precedente, il nome dell'immagine deve iniziare con l'URI del registro contenitori di Azure. È anche possibile usare una variabile di compilazione per assegnare parametri al tag dell'immagine, in questo esempio l'identificatore di compilazione.

1. Per ogni immagine, configurare un secondo passaggio che usi il comando `docker push`.

    ![Azure DevOps Services - Effettuazione del push di Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Per l'operazione di push, selezionare il registro contenitori di Azure, l'azione **Push an image** (Push immagine) e immettere il **nome dell'immagine** compilato nel passaggio precedente.

1. Dopo aver configurato i passaggi di compilazione e push per ognuna delle cinque immagini, aggiungere altri due passaggi nel flusso di lavoro di compilazione.

    a. Un'attività della riga di comando che usi uno script bash per sostituire l'occorrenza *BuildNumber* nel file docker-compose.yml con l'ID di compilazione corrente. Vedere i dettagli nella schermata di seguito.

    ![Azure DevOps Services- Aggiornamento del file Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Un'attività che rimuove il file Compose aggiornato come elemento di compilazione per poterlo usare nel rilascio. Vedere i dettagli nella schermata di seguito.

    ![Azure DevOps Services - Pubblicazione del file Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Fare clic su **Salva** e assegnare un nome alla pipeline di compilazione.

## <a name="step-3-create-the-release-pipeline"></a>Passaggio 3: Creare la pipeline di versione

Servizi di Azure DevOps consente di [gestire le versioni in vari ambienti](https://www.visualstudio.com/team-services/release-management/). È possibile abilitare la distribuzione continua per assicurarsi che l'applicazione venga distribuita in ambienti diversi (ad esempio sviluppo, test, preproduzione e produzione) in modo uniforme. È possibile creare un nuovo ambiente che rappresenti il cluster Docker Swarm del servizio Azure Container.

![Azure DevOps Services - Rilascio su ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configurazione iniziale del rilascio

1. Per creare una pipeline di versione, fare clic su **Rilascio** >  **+ Rilascio**

1. Per configurare l'origine dell'elemento, fare clic su **Elementi** > **Collega un'origine elemento**. In questo caso, è possibile collegare questa nuova pipeline di versione alla compilazione definita nel passaggio precedente. In questo modo, il file docker-compose.yml è disponibile nel processo di rilascio.

    ![Azure DevOps Services - Rilascio di artefatti](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Per configurare il trigger di rilascio, fare clic su **Trigger** e selezionare **Distribuzione continua**. Impostare il trigger sulla stessa origine dell'elemento. Questa impostazione garantisce che un nuovo rilascio venga avviato non appena la compilazione viene completata correttamente.

    ![Azure DevOps Services - Rilascio dei trigger](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definire il flusso di lavoro di rilascio

Il flusso di lavoro di rilascio è composto da due attività che vengono aggiunte.

1. Configurare un'attività per copiare in modo sicuro il file Compose in una cartella di *distribuzione* nel nodo master Docker Swarm, tramite la connessione SSH configurata in precedenza. Vedere i dettagli nella schermata di seguito.

    ![Servizi di Azure DevOps - Rilascio SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configurare una seconda attività in modo che esegua un comando bash per i comandi `docker` e `docker-compose` nel nodo principale. Vedere i dettagli nella schermata di seguito.

    ![Azure DevOps Services- Rilascio di Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Il comando eseguito nel nodo principale usa l'interfaccia della riga di comando di Docker e Docker-Compose per eseguire le attività seguenti:

   - Accedere al registro contenitori di Azure (usa tre variabili di compilazione definite nella scheda **Variabili**)
   - Definire la variabile **DOCKER_HOST** in modo che sia compatibile con l'endpoint Swarm (:2375)
   - Accedere alla cartella di *distribuzione* che è stata creata dall'attività di copia sicura precedente e che contiene il file docker-compose.yml 
   - Eseguire i comandi `docker-compose` che effettuano il pull di nuove immagini, interrompono e rimuovono i servizi e creano i contenitori.

     >[!IMPORTANT]
     > Come illustrato nella schermata precedente, lasciare deselezionata la casella **Interrompi in caso di STDERR**. Questa è un'impostazione importante, perché `docker-compose` stampa diversi messaggi di diagnostica, ad esempio in caso di contenitori arrestati o in fase di eliminazione, nell'output di errore standard. Se si seleziona la casella di controllo, Azure DevOps Services segnala che si sono verificati errori durante il rilascio, anche se tutto va bene.
     >
1. Salvare la nuova pipeline di versione.


>[!NOTE]
>Questa distribuzione include tempi di inattività perché vengono interrotti i servizi precedenti per eseguire quello nuovo. È possibile evitare il problema effettuando una distribuzione di tipo blu-verde.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passaggio 4. Testare la pipeline CI/CD

Dopo aver completato la configurazione, è il momento di testare la nuova pipeline CI/CD. Il modo più semplice per eseguire il test consiste nell'aggiornare il codice sorgente ed eseguire il commit delle modifiche nel repository GitHub. Pochi secondi dopo aver effettuato il push del codice, verrà visualizzata una nuova compilazione in esecuzione in Azure DevOps Services. Dopo il suo completamento, verrà attivato un nuovo rilascio, che distribuirà la nuova versione dell'applicazione nel cluster del servizio Azure Container.

## <a name="next-steps"></a>Fasi successive

* Per altre informazioni su CI/CD con Azure DevOps Services, consultare la [Panoramica della compilazione di Azure DevOps Services](https://www.visualstudio.com/docs/build/overview).
