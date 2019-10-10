---
title: 'Esercitazione: Distribuire app Node.js con tecnologia Azure Cosmos DB con Azure DevOps Projects'
description: Azure DevOps Projects permette di iniziare a usare Azure velocemente. Con DevOps Projects, è possibile distribuire l'app Node.js con tecnologia Azure Cosmos DB in un'app Web Windows in pochi passaggi rapidi.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 1c16368990148406fbacdde2981c10fab0b2d405
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969668"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Distribuire app Node.js con tecnologia Azure Cosmos DB con DevOps Projects

Azure DevOps Projects offre un'esperienza semplificata in cui è possibile creare una pipeline di integrazione continua (CI, Continuous Integration) e distribuzione continua (CD, Continuous Deployment) in Azure. A tale scopo, usare il codice esistente e il repository GIT oppure selezionare un'applicazione di esempio.

DevOps Projects permette anche di:

* Creare automaticamente le risorse di Azure, ad esempio Azure Cosmos DB, Azure Application Insights, Servizio app di Azure e il piano servizio app.e

* Creare e configurare una pipeline di versione CI/CD in Azure DevOps

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

È necessaria una sottoscrizione di Azure, che è possibile ottenere gratuitamente tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Usare DevOps Projects per distribuire l'app Node.js

DevOps Projects crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Projects crea anche risorse di Azure, ad esempio un database Azure Cosmos DB, Application Insights, Servizio app di Azure e piani di servizio app, nella sottoscrizione di Azure di propria scelta.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea risorsa**.

1. Nella casella di ricerca, immettere **DevOps Projects**, quindi selezionare **Aggiungi**.

   ![Riquadro DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Selezionare **Node.js** come runtime e quindi fare clic **su Avanti**. In **Scegli un framework applicazione** selezionare **Express.js**.

1. Abilitare la sezione **Aggiungi un database** per **Cosmos DB** e quindi selezionare **Avanti**.

    ![Aggiungi un database](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Projects supporta diversi framework applicazione come **Express.js**, l'**app Node.js di esempio** e **Sail.js**. In questa esercitazione si userà **Express.js**.

1. Selezionare un servizio di Azure per distribuire l'applicazione, quindi selezionare **Avanti**. Sono disponibili opzioni come l'app Web Windows, il servizio Azure Kubernetes e l'app Web per contenitori di Azure. In questa esercitazione viene usata l'**app Web Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure

1. Immettere un nome per il progetto Azure DevOps.

1. Creare una nuova organizzazione di Azure DevOps o selezionare un'organizzazione esistente.

1. Selezionare la sottoscrizione di Azure.

1. Per visualizzare le impostazioni di configurazione aggiuntive di Azure o identificare il piano tariffario e la posizione, selezionare **Impostazioni aggiuntive**. Questo riquadro mostra diverse opzioni per la configurazione del piano tariffario e della posizione dei servizi di Azure.

1. Chiudere l'area di configurazione di Azure e selezionare **Fine**.

1. Il processo viene completato dopo alcuni minuti. Viene configurata un'app Node.js in un repository GIT nell'organizzazione Azure DevOps. Quindi, vengono create le risorse Azure Cosmos DB, servizio app, piano di servizio app e Application Insights, oltre alla pipeline CI/CD. L'app viene quindi distribuita in Azure.

   Al termine di tutti i processi, il dashboard di Azure DevOps Project viene visualizzato nel portale di Azure. È anche possibile passare al dashboard di DevOps Projects direttamente da **Tutte le risorse** nel portale di Azure.

   Questo dashboard fornisce visibilità su repository di codice di Azure DevOps, pipeline CI/CD e nel database Azure Cosmos DB. È possibile configurare le opzioni aggiuntive di CI/CD nella pipeline di Azure DevOps. A destra del dashboard selezionare **Azure Cosmos DB** per visualizzare queste opzioni.

## <a name="examine-azure-cosmos-db"></a>Esaminare il database Azure Cosmos DB

DevOps Projects configura automaticamente Azure Cosmos DB, che è possibile esplorare e personalizzare. Per acquisire familiarità con Azure Cosmos DB, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

    ![Dashboard di DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. A destra selezionare Azure Cosmos DB. Viene visualizzato un riquadro per Azure Cosmos DB. Da questa visualizzazione è possibile eseguire varie azioni, ad esempio monitorare le operazioni ed eseguire ricerche nei log.

    ![Riquadro di Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Esaminare la pipeline di integrazione continua

DevOps Projects configura automaticamente una pipeline CI/CD nell'organizzazione di Azure DevOps. È possibile esplorare e personalizzare la pipeline. Per acquisire familiarità, eseguire questa procedura:

1. Passare al dashboard di DevOps Projects.

1. Selezionare il collegamento ipertestuale sotto **Compilare**. In una scheda del browser viene visualizzata la pipeline di compilazione per il nuovo progetto.

    ![Riquadro Compilare](_img/azure-devops-project-cosmos-db/build.png)

1. Selezionare **Modifica**. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero del codice sorgente dal repository Git, la compilazione dell'applicazione, l'esecuzione di unit test e la pubblicazione degli output usati per le distribuzioni.

1. Selezionare **Trigger**. DevOps Projects crea automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione. È possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, quindi selezionare **Salva** dall'elenco a discesa **Salva e accoda**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**. Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

## <a name="examine-the-cd-release-pipeline"></a>Esaminare la pipeline di versione CD

DevOps Projects crea e configura automaticamente i passaggi necessari per la distribuzione dall'organizzazione di Azure DevOps nella sottoscrizione di Azure. Questi passaggi includono la configurazione di una connessione al servizio di Azure per eseguire l'autenticazione di Azure DevOps nella sottoscrizione di Azure. L'automazione crea anche una pipeline di versione che fornisce la distribuzione continua in Azure. Per altre informazioni sulla pipeline di versione, eseguire le operazioni seguenti:

1. Passare a **Pipeline** e selezionare **Versioni**.

1. Selezionare **Modifica**.

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto.

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**. Per questa pipeline di versione è abilitato un trigger di distribuzione continua che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. È possibile disabilitare il trigger, per eseguire manualmente le distribuzioni.

1. A destra, selezionare la sezione **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare la versione, in cui viene visualizzata la pipeline. Selezionare qualsiasi ambiente per verificare il riepilogo e i commit della versione e gli elementi di lavoro associati.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati a questa distribuzione. È possibile confrontare le versioni per visualizzare le differenze di commit tra le distribuzioni.

1. Selezionare **Visualizza log**. I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Eseguire il commit delle modifiche al codice e la pipeline CI/CD

> [!NOTE]
> La procedura seguente verifica la pipeline CI/CD, apportando una modifica di testo semplice.

A questo punto, si è pronti per collaborare con un team a un'app usando un processo di CI/CD che distribuisce il lavoro più recente nel servizio app. Ogni modifica al repository Git avvia una compilazione in DevOps di Azure e una pipeline CD esegue una distribuzione in Azure. Seguire la procedura in questa sezione o usare altre tecniche per eseguire il commit delle modifiche nel repository. Ad esempio, è possibile clonare il repository Git nello strumento o nell'IDE preferito e quindi eseguire il push delle modifiche in questo repository.

1. Nel menu di Azure DevOps, selezionare **Repos** e quindi **File**. Quindi, passare al repository.

1. Il repository contiene già il codice basato sulla lingua dell'applicazione che si è scelta nel processo di creazione. Aprire il file **Application/views/index.pug**.

1. Selezionare **Modifica** e quindi apportare una modifica al **numero di riga 15**. Ad esempio, è possibile modificarlo in "Prima distribuzione nel servizio app di Azure con tecnologia Azure Cosmos DB".

1. In alto a destra, selezionare **Commit**, quindi selezionare **Commit** nuovamente per eseguire il push della modifica.

     Dopo alcuni secondi verrà avviata una compilazione in Azure DevOps e verrà eseguita una versione per distribuire le modifiche. Monitorare lo stato della compilazione nel dashboard di DevOps Projects oppure nel browser con l'organizzazione di Azure DevOps.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, eliminare le risorse correlate create in precedenza. Usare la funzionalità **Elimina** nel dashboard di DevOps Projects.

## <a name="next-steps"></a>Passaggi successivi

È possibile modificare queste pipeline di compilazione e di versione in base alle esigenze del team. È anche possibile usare questo modello di CI/CD come modello per altre pipeline. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare DevOps Projects per distribuire un'app Node.js con tecnologia Azure Cosmos DB
> * Configurare Azure DevOps e una sottoscrizione di Azure 
> * Esaminare il database Azure Cosmos DB
> * Esaminare la pipeline di integrazione continua
> * Esaminare la pipeline di distribuzione continua
> * Eseguire il commit delle modifiche in Git e la distribuzione automatica in Azure
> * Pulire le risorse

Per altre informazioni e per i passaggi successivi, vedere [Definire la pipeline di distribuzione continua (CD) in più fasi](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts).


