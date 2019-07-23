---
title: 'Esercitazione: Distribuire app Node.js con tecnologia Azure Cosmos DB con Azure DevOps Projects'
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. Con DevOps Projects, è possibile distribuire l'app Node. js con tecnologia Azure Cosmos DB in un'app Web Windows in pochi passaggi rapidi.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813117"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Distribuire app Node.js con tecnologia Azure Cosmos DB con DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile usare il codice esistente e il repository Git oppure selezionare un'applicazione di esempio per creare una pipeline di integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) in Azure.

DevOps Projects permette anche di:

* creare automaticamente le risorse di Azure, ad esempio Azure Cosmos DB, Application Insights, servizio app e il piano servizio app.

* Creare e configurare una pipeline di versione in Azure DevOps per CI/CD

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire un'app Node.js con tecnologia Azure Cosmos DB
> * Configurare Azure DevOps e una sottoscrizione di Azure
> * Esaminare il database Azure Cosmos DB
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. È possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Usare DevOps Projects per distribuire l'app Node.js

DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure, ad esempio un database Azure Cosmos DB, Application Insights, Servizio app di Azure e un piano di servizio app, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com)

1. Nel riquadro a sinistra selezionare la sezione **Crea risorsa**.

1. Nella casella di ricerca, digitare **DevOps Projects**, quindi fare clic su **Aggiungi**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Selezionare **Node.js** come runtime e quindi fare clic **su Avanti**. In **Scegli un framework applicazione** selezionare **Express.js**.

1. Abilitare la sezione **Aggiungi un database** per **Cosmos DB** e fare clic su **Avanti**.

    ![Aggiungi database](_img/azure-devops-project-cosmos-db/add-database.png)

    Cosmos DB supporta diversi framework applicazione come **Express.js**, l'**app Node.js di esempio** e **Sail.js**. Questa esercitazione prende in considerazione **Express.js**.

1. Selezionare un servizio di Azure per distribuire l'applicazione. Sono disponibili servizi diversi come l'app Web Windows, il servizio Kubernetes e l'app Web per contenitori. Per questa esercitazione verrà usata l'**app Web Windows**. Fare clic su **Avanti**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Immettere un nome per il progetto Azure DevOps.

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente.

1. Selezionare la sottoscrizione di Azure.

1. Per visualizzare le impostazioni di configurazione aggiuntive di Azure e identificare il piano tariffario e la posizione, fare clic su Impostazioni aggiuntive. Questo riquadro mostra diverse opzioni per la configurazione del piano tariffario e della posizione dei servizi di Azure.

1. Chiudere l'area di configurazione di Azure e selezionare **Fine**.

1. Il processo verrà completato dopo alcuni minuti. Viene configurata un'app di esempio Node.js in un repository Git nell'organizzazione di Azure DevOps, vengono creati un database Azure Cosmos, un servizio app, un piano di servizio app e Application Insights, viene eseguita una pipeline CI/CD e l'app viene distribuita in Azure.

   Al termine, il dashboard di Azure DevOps Projects viene visualizzato nel portale di Azure. È anche possibile passare al dashboard di DevOps Projects direttamente da **Tutte le risorse** nel portale di Azure.

   Questo dashboard fornisce visibilità su repository di codice di Azure DevOps, pipeline CI/CD e in Azure Cosmos DB. È possibile configurare le opzioni aggiuntive di CI/CD nella pipeline di Azure DevOps. A destra selezionare **Azure Cosmos DB** per visualizzarlo.

## <a name="examine-the-azure-cosmos-db"></a>Esaminare il database Azure Cosmos DB

DevOps Projects configura automaticamente Cosmos DB, che è possibile esplorare e personalizzare. Per acquisire familiarità con il database Cosmos DB, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. A destra, selezionare il database Cosmos DB. Viene visualizzato un riquadro per il database Cosmos DB. Da questa visualizzazione è possibile eseguire varie azioni, ad esempio monitorare le operazioni ed eseguire ricerche nei log.

    ![App per le funzioni](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

DevOps Projects configura automaticamente una pipeline CI/CD nell'organizzazione di Azure DevOps. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

1. Fare clic sul collegamento ipertestuale sotto **Compilare**. In una scheda del browser viene visualizzata la pipeline di compilazione per il nuovo progetto.

    ![Compilare](_img/azure-devops-project-cosmos-db/build.png)

1. Selezionare **Modifica**. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero del codice sorgente dal repository Git, la compilazione dell'applicazione, l'esecuzione di unit test e la pubblicazione degli output usati per le distribuzioni.

1. Selezionare **Trigger**. DevOps Projects crea automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, quindi selezionare **Salva** dall'elenco a discesa **Salva e accoda**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**. Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

## <a name="examine-the-cd-release-pipeline"></a>Esaminare la pipeline di versione CD

DevOps Projects crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps nella sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps nella sottoscrizione di Azure. L'automazione crea anche una pipeline di versione che fornisce la distribuzione continua in Azure. Per altre informazioni sulla pipeline di versione, eseguire le operazioni seguenti:

1. Passare a **Pipeline | Versioni**.

1. Fare clic su **Edit** (Modifica).

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto.

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**. Per questa pipeline di versione è abilitato un trigger di distribuzione continua che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale.

1. A destra, selezionare la sezione **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Fare clic sulla versione, in cui viene visualizzata la pipeline. Fare clic su qualsiasi ambiente per verificare **il riepilogo e i commit** della versione e gli **elementi di lavoro** associati.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Visualizza log**. I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

> [!NOTE]
> La procedura seguente verifica la pipeline CI/CD, apportando una modifica di testo semplice.

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce automaticamente il lavoro più recente nel servizio app di Azure. Ogni modifica al repository Git avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Ad esempio, è possibile clonare il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Nel menu di Azure DevOps, selezionare **Repos | File** e quindi passare al repository.

1. Il repository contiene già il codice basato sulla lingua dell'applicazione che si è scelta nel processo di creazione. Aprire il file **Application/views/index.pug**.

1. Selezionare **Modifica** e quindi apportare una modifica al **numero di riga 15**. Ad esempio, è possibile aggiornarlo come **Prima distribuzione nel servizio app di Azure con tecnologia Azure Cosmos DB**

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.

     Dopo alcuni istanti verrà avviata una compilazione in Azure DevOps e verrà eseguita una versione per distribuire le modifiche. Monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nel browser con l'organizzazione di Azure DevOps.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare le risorse correlate create in precedenza. Usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire un'app Node.js con tecnologia Azure Cosmos DB
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare il database Azure Cosmos DB
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulire le risorse
