---
title: CI/CD con il servizio contenitore di Azure e Swarm
description: Usare il servizio contenitore di Azure con Docker Swarm, un Registro di sistema del contenitore di Azure e Visual Studio Team Services per distribuire un'applicazione .NET Core multi-contenitore in modo continuativo
services: container-service
author: jcorioland
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 38877afb63e993eeaab723a6ea5f4c40d3c956a5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Pipeline CI/CD completa per distribuire un'applicazione multi-contenitore nel servizio contenitore di Azure con Docker Swarm tramite Visual Studio Team Services

Una delle sfide principali quando si sviluppano applicazioni moderne per il cloud è quella di riuscire a distribuire le applicazioni in modo continuativo. In questo articolo vengono offerte informazioni su come implementare una pipeline (CI/CD) completa di distribuzione e integrazione continuativa attraverso la gestione di compilazione e rilascio del servizio contenitore di Azure di Docker Swarm, Registro di sistema del contenitore di Azure e Visual Studio Team Services.

Questo articolo si basa su una semplice applicazione, disponibile in [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) e sviluppata con ASP.NET Core. L'applicazione è composta da quattro servizi diversi: tre API Web e un Web front-end:

![Applicazione MyShop di esempio](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

L'obiettivo consiste nell'assicurare l'applicazione in modo continuativo in un cluster Docker Swarm attraverso Visual Studio Team Services. Nell'immagine seguente sono illustrati i dettagli della pipeline di distribuzione continuativa:

![Applicazione MyShop di esempio](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Una breve spiegazione dei passaggi:

1. Viene eseguito il commit delle modifiche al codice nel repository del codice sorgente (in questo caso GitHub) 
2. GitHub attiva una compilazione in Visual Studio Team Services 
3. Visual Studio Team Services recupera la versione più recente delle origini e compila tutte le immagini che compongono l'applicazione 
4. Visual Studio Team Services effettua il push di ogni immagine a un Registro di sistema Docker creato tramite il servizio Registro di sistema del contenitore di Azure 
5. Visual Studio Team Services attiva un nuovo rilascio 
6. Il rilascio esegue alcuni comandi tramite SSH nel nodo principale del cluster del servizio contenitore di Azure 
7. Docker Swarm nel cluster effettua il pull della versione più recente delle immagini 
8. La nuova versione dell'applicazione viene distribuita mediante Docker Compose 

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare questa esercitazione, è necessario soddisfare i requisiti seguenti:

- [Creare un cluster Swarm nel servizio contenitore di Azure](container-service-deployment.md)
- [Connettersi a un cluster Swarm nel servizio contenitore di Azure](../container-service-connect.md)
- [Creare un Registro di sistema del contenitore di Azure](../../container-registry/container-registry-get-started-portal.md)
- [Disporre di un account Visual Studio Team Services e aver creato un progetto di team](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Creare una fork del repository GitHub nell'account GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

È inoltre necessario disporre di un computer Ubuntu (14.04 o 16.04) con Docker. Questo computer viene usato da Visual Studio Team Services durante i processi di compilazione e rilascio. Un modo per creare questo computer consiste nell'usare l'immagine disponibile in [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Passaggio 1: Configurare l'account di Visual Studio Team Services 

In questa sezione viene configurato l'account di Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Configurare un agente di compilazione Linux di Visual Studio Team Services

Per creare immagini Docker ed effettuare il push in un Registro di sistema del contenitore di Azure da una compilazione di Visual Studio Team Services, è necessario registrare un agente Linux. Sono disponibili queste opzioni di installazione:

* [Distribuire un agente in Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Usare Docker per eseguire l'agente VSTS](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Installare l'estensione VSTS di Docker Integration

Microsoft offre un'estensione di VSTS per lavorare con Docker nei processi di compilazione e rilascio. Questa estensione è disponibile nel [marketplace di VSTS](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Fare clic su **Installa** per aggiungere l'estensione all'account VSTS:

![Installare Docker Integration](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

È necessario connettersi all'account VSTS usando le credenziali. 

### <a name="connect-visual-studio-team-services-and-github"></a>Connettere Visual Studio Team Services e GitHub

Impostare una connessione tra il progetto VSTS e l'account GitHub.

1. Nel progetto di Visual Studio Team Services fare clic sull'icona **Impostazioni** nella barra degli strumenti e selezionare **Servizi**.

    ![Visual Studio Team Services - Connessione esterna](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. A sinistra fare clic su **Nuovo endpoint del servizio** > **GitHub**.

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Per autorizzare VSTS a lavorare con il proprio account GitHub, fare clic su **Autorizza** e attenersi alla procedura nella finestra visualizzata.

    ![Visual Studio Team Services - Autorizzazione GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Connettere VSTS al Registro di sistema del contenitore di Azure e al cluster del servizio contenitore di Azure

Gli ultimi passaggi prima di approfondire la pipeline CI/CD consistono nel configurare le connessioni esterne nel Registro di sistema del contenitore e nel cluster Docker Swarm in Azure. 

1. Nelle impostazioni **Servizi** del progetto Visual Studio Team Services aggiungere un endpoint di servizio di tipo **Docker Registry**. 

2. Nel popup che viene visualizzato immettere l'URL e le credenziali del Registro di sistema del contenitore di Azure.

    ![Visual Studio Team Services - Docker Registry](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Per il cluster Docker Swarm, aggiungere un endpoint di tipo **SSH**. Successivamente, immettere le informazioni di connessione SSH del cluster Swarm.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Tutti i passaggi di configurazione vengono eseguiti ora. Nei passaggi successivi viene creata la pipeline CI/CD che compila e distribuisce l'applicazione al cluster Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Passaggio 2: creare la definizione di compilazione

In questo passaggio viene configurata una definizione di compilazione per il progetto VSTS e viene definito il flusso di lavoro di compilazione per le immagini contenitore

### <a name="initial-definition-setup"></a>Configurazione iniziale della definizione

1. Per creare una definizione di compilazione, connettersi al progetto di Visual Studio Team Services e fare clic su **Build & Release** (Compilazione e rilascio). 

2. Nella sezione **Definizione di compilazione** fare clic su **+ New** (+Nuova). Selezionare il modello **Vuoto**.

    ![Visual Studio Team Services - Nuova definizione di compilazione](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Configurare la nuova compilazione con un'origine di repository GitHub, selezionare **Integrazione continua**, quindi scegliere la coda dell'agente in cui è registrato l'agente Linux. Fare clic su **Crea** per creare la definizione di compilazione.

    ![Visual Studio Team Services - Creazione definizione di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. Nella pagina **Definizioni di compilazione** innanzitutto aprire la scheda **Repository** e configurare la compilazione in modo che usi la fork del progetto MyShop creata nei prerequisiti. Assicurarsi di selezionare *acs-docs* come **ramo predefinito**.

    ![Visual Studio Team Services - Configurazione repository di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. Nella scheda **Trigger** configurare la compilazione in modo che venga attivata dopo ciascun commit. Selezionare **Integrazione continua** e **Modifiche bacth**.

    ![Visual Studio Team Services - Configurazione trigger di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definire il flusso di lavoro di compilazione
Nei passaggi successivi viene definito il flusso di lavoro di compilazione. Esistono cinque immagini contenitore per la compilazione dell'applicazione *MyShop*. Ogni immagine viene compilata attraverso il file Docker che si trova nelle cartelle del progetto:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

È necessario aggiungere due passaggi di Docker per ogni immagine, uno per compilare l'immagine, l'altro per effettuare il push dell'immagine nel Registro di sistema del contenitore di Azure. 

1. Per aggiungere un passaggio nel flusso di lavoro di compilazione fare clic su **+ Aggiungi istruzione di compilazione** e selezionare **Docker**.

    ![Visual Studio Team Services - Aggiunta passaggi di compilazione](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Per ogni immagine, configurare un passaggio che usi il comando `docker build`.

    ![Visual Studio Team Services - Compilazione Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Per l'operazione di compilazione, selezionare il Registro di sistema del contenitore di Azure, l'azione **Build an image** (Compila un'immagine) e il file Docker che definisce ogni immagine. Impostare **Build context** (Contesto compilazione) come directory radice del file Docker e definire **Nome immagine**. 
    
    Come mostrato nella schermata precedente, il nome dell'immagine deve iniziare con l'URI del Registro di sistema del contenitore di Azure. È anche possibile usare una variabile di compilazione per assegnare parametri al tag dell'immagine, in questo esempio l'identificatore di compilazione.

3. Per ogni immagine, configurare un secondo passaggio che usi il comando `docker push`.

    ![Visual Studio Team Services - Push Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Per l'operazione di push, selezionare il Registro di sistema del contenitore di Azure, l'azione **Push an image** (Push immagine) e immettere il **nome dell'immagine** compilato nel passaggio precedente.

4. Dopo aver configurato i passaggi di compilazione e push per ognuna delle cinque immagini, aggiungere altri due passaggi nel flusso di lavoro di compilazione.

    a. Un'attività della riga di comando che usi uno script bash per sostituire l'occorrenza *BuildNumber* nel file docker-compose.yml con l'ID di compilazione corrente. Vedere i dettagli nella schermata di seguito.

    ![Visual Studio Team Services - Aggiornamento file Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Un'attività che rimuove il file Compose aggiornato come elemento di compilazione per poterlo usare nel rilascio. Vedere i dettagli nella schermata di seguito.

    ![Visual Studio Team Services - Pubblicazione file Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Fare clic su **Salva** e assegnare un nome alla definizione di compilazione.

## <a name="step-3-create-the-release-definition"></a>Passaggio 3: creare la definizione di versione

Visual Studio Team Services consente di [gestire i rilasci nei vari ambienti](https://www.visualstudio.com/team-services/release-management/). È possibile abilitare la distribuzione continua per assicurarsi che l'applicazione venga distribuita in ambienti diversi (ad esempio sviluppo, test, preproduzione e produzione) in modo uniforme. È possibile creare un nuovo ambiente che rappresenti il cluster Docker Swarm del servizio contenitore di Azure.

![Visual Studio Team Services - Rilascio su ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configurazione iniziale del rilascio

1. Per creare una definizione di rilascio, fare clic su **Rilascio** > **+ Rilascio**

2. Per configurare l'origine dell'elemento, fare clic su **Elementi** > **Collega un'origine elemento**. Da qui collegare la nuova definizione di rilascio alla compilazione definita nel passaggio precedente. In questo modo, il file docker-compose.yml è disponibile nel processo di rilascio.

    ![Visual Studio Team Services - Rilascio elementi](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Per configurare il trigger di rilascio, fare clic su **Trigger** e selezionare **Distribuzione continua**. Impostare il trigger sulla stessa origine dell'elemento. Questa impostazione garantisce che un nuovo rilascio venga avviato non appena la compilazione viene completata correttamente.

    ![Visual Studio Team Services - Rilascio trigger](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definire il flusso di lavoro di rilascio

Il flusso di lavoro di rilascio è composto da due attività che vengono aggiunte.

1. Configurare un'attività per copiare in modo sicuro il file Compose in una cartella di *distribuzione* nel nodo master Docker Swarm, tramite la connessione SSH configurata in precedenza. Vedere i dettagli nella schermata di seguito.

    ![Visual Studio Team Services - Rilascio SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Configurare una seconda attività in modo che esegua un comando bash per i comandi `docker` e `docker-compose` nel nodo principale. Vedere i dettagli nella schermata di seguito.

    ![Visual Studio Team Services - Rilascio bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Il comando eseguito nel nodo principale usa l'interfaccia della riga di comando di Docker e Docker-Compose per eseguire le attività seguenti:

    - Accedere al Registro di sistema del contenitore di Azure (usa tre variabili di compilazione definite nella scheda **Variabili**)
    - Definire la variabile **DOCKER_HOST** in modo che sia compatibile con l'endpoint Swarm (:2375)
    - Accedere alla cartella di *distribuzione* che è stata creata dall'attività di copia sicura precedente e che contiene il file docker-compose.yml 
    - Eseguire i comandi `docker-compose` che effettuano il pull di nuove immagini, interrompono e rimuovono i servizi e creano i contenitori.

    >[!IMPORTANT]
    > Come illustrato nella schermata precedente, lasciare deselezionata la casella **Interrompi in caso di STDERR**. Questa è un'impostazione importante, perché `docker-compose` stampa diversi messaggi di diagnostica, ad esempio in caso di contenitori arrestati o in fase di eliminazione, nell'output di errore standard. Se si seleziona la casella di controllo, Visual Studio Team Services segnala che si sono verificati errori durante il rilascio, anche se tutto va bene.
    >
3. Salvare la nuova definizione di rilascio.


>[!NOTE]
>Questa distribuzione include tempi di inattività perché vengono interrotti i servizi precedenti per eseguire quello nuovo. È possibile evitare il problema effettuando una distribuzione di tipo blu-verde.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passaggio 4. Testare la pipeline CI/CD

Dopo aver completato la configurazione, è il momento di testare la nuova pipeline CI/CD. Il modo più semplice per eseguire il test consiste nell'aggiornare il codice sorgente ed eseguire il commit delle modifiche nel repository GitHub. Pochi secondi dopo il push del codice, verrà visualizzata una nuova compilazione in esecuzione in Visual Studio Team Services. Dopo il suo completamento, verrà attivato un nuovo rilascio, che distribuirà la nuova versione dell'applicazione nel cluster del servizio contenitore di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su CI/CD con Visual Studio Team Services, vedere la [panoramica sulla compilazione di VSTS](https://www.visualstudio.com/docs/build/overview).
